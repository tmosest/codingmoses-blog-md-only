---
title: LeetCode 1952. Three Divisors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Three Divisors – LeetCode 1952  
**Difficulty:** Easy | **Tags:** Math | Number Theory | Math‑Intuition

> “Given an integer `n`, return true if `n` has exactly three positive divisors. Otherwise, return false.”

---

### 1.  The “aha” observation  

For any integer `n` to have **exactly 3 divisors** it must be of the form  

```
n = p²
```

where `p` is a **prime**.

* 1 is always a divisor.  
* `p` is the second divisor.  
* `p²` itself is the third divisor.  

If `n` were not a perfect square, the number of divisors would be even (pairs of `d` and `n/d`).  
If the square root isn’t prime, `p²` will have at least one extra divisor (e.g., `p·q`).

So the problem boils down to:

1. Is `n` a perfect square?  
2. Is the integer square root a prime number?

Both steps are `O(√n)` at worst, but for `n ≤ 10⁴` it is trivial.

---

## Code

Below are clean, idiomatic implementations in **Java, Python, and C++**.

| Language | Code | Complexity |
|----------|------|------------|
| **Java** | ```java
class Solution {
    public boolean isThree(int n) {
        // 1. perfect‑square test
        int r = (int) Math.sqrt(n);
        if (r * r != n) return false;

        // 2. primality test for r
        if (r < 2) return false;          // 1 is not prime
        for (int i = 2; i * i <= r; i++)
            if (r % i == 0) return false;
        return true;
    }
}
``` | `O(√n)` time, `O(1)` space |
| **Python** | ```python
class Solution:
    def isThree(self, n: int) -> bool:
        r = int(n ** 0.5)
        if r * r != n:          # not a perfect square
            return False

        if r < 2:               # 1 is not prime
            return False

        for i in range(2, int(r ** 0.5) + 1):
            if r % i == 0:
                return False
        return True
``` | `O(√n)` time, `O(1)` space |
| **C++** | ```cpp
class Solution {
public:
    bool isThree(int n) {
        int r = static_cast<int>(sqrt(n));
        if (r * r != n) return false;          // not a perfect square

        if (r < 2) return false;               // 1 is not prime

        for (int i = 2; i * i <= r; ++i)
            if (r % i == 0) return false;      // composite
        return true;
    }
};
``` | `O(√n)` time, `O(1)` space |

> **Why this is the “best” solution?**  
> 1. **Fast** – only a few integer operations per test case.  
> 2. **Memory‑efficient** – no auxiliary data structures.  
> 3. **Readable** – the math‑intuition is spelled out in the code comments.

---

## The Blog Article

> **Title (SEO‑Optimized)**  
> “Three Divisors – LeetCode 1952: Java, Python, & C++ Solutions for Your Next Coding Interview”

---

### 1. Introduction

The **Three Divisors** problem is a staple in the LeetCode “Number Theory” section. Even though it is marked **Easy**, many candidates stumble because they forget the key mathematical insight: *only the square of a prime has exactly three divisors.*  
In this article we’ll walk through the **good**, the **bad**, and the **ugly** ways to solve the problem, and show you clean, interview‑ready code in **Java, Python, and C++**.

**Keywords:** LeetCode 1952, Three Divisors, Java solution, Python solution, C++ solution, number theory interview, coding interview, algorithm, job interview, programming.

---

### 2. What Makes a Number Have Exactly Three Divisors?

A divisor of `n` is an integer `d` such that `n = d * k` for some integer `k`.  
If `n` is a perfect square, its divisor pairs are symmetrical; the middle divisor is the square root itself.  
When `n = p²` and `p` is a prime:

| divisor | description |
|---------|-------------|
| `1` | always divides any integer |
| `p` | the prime factor |
| `p²` | the number itself |

No other integer can divide `p²` because `p` has no divisors other than `1` and `p`.  
If `p` were composite, say `p = a · b`, then `a` and `b` would also divide `p²`, giving more than three divisors.

