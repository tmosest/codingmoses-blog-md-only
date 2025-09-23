---
title: LeetCode 2156. Find Substring With Given Hash Value - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Code – Three Languages

Below you will find clean, production‑ready solutions for LeetCode **2156 – Find Substring With Given Hash Value** in **Java**, **Python**, and **C++**.  
All three use the same idea – a *rolling hash* computed *backwards* (right‑to‑left) – so you can pick and paste the one that matches your stack.

> **Tip** – When you submit to LeetCode always double‑check that you use **long** (64‑bit) for the intermediate hash.  
> The product `val * power` can easily overflow a 32‑bit int when `modulo` ≈ 10⁹.

---

### ⚙️  Java – `Solution.java`

```java
class Solution {
    public String subStrHash(String s, int power, int modulo, int k, int hashValue) {
        // We walk from the end of the string to the start.
        long curHash = 0;          // current rolling hash (modulo)
        long powerK   = 1;         // power^k % modulo (will grow as we move left)
        int n = s.length();
        int answer = 0;            // index of the first matching substring

        // Pre‑compute power^k % modulo while walking
        for (int i = n - 1; i >= 0; i--) {
            // Add the new character (current position)
            curHash = (curHash * power + (s.charAt(i) - 'a' + 1)) % modulo;

            // If we already passed k characters, remove the oldest one
            if (i + k < n) {
                // oldest char = s.charAt(i + k)
                long remove = ((long)(s.charAt(i + k) - 'a' + 1) * powerK) % modulo;
                curHash = (curHash - remove + modulo) % modulo;   // keep it positive
            }

            // Once we hit the first k‑length window, record it
            if (i + k <= n) {
                // Remember this index only if the hash matches.
                // Because we are moving *backwards*, the last hit is the
                // earliest substring in the original order.
                if (curHash == hashValue) {
                    answer = i;
                }
            }

            // Grow powerK for the next removal (next iteration moves one more step left)
            powerK = (powerK * power) % modulo;
        }

        return s.substring(answer, answer + k);
    }
}
```

> **Why walk from right to left?**  
> Division (by `power`) is impossible under modulo because `modulo` may not be coprime with `power`.  
> Walking backwards lets us *multiply* instead of *divide*, which keeps the math simple.

---

### ⚙️  Python – `solution.py`

```python
class Solution:
    def subStrHash(self, s: str, power: int, modulo: int,
                   k: int, hashValue: int) -> str:

        cur_hash = 0           # current hash (modulo)
        power_k  = 1           # power^k % modulo
        n = len(s)
        answer = 0

        # Walk from the end to the beginning
        for i in range(n - 1, -1, -1):
            cur_hash = (cur_hash * power + (ord(s[i]) - 96)) % modulo

            if i + k < n:               # we have a full window, drop oldest
                remove = ((ord(s[i + k]) - 96) * power_k) % modulo
                cur_hash = (cur_hash - remove) % modulo

            # remember *last* index that matched – that is the earliest substring
            if cur_hash == hashValue:
                answer = i

            power_k = (power_k * power) % modulo

        return s[answer:answer + k]
```

> `ord(s[i]) - 96` is a quick way to compute `val(s[i])` (`'a'` → 1, `'b'` → 2 …).

---

### ⚙️  C++ – `Solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string subStrHash(string s, int power, int modulo,
                      int k, int hashValue) {
        long long curHash = 0;          // current rolling hash (modulo)
        long long powerK  = 1;          // power^k % modulo
        int n = (int)s.size();
        int answer = 0;                 // index of the first matching substring

        for (int i = n - 1; i >= 0; --i) {
            // Add the new character at position i
            curHash = (curHash * power + (s[i] - 'a' + 1)) % modulo;

            // Remove the character that is now out of the window
            if (i + k < n) {
                long long remove = ((long long)(s[i + k] - 'a' + 1) * powerK) % modulo;
                curHash = (curHash - remove + modulo) % modulo;
            }

            if (curHash == hashValue)          // remember last hit
                answer = i;

            powerK = (powerK * power) % modulo;
        }

        return s.substr(answer, k);
    }
};
```

> `substr` is equivalent to `substring` in Java – both take *zero‑based* indices.

---

## 2️⃣  Blog Post – SEO‑Optimised & Job‑Interview Friendly

> **Title (H1)**  
> **“LeetCode 2156 – Find Substring With Given Hash Value: Rolling‑Hash + Sliding‑Window Solutions (Java, Python, C++)”**

---

### 📚  Problem Summary

In LeetCode’s **2156**, you’re given a string `s`, an integer `power`, a large integer `modulo`, a window length `k`, and a target hash value `hashValue`.  
You need to return the **first** substring of length `k` whose *hash* (defined below) equals `hashValue`.  

**Hash definition**

```
hash(s[i … i + k – 1], power, modulo) =
    ( val(s[i])   * power^0
   + val(s[i+1]) * power^1
   + … + val(s[i+k-1]) * power^(k-1) ) % modulo
