---
title: LeetCode 2290. Minimum Obstacle Removal to Reach Corner - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2290 – Minimum Obstacle Removal to Reach Corner  
**Hard** | 0‑1 BFS | Java | Python | C++  

---

### Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Intuition & Key Insight](#intuition)  
3. [0‑1 BFS – The Perfect Fit](#0-1-bfs)  
4. [Step‑by‑Step Algorithm](#algorithm)  
5. [Complexity Analysis](#complexity)  
6. [Common Pitfalls (The Ugly)](#pitfalls)  
7. [Alternative Approaches (The Good & the Bad)](#alternatives)  
8. [Full Code – Java, Python, C++](#code)  
9. [Testing & Edge Cases](#testing)  
10. [Why This Blog Helps Your Job Hunt](#job-hunt)  
11. [Conclusion & Take‑aways](#conclusion)  

---

<a name="problem-recap"></a>
## 1. Problem Recap  

Given an `m x n` grid where `grid[i][j]` is either `0` (free cell) or `1` (obstacle that can be removed), find the **minimum number of obstacles to remove** so you can travel from the top‑left `(0,0)` to the bottom‑right `(m-1,n-1)`.  
Movement is allowed only in four orthogonal directions.  

**Constraints**  
- `1 ≤ m, n ≤ 10⁵`  
- `2 ≤ m*n ≤ 10⁵`  
- `grid[0][0] == grid[m-1][n-1] == 0`  

The constraints guarantee that the grid is sparse enough for an `O(m*n)` algorithm to run in time.

---

<a name="intuition"></a>
## 2. Intuition & Key Insight  

At first glance this feels like a shortest‑path problem on a weighted grid: moving to a free cell costs **0**, moving onto an obstacle costs **1** (we remove it).  

Because all edge weights are **0 or 1**, we don't need a full Dijkstra priority‑queue. A **0‑1 BFS** (deque‑based BFS) will give the same result **with linear time and memory**.  

> **0‑1 BFS**:  
> *When exploring a node, if the edge cost is 0 push the neighbor to the **front** of the deque, otherwise push it to the **back**.*  

This guarantees that nodes are processed in non‑decreasing order of distance, just like Dijkstra but without the overhead.

---

<a name="0-1-bfs"></a>
## 3. 0‑1 BFS – The Perfect Fit  

| Edge Cost | Deque Operation | Why It Works |
|-----------|-----------------|--------------|
| `0` (free cell) | `push_front` | We can reach this neighbor *without* increasing the obstacle count; it should be processed earlier. |
| `1` (obstacle) | `push_back` | Removing an obstacle is more expensive; we process it later. |

Because every time we pop from the front we are guaranteed to have the current minimal obstacle count for that cell, the first time we reach the target `(m-1,n-1)` we already have the optimal answer.

---

<a name="algorithm"></a>
## 4. Step‑by‑Step Algorithm  

1. **Initialize**  
   - `m = grid.size()`, `n = grid[0].size()`.  
   - `dist[m][n]` array with `INF` (`Integer.MAX_VALUE` / `float('inf')` / `INT_MAX`).  
   - Deque `dq` containing the start cell `(0,0)` and `dist[0][0] = 0`.  
2. **Directions**: `[(0,1),(1,0),(0,-1),(-1,0)]`.  
3. **BFS Loop**  
   - Pop `(x,y)` from front.  
   - For each neighbor `(nx,ny)` inside bounds:  
     - `newDist = dist[x][y] + grid[nx][ny]`.  
     - If `newDist < dist[nx][ny]`: update `dist[nx][ny] = newDist`.  
       - If `grid[nx][ny] == 0` → `push_front(nx,ny)`;  
       - Else → `push_back(nx,ny)`.  
4. **Return** `dist[m-1][n-1]`.  

---

<a name="complexity"></a>
## 5. Complexity Analysis  

| Measure | Complexity |
|---------|------------|
| Time | `O(m*n)` – each cell is processed at most twice (once per direction). |
| Space | `O(m*n)` – distance matrix + deque. |

Both fit comfortably within the given constraints (`m*n ≤ 100,000`).

---

<a name="pitfalls"></a>
## 6. Common Pitfalls (The Ugly)  

| Pitfall | Fix |
|----------|-----|
| **Using Dijkstra with a priority queue** – Works but slower (O((m*n) log(m*n))) and heavier memory. |
| **Not distinguishing 0‑cost vs 1‑cost edges** – Leads to incorrect distances. |
| **Incorrect bounds check** – Off‑by‑one errors causing index errors. |
| **Using `int` for INF incorrectly** – `INT_MAX` + 1 overflows; always compare with `dist[x][y] + grid[nx][ny] < dist[nx][ny]`. |
| **Mutable default arguments in Python** – Re‑initialising `deque` inside the function. |

---

<a name="alternatives"></a>
## 7. Alternative Approaches (The Good & the Bad)  

| Approach | Pros | Cons |
|----------|------|------|
| **0‑1 BFS (Deque)** | Linear time, minimal code, easy to understand. | None for this problem. |
| **Dijkstra with Min‑Heap** | Works for arbitrary weights; familiar to many candidates. | Extra log factor; more code. |
| **A* Search** | Can prune search using heuristic (Manhattan distance). | Requires careful implementation; worst‑case still O(m*n). |
| **Dynamic Programming (DP)** | Straightforward for grids without obstacles. | Fails when obstacles must be removed; cannot handle the 0‑1 cost semantics. |

---

<a name="code"></a>
## 8. Full Code – Java, Python, C++  

### Java  
```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int minimumObstacles(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dist = new int[m][n];
        final int INF = Integer.MAX_VALUE;
        for (int i = 0; i < m; i++) {
            java.util.Arrays.fill(dist[i], INF);
        }

        int[] dx = {1, -1, 0, 0};
        int[] dy = {0, 0, 1, -1};

        Deque<int[]> dq = new ArrayDeque<>();
        dist[0][0] = 0;
        dq.offerFirst(new int[]{0, 0});

        while (!dq.isEmpty()) {
            int[] cur = dq.pollFirst();
            int x = cur[0], y = cur[1];
            for (int dir = 0; dir < 4; dir++) {
                int nx = x + dx[dir], ny = y + dy[dir];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                int cost = grid[nx][ny];
                int newDist = dist[x][y] + cost;
                if (newDist < dist[nx][ny]) {
                    dist[nx][ny] = newDist;
                    if (cost == 0) {
                        dq.offerFirst(new int[]{nx, ny});
                    } else {
                        dq.offerLast(new int[]{nx, ny});
                    }
                }
            }
        }
        return dist[m - 1][n - 1];
    }
}
```

### Python  
```python
from collections import deque
from typing import List

class Solution:
    def minimumObstacles(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        INF = float('inf')
        dist = [[INF] * n for _ in range(m)]

        dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]
        dq = deque()
        dist[0][0] = 0
        dq.appendleft((0, 0))

        while dq:
            x, y = dq.popleft()
            for dx, dy in dirs:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n:
                    new_dist = dist[x][y] + grid[nx][ny]
                    if new_dist < dist[nx][ny]:
                        dist[nx][ny] = new_dist
                        if grid[nx][ny] == 0:
                            dq.appendleft((nx, ny))
                        else:
                            dq.append((nx, ny))
        return dist[m - 1][n - 1]
```

### C++  
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumObstacles(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        const int INF = INT_MAX;
        vector<vector<int>> dist(m, vector<int>(n, INF));

        int dx[4] = {1, -1, 0, 0};
        int dy[4] = {0, 0, 1, -1};

        deque<pair<int,int>> dq;
        dist[0][0] = 0;
        dq.emplace_front(0, 0);

        while (!dq.empty()) {
            auto [x, y] = dq.front(); dq.pop_front();
            for (int dir = 0; dir < 4; ++dir) {
                int nx = x + dx[dir];
                int ny = y + dy[dir];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                int newDist = dist[x][y] + grid[nx][ny];
                if (newDist < dist[nx][ny]) {
                    dist[nx][ny] = newDist;
                    if (grid[nx][ny] == 0)
                        dq.emplace_front(nx, ny);
                    else
                        dq.emplace_back(nx, ny);
                }
            }
        }
        return dist[m-1][n-1];
    }
};
```

> All three snippets use the **0‑1 BFS** strategy and conform to LeetCode’s `class Solution` contract.

---

<a name="testing"></a>
## 9. Testing & Edge Cases  

| Test | Input | Expected Output |
|------|-------|-----------------|
| 1 | `[[0,0,0],[0,1,0],[1,1,0]]` | `0` |
| 2 | `[[0,1,1],[1,1,1],[1,1,0]]` | `3` |
| 3 | `[[0,0,0],[0,1,0],[0,0,0]]` | `0` |
| 4 | `[[0,1,1,0],[1,1,1,1],[0,1,1,1],[0,0,0,0]]` | `2` |
| 5 | Single‑row grid | Works as long as start & end are `0`. |
| 6 | Single‑column grid | Works symmetrically. |

*Always verify that `grid[0][0]` and `grid[m-1][n-1]` are `0`; the problem guarantees this.*

---

<a name="testing"></a>
## 10. Testing & Edge Cases  

1. **Large Sparse Grid** – e.g., `m = 1, n = 100000`.  
   - Deque never exceeds 2 elements.  
2. **Fully Free Grid** – `grid` all zeros.  
   - The algorithm still runs `O(m*n)` and returns `0`.  
3. **Fully Obstacle‑Heavy but Removable** – e.g., all `1` except the start & end.  
   - Each obstacle costs `1`, answer equals the Manhattan distance (`m-1 + n-1`).  
4. **Unreachable Path** – Not possible here because start & end are always free and obstacles can always be removed; but if the constraints were relaxed, we would return `-1`.  

---

<a name="job-hunt"></a>
## 11. Why This Blog Helps Your Job Hunt  

| How | Why It Matters |
|-----|----------------|
| **Keyword‑Rich Title** – “LeetCode 2290: Minimum Obstacle Removal to Reach Corner – 0‑1 BFS” attracts recruiters searching for interview problems. |
| **Clear Code Samples** – Candidates can copy‑paste and adapt. |
| **Algorithmic Insight** – Shows you *why* 0‑1 BFS is optimal, a concept recruiters love. |
| **Complexity Breakdown** – Demonstrates that you understand big‑O analysis. |
| **Pitfalls & Alternatives** – Shows you’re aware of edge cases and different solutions. |
| **Job‑Hunt Focus** – Frequent tags like “coding interview”, “algorithm”, “LeetCode interview prep” help the article surface in search results for recruiters. |

A polished post like this on **Medium, Dev.to, or your personal blog** can be shared on LinkedIn and GitHub, giving you a tangible artifact to discuss during interviews.

---

<a name="conclusion"></a>
## 12. Conclusion & Take‑aways  

- **0‑1 BFS** is the gold‑standard for weighted grids with edges `0` or `1`.  
- For LeetCode 2290, it runs in linear time, uses a single `deque`, and is simple to implement in any language.  
- Avoid the overhead of a priority‑queue unless you’re handling general weights.  
- Always double‑check boundary conditions and treat `INF` carefully to avoid overflows.  

Now you’re ready to crack **LeetCode 2290** (and impress recruiters) with confidence!  

---

### 🔑 Key Take‑aways  
- Understand the **edge‑weight semantics** (0 vs 1).  
- Apply **0‑1 BFS** when edge weights are 0/1.  
- Write clean, boundary‑safe code.  
- Document complexity; recruiters love a concise explanation.  

Happy coding! 👩‍💻👨‍💻