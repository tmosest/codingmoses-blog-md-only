---
title: LeetCode 3286. Find a Safe Walk Through a Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3286 – “Find a Safe Walk Through a Grid”  
*Java / Python / C++ Solutions + In‑Depth Blog Post*  

---

## TL;DR  
- **Goal:** Start at `(0,0)`, finish at `(m‑1,n‑1)` while keeping health > 0 after stepping on every cell.  
- **Health loss:** `1` point for each unsafe cell (`grid[i][j]==1`).  
- **Return:** `true` if a safe path exists, otherwise `false`.  

**Optimal Strategy:**  
1. **BFS with state (row, col, remainingHealth).**  
2. **Keep the best health we’ve seen at each cell** – only re‑queue when we arrive with *more* health.  
3. **Time:** `O(m × n × health)` (≤ 50 × 50 × 100 ≈ 250 000).  
4. **Space:** `O(m × n)` for the best‑health table.  

We’ll also show a *DP* (0‑1 BFS) alternative that runs in `O(m × n)`.

---

## 1. Algorithmic Insights

| Good | Bad | Ugly |
|------|-----|------|
| **BFS** guarantees we explore the *shortest* health‑loss path first. | **State explosion** if we naively keep every health value → memory blow‑up. | **Recursive DFS + memo** can cause stack overflow on 50×50 grids. |
| **Visiting with max health** avoids revisiting a cell with lower or equal health. | **Edge case:** Starting cell may be unsafe – we must subtract that loss before BFS. | **Off‑by‑one bug:** Failing to check `healthRemaining > 0` after every move leads to false positives. |
| **0‑1 BFS DP** turns the problem into “minimum number of unsafe cells along any path”. | **DP only works when you know the optimal cost** (here 0/1 costs). | **Large memory for 3‑D dp** (`m×n×health`) is unnecessary and slow. |

---

## 2. Reference Solutions

Below you’ll find clean, idiomatic implementations in **Java, Python, and C++**.

> **All solutions assume the input grid is non‑empty and rectangular.**  
> **`health` is guaranteed to be `>= 1`.**

---

### 2.1 Java – BFS with “best health” pruning

```java
import java.util.*;

public class Solution {
    // directions: up, down, left, right
    private static final int[][] DIRS = {{-1,0},{1,0},{0,-1},{0,1}};

    public boolean findSafeWalk(List<List<Integer>> grid, int health) {
        int m = grid.size();
        int n = grid.get(0).size();

        // Initial health after stepping on start cell
        int startLoss = grid.get(0).get(0);
        if (health <= startLoss) return false;
        int startHealth = health - startLoss;

        // bestHealth[i][j] = maximum health left when we first reach cell (i,j)
        int[][] bestHealth = new int[m][n];
        for (int[] row : bestHealth) Arrays.fill(row, -1);

        Queue<int[]> q = new ArrayDeque<>();
        q.offer(new int[]{0, 0, startHealth});
        bestHealth[0][0] = startHealth;

        while (!q.isEmpty()) {
            int[] cur = q.poll();
            int r = cur[0], c = cur[1], h = cur[2];

            if (r == m - 1 && c == n - 1) return true; // reached goal

            for (int[] d : DIRS) {
                int nr = r + d[0];
                int nc = c + d[1];
                if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;

                int loss = grid.get(nr).get(nc);
                int nh = h - loss;
                if (nh <= 0) continue;               // would die
                if (nh <= bestHealth[nr][nc]) continue; // no improvement

                bestHealth[nr][nc] = nh;
                q.offer(new int[]{nr, nc, nh});
            }
        }
        return false; // no path
    }
}
```

**Why it works**

* The queue guarantees we explore cells in increasing order of the *number of steps taken*, not health.  
* The `bestHealth` table prunes revisits unless we arrive with *more* health than before.  
* Because each cell is enqueued at most `health` times, the algorithm runs fast enough for the constraints.

---

### 2.2 Python – BFS + best‑health table

```python
from collections import deque
from typing import List

class Solution:
    def findSafeWalk(self, grid: List[List[int]], health: int) -> bool:
        m, n = len(grid), len(grid[0])
        start_loss = grid[0][0]
        if health <= start_loss:
            return False
        start_health = health - start_loss

        best = [[-1] * n for _ in range(m)]
        best[0][0] = start_health
        q = deque([(0, 0, start_health)])

        dirs = [(-1, 0), (1, 0), (0, -1), (0, 1)]

        while q:
            r, c, h = q.popleft()
            if r == m - 1 and c == n - 1:
                return True
            for dr, dc in dirs:
                nr, nc = r + dr, c + dc
                if 0 <= nr < m and 0 <= nc < n:
                    loss = grid[nr][nc]
                    nh = h - loss
                    if nh <= 0:
                        continue
                    if nh <= best[nr][nc]:
                        continue
                    best[nr][nc] = nh
                    q.append((nr, nc, nh))
        return False
```

---

