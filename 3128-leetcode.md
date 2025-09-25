---
title: LeetCode 3128. Right Triangles - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Three‑language Solutions for LeetCode 3128 – *Right Triangles*  

Below are clean, production‑ready implementations in **Java**, **Python** and **C++** that solve the problem in *O(m × n)* time and *O(m + n)* additional memory, where *m* is the number of rows and *n* the number of columns.

> **Problem recap**  
> A **right triangle** is formed by three cells that all contain `1`.  
> One vertex must share a **row** with the second vertex, and the first vertex must share a **column** with the third vertex.  
> Count all such triangles in a binary matrix.

---

### 1.1  Java

```java
import java.util.*;

class Solution {
    public long numberOfRightTriangles(int[][] grid) {
        int rows = grid.length;
        int cols = grid[0].length;

        // Pre‑count ones in each row and column
        int[] rowCnt = new int[rows];
        int[] colCnt = new int[cols];

        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 1) {
                    rowCnt[r]++;
                    colCnt[c]++;
                }
            }
        }

        long triangles = 0;
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (grid[r][c] == 1) {
                    // (rowCnt[r] - 1) choices for the horizontal leg
                    // (colCnt[c] - 1) choices for the vertical leg
                    triangles += 1L * (rowCnt[r] - 1) * (colCnt[c] - 1);
                }
            }
        }
        return triangles;
    }
}
```

*Why `long`?*  
The maximum number of triangles can reach `10¹²` (1 000 × 1 000 grid filled with ones). `int` would overflow.

---

### 1.2  Python

```python
class Solution:
    def numberOfRightTriangles(self, grid: List[List[int]]) -> int:
        rows, cols = len(grid), len(grid[0])

        # Row and column counters
        row_cnt = [0] * rows
        col_cnt = [0] * cols

        for r in range(rows):
            for c in range(cols):
                if grid[r][c]:
                    row_cnt[r] += 1
                    col_cnt[c] += 1

        triangles = 0
        for r in range(rows):
            for c in range(cols):
                if grid[r][c]:
                    triangles += (row_cnt[r] - 1) * (col_cnt[c] - 1)

        return triangles
```

Python’s arbitrary‑precision integers mean we don’t need to worry about overflow.

---

### 1.3  C++

```cpp
#include <vector>

class Solution {
public:
    long long numberOfRightTriangles(std::vector<std::vector<int>>& grid) {
        int rows = grid.size();
        int cols = grid[0].size();

        std::vector<int> rowCnt(rows, 0), colCnt(cols, 0);

        // Count 1s in each row and column
        for (int r = 0; r < rows; ++r)
            for (int c = 0; c < cols; ++c)
                if (grid[r][c]) {
                    ++rowCnt[r];
                    ++colCnt[c];
                }

        long long triangles = 0;
        for (int r = 0; r < rows; ++r)
            for (int c = 0; c < cols; ++c)
                if (grid[r][c]) {
                    triangles += 1LL * (rowCnt[r] - 1) * (colCnt[c] - 1);
                }

        return triangles;
    }
};
```

`long long` guarantees safe 64‑bit arithmetic.

---

## 2.  Blog Article: “The Good, the Bad, and the Ugly of Counting Right Triangles”

### 2.1  Why This Problem Matters in Interviews

- **Matrix traversal** – a staple of interview questions.
- **Combination counting** – tests whether you can spot *O(m × n)* solutions instead of brute‑force O((m × n)³).
- **Edge‑case thinking** – handling very large outputs and memory constraints.
- **Cross‑language fluency** – interviewers often ask for Java/Python/C++ solutions on the fly.

### 2.2  The Good – Intuition & Simplicity

The key insight:  
> *If a cell (r, c) is a right‑angle vertex, any other `1` in its row can be the horizontal leg, and any other `1` in its column can be the vertical leg.*

Thus, for each `1` we only need the counts of `1`s in its row and column.  
The number of triangles with that cell as the right angle is simply  
`(rowCount[r] – 1) × (colCount[c] – 1)`.

This reduces a seemingly combinatorial explosion to a linear pass.

### 2.3  The Bad – Naïve Approaches that TLE

