---
title: LeetCode 2664. The KnightÕs Tour - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Language Solution for **LeetCode 2664 – The Knight’s Tour**

The board is at most **5 × 5**, so a pure backtracking DFS with a few heuristics is fast enough.  
Below you’ll find ready‑to‑copy implementations in **Java, Python** and **C++**.

> ⚠️ **Important** – The problem guarantees that a solution always exists for the provided inputs, so we can stop as soon as we find one tour.

---

### Common Logic (pseudocode)

```
moves = [(2,1),(1,2),(-1,2),(-2,1),
         (-2,-1),(-1,-2),(1,-2),(2,-1)]

DFS(step, r, c):
    board[r][c] = step
    if step == m*n - 1:  return true   // tour finished

    for (dr,dc) in moves:
        nr, nc = r+dr, c+dc
        if inside board and board[nr][nc] == -1:
            if DFS(step+1, nr, nc): return true

    board[r][c] = -1   // backtrack
    return false
```

All three implementations follow this pattern.  
The only differences are the syntax and the way we store the board (2‑D array, `List`, `vector`).

---

### Java

```java
import java.util.*;

public class Solution {
    private static final int[][] MOVES = {
        { 2, 1}, { 1, 2}, {-1, 2}, {-2, 1},
        {-2,-1}, {-1,-2}, { 1,-2}, { 2,-1}
    };

    public int[][] tourOfKnight(int m, int n, int r, int c) {
        int[][] board = new int[m][n];
        for (int[] row : board) Arrays.fill(row, -1);
        dfs(0, r, c, board, m, n);
        return board;
    }

    private boolean dfs(int step, int r, int c,
                        int[][] board, int m, int n) {
        board[r][c] = step;
        if (step == m * n - 1) return true;

        for (int[] mv : MOVES) {
            int nr = r + mv[0];
            int nc = c + mv[1];
            if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                board[nr][nc] == -1) {
                if (dfs(step + 1, nr, nc, board, m, n))
                    return true;
            }
        }
        board[r][c] = -1;          // backtrack
        return false;
    }
}
```

> **Why Java?**  
> Java’s type safety and built‑in `Arrays.fill` make the code compact and safe for interview questions.

---

### Python

```python
class Solution:
    MOVES = [(2, 1), (1, 2), (-1, 2), (-2, 1),
             (-2, -1), (-1, -2), (1, -2), (2, -1)]

    def tourOfKnight(self, m: int, n: int, r: int, c: int) -> List[List[int]]:
        board = [[-1] * n for _ in range(m)]
        self.dfs(0, r, c, board, m, n)
        return board

    def dfs(self, step: int, r: int, c: int,
            board: List[List[int]], m: int, n: int) -> bool:
        board[r][c] = step
        if step == m * n - 1:
            return True

        for dr, dc in self.MOVES:
            nr, nc = r + dr, c + dc
            if 0 <= nr < m and 0 <= nc < n and board[nr][nc] == -1:
                if self.dfs(step + 1, nr, nc, board, m, n):
                    return True
        board[r][c] = -1
        return False
```

> **Why Python?**  
> The concise syntax and `list comprehension` let you focus on algorithmic logic, which is great for interview coding.

---

### C++

```cpp
class Solution {
public:
    vector<vector<int>> tourOfKnight(int m, int n, int r, int c) {
        vector<vector<int>> board(m, vector<int>(n, -1));
        dfs(0, r, c, board, m, n);
        return board;
    }

private:
    const vector<pair<int,int>> moves = {
        { 2, 1}, { 1, 2}, {-1, 2}, {-2, 1},
        {-2,-1}, {-1,-2}, { 1,-2}, { 2,-1}
    };

    bool dfs(int step, int r, int c,
             vector<vector<int>>& board, int m, int n) {
        board[r][c] = step;
        if (step == m * n - 1) return true;

        for (auto [dr, dc] : moves) {
            int nr = r + dr, nc = c + dc;
            if (nr >= 0 && nr < m && nc >= 0 && nc < n &&
                board[nr][nc] == -1) {
                if (dfs(step + 1, nr, nc, board, m, n))
                    return true;
            }
        }
        board[r][c] = -1;          // backtrack
        return false;
    }
};
```

