---
title: LeetCode 1926. Nearest Exit from Entrance in Maze - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. LeetCode 1926 – “Nearest Exit from Entrance in Maze”

**Problem Summary**

| Item | Description |
|------|-------------|
| **Goal** | Find the minimum number of steps from a given entrance cell to the nearest exit (an empty border cell) in an `m × n` maze. |
| **Movement** | 4‑directional (up, down, left, right). Cannot walk into walls (`'+'`) or outside the maze. |
| **Input** | `char[][] maze`, `int[] entrance` (entrance is guaranteed to be `'.'`). |
| **Output** | Minimum steps to the nearest exit, or `-1` if none exist. |
| **Constraints** | `1 ≤ m, n ≤ 100` |


The classic solution is **Breadth‑First Search (BFS)** – the shortest‑path tree in an unweighted graph.  
Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  

---

## 2. Code – Three Languages

### 2.1 Java – Clean, Fast BFS

```java
import java.util.*;

public class Solution {
    public int nearestExit(char[][] maze, int[] entrance) {
        int rows = maze.length;
        int cols = maze[0].length;

        // Directions: right, left, down, up
        int[][] dirs = {{0, 1}, {0, -1}, {1, 0}, {-1, 0}};
        Queue<int[]> q = new ArrayDeque<>();
        q.offer(entrance);
        maze[entrance[0]][entrance[1]] = '+';        // mark visited

        int steps = 0;
        while (!q.isEmpty()) {
            steps++;                                 // one layer = one step
            int size = q.size();
            for (int i = 0; i < size; i++) {
                int[] cur = q.poll();
                for (int[] d : dirs) {
                    int r = cur[0] + d[0];
                    int c = cur[1] + d[1];
                    if (r < 0 || r >= rows || c < 0 || c >= cols) continue;
                    if (maze[r][c] == '+') continue;  // wall or already visited

                    // Exit found (on border, but not the entrance)
                    if (r == 0 || r == rows-1 || c == 0 || c == cols-1)
                        return steps;

                    maze[r][c] = '+';                // mark visited
                    q.offer(new int[]{r, c});
                }
            }
        }
        return -1;  // no exit reachable
    }
}
```

### 2.2 Python – Simple BFS with `deque`

```python
from collections import deque
from typing import List

class Solution:
    def nearestExit(self, maze: List[List[str]], entrance: List[int]) -> int:
        R, C = len(maze), len(maze[0])
        dirs = [(0, 1), (0, -1), (1, 0), (-1, 0)]
        q = deque([tuple(entrance)])
        maze[entrance[0]][entrance[1]] = '+'   # visited flag

        steps = 0
        while q:
            steps += 1
            for _ in range(len(q)):
                r, c = q.popleft()
                for dr, dc in dirs:
                    nr, nc = r + dr, c + dc
                    if not (0 <= nr < R and 0 <= nc < C):  # outside
                        continue
                    if maze[nr][nc] == '+':  # wall / visited
                        continue
                    # Border exit (not entrance)
                    if nr == 0 or nr == R-1 or nc == 0 or nc == C-1:
                        return steps
                    maze[nr][nc] = '+'
                    q.append((nr, nc))
        return -1
```

### 2.3 C++ – BFS with `queue` (C++17)

```cpp
#include <vector>
#include <queue>

class Solution {
public:
    int nearestExit(std::vector<std::vector<char>>& maze, std::vector<int>& entrance) {
        int R = maze.size(), C = maze[0].size();
        const std::vector<std::pair<int,int>> dirs = {
            {0,1}, {0,-1}, {1,0}, {-1,0}
        };
        std::queue<std::pair<int,int>> q;
        q.push({entrance[0], entrance[1]});
        maze[entrance[0]][entrance[1]] = '+';          // visited flag

        int steps = 0;
        while (!q.empty()) {
            ++steps;                                    // layer = step
            int sz = q.size();
            for (int i=0; i<sz; ++i) {
                auto [r, c] = q.front(); q.pop();
                for (auto [dr, dc] : dirs) {
                    int nr = r + dr, nc = c + dc;
                    if (nr < 0 || nr >= R || nc < 0 || nc >= C) continue;
                    if (maze[nr][nc] == '+') continue;

                    // Exit on border (but not the entrance itself)
                    if (nr==0 || nr==R-1 || nc==0 || nc==C-1)
                        return steps;

                    maze[nr][nc] = '+';
                    q.push({nr, nc});
                }
            }
        }
        return -1;
    }
};
```

