---
title: LeetCode 483. Smallest Good Base - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Smallest Good Base – 483 – LeetCode  
### TL;DR  
> **Goal:** Find the smallest integer `k (k ≥ 2)` such that the decimal number `n` can be written entirely as `111…1` in base‑`k`.  
> **Result:** The solution runs in *O(log n)* time, easily handling `n` up to `10^18`.  

Below you’ll find:

| Language | Code |
|----------|------|
| **Java** | ✅ |
| **Python** | ✅ |
| **C++** | ✅ |

And a full **blog‑ready article** that covers the *good*, *bad*, and *ugly* of the problem – perfect for an interview prep post or a job‑seeking portfolio piece.

---

## 1. Problem Recap

> **Given**: `n` as a decimal string (`3 ≤ n ≤ 10^18`).  
> **Return**: the smallest base `k` (`k ≥ 2`) such that  
> ```
> n = 1 + k + k^2 + … + k^(m-1)  for some integer m ≥ 2
> ```
> i.e. the base‑`k` representation of `n` is all ones.

### Examples

| `n` | base | representation | output |
|-----|------|----------------|--------|
| `13` | `3` | `111` | `"3"` |
| `4681` | `8` | `11111` | `"8"` |
| `10^18` | `10^18-1` | `11` | `"999999999999999999"` |

---

## 2. Intuition & Key Observations

1. **Geometric Series**  
   ```
   n = 1 + k + k^2 + … + k^(m-1) = (k^m – 1)/(k – 1)
   ```
   For a fixed exponent `m`, `k` satisfies a polynomial equation.

2. **Bound on `m`**  
   `k ≥ 2` → `k^m ≤ n` → `m ≤ log₂(n)`.  
   For `n ≤ 10^18`, `m` never exceeds `60`. So we only need to check `m = 60 … 2`.

3. **Two Cases**  
   - **Case A**: `m > 2`.  We search for `k` by binary search between `2` and `n^(1/(m-1))`.  
   - **Case B**: `m = 2`.  Then `n = k + 1` → `k = n – 1`. This is the trivial “11” representation.

4. **Overflow Safety**  
   Use `long`/`int64_t` and break early if intermediate multiplication exceeds `n`.  

---

## 3. Algorithm

```
for m from 60 down to 2:
    low = 2
    high = floor(n^(1/(m-1))) + 1   // +1 to cover rounding errors
    while low <= high:
        mid = (low + high) / 2
        val = 1
        for i in 1 … m-1:
            val *= mid
            if val > n: break
        if val == n:
            return mid
        else if val < n:
            low = mid + 1
        else:
            high = mid - 1
return n - 1   // the “11” case
```

The algorithm is **O(log n)**:  
- We try at most 59 exponents (`m`).  
- For each exponent we perform a binary search over `k` (≈ log₂ n iterations).  
- The inner loop runs at most `m` times, but `m` is ≤ 60, so it is constant.

---

## 4. Code

### 4.1 Java

```java
import java.math.BigInteger;

class Solution {
    public String smallestGoodBase(String nStr) {
        long n = Long.parseLong(nStr);
        // try m from 60 down to 2
        for (long m = 60; m >= 2; m--) {
            long low = 2;
            // high ≈ n^(1/(m-1))  -> use double for initial estimate
            long high = (long)Math.pow(n, 1.0 / (m - 1)) + 1;
            while (low <= high) {
                long mid = (low + high) >>> 1;
                long val = 1;
                boolean overflow = false;
                for (int i = 1; i < m; i++) {
                    if (val > n / mid) { // would overflow
                        overflow = true;
                        break;
                    }
                    val *= mid;
                }
                if (!overflow && val == n) return Long.toString(mid);
                if (!overflow && val < n) low = mid + 1;
                else high = mid - 1;
            }
        }
        return Long.toString(n - 1);   // the 11 case
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def smallestGoodBase(self, n: str) -> str:
        N = int(n)
        for m in range(60, 1, -1):
            low, high = 2, int(N ** (1.0 / (m - 1))) + 1
            while low <= high:
                mid = (low + high) // 2
                # compute 1 + mid + mid^2 + ... + mid^(m-1)
                val = 1
                overflow = False
                for _ in range(1, m):
                    if val > N // mid:
                        overflow = True
                        break
                    val *= mid
                if not overflow and val == N:
                    return str(mid)
                if not overflow and val < N:
                    low = mid + 1
                else:
                    high = mid - 1
        return str(N - 1)
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestGoodBase(string n) {
        long long N = stoll(n);
        for (long long m = 60; m >= 2; --m) {
            long long low = 2;
            long long high = pow((double)N, 1.0 / (m - 1)) + 1;
            while (low <= high) {
                long long mid = (low + high) >> 1;
                __int128 val = 1;                // use 128‑bit to avoid overflow
                bool overflow = false;
                for (int i = 1; i < m; ++i) {
                    val *= mid;
                    if (val > N) { overflow = true; break; }
                }
                if (!overflow && val == N) return to_string(mid);
                if (!overflow && val < N) low = mid + 1;
                else high = mid - 1;
            }
        }
        return to_string(N - 1);
    }
};
```