### 2.3 C++ – BFS with priority on remaining health

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool findSafeWalk(vector<vector<int>>& grid, int health) {
        int m = grid.size(), n = grid[0].size();
        int startLoss = grid[0][0];
        if (health <= startLoss) return false;
        int startHealth = health - startLoss;

        vector<vector<int>> best(m, vector<int>(n, -1));
        queue<tuple<int,int,int>> q;
        q.emplace(0,0,startHealth);
        best[0][0] = startHealth;

        const int dr[4] = {-1,1,0,0};
        const int dc[4] = {0,0,-1,1};

        while (!q.empty()) {
            auto [r,c,h] = q.front(); q.pop();
            if (r==m-1 && c==n-1) return true;
            for (int k=0;k<4;++k){
                int nr=r+dr[k], nc=c+dc[k];
                if (nr<0||nr>=m||nc<0||nc>=n) continue;
                int loss = grid[nr][nc];
                int nh = h - loss;
                if (nh<=0) continue;
                if (nh <= best[nr][nc]) continue;
                best[nr][nc] = nh;
                q.emplace(nr,nc,nh);
            }
        }
        return false;
    }
};
```

---

## 3. A DP (0‑1 BFS) Alternative

The BFS above is already optimal, but many interviewers like to see a *dynamic‑programming* take‑away:  
“Find the minimum number of unsafe cells (`grid[i][j]==1`) along any path.”  

If that minimum is `k`, we only need `health > k` (because each unsafe cell deducts one health point).  

### 3.1 Why 0‑1 BFS?  
* Each move costs either `0` (safe cell) or `1` (unsafe cell).  
* Standard Dijkstra would be overkill; 0‑1 BFS uses a double‑ended queue to process cost‑0 edges before cost‑1 edges, giving linear time.

### 3.2 Java implementation

```java
import java.util.*;

public class SolutionDP {
    private static final int[][] DIRS = {{-1,0},{1,0},{0,-1},{0,1}};

    public boolean findSafeWalk(List<List<Integer>> grid, int health) {
        int m = grid.size(), n = grid.get(0).size();
        int[][] dist = new int[m][n];
        for (int[] row : dist) Arrays.fill(row, Integer.MAX_VALUE);

        Deque<int[]> dq = new ArrayDeque<>();
        int startLoss = grid.get(0).get(0);
        if (health <= startLoss) return false;          // cannot even step on the start

        dist[0][0] = startLoss;   // cost includes start cell
        dq.offerFirst(new int[]{0,0});

        while (!dq.isEmpty()) {
            int[] cur = dq.pollFirst();
            int r = cur[0], c = cur[1];

            for (int[] d : DIRS) {
                int nr = r + d[0], nc = c + d[1];
                if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;
                int add = grid.get(nr).get(nc);
                int nd = dist[r][c] + add;
                if (nd < dist[nr][nc]) {
                    dist[nr][nc] = nd;
                    if (add == 0) dq.offerFirst(new int[]{nr,nc});
                    else dq.offerLast(new int[]{nr,nc});
                }
            }
        }
        // dist[m-1][n-1] = minimal # of unsafe cells on any path
        return health > dist[m-1][n-1];
    }
}
```

### 3.2 Python version

```python
from collections import deque
from typing import List

class Solution:
    def findSafeWalk(self, grid: List[List[int]], health: int) -> bool:
        m, n = len(grid), len(grid[0])
        INF = 10**9
        dist = [[INF]*n for _ in range(m)]
        dist[0][0] = grid[0][0]
        dq = deque([(0,0)])

        dirs = [(-1,0),(1,0),(0,-1),(0,1)]

        while dq:
            r,c = dq.popleft()
            for dr,dc in dirs:
                nr, nc = r+dr, c+dc
                if 0 <= nr < m and 0 <= nc < n:
                    cost = grid[nr][nc]
                    nd = dist[r][c] + cost
                    if nd < dist[nr][nc]:
                        dist[nr][nc] = nd
                        if cost == 0:
                            dq.appendleft((nr,nc))
                        else:
                            dq.append((nr,nc))

        return health > dist[m-1][n-1]
```

**Complexity**

* **Time:** `O(m × n)` – each cell processed at most twice (once for a `0` edge, once for a `1` edge).  
* **Space:** `O(m × n)` – a simple 2‑D array of minimal unsafe‑cell counts.

---

## 4. Edge‑Case Checklist

| Case | How to handle | Why it matters |
|------|---------------|----------------|
| **Start cell unsafe** | Subtract its loss *before* BFS. | Failing to do so will return `true` even if you die immediately. |
| **Health exactly equal to loss** | Return `false`. | The problem explicitly says health must stay **> 0**. |
| **Goal cell unsafe** | Ensure `remainingHealth - grid[goal] > 0`. | If we ignore the loss at the final cell, we may think a path exists when it’s fatal. |
| **Large health** | No special handling – the pruning table takes care of it. | Only cells that improve health get re‑queued, so memory stays `O(m × n)`. |

---

## 5. Interview‑Ready Tips

| Tip | Code Snippet | Why it’s helpful |
|-----|--------------|------------------|
| **Explain the state** – `(r, c, remainingHealth)` – **before** diving into code. | `int[] state = {r, c, healthLeft};` | Shows you understand the problem constraints. |
| **Show the pruning idea** – “we’ll keep the best health for each cell”. | `if (nh <= bestHealth[nr][nc]) continue;` | Cuts down on unnecessary re‑exploration. |
| **Handle the starting cell explicitly**. | `int startHealth = health - grid[0][0];` | Many candidates forget this, leading to subtle bugs. |
| **Mention 0‑1 BFS** as an alternative. | `deque<int[]> dq;` | Demonstrates breadth of knowledge (DP + BFS). |

---

## 6. Final Thoughts

* The BFS approach is **simple, fast, and guaranteed correct** under the constraints.  
* The DP/0‑1 BFS alternative is great for demonstrating a “cost‑minimization” view.  
* Avoid 3‑D DP or naive DFS recursion – they’re unnecessary and can mislead interviewers.

Good luck! 🚀  

---

### 7. Search‑Engine‑Ready Keywords

- LeetCode 3286  
- Find a Safe Walk Through a Grid  
- BFS health pruning  
- 0‑1 BFS dynamic programming  
- Java BFS LeetCode solution  
- Python BFS grid path  
- C++ BFS with remaining health  

---