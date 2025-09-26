---
title: LeetCode 1293. Shortest Path in a Grid with Obstacles Elimination - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Shortest Path in a Grid with Obstacles Elimination  
**(LeetCode 1293 – Hard)**  
*Python | Java | C++ – 3‑language solution + a job‑ready blog post*

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Key Insights & Greedy “No‑Go”](#key-insights)  
3. [Algorithm Design](#algorithm-design)  
4. [Complexity Analysis](#complexity-analysis)  
5. [Implementation in 3 Languages](#implementations)  
   * Java – 3‑D visited + BFS  
   * Python – `collections.deque` + 3‑D visited  
   * C++ – `struct` + `queue` + 3‑D visited  
6. [The Good, The Bad, and The Ugly](#good-bad-ugly)  
7. [SEO‑Ready Summary & Takeaway for Interviewers](#seo-summary)  
8. [Further Reading & Resources](#resources)  

---

## <a name="problem-overview"></a>Problem Overview  

You’re given a 2‑D grid (`m × n`) consisting of 0s (free cells) and 1s (obstacles).  
You can move **up, down, left, right** in one step.  
You start at `(0,0)` and want to reach `(m‑1,n‑1)` as fast as possible.  

You’re allowed to destroy **at most `k` obstacles** (turn a 1 into a 0).  
If it’s impossible to reach the goal, return `-1`.

> **Examples**  
> 1. `grid = [[0,0,0],[1,1,0],[0,0,0],[0,1,1],[0,0,0]]`, `k = 1` → `6`  
> 2. `grid = [[0,1,1],[1,1,1],[1,0,0]]`, `k = 1` → `-1`

Constraints (`1 ≤ m,n ≤ 40`, `1 ≤ k ≤ m × n`).

---

## <a name="key-insights"></a>Key Insights – “No‑Go” Strategy  

| Insight | Why it matters |
|---------|----------------|
| **BFS guarantees shortest path** | Because each move has equal cost (1). |
| **State must include remaining eliminations** | The same cell can be reached via a path that used fewer eliminations, giving more flexibility later. |
| **Prune impossible states early** | Visiting a cell with *more* remaining eliminations is always better. |
| **3‑D visited array** | Keeps track of `(x, y, remaining_k)` combos, preventing exponential blow‑up. |

---

## <a name="algorithm-design"></a>Algorithm Design  

1. **Queue entry** – `State(x, y, remaining_k, steps)`.  
2. **Visited** – `visited[x][y][remaining_k]` (boolean).  
3. **Initialize** – push `(0,0,k,0)`; mark visited.  
4. **While queue not empty**  
   * Pop front.  
   * If `(x,y)` is target → return `steps`.  
   * For each of 4 directions:  
     * If out of bounds → skip.  
     * If next cell is obstacle (`grid[nx][ny]==1`) and `remaining_k>0` → decrement and enqueue.  
     * If next cell is free (`grid[nx][ny]==0`) → enqueue with same `remaining_k`.  
     * Enqueue only if that state hasn’t been visited yet.  
5. **Return `-1`** if BFS exhausts all possibilities.

> **Why 3‑D visited works**  
> For a fixed cell `(x,y)`, only the *highest* remaining eliminations matter.  
> If we reach the same cell with fewer remaining eliminations, it can’t lead to a better outcome.  
> Thus we store the exact remaining count; the first visit for each `(x,y,remaining_k)` is optimal.

---

## <a name="complexity-analysis"></a>Complexity Analysis  

|  | Time | Space |
|--|------|-------|
| **BFS (3‑D visited)** | `O(m × n × (k+1))` worst‑case (each state processed once) | `O(m × n × (k+1))` for visited + queue |

With `m,n ≤ 40` and `k ≤ m × n`, this is comfortably within limits (≈ 40 × 40 × 1600 ≈ 2.5 M states).

---

## <a name="implementations"></a>Implementations in 3 Languages  

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    private static final int[][] DIRS = {
        {1, 0}, {-1, 0}, {0, 1}, {0, -1}
    };

    public int shortestPath(int[][] grid, int k) {
        int m = grid.length, n = grid[0].length;
        if (m == 1 && n == 1) return 0;

        boolean[][][] visited = new boolean[m][n][k + 1];
        Queue<State> q = new ArrayDeque<>();
        q.offer(new State(0, 0, k, 0));
        visited[0][0][k] = true;

        while (!q.isEmpty()) {
            State cur = q.poll();
            int x = cur.x, y = cur.y, remaining = cur.rem, steps = cur.steps;

            for (int[] d : DIRS) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;

                int nextRem = remaining;
                if (grid[nx][ny] == 1) {
                    if (remaining == 0) continue;
                    nextRem--;
                }

                if (nx == m - 1 && ny == n - 1) return steps + 1;
                if (!visited[nx][ny][nextRem]) {
                    visited[nx][ny][nextRem] = true;
                    q.offer(new State(nx, ny, nextRem, steps + 1));
                }
            }
        }
        return -1;
    }

    private static class State {
        int x, y, rem, steps;
        State(int x, int y, int rem, int steps) {
            this.x = x; this.y = y; this.rem = rem; this.steps = steps;
        }
    }
}
```

> **Why it’s clean** – `visited` is a 3‑D `boolean`, queue stores a lightweight `State`.  
> The `DIRS` array keeps movement logic separate.

---

### 2️⃣ Python

```python
from collections import deque
from typing import List

class Solution:
    DIRS = [(1,0), (-1,0), (0,1), (0,-1)]

    def shortestPath(self, grid: List[List[int]], k: int) -> int:
        m, n = len(grid), len(grid[0])
        if m == 1 and n == 1:
            return 0

        visited = [[[False] * (k+1) for _ in range(n)] for _ in range(m)]
        q = deque([(0, 0, k, 0)])          # (x, y, remaining_k, steps)
        visited[0][0][k] = True

        while q:
            x, y, rem, steps = q.popleft()
            for dx, dy in self.DIRS:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n:
                    next_rem = rem
                    if grid[nx][ny] == 1:
                        if rem == 0:
                            continue
                        next_rem -= 1
                    if nx == m-1 and ny == n-1:
                        return steps + 1
                    if not visited[nx][ny][next_rem]:
                        visited[nx][ny][next_rem] = True
                        q.append((nx, ny, next_rem, steps + 1))
        return -1
```

> **Pythonic touches** – `deque` for O(1) pops/pushes, list comprehension for 3‑D visited.

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct State {
    int x, y, rem, steps;
    State(int _x, int _y, int _rem, int _steps)
        : x(_x), y(_y), rem(_rem), steps(_steps) {}
};

class Solution {
    const int DIRS[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};
public:
    int shortestPath(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        if (m == 1 && n == 1) return 0;

        vector<vector<vector<bool>>> visited(
            m, vector<vector<bool>>(n, vector<bool>(k+1, false))
        );
        queue<State> q;
        q.emplace(0, 0, k, 0);
        visited[0][0][k] = true;

        while (!q.empty()) {
            State cur = q.front(); q.pop();
            int x = cur.x, y = cur.y, rem = cur.rem, steps = cur.steps;

            for (auto &d : DIRS) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;

                int next_rem = rem;
                if (grid[nx][ny] == 1) {
                    if (rem == 0) continue;
                    next_rem--;
                }

                if (nx == m-1 && ny == n-1) return steps + 1;
                if (!visited[nx][ny][next_rem]) {
                    visited[nx][ny][next_rem] = true;
                    q.emplace(nx, ny, next_rem, steps + 1);
                }
            }
        }
        return -1;
    }
};
```

> **C++‑style** – a `struct` that holds only primitive types, `vector` for 3‑D visited, `queue` for BFS.

---

## <a name="good-bad-ugly"></a>The Good, The Bad, and The Ugly  

| Stage | What’s Good | What to avoid (Bad) | Ugly patterns to watch out for |
|-------|-------------|---------------------|--------------------------------|
| **Data‑Structure Choice** | 3‑D visited → O(1) look‑ups | Using a *set of tuples* (`(x,y,rem)`) in Python adds a log‑factor and slows memory. | Declaring `int[][][]` in Java and forgetting to multiply by `(k+1)` leads to `ArrayIndexOutOfBoundsException`. |
| **State Pruning** | Check `if !visited[nx][ny][next_rem]` before enqueueing | Neglecting this results in *duplicate work* (exponential time). | Destroying an obstacle but still enqueuing the *same* `(x,y,rem)` state → endless loops. |
| **Early Exit** | Return immediately once you pop the target | Returning only after the loop finishes may miss the shortest solution. | Forgetting to handle the `1×1` grid edge case → wrong answer on small test. |
| **Coding Style** | Keep directions in a separate constant, use `struct / dataclass` for the state | Mixing logic with I/O boilerplate makes debugging hard. | Using recursion + memoization (DFS) instead of BFS → hard to prove optimality. |

> **Bottom line** – A *correct* BFS + 3‑D visited is both elegant and fast.  
> The “ugly” approaches (DFS + memo, priority queue with `k` as priority) may pass small tests but fail on the worst‑case, and are harder to explain in an interview.

---

## <a name="seo-summary"></a>SEO‑Ready Summary for Interviewers  

- **Keyword‑dense headline**: “Shortest Path in Grid with Obstacles Elimination – LeetCode 1293 – BFS / Java / Python / C++”
- **Meta description**:  
  > “Learn the hardest LeetCode 1293 solution in three languages. Understand why BFS + 3‑D visited is optimal, and discover common pitfalls that can make or break your coding interview.”
- **Focus points for hiring managers**:  
  * Correct use of BFS for shortest paths  
  * Proper state‑tracking (cell + remaining eliminations)  
  * Memory‑efficient 3‑D visited array  
  * Edge‑case handling (1×1 grid, no obstacles, `k=0`)  

> By presenting a *polished* multi‑language solution and discussing the “good, bad, ugly” aspects, you demonstrate not only algorithmic skill but also **software‑engineering mindset**—exactly what interviewers look for.

---

## <a name="resources"></a>Further Reading & Resources  

| Resource | Topic |
|----------|-------|
| [LeetCode 1293 – Discussion](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/discuss/) | Community solutions, edge‑case tricks |
| [Graph BFS Basics – Big O](https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/) | BFS fundamentals |
| [Three‑Dimensional Visited Array Pattern](https://leetcode.com/problems/shortest-path-in-a-grid-with-obstacles-elimination/discuss/520876/3-D-visited) | Why we need the third dimension |
| [Coding Interviews – LeetCode Hard Problems](https://leetcode.com/tag/graph/) | More grid‑based hard questions |

---

### TL;DR for Your Resume / Portfolio  

> *“I solved LeetCode 1293 (Shortest Path in a Grid with Obstacles Elimination) using an optimal BFS that tracks remaining obstacle‑eliminations in a 3‑D visited array. The solution runs in `O(m·n·k)` time and memory, works in Java, Python, and C++, and is a proven interview‑favorite.”*

Add this to your **GitHub Gist**, **LeetCode Profile**, or **coding‑interview blog** to show recruiters you’re ready for graph‑search challenges.  

Good luck – and remember: *BFS + state* is the recipe for all shortest‑path grid problems! 🚀