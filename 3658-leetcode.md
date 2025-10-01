---
title: LeetCode 3658. GCD of Odd and Even Sums - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3658. GCD of Odd and Even Sums – LeetCode  
**Tags**: Easy | Math | One‑liner | O(1) | Interview

> *“The only number that divides both n² and n(n+1) is n.”* – The key insight that turns a 3‑minute interview question into a 5‑line code snippet.

---

### Why This Problem Rocks Your Resume

- **Math meets programming** – You’ll impress interviewers who love clean, mathematical solutions.
- **Zero‑runtime complexity** – The solution runs in *O(1)* regardless of `n`.
- **Cross‑language demonstration** – Show you can solve the same problem in Java, Python, and C++.
- **Great for a portfolio blog** – SEO‑friendly content that shows mastery of LeetCode, GCD, and O(1) tricks.

---

## Problem Recap

> **Given** an integer `n` (1 ≤ n ≤ 10⁹).  
> **Compute** the greatest common divisor (GCD) of:
> - `sumOdd` – the sum of the first `n` odd numbers  
> - `sumEven` – the sum of the first `n` even numbers  
> **Return** the GCD.

> **Example**  
> `n = 4` → `sumOdd = 1+3+5+7 = 16`, `sumEven = 2+4+6+8 = 20` → `GCD(16,20) = 4`.

---

## The Math Behind the One‑Liner

| Quantity | Formula | Proof Sketch |
|----------|---------|--------------|
| Sum of first `n` odd numbers | `n²` | 1 + 3 + 5 + … + (2n‑1) = n² (well‑known identity). |
| Sum of first `n` even numbers | `n(n+1)` | 2 + 4 + 6 + … + 2n = 2(1+2+…+n) = 2 * n(n+1)/2 = n(n+1). |
| GCD(n², n(n+1)) | `n` | `n` divides both terms; any common divisor must divide their difference `n² - n(n+1) = -n²` → factor of `n`. The largest such divisor is `n` itself. |

**Bottom line:** The answer is simply `n`.

---

## “Good, Bad, Ugly” – Code & Edge Cases

### The Good

| Language | One‑liner Code | Why It’s Good |
|----------|----------------|---------------|
| **Python** | `return n` | Uses built‑in int (unbounded). |
| **Java** | `return n;` | Uses `long` if needed; no overflow for 64‑bit. |
| **C++** | `return n;` (or `std::gcd(n*n, n*(n+1))` for illustration) | Uses `long long` and `std::gcd` for clarity. |

### The Bad (Naïve Approach)

```python
# Python: O(n) loop
sOdd = sum(2*i+1 for i in range(n))
sEven = sum(2*i+2 for i in range(n))
return math.gcd(sOdd, sEven)
```

- **Time**: O(n) – unnecessary for this problem.  
- **Space**: O(1) but heavy computations.  
- **Risk**: `n` could be 10⁹ → loops will time out.

### The Ugly (Overflow Pitfalls)

- In languages with fixed‑size integers (Java `int`, C++ `int`), `n²` overflows if `n > 46340`.  
- **Fix**: Use 64‑bit types (`long` in Java, `long long` in C++) or built‑in big integer types (`BigInteger` in Java, `int` in Python is already big).

---

## Full Solutions

### Python 3

```python
# LeetCode 3658
class Solution:
    def gcdOfOddEvenSums(self, n: int) -> int:
        # The GCD of n² and n(n+1) is always n
        return n
```

### Java (Java 17)

```java
// LeetCode 3658
public class Solution {
    public int gcdOfOddEvenSums(int n) {
        // n fits into 32‑bit, but to be safe use long if constraints grow
        return n;          // GCD of n*n and n*(n+1) is n
    }
}
```

### C++17

```cpp
// LeetCode 3658
class Solution {
public:
    int gcdOfOddEvenSums(int n) {
        // For demonstration: compute sums and use std::gcd
        long long sumOdd  = 1LL * n * n;          // n^2
        long long sumEven = 1LL * n * (n + 1);    // n*(n+1)
        return std::gcd(sumOdd, sumEven);       // equals n
    }
};
```

> **Note**: The explicit `std::gcd` call is optional; you could simply `return n;`.  
> The code uses `long long` to avoid overflow for very large `n`.

---

## How to Present This in Your Portfolio

1. **Write a Blog Post** – The one above is a template.  
2. **Add Visuals** – A small diagram of odd/even sums or a table of values.  
3. **Highlight Complexity** – Use bullet points: *Time: O(1)*, *Space: O(1)*.  
4. **Show Alternate Approaches** – Mention the naïve loop and why it’s suboptimal.  
5. **SEO Tags** – `LeetCode 3658`, `GCD`, `One‑liner solution`, `Java`, `Python`, `C++`, `Interview Preparation`.

---

## Final Thoughts

- **The GCD trick**: Whenever you see `n²` and `n(n+1)`, remember the GCD is `n`.  
- **Big integers**: Python handles arbitrarily large integers out of the box, but in Java and C++ you must be cautious.  
- **Interviews**: Explain the math first, then show the code. Interviewers love reasoning as much as the solution.

> *“A beautiful problem in one line is the proof that mathematics is the true language of programming.”* – **LeetCode 3658**

Good luck landing that job! 🚀