---
title: LeetCode 305. Number of Islands II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Summary of the approach**

The idea is to treat every new land cell as a new “island id”.  
When a new cell is added we look at its 4 neighbours:

1. **No neighbours are already land** – the new cell forms a brand‑new island.  
   → give it a fresh id, increase the island count.

2. **One or more neighbours are land** – all those neighbours belong to islands that must now be merged into a single island.  
   → choose one of the neighbour ids (the smallest is used in the code) and flood‑fill the whole connected component, replacing every old id by the new one.  
   → the number of islands is reduced by the number of distinct neighbour islands minus one.

This works in **O(L · m · n)** time (worst‑case DFS over the whole grid) and **O(m · n)** extra space.

---

### Java implementation (straightforward DFS)

```java
import java.util.*;

class Solution {
    private static final int[][] DIRS = {{1,0},{-1,0},{0,1},{0,-1}};

    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        int[][] grid = new int[m][n];          // 0 = water, >0 = island id
        int nextId = 1;                         // ids start from 1
        int islandCnt = 0;
        List<Integer> ans = new ArrayList<>();

        for (int[] pos : positions) {
            int r = pos[0], c = pos[1];

            // Ignore duplicate land addition
            if (grid[r][c] != 0) {
                ans.add(islandCnt);
                continue;
            }

            Set<Integer> neighbourIds = new HashSet<>();
            int minNeighbour = Integer.MAX_VALUE;

            // Inspect 4 neighbours
            for (int[] d : DIRS) {
                int nr = r + d[0], nc = c + d[1];
                if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;
                int id = grid[nr][nc];
                if (id == 0) continue;          // water
                neighbourIds.add(id);
                minNeighbour = Math.min(minNeighbour, id);
            }

            if (neighbourIds.isEmpty()) {
                // New isolated island
                grid[r][c] = nextId++;
                islandCnt++;
            } else {
                // Merge all neighbour islands
                grid[r][c] = -1;                 // temporary marker
                floodFill(grid, r, c, minNeighbour);
                islandCnt -= neighbourIds.size() - 1;
            }

            ans.add(islandCnt);
        }
        return ans;
    }

    private void floodFill(int[][] grid, int r, int c, int newId) {
        int m = grid.length, n = grid[0].length;
        if (r < 0 || r >= m || c < 0 || c >= n) return;
        if (grid[r][c] == 0 || grid[r][c] == newId) return;
        grid[r][c] = newId;
        for (int[] d : DIRS) {
            floodFill(grid, r + d[0], c + d[1], newId);
        }
    }
}
```

---

### Python implementation (concise 20‑line DFS)

```python
class Solution:
    DIRS = [(1,0),(-1,0),(0,1),(0,-1)]

    def numIslands2(self, m: int, n: int, positions: List[List[int]]) -> List[int]:
        grid = [[0]*n for _ in range(m)]
        next_id = 1
        cnt = 0
        ans = []

        def dfs(r, c, target):
            if r<0 or r>=m or c<0 or c>=n or grid[r][c]==0 or grid[r][c]==target:
                return
            grid[r][c] = target
            for dr,dc in self.DIRS:
                dfs(r+dr, c+dc, target)

        for r, c in positions:
            if grid[r][c] != 0:
                ans.append(cnt)
                continue

            neighbours = set()
            for dr,dc in self.DIRS:
                nr, nc = r+dr, c+dc
                if 0<=nr<m and 0<=nc<n and grid[nr][nc]:
                    neighbours.add(grid[nr][nc])

            if not neighbours:
                grid[r][c] = next_id
                next_id += 1
                cnt += 1
            else:
                min_id = min(neighbours)
                grid[r][c] = -1
                dfs(r, c, min_id)
                cnt -= len(neighbours)-1

            ans.append(cnt)

        return ans
```

Both implementations run comfortably within the limits for typical constraints. The DFS approach is easy to understand, but for very large grids (where *m × n* is huge compared to the number of operations) a Union‑Find solution is usually preferable.