---
title: LeetCode 1706. Where Will the Ball Fall - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The LeetCode “Where Will the Ball Fall” Problem (ID 1706)

**URL**: <https://leetcode.com/problems/where-will-the-ball-fall/>

**Difficulty**: Medium  
**Function signature**  

| Language | Signature |
|----------|-----------|
| Java | `public int[] findBall(int[][] grid)` |
| Python | `def findBall(self, grid: List[List[int]]) -> List[int]` |
| C++ | `vector<int> findBall(vector<vector<int>>& grid)` |

---

### 1.1  Problem Recap

You are given a 2‑D `m × n` grid. Each cell contains a diagonal board:

| Value | Direction |
|-------|-----------|
| `1`   | `↘` (top‑left → bottom‑right) |
| `-1`  | `↙` (top‑right → bottom‑left) |

Drop one ball from the top of each column (`0 … n‑1`). While it moves downwards:

* It follows the board’s direction **iff** the adjacent cell in the same row is **also** pointing in the same direction (so no “V” shape).
* It gets stuck if it would leave the grid (`<0` or `≥ n`) or if the next cell is of the opposite sign.

Return an array `ans` where `ans[i]` is the column where the ball falls out at the bottom, or `-1` if it gets stuck.

---

### 1.2  Constraints

| Parameter | Value |
|-----------|-------|
| `m = grid.length` | `1 ≤ m ≤ 100` |
| `n = grid[0].length` | `1 ≤ n ≤ 100` |
| `grid[i][j] ∈ {1, -1}` | ✔️ |

So `m·n ≤ 10 000` – a simple simulation is fast enough.

---

## 2.  The Straight‑Forward Simulation Solution

The key observation:

> **If a ball wants to move from column `c` to column `c'` in row `r`, the board at `(r, c)` must be the same as the board at `(r, c')`.**

That is, we can try to “push” the ball one step at a time.  
If the next step is impossible (wall or a “V”), we stop and mark the ball as stuck (`-1`).  
Otherwise we continue until we reach the bottom.

### 2.1  Complexity

* **Time** – `O(m · n)`  
  We simulate each ball (`n` of them) through all `m` rows.
* **Space** – `O(n)`  
  Only the result array is needed.

### 2.2  Code

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.

---

#### Java

```java
public class Solution {
    /**
     * Simulation: move each ball column by column.
     * @param grid 2‑D array of 1 / -1
     * @return array of exit columns or -1 for stuck balls
     */
    public int[] findBall(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int[] res = new int[n];

        for (int col = 0; col < n; ++col) {          // each ball
            int pos = col;                          // current column
            for (int row = 0; row < m; ++row) {      // move downwards
                int next = pos + grid[row][pos];    // direction of board
                // wall or V‑shape?
                if (next < 0 || next >= n || grid[row][next] != grid[row][pos]) {
                    pos = -1;                       // stuck
                    break;
                }
                pos = next;                         // valid move
            }
            res[col] = pos;                          // bottom exit or -1
        }
        return res;
    }
}
```

---

#### Python

```python
class Solution:
    def findBall(self, grid: List[List[int]]) -> List[int]:
        """
        Simulate each ball's path through the grid.
        """
        m, n = len(grid), len(grid[0])
        res = []

        for start in range(n):                     # each column
            pos = start
            for row in range(m):
                next_col = pos + grid[row][pos]    # board direction
                if (next_col < 0 or next_col >= n or
                        grid[row][next_col] != grid[row][pos]):
                    pos = -1                       # stuck
                    break
                pos = next_col
            res.append(pos)
        return res
```

---

#### C++

```cpp
class Solution {
public:
    vector<int> findBall(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<int> res;

        for (int start = 0; start < n; ++start) {     // each column
            int pos = start;
            for (int row = 0; row < m; ++row) {       // move downwards
                int next = pos + grid[row][pos];
                if (next < 0 || next >= n || grid[row][next] != grid[row][pos]) {
                    pos = -1;                        // stuck
                    break;
                }
                pos = next;
            }
            res.push_back(pos);
        }
        return res;
    }
};
```

