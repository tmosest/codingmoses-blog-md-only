---
title: LeetCode 878. Nth Magical Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code Solutions

Below are fully‑working, production‑ready implementations of the **Nth Magical Number** solution (LeetCode 878) in **Java**, **Python**, and **C++**.  
All three use the same algorithmic idea:  
* Count how many numbers ≤ `x` are divisible by `a` or `b` using inclusion‑exclusion.  
* Binary‑search for the smallest `x` that yields at least `n` magical numbers.  
* Return the answer modulo `1 000 000 007`.

```java
/* ------------------------------------
   Java – LeetCode 878
   ------------------------------------ */
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int nthMagicalNumber(int n, int a, int b) {
        long A = a, B = b;

        // ---- GCD → LCM ----
        long gcd = gcd(A, B);
        long lcm = (A / gcd) * B;          // safe: A,B ≤ 4·10⁴

        long low  = Math.min(a, b);            // smallest possible magical number
        long high = (long)n * Math.min(a, b);  // an upper bound

        while (low < high) {
            long mid = low + (high - low) / 2;
            long count = mid / a + mid / b - mid / lcm; // inclusion‑exclusion

            if (count < n) {
                low = mid + 1;   // need larger numbers
            } else {
                high = mid;      // mid might be the answer
            }
        }
        return (int)(low % MOD);
    }

    private long gcd(long x, long y) {
        while (y != 0) {
            long t = x % y;
            x = y;
            y = t;
        }
        return x;
    }
}
```

```python
# ------------------------------------
# Python – LeetCode 878
# ------------------------------------
from math import gcd

MOD = 1_000_000_007

def nthMagicalNumber(n: int, a: int, b: int) -> int:
    lcm = a // gcd(a, b) * b

    lo, hi = min(a, b), n * min(a, b)          # 64‑bit range is fine

    while lo < hi:
        mid = (lo + hi) // 2
        cnt = mid // a + mid // b - mid // lcm
        if cnt < n:
            lo = mid + 1
        else:
            hi = mid

    return lo % MOD
```

```cpp
// ------------------------------------
// C++ – LeetCode 878
// ------------------------------------
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int nthMagicalNumber(int n, int a, int b) {
        const long long MOD = 1'000'000'007LL;

        long long g = std::gcd((long long)a, (long long)b);
        long long lcm = (long long)a / g * b;   //  a,b ≤ 4·10⁴ → no overflow

        long long low  = min((long long)a, (long long)b);
        long long high = (long long)n * min((long long)a, (long long)b);

        while (low < high) {
            long long mid = low + (high - low) / 2;
            long long cnt = mid / a + mid / b - mid / lcm;
            if (cnt < n) low = mid + 1;
            else         high = mid;
        }
        return (int)(low % MOD);
    }
};
```

> **Why this algorithm?**  
> *Time*: `O(log (n·min(a,b)))` – binary search over the answer.  
> *Space*: `O(1)` – only a few 64‑bit variables.  
> All edge‑cases (very large `n`, co‑prime `a` and `b`, etc.) are handled automatically by the formula and binary search.



## 2.  Blog Article  
*(Markdown format, ready to copy‑paste into a CMS or GitHub README)*

---

# Nth Magical Number – LeetCode 878  
**The Good, The Bad, and The Ugly (and How to Nail the Interview)**

> **Keywords**: *Nth Magical Number, LeetCode 878, binary search, inclusion‑exclusion, LCM, job interview, algorithm interview, coding interview, job‑search, CS interview, data structures*

---

## 1.  Problem Snapshot

> **Given** `n`, `a`, and `b`, find the *n‑th* smallest positive integer that is divisible by **either** `a` **or** `b`.  
> Return the answer **modulo** `10^9 + 7`.

*Constraints*

| Variable | Range | Note |
|----------|-------|------|
| `n` | `1 … 10^9` | huge → O(n) is impossible |
| `a`, `b` | `2 … 4·10^4` | small enough for 64‑bit math |
| Modulo | `10^9 + 7` | standard prime modulus |

