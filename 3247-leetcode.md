---
title: LeetCode 3247. Number of Subsequences with Odd Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣ Problem Recap – LeetCode 3247  
**Number of Subsequences with Odd Sum**  
> Given an integer array `nums`, count all subsequences whose element sum is **odd**.  
> Return the answer modulo \(10^9+7\).

> **Constraints**  
> - \(1 \leq n = \text{nums.length} \le 10^5\)  
> - \(1 \leq \text{nums}[i] \le 10^9\)

---

## 2️⃣ Key Insight  

For a subsequence only the **parity** of its sum matters.

* If we add an **odd** number to an existing subsequence, its parity flips.  
* Adding an **even** number keeps the parity unchanged.

This allows us to keep **just two counters**:

| `oddCnt` | `evenCnt` | Meaning |
|----------|-----------|---------|
| # of subsequences seen so far with an odd sum | # of subsequences seen so far with an even sum | Updated after inspecting each array element |

When we encounter a new number `x` we can decide:

| `x` parity | New `oddCnt` | New `evenCnt` |
|------------|--------------|---------------|
| **Odd** | `oddCnt + evenCnt + 1`  *(existing odds, existing evens flipped, new subsequence `[x]`)* | `oddCnt + evenCnt` |
| **Even**| `2 * oddCnt`  *(existing odds duplicated, with or without `x`)* | `2 * evenCnt + 1` *(existing evens duplicated + `[x]`)* |

All operations are performed modulo \(10^9+7\).

The algorithm is a single pass: **O(n)** time, **O(1)** extra space.

---

## 3️⃣ Solution Code  

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
Each implementation follows the same O(n) DP logic described above.

---

### 3.1 Java

```java
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int subsequenceCount(int[] nums) {
        long odd  = 0;
        long even = 0;

        for (int num : nums) {
            if ((num & 1) == 1) {          // odd number
                long newOdd  = (odd + even + 1) % MOD;
                long newEven = (odd + even) % MOD;
                odd  = newOdd;
                even = newEven;
            } else {                       // even number
                long newOdd  = (odd * 2) % MOD;
                long newEven = (even * 2 + 1) % MOD;
                odd  = newOdd;
                even = newEven;
            }
        }
        return (int) odd;                  // oddCnt holds the answer
    }
}
```

---

### 3.2 Python

```python
class Solution:
    MOD = 1_000_000_007

    def subsequenceCount(self, nums: List[int]) -> int:
        odd, even = 0, 0
        for num in nums:
            if num % 2:                 # odd
                new_odd  = (odd + even + 1) % self.MOD
                new_even = (odd + even) % self.MOD
                odd, even = new_odd, new_even
            else:                       # even
                new_odd  = (odd * 2) % self.MOD
                new_even = (even * 2 + 1) % self.MOD
                odd, even = new_odd, new_even
        return odd
```

---

### 3.3 C++

```cpp
class Solution {
public:
    int subsequenceCount(vector<int>& nums) {
        const long long MOD = 1'000'000'007LL;
        long long odd  = 0, even = 0;

        for (int num : nums) {
            if (num & 1) {                    // odd
                long long newOdd  = (odd + even + 1) % MOD;
                long long newEven = (odd + even) % MOD;
                odd  = newOdd;
                even = newEven;
            } else {                          // even
                long long newOdd  = (odd * 2) % MOD;
                long long newEven = (even * 2 + 1) % MOD;
                odd  = newOdd;
                even = newEven;
            }
        }
        return static_cast<int>(odd);
    }
};
```

---

## 4️⃣ The Good, The Bad, and The Ugly  

| Aspect | What’s Good | What’s Bad | The Ugly |
|--------|-------------|------------|----------|
| **Concept** | Simple parity‑based DP → O(n) time, O(1) space | None | None |
| **Brute‑Force** | Exponential subsets (2^n) | Exponential time & memory → TLE/MLE | Stack overflow if recursion not careful |
| **Top‑Down Memoization** | Easy to reason about (state = `index, parity`) | Needs O(n) recursion depth, extra memo table | Recursion depth of 10^5 → stack overflow; slower than iterative |
| **Bottom‑Up DP** | Matches the iterative solution above | Slightly more code if you store arrays | Overkill – you don’t need an array of size n |

> **Why the iterative DP is the star of interviews**  
> *Fast*: one scan, constant work per element.  
> *Robust*: no risk of recursion limits, no large auxiliary arrays.  
> *Clear*: two numbers, two rules – easy to explain on a whiteboard.

---

## 5️⃣ Complexity Analysis  

| Metric | Calculation |
|--------|-------------|
| **Time** | `O(n)` – one pass over the array |
| **Space** | `O(1)` – only two 64‑bit counters |
| **Modulo Operations** | `O(n)` as well – each step uses a few `% MOD` ops |

---

## 6️⃣ Interview‑Ready Tips  

1. **Explain the parity observation first** – this shows you understand why only two states are enough.  
2. **Walk through a tiny example** (e.g., `[1, 2, 3]`) while updating `oddCnt` and `evenCnt`.  
3. **Mention the modulo** – many interviewers expect you to avoid overflow early.  
4. **Talk about alternatives** (brute force, DP with array) and why you discarded them.  
5. **Show the code** in your preferred language, keeping variable names self‑descriptive (`odd`, `even`).  

---

## 7️⃣ Wrap‑Up  

The “Number of Subsequences with Odd Sum” problem is a classic example of how a simple observation (parity flipping) can turn a seemingly exponential combinatorial problem into a linear‑time DP.  
The solution is compact, efficient, and language‑agnostic – making it a great interview talking point.  

> **SEO Keywords**: LeetCode 3247, number of subsequences with odd sum, Java DP solution, Python DP solution, C++ DP solution, interview coding, algorithm interview, job interview prep, O(n) DP, dynamic programming parity, modulo 1e9+7.

Good luck on your coding journey! 🚀