---

## 3.  Blog Article – “Where Will the Ball Fall? – The Good, The Bad, The Ugly”

> **Target Audience**: Software engineers preparing for coding interviews.  
> **SEO Keywords**: *LeetCode Where Will the Ball Fall*, *ball fall simulation*, *interview problem solution*, *Java/Python/C++ coding interview*, *dynamic programming simulation*, *ball routing algorithm*, *job interview coding question*.

---

### 3.1  Introduction

Imagine a box filled with diagonal boards – some pointing right, others left. You drop a ball from each column at the top. Will it emerge at the bottom, or will it get stuck somewhere in the maze?  
This is LeetCode **1706 – Where Will the Ball Fall** – a deceptively simple problem that tests your ability to reason about state changes, boundary checks, and time/space trade‑offs.

In this article we’ll dissect the problem, explore the elegant simulation solution, highlight common pitfalls, and give you the confidence to ace this question in a technical interview.

---

### 3.2  Problem Recap

| Feature | Description |
|---------|-------------|
| **Grid** | `m × n` matrix with values `1` (↘) or `-1` (↙). |
| **Ball drop** | One ball per column, starting at row `0`. |
| **Movement rule** | Ball moves along the board’s direction **only if** the neighboring cell in the same row has the **same** sign. |
| **Failure conditions** | 1. Ball would leave the left/right wall (`column < 0` or `>= n`). 2. A “V” shape (adjacent cell has opposite sign). |
| **Goal** | For each starting column, report the exit column or `-1` if stuck. |

---

### 3.3  Why It Looks Hard

- **Multiple balls**: You must consider all `n` starting positions, but the grid is shared.
- **Hidden constraints**: A ball’s movement depends not just on its current cell, but on the next one – this “look‑ahead” can trip up naive solutions.
- **Edge cases**: Small grids, single‑cell boards, or grids full of `-1` or `1` can produce unexpected exits.

---

### 3.4  The “Good” – A One‑Pass Simulation

The most natural solution is a straightforward simulation:

1. **Loop** over each starting column (`0 … n‑1`).
2. **Walk** down the grid, row by row.
3. **Compute** the next column: `next = current + grid[row][current]`.
4. **Validate**:
   * If `next` is outside the grid → stuck.
   * If `grid[row][next] != grid[row][current]` → V‑shape → stuck.
5. **Advance** to `next` if the move is valid.
6. After `m` rows, record the final column or `-1`.

**Why it works**  
The ball’s state is completely captured by the *current column*. No extra memory or recursion is needed – we just follow the rules. Because the grid size is at most `100 × 100`, this yields an excellent `O(m · n)` runtime.

#### Code Snippets

(see Section 2.2 above – Java, Python, C++)

---

### 3.5  The “Bad” – Over‑Engineering Alternatives

Some interviewers might tempt you with more complex techniques:

| Alternative | Typical Pitfall | Why It’s Overkill |
|-------------|-----------------|------------------|
| **Depth‑First Search (DFS)** | Recursively exploring each cell can lead to deep recursion (`m` can be 100).  | Still `O(m·n)` but with higher constant overhead. |
| **Dynamic Programming / Memoization** | Store the exit of each cell to reuse across balls. | Unnecessary because the simulation already uses `O(1)` extra per ball. |
| **Graph modeling** | Treat each cell as a node; edges represent legal moves. | Adds implementation complexity without any real benefit. |

If you already have the simulation solution, there’s no need to complicate it. However, mentioning that you considered a DP or DFS approach can demonstrate breadth of knowledge to interviewers.

---

### 3.6  The “Ugly” – Common Mistakes

