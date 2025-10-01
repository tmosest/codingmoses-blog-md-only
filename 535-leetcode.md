---
title: LeetCode 535. Encode and Decode TinyURL - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 535 Encode and Decode TinyURL  

| Topic | Description |
|-------|-------------|
| **Goal** | Build a *tiny URL* service that can encode any long URL into a short one and decode it back. |
| **Constraints** | `1 ≤ url.length ≤ 10⁴`, the short URL will only contain characters from the set `0‑9 a‑z A‑Z`. |
| **Input / Output** | `encode(String longUrl) → String shortUrl` <br> `decode(String shortUrl) → String longUrl` |
| **Important** | The same `Codec` object will always be used for both operations – therefore the mapping can be kept in memory. |

> **Why is this a great interview question?**  
> 1. **Systems‑design flavour** – you have to think about unique ID generation, collision avoidance, and persistence.  
> 2. **Algorithms & data‑structures** – hash maps, random ID generation, base‑62 encoding.  
> 3. **Edge cases** – collisions, very long URLs, and how to keep the solution fast.

---

## 2.  Solution Overview

The simplest, most common approach:

1. **Generate a random 6‑character key** from the 62‑character alphabet (`0‑9 a‑z A‑Z`).  
2. **Store** `key → longUrl` in a `HashMap`.  
3. **Encode**: prepend a base domain (`http://tinyurl.com/`) to the key.  
4. **Decode**: strip the domain, look up the key in the map.

Because the same object keeps the map alive, decoding is guaranteed to succeed.  
If a collision occurs we just retry with a new key.  

**Why 6 characters?**  
`62⁶ ≈ 56 800 235 584` unique combinations – enough for a personal or interview‑scale project.

---

## 3.  Time / Space Complexity

| Operation | Time | Space |
|-----------|------|-------|
| `encode` | `O(1)` on average (hash map lookup & insertion) | `O(1)` additional per URL |
| `decode` | `O(1)` (hash map lookup) | `O(1)` |
| **Total** | **`O(n)`** for `n` encode/decode calls | **`O(n)`** for stored URLs |

---

## 4.  Code Snippets

### 4.1  Java

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Random;

public class Codec {

    private static final String ALPHABET = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    private static final String DOMAIN = "http://tinyurl.com/";
    private static final int KEY_LENGTH = 6;

    private final Random rand = new Random();
    private final Map<String, String> map = new HashMap<>();

    /** Generate a random 6‑char key. */
    private String randomKey() {
        StringBuilder sb = new StringBuilder(KEY_LENGTH);
        for (int i = 0; i < KEY_LENGTH; i++) {
            sb.append(ALPHABET.charAt(rand.nextInt(ALPHABET.length())));
        }
        return sb.toString();
    }

    /** Encodes a URL to a tiny URL. */
    public String encode(String longUrl) {
        String key = randomKey();
        // Resolve collisions – in practice this loop will run only once
        while (map.containsKey(key)) {
            key = randomKey();
        }
        map.put(key, longUrl);
        return DOMAIN + key;
    }

    /** Decodes a tiny URL to its original URL. */
    public String decode(String shortUrl) {
        String key = shortUrl.substring(DOMAIN.length());
        return map.getOrDefault(key, "");
    }
}
```

---

### 4.2  Python

```python
import random
import string
from typing import Dict

class Codec:
    ALPHABET = string.digits + string.ascii_lowercase + string.ascii_uppercase
    DOMAIN = "http://tinyurl.com/"
    KEY_LENGTH = 6

    def __init__(self):
        self.store: Dict[str, str] = {}

    def _rand_key(self) -> str:
        return "".join(random.choice(self.ALPHABET) for _ in range(self.KEY_LENGTH))

    def encode(self, longUrl: str) -> str:
        key = self._rand_key()
        while key in self.store:            # very unlikely collision
            key = self._rand_key()
        self.store[key] = longUrl
        return f"{self.DOMAIN}{key}"

    def decode(self, shortUrl: str) -> str:
        key = shortUrl.replace(self.DOMAIN, "", 1)
        return self.store.get(key, "")
```

---

### 4.3  C++

```cpp
#include <unordered_map>
#include <string>
#include <random>
#include <sstream>

class Codec {
private:
    static const std::string alphabet;
    static constexpr const char* DOMAIN = "http://tinyurl.com/";
    static constexpr int KEY_LEN = 6;

    std::unordered_map<std::string, std::string> table;
    std::mt19937 rng{std::random_device{}()};