So the problem is reduced to:

1. **Is `n` a perfect square?**  
2. **Is the square root a prime?**

---

### 3. The “Good” – An Elegant, Math‑Driven Solution

*Why it shines:*

- **Clarity:** The code mirrors the math.  
- **Performance:** `O(√n)` time, `O(1)` space.  
- **Robustness:** Handles edge cases (`n = 1`, non‑squares, etc.) gracefully.

**Java**

```java
public boolean isThree(int n) {
    int r = (int) Math.sqrt(n);
    if (r * r != n) return false;          // 1️⃣ Not a perfect square

    if (r < 2) return false;               // 2️⃣ 1 isn’t prime

    for (int i = 2; i * i <= r; i++)       // 3️⃣ Check primality
        if (r % i == 0) return false;
    return true;
}
```

**Python**

```python
def isThree(n: int) -> bool:
    r = int(n ** 0.5)
    if r * r != n:          # Not a perfect square
        return False
    if r < 2:               # 1 isn’t prime
        return False
    for i in range(2, int(r ** 0.5) + 1):
        if r % i == 0:
            return False
    return True
```

**C++**

```cpp
bool isThree(int n) {
    int r = static_cast<int>(sqrt(n));
    if (r * r != n) return false;          // Not a perfect square
    if (r < 2) return false;               // 1 isn’t prime
    for (int i = 2; i * i <= r; ++i)       // Primality test
        if (r % i == 0) return false;
    return true;
}
```

---

### 4. The “Bad” – Brute‑Force Divisor Counting

A common beginner’s mistake is to iterate over **all** integers from `1` to `n` and count the divisors.

```java
public boolean isThree(int n) {
    int count = 0;
    for (int i = 1; i <= n; i++) {
        if (n % i == 0) count++;
    }
    return count == 3;
}
```

**Problems**

- **Time‑consuming:** `O(n)` operations. For `n = 10⁴` it’s fine, but it scales poorly.  
- **Unnecessary:** It does a lot of work for a very simple mathematical shortcut.  
- **Hard to explain in an interview:** You’d waste valuable interview time.

---

### 5. The “Ugly” – Over‑Optimised or Hard‑to‑Read Hacks

Some candidates try to avoid the perfect‑square check entirely, or use advanced tricks that hurt readability.

```cpp
bool isThree(int n) {
    int r = round(sqrt(n));
    return r * r == n && isPrime(r);   // isPrime uses a sieve or other heavy machinery
}
```

While this is fast, the extra function (`isPrime`) can be an overkill for a problem with such small input constraints. It also makes the solution harder to audit quickly during a coding interview.

---

### 6. Takeaway for Your Next Coding Interview

| ✅ Tip | Why |
|--------|-----|
| **State the math first** | It gives you a roadmap for the code. |
| **Use `sqrt(n)` only once** | Avoid recomputing the same value. |
| **Check `r < 2` before primality** | Quickly reject the special case `n = 1`. |
| **Write comments** | Helps interviewers follow your logic. |
| **Test edge cases** | `n = 1`, `n = 4`, `n = 9`, `n = 16`, etc. |

> **Remember:** The interviewer isn’t just judging your ability to code; they’re assessing your problem‑solving mindset. A short, mathematically sound solution is usually better than a lengthy brute‑force approach.

---

### 7. Closing Thoughts

The **Three Divisors** problem is a wonderful example of how a solid grasp of number theory can turn an O(n) brute force into a clean O(√n) solution.  
Implementing it in **Java, Python, and C++** shows that the underlying algorithm is language‑agnostic; only the syntax changes.  

Keep this pattern in mind: **identify the core property** (perfect square + prime), **translate it into a minimal set of checks**, and **code it with clarity**. That’s the recipe for success on LeetCode and in technical interviews alike.

---

**Happy coding and best of luck with your job search!**