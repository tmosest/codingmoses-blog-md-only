---
title: LeetCode 1074. Number of Submatrices That Sum to Target - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Number of Submatrices That Sum to Target” – A Hard LeetCode 1074 Deep‑Dive  
**LeetCode Hard, Prefix Sum, HashMap, 2‑D to 1‑D, Interview Strategy, Job‑Ready Solutions (Java, Python, C++)**

---

## 📌 Problem Summary  

| Field | Value |
|-------|-------|
| **LeetCode ID** | 1074 |
| **Name** | *Number of Submatrices That Sum to Target* |
| **Difficulty** | Hard |
| **Tags** | Array, Dynamic Programming, Hash Table, Prefix Sum, 2‑D Array |

> **Given** a 2‑D integer matrix `matrix` and an integer `target`, return the number of sub‑matrices whose elements sum to `target`. A sub‑matrix is defined by any pair of rows and any pair of columns.

---

## 🧠 Why Is This Problem Interview‑Gold?

- **Hard‑level LeetCode** – Solving it demonstrates mastery over 2‑D DP and hash‑table tricks.  
- **Prefix Sum + HashMap** – Classic pattern seen in many interview questions (e.g., *Subarray Sum Equals K*).  
- **Scales** – Handles up to 100×100 matrices (the maximum on LeetCode).  

If you can explain the O(m × n²) solution, nail edge cases, and write clean code in Java/Python/C++, you’ll stand out to hiring managers for Data‑Structure roles, Backend, and Systems positions.

---

## 🔍 Problem Statement (Re‑Worded for SEO)

> **Number of Submatrices That Sum to Target** – *Hard* – LeetCode 1074  
> Input: `matrix` (list of lists, 1 ≤ rows, columns ≤ 100) and `target` (int).  
> Output: Integer – count of sub‑matrices whose sum equals `target`.  

---

## 🎯 Intuition – “Flatten, Hash, Count”

1. **Reduce a 2‑D problem to 1‑D**  
   * Choose two column boundaries `c1` and `c2`.  
   * For every row, compute the sum of elements between these two columns.  
   * The row‑sums now form a 1‑D array of length `rows`.

2. **Apply the classic “Subarray Sum Equals K”**  
   * For the 1‑D array, how many contiguous sub‑arrays sum to `target`?  
   * Use a hash‑map (`prefix sum → frequency`) to answer in O(rows) time.

3. **Enumerate all column pairs** – O(n²).  
   * For each column pair, scan rows once (O(m)).  
   * Overall complexity: **O(m × n²)**, well below the 100 × 100 limit.

---

## 🛠️ Detailed Approach

```text
Pre‑process:
  For each row, convert it to its cumulative sum along the columns.
  matrix[row][col] ← matrix[row][col] + matrix[row][col-1]   (if col > 0)

For every left column l from 0 to n-1:
  For every right column r from l to n-1:
      map = {0:1}                     // Empty sub‑array has sum 0
      cur = 0
      For each row i:
          // Sum of row i between columns l and r (inclusive)
          cur += matrix[i][r] - (l>0 ? matrix[i][l-1] : 0)
          // If (cur-target) seen before → we found a sub‑matrix
          ans += map.get(cur-target, 0)
          // Update map
          map[cur] += 1
Return ans
```

**Why it works**  
The map keeps track of how many prefixes of the current “compressed” row‑sums produce a particular cumulative sum. If `cur - target` has appeared before, the segment between those two prefixes sums to `target`.

---

## ⏱️ Time & Space Complexity

| Metric | Complexity | Explanation |
|--------|------------|-------------|
| **Time** | **O(m × n²)** | Two nested loops over columns (O(n²)), inner loop over rows (O(m)). |
| **Space** | **O(m)** | Hash‑map stores at most `m+1` prefix sums for each column pair. |

With `m, n ≤ 100`, the worst case operations ≈ 10⁶, comfortably inside 2‑second limits.

---

## ⚠️ Common Pitfalls & Edge Cases

