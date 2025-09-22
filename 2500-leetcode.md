---
title: LeetCode 2500. Delete Greatest Value in Each Row - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Delete Greatest Value in Each Row – LeetCode 2500  
**Java | Python | C++ – Fast, Clean & Interview‑Ready Solutions**  

---

## Table of Contents
1. [Problem Overview](#problem-overview)  
2. [Greedy Insight & Algorithm](#greedy-insight-&-algorithm)  
3. [Time & Space Complexity](#time-&-space-complexity)  
4. [Implementation Highlights](#implementation-highlights)  
   * Java  
   * Python  
   * C++  
5. [Good, Bad & Ugly – What Recruiters Care About](#good-bad-&-ugly)  
6. [Interview Tips & Frequently Asked Questions](#interview-tips)  
7. [SEO‑Optimized Summary](#seo-summary)  
8. [References & Further Reading](#references)

---

## Problem Overview <a name="problem-overview"></a>
You’re given an **m × n** grid of positive integers. Repeatedly perform the following operation until the grid is empty:

1. **Delete** the largest value from each row (any one if there’s a tie).  
2. **Add** the maximum of all deleted values to a running total.

Return the final total.

> **Example**  
> grid = `[[1,2,4],[3,3,1]]` → Answer = **8**  
> (Delete `4` & `3` → add `4`; Delete `2` & `3` → add `3`; Delete `1` & `1` → add `1`).

**Constraints**  
- `1 ≤ m, n ≤ 50`  
- `1 ≤ grid[i][j] ≤ 100`

---

## Greedy Insight & Algorithm <a name="greedy-insight-&-algorithm"></a>
When the rows are **sorted in ascending order**, the *k*‑th deletion always removes the element in the *k*‑th column of the sorted row.  
Thus the maximum deleted value in the *k*‑th round equals the maximum among the *k*‑th column of the **sorted** grid.

**Algorithm**  
1. **Sort each row** in ascending order.  
2. For every column index `j` (0 … n‑1):  
   * Find `max(grid[i][j])` for all rows `i`.  
   * Add that maximum to the answer.  
3. Return the answer.

This is a classic greedy simulation; no back‑tracking or DP is needed.

---

## Time & Space Complexity <a name="time-&-space-complexity"></a>
| Metric | Formula | Reason |
|--------|---------|--------|
| **Time** | `O(m · n · log n)` | Sorting each of the `m` rows costs `O(n log n)`; scanning all columns costs `O(m · n)`. |
| **Space** | `O(1)` (in‑place) | Sorting is done in‑place; only a few auxiliary variables are used. |

With `m, n ≤ 50`, this easily satisfies the limits.

---

## Implementation Highlights <a name="implementation-highlights"></a>
Below are **ready‑to‑copy** solutions in Java, Python, and C++.  
Each implementation follows the same O(m · n · log n) approach.

### Java

```java
import java.util.Arrays;

class Solution {
    public int deleteGreatestValue(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int total = 0;

        // Sort each row ascending
        for (int[] row : grid) {
            Arrays.sort(row);
        }

        // For each column, pick the largest value among all rows
        for (int col = 0; col < n; col++) {
            int colMax = 0;
            for (int row = 0; row < m; row++) {
                colMax = Math.max(colMax, grid[row][col]);
            }
            total += colMax;
        }

        return total;
    }
}
```

### Python

```python
from typing import List

class Solution:
    def deleteGreatestValue(self, grid: List[List[int]]) -> int:
        # Sort each row (in place)
        for row in grid:
            row.sort()

        total = 0
        n = len(grid[0])
        for col in range(n):
            col_max = max(row[col] for row in grid)
            total += col_max

        return total
```

### C++

```cpp
class Solution {
public:
    int deleteGreatestValue(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        int total = 0;

        // Sort each row
        for (auto &row : grid)
            sort(row.begin(), row.end());

        // Scan columns
        for (int col = 0; col < n; ++col) {
            int colMax = 0;
            for (int row = 0; row < m; ++row)
                colMax = max(colMax, grid[row][col]);
            total += colMax;
        }

        return total;
    }
};
```

> **Why sorting is enough?**  
> After sorting, each row’s “deleted” element at round *k* is simply the element at index *k*.  
> The algorithm then turns the process into a simple column‑wise maximum scan.

---

## Good, Bad & Ugly – What Recruiters Care About <a name="good-bad-&-ugly"></a>
| Category | What Recruiters Look For | Common Pitfalls |
|----------|-------------------------|-----------------|
| **Good** | - Clear, self‑documented code<br>- No over‑engineering (e.g., no heaps unless needed)<br>- Handles edge cases (`m==1`, `n==1`) naturally | - Use of built‑in sorting keeps complexity minimal |
| **Bad** | - Hard‑coded assumptions (e.g., grid always rectangular) | - Assuming all rows have same length; guard against malformed input |
| **Ugly** | - Manual simulation with queues or heaps, adding unnecessary log‑factor overhead<br>- Using global variables or mutating input in surprising ways | - Over‑complicating with priority queues per row (works but slower for 50×50) |

**Bottom line:** The *sort‑and‑scan* solution is concise, fast, and aligns with interview expectations for a greedy simulation problem.

---

## Interview Tips & Frequently Asked Questions <a name="interview-tips"></a>
1. **Explain the greedy choice**: “Sorting rows ensures that the k‑th deletion is always the element in the k‑th column.”
2. **Discuss alternatives**: Mention priority queues (max‑heaps) per row as a valid but slower approach.
3. **Complexity check**: Show that `O(m·n·log n)` satisfies the constraints (`≤ 50 × 50`).
4. **Edge cases**: Test `[[1]]`, `[[5,5],[5,5]]`, `[[1,100],[100,1]]`.
5. **Ask clarifying questions**: If the problem statement is ambiguous (e.g., “any deleted element if tie”), confirm with the interviewer.

---

## SEO‑Optimized Summary <a name="seo-summary"></a>
> **Delete Greatest Value in Each Row – LeetCode 2500**  
> Master this easy‑to‑solve greedy problem with concise Java, Python, and C++ solutions.  
> Learn the optimal `O(m·n·log n)` algorithm, understand why sorting rows works, and see how to avoid common pitfalls.  
> Perfect for *coding interview* practice, *Java solution*, *Python solution*, *C++ solution*, *LeetCode interview prep*, and *greedy algorithm* topics.  

Use this article to boost your technical blog, ace your LeetCode interview, and showcase a clean, interview‑ready implementation.

---

## References & Further Reading <a name="references"></a>
- [LeetCode 2500 – Delete Greatest Value in Each Row](https://leetcode.com/problems/delete-greatest-value-in-each-row/)  
- [Greedy Algorithms – YouTube Playlist](https://www.youtube.com/playlist?list=PLcR2n8XkK1kR0Wm4w2Y1k9R6Yy2Y)  
- [Sorting vs. Priority Queue Trade‑offs](https://stackoverflow.com/questions/1577611/when-to-use-heap-vs-sort)  

---  

Feel free to adapt the code snippets, run them on your local environment, and practice the discussion points. Good luck in your next coding interview!