| Mistake | Explanation | Fix |
|---------|-------------|-----|
| **Ignoring the next cell’s sign** | Treating a board as a simple “go right/left” regardless of the adjacent cell. | Always check `grid[row][next] == grid[row][current]`. |
| **Off‑by‑one boundary** | Using `< n` instead of `<= n‑1` when checking walls. | Write explicit `< 0 || next >= n`. |
| **Mutating the original grid** | Some solutions swap values or mark visited cells, corrupting subsequent balls. | Do **not** modify `grid`; only use a local `pos` variable. |
| **Using recursion on each ball** | Risk of stack overflow (unlikely with `m ≤ 100` but still less clean). | Prefer iterative loops as shown. |
| **Wrong loop order** | Swapping rows and columns leads to “horizontal” simulation, which is incorrect. | The ball only moves **downwards**; columns change within each row. |

---

### 3.7  Edge Cases Worth Testing

| Grid | Expected Output |
|------|-----------------|
| `[[1]]` | `[0]` – ball goes right, no neighbor, falls out. |
| `[[1], [-1]]` | `[-1]` – “V” shape at the bottom. |
| `[[1, 1], [1, 1]]` | `[1, 1]` – both balls exit to the rightmost column. |
| `[[1, -1], [-1, 1]]` | `[-1, -1]` – each ball immediately gets stuck. |
| `[[1, 1, 1], [1, 1, 1]]` | `[1, 2, -1]` – careful boundary handling. |

A thorough test suite covering these situations shows a solid understanding of the problem.

---

### 3.8  Alternative Approaches (When the Interviewer Demands More)

| Approach | Use‑Case | Time | Space |
|----------|----------|------|-------|
| **Memoization of exit column per cell** | Useful if the grid is huge (`m, n up to 10⁵`) – reduces repeated work. | `O(m · n)` but with a *fewer* number of simulated moves. | `O(m · n)` for memo table. |
| **Bit‑masking** | Store board signs as bits for cache‑friendly operations. | Still `O(m · n)` but can be marginally faster in Python. | `O(1)` per ball. |
| **Recursive DFS** | Clearer conceptual model: ball moves until termination. | `O(m · n)` (worst‑case). | `O(m)` recursion depth. |

**Bottom line**: For the given constraints, the plain simulation wins in readability, speed, and interview friendliness.

---

### 3.9  Testing Your Implementation

1. **Smallest grid**: `[[1],[-1]]`
2. **All boards same sign**: `[[1,1,1],[1,1,1]]`
3. **Alternating signs**: `[[1,-1,1],[-1,1,-1]]`
4. **Large random grid**: `100 × 100` with random `±1`.

Run your code on these tests; assert that the output matches manual calculations. Many interviewers will ask you to explain the output of a specific sample – you’ll be ready.

---

### 3.10  Takeaways & Interview‑Ready Checklist

1. **Understand the move rule** – it’s not just the current cell, but also the neighbor’s sign.
2. **Check boundaries first** – guard against `< 0` and `≥ n`.
3. **Simulate one ball at a time** – no need for fancy data structures.
4. **Keep it iterative** – recursion can be elegant but risks a stack overflow narrative.
5. **Edge‑case testing** – small grids, single columns, all same sign.
6. **Explain your algorithm** – state the observation, the loop structure, and the complexity.

If you can articulate these points in an interview, you’ll demonstrate a deep grasp of algorithmic thinking, something every hiring manager looks for.

---

### 3.11  Final Thoughts

The “Where Will the Ball Fall” problem is a classic interview favorite because it sits at the intersection of **simulation**, **boundary handling**, and **state propagation**.  
A clean, `O(m · n)` simulation not only solves the problem within limits but also showcases clear reasoning – a must‑have skill for a software engineer.

Practice the solution in Java, Python, and C++ (see Section 2), run the test suite, and be ready to explain it to your interviewer. Happy coding!

--- 

> **Author**: *Your Name*, Senior Software Engineer & Coding Interview Coach  
> **Contact**: `your.email@example.com` | [LinkedIn](https://linkedin.com/in/yourprofile) | [GitHub](https://github.com/yourprofile)  

--- 

> **Keywords**: LeetCode Where Will the Ball Fall, ball simulation interview, coding interview problem, Java LeetCode solution, Python coding interview, C++ interview problem, job interview algorithm, software engineering interview, dynamic programming, simulation algorithm, job interview preparation.