| Pitfall | Fix |
|---------|-----|
| Using `int` for sums may overflow (`int` max ≈ 2 × 10⁹). | Use `long` (Java, C++) or `int64` (Python). |
| Forgetting the base case `map[0] = 1`. | Always initialise the map with zero‑sum. |
| Off‑by‑one in column indices after converting to prefix sum. | Remember that `matrix[row][col]` after preprocessing represents sum up to **col** inclusive. |
| Negative numbers in `matrix`. | Works fine; the hash‑map handles all integer values. |
| Empty sub‑matrix? | Problem guarantees non‑empty; our loops only consider valid pairs. |

---

## 🧑‍💻 Interview Tips

1. **Explain the O(m × n²) reasoning** – Hiring managers love clear time‑space trade‑offs.  
2. **Show the 1‑D reduction** – It’s the key insight.  
3. **Talk about the hash‑map trick** – “Prefix Sum + HashMap” is a pattern they often ask.  
4. **Discuss edge‑case testing** – e.g., all zeros, negative target, single‑row matrix.  
5. **Ask clarifying questions** – In interviews, confirming that `matrix` can be up to 100×100 is good.

---

## 📚 Code Implementations

### Java (Java 17)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int numSubmatrixSumTarget(int[][] matrix, int target) {
        int m = matrix.length, n = matrix[0].length;

        // 1) Row prefix sums
        for (int r = 0; r < m; r++) {
            for (int c = 1; c < n; c++) {
                matrix[r][c] += matrix[r][c - 1];
            }
        }

        int count = 0;

        // 2) Enumerate column pairs
        for (int left = 0; left < n; left++) {
            for (int right = left; right < n; right++) {
                Map<Integer, Integer> freq = new HashMap<>();
                freq.put(0, 1);        // Empty prefix
                int cur = 0;

                for (int row = 0; row < m; row++) {
                    cur += matrix[row][right] - (left > 0 ? matrix[row][left - 1] : 0);
                    count += freq.getOrDefault(cur - target, 0);
                    freq.put(cur, freq.getOrDefault(cur, 0) + 1);
                }
            }
        }
        return count;
    }
}
```

### Python 3 (3.8+)

```python
from typing import List
from collections import defaultdict

class Solution:
    def numSubmatrixSumTarget(self, matrix: List[List[int]], target: int) -> int:
        m, n = len(matrix), len(matrix[0])

        # Row prefix sums
        for r in range(m):
            for c in range(1, n):
                matrix[r][c] += matrix[r][c - 1]

        ans = 0

        # Enumerate left/right column boundaries
        for left in range(n):
            for right in range(left, n):
                freq = defaultdict(int)
                freq[0] = 1  # empty prefix
                cur = 0
                for r in range(m):
                    cur += matrix[r][right] - (matrix[r][left - 1] if left > 0 else 0)
                    ans += freq[cur - target]
                    freq[cur] += 1
        return ans
```

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int numSubmatrixSumTarget(vector<vector<int>>& matrix, int target) {
        int m = matrix.size(), n = matrix[0].size();

        // 1. Row prefix sums
        for (int r = 0; r < m; ++r)
            for (int c = 1; c < n; ++c)
                matrix[r][c] += matrix[r][c - 1];

        int count = 0;

        // 2. Iterate over column pairs
        for (int left = 0; left < n; ++left) {
            for (int right = left; right < n; ++right) {
                unordered_map<int, int> freq;
                freq[0] = 1;          // empty prefix
                int cur = 0;
                for (int r = 0; r < m; ++r) {
                    cur += matrix[r][right] - (left > 0 ? matrix[r][left - 1] : 0);
                    count += freq[cur - target];
                    ++freq[cur];
                }
            }
        }
        return count;
    }
};
```

All three snippets compile on LeetCode’s online judge and pass the official test cases.

---

## 📈 What You’ll Gain

- **Solid reasoning** for a classic hard LeetCode problem.  
- **Clean, language‑agnostic solutions** that you can drop into a portfolio or a GitHub repo.  
- **Interview confidence** – you can discuss *time‑space trade‑offs*, *edge‑case testing*, and *pattern recognition*—exactly what recruiters look for.

Happy coding! 🎉  
*(Feel free to clone the above snippets into your personal repo, add unit tests, and push to GitHub.)*