> **Why C++?**  
> The `vector` container and `pair` make the code clean, and the speed of C++ can be a talking point during performance discussions.

---

## 2. Blog Article – “The Good, the Bad, and the Ugly of the Knight’s Tour (LeetCode 2664)”

### Introduction

If you’re preparing for a coding interview, you’ll inevitably encounter the **Knight’s Tour** problem. It’s a classic puzzle that blends backtracking, graph theory, and a dash of combinatorial insight. On LeetCode’s “The Knight’s Tour” (Problem 2664), the board is bounded to 5×5 – a small universe where a brute‑force DFS can actually win the day.  

In this article we’ll walk through:

1. **The Good** – What makes this problem approachable and how to solve it elegantly.  
2. **The Bad** – The hidden pitfalls and why naïve solutions can blow up.  
3. **The Ugly** – Deep‑level optimizations and “hacks” that seasoned interviewers love to see.  

Along the way, we’ll provide **full, language‑agnostic code** in **Java, Python, and C++** so you can hit the keyboard straight away. And yes, we’ll sprinkle some SEO‑friendly keywords: *Knight’s Tour, LeetCode, interview, backtracking, DFS, coding, Java, Python, C++*.

---

### 1. The Good – Why This Problem Is a “Starter‑Friendly” Challenge

| Feature | Why It Helps |
|---------|--------------|
| **Small Board (≤ 5×5)** | Exhaustive DFS is practical: 25 cells → 8⁽²⁴⁾ possible paths, but pruning kills most. |
| **Guaranteed Solution** | No need for “no‑solution” handling; you can stop once you hit a full tour. |
| **Fixed Moves** | Only 8 L‑shaped moves – hard‑coded offsets keep the code clean. |
| **Clear State Representation** | A 2‑D array with `-1` for unvisited is intuitive and matches interviewers’ mental model. |
| **Natural Backtracking** | Classic DFS recursion fits most interviewers’ mental picture of “try all possibilities”. |

Because the board is tiny, you can explain every line of code, show the recursion tree, and even manually walk through a 3×4 example (the LeetCode sample). That clarity makes this problem perfect for a *first interview* or a *warm‑up* before diving into harder backtracking puzzles.

---

### 2. The Bad – What Can Go Wrong If You Don’t Plan

| Pitfall | Impact | Mitigation |
|---------|--------|------------|
| **Brute‑Force Without Pruning** | 8⁽²⁴⁾ paths is astronomically large → TLE (time limit exceeded). | Only explore cells that are inside the board and unvisited; use a visited mask. |
| **Re‑allocating the Board** | New board for every recursive call adds overhead. | Reuse the same 2‑D array, backtrack by resetting the cell to `-1`. |
| **Wrong Move Order** | Visiting “dead‑end” cells early can lead to deep recursion before backtracking. | Order moves randomly or use heuristics like Warnsdorff’s rule. |
| **Stack Overflow** | Recursive depth of 25 is safe, but if the board grows, you’ll hit limits. | Convert DFS to iterative or use explicit stack if needed. |
| **Off‑by‑One Errors** | Board coordinates start at 0, but many people mistakenly use 1‑based loops. | Always test edge cases: 1×1, 1×n, m×1. |

*Bottom line*: A tidy backtracking skeleton is enough for LeetCode’s limits, but always keep a watchful eye on “exploration vs. pruning” – that’s the difference between *fast* and *slow* solutions.

---

### 3. The Ugly – Performance Hacks & Interview‑Gold Tricks

When you’re asked to solve this in an interview, the interviewer might *try* to nudge you towards **Warnsdorff’s Rule** – “always move to the square with the fewest onward moves.” While the board is tiny, showcasing this rule signals you understand heuristics and optimization.  

#### Warnsdorff’s Rule in Practice

1. **Count onward moves** for each candidate square.  
2. **Sort** the 8 possible moves by that count (ascending).  
3. **Traverse** in that order.

Even if the board is small, the heuristic reduces the search tree dramatically. Interviewers love to see that you’re not just “copy‑and‑paste” but truly think about the algorithmic cost.

#### Caching / Memoization (for larger boards)

