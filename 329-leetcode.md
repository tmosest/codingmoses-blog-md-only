---
title: LeetCode 329. Longest Increasing Path in a Matrix - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 329 – Longest Increasing Path in a Matrix  
> **Hard** | **Time Complexity:** *O(m × n)* | **Space Complexity:** *O(m × n)*

---

## Table of Contents
| Section | Link |
|---|---|
| Problem statement | ⏬ |
| Intuition & Observations | ⏬ |
| Two Gold‑Standard Solutions | ⏬ |
| Java | ⏬ |
| Python | ⏬ |
| C++ | ⏬ |
| SEO‑Friendly Blog Post | ⏬ |

---

## 📄 Problem Statement (LeetCode 329)

Given an `m × n` integer matrix, return the length of the longest increasing path.  
From any cell, you may move **up, down, left, or right** (no diagonals, no wrap‑around).  
You cannot revisit a cell in the same path.

> **Examples**  
> 1. `[[9,9,4],[6,6,8],[2,1,1]] → 4` (`[1,2,6,9]`)  
> 2. `[[3,4,5],[3,2,6],[2,2,1]] → 4` (`[3,4,5,6]`)  
> 3. `[[1]] → 1`

> **Constraints**  
> - `1 ≤ m, n ≤ 200`  
> - `0 ≤ matrix[i][j] ≤ 2³¹ − 1`

---

## 🔍 Intuition & Observations

1. **Graph Perspective** – Each cell is a node. An edge exists from a cell to any neighbor with a *strictly larger* value.
2. **Directed Acyclic Graph (DAG)** – The “larger” relation guarantees no cycles.  
3. **Longest Path in DAG** – Can be solved by **topological sorting** or by **DFS + memoization**.  
4. **Memoization** – Store the longest path starting from each cell; reuse across overlapping sub‑problems.

The two most popular approaches are:

| Approach | How it works | Pros | Cons |
|---|---|---|---|
| DFS + Memoization | Recursively compute the longest path from each cell; cache results. | Simple, natural, easy to implement. | Risk of stack overflow on deep recursion (use iterative or increase recursion limit). |
| Topological Sort (BFS) | Compute out‑degrees; peel nodes with zero out‑degree layer by layer. | No recursion; can be iterative. | Slightly more code; harder to understand at first glance. |

Both have *O(m × n)* time and space.

---

## 🧑‍💻 Code Solutions

Below are fully‑commented, ready‑to‑run solutions in **Java, Python, and C++**.  
Feel free to copy‑paste into your favourite IDE or online judge.

---

### 1️⃣ Java – DFS + Memoization

```java
import java.util.*;

public class Solution {
    private int[][] dirs = {{0,1},{1,0},{0,-1},{-1,0}};
    private int[][] memo;
    private int m, n;

    public int longestIncreasingPath(int[][] matrix) {
        if (matrix == null || matrix.length == 0) return 0;
        m = matrix.length; n = matrix[0].length;
        memo = new int[m][n];
        int best = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                best = Math.max(best, dfs(matrix, i, j));
            }
        }
        return best;
    }

    private int dfs(int[][] matrix, int r, int c) {
        if (memo[r][c] != 0) return memo[r][c];
        int longest = 1;                     // the cell itself
        for (int[] d : dirs) {
            int nr = r + d[0], nc = c + d[1];
            if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                matrix[nr][nc] > matrix[r][c]) {
                longest = Math.max(longest, 1 + dfs(matrix, nr, nc));
            }
        }
        memo[r][c] = longest;                 // cache the result
        return longest;
    }
}
```

---

### 2️⃣ Python – DFS + Memoization

```python
from typing import List

class Solution:
    def longestIncreasingPath(self, matrix: List[List[int]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        m, n = len(matrix), len(matrix[0])
        memo = [[0] * n for _ in range(m)]
        dirs = [(0, 1), (1, 0), (0, -1), (-1, 0)]

        def dfs(r: int, c: int) -> int:
            if memo[r][c]:
                return memo[r][c]
            best = 1
            for dr, dc in dirs:
                nr, nc = r + dr, c + dc
                if 0 <= nr < m and 0 <= nc < n and matrix[nr][nc] > matrix[r][c]:
                    best = max(best, 1 + dfs(nr, nc))
            memo[r][c] = best
            return best

        answer = 0
        for i in range(m):
            for j in range(n):
                answer = max(answer, dfs(i, j))
        return answer
```

---

