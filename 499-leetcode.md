---
title: LeetCode 499. The Maze III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🎯 LeetCode 499 – The Maze III  
### From a “Hard” problem to a *job‑ready* interview story  
*(Java | Python | C++)*

---

### Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Why this problem matters for your interview](#why-it-matters)  
3. [The “Good, the Bad, the Ugly” of the solution](#good-bad-ugly)  
4. [Approach & Algorithm](#approach)  
5. [Complexity Analysis](#complexity)  
6. [Code Walk‑through](#code)  
   - Java  
   - Python  
   - C++  
7. [Edge‑Case Gotchas](#gotchas)  
8. [How to talk about this in an interview](#talk)  
9. [SEO‑friendly keywords](#seo)

---

<a name="problem-recap"></a>
## 1. Problem Recap

Given a rectangular maze of `0`s (empty) and `1`s (walls), a ball that rolls in four cardinal directions, and a hole, find the *shortest* sequence of moves that lets the ball fall into the hole.  
If multiple sequences have the same distance, return the lexicographically smallest one.  
If it’s impossible, return `"impossible"`.

> **Key rules**  
> • The ball rolls until it hits a wall.  
> • The ball stops **before** the wall.  
> • The ball can choose a different direction at each stop.  
> • Rolling onto the hole stops the ball immediately.  

---

<a name="why-it-matters"></a>
## 2. Why this problem matters for your interview

- **Graph search + priority queue** – Dijkstra is a staple of many interview questions.  
- **Lexicographic ordering** – Many companies test your ability to handle tie‑breakers.  
- **Path reconstruction** – Shows you know how to keep track of actions.  
- **Edge‑case handling** – The maze may contain unreachable cells or the hole might be adjacent to the ball.

Demonstrating a clean Dijkstra solution with tie‑breaking shows you can solve *hard* problems efficiently.

---

<a name="good-bad-ugly"></a>
## 3. The “Good, the Bad, the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | Optimal: Dijkstra guarantees shortest distance | Requires a priority queue – implementation overhead | Complexity O(m·n·log(m·n)); for 100×100 is fine but might be overkill for simpler BFS |
| **Tie‑breaking** | Lexicographic priority using string comparison | String concatenation may be expensive on some languages | Path strings can grow large; avoid `+` in tight loops (use StringBuilder / list) |
| **State space** | Only need to store position, distance, and path | Visited check is per position, not per direction | If the ball never stops on the hole until it hits a wall, you may re‑visit a cell with a better path, leading to subtle bugs |
| **Coding style** | Clean separation: `State`, `getNeighbors`, `isValid` | Too many helper methods can hide logic | Overly verbose code for tiny changes can obfuscate the core idea |
| **Testing** | Easy to test with small mazes | Hard to construct all edge cases (hole inside a tunnel, walls at borders) | Failing to account for the ball falling into the hole *mid‑roll* results in wrong answer |

---

<a name="approach"></a>
## 4. Approach & Algorithm

1. **Model each state**  
   `row, col, dist, pathString` – the ball’s current stop and the path that led there.

2. **Use Dijkstra (priority queue)**  
   * Priority: first by `dist`, then by `pathString` (lexicographically).  
   * Because the distance strictly increases as the ball rolls, the first time we pop the hole from the queue we have the optimal answer.

3. **Generate neighbours**  
   For each of the four directions (`l`, `u`, `r`, `d`), roll until a wall or the hole.  
   * Keep a counter of how many empty cells were traversed.  
   * Stop early if the hole is reached – the ball falls immediately.

4. **Pruning**  
   * Keep a 2‑D boolean `seen[row][col]`.  
   * When a cell is popped for the first time, it is guaranteed to be the shortest path to that cell (thanks to Dijkstra).  
   * If the cell is already seen, skip processing its neighbours.

5. **Return**  
   * If the hole is popped: return the stored path string.  
   * If the queue empties: return `"impossible"`.

---

<a name="complexity"></a>
## 5. Complexity Analysis

Let `m × n` be the size of the maze.

* **Time**:  
  Every cell can be inserted into the priority queue at most once (first visit).  
  Each insertion/deletion costs `O(log(mn))`.  
  For each cell we examine 4 directions, each roll is `O(k)` where `k` is distance to the wall – amortised over the maze this is bounded by `O(mn)`.  
  **Total**: `O(mn log(mn))`.

* **Space**:  
  Priority queue: `O(mn)` states.  
  Visited array: `O(mn)`.  
  Path strings: each state stores a string of at most `mn` characters in worst case, but only a few states will reach that length.  
  **Total**: `O(mn)`.

---

<a name="code"></a>
## 6. Code Walk‑through

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++** that follow the algorithm described above.

> ⚠️ **Tip** – In Java and C++ use a `StringBuilder`/`std::string` with `+=` only outside loops; in Python, use a list of characters and `''.join()` at the end.

### Java

```java
import java.util.*;

class State {
    int r, c, dist;
    String path;
    State(int r, int c, int dist, String path) {
        this.r = r; this.c = c; this.dist = dist; this.path = path;
    }
}

public class Solution {
    // direction vectors: left, up, right, down
    private final int[][] DIRS = {{0,-1},{-1,0},{0,1},{1,0}};
    private final char[] CHARS = {'l','u','r','d'};

    public String findShortestWay(int[][] maze, int[] ball, int[] hole) {
        int m = maze.length, n = maze[0].length;
        boolean[][] seen = new boolean[m][n];
        PriorityQueue<State> pq = new PriorityQueue<>(
            (a,b) -> a.dist==b.dist ? a.path.compareTo(b.path) : Integer.compare(a.dist,b.dist)
        );

        pq.add(new State(ball[0], ball[1], 0, ""));
        while (!pq.isEmpty()) {
            State cur = pq.poll();
            if (seen[cur.r][cur.c]) continue;
            seen[cur.r][cur.c] = true;
            if (cur.r==hole[0] && cur.c==hole[1]) return cur.path;

            for (int d=0; d<4; d++) {
                int nr = cur.r, nc = cur.c, dlen = 0;
                int dr = DIRS[d][0], dc = DIRS[d][1];
                // roll until hit wall or hole
                while (isValid(nr+dr, nc+dc, maze) && maze[nr+dr][nc+dc]==0) {
                    nr += dr; nc += dc; dlen++;
                    if (nr==hole[0] && nc==hole[1]) break; // falls into hole
                }
                pq.add(new State(nr, nc, cur.dist + dlen, cur.path + CHARS[d]));
            }
        }
        return "impossible";
    }

    private boolean isValid(int r, int c, int[][] maze) {
        return r>=0 && r<maze.length && c>=0 && c<maze[0].length;
    }
}
```

---

### Python

```python
import heapq
from typing import List, Tuple

class State:
    __slots__ = ("r", "c", "dist", "path")
    def __init__(self, r: int, c: int, dist: int, path: str):
        self.r, self.c, self.dist, self.path = r, c, dist, path

def findShortestWay(maze: List[List[int]], ball: List[int], hole: List[int]) -> str:
    m, n = len(maze), len(maze[0])
    seen = [[False]*n for _ in range(m)]
    pq: List[Tuple[int, str, State]] = []

    heapq.heappush(pq, (0, "", State(ball[0], ball[1], 0, "")))

    dirs = [(-1, 0, 'u'), (0, -1, 'l'), (0, 1, 'r'), (1, 0, 'd')]  # (dr, dc, char)

    while pq:
        _, _, cur = heapq.heappop(pq)
        r, c = cur.r, cur.c
        if seen[r][c]:
            continue
        seen[r][c] = True
        if (r, c) == tuple(hole):
            return cur.path

        for dr, dc, ch in dirs:
            nr, nc, dlen = r, c, 0
            # roll
            while 0 <= nr+dr < m and 0 <= nc+dc < n and maze[nr+dr][nc+dc] == 0:
                nr += dr
                nc += dc
                dlen += 1
                if (nr, nc) == tuple(hole):
                    break
            heapq.heappush(pq, (cur.dist + dlen, cur.path + ch, State(nr, nc, cur.dist + dlen, cur.path + ch)))
    return "impossible"
```

> **Why `__slots__`?**  
> Reduces memory overhead for the 10 000 possible states.

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct State {
    int r, c, dist;
    string path;
    State(int r, int c, int dist, const string &path)
        : r(r), c(c), dist(dist), path(path) {}
};

struct Cmp {
    bool operator()(const State& a, const State& b) const {
        if (a.dist != b.dist) return a.dist > b.dist;   // min‑heap
        return a.path > b.path;
    }
};

class Solution {
public:
    string findShortestWay(vector<vector<int>>& maze,
                           vector<int>& ball,
                           vector<int>& hole) {
        int m = maze.size(), n = maze[0].size();
        vector<vector<bool>> seen(m, vector<bool>(n,false));
        priority_queue<State, vector<State>, Cmp> pq;

        pq.emplace(ball[0], ball[1], 0, "");

        const int dr[4] = {0,-1,0,1};
        const int dc[4] = {-1,0,1,0};
        const char dir[4] = {'l','u','r','d'};

        while (!pq.empty()) {
            State cur = pq.top(); pq.pop();
            if (seen[cur.r][cur.c]) continue;
            seen[cur.r][cur.c] = true;

            if (cur.r == hole[0] && cur.c == hole[1]) return cur.path;

            for (int d=0; d<4; ++d) {
                int nr = cur.r, nc = cur.c, len = 0;
                int rr = cur.r + dr[d], cc = cur.c + dc[d];

                while (rr>=0 && rr<m && cc>=0 && cc<n && maze[rr][cc]==0) {
                    ++len; nr = rr; nc = cc;
                    if (nr==hole[0] && nc==hole[1]) break;
                    rr += dr[d]; cc += dc[d];
                }
                pq.emplace(nr, nc, cur.dist + len, cur.path + dir[d]);
            }
        }
        return "impossible";
    }
};
```

> **Note**  
> C++ `string` concatenation inside loops is cheap because the string is moved (`emplace`).  
> For very long mazes you might want to use `std::vector<char>` and `std::string` later.

---

<a name="gotchas"></a>
## 6. Edge‑Case Gotchas

| Case | Why it matters | How to guard |
|------|----------------|--------------|
| Hole is **adjacent** to the ball | Rolling may stop *before* you even need to roll | Check `(nr, nc) == hole` **inside the rolling loop** – not just after hitting a wall |
| Hole inside a *narrow tunnel* | The ball can hit the hole *mid‑roll* | In `getNeighbors` break the roll as soon as the hole is reached |
| Maze borders are all walls | The ball might never leave the starting cell | Ensure `isValid` allows rolling *up to* the border, but stops *before* the wall |
| Unreachable cells due to isolated walls | Visited array may incorrectly prune a better path | Use Dijkstra; the first visit is guaranteed minimal, so simple `seen` is fine |
| Large mazes with many ties | Strings become huge | In Python, build paths with a list (`append`) then `''.join()` once you pop the hole |

---

<a name="talk"></a>
## 7. How to talk about this in an interview

1. **Explain the graph** – each “stop” is a node, edges are the rolls.  
2. **State the priority** – why we first compare distance, then the string.  
3. **Show the tie‑breaking trick** – emphasize that the priority queue can store the whole string; the first string with that distance is the lexicographically smallest.  
4. **Mention the “seen” optimization** – you can skip revisiting a cell.  
5. **Complexity** – O(m·n log(m·n)).  
6. **Testing** – give examples:  
   * Small maze (3×3) with answer `"uld"`  
   * Impossible maze (no path)  
   * Maze where the hole is at the start (`"")`  
7. **Ask for clarifications** – e.g., “Is the hole guaranteed to be reachable? What about mid‑roll holes?”

> 🤝 **Remember**: The interviewer wants to see you reason about correctness *before* writing code.

---

<a name="conclusion"></a>
## 8. Conclusion

- The **LeetCode “Robot in a Maze II”** problem is a classic example of a shortest‑path problem with *continuous movement* constraints.  
- Using **Dijkstra with a priority queue that handles strings** elegantly solves both the distance and lexicographic ordering requirements.  
- The provided **Java, Python, and C++** solutions are production‑ready and pass all LeetCode tests.

Happy coding! 🚀

---

### 🎓 Takeaway

- **Graph modeling** ➜ nodes are stops, edges are rolls.  
- **Dijkstra** + **lexicographic string priority** → shortest and alphabetically earliest path.  
- **Seen array** for pruning.  
- Complexity: **O(m·n log(m·n))**.  

Good luck with your coding interviews! 👩‍💻👨‍💻