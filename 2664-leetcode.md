---
title: LeetCode 2664. The KnightÕs Tour - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2664 – **The Knight’s Tour**  
### Code in Java, Python, & C++  
Below are clean, production‑ready solutions that work for the tiny 1 – 5 × 5 boards LeetCode uses.  
All three implementations use a classic depth‑first search (DFS) with backtracking and an optional *Warnsdorff* heuristic (the most visited‑next‑cell rule). Because the board is tiny the plain DFS is fast enough, but the heuristic can help you win on the very worst test cases.

---

### Common Parts – 8 Knight Moves
```text
dx = {  2,  1, -1, -2, -2, -1,  1,  2}
dy = {  1,  2,  2,  1, -1, -2, -2, -1}
```

---

## 2. Java

```java
import java.util.*;

public class Solution {
    private static final int[] DX = {  2,  1, -1, -2, -2, -1,  1,  2};
    private static final int[] DY = {  1,  2,  2,  1, -1, -2, -2, -1};

    public int[][] tourOfKnight(int m, int n, int r, int c) {
        int[][] board = new int[m][n];
        int[][] visited = new int[m][n];
        for (int i = 0; i < m; i++) Arrays.fill(visited[i], -1);

        if (!dfs(r, c, 0, board, visited, m, n)) {
            throw new IllegalStateException("Solution not found – problem guarantees existence");
        }
        return board;
    }

    private boolean dfs(int x, int y, int step,
                        int[][] board, int[][] visited,
                        int m, int n) {
        board[x][y] = step;
        visited[x][y] = step;

        if (step == m * n - 1) return true;

        // Warnsdorff: sort next moves by number of onward moves
        List<int[]> next = new ArrayList<>();
        for (int i = 0; i < 8; i++) {
            int nx = x + DX[i], ny = y + DY[i];
            if (nx >= 0 && nx < m && ny >= 0 && ny < n && visited[nx][ny] == -1) {
                next.add(new int[]{nx, ny});
            }
        }

        next.sort(Comparator.comparingInt(pos -> countOnward(pos[0], pos[1], visited, m, n)));

        for (int[] pos : next) {
            if (dfs(pos[0], pos[1], step + 1, board, visited, m, n))
                return true;
        }

        // backtrack
        board[x][y] = -1;
        visited[x][y] = -1;
        return false;
    }

    private int countOnward(int x, int y, int[][] visited, int m, int n) {
        int cnt = 0;
        for (int i = 0; i < 8; i++) {
            int nx = x + DX[i], ny = y + DY[i];
            if (nx >= 0 && nx < m && ny >= 0 && ny < n && visited[nx][ny] == -1) cnt++;
        }
        return cnt;
    }
}
```

### Why this works  
* **Backtracking** guarantees we explore all possibilities until we hit the final step.  
* **Warnsdorff’s rule** (sort by onward moves) prunes branches early and keeps the recursion shallow – even on a 5 × 5 board the search finishes in milliseconds.  
* The solution is **deterministic** – the same board always returns the same tour order, making it easy to test.

---

## 3. Python 3

```python
from typing import List

# Pre‑computed 8 knight moves
DX = [ 2,  1, -1, -2, -2, -1,  1,  2]
DY = [ 1,  2,  2,  1, -1, -2, -2, -1]

def tour_of_knight(m: int, n: int, r: int, c: int) -> List[List[int]]:
    board = [[-1] * n for _ in range(m)]
    visited = [[False] * n for _ in range(m)]

    def count_onward(x: int, y: int) -> int:
        """Warnsdorff helper – how many free squares a knight can jump to."""
        return sum(
            0 <= x + DX[k] < m and 0 <= y + DY[k] < n and not visited[x + DX[k]][y + DY[k]]
            for k in range(8)
        )

    def dfs(x: int, y: int, step: int) -> bool:
        board[x][y] = step
        visited[x][y] = True

        if step == m * n - 1:
            return True

        # Generate and sort next positions by Warnsdorff heuristic
        nxt = sorted(
            ((x + DX[k], y + DY[k]) for k in range(8)
             if 0 <= x + DX[k] < m and 0 <= y + DY[k] < n and not visited[x + DX[k]][y + DY[k]]),
            key=lambda p: count_onward(*p)
        )

        for nx, ny in nxt:
            if dfs(nx, ny, step + 1):
                return True

        # backtrack
        board[x][y] = -1
        visited[x][y] = False
        return False

    if not dfs(r, c, 0):
        raise RuntimeError("No solution – problem guarantees one exists")
    return board
```

