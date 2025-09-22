---
title: LeetCode 1210. Minimum Moves to Reach Target with Rotations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 1210 – **Minimum Moves to Reach Target with Rotations**  
*The good, the bad, and the ugly – how to crack it in Java, Python & C++*

---

## 1. Problem Recap  

```
Input : int[][] grid          // 0 = empty, 1 = blocked
Output: minimum number of moves, or -1 if impossible
```

The “snake” is always two cells long:

| Orientation | Cells occupied |
|-------------|----------------|
| Horizontal  | (r, c) and (r, c+1) |
| Vertical    | (r, c) and (r+1, c) |

*   Start state: **Horizontal** at the top‑left corner → cells (0,0) and (0,1).  
*   Target state: **Horizontal** at the bottom‑right corner → cells (n‑1, n‑2) and (n‑1, n‑1).  
*   One move can be:

| Move | Condition | Result |
|------|-----------|--------|
| Right | Both cells can shift right | Same orientation |
| Down | Both cells can shift down | Same orientation |
| Clockwise | Horizontal & the two cells below it are empty | Becomes Vertical |
| Counter‑clockwise | Vertical & the two cells to its right are empty | Becomes Horizontal |

`n` is at most 100, so an exhaustive BFS over all states is feasible.



---

## 2. Why Breadth‑First Search (BFS)

* Each state (position + orientation) has a fixed cost of 1 to reach the next state.  
* BFS guarantees that the first time we pop the target state from the queue we have used the minimum number of moves.  
* The state space is bounded: `n × n × 2 ≤ 20 000` – trivial for modern hardware.



---

## 3. Core BFS Idea (in plain English)

1. **Represent a state** as `(row, col, orientation)` where `row, col` are the coordinates of the *head* cell (the top‑most or left‑most cell).  
2. Keep a 3‑D boolean array `visited[n][n][2]` to avoid revisiting states.  
3. Start with `(0, 0, 0)` – horizontal.  
4. While the queue is not empty:
   * Pop a state, increment `steps`.  
   * If it’s the target, return `steps`.  
   * Generate up to four new states by applying the four move rules; add those that are legal and unvisited to the queue.  
5. If the queue empties without hitting the target → return `-1`.



---

## 4. Edge‑Case Checklist

| Check | Why it matters |
|-------|----------------|
| Moving right while **horizontal** – must verify cell `(r, c+2)` is inside the grid and empty. |
| Moving right while **vertical** – must verify both `(r, c+1)` and `(r+1, c+1)` are empty. |
| Rotating clockwise – need **both** cells below the snake to be empty: `(r+1, c)` and `(r+1, c+1)`. |
| Rotating counter‑clockwise – need **both** cells to the right of the snake to be empty: `(r, c+1)` and `(r+1, c+1)`. |
| Never move outside the grid (`r < 0`, `c < 0`, `r+1 >= n`, `c+1 >= n`). |
| The target itself must be on empty cells – the problem guarantees this, but defensive checks help avoid hidden bugs. |

---

## 5. Implementation

Below are clean, idiomatic implementations in **Java, Python, and C++**.  
All three follow the same BFS pattern described above.

### 5.1 Java 17

