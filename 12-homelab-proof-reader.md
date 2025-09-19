---
title: Day 12 HomeLab - Saved By The Cat
description: Simple Node, Express, NEST, MYSQL App
date: 2025-09-06
categories: [Yolo, VisionAI, NEST]
author: moses
tags: [yolo]
hideToc: true
---

**TL;DR**  
- Spin up a *dedicated* MySQL (or Postgres) container that you **don’t** recreate on every deploy.  
- Write a **single, bulk‑insert script** that parses both the JSON repo and your scraped text files and writes *once* to that DB.  
- Call that script from your CI‑pipeline (or run it locally) instead of using Postman / Python for each book/verse.  
- Keep the production data on a read‑only replica; only the dev/beta copy needs to be refreshed.  

Below is a concrete plan (and a little cat‑cuddle break at the end) that should keep the mice— and your data— under control.

## 1.  Dedicated, persistent database

### Docker‑Compose (one‑off)

```yaml
# docker-compose-mysql.yml
services:
  mysql:
    image: mysql:8.0
    container_name: bible-mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: bible
      MYSQL_USER: bible_user
      MYSQL_PASSWORD: ${MYSQL_BIBLE_PASSWORD}
    ports:
      - "3306:3306"
    volumes:
      - db-data:/var/lib/mysql
volumes:
  db-data:
```

- Run this **once** (or keep it in your `terraform`/`proxmox` config if you want a VM‑based DB).  
- Store credentials in a `.env` file or a secrets manager (HashiCorp Vault, AWS Secrets Manager, etc.).  
- Since the volume is persistent, you won’t lose data on rebuilds.

### Production‑ready schema

```sql
-- books
CREATE TABLE IF NOT EXISTS books (
    id          BIGINT AUTO_INCREMENT PRIMARY KEY,
    title       VARCHAR(255) NOT NULL,
    language    VARCHAR(50),
    translator  VARCHAR(255),
    version     VARCHAR(50) NOT NULL,
    UNIQUE KEY uq_book (title, language, translator, version)
);

-- verses
CREATE TABLE IF NOT EXISTS verses (
    id          BIGINT AUTO_INCREMENT PRIMARY KEY,
    book_id     BIGINT NOT NULL,
    chapter     INT NOT NULL,
    verse       INT NOT NULL,
    text        TEXT NOT NULL,
    FOREIGN KEY (book_id) REFERENCES books(id)
);
```

> **Tip**: Use `UNIQUE KEY` on the book columns so you don’t insert duplicates.

---

## 2.  Bulk‑import script (Node + Sequelize)

You already have a `NestJS` + `Sequelize` stack; let’s leverage it for a one‑shot import.

```ts
// src/importer/importer.ts
import { Sequelize, Model, DataTypes } from 'sequelize';
import fs from 'fs';
import path from 'path';

const sequelize = new Sequelize({
  dialect: 'mysql',
  host: process.env.DB_HOST,
  database: 'bible',
  username: process.env.DB_USER,
  password: process.env.DB_PASS,
});

class Book extends Model {}
Book.init(
  {
    title: DataTypes.STRING,
    language: DataTypes.STRING,
    translator: DataTypes.STRING,
    version: DataTypes.STRING,
  },
  { sequelize, tableName: 'books', timestamps: false }
);

class Verse extends Model {}
Verse.init(
  {
    bookId: { type: DataTypes.BIGINT, field: 'book_id' },
    chapter: DataTypes.INTEGER,
    verse: DataTypes.INTEGER,
    text: DataTypes.TEXT,
  },
  { sequelize, tableName: 'verses', timestamps: false }
);

async function bulkInsertBooks(jsonPath: string) {
  const raw = fs.readFileSync(jsonPath, 'utf-8');
  const books = JSON.parse(raw);

  // Transform JSON into format Sequelize expects
  const bookRecords = books.map(b => ({
    title: b.title,
    language: b.language,
    translator: b.translator,
    version: b.version,
  }));

  // Bulk insert books
  const inserted = await Book.bulkCreate(bookRecords, { ignoreDuplicates: true });

  // Map from (title, language, translator, version) to id
  const bookMap: Record<string, number> = {};
  inserted.forEach(b => {
    const key = `${b.title}|${b.language}|${b.translator}|${b.version}`;
    bookMap[key] = b.id;
  });

  // Now insert verses
  const verseRecords = [];
  for (const book of books) {
    const key = `${book.title}|${book.language}|${book.translator}|${book.version}`;
    const bookId = bookMap[key];

    for (const ch of book.chapters) {
      for (const v of ch.verses) {
        verseRecords.push({
          bookId,
          chapter: ch.chapter,
          verse: v.verse,
          text: v.text,
        });
      }
    }
  }

  await Verse.bulkCreate(verseRecords, { ignoreDuplicates: true });
}

async function main() {
  await sequelize.authenticate();
  await sequelize.sync(); // create tables if not exist

  const jsonFile = path.resolve(process.argv[2] || 'bible.json');
  await bulkInsertBooks(jsonFile);

  console.log('Import finished');
  process.exit(0);
}

main().catch(err => {
  console.error(err);
  process.exit(1);
});
```

