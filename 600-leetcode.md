---
title: LeetCode 600. Non-negative Integers without Consecutive Ones - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 600 – *Non‑Negative Integers Without Consecutive Ones*  
**Languages:** Java | Python | C++  
**Tags:** `Dynamic Programming`, `Bit Manipulation`, `Recursion`, `Interview Question`  

---

### 📌 Problem Summary

> **Given** a positive integer `n` (1 ≤ n ≤ 10⁹).  
> **Return** the number of integers in the inclusive range `[0, n]` whose binary representation **does not** contain two consecutive `1`s.

**Examples**

| n | answer | Explanation |
|---|--------|-------------|
| 5 | 5 | `0,1,10,100,101` – only `11` (3) is invalid |
| 1 | 2 | `0,1` |
| 2 | 3 | `0,1,10` |

---

### 🎯 Why This Question Rocks for Interviews

* Tests *dynamic programming* knowledge – the Fibonacci‑like recurrence is hidden behind binary constraints.  
* Forces you to think about **bit‑level** operations and state representation.  
* Can be solved in **O(log n)** time – a perfect showcase of algorithmic efficiency.  

If you can nail this problem (and explain it cleanly), you’ll stand out to hiring managers at **FAANG, Bloomberg, Google, Microsoft, etc.**  

---

## 🔧 Solution Idea – Digit DP + Fibonacci Cache

### 1️⃣ Observe the Structure

* Let `dp[i]` be the number of valid binary strings of length `i` (i.e., `i` bits) that **do not** contain consecutive `1`s.  
* The recurrence is simply the Fibonacci sequence:

```
dp[0] = 1   // empty string
dp[1] = 2   // "0" and "1"
dp[i] = dp[i-1] + dp[i-2]  (i ≥ 2)
```

Reason:  
- If the first bit is `0`, the remaining `i‑1` bits can be any valid string of length `i‑1`.  
- If the first bit is `1`, the second bit must be `0`, leaving `i‑2` free bits.

### 2️⃣ Count Numbers ≤ n

Walk through the bits of `n` from the most significant bit (MSB) to the least significant bit (LSB).  
Maintain:

- `prev` – whether the previous processed bit was `1`.  
- `ans` – accumulated count of valid numbers discovered so far.

At each bit position `i`:

```
if n has a 1 at bit i:
    // If we set this bit to 0, the remaining lower bits can be any valid string of length i
    ans += dp[i]
    // If we set this bit to 1, we must check that prev==0 (no consecutive 1s)
    if prev == 1:  // consecutive ones would appear
        break   // no further numbers are valid
    prev = 1
else:
    prev = 0
```

After finishing the loop, include `n` itself (if it has no consecutive ones) by adding 1.

### 3️⃣ Complexity

* **Time:** `O(log n)` – we scan at most 31 bits (`n ≤ 10⁹`).  
* **Space:** `O(1)` – only a few integer variables and a small DP array of size ~32.

---

## 🧑‍💻 Code Implementations

### 1. Java

```java
public class Solution {
    // Pre-computed dp[0..31] for Fibonacci-like sequence
    private final int[] dp = new int[32];

    public Solution() {
        dp[0] = 1;
        dp[1] = 2;
        for (int i = 2; i < dp.length; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
    }

    public int findIntegers(int n) {
        int ans = 0;
        int prev = 0;          // previous bit processed
        for (int i = 30; i >= 0; i--) {   // 31 bits are enough for n <= 1e9
            if (((n >> i) & 1) == 1) {    // bit i is 1
                ans += dp[i];             // put 0 here
                if (prev == 1) {          // would create 11
                    return ans;           // stop, rest are invalid
                }
                prev = 1;                  // set this bit to 1
            } else {
                prev = 0;                  // bit is 0
            }
        }
        return ans + 1; // include n itself
    }
}
```

### 2. Python

```python
class Solution:
    def __init__(self):
        self.dp = [0] * 32
        self.dp[0], self.dp[1] = 1, 2
        for i in range(2, 32):
            self.dp[i] = self.dp[i - 1] + self.dp[i - 2]

    def findIntegers(self, n: int) -> int:
        ans = 0
        prev = 0
        for i in range(30, -1, -1):          # 31 bits
            if (n >> i) & 1:
                ans += self.dp[i]            # set this bit to 0
                if prev == 1:                # consecutive 1 would appear
                    return ans
                prev = 1
            else:
                prev = 0
        return ans + 1  # include n itself
```

### 3. C++

```cpp
class Solution {
public:
    Solution() {
        dp[0] = 1;
        dp[1] = 2;
        for (int i = 2; i < 32; ++i)
            dp[i] = dp[i - 1] + dp[i - 2];
    }

    int findIntegers(int n) {
        int ans = 0;
        int prev = 0;
        for (int i = 30; i >= 0; --i) {
            if ((n >> i) & 1) {
                ans += dp[i];
                if (prev == 1) return ans; // consecutive 1 found
                prev = 1;
            } else {
                prev = 0;
            }
        }
        return ans + 1; // include n itself
    }
private:
    int dp[32];
};
```

---

## 📖 Blog Article – “The Good, The Bad, and The Ugly of LeetCode 600”

### Title
> **Non‑Negative Integers Without Consecutive Ones – The Good, The Bad, and The Ugly (A Winning Interview Guide)**  

### Meta Description (SEO)
> Master LeetCode 600 in Java, Python, and C++. Learn dynamic programming, bit tricks, pitfalls, and how to ace this hard interview problem. Perfect for software engineer job seekers.

---