---

## 2.  The Good – A Beautiful O(log n) Solution

### 2.1  The math behind it

- Count how many numbers ≤ `x` are divisible by `a`: `x / a`.  
- Count for `b`: `x / b`.  
- Numbers divisible by **both** appear twice; subtract the overlap: `x / lcm(a,b)`.

So the **count** of magical numbers ≤ `x` is

```
count(x) = x/a + x/b – x/lcm(a,b)
```

We need the smallest `x` with `count(x) >= n`. This is a classic *monotone predicate* problem → binary search.

### 2.2  Why binary search works

`count(x)` is non‑decreasing in `x`.  
If `count(mid) < n`, every number ≤ `mid` is too small → search right.  
Otherwise, `mid` might be the answer → search left (inclusive).

### 2.3  LCM via GCD

`lcm(a,b) = a / gcd(a,b) * b` – no overflow (`a,b ≤ 4·10^4`).

### 2.4  Pseudocode

```
lcm = a / gcd(a,b) * b
low = min(a,b)
high = n * min(a,b)          // safe upper bound

while low < high:
    mid = (low + high) / 2
    if mid/a + mid/b - mid/lcm < n:
        low = mid + 1
    else:
        high = mid

return low % MOD
```

### 2.5  Complexity

- **Time**: `O(log(n · min(a,b)))` – at most ~50 iterations.  
- **Space**: `O(1)` – constant extra memory.

---

## 3.  The Bad – Naïve Approaches That Fail

| Approach | Why It Fails |
|----------|--------------|
| **Brute force loop** (`for i = 1; count < n; i++`) | `n` can be 1e9 → 10⁹ iterations → TLE. |
| **Linear search on multiples** (`next = min(nextA, nextB)`) | Still O(n) steps; the inner arithmetic is cheap but the outer loop blows up. |
| **Mathematical closed‑form** | No known closed form; the sequence interleaves multiples of `a` and `b` irregularly. |

The take‑away: *You need logarithmic search, not linear counting.*

---

## 4.  The Ugly – Common Pitfalls & Edge Cases

1. **Overflow in `a*b`**  
   - In languages with 32‑bit ints (Java, C++ `int`), `a*b` may overflow.  
   - **Fix**: cast to 64‑bit (`long` / `long long`) *before* multiplication.

2. **Using `Math.pow(10,9)+7`**  
   - `Math.pow` returns a double → precision loss.  
   - **Fix**: use integer literal `1_000_000_007L`.

3. **Incorrect modulo handling**  
   - Remember to mod *after* the binary search, not during it.  
   - Binary search must work on the raw numbers.

4. **Off‑by‑one in binary search bounds**  
   - The invariant is `low < high`.  
   - On `count(mid) < n`, set `low = mid + 1`.  
   - Else, set `high = mid`.  
   - Final answer is `low`.

5. **`lcm` calculation order**  
   - Do `a / gcd * b` not `(a*b)/gcd` to avoid overflow.

6. **Large `n` vs. small `a,b`**  
   - The upper bound `n * min(a,b)` fits in 64‑bit (`≤ 4·10¹³`).

---

## 5.  Interview Tips – How to Showcase This Solution

| Tip | Why it matters |
|-----|----------------|
| **Explain the monotonic predicate** | Shows you understand binary search beyond arrays. |
| **Show the math** (`count = x/a + x/b - x/lcm`) | Demonstrates mathematical reasoning. |
| **Discuss edge cases** | Proves you’re thinking about overflow, modulo, bounds. |
| **Walk through a small example** | Makes your logic concrete. |
| **Mention complexity** | Interviewers love concise O‑notation. |
| **Mention modular arithmetic** | Many interview problems involve it. |

> *Remember:* “Good code is readable. Even if the algorithm is optimal, if the interviewer can’t follow, they’ll lose points.”