### 3️⃣ C++ – Topological Sort (BFS)  
*(Demonstrates the alternative approach from the LeetCode editorial.)*

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestIncreasingPath(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) return 0;
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> outdeg(m, vector<int>(n, 0));
        int dirs[4][2] = {{0,1},{1,0},{0,-1},{-1,0}};

        // compute out‑degree for every cell
        for (int r = 0; r < m; ++r) {
            for (int c = 0; c < n; ++c) {
                for (auto &d : dirs) {
                    int nr = r + d[0], nc = c + d[1];
                    if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                        grid[nr][nc] > grid[r][c])
                        ++outdeg[r][c];
                }
            }
        }

        // initial layer – all nodes with outdeg 0
        queue<pair<int,int>> q;
        for (int r = 0; r < m; ++r)
            for (int c = 0; c < n; ++c)
                if (outdeg[r][c] == 0)
                    q.emplace(r, c);

        int height = 0;                       // layers processed
        while (!q.empty()) {
            ++height;
            int sz = q.size();
            for (int i = 0; i < sz; ++i) {
                auto [r, c] = q.front(); q.pop();
                for (auto &d : dirs) {
                    int nr = r + d[0], nc = c + d[1];
                    if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                        grid[nr][nc] < grid[r][c]) {
                        if (--outdeg[nr][nc] == 0)
                            q.emplace(nr, nc);
                    }
                }
            }
        }
        return height;
    }
};
```

---

> ⚠️ **Tip** – In C++ you could also use DFS with memoization, but the BFS version eliminates recursion depth issues and is often favored in large‑input interview settings.

---

## 📚 The SEO‑Friendly Blog Post

> *Title: “LeetCode 329 – Longest Increasing Path in a Matrix | Java, Python, & C++ Solutions”*  
> *Focus keywords: “Longest Increasing Path”, “LeetCode 329”, “DFS memoization”, “topological sort”, “graph DP interview”, “Java solution”, “Python code”, “C++ BFS”*

---

### ✍️ Blog Post

> **Longest Increasing Path in a Matrix – Master the Graph‑DP Interview Question**

---

### 1️⃣ Why This Question Rocks for Interviews

- **Hard** but solvable in linear time – proves you can reduce a problem to a *DAG*.
- Shows understanding of **graph traversal**, **memoization**, **dynamic programming**, and **topological sorting**.
- Commonly asked by top tech companies (Google, Meta, Amazon, Netflix, etc.).
- Demonstrates ability to *reason about states* and *reuse sub‑solutions*.

---

### 2️⃣ Intuition in a Nutshell

> Think of the matrix as a *directed acyclic graph* (DAG) where each edge goes from a lower number to a higher number.  
> The longest increasing path is simply the longest path in this DAG.

---

### 3️⃣ Two Gold‑Standard Approaches

| # | Approach | Code Language | Complexity |
|---|---|---|---|
| **DFS + Memoization** | Recursive depth‑first search + caching | **Java** (classic), **Python** (clear recursion) | *O(m × n)* time, *O(m × n)* space |
| **Topological Sort (BFS)** | Peel nodes with zero out‑degree layer by layer | **C++** (iterative, no recursion) | *O(m × n)* time, *O(m × n)* space |

> *Pick the one that fits your style*:  
> - Recursion feels natural if you’re comfortable with stack depth.  
> - BFS is safer on very large grids (e.g., 200 × 200).

---

### 4️⃣ Complexity Breakdown

- **Time**:  
  - Each cell is visited once, and each edge examined at most once → `O(m × n)`  
- **Space**:  
  - Memoization array or out‑degree matrix → `O(m × n)`  
  - Recursion depth ≤ number of distinct values (≤ m × n) → still linear.

---

### 5️⃣ Common Pitfalls & How to Avoid Them

| Pitfall | Why it hurts | Fix |
|---|---|---|
| **Re‑computing paths** | Exponential blow‑up | Cache results (`memo` or `outdeg` matrix) |
| **Using `>=` instead of `>`** | Allows equal neighbors → invalid paths | Use strict comparison |
| **Stack overflow (Java/Python)** | Deep recursion on 200×200 grid | Use iterative DFS or set higher recursion limit |
| **Off‑by‑one errors in bounds** | Wrong neighbors → crashes | Guard with `0 <= nr < m` & `0 <= nc < n` |
| **Wrong direction array** | Missing a neighbor → under‑count | Double‑check all four directions |

---

### 6️⃣ Why Interviewers Love This Problem

1. **Graph‑DP hybrid** – Shows you can see the underlying graph structure and apply DP.
2. **Edge Cases** – Empty matrix, single‑row/column, all equal values.
3. **Optimization Discussion** – Candidates can talk about recursion depth, iterative BFS, or even in‑place memoization to save memory.
4. **Coding Cleanliness** – Well‑commented code with clear variable names is a must.

---

### 7️⃣ Takeaway & What to Practice Next

| Skill | What LeetCode 329 Teaches |
|---|---|
| DAG Longest Path | Real‑world graphs (dependency resolution, build systems). |
| Memoization | Re‑using overlapping sub‑problems (typical DP pattern). |
| BFS Topological Sort | Iterative DAG processing, avoiding recursion. |

**Next problem to tackle**: *“Maximum Product of Splitted Binary Tree” (LeetCode 1339)* – another graph‑DP interview favorite.

---

## 🚀 Wrap‑up

- **Java, Python, C++** code provided (ready to run).  
- Two proven strategies (DFS + memoization vs. Topological BFS).  
- A full SEO‑friendly blog post explaining the problem, intuition, pitfalls, and why it matters for your next interview.

Good luck slaying LeetCode 329 and impressing your future employer! 🎉

---

> **Follow me on**: GitHub / LinkedIn / Twitter for more interview‑prep content.  
> **Tag:** `#LeetCode329 #LongestIncreasingPath #GraphDP #InterviewPrep #Java #Python #C++`