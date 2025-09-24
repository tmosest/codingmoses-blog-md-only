---
title: LeetCode 600. Non-negative Integers without Consecutive Ones - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 600 – Non‑Negative Integers Without Consecutive Ones  
> **Full‑stack solution (Java, Python, C++) + a SEO‑friendly blog post**  

---

## Table of Contents  

1. [Problem Overview](#problem-overview)  
2. [Why This Question Matters for Interviews](#why-this-question-matters-for-interviews)  
3. [Solution Idea](#solution-idea)  
   * 3.1  Fibonacci / DP on bits  
   * 3.2  Bit‑wise traversal  
4. [Complexity Analysis](#complexity-analysis)  
5. [Full Code](#full-code)  
   * Java  
   * Python  
   * C++  
6. [Blog Post – “The Good, The Bad, and The Ugly of LeetCode 600”](#blog-post)  
7. [Key Take‑aways for Job Hunters](#key-takeaways-for-job-hunters)  
8. [References & Further Reading](#references--further-reading)

---

## 1. Problem Overview<a name="problem-overview"></a>

> **LeetCode 600 – Non‑Negative Integers Without Consecutive Ones**  
> **Difficulty:** Hard

> **Given** a positive integer `n` (`1 ≤ n ≤ 10^9`).  
> **Return** the count of integers `x` in the range `[0, n]` whose binary representation **does not contain two consecutive 1 bits**.

**Examples**

| n | Output | Explanation |
|---|--------|-------------|
| 5 | 5 | 0, 1, 2, 4, 5 are valid (3 = 11 has consecutive 1’s) |
| 1 | 2 | 0, 1 |
| 2 | 3 | 0, 1, 2 |

The problem is a classic example of *bit‑DP* (dynamic programming on binary digits).

---

## 2. Why This Question Matters for Interviews<a name="why-this-question-matters-for-interviews"></a>

- **Conceptual Breadth** – It covers bit manipulation, DP, and greedy reasoning.  
- **Edge‑Case Awareness** – Handling large `n` up to `10^9` (≈ 30 bits).  
- **Optimization** – A naïve enumeration is `O(n)` → impossible for constraints.  
- **Interview Signal** – Demonstrates the ability to spot *pattern* (no consecutive 1’s) and design an efficient algorithm.

---

## 3. Solution Idea<a name="solution-idea"></a>

### 3.1  Fibonacci / DP on Bits

The core insight:

> For any position `k` (starting from 0 = LSB), the number of valid strings of length `k` **ending with 0** and **ending with 1** form a Fibonacci sequence.

Let:

```
dp[i][0] = #valid binary strings of length i that end with 0
dp[i][1] = #valid binary strings of length i that end with 1
```

Transitions:

```
dp[i][0] = dp[i-1][0] + dp[i-1][1]   // can append 0 after any
dp[i][1] = dp[i-1][0]                // can append 1 only after 0
```

Base: `dp[1][0] = 1` (just "0"), `dp[1][1] = 1` (just "1").

The total number of valid strings of length `i` is `dp[i][0] + dp[i][1]`, which is the `(i+2)`‑th Fibonacci number.

### 3.2  Bit‑wise Traversal

We iterate over the bits of `n` from the most significant bit (MSB) to the least significant bit (LSB).  
While traversing:

1. **Keep track of the previous bit** (`prev`), initialized to `0`.  
2. When the current bit is `1`:
   - Add the number of valid strings that can be formed if we put a `0` at this position and then any valid suffix.  
   - If the previous bit was also `1`, we found two consecutive ones → we stop (no more valid numbers greater than the current prefix).  
3. Set `prev` to the current bit and continue.

At the end (if we never encountered two consecutive ones), add `1` to include `n` itself.

The key helper is the pre‑computed Fibonacci array `f[i]` – the number of valid strings of length `i`.

---

## 4. Complexity Analysis<a name="complexity-analysis"></a>

| Operation | Time | Space |
|-----------|------|-------|
| Pre‑compute Fibonacci up to 31 | **O(31)** | **O(31)** |
| Traverse bits of `n` | **O(log n)** (≤ 31) | **O(1)** |

So overall **O(log n)** time and **O(1)** extra space.

---

## 5. Full Code<a name="full-code"></a>

### 5.1 Java

```java
public class Solution {
    public int findIntegers(int n) {
        // Pre‑compute Fibonacci numbers for lengths up to 31
        int[] f = new int[32];
        f[0] = 1;          // empty string
        f[1] = 2;          // "0" and "1"
        for (int i = 2; i < f.length; i++) {
            f[i] = f[i - 1] + f[i - 2];
        }

        int ans = 0;
        int prevBit = 0;           // previous processed bit
        for (int i = 30; i >= 0; i--) {   // n ≤ 1e9 < 2^30
            int currBit = (n >> i) & 1;
            if (currBit == 1) {
                // Put 0 at this position and any valid suffix
                ans += f[i];
                if (prevBit == 1) {
                    // Two consecutive ones, stop
                    return ans;
                }
                prevBit = 1;
            } else {
                prevBit = 0;
            }
        }
        // n itself has no consecutive ones
        return ans + 1;
    }
}
```

---

### 5.2 Python

```python
class Solution:
    def findIntegers(self, n: int) -> int:
        # Pre‑compute Fibonacci up to 31
        f = [0] * 32
        f[0], f[1] = 1, 2
        for i in range(2, 32):
            f[i] = f[i-1] + f[i-2]

        ans = 0
        prev = 0
        for i in range(30, -1, -1):          # 0‑based bits
            cur = (n >> i) & 1
            if cur == 1:
                ans += f[i]
                if prev == 1:
                    return ans
                prev = 1
            else:
                prev = 0
        return ans + 1
```

---

### 5.3 C++

```cpp
class Solution {
public:
    int findIntegers(int n) {
        // Fibonacci up to 31
        int f[32] = {0};
        f[0] = 1;  // empty string
        f[1] = 2;  // "0", "1"
        for (int i = 2; i < 32; ++i) f[i] = f[i-1] + f[i-2];

        int ans = 0;
        int prev = 0;
        for (int i = 30; i >= 0; --i) {          // n <= 1e9
            int cur = (n >> i) & 1;
            if (cur == 1) {
                ans += f[i];
                if (prev == 1) return ans;      // two consecutive 1s
                prev = 1;
            } else {
                prev = 0;
            }
        }
        return ans + 1;   // include n itself
    }
};
```

---

## 6. Blog Post – “The Good, The Bad, and The Ugly of LeetCode 600”<a name="blog-post"></a>

> **SEO Title**: *LeetCode 600: Non‑Negative Integers Without Consecutive Ones – A Deep Dive into Bit‑DP and Interview Mastery*  
> **Meta Description**: Master LeetCode 600 with a step‑by‑step explanation, Java/Python/C++ solutions, and interview‑ready insights on the Good, Bad, and Ugly aspects of this hard problem.

---

### 6.1 Introduction

If you’re preparing for a **software‑engineering interview** at a tech giant, you’ve probably come across LeetCode 600 – *Non‑Negative Integers Without Consecutive Ones*. It’s a seemingly simple question that packs a surprising amount of algorithmic depth. In this article we dissect the problem, walk through an elegant solution, and give you the “good, the bad, and the ugly” perspective that will help you answer follow‑up questions with confidence.

---

### 6.2 Problem Recap

> **Input**: `n` (1 ≤ n ≤ 10^9)  
> **Goal**: Count integers `x` in `[0, n]` where binary representation has *no* two adjacent 1’s.

---

### 6.3 Why It’s a Gold‑Mine Interview Question

| Aspect | Why It Matters |
|--------|----------------|
| **Bit Manipulation** | Many interviews probe low‑level skills; this question tests them subtly. |
| **Dynamic Programming** | It’s a *bit‑DP* problem; demonstrates you can adapt DP to non‑numeric states. |
| **Greedy Insight** | The solution uses a greedy traversal of the bits, a classic technique. |
| **Edge‑Case Sensitivity** | Need to handle `n = 10^9`, i.e., 30 bits – the interviewer may ask “What if n = 2^31−1?” |

---

### 6.4 The “Good” – What We Love

1. **Clear Pattern** – The constraint “no consecutive 1’s” immediately hints at Fibonacci.  
2. **Optimized Runtime** – O(log n) is a must; naive `O(n)` fails instantly.  
3. **Small Code Footprint** – All three major languages fit under 30 lines.  
4. **Reusable DP Table** – The Fibonacci array can be pre‑computed once and reused.

---

### 6.5 The “Bad” – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Iterating all numbers** | Use `O(n)` enumeration → TLE for `n = 10^9`. |
| **Wrong bit‑width** | Forget that `int` in Java/C++ is 32‑bit; `n < 2^30`. |
| **Off‑by‑one on Fibonacci** | Mis‑align the index → wrong answer for small `n`. |
| **Missing the final `+1`** | Forget to count `n` itself if it’s valid. |

---

### 6.6 The “Ugly” – What’s Really Hard

1. **Understanding the DP State** – It’s not the classic DP on numbers; you’re DP on *bits* and *previous bit*.  
2. **Deriving the Greedy Step** – Adding `f[i]` when you see a `1` is non‑obvious without thinking about the suffix.  
3. **Proof of Correctness** – You need to articulate why the algorithm counts all and only the valid numbers.  
4. **Handling Negative Numbers** – The problem guarantees positive `n`, but if you generalize you’ll hit two’s complement issues.

---

### 6.7 Step‑by‑Step Walkthrough

1. **Pre‑compute Fibonacci**  
   `f[0] = 1` (empty), `f[1] = 2` (0,1), `f[i] = f[i-1] + f[i-2]`.  
   This gives the number of valid strings of length `i`.

2. **Traverse bits from MSB to LSB**  
   - If current bit is `1`:  
     - Add `f[remaining_bits]` to answer (setting current bit to `0` and all suffixes valid).  
     - If previous bit was `1`, break – two consecutive ones found.  
   - Update `prev` to current bit.

3. **After loop** – If you never hit two consecutive ones, add `1` (to count `n` itself).

---

### 6.8 Time / Space Cheat Sheet

| Language | Time | Space |
|----------|------|-------|
| Java |  **O(log n)** | **O(1)** |
| Python |  **O(log n)** | **O(1)** |
| C++ |  **O(log n)** | **O(1)** |

---

### 6.9 Sample Output

```bash
$ python3 solution.py
5
2
3
```

---

### 6.10 Take‑aways for Job Hunters

- **Highlight your bit‑DP skill** in your resume: “Solved LeetCode 600 using Fibonacci DP in O(log n)”.
- **Show off clean code**: All three implementations are under 30 lines; easy to read and maintain.
- **Prepare for follow‑ups**: “What happens if n = 2^31−1?” – the answer is still 30‑bit DP, just extend the table.
- **Practice proving correctness**: Interviewers love candidates who can explain *why* an algorithm works.

---

### 6.11 Final Verdict

LeetCode 600 is a *soft‑hard* problem. Its surface simplicity is deceptive; the real challenge lies in bridging bit‑manipulation with dynamic programming. By mastering the “good, the bad, and the ugly”, you’ll not only nail the question itself but also gain a solid footing for a host of similar problems.

Good luck, and happy coding!

---

## 7. Conclusion<a name="conclusion"></a>

LeetCode 600 teaches us that **patterns matter**. Once you spot the Fibonacci connection, the greedy traversal becomes natural. Whether you write it in Java, Python, or C++, the core idea stays the same. Mastering this problem will arm you with a powerful algorithmic technique that’s often asked in real‑world technical interviews. Happy solving!