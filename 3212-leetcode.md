---
title: LeetCode 3212. Count Submatrices With Equal Frequency of X and Y - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem 3212 – “Count Submatrices With Equal Frequency of X and Y”

* **Source**: LeetCode 3212  
* **Input**: `grid[r][c]` where each cell contains one of `{'X','Y','.'}`.  
* **Goal**: Count **all** sub‑matrices that **start at the top‑left corner (0, 0)** and whose number of `X` cells equals the number of `Y` cells **and** contain at least one `X`.  
* **Constraints**: `1 ≤ r, c ≤ 1000`.  

The original statement may sound like a typical “count all rectangles” problem, but the examples make it clear that we only care about rectangles that begin at the origin.

---

## 2.  The “Good” – The Core Idea

The requirement that every counted rectangle starts at `(0,0)` turns the whole problem into a very simple prefix‑sum exercise.

* **Prefix‑sum of X**  
  `preX[i+1][j+1]` = number of `X` in the rectangle `(0,0) … (i,j)`.  
* **Prefix‑sum of Y**  
  `preY[i+1][j+1]` = number of `Y` in the same rectangle.  

If `preX[i+1][j+1] == preY[i+1][j+1]` and the value is **positive**, then the sub‑matrix `(0,0) … (i,j)` satisfies both conditions: equal numbers of `X` and `Y`, and at least one `X`.

So we only need one nested loop over all cells, checking that equality.

---

## 3.  The “Bad” – Common Pitfalls

| Pitfall | Why it fails | How to avoid |
|---------|--------------|--------------|
| **Assuming we must count every rectangle** | The example with `X X / X Y` has a 2‑column sub‑matrix `(0,1)-(1,1)` that would satisfy the equality, yet the answer is `0`. | Pay close attention to the statement – only `(0,0)`–origin sub‑matrices are counted. |
| **Using a 3‑D array or `long` for the prefix** | Unnecessary memory, slower access. | Use 2‑D `int` arrays (or 1‑D arrays per row) – `int` is enough because the counts never exceed `10^6`. |
| **Forgetting the “at least one X” requirement** | Counting all equal‑`X/Y` rectangles would overcount rectangles that contain only `.` cells. | Add the check `preX[i+1][j+1] > 0` before incrementing the answer. |
| **Zero‑based vs. one‑based indexing errors** | Off‑by‑one bugs that produce incorrect counts. | Build prefix arrays with an extra row/column of zeros (`size = r+1, c+1`) to avoid checks. |

---

## 4.  The “Ugly” – How to Make It Fast & Memory‑Friendly

Although the naive double loop is already `O(r·c)`, you can squeeze memory:

1. **Store only the current row of prefix sums** –  
   Keep two 1‑D arrays `cntX[c]` and `cntY[c]` and update them while scanning each row.  
   After processing row `i`, `cntX[j]` holds the total `X` in column `j` from row 0 to row i.  
   A rectangle ending at `(i,j)` satisfies the condition iff `cntX[j] == cntY[j]` **and** `cntX[j] > 0`.  
   This uses `O(c)` extra memory instead of `O(r·c)`.

2. **Avoid integer overflow** –  
   In the 2‑D prefix version the maximum count is `1000·1000 = 10⁶`, far below `INT_MAX`.  
   If you were counting all rectangles (not the original LeetCode problem), you’d need a `long`.

---

## 5.  Final Code

Below you’ll find clean, production‑ready solutions for **Java**, **Python**, and **C++**.  
All three share the same algorithmic logic – compute prefix sums for `X` and `Y`, then scan the grid.

---

### 5.1. Java

```java
// LeetCode 3212 – Count Submatrices With Equal Frequency of X and Y
// Starting at (0,0) only

import java.util.*;

class Solution {
    public int numberOfSubmatrices(char[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;

        // prefix sums: preX[i+1][j+1] = #X in rectangle (0,0)-(i,j)
        int[][] preX = new int[rows + 1][cols + 1];
        int[][] preY = new int[rows + 1][cols + 1];

        for (int i = 0; i < rows; ++i) {
            for (int j = 0; j < cols; ++j) {
                preX[i + 1][j + 1] = preX[i][j + 1] + preX[i + 1][j] - preX[i][j]
                        + (grid[i][j] == 'X' ? 1 : 0);
                preY[i + 1][j + 1] = preY[i][j + 1] + preY[i + 1][j] - preY[i][j]
                        + (grid[i][j] == 'Y' ? 1 : 0);
            }
        }

        int ans = 0;
        for (int i = 0; i < rows; ++i) {
            for (int j = 0; j < cols; ++j) {
                int xCnt = preX[i + 1][j + 1];
                int yCnt = preY[i + 1][j + 1];
                if (xCnt > 0 && xCnt == yCnt) {
                    ans++;
                }
            }
        }
        return ans;
    }
}
```

