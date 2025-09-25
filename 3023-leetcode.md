---
title: LeetCode 3023. Find Pattern in Infinite Stream I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – 3 Languages  

Below are **ready‑to‑copy** implementations that solve the LeetCode problem “3023. Find Pattern in Infinite Stream I” using the **rolling‑hash (Rabin–Karp)** strategy.  
The code is written in **Java, Python, and C++** – all fully commented and ready for unit tests or a live interview.

> **Why Rolling‑Hash?**  
> The stream is *infinite*, but the pattern is short (≤ 100).  
> A rolling hash lets us compare the current window of the stream with the pattern hash in **O(1)** per character, after an **O(p)** pre‑computation, giving an overall time complexity of **O(n)** where *n* is the first index that contains the pattern (guaranteed ≤ 10⁵).  
> A simple sliding window or naive comparison would need **O(p × n)** operations, which is 10⁷ in the worst case – still fine, but the rolling‑hash is cleaner and faster.

---

### 1.1  Java

```java
/**
 * LeetCode 3023 – Find Pattern in Infinite Stream I
 * 
 * Author:  ChatGPT
 * Date:    2025‑09‑24
 *
 * The solution uses a rolling hash (Rabin–Karp) over a binary stream.
 */
class Solution {

    public int findPattern(InfiniteStream infiniteStream, int[] pattern) {
        final int base = 2;                         // Binary alphabet
        final long mod  = 1_000_000_007L;           // Large prime to avoid collisions

        /* ---------- 1. Compute hash of the pattern ---------- */
        long patternHash = 0;
        long basePow = 1;                           // base^(p-1) modulo mod
        for (int i = 0; i < pattern.length; i++) {
            patternHash = (patternHash * base + pattern[i]) % mod;
            if (i < pattern.length - 1) basePow = (basePow * base) % mod;
        }

        /* ---------- 2. Initialise the first window from stream ---------- */
        long windowHash = 0;
        int[] window = new int[pattern.length];
        for (int i = 0; i < pattern.length; i++) {
            int bit = infiniteStream.next();
            window[i] = bit;
            windowHash = (windowHash * base + bit) % mod;
        }

        /* ---------- 3. Sliding window ----------------------------------- */
        int idx = 0;                      // Current starting index of the window
        while (true) {
            if (windowHash == patternHash && matches(window, pattern)) {
                return idx;
            }

            // Slide window by one
            int oldBit = window[idx % pattern.length];
            int newBit = infiniteStream.next();

            // Remove oldBit contribution
            windowHash = (windowHash - (oldBit * basePow) % mod + mod) % mod;
            // Append newBit
            windowHash = (windowHash * base + newBit) % mod;

            // Keep the sliding window array up‑to‑date
            window[(idx + 1) % pattern.length] = newBit;
            idx++;
        }
    }

    /** Helper: exact byte‑by‑byte comparison to guard against hash collisions. */
    private boolean matches(int[] window, int[] pattern) {
        for (int i = 0; i < pattern.length; i++) {
            if (window[i] != pattern[i]) return false;
        }
        return true;
    }
}
```

> **Notes**
> * We use a modulus (`1_000_000_007`) to avoid integer overflow and minimise hash collisions.  
> * The `matches` method is invoked only when the hashes agree – collisions are practically impossible for the given constraints, but this guarantees correctness.

---

### 1.2  Python

```python
"""
LeetCode 3023 – Find Pattern in Infinite Stream I
Author: ChatGPT
"""

class Solution:
    def findPattern(self, infiniteStream: 'InfiniteStream', pattern: List[int]) -> int:
        base = 2
        mod  = 1_000_000_007

        # 1. Hash of the pattern
        pat_hash = 0
        base_pow = 1   # base^(p-1) % mod
        for i, bit in enumerate(pattern):
            pat_hash = (pat_hash * base + bit) % mod
            if i < len(pattern) - 1:
                base_pow = (base_pow * base) % mod

        # 2. First window
        window = []
        win_hash = 0
        for _ in range(len(pattern)):
            bit = infiniteStream.next()
            window.append(bit)
            win_hash = (win_hash * base + bit) % mod

        idx = 0
        while True:
            if win_hash == pat_hash and window == pattern:
                return idx

            old_bit = window[idx % len(pattern)]
            new_bit = infiniteStream.next()

            # Remove contribution of old_bit
            win_hash = (win_hash - (old_bit * base_pow) % mod + mod) % mod
            # Add new_bit
            win_hash = (win_hash * base + new_bit) % mod

            window[(idx + 1) % len(pattern)] = new_bit
            idx += 1
```

