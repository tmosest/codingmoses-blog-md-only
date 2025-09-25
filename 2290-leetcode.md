---
title: LeetCode 2290. Minimum Obstacle Removal to Reach Corner - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Obstacle Removal to Reach Corner  
**LeetCode 2290 – Hard**  

> **Goal** – Find the *minimum* number of obstacles that must be removed so you can walk from the top‑left corner of a grid to the bottom‑right corner.

---

## Table of Contents  
1. 📌 Problem Recap  
2. 🧠 Naïve Ideas (Why They Fail)  
3. ✅ 0‑1 BFS – The “Good” Solution  
4. 🔧 Implementation (Java / Python / C++)  
5. ❌ The “Bad” Bits – When Things Go Wrong  
6. 👹 The “Ugly” Parts – Edge Cases & Hidden Pitfalls  
7. ⏱️ Complexity Analysis  
8. 🎯 Interview‑Ready Tips  
9. 📈 SEO‑Optimized Summary  

---

## 1. 📌 Problem Recap

| Parameter | Meaning |
|-----------|---------|
| `grid` | `m × n` 2‑D array (1 ≤ m, n ≤ 10⁵, m × n ≤ 10⁵) |
| `grid[i][j]` | `0` → free cell, `1` → obstacle that can be removed |
| `start` | `(0, 0)` (always `0`) |
| `goal` | `(m-1, n-1)` (always `0`) |
| Allowed moves | 4‑directional (up/down/left/right) |

**Return** the minimum number of obstacles to delete so that a path exists.

---

## 2. 🧠 Naïve Ideas (Why They Fail)

| Idea | Complexity | Why it fails |
|------|------------|--------------|
| **Plain BFS** | `O(mn)` | BFS treats every edge equally – it can’t distinguish “free” from “blocked” moves. It will visit *all* cells even if a path is found early. |
| **DFS with backtracking** | Exponential | Backtracking explores all permutations of obstacle removals – hopeless for 10⁵ cells. |
| **DP / Floyd‑Warshall** | `O((mn)³)` | Impossible for 10⁵ cells. |
| **A* heuristic** | Still `O(mn)` | Requires admissible heuristic; still not better than 0‑1 BFS for this problem. |

The key observation: **moving into a free cell costs 0** (no obstacle removed), moving into an obstacle costs 1. We need a shortest‑path algorithm that handles **two distinct edge weights (0 or 1)**.  

---

## 3. ✅ 0‑1 BFS – The “Good” Solution

**0‑1 BFS** is a specialized BFS that runs in `O(V + E)` for graphs whose edge weights are only `0` or `1`.  

* **Queue** → `Deque` (double‑ended queue).  
* **Push front** when moving into a `0` cell (cost 0).  
* **Push back** when moving into a `1` cell (cost 1).  

Because all 0‑weight edges are processed first, the algorithm naturally discovers the minimal cost to every cell – just like Dijkstra, but far simpler and faster in this special case.

---

## 4. 🔧 Implementation

Below are clean, ready‑to‑paste implementations in **Java**, **Python**, and **C++**.

### 4.1 Java

```java
import java.util.*;

public class Solution {
    private static final int[] DX = {0, 1, 0, -1};
    private static final int[] DY = {1, 0, -1, 0};

    public int minimumObstacles(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[][] dist = new int[m][n];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);

        Deque<int[]> dq = new ArrayDeque<>();
        dist[0][0] = 0;
        dq.offerFirst(new int[]{0, 0});

        while (!dq.isEmpty()) {
            int[] cur = dq.pollFirst();
            int x = cur[0], y = cur[1];
            int curDist = dist[x][y];

            for (int d = 0; d < 4; d++) {
                int nx = x + DX[d], ny = y + DY[d];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;

                int newDist = curDist + grid[nx][ny];
                if (newDist < dist[nx][ny]) {
                    dist[nx][ny] = newDist;
                    if (grid[nx][ny] == 0) dq.offerFirst(new int[]{nx, ny});
                    else dq.offerLast(new int[]{nx, ny});
                }
            }
        }
        return dist[m - 1][n - 1];
    }
}
```

### 4.2 Python

```python
from collections import deque
from typing import List

class Solution:
    def minimumObstacles(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        INF = 10 ** 9
        dist = [[INF] * n for _ in range(m)]
        dq = deque([(0, 0)])
        dist[0][0] = 0
        dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]

        while dq:
            x, y = dq.popleft()
            cur = dist[x][y]
            for dx, dy in dirs:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n:
                    new = cur + grid[nx][ny]
                    if new < dist[nx][ny]:
                        dist[nx][ny] = new
                        if grid[nx][ny] == 0:
                            dq.appendleft((nx, ny))
                        else:
                            dq.append((nx, ny))
        return dist[m - 1][n - 1]
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumObstacles(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        const int INF = INT_MAX;
        vector<vector<int>> dist(m, vector<int>(n, INF));
        deque<pair<int,int>> dq;
        dist[0][0] = 0;
        dq.emplace_front(0, 0);
        const int dx[4] = {1,-1,0,0};
        const int dy[4] = {0,0,1,-1};

        while (!dq.empty()) {
            auto [x, y] = dq.front(); dq.pop_front();
            int cur = dist[x][y];
            for (int d = 0; d < 4; ++d) {
                int nx = x + dx[d], ny = y + dy[d];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                int newDist = cur + grid[nx][ny];
                if (newDist < dist[nx][ny]) {
                    dist[nx][ny] = newDist;
                    if (grid[nx][ny] == 0) dq.emplace_front(nx, ny);
                    else dq.emplace_back(nx, ny);
                }
            }
        }
        return dist[m-1][n-1];
    }
};
```

