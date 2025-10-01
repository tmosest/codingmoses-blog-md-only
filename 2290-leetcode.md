---
title: LeetCode 2290. Minimum Obstacle Removal to Reach Corner - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2290. Minimum Obstacle Removal to Reach Corner  
**Hard – LeetCode**  
> “You are given a 0‑indexed 2‑D integer array `grid` …  
> Return the minimum number of obstacles to remove so you can move from the upper left corner (0, 0) to the lower right corner (m‑1, n‑1).”

Below you’ll find **fully‑working, well‑commented implementations** in **Java, Python, and C++**, followed by a **SEO‑friendly blog post** that explains the solution, its trade‑offs (“The Good, The Bad, and The Ugly”), and how you can use it to land your next software‑engineering job.

---

## 1. Code – 0‑1 BFS (Deque) Implementation  

All three solutions share the same algorithmic idea:

1. **Treat the grid as a weighted graph** where moving onto a `0` costs `0` and onto a `1` costs `1`.
2. **Run a 0‑1 BFS** (a special case of Dijkstra where edge weights are only 0 or 1).  
   * A **deque** (`std::deque` / `collections.deque` / `ArrayDeque`) is used to store frontier nodes.  
   * If the next cell is empty (`0`) we `push_front` – giving it higher priority.  
   * If the next cell is an obstacle (`1`) we `push_back` – giving it lower priority.

Time Complexity: **O(m × n)**  
Space Complexity: **O(m × n)** (distance array + deque)

---

### 1.1 Java

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int minimumObstacles(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dist = new int[m][n];
        final int INF = Integer.MAX_VALUE / 2;          // avoid overflow
        for (int[] row : dist) java.util.Arrays.fill(row, INF);
        dist[0][0] = 0;

        Deque<int[]> dq = new ArrayDeque<>();
        dq.addFirst(new int[]{0, 0});

        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

        while (!dq.isEmpty()) {
            int[] cur = dq.pollFirst();
            int x = cur[0], y = cur[1];
            for (int[] d : dirs) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                int w = grid[nx][ny];
                if (dist[x][y] + w < dist[nx][ny]) {
                    dist[nx][ny] = dist[x][y] + w;
                    if (w == 0) dq.addFirst(new int[]{nx, ny});
                    else dq.addLast(new int[]{nx, ny});
                }
            }
        }
        return dist[m - 1][n - 1];
    }
}
```

---

### 1.2 Python

```python
from collections import deque
from typing import List

class Solution:
    def minimumObstacles(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        INF = 10**9
        dist = [[INF] * n for _ in range(m)]
        dist[0][0] = 0

        dq = deque([(0, 0)])
        dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]

        while dq:
            x, y = dq.popleft()
            for dx, dy in dirs:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n:
                    w = grid[nx][ny]
                    if dist[x][y] + w < dist[nx][ny]:
                        dist[nx][ny] = dist[x][y] + w
                        if w == 0:
                            dq.appendleft((nx, ny))
                        else:
                            dq.append((nx, ny))
        return dist[m - 1][n - 1]
```

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumObstacles(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        const int INF = 1e9;
        vector<vector<int>> dist(m, vector<int>(n, INF));
        dist[0][0] = 0;

        deque<pair<int,int>> dq;
        dq.emplace_front(0,0);

        const int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
        while (!dq.empty()) {
            auto [x, y] = dq.front();
            dq.pop_front();
            for (auto &d : dirs) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                int w = grid[nx][ny];
                if (dist[x][y] + w < dist[nx][ny]) {
                    dist[nx][ny] = dist[x][y] + w;
                    if (w == 0) dq.emplace_front(nx, ny);
                    else dq.emplace_back(nx, ny);
                }
            }
        }
        return dist[m-1][n-1];
    }
};
```

---

## 2. Blog Post – “LeetCode 2290: Minimum Obstacle Removal to Reach Corner – The Good, The Bad, and The Ugly”

> **SEO Keywords**: LeetCode 2290, Minimum Obstacle Removal, 0‑1 BFS, Graph Algorithms, Job Interview, Software Engineer, Data Structures, Dijkstra, BFS, Path‑finding, Interview Problem.

### Title
**LeetCode 2290 – Minimum Obstacle Removal to Reach Corner: A 0‑1 BFS Masterclass (The Good, The Bad & The Ugly)**

