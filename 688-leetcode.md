---
title: LeetCode 688. Knight Probability in Chessboard - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Three‑Language Solutions for “Knight Probability on a Chessboard”

The LeetCode problem **688 – Knight Probability in Chessboard** asks you to compute the probability that a knight remains on an *n × n* board after performing exactly *k* moves, starting from the cell `(row, column)`.  
The knight chooses one of its 8 possible moves uniformly at random – even if the chosen move would land it off the board.  
When it leaves the board the process stops; otherwise it continues until it has made *k* moves.

The most common, time‑efficient approach is **dynamic programming** (DP).  
Below you will find clean, self‑contained implementations in **Java, Python, and C++**.  
All three share the same algorithmic idea:

1. **DP state** – `dp[i][j]` is the probability that the knight is on square `(i, j)` after a given number of moves.
2. **Transition** – for every square and every legal knight move, add `dp[prev] / 8.0` to the new probability grid.
3. **Iteration** – repeat the transition exactly *k* times.
4. **Answer** – sum all probabilities in the final grid (the knight could be anywhere on the board after *k* moves).

The runtime is **O(k · n²)** and the memory usage is **O(n²)** – optimal for the given constraints (n ≤ 25, k ≤ 100).

---

### 1.1  Java 17

```java
import java.util.*;

public class Solution {
    // All 8 knight moves
    private static final int[][] MOVES = {
            {-2, -1}, {-2,  1}, {-1, -2}, {-1,  2},
            { 1, -2}, { 1,  2}, { 2, -1}, { 2,  1}
    };

    public double knightProbability(int n, int k, int row, int column) {
        double[][] dp = new double[n][n];
        dp[row][column] = 1.0;          // start position

        for (int move = 0; move < k; move++) {
            double[][] next = new double[n][n];
            for (int r = 0; r < n; r++) {
                for (int c = 0; c < n; c++) {
                    if (dp[r][c] == 0) continue;    // nothing to spread
                    for (int[] m : MOVES) {
                        int nr = r + m[0];
                        int nc = c + m[1];
                        if (isValid(nr, nc, n)) {
                            next[nr][nc] += dp[r][c] / 8.0;
                        }
                    }
                }
            }
            dp = next;
        }

        double total = 0.0;
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) total += dp[r][c];
        }
        return total;
    }

    private boolean isValid(int r, int c, int n) {
        return r >= 0 && r < n && c >= 0 && c < n;
    }
}
```

---

### 1.2  Python 3

```python
from typing import List

class Solution:
    # 8 possible knight moves
    MOVES: List[List[int]] = [
        (-2, -1), (-2, 1), (-1, -2), (-1, 2),
        (1, -2),  (1, 2),  (2, -1), (2, 1)
    ]

    def knightProbability(self, n: int, k: int, row: int, col: int) -> float:
        dp = [[0.0] * n for _ in range(n)]
        dp[row][col] = 1.0

        for _ in range(k):
            next_dp = [[0.0] * n for _ in range(n)]
            for r in range(n):
                for c in range(n):
                    if dp[r][c] == 0:
                        continue
                    for dr, dc in self.MOVES:
                        nr, nc = r + dr, c + dc
                        if 0 <= nr < n and 0 <= nc < n:
                            next_dp[nr][nc] += dp[r][c] / 8.0
            dp = next_dp

        return sum(map(sum, dp))
```

---

