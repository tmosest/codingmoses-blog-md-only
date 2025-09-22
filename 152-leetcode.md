---
title: LeetCode 152. Maximum Product Subarray - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🔥 Mastering LeetCode 152 – “Maximum Product Subarray”  
*(Java | Python | C++ implementations + SEO‑friendly blog post)*  

---

## 🎯 Problem Recap

> **Maximum Product Subarray**  
> Given an integer array `nums`, find a **contiguous** sub‑array with the **largest** product, and return that product.  

- `1 ≤ nums.length ≤ 2·10⁴`  
- `-10 ≤ nums[i] ≤ 10`  
- The answer is guaranteed to fit into a 32‑bit signed integer.

> **Example**  
> `nums = [2, 3, -2, 4] → 6` (sub‑array `[2,3]`)  

---

## 📈 Why It Matters

- **Interview favorite**: Most interviewers ask a variation of this problem to gauge your grasp of *dynamic programming* and *edge‑case handling*.
- **Kadane’s algorithm**: The classic maximum sub‑array sum problem, but with *multiplication*, which introduces a twist.
- **Job‑search advantage**: Showing a clean, idiomatic solution in multiple languages demonstrates versatility to recruiters.

---

## 🚀 The Idea: Keep Track of *Both* Max and Min

Multiplication behaves differently from addition:

1. A *negative* times a *negative* → *positive*.  
2. A *positive* times a *negative* → *negative*.

Thus, while scanning the array we must remember **both** the current maximum product *ending* at the current index and the current minimum product *ending* at the current index. The minimum is essential because the next negative number could turn it into the new maximum.

### Algorithm (Kadane‑style)

```
max_so_far = nums[0]
current_max = nums[0]
current_min = nums[0]

for each num in nums[1:]:
    temp_max = current_max
    current_max = max(num, temp_max * num, current_min * num)
    current_min = min(num, temp_max * num, current_min * num)
    max_so_far = max(max_so_far, current_max)

return max_so_far
```

- **Time**: `O(n)` – one pass.  
- **Space**: `O(1)` – only a handful of variables.

---

## 🖥️ Code – Java, Python, C++

Below are clean, production‑ready snippets for each language. Each contains comments and is ready to paste into your LeetCode editor.

### 1️⃣ Java

```java
/**
 * LeetCode 152 – Maximum Product Subarray
 * Time: O(n)  Space: O(1)
 */
class Solution {
    public int maxProduct(int[] nums) {
        int maxSoFar = nums[0];
        int currMax = nums[0];
        int currMin = nums[0];

        for (int i = 1; i < nums.length; i++) {
            int n = nums[i];
            // if n is negative, currMax and currMin will swap roles
            int tempMax = currMax;
            currMax = Math.max(n, Math.max(tempMax * n, currMin * n));
            currMin = Math.min(n, Math.min(tempMax * n, currMin * n));
            maxSoFar = Math.max(maxSoFar, currMax);
        }
        return maxSoFar;
    }
}
```

### 2️⃣ Python

```python
# LeetCode 152 – Maximum Product Subarray
# Time: O(n)  Space: O(1)

class Solution:
    def maxProduct(self, nums: list[int]) -> int:
        max_so_far = curr_max = curr_min = nums[0]
        for n in nums[1:]:
            temp_max = curr_max
            curr_max = max(n, temp_max * n, curr_min * n)
            curr_min = min(n, temp_max * n, curr_min * n)
            max_so_far = max(max_so_far, curr_max)
        return max_so_far
```

### 3️⃣ C++

```cpp
// LeetCode 152 – Maximum Product Subarray
// Time: O(n)  Space: O(1)

class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int max_so_far = nums[0];
        int curr_max = nums[0];
        int curr_min = nums[0];

        for (int i = 1; i < nums.size(); ++i) {
            int n = nums[i];
            int temp_max = curr_max;
            curr_max = max({n, temp_max * n, curr_min * n});
            curr_min = min({n, temp_max * n, curr_min * n});
            max_so_far = max(max_so_far, curr_max);
        }
        return max_so_far;
    }
};
```

---

## 🔎 Edge Cases & Pitfalls

| # | Edge Case | Why it matters | How the algorithm handles it |
|---|------------|----------------|------------------------------|
| 1 | **All negative numbers** | The maximum product can be a single negative if there’s an odd count, or a positive if even. | `curr_max` starts with the first element; the loop updates correctly. |
| 2 | **Zeros in the array** | Zero breaks the product chain; a new sub‑array must start after it. | `max(n, ...)` includes `n` itself, which becomes 0, resetting `curr_max` and `curr_min`. |
| 3 | **Single element array** | Must return that element. | The loop never runs; `max_so_far` is returned. |
| 4 | **Large negative * negative** | Can overflow if using 32‑bit integers. | Problem guarantees the result fits in a 32‑bit signed int, so `int` is safe. |

---

## 📌 The Good, The Bad, The Ugly

### The Good
- **O(n)** time and **O(1)** space – perfect for interview constraints.
- Handles all cases (negative, zero, positive) in one pass.
- Very readable once you understand the *max/min pair* idea.

### The Bad
- The code can look a bit magical to a first‑time reader; the *swapping of roles* for negative numbers isn’t immediately obvious.
- Requires careful use of temporary variables to avoid accidentally overwriting `curr_max` before `curr_min` is updated.

### The Ugly
- Some naive solutions try to pre‑compute prefix products or use dynamic programming arrays of size `n`, resulting in **O(n)** space and extra overhead.
- A common bug: forgetting to reset the min/max after encountering a zero, leading to incorrect results.

---

## 🎯 Quick Checklist Before Your Interview

- [ ] Explain why we need **both** `currMax` and `currMin`.  
- [ ] Show how a negative number *swaps* them.  
- [ ] Walk through a mixed‑sign example on the whiteboard.  
- [ ] Mention the `max_so_far` update.  
- [ ] Discuss time/space complexity and why it’s optimal.  

---

## 🚀 Bonus – Alternative O(n) Solution (Prefix + Postfix)

Some interviewers might ask for a *two‑pass* approach:

1. Forward pass: compute maximum product ending at each index.  
2. Backward pass: compute maximum product starting at each index.  
3. Take the maximum of the product of the two passes at every index.

This also uses `O(n)` space but can be conceptually easier to explain.

---

## 🎓 Final Thoughts

The Maximum Product Subarray problem is a classic example of how a simple problem statement can hide a subtle twist. By keeping track of the **maximum** and **minimum** products at each step, we elegantly handle negative numbers and zeros in a single pass. Master this pattern, and you’ll feel confident tackling not only LeetCode 152 but a whole host of interview problems that involve *prefix products* or *two‑sided scans*.

---

## 📣 Take Action

- **Implement** the snippets in your preferred language and test on LeetCode.  
- **Explain** the algorithm to a friend or record a short video; teaching solidifies learning.  
- **Add** this solution to your portfolio or GitHub repo; recruiters love seeing clean, multi‑language code.  

Happy coding, and good luck landing that dream job! 🚀

---

### SEO Keywords (for the article):
- Maximum Product Subarray  
- LeetCode 152 solution  
- Kadane's algorithm for product  
- Dynamic programming interview problems  
- Java Python C++ interview coding  
- Job interview preparation  
- Handle negative numbers in arrays  
- O(n) time O(1) space array problems  

---