### Meta Description
Learn how to solve LeetCode 2290 in Java, Python, and C++ with a 0‑1 BFS algorithm. Discover the trade‑offs, pitfalls, and interview‑ready insights to impress hiring managers.

---

#### Introduction

When preparing for a **software‑engineering interview**, you’ll encounter graph‑based path‑finding problems almost daily. **LeetCode 2290 – “Minimum Obstacle Removal to Reach Corner”** is a classic Hard‑level challenge that tests your grasp of BFS, Dijkstra, and dynamic programming. In this post we:

1. **Explain the problem** in plain English.  
2. **Show the optimal solution** using 0‑1 BFS (deque).  
3. **Compare the good, the bad, and the ugly** – what you should love, watch out for, and avoid.  
4. **Provide ready‑to‑copy code** in **Java, Python, and C++**.  
5. **Give interview‑specific talking points** that will make you stand out.

---

#### 2.1 Problem Statement

> You are given a grid where `0` = empty cell, `1` = obstacle that can be removed.  
> Starting from the top‑left corner `(0,0)` to the bottom‑right corner `(m-1,n-1)`, **minimize the number of obstacles removed** to create a valid path.  
> You can move up, down, left, or right.  

**Constraints**  
- `1 ≤ m, n ≤ 10^5`  
- `2 ≤ m*n ≤ 10^5`  
- `grid[0][0] == grid[m-1][n-1] == 0`

The challenge is to find the shortest “cost‑path” where cost is the number of removed obstacles.

---

#### 2.2 The Good: Why 0‑1 BFS Is a Winner

| What You’ll Love | Why It Matters |
|------------------|----------------|
| **Linear Time** – `O(m*n)` runs fast even at the upper bound. |
| **Simple Code** – A single deque and a 4‑direction loop. |
| **No Priority Queue Needed** – `deque` is lighter than `BinaryHeap` and avoids costly `push_heap` operations. |
| **Memory Friendly** – Only the distance matrix and the deque are stored. |
| **Extensible** – The same pattern applies to other “0/1 weight” graph problems. |

**Takeaway for Interviewers**  
> “I can solve large‑scale weighted BFS in linear time, which is a core skill for building real‑world routing systems.”

---

#### 2.3 The Bad: Edge Cases & Hidden Pitfalls

| Bad Practice | Consequence | Mitigation |
|--------------|-------------|------------|
| Using `int INF = Integer.MAX_VALUE;` and adding `w` directly | **Overflow** → `dist + 1` might wrap around. | Use `INF / 2` or `1e9`. |
| Forgetting to check bounds before pushing onto the deque | **Segmentation fault / IndexOutOfBoundsException**. | Always guard with `0 <= nx < m` & `0 <= ny < n`. |
| Treating all edges as weight‑1 → standard BFS | **Sub‑optimal** runtime; still works but slower in practice. | Use 0‑1 BFS or Dijkstra for guaranteed optimality. |
| Not resetting `dist` array to `INF` before visiting | **Incorrect distances** due to stale values. | Explicitly initialize every cell. |

---

#### 2.4 The Ugly: Common Mistakes That Break Your Solution

| Ugly Mistake | What Happens | Fix |
|--------------|--------------|-----|
| **Wrong deque operations** (push_back for zero‑cost cells) | Dequeue may grow unnecessarily, leading to O(m*n) queue operations but still correct—slow. | Push zero‑cost cells to the *front* (`push_front`). |
| **Using a priority queue but never updating visited flag** | The algorithm may push the same node many times, still O(m*n) but with a large constant factor. | Maintain a `visited` or `dist` array; update only if a better cost is found. |
| **Not considering the cost of the starting or ending cell** | While constraints guarantee `0`, failing to assert it can cause logic errors in generalized solutions. | Add a quick guard: `if grid[0][0] == 1 or grid[m-1][n-1] == 1: return -1`. |
| **Assuming grid is always square** | Accessing `grid[0][n]` when `n=0` triggers runtime error. | Use `int m = grid.size(); int n = grid[0].size();` safely. |
| **Using recursion for BFS** | Stack overflow on large grids. | Always use iterative deque or queue. |

---

#### 2.5 Interview Talking Points

