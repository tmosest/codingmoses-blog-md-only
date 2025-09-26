---
title: LeetCode 3225. Maximum Score From Grid Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# **Maximum Score From Grid Operations (LeetCode 3225) – A Deep‑Dive**

> *Hard – 5 – 100 × 100 grid – DP + recursion – O(n³) time, O(n³) space*

> **Keywords** – LeetCode, Dynamic Programming, Java, Python, C++, Interview, Grid Operations, Coding Interview, Problem Solving, Job Interview, Algorithm Analysis

---

## 1. Problem Statement

You are given an `n × n` matrix `grid`.  
All cells are initially **white**.

**Operation**  
Pick any cell `(i , j)` and paint **black** every cell in column `j` from row `0` up to row `i` (inclusive).

**Score**  
For every *white* cell that has a **horizontally adjacent black** cell (to its left or right) you earn `grid[i][j]`.  
The score of the whole grid is the sum of all such earned values.

> **Goal** – Perform any number of operations to maximize the score.

---

## 2. Why Is This Hard?

| What makes it hard | Explanation |
|--------------------|-------------|
| **Large value range** | `grid[i][j]` can be up to `10⁹`, so the answer doesn’t fit in a 32‑bit integer. |
| **Complex state** | Each column may be partially painted; the future impact depends on *how many* rows were already painted in previous columns. |
| **Interdependency between columns** | Painting a cell in column `j` can affect the possibility of painting cells in column `j+1` (because a black cell blocks a white one). |
| **Exponential naïve search** | Every column has `n+1` possibilities (paint 0 … n rows). Brute force would be `O((n+1)^n)` – impossible for `n = 100`. |

The accepted solution uses **dynamic programming** with a 3‑dimensional state that captures exactly the information needed for optimal sub‑problems.

---

## 3. Intuition & State Design

Let’s look at the columns from **left to right**.

When we are at column `c`, we already know:

1. **`last1`** – the number of rows painted **by column `c-1`**.  
   This tells us which rows are already black at the *start* of column `c`.

2. **`last2`** – the number of rows painted **by column `c-2`**.  
   This matters because a black cell in column `c-2` may block a white cell in column `c-1`, which in turn may block a white cell in column `c`.

With these two numbers we can compute the contribution of column `c` for any choice we make in this column:

* **Case I – Don’t paint anything in column `c`**  
  We only keep the rows that were already black from `last2`.  

* **Case II – Paint some rows in column `c` and **block** column `c+1`**  
  If we paint rows starting from `last1` to `i`, we must set `last1` for the next column to `i+1` and `last2` to `0` (because the rows from `last1` will be black and no longer “available” for column `c+1`).  

* **Case III – Paint rows in column `c` but **do not block** column `c+1`**  
  We keep the rows from `last2` and paint all rows from `last2` downwards.  
  For the next column, `last1` becomes `0` and `last2` becomes the new painted count.

This leads to the DP recurrence:

```
dp[col][last1][last2] = maximum score starting from column col
```

The transition enumerates all possible choices for the current column (Case I, II, III) and recurses to the next column, keeping track of the new `last1` and `last2`.

---

## 4. Complexity

* **Time** – `O(n³)`  
  For each column (`n`) we iterate over `last1` (`≤ n`) and `last2` (`≤ n`). Inside we loop over rows again (`≤ n`).  
* **Space** – `O(n³)` for memoization (can be reduced to `O(n²)` if you keep only two layers, but the simple 3‑dimensional table is easier to understand).  
* **Answer Size** – Use 64‑bit integers (`long`/`int64_t`/`int` in Python).

---

## 5. Reference Implementations

Below are clean, well‑commented implementations in **Java**, **Python**, and **C++**.

---

### 5.1 Java (Recursive DP with Memoization)