    std::string randomKey() {
        std::uniform_int_distribution<int> dist(0, alphabet.size() - 1);
        std::string key;
        key.reserve(KEY_LEN);
        for (int i = 0; i < KEY_LEN; ++i)
            key.push_back(alphabet[dist(rng)]);
        return key;
    }

public:
    /** Encodes a URL to a tiny URL. */
    std::string encode(const std::string& longUrl) {
        std::string key = randomKey();
        while (table.find(key) != table.end()) {   // collision guard
            key = randomKey();
        }
        table[key] = longUrl;
        std::ostringstream oss;
        oss << DOMAIN << key;
        return oss.str();
    }

    /** Decodes a tiny URL to its original URL. */
    std::string decode(const std::string& shortUrl) {
        std::string key = shortUrl.substr(std::strlen(DOMAIN));
        auto it = table.find(key);
        return it != table.end() ? it->second : "";
    }
};

const std::string Codec::alphabet =
    "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
```

---

## 5.  Blog Article – “Encode & Decode TinyURL: The Good, the Bad, and the Ugly”

> **Keywords** – *Encode and Decode TinyURL*, *LeetCode 535*, *URL shortener*, *coding interview*, *Java*, *Python*, *C++*, *hashmap*, *random key generation*, *time complexity*, *space complexity*  

---

### 5.1  Introduction

When you type a long URL into a browser, it can be intimidating – but what if a tiny link could carry the same meaning?  
The **LeetCode 535 – Encode and Decode TinyURL** problem forces you to build that miniature service, and it’s a *gold‑mine* for interviews. In this article we walk through the classic solution, dissect its strengths and pitfalls, and finally hand‑craft code in **Java, Python, and C++** that you can paste straight into a coding test.

---

### 5.2  The “Good” – Simplicity & Speed

1. **One‑pass O(1) operations** – Inserting a mapping and retrieving it from a hash map is practically instantaneous.  
2. **No external dependencies** – All we need is a random generator and a dictionary; no database or file I/O.  
3. **Collision resilience** – The 6‑character base‑62 key space gives us ~56 billion unique IDs.  
4. **Deterministic decode** – Since the same `Codec` instance holds the state, decoding is guaranteed to work for any encoded URL.  

---

### 5.3  The “Bad” – Collision & Scalability

| Issue | Why it matters | Mitigation |
|-------|----------------|------------|
| **Random collisions** | Rare, but possible. | Loop until a free key is found. |
| **Memory footprint** | Every URL stays in memory. | For production, persist to a database or use a CDN. |
| **No persistence** | Restarting the service loses data. | Use a persistent store or a distributed cache. |
| **Fixed key length** | 6 chars limit to ~56B URLs – fine for an interview, not for a real tinyURL service. | Use incremental IDs and base‑62 encode or increase key length. |

---

### 5.4  The “Ugly” – Edge Cases & Security

- **Very long URLs** – Up to 10 000 chars, which is fine for hashing but can bloat memory.  
- **URL normalization** – `http://a.com` vs `https://a.com/` are treated as distinct keys.  
- **Security** – Random keys are not cryptographically secure. Attackers can guess a URL if the key space is small.  
- **Error handling** – Current code returns an empty string for unknown keys. A robust service would throw an exception or return `null`.

---

### 5.5  Putting It All Together – Full Code

(See the code blocks above for Java, Python, and C++.)

---

### 5.6  Time & Space Complexity (Quick Cheat‑Sheet)

- `encode` → **O(1)** time, **O(1)** extra space per call.  
- `decode` → **O(1)** time, **O(1)** space.  
- Total memory for `n` URLs → **O(n)**.

---

### 5.7  Take‑aways for Your Next Coding Interview

1. **Explain your data‑structure choice** – “I’m using a hash map because it gives constant‑time look‑ups.”  
2. **Mention collision handling** – “The probability is negligible, but we’ll loop until we find a free key.”  
3. **Show awareness of scalability** – “In a real system, we’d store mappings in Redis or a database.”  
4. **Add a tiny demo** – Paste the snippet into the editor and test quickly.  

---

### 5.8  Final Thoughts

The **Encode and Decode TinyURL** problem may look simple at first glance, but it’s a microcosm of real‑world URL shorteners. By mastering the *good*, *bad*, and *ugly* parts, you’ll not only ace the LeetCode challenge but also impress interviewers with your depth of understanding.

Happy coding – and may your URLs always be tiny and your solutions massive! 🚀

---