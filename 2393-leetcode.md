---
title: LeetCode 2393. Count Strictly Increasing Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2393. Count Strictly Increasing Subarrays – A Complete Guide  
*(Java | Python | C++) –  O(n) Time, O(1) Extra Space)*  

> **SEO‑friendly headline:**  
> **“Master LeetCode 2393 – Count Strictly Increasing Subarrays in Linear Time (Java, Python, C++) – Boost Your Interview Score”**

---

## 1. Problem Statement

You are given an array `nums` of **positive** integers.  
Count the number of contiguous subarrays that are **strictly increasing**.

> **Examples**  
> *Input:* `[1,3,5,4,4,6]`  
> *Output:* `10`  
> *Explanation:*  
> 6 subarrays of length 1, 3 subarrays of length 2, and 1 subarray of length 3.

> *Input:* `[1,2,3,4,5]`  
> *Output:* `15` – every subarray is strictly increasing.

**Constraints**  
```
1 ≤ nums.length ≤ 10^5
1 ≤ nums[i] ≤ 10^6
```

---

## 2. The Core Idea

Every strictly increasing subarray belongs to a *contiguous increasing segment*:

```
[1, 3, 5]   |   [4]   |   [4, 6]
```

If a segment has length `L`, the number of strictly increasing subarrays inside it equals the sum of the first `L` natural numbers:

```
L + (L-1) + … + 1  =  L*(L+1)/2
```

So the whole problem reduces to:

1. Scan the array once.
2. Whenever the increasing property breaks, compute `L*(L+1)/2` for the segment just finished.
3. Add it to the answer.
4. Repeat until the end.

That’s a classic **two‑pointer / sliding window** in disguise – we only keep track of the current segment’s start index.

---

## 3. Correctness Proof

We prove that the algorithm returns the exact number of strictly increasing subarrays.

### Lemma 1  
All strictly increasing subarrays of `nums` are contained completely inside a single maximal increasing segment.

*Proof.*  
Assume a strictly increasing subarray `A[l..r]` crosses two increasing segments, i.e., there exist `i < j` such that `l ≤ i < j ≤ r` and `nums[i] > nums[i+1]`.  
Then `A[i] > A[i+1]`, contradicting the strictly increasing property of `A`.  
∎

### Lemma 2  
For a maximal increasing segment of length `L`, the number of strictly increasing subarrays inside it equals `L*(L+1)/2`.

*Proof.*  
Any subarray of that segment is defined by a pair of indices `(s, e)` with `0 ≤ s ≤ e < L`.  
Fix the starting index `s`; the ending index can be any of `s, s+1, …, L-1` → `L-s` possibilities.  
Summing over all `s`:

```
Σ_{s=0}^{L-1} (L-s) = L + (L-1) + … + 1 = L*(L+1)/2
```
∎

### Theorem  
The algorithm outputs the total number of strictly increasing subarrays of `nums`.

*Proof.*  
During a single left‑to‑right scan, the algorithm splits `nums` into its maximal increasing segments (Lemma 1).  
For each segment it adds exactly `L*(L+1)/2` to the answer (Lemma 2).  
Since the segments are disjoint and cover the whole array, the sum of their contributions is precisely the total number of strictly increasing subarrays.  
∎

---

## 4. Complexity Analysis

- **Time:**  
  We traverse the array once → **O(n)**.

- **Space:**  
  Only a few integer variables → **O(1)**.

- **Overflow warning:**  
  The answer can be as large as `n*(n+1)/2` with `n = 10^5`, i.e., about `5×10^9`.  
  Use 64‑bit integers (`long` in Java, `long long` in C++, `int` in Python is arbitrary‑precision).

---

## 5. Edge Cases & Pitfalls

| Situation | What to watch for | Fix |
|-----------|-------------------|-----|
| All elements equal | No increasing segment longer than 1 | The algorithm naturally counts each single element. |
| Array length 1 | The while loop never runs | Final segment length `1` is handled after the loop. |
| Very large numbers | Not an issue – comparison only | |
| Overflow | Use 64‑bit type | |

---

## 6. Code Implementations

### 6.1 Java (LeetCode‑compatible)

```java
class Solution {
    public long countSubarrays(int[] nums) {
        long ans = 0L;
        int start = 0;          // beginning of current increasing segment
        int n = nums.length;

        for (int i = 1; i < n; i++) {
            if (nums[i] <= nums[i - 1]) {       // segment ends
                long len = i - start;           // length of segment
                ans += len * (len + 1) / 2;     // add subarrays in this segment
                start = i;                      // new segment starts here
            }
        }
        // handle the last segment
        long len = n - start;
        ans += len * (len + 1) / 2;
        return ans;
    }
}
```

### 6.2 Python 3

```python
class Solution:
    def countSubarrays(self, nums: List[int]) -> int:
        ans = 0
        start = 0
        n = len(nums)

        for i in range(1, n):
            if nums[i] <= nums[i - 1]:
                length = i - start
                ans += length * (length + 1) // 2
                start = i

        length = n - start
        ans += length * (length + 1) // 2
        return ans
```

### 6.3 C++17

```cpp
class Solution {
public:
    long long countSubarrays(vector<int>& nums) {
        long long ans = 0;
        int start = 0;
        int n = nums.size();

        for (int i = 1; i < n; ++i) {
            if (nums[i] <= nums[i - 1]) {
                long long len = i - start;
                ans += len * (len + 1) / 2;
                start = i;
            }
        }
        long long len = n - start;
        ans += len * (len + 1) / 2;
        return ans;
    }
};
```

All three codes run in **O(n)** time and use constant auxiliary memory.

---

## 7. The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Simplicity** | One linear pass; easy to reason | Requires careful handling of the final segment | Mis‑counting when the array ends with an increasing sequence |
| **Time / Space** | Optimal O(n) time, O(1) space | None | None |
| **Overflow Risk** | 64‑bit integer necessary | Forgetting to use `long long` in C++ or `long` in Java | Mis‑using `int` in Java leading to overflow for large inputs |
| **Coding Style** | Clean, few variables | None | Extra debugging prints in production code |
| **Readability** | Self‑explanatory | None | Over‑complicated logic (nested loops, DP) defeats clarity |

### Take‑away for Interviews
- *Show that you know a clean O(n) solution.*  
- *Explain the formula `L*(L+1)/2` and why it works.*  
- *Highlight the overflow guard.*  
- *Mention the two‑pointer sliding window pattern.*  

---

## 8. Why This Problem Helps You Land a Job

| Skill | How the problem showcases it |
|-------|------------------------------|
| **Two‑pointer / sliding window** | You naturally split the array into increasing segments. |
| **Mathematical reasoning** | Using the sum of consecutive integers to count subarrays. |
| **Edge‑case awareness** | Properly handling equal elements, end of array, and overflow. |
| **Time/Space trade‑off** | You get an optimal O(n) algorithm with O(1) space. |
| **Code readability** | Clean code is often a proxy for clean thinking in interviews. |

Mentioning this problem in a portfolio or on LinkedIn with a concise write‑up (like this) demonstrates both algorithmic depth and communication skills—two qualities hiring managers love.

---

## 9. Conclusion

Counting strictly increasing subarrays is a textbook sliding‑window problem.  
A single pass, a simple formula, and careful overflow handling make the solution elegant and production‑ready.  

Feel free to copy the code snippets into your projects, add unit tests, and share on GitHub.  
Good luck cracking LeetCode 2393 and landing that dream role! 🚀