All three solutions run in **O(m × n)** time, using an in‑place visited flag (by converting cells to `‘+’`).  
They handle every edge case – entrance on the border, isolated cells, large open areas – safely.

---

## 3. Blog Article – “The Good, The Bad, The Ugly of LeetCode 1926”

---

# 🚀 Nearest Exit from Entrance in Maze – LeetCode 1926  
### The BFS Approach Explained in Java, Python, and C++  
> *Ready for your next technical interview? Master this maze‑shortest‑path problem and shine on your next job hunt.*

---

## 3.1 Meta‑Description  
**Want to ace LeetCode 1926?** Learn the BFS solution, see clean Java/Python/C++ code, and discover the *good, bad, and ugly* of this classic interview problem. Ideal for software‑engineering interview prep.  

---

## 3.2 Problem Recap

> **Nearest Exit from Entrance in Maze**  
> Find the fewest steps from the given entrance to an empty border cell in a 2‑D grid where `'+'` are walls and `'.'` are walkable cells.

- **Grid size:** `1 ≤ m, n ≤ 100`
- **Movement:** 4‑directional (no diagonals)
- **Goal:** Minimum steps → `-1` if no exit

---

## 3.3 Why BFS? The Core Insight

*All edges have weight 1* – the maze is an **unweighted graph**.  
BFS expands nodes level‑by‑level; the first time we hit a border cell (except the entrance) we know we have the shortest distance.  
No need for Dijkstra, no DP, no heuristic – plain BFS is optimal.

---

## 3.4 Algorithm Walkthrough

1. **Mark Entrance** – Set `maze[entrance]` to a wall (`‘+’`) so we never revisit it.
2. **Initialize Queue** – Push the entrance coordinate.
3. **Directional Offsets** – `[(0,1),(0,-1),(1,0),(-1,0)]`.
4. **Level‑by‑Level Expansion**  
   - `steps++` before processing the current layer.  
   - For each node in the layer, explore all 4 directions.  
   - Skip out‑of‑bounds cells or walls.  
   - If the new cell lies on the border → **return** current `steps`.  
   - Else mark as visited (`‘+’`) and enqueue.
5. **No Exit** – Queue empties → return `-1`.

---

## 3.5 Corner‑Case Checklist

| Edge Case | Why It Matters | Fix |
|-----------|----------------|-----|
| Entrance on the border | Might be considered an exit → **must not** be counted. | Mark entrance as visited immediately. |
| All cells are walls | No path → `-1`. | BFS will terminate after 1 × n queue ops. |
| Large open area | Queue can grow big → use `ArrayDeque`/`deque` (no linked list overhead). | Use `ArrayDeque` in Java / `deque` in Python. |
| Input size 100 × 100 | Max 10,000 cells → fine for memory. | In‑place marking keeps visited array out of picture. |

---

## 3.6 Good, Bad & Ugly – In‑Depth Analysis