**Run it**

```bash
node importer/importer.js ./data/king_james.json
```

- The script reads the JSON once, builds two arrays (`bookRecords` and `verseRecords`), and calls `bulkCreate` twice.  
- `ignoreDuplicates: true` prevents duplicate rows.  
- For the scraped text files, you can write a quick Python or Node script that parses the files into the same JSON structure and feeds it to the same importer.

### Speed‑ups

| Technique | How |
|-----------|-----|
| `bulkCreate` | Sends a single INSERT with many VALUES instead of dozens of single INSERTs. |
| `IGNORE DUPLICATE` | Avoids 2nd‑pass lookups. |
| Batch the verses (1000 per `bulkCreate`) | Keeps memory in check. |
| Parallel parsing | Parse JSON / text files on several workers (e.g., `Promise.all`) if your dataset is > 50 M rows. |

---

## 3.  Integrate into CI / Proxmox

Add a new job to your Docker‑Compose (or Terraform) deployment that **doesn’t restart** the MySQL container but simply runs the importer against the *already‑seeded* database.

```yaml
# docker-compose-dev.yml
services:
  importer:
    image: node:18
    volumes:
      - ./:/app
    working_dir: /app
    command: sh -c "npm install && node importer/importer.js ./data/king_james.json"
    environment:
      DB_HOST: bible-mysql
      DB_USER: bible_user
      DB_PASS: ${MYSQL_BIBLE_PASSWORD}
    depends_on:
      - mysql
```

> *You can drop the `depends_on` line if you’re calling the script from CI outside the container; just point `DB_HOST` at the IP of your persistent DB.*

---

## 4.  Keeping production data read‑only

- **Dev / beta**: runs the importer **every 1–2 weeks** (when you add a new translation or fix a typo).  
- **Prod**: runs on a **replica** that only pulls the latest commit from the import repo; you never write to it in CI.  
- You can set up a read‑only MySQL replica (using `mysqldump --single-transaction` or `pg_dump`) or simply point the prod app at the persistent MySQL container above and set `READONLY=true` in your app config.

---

## 5.  Static IP & routing

Since you’re already using OPNsense and NGINX Proxy Manager:

1. **OPNsense**  
   - Allocate a static IP for your VM/Container (e.g., `192.168.1.100`).  
   - Open the port mapping (`3306` for MySQL, `80`/`443` for your API).  

2. **Nginx Proxy Manager**  
   - Create a *Host* pointing to `192.168.1.100:80` (or `443` if you’re using TLS).  
   - Set the domain (e.g., `bible.dev.yourdomain.com`) and enable *SSL* (Let’s Encrypt).  

3. **NestJS**  
   - In your `.env`, set `APP_PORT=80` (or whatever port you expose).  
   - The Dockerfile will bind that port, and Nginx Proxy Manager will forward HTTPS requests to it.

---

## 6.  The 5 AM “cat‑cuddle” note

You mentioned that it’s 5 AM and a cat is chewing the mice‑trap. Don’t worry – with the bulk‑import script above, the data will be ready *once* and then stay ready.  
When you’re done, grab a cupcake (or a banana‑topped cat‑treat) and let the mice run to their own little corner.  

> **Final tip**: Keep your importer idempotent (`ignoreDuplicates: true`). Then you can safely re‑run it whenever you want to refresh the beta copy without harming the production read‑only data.

---

## 🎉 Quick Cat‑Cuddle Summary

- **Deploy a persistent MySQL container** once.  
- **Bulk‑import** books/verses in a single script (`bulkCreate`).  
- Keep the **production copy** read‑only; only dev needs the refresh.  
- **Run the import** from CI or locally – no Postman marathon needed.  

Now the mice stay away, the cat’s out of the cage, and your Bible API is ready to serve verse‑requests in record time!