1. **Explain the mapping**: “Treat each cell as a node, and moving onto an obstacle as an edge with weight 1.”
2. **Why 0‑1 BFS beats Dijkstra**: fewer heap operations → faster constant factor.
3. **Space optimization**: Could we replace the `dist` matrix with a `HashSet` for visited cells? Explain trade‑offs.
4. **Complexity discussion**: “O(m*n) time – linear – that's optimal because every cell has to be examined at most once.”
5. **Testing strategy**: Write unit tests for edge cases:
   - All zeros (no obstacles).  
   - Grid with a single obstacle in the middle.  
   - Impossible path (but start & end are zero, so always possible after removals).  

6. **Follow‑up questions**:  
   *“What if we had a cost of 2 for some cells?”* – Discuss generalizing to Dijkstra.  
   *“What if diagonal moves were allowed?”* – How the direction array changes.

---

#### 2.6 Ready‑to‑Copy Code

> The following code blocks are *production‑ready*; you can paste them into your LeetCode solution editor and submit instantly.

```java
// Java – 0‑1 BFS
class Solution {
    public int minimumObstacles(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dist = new int[m][n];
        final int INF = Integer.MAX_VALUE / 2;
        for (int[] row : dist) java.util.Arrays.fill(row, INF);
        dist[0][0] = 0;

        Deque<int[]> dq = new ArrayDeque<>();
        dq.addFirst(new int[]{0, 0});

        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
        while (!dq.isEmpty()) {
            int[] cur = dq.pollFirst();
            int x = cur[0], y = cur[1];
            for (int[] d : dirs) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                int w = grid[nx][ny];
                if (dist[x][y] + w < dist[nx][ny]) {
                    dist[nx][ny] = dist[x][y] + w;
                    if (w == 0) dq.addFirst(new int[]{nx, ny});
                    else dq.addLast(new int[]{nx, ny});
                }
            }
        }
        return dist[m - 1][n - 1];
    }
}
```

```python
# Python – 0‑1 BFS
from collections import deque
from typing import List

class Solution:
    def minimumObstacles(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        INF = 10**9
        dist = [[INF] * n for _ in range(m)]
        dist[0][0] = 0

        dq = deque([(0, 0)])
        dirs = [(1,0), (-1,0), (0,1), (0,-1)]

        while dq:
            x, y = dq.popleft()
            for dx, dy in dirs:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n:
                    w = grid[nx][ny]
                    if dist[x][y] + w < dist[nx][ny]:
                        dist[nx][ny] = dist[x][y] + w
                        if w == 0:
                            dq.appendleft((nx, ny))
                        else:
                            dq.append((nx, ny))
        return dist[m-1][n-1]
```

```cpp
// C++ – 0‑1 BFS
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumObstacles(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        const int INF = 1e9;
        vector<vector<int>> dist(m, vector<int>(n, INF));
        dist[0][0] = 0;

        deque<pair<int,int>> dq;
        dq.emplace_front(0,0);

        const int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
        while (!dq.empty()) {
            auto [x, y] = dq.front();
            dq.pop_front();
            for (auto &d : dirs) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                int w = grid[nx][ny];
                if (dist[x][y] + w < dist[nx][ny]) {
                    dist[nx][ny] = dist[x][y] + w;
                    if (w == 0) dq.emplace_front(nx, ny);
                    else dq.emplace_back(nx, ny);
                }
            }
        }
        return dist[m-1][n-1];
    }
};
```

---

#### 2.7 Final Takeaway

- **LeetCode 2290** is a *must‑know* Hard‑level interview problem because it combines graph traversal with a subtle cost model.  
- A **0‑1 BFS** is the most elegant, fastest, and memory‑efficient solution.  
- **Interviewers love** clear explanations of why the algorithm is optimal.  
- **Watch out** for overflow, wrong deque operations, and forgotten bounds.  
- **Avoid** naive BFS + DP or brute‑force DFS – they’ll TLE or MLE on the upper constraints.  

Use the code snippets above to practice on LeetCode, GitHub, or your own test harness. When you nail this problem in an interview, you’ll demonstrate mastery of graph algorithms, data‑structure trade‑offs, and a deep understanding of algorithmic optimisation – exactly the skill set that hiring managers crave.

Good luck – the next software‑engineering role is just one *obstacle* away! 🚀

---  

*Prepared by: [Your Name] – Senior Software Engineer & Interview Coach*  

---  

### Suggested URL Slugs
```
leetcode-2290-solution-0-1-bfs
```

### Suggested Keywords for SEO
```
leetcode 2290, 0-1 BFS, weighted graph, interview problem, linear time algorithm, priority queue, Java deque, Python collections, C++ deque
```

---  

**END OF BLOG POST**