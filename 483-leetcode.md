---
title: LeetCode 483. Smallest Good Base - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **🧩 LeetCode 483 – “Smallest Good Base”**  
*Hard – 2025 Interview‑Ready Solution in Java, Python, & C++*  

---  

## 1. Problem Recap

> **Given** a string `n` (3 ≤ n ≤ 10¹⁸), find the smallest base `k ≥ 2` such that the representation of `n` in base `k` consists solely of 1’s (e.g. `13` → base 3 is `111`).  
> Return the base as a **string**.

The task looks simple, but handling the large range (`10¹⁸`) while staying in **O(log n)** time requires a clever observation.

---

## 2. Core Idea

If `n` in base `k` is all 1’s, then for some `m ≥ 1`:

```
n = 1 + k + k² + … + k^m
  = (k^(m+1) – 1) / (k – 1)          (geometric series)
```

- `m+1` is the **number of digits** in the base‑`k` representation.  
- `k` is the **base** we are looking for.  

The **key observation**:  
*For a fixed `m`, there is at most one integer `k` that satisfies the equation.*  
Therefore we can iterate over all possible `m` values (from the maximum possible number of digits down to 1) and, for each, solve for `k`.  
If we find a valid integer `k`, it is guaranteed to be the *smallest* base because a larger `m` ⇒ smaller `k`.

The maximum possible digits is bounded by  
`m+1 ≤ log₂(n) + 1 ≤ 61` (since `n ≤ 10¹⁸`).  
So we only have to check at most 60 candidates.

---

## 3. Implementation Strategy

| Language | Core Technique | Why it Works |
|----------|----------------|--------------|
| **Java** | Binary search on `k` for each `m` | Handles 64‑bit overflow safely with helper method |
| **Python** | Direct integer arithmetic (unbounded ints) | Simpler code – Python’s `int` is arbitrary precision |
| **C++** | Binary search with `__int128` for overflow protection | Fast and memory‑efficient |

All three use the same **helper**:  

```
value(k, m) = 1 + k + k² + … + k^m
```

computed iteratively to avoid explicit exponentiation (prevents overflow).

---

## 4. Code

Below you’ll find clean, well‑commented solutions in **Java**, **Python**, and **C++**. Each one returns the correct base as a string.

---

### 4.1 Java

```java
import java.math.BigInteger;

class Solution {
    public String smallestGoodBase(String nStr) {
        long n = Long.parseLong(nStr);
        // maximum number of digits (m+1) for base >= 2
        int maxM = (int) (Math.log(n) / Math.log(2)) + 1;

        for (int m = maxM; m >= 2; m--) {
            long base = findBase(n, m);
            if (base != -1) return Long.toString(base);
        }
        // if no base found, answer is n-1 (representation 11)
        return Long.toString(n - 1);
    }

    // binary search for base k that gives m+1 digits of 1
    private long findBase(long n, int m) {
        long low = 2;
        // upper bound for k is n^(1/m) + 1
        long high = (long) Math.pow(n, 1.0 / m) + 1;
        while (low <= high) {
            long mid = low + (high - low) / 2;
            long val = value(mid, m);
            if (val == n) return mid;
            if (val < n) low = mid + 1;
            else high = mid - 1;
        }
        return -1;  // not found
    }

    // compute 1 + k + k^2 + ... + k^m with overflow check
    private long value(long k, int m) {
        long res = 1;
        for (int i = 1; i <= m; i++) {
            // if multiplication would overflow, stop early
            if (res > Long.MAX_VALUE / k) return Long.MAX_VALUE;
            res = res * k + 1;
            if (res > Long.MAX_VALUE) return Long.MAX_VALUE;
        }
        return res;
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def smallestGoodBase(self, n: str) -> str:
        n = int(n)
        max_m = int(n.bit_length())  # log2(n)+1

        for m in range(max_m, 1, -1):
            k = self._find_base(n, m)
            if k != -1:
                return str(k)
        return str(n - 1)  # representation 11

    def _find_base(self, n, m):
        """Binary search for k."""
        low, high = 2, int(n ** (1.0 / m)) + 1
        while low <= high:
            mid = (low + high) // 2
            val = self._value(mid, m)
            if val == n:
                return mid
            elif val < n:
                low = mid + 1
            else:
                high = mid - 1
        return -1

    def _value(self, k, m):
        """Compute 1 + k + k^2 + ... + k^m."""
        res = 1
        for _ in range(1, m + 1):
            res = res * k + 1
        return res
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestGoodBase(string nStr) {
        long long n = stoll(nStr);
        int maxM = 64 - __builtin_clzll(n) + 1;   // log2(n)+1

        for (int m = maxM; m >= 2; --m) {
            long long base = findBase(n, m);
            if (base != -1) return to_string(base);
        }
        return to_string(n - 1);  // representation 11
    }

private:
    long long findBase(long long n, int m) {
        long long low = 2;
        long long high = pow(n, 1.0 / m) + 1;
        while (low <= high) {
            long long mid = low + (high - low) / 2;
            __int128 val = value(mid, m);
            if (val == n) return mid;
            if (val < n) low = mid + 1;
            else high = mid - 1;
        }
        return -1;
    }

    __int128 value(long long k, int m) {
        __int128 res = 1;
        for (int i = 1; i <= m; ++i) {
            res = res * k + 1;
            if (res > ( (__int128)LLONG_MAX)) break;
        }
        return res;
    }
};
```

