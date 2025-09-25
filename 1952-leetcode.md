---
title: LeetCode 1952. Three Divisors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## Three Divisors – A Simple LeetCode Interview Question (Java | Python | C++)

### 📌 Problem Statement (LeetCode 1952)

> **Three Divisors**  
> *Easy*  
> 
> Given an integer `n`, return `true` if `n` has **exactly three positive divisors**; otherwise return `false`.  
> 
> `1 <= n <= 10^4`

---

## Why This Question Is Great for Interviews

| ✅  | 🎯  | 👀 |
|-----|-----|----|
| **Math + Coding** – tests your understanding of number theory and your ability to translate it into code. | **Common** – shows up in many coding‑interview prep books. | **Simple** – you can answer it in under 10 minutes with a correct solution. |

Because it’s an “easy” problem, it’s a **great showcase** of how you approach problems, reason about edge‑cases, and write clean, readable code.  
If you want to land a role in software engineering, be sure you can explain this problem clearly and discuss its optimal solution.

---

## The Key Insight

A positive integer has **exactly three divisors** **iff** it can be expressed as a square of a prime number.

*Proof Sketch*  
Let the divisors of `n` be `1`, `p`, and `n`.  
If `p` is a prime and `n = p²`, the divisors are indeed `1`, `p`, and `p²`.  
Conversely, if `n` has exactly three divisors, the only possible factorization is `1 × p × p`, so `n = p²` with `p` prime.

Thus the algorithm reduces to:

1. Is `n` a perfect square?  
2. If yes, is its square root a prime?  

If both conditions hold → `true`; otherwise → `false`.

---

## Optimal Algorithm

```text
isThree(n):
    r = floor(sqrt(n))
    if r * r != n:          // n is not a perfect square
        return false
    return isPrime(r)       // r must be prime
```

- **Perfect‑square check** – O(1) arithmetic.
- **Prime check** – O(√r) time (≤ O(n¹⁄⁴) for the given constraints).  
  For `n ≤ 10⁴`, `r ≤ 100`, so the loop runs at most 10 iterations – negligible.

**Time Complexity**: `O(√n)` in the worst case (when `n` is prime, we still do a sqrt).  
**Space Complexity**: `O(1)`.

---

## Code Implementations

Below are clean, self‑documenting solutions in **Java**, **Python**, and **C++**.  
Each version follows the same logic and includes inline comments.

### Java

```java
/**
 * LeetCode 1952 – Three Divisors
 * 
 * Time:  O(sqrt(n))   (<= O(n^1/4) due to the square‑root check)
 * Space: O(1)
 */
class Solution {
    public boolean isThree(int n) {
        int root = (int)Math.sqrt(n);        // integer sqrt
        if (root * root != n) return false;  // not a perfect square

        return isPrime(root);
    }

    // Helper: check if x is prime (x >= 2)
    private boolean isPrime(int x) {
        if (x < 2) return false;
        if (x % 2 == 0) return x == 2;
        for (int i = 3; i * i <= x; i += 2) {
            if (x % i == 0) return false;
        }
        return true;
    }
}
```

### Python

```python
"""
LeetCode 1952 – Three Divisors
Time:   O(sqrt(n))
Space:  O(1)
"""

import math

class Solution:
    def isThree(self, n: int) -> bool:
        root = int(math.isqrt(n))
        if root * root != n:
            return False          # not a perfect square

        return self.is_prime(root)

    @staticmethod
    def is_prime(x: int) -> bool:
        if x < 2:
            return False
        if x % 2 == 0:
            return x == 2
        i = 3
        while i * i <= x:
            if x % i == 0:
                return False
            i += 2
        return True
```

### C++

```cpp
/**
 * LeetCode 1952 – Three Divisors
 *
 * Time Complexity : O(sqrt(n))
 * Space Complexity: O(1)
 */
class Solution {
public:
    bool isThree(int n) {
        int root = static_cast<int>(sqrt(n));
        if (root * root != n) return false;   // not a perfect square

        return isPrime(root);
    }

private:
    bool isPrime(int x) {
        if (x < 2) return false;
        if (x % 2 == 0) return x == 2;
        for (int i = 3; i * i <= x; i += 2) {
            if (x % i == 0) return false;
        }
        return true;
    }
};
```

---

## What Makes This Solution “Good”

| ✅ | Explanation |
|---|-------------|
| **Mathematically sound** – uses the prime‑square theorem, eliminating unnecessary loops. |
| **Fast** – runs in microseconds for all inputs up to `10⁴`. |
| **Readable** – short, well‑named helper functions (`isPrime`) and inline comments. |
| **Language‑agnostic** – the same logic works in Java, Python, or C++. |

---

## Common Pitfalls (“Bad” and “Ugly”)

| 🛑 | Problem | Fix |
|---|---------|-----|
| **Iterate to `n`** | The naive loop `for i in 1..n` is *O(n)* and unnecessary. | Stop at `sqrt(n)` and use the perfect‑square check. |
| **Floating‑point inaccuracies** | Using `Math.sqrt(n)` in Java/Python may give a non‑integer due to floating‑point errors. | Use integer square root (`Math.isqrt` in Python, `Math.sqrt` cast to `int` in Java, `sqrt` + cast in C++). |
| **Prime check for 1** | Some implementations forget to guard against `n = 1`. | Ensure `isPrime` returns `false` for `x < 2`. |
| **Time‑limit issues in larger constraints** | For `n` up to `10¹⁰⁰` the O(√n) check would be slow. | Use a deterministic Miller–Rabin primality test or pre‑compute primes via sieve. |
| **Missing `return false`** | Omitting a final `return false` leads to ambiguous control flow. | Always end with `return false;` after the prime check. |

---

## Edge Cases

| Input | Expected Output | Why |
|-------|-----------------|-----|
| `1` | `false` | 1 has only one divisor. |
| `2` | `false` | Only two divisors (1, 2). |
| `4` | `true` | 1, 2, 4 – three divisors. |
| `9` | `true` | 1, 3, 9 – three divisors. |
| `16` | `false` | 1, 2, 4, 8, 16 – five divisors. |
| `prime^2` (e.g., `25`) | `true` | Square of a prime. |
| `non‑prime square` (e.g., `36`) | `false` | Divisors: 1, 2, 3, 4, 6, 9, 12, 18, 36 – many. |

---

## Take‑away: How to Explain This in an Interview

1. **State the problem** – what are we asked to check?
2. **Show the insight** – “exactly three divisors → perfect square of a prime”.
3. **Outline the algorithm** – perfect‑square test + prime test.
4. **Discuss complexity** – `O(√n)` time, `O(1)` space.
5. **Mention edge cases** – `n = 1`, `n = 2`, etc.
6. **Walk through the code** – highlight helper functions, integer arithmetic, and short loops.

---

## Bonus: SEO‑Friendly Title & Meta Description

- **Title**: “Three Divisors Problem – Java, Python & C++ Solutions + Interview Tips”
- **Meta Description**: “Solve LeetCode 1952 – Three Divisors in Java, Python, and C++. Learn the math behind the solution, optimal code, and interview‑ready explanations.”

---

### Final Thought

The **Three Divisors** problem is a textbook example of turning a simple mathematical fact into an efficient algorithm. By mastering this question you’ll impress interviewers with your:

- Mathematical intuition
- Code clarity
- Attention to edge cases

Good luck landing that next software‑engineering role! 🚀