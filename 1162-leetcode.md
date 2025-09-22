---
title: LeetCode 1162. As Far from Land as Possible - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏝️ “As Far From Land as Possible” – LeetCode 1162  
**Medium | BFS | O(n²) time | O(n²) space**  

> **Goal** – For a square `n × n` grid of `0`s (water) and `1`s (land), find a water cell whose Manhattan distance to the *nearest* land cell is **maximized**.  
> Return that maximum distance or `-1` if the grid contains only land or only water.

---

### 📌 Why This Problem Rocks for Interviews

| Good | Bad | Ugly |
|------|-----|------|
| **Intuitive** – Multi‑source BFS is a natural fit. | **Edge‑case prone** – Empty land/water grids or all‑land/all‑water grids require special handling. | **Memory overhead** – Storing a visited matrix doubles the memory cost (`O(n²)` extra). |
| **Reusable** – The same BFS template solves many “distance to nearest target” problems. | **Runtime limits** – For `n = 100` the BFS touches 10,000 cells, which is fine, but naive recursion (DFS) can blow the stack. | **Mutable input** – Some solutions modify the input grid to save space, but that is usually discouraged in interviews. |

---

## 🔍 Problem Statement (Paraphrased)

> **Input**: `int[][] grid`, `n = grid.length` (`n == grid[i].length`), `grid[i][j] ∈ {0, 1}`.  
> **Output**: `int` – the largest Manhattan distance from any water cell to its nearest land cell, or `-1` if no such water cell exists.

> **Manhattan distance** between `(x₀, y₀)` and `(x₁, y₁)` is `|x₀−x₁| + |y₀−y₁|`.

---

## 🧠 Intuition & High‑Level Strategy

1. **Multi‑source BFS**  
   *Treat every land cell `(i, j)` as a source node.*  
   *Enqueue all land cells first.*  
2. **Layer by layer expansion**  
   *When we pop a cell, we explore its four neighbours.*  
   *If a neighbour is water (`0`), it gets discovered at distance `current_distance + 1`.*  
3. **Track the maximum distance**  
   *Every time we finish exploring a whole BFS layer, we increment the distance counter.*  
   *The counter after the last layer is the answer.*  

If there are no land cells (`queue empty`) or every cell is land (`queue size == n²`), we return `-1`.

---

## 📦 Implementation Details

| Language | Key Points |
|----------|------------|
| **Java** | Use `Queue<int[]>` or `ArrayDeque`. Keep a `boolean[][] visited` or modify the grid. |
| **Python** | `collections.deque` for the queue. Use list comprehension for directions. |
| **C++** | `queue<pair<int,int>>`. Use a 2‑D vector for `visited` or modify `grid`. |

We’ll provide *clean*, *commented* code for each language.

---

## 🧩 Code – Java

```java
import java.util.*;

/**
 * LeetCode 1162: As Far from Land as Possible
 * BFS – multi‑source (all land cells)
 */
public class Solution {
    public int maxDistance(int[][] grid) {
        int n = grid.length;
        Queue<int[]> q = new ArrayDeque<>();
        boolean[][] visited = new boolean[n][n];

        // 1️⃣ Push all land cells into the queue
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {
                    q.offer(new int[]{i, j});
                    visited[i][j] = true;   // mark as visited
                }
            }
        }

        // 2️⃣ Edge cases: no land or no water
        if (q.isEmpty() || q.size() == n * n) return -1;

        int[] dx = {1, -1, 0, 0};
        int[] dy = {0, 0, 1, -1};
        int distance = -1;          // will become 0 after first layer

        // 3️⃣ Standard BFS
        while (!q.isEmpty()) {
            int sz = q.size();
            distance++;                 // we are moving one layer further
            for (int k = 0; k < sz; k++) {
                int[] cur = q.poll();
                for (int dir = 0; dir < 4; dir++) {
                    int ni = cur[0] + dx[dir];
                    int nj = cur[1] + dy[dir];
                    if (ni < 0 || ni >= n || nj < 0 || nj >= n) continue;
                    if (visited[ni][nj]) continue;
                    visited[ni][nj] = true;
                    q.offer(new int[]{ni, nj});
                }
            }
        }
        return distance;
    }
}
```

---

## 🧩 Code – Python

