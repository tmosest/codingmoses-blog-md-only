---
title: LeetCode 535. Encode and Decode TinyURL - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Encode and Decode TinyURL  
## The Good, the Bad, and the Ugly – A Job‑Interview‑Ready Guide

> **Meta description** – Want to ace the “TinyURL” coding interview?  
> Learn the most common LeetCode 535 solution, the pros & cons of each approach, and how to write production‑ready Java, Python, and C++ code that will impress recruiters.

---

## 1. Problem Recap

> **LeetCode 535 – Encode and Decode TinyURL**  
> Implement a class that can **encode** a long URL into a short one and **decode** that short URL back to its original form.

```text
Solution obj = new Solution();
String tiny  = obj.encode(longUrl);   // http://tinyurl.com/xxxxxx
String back  = obj.decode(tiny);      // returns longUrl
```

- No fixed algorithm is required – you just need a reversible mapping.  
- The same `Solution` instance will perform the encoding/decoding pair.  
- URL length ≤ 10⁴, guaranteed valid.

---

## 2. Why This Matters in a Job Interview

- **Systems Design + Coding** – The problem is the “toy” version of a real‑world URL shortener.
- **Trade‑off discussion** – Candidates can show understanding of randomness, collisions, persistence, scalability, and security.
- **Language agility** – Interviewers may ask for Java/Python/C++ or even a mixed‑language stack.

---

## 3. The Classic “Good” Solution – Incremental ID + Base62

### 3.1. Core Idea

1. Keep a monotonically increasing integer (`id`).  
2. Convert that integer to a **Base‑62** string (`[0‑9a‑zA‑Z]`).  
3. Store `id → longUrl` in a `HashMap`.  
4. To decode, strip the domain, look up the id, and return the URL.

### 3.2. Why It’s Good

| Aspect | Benefit |
|--------|---------|
| **Deterministic** | No random collisions to handle. |
| **Scalable** | IDs grow only until the counter overflows. |
| **Fast** | O(1) encode/decode (hash look‑up + base‑62 conversion). |
| **Easy to audit** | Short URL length is predictable. |

### 3.3. Potential Drawbacks

| Drawback | Why it matters |
|----------|----------------|
| **Predictable URLs** | Easy to guess the next short link (security concern). |
| **Limited by counter size** | 32‑bit int → ~4 B URLs; 64‑bit int → practically unlimited. |
| **Not “random”** | Might reveal usage patterns. |

### 3.4. “Ugly” Edge Cases

- Handling counter wrap‑around (overflow).  
- Thread‑safety for concurrent requests (needs `AtomicLong`).  
- Persisting state across restarts (requires DB or file).  

---

## 4. Alternative “Bad” Solution – Random Short Code

1. Generate a random 6‑char string from Base‑62.  
2. Check for collision; regenerate if necessary.  
3. Store `code → longUrl` in a `HashMap`.  

Pros:  
- **Highly random** – difficult to predict.  
- Small code size (6 chars).

Cons:  
- **Collision probability** grows with more URLs.  
- Need to generate repeatedly until a free code is found.  
- Not deterministic; debugging harder.  

---

## 5. Final Verdict

The **incremental ID + Base62** approach is the most common interview choice because it is easy to explain, implement, and reason about. It gives you the opportunity to discuss:

- *Base‑62 conversion* (why it matters).
- *Collision handling* (none needed).
- *Security implications* (predictability, custom salts).
- *Scalability* (sharding by prefix, CDN cache).

---

## 6. Code – Java, Python, C++

Below are three clean implementations, one in each language. Each follows the deterministic ID + Base62 strategy.

### 6.1. Java

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.atomic.AtomicLong;

public class Codec {

    // Characters used for Base62 encoding
    private static final String BASE62 = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    private static final String DOMAIN = "http://tinyurl.com/";

    private final AtomicLong counter = new AtomicLong(1);   // start at 1
    private final Map<Long, String> idToUrl = new HashMap<>();

    // Encode a long URL to a tiny URL
    public String encode(String longUrl) {
        long id = counter.getAndIncrement();          // thread‑safe
        idToUrl.put(id, longUrl);
        return DOMAIN + toBase62(id);
    }

    // Decode a tiny URL to its original long URL
    public String decode(String shortUrl) {
        String code = shortUrl.replace(DOMAIN, "");
        long id = fromBase62(code);
        return idToUrl.get(id);
    }

    // Helper: Convert long to Base62 string
    private String toBase62(long num) {
        StringBuilder sb = new StringBuilder();
        while (num > 0) {
            int rem = (int) (num % 62);
            sb.append(BASE62.charAt(rem));
            num /= 62;
        }
        return sb.reverse().toString();   // most significant digit first
    }

    // Helper: Convert Base62 string back to long
    private long fromBase62(String str) {
        long num = 0;
        for (int i = 0; i < str.length(); i++) {
            num = num * 62 + BASE62.indexOf(str.charAt(i));
        }
        return num;
    }
}
```

**Complexity**  
- `encode`: O(log₆₂ id) for Base62 conversion + O(1) map insertion.  
- `decode`: O(log₆₂ id) for Base62 conversion + O(1) map lookup.  
Space: O(n) where *n* is number of URLs stored.

---

### 6.2. Python

```python
import string
import threading

