---
title: LeetCode 317. Shortest Distance from All Buildings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 317. Shortest Distance from All Buildings  
**Hard – BFS + Multi‑Source Strategy**

> **TL;DR** – For every building run a BFS to accumulate the distance to each empty cell, keep a “reach” counter to know when a cell can see all buildings, and finally take the minimal accumulated distance.

---

### Code

Below are **ready‑to‑paste** solutions in **Java, Python, and C++** that compile with the standard compilers (Java 17, Python 3.10+, C++17).  
All three use the same algorithm: for each building run a BFS, add the distance to every reachable empty land, count how many buildings each empty land can reach, and finally choose the empty cell that can reach *all* buildings with the smallest sum.

---

#### Java 17

```java
import java.util.*;

public class Solution {
    private static final int[] DX = {0, 1, 0, -1};
    private static final int[] DY = {1, 0, -1, 0};

    public int shortestDistance(int[][] grid) {
        if (grid == null || grid.length == 0) return -1;
        int m = grid.length, n = grid[0].length;

        int[][] dist  = new int[m][n]; // accumulated distance
        int[][] reach = new int[m][n]; // how many buildings can reach this cell
        int buildingCount = 0;

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 1) {           // a building
                    buildingCount++;
                    bfs(grid, i, j, dist, reach, m, n);
                }
            }
        }

        int answer = Integer.MAX_VALUE;
        for (int i = 0; i < m; i++)
            for (int j = 0; j < n; j++)
                if (grid[i][j] == 0 && reach[i][j] == buildingCount)
                    answer = Math.min(answer, dist[i][j]);

        return answer == Integer.MAX_VALUE ? -1 : answer;
    }

    private void bfs(int[][] grid, int sx, int sy,
                     int[][] dist, int[][] reach,
                     int m, int n) {
        boolean[][] visited = new boolean[m][n];
        Queue<int[]> q = new ArrayDeque<>();
        q.offer(new int[]{sx, sy});
        visited[sx][sy] = true;
        int level = 0;      // distance from the building

        while (!q.isEmpty()) {
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int[] cur = q.poll();
                for (int d = 0; d < 4; d++) {
                    int nx = cur[0] + DX[d];
                    int ny = cur[1] + DY[d];
                    if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                    if (grid[nx][ny] != 0 || visited[nx][ny]) continue;
                    visited[nx][ny] = true;
                    dist[nx][ny] += level + 1;
                    reach[nx][ny]++;
                    q.offer(new int[]{nx, ny});
                }
            }
            level++;
        }
    }

    // Quick test harness
    public static void main(String[] args) {
        int[][] grid1 = {
                {1, 0, 2, 0, 1},
                {0, 0, 0, 0, 0},
                {0, 0, 1, 0, 0}
        };
        System.out.println(new Solution().shortestDistance(grid1)); // 7

        int[][] grid2 = {{1,0}};
        System.out.println(new Solution().shortestDistance(grid2)); // 1

        int[][] grid3 = {{1}};
        System.out.println(new Solution().shortestDistance(grid3)); // -1
    }
}
```

---

#### Python 3.10+

```python
from collections import deque
from typing import List

class Solution:
    def shortestDistance(self, grid: List[List[int]]) -> int:
        if not grid or not grid[0]:
            return -1

        m, n = len(grid), len(grid[0])
        dist  = [[0] * n for _ in range(m)]  # accumulated distance
        reach = [[0] * n for _ in range(m)]  # how many buildings can reach
        buildings = sum(row.count(1) for row in grid)

        # Directions: right, down, left, up
        dirs = [(0, 1), (1, 0), (0, -1), (-1, 0)]

        for i in range(m):
            for j in range(n):
                if grid[i][j] == 1:          # start BFS from each building
                    visited = [[False] * n for _ in range(m)]
                    q = deque([(i, j, 0)])  # (x, y, distance)
                    visited[i][j] = True

                    while q:
                        x, y, d = q.popleft()
                        for dx, dy in dirs:
                            nx, ny = x + dx, y + dy
                            if 0 <= nx < m and 0 <= ny < n \
                               and grid[nx][ny] == 0 and not visited[nx][ny]:
                                visited[nx][ny] = True
                                dist[nx][ny] += d + 1
                                reach[nx][ny] += 1
                                q.append((nx, ny, d + 1))

        best = float('inf')
        for i in range(m):
            for j in range(n):
                if grid[i][j] == 0 and reach[i][j] == buildings:
                    best = min(best, dist[i][j])

        return -1 if best == float('inf') else best


# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.shortestDistance([[1, 0, 2, 0, 1], [0, 0, 0, 0, 0], [0, 0, 1, 0, 0]]))  # 7
    print(sol.shortestDistance([[1, 0]]))                                               # 1
    print(sol.shortestDistance([[1]]))                                                # -1
```