```java
import java.util.*;

/**
 * LeetCode 1210 – Minimum Moves to Reach Target with Rotations
 */
public class Solution {
    private static final int HORIZ = 0, VERT = 1;
    private static final int[] DR = {0, 0};          // right, down for horizontal
    private static final int[] DC = {1, 1};

    public int minimumMoves(int[][] grid) {
        int n = grid.length;
        boolean[][][] visited = new boolean[n][n][2];
        Queue<State> q = new ArrayDeque<>();

        // start: horizontal at (0,0)
        visited[0][0][HORIZ] = true;
        q.offer(new State(0, 0, HORIZ));

        int steps = 0;
        int targetR = n - 1, targetC = n - 2;

        while (!q.isEmpty()) {
            int sz = q.size();
            while (sz-- > 0) {
                State cur = q.poll();
                int r = cur.r, c = cur.c, ori = cur.ori;

                if (r == targetR && c == targetC && ori == HORIZ) return steps;

                // ----- 1. Move right
                if (canMoveRight(grid, r, c, ori)) {
                    State nxt = nextRight(r, c, ori);
                    if (!visited[nxt.r][nxt.c][nxt.ori]) {
                        visited[nxt.r][nxt.c][nxt.ori] = true;
                        q.offer(nxt);
                    }
                }

                // ----- 2. Move down
                if (canMoveDown(grid, r, c, ori)) {
                    State nxt = nextDown(r, c, ori);
                    if (!visited[nxt.r][nxt.c][nxt.ori]) {
                        visited[nxt.r][nxt.c][nxt.ori] = true;
                        q.offer(nxt);
                    }
                }

                // ----- 3. Rotate clockwise (H -> V)
                if (ori == HORIZ && canRotateClockwise(grid, r, c)) {
                    State nxt = new State(r, c, VERT);
                    if (!visited[nxt.r][nxt.c][VERT]) {
                        visited[nxt.r][nxt.c][VERT] = true;
                        q.offer(nxt);
                    }
                }

                // ----- 4. Rotate counter‑clockwise (V -> H)
                if (ori == VERT && canRotateCounterClockwise(grid, r, c)) {
                    State nxt = new State(r, c, HORIZ);
                    if (!visited[nxt.r][nxt.c][HORIZ]) {
                        visited[nxt.r][nxt.c][HORIZ] = true;
                        q.offer(nxt);
                    }
                }
            }
            steps++;
        }
        return -1;  // impossible
    }

    /* ---------- Helper methods for the four move types ---------- */

    private boolean canMoveRight(int[][] g, int r, int c, int ori) {
        int n = g.length;
        if (ori == HORIZ) {
            int nc = c + 2;
            return nc < n && g[r][nc] == 0;
        } else { // vertical
            return (c + 1 < n && g[r][c + 1] == 0) &&
                   (c + 1 < n && g[r + 1][c + 1] == 0);
        }
    }

    private State nextRight(int r, int c, int ori) {
        if (ori == HORIZ) return new State(r, c + 1, HORIZ);
        return new State(r, c + 1, VERT);
    }

    private boolean canMoveDown(int[][] g, int r, int c, int ori) {
        int n = g.length;
        if (ori == VERT) {
            int nr = r + 2;
            return nr < n && g[nr][c] == 0;
        } else { // horizontal
            return (r + 1 < n && g[r + 1][c] == 0) &&
                   (r + 1 < n && g[r + 1][c + 1] == 0);
        }
    }

    private State nextDown(int r, int c, int ori) {
        if (ori == VERT) return new State(r + 1, c, VERT);
        return new State(r + 1, c, HORIZ);
    }

    private boolean canRotateClockwise(int[][] g, int r, int c) {
        int n = g.length;
        return (r + 1 < n) &&
               (g[r + 1][c] == 0 && g[r + 1][c + 1] == 0);
    }

    private boolean canRotateCounterClockwise(int[][] g, int r, int c) {
        int n = g.length;
        return (c + 1 < n) &&
               (g[r][c + 1] == 0 && g[r + 1][c + 1] == 0);
    }

    /* ---------- State class ---------- */
    private record State(int r, int c, int ori) {}
}
```

**Test harness**

```java
class Main {
    public static void main(String[] args) {
        int[][] grid = {
            {0,0,0,0,0,1},
            {1,1,0,0,1,0},
            {0,0,0,0,1,1},
            {0,0,1,0,1,0},
            {0,1,1,0,0,0},
            {0,1,1,0,0,0}
        };
        System.out.println(new Solution().minimumMoves(grid)); // → 11
    }
}
```

---

### 5.2 Python 3.10

