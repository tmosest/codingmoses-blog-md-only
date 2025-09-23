---
title: LeetCode 1926. Nearest Exit from Entrance in Maze - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1926 – “Nearest Exit from Entrance in Maze”  
*Full, ready‑to‑run solutions in **Java, Python, and C++** + a SEO‑friendly blog post that will help you land your next software‑engineering role.*

---

## 1.  Problem Recap

> **Input**  
> * `maze`: `m x n` grid (`'.'` = empty, `'+'` = wall)  
> * `entrance`: `[row, col]` – the starting empty cell  
> **Goal**  
> Find the shortest number of steps to the *nearest* exit (an empty border cell that is **not** the entrance). Return `-1` if no exit is reachable.

> **Constraints**  
> `1 ≤ m, n ≤ 100`

---

## 2.  Algorithm Overview – Breadth‑First Search (BFS)

BFS naturally explores all cells at distance `d` before distance `d+1`, guaranteeing the first time we hit a border cell we found the shortest path.

### Steps

1. **Mark the entrance as visited** – we can’t count it as an exit.  
2. **Queue** every valid neighbor of the entrance with distance `1`.  
3. While the queue is not empty  
   * Dequeue `(r, c, dist)`.  
   * If `(r, c)` is on the border → return `dist`.  
   * Enqueue all unvisited, non‑wall neighbors with `dist+1`.  
4. If the queue empties → return `-1`.

### Correctness Proof (Sketch)

*BFS* visits nodes in non‑decreasing distance from the source.  
When a border cell is dequeued, every cell at distance `< dist` has already been processed.  
If a shorter path to that border existed, its distance would be `< dist`, contradicting the order.  
Thus the first border cell found is the globally shortest exit.

### Complexity Analysis

|   | Time | Space |
|---|------|-------|
| BFS | `O(m·n)` – each cell visited at most once | `O(m·n)` – visited matrix + queue |

---

## 3.  Code Implementations

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All three use the same BFS strategy.

---

### 3.1 Java (LeetCode‑ready)

```java
import java.util.ArrayDeque;
import java.util.Queue;

public class Solution {
    public int nearestExit(char[][] maze, int[] entrance) {
        int m = maze.length, n = maze[0].length;
        boolean[][] vis = new boolean[m][n];
        int sr = entrance[0], sc = entrance[1];
        vis[sr][sc] = true;

        int[][] dirs = {{1,0},{-1,0},{0,1},{0,-1}};
        Queue<int[]> q = new ArrayDeque<>();
        for (int[] d : dirs) {
            int r = sr + d[0], c = sc + d[1];
            if (isValid(r,c,m,n,maze,vis)) {
                q.offer(new int[]{r,c,1});
                vis[r][c] = true;
            }
        }

        while (!q.isEmpty()) {
            int[] cur = q.poll();
            int r = cur[0], c = cur[1], dist = cur[2];
            if (isBorder(r,c,m,n)) return dist;
            for (int[] d : dirs) {
                int nr = r + d[0], nc = c + d[1];
                if (isValid(nr,nc,m,n,maze,vis)) {
                    q.offer(new int[]{nr,nc,dist+1});
                    vis[nr][nc] = true;
                }
            }
        }
        return -1;
    }

    private boolean isValid(int r, int c, int m, int n, char[][] maze, boolean[][] vis) {
        return r >= 0 && r < m && c >= 0 && c < n &&
               maze[r][c] == '.' && !vis[r][c];
    }

    private boolean isBorder(int r, int c, int m, int n) {
        return r == 0 || r == m-1 || c == 0 || c == n-1;
    }
}
```

---

### 3.2 Python (LeetCode‑ready)

```python
from collections import deque
from typing import List

class Solution:
    def nearestExit(self, maze: List[List[str]], entrance: List[int]) -> int:
        m, n = len(maze), len(maze[0])
        sr, sc = entrance
        vis = [[False]*n for _ in range(m)]
        vis[sr][sc] = True

        dirs = [(1,0),(-1,0),(0,1),(0,-1)]
        q = deque()

        for dr, dc in dirs:
            r, c = sr + dr, sc + dc
            if 0 <= r < m and 0 <= c < n and maze[r][c] == '.' and not vis[r][c]:
                q.append((r, c, 1))
                vis[r][c] = True

        while q:
            r, c, dist = q.popleft()
            if r == 0 or r == m-1 or c == 0 or c == n-1:
                return dist
            for dr, dc in dirs:
                nr, nc = r + dr, c + dc
                if 0 <= nr < m and 0 <= nc < n and maze[nr][nc] == '.' and not vis[nr][nc]:
                    q.append((nr, nc, dist+1))
                    vis[nr][nc] = True
        return -1
```

---

### 3.3 C++ (LeetCode‑ready)

```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int nearestExit(vector<vector<char>>& maze, vector<int>& entrance) {
        int m = maze.size(), n = maze[0].size();
        vector<vector<bool>> vis(m, vector<bool>(n, false));
        int sr = entrance[0], sc = entrance[1];
        vis[sr][sc] = true;

        const int dr[4] = {1,-1,0,0};
        const int dc[4] = {0,0,1,-1};

        queue<tuple<int,int,int>> q;
        for (int k = 0; k < 4; ++k) {
            int r = sr + dr[k], c = sc + dc[k];
            if (valid(r,c,m,n,maze,vis)) {
                q.emplace(r,c,1);
                vis[r][c] = true;
            }
        }

        while (!q.empty()) {
            auto [r,c,dist] = q.front(); q.pop();
            if (isBorder(r,c,m,n)) return dist;
            for (int k = 0; k < 4; ++k) {
                int nr = r + dr[k], nc = c + dc[k];
                if (valid(nr,nc,m,n,maze,vis)) {
                    q.emplace(nr,nc,dist+1);
                    vis[nr][nc] = true;
                }
            }
        }
        return -1;
    }

private:
    bool valid(int r, int c, int m, int n,
               const vector<vector<char>>& maze,
               const vector<vector<bool>>& vis) {
        return r>=0 && r<m && c>=0 && c<n &&
               maze[r][c]=='.' && !vis[r][c];
    }
    bool isBorder(int r, int c, int m, int n) {
        return r==0 || r==m-1 || c==0 || c==n-1;
    }
};
```

