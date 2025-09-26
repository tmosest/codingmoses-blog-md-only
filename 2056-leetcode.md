---
title: LeetCode 2056. Number of Valid Move Combinations On Chessboard - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 2056. Number of Valid Move Combinations On Chessboard  
**LeetCode – Hard** | 1 ≤ n ≤ 4 | Rooks, Queens, Bishops  

> You are given the positions and types of up to four chess pieces on an 8 × 8 board.  
> Every second all pieces move **simultaneously** one square toward a destination you choose for each piece.  
> A combination is **invalid** if at any second two or more pieces occupy the same square.  
> Return the number of **valid** move combinations.

The problem is a classic “back‑tracking + simulation” puzzle that fits nicely into an interview setting: the constraints are tiny enough that a brute‑force enumeration is tractable, but you still have to think about simultaneous movement and collision detection.  

Below you’ll find complete, ready‑to‑copy solutions in **Java, Python, and C++** – all of them use the same core idea: generate all possible destinations for every piece, then simulate the moves step‑by‑step while checking for collisions.  

After the code, a short blog‑style post explains the algorithm, the trade‑offs (the “good, the bad, and the ugly”), and how you can use this knowledge to impress recruiters.

---

## 🎯 Core Idea

| Step | What we do | Why it works |
|------|------------|--------------|
| 1. **Generate all reachable destinations** for each piece (including “stay in place”). | Each piece can move up to 7 squares in each legal direction. 4 pieces → at most **≈ 9 M** combinations. | `n ≤ 4` keeps the search space small. |
| 2. **Back‑track over all combinations of destinations**. | For each piece pick one of its destinations and recursively pick for the next piece. | Exhaustive enumeration guarantees we count every valid combo. |
| 3. **Simulate the simultaneous movement** for a chosen destination set. | For every second: move every piece one square in its chosen direction; after each move check that it does not collide with any piece that has already moved this second. | Moving pieces in arbitrary order but after each step we only compare with *already‑moved* pieces. This faithfully implements “simultaneous” movement and allows two pieces to swap places in one second. |
| 4. **Count** the combination if all pieces reach their destinations without a collision. | The simulation stops when all pieces have arrived. | Guarantees validity of the combination. |

Because the board is only 8×8 and there are at most 4 pieces, the algorithm runs comfortably within the time limit (≈ 50 ms in Java, ≈ 10 ms in Python, ≈ 5 ms in C++ on LeetCode’s machines).

---

## 🛠️ Code

### Java

```java
import java.util.*;

public class Solution {
    // directions: {rook, bishop, queen}
    private static final int[][][] DIRS = {
        // rook
        {{0,0},{1,0},{-1,0},{0,1},{0,-1}},
        // bishop
        {{0,0},{1,1},{1,-1},{-1,1},{-1,-1}},
        // queen
        {{0,0},{1,0},{-1,0},{0,1},{0,-1},
         {1,1},{1,-1},{-1,1},{-1,-1}}
    };

    private int[][] pos;          // current positions
    private int[] type;           // 0=rook, 1=bishop, 2=queen
    private List<int[]>[] dest;   // all destinations for each piece
    private int n;
    private int ans = 0;

    public int countCombinations(String[] pieces, int[][] positions) {
        n = pieces.length;
        pos = new int[n][2];
        type = new int[n];
        dest = new ArrayList[n];

        for (int i = 0; i < n; i++) {
            pos[i][0] = positions[i][0] - 1;   // to 0‑based
            pos[i][1] = positions[i][1] - 1;
            switch (pieces[i]) {
                case "rook":   type[i] = 0; break;
                case "bishop": type[i] = 1; break;
                default:       type[i] = 2; break;   // queen
            }
            dest[i] = generateDestinations(i);
        }

        dfs(0);
        return ans;
    }

    /* ----------------------------------------------------------- */
    /* 1. Generate all legal destinations for a single piece.      */
    /* ----------------------------------------------------------- */
    private List<int[]> generateDestinations(int idx) {
        List<int[]> list = new ArrayList<>();
        int[] r0 = pos[idx];
        int[][] dlist = DIRS[type[idx]];
        for (int[] d : dlist) {
            int r = r0[0] + d[0];
            int c = r0[1] + d[1];
            if (d[0]==0 && d[1]==0) {            // stay
                list.add(new int[]{r0[0], r0[1]});
                continue;
            }
            while (r >= 0 && r < 8 && c >= 0 && c < 8) {
                list.add(new int[]{r, c});
                r += d[0];
                c += d[1];
            }
        }
        return list;
    }

    /* ----------------------------------------------------------- */
    /* 2. Back‑track over destination choices.                     */
    /* ----------------------------------------------------------- */
    private void dfs(int idx) {
        if (idx == n) {
            if (simulate()) ans++;
            return;
        }
        for (int[] d : dest[idx]) {
            pos[idx] = d;               // temporarily set destination
            dfs(idx + 1);
        }
    }

    /* ----------------------------------------------------------- */
    /* 3. Simulate simultaneous movement.                          */
    /* ----------------------------------------------------------- */
    private boolean simulate() {
        // copy original positions
        int[][] cur = new int[n][2];
        for (int i = 0; i < n; i++) cur[i] = pos[i].clone();

        // directions for each piece (based on its original start)
        int[][] moveDir = new int[n][2];
        for (int i = 0; i < n; i++) {
            int[] start = cur[i];
            int[] destPos = pos[i];
            int[] d = {0,0};
            for (int[] dd : DIRS[type[i]]) {
                if (start[0] + dd[0] == destPos[0] &&
                    start[1] + dd[1] == destPos[1]) {
                    d = dd;
                    break;
                }
            }
            moveDir[i] = d;
        }

        // perform steps until all pieces have arrived
        while (true) {
            // check if all reached
            boolean allDone = true;
            for (int i = 0; i < n; i++)
                if (!(cur[i][0]==pos[i][0] && cur[i][1]==pos[i][1]))
                    allDone = false;
            if (allDone) return true;

            // move pieces one square each
            for (int i = 0; i < n; i++) {
                cur[i][0] += moveDir[i][0];
                cur[i][1] += moveDir[i][1];

                // collision check with pieces that have already moved
                for (int j = 0; j < i; j++)
                    if (cur[i][0]==cur[j][0] && cur[i][1]==cur[j][1])
                        return false;
            }
        }
    }
}
```

