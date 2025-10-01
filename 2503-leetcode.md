---
title: LeetCode 2503. Maximum Number of Points From Grid Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2503 – Maximum Number of Points From Grid Queries  
**Hard | 5 k+ votes | 3‑language solution (Java 17, Python 3, C++17)**  

> In this post we’ll walk through the full solution to LeetCode’s “Maximum Number of Points From Grid Queries” problem.  
> We’ll cover the intuition, data‑structure choice, time‑/space‑complexities, and pitfalls.  
> We’ll also give you three clean implementations (Java, Python, C++) and a little SEO‑friendly blog article that will help you land a job.  

---

## Problem Recap

You’re given an `m × n` integer matrix `grid` and an array `queries`.  
Starting from the top‑left cell `(0, 0)`, for a particular query `q` you may move to any adjacent cell (4‑directionally) **only if** the value in the current cell is **strictly less than `q`**.  
Each time you enter a cell for the first time you earn a point.  
You can visit a cell multiple times, but only the first visit gives a point.  

For each `q` in `queries` you have to return the maximum number of points you can earn.

**Constraints**

| | |
|---|---|
| `2 ≤ m, n ≤ 1000` |
| `4 ≤ m*n ≤ 10⁵` |
| `1 ≤ k ≤ 10⁴` |
| `1 ≤ grid[i][j], queries[i] ≤ 10⁶` |

---

## Why a “BFS + Priority Queue” Works

If we think about a single query, the set of cells that can be reached is simply **all cells whose values are `< q`** that are *connected* to the start.  
When we process queries in **ascending order** we can “grow” this reachable set incrementally:

1. For the smallest query we run a BFS (or DFS) from the start, collecting every cell `< q`.  
2. For the next larger query we only need to look at cells that have just become “open” (their value lies between the previous and the current query).  
3. This incremental expansion can be implemented elegantly with a **min‑heap**:  
   * The heap always contains the frontier cells sorted by their value.  
   * While the smallest cell in the heap is `< currentQuery` we pop it, count a point, and push all of its unvisited neighbors.  

Because we never revisit a cell, each cell is inserted into the heap **exactly once** – once we decide it is reachable.  
Thus the total work is `O(m*n log(m*n))` (heap operations) plus `O(k log k)` for sorting the queries.

---

## The “Good, The Bad, The Ugly”

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Concept** | Simple BFS + priority queue, incremental | None | None |
| **Complexity** | `O(m*n log(m*n) + k log k)` | Heap overhead can be heavy for tight constraints | Re‑initialising visited arrays for every query would be O(k·m·n) – a big no‑no |
| **Memory** | `O(m*n)` for visited + heap | None | None |
| **Edge Cases** | Queries smaller than `grid[0][0]` → 0 points | None | None |
| **Implementation** | All three languages are straightforward | Need to manage zero‑based indices | Avoid forgetting to sort queries and restore original order |

---

## Full Solutions

Below are three self‑contained, ready‑to‑run solutions (Java, Python, C++).  
All three follow the same logic: sort queries, incremental BFS via a min‑heap, record results in original order.

> **Tip:** The key trick is to keep the *frontier* in a heap. The heap’s top is always the “cheapest” next cell to visit.

---

### 1️⃣ Java 17

```java
import java.util.*;

public class Solution {
    // Directions: up, down, left, right
    private static final int[][] DIRS = {{-1,0},{1,0},{0,-1},{0,1}};

    public int[] maxPoints(int[][] grid, int[] queries) {
        int m = grid.length, n = grid[0].length;
        int totalCells = m * n;

        // Pair: (queryValue, originalIndex)
        int[][] sortedQueries = new int[queries.length][2];
        for (int i = 0; i < queries.length; i++) {
            sortedQueries[i][0] = queries[i];
            sortedQueries[i][1] = i;
        }
        Arrays.sort(sortedQueries, Comparator.comparingInt(a -> a[0]));

        // Visited matrix
        boolean[][] visited = new boolean[m][n];
        // Min‑heap of cells by value
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        // Start from (0,0)
        pq.offer(new int[]{grid[0][0], 0, 0});
        visited[0][0] = true;

        int points = 0;            // Points collected so far
        int[] result = new int[queries.length];

        for (int[] q : sortedQueries) {
            int limit = q[0];
            int idx   = q[1];

            while (!pq.isEmpty() && pq.peek()[0] < limit) {
                int[] cell = pq.poll();
                points++;                    // First visit earns a point

                int r = cell[1], c = cell[2];
                for (int[] d : DIRS) {
                    int nr = r + d[0];
                    int nc = c + d[1];
                    if (nr >= 0 && nr < m && nc >= 0 && nc < n && !visited[nr][nc]) {
                        pq.offer(new int[]{grid[nr][nc], nr, nc});
                        visited[nr][nc] = true;
                    }
                }
            }
            result[idx] = points;
        }

        return result;
    }

    /* ---------- Test harness (optional) ---------- */
    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] grid = {{1,2,3},{2,5,7},{3,5,1}};
        int[] queries = {5,6,2};
        System.out.println(Arrays.toString(s.maxPoints(grid, queries))); // [5, 8, 1]
    }
}
```

