---
title: LeetCode 1706. Where Will the Ball Fall - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 1706 – “Where Will the Ball Fall?”  
## A Complete, SEO‑Optimized Guide (Java / Python / C++)  
> **Want to ace your next coding interview?**  
> Read how to solve LeetCode 1706, understand the pitfalls, and learn how to talk about it in a *STAR* interview format.

---

## 1. Problem Recap (SEO keywords: *LeetCode 1706*, *ball simulation*, *grid problem*)

You’re given an `m x n` 2‑D array `grid`.  
Each cell contains either `1` (↘️ diagonal) or `-1` (↙️ diagonal).  

A ball is dropped at the top of each column.  
While moving downwards:

* If a ball encounters a **V‑shaped pair** (`1` next to `-1`), it gets stuck.  
* If a ball hits a wall (left or right border) due to the board’s direction, it also gets stuck.  

Return an array `answer` of size `n` where `answer[i]` is the column where the ball falls out at the bottom, or `-1` if it gets stuck.

---

## 2. Intuition & Key Observation

> **Observation** – *A ball can only move from column `c` to column `c+grid[row][c]` if the board at the next column points in the same direction.*

In plain English:  
If a cell at `(row, c)` is `1`, the ball will try to move right to column `c+1`.  
But the ball will actually move **only if the cell `(row, c+1)` is also `1`**.  
The same rule applies for `-1` (moving left).  

This simple rule lets us simulate the path of each ball in **O(m)** time, where `m` is the number of rows.

---

## 3. Brute‑Force DFS vs Iterative Simulation

| Approach | Pros | Cons |
|----------|------|------|
| DFS (recursive) | Intuitive, natural recursion | Risk of stack overflow for large grids (m ≤ 100 → fine, but bad style) |
| Iterative simulation | O(1) space per ball, no recursion | Slightly more boilerplate, but clear |

**We’ll use the iterative simulation** because it’s faster, safer, and easier to explain in an interview.

---

## 4. The Solution – Step by Step

1. **Result array** – `int[] answer = new int[n];`.
2. **Loop over each starting column** `startCol`.
3. **Simulate the ball**:  
   * `col = startCol;`  
   * For each `row` from `0` to `m-1`:
     * `nextCol = col + grid[row][col];` (the direction of the current board)
     * **Boundary check**: if `nextCol` is outside `[0, n-1]`, the ball is stuck → `answer[startCol] = -1`.
     * **V‑shape check**: if `grid[row][nextCol] != grid[row][col]`, the ball is stuck → `answer[startCol] = -1`.
     * Otherwise, set `col = nextCol` and continue to the next row.
4. If the loop finishes, the ball has exited → `answer[startCol] = col`.

---

## 5. Code Implementation

### 5.1 Java

```java
/**
 * LeetCode 1706 – Where Will the Ball Fall
 *
 * Iterative simulation – O(m * n) time, O(1) extra space.
 */
class Solution {
    public int[] findBall(int[][] grid) {
        int m = grid.length;          // number of rows
        int n = grid[0].length;       // number of columns
        int[] result = new int[n];

        for (int start = 0; start < n; start++) {
            int col = start;          // current column of the ball

            for (int row = 0; row < m; row++) {
                int nextCol = col + grid[row][col];          // target column

                // Stuck if we go out of bounds
                if (nextCol < 0 || nextCol >= n) {
                    col = -1;
                    break;
                }

                // Stuck if we hit a V‑shape
                if (grid[row][nextCol] != grid[row][col]) {
                    col = -1;
                    break;
                }

                col = nextCol;   // move ball to next column
            }

            result[start] = col;   // final column (or -1 if stuck)
        }
        return result;
    }
}
```

### 5.2 Python

```python
class Solution:
    """
    LeetCode 1706 – Where Will the Ball Fall
    Time:  O(m * n)
    Space: O(1) per ball (result array is O(n))
    """

    def findBall(self, grid: List[List[int]]) -> List[int]:
        m, n = len(grid), len(grid[0])
        res = []

        for start in range(n):
            col = start
            for row in range(m):
                next_col = col + grid[row][col]
                # Out of bounds
                if next_col < 0 or next_col >= n:
                    col = -1
                    break
                # V‑shape detection
                if grid[row][next_col] != grid[row][col]:
                    col = -1
                    break
                col = next_col
            res.append(col)
        return res
```

### 5.3 C++

```cpp
/**
 * LeetCode 1706 – Where Will the Ball Fall
 *
 * Time:  O(m * n)
 * Space: O(1) extra space
 */
class Solution {
public:
    vector<int> findBall(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<int> answer(n);

        for (int start = 0; start < n; ++start) {
            int col = start;          // current column of the ball

            for (int row = 0; row < m; ++row) {
                int nextCol = col + grid[row][col];

                // Ball gets stuck at the wall
                if (nextCol < 0 || nextCol >= n) {
                    col = -1;
                    break;
                }

                // Ball gets stuck in a V‑shape
                if (grid[row][nextCol] != grid[row][col]) {
                    col = -1;
                    break;
                }

                col = nextCol;   // advance to the next column
            }

            answer[start] = col;   // final column or -1
        }

        return answer;
    }
};
```

---

## 6. Complexity Analysis

