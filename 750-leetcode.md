---
title: LeetCode 750. Number Of Corner Rectangles - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 750. Number of Corner Rectangles – The Complete Solution Pack

| Language | Time Complexity | Space Complexity | Notes |
|----------|-----------------|------------------|-------|
| **Java** | O(max(M,N)·min(M,N)²) | O(1) | Optimized for the sparse grid – two‑loop over rows/cols + combinatorics. |
| **Python** | O(max(M,N)·min(M,N)²) | O(1) | Same idea, written idiomatically. |
| **C++** | O(max(M,N)·min(M,N)²) | O(1) | Uses bitset/array‑based approach for speed. |

> **Why you should read this article**  
> Leetcode 750 is a classic “matrix + combinatorics” interview question that is frequently asked by top tech companies.  
> Understanding the *good*, the *bad*, and the *ugly* parts of its solutions will help you impress hiring managers, score high on coding assessments, and land that software‑engineering role you’ve been eyeing.

---

## 📄 Problem Statement

> **Given** an `m × n` integer matrix `grid` where each entry is either `0` or `1`, return the number of *corner rectangles*.  
> A *corner rectangle* is a set of four distinct `1`s that form an axis‑aligned rectangle. Only the corners must contain `1`; the inside cells can be `0` or `1`. All four `1`s must be distinct (so a single row or column of `1`s doesn’t count).

*Constraints*

- `1 ≤ m, n ≤ 200`
- `grid[i][j] ∈ {0, 1}`
- `1 ≤ #1s ≤ 6000`

---

## 🧩 Why It’s a “Medium” but *Very* Interview‑Ready Problem

- **Combinatorics** – You’ll need to think about “how many ways can we pick two columns that both have a `1` in two different rows?”  
- **Sparse Matrix Awareness** – In many real‑world interview settings, the grid is very sparse (only a few `1`s). A naïve `O(m²·n²)` solution will TLE.  
- **Trade‑offs** – A pure brute‑force is easy to code but slow; a row‑by‑row DP or bitset solution is fast but harder to explain on a whiteboard.

---

## ✅ The *Good* – Fast, Elegant, and Easy to Reason About

The key insight:

1. **Fix two rows** (or two columns).  
2. Scan the other dimension to count how many columns contain a `1` in **both** selected rows.  
3. If you find `k` such columns, you can pick any two of them to form a rectangle: `C(k, 2) = k·(k‑1)/2`.

> **Why two rows?**  
> Because the rectangle’s corners lie on two distinct rows. Once the rows are fixed, columns become the only degrees of freedom.

**Pseudo‑code**

```
result = 0
for each pair of rows (r1 < r2):
    count = 0
    for each column c:
        if grid[r1][c] == 1 && grid[r2][c] == 1:
            count++
    result += count * (count-1) / 2
return result
```

**Complexity**

- `O(min(M,N)² · max(M,N))` time  
- `O(1)` auxiliary space (or `O(N²)` if you want to reuse a 2‑D array for optimization)

---

## ⚠️ The *Bad* – Brute‑Force O(M²·N²) and Why It Doesn’t Pass

```python
# O(M^2 * N^2) – too slow for 200×200 grid
for r1 in range(M):
    for r2 in range(r1+1, M):
        for c1 in range(N):
            for c2 in range(c1+1, N):
                if grid[r1][c1] and grid[r1][c2] and grid[r2][c1] and grid[r2][c2]:
                    res += 1
```

- Even with the maximum constraints (`M = N = 200`), this loops **1.6 billion** times → TLE on Leetcode.  
- It also uses four nested loops – hard to explain quickly on an interview board.

---

## 😈 The *Ugly* – Memory‑Intensive DP (but still efficient)

A slightly different viewpoint:  
Instead of counting per pair of rows, keep a running count of how many previous rows share a `1` in each column pair.  
This allows us to accumulate rectangles in **O(M·N²)** time with **O(N²)** space, but the DP array can become a bottleneck if `N` is large.

> **When to use it?**  
> In interview settings where you’re given a *very sparse* grid and can afford an `O(N²)` auxiliary matrix, this method often scores the fastest runtime.

---

## 📚 Full Code Implementations

### 1️⃣ Java (Space‑Optimized, Sparse‑Matrix Friendly)

