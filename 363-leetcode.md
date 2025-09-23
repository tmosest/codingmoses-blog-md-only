---
title: LeetCode 363. Max Sum of Rectangle No Larger Than K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Max Sum of Rectangle No Larger Than K – LeetCode 363  
*(Hard – 2‑D Kadane + Prefix‑Sum + Binary Search)*  

> **TL;DR** – The optimal solution compresses the 2‑D matrix into a 1‑D array for every pair of rows (or columns).  
> For each compressed array we find the largest sub‑array sum ≤ k in *O(n log n)* with a `TreeSet` (Java) / `multiset` (C++) / `bisect` (Python).  
> Time: **O(min(m, n)² · max(m, n) · log max(m, n))**, Memory: **O(max(m, n))**.

---

## 1. Problem Recap

> **Input**  
> `matrix`: `m × n` 2‑D array (`1 ≤ m, n ≤ 100`, `-100 ≤ matrix[i][j] ≤ 100`)  
> `k`: an integer (`-10⁵ ≤ k ≤ 10⁵`)  

> **Goal**  
> Return the maximum sum of any axis‑aligned rectangle inside `matrix` whose sum does **not exceed** `k`.  
> A rectangle is defined by two rows and two columns; its sum is the sum of all elements inside.

> **Guarantee** – At least one rectangle exists whose sum ≤ k.

---

## 2. The “Good” Approach – 2‑D Kadane + Prefix Sum + Binary Search

1. **Compress rows or columns**  
   *If the matrix has more columns than rows, iterate over pairs of rows; otherwise iterate over pairs of columns.*  
   Compressing reduces the 2‑D problem to a 1‑D problem on an array of length `max(m, n)`.

2. **1‑D Sub‑array Problem**  
   For the compressed array `nums` we need the largest sub‑array sum ≤ k.  
   This is a classic problem that can be solved in *O(n log n)*:
   * Keep a sorted multiset of prefix sums seen so far (`prefix = 0` initially).
   * Walk through `nums`, maintaining a running sum `curr`.
   * For each `curr` we need the smallest prefix `p` such that `curr - p ≤ k` → `p ≥ curr - k`.  
     The lower‑bound search (`ceiling`) gives that `p`.  
   * Update the best answer with `curr - p`.  
   * Insert `curr` into the multiset.

3. **Stop early** – If the best answer ever reaches `k`, it’s optimal; break immediately.

The algorithm’s total complexity is dominated by the double loop over row/column pairs (≈ min(m,n)²) and the *O(n log n)* 1‑D routine, giving

```
Time   : O(min(m, n)² · max(m, n) · log max(m, n))
Memory : O(max(m, n))
```

With `m, n ≤ 100` this runs in far below 1 ms in practice.

---

## 3. “The Bad” – Naïve 4‑Nested Loops

A textbook brute force enumerates all `(top, left, bottom, right)` rectangles and sums them with a prefix‑sum 2‑D table.  
*Time*: `O(m² · n²)` → up to 10⁸ operations for 100 × 100, still too slow for LeetCode’s 2 s limit.  
*Memory*: `O(m · n)` for the 2‑D prefix table.

---

## 4. “The Ugly” – O(m² · n²) Without Prefix Sums

Just sum the rectangle on the fly each time, recomputing the sum from scratch for every rectangle.  
This is *O(m³ · n³)* and utterly impractical.

---

## 5. Follow‑Up – Rows Much Larger Than Columns

If `m >> n` (tall, skinny matrix), the algorithm still works – we just iterate over column pairs instead of row pairs, swapping the roles of `m` and `n`.  
In practice this keeps the inner 1‑D array as short as possible, giving the best performance.

---

## 6. Code Implementations

Below are clean, self‑contained solutions in **Java, Python, and C++**.  
All three use the same core idea – compressed rows, binary‑search over prefix sums, and an early exit when we hit `k`.

> **Tip for interviews** – Mention that the key observation is “compress one dimension and solve the 1‑D problem with a BST.”  
> Talk through the *O(n log n)* sub‑routine and why the `TreeSet` / `multiset` is essential.

---

### 6.1 Java