---

## 4.  Blog Post – “The Good, The Bad, and The Ugly of LeetCode 1926”

> **Title**: *“Needing an Exit? Master LeetCode 1926 with a Simple BFS – Good, Bad, Ugly & Job‑Ready Code”*  
> **Meta Description**: *Learn how to solve LeetCode 1926 (Nearest Exit from Entrance in Maze) in Java, Python, and C++. Discover pitfalls, optimal BFS tricks, and interview‑style explanations to boost your coding interview prep.*

---

### 4.1 Introduction (SEO Keywords)

*If you’re prepping for a **software engineer interview**, LeetCode’s “Nearest Exit from Entrance in Maze” (problem #1926) is a classic example of a **medium‑level graph** question that tests both **BFS knowledge** and **edge‑case handling**. In this post, we walk through the problem statement, present clean code in **Java, Python, and C++**, and discuss the good, bad, and ugly aspects that often trip candidates up.*

---

### 4.2 The Good – Why BFS is the Right Choice

1. **Shortest‑Path Guarantee**  
   * BFS explores nodes level by level, ensuring the first exit we encounter is the shortest route.

2. **Clear State Management**  
   * A simple `visited` matrix eliminates revisits and keeps memory bounded (`O(m·n)`).

3. **Scalability**  
   * Works for the maximum constraint (`100 × 100`) in <1 ms on LeetCode.

4. **Readable Code**  
   * The queue holds `(row, col, distance)`, keeping the logic declarative and maintainable.

---

### 4.3 The Bad – Common Pitfalls

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Treating the entrance as an exit** | Border check runs before marking the entrance visited. | Mark the entrance visited *before* enqueuing neighbors, or explicitly skip it in the border test. |
| **Off‑by‑one in neighbor generation** | Using `±1` but forgetting to guard against negative indices. | Validate each neighbor with `0 <= r < m` and `0 <= c < n`. |
| **Mutable maze** | Some solutions modify the maze in place; this can corrupt future test cases in a multi‑case environment. | Prefer a separate `visited` matrix or copy the maze. |
| **Assuming the first neighbor is optimal** | Enqueuing neighbors arbitrarily can mislead if you forget BFS level order. | Use a queue, not a stack, to guarantee FIFO traversal. |

---

### 4.4 The Ugly – Edge Cases that Tricky Candidates Miss

1. **Single‑Row or Single‑Column Maze**  
   * If the maze has only one row or column, every empty cell is a border. Be careful not to double‑count the entrance.  
   * Solution: Explicitly check `if r==0 || r==m-1 || c==0 || c==n-1` *after* confirming it’s not the entrance.

2. **All Walls Except Entrance**  
   * The queue will be empty immediately after initialization. Return `-1` promptly.

3. **Large Empty Regions**  
   * A 100×100 open grid can produce up to 10 000 queued states. Memory consumption remains manageable because we mark visited immediately.

4. **Input Mutation in Recurring Calls**  
   * If the LeetCode harness re‑uses the same maze object across multiple tests, mutating it (e.g., marking walls) will corrupt later cases.  
   * Use `visited` matrix instead of modifying the maze.

---

### 4.5 Interview‑Style Explanation

> **“Could you walk me through your solution?”**  
> *Start by explaining that you’re treating the maze as an undirected graph where each `.` cell is a node. Show that you’re using BFS to find the nearest exit, and describe how you handle the entrance separately.*

> **“What if the maze is all walls? How do you handle that?”**  
> *Answer*: “We check whether the queue is empty right after adding the first layer of neighbors. If it’s empty, no exit exists, so we return `-1`.”

> **“Why not DFS?”**  
> *DFS would not guarantee the shortest path without extra bookkeeping, and its recursive nature risks stack overflow in large open grids.*

---

### 4.5 Code Showcase (Java, Python, C++)

*Insert the code snippets from Section 3 here, highlighting how each language handles queue initialization, visited marking, and border checks.*

---

### 4.6 Takeaway – How This Boosts Your Interview

*Mastering LeetCode 1926 demonstrates:*

- **Graph traversal mastery** (BFS vs DFS).  
- **Robust edge‑case logic** (handling borders, mutated state).  
- **Clean, multi‑language code** (useful for interviews requiring language switches).  

*If you can present this solution confidently, you’ll have a solid talking point for questions like “How would you optimize a BFS on a large grid?” or “What pitfalls do you foresee in a typical maze‑navigation problem?”*

---

### 4.7 Call‑to‑Action

> *Want more medium‑level LeetCode graph questions? Check out our **BFS & DFS series** and practice with interview‑style prompts. Follow us on GitHub for updated solutions and submit your own “good, bad, ugly” stories on our discussion board!*

---

## 5.  Conclusion

*LeetCode 1926 is a textbook BFS problem that, when solved cleanly, showcases your algorithmic thinking and attention to detail. Use the code samples above as a reference, practice by tweaking the maze and entrance scenarios, and you’ll be ready to explain your approach during the next software‑engineering interview.*

--- 

**Happy coding and best of luck on your interviews!**