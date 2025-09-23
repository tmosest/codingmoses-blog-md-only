---
title: LeetCode 1895. Largest Magic Square - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 The Complete Guide to LeetCode 1895 – Largest Magic Square  
*Java, Python & C++ solutions + a “good‑bad‑ugly” deep dive*  

---

### 1️⃣ Problem Recap  
> **Largest Magic Square**  
> You are given an `m × n` integer grid (`1 ≤ m, n ≤ 50`).  
> A **k × k** sub‑square is a *magic square* if **every row sum, every column sum, and both diagonal sums are identical**.  
> The numbers inside a magic square do **not** have to be distinct.  
> Return the side length `k` of the *largest* magic square that can be found inside the grid.

---

### 2️⃣ Why This Problem Matters  
- **Interviews:** Many companies ask for a “magic square” or “sub‑matrix” variant to test **dynamic programming** and **prefix sums** knowledge.  
- **Algorithmic Thinking:** You’ll learn how to reduce a brute‑force O(m² n²) approach to an efficient O(m n min(m, n)) solution.  
- **Cross‑Language Proficiency:** The same logic can be expressed in Java, Python, or C++ – perfect for your portfolio.

---

## 3️⃣ Quick “Good‑Bad‑Ugly” Summary  

| # | Aspect | Good | Bad | Ugly |
|---|--------|------|-----|------|
| 1 | **Brute‑force** (check every sub‑square) | Conceptually simple | 50⁴ ≈ 6.25M checks – still okay but slow | Still O(m⁴) – bad for larger constraints |
| 2 | **Prefix sums** | O(1) sub‑square sum retrieval | Need to maintain separate 2‑D arrays | Off‑by‑one indexing bugs |
| 3 | **Greedy size‑check** | Stops checking smaller sizes once a larger one is found | Extra loop overhead | Mistaking diagonal sums (mixing up `+` / `-`) |
| 4 | **Language choice** | Java/C++ give speed, Python gives brevity | Java needs explicit array sizes | C++ requires careful pointer arithmetic |

---

## 4️⃣ The Efficient Solution – Prefix Sums + Greedy Search

### 4.1 Pre‑computations

| Data | Formula | Purpose |
|------|---------|---------|
| `rowPrefix[i][j]` | `rowPrefix[i][j-1] + grid[i][j]` | Sum of row `i` from column 0 to j |
| `colPrefix[i][j]` | `colPrefix[i-1][j] + grid[i][j]` | Sum of column `j` from row 0 to i |

With these two tables we can get the sum of any horizontal or vertical segment in O(1).

### 4.2 Checking a Square

1. **Target sum** – use the first row (or column) of the candidate square.  
2. **All rows** – verify that each row sum equals the target.  
3. **All columns** – verify that each column sum equals the target.  
4. **Diagonals** – compute both main and anti‑diagonals directly (cost O(k)).  
5. If all checks pass, we found a magic square of size `k`.

### 4.3 Complexity

- **Pre‑compute**: `O(m n)`  
- **Main loop**: For every top‑left corner we try sizes from the largest possible down to the current best.  
  Each size check costs `O(k)` for rows + `O(k)` for columns + `O(k)` for diagonals = `O(k)`.  
  Since `k ≤ min(m, n) ≤ 50`, the total remains `O(m n min(m, n))` ≈ `O(125 000)` – well within limits.  
- **Space**: `O(m n)` for the two prefix arrays.

---

## 5️⃣ Code in Three Languages

### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int largestMagicSquare(int[][] grid) {
        int m = grid.length, n = grid[0].length;

        // Prefix sums for rows and columns
        int[][] rowPref = new int[m][n];
        int[][] colPref = new int[m][n];

        for (int i = 0; i < m; i++) {
            int rSum = 0;
            for (int j = 0; j < n; j++) {
                rSum += grid[i][j];
                rowPref[i][j] = rSum;
            }
        }

        for (int j = 0; j < n; j++) {
            int cSum = 0;
            for (int i = 0; i < m; i++) {
                cSum += grid[i][j];
                colPref[i][j] = cSum;
            }
        }

        int best = 1;                     // Every 1x1 square is magic
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int maxSide = Math.min(m - i, n - j);

                // Only try larger sizes than we already found
                for (int k = maxSide; k > best; k--) {
                    if (isMagic(i, j, k, grid, rowPref, colPref)) {
                        best = k;
                        break; // No need to check smaller sizes at this top‑left
                    }
                }
            }
        }
        return best;
    }

    private boolean isMagic(int r, int c, int sz,
                            int[][] grid, int[][] rowPref, int[][] colPref) {

        // Sum of the first row segment
        int target = rowPref[r][c + sz - 1];
        if (c > 0) target -= rowPref[r][c - 1];

        // Check every row
        for (int i = 0; i < sz; i++) {
            int rowSum = rowPref[r + i][c + sz - 1];
            if (c > 0) rowSum -= rowPref[r + i][c - 1];
            if (rowSum != target) return false;
        }

        // Check every column
        for (int j = 0; j < sz; j++) {
            int colSum = colPref[r + sz - 1][c + j];
            if (r > 0) colSum -= colPref[r - 1][c + j];
            if (colSum != target) return false;
        }

        // Main diagonal
        int diag1 = 0;
        for (int k = 0; k < sz; k++) diag1 += grid[r + k][c + k];
        if (diag1 != target) return false;

        // Anti‑diagonal
        int diag2 = 0;
        for (int k = 0; k < sz; k++)
            diag2 += grid[r + k][c + sz - 1 - k];
        return diag2 == target;
    }
}
```

> **Tip:** The `rowPref`/`colPref` arrays are 0‑indexed – be careful with `c > 0` / `r > 0` checks.

---

### 5.2 Python

```python
class Solution:
    def largestMagicSquare(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])

        # Prefix sums
        row_pref = [[0]*n for _ in range(m)]
        col_pref = [[0]*n for _ in range(m)]

        for i in range(m):
            s = 0
            for j in range(n):
                s += grid[i][j]
                row_pref[i][j] = s

        for j in range(n):
            s = 0
            for i in range(m):
                s += grid[i][j]
                col_pref[i][j] = s

        best = 1
        for i in range(m):
            for j in range(n):
                max_side = min(m - i, n - j)
                # Try larger sizes first
                for sz in range(max_side, best, -1):
                    if self.is_magic(i, j, sz, grid, row_pref, col_pref):
                        best = sz
                        break
        return best

    def is_magic(self, r, c, sz,
                 grid, row_pref, col_pref) -> bool:
        # target = first row sum
        target = row_pref[r][c + sz - 1]
        if c > 0: target -= row_pref[r][c - 1]

        # Check all rows
        for i in range(r, r + sz):
            row_sum = row_pref[i][c + sz - 1]
            if c > 0: row_sum -= row_pref[i][c - 1]
            if row_sum != target:
                return False

        # Check all columns
        for j in range(c, c + sz):
            col_sum = col_pref[r + sz - 1][j]
            if r > 0: col_sum -= col_pref[r - 1][j]
            if col_sum != target:
                return False

        # Main diagonal
        diag = sum(grid[r + k][c + k] for k in range(sz))
        if diag != target:
            return False

        # Anti‑diagonal
        anti = sum(grid[r + k][c + sz - 1 - k] for k in range(sz))
        return anti == target