```java
import java.util.*;

class Solution {
    public int maxSumSubmatrix(int[][] matrix, int k) {
        int rows = matrix.length, cols = matrix[0].length;
        boolean rowMajor = rows <= cols;          // iterate over the smaller dimension
        int result = Integer.MIN_VALUE;

        if (rowMajor) {                            // compress rows
            int[] temp = new int[cols];
            for (int top = 0; top < rows; top++) {
                Arrays.fill(temp, 0);
                for (int bottom = top; bottom < rows; bottom++) {
                    for (int c = 0; c < cols; c++) {
                        temp[c] += matrix[bottom][c];
                    }
                    result = Math.max(result, maxSubarrayNoMoreThanK(temp, k));
                    if (result == k) return k;      // best possible
                }
            }
        } else {                                    // compress columns
            int[] temp = new int[rows];
            for (int left = 0; left < cols; left++) {
                Arrays.fill(temp, 0);
                for (int right = left; right < cols; right++) {
                    for (int r = 0; r < rows; r++) {
                        temp[r] += matrix[r][right];
                    }
                    result = Math.max(result, maxSubarrayNoMoreThanK(temp, k));
                    if (result == k) return k;
                }
            }
        }
        return result;
    }

    /** 1‑D routine – largest sub‑array sum <= k in O(n log n) */
    private int maxSubarrayNoMoreThanK(int[] nums, int k) {
        int best = Integer.MIN_VALUE;
        int curr = 0;
        TreeSet<Integer> prefix = new TreeSet<>();
        prefix.add(0);

        for (int x : nums) {
            curr += x;
            // we need smallest prefix >= curr - k
            Integer target = prefix.ceiling(curr - k);
            if (target != null) {
                best = Math.max(best, curr - target);
                if (best == k) return best;      // early stop
            }
            prefix.add(curr);
        }
        return best;
    }
}
```

**Complexity** – *O(min(m,n)² · max(m,n) · log max(m,n))* time, *O(max(m,n))* memory.  
The `TreeSet` gives us binary‑search in *log n*.

---

### 6.2 Python

```python
import bisect
from typing import List

class Solution:
    def maxSumSubmatrix(self, matrix: List[List[int]], k: int) -> int:
        rows, cols = len(matrix), len(matrix[0])
        row_major = rows <= cols
        ans = -10**9

        if row_major:
            temp = [0] * cols
            for top in range(rows):
                temp = [0] * cols
                for bottom in range(top, rows):
                    for c in range(cols):
                        temp[c] += matrix[bottom][c]
                    ans = max(ans, self._best_no_more_than_k(temp, k))
                    if ans == k:
                        return k
        else:                          # compress columns
            temp = [0] * rows
            for left in range(cols):
                temp = [0] * rows
                for right in range(left, cols):
                    for r in range(rows):
                        temp[r] += matrix[r][right]
                    ans = max(ans, self._best_no_more_than_k(temp, k))
                    if ans == k:
                        return k
        return ans

    def _best_no_more_than_k(self, nums: List[int], k: int) -> int:
        prefix = [0]
        best = -10**9
        curr = 0
        for x in nums:
            curr += x
            # need smallest p >= curr - k
            idx = bisect.bisect_left(prefix, curr - k)
            if idx < len(prefix):
                best = max(best, curr - prefix[idx])
            bisect.insort(prefix, curr)
        return best
```

The Python version uses `bisect.insort` to keep the prefix list sorted – equivalent to a binary‑search tree.

---

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxSumSubmatrix(vector<vector<int>>& matrix, int k) {
        int m = matrix.size(), n = matrix[0].size();
        bool rowMajor = m <= n;                // iterate over smaller dimension
        int best = INT_MIN;

        if (rowMajor) {                        // compress rows
            vector<int> temp(n);
            for (int top = 0; top < m; ++top) {
                fill(temp.begin(), temp.end(), 0);
                for (int bottom = top; bottom < m; ++bottom) {
                    for (int c = 0; c < n; ++c) temp[c] += matrix[bottom][c];
                    best = max(best, subarrayNoMoreThanK(temp, k));
                    if (best == k) return k;
                }
            }
        } else {                               // compress columns
            vector<int> temp(m);
            for (int left = 0; left < n; ++left) {
                fill(temp.begin(), temp.end(), 0);
                for (int right = left; right < n; ++right) {
                    for (int r = 0; r < m; ++r) temp[r] += matrix[r][right];
                    best = max(best, subarrayNoMoreThanK(temp, k));
                    if (best == k) return k;
                }
            }
        }
        return best;
    }