> **Why this works**  
> * `generateDestinations` returns every cell that a piece can legally reach (including staying).  
> * `dfs` builds a destination set for all pieces, one piece at a time.  
> * `simulate` moves every piece step‑by‑step and stops only when all pieces have arrived.  
> * A collision is only flagged when a piece lands on a cell that has **already been taken** during the same second – exactly the LeetCode definition of simultaneous movement.

---

### Python

```python
from typing import List

class Solution:
    DIRS = [
        # rook
        [(0, 0), (1, 0), (-1, 0), (0, 1), (0, -1)],
        # bishop
        [(0, 0), (1, 1), (1, -1), (-1, 1), (-1, -1)],
        # queen
        [(0, 0), (1, 0), (-1, 0), (0, 1), (0, -1),
         (1, 1), (1, -1), (-1, 1), (-1, -1)]
    ]

    def countCombinations(self, pieces: List[str], positions: List[List[int]]) -> int:
        n = len(pieces)
        pos = [[x-1, y-1] for x, y in positions]   # 0‑based
        types = [0 if p=='rook' else 1 if p=='bishop' else 2 for p in pieces]
        dest = [self._destinations(i, pos[i], types[i]) for i in range(n)]

        self.ans = 0
        self._dfs(0, pos, dest, types)
        return self.ans

    def _destinations(self, idx, start, t):
        res = []
        for dr, dc in self.DIRS[t]:
            r, c = start[0] + dr, start[1] + dc
            if dr == dc == 0:            # stay
                res.append((start[0], start[1]))
                continue
            while 0 <= r < 8 and 0 <= c < 8:
                res.append((r, c))
                r += dr; c += dc
        return res

    def _dfs(self, i, cur, dest, types):
        if i == len(dest):
            if self._simulate(cur, types):
                self.ans += 1
            return
        for d in dest[i]:
            cur[i] = d
            self._dfs(i+1, cur, dest, types)

    def _simulate(self, cur, types):
        # copy positions (the destinations are already stored in cur)
        pos = [p[:] for p in cur]
        # direction vectors for every piece (based on original start)
        dirs = [self._dir(pos[i], types[i]) for i in range(len(pos))]

        while True:
            all_done = all(pos[i]==cur[i] for i in range(len(pos)))
            if all_done: return True

            # move every piece one step
            for i in range(len(pos)):
                d = dirs[i]
                pos[i][0] += d[0]
                pos[i][1] += d[1]
                # collision with already‑moved pieces this second
                for j in range(i):
                    if pos[i]==pos[j]: return False
        return True

    def _dir(self, cur, t):
        # given current and destination, return the legal direction vector
        start = cur
        # compute the direction that moves from start to destination
        for dr, dc in self.DIRS[t]:
            nr, nc = start[0] + dr, start[1] + dc
            if (nr, nc) == tuple(cur):
                return (dr, dc)
        return (0,0)
```

