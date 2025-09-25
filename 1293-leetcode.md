---
title: LeetCode 1293. Shortest Path in a Grid with Obstacles Elimination - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧭 Shortest Path in a Grid with Obstacles Elimination (LeetCode 1293)

> **Problem type:** Hard – BFS + State Tracking  
> **Tags:** *Breadth‑First Search*, *Matrix*, *DP*, *Sliding Window*, *Path‑Finding*, *Dynamic Programming*  

---

## 1. Problem Recap

You are given an `m x n` grid (`grid[i][j] ∈ {0,1}`) where `0` is an empty cell and `1` is an obstacle.  
You can move in the four cardinal directions (up, down, left, right) in one step.  
You start at `(0,0)` and want to reach `(m-1, n-1)`.  

You are allowed to eliminate **at most `k` obstacles**.  
Return the *minimum number of steps* needed to reach the goal, or `-1` if it’s impossible.

**Constraints**

```
1 ≤ m, n ≤ 40
0 ≤ k ≤ m * n
grid[0][0] == grid[m-1][n-1] == 0
```

---

## 2. Why This Problem is a Good Interview Question

| Good | Bad | Ugly |
|------|-----|------|
| **Good** – Demonstrates your ability to reason about **stateful BFS** and **multi‑dimensional visited** structures. | **Bad** – Many candidates forget that the same cell can be reached with a different number of remaining eliminations, leading to premature pruning. | **Ugly** – A naïve DFS + pruning will TLE; a DP without BFS will produce an incorrect answer because the *shortest* path may use more eliminations earlier than a longer but “cheaper” path later. |

---

## 3. High‑Level Solution Idea

1. **Breadth‑First Search (BFS)** guarantees the shortest path in an un‑weighted graph.  
2. **State = (x, y, remaining‑k)**.  
   * We need a 3‑D `visited` array `vis[x][y][rem]` to remember if we’ve already visited cell `(x,y)` with `rem` eliminations left.  
3. Push the starting state `(0,0,k,0)` onto a queue (`steps` starts at `0`).  
4. While the queue is not empty, pop the front state and explore its 4 neighbours:
   * If the neighbour is an obstacle (`grid[nx][ny]==1`) **and** `rem>0`, we can step there after spending one elimination (`rem-1`).
   * If the neighbour is empty, we just step there (`rem` stays the same).  
5. If we reach `(m-1,n-1)` return the number of steps taken.  
6. If the queue empties, return `-1`.

The algorithm runs in **O(m × n × k)** time and the same order of space for the visited array – which is perfectly fine for the given limits (`m,n ≤ 40`).

---

## 4. Corner Cases & Gotchas

| Scenario | Why it matters | Fix |
|----------|----------------|-----|
| `k` is very large (`k >= m+n-2`) | You could walk the Manhattan path ignoring all obstacles. | Early exit: `if (k >= m+n-2) return m+n-2;` |
| The grid is 1×1 | Start == end | Return `0` immediately. |
| Large `k` but we use `int[][][] vis = new int[m][n][k+1]` | Memory blow‑up (max 40×40×1600≈2.5M ints → ~10 MB). | Acceptable, but be mindful if you push to >200 k. |
| Forget to check bounds | Index out of bounds during neighbour exploration. | `0 ≤ nx < m && 0 ≤ ny < n` |
| Re‑visiting a cell with *more* remaining eliminations is always better | We should **not** prune a state if we reach the same cell with *fewer* eliminations left. | Use a 3‑D boolean `visited`; no comparison of remaining k is needed. |

---

## 5. Reference Implementation – Java

```java
import java.util.*;

/**
 * LeetCode 1293. Shortest Path in a Grid with Obstacles Elimination
 */
public class Solution {
    private static final int[][] DIRS = {
            {1, 0}, {-1, 0}, {0, 1}, {0, -1}
    };

    public int shortestPath(int[][] grid, int k) {
        int m = grid.length, n = grid[0].length;
        // Early exit when k is enough to ignore all obstacles
        if (k >= m + n - 2) return m + n - 2;

        boolean[][][] visited = new boolean[m][n][k + 1];
        Queue<int[]> q = new ArrayDeque<>();
        // state: {x, y, remaining_k}
        q.offer(new int[]{0, 0, k});
        visited[0][0][k] = true;
        int steps = 0;

        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; ++i) {
                int[] cur = q.poll();
                int x = cur[0], y = cur[1], rem = cur[2];
                if (x == m - 1 && y == n - 1) return steps;

                for (int[] d : DIRS) {
                    int nx = x + d[0], ny = y + d[1];
                    if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;

                    int nrem = rem - grid[nx][ny];
                    if (nrem < 0) continue;   // no eliminations left for this obstacle

                    if (!visited[nx][ny][nrem]) {
                        visited[nx][ny][nrem] = true;
                        q.offer(new int[]{nx, ny, nrem});
                    }
                }
            }
            steps++;
        }
        return -1;   // unreachable
    }
}
```

---

## 6. Reference Implementation – Python

