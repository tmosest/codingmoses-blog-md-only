---
title: LeetCode 1706. Where Will the Ball Fall - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Where Will the Ball Fall? – LeetCode 1706 – Java / Python / C++ Solutions & Interview‑Ready Blog Post

> **TL;DR** – Drop a ball through a grid of slanted boards.  
> **Time** = O(m × n) | **Space** = O(n)  
> Code is ready‑to‑copy for Java, Python, and C++.

---

## 1. Problem Overview  

**LeetCode #1706 – “Where Will the Ball Fall”**

You’re given an **m × n** 2‑D grid that represents a vertical box.  
Each cell contains a diagonal board:

| Value | Direction | Visual | Meaning |
|-------|-----------|--------|---------|
| **1** | ⟶  | ↘︎ | Board goes **right** (top‑left → bottom‑right) |
| **-1**| ⟵ | ↙︎ | Board goes **left** (top‑right → bottom‑left) |

You drop **one ball** into each column of the top row.  
At every step the ball moves down one row:

* If the current board points **right** (`1`), the ball tries to move to column + 1.  
* If the current board points **left** (`-1`), the ball tries to move to column – 1.

The ball gets **stuck** if:

1. It would leave the grid (hit a wall).  
2. It encounters a “V” shape: the current board and the next board in the row point towards each other (`1` then `-1` or vice‑versa).

Return an array `ans` of size `n` where `ans[i]` is the column the ball exits at the bottom or `-1` if it gets stuck.

---

## 2. Why This Problem Is Interview‑Friendly  

| ✅ | ✅ | ✅ |
|---|---|---|
| **Clear input / output** – no hidden tricks. | **Simulate** – tests your ability to translate a verbal description into code. | **Edge‑case aware** – must handle walls, “V” shapes, and small grids. |

Typical interview questions that ask you to “simulate a process” are great for demonstrating:
* Loop invariants
* Boundary checks
* Clean code structure

---

## 3. Brute‑Force vs. Optimal  

| Approach | Complexity | Feasibility (m,n ≤ 100) |
|----------|------------|--------------------------|
| DFS from each start cell, exploring two possibilities | **O(m × n)** but with recursion overhead | Works, but unnecessary recursion. |
| **Iterative simulation per ball** | **O(m × n)** | **Best** – simple, fast, no recursion. |

The iterative simulation is the *gold standard* because each cell is visited once for every ball, which is already optimal.

---

## 4. Algorithm Walk‑Through  

1. **Result array** `res[n]` – holds final column or `-1` per starting ball.  
2. For each starting column `start` in `0 … n-1`:
   * Set `col = start`.  
   * For each row `row` from `0 … m-1`:
     * `nextCol = col + grid[row][col]`  
       (since `grid[row][col]` is `±1`).  
     * **Stuck conditions**:
       * `nextCol < 0` or `nextCol >= n`  → out of bounds.  
       * `grid[row][nextCol] != grid[row][col]` → “V” shape.  
     * If stuck → set `col = -1` and break.  
     * Else → `col = nextCol` and continue to next row.  
3. After the loop, `res[start] = col` (either final column or `-1`).  
4. Return `res`.

The key observation: **A ball can only move to an adjacent column if the two adjacent boards face the same direction**. This keeps the simulation O(1) per cell.

---

## 5. Code Implementations

> All three solutions share identical logic; only syntax differs.

### 5.1 Java

```java
// Java 17 – LeetCode 1706
class Solution {
    public int[] findBall(int[][] grid) {
        int m = grid.length;        // rows
        int n = grid[0].length;     // columns
        int[] res = new int[n];     // answer array

        for (int start = 0; start < n; start++) {
            int col = start;        // current column

            for (int row = 0; row < m; row++) {
                int next = col + grid[row][col];   // try to move

                // out of bounds OR “V” shape
                if (next < 0 || next >= n ||
                    grid[row][next] != grid[row][col]) {
                    col = -1;              // ball stuck
                    break;
                }
                col = next;                // move to next column
            }
            res[start] = col;          // final position
        }
        return res;
    }
}
```

### 5.2 Python

```python
# Python 3 – LeetCode 1706
class Solution:
    def findBall(self, grid: List[List[int]]) -> List[int]:
        m, n = len(grid), len(grid[0])
        ans = []

        for start in range(n):
            col = start
            for row in range(m):
                next_col = col + grid[row][col]
                # stuck if out of bounds or V‑shape
                if next_col < 0 or next_col >= n or grid[row][next_col] != grid[row][col]:
                    col = -1
                    break
                col = next_col
            ans.append(col)
        return ans
```