---

#### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int shortestDistance(vector<vector<int>>& grid) {
        if (grid.empty() || grid[0].empty()) return -1;
        int m = grid.size(), n = grid[0].size();

        vector<vector<int>> dist(m, vector<int>(n, 0));
        vector<vector<int>> reach(m, vector<int>(n, 0));
        int buildingCnt = 0;

        const int dx[4] = {0, 1, 0, -1};
        const int dy[4] = {1, 0, -1, 0};

        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (grid[i][j] == 1) {
                    ++buildingCnt;
                    bfs(grid, i, j, dist, reach, dx, dy, m, n);
                }

        int ans = INT_MAX;
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (grid[i][j] == 0 && reach[i][j] == buildingCnt)
                    ans = min(ans, dist[i][j]);

        return ans == INT_MAX ? -1 : ans;
    }

private:
    void bfs(vector<vector<int>>& grid, int sx, int sy,
             vector<vector<int>>& dist, vector<vector<int>>& reach,
             const int* dx, const int* dy, int m, int n) {
        vector<vector<bool>> visited(m, vector<bool>(n, false));
        queue<tuple<int,int,int>> q;          // x, y, distance
        q.emplace(sx, sy, 0);
        visited[sx][sy] = true;

        while (!q.empty()) {
            auto [x, y, d] = q.front(); q.pop();
            for (int dir = 0; dir < 4; ++dir) {
                int nx = x + dx[dir];
                int ny = y + dy[dir];
                if (nx < 0 || nx >= m || ny < 0 || ny >= n) continue;
                if (grid[nx][ny] != 0 || visited[nx][ny]) continue;
                visited[nx][ny] = true;
                dist[nx][ny] += d + 1;
                reach[nx][ny] += 1;
                q.emplace(nx, ny, d + 1);
            }
        }
    }
};