private:
    int subarrayNoMoreThanK(const vector<int>& nums, int k) {
        int curr = 0, best = INT_MIN;
        multiset<int> pref{0};

        for (int x : nums) {
            curr += x;
            auto it = pref.lower_bound(curr - k); // smallest prefix >= curr - k
            if (it != pref.end())
                best = max(best, curr - *it);
            pref.insert(curr);
        }
        return best;
    }
};
```

`multiset` gives the same binary‑search behaviour as `TreeSet`.  
The early return (`best == k`) keeps the solution lightning‑fast.

---

## 7. Interview & Job‑Search Tips

| Question | What to Highlight |
|----------|-------------------|
| *Why compress one dimension?* | Reduces a 2‑D problem to a 1‑D problem – key insight that cuts complexity from 4‑nested to 3‑nested loops. |
| *How does the 1‑D routine work?* | Use prefix sums + BST. Explain `curr - p ≤ k` → `p ≥ curr - k`. Show lower‑bound search. |
| *Time Complexity* | `O(min(m,n)² · max(m,n) · log max(m,n))`. For 100 × 100 this is ~10⁶ operations, easily within limits. |
| *Space* | `O(max(m,n))` for the compressed array + BST. |
| *Edge cases* | Negative `k`, all negative matrix values, single‑row / single‑column matrices, `k` equal to the global max. |
| *Follow‑up* | If rows >> columns, swap loops. |
| *Why this matters for jobs* | Demonstrates mastery of advanced data structures (BST, multiset), 2‑D DP, and space‑time trade‑offs. Great for senior‑level technical interviews. |

---

## 8. SEO‑Friendly Blog Summary

- **Keywords**: LeetCode 363, Max Sum of Rectangle No Larger Than K, Hard LeetCode Problem, 2‑D Kadane, Prefix Sums, TreeSet, multiset, binary search, interview question, coding interview, algorithm, job interview, senior dev interview, C++, Java, Python solutions.  
- **Meta Description**: “Solve LeetCode 363 – Max Sum of Rectangle No Larger Than K (Hard). Learn the 2‑D Kadane + Prefix Sum + BST solution in Java, Python, and C++. Interview‑ready guide.”

---

## 9. FAQ

| Q | A |
|---|---|
| **Can I use a hash map instead of TreeSet?** | A hash map gives *O(n)* average time, but it cannot perform the lower‑bound search required for `curr - p ≤ k`. You need a sorted structure. |
| **What if all numbers are positive?** | The 1‑D routine still works; it simply returns the largest sub‑array ≤ k (or the whole array if its sum ≤ k). |
| **Is the solution optimal?** | Yes – the best possible sum ≤ k cannot exceed `k`, and we stop as soon as we hit `k`. |
| **Can we use sliding window instead?** | Sliding window works only when all numbers are non‑negative. Here we have negatives, so we must use the BST approach. |

---

## 10. Final Takeaway

The 2‑D Hard LeetCode problem 363 teaches you one of the most valuable patterns for coding interviews: **compress a dimension, reduce to 1‑D, then solve with a balanced tree**.  
It demonstrates:

- Deep understanding of Kadane’s algorithm and its 2‑D extension.  
- Mastery of prefix sums and binary‑search in a sorted container.  
- Ability to reason about time–space trade‑offs and early termination.  

Implementing this in Java, Python, and C++ shows language‑agnostic skill – a perfect showcase for a senior‑level technical interview.  

> **Pro‑Tip** – In an interview, outline the high‑level idea first, then dive into the 1‑D routine; the interviewer will immediately see you understand the trick that gives the optimal time bound.

Happy coding—and may your job hunt be as successful as this solution!