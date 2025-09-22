---
title: LeetCode 2664. The KnightÕs Tour - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview**

The task is to produce *any* sequence of knight moves that visits every cell of an `m × n` board exactly once (a *knight’s tour*).  
The board is represented by a 2‑D array:

```
-1   –  cell not visited yet
 0   –  start cell (given by r, c)
 1   –  cell visited as the 2nd step
 2   –  cell visited as the 3rd step
...
```

If no tour exists we simply leave the remaining cells as `-1` – the algorithm
will return whatever it managed to build.

--------------------------------------------------------------------

### 1.  Key idea – depth first search with backtracking

The knight has at most 8 possible moves from any position.  
A naïve depth‑first search (DFS) exploring all possibilities
has time complexity `O(8^(m*n))`, but on an 8×8 board it is still fast
because the search tree collapses early – most branches are pruned by the
`-1`‑check.

To make the search practical we use **Warnsdorff’s rule**:
when we are at a cell we first try the neighbour that has the fewest
unvisited onward moves.  
This heuristic greatly reduces the branching factor and is guaranteed to
find a tour on all boards that admit one.

--------------------------------------------------------------------

### 2.  Algorithm

```
directions = [(-2,-1), (-2, 1), (-1,-2), (-1, 2),
              ( 1,-2), ( 1, 2), ( 2,-1), ( 2, 1)]

function tourOfKnight(m, n, r, c):
    board = m×n array filled with -1
    board[r][c] = 0                     # start
    dfs(r, c, 0, board, m*n-1)
    return board

function dfs(x, y, step, board, lastStep):
    board[y][x] = step
    if step == lastStep:                # all cells visited
        return True

    # generate possible next moves that are inside the board
    moves = []
    for (dx, dy) in directions:
        nx, ny = x + dx, y + dy
        if 0 <= nx < n and 0 <= ny < m and board[ny][nx] == -1:
            moves.append((nx, ny))

    # Warnsdorff: count further moves for each candidate
    moves.sort(key=lambda (nx,ny): count_next_moves(nx,ny,board))

    for nx, ny in moves:
        if dfs(nx, ny, step+1, board, lastStep):
            return True

    board[y][x] = -1                    # backtrack
    return False

function count_next_moves(x, y, board):
    cnt = 0
    for (dx,dy) in directions:
        nx, ny = x+dx, y+dy
        if 0 <= nx < n and 0 <= ny < m and board[ny][nx] == -1:
            cnt += 1
    return cnt
```

--------------------------------------------------------------------

### 3.  Correctness Proof  

We prove that the algorithm returns a board that contains a valid knight’s tour
if one exists, and otherwise returns the partial tour found by DFS.

#### Lemma 1  
During the execution of `dfs`, the cell `(x,y)` is marked with a value
`step` *iff* the current recursive path has visited exactly `step+1` distinct
cells (including `(x,y)`).

**Proof.**

*Initialization.*  
Before the first call `dfs(r,c,0,…)` we set `board[r][c]=0`.  
Thus the statement holds for the first step.

*Induction.*  
Assume the lemma holds when entering `dfs(x,y,step,…)`.  
The function writes `board[y][x] = step` – now the path contains
`step+1` cells.  
If the recursive call fails, the algorithm sets `board[y][x] = -1`,
removing that cell from the current path – again preserving the lemma
for the caller. ∎



#### Lemma 2  
When `dfs` returns `True`, the board contains a *Hamiltonian path*
(i.e., a sequence of knight moves that visits every cell exactly once).

**Proof.**

`dfs` returns `True` only in two places:

1. **Base case** – `step == lastStep` (i.e. `step == m*n-1`).  
   Then all `m*n` cells have been visited (Lemma&nbsp;1).  
   Because a move is performed only to an unvisited cell,
   the sequence of assignments to the board is exactly the tour.

2. **Recursive success** – a recursive call returns `True`.  
   By induction hypothesis the recursive call left a complete tour in the
   board. The caller only added its own cell `(x,y)` at the beginning of
   this call, so the resulting board still contains a complete tour. ∎



#### Lemma 3  
If a complete tour exists for the given board and start position,
`dfs` will eventually find one.

**Proof.**

The DFS explores *all* possible sequences of knight moves
starting from `(r,c)` that visit distinct cells.  
Because the search is exhaustive, it must eventually reach
a node that visits all `m*n` cells – i.e. a complete tour.  
At that node the function returns `True` and propagates this success
upwards, causing the outermost call to terminate with a tour. ∎



#### Theorem  
`tourOfKnight` returns a board that

* contains a valid knight’s tour if one exists,
* otherwise contains the partial tour found by the search (all other cells are `-1`).

**Proof.**

If a tour exists, Lemma&nbsp;3 guarantees that `dfs` returns `True`,
and by Lemma&nbsp;2 the board holds a complete tour.  
If no tour exists, every possible sequence of moves will eventually
backtrack; the algorithm will terminate with `dfs` returning `False`
and the board will have all cells left as `-1` except those visited
before backtracking – exactly what the problem statement asks for. ∎



--------------------------------------------------------------------

### 4.  Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| `dfs` call | `O(8)` *average* (each cell has ≤8 neighbours) | `O(1)` |
| Full recursion | `O(8^(m*n))` worst‑case, practically far less due to pruning | `O(m*n)` for the board (plus recursion depth `O(m*n)`) |

For the maximal board (8×8) this easily fits into time limits for
the problem’s constraints.

--------------------------------------------------------------------

### 5.  Reference Implementation (Python 3)

```python
from typing import List

class Solution:
    # knight moves
    DIRS = [(-2, -1), (-2, 1), (-1, -2), (-1, 2),
            (1, -2),  (1, 2),  (2, -1),  (2, 1)]

    def tourOfKnight(self, m: int, n: int, r: int, c: int) -> List[List[int]]:
        # board initialisation
        board = [[-1] * n for _ in range(m)]
        board[r][c] = 0                # start step

        self.dfs(r, c, 0, m * n - 1, board)
        return board

    # depth first search + Warnsdorff
    def dfs(self, x: int, y: int, step: int,
            last_step: int, board: List[List[int]]) -> bool:
        if step == last_step:
            # all cells visited
            return True

        # generate legal neighbours
        moves = []
        for dx, dy in self.DIRS:
            nx, ny = x + dx, y + dy
            if 0 <= nx < len(board[0]) and 0 <= ny < len(board) \
               and board[ny][nx] == -1:
                moves.append((nx, ny))

        # Warnsdorff: sort by number of onward moves
        moves.sort(key=lambda pos: self.count_onward_moves(*pos, board))

        for nx, ny in moves:
            board[ny][nx] = step + 1
            if self.dfs(nx, ny, step + 1, last_step, board):
                return True
            board[ny][nx] = -1          # backtrack

        return False

    def count_onward_moves(self, x: int, y: int,
                           board: List[List[int]]) -> int:
        cnt = 0
        for dx, dy in self.DIRS:
            nx, ny = x + dx, y + dy
            if 0 <= nx < len(board[0]) and 0 <= ny < len(board) \
               and board[ny][nx] == -1:
                cnt += 1
        return cnt
```

The same idea can be translated into any language (Java, C++, etc.)
by keeping the DFS + Warnsdorff logic identical.

--------------------------------------------------------------------

**Happy coding!**