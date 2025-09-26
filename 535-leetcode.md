---
title: LeetCode 535. Encode and Decode TinyURL - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 535. Encode and Decode TinyURL – Code, Concepts & SEO‑Ready Blog

> **Target keyword bundle:** *Encode and Decode TinyURL, TinyURL URL shortener, LeetCode 535, job interview coding, Java/Python/C++ solution, URL encoder, software engineering interview.*  
> **Meta‑Title (SEO):** “Encode & Decode TinyURL (LeetCode 535) – Java, Python & C++ Solutions | Interview‑Ready Blog”

---

### 1️⃣ Problem Summary (LeetCode 535)

> **Goal:** Design a tiny URL service – `encode(longUrl)` returns a shortened URL; `decode(shortUrl)` returns the original long URL.  
> **Constraints:**  
> * `1 ≤ url.length ≤ 10⁴`  
> * The same `Codec` object is used for both encode/decode; no collisions guarantee.  
> * You may pick any encoding scheme – base‑62 strings, hashing, counter, etc.  

> **Why it matters:** URL shorteners are everywhere (TinyURL, Bit.ly, GitHub Gists). Interviewers love this problem because it tests:
> * Hashing & dictionary usage
> * Randomness & collision avoidance
> * Space/time trade‑offs
> * Practical API design

---

### 2️⃣ Common Design Approaches

| # | Approach | Pros | Cons | Typical Use‑Case |
|---|----------|------|------|------------------|
| 1 | **Random 6‑char base‑62 + HashMap** | • Extremely simple <br>• Constant‑time ops <br>• Easy to reset | • Needs collision check (rare) <br>• Not deterministic | LeetCode & small‑scale services |
| 2 | **Incremental counter + base‑62** | • No collisions, deterministic <br>• Predictable URLs | • State persistence required <br>• Longer URLs if many entries | Real production systems |
| 3 | **Hash of longUrl (e.g., MD5) + truncation** | • No storage needed (pure stateless) <br>• Fast | • Possible collisions <br>• Truncated hash may break | CDN or caching proxies |
| 4 | **Custom encoding (base‑36, base‑64, or Huffman)** | • Very short URLs <br>• Can compress data | • Implementation complexity | High‑throughput services |

> **Takeaway:** For interview coding, the *random 6‑char base‑62* approach (approach 1) is the de‑facto standard. It balances simplicity, correctness, and clear reasoning.

---

## 3️⃣ Reference Solutions

> **Note:** All solutions are self‑contained, use `HashMap`/`dict`/`unordered_map`, and keep state in a single class.

### 3.1 Java (OOP style)

```java
import java.util.HashMap;
import java.util.Random;

public class Codec {
    private static final String ALPHABET = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    private static final int CODE_LEN = 6;

    private final Random random = new Random();
    private final HashMap<String, String> map = new HashMap<>();

    /** Generate a random 6‑char key */
    private String generateKey() {
        StringBuilder sb = new StringBuilder(CODE_LEN);
        for (int i = 0; i < CODE_LEN; i++) {
            sb.append(ALPHABET.charAt(random.nextInt(ALPHABET.length())));
        }
        return sb.toString();
    }

    /** Encodes a URL to a shortened URL. */
    public String encode(String longUrl) {
        String key;
        do {
            key = generateKey();
        } while (map.containsKey(key));   // Avoid collisions
        map.put(key, longUrl);
        return "http://tinyurl.com/" + key;
    }

    /** Decodes a shortened URL to its original URL. */
    public String decode(String shortUrl) {
        String key = shortUrl.replace("http://tinyurl.com/", "");
        return map.get(key);              // Guaranteed to exist
    }
}
```

> **Complexity**  
> *Time:* `O(1)` average for both `encode` & `decode`.  
> *Space:* `O(N)` where `N` is the number of encoded URLs.

---

### 3.2 Python

```python
import random
import string

class Codec:
    _ALPHABET = string.digits + string.ascii_letters
    _CODE_LEN = 6

    def __init__(self):
        self._store = {}

    def _random_code(self) -> str:
        return ''.join(random.choice(self._ALPHABET) for _ in range(self._CODE_LEN))

    def encode(self, long_url: str) -> str:
        while True:
            code = self._random_code()
            if code not in self._store:          # Collision check
                break
        self._store[code] = long_url
        return f"http://tinyurl.com/{code}"

    def decode(self, short_url: str) -> str:
        code = short_url.replace("http://tinyurl.com/", "")
        return self._store[code]
```

> **Pythonic Touches**  
> * `string.digits + string.ascii_letters` gives the base‑62 alphabet.  
> * `self._store` is the internal dictionary.

---

### 3.3 C++