A straightforward O((m × n)³) algorithm would:
1. Enumerate all triplets of cells.
2. Check the geometric condition.

Even for a 100×100 grid this involves billions of operations—far beyond interview‑time limits.  
The problem’s constraints (1000×1000) rule out any cubic or quadratic‑in‑cells solution.

### 2.4  The Ugly – Corner Cases & Overflow

- **All cells are `1`**:  
  The number of triangles can be as high as  
  `Σ (rowCount[r] – 1) × (colCount[c] – 1)` ≈ `10¹²`.  
  Using a 32‑bit integer overflows; use 64‑bit (`long`/`long long`).
- **Sparse grids**:  
  Many rows/columns contain zero `1`s. Our algorithm handles this gracefully because `(rowCount[r] – 1)` becomes negative only if `rowCount[r] == 0`, but we guard by checking `grid[r][c] == 1` before computing the product.
- **Non‑rectangular inputs**:  
  LeetCode guarantees `grid[i].length` is constant, but defensive code can assert or handle jagged arrays.

### 2.5  Step‑by‑Step Walkthrough (Java)

```text
1. Count 1s in each row → rowCnt[]
2. Count 1s in each column → colCnt[]
3. For every cell that is 1:
       triangles += (rowCnt[r] - 1) * (colCnt[c] - 1)
```

The first two passes are O(m × n).  
The third pass is also O(m × n).  
Total time: **O(m × n)**.  
Extra memory: **O(m + n)** for the counters.

### 2.6  Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Counting rows | O(m × n) | O(m) |
| Counting columns | O(m × n) | O(n) |
| Computing triangles | O(m × n) | O(1) (aside from counters) |
| **Total** | **O(m × n)** | **O(m + n)** |

This meets the constraints comfortably:  
- For a 1000 × 1000 grid: ~1 000 000 operations, ~8 KB of auxiliary storage.

### 2.7  Testing & Edge‑Case Checklist

| Test | Grid | Expected | Reason |
|------|------|----------|--------|
| All zeros | 3×3 all `0` | 0 | No triangles |
| All ones | 4×4 all `1` | 4 * (3 * 3) = 36 | Max count |
| Sparse ones | 3×3 with isolated `1` | 0 | Need at least two 1s in same row/col |
| Non‑square | 2×5 grid | Count accordingly | Handles rectangular shapes |
| Large input | 1000×1000 with 50% ones | Run in <0.1 s | Performance benchmark |

### 2.8  Take‑aways for Your Next Interview

- **Start with counting helpers**: When a problem involves “same row / same column” constraints, pre‑compute row/column frequencies.
- **Beware of integer overflow**: If you suspect results can exceed 2³¹ – 1, use 64‑bit types.
- **Explain your logic**: Even if your code is perfect, articulating the intuition (like the “right‑angle vertex” view) impresses interviewers.
- **Time‑complexity first**: Show you considered O((m × n)³) and why it fails before presenting the O(m × n) solution.

### 2.9  Final Verdict

- **Good** – A beautiful *O(m × n)* algorithm that’s both **efficient** and **readable**.  
- **Bad** – Any brute‑force approach will *time‑out* on large test cases.  
- **Ugly** – Watch out for overflow and sparsity, but a careful 64‑bit product fixes it.

This problem is a *classic example* of how a simple combinatorial observation can transform a seemingly intractable challenge into a clean, interview‑friendly solution. Mastering it will give you a solid footing for any matrix‑based interview question.

---

### 2.9  Want to Impress Even More?

- **Add a one‑liner for Python**:  
  ```python
  triangles = sum((row_cnt[r]-1)*(col_cnt[c]-1)
                  for r in range(rows) for c in range(cols) if grid[r][c])
  ```
- **Discuss parallelization**: Two separate scans can be parallel‑executed; talk about the possibility if the interviewer asks about *distributed computing*.

### 2.10  Final Words

Counting right triangles is a microcosm of the *“matrix + combinatorics + careful counting”* interview theme. By learning the pattern of “right‑angle vertex” counting, you’ll be ready for a wide variety of interview questions that ask you to *count* structures inside 2‑D grids.  

Happy coding—and good luck on your next interview!