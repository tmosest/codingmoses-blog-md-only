---
title: LeetCode 600. Non-negative Integers without Consecutive Ones - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Leetcode 600 – “Non‑Negative Integers without Consecutive Ones”  
### A Deep Dive for Your Next Interview

> **TL;DR** – The optimal solution runs in **O(log n)** time by treating the binary representation of *n* as a DP problem.  
> **Key Ideas**:  
> 1. Count valid numbers that are *strictly smaller* than the binary prefix of *n*.  
> 2. Use Fibonacci numbers to know how many valid suffixes exist for any prefix.  
> 3. Stop when you encounter the first “11” in *n*’s bits.  

---

### 1. Problem Recap

> **Given** a positive integer `n` (`1 ≤ n ≤ 10⁹`),  
> **Return** the count of integers `x` in the range `[0, n]` whose binary representation **does not contain two consecutive `1` bits**.

> **Examples**  
> *n = 5* → answer = **5** (0, 1, 2, 4, 5).  
> *n = 1* → answer = **2** (0, 1).  
> *n = 2* → answer = **3** (0, 1, 2).

---

### 2. Why Naïve Brute Force Fails

Iterating from `0` to `n` and checking each number with a bit‑scan is `O(n log n)` – far too slow for `n = 10⁹` (≈ 1 billion checks).  

Even a simple recursion that builds numbers bit‑by‑bit will explode in both time and stack depth.  

> **Bad**: `O(2^k)` where `k` is the number of bits in `n`.  
> **Ugly**: Over‑engineering with memo tables that never get reused.

---

### 3. The Elegant DP‑on‑Bits Approach

#### 3.1  Observation

The number of binary strings of length `k` without consecutive `1`s is the `k`‑th Fibonacci number (`F[k+2]`).  
Why?  
- A string of length `k` can start with `0` followed by any valid string of length `k‑1`.  
- Or it can start with `10` followed by any valid string of length `k‑2`.  
That recurrence is exactly Fibonacci.

#### 3.2  Strategy

1. **Pre‑compute** the first 31 Fibonacci numbers (`F[0] = 0, F[1] = 1`).  
2. Walk through the bits of `n` from the most significant bit (MSB) to the least.  
3. Keep a flag `prevOne` to remember if the previous bit was `1`.  
4. When you see a `1` bit:
   - **Add** `F[remainingBits]` to the answer – all numbers that could have `0` here and any valid suffix.  
   - If the previous bit was also `1`, **stop** – any number that follows would already contain “11”.  
5. After the loop, add `1` to include `n` itself if it never had consecutive ones.

The algorithm is linear in the number of bits (~ 30 for `10⁹`) – far faster than any brute force.

---

### 4. Code

Below you’ll find clean, self‑contained implementations in **Java**, **Python**, and **C++**.

> **All solutions** run in **O(log n)** time and use **O(1)** extra space.

---

#### 4.1 Java

```java
public class Solution {
    public int findIntegers(int n) {
        // Pre‑compute Fibonacci numbers up to 32 (bits of n)
        int[] fib = new int[32];
        fib[0] = 0; fib[1] = 1;
        for (int i = 2; i < fib.length; i++) {
            fib[i] = fib[i - 1] + fib[i - 2];
        }

        int ans = 0;
        int prevOne = 0;           // 0 if previous bit is 0, 1 if previous bit is 1
        int bits = 31;             // 0‑based index of MSB for 32‑bit int

        for (; bits >= 0; bits--) {
            int curBit = (n >> bits) & 1;
            if (curBit == 1) {
                ans += fib[bits]; // all numbers with 0 here + valid suffix
                if (prevOne == 1) // we hit "11"
                    return ans;
                prevOne = 1;
            } else {
                prevOne = 0;
            }
        }

        // n itself is valid (no consecutive 1s)
        return ans + 1;
    }
}
```

---

#### 4.2 Python

```python
class Solution:
    def findIntegers(self, n: int) -> int:
        # Pre‑compute Fibonacci up to 32 bits
        fib = [0, 1]
        for _ in range(30):            # 30 extra slots (0‑based)
            fib.append(fib[-1] + fib[-2])

        ans = 0
        prev_one = 0
        bits = n.bit_length()          # number of bits needed to represent n

        for i in range(bits - 1, -1, -1):
            cur = (n >> i) & 1
            if cur == 1:
                ans += fib[i]
                if prev_one:
                    return ans
                prev_one = 1
            else:
                prev_one = 0

        return ans + 1  # include n itself
```

---

#### 4.3 C++

```cpp
class Solution {
public:
    int findIntegers(int n) {
        // Fibonacci numbers up to 32 bits
        vector<int> fib(32);
        fib[0] = 0; fib[1] = 1;
        for (int i = 2; i < 32; ++i) fib[i] = fib[i-1] + fib[i-2];

        int ans = 0;
        bool prevOne = false;
        for (int i = 30; i >= 0; --i) {          // 0‑based index (bit 30 is MSB for 10^9)
            bool cur = (n >> i) & 1;
            if (cur) {
                ans += fib[i];
                if (prevOne) return ans;        // found "11"
                prevOne = true;
            } else {
                prevOne = false;
            }
        }
        return ans + 1;                          // include n itself
    }
};
```

---

### 5. Good, Bad, and Ugly

| Phase | Good | Bad | Ugly |
|-------|------|-----|------|
| **Brute‑Force** | Simple to understand | O(n log n) – not viable for 10⁹ | None |
| **Recursion** | Builds bit‑by‑bit | Exponential blow‑up; stack overflow | Recursion depth hack |
| **DP‑on‑Bits** | O(log n) time, O(1) space, clear logic | Requires careful handling of bit indexes | Over‑engineering: using maps for memoization when not needed |

---

### 6. Why This Blog Helps Your Career

- **Interview Ready** – Leetcode 600 is a staple in coding interviews for mid‑level positions.  
- **Clear Architecture** – Demonstrates how to translate a combinatorial counting problem into DP.  
- **Language Agnostic** – Provides solutions in Java, Python, and C++ – the most common interview languages.  
- **SEO‑Optimized** – Target keywords like “Leetcode 600 solution”, “non‑negative integers without consecutive ones”, “dynamic programming interview”, “software engineer interview questions”.

**Tip:** When writing your résumé, highlight your ability to solve this problem and explain the intuition. Recruiters love seeing candidates who can explain **why** an algorithm works, not just the code.

---

### 7. Final Thoughts

The “non‑negative integers without consecutive ones” problem is a beautiful blend of number theory, combinatorics, and bit manipulation.  
Once you master the DP‑on‑bits pattern, you’ll find similar techniques useful in many interview questions such as:

- Counting numbers with digit constraints (e.g., “numbers without repeated digits”).
- Fibonacci‑style DP over string/array positions.
- Bit‑mask DP in constrained search spaces.

Keep practicing, keep explaining, and good luck landing that dream job! 🚀

---