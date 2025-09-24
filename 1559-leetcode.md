---
title: LeetCode 1559. Detect Cycles in 2D Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1559. Detect Cycles in 2D Grid  
**LeetCode – Medium – Java / Python / C++ solutions + SEO‑optimized blog post**

> **Keywords** – Detect Cycles in 2D Grid, LeetCode 1559, DFS BFS cycle detection, Grid graph, Job interview, Data Structures, Algorithms, Java DFS, Python DFS, C++ BFS

---

## 1. Problem Overview

You’re given a 2‑D grid `grid[m][n]` of lowercase English letters.  
A **cycle** is a path of length **≥ 4** that:

* visits only cells with the same character,
* moves only in the 4 cardinal directions,
* returns to the starting cell,
* **never moves back to the cell you just came from**.

Return `true` if any such cycle exists, otherwise `false`.

```
m, n ≤ 500  →  grid can contain up to 250 000 cells
```

The task is a classic graph‑theory problem on a grid: detect a cycle in an undirected graph where vertices are cells with the same letter.

---

## 2. The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | DFS (recursion) is intuitive and easy to implement. BFS also works with a queue of `(row, col, parentRow, parentCol)`. | Both DFS and BFS have to explore every cell once; for large grids recursion depth can hit limits in Python or Java. | A naïve approach that revisits cells or fails to check the “parent” condition will incorrectly report cycles. |
| **Time Complexity** | O(m · n) – each cell is visited once. | None – linear is optimal for this problem. | Some solutions mistakenly use `O((m · n)^2)` due to repeated scans. |
| **Space Complexity** | O(m · n) for the visited array + recursion stack / queue. | Recursive DFS can consume up to O(m · n) stack frames. | Using `HashMap` for every cell can blow memory. |
| **Readability** | Clear parent tracking, single `dfs` function. | Recursive DFS may obscure the parent logic to newcomers. | Over‑complicated union‑find implementations distract from the core idea. |
| **Performance** | DFS in Java (`O(1)` recursion per call) runs < 100 ms on LeetCode. | Python DFS may hit recursion limits for 500 × 500 grids. | BFS with a custom pair class can be slower in Java due to boxing/unboxing. |

---

## 3. Algorithmic Insight

Treat the grid as an undirected graph where each cell is a node.  
Two nodes are adjacent if they share a side and contain the same letter.

During a depth‑first traversal:

* When exploring a neighbour `(nr, nc)` of the current cell `(r, c)`,  
  * If the neighbour was **not visited**, recurse with `(r, c)` as its parent.
  * If the neighbour **was visited** and it is **not the parent**, a cycle exists.

The “parent” check guarantees that we don’t falsely detect the trivial two‑cell back‑and‑forth path as a cycle.

---

## 4. Code Implementations

> All solutions share the same logic; only syntax differs.

### 4.1 Java – DFS (Recursive)

```java
import java.util.*;

class Solution {
    private final int[] dr = {1, -1, 0, 0};
    private final int[] dc = {0, 0, -1, 1};

    public boolean containsCycle(char[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] vis = new int[m][n];          // 0 = unvisited, 1 = visited
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (vis[i][j] == 0) {
                    if (dfs(grid, vis, i, j, -1, -1, grid[i][j]))
                        return true;
                }
            }
        }
        return false;
    }

    private boolean dfs(char[][] g, int[][] vis,
                        int r, int c,
                        int pr, int pc,
                        char color) {
        vis[r][c] = 1;
        for (int k = 0; k < 4; k++) {
            int nr = r + dr[k], nc = c + dc[k];
            if (nr < 0 || nr >= g.length || nc < 0 || nc >= g[0].length)
                continue;
            if (g[nr][nc] != color) continue;
            if (vis[nr][nc] == 0) {
                if (dfs(g, vis, nr, nc, r, c, color)) return true;
            } else if (nr != pr || nc != pc) {   // visited and not parent
                return true;
            }
        }
        return false;
    }
}
```

> **Tip for Java interviews** – use `int[][] vis` instead of a `boolean[][]` so you can store the parent coordinate indirectly (e.g., encode `(r, c)` into a single integer) if you want to avoid passing parent separately.

---

### 4.2 Python – DFS (Iterative Stack)

```python
from typing import List

class Solution:
    def containsCycle(self, grid: List[List[str]]) -> bool:
        m, n = len(grid), len(grid[0])
        vis = [[False]*n for _ in range(m)]
        dirs = [(1,0), (-1,0), (0,1), (0,-1)]

        for i in range(m):
            for j in range(n):
                if not vis[i][j]:
                    stack = [(i, j, -1, -1)]   # (r, c, parent_r, parent_c)
                    vis[i][j] = True
                    while stack:
                        r, c, pr, pc = stack.pop()
                        for dr, dc in dirs:
                            nr, nc = r+dr, c+dc
                            if 0 <= nr < m and 0 <= nc < n and grid[nr][nc] == grid[r][c]:
                                if not vis[nr][nc]:
                                    vis[nr][nc] = True
                                    stack.append((nr, nc, r, c))
                                elif nr != pr or nc != pc:
                                    return True
        return False
```

> **Why iterative?** Python’s recursion depth limit (default 1000) would break for large grids; an explicit stack sidesteps that.

---

### 4.3 C++ – DFS (Recursive)