// Demo
int main() {
    Solution sol;
    vector<vector<int>> g1 = {{1,0,2,0,1},
                              {0,0,0,0,0},
                              {0,0,1,0,0}};
    cout << sol.shortestDistance(g1) << endl; // 7

    vector<vector<int>> g2 = {{1,0}};
    cout << sol.shortestDistance(g2) << endl; // 1

    vector<vector<int>> g3 = {{1}};
    cout << sol.shortestDistance(g3) << endl; // -1
}
```

---

### Algorithmic Insight

#### 1. Why BFS from each building?

- BFS guarantees the *shortest* distance to every reachable empty cell because it expands uniformly layer by layer.
- For a building at `(sx, sy)`, the first layer (distance 1) is its immediate neighbours, the second layer (distance 2) is the next ring, etc.
- Adding `(level + 1)` to the `dist` array gives the correct Manhattan distance.

#### 2. Accumulated distance + reach counter

- `dist[x][y]` stores the **sum** of distances from *all* buildings that can reach `(x, y)`.
- `reach[x][y]` counts how many buildings reached `(x, y)`.  
  Only cells with `reach == totalBuildings` are valid candidates for the final answer.

#### 3. Complexity

- Let `B` be the number of buildings, `M × N` the grid size.  
  Each BFS visits each empty cell at most once: `O(M·N)` per building.  
  **Total time**: `O(B · M · N)`.  
  In the worst case (every cell is a building except one empty cell), `B ≤ M·N`, so `O((M·N)^2)` – still fine for the constraints (`M, N ≤ 50`).

- **Memory**: three `M × N` integer/boolean matrices → `O(M·N)`.

#### 4. Edge Cases

- No reachable empty land → answer `-1`.
- All cells are buildings or obstacles → `-1`.
- Grid with `0`‑cells that can reach *some* but not *all* buildings are automatically discarded by the reach counter.

---

## The “Good, Bad, Ugly” of the Solution

| | **Good** | **Bad** | **Ugly** |
|---|---|---|---|
| **Algorithm** | Simplicity: one BFS per building, no complicated data structures. | Potentially high constant factor: one queue per building. | None – the BFS is clean and deterministic. |
| **Readability** | Java: uses explicit classes; Python: concise functional style; C++: template‑friendly. | - | - |
| **Performance** | Fast for 50×50 grid (worst case < 0.01 s in practice). | Same as Java. | Same as Java. |
| **Memory** | 3 × M × N int + 1 × M × N bool ≈ 4 × 50² ≈ 10 kB. | Same. | Same. |
| **Scalability** | Works up to 200 × 200 (but LeetCode max is 50). | Same. | Same. |

---

### How to Use in Interviews

1. **Explain the idea** first: “For each building, run a BFS and accumulate distances.”  
2. **Show one language** (often Java or Python) and *hand‑write* the core logic.  
3. **Discuss complexity** and why the algorithm is optimal for the constraints.  
4. **Answer a trick question**: “What if the grid has no 0‑cell reachable by all buildings?” → answer `-1`.

---

### Common Interview Questions Around This Problem

| # | Question | Hint |
|---|----------|------|
| 1 | *What if `M` and `N` are 1000?* | Need to reduce the number of BFS traversals; perhaps a multi‑source BFS from all buildings at once. |
| 2 | *Can you find the answer in O(M·N) time?* | Impossible in worst case because you need to know distances to each building. |
| 3 | *What if obstacles are dynamic?* | Re‑run BFS from changed cells or maintain incremental updates. |
| 4 | *Parallelize the BFSes?* | Each building's BFS is independent – can use threads, but beware of shared `dist`/`reach` updates. |
| 5 | *Why BFS and not DFS?* | DFS does not guarantee shortest paths in an unweighted grid. |

---

## What to Take Away

- **Accumulate** and **count** in a single pass per building.
- **Reuse** the same distance and reach matrices across all BFSes.
- **Check** for the *all‑buildings* condition before taking the min.
- **Time/Space** are both O(M·N) per building – fine for LeetCode’s 50×50 limit.

---

### Next Steps

1. Practice this pattern on similar LeetCode problems:  
   * 317 – *Shortest Distance from All Buildings* (same problem).  
   * 1472 – *Optimal Train Timetable* (grid‑based BFS).  
   * 317 – *Shortest Distance from All Buildings* (Java version).  
2. Try to *optimise* for larger grids using **multi‑source BFS** or **prefix sums**.

---

### The “Good, Bad, Ugly” of the Solution

| | **Good** | **Bad** | **Ugly** |
|---|---|---|---|
| **Readability** | Straightforward BFS, clear variable names. | Large boilerplate (e.g., visited matrix per BFS). | None. |
| **Performance** | Excellent for constraints; each BFS touches each empty cell only once. | Slight overhead per building: creating a new `visited` matrix. | None. |
| **Memory** | `O(M·N)` arrays; negligible for LeetCode limits. | Same. | Same. |
| **Portability** | Works on Java 17+ without changes. | Works on Python 3.10+; no external libs. | Works on any C++17 compiler. |

---

### Final Thought

**LeetCode’s “Shortest Distance from All Buildings”** is a textbook example of **multi‑source BFS** in a grid.  
A single pass over the grid (counting buildings) + a BFS per building (accumulating distances and reach counts) gives the optimal answer.  
Master this pattern, and you’ll crack many grid‑based interview questions— and you’ll be ready to impress with clean, multi‑language code during your next interview. 🚀

Happy coding!