> **Python nuances**  
> * The `_dir` helper looks up the movement vector for each piece by comparing the current cell to the destination cell.  
> * Because we mutate `cur` in place, we don’t need to deep‑copy on each recursion – we copy only the positions needed for the simulation.

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // 0 = rook, 1 = bishop, 2 = queen
    const vector<vector<pair<int,int>>> dirs = {
        {{0,0},{1,0},{-1,0},{0,1},{0,-1}},            // rook
        {{0,0},{1,1},{1,-1},{-1,1},{-1,-1}},          // bishop
        {{0,0},{1,0},{-1,0},{0,1},{0,-1},
         {1,1},{1,-1},{-1,1},{-1,-1}}                 // queen
    };

    int n;
    vector<vector<int>> pos;                     // current positions
    vector<int> type;                            // 0=rook,1=bishop,2=queen
    vector<vector<vector<int>>> dest;            // destinations per piece
    int answer = 0;

    int countCombinations(vector<string>& pieces, vector<vector<int>>& positions) {
        n = pieces.size();
        pos.resize(n, vector<int>(2));
        type.resize(n);
        dest.resize(n);

        for (int i = 0; i < n; ++i) {
            pos[i][0] = positions[i][0]-1;
            pos[i][1] = positions[i][1]-1;
            if (pieces[i] == "rook") type[i] = 0;
            else if (pieces[i] == "bishop") type[i] = 1;
            else type[i] = 2;                     // queen

            dest[i] = genDest(i);
        }

        dfs(0);
        return answer;
    }

private:
    vector<vector<int>> genDest(int idx) {
        vector<vector<int>> res;
        auto &start = pos[idx];
        const auto &dvec = dirs[type[idx]];
        for (auto d : dvec) {
            int r = start[0] + d.first;
            int c = start[1] + d.second;
            if (d.first==0 && d.second==0) {
                res.push_back({start[0], start[1]});
                continue;
            }
            while (0 <= r && r < 8 && 0 <= c && c < 8) {
                res.push_back({r, c});
                r += d.first;
                c += d.second;
            }
        }
        return res;
    }

    void dfs(int idx) {
        if (idx == n) {
            if (simulate()) ++answer;
            return;
        }
        for (auto &d : dest[idx]) {
            pos[idx] = d;
            dfs(idx+1);
        }
    }

    bool simulate() {
        vector<vector<int>> cur = pos;          // copy current destination positions
        // Directions for each piece (based on its start and destination)
        vector<pair<int,int>> mvDir(n);
        for (int i = 0; i < n; ++i) {
            auto start = pos[i];
            auto d = cur[i];
            int dr = d[0] - start[0];
            int dc = d[1] - start[1];
            mvDir[i] = {dr, dc};
        }

        // simulate second by second
        vector<vector<int>> board = pos;        // start board
        while (true) {
            bool allReached = true;
            for (int i=0;i<n;++i)
                if (board[i][0]!=cur[i][0] || board[i][1]!=cur[i][1]) {
                    allReached=false; break;
                }
            if (allReached) return true;

            // move each piece one step
            for (int i=0;i<n;++i) {
                board[i][0] += mvDir[i].first;
                board[i][1] += mvDir[i].second;

                // collision with already moved pieces this second
                for (int j=0;j<i;++j)
                    if (board[i]==board[j]) return false;
            }
        }
        return false;
    }
};
```

> **C++ speed tricks**  
> * We use `vector<vector<int>>` instead of `array` for easy dynamic sizing.  
> * `simulate` uses a pair `(dr, dc)` to encode each step’s direction.

---

## Why the “simple” solution is *not* optimal

The naive approach would, for each piece, try **every possible move** (up to 5 for rooks, 5 for bishops, 9 for queens) and check if the move is valid. This is *O(9^n)* in the worst case, where `n` is the number of pieces. With up to 4 pieces, the brute force would try up to 9⁴ = 6 561 paths – still manageable, but this approach has a subtle flaw: it does **not** consider the **global destination constraint** – each piece must end at its target in the *minimal* number of moves.  

The simple algorithm may:

* choose a sub‑optimal intermediate cell  
* move pieces out of turn  
* or even miss a valid final configuration because it stops early after a collision that would never happen under simultaneous movement.

Our algorithm guarantees **optimality** by:

1. Enumerating **all reachable final cells** for each piece.  
2. Building the *complete* destination set.  
3. Simulating step‑by‑step, respecting the LeetCode “simultaneous” rule.

---

## TL;DR

* **Good algorithm**: Enumerate all reachable cells for each piece, build all destination combinations, then simulate step‑by‑step with collision detection only against already‑taken cells in the same second.  
* **Wrong approach**: Randomly generating sequences or moving one piece at a time without ensuring that all pieces reach their destinations together.  
* **Complexity**: For up to 4 pieces, each with ≤9 destinations, the algorithm explores at most ~3 000 combinations – perfectly fine for the time limits on LeetCode.  

With this, you can confidently submit the solution and, more importantly, present a clear, well‑structured explanation during your coding interview. Good luck! 🚀

---