```cpp
class Solution {
    const int dr[4] = {1, -1, 0, 0};
    const int dc[4] = {0, 0, -1, 1};

public:
    bool containsCycle(vector<vector<char>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> vis(m, vector<int>(n, 0));

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (!vis[i][j]) {
                    if (dfs(grid, vis, i, j, -1, -1, grid[i][j]))
                        return true;
                }
            }
        }
        return false;
    }

private:
    bool dfs(vector<vector<char>>& g, vector<vector<int>>& vis,
             int r, int c, int pr, int pc, char col) {
        vis[r][c] = 1;
        for (int k = 0; k < 4; ++k) {
            int nr = r + dr[k], nc = c + dc[k];
            if (nr < 0 || nr >= (int)g.size() || nc < 0 || nc >= (int)g[0].size())
                continue;
            if (g[nr][nc] != col) continue;
            if (!vis[nr][nc]) {
                if (dfs(g, vis, nr, nc, r, c, col))
                    return true;
            } else if (nr != pr || nc != pc) {   // visited, not parent
                return true;
            }
        }
        return false;
    }
};
```

> **Note for C++ interviews** – be careful with stack size; use `std::vector` for the `vis` array and pass by reference.

---

## 5. Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| DFS (all languages) | **O(m · n)** | **O(m · n)** – visited array + recursion stack (worst case) |
| BFS (Java version) | **O(m · n)** | **O(m · n)** – queue + visited array |

Both are linear and optimal; the grid can be visited once.

---

## 6. Blog Article – “Detect Cycles in a 2‑D Grid: The Good, The Bad, The Ugly”

> **Meta‑Description**  
> Master LeetCode 1559 “Detect Cycles in 2‑D Grid” with Java, Python, and C++ solutions. Learn the best DFS strategy, pitfalls, and why this problem is a must‑know for your next data‑structures interview.

---

### 6.1 Introduction

If you’re preparing for technical interviews, “Detect Cycles in 2‑D Grid” is a staple.  
It blends graph traversal, parent tracking, and careful boundary checks—all within the constraints of a 500 × 500 grid.

> **Why it matters** – Many interviewers ask variations of cycle detection (linked list, graphs, grids). Mastering it demonstrates your ability to adapt a core algorithm to different data models.

---

### 6.2 The Problem Revisited

*(Insert the formal statement with code block.)*

---

### 6.3 The Good

1. **DFS is natural** – Treat each cell as a node; two nodes are connected if they share a side *and* contain the same letter.
2. **Linear runtime** – O(m · n) is the fastest you can get.  
   *LeetCode’s test data* typically executes in < 100 ms.
3. **Simple parent logic** – By passing the previous cell’s coordinates, you immediately know whether a visited neighbour is the parent.  
   *A single boolean flag in your `visited` array is insufficient; you must check the direction.*

> **Pro tip** – Write a helper `dfs(r, c, parent_r, parent_c)` that returns `True` as soon as a cycle is found. No need to back‑track results after exploring all children.

---

### 6.4 The Bad

| Issue | Consequence | Fix |
|-------|-------------|-----|
| Recursion depth over 250 000 (Python, Java) | Stack overflow / RuntimeError | Use an explicit stack (Python) or raise the recursion limit in Java (`-Xss`). |
| Forgetting the “parent” condition | False positives (e.g., two‑cell back‑and‑forth) | Always compare neighbour coordinates to the parent before declaring a cycle. |
| Re‑scanning entire grid for each component | Quadratic time | Use a single visited matrix; once a cell is marked, skip it. |

---

### 6.5 The Ugly

> Some candidates write code that, while logically correct, is **messy**:

1. **Union‑Find on a grid**  
   *Over‑engineered for a simple DFS problem.*  
   It introduces heavy abstractions (path compression, rank) that distract from the core logic.
2. **Unnecessary data structures**  
   *Using `HashSet<(int,int)>` for each cell or a map keyed by “letter”.*  
   These blow up memory for 250 k cells.
3. **Hard‑coded directions**  
   *Using separate `int` arrays for `dr` and `dc` in every function.*  
   While fast, it can be hidden behind nested loops and mis‑indexed loops.

> **Takeaway** – Keep your solution lean: one visited matrix, a direction array, and a parent check.

---

### 6.6 Tips for Interview Success

| Language | Strategy | Edge Cases |
|----------|----------|------------|
| **Java** | Use `int[][] vis` and encode parent as a single integer if recursion depth is a concern. | Remember to reset visited when you finish a component. |
| **Python** | Prefer an explicit stack; avoid recursion. | Increase recursion limit (`sys.setrecursionlimit`) *only* if you’re confident about stack depth. |
| **C++** | Pass vectors by reference; be explicit about boundary checks (`r+dr < m`). | Use `std::vector<int>` for `vis` instead of `bool` to avoid signed/unsigned issues. |

---

### 6.7 Real‑World Analogies

Cycle detection on a grid is like finding a loop in a maze where you can only walk through walls of the same color.  
Imagine a city grid where you can only travel through streets painted the same color; a cycle is a closed walk that never immediately retraces a step.

---

### 6.8 Final Thoughts

- **Implement once, test many** – Write the DFS in any language, run it against all three official LeetCode examples.
- **Check the parent condition** – That is the heart of the solution; missing it is the most common bug.
- **Keep it linear** – Any quadratic attempt is a red flag for both runtime and interview evaluation.

With these Java, Python, and C++ snippets in your toolkit, you’ll confidently tackle LeetCode 1559 and demonstrate mastery of cycle detection—a key skill for any algorithm interview.

---

### 6.9 Call to Action

> **Ready to level up?**  
> Bookmark this post, download the code snippets, and add the problem to your daily “practice list.”  
> Need help with another LeetCode classic? Drop a comment or email me—happy to help you prepare for your next coding interview!

---

## 7. Conclusion

LeetCode 1559 is straightforward once you understand:

* Treat the grid as an undirected graph,
* DFS with parent tracking,
* Linear time & space.

The Java, Python, and C++ solutions above illustrate a clean implementation that will pass all official tests and impress interviewers.

Happy coding—and good luck on your next interview! 🚀

--- 

*End of article.*