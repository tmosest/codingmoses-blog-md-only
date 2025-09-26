---
title: LeetCode 1102. Path With Maximum Minimum Value - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1102. Path With Maximum Minimum Value – A Complete Guide  
*(Java, Python, C++ – Dijkstra’s Max‑Heap + 4‑direction BFS)*  

---

## Table of Contents  
1. 📌 Problem Summary  
2. 🚀 Why Dijkstra + Max‑Heap Works  
3. 🛠️ 3‑Language Implementation (Java / Python / C++)  
4. ⚖️ Time & Space Complexity  
5. 🤔 Common Pitfalls (The “Bad” & “Ugly” parts)  
6. 🎯 Variants & Extensions  
7. 👋 Take‑away & Career‑boosting Tips  
8. 📚 Further Reading & Resources  

> **SEO Keywords**: Path With Maximum Minimum Value, LeetCode 1102, Dijkstra algorithm, grid path problem, Java BFS, Python priority queue, C++ priority queue, interview coding problem, interview preparation, software engineering interview, coding interview tips, algorithm blog, data structure interview, algorithmic thinking.

---

## 1. 📌 Problem Summary

**LeetCode #1102 – Path With Maximum Minimum Value**

> *Given an `m × n` integer grid `grid`, find a path from the top‑left cell `(0,0)` to the bottom‑right cell `(m‑1,n‑1)` that maximizes the **minimum** value encountered along the path.*  

The path may move only in the four cardinal directions (up, down, left, right).  
The answer is the *score* of the best path – the largest possible value of the **smallest** grid entry on that path.

### Examples

| Input | Output | Explanation |
|-------|--------|-------------|
| `[[5,4,5],[1,2,6],[7,4,6]]` | `4` | The highlighted path yields min = 4. |
| `[[2,2,1,2,2,2],[1,2,2,2,1,2]]` | `2` | |
| `[[3,4,6,3,4],[0,2,1,1,7],[8,8,3,2,7],[3,2,4,9,8],[4,1,2,0,0],[4,6,5,4,3]]` | `3` | |

> **Constraints**  
> 1 ≤ m, n ≤ 100, 0 ≤ grid[i][j] ≤ 10⁹

---

## 2. 🚀 Why Dijkstra + Max‑Heap Works

The problem is a *maximization of a minimum* along a path.  
Think of each cell as a node with weight `grid[i][j]`.  
When we move to a neighbor, the path’s score becomes `min(current_score, neighbor_value)`.  
We want the best score for every node – the **maximum** possible such score.  

This is exactly what Dijkstra’s algorithm does if we interpret the edge cost as “negative” or use a *max‑heap* instead of a min‑heap.  
At each step we take the node that currently has the highest reachable score, then relax its neighbours.

*Why max‑heap?*  
The priority queue stores tuples `(score, x, y)` sorted by **descending** score.  
When we pop a node, its score is guaranteed to be the largest possible among all unreached nodes – the same greedy property that underpins Dijkstra.

**Key invariant**

> The first time we pop the destination cell, the score we pop is the maximum minimum score of any path from `(0,0)` to that cell.

Proof is identical to the classic Dijkstra proof, just with `max` instead of `min` in the comparison.

---

## 3. 🛠️ 3‑Language Implementation

> All three solutions follow the same logic:  
> *Initialize a `scores` matrix (`-1` = unreached).  
> Push `(grid[0][0], 0, 0)` into a priority queue.  
> While the queue is non‑empty, pop the cell with the largest score, relax its four neighbours, and update their scores if we found a better one.*

> **Important:**  
> *Avoid visiting a node twice with a lower score.*  
> Only enqueue a neighbour if the new score is strictly greater than the current stored score for that cell.

### 3.1 Java (JDK 17+)

```java
import java.util.*;

public class Solution {
    private static final int[][] DIRS = {{0,1},{1,0},{0,-1},{-1,0}};

    public int maximumMinimumPath(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] best = new int[m][n];
        for (int[] row : best) Arrays.fill(row, -1);
        best[0][0] = grid[0][0];

        // max‑heap ordered by score
        PriorityQueue<int[]> pq = new PriorityQueue<>((a,b) -> Integer.compare(b[2], a[2]));
        pq.offer(new int[]{0, 0, grid[0][0]}); // {x, y, score}

        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            int x = cur[0], y = cur[1], score = cur[2];

            if (x == m-1 && y == n-1) return score;

            for (int[] d : DIRS) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || ny < 0 || nx >= m || ny >= n) continue;

                int newScore = Math.min(score, grid[nx][ny]);
                if (newScore > best[nx][ny]) {
                    best[nx][ny] = newScore;
                    pq.offer(new int[]{nx, ny, newScore});
                }
            }
        }
        return -1; // unreachable (never happens on valid input)
    }

    // --- Driver for quick local testing
    public static void main(String[] args) {
        int[][] g = {{5,4,5},{1,2,6},{7,4,6}};
        System.out.println(new Solution().maximumMinimumPath(g)); // 4
    }
}
```

> **Why this Java version is great**  
> *Explicit `int[][] best` matrix → O(mn) memory.*  
> *Max‑heap comparator with `Integer.compare(b[2], a[2])` – no wrapper objects.*  
> *Clear separation of relaxation logic.*

### 3.2 Python (3.9+)

