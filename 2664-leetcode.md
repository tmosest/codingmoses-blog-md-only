---
title: LeetCode 2664. The KnightÕs Tour - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## The Knight’s Tour on LeetCode #2664  
**Good, Bad, and Ugly – A Job‑Seeking Guide**

---

### TL;DR

| Language | Complexity | Notes |
|----------|------------|-------|
| **Java** | O(m·n) backtracking (worst‑case ≈ 120 000 paths) | Uses 0‑based board, recursion depth ≤ 25 |
| **Python** | Same as Java | Uses tuples for positions, `itertools` not needed |
| **C++** | Same | Uses `vector<vector<int>>` and a global `moves` array |

The board is at most 5 × 5, so an exhaustive depth‑first search (DFS) is fast enough.  
The key is to store the visitation order in `board[r][c]` and backtrack when a dead‑end is hit.  

---

## 1. Problem Recap

You’re given a board of size `m × n` (1 ≤ m, n ≤ 5) and a starting position `(r, c)`.  
A knight moves like in chess: `(dr, dc)` is a pair where one component is 2 and the other is 1.  
Your task is to return a matrix where each cell contains the step number at which the knight visited that square, starting at `0` for the initial cell.  
It is guaranteed that a full tour exists for the supplied test cases.

---

## 2. Why Backtracking Works Here

- **State Space:** `m · n` cells, each visited exactly once → at most 25! possible paths (tiny for m,n ≤ 5).  
- **Feasibility:** The problem guarantees a solution, so we can stop when we find the first tour.  
- **Determinism:** The output is deterministic once we decide on the order in which we try moves.

Because the board is small, we can ignore more advanced heuristics (Warnsdorff’s rule) and still finish well under 1 ms.  
However, adding a simple move ordering (Warnsdorff) speeds up the search and demonstrates good coding interview practices.

---

## 3. Algorithm Overview

1. **Define** the 8 knight moves.
2. **Create** a 2‑D array `board` initialized with `-1` to mark unvisited cells.
3. **DFS(position, step):**  
   - Mark the current cell with `step`.  
   - If `step == m*n - 1`, we finished → return `true`.  
   - Generate candidate next cells.  
   - *Optional:* sort candidates by the number of onward moves (Warnsdorff).  
   - Recurse on each candidate.  
   - If recursion fails, backtrack: set `board[next]` back to `-1`.  
4. **Call** DFS starting from `(r, c, 0)`.

---

## 4. Complexity Analysis

- **Time:**  
  In the worst case we visit every node of the recursion tree:  
  \(O(8^{m·n})\).  
  For m,n ≤ 5 this is trivial (≤ 8^25 ≈ 3·10^22, but the search prunes heavily).  
  Practically < 1 ms on modern hardware.

- **Space:**  
  - `board`: O(m·n).  
  - Recursion stack: O(m·n).  
  - No extra large data structures.

---

## 5. Edge Cases

| Case | Why it matters | Handling |
|------|----------------|----------|
| 1 × 1 board | No moves possible | DFS immediately returns success |
| Unreachable squares (odd boards) | Problem guarantees solvable input | No need for checks |
| Starting position on corner | Moves reduce | Works out of the box |

---

## 6. Code Implementations

Below are clean, interview‑ready solutions in **Java, Python, and C++**.  
All three use the same backtracking core.

### 6.1 Java

```java
import java.util.*;

public class Solution {
    private static final int[] DR = {2, 1, -1, -2, -2, -1, 1, 2};
    private static final int[] DC = {1, 2, 2, 1, -1, -2, -2, -1};

    public int[][] tourOfKnight(int m, int n, int r, int c) {
        int[][] board = new int[m][n];
        for (int[] row : board) Arrays.fill(row, -1);
        dfs(board, r, c, 0);
        return board;
    }

    private boolean dfs(int[][] board, int r, int c, int step) {
        board[r][c] = step;
        if (step == board.length * board[0].length - 1) return true;

        List<int[]> moves = new ArrayList<>();
        for (int k = 0; k < 8; k++) {
            int nr = r + DR[k], nc = c + DC[k];
            if (nr >= 0 && nr < board.length &&
                nc >= 0 && nc < board[0].length &&
                board[nr][nc] == -1) {
                moves.add(new int[]{nr, nc});
            }
        }

        // Optional Warnsdorff ordering
        moves.sort(Comparator.comparingInt(a -> onwardCount(board, a[0], a[1])));

        for (int[] mv : moves) {
            if (dfs(board, mv[0], mv[1], step + 1)) return true;
        }

        board[r][c] = -1; // backtrack
        return false;
    }

    private int onwardCount(int[][] board, int r, int c) {
        int cnt = 0;
        for (int k = 0; k < 8; k++) {
            int nr = r + DR[k], nc = c + DC[k];
            if (nr >= 0 && nr < board.length &&
                nc >= 0 && nc < board[0].length &&
                board[nr][nc] == -1) cnt++;
        }
        return cnt;
    }
}
```

### 6.2 Python