### 5.3 C++

```cpp
// C++17 – LeetCode 1706
class Solution {
public:
    vector<int> findBall(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<int> res(n);

        for (int start = 0; start < n; ++start) {
            int col = start;
            for (int row = 0; row < m; ++row) {
                int next = col + grid[row][col];
                // out of bounds or V‑shape
                if (next < 0 || next >= n || grid[row][next] != grid[row][col]) {
                    col = -1;
                    break;
                }
                col = next;
            }
            res[start] = col;
        }
        return res;
    }
};
```

All three codes run in **O(m × n)** time and **O(n)** auxiliary space (only the result array).

---

## 6. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | O(m × n) | O(m × n) | O(m × n) |
| Space  | O(n) | O(n) | O(n) |

*The grid itself is part of input, not counted as extra space.*

---

## 7. Common Pitfalls (The “Bad” & “Ugly” Parts)

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Using recursion (DFS)** | Adds stack overhead; risk of stack overflow for large grids. | Use iterative simulation as shown above. |
| **Checking only one direction** | Fails to detect “V” shapes (when left board meets right board). | Compare both `grid[row][col]` and `grid[row][next]`. |
| **Ignoring bounds early** | Mis‑calculating `nextCol` before verifying column validity. | Check bounds *before* accessing `grid[row][next]`. |
| **Mutable input** | Some people modify `grid` during simulation → hard to debug. | Never alter input; operate on local variables. |
| **Mis‑indexed loops** | `start` vs. `m` vs. `n`. | Keep clear comments: `m` = rows, `n` = columns. |

---

## 8. What Makes This Problem “Good”

| Feature | Impact |
|---------|--------|
| **Deterministic movement** | You can reason about each step with a simple rule. |
| **Small input bounds** | You can safely use a nested loop. |
| **Clear failure conditions** | No hidden state; you can write a quick test harness. |

---

## 8. Variations You Might Encounter

1. **Dynamic board values (e.g., `2`, `-2`, …)** – change the move offset accordingly.  
2. **Randomized ball starts (not just top row)** – modify outer loop to start from any row.  
3. **3‑D version** – same idea, just iterate over depth.  

Understanding the *direction‑face‑same* rule generalizes to these variants.

---

## 8. Interview‑Practical Tips

1. **Explain your plan verbally before coding** – shows you understand the problem.  
2. **Show the simulation step** with a small grid on paper – helps the interviewer follow your reasoning.  
3. **Mention the key observation** (`grid[row][next] == grid[row][col]`) – demonstrates you’re looking for an efficient shortcut.  
4. **Ask clarifying questions**: “Can the ball move diagonally multiple cells in a single step?” – often a red‑flag for misinterpretation.  

---

## 8. Practice Resources

| Resource | What to Look For |
|----------|-----------------|
| LeetCode 1706 | Same problem in all languages |
| “Simulation” problems | *Binary Tree inorder traversal*, *Maximum Subarray*, *Trapping Rain Water* |
| InterviewBit “Ball and Grid” | Different board representation |

Keep a notebook of edge cases (grid size 1, all 1’s, alternating 1 & -1) to test against.

---

## 9. Conclusion – Turning Good Into Great

> **Good**: The problem is straightforward, tests simulation skills, and can be solved in a single nested loop.  
> **Bad**: A naïve DFS can lead to unnecessary recursion and stack depth concerns.  
> **Ugly**: Forgetting to check both *out‑of‑bounds* and *V‑shape* conditions can make your solution wrong on 30 % of test cases.

By following the iterative approach above, you’ll produce clean, readable code in Java, Python, or C++—exactly what a hiring manager wants to see in a technical interview.

---

## 10. SEO Snapshot (Meta Tags & Keywords)

```html
<title>LeetCode 1706 Where Will the Ball Fall – Java Python C++ Interview Solution</title>
<meta name="description" content="Master LeetCode 1706 – Where Will the Ball Fall. Read Java, Python, and C++ solutions, time/space analysis, interview tips, and common pitfalls.">
<meta name="keywords" content="LeetCode 1706, Where Will the Ball Fall, Java solution, Python solution, C++ solution, coding interview, job interview, algorithm simulation, ball grid, coding challenge, algorithm explanation">
```

Use headings (`h1`‑`h3`) as shown above; they contain the target keywords naturally. This structure will help search engines index your post under “LeetCode 1706 solutions”.

---

### Takeaway

Drop a ball, simulate its movement, check two simple conditions, and you’re done.  
Copy the code, add your own unit tests, and you’ll be ready to ace this LeetCode question in any interview setting. Happy coding!