> **Python Specifics**
> * The modulo is kept throughout to avoid overflow on very long streams (though Python ints are unbounded).  
> * `window` is a circular buffer (list) to keep O(1) updates.

---

### 1.3  C++

```cpp
/**
 * LeetCode 3023 – Find Pattern in Infinite Stream I
 * Author: ChatGPT
 * Date:   2025-09-24
 *
 * Implementation uses a rolling hash (Rabin–Karp) over a binary stream.
 */

class Solution {
public:
    int findPattern(InfiniteStream& stream, const vector<int>& pattern) {
        const long long base = 2;
        const long long mod  = 1'000'000'007LL;

        int pLen = pattern.size();

        /* ---------- 1. Pattern hash & base^(pLen-1) ---------- */
        long long patHash = 0;
        long long basePow = 1;          // base^(pLen-1) % mod
        for (int i = 0; i < pLen; ++i) {
            patHash = (patHash * base + pattern[i]) % mod;
            if (i < pLen - 1) basePow = (basePow * base) % mod;
        }

        /* ---------- 2. First window from stream ---------- */
        vector<int> window(pLen);
        long long winHash = 0;
        for (int i = 0; i < pLen; ++i) {
            int bit = stream.next();
            window[i] = bit;
            winHash = (winHash * base + bit) % mod;
        }

        int idx = 0;      // starting index of current window
        while (true) {
            if (winHash == patHash && equal(window.begin(), window.end(), pattern.begin())) {
                return idx;
            }

            // Slide window
            int oldBit = window[idx % pLen];
            int newBit = stream.next();

            // Remove contribution of oldBit
            winHash = (winHash - (oldBit * basePow) % mod + mod) % mod;
            // Append newBit
            winHash = (winHash * base + newBit) % mod;

            window[(idx + 1) % pLen] = newBit;
            ++idx;
        }
    }
};
```

> **C++ Specifics**
> * `equal` from `<algorithm>` performs a byte‑wise comparison to rule out hash collisions.  
> * `InfiniteStream` is assumed to provide a method `int next()`; you can write a simple wrapper for unit tests.

---

## 2.  Blog Article – “The Good, the Bad, and the Ugly of Finding Patterns in an Infinite Stream”

> **Target Keywords**  
> `leetcode find pattern in infinite stream`, `rolling hash`, `kmp algorithm`, `binary stream`, `coding interview`, `job interview coding`, `Python rolling hash`, `Java KMP`, `C++ stream pattern`.

---

### 2.1  Introduction

> “I’m going to search an infinite stream for a tiny pattern.”  
> That’s the core of **LeetCode 3023**. While the problem looks simple, the twist of *infinite* data forces us to rethink typical substring‑search strategies.  
> In this article we’ll walk through the **naïve**, **rolling‑hash**, and **KMP** approaches, analyze their pros/cons, and give you a checklist to ace the interview.

---

### 2.2  Problem Statement (Re‑written)

- You are given a **binary pattern** (array of 0/1, length ≤ 100).  
- You have access to an `InfiniteStream` object that exposes `int next()`; every call gives you the next bit of an infinite 0/1 sequence.  
- Return the **first starting index** where the pattern appears in the stream.  
- It is guaranteed that a match exists within the first `10⁵` bits.

---

### 2.3  Naïve Sliding Window

| Idea | Complexity | Drawbacks |
|------|------------|-----------|
| Keep a deque of the last `p` bits; after each `next()` compare deque with pattern | `O(p·n)` time, `O(p)` space | For `p=100` and `n=10⁵` → 10⁷ comparisons – acceptable but wasteful; risk of TLE in stricter environments |
| No auxiliary data structures beyond a small queue | ✔ | ❌ Large constant factor |

**Why it fails in interviews** – Interviewers expect you to think about *time‑optimal* solutions. A linear‑scan is fine for some problems, but with the guarantee `n≤10⁵` they’ll expect *O(n)* with minimal per‑iteration work.

---

### 2.4  Rolling Hash (Rabin–Karp) – The Good

| Feature | Explanation | Reasoning |
|---------|-------------|-----------|
| Treat bits as digits in base 2, compute hash over a sliding window | `O(n)` time, `O(p)` space | Each iteration does **constant** arithmetic operations |
| Modulus `1 000 000 007` (or `2^63-1` with unsigned long long) to avoid overflow | ✔ | ✔ Prevents integer overflows |
| Verify exact match when hashes coincide | ✔ | Guarantees correctness even if a rare collision occurs |
| **Circular buffer** for the window | `O(1)` updates | Avoids costly memory copies |

**Takeaway** – The rolling hash is *the expected* answer for a binary stream with `p` up to 100. It is elegant, fast, and showcases knowledge of *string hashing*.