| **Operation** | **Time** | **Space** |
|---------------|----------|-----------|
| Simulating one ball | `O(m)` | `O(1)` |
| All `n` balls | `O(m * n)` | `O(n)` for the output array |

Given the constraints (`1 ≤ m, n ≤ 100`), this solution runs in milliseconds and is well within limits.

---

## 7. Edge Cases to Discuss in Interviews

1. **Single row / single column** – ensures the ball always exits or immediately gets stuck.  
2. **All `1` or all `-1` grids** – the ball will simply move straight right or left until the wall.  
3. **Alternating pattern `1,-1,1,-1…`** – every ball gets stuck because of V‑shapes.  
4. **Large consecutive `1` or `-1` segments** – the ball can move many columns in one row; the next‑cell check guarantees correctness.  

Show the test case in the article to reinforce that the code handles all of them.

---

## 8. “Good / Bad / Ugly” – Interview‑Friendly Discussion

| Category | What I’d Highlight |
|----------|--------------------|
| **Good** | • **Simplicity** – The simulation is a one‑liner in each language. <br>• **No recursion** – Avoids stack overflow. <br>• **Predictable memory** – O(1) per ball, easy to explain. |
| **Bad** | • **Quadratic if coded poorly** – Some people try to simulate every cell for every ball, leading to `O(m*n^2)` (unlikely with `m,n≤100`, but still a bad pattern). |
| **Ugly** | • **DFS recursion** – Works but feels “magical” and may crash on much larger inputs. <br>• **Over‑optimization** – Trying to use bit‑mask tricks or fancy mathematics can make the solution unreadable. |

> **Interview Tip** – If a candidate proposes a DFS solution, ask them to explain the stack usage and why an iterative approach is preferable.

---

## 8. Talking About the Problem in an Interview (STAR Format)

| **Situation** | **Task** | **Action** | **Result** |
|---------------|----------|------------|------------|
| “We had an `m x n` grid of diagonals.” | “We needed to know where a ball would exit or if it would get stuck.” | “I simulated the ball for each column, checking for out‑of‑bounds and V‑shapes – an `O(m*n)` algorithm.” | “The algorithm finished in < 1 ms, passed all edge cases, and I explained the complexity clearly.” |

Be ready to answer follow‑up questions such as:

* *Why do we check `grid[row][nextCol] != grid[row][col]`?* – Because a ball only moves if the next board points in the same direction; otherwise we hit a V‑shape.  
* *What if we had 10⁵ rows?* – Discuss using **amortized O(m)** simulation per ball and how you’d pre‑compute reachable columns with DP or memoization.  
* *Can we do better than O(m*n)?* – No, each ball must examine every row at least once; you can only save the `O(n)` output array.

---

## 9. Interview Prep Checklist

1. **Read the problem carefully** – pay attention to “stuck” conditions.  
2. **Explain the key observation** – board direction must match the next cell.  
3. **Show the iterative simulation** – write pseudo‑code on the whiteboard.  
4. **Write code in one language** (Java, Python, or C++) – make it clean, commented, and test‑driven.  
5. **Analyze complexity** – `O(m*n)` time, `O(n)` space.  
6. **Talk through edge cases** – walls, V‑shapes, single‑row grids.  
7. **Answer follow‑ups** – stack safety, alternative approaches.

---

## 10. How This Problem Helps You Land a Job

* **Data‑Structure Skills** – You demonstrate array manipulation and two‑dimensional indexing.  
* **Algorithmic Thinking** – You showcase observation → simulation → implementation.  
* **Coding Style** – Clean, iterative code is a hallmark of a senior engineer.  
* **Interview Conversation** – You can use this problem as a talking point for *System Design* (visualizing the grid) and *Data Structures* (handling 2‑D arrays).  

> **Pro tip:** When your interviewer says *“Could you solve this recursively?”*, start with DFS, then gracefully pivot to the iterative method, explaining why the iterative one is superior.

---

## 11. Conclusion – Final Takeaways

* **LeetCode 1706** is a classic *grid‑ball* simulation problem that tests your ability to reason about direction, boundaries, and failure conditions.  
* The **iterative simulation** is the most robust, efficient, and interview‑friendly solution.  
* By understanding the *good* (simplicity), *bad* (recursion pitfalls), and *ugly* (over‑engineering) aspects, you’ll be able to explain the problem confidently during your next coding interview.

---

### Bonus: Quick Reference Cheat‑Sheet

| Language | Time | Space | Code Snippet |
|----------|------|-------|--------------|
| Java | `O(m*n)` | `O(n)` | <details><summary>Show code</summary>Java code block above</details> |
| Python | `O(m*n)` | `O(n)` | <details><summary>Show code</summary>Python code block above</details> |
| C++ | `O(m*n)` | `O(n)` | <details><summary>Show code</summary>C++ code block above</details> |

---

### Final Word

Mastering **LeetCode 1706 Where Will the Ball Fall** shows you can:

* Translate a real‑world “physics” description into clean, efficient code.  
* Pick the right algorithmic strategy (simulation over DFS).  
* Communicate complexity and edge cases to interviewers.

**Now go practice!** Add this problem to your interview preparation checklist, and you’ll walk into your next technical interview with confidence.

Happy coding! 🚀