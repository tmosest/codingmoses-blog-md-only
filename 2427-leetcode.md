---
title: LeetCode 2427. Number of Common Factors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Number of Common Factors” (LeetCode 2427) – A Full‑Stack Guide  
*Java, Python, & C++ Solutions + a SEO‑optimized Blog Post to Boost Your Interview Game*

---

## Table of Contents
1. [Problem Recap](#problem-recap)
2. [Naïve vs. Optimal Approaches](#naïve-vs-optimal-approaches)
3. [The GCD‑Based Solution](#the-gcd‑based-solution)
4. [Complexity Analysis](#complexity-analysis)
5. [Full Code Listings](#full-code-listings)
   - [Java](#java)
   - [Python](#python)
   - [C++](#cpp)
6. [The Good, the Bad, and the Ugly](#the-good-the-bad-and-the-ugly)
7. [Why This Matters for Your Job Hunt](#why-this-matters-for-your-job-hunt)
8. [Final Take‑Away](#final-take‑away)

---

## 1. Problem Recap <a name="problem-recap"></a>

**LeetCode 2427 – Number of Common Factors**  
> **Given** two positive integers `a` and `b` (1 ≤ a, b ≤ 1000),  
> **Return** the count of integers `x` such that `x` divides both `a` and `b`.

> **Examples**  
> *Input:* `a = 12, b = 6` → **Output:** `4` (`{1, 2, 3, 6}`)  
> *Input:* `a = 25, b = 30` → **Output:** `2` (`{1, 5}`)

---

## 2. Naïve vs. Optimal Approaches <a name="naïve-vs-optimal-approaches"></a>

| Approach | Idea | Time Complexity | Space Complexity | When It Works |
|----------|------|-----------------|------------------|---------------|
| **Brute‑Force** | Enumerate all integers from `1` to `min(a, b)` and check divisibility. | `O(min(a, b))` (≤ 1000) | `O(1)` | Works because constraints are tiny, but it scales poorly if limits grow. |
| **GCD‑Based** | Compute `g = gcd(a, b)`; count divisors of `g`. | `O(√g)` (≤ √1000) | `O(1)` | Extremely fast and future‑proof. |

While the Brute‑Force solution is perfectly acceptable for the given constraints, **the GCD‑based solution is the “good” approach** – it’s scalable, mathematically elegant, and demonstrates deeper algorithmic thinking – something interviewers love.

---

## 3. The GCD‑Based Solution <a name="the-gcd‑based-solution"></a>

1. **Compute GCD**  
   The greatest common divisor of `a` and `b` is the largest number that divides both. All common factors of `a` and `b` are exactly the divisors of this GCD.

2. **Count Divisors of `g`**  
   For each integer `i` from `1` to `√g`:
   - If `i` divides `g`, increment the count.
   - If `i` is not equal to `g / i`, increment the count again (to account for the complementary divisor).

3. **Return the total count**.

---

## 4. Complexity Analysis <a name="complexity-analysis"></a>

| Step | Complexity |
|------|------------|
| Compute GCD (Euclidean algorithm) | `O(log min(a,b))` |
| Count divisors of `g` | `O(√g)` |
| **Total** | **`O(√g)`** (worst case ~ 32 for g = 1000) |
| Space | **`O(1)`** |

The algorithm is *almost* constant‑time for the problem’s limits, and it scales smoothly if `a` and `b` become much larger.

---

## 5. Full Code Listings <a name="full-code-listings"></a>

### Java <a name="java"></a>
```java
import java.util.*;

public class Solution {
    // Euclidean algorithm for GCD
    private int gcd(int x, int y) {
        while (y != 0) {
            int tmp = x % y;
            x = y;
            y = tmp;
        }
        return x;
    }

    public int commonFactors(int a, int b) {
        int g = gcd(a, b);
        int count = 0;
        for (int i = 1; i * i <= g; i++) {
            if (g % i == 0) {
                count++;                     // divisor i
                if (i != g / i) count++;     // complementary divisor
            }
        }
        return count;
    }
}
```

> **Why this is “good”**  
> * Clean separation of concerns (`gcd` helper).  
> * Uses the Euclidean algorithm (optimal GCD).  
> * Linear traversal only up to √g.

---

### Python <a name="python"></a>
```python
class Solution:
    def gcd(self, a: int, b: int) -> int:
        while b:
            a, b = b, a % b
        return a

    def commonFactors(self, a: int, b: int) -> int:
        g = self.gcd(a, b)
        count = 0
        i = 1
        while i * i <= g:
            if g % i == 0:
                count += 1
                if i != g // i:
                    count += 1
            i += 1
        return count
```

> **Why this is “good”**  
> * Pythonic loop and integer division.  
> * No extra libraries; fully self‑contained.

---

### C++ <a name="cpp"></a>
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Euclidean algorithm
    int gcd(int a, int b) {
        while (b != 0) {
            int tmp = a % b;
            a = b;
            b = tmp;
        }
        return a;
    }

    int commonFactors(int a, int b) {
        int g = gcd(a, b);
        int count = 0;
        for (int i = 1; i * i <= g; ++i) {
            if (g % i == 0) {
                ++count;                 // divisor i
                if (i != g / i) ++count; // complementary divisor
            }
        }
        return count;
    }
};
```

> **Why this is “good”**  
> * Uses fast integer arithmetic, minimal memory.  
> * `bits/stdc++.h` is acceptable in competitive coding; otherwise include `<algorithm>` and `<numeric>`.

---

## 6. The Good, the Bad, and the Ugly <a name="the-good-the-bad-and-the-ugly"></a>

| Phase | Typical Code | What Went Wrong? | How to Fix |
|-------|--------------|------------------|------------|
| **Good** | `for (int i = 1; i * i <= g; ++i)` | Correct upper bound, no redundant checks | ✅ Keep it! |
| **Bad** | `for (int i = 1; i <= Math.max(a, b); ++i)` | Loops up to 1000, unnecessary when `g` is small | Switch to `Math.sqrt(g)` or `i * i <= g` |
| **Ugly** | `for (int i = 1; i <= g; ++i) { if (a % i == 0 && b % i == 0) count++; }` | O(min(a,b)) runtime; fails for 10⁹ inputs | Use GCD first, then divisor counting |

**Key take‑aways:**
- **Always think GCD first** – it collapses the problem space.
- **Avoid repeated modulo operations** – only compute once per divisor candidate.
- **Never assume constraints will stay small** – write solutions that scale.

---

## 7. Why This Matters for Your Job Hunt <a name="why-this-matters-for-your-job-hunt"></a>

- **Interviewers love math‑based optimizations.** A candidate who picks the GCD route shows they understand number theory and algorithmic efficiency.
- **Coding tests often hide subtle constraints.** Even though LeetCode’s limits are low, employers may test on larger hidden inputs.
- **Clean code is hiring‑friendly.** The provided solutions have minimal lines, no extra dependencies, and clear comments – perfect for a portfolio or a GitHub Gist.
- **Cross‑language fluency.** Presenting Java, Python, and C++ versions demonstrates adaptability – a big plus for tech roles that require multiple stacks.

---

## 8. Final Take‑Away <a name="final-take‑away"></a>

> **The “Number of Common Factors” problem is a perfect playground for showing off algorithmic maturity.**  
> * Start with a brute‑force proof‑of‑concept.  
> * Refactor to GCD + divisor counting – that’s the “good” solution.  
> * Keep the code lean, well‑commented, and language‑agnostic.  

> When you share these snippets on your résumé, blog, or coding‑portfolio, you’re not just solving a problem – you’re telling a story about **efficiency, clarity, and the ability to scale**. That’s exactly what employers want.

Good luck, and may your code compile cleanly in every language! 🚀

---