---

## 6.  Code Snippets – Ready to Copy

### Java

```java
public class Solution {
    private static final long MOD = 1_000_000_007L;
    public int nthMagicalNumber(int n, int a, int b) {
        long gcd = gcd(a, b);
        long lcm = (a / gcd) * b;          // 64‑bit safe

        long low  = Math.min(a, b);
        long high = (long)n * Math.min(a, b);

        while (low < high) {
            long mid = low + (high - low) / 2;
            long cnt = mid / a + mid / b - mid / lcm;
            if (cnt < n) low = mid + 1;
            else         high = mid;
        }
        return (int)(low % MOD);
    }
    private long gcd(long x, long y) { … }
}
```

### Python

```python
def nthMagicalNumber(n, a, b):
    from math import gcd
    lcm = a // gcd(a, b) * b
    lo, hi = min(a,b), n * min(a,b)
    while lo < hi:
        mid = (lo + hi)//2
        cnt = mid//a + mid//b - mid//lcm
        lo = mid+1 if cnt < n else hi = mid
    return lo % 1_000_000_007
```

### C++

```cpp
class Solution {
public:
    int nthMagicalNumber(int n, int a, int b) {
        const long long MOD = 1'000'000'007LL;
        long long g = std::gcd((long long)a, (long long)b);
        long long lcm = (long long)a / g * b;

        long long low  = min((long long)a, (long long)b);
        long long high = (long long)n * min((long long)a, (long long)b);

        while (low < high) {
            long long mid = low + (high - low)/2;
            long long cnt = mid/a + mid/b - mid/lcm;
            if (cnt < n) low = mid + 1;
            else         high = mid;
        }
        return (int)(low % MOD);
    }
};
```

> **Compile**: `javac Solution.java`, `g++ -std=c++17 -O2 -pipe -static -s solution.cpp`

---

## 7.  Final Thought – Turning a Problem into a Portfolio Piece

If you’re **looking for a software engineering role**:

- Add this solution to your GitHub repo, tag it with `LeetCode-878`.  
- Write a **blog post** (like this one) or a **LinkedIn article**.  
- Mention it on your résumé: “Solved LeetCode 878 – O(log n) binary‑search algorithm”.

If you’re **preparing for technical interviews**:

- Use the “Good, Bad, Ugly” structure to discuss the problem.  
- It’s a great **case study** on turning a mathematical insight into a scalable algorithm.

> **Pro tip:** Recruiters scan for “binary search”, “GCD/LCM”, “modulo”, and “O‑notation”. If those words appear in your conversation, you’re already scoring points.

---

**Happy coding and good luck with your next interview!**

--- 

*End of article.* 

--- 

*(Feel free to adjust the heading styles or add images/diagrams to your platform.)* 

--- 

### How this helps your job search

- **Showcase expertise**: The article positions you as a problem solver, not just a coder.  
- **SEO advantage**: The selected keywords target the most common search terms recruiters use.  
- **Portfolio value**: A well‑written article becomes a *shareable* piece of content, increasing your visibility on LinkedIn, GitHub, or a personal blog.  

Good luck – may your next interview be *magical*! 🚀

---


--- 

### 7.  Closing Remarks

- **LeetCode 878** is a *must‑know* for anyone interviewing in tech.  
- The binary‑search approach is elegant, efficient, and covers all edge‑cases.  
- Sharing the “Good, Bad, Ugly” analysis demonstrates deep understanding and interview readiness.

> *Keep coding, keep explaining, and keep crushing those interviews!* 🌟



--- 

*Happy Interviewing!*

--- 

*(End of Markdown)*

---

> **What makes this article stand out?**  
> *A clear, concise math derivation, a defensive coding checklist, and interview‑specific advice.*  
> Recruiters and interviewers often read the first few lines – make them count.  

---

**If you enjoyed this post, consider subscribing for more algorithm deep‑dives and interview prep hacks!**