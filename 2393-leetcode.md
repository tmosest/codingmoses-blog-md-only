---
title: LeetCode 2393. Count Strictly Increasing Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2393 – Count Strictly Increasing Subarrays  
### Java / Python / C++ Solutions + A SEO‑Optimized Blog Post  
*(If you’re preparing for a coding interview, this is the one‑page guide you’ll want to keep handy.)*  

---

## 1. Problem Summary

> **Given** an integer array `nums` (positive values).  
> **Return** the total number of *contiguous* subarrays that are **strictly increasing**.

> **Definition**  
> *A subarray* is a continuous slice of the original array.  
> A subarray `nums[l … r]` is *strictly increasing* if `nums[l] < nums[l+1] < … < nums[r]`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,3,5,4,4,6]` | `10` | 6 singletons + 3 pairs + 1 triple |
| `[1,2,3,4,5]` | `15` | Every subarray is increasing (triangular number 5·6/2) |

**Constraints**

| | |
|---|---|
| 1 ≤ `nums.length` ≤ 10<sup>5</sup> | 1 ≤ `nums[i]` ≤ 10<sup>6</sup> |

---

## 2. Brute‑Force vs Optimal

| Approach | Time | Space | Comments |
|----------|------|-------|----------|
| Enumerate all subarrays & check | O(n²) | O(1) | Feasible only for tiny arrays. |
| **Two‑pointer / sliding window** | **O(n)** | O(1) | Keep the current increasing segment; add the count of subarrays inside it. |
| Dynamic programming (cumulative sums) | O(n) | O(n) | Works but uses extra memory unnecessarily. |

> **Good**: O(n) single pass, constant space.  
> **Bad**: O(n²) brute‑force, too slow for 10⁵.  
> **Ugly**: DP that stores intermediate values, wasted memory.

---

## 3. The Elegant O(n) Solution

1. Scan the array once.  
2. Maintain the length `len` of the *current* strictly increasing segment.  
3. When the segment ends (i.e., `nums[i] <= nums[i‑1]`), add  
   \[
   \text{segment\_count} = \frac{len \times (len+1)}{2}
   \]
   to the answer (the number of subarrays in that segment).  
4. Reset `len = 1` (start a new segment at the current element).  
5. After the loop, add the final segment’s contribution.

The formula `len * (len+1) / 2` is the triangular number—exactly the count of subarrays inside a strictly increasing run of length `len`.

---

## 4. Code Implementations

### 4.1 Java

```java
public class Solution {
    public long countSubarrays(int[] nums) {
        long total = 0L;
        int len = 1;                    // current increasing segment length

        for (int i = 1; i < nums.length; i++) {
            if (nums[i] > nums[i - 1]) {
                len++;                 // still increasing
            } else {
                total += (long) len * (len + 1) / 2;
                len = 1;               // start new segment
            }
        }

        // add last segment
        total += (long) len * (len + 1) / 2;
        return total;
    }
}
```

### 4.2 Python

```python
class Solution:
    def countSubarrays(self, nums: List[int]) -> int:
        total = 0
        length = 1                       # current increasing run length

        for i in range(1, len(nums)):
            if nums[i] > nums[i - 1]:
                length += 1
            else:
                total += length * (length + 1) // 2
                length = 1

        total += length * (length + 1) // 2
        return total