```python
from collections import deque
from typing import List

class Solution:
    HORIZ, VERT = 0, 1

    def minimumMoves(self, grid: List[List[int]]) -> int:
        n = len(grid)
        visited = [[[False]*2 for _ in range(n)] for _ in range(n)]
        q = deque([(0, 0, self.HORIZ)])          # (row, col, orientation)

        visited[0][0][self.HORIZ] = True
        steps = 0
        target = (n-1, n-2, self.HORIZ)

        while q:
            for _ in range(len(q)):
                r, c, o = q.popleft()
                if (r, c, o) == target:
                    return steps

                # 1. Move right
                if self.can_move_right(grid, r, c, o):
                    nr, nc, no = self.next_right(r, c, o)
                    if not visited[nr][nc][no]:
                        visited[nr][nc][no] = True
                        q.append((nr, nc, no))

                # 2. Move down
                if self.can_move_down(grid, r, c, o):
                    nr, nc, no = self.next_down(r, c, o)
                    if not visited[nr][nc][no]:
                        visited[nr][nc][no] = True
                        q.append((nr, nc, no))

                # 3. Rotate clockwise (H → V)
                if o == self.HORIZ and self.can_rotate_cw(grid, r, c):
                    if not visited[r][c][self.VERT]:
                        visited[r][c][self.VERT] = True
                        q.append((r, c, self.VERT))

                # 4. Rotate counter‑clockwise (V → H)
                if o == self.VERT and self.can_rotate_ccw(grid, r, c):
                    if not visited[r][c][self.HORIZ]:
                        visited[r][c][self.HORIZ] = True
                        q.append((r, c, self.HORIZ))

            steps += 1
        return -1

    # ---------- helper methods ----------
    def can_move_right(self, g, r, c, o):
        n = len(g)
        if o == self.HORIZ:
            return c + 2 < n and g[r][c + 2] == 0
        # vertical
        return (c + 1 < n and g[r][c + 1] == 0 and
                c + 1 < n and g[r + 1][c + 1] == 0)

    def next_right(self, r, c, o):
        if o == self.HORIZ:
            return r, c + 1, o
        return r, c + 1, o

    def can_move_down(self, g, r, c, o):
        n = len(g)
        if o == self.VERT:
            return r + 2 < n and g[r + 2][c] == 0
        # horizontal
        return (r + 1 < n and g[r + 1][c] == 0 and
                r + 1 < n and g[r + 1][c + 1] == 0)

    def next_down(self, r, c, o):
        if o == self.VERT:
            return r + 1, c, o
        return r + 1, c, o

    def can_rotate_cw(self, g, r, c):
        n = len(g)
        return r + 1 < n and c + 1 < n and \
               g[r + 1][c] == 0 and g[r + 1][c + 1] == 0

    def can_rotate_ccw(self, g, r, c):
        n = len(g)
        return r + 1 < n and c + 1 < n and \
               g[r][c + 1] == 0 and g[r + 1][c + 1] == 0
```

**Complexity** – `O(n²)` time, `O(n²)` space.



---

### 5.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumMoves(vector<vector<int>>& grid) {
        const int n = grid.size();
        const int H = 0, V = 1;
        vector<vector<array<bool, 2>>> vis(n, vector<array<bool, 2>>(n, {false, false}));

        queue<tuple<int,int,int>> q;               // row, col, orientation
        q.emplace(0, 0, H);
        vis[0][0][H] = true;
        int steps = 0;
        const int tr = n - 1, tc = n - 2;          // target bottom‑right of the block

        while (!q.empty()) {
            int sz = q.size();
            while (sz--) {
                auto [r, c, o] = q.front(); q.pop();
                if (r == tr && c == tc && o == H) return steps;

                /* 1. Move right */
                if (canMoveRight(grid, r, c, o)) {
                    auto [nr, nc, no] = nextRight(r, c, o);
                    if (!vis[nr][nc][no]) {
                        vis[nr][nc][no] = true;
                        q.emplace(nr, nc, no);
                    }
                }

                /* 2. Move down */
                if (canMoveDown(grid, r, c, o)) {
                    auto [nr, nc, no] = nextDown(r, c, o);
                    if (!vis[nr][nc][no]) {
                        vis[nr][nc][no] = true;
                        q.emplace(nr, nc, no);
                    }
                }

                /* 3. Rotate clockwise (H -> V) */
                if (o == H && canRotateCW(grid, r, c)) {
                    if (!vis[r][c][V]) {
                        vis[r][c][V] = true;
                        q.emplace(r, c, V);
                    }
                }

                /* 4. Rotate counter‑clockwise (V -> H) */
                if (o == V && canRotateCCW(grid, r, c)) {
                    if (!vis[r][c][H]) {
                        vis[r][c][H] = true;
                        q.emplace(r, c, H);
                    }
                }
            }
            ++steps;
        }
        return -1;
    }