```java
class Solution {
    public int countCornerRectangles(int[][] grid) {
        if (grid == null || grid.length <= 1 || grid[0].length <= 1) {
            return 0;
        }

        int rows = grid.length;
        int cols = grid[0].length;

        // Pick the smaller dimension to iterate over pairs
        if (rows < cols) {
            return countByRows(grid, rows, cols);
        } else {
            return countByCols(grid, rows, cols);
        }
    }

    private int countByRows(int[][] grid, int rows, int cols) {
        int res = 0;
        for (int r1 = 0; r1 < rows - 1; r1++) {
            for (int r2 = r1 + 1; r2 < rows; r2++) {
                int cnt = 0;
                for (int c = 0; c < cols; c++) {
                    if ((grid[r1][c] & grid[r2][c]) == 1) cnt++;
                }
                res += cnt * (cnt - 1) / 2;
            }
        }
        return res;
    }

    private int countByCols(int[][] grid, int rows, int cols) {
        int res = 0;
        for (int c1 = 0; c1 < cols - 1; c1++) {
            for (int c2 = c1 + 1; c2 < cols; c2++) {
                int cnt = 0;
                for (int r = 0; r < rows; r++) {
                    if ((grid[r][c1] & grid[r][c2]) == 1) cnt++;
                }
                res += cnt * (cnt - 1) / 2;
            }
        }
        return res;
    }
}
```

---

### 2️⃣ Python (Elegant & Easy to Read)

```python
class Solution:
    def countCornerRectangles(self, grid: List[List[int]]) -> int:
        if not grid or len(grid) <= 1 or len(grid[0]) <= 1:
            return 0

        m, n = len(grid), len(grid[0])
        # Iterate over the smaller dimension
        if m < n:
            return self._by_rows(grid, m, n)
        else:
            return self._by_cols(grid, m, n)

    def _by_rows(self, grid, m, n):
        res = 0
        for r1 in range(m - 1):
            for r2 in range(r1 + 1, m):
                cnt = 0
                for c in range(n):
                    if grid[r1][c] & grid[r2][c]:
                        cnt += 1
                res += cnt * (cnt - 1) // 2
        return res

    def _by_cols(self, grid, m, n):
        res = 0
        for c1 in range(n - 1):
            for c2 in range(c1 + 1, n):
                cnt = 0
                for r in range(m):
                    if grid[r][c1] & grid[r][c2]:
                        cnt += 1
                res += cnt * (cnt - 1) // 2
        return res
```

---

### 3️⃣ C++ (Fastest, Bitset‑Optimized for Leetcode)

```cpp
class Solution {
public:
    int countCornerRectangles(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        if (m <= 1 || n <= 1) return 0;
        // Use bitset if n <= 200 (fits into a 200‑bit bitset)
        vector<bitset<200>> rows(m);
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (grid[i][j]) rows[i].set(j);

        int res = 0;
        for (int i = 0; i < m - 1; ++i) {
            for (int j = i + 1; j < m; ++j) {
                // intersection of two rows
                bitset<200> inter = rows[i] & rows[j];
                long long cnt = inter.count();
                res += cnt * (cnt - 1) / 2;
            }
        }
        return res;
    }
};
```

> **Why bitset?**  
> A `bitset<200>` gives O(1) intersection and O(1) pop‑count, turning the inner loop into a handful of machine instructions. This is the fastest solution for Leetcode’s hard‑time limits.

---

## 🔎 How to Explain It on an Interview Board

1. **Draw two rows** (`r1` and `r2`).  
2. **Show a sweep** across columns, counting where both have a `1`.  
3. **Apply the combination formula**.  
4. **Generalise** – “If columns are fewer, we would swap the logic and pair columns instead.”  

> **Tip:** Keep your reasoning short: *“We’re essentially counting column pairs that share a `1` in two rows, so for `k` columns we have `k choose 2` rectangles.”*  
> Use the whiteboard to sketch the bitset intersection if you’re comfortable with C++.

---

## 🚀 Take‑away: What Hiring Managers Are Looking For

| Skill | Why It Matters | How to Show It |
|-------|----------------|----------------|
| **Algorithmic thinking** | Ability to spot the pair‑rows pattern | Briefly describe the combinatorics step |
| **Time‑Space trade‑off** | Understand when to favour time over code length | Mention “we pick the smaller dimension to keep time linear” |
| **Use of data structures** | Recognise Leetcode’s sparse matrices | Explain the bitset or DP array optimisation |
| **Communication** | Clear, concise explanation | Practice the board‑walk in a mock interview |

---

## 🎯 Final Thoughts & Next Steps

1. **Practice** – Run these solutions on the sample inputs from Leetcode, then create your own edge cases (e.g., all `1`s, all `0`s, diagonal `1`s).  
2. **Explain the math** – On interviews, always verbalise `C(k, 2)` before coding.  
3. **Be ready to adapt** – If the interviewer asks for “fastest possible” or “memory‑efficient”, pivot to the DP or bitset version.

> **Landing a role**: Mastering Leetcode 750 demonstrates mastery of *matrix manipulation*, *combinatorics*, and *algorithmic optimisation*—skills that are core to many software‑engineering positions (e.g., backend, data‑engineering, algorithmic trading).

Happy coding, and may your next interview go *off‑the‑chart*! 🚀

--- 

> *Follow me on GitHub for more interview‑ready solutions, mock interview practice, and career‑advancement resources.*  
> 👉 **[Your GitHub]** | 👉 **[Your LinkedIn]** | 👉 **[Your Blog]**

---


---


(End of article)