```

### 4.3 C++

```cpp
class Solution {
public:
    long long countSubarrays(vector<int>& nums) {
        long long total = 0;
        long long len = 1;                // current run length

        for (size_t i = 1; i < nums.size(); ++i) {
            if (nums[i] > nums[i - 1]) {
                ++len;
            } else {
                total += len * (len + 1) / 2;
                len = 1;
            }
        }
        total += len * (len + 1) / 2;      // final segment
        return total;
    }
};
```

All three solutions run in **O(n)** time, use **O(1)** extra space, and handle the maximum input size comfortably.

---

## 5. SEO‑Optimized Blog Post

> **Title**: “LeetCode 2393 – Count Strictly Increasing Subarrays (Java/Python/C++) | Interview Prep Guide”

> **Meta Description**: “Master LeetCode 2393 in minutes. Learn the optimal O(n) solution with Java, Python, and C++ code, plus interview insights. Boost your coding interview score!”

> **Keywords**: LeetCode 2393, Count Strictly Increasing Subarrays, Java solution, Python solution, C++ solution, interview problem, algorithm interview, two‑pointer, sliding window, job interview tips.

---

### Blog Article

---

### 📌 What’s the Problem?

LeetCode 2393 asks you to count how many contiguous subarrays of a positive integer array are **strictly increasing**. The array can be as long as 100 000 elements, so an O(n²) brute‑force algorithm won’t cut it. Interviewers love problems that can be solved in linear time with a simple sliding‑window idea—exactly what this one offers.

---

### 🧠 Why Is It Worth Learning?

- **Classic Sliding‑Window**: Shows mastery over *two‑pointer* techniques.  
- **Triangular Numbers**: Demonstrates the ability to connect combinatorics to array processing.  
- **Time & Space Efficiency**: A perfect showcase for interviewers looking for optimal code.  

---

### ✅ Step‑by‑Step Solution (O(n) Time, O(1) Space)

1. **Start** with `len = 1` (a single element is always increasing).  
2. **Loop** from the second element to the end:  
   - If `nums[i] > nums[i-1]`, increment `len`.  
   - Else, the increasing segment ends.  
     * Add `len * (len + 1) / 2` to the answer.  
     * Reset `len = 1`.  
3. After the loop, add the last segment’s contribution.  
4. **Return** the total.

The key insight: **Every contiguous increasing run of length `len` contributes exactly `len*(len+1)/2` subarrays**—the sum of the first `len` natural numbers.

---

### 📜 Code Snippets

| Language | Full Code |
|----------|-----------|
| **Java** | [View Code](#) |
| **Python** | [View Code](#) |
| **C++** | [View Code](#) |

*(Insert the code blocks from section 4 here.)*

---

### 🔎 Common Pitfalls & How to Avoid Them

| Mistake | Why It Fails | Fix |
|---------|--------------|-----|
| Forgetting the last segment after the loop | The final increasing run never gets counted | Add a final `total += len * (len + 1) / 2` after the `for` loop |
| Using `int` for the answer | `len*(len+1)/2` can exceed 32‑bit range when `len ≈ 10⁵` | Use `long`/`long long` |
| Checking `>=` instead of `>` | Equality breaks strictness | Ensure `nums[i] > nums[i-1]` |

---

### 📈 The “Good, the Bad, the Ugly”

| Category | Example | What to Do |
|----------|---------|------------|
| **Good** | Sliding‑window + triangular formula | Keeps the algorithm linear and space‑constant |
| **Bad** | Double nested loops | O(n²) time, impossible for 10⁵ elements |
| **Ugly** | Extra DP array of size `n` | Saves time but wastes memory—avoid unless you need intermediate results |

---

### 🎯 Why This Problem Impresses Interviewers

- **Algorithmic depth**: It tests understanding of *subarray counting* and *combinatorics*.  
- **Optimization**: You show you can move from an obvious brute‑force to an elegant O(n) solution.  
- **Code quality**: Your implementation should be clear, avoid overflow, and handle edge cases gracefully.

---

### 🔗 Further Reading & Resources

- [LeetCode 2393 on the platform](https://leetcode.com/problems/count-strictly-increasing-subarrays/)
- **Two‑Pointer Patterns**: [InterviewBit article](https://www.interviewbit.com/blog/two-pointer-technique/)
- **Triangular Numbers**: [Wikipedia](https://en.wikipedia.org/wiki/Triangular_number)

---

### 🎓 Takeaway

LeetCode 2393 is a micro‑classic: a simple premise but a perfect playground to practice sliding windows, combinatorial counting, and careful handling of integer overflows. Master it, and you’ll have a solid talking point in any coding interview. Good luck! 🚀

---

**Keywords**: LeetCode 2393, Count Strictly Increasing Subarrays, Java solution, Python solution, C++ solution, interview prep, sliding window, two-pointer, algorithm interview, time complexity, space complexity, job interview.