class Codec:
    BASE62 = string.digits + string.ascii_lowercase + string.ascii_uppercase
    DOMAIN = "http://tinyurl.com/"

    def __init__(self):
        self.counter = 1
        self.lock = threading.Lock()
        self.id_to_url = {}

    def _to_base62(self, num: int) -> str:
        """Convert integer to Base62 string."""
        if num == 0:
            return self.BASE62[0]
        res = []
        while num:
            res.append(self.BASE62[num % 62])
            num //= 62
        return ''.join(reversed(res))

    def _from_base62(self, s: str) -> int:
        """Convert Base62 string back to integer."""
        num = 0
        for ch in s:
            num = num * 62 + self.BASE62.index(ch)
        return num

    def encode(self, longUrl: str) -> str:
        """Encode a URL to a shortened URL."""
        with self.lock:                  # thread‑safe counter increment
            cur_id = self.counter
            self.counter += 1
        self.id_to_url[cur_id] = longUrl
        return self.DOMAIN + self._to_base62(cur_id)

    def decode(self, shortUrl: str) -> str:
        """Decode a shortened URL to its original URL."""
        code = shortUrl.replace(self.DOMAIN, "")
        cur_id = self._from_base62(code)
        return self.id_to_url.get(cur_id, "")
```

---

### 6.3. C++

```cpp
#include <unordered_map>
#include <string>
#include <mutex>

class Codec {
private:
    static constexpr char const *BASE62 = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
    static constexpr const char *DOMAIN = "http://tinyurl.com/";
    unsigned long long counter = 1;          // 64‑bit to avoid overflow
    std::unordered_map<unsigned long long, std::string> idToUrl;
    std::mutex mtx;                          // for thread safety

    std::string toBase62(unsigned long long num) {
        std::string res;
        if (num == 0) return std::string(1, BASE62[0]);
        while (num) {
            res.push_back(BASE62[num % 62]);
            num /= 62;
        }
        std::reverse(res.begin(), res.end());
        return res;
    }

    unsigned long long fromBase62(const std::string &s) {
        unsigned long long num = 0;
        for (char ch : s) {
            num = num * 62 + std::string(BASE62).find(ch);
        }
        return num;
    }

public:
    std::string encode(std::string longUrl) {
        std::unique_lock<std::mutex> lock(mtx);
        unsigned long long curId = counter++;
        lock.unlock();

        idToUrl[curId] = std::move(longUrl);
        return std::string(DOMAIN) + toBase62(curId);
    }

    std::string decode(std::string shortUrl) {
        std::string code = shortUrl.substr(std::strlen(DOMAIN));
        unsigned long long curId = fromBase62(code);
        auto it = idToUrl.find(curId);
        return it != idToUrl.end() ? it->second : "";
    }
};
```

---

## 7. Discussion – Good, Bad, Ugly

| Perspective | What the interviewer is looking for |
|-------------|-------------------------------------|
| **Good** | Clean algorithm, correct use of data structures, O(1) time, no collisions. |
| **Bad** | Over‑engineering (e.g., random collisions, DB access when not needed). |
| **Ugly** | Ignoring thread safety, using global static state that cannot be reset between tests. |

### Common Interview Pitfalls

1. **Hard‑coding the domain** – The interview might test different domains. Use a constant or pass it as a parameter.  
2. **Ignoring counter overflow** – With 32‑bit ints you hit 4 B URLs. Use `long`/`unsigned long long`.  
3. **Not handling empty strings** – `decode` should handle invalid inputs gracefully.  
4. **Assuming `String.hashCode()` is safe** – Hash codes can collide; we store mapping ourselves.  

### Security Talking Points

- *Salting* the ID before Base62 conversion (e.g., XOR with a secret).  
- *Custom short code generation* for special users.  
- *Rate limiting* or *CAPTCHA* to prevent abuse.

---

## 8. Take‑Away for Your Next Interview

1. Pick a deterministic solution, explain Base62, highlight scalability.  
2. Discuss **trade‑offs** (predictability vs randomness).  
3. Show awareness of **thread safety** and **persistence**.  
4. Be ready to adapt the code for **Python/Pythonic** or **C++/STL** patterns.  
5. Prepare to talk about **deployment**: how would you scale this across servers?  

---

## 8.1. Quick Test in Each Language

```python
codec = Codec()
tiny = codec.encode("https://leetcode.com/problems/design-tinyurl")
print(tiny)                   # http://tinyurl.com/1
print(codec.decode(tiny))     # https://leetcode.com/problems/design-tinyurl
```

Works identically in Java/C++ after copying the class name.

---

## 8.2. Next Steps

- **Persistence** – Use a DB or key‑value store.  
- **Cache** – Put most common URLs in Redis.  
- **Analytics** – Count visits, time‑to‑first‑click.  
- **Security** – Add cryptographic signatures or HMAC to the short code.

---

## 8.3. Resources for Further Learning

- [Base‑62 Encoding in JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/ToString)  
- [LeetCode – Design TinyURL](https://leetcode.com/problems/design-tinyurl/)  
- [Google's URL Shortener Architecture](https://research.google/pubs/pub36128/)  
- [Scalable URL Shortener in Go – A Production‑Ready Example](https://github.com/benbjohnson/shortener)  

---

## 8.4. Final Words

A concise, deterministic solution showcases algorithmic prowess while opening a broader discussion on systems design, concurrency, and security. Use the code snippets above as a starting point, adapt them for the specific language and environment, and be ready to talk through every design decision. Good luck on your next technical interview! 🚀

--- 

*Prepared by an AI trained on millions of interview questions – for more career‑boosting content, subscribe to our newsletter!*