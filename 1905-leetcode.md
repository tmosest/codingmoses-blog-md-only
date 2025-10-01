---
title: LeetCode 1905. Count Sub Islands - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every cell `(i , j)` the value `1` means *land* and `0` means *water*.

```
grid1[i][j] = 1  – land in the first map
grid2[i][j] = 1  – land in the second map
```

An island is a maximal 4‑connected component of `1`s.
An island of `grid2` is a **sub‑island** if *every* cell of that island
is also land in `grid1`.

The task is to output the number of sub‑islands that exist in `grid2`.



--------------------------------------------------------------------

#### 1.  Observation

If we start a depth first search (DFS) from a land cell of `grid2`,
all cells that belong to the same island of `grid2` are visited during this DFS.
While exploring, we can check the corresponding cell in `grid1`:

* If at any moment `grid1[i][j] == 0` while `grid2[i][j] == 1`,
  the whole island cannot be a sub‑island – it contains a cell that lies
  on water in `grid1`.

After finishing the DFS for one island we already know whether the island
is a sub‑island or not.  
Finally we simply count the islands for which the answer is *yes*.



--------------------------------------------------------------------

#### 2.  Algorithm
```
input m, n
read grid1[0 … m-1][0 … n-1]
read grid2[0 … m-1][0 … n-1]

sub_island_cnt = 0
for every cell (i, j) in grid2:
    if grid2[i][j] == 1:                     # a new island starts here
        is_sub_island = True
        run DFS(grid1, grid2, i, j)          # mark whole island, check validity
        if is_sub_island:
            sub_island_cnt += 1

print(sub_island_cnt)
```

`DFS(grid1, grid2, i, j)`  

```
if i, j outside the grid or grid2[i][j] == 0: return
if grid1[i][j] == 0:          # land of grid2 but water of grid1
        is_sub_island = False
grid2[i][j] = 0               # mark visited
DFS( i+1, j )                 # down
DFS( i-1, j )                 # up
DFS( i, j+1 )                 # right
DFS( i, j-1 )                 # left
```

`is_sub_island` is a global (or closure) variable that is shared
between the DFS calls of the current island.



--------------------------------------------------------------------

#### 3.  Correctness Proof  

We prove that the algorithm outputs the exact number of sub‑islands.

---

##### Lemma 1  
During the DFS started at a land cell of `grid2`, every cell of the
same island is visited exactly once.

**Proof.**

The DFS moves only to 4‑neighbouring cells.
Because the start cell is part of the island, every other cell of the
same island is reachable by a chain of such moves.
DFS visits a cell only when it is land (`grid2[i][j]==1`);
once visited it is turned to `0` and will never be processed again.
Hence every cell of the island is visited exactly once. ∎



##### Lemma 2  
`is_sub_island` is set to *False* **iff** the processed island contains
at least one cell that lies over water in `grid1`.

**Proof.**

*If* part:  
Whenever the DFS visits a cell `(x,y)` with `grid1[x][y]==0`,
the algorithm sets `is_sub_island = False`.  
Thus, if the island contains such a cell, the flag becomes *False*.

*Only if* part:  
Assume the flag becomes *False*.  
It can only happen in the statement `if grid1[x][y] != grid2[x][y]`.
Because the DFS never visits water cells of `grid2`, this condition
holds exactly when `grid2[x][y]==1` and `grid1[x][y]==0`.  
Therefore a cell of the island lies over water in `grid1`. ∎



##### Lemma 3  
An island of `grid2` is counted by the algorithm **iff** it is a
sub‑island of `grid1`.

**Proof.**

*If part:*  
Consider a counted island.  
Its DFS finishes with `is_sub_island = True`.  
By Lemma&nbsp;2 the island contains **no** cell over water in `grid1`;
hence all its cells are land in `grid1`.  
Therefore the island is a sub‑island.

*Only if part:*  
Let an island be a sub‑island.  
All its cells satisfy `grid1[i][j]==1`.  
During its DFS no assignment to `False` can happen, thus
`is_sub_island` stays *True* and the algorithm counts the island.

∎



##### Theorem  
`sub_island_cnt` printed by the algorithm equals the number of
sub‑islands of `grid2` with respect to `grid1`.

**Proof.**

By Lemma&nbsp;1 the algorithm examines every island of `grid2`
exactly once.
By Lemma&nbsp;3 each examined island is counted exactly when it is a
sub‑island.
Therefore the final counter equals the required number of sub‑islands. ∎



--------------------------------------------------------------------

#### 4.  Complexity Analysis

Let `m` be the number of rows and `n` the number of columns
(`1 ≤ m,n ≤ 500` in the official constraints).

*Every cell is processed at most once.*

```
Time   :  Θ(m · n)
Memory :  Θ(m · n)   – the two input grids
```

The recursion depth of DFS never exceeds the size of the current island,
so with `sys.setrecursionlimit(10**6)` it is safe for all test data.



--------------------------------------------------------------------

#### 5.  Reference Implementation  (Python 3)

```python
import sys
sys.setrecursionlimit(1 << 25)          # allow deep recursions


def solve() -> None:
    data = list(map(int, sys.stdin.read().split()))
    if not data:
        return

    it = iter(data)
    m = next(it)
    n = next(it)

    # read grid1
    grid1 = [[next(it) for _ in range(n)] for _ in range(m)]
    # read grid2
    grid2 = [[next(it) for _ in range(n)] for _ in range(m)]

    sub_island_cnt = 0

    # shared flag for the current island
    is_sub_island = True

    # four neighbour directions
    dirs = ((1, 0), (-1, 0), (0, 1), (0, -1))

    def dfs(x: int, y: int) -> None:
        nonlocal is_sub_island
        if x < 0 or x >= m or y < 0 or y >= n or grid2[x][y] == 0:
            return
        if grid1[x][y] == 0:          # land of grid2 but water of grid1
            is_sub_island = False
        grid2[x][y] = 0               # mark visited
        for dx, dy in dirs:
            dfs(x + dx, y + dy)

    for i in range(m):
        for j in range(n):
            if grid2[i][j] == 1:
                is_sub_island = True
                dfs(i, j)
                if is_sub_island:
                    sub_island_cnt += 1

    print(sub_island_cnt)


if __name__ == "__main__":
    solve()
```

The program follows exactly the algorithm proven correct above and
conforms to the required `solve()` signature.