```python
from collections import deque
from typing import List

class Solution:
    def maxDistance(self, grid: List[List[int]]) -> int:
        n = len(grid)
        q = deque()

        # 1️⃣ enqueue all land cells
        for i in range(n):
            for j in range(n):
                if grid[i][j] == 1:
                    q.append((i, j))

        # 2️⃣ no land or no water
        if not q or len(q) == n * n:
            return -1

        dirs = [(1,0), (-1,0), (0,1), (0,-1)]
        dist = -1

        # 3️⃣ BFS
        while q:
            dist += 1
            for _ in range(len(q)):
                x, y = q.popleft()
                for dx, dy in dirs:
                    nx, ny = x + dx, y + dy
                    if 0 <= nx < n and 0 <= ny < n and grid[nx][ny] == 0:
                        grid[nx][ny] = 1          # mark visited by converting to land
                        q.append((nx, ny))

        return dist
```

> **Why we mutate `grid`?**  
> In Python it’s common to mutate the input to avoid an extra visited array.  
> In interviews, be explicit if mutation is allowed; otherwise, create a `visited` matrix.

---

## 🧩 Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxDistance(vector<vector<int>>& grid) {
        int n = grid.size();
        queue<pair<int,int>> q;
        vector<vector<bool>> visited(n, vector<bool>(n,false));

        // 1️⃣ enqueue all land cells
        for (int i=0;i<n;i++){
            for (int j=0;j<n;j++){
                if (grid[i][j]==1){
                    q.emplace(i,j);
                    visited[i][j]=true;
                }
            }
        }

        if (q.empty() || (int)q.size() == n*n) return -1;

        int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
        int dist = -1;

        // 2️⃣ BFS
        while (!q.empty()){
            int sz = q.size();
            dist++;                     // layer increment
            for (int k=0;k<sz;k++){
                auto [x,y] = q.front(); q.pop();
                for (auto &d: dirs){
                    int nx = x + d[0], ny = y + d[1];
                    if (nx<0 || nx>=n || ny<0 || ny>=n) continue;
                    if (visited[nx][ny]) continue;
                    visited[nx][ny] = true;
                    q.emplace(nx,ny);
                }
            }
        }
        return dist;
    }
};
```

---

## ⏱️ Complexity Analysis

| Measure | Reason |
|---------|--------|
| **Time** | Each cell is enqueued/processed at most once → `O(n²)` |
| **Space** | Queue + visited array → `O(n²)` |

> **Why `O(n²)` is optimal**  
> The answer can be as large as `2n-2` (e.g., all land on one corner).  
> Any algorithm that reads the whole grid must touch every cell → lower bound `Ω(n²)`.

---

## ⚠️ Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Recursive DFS** – stack overflow on large grids | Use iterative BFS |
| **Missing edge cases** – all `0`s or all `1`s | Explicitly check queue size before BFS |
| **Updating grid in place** – may violate interview constraints | Create a separate `visited` matrix or ask if mutation is allowed |
| **Wrong distance counter** – starting at `-1` vs `0` | Initialize `dist = -1` and increment *after* processing a layer |

---

## 📚 Variations & Extensions

| Variation | What changes |
|-----------|--------------|
| **Weighted grid** – each cell has a cost | Use Dijkstra’s algorithm instead of plain BFS |
| **Different target value** | Replace `1` with any target value |
| **K‑th nearest land** | Store distances in a vector, sort, then pick the K‑th smallest |

---

## 🚀 Ready‑to‑Deploy Checklist

1. **Choose BFS** – always the safe choice for distance‑to‑nearest‑target problems.  
2. **Handle all‑land / all‑water early** – this saves a lot of confusion.  
3. **Clarify mutation rules** – if the interview is strict about input immutability.  
4. **Comment your code** – show that you understand what each block does.  
5. **Run the sample**  

```java
int[][] grid = {{1,0,0},
                {0,0,0},
                {0,0,1}};
System.out.println(new Solution().maxDistance(grid)); // 2
```

---

## 🎯 How to Ace the Interview

1. **Explain the BFS idea first** – it shows you know the right algorithmic paradigm.  
2. **Walk through edge cases** – demonstrate you’re aware of `-1` conditions.  
3. **Write clear, typed code** – use a template queue, directions array, and a counter.  
4. **Discuss alternatives** – “I could also mutate the grid to save memory, but I’ll keep it pure here.”  

---

### 👇 TL;DR

- Use **multi‑source BFS** – enqueue all land cells.  
- Expand layer by layer, incrementing a `distance` counter.  
- Return the counter after the last layer (or `-1` for edge cases).  

> That’s all there is to “Being Far from Land” – simple, elegant, and interview‑ready!  

Happy coding, and may your answers always be **the farthest possible**! 🚀