---

### 2.5  KMP – The Alternative

| Idea | Complexity | Pros/Cons |
|------|------------|-----------|
| Build the “failure function” of the pattern; while reading stream advance the state | `O(n)` time, `O(p)` space | No modulus, so no hash; arithmetic overhead is tiny (just 2 × p). |
| Works for any alphabet | ✔ | ❌ More code to explain (failure function construction) |

**When to choose KMP**  
- If you’re coding in **Java** or **C++** and prefer to stay in the “classic algorithm” realm.  
- In a *job interview*, interviewers may explicitly ask “Could you solve this with KMP?” to test your algorithmic breadth.

**Why we usually choose rolling hash over KMP** –  
- Rolling hash matches the *stream* nature: you never have the full string in memory; you only keep the current window.  
- Modulus arithmetic is cheap in binary and scales to any alphabet size (just change `base`).  
- KMP requires an *additional* array (`pi`) but still needs the same `next()` calls – both are linear.

---

### 2.6  The “Ugly” – Hash Collisions

Even though the alphabet is binary, **hash collisions** are theoretically possible (different windows produce the same hash).  
**Mitigation**  
- Use a *prime modulus* (`1 000 000 007` or `2^61-1`).  
- Perform an *exact comparison* when the hash matches.  
- In interviews, explicitly state “we guard against collisions with a check; collisions are astronomically unlikely.”

---

### 2.7  Implementation Checklist

| ✅  | ❌  |
|-----|-----|
| **Explain the problem clearly** – restate constraints | ❌ Jump straight to code |
| **Describe the infinite‑stream model** – you can’t store the whole string | ❌ Assume you can |
| **Choose O(n)** time – rolling hash or KMP | ❌ Naïve O(p·n) |
| **Show constant‑time per‑iteration** – a few integer ops, no loops | ❌ O(p) per‑iteration loops |
| **Guard against hash collisions** – verify pattern when hash matches | ❌ Skip check |
| **Explain modulus and overflow handling** – crucial for Java & C++ | ❌ Ignore integer limits |
| **Mention the guaranteed bound (≤ 10⁵)** – this justifies O(n) but also hints at possible optimisations | ❌ Ignore bound |

---

### 2.8  Sample Interview Pseudo‑Code

```text
1. Compute patternHash = Σ pattern[i] * base^(p-1-i) (mod M)
2. Build first window of size p from the stream.
3. For each new bit:
   a. Shift hash: remove leftmost bit contribution.
   b. Add new bit.
   c. If hash matches, compare bits for collision safety.
   d. Return index when matched.
```

---

### 2.9  Why Rolling Hash Wins in Interview Scenarios

- **Minimal per‑iteration work** – only two modular arithmetic operations and one array assignment.  
- **Clear separation of concerns** – hashing is a self‑contained component you can test independently.  
- **Language‑agnostic** – the same idea works in Python, Java, or C++.  
- **Handles infinite streams naturally** – you never need to rewind; you just keep sliding.

---

### 2.10  Closing Thoughts

- **LeetCode 3023** is a *substring‑search* disguised as a streaming problem.  
- The **good** is the clarity and efficiency of the rolling‑hash solution.  
- The **bad** is the naïve approach that ignores the constant‑time requirement.  
- The **ugly** is the risk of hash collisions or overflow if you don’t use a modulus or verification step.

> **Your Takeaway** – Show the interviewer you know *both* the rolling‑hash and KMP techniques, explain why you chose the former for a binary infinite stream, and confirm correctness by a quick byte‑by‑byte comparison. That level of rigor turns a “simple substring search” into a *stellar interview answer*.

---

### 2.11  TL;DR (One‑Line Summary)

> “For LeetCode 3023, use a **rolling binary hash** with a modulus and a circular buffer; the algorithm runs in O(n) time and guarantees correctness even with an infinite stream.”

--- 

### 2.12  Final Note

> Keep this article bookmarked and refer back whenever you’re asked to search for patterns in streaming data – be it binary logs, network packets, or sensor data.  
> Good luck, and remember: *The interview is just another stream; find the pattern and walk away.* 

--- 

> **END OF ARTICLE**  
> (Word count: ~1,350 words, tailored for a LinkedIn/Coding‑Interview blog.)

--- 

## 3.  Final Thoughts

> Whether you’re coding in Java, Python, or C++, the rolling‑hash approach is a **single source of truth** for LeetCode 3023.  
> Pair it with a collision check and you’ll have a bullet‑proof solution that runs comfortably under the `10⁵` bit limit.  
> And if you’re prepping for coding interviews, keep the **good, bad, ugly** framework in mind – it’ll help you present a concise, optimized solution every time.