```

> **Python note:** Using list comprehensions keeps the code short, but keep an eye on integer overflow for larger inputs (not a problem for the given limits).

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int largestMagicSquare(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();

        // Prefix sums
        vector<vector<int>> rowPref(m, vector<int>(n));
        vector<vector<int>> colPref(m, vector<int>(n));

        for (int i = 0; i < m; ++i) {
            int r = 0;
            for (int j = 0; j < n; ++j) {
                r += grid[i][j];
                rowPref[i][j] = r;
            }
        }

        for (int j = 0; j < n; ++j) {
            int c = 0;
            for (int i = 0; i < m; ++i) {
                c += grid[i][j];
                colPref[i][j] = c;
            }
        }

        int best = 1;
        for (int r = 0; r < m; ++r) {
            for (int c = 0; c < n; ++c) {
                int maxSide = min(m - r, n - c);
                for (int sz = maxSide; sz > best; --sz) {
                    if (isMagic(r, c, sz, grid, rowPref, colPref)) {
                        best = sz;
                        break;          // no need to check smaller sizes
                    }
                }
            }
        }
        return best;
    }

private:
    bool isMagic(int r, int c, int sz,
                 vector<vector<int>>& grid,
                 vector<vector<int>>& rowPref,
                 vector<vector<int>>& colPref) {
        // Target sum from the first row
        int target = rowPref[r][c + sz - 1];
        if (c > 0) target -= rowPref[r][c - 1];

        // All rows
        for (int i = 0; i < sz; ++i) {
            int sumRow = rowPref[r + i][c + sz - 1];
            if (c > 0) sumRow -= rowPref[r + i][c - 1];
            if (sumRow != target) return false;
        }

        // All columns
        for (int j = 0; j < sz; ++j) {
            int sumCol = colPref[r + sz - 1][c + j];
            if (r > 0) sumCol -= colPref[r - 1][c + j];
            if (sumCol != target) return false;
        }

        // Main diagonal
        int diag1 = 0;
        for (int k = 0; k < sz; ++k) diag1 += grid[r + k][c + k];
        if (diag1 != target) return false;

        // Anti‑diagonal
        int diag2 = 0;
        for (int k = 0; k < sz; ++k) diag2 += grid[r + k][c + sz - 1 - k];
        if (diag2 != target) return false;

        return true;
    }
};
```

---

## 6️⃣ Debugging Checklist (The Ugly Part)

| Common Pitfall | What Happens | Fix |
|----------------|--------------|-----|
| **Off‑by‑one in prefix sums** | Wrong row/column sums | Use 0‑based indices consistently. Test with a 1×1 grid first. |
| **Integer overflow** | Wrong comparison on very large grids | LeetCode limits numbers to 10⁶; use `long` if you suspect bigger data. |
| **Diagonal mix‑up** | `diag1 != diag2` even though sums match | Compute main and anti‑diagonal separately; avoid re‑using a single `diag` variable. |
| **Loop bounds** | Trying size `k` larger than the remaining rows/columns | `int maxSide = min(m - r, n - c)` is essential. |

---

## 7️⃣ SEO‑Optimized Blog Post

> **Title:**  
> **“Mastering LeetCode 1895 – Largest Magic Square (Java, Python, C++ Solutions)”**

> **Meta Description:**  
> Learn the most efficient solution to LeetCode 1895 – Largest Magic Square. Get ready for interviews with step‑by‑step Java, Python, and C++ code. Boost your coding confidence today!

> **Primary Keywords:**  
> LeetCode 1895, Largest Magic Square, prefix sum algorithm, coding interview, data structures, 2D arrays, dynamic programming, algorithmic puzzles.

> **Headings & Content Outline:**

```
H1: Mastering LeetCode 1895 – Largest Magic Square
   H2: Problem Overview
   H2: Why Prefix Sums Are Game‑Changing
   H3: Understanding the 2D Prefix Sum Trick
   H2: 3 Languages, 3 Clean Code Samples
      H3: Java Implementation (with code snippets)
      H3: Python Implementation (with code snippets)
      H3: C++ Implementation (with code snippets)
   H2: Common Bugs and How to Avoid Them
   H2: How This Problem Helps Your Interview Prep
   H2: Takeaway – The Magic of O(n³) with O(1) Checks
   H2: Ready to Conquer Interview Questions?
```

> **Suggested Keywords throughout the article:**
> `LeetCode 1895`, `Largest Magic Square`, `prefix sum`, `2D arrays`, `dynamic programming`, `coding interview`, `Java solution`, `Python solution`, `C++ solution`, `algorithmic challenge`, `competitive programming`.

> **Internal Links:**  
> - Link to a tutorial on `2D prefix sums`.  
> - Link to a general `LeetCode discussion` for 1895.

> **Call‑to‑Action:**  
> At the end, invite readers to comment with their own solutions, or to try similar problems like **LeetCode 1984 – Largest Submatrix With Rearrangements** or **LeetCode 1738 – Count Items Matching a Condition**.

---

## 8️⃣ Final Thoughts

You now have:

1. A **clear understanding** of the problem statement and constraints.  
2. **Three production‑ready solutions** in your favorite language.  
3. A **debugging cheat sheet** to keep the code bullet‑proof.  
4. An **SEO‑friendly article** to showcase your expertise on Medium, Dev.to, or your personal blog.

> **Deploy your solution, share your post, and ace that coding interview!**

---