```java
import java.util.*;

public class Solution {
    // memoization table
    private Long[][][] memo;
    private int[][] grid;
    private int n;

    public long maximumScore(int[][] grid) {
        this.grid = grid;
        this.n = grid.length;
        // +1 because last1 / last2 can be equal to n (paint all rows)
        memo = new Long[n + 1][n + 1][n + 1];
        return dfs(0, 0, 0);
    }

    /** @param col    current column (0‑based)
     *  @param last1  rows painted by column col‑1
     *  @param last2  rows painted by column col‑2
     */
    private long dfs(int col, int last1, int last2) {
        if (col == n) return 0;                // finished all columns
        if (memo[col][last1][last2] != null)
            return memo[col][last1][last2];

        long best = 0;

        /* ---- Case I : do not paint this column ---- */
        long fromLast2 = 0;
        for (int r = 0; r < last2; r++) fromLast2 += grid[r][col];
        best = Math.max(best,
                        fromLast2 + dfs(col + 1, 0, last1));

        /* ---- Case II : paint some rows, block next column ---- */
        long sum = 0;
        if (col + 1 < n) {
            for (int r = last1; r < n; r++) {
                sum += grid[r][col];
                best = Math.max(best,
                        sum + dfs(col + 1, r + 1, 0));
            }
        }

        /* ---- Case III : paint rows but do not block next column ---- */
        long temp = 0;
        for (int r = 0; r < n; r++) {
            if (r < last2) temp -= grid[r][col];
            best = Math.max(best,
                    temp + dfs(col + 1, 0, r + 1));
        }

        memo[col][last1][last2] = best;
        return best;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] g = {
            {0,0,0,0,0},
            {0,0,3,0,0},
            {0,1,0,0,0},
            {5,0,0,3,0},
            {0,0,0,0,2}
        };
        System.out.println(s.maximumScore(g)); // 11
    }
}
```

---

### 5.2 Python (Recursion + `functools.lru_cache`)

```python
from functools import lru_cache
from typing import List

class Solution:
    def maximumScore(self, grid: List[List[int]]) -> int:
        n = len(grid)

        @lru_cache(maxsize=None)
        def dfs(col: int, last1: int, last2: int) -> int:
            if col == n:
                return 0

            # --- Case I: skip this column
            from_last2 = sum(grid[r][col] for r in range(last2))
            best = from_last2 + dfs(col + 1, 0, last1)

            # --- Case II: paint rows, block next column
            sum_ = 0
            if col + 1 < n:
                for r in range(last1, n):
                    sum_ += grid[r][col]
                    best = max(best, sum_ + dfs(col + 1, r + 1, 0))

            # --- Case III: paint rows, but keep next column open
            temp = 0
            for r in range(n):
                if r < last2:
                    temp -= grid[r][col]
                best = max(best, temp + dfs(col + 1, 0, r + 1))

            return best

        return dfs(0, 0, 0)

# Sample test
if __name__ == "__main__":
    s = Solution()
    g = [
        [0,0,0,0,0],
        [0,0,3,0,0],
        [0,1,0,0,0],
        [5,0,0,3,0],
        [0,0,0,0,2]
    ]
    print(s.maximumScore(g))   # 11
```

---

### 5.3 C++ (Top‑Down DP with Memoization)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maximumScore(vector<vector<int>>& grid) {
        n = grid.size();
        this->grid = &grid;
        memo.assign(n + 1, vector<vector<long long>>(n + 1, vector<long long>(n + 1, LLONG_MIN)));
        return dfs(0, 0, 0);
    }

private:
    int n;
    vector<vector<int>>* grid;
    vector<vector<vector<long long>>> memo;

    long long dfs(int col, int last1, int last2) {
        if (col == n) return 0;
        if (memo[col][last1][last2] != LLONG_MIN) return memo[col][last1][last2];

        long long best = 0;

        // Case I: skip column
        long long fromLast2 = 0;
        for (int r = 0; r < last2; ++r) fromLast2 += (*grid)[r][col];
        best = max(best, fromLast2 + dfs(col + 1, 0, last1));

        // Case II: paint rows, block next column
        long long sum = 0;
        if (col + 1 < n) {
            for (int r = last1; r < n; ++r) {
                sum += (*grid)[r][col];
                best = max(best, sum + dfs(col + 1, r + 1, 0));
            }
        }

        // Case III: paint rows, keep next column open
        long long temp = 0;
        for (int r = 0; r < n; ++r) {
            if (r < last2) temp -= (*grid)[r][col];
            best = max(best, temp + dfs(col + 1, 0, r + 1));
        }

        memo[col][last1][last2] = best;
        return best;
    }
};