---

### 2️⃣ Python 3

```python
import heapq
from typing import List

class Solution:
    def maxPoints(self, grid: List[List[int]], queries: List[int]) -> List[int]:
        m, n = len(grid), len(grid[0])

        # Sort queries, keep original index
        sorted_q = sorted((q, i) for i, q in enumerate(queries))
        result = [0] * len(queries)

        visited = [[False] * n for _ in range(m)]
        pq = []                      # min‑heap of (value, r, c)
        heapq.heappush(pq, (grid[0][0], 0, 0))
        visited[0][0] = True

        points = 0
        dirs = [(1,0),(-1,0),(0,1),(0,-1)]

        for limit, idx in sorted_q:
            while pq and pq[0][0] < limit:
                val, r, c = heapq.heappop(pq)
                points += 1
                for dr, dc in dirs:
                    nr, nc = r + dr, c + dc
                    if 0 <= nr < m and 0 <= nc < n and not visited[nr][nc]:
                        heapq.heappush(pq, (grid[nr][nc], nr, nc))
                        visited[nr][nc] = True
            result[idx] = points
        return result

# ---- quick test ----
if __name__ == "__main__":
    sol = Solution()
    grid = [[1,2,3],[2,5,7],[3,5,1]]
    queries = [5,6,2]
    print(sol.maxPoints(grid, queries))  # [5, 8, 1]
```

---

### 3️⃣ C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maxPoints(vector<vector<int>>& grid, vector<int>& queries) {
        int m = grid.size(), n = grid[0].size();

        // Store queries with original indices
        vector<pair<int,int>> sortedQueries;
        for (int i = 0; i < queries.size(); ++i)
            sortedQueries.push_back({queries[i], i});
        sort(sortedQueries.begin(), sortedQueries.end());

        vector<vector<bool>> visited(m, vector<bool>(n,false));
        priority_queue<tuple<int,int,int>,
                       vector<tuple<int,int,int>>,
                       greater<tuple<int,int,int>>> pq;
        // Start cell
        pq.emplace(grid[0][0], 0, 0);
        visited[0][0] = true;

        vector<int> ans(queries.size());
        int points = 0;
        const int dr[4] = {-1,1,0,0};
        const int dc[4] = {0,0,-1,1};

        for (auto [limit, idx] : sortedQueries) {
            while (!pq.empty() && get<0>(pq.top()) < limit) {
                auto [val, r, c] = pq.top(); pq.pop();
                ++points;   // first visit earns a point

                for (int d = 0; d < 4; ++d) {
                    int nr = r + dr[d], nc = c + dc[d];
                    if (nr>=0 && nr<m && nc>=0 && nc<n && !visited[nr][nc]) {
                        pq.emplace(grid[nr][nc], nr, nc);
                        visited[nr][nc] = true;
                    }
                }
            }
            ans[idx] = points;
        }
        return ans;
    }
};

/* ---------- Optional test harness ---------- */
int main() {
    Solution s;
    vector<vector<int>> grid = {{1,2,3},{2,5,7},{3,5,1}};
    vector<int> queries = {5,6,2};
    vector<int> res = s.maxPoints(grid, queries);
    for (int x : res) cout << x << " ";
    // Output: 5 8 1
}
```

---

## How to Submit

* If you’re on LeetCode, paste the `maxPoints` function body inside the provided `Solution` class.  
* If you want to run locally, use the test harnesses in the examples.  
* Remember to import/define `DIRS` or equivalent directions array.

---

## 🚀 Takeaway

*Sort queries → incremental BFS via a min‑heap → record results.*  
The same algorithm works flawlessly in Java, Python, and C++.  
The problem becomes a textbook example of “process queries in increasing order” to avoid redundant work.

Happy coding! 🎉

--- 
*Reference:*  
[LeetCode Problem 2437 – Maximum Points on a Grid](https://leetcode.com/problems/maximum-points-on-a-grid/)

--- 

*Prepared by @**YourName** – Code reviewer, algorithm enthusiast, and Java/ Python/ C++ lover.*