### 1.3  C++17

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    double knightProbability(int n, int k, int row, int column) {
        const int moves[8][2] = {
            {-2, -1}, {-2,  1}, {-1, -2}, {-1,  2},
            { 1, -2}, { 1,  2}, { 2, -1}, { 2,  1}
        };

        vector<vector<double>> dp(n, vector<double>(n, 0.0));
        dp[row][column] = 1.0;

        for (int step = 0; step < k; ++step) {
            vector<vector<double>> next(n, vector<double>(n, 0.0));
            for (int r = 0; r < n; ++r) {
                for (int c = 0; c < n; ++c) {
                    if (dp[r][c] == 0) continue;
                    for (auto &m : moves) {
                        int nr = r + m[0];
                        int nc = c + m[1];
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n) {
                            next[nr][nc] += dp[r][c] / 8.0;
                        }
                    }
                }
            }
            dp.swap(next);
        }

        double result = 0.0;
        for (auto &row : dp)
            for (double v : row) result += v;
        return result;
    }
};
```

---

## 2.  Blog Article: *Knight Probability on a Chessboard – The Good, The Bad, and The Ugly*

### 2.1  Why This Problem Rocks (and Why It’s a Great Interview Topic)

- **Mathematical Flair** – It blends combinatorics, probability, and graph theory in a tangible, visual way.  
- **Multiple Solution Paths** – Brute‑force recursion, memoization, iterative DP, and even matrix exponentiation.  
- **Scalable Constraints** – The solution must be efficient yet readable; a perfect playground to demonstrate algorithmic trade‑offs.  

If you’re applying for software‑engineering roles that emphasize algorithmic thinking (Google, Amazon, Facebook, etc.), mastering this problem shows you can:

- Translate a natural‑language description into formal states.
- Optimize nested loops.
- Balance time‑space trade‑offs.

### 2.2  The Good – The Classic DP Solution

**What It Does:**  
The DP array keeps track of *how many ways* (or equivalently, *what probability*) the knight can reach each board cell after a certain number of moves.

**Why It’s Good:**

| Feature | Explanation |
|---------|-------------|
| **Time Complexity** | `O(k · n²)` – linear in the number of moves and board cells. |
| **Space Complexity** | `O(n²)` – only two `n × n` grids are needed. |
| **Simplicity** | Clear state transition; easy to debug. |
| **Extensibility** | Adding more moves (e.g., a “super knight”) is a trivial change. |

**Sample Code (Java)** – see above.  

---

### 2.3  The Bad – A Naïve Recursive Approach

**What It Looks Like:**  
A depth‑first search exploring all 8ⁿ possible move sequences, pruning when a move leaves the board.

**Why It’s Bad:**

- **Exponential Blow‑up** – Even for `k = 10`, there are 8¹⁰ ≈ 1,073,741,824 paths.  
- **Redundant Work** – Many sub‑problems are recomputed (e.g., reaching the same cell after the same number of moves).  
- **Stack Overflow Risk** – Deep recursion may hit the call‑stack limit for larger *k*.  

**Mitigation:**  
Use memoization to store the probability of reaching a cell at a given step. That essentially turns recursion into DP.  

---

### 2.4  The Ugly – Over‑Optimizing with a 3‑D Array or Matrix Power

While you can pre‑compute transition probabilities using a 3‑D array or treat the board as a graph and use matrix exponentiation (`Aᵏ`), this “ugly” level of optimization is rarely needed in interviews:

- **3‑D DP** (`dp[step][i][j]`) gives a clean formulation but increases memory to `O(k · n²)` (not great for `k = 100`).  
- **Matrix Exponentiation** reduces the runtime to `O(log k · (n²)³)` but requires a lot of boilerplate code and a deep understanding of linear algebra.  
- **Readability Hit** – Interviewers often penalize solutions that look clever but are hard to follow.  

Use these advanced tricks only if you’re writing a production‑grade library or tackling a *theoretical* follow‑up.

---

### 2.5  SEO Tips – Make Your Blog Stand Out

| SEO Element | How It Helps |
|-------------|--------------|
| **Title** – *“Knight Probability on a Chessboard: DP, Recursion, & Matrix Power Explained”* | Captures the keyword “Knight Probability” + “DP” |
| **Meta Description** – 155‑character snippet that contains the question ID and key terms. | Improves click‑through rate on search results. |
| **Headers** – H1, H2, H3 with keywords. | Search engines parse content hierarchy. |
| **Code Snippets** – Each language wrapped in `<pre><code>` tags. | Easier for readers (and bots) to parse. |
| **Anchor Links** – “#the-good”, “#the-bad”, “#the-ugly” | Improves navigation and SEO. |
| **Internal/External Links** – Link to the LeetCode page, similar problems (e.g., “Super Jumping Game”). | Signals relevance. |

---

### 2.6  Putting It All Together – A Job‑Ready Narrative

When you’re in an interview, describe the problem as:

1. **State Definition** – “Let’s track the probability of the knight being on each cell after *t* moves.”  
2. **Base Case** – “Initially, the probability is 1.0 at the starting cell and 0 elsewhere.”  
3. **Transition** – “Each move distributes the current probability to the 8 destinations, divided by 8.”  
4. **Iteration** – “Repeat the transition *k* times.”  
5. **Result** – “Sum the final grid.”  

Add a quick sanity check (e.g., `n=8, k=1, row=0, col=0` → probability ≈ 0.125).  
This demonstrates both correctness and a depth of thought.

---

### 2.7  Take‑away for Job Seekers

1. **Show the Math, Then the Code** – Explain the DP logic before diving into the implementation.  
2. **Highlight Trade‑offs** – Mention why brute force fails and how DP solves it.  
3. **Keep It Clean** – Use constants for moves, guard clauses, and clear variable names.  
4. **Talk About Variants** – Mention that the same DP framework can solve *Super Knight* (10‑move) or *Hexa‑Knight* (5‑move) variants.  
5. **Link Back to Your Portfolio** – If you host your code on GitHub, include a link (e.g., `https://github.com/your‑repo/knight-probability`).

**Result:** Interviewers will see that you can:

- **Interpret** the problem statement,
- **Choose** an appropriate algorithm,
- **Implement** it in multiple languages,
- **Explain** the reasoning—exactly the skill set they’re hunting for.

---

## 3.  Quick Checklist for Your Interview

| Item | Status | Why it matters |
|------|--------|----------------|
| ✅  Understand board as a graph | Helps in state modeling |
| ✅  Master the 8 knight moves | Core of DP transition |
| ✅  Write iterative DP | Most interview‑grade solution |
| ✅  Mention memoization vs brute force | Shows awareness of redundancy |
| ✅  Discuss time/space trade‑offs | Demonstrates optimization mindset |
| ✅  Optional: Matrix exponentiation | Bonus for theoretical roles |

Happy coding, and good luck on your next algorithm interview! 🚀