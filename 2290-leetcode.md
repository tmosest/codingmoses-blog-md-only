---
title: LeetCode 2290. Minimum Obstacle Removal to Reach Corner - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Minimum Obstacle Removal to Reach Corner – LeetCode 2290  
### A Deep‑Dive, 0‑1 BFS Masterclass (Java / Python / C++)  
> **SEO Keywords** – *LeetCode 2290*, *Minimum Obstacle Removal to Reach Corner*, *0‑1 BFS*, *job interview coding*, *Java BFS solution*, *Python Dijkstra*, *C++ grid traversal*, *software engineering interview*  

---

### TL;DR  
- **Problem**: Find the minimum number of obstacles (`1`) you have to delete to travel from `(0,0)` to `(m‑1,n‑1)` in an `m × n` grid of `0`s (free) and `1`s (obstacle).  
- **Solution**: 0‑1 Breadth‑First Search (deque‑based Dijkstra).  
- **Time**: `O(m · n)`  
- **Space**: `O(m · n)`  

---

## 1. Problem Recap

You’re given a rectangular grid where each cell is either empty (`0`) or contains an obstacle (`1`).  
You can move up, down, left, or right **only onto empty cells**.  
Your task: *remove as few obstacles as possible* so that a path from the top‑left corner to the bottom‑right corner exists.

> **Edge cases**  
> • `grid[0][0]` and `grid[m-1][n-1]` are guaranteed to be `0`.  
> • Grid size can be as large as `10^5` cells (`m * n ≤ 100 000`).  
> • `1 ≤ m, n ≤ 10^5`.  

---

## 2. Why 0‑1 BFS?

The grid can be seen as a graph where every cell is a node and edges connect orthogonally adjacent cells.  
Moving onto a **free** cell costs `0` (no obstacle removal), whereas moving onto an **obstacle** costs `1` (you have to delete it).  

This is a classic *edge‑weight‑`0` or `1` shortest path* problem.  
Instead of a full Dijkstra (priority queue), a *deque* suffices:

- If the edge cost is `0`, push the node to the **front** (higher priority).  
- If the edge cost is `1`, push it to the **back** (lower priority).  

This guarantees that the first time you pop a node, you’ve already found the optimal number of obstacle removals for that cell.

---

## 3. Algorithm Overview

1. **Initialisation**  
   * `dist[m][n]` – distance matrix, initialised with `∞`.  
   * `dist[0][0] = 0`.  
   * Deque `dq` starts with `(0,0)`.

2. **BFS Loop**  
   While the deque isn’t empty:  
   * Pop `(x, y)`.  
   * For each of the four neighbours `(nx, ny)` inside bounds:  
     - `newDist = dist[x][y] + grid[nx][ny]`.  
     - If `newDist < dist[nx][ny]`:  
       * Update `dist[nx][ny]`.  
       * Push `(nx, ny)` to front if `grid[nx][ny] == 0`, else back.

3. **Result**  
   Return `dist[m-1][n-1]`.

---

## 4. Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
Each uses the 0‑1 BFS pattern described above.

> **Tip**: When writing for interviews, keep the code concise but readable – add comments only where the logic isn’t obvious.

---

### 4.1 Java (Java 17)

```java
import java.util.ArrayDeque;
import java.util.Arrays;
import java.util.Deque;

public class Solution {
    public int minimumObstacles(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int INF = Integer.MAX_VALUE / 2;          // avoid overflow

        int[][] dist = new int[m][n];
        for (int[] row : dist) Arrays.fill(row, INF);

        Deque<int[]> dq = new ArrayDeque<>();
        dist[0][0] = 0;
        dq.offerFirst(new int[]{0, 0});

        int[][] dirs = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};

        while (!dq.isEmpty()) {
            int[] cur = dq.pollFirst();
            int x = cur[0], y = cur[1];

            for (int[] d : dirs) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;

                int newDist = dist[x][y] + grid[nx][ny];
                if (newDist < dist[nx][ny]) {
                    dist[nx][ny] = newDist;
                    if (grid[nx][ny] == 0) {
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

---

### 4.2 Python (Python 3.10+)

```python
from collections import deque
from typing import List

class Solution:
    def minimumObstacles(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        INF = 10**9
        dist = [[INF] * n for _ in range(m)]

        dq = deque()
        dist[0][0] = 0
        dq.append((0, 0))

        dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]

        while dq:
            x, y = dq.popleft()
            for dx, dy in dirs:
                nx, ny = x + dx, y + dy
                if 0 <= nx < m and 0 <= ny < n:
                    new = dist[x][y] + grid[nx][ny]
                    if new < dist[nx][ny]:
                        dist[nx][ny] = new
                        if grid[nx][ny] == 0:
                            dq.appendleft((nx, ny))
                        else:
                            dq.append((nx, ny))
        return dist[m - 1][n - 1]
