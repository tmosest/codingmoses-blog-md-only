---
title: Day 11 HomeLab - I Fought the Mouse and the Mouse won...
description: Part 2 of Creating a Vision AI system to detect rodents.
date: 2025-09-06
categories: [Yolo, VisionAI, ]
author: moses
tags: [yolo]
hideToc: true
---

# The Clouds!

The mice... they have won... I am still surrounded...

```
I am in the midst of lions;
    I am forced to dwell among ravenous beasts—
men whose teeth are spears and arrows,
    whose tongues are sharp swords.
```

Psalm 57 - King David...

Might as well see what God has to say...

```
Look! He comes with the clouds of heaven. And everyone will see him— even those who pierced him. And all the nations of the world will mourn for him. Yes! Amen!
```
- Revelation 1:7

That's Brilliant Harry, we can look to see if there are any cloud sets on Kaggle that are already working!

What's a Kaggle? Well it's not what your sister does when she's board in math class...

- [This is a Racer X Song](https://youtu.be/yfXeO_Ff_Wc?si=4foWhjtE1st6YYSc&t=71)

Take it away Chat GPT!:

```
What Kaggle Is

Competition Platform: Originally, Kaggle was famous for hosting competitions. Companies or researchers would post a problem (like predicting house prices, detecting cancer in medical images, or improving recommendation systems) and offer prize money. Data scientists worldwide would compete to build the best models.

Dataset Hub: Today, one of its biggest draws is the massive collection of open datasets. You can find almost anything — images of cats and dogs, financial data, satellite imagery, text corpora, etc.

Code Sharing: People publish “Kernels” (now called Notebooks) — basically ready-to-run code that demonstrates how to solve problems with data.

Learning Resource: Kaggle has tutorials (“micro-courses”) for Python, machine learning, deep learning, SQL, etc. A lot of people use it to learn ML hands-on.

👩‍💻 Why It’s Useful

You don’t need a powerful computer — Kaggle gives you free cloud GPUs/TPUs for training models.

You can fork other people’s notebooks and play with them, like GitHub but focused on data science.

It’s a great place to practice ML workflows (import data, clean it, train models, visualize results).

Many CV (computer vision) researchers and hobbyists upload YOLO datasets, pretrained models, and walkthroughs there.

🌐 Quick Example

If you were working on your rodent-tracking idea:

You could check Kaggle to see if anyone uploaded a mouse/squirrel dataset.

You could fork a YOLOv8 notebook and retrain it on your own images.

You could share your own results back with the community.

👉 Think of Kaggle as GitHub + StackOverflow + Free Cloud GPUs + Data Playground all rolled into one.

Do you want me to pull together a Kaggle starter roadmap for your homelab projects (like rodent-tracking, YOLO training, etc.)?
```

The Leetcode of DataScience! Therefore we can just copy what all the Greats do best and use other peoples data and hard work and call it our own...
ooooopppppsss I mean... come back there's cake?..

- https://www.kaggle.com/datasets?search=animal&tags=13207-Computer+Vision
- https://www.kaggle.com/datasets/iamsouravbanerjee/animal-image-dataset-90-different-animals
- https://www.kaggle.com/datasets/iamsouravbanerjee/animal-image-dataset-90-different-animals/code
- https://www.kaggle.com/code/mygaps/custom-imagenet/notebook
- https://www.kaggle.com/code/kasaligabriel/animal-classification-with-tensorflow
- https://www.kaggle.com/code/renjiabarai/yolo11x-cls-workflow
- https://www.kaggle.com/code/muhammadfaizan65/90-animals-image-classification-efficientnetb3
- https://www.kaggle.com/datasets/goelyash/animal-dataset

Found another set to test with:

- https://www.kaggle.com/datasets/utkarshsaxenadn/animal-image-classification-dataset
- https://www.kaggle.com/code/tmosest/yolo11x-cls-workflow/edit#YOLO

Well I added another data set to test against and this model thinks that a mouse might be a pigeon... need to train more and get better data...

Which will require the homelab to be back up and operational...

Well I'm SOL... I need a miracle..

`Enter into the rock and hide yourself in the dust from before the terror of the Lord and from the glory of His majesty.`
- Isaih 2

## Melchizedek Bible App

Speaking of which, I decided to make a free Bible / Book Server in Node, Express, Typescript and MySQL.

That was inspired by a job interview for a AI Law company and the fact that Hallow's bible app isn't free like it is advertised.

That and I enjoy rollig the dice and seeing what the universe has to say sometimes. The main API is as follows:

Requests

```
{
    "search": "Genesis",
    "bibleVersion": 1
}
```

```
{
    "search": "Genesis 1",
    "bibleVersion": 1
}
```

```
{
    "search": "Genesis 1-3",
    "bibleVersion": 1
}
```

```
{
    "search": "Genesis 1:1-3",
    "bibleVersion": 1
}
```

Response 

```
[
    {
        "id": 1,
        "book": 1,
        "chapter": 1,
        "verse": 1,
        "bibleVersion": 1,
        "section_title": null,
        "foot_note": null,
        "cross_reference": null,
        "commentary": null,
        "content": "In the beginning God created the heaven and the earth.",
        "createdAt": "2025-01-24T09:53:17.000Z",
        "updatedAt": "2025-01-24T09:53:17.000Z"
    }
    // ...
]
```

Which allows a user to easily search like most bible sites. It can also generate random verses as well. The UI side is lacking at the moment but that a problem for another time after I figure out how to clone myself. The end goal was more complex search queirers. Prayer requests. Notes etc. Other book uploads. Basically a book and note scanner as well. All of which is still very doable now that I have pipelines in my homelab. 

To get this deployed on the home network, I had to make some simple modifications. The application was already wrapped in docker compose so there was no problem there.

The mysql credentials were still hard coded in the express app but I switched them to env variables in docker for now for the beta deploy.
Later they will be env variables in woodpecker. But for now this will get our deployment working.

```
services:
  mysql:
    image: mysql:8.0 # Or your desired MySQL image
    environment:
      MYSQL_USER: user
      MYSQL_PASSWORD: password
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: db
      MYSQL_ALLOW_EMPTY_PASSWORD: true
    tty: true
    ports:
      - "3306:3306"
    command:
      - --default-authentication-plugin=mysql_native_password
    volumes:
      - /var/lib/mysql # Persistent storage for MySQL data
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-u", "root", "-p$$MYSQL_ROOT_PASSWORD"]
      interval: 5s
      timeout: 5s
      retries: 5
      start_period: 30s # Optional: Time to wait before first health check
  web:
    build: 
      context: ./melchizedek-bible-api
      dockerfile: Dockerfile
    ports:
      - "3000:3000" # expose local port
    volumes:
    - ./melchizedek-bible-api:/usr/src/app # mount local working directory for live reload
    - /usr/src/app/node_modules # mount node_modules for caching
    # - /usr/src/app/dist # mount node_modules for caching
    depends_on:
      mysql:
        condition: service_healthy
    # links:
    #  - "mysql:mysql"
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: user
      MYSQL_PASSWORD: password
      MYSQL_DATABASE: db
      NODE_ENV: development
      DEBUG: express:*
      DEBUG_SHOW_HIDDEN: true
      MYSQL_PORT: 3306
    # tty: false
# Names our volume
volumes:
  # db_data:
  melchizedek-bible-api:

networks:
  my-network:

```

That and a minor update to the `app.module.ts` file and we have working database connections again.

```
// TODO get MySQL variables from the docker-compose file.
@Module({
  imports: [
    SequelizeModule.forRoot({
      dialect: 'mysql',
      host: process.env.MYSQL_HOST,
      port: Number(process.env.MYSQL_PORT),
      username: process.env.MYSQL_USER,
      password: process.env.MYSQL_PASSWORD,
      database: process.env.MYSQL_DATABASE,
      models: [Bible, BibleBook, BibleVersion],
      logging: (...msg) => logger.error(msg),
      // socketPath: '/var/run/mysqld/mysqld.sock',
      autoLoadModels: true,                             // TODO turn off for production as well.
      synchronize: true,                                // TODO note need to turn this off for production.
    }),
    BibleModule,
    BibleBookModule,
    BibleVersionModule
  ],
  controllers: [AppController],
  providers: [AppService],
})
export class AppModule {}
```

There were several issues getting this to work at first. So I updated the logger as well:

```
import { createLogger, transports, format } from 'winston';
import { WinstonModule } from 'nest-winston';
// import { MySQL } from 'winston-mysql'; // Import the MySQL transport

const logger = WinstonModule.createLogger({
  level: 'info', // Set the default logging level
  format: format.combine(
    format.timestamp({ format: 'YYYY-MM-DD HH:mm:ss' }),
    format.printf(({ level, message, timestamp }) => {
      return `${timestamp} [${level.toUpperCase()}]: ${message}`;
    })
  ),
  transports: [
    new transports.Console({
      format: format.combine(
        format.timestamp(),
        format.colorize(),
        format.printf(({ timestamp, level, message, context }) => {
          return `${timestamp} [${context}] ${level}: ${message}`;
        }),
      ),
    }), 
    /*
    // Log to the console
    new MySQL({
      host: process.env.MYSQL_HOST,
      user: process.env.MYSQL_USER,
      password: process.env.MYSQL_PASSWORD,
      database: process.env.MYSQL_DATABASE,
      table: 'logs', // Table to store logs
      // Optional: customize fields if your table schema differs
      // fields: { level: 'log_level', message: 'log_message', meta: 'log_metadata', timestamp: 'created_at' }
    }),
    */
    new transports.File({ filename: 'error.log', level: 'error' }), // Log errors to a file
    new transports.File({ filename: 'combined.log' }), // Log all levels to another file
  ],
});

export default logger;
```

Hook up the logger:

```
  logger.error(` host: ${process.env.MYSQL_HOST},
      port: ${process.env.MYSQL_PORT},
      username: ${process.env.MYSQL_USER},
      password: ${process.env.MYSQL_PASSWORD},
      database: ${process.env.MYSQL_DATABASE},`);

async function bootstrap() {
  const app = await NestFactory.create(AppModule, {
        logger: logger, // Pass your Winston logger instance
  });
  await app.listen(process.env.PORT ?? 3000);
}
bootstrap();
```

One annoyingn issue here was that the Database connection would work fine but then the auto sync would fail.
Apparently, I was not the only one to suffer from this as someone else took three days to realize it was a data type issue for them.

- https://stackoverflow.com/questions/79444382/sequelizemodule-autoloadmodels-nest-error-sequelizemodule-unable-to-connec

```
Change your users.model.ts from:

@Column({ type: DataType.NUMBER, unique: true, autoIncrement: true, primaryKey: true })
id: number;

to:

@Column({ type: DataType.INTEGER, unique: true, autoIncrement: true, primaryKey: true })
id: number; 
```

I was much more interested in how he got more detailed SQL logs though:

```
[Nest] 14312  - 16.02.2025, 20:41:44     LOG [NestFactory] Starting Nest application...
[Nest] 14312  - 16.02.2025, 20:41:46     LOG [InstanceLoader] AppModule dependencies initialized +1744ms
[Nest] 14312  - 16.02.2025, 20:41:46     LOG [InstanceLoader] SequelizeModule dependencies initialized +0ms
[Nest] 14312  - 16.02.2025, 20:41:46     LOG [InstanceLoader] UsersModule dependencies initialized +1ms
[Nest] 14312  - 16.02.2025, 20:41:46     LOG [InstanceLoader] ConfigHostModule dependencies initialized +0ms
[Nest] 14312  - 16.02.2025, 20:41:46     LOG [InstanceLoader] ConfigModule dependencies initialized +0ms
Executing (default): SELECT 1+1 AS result
Executing (default): SELECT table_name FROM information_schema.tables WHERE table_schema = 'public' AND table_name = 'users'
Executing (default): CREATE TABLE IF NOT EXISTS "users" ("id" NUMBER SERIAL UNIQUE , "email" VARCHAR(255) NOT NULL UNIQUE, "password" VARCHAR(255) NOT NULL, "banned" BOOLEAN DEFAULT false, "banReason" VARCHAR(255), "createdAt" TIMESTAMP WITH TIME ZONE NOT NULL, "updatedAt" TIMESTAMP WITH TIME ZONE NOT NULL, PRIMARY KEY ("id"));
[Nest] 14312  - 16.02.2025, 20:41:46   ERROR [SequelizeModule] Unable to connect to the database. Retrying (1)...
Error
    at Query.run (D:\IT\IT\PROJECTS\my_projects\ulbi\backend_node_nest_docker\node_modules\sequelize\src\dialects\postgres\query.js:76:25)
    at <anonymous> (D:\IT\IT\PROJECTS\my_projects\ulbi\backend_node_nest_docker\node_modules\sequelize\src\sequelize.js:650:28)
    at process.processTicksAndRejections (node:internal/process/task_queues:95:5)
    at PostgresQueryInterface.createTable (D:\IT\IT\PROJECTS\my_projects\ulbi\backend_node_nest_docker\node_modules\sequelize\src\dialects\abstract\query-interface.js:229:12)
    at Function.sync (D:\IT\IT\PROJECTS\my_projects\ulbi\backend_node_nest_docker\node_modules\sequelize\src\model.js:1353:7)
    at Sequelize.sync (D:\IT\IT\PROJECTS\my_projects\ulbi\backend_node_nest_docker\node_modules\sequelize\src\sequelize.js:825:9)
    at async D:\IT\IT\PROJECTS\my_projects\ulbi\backend_node_nest_docker\node_modules\@nestjs\sequelize\dist\sequelize-core.module.js:123:17

```

My docker instance was now going on strike and demanding a peanut butter sandwich but ALL WE HAD WAS TUNAAAAAAAAAAAAAAAAA!!!!!!!

- https://www.youtube.com/watch?v=3okU50_eqpQ

Really it was this issue but `Error: EBUSY: resource busy or locked, rmdir`

- https://stackoverflow.com/questions/55212864/error-ebusy-resource-busy-or-locked-rmdir

Therefore I spun around three times like a good pigeon, pushed the button, and rebooted the instance... and still broken... but after making some minor updates to my docker config it was working again!

Well this was after setting up another terraform deploy as well:

```
terraform {
  required_providers {
    proxmox = {
      source = "telmate/proxmox"
      #latest version as of Nov 30 2022
      version = "3.0.2-rc04"
    }
  }
}

provider "proxmox" {
  # References our vars.tf file to plug in the api_url 
  pm_api_url = var.api_url
  # References our secrets.tfvars file to plug in our token_id
  pm_api_token_id = var.token_id
  # References our secrets.tfvars to plug in our token_secret 
  pm_api_token_secret = var.token_secret
  # Default to `true` unless you have TLS working within your pve setup 
  pm_tls_insecure = true

  pm_log_enable = true
  pm_log_file   = "terraform-plugin-proxmox.log"
  pm_debug      = true
  pm_log_levels = {
    _default    = "debug"
    _capturelog = ""
  }
}

resource "proxmox_vm_qemu" "melchizedek-server-beta" {
    name            = "melchizedek-server-beta"
    target_node     = "pve"
    clone           = "template"
    bios            = "seabios"
    onboot          = true
    ipconfig0       = "ip=dhcp" 
    agent           = 1
    # iso           = "local:iso/ubuntu-25.04-live-server-amd64.iso" # "/var/lib/vz/template/iso/ubuntu-25.04-live-server-amd64.iso" # "local:iso/ubuntu-25.04-live-server-amd64.iso"
    # template      = "ubuntu-25.04"
    cpu {
      cores         = 2
      sockets       = 1
      type          = "host"
    }
    # cpu_type      = "host"
    # cpu_type      = "qemu64" 
    
    memory          = 4096
    boot            = "order=scsi0;net0"
    kvm             = true

    skip_ipv6       = true

    disk {
      slot = "scsi0"
      # set disk size here. leave it small for testing because expanding the disk takes time.
      size = 32
      type = "disk"
      storage = "local-lvm"
      # iothread = true
     }

    network {
        id        = 0
        bridge    = "vmbr0"
        model     = "virtio"
        macaddr   = "aa:bb:cc:dd:ee:ff"
    }

    provisioner "local-exec" {
        when    = create
        command = "sleep 15"
    }
}

output "vm_ip_address" {
    value = proxmox_vm_qemu.melchizedek-server-beta.default_ipv4_address
}

output "vm_mac_address" {
    value = proxmox_vm_qemu.melchizedek-server-beta.network.0.macaddr
    description = "The MAC address of the first network interface of the VM."
}

```

and some woodpecker stuff:

.woodpecker/main.yml

```
# .woodpecker.yml
when:
  event: [push, manual]
  branch: develop

steps:
  terraform-beta-deploy:
    image: hashicorp/terraform:latest
    # privileged: true
    user: builder 
    depends_on: []
    commands:
      - whoami
      - cd terraform
      - terraform init
      - terraform plan -out plan
      - terraform apply "plan"
      - export IP_ADDR="$(terraform output vm_ip_address | tr -d '"')"
      - export MAC_ADDR="$(terraform output vm_mac_address | tr -d '"')"
      - echo $IP_ADDR
      - echo $MAC_ADDR
      - apk update && apk add curl && apk add net-tools && apk add sshpass && apk add openssh
      - echo "Starting deployment of ssh keys"
      - sleep 1
      - export USER="$(whoami)"
      - echo "SSH with $USER @ $IP_ADDR"
      - ssh-keygen -t rsa -b 4096 -C "woodpecker-ci-key" -f ~/.ssh/id_rsa -q -N ""
      - ssh-keyscan -T 240 "$IP_ADDR" >> ~/.ssh/known_hosts
      - echo "Running ssh-copy-id -i ~/.ssh/id_rsa.pub $USER@$IP_ADDR"
      - sshpass -v -p "$HOST_PASSWORD" ssh-copy-id $USER@$IP_ADDR
      - echo "SSH Copied to $USER @ $IP_ADDR"
      # - ssh  $USER@$IP_ADDR "mkdir /var/www"
      # - scp 
      - ssh $USER@$IP_ADDR "pwd; rm -Rf MelchizedekBibleAppApi;git clone https://gitea.homelab.com/tmosest/MelchizedekBibleAppApi.git; cd MelchizedekBibleAppApi; git checkout develop; docker compose up --build -d;"
      - echo "Started Application"

  test-beta:
    image: docker:latest
    privileged: true
    depends_on: [terraform-beta-deploy]
    commands:
      # Run a container from the image to test it
      - apk update && apk add curl && apk add net-tools
      - ifconfig
      - sleep 1 # Wait for the app to start
      # - netstat -tulnp | grep :5000
      - curl --fail https://bible.homelab.com/status
      - echo "Application is running and responding."
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    when:
      status: success
      steps: build

```

Make sure the static ip and subdomain / port get added to my opsense and NGINX PROXY Manager hosts and we are good to go. I am now able to make queries against the server.

### Data issues

Need to work on a better dedicated database for this and a better data upload process.

At the moment, there are just some python and / or POSTMAN API scripts to upload the books and verses.

I also have to deal with the fact that I have both some JSON data from a free King James bible repo and I also have data that was scrapped from the web into text files. Using the API to upload all these versions takes way too long to do it over and again so it would make more sense for beta to just do small test set and then there to be a prod database that doesn't need to be updated every deployment.

## Steven to the rescue!

It's 5 AM and the screeches of a hungry cat can be heard from the other side of the door!

I am saved at last!!! The mice are driven away by the cat... now I just need to find a cupcake for a kitten. 

## Sources

- https://medium.com/@chrischuck35/how-to-create-a-mysql-instance-with-docker-compose-1598f3cc1bee
- https://www.bibleref.com/Genesis/1/Genesis-1-2.html
- https://docs.nestjs.com/techniques/database
- https://docs.nestjs.com/exception-filters
- https://sequelize.org/
- https://www.geeksforgeeks.org/loop-through-a-json-array-in-python/
- PIP https://discuss.python.org/t/on-macos-14-pip-install-throws-error-externally-managed-environment/50352/6
- Custom Voice Model https://www.youtube.com/watch?v=e71H--vxRvo
- https://github.com/rdhyee/py-applescript
- https://medium.com/@nimritakoul01/offline-speech-to-text-in-python-f5d6454ecd02
- https://medium.com/@obaff/building-a-simple-program-with-chatgpt-api-and-python-dd0e464e71be

Note: The tech is factual the other content is less serious.