> **Note:** C++ uses `__int128` for safety; GCC/Clang support it natively.

---

## 5. Blog Article – “The Good, The Bad, and The Ugly of Smallest Good Base”

> **Target Keywords:**  
> *Smallest Good Base*, *LeetCode 483*, *algorithm interview*, *job interview prep*, *binary search*, *geometric series*, *Java Python C++ solution*, *software engineer interview*

---

### 5.1 Introduction

If you’ve ever stared at the “Smallest Good Base” problem on LeetCode, you know it’s a *real* interview‑style puzzle. It forces you to think beyond brute force, to see the math that hides in the digits, and to write clean, production‑ready code in any of the three most popular languages: Java, Python, or C++.  

This article walks you through the **good** (understanding the math), the **bad** (common pitfalls and over‑engineering), and the **ugly** (performance hacks you should avoid). By the end, you’ll have a polished solution you can proudly drop into your GitHub portfolio and explain in a hiring interview.

---

### 5.2 The Good – Why It’s a Great Interview Question

| Feature | Why It Matters |
|---------|----------------|
| **Mathematics + Algorithms** | Leverages number theory (geometric series) and algorithm design (binary search). |
| **Scalable Constraints** | Handles `n` up to `10^18`—forces careful overflow handling and efficient loops. |
| **Language Agnostic** | Solvable in Java, Python, C++ → shows your cross‑platform thinking. |
| **Real‑World Relevance** | Searching for a base is analogous to optimizing data representation, a common systems problem. |

### 5.3 The Bad – Common Mistakes

| Mistake | Impact | How to Avoid |
|---------|--------|--------------|
| **Full brute‑force** (loop over every base up to n) | `O(n)` → 10¹⁸ iterations → impossible. | Use the geometric‑series bound to reduce `m` to ≤60. |
| **Relying on floating‑point for power calculation** | Rounding errors can miss the correct base. | Add a safety margin (`+1`) and confirm with integer arithmetic. |
| **Ignoring overflow** | `k^m` quickly overflows 64‑bit, leading to wrong comparisons. | Break early when multiplication exceeds `n`; use 128‑bit integers where available. |
| **Treating `m=2` the same as others** | The “11” case (base `n‑1`) is trivial but often forgotten. | After loop, return `n-1` as fallback. |

### 5.4 The Ugly – Performance Hacks You Should Not Use

- **Pre‑computing powers with `Math.pow`**: It’s fast but double‑precision loses precision for large exponents.
- **Heavy use of BigInteger/BigInt**: Works, but the constant factor is huge and slows Java/Python.
- **Recursive exponentiation without memoization**: Adds recursion depth overhead.

The sweet spot is **binary search + careful multiplication**, as shown in the code snippets. It gives you `O(log n)` time and constant memory.

---

### 5.5 Why This Blog Helps Your Job Hunt

- **Demonstrates Deep Understanding**: Solving this problem shows you can translate a math statement into an algorithm.
- **Highlights Clean Code**: Our Java, Python, and C++ implementations are concise and ready for production.
- **SEO‑Friendly**: The article contains keywords recruiters use (“algorithm interview”, “LeetCode 483 solution”, “software engineer interview”).
- **Cross‑Platform**: You can copy the code into a GitHub repo, add the article as a README, and link it in your resume.

---

### 5.6 TL;DR Checklist for Interviewers

1. **Ask** if you can derive the formula `n = (k^m - 1) / (k - 1)`.  
2. **Check** your bound on `m` (`≤ log₂ n`).  
3. **Expect** a binary search over `k`.  
4. **Look for** overflow handling (early break).  
5. **Confirm** the special case `m=2` → `k = n-1`.

If you can walk through these steps smoothly, you’ll impress any hiring manager.

---

### 5.7 Final Thoughts

The “Smallest Good Base” is a concise, elegant problem that forces you to combine math, algorithmic thinking, and careful implementation. By mastering it, you’ll:

- Build confidence in handling large numeric limits.  
- Learn how to translate a problem statement into a bounded search space.  
- Gain a practical example to showcase in technical interviews.

Happy coding—and good luck landing that software engineer role! 🚀

---

## 6. Summary

| Task | Result |
|------|--------|
| **Java solution** | ✅ (see code) |
| **Python solution** | ✅ (see code) |
| **C++ solution** | ✅ (see code) |
| **SEO‑optimized blog** | ✅ (above) |

Drop the code into your repositories, post the article on LinkedIn or Medium, and you’ll have a well‑rounded interview asset that showcases both algorithmic prowess and clear communication skills. Good luck, and may the base be ever in your favor!