```python
import heapq
from typing import List

class Solution:
    DIRS = [(0, 1), (1, 0), (0, -1), (-1, 0)]

    def maximumMinimumPath(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        best = [[-1] * n for _ in range(m)]
        best[0][0] = grid[0][0]

        # max‑heap: store (-score, x, y) because heapq is a min‑heap
        heap = [(-grid[0][0], 0, 0)]

        while heap:
            neg_score, x, y = heapq.heappop(heap)
            score = -neg_score

            if x == m - 1 and y == n - 1:
                return score

            for dx, dy in self.DIRS:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n:
                    new_score = min(score, grid[nx][ny])
                    if new_score > best[nx][ny]:
                        best[nx][ny] = new_score
                        heapq.heappush(heap, (-new_score, nx, ny))
        return -1  # unreachable – never occurs for valid inputs

# quick sanity test
if __name__ == "__main__":
    sol = Solution()
    print(sol.maximumMinimumPath([[5,4,5],[1,2,6],[7,4,6]]))   # 4
```

> **Why this Python version is great**  
> *No external dependencies – only the standard library.*  
> *`-score` trick turns the min‑heap into a max‑heap, keeping the code minimal.*  
> *Using `best` as a memoization table prevents revisiting cells with lower scores.*

### 3.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumMinimumPath(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> best(m, vector<int>(n, -1));
        best[0][0] = grid[0][0];

        using T = tuple<int,int,int>; // (score, x, y)
        priority_queue<T> pq;                 // max‑heap by default
        pq.emplace(grid[0][0], 0, 0);

        const int dirs[4][2] = {{0,1},{1,0},{0,-1},{-1,0}};
        while (!pq.empty()) {
            auto [score, x, y] = pq.top(); pq.pop();

            if (x == m-1 && y == n-1) return score;

            for (auto& d : dirs) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || ny < 0 || nx >= m || ny >= n) continue;

                int newScore = min(score, grid[nx][ny]);
                if (newScore > best[nx][ny]) {
                    best[nx][ny] = newScore;
                    pq.emplace(newScore, nx, ny);
                }
            }
        }
        return -1; // unreachable (won't happen)
    }
};
```

> **Why this C++ version is great**  
> *Zero‑overhead priority queue – the `tuple` is packed into the heap.*  
> *Explicit `dirs` array makes direction handling crystal clear.*  
> *Use of `min` and `max` keeps the algorithm concise.*

---

## 3. ⚖️ Time & Space Complexity

| Language | Complexity | Notes |
|----------|------------|-------|
| **Java / Python / C++** | **O(m · n log(m · n))** | Each cell can be pushed into the heap up to 4 times (worst‑case). |
| Memory | **O(m · n)** | `best` matrix + heap storage. |

The log factor comes from the heap operations.  
With `m, n ≤ 100`, this is easily fast enough (≈ 4 000 log 4 000 ≈ 48 000 operations).

---

## 4. 🤔 Common Pitfalls (The “Bad” & “Ugly” parts)

| Problem | Bad/Ugly Code | Fix / Good Practice |
|---------|---------------|---------------------|
| **Using a min‑heap** | `priority_queue<pair<int,int>> pq;`  // wrong order | Switch to max‑heap: `priority_queue<pair<int,int>> pq;` or push negative values. |
| **Ignoring better scores** | Popping a node once and never revisiting | Keep a `best` matrix; only relax neighbours when the new score is higher. |
| **Off‑by‑one errors in bounds** | Forgetting `x+dx < 0` check | Always validate `0 ≤ nx < m` and `0 ≤ ny < n` before accessing the grid. |
| **Mutable grid as “visited”** | `grid[nx][ny] = -1` | Use a separate `best` matrix or a `visited` boolean array; modifying input can be surprising. |
| **Using recursion / DFS** | Recursion depth > 10⁴ | Not stack‑safe for large grids; use iterative BFS. |

---

## 5. 🎯 Variants & Extensions

| Variant | How to Adapt |
|---------|--------------|
| **Binary Search + Union‑Find** | Search for the maximum threshold `t` such that cells ≥ `t` are connected. |
| **Binary Search + DFS** | For each threshold, run a DFS to check connectivity; log‑time *O(log(maxValue) · m·n)*. |
| **Minimum Effort Path (LeetCode 1631)** | Replace `min(current, neighbor)` with `max(current, abs(diff))`; similar max‑heap approach. |
| **Weighted edges (non‑cell weights)** | Dijkstra with edge costs instead of node costs. |

---

## 6. 👋 Take‑away & Career‑boosting Tips

1. **Show the invariant** – In interviews, articulate that the max‑heap guarantees you’re always picking the best possible score so far.  
2. **Explain the “max” vs. “min” switch** – Highlight how swapping the comparison turns a standard Dijkstra into the exact solution.  
3. **Avoid mutating the input** – Keep the grid untouched; use a `best` matrix for clarity.  
4. **Keep the code tidy** – Use helper constants (`DIRS`) and short‑lived local variables.  
5. **Benchmark mentally** – Even if the problem is small, discuss the asymptotic complexity; this shows algorithmic awareness.

Mastering this problem demonstrates proficiency in graph algorithms, data structures, and careful coding—a perfect showcase for technical interviews or technical blogs.

---

### Keywords for SEO & Knowledge Base

- `Maximum Minimum Path`  
- `LeetCode 1091`  
- `Dijkstra max‑heap`  
- `Java priority queue`  
- `Python heapq max‑heap`  
- `C++ priority_queue tuple`  
- `Graph connectivity binary search`  
- `Union-Find LeetCode`  
- `Minimum Effort Path`  

Feel free to adapt any of the snippets to your preferred IDE or online judge. Happy coding!