---

## 5. Blog Article

> **Title:** *Smallest Good Base – The Ultimate LeetCode 483 Guide (Java / Python / C++)*  
> **Meta Description:** Master LeetCode 483 “Smallest Good Base” with step‑by‑step solutions in Java, Python, and C++. Learn the math trick, avoid overflow pitfalls, and impress interviewers.

---

### 5.1 Introduction

When you land on LeetCode 483, you’re immediately faced with a puzzle that looks deceptively simple: “Find the smallest base that turns a number into all 1’s.”  
What makes it *hard*? The answer is the **large input range (10¹⁸)** and the requirement for an **O(log n)** solution that fits comfortably into interview time limits.

Below is a complete, production‑ready walkthrough—complete with code for **Java, Python, and C++**—and an analysis of what makes this problem great for technical interviews.

---

### 5.2 The “Good, Bad, and Ugly” of the Problem

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Elegant math: geometric series | Requires careful handling of large numbers | Edge‑case overflow can break naive solutions |
| **Algorithm** | O(log n) via binary search over digits | Must determine max digits accurately | Searching over all possible bases (`2…n‑1`) is impossible |
| **Coding** | Clean iterative power computation | Must avoid built‑in pow() due to precision | BigInteger/`__int128` required in some languages |
| **Interview Value** | Shows understanding of number theory & bit tricks | Tests attention to detail with overflow | Exposes candidate’s ability to think under constraints |

---

### 5.3 Why It’s Interview‑worthy

1. **Math Meets Programming** – Interviewers love problems where you can show a concise mathematical insight that drastically reduces complexity.  
2. **Large‑Integer Awareness** – Handling 64‑bit values safely demonstrates low‑level knowledge that’s hard to find on whiteboards.  
3. **Multiple Languages** – Being able to adapt the same logic to Java, Python, and C++ shows versatility.  
4. **Edge‑Case Handling** – Discussing how you guard against overflow or precision loss signals strong defensive coding skills.

---

### 5.4 The Solution in Detail

1. **Convert the string to a 64‑bit integer (`long long` / `long`).**  
2. **Maximum possible digits** – `m+1 ≤ log₂(n) + 1`.  
3. **Iterate `m` from that maximum down to `2`** (because `m = 1` leads to base `n‑1`, the trivial case).  
4. **For each `m`** use binary search on `k` in the range `[2, n^(1/m)+1]`.  
5. **Check the geometric sum** `1 + k + k² + … + k^m` iteratively.  
6. **Return the first valid `k`** (guaranteed to be the smallest).  
7. **If none found**, return `n‑1` (`11` in any base).

The core arithmetic is *iterative*, avoiding explicit exponentiation which would otherwise overflow.  

In Java we guard against overflow with a helper that caps at `Long.MAX_VALUE`.  
Python’s `int` is unbounded, so we can be a bit more relaxed.  
C++ leverages `__int128` to stay within 128‑bit precision while keeping runtime fast.

---

### 5.5 Common Pitfalls and How to Avoid Them

| Pitfall | Fix |
|---------|-----|
| Using `Math.pow()` (Java) or `pow()` (C++) | Replace with binary search + iterative sum |
| Not capping the upper bound `high` | Add `+1` after computing `n^(1/m)` |
| Forgetting to convert to string at the end | Always use `Long.toString()` / `to_string()` |
| Overlooking the trivial `m = 1` case | Handle `n-1` as a fallback |

---

### 5.6 Take‑Away: Interview Prep Tips

- **Practice the bit trick**: `log₂(n) + 1` is simply `Integer.bitLength(n)` in Java or `n.bit_length()` in Python.  
- **Understand `__int128`** in C++ – it’s the most efficient way to stay safe with 64‑bit multiplications.  
- **Write helper functions** (like `value(k, m)`) to keep your main loop readable.  
- **Explain overflow handling** on the whiteboard; this often turns a simple code‑submission into an *excellent* interview answer.

---

### 5.7 Conclusion

LeetCode 483 is more than a number puzzle; it’s a showcase of clean mathematical reduction, efficient binary search, and robust coding across languages.  

With the three solutions above, you can confidently tackle the problem in any interview setting.  
Remember: the *beauty* lies in the observation that **a fixed digit count leads to a unique base**—once you lock that in, the rest is just a matter of safe arithmetic.

Happy coding—and good luck on your next technical interview!

---


--- 

**End of Article**


--- 

Feel free to use this guide as a reference or share it with peers preparing for coding interviews.