```

---

### 4.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumObstacles(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        const int INF = INT_MAX / 2;

        vector<vector<int>> dist(m, vector<int>(n, INF));
        deque<pair<int,int>> dq;

        dist[0][0] = 0;
        dq.emplace_front(0, 0);

        const int dirs[4][2] = {{1,0},{-1,0},{0,1},{0,-1}};

        while (!dq.empty()) {
            auto [x, y] = dq.front();
            dq.pop_front();

            for (auto& d : dirs) {
                int nx = x + d[0], ny = y + d[1];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;

                int nd = dist[x][y] + grid[nx][ny];
                if (nd < dist[nx][ny]) {
                    dist[nx][ny] = nd;
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

---

## 5. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(m · n)` | `O(m · n)` | `O(m · n)` |
| **Space** | `O(m · n)` | `O(m · n)` | `O(m · n)` |
| **Why it’s fast** | Every cell is popped at most once from the front; pushing to front/back is `O(1)`. | Same reasoning with `deque`. | Same reasoning. |

---

## 6. Edge‑Case Handling & Gotchas

| Situation | What to watch for | Fix |
|-----------|-------------------|-----|
| **Large grid** (`m*n = 10^5`) | 32‑bit `int` distance can overflow if you add many `1`s. | Use `INF = INT_MAX/2` (C++/Java) or `10**9` (Python). |
| **Zero‑cost cycle** | 0‑cost edges could create loops. | 0‑1 BFS automatically handles this; the distance array ensures we never revisit a node with a worse distance. |
| **Neighbour bounds** | Off‑by‑one errors are common. | Pre‑compute `m, n` once; use `if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;` |

---

## 7. “Good / Bad / Ugly” from an Interview Lens

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Intuition** | Recognising the `0/1` weight graph is elegant. | Forgetting that you can only step on `0`s → wrong BFS. | Trying to over‑engineer with full Dijkstra when 0‑1 BFS is simpler. |
| **Algorithm Choice** | 0‑1 BFS = optimal + concise. | Using a heap slows down constant factors. | Blindly applying BFS without cost handling → O(n^2) runtime. |
| **Implementation** | Clean, single‑pass code. | Over‑commenting makes the solution noisy. | Forgetting to reset `INF` before each test case (state leakage). |
| **Testing** | Include edge‑cases: all `0`, all `1` (except corners), sparse grid, large sparse grid. | Only test on the sample – risk missing edge cases. | No `assert` statements, so bugs slip into production. |
| **Readability** | Comments only where logic is non‑trivial. | Inline lambda for deque push → obscures intent. | Mixed C++ `using namespace std;` vs explicit `std::` usage. |

---

## 8. Why This Solution Rocks for Your Next Job

1. **Shows algorithmic depth** – 0‑1 BFS is a classic interview pattern.  
2. **Demonstrates coding discipline** – clean data structures, no magic numbers, proper bounds checking.  
3. **Proves efficiency** – handles the `100 000`‑cell limit comfortably.  

> **Hiring Manager’s Perspective**  
> *“Can you find the shortest path on a grid with cost 0/1 in linear time?”*  
> ✔️ *“Yes, with 0‑1 BFS.”*  
> ✔️ *“Here’s my Java implementation – ready to paste into the IDE.”*  

---

## 9. Call‑to‑Action for the Ambitious Coder

If you’ve nailed this problem:

1. **Post it on LinkedIn** with the title “My LeetCode 2290 Solution – 0‑1 BFS in Java / Python / C++”.  
2. **Tag recruiters** in the tech space – recruiters often search for *LeetCode 2290* solutions.  
3. **Add the code** to your GitHub repo under a folder `leetcode/2290_min_obstacle_removal`.  

> 👉 **Want to land a software engineering role?**  
>  1️⃣ Build a personal portfolio with well‑commented solutions.  
>  2️⃣ Share your blog post on Medium, dev.to, and Hacker News.  
>  3️⃣ Apply to companies that love algorithmic challenges – Amazon, Google, Stripe, Shopify, etc.  

---

## 10. Final Thoughts

The *Minimum Obstacle Removal to Reach Corner* problem is a **perfect showcase** of:

- Graph modeling of a grid  
- 0‑1 BFS – a concise Dijkstra variant  
- Thoughtful edge‑case handling  

Master this pattern, and you’ll not only ace LeetCode 2290 but also impress recruiters who value clean, efficient code.

> **Your next big interview question might be a grid problem—just remember the deque trick!**  

Happy coding, and may the algorithm be ever in your favour!  

--- 

**Follow me** on Twitter @YourCodingJourney for more interview prep, algorithm deep‑dives, and career‑advancement tips.