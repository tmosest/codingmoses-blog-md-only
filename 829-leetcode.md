---
title: LeetCode 829. Consecutive Numbers Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 829 – Consecutive Numbers Sum  
**Hard** | **Java | Python | C++**  
> “Given an integer *n*, return the number of ways you can write *n* as the sum of consecutive positive integers.”

---

### Table of Contents  
1. [Problem Restatement & Examples](#problem)  
2. [Mathematical Intuition](#intuition)  
3. [Algorithm & Code (O(√n))](#algorithm)  
4. [Alternative – Counting Odd Divisors (O(log n))](#odd)  
5. [Good, Bad & Ugly – What the Interviewer Actually Wants](#goodbad)  
6. [Edge Cases & Testing](#edge)  
7. [SEO‑Friendly Summary & Job‑Ready Tips](#seo)  
8. [References & Further Reading](#refs)

---

<a name="problem"></a>
## 1. Problem Restatement & Examples

| n | Ways to express as consecutive sums | Explanation |
|---|--------------------------------------|-------------|
| 5 | 2 | 5 = **5** (single term) <br>5 = 2 + 3 |
| 9 | 3 | 9 = **9** <br>9 = 4 + 5 <br>9 = 2 + 3 + 4 |
| 15 | 4 | 15 = **15** <br>15 = 8 + 7 <br>15 = 4 + 5 + 6 <br>15 = 1 + 2 + 3 + 4 + 5 |

**Constraints**  
- 1 ≤ n ≤ 10⁹  

The challenge is to do this **fast** – obviously a brute force O(n²) is impossible.

---

<a name="intuition"></a>
## 2. Mathematical Intuition

Let the sequence have length `k` and first term `a`.

```
a + (a+1) + (a+2) + … + (a+k-1) = n
```

Sum of an arithmetic progression:

```
k * a + k*(k-1)/2 = n
```

Rearrange:

```
k * a = n – k*(k-1)/2
```

**Key Observations**

| Observation | Why it matters |
|-------------|----------------|
| `k` must be **positive** | Sequence length can’t be zero or negative. |
| `a` must be **positive** | We only allow positive integers. |
| `n – k*(k-1)/2` must be **positive** | Because `k*a` > 0. |
| `k*(k-1)/2 < n` | Equivalent to `k*(k-1) < 2n`. |
| For large `k`, the left side grows ~k² → **k < sqrt(2n)** | Gives a finite loop bound. |
| `n – k*(k-1)/2` must be divisible by `k` | Then `a` is an integer. |

So the algorithm is:  
*Loop over all `k` from 1 up to `⌊√(2n)⌋`. For each, check if `(n - k*(k-1)/2) % k == 0`. If so, increment the counter.*

That’s all – a single simple loop that runs in `O(√n)` time and `O(1)` space.

---

<a name="algorithm"></a>
## 3. Algorithm & Code (O(√n))

### Java

```java
class Solution {
    public int consecutiveNumbersSum(int N) {
        int count = 0;
        // Use long to avoid overflow when computing k*(k-1)/2
        for (long k = 1; k * k < 2L * N; k++) {
            long temp = N - k * (k - 1) / 2;
            if (temp > 0 && temp % k == 0) count++;
        }
        return count;
    }
}
```

### Python

```python
class Solution:
    def consecutiveNumbersSum(self, n: int) -> int:
        count = 0
        k = 1
        while k * k < 2 * n:
            temp = n - k * (k - 1) // 2
            if temp > 0 and temp % k == 0:
                count += 1
            k += 1
        return count
```

### C++

```cpp
class Solution {
public:
    int consecutiveNumbersSum(int n) {
        int count = 0;
        for (long long k = 1; k * k < 2LL * n; ++k) {
            long long temp = n - k * (k - 1) / 2;
            if (temp > 0 && temp % k == 0) ++count;
        }
        return count;
    }
};
```

**Why it passes the constraints**

* `k` iterates up to ≈ √(2 × 10⁹) ≈ 44721 – trivial for any CPU.  
* All arithmetic stays within 64‑bit signed integers.  

---

<a name="odd"></a>
## 4. Alternative – Counting Odd Divisors (O(log n))

There is a well‑known math trick:

> *The number of ways to write n as a sum of consecutive positive integers equals the number of odd divisors of n.*

Why? Because each representation corresponds to a factor `k` of `n` where `k` is odd (or, more formally, to the length of the sequence).  

**Implementation in O(log n)**:

```java
int count = 0;
int temp = n;
for (int p = 3; p * p <= temp; p += 2) {
    if (temp % p == 0) {
        count++;                // one odd divisor found
        while (temp % p == 0) temp /= p;
    }
}
if (temp > 1) count++;            // remaining odd prime
return count;
```

(plus `1` if `n` itself is odd, handled by the loop).  
This is even faster for huge inputs, but the `O(√n)` loop is perfectly fine for LeetCode’s constraints and is much easier to reason about in an interview.

---

<a name="goodbad"></a>
## 5. Good, Bad & Ugly – What the Interviewer Actually Wants

| What | Why it matters | What to do |
|------|----------------|------------|
| **Clear Problem Statement** | Misunderstanding “consecutive *positive* integers” leads to wrong solutions. | Restate: `k` must be ≥ 1 and `a` ≥ 1. |
| **Mathematical Insight** | A 2‑liner solution beats a 100‑line brute force in interviews. | Show the derivation: `k*a = n – k*(k-1)/2`. |
| **Overflow Awareness** | `k*(k-1)/2` can overflow 32‑bit ints for n≈10⁹. | Use `long`/`long long` everywhere. |
| **Loop Bound** | Looping up to `n` would be disastrous. | Bound `k` by `sqrt(2n)`. |
| **Edge Cases** | n=1, n=2, n=3, powers of two, large primes. | Test these explicitly. |
| **Time & Space Complexity** | Interviewers ask “how fast is this?”. | Mention `O(√n)` time, `O(1)` space. |
| **Alternate Elegant Solution** | Shows depth of knowledge. | Briefly discuss odd‑divisor method. |

**“Ugly” Pitfalls**

- Forgetting that a single number itself counts as a valid consecutive sum.  
- Using `int` in Java/C++ for intermediate calculations – overflow causes wrong answers.  
- Off‑by‑one errors in loop bound (`k*k < 2*n` vs `k*k <= 2*n`).  

---

<a name="edge"></a>
## 6. Edge Cases & Testing

| n | Expected | Why |
|---|----------|-----|
| 1 | 1 | Only `[1]`. |
| 2 | 1 | Only `[2]`. |
| 3 | 2 | `[3]`, `[1+2]`. |
| 4 | 1 | `[4]` only. |
| 8 | 1 | `[8]` only (8 is a power of two). |
| 15 | 4 | As in examples. |
| 1000000000 | 1 | Only `[1000000000]` (even power of two). |

Run a quick `main` or `pytest` with these values to guarantee correctness.

---

<a name="seo"></a>
## 7. SEO‑Friendly Summary & Job‑Ready Tips

### Keywords  
- **LeetCode 829**  
- **Consecutive Numbers Sum**  
- **Java Python C++ solution**  
- **Consecutive positive integers**  
- **Job interview coding problem**  
- **O(√n) algorithm**  
- **Counting odd divisors**  

### Meta Description  
> Master LeetCode 829 “Consecutive Numbers Sum” with a concise O(√n) solution in Java, Python, and C++. Learn the math, code, and interview tricks that impress hiring managers.  

### Blog Title  
> “LeetCode 829: Consecutive Numbers Sum – A 3‑liner Math Solution (Java, Python, C++)”

### What Recruiters Look For  
1. **Mathematical Thinking** – Derive the formula quickly.  
2. **Time Complexity** – Show awareness of `√n`.  
3. **Correctness** – Handle overflow and edge cases.  
4. **Clean Code** – One‑liner loop, clear variable names.  
5. **Extra Credit** – Mention the odd‑divisor method.  

Practice explaining the derivation aloud – that’s what *really* impresses the interviewer.  

---

<a name="refs"></a>
## 8. References & Further Reading

1. [GeeksforGeeks – “Consecutive Numbers Sum”](https://www.geeksforgeeks.org/leetcode-829-consecutive-numbers-sum/)  
2. [LeetCode Discussion – 3‑liner Java solution](https://leetcode.com/problems/consecutive-numbers-sum/discuss/1332926/Concise-3-line-Java-solution)  
3. [Math StackExchange – “Consecutive sum representations and odd divisors”](https://math.stackexchange.com/questions/1105/how-to-represent-a-number-as-sum-of-consecutive-numbers)  
4. [Interview Cake – “Arithmetic Progressions in Coding Interviews”](https://www.interviewcake.com/interview-article/arith-progression)  

---

## Closing Thought

A beautiful `O(√n)` loop, a single line of math, and a tiny amount of overflow care – that’s all you need to conquer LeetCode 829. Show the interviewer you can **derive** fast solutions, **code** cleanly, and **think mathematically**. That combination is a recipe for a job‑offer.

Happy coding! 🚀

--- 

*Feel free to copy the code snippets, paste them into your IDE, and test them against the examples. Good luck on your next coding interview!*