// Example usage
int main() {
    Solution s;
    vector<vector<int>> g = {
        {0,0,0,0,0},
        {0,0,3,0,0},
        {0,1,0,0,0},
        {5,0,0,3,0},
        {0,0,0,0,2}
    };
    cout << s.maximumScore(g) << endl; // 11
}
```

---

## 6. “Good” vs “Bad” Solutions – A Checklist

| Question | Good Solution? | Why |
|----------|----------------|-----|
| **Handles 64‑bit answer?** | ✅ | `long` in Java, `long long` in C++, `int` in Python. |
| **Runs within time on n=100?** | ✅ | `O(n³)` passes in < 2 s for typical judges. |
| **Memoizes correctly?** | ✅ | 3‑dimensional table or `lru_cache`. |
| **Avoids integer overflow?** | ✅ | 64‑bit types, `LLONG_MIN` sentinel. |
| **Follows DP recurrence cleanly?** | ✅ | Explicit Case I/II/III loops. |
| **Is the code well‑commented?** | ✅ | Inline comments help readability. |

If any of the above is missing, the solution may be **bad** (e.g., using `int` in Java, or missing memoization leading to exponential time).

---

## 7. Testing & Edge Cases

| Grid Size | Edge Case | Expected Behaviour |
|-----------|-----------|--------------------|
| `1x1` | Minimal grid | Answer equals grid[0][0] |
| `100x100` | All zeros | Result `0` |
| `100x100` | All 1s | Result equals total sum of all cells |
| `n = 100` with random values | Performance | Should finish in ~1‑2 seconds on Java/ C++ |
| `n = 50` but values close to `10⁹` | Overflow risk | 64‑bit must be used |

Test these with small scripts to guarantee correctness before submitting.

---

## 8. What Makes a *Good* Solution?

A **good** DP solution for this problem will:

1. **Encapsulate the exact state** needed for optimal sub‑problems (here: `col, last1, last2`).  
2. **Enumerate all valid choices** (Case I/II/III) without duplication.  
3. **Memoize** results to avoid recomputation.  
4. **Handle large numbers** using 64‑bit integers.  
5. **Stay within time/space limits** (`O(n³)`).  
6. **Be easy to read** – use descriptive variable names and comments.

---

## 9. Takeaway & Practical Lessons

* **3‑dimensional DP** may seem heavy, but it is often the *right* way to encode dependencies that span more than one step (here, `last1` and `last2`).  
* **Memoization** can be implemented in many languages: recursion + `lru_cache` in Python, `@lru_cache` or manual tables in Java/C++.  
* **Complexity analysis**: Always confirm that the DP recurrence actually reduces the problem size.  
* **Code clarity**: A clean implementation (as shown above) is more valuable in interviews and on production systems than a highly optimized but opaque version.  

---

## 10. Final Words

Dynamic programming is a *powerful* technique that turns an otherwise intractable combinatorial explosion into a polynomial‑time solution.  
By carefully studying the interaction between columns in this problem, we crafted a 3‑dimensional DP that captures exactly the information needed for optimal decisions.  

If you’re preparing for technical interviews, mastering this kind of state design will help you tackle many “grid‑painting” or “blocking” problems that appear in contests or real‑world systems.

Happy coding! 🚀

--- 

**Search tags**: #Java #Python #C++ #DynamicProgramming #LeetCode #InterviewQuestion #DPStateDesign #64bitInteger #Memoization
--- 
*Posted on 2024‑03‑18 by “CodingMentor”* 
--- 
*If you liked this post, consider subscribing for more clean DP and algorithm tutorials.* 
--- 
*Feel free to open issues if you spot bugs or want optimizations.*