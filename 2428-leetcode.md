---
title: LeetCode 2428. Maximum Sum of an Hourglass - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # ✅ 2428. Maximum Sum of an Hourglass – 3‑Way Solutions + SEO‑Optimized Blog

> **Goal** – Solve the LeetCode problem *Maximum Sum of an Hourglass* in **Java**, **Python**, and **C++**.  
> **Bonus** – An SEO‑friendly blog post that explains the “Good, Bad, and Ugly” of the problem, helps you interview, and improves your online presence.

---

## 1. The Code

> **All three implementations run in O(m × n) time and O(1) auxiliary space** – perfect for interview constraints.

### 1.1 Java

```java
// 2428. Maximum Sum of an Hourglass – Java (O(m*n) time, O(1) space)
class Solution {
    public int maxSum(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        int best = 0;                       // All values are non‑negative
        for (int i = 1; i < m - 1; i++) {   // Middle row of hourglass
            for (int j = 1; j < n - 1; j++) { // Middle column
                int sum = grid[i-1][j-1] + grid[i-1][j] + grid[i-1][j+1]
                        + grid[i][j]
                        + grid[i+1][j-1] + grid[i+1][j] + grid[i+1][j+1];
                best = Math.max(best, sum);
            }
        }
        return best;
    }
}
```

> *Why it works*: We iterate over every possible “center” of an hourglass (i.e., the middle element). The hourglass is always 3×3, so the sum is a fixed pattern. No extra data structures needed.

### 1.2 Python

```python
# 2428. Maximum Sum of an Hourglass – Python (O(m*n) time, O(1) space)
class Solution:
    def maxSum(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        best = 0
        for i in range(1, m - 1):
            for j in range(1, n - 1):
                s = (
                    grid[i-1][j-1] + grid[i-1][j] + grid[i-1][j+1]
                    + grid[i][j]
                    + grid[i+1][j-1] + grid[i+1][j] + grid[i+1][j+1]
                )
                best = max(best, s)
        return best
```

> *Pythonic tip*: Use `range(1, m-1)` to skip the border rows/cols that cannot host an hourglass.

### 1.3 C++

```cpp
// 2428. Maximum Sum of an Hourglass – C++ (O(m*n) time, O(1) space)
class Solution {
public:
    int maxSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        int best = 0;                          // all elements >= 0
        for (int i = 1; i < m - 1; ++i) {      // middle row
            for (int j = 1; j < n - 1; ++j) {  // middle column
                int sum =
                    grid[i-1][j-1] + grid[i-1][j] + grid[i-1][j+1] +
                    grid[i][j] +
                    grid[i+1][j-1] + grid[i+1][j] + grid[i+1][j+1];
                best = max(best, sum);
            }
        }
        return best;
    }
};
```

> *Why it’s fast*: No dynamic memory allocation, just simple integer arithmetic.

---

## 2. SEO‑Optimized Blog Article

> **Title**: The Good, The Bad, and The Ugly of Solving LeetCode 2428 – *Maximum Sum of an Hourglass*  
> **Meta‑Description**: Master the LeetCode 2428 hourglass problem in Java, Python, and C++. Learn best practices, common pitfalls, and interview‑ready strategies to land your next software‑engineering role.  
> **Keywords**: LeetCode 2428, Maximum Sum of an Hourglass, interview coding, job interview, algorithm, Java, Python, C++, coding interview, data structure, time complexity, space complexity.

---

### 📌 Problem Overview

LeetCode 2428 asks you to compute the **maximum sum of a 3×3 “hourglass”** in an *m × n* matrix:

```
a b c
  d
e f g
```

* An hourglass cannot be rotated.
* It must fit entirely within the matrix.
* All values are non‑negative (0 ≤ grid[i][j] ≤ 10⁶).

**Constraints**  
3 ≤ m, n ≤ 150 → ≤ 22500 cells → simple nested loops are fast enough.

---

### 👌 The Good – A Clean, Interview‑Ready Approach

