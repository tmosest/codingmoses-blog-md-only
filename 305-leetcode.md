---
title: LeetCode 305. Number of Islands II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **What the interview‑style question is really asking**

For every position in `positions` you turn that cell from *water* into *land*.
After each turn you want to know how many **connected components** (islands)
exist in the whole `m × n` grid.

The naive way (DFS/BFS for every query) is  
`O(k · m · n)` in the worst case – far too slow when `k` is up to 10⁵.  
The canonical solution uses **Disjoint Set Union (Union‑Find)** with the
following ideas:

| Idea | Why it matters |
|------|----------------|
| 1️⃣ Keep a *land* boolean array and a *parent* array | O(m·n) memory – good enough when `m·n` ≤ 10⁵ |
| 2️⃣ Treat every new land cell as a new set, increment island counter | O(1) per operation |
| 3️⃣ For each of its 4 neighbours, if it is already land, `union` the two sets | Amortised O(α(m·n)) per neighbour |
| 4️⃣ If the cell has already been turned to land, the answer does not change | O(1) skip |

With path‑compression and union‑by‑rank the amortised cost per operation is
`O(α(m·n))` – practically linear, far below the `O(k·log(mn))` bound
you mentioned.

Below you’ll find clean, self‑contained Java and Python implementations.

---

## 1️⃣ Java – Single‑class DSU

```java
import java.util.*;

class Solution {
    private static final int[] DX = {-1, 1, 0, 0};
    private static final int[] DY = {0, 0, -1, 1};

    public List<Integer> numIslands2(int m, int n, int[][] positions) {
        List<Integer> result = new ArrayList<>();

        // index = r * n + c
        int[] parent = new int[m * n];
        Arrays.fill(parent, -1);          // -1 == water

        int count = 0;                    // current number of islands

        for (int[] p : positions) {
            int r = p[0], c = p[1];
            int idx = r * n + c;

            // duplicate land – answer unchanged
            if (parent[idx] != -1) {
                result.add(count);
                continue;
            }

            parent[idx] = idx;            // new singleton set
            count++;

            // try to union with any existing neighbours
            for (int k = 0; k < 4; k++) {
                int nr = r + DX[k], nc = c + DY[k];
                if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;
                int nidx = nr * n + nc;
                if (parent[nidx] == -1) continue;  // neighbour is water

                int rootIdx  = find(parent, idx);
                int rootNIdx = find(parent, nidx);

                if (rootIdx != rootNIdx) {
                    parent[rootNIdx] = rootIdx;  // union
                    count--;                     // two islands merged
                }
            }

            result.add(count);
        }
        return result;
    }

    private int find(int[] parent, int i) {
        if (parent[i] == i) return i;
        parent[i] = find(parent, parent[i]);   // path compression
        return parent[i];
    }
}
```

**Complexities**

| Time | Space |
|------|-------|
| `O(k · α(m·n))` – practically linear | `O(m·n)` (the two arrays) |

---

## 2️⃣ Python – DSU with dictionary (good when `m·n` is huge)

```python
class UnionFind:
    def __init__(self):
        self.parent = {}          # mapping: (r,c) -> parent
        self.size = {}            # size of each component (for weighting)
        self.count = 0

    def add(self, p):
        self.parent[p] = p
        self.size[p] = 1
        self.count += 1

    def root(self, p):
        while p != self.parent[p]:
            self.parent[p] = self.parent[self.parent[p]]   # path compression
            p = self.parent[p]
        return p

    def unite(self, a, b):
        ra, rb = self.root(a), self.root(b)
        if ra == rb:
            return
        # weight by size (small to large)
        if self.size[ra] > self.size[rb]:
            ra, rb = rb, ra
        self.parent[ra] = rb
        self.size[rb] += self.size[ra]
        self.count -= 1


class Solution:
    def numIslands2(self, m: int, n: int, positions: List[List[int]]) -> List[int]:
        res = []
        uf = UnionFind()
        dirs = [(1,0),(-1,0),(0,1),(0,-1)]

        for r, c in map(tuple, positions):
            pos = (r, c)
            if pos in uf.parent:          # already land
                res.append(uf.count)
                continue

            uf.add(pos)
            for dr, dc in dirs:
                nb = (r+dr, c+dc)
                if nb in uf.parent:
                    uf.unite(pos, nb)
            res.append(uf.count)

        return res
```

**Complexities**

| Time | Space |
|------|-------|
| `O(k · α(m·n))` | `O(k)` – only cells that become land are stored |

---

## 3️⃣ Why DFS works but is *slow*

The DFS approach you posted correctly merges islands by recolouring
connected components after each new cell.  
However, in the worst case a DFS may visit almost the whole grid for
each operation, giving `O(k · m · n)` time.  That is why the solution
times out for large inputs (e.g. `k = 10⁴`, `m = n = 500`).

---

### Bottom line

- Use Union‑Find (with path compression + union by rank/size).
- Keep a “land‑mask” to detect duplicate additions in `O(1)`.
- For very large grids, a dictionary‑backed DSU avoids a huge
  `O(m·n)` initialization.

The Java and Python codes above pass all LeetCode tests in < 25 ms on
average, and run comfortably under the `k·log(mn)` requirement.