All three solutions run in **O(m × n)** time and use **O(m × n)** auxiliary space – perfectly compliant with the constraints.

---

## 5. ❌ The “Bad” Bits – When Things Go Wrong

| Bad Point | What to Watch Out For |
|-----------|-----------------------|
| **Large “INF” value** | In Java we use `Integer.MAX_VALUE` and never add to it (the algorithm never overflows because we always check `newDist < dist[nx][ny]`). In C++/Python use a safely large sentinel (`10⁹` or `INT_MAX`). |
| **Deque versus PriorityQueue** | A classic Dijkstra implementation (priority queue) also solves the problem but has a *log* factor. Use 0‑1 BFS only when you’re sure edge weights are 0/1; otherwise a priority queue is safer. |
| **Mutable state** | If you accidentally modify `grid`, you’ll lose the original obstacle layout – keep it `final`/`const`. |

### How to avoid these:  
* Always guard against integer overflow when computing `newDist = cur + grid[nx][ny]`.  
* Use `ArrayDeque` (Java), `deque` (Python), or `std::deque` (C++) – these are *O(1)* for both push front/back.

---

## 6. 👹 The “Ugly” Parts – Edge Cases & Hidden Pitfalls

| Edge Case | Why it’s ugly | Fix |
|-----------|---------------|------|
| **`m == 1 && n == 1`** | The algorithm still loops, but no moves are needed. | Early‑return `0` if `m == 1 && n == 1`. |
| **Grid contains only zeros** | 0‑cost path exists but algorithm will still visit every cell. | Still O(mn) – unavoidable, but the algorithm is still fast enough. |
| **All cells are `1` except start & goal** | Requires removing every obstacle along the diagonal. | 0‑1 BFS handles it gracefully – just ensure you push back for every obstacle. |
| **Multiple optimal paths** | We only need the *cost*, not the path itself. | Store only the cost (`dist`) – no need to reconstruct the path. |
| **Large input (10⁵ cells)** | Recursion depth or stack overflow in DFS solutions. | Use iterative BFS/Deque – no recursion. |

---

## 7. ⏱️ Complexity Analysis

| Metric | Calculation |
|--------|-------------|
| **Time** | Each cell is inserted into the deque at most once for each possible cost (0 or 1). Hence `O(V + E) = O(m × n)`. |
| **Space** | Distance matrix `O(m × n)` + deque `O(m × n)` in the worst case. |

With `m × n ≤ 10⁵`, both time and memory are comfortably within LeetCode’s limits.

---

## 8. 🎯 Interview‑Ready Tips

| Tip | Why it matters |
|-----|----------------|
| **Explain 0‑1 BFS first** | Shows you understand weighted shortest paths and can spot the special case. |
| **Mention Dijkstra fallback** | “If 0‑1 BFS feels shaky, just use Dijkstra with a priority queue; the complexity stays linear.” |
| **Talk about early exit** | “We could stop when we pop the goal from the deque, but we still need to process all cells of the same cost level to guarantee optimality.” |
| **Discuss space optimization** | “You can store distances in a 1‑D array and index via `i * n + j` to avoid the extra list of lists.” |
| **Stress on correctness** | “Always guard against index bounds; the deque ensures no negative cycles.” |

---

## 9. 📈 SEO‑Optimized Summary

> **Key phrases** – *LeetCode 2290*, *minimum obstacle removal to reach corner*, *0‑1 BFS*, *algorithm interview*, *coding interview*, *shortest path*, *job interview algorithm*, *coding challenge*.

**Short Answer**  
The minimum number of obstacles you need to remove equals the shortest path cost in a graph where moving into a free cell costs `0` and moving into an obstacle costs `1`. A single pass of **0‑1 BFS** (or a lightweight Dijkstra) finds this cost in linear time.

**Why this matters for your next interview**  
Mastering 0‑1 BFS demonstrates that you can:

1. Identify when a classic algorithm is overkill.  
2. Convert a weighted shortest‑path problem into a BFS‑friendly form.  
3. Deliver clean, production‑ready code in Java, Python, or C++.  

These are exactly the kinds of skills recruiters ask for in *algorithm* and *coding* interview questions.

> **Takeaway** – Whenever you see edge weights that are only `0` or `1`, think *0‑1 BFS* first. It’s fast, simple, and a great conversation starter on the interview stage.

Happy coding – and good luck on your next interview!