```cpp
#include <unordered_map>
#include <string>
#include <random>

class Codec {
    static constexpr const char* alphabet = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    static constexpr int codeLen = 6;

    std::unordered_map<std::string, std::string> mp;
    std::mt19937 rng{ std::random_device{}() };
    std::uniform_int_distribution<> dist{0, 61};

    std::string randCode() {
        std::string s(codeLen, '\0');
        for (int i = 0; i < codeLen; ++i) {
            s[i] = alphabet[dist(rng)];
        }
        return s;
    }

public:
    std::string encode(const std::string& longUrl) {
        std::string key;
        do {
            key = randCode();
        } while (mp.find(key) != mp.end()); // collision check
        mp[key] = longUrl;
        return "http://tinyurl.com/" + key;
    }

    std::string decode(const std::string& shortUrl) {
        std::string key = shortUrl.substr(shortUrl.find_last_of('/') + 1);
        return mp[key];  // guaranteed to exist
    }
};
```

> **Why `mt19937`?**  
> It’s a fast, high‑quality PRNG suitable for coding interviews and real services.

---

## 4️⃣ The Good, The Bad, & The Ugly

| Category | The Good | The Bad | The Ugly |
|----------|----------|---------|----------|
| **Simplicity** | Random 6‑char keys are *tiny* and easy to implement. | Need to handle rare collisions. | Over‑engineering (e.g., distributed lock, sharding) for a simple interview. |
| **Determinism** | Stateless solutions (hash‑based) guarantee reproducibility. | Random solutions produce different URLs each run → not ideal for unit tests. | Using global static RNG state can lead to flaky tests. |
| **Scalability** | HashMap works fine for millions of URLs in memory. | Memory grows linearly; not persistent. | Trying to store every key‑value pair in a file or DB inside the interview → overkill. |
| **Security** | Short, non‑guessable URLs protect against enumeration. | Random strings can still be brute‑forced in large scale. | Exposing the mapping (e.g., via debug prints) leaks data. |
| **Thread‑Safety** | Java’s `HashMap` is *not* thread‑safe; Python’s dict is also not thread‑safe. | Ignoring concurrency can cause race conditions. | In a real tinyURL service you’d need to lock or use `ConcurrentHashMap`. |
| **Real‑world concerns** | Incremental counter guarantees uniqueness and is easier to audit. | Random collisions require re‑generation logic. | Using external services (Redis, S3) for persistence adds latency and complexity. |

---

## 5️⃣ Optimizations & Variants

| Idea | Implementation | When to use |
|------|----------------|-------------|
| **Base‑62 counter** | Convert an integer counter to base‑62 string. | Deterministic URLs, easier debugging. |
| **Collision‑resistant hash** | Use `hashlib.sha256(longUrl)` → take first 6 bytes. | Stateless service; minimal storage. |
| **Prefix with domain** | Store domain in a config file, e.g., `http://tinyurl.com`. | Flexible for multiple tenants. |
| **Shorter keys** | Use 4‑char keys → 62⁴ ≈ 14 million unique combos. | If you know your URL count < 14M. |
| **Stateless** | Return `https://tinyurl.com/` + base62(sha256(longUrl)). | No storage, no decode map (but lose reverse lookup). |

---

## 6️⃣ Interview Tips

1. **Clarify requirements first**  
   * Ask if persistence is needed.  
   * Are you allowed to use external libraries?  
   * What about thread safety?

2. **Explain the design trade‑offs**  
   * Random vs counter vs hash.  
   * Collision handling strategy.

3. **Write clean code**  
   * Separate concerns: key generation, map management.  
   * Use constants for domain, alphabet, key length.

4. **Discuss edge cases**  
   * Very long URLs (10 kB).  
   * Re‑encoding the same URL multiple times.

5. **Show complexity**  
   * `O(1)` time, `O(N)` space.  
   * Potential memory bottleneck for huge N.

6. **Offer a production extension**  
   * Persist the map in a DB.  
   * Use a distributed counter.  
   * Add rate‑limiting and analytics.

---

## 7️⃣ Final Thoughts

- The *random 6‑char base‑62* solution is the textbook answer for LeetCode 535 – it balances brevity, correctness, and performance.
- In a real service, you’d lean toward a **counter‑based** approach with persistent storage (e.g., Redis) to guarantee uniqueness and allow for analytics.
- Always keep the interview’s scope in mind: show you understand trade‑offs, can write clean code, and can discuss potential real‑world extensions.

> **Pro tip for job hunting** – post these solutions on your GitHub repo, tag them with `tinyurl`, and mention them in your résumé’s “Coding Projects” section. Recruiters love seeing the exact implementation you used for a classic interview problem.

---

## 8️⃣ Want More Practice?

- **Top Interview Coding Problems** – 30+ questions covering data structures, algorithms, and system design.  
- **GitHub Repository** – `tinyurl-leetcode-535` with all language variants and unit tests.  
- **Live Coding Session** – schedule a 1‑hour mock interview on GMeet or Zoom; I’ll walk you through these solutions.

> **Get ready**: You’ve just mastered one of the *most common* interview questions. Use the insights above to ace the next tech interview!

--- 

💡 **SEO Keywords**: TinyURL design, LeetCode 535 solution, random base‑62 mapping, URL shortening, hash map interview, counter‑based URL shortener, collision avoidance, real‑world tinyURL service, coding interview patterns.  

--- 

**Good luck!** 🚀 