**Quick sanity test**

```python
>>> tour_of_knight(1, 1, 0, 0)
[[0]]
>>> tour_of_knight(3, 4, 0, 0)
[[0, 3, 6, 9], [11, 8, 1, 4], [2, 5, 10, 7]]
```

---

## 4. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static const int DX[8];
    static const int DY[8];

    vector<vector<int>> tourOfKnight(int m, int n, int r, int c) {
        vector<vector<int>> board(m, vector<int>(n, -1));
        vector<vector<int>> visited(m, vector<int>(n, 0));
        dfs(r, c, 0, m, n, board, visited);
        return board;
    }

private:
    bool dfs(int x, int y, int step, int m, int n,
             vector<vector<int>>& board,
             vector<vector<int>>& visited) {
        board[x][y] = step;
        visited[x][y] = 1;

        if (step == m * n - 1) return true;

        vector<pair<int,int>> nxt;
        for (int k = 0; k < 8; ++k) {
            int nx = x + DX[k], ny = y + DY[k];
            if (nx >= 0 && nx < m && ny >= 0 && ny < n && !visited[nx][ny]) {
                nxt.emplace_back(nx, ny);
            }
        }

        sort(nxt.begin(), nxt.end(),
            [&](const pair<int,int>& a, const pair<int,int>& b) {
                return countOnward(a.first, a.second, visited, m, n)
                     < countOnward(b.first, b.second, visited, m, n);
            });

        for (auto [nx, ny] : nxt) {
            if (dfs(nx, ny, step + 1, m, n, board, visited))
                return true;
        }

        // backtrack
        board[x][y] = -1;
        visited[x][y] = 0;
        return false;
    }

    int countOnward(int x, int y,
                    const vector<vector<int>>& visited,
                    int m, int n) {
        int cnt = 0;
        for (int k = 0; k < 8; ++k) {
            int nx = x + DX[k], ny = y + DY[k];
            if (nx >= 0 && nx < m && ny >= 0 && ny < n && !visited[nx][ny])
                ++cnt;
        }
        return cnt;
    }
};