private:
    const int H = 0, V = 1;

    /* ---------- helper methods ---------- */
    bool canMoveRight(const vector<vector<int>>& g, int r, int c, int o) {
        int n = g.size();
        if (o == H) {                 // horizontal
            return c + 2 < n && g[r][c + 2] == 0;
        }                              // vertical
        return (c + 1 < n && g[r][c + 1] == 0 &&
                c + 1 < n && g[r + 1][c + 1] == 0);
    }

    tuple<int,int,int> nextRight(int r, int c, int o) {
        return {r, c + 1, o};
    }

    bool canMoveDown(const vector<vector<int>>& g, int r, int c, int o) {
        int n = g.size();
        if (o == V) {                 // vertical
            return r + 2 < n && g[r + 2][c] == 0;
        }                              // horizontal
        return (r + 1 < n && g[r + 1][c] == 0 &&
                r + 1 < n && g[r + 1][c + 1] == 0);
    }

    tuple<int,int,int> nextDown(int r, int c, int o) {
        return {r + 1, c, o};
    }

    bool canRotateCW(const vector<vector<int>>& g, int r, int c) {
        int n = g.size();
        return r + 1 < n && c + 1 < n &&
               g[r + 1][c] == 0 && g[r + 1][c + 1] == 0;
    }

    bool canRotateCCW(const vector<vector<int>>& g, int r, int c) {
        int n = g.size();
        return r + 1 < n && c + 1 < n &&
               g[r][c + 1] == 0 && g[r + 1][c + 1] == 0;
    }
};
```

**Usage**

```cpp
int main() {
    vector<vector<int>> grid = {
        {0,0,0,0,0,1},
        {1,1,0,0,1,0},
        {0,0,0,0,1,1},
        {0,0,1,0,1,0},
        {0,1,1,0,0,0},
        {0,1,1,0,0,0}
    };
    cout << Solution().minimumMoves(grid) << '\n'; // 11
}
```

---

---

## 6. Discussion – “Good / Bad” Parts

| Aspect | Good (What we did well) | Bad (Common pitfalls) |
|--------|------------------------|-----------------------|
| **Explicit state** | Represented by `(row, col, orientation)` – easy to hash & visit. | Forgetting to check *both* cells after a rotation. |
| **Move legality checks** | Each move has a tiny, independent helper – no duplicated logic. | Mixing up indices for horizontal vs. vertical moves. |
| **BFS layer processing** | Increment `steps` after processing one layer – ensures correct distance. | Using a single queue and forgetting to loop over `size()`. |
| **Boundary conditions** | Clear checks (`r+1 < n`, `c+1 < n`) to avoid out‑of‑bounds. | Off‑by‑one errors in `c+2 < n` or `r+2 < n`. |
| **Space** | 2‑D visited array *per* orientation → O(n²). | Using `unordered_set` of tuples would add overhead. |

---

## 7. Take‑away for Interviewers & Candidates

* The problem boils down to **grid navigation with a sliding/rotating piece**.
* A BFS over a 3‑D state space (`row × col × orientation`) solves it in `O(n²)` time.
* Pay careful attention to the **four move conditions** – they’re the only subtlety.
* Avoid index mistakes: Always check the **maximum index** before accessing the grid.

---

### 8. Final Verdict

This is a classic interview puzzle that tests:

1. **State representation** – modeling the block’s orientation.
2. **Boundary handling** – avoiding off‑by‑one errors.
3. **Algorithmic thinking** – BFS over a small state space.

Once you write the four helper checks, the rest is mechanical. All three implementations above should get a perfect **5/5** on a typical coding interview.

Happy coding! 🚀

--- 

**Keywords:** `grid navigation`, `sliding block`, `BFS`, `orientation`, `rotation`, `LeetCode`, `Dynamic Programming`, `C++`, `Python`, `Java`.