```

`val(c)` = (`'a'` → 1, `'b'` → 2 …).

---

### 🔍  Why a Rolling Hash?

| ✅ **Pros** | ❌ **Cons** |
|-------------|------------|
| • **O(n)** time – only one pass over `s` (≤ 10⁵) | • Requires careful handling of **modular arithmetic**; negative values after subtraction |
| • **O(1)** auxiliary space (just a few longs) | • If you compute from left‑to‑right you need a modular inverse of `power`, which is expensive/complicated |
| • Works for *any* `modulo`, even non‑coprime with `power` | • Overflow risk if you use 32‑bit ints for intermediate products |

---

### 🚀  The Backward‑to‑Left Technique

1. **Pre‑compute** `power^k mod modulo` while walking left.  
   This value (`powerK`) lets you *subtract* the character that leaves the window in O(1).

2. **Start from the end** of the string.  
   For each character `c = s[i]`:
   * `curHash = (curHash * power + val(c)) % modulo` –‑ add the new character.
   * If we have already processed at least `k` characters, subtract the *oldest* one:  
     `remove = val(s[i+k]) * powerK % modulo`  
     `curHash = (curHash - remove + modulo) % modulo` (add `modulo` before `%` to keep it positive).

3. **Record the last index** where `curHash == hashValue`.  
   Because we walk from the right, the *last* hit in this pass is the *first* matching substring in forward order.

---

### 📈  Complexity

- **Time** : `O(n)` – one pass over `s`.  
- **Space** : `O(1)` – a handful of long variables.

---

### 🛠️  Common Pitfalls & Fixes

| Issue | Fix |
|-------|-----|
| **Overflow** (`val * power`) | Use `long` (64‑bit). `((long)val * power) % modulo`. |
| **Negative modulo** after subtraction | Add `modulo` before `%` : `(curHash - remove + modulo) % modulo`. |
| **Off‑by‑one** in substring indices | Remember `answer` is the *start* of the window; `s.substr(answer, k)` (C++/Python) or `s.substring(answer, answer + k)` (Java). |
| **Large `modulo` ≈ 10⁹** | Ensure you don’t cast the product back to `int` before `% modulo`. |

---

### 🔍  Sample Test Case

| Input | `s` | `power` | `modulo` | `k` | `hashValue` | Output |
|-------|-----|---------|----------|-----|-------------|--------|
| `"abcd"` | 1 | 100 | 3 | 2 | 4 | `"ab"` |
| `"abcd"` | 2 | 1000 | 3 | 2 | 2 | `"bc"` |
| `"a"` | 1 | 1000 | 1 | 1 | 1 | `"a"` |

Run the code in LeetCode or your favourite IDE to verify.

---

## 3️⃣  Blog – “Mastering LeetCode 2156: A Rolling‑Hash Interview Cheat Sheet”

> **Keywords** – `LeetCode 2156`, `Find Substring With Given Hash Value`, `rolling hash`, `sliding window`, `Java solution`, `Python solution`, `C++ solution`, `hash function`, `job interview algorithm`, `coding interview practice`.

---

### 📖  Introduction

> *“Hash it up, slide it forward – the LeetCode 2156 challenge is a textbook case of rolling hash and sliding window. Master it, and you’ll ace a whole new set of interview questions.”*

If you’re preparing for a data‑structures interview or a *software engineering* role, this problem is a perfect showcase of your ability to handle **modular arithmetic** and **efficient string processing**. The following article walks you through the **good**, **bad**, and **ugly** parts of solving 2156, gives you the full code in three languages, and explains why it’s a great interview showcase.

---

### 🧩  Problem Recap

> **Input**  
> - `s` – a string of lowercase letters (`1 ≤ |s| ≤ 10⁵`)  
> - `power` – base of the hash (`2 ≤ power ≤ 10⁶`)  
> - `modulo` – modulus (`2 ≤ modulo ≤ 10⁹`)  
> - `k` – length of the substring window (`1 ≤ k ≤ |s|`)  
> - `hashValue` – target hash value (`0 ≤ hashValue < modulo`)

> **Task**  
> Find the **first** substring of length `k` whose hash equals `hashValue`, according to the formula above.  
> If no such substring exists, return an empty string.

---

### 🎯  Why is 2156 a “Must‑Know” Interview Question?

1. **String Hashing** – Shows you can turn a string into a numeric fingerprint, a common technique in *algorithm design* (e.g., Rabin‑Karp).
2. **Modular Arithmetic** – Working with big moduli is a subtle skill that many candidates overlook.
3. **Sliding Window** – Highlights your understanding of *O(1)* window updates, essential for many *real‑time* problems.
4. **Algorithmic Elegance** – The backward walk eliminates the need for modular inverses, making the solution both **clean** and **fast**.

---

### ✅  The “Good” – What Works So Well

1. **Linear Time** – `O(n)` ensures that even the longest allowed strings finish in milliseconds.
2. **Constant Space** – Only a few long variables; no extra arrays or hash tables.
3. **No Modulo Inverse** – Avoids heavy math; instead you multiply, which is trivial under modulus.
4. **Reusable Technique** – The same pattern (backward rolling + power^k subtraction) applies to *Rabin‑Karp* pattern matching and *prefix‑hash* queries.

---

### ❌  The “Bad” – What Traps Beginners

| Bad Practice | Why It Fails |
|--------------|--------------|
| **Left‑to‑right multiplication** | Without a modular inverse, you’re stuck; the base `power` may share factors with `modulo`. |
| **Ignoring negative results** | Subtracting the leaving character can produce a negative number; using `%` on a negative long in C++/Python leaves it negative, breaking the equality check. |
| **Using 32‑bit ints** | The product `val * power` can exceed `2³¹`; the compiler may silently wrap around, yielding wrong hashes. |
| **Index confusion** | Remember Java’s `substring` uses *end‑exclusive* indices; C++’s `substr` takes length. A common off‑by‑one error crops up when converting between languages. |

---

### 😖  The “Ugly” – Edge‑Case Traps

| Edge Case | Ugly Consequence | Remedy |
|-----------|------------------|--------|
| `k` equals the entire string | Your window never moves; you still need to handle the first hit correctly. | Treat the full string as one window; the algorithm naturally falls back. |
| `modulo` equals `1` | All hashes become zero; but `hashValue` may still be non‑zero. | Ensure the algorithm doesn’t divide by zero; the product `(powerK * power) % modulo` will stay zero. |
| Very large `power` (≈ 10⁶) & very small `modulo` (≈ 2) | Frequent overflow, but still manageable with `long`. | Use 64‑bit integers; modulo reduces the value quickly, preventing runaway growth. |

---

### 📦  Full Code Snippets (Three Languages)

The article above lists the **Java**, **Python**, and **C++** implementations in one place. Copy/paste them into LeetCode, test on your local machine, or run them in an online compiler. The core logic – backward traversal + `powerK` subtraction – remains the same, just syntax changes.

---

### 🎓  Why 2156 is a **Show‑case** Problem

- **Demonstrates modular arithmetic**: Many interviewers ask about “hashing” or “Rabin‑Karp”. You show you know how to handle `%` correctly, including the subtraction corner‑case.
- **Highlights sliding‑window**: Sliding windows are a go‑to for *range queries* and *interval problems*. If you nail 2156, you’re already comfortable with this pattern.
- **Scales**: The `O(n)` solution shows you can solve problems with large constraints (10⁵ characters) efficiently – a must‑have for high‑performance software.
- **Language‑agnostic**: Having ready‑to‑use solutions in Java, Python, and C++ shows you can switch contexts depending on the job’s tech stack.

---

### 🚀  How to Use This Cheat Sheet

1. **Practice** – Run the three implementations on all official test cases on LeetCode; tweak the code for clarity or to add comments.
2. **Explain** – In an interview, walk through the backward approach, emphasizing why we multiply rather than divide.
3. **Show Variants** – Ask: “What if `power` was 1? What if `modulo` was 1?” Show you can reason about corner cases.
4. **Mention Trade‑offs** – Briefly state that left‑to‑right would need a modular inverse and is thus less efficient.

---

### 🎉  Takeaway

> *If you can explain LeetCode 2156 in a room full of engineers, you’ll demonstrate mastery of both **string hashing** and **modular arithmetic** – two pillars of algorithm design.*  

> Whether you’re aiming for a **software developer** role, a **data‑engineer** position, or a **competitive programming** career, the rolling‑hash technique you learn here will pay dividends for months to come.

---

### 📚  Further Reading

- *Rabin–Karp algorithm* – the classic rolling hash approach for substring search.  
- *Sliding Window* – a pattern that appears in problems like “Maximum Subarray Sum of Size K”.  
- *Modular Inverse* – when and how to compute it (useful for other problems).  
- *Big O Notation* – keep practicing to quickly spot whether an algorithm will pass large inputs.

---

### 🏁  Closing

> “Hash it, slide it, solve it – that’s the mantra for LeetCode 2156. Code the solution, understand the math, and bring it up in your next interview.”  

Happy coding, and may your hashes always be *right*!

---

**End of blog.**  
(Feel free to publish on Medium, Dev.to, or your personal portfolio site – the structure ensures it will rank well for the targeted keywords.)