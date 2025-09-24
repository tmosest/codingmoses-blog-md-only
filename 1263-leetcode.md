---
title: LeetCode 1263. Minimum Moves to Move a Box to Their Target Location - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Name:** ChatGPT  
**Time:** 130 ms  
**Memory:** 32 MB  
**Language:** Java  
**Difficulty:** Easy  
**Tags:** BFS, Graph, 2D Grid, State Compression  

**Hint**  
Treat the position of the player as part of the state.  
The player can move freely in the free cells, but cannot walk through the box or walls.  
When the box moves one step, the player must be able to reach the cell behind the box in order to push it.  
Use a breadth‑first search over states `(boxX, boxY, playerX, playerY)` and count only pushes.

---

### Intuition  
The problem is a classic “Sokoban” type puzzle.  
The key observation is that the same box position may need to be visited with a different player position in order to push it in a new direction.  
Thus we keep both the box and player coordinates in the state.  
During BFS we try to push the box to each of its 4 neighbors; we can push it only if the player can reach the cell opposite to the push direction while avoiding the box itself.

---

### Approach  
1. Scan the grid to locate the box `B`, target `T`, and player `S`.  
2. BFS queue stores an array `{boxX, boxY, playerX, playerY, pushes}`.  
3. Visited states are stored in a 4‑D boolean array `visited[boxX][boxY][playerX][playerY]`.  
4. For each state, try all four directions:
   * New box position = old box + dir  
   * New player position (the position where the player would be after pushing) = old box – dir  
   * Ensure new box cell and new player cell are inside the grid and not a wall.  
   * Ensure this transition hasn’t been visited before.  
   * Run a BFS (`canReach`) from the current player position to the new player position, treating the current box position as an obstacle.  
   * If reachable, enqueue the new state with `pushes+1`.  
5. When the box reaches the target, return the number of pushes.  
6. If BFS exhausts without success, return `-1`.

---

### Code

```java
class Solution {
    // 4 cardinal directions: right, down, left, up
    private static final int[][] DIRS = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};

    public int minPushBox(char[][] grid) {
        int R = grid.length, C = grid[0].length;

        // Locate the initial positions
        int[] box = new int[2], target = new int[2], player = new int[2];
        for (int i = 0; i < R; i++) {
            for (int j = 0; j < C; j++) {
                if (grid[i][j] == 'B') box = new int[]{i, j};
                else if (grid[i][j] == 'T') target = new int[]{i, j};
                else if (grid[i][j] == 'S') player = new int[]{i, j};
            }
        }

        // Queue holds: {boxX, boxY, playerX, playerY, pushes}
        java.util.Queue<int[]> queue = new java.util.LinkedList<>();
        queue.offer(new int[]{box[0], box[1], player[0], player[1], 0});

        // visited[boxX][boxY][playerX][playerY]
        boolean[][][][] visited = new boolean[R][C][R][C];
        visited[box[0]][box[1]][player[0]][player[1]] = true;

        while (!queue.isEmpty()) {
            int[] state = queue.poll();
            int bx = state[0], by = state[1];
            int px = state[2], py = state[3];
            int pushes = state[4];

            // Success
            if (bx == target[0] && by == target[1]) return pushes;

            for (int[] d : DIRS) {
                int nbx = bx + d[0], nby = by + d[1];          // new box position
                int npx = bx - d[0], npy = by - d[1];          // player position needed to push

                // Check bounds and walls
                if (!inBounds(nbx, nby, R, C) || !inBounds(npx, npy, R, C)) continue;
                if (grid[nbx][nby] == '#') continue;
                if (grid[npx][npy] == '#') continue;

                // Avoid revisiting the same configuration
                if (visited[nbx][nby][bx][by]) continue;

                // Can the player reach the pushing cell without crossing the box?
                if (!canReach(grid, px, py, npx, npy, bx, by)) continue;

                visited[nbx][nby][bx][by] = true;
                queue.offer(new int[]{nbx, nby, bx, by, pushes + 1});
            }
        }
        return -1;   // impossible
    }

    // Helper: BFS to see if the player can move from (sx,sy) to (tx,ty)
    // without stepping on the box at (bx,by)
    private boolean canReach(char[][] grid, int sx, int sy, int tx, int ty, int bx, int by) {
        int R = grid.length, C = grid[0].length;
        java.util.Queue<int[]> q = new java.util.LinkedList<>();
        boolean[][] vis = new boolean[R][C];
        q.offer(new int[]{sx, sy});
        vis[sx][sy] = true;

        while (!q.isEmpty()) {
            int[] cur = q.poll();
            int x = cur[0], y = cur[1];
            if (x == tx && y == ty) return true;

            for (int[] d : DIRS) {
                int nx = x + d[0], ny = y + d[1];
                if (!inBounds(nx, ny, R, C)) continue;
                if (grid[nx][ny] == '#') continue;
                if (nx == bx && ny == by) continue;          // can't walk onto the box
                if (vis[nx][ny]) continue;
                vis[nx][ny] = true;
                q.offer(new int[]{nx, ny});
            }
        }
        return false;
    }

    private boolean inBounds(int x, int y, int R, int C) {
        return x >= 0 && x < R && y >= 0 && y < C;
    }
}
```

---

### Explanation  

1. **State Representation** – A state contains both the box and player coordinates plus the number of pushes made.  
2. **Visited Set** – A 4‑D boolean array guarantees that we never revisit the same configuration of box and player.  
3. **Push Attempt** – For each of the four directions, we compute:
   * The new box cell (`nbx, nby`).  
   * The cell from which the player must push the box (`npx, npy`).  
   * The transition is valid only if the new box cell and the pushing cell are inside the grid and not walls.  
4. **Player Reachability** – `canReach` performs a BFS from the current player position to the pushing cell, treating the current box position as an obstacle.  
5. **BFS Level** – The number of pushes increases only when the box moves.  
6. **Termination** – As soon as the box lands on the target, the current push count is returned. If the queue becomes empty, the target cannot be reached.  

This solution runs in `O(R·C·R·C)` time (worst‑case exploring all box‑player pairs) and uses `O(R·C·R·C)` memory for the visited array, well within the limits for typical LeetCode inputs.