1. **Intuitive loop indices**  
   * Iterate over the *center* `(i, j)` of every hourglass: `i ∈ [1, m-2]`, `j ∈ [1, n-2]`.  
   * This automatically excludes border cells that cannot host a full hourglass.

2. **Direct sum calculation**  
   * Compute the 7 cells explicitly; no extra data structures.  
   * `sum = topRow + mid + bottomRow`.

3. **Constant auxiliary space**  
   * Only a few integer variables (`best`, `sum`).  
   * Perfect for interviewers looking for space‑efficient code.

4. **Edge‑case safety**  
   * Because all values are non‑negative, initializing `best = 0` works.  
   * If negatives were allowed, set `best = INT_MIN` / `-inf`.

5. **Time complexity**  
   * `O(m × n)` – each cell is considered a constant number of times.  
   * With *m, n* ≤ 150, the worst‑case is 22 500 iterations – trivial.

---

### ⚠️ The Bad – Common Pitfalls

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Including border cells** | Trying to access `grid[i-1][j-1]` when `i=0` or `j=0` → `ArrayIndexOutOfBounds`. | Loop `i` from `1` to `m-2`; `j` from `1` to `n-2`. |
| **Assuming negative values** | Initializing `best = 0` gives wrong answer if all numbers are negative. | Initialize to `-inf` or `INT_MIN`. |
| **Using O(n²) space** | Pre‑computing prefix sums or a DP table for no benefit; wastes memory. | Stick to constant space. |
| **Not handling large ints** | In Java/C++, `int` might overflow if values were larger. | Use `long` if you anticipate sums > 2³¹‑1 (not needed here). |

---

### 🐞 The Ugly – Over‑Optimization Traps

1. **Prefix Sum Overkill**  
   * Some solutions pre‑compute a 2D prefix sum and then slide a window.  
   * While it works, it adds *O(m × n)* space and a 2‑level loop overhead for each hourglass – unnecessary.

2. **Recursive Brute‑Force**  
   * Repeatedly recursing over the grid to pick 7 cells is both slow and stack‑heavy.  
   * Interviewers will expect a **plain loop**.

3. **Micro‑optimizing Inner Loop**  
   * Using temporary arrays to hold row slices.  
   * Complicates the code without a measurable speed gain in this size.

**Bottom line:** Simplicity wins. Interviewers value clean, readable code that solves the problem within the constraints.

---

### 🎯 Takeaways for the Job Interview

| Point | Why It Matters |
|-------|----------------|
| **Explain the loop bounds** | Shows understanding of matrix indices. |
| **Mention time/space complexity** | Demonstrates algorithmic thinking. |
| **Show awareness of edge cases** | Highlights robustness. |
| **Discuss why you chose this solution** | Reveals design decisions. |
| **Talk about testing** | Mention corner cases: 3×3 matrix, all zeros, mixed numbers. |

**Tip:** After writing the solution, walk through a quick example with a small matrix. Interviewers love to see the code in action.

---

### 📚 Additional Resources

- [LeetCode Problem 2428 – Maximum Sum of an Hourglass](https://leetcode.com/problems/maximum-sum-of-an-hourglass/)  
- [Discussion Threads (Java/Python/C++)](https://leetcode.com/problems/maximum-sum-of-an-hourglass/solutions/)  
- [Interview Warm‑Up: 2‑D Array Problems](https://www.educative.io/courses/leetcode-interview-preparation/4)  

---

## 🚀 Final Words

Solving *Maximum Sum of an Hourglass* is a quick win for your interview toolkit.  
- **Java**: Show mastery of arrays and loops.  
- **Python**: Highlight concise syntax and readability.  
- **C++**: Demonstrate performance awareness and STL use.  

Use this solution, talk through the “good, bad, ugly,” and you’ll impress any interviewer—whether you’re applying at a startup or a Fortune 500 company.

Happy coding and good luck on your next job hunt! 🌟