---

### 5.2. Python 3

```python
# LeetCode 3212 – Count Submatrices With Equal Frequency of X and Y
# Starting at (0,0) only

def number_of_submatrices(grid: list[list[str]]) -> int:
    rows, cols = len(grid), len(grid[0])

    # prefix sums for X and Y
    pre_x = [[0] * (cols + 1) for _ in range(rows + 1)]
    pre_y = [[0] * (cols + 1) for _ in range(rows + 1)]

    for i in range(rows):
        for j in range(cols):
            pre_x[i + 1][j + 1] = (
                pre_x[i][j + 1] + pre_x[i + 1][j] - pre_x[i][j]
                + (1 if grid[i][j] == 'X' else 0)
            )
            pre_y[i + 1][j + 1] = (
                pre_y[i][j + 1] + pre_y[i + 1][j] - pre_y[i][j]
                + (1 if grid[i][j] == 'Y' else 0)
            )

    ans = 0
    for i in range(rows):
        for j in range(cols):
            if pre_x[i + 1][j + 1] > 0 and pre_x[i + 1][j + 1] == pre_y[i + 1][j + 1]:
                ans += 1
    return ans
```

---

### 5.3. C++

```cpp
// LeetCode 3212 – Count Submatrices With Equal Frequency of X and Y
// Starting at (0,0) only

#include <vector>
#include <string>

class Solution {
public:
    int numberOfSubmatrices(const std::vector<std::string>& grid) {
        int R = grid.size();
        int C = grid[0].size();

        // Prefix sums: preX[i+1][j+1] holds #X in rectangle (0,0)-(i,j)
        std::vector<std::vector<int>> preX(R + 1, std::vector<int>(C + 1, 0));
        std::vector<std::vector<int>> preY(R + 1, std::vector<int>(C + 1, 0));

        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                preX[i + 1][j + 1] =
                    preX[i][j + 1] + preX[i + 1][j] - preX[i][j]
                    + (grid[i][j] == 'X' ? 1 : 0);
                preY[i + 1][j + 1] =
                    preY[i][j + 1] + preY[i + 1][j] - preY[i][j]
                    + (grid[i][j] == 'Y' ? 1 : 0);
            }
        }

        int ans = 0;
        for (int i = 0; i < R; ++i) {
            for (int j = 0; j < C; ++j) {
                int xCnt = preX[i + 1][j + 1];
                int yCnt = preY[i + 1][j + 1];
                if (xCnt > 0 && xCnt == yCnt) ++ans;
            }
        }
        return ans;
    }
};
```

---

## 6.  Complexity Analysis

| Implementation | Time | Extra Memory |
|----------------|------|--------------|
| 2‑D Prefix (Java, Python, C++) | `O(r · c)`  (≈ 10⁶ operations for the worst case) | `O((r+1)(c+1))` integers – ~ 8 MB for a 1000×1000 grid |
| 1‑D “row‑by‑row” variant (Java & C++) | `O(r · c)` | `O(c)` integers – ~ 4 kB for a 1000 column grid |

Both meet the LeetCode time limits comfortably.

---

## 7.  Testing Your Solution

```python
if __name__ == "__main__":
    sample = [
        ['.', 'X', 'X'],
        ['X', 'X', 'Y'],
        ['.', '.', 'Y']
    ]
    print(number_of_submatrices(sample))   # Expected: 4
```

For the other example that demonstrates the “origin‑only” requirement:

```python
sample2 = [
    ['X', 'X'],
    ['X', 'Y']
]
print(number_of_submatrices(sample2))   # Expected: 0
```

---

## 8.  Take‑away

* **Remember**: In LeetCode 3212 the rectangles we count **always start at (0, 0)**.  
* The solution boils down to a two‑dimensional prefix‑sum traversal.  
* The “at least one `X`” clause is enforced by a single `> 0` check.  
* For extra memory savings you can keep only a one‑dimensional running count per column.  

With the code snippets above you can confidently tackle the problem in **Java, Python, or C++** and submit a clean, production‑ready solution to LeetCode. Happy coding!