```python
class Solution:
    moves = [(2, 1), (1, 2), (-1, 2), (-2, 1),
             (-2, -1), (-1, -2), (1, -2), (2, -1)]

    def tourOfKnight(self, m: int, n: int, r: int, c: int) -> list[list[int]]:
        board = [[-1] * n for _ in range(m)]
        self.dfs(board, r, c, 0)
        return board

    def dfs(self, board, r, c, step):
        board[r][c] = step
        if step == len(board) * len(board[0]) - 1:
            return True

        candidates = []
        for dr, dc in self.moves:
            nr, nc = r + dr, c + dc
            if 0 <= nr < len(board) and 0 <= nc < len(board[0]) and board[nr][nc] == -1:
                candidates.append((nr, nc))

        # Warnsdorff: sort by the number of onward moves
        candidates.sort(key=lambda pos: self.onward(board, *pos))

        for nr, nc in candidates:
            if self.dfs(board, nr, nc, step + 1):
                return True

        board[r][c] = -1  # backtrack
        return False

    def onward(self, board, r, c):
        count = 0
        for dr, dc in self.moves:
            nr, nc = r + dr, c + dc
            if 0 <= nr < len(board) and 0 <= nc < len(board[0]) and board[nr][nc] == -1:
                count += 1
        return count
```

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    const int dr[8] = {2,1,-1,-2,-2,-1,1,2};
    const int dc[8] = {1,2,2,1,-1,-2,-2,-1};

    vector<vector<int>> tourOfKnight(int m, int n, int r, int c) {
        vector<vector<int>> board(m, vector<int>(n, -1));
        dfs(board, r, c, 0);
        return board;
    }

private:
    bool dfs(vector<vector<int>>& board, int r, int c, int step) {
        board[r][c] = step;
        if (step == board.size() * board[0].size() - 1) return true;

        vector<pair<int,int>> moves;
        for (int k=0;k<8;k++){
            int nr=r+dr[k], nc=c+dc[k];
            if (nr>=0 && nr<(int)board.size() &&
                nc>=0 && nc<(int)board[0].size() &&
                board[nr][nc]==-1)
                moves.push_back({nr,nc});
        }

        // Warnsdorff heuristic
        sort(moves.begin(), moves.end(),
            [&](const pair<int,int>& a, const pair<int,int>& b){
                return onward(board,a.first,a.second) <
                       onward(board,b.first,b.second);
            });

        for (auto [nr,nc]: moves) {
            if (dfs(board,nr,nc,step+1)) return true;
        }
        board[r][c] = -1; // backtrack
        return false;
    }

    int onward(const vector<vector<int>>& board, int r, int c){
        int cnt=0;
        for (int k=0;k<8;k++){
            int nr=r+dr[k], nc=c+dc[k];
            if (nr>=0 && nr<(int)board.size() &&
                nc>=0 && nc<(int)board[0].size() &&
                board[nr][nc]==-1) cnt++;
        }
        return cnt;
    }
};
```

---

## 7. Blog‑Style Exposition: “The Knight’s Tour – Good, Bad, and Ugly”

### 7.1 Good – Why This Problem is a Gold‑Mine for Interviewers

1. **Shows Mastery of DFS & Backtracking**  
   The Knight’s Tour is a classic DFS problem that checks whether the candidate can handle recursion depth, pruning, and state restoration.  
2. **Encourages Algorithmic Thinking**  
   Candidates need to reason about a combinatorial search space, decide on move ordering, and manage visited states.  
3. **Time & Space Constraints Fit LeetCode’s “Medium” Category**  
   With `m, n ≤ 5`, brute‑force is acceptable, but it’s still a subtle puzzle—good for distinguishing fast thinkers.

### 7.2 Bad – Common Pitfalls Candidates Hit

| Pitfall | Why It’s Bad | How to Fix |
|---------|--------------|------------|
| **Storing visited in a 1‑D array** | Hard to map coordinates → off‑by‑one errors | Use a 2‑D matrix or bitmask with `(r * n + c)` |
| **No backtracking** | Leaves the board in a half‑filled state → wrong answer | After recursion, reset cell to `-1` |
| **Missing base case** | Recursion never ends → stack overflow | Check `step == m*n - 1` |
| **Using global mutable state without reset** | Subsequent calls reuse the same board | Re‑initialize board in each public method |

### 7.3 Ugly – Why It Can Spiral Out of Control

1. **Unordered Move Generation**  
   Without Warnsdorff or any pruning, the search explores many dead ends, especially on 5 × 5 boards.  
2. **Excessive Recursion Depth in Other Languages**  
   In languages like Python, recursion depth defaults to 1000, but for a 25‑cell board it’s fine; still, always set a guard if you plan to extend.  
3. **Ignoring the Guarantee of a Solution**  
   Some candidates write generic Hamiltonian‑Path solvers that handle any board size—overkill for this problem.

### 7.4 Tips for Nail‑Down Your Solution

- **Always initialize the board to `-1`** (unvisited).  
- **Use the provided 8 moves** – no magic numbers.  
- **Apply Warnsdorff** (optional) – sort moves by onward‑move count to reduce branching.  
- **Test edge cases**: 1 × 1, starting in the middle, starting on a corner.  

---

## 8. Final Words for the Recruiter

If you’re hiring a software engineer, presenting the Knight’s Tour in an interview session is a strong signal of a candidate’s **algorithmic maturity**.  
The solutions above demonstrate the clean, production‑grade approach most recruiters appreciate.  
And for job seekers: a solid implementation here earns you a “yes” and a quick confidence boost for your next coding round.

---

### 9. References & Further Reading

- [LeetCode 1231. Knights Tour (hard)](https://leetcode.com/problems/knights-tour/)
- [Warnsdorff's Rule – Wikipedia](https://en.wikipedia.org/wiki/Warnsdorff%27s_rule)
- [Backtracking Primer – Cracking the Coding Interview](https://www.crackingthecodinginterview.com/)

---

*This post blends algorithmic rigor with interview strategy insights—perfect for both candidates sharpening their skills and recruiters refining their evaluation criteria.*