```python
from collections import deque
from typing import List

class Solution:
    def shortestPath(self, grid: List[List[int]], k: int) -> int:
        m, n = len(grid), len(grid[0])
        if k >= m + n - 2:
            return m + n - 2

        visited = [[[False] * (k + 1) for _ in range(n)] for _ in range(m)]
        q = deque([(0, 0, k)])          # (x, y, remaining_k)
        visited[0][0][k] = True
        steps = 0
        dirs = [(1,0),(-1,0),(0,1),(0,-1)]

        while q:
            for _ in range(len(q)):
                x, y, rem = q.popleft()
                if x == m - 1 and y == n - 1:
                    return steps
                for dx, dy in dirs:
                    nx, ny = x + dx, y + dy
                    if 0 <= nx < m and 0 <= ny < n:
                        nrem = rem - grid[nx][ny]
                        if nrem < 0:
                            continue
                        if not visited[nx][ny][nrem]:
                            visited[nx][ny][nrem] = True
                            q.append((nx, ny, nrem))
            steps += 1
        return -1
```

---

## 7. Reference Implementation – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int shortestPath(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        if (k >= m + n - 2) return m + n - 2;

        vector<vector<vector<bool>>> vis(m,
            vector<vector<bool>>(n, vector<bool>(k + 1, false)));
        queue<tuple<int,int,int>> q;                // x, y, remaining_k
        q.emplace(0, 0, k);
        vis[0][0][k] = true;

        int steps = 0;
        const int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
        while (!q.empty()) {
            for (int sz = q.size(); sz > 0; --sz) {
                auto [x, y, rem] = q.front(); q.pop();
                if (x == m-1 && y == n-1) return steps;
                for (auto &d : dirs) {
                    int nx = x + d[0], ny = y + d[1];
                    if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                    int nrem = rem - grid[nx][ny];
                    if (nrem < 0) continue;
                    if (!vis[nx][ny][nrem]) {
                        vis[nx][ny][nrem] = true;
                        q.emplace(nx, ny, nrem);
                    }
                }
            }
            ++steps;
        }
        return -1;   // unreachable
    }
};
```

---

## 8. Optional Optimisation – Dijkstra‑like Greedy

If you’re looking for a *micro‑performance tweak*, notice that we *only* need to know the **minimum remaining‑k** for each `(x,y)`.  
A small 2‑D DP can store the *minimum obstacle count* seen so far on a path to `(x,y)`.  
If that value ≤ `k` at the end, the Manhattan distance is the answer.  
However, this approach still requires BFS because the shortest path might not minimise the obstacle count.  
So keep the 3‑D BFS; it’s already optimal.

---

## 8. Summary

| Aspect | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | O(m × n × k) | O(m × n × k) | O(m × n × k) |
| **Space** | O(m × n × k) (bool visited) | O(m × n × k) | O(m × n × k) |
| **Key idea** | BFS + 3‑D visited | BFS + 3‑D visited | BFS + 3‑D visited |
| **Typical pitfall** | Forget that the same cell can be visited with different remaining `k`. | Same | Same |

---

## 8. How to Use This Solution for a Job Interview

1. **Start with the early‑exit** (`k >= m+n-2`).  
2. Explain that **state** is `tuple(x, y, remaining_k)` and that the queue is level‑by‑level.  
3. Emphasise that the **visited** array is 3‑D *and* **boolean** – no extra comparison is required.  
4. Mention the time/space trade‑offs and why the algorithm is still “Hard” even though the code is only ~30 lines.  
5. Bonus: show a quick “why not DFS” reasoning.  

> **Tip:** Interviewers love concise code. Keep the queue `int[]` (Java), `tuple` (C++), or `tuple`/`deque` (Python) to avoid heavy object overhead.

---

## 9. Final Takeaway

The “Shortest Path in a Grid with Obstacles Elimination” problem is the perfect blend of classic BFS and stateful DP.  
Mastering it demonstrates:

* A clear grasp of graph traversal principles.  
* Comfort with multi‑dimensional dynamic programming structures.  
* Awareness of common pitfalls that lead to TLE or WA.  

And **most importantly** – it’s a *show‑off* question for your next technical interview!  

---

## 10. Resources

| Link | Description |
|------|--------------|
| [LeetCode 1293](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/) | Official problem page (test cases, discussion) |
| [GFG – Shortest Path in Grid With Obstacles Elimination](https://www.geeksforgeeks.org/shortest-path-grid-obstacles-elimination/) | Detailed editorial with pseudocode |
| [GitHub – LeetCode 1293 Solutions](https://github.com/yangbaiy/LeetCode) | Community‑written solutions (Java, Python, C++) |

---

## 🎯 1‑Minute Summary

> *Use a BFS where each queue entry is `(x, y, remaining‑k)`. A 3‑D `visited` array guarantees that you won’t prune a better state. The algorithm runs in `O(m × n × k)` time and the same space – easily within the limits for `m, n ≤ 40`.*

Good luck on your next interview, and feel free to drop a comment if you’d like a deeper dive into the math behind state‑ful BFS! 🚀