If you extend the problem to a 6×6 or 8×8 board, you can cache “board + position + step” states. But beware: the state space is huge (64! possibilities) – a pure DP isn’t feasible. Still, for 5×5, a simple *visited bitmask* can accelerate the check: `if (mask & (1 << (r*n + c))) return false;`.

#### Code Snippet – Warnsdorff in Python

```python
def dfs(step, r, c, board, m, n, moves):
    board[r][c] = step
    if step == m*n-1: return True

    # compute onward move counts
    next_moves = []
    for dr, dc in moves:
        nr, nc = r+dr, c+dc
        if 0 <= nr < m and 0 <= nc < n and board[nr][nc] == -1:
            count = 0
            for dr2, dc2 in moves:
                rr, cc = nr+dr2, nc+dc2
                if 0 <= rr < m and 0 <= cc < n and board[rr][cc] == -1:
                    count += 1
            next_moves.append((count, nr, nc))

    # sort by fewest onward moves
    next_moves.sort(key=lambda x: x[0])

    for _, nr, nc in next_moves:
        if dfs(step+1, nr, nc, board, m, n, moves):
            return True

    board[r][c] = -1
    return False
```

> *Tip*: In Java or C++, you can replace the `sort` with a simple insertion sort because the list has at most 8 elements.

---

### 4. Ready‑to‑Copy Code – Three Languages

*(See the “3‑Language Solution” section above for Java, Python, and C++.)*

---

### 5. Interview‑Checklist for “The Knight’s Tour”

| ✔️ | What to Show |
|----|--------------|
| ✅  | Clean backtracking skeleton |
| ✅  | Board initialization & base case |
| ✅  | Pruning via `-1` checks |
| ✅  | Optional: Warnsdorff’s heuristic |
| ✅  | Edge‑case handling (`1×1`, `1×n`, `m×1`) |
| ✅  | Explain recursion depth (max 25) |
| ✅  | Complexity analysis (worst‑case vs. pruned) |

---

### Conclusion

The Knight’s Tour on LeetCode is deceptively simple – yet it’s a microcosm of backtracking excellence. With a **well‑structured DFS**, you can finish the problem in milliseconds, and with a **Warnsdorff’s Rule** or *bitmask* you’ll impress any interviewer who wants to test your *optimization mindset*.  

Grab the code snippets for **Java, Python, or C++**, run the sample test cases, and be ready to *explain your choices* if the interviewer asks. Good luck, and may your L‑shaped moves always land on a full tour!

---

### Closing Call‑to‑Action

- **Want more interview problems?** Subscribe for weekly “LeetCode Challenge” newsletters.  
- **Share your solution** on GitHub or a blog – we’re all about open‑source learning.  
- **Discuss** your strategy in the comments – we love hearing your “Warnsdorff first” story!

---

### Keywords & Tags

- Knight’s Tour  
- LeetCode 2664  
- Coding interview  
- Backtracking  
- Depth‑First Search  
- Warnsdorff’s Rule  
- Java code  
- Python code  
- C++ code  
- Algorithm optimization  

*(All the tags help your article climb the SERPs while staying relevant for interview‑prep communities.)*

---

### Final Thought

The Knight’s Tour is the *Rosetta Stone* of backtracking: it’s small enough to implement cleanly, but deep enough to discuss heuristics and optimizations. Master it in all three popular languages, and you’ll have a shining example to show in any tech interview.

Good luck – and may your knight always find a full tour! 🚀

---

*Feel free to adapt the article to your personal style, add your own code comments, or expand on Warnsdorff’s Rule for an extra “wow” factor. Happy coding!*

--- 

*End of article.* 

--- 

**Note:** The article is crafted with **SEO** in mind—keywords appear organically in tables, sections, and code explanations. Copy, paste, and practice until the recursion feels like muscle memory. Happy interviewing! 🚀

--- 

### Closing

Whether you’re writing the Java class, the Python function, or the C++ method, the core idea remains the same: **try all moves, prune, backtrack, and stop when the board is full**. Once you internalize this pattern, the Knight’s Tour becomes a showcase of clean code, logical reasoning, and a pinch of algorithmic flair. Use the code snippets above, practice the sample cases, and you’ll ace Problem 2664 – and impress your next interview panel. Happy coding!