### Introduction

In the world of coding interviews, one question keeps popping up at the top of the “hard” lists: **LeetCode 600 – *Non‑Negative Integers Without Consecutive Ones***.  
Why? Because it forces you to blend **bitwise insight** with **dynamic programming**. If you can solve it cleanly and explain the reasoning, you’ll impress hiring managers at Google, Amazon, Microsoft, and beyond.

This post dives into:

* The **good** – elegant DP solution that runs in O(log n).  
* The **bad** – common pitfalls, misconceptions, and why naïve recursion fails.  
* The **ugly** – over‑optimised or hacky attempts that break readability.  

And yes – we’ll show you working code in **Java**, **Python**, and **C++** so you can pick the language you’re most comfortable with.

---

### 1️⃣ The Problem in Plain English

> **Given** an integer `n` (1 ≤ n ≤ 10⁹), count all numbers `x` in the range `[0, n]` such that the binary representation of `x` does *not* contain two adjacent `1`s.

**Why is it hard?**  
Because you can’t just brute‑force `0…n`. Even at 10⁹, that’s a billion checks. The challenge is to count *implicitly* using math.

---

### 2️⃣ The Good – A Clean DP + Digit‑DP Approach

1. **Build a Fibonacci‑like table**  
   * `dp[i]` = number of valid binary strings of length `i`.  
   * Recurrence: `dp[i] = dp[i-1] + dp[i-2]` (classic Fibonacci).  
   * Complexity: constant pre‑computation (32 entries).

2. **Process the bits of `n`**  
   * Walk from the most significant bit down to 0.  
   * When the current bit of `n` is `1`, we have two choices:
     - Set it to `0`: all lower bits can be any valid string of that length (`dp[i]`).
     - Set it to `1`: only possible if the previous bit wasn’t `1`.  
   * Stop early if two consecutive `1`s would appear.

3. **Add the final number**  
   * After the loop, we’ve counted all numbers *smaller* than `n`.  
   * Include `n` itself if it has no consecutive `1`s.

**Result** – O(log n) time, O(1) space.

---

### 3️⃣ The Bad – What Goes Wrong

| Bad Practice | Why It Fails |
|--------------|--------------|
| **Pure recursion** (append 0/1 and count) | Exponential blow‑up. For `n = 1e9`, recursion depth and number of calls explode beyond any feasible stack. |
| **Dynamic programming without memoization** | Still O(n) time if you iterate through all numbers up to `n`. |
| **Ignoring the bit‑length** | Failing to handle leading zeros correctly, causing miscounts for numbers with fewer bits. |
| **Using `long` unnecessarily** | While `int` suffices for 10⁹, using 64‑bit arithmetic everywhere slows down the solution marginally and can mislead about the true complexity. |

> **Bottom line:** Aim for the O(log n) digit‑DP solution. It’s concise, fast, and scales to the maximum input.

---

### 4️⃣ The Ugly – Over‑Optimised / Hard‑to‑Read Code

Sometimes candidates try to squeeze every millisecond by:

* Writing a single line of bit‑wise magic that is cryptic (`ans += (1 << i) & ~((1 << (i+1)) - 1)`).
* Unrolling loops or using manual stack simulation instead of recursion.
* Over‑commenting every trivial operation, cluttering the logic.

> **Pro tip:** *Readability = interview score.*  
> A clean, well‑structured solution is usually better than a one‑liner that nobody else can understand.

---

### 5️⃣ Final Java / Python / C++ Code (See Above)

> Copy‑paste the snippet that matches your interview language. Don’t forget to test with the sample cases!

---

### 6️⃣ How to Use This Knowledge to Land a Job

1. **Highlight the concept**  
   * In your resume and LinkedIn: “Solved LeetCode 600 in O(log n) using DP + bit manipulation.”  
   * In interviews: “I used a Fibonacci‑like DP table to count valid binary strings, then performed a digit‑DP traversal of `n`.”

2. **Explain the trade‑offs**  
   * “A naive recursion would hit exponential time; my solution scales to the 32‑bit limit.”  
   * “I avoided heavy memory usage; the DP table fits in a fixed array.”

3. **Show the code in the language of the company**  
   * Many tech giants have Java or C++ in their tech stack; Python is often used for scripting.  
   * Having all three versions demonstrates versatility.

4. **Practice follow‑up questions**  
   * *“What if `n` were 64‑bit?”* – Answer: Increase DP array to 64 entries.  
   * *“Could you generalise for k consecutive `1`s?”* – Answer: Extend DP recurrence accordingly.

---

### Conclusion

LeetCode 600 is more than a number‑counting puzzle. It’s a **micro‑cosm of interview challenges** – combine mathematical insight, algorithmic efficiency, and clear communication.  

By mastering the **good**, avoiding the **bad**, and steering clear of the **ugly**, you’ll walk into an interview with confidence.  

Happy coding—and good luck landing that dream software engineering role!

---  

### Author Bio
> *Alex Chen* – Full‑stack engineer, 5+ years at cloud services, mentor on *LeetCode Solutions* and *Coding Interview Prep*. Follow me on Twitter @AlexCoding for more interview hacks.  

---

### References
* LeetCode Problem 600 – [https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/](https://leetcode.com/problems/non-negative-integers-without-consecutive-ones/)  
* Classic Fibonacci DP – Wikipedia.  

--- 

*End of Blog Post*  

---  

This comprehensive post should help you master LeetCode 600, avoid common pitfalls, and impress interviewers—whether you’re coding in **Java**, **Python**, or **C++**. Happy interviewing!