| Aspect | What’s Good | What’s Bad | The Ugly |
|--------|-------------|------------|----------|
| **Algorithmic Strength** | BFS guarantees optimality, O(mn) time, O(mn) memory. | Requires a queue – overhead of object allocation in Java, list in Python. | None if you forget to mark visited → infinite loop. |
| **Coding Simplicity** | Four‑direction array, layer‑counting, single loop. | Very readable, no recursion. | In‑place marking modifies input; if the caller re‑uses the maze this is destructive. |
| **Performance** | Constant‑time neighbor checks, early exit stops early. | Worst‑case visits every cell → 10,000 ops (trivial). | Using `'+`' to mark visited may cause cache‑miss if maze is large; a separate `bool` matrix could be clearer. |
| **Robustness** | Handles any valid entrance, including border. | Must explicitly ignore entrance as exit. | Forgetting to check `if (r == 0 || …)` can give wrong distance when entrance is already on the border. |
| **Space** | O(mn) with in‑place visited flag; queue max size ~ perimeter. | If you use a separate `visited` matrix you’ll double the memory. | If the maze is huge (not in constraints), recursion depth/stack could blow in DFS alternatives. |

---

## 3.7 Performance & Space Tweaks

| Technique | When to Use | Impact |
|-----------|-------------|--------|
| **In‑place visited flag** (`maze[i][j] = '+'`) | When you’re allowed to mutate the input (most LeetCode problems). | Saves `O(mn)` boolean array. |
| **Pre‑allocate directions array** | Prevents creating 4 new `int[]` per node. | Minor speedup in Java/C++. |
| **`ArrayDeque` instead of `LinkedList`** | Java 8+ | Faster queue operations, less GC churn. |
| **Use `deque` in Python** | Python 3 | `popleft()` is O(1) – faster than list pop(0). |
| **Layer‑counting** (`steps++` outside inner loop) | Needed to return the exact distance. | Avoids tracking a distance per node. |
| **Early exit check** (`if on border return steps`) | Stops when the first exit is found. | Saves time in sparse mazes. |

---

## 3.8 Interview Tips – How to Nail This Question

1. **Clarify the Definition of an Exit**  
   *An exit is an empty border cell that is **not** the entrance itself.*

2. **State Your Assumptions**  
   *Entrance is walkable (`'.'`).  
   *You cannot walk into walls (`'+'`).*

3. **Sketch the Graph**  
   *Treat each cell as a node; edges connect walkable adjacent cells.*

4. **Choose BFS**  
   *Explain why BFS is the optimal choice: unweighted graph, shortest path.  
   *Show that DFS would be incorrect (might find a longer path).*

5. **Complexity**  
   *Time: O(m × n).  
   *Space: O(m × n) for queue + visited flag.*

6. **Edge Cases**  
   *Walk through the “entrance on the border” case.  
   *Explain how you avoid counting it as an exit.*

6. **Write Clean Code**  
   *Use concise loops, avoid redundant data structures.  
   *In Java, use `ArrayDeque`.*

7. **Test on Your Phone**  
   *If possible, manually run a 3×3 example to illustrate level counting.*

---

## 3.9 Conclusion – The Takeaway

LeetCode 1926 may look like a simple maze puzzle, but it tests *graph traversal intuition* and *boundary handling*.  
By mastering the BFS solution – and knowing the good, bad, and ugly details – you’ll not only solve this problem efficiently but also demonstrate:

- A solid grasp of **algorithmic fundamentals**.  
- The ability to write **clean, production‑ready code**.  
- Practical knowledge of **performance optimizations**.

Good luck on your next interview! 🎉  

---

## 3.10 Share & Discuss

> Found this article helpful? Drop a comment below with your own edge‑case hacks or ask for help on other LeetCode problems.  
> **Happy coding!**  

---  

*(End of article)*

---

## 4. Wrapping Up

You now have:

- **Three battle‑tested implementations** ready for any interview language.
- A deep dive into *why BFS works*, how to avoid pitfalls, and how to handle every edge case.
- A blog‑style guide that frames the problem in a way interviewers love: clear assumptions, step‑by‑step reasoning, complexity discussion, and real‑world tips.

Use this knowledge to strengthen your portfolio, impress recruiters, and secure that dream software‑engineering role. Happy hacking! 🏁

--- 

*Good luck, coder!*