const int Solution::DX[8] = { 2,  1, -1, -2, -2, -1,  1,  2};
const int Solution::DY[8] = { 1,  2,  2,  1, -1, -2, -2, -1};
```

---

## 5. Blog Article – “The Good, The Bad, and The Ugly of Implementing Knight’s Tour in LeetCode”

> **Keywords**: *Knight’s Tour*, *LeetCode 2664*, *DFS backtracking*, *Warnsdorff heuristic*, *coding interview*, *job interview*, *algorithm design*, *C++*, *Java*, *Python*

---

### 1. The Good – Why Knight’s Tour is a “Show‑stopper” Skill

- **Showcases deep algorithmic thinking.**  
  Solving a Hamiltonian path on a chessboard demonstrates mastery over graph traversal, recursion, and combinatorial optimization.

- **Scales across languages.**  
  Whether you code in C++, Java or Python, the same logical structure works. This is a huge plus for interviews that ask you to translate an idea into multiple languages.

- **Time & space efficiency on tiny boards.**  
  For the LeetCode constraints (≤ 5 × 5) a plain DFS runs in a fraction of a millisecond. Even with the *Warnsdorff* pruning the recursion depth never exceeds 25. Recruiters love solutions that run in *O(m · n)* for tiny inputs – they’re clear about constraints and still elegant.

- **A clean, deterministic output.**  
  The board values `[0…m·n-1]` give a unique tour order. When you can explain the output to a hiring manager, you instantly elevate the conversation from “I wrote a solver” to “I understand the problem space.”

---

### 2. The Bad – Common Pitfalls That Turn “Nice” into “No‑Go”

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Infinite recursion** | Forgetting to mark the current cell as visited before exploring next moves. | Set `visited[x][y] = true` **before** the for‑loop, and reset it on backtrack. |
| **Wrong board dimensions** | Mixing `m` (height) and `n` (width) leads to off‑by‑one errors. | Use `board[m][n]` consistently; validate indices with `< m` and `< n`. |
| **No solution path** | Wrong start coordinates or board that is inherently impossible. | Problem guarantees existence; double‑check the move generation logic. |
| **Performance hit on 5 × 5** | Plain backtracking without heuristics can explore many dead ends. | Apply Warnsdorff’s rule: sort moves by *number of onward moves*. |
| **Incorrect return value** | Returning `board` after first successful DFS call, but forgetting to clone the array for each language’s semantics. | In Java/C++ return the array itself; in Python return the list directly. |

---

### 3. The Ugly – When the Interviewer Turns Up the Stakes

1. **“What if the board was 8 × 8?”**  
   The naive backtracking explodes combinatorially. In that case you must implement *Warnsdorff’s rule* **without** backtracking or use a *randomized* tour generator. Mention the trade‑offs: the heuristic guarantees a tour on an empty board, but you must handle edge cases.

2. **“Can you make this multi‑threaded?”**  
   Parallel DFS is possible but fragile. A cleaner solution is to pre‑compute a *canonical* tour and then rotate/reflect it. Show you can discuss algorithmic complexity and why you would avoid concurrency bugs.

3. **“Explain the time complexity formally.”**  
   While the board is tiny, recruiters may ask: *Why is it O(3 · m · n)?*  
   • **Each node** has at most 8 outgoing edges.  
   • In the worst case we traverse *every* edge once, i.e., `8 · m · n`.  
   For a 5 × 5 board that’s just 200 operations – trivial but still correct.

4. **“Do you support multiple start positions?”**  
   In LeetCode you get a fixed `(r, c)`. But you can generalise the function to accept a vector of start points and return a *vector of tours*. This demonstrates robustness.

5. **“Show me the complexity analysis on paper.”**  
   Bring a whiteboard and sketch the recursion tree. Highlight how Warnsdorff’s rule keeps the tree shallow, effectively reducing the branching factor from 8 to about 2–3 on average.

---

### 4. Turning the Ugly into a Success Story

- **Frame your solution as a *module*.**  
  “I’ve written a reusable `KnightTour` component in Java, and it can be ported to Python or C++ with minimal changes.”  

- **Talk about *space optimisation*.**  
  Use a single `int[25]` array for visited flags (bit‑mask). In C++ you can replace `vector<vector<int>>` with a flat `vector<int>` of size `m*n`. This reduces cache misses – a detail interviewers love.

- **Mention *test‑first* methodology.**  
  Write unit tests for 1 × 1, 2 × 2, 3 × 4, 5 × 5. Show that your code passes them before presenting to the interviewer. It gives a sense of confidence and quality.

- **Show off the deterministic output.**  
  “The first value is always `0` at the start, and the last value is always `m·n‑1`. We can use this property to validate the correctness on the fly.”  

- **Offer to extend the algorithm.**  
  “If we were to support blocked cells or obstacles, we could modify the visited matrix accordingly. The core DFS logic stays identical; we just skip pre‑blocked squares.”

---

### 4.1 Wrap‑Up: What Recruiters Care About

| Interviewer’s Question | What to Focus On |
|------------------------|-----------------|
| “Can you write it in multiple languages?” | Show the same DFS skeleton, adapt syntax, keep the algorithmic logic identical. |
| “Explain the complexity.” | O(1) for 5 × 5, but O(m · n) with Warnsdorff’s rule for larger boards. |
| “What if we want to generate many tours?” | Use a pre‑computed canonical tour; or randomised backtracking with time budget. |
| “Why do you think this matters?” | It demonstrates understanding of graph traversal, Hamiltonian paths, and the trade‑offs between brute‑force and heuristics. |

---

## 6. Take‑away for the Job‑Seeker

*LeetCode’s Knight’s Tour problem is a micro‑version of a classic NP‑hard problem.*  
When you answer an interview question with a **clean** DFS + Warnsdorff heuristic in Java, Python, or C++, you demonstrate:

* **Algorithmic rigour** – you’re not just coding a brute‑force loop.  
* **Cross‑platform fluency** – you can port ideas between languages effortlessly.  
* **Optimised thinking** – you’re aware of the problem’s constraints and use them to write the fastest code possible.

Next time you see “2664 Knight’s Tour” on a recruiter’s screen, walk into the interview confident that you’ve already ticked the boxes recruiters are looking for. And if they throw a bigger board or parallelism at you – you’ve got a solid foundation to explain the necessary trade‑offs.

---

### Final Thought

> **“Algorithms are puzzles.”**  
> Knight’s Tour is a puzzle that is *solved* by the right algorithm, *verified* by the output, and *valued* by recruiters. Master the DFS skeleton, sprinkle in Warnsdorff’s heuristic, and you’ll have a solution that is as elegant as a winning chess move.

Happy coding and happy interviewing!