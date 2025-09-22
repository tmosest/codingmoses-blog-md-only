---
title: LeetCode 2711. Difference of Number of Distinct Values on Diagonals - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 2711 – Difference of Number of Distinct Values on Diagonals  
### “The Good, The Bad, and The Ugly” – A Complete Java / Python / C++ Solution Pack

> **Why this article matters for your next interview**  
> Mastering LeetCode 2711 shows you can:  
> * Read a non‑trivial 2‑D problem statement  
> * Think about diagonal traversal, sets, and bit‑mask optimisation  
> * Deliver clean, language‑agnostic code in Java, Python and C++  
> * Discuss time‑space trade‑offs – a must‑know interview skill  

---

## 1. Problem Statement (from LeetCode)

> **Given** a 2‑D grid `grid[m][n]` (1 ≤ m, n ≤ 50, 1 ≤ grid[i][j] ≤ 50)  
> **Define** for each cell (r, c):
> * `leftAbove[r][c]` – number of **distinct** values on the diagonal to the left‑above (excluding the cell itself).  
> * `rightBelow[r][c]` – number of **distinct** values on the diagonal to the right‑below (excluding the cell itself).  
> **Return** an `ans[m][n]` where  
> `ans[r][c] = | leftAbove[r][c] – rightBelow[r][c] |`.

A diagonal is any line that starts from a cell on the top row or left column and moves one step down‑right until the grid ends.

---

## 2. The Good – Brute‑Force Approach (Intuitive)

```java
public int[][] differenceOfDistinctValues(int[][] grid) {
    int m = grid.length, n = grid[0].length;
    int[][] ans = new int[m][n];
    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < n; ++j) {
            Set<Integer> left = new HashSet<>();
            for (int r = i-1, c = j-1; r >= 0 && c >= 0; --r, --c)
                left.add(grid[r][c]);

            Set<Integer> right = new HashSet<>();
            for (int r = i+1, c = j+1; r < m && c < n; ++r, ++c)
                right.add(grid[r][c]);

            ans[i][j] = Math.abs(left.size() - right.size());
        }
    }
    return ans;
}
```

* **Pros** – Easy to understand, uses Java’s `HashSet`.  
* **Cons** – Time complexity `O(m*n*min(m,n))`, worst‑case ≈ 125 000 operations per cell (fine for 50x50 but slow in contests).  
* **Memory** – `O(min(m,n))` for the two temporary sets.

---

## 3. The Bad – Why the Brute‑Force Fails in Real Interviews

* **Time‑Limit Exceeded**: On larger inputs or in strict contest limits, the `O(m*n*min(m,n))` solution may hit 2 s timeout.  
* **Re‑computing Sets**: Each cell recomputes the same diagonals repeatedly.  
* **High Constant Factors**: Java `HashSet` allocation overhead per cell is expensive.  

> **Bottom line** – you’ll impress your recruiter if you can reduce the algorithm to linear time.

---

## 4. The Ugly – Edge Cases That Break Naïve Code

| Case | Why it breaks |
|------|----------------|
| Single cell `[[1]]` | Diagonals are empty; sets correctly return size 0. |
| Rectangular grid `3x5` | The diagonal length differs; must guard bounds `r>=0 && c>=0` and `r<m && c<n`. |
| Repeated values along a diagonal | Sets correctly collapse duplicates, but naive counting would mis‑count. |

The brute‑force code handles all of them, but it’s fragile if you modify the loops.

---

## 5. The Optimised Solution – Linear Time with Bit‑Masks

Because every cell value is in `[1, 50]`, we can store a 64‑bit mask where bit `k` (0‑based) indicates that value `k+1` has appeared.  
Two masks per cell are enough:

| Symbol | Meaning | Transition |
|--------|---------|------------|
| `leftMask[i][j]` | Distinct values on the left‑above diagonal up to `(i,j)` **excluding** `(i,j)` | `leftMask[i][j] = leftMask[i-1][j-1] | (1L << (grid[i-1][j-1]-1))` |
| `rightMask[i][j]` | Distinct values on the right‑below diagonal starting **after** `(i,j)` | `rightMask[i][j] = rightMask[i+1][j+1] | (1L << (grid[i+1][j+1]-1))` |

The answer for a cell is the absolute difference of the pop‑count of the two masks.

### 5.1 Java Implementation

```java
class Solution {
    public int[][] differenceOfDistinctValues(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        long[][] leftMask = new long[m][n];
        long[][] rightMask = new long[m][n];
        int[][] ans = new int[m][n];

        // Build leftMask (top‑left → bottom‑right)
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                if (i > 0 && j > 0) {
                    leftMask[i][j] = leftMask[i-1][j-1]
                            | (1L << (grid[i-1][j-1] - 1));
                }
            }
        }

        // Build rightMask (bottom‑right → top‑left)
        for (int i = m-1; i >= 0; --i) {
            for (int j = n-1; j >= 0; --j) {
                if (i < m-1 && j < n-1) {
                    rightMask[i][j] = rightMask[i+1][j+1]
                            | (1L << (grid[i+1][j+1] - 1));
                }
            }
        }

        // Compute answer
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                int leftCount  = Long.bitCount(leftMask[i][j]);
                int rightCount = Long.bitCount(rightMask[i][j]);
                ans[i][j] = Math.abs(leftCount - rightCount);
            }
        }
        return ans;
    }
}
```

### 5.2 Python Implementation

```python
class Solution:
    def differenceOfDistinctValues(self, grid: List[List[int]]) -> List[List[int]]:
        m, n = len(grid), len(grid[0])
        left_mask  = [[0] * n for _ in range(m)]
        right_mask = [[0] * n for _ in range(m)]

        # leftMask: top‑left -> bottom‑right
        for i in range(m):
            for j in range(n):
                if i > 0 and j > 0:
                    left_mask[i][j] = left_mask[i-1][j-1] | (1 << (grid[i-1][j-1] - 1))

        # rightMask: bottom‑right -> top‑left
        for i in range(m-1, -1, -1):
            for j in range(n-1, -1, -1):
                if i < m-1 and j < n-1:
                    right_mask[i][j] = right_mask[i+1][j+1] | (1 << (grid[i+1][j+1] - 1))

        ans = [[0] * n for _ in range(m)]
        for i in range(m):
            for j in range(n):
                left_cnt  = left_mask[i][j].bit_count()
                right_cnt = right_mask[i][j].bit_count()
                ans[i][j] = abs(left_cnt - right_cnt)
        return ans
```

### 5.3 C++ Implementation

```cpp
class Solution {
public:
    vector<vector<int>> differenceOfDistinctValues(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<unsigned long long>> leftMask(m, vector<unsigned long long>(n, 0));
        vector<vector<unsigned long long>> rightMask(m, vector<unsigned long long>(n, 0));
        vector<vector<int>> ans(m, vector<int>(n, 0));

        // leftMask: top-left to bottom-right
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                if (i && j)
                    leftMask[i][j] = leftMask[i-1][j-1] |
                                     (1ULL << (grid[i-1][j-1] - 1));

        // rightMask: bottom-right to top-left
        for (int i = m-1; i >= 0; --i)
            for (int j = n-1; j >= 0; --j)
                if (i < m-1 && j < n-1)
                    rightMask[i][j] = rightMask[i+1][j+1] |
                                     (1ULL << (grid[i+1][j+1] - 1));

        // Final answer
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j) {
                int leftCnt  = __builtin_popcountll(leftMask[i][j]);
                int rightCnt = __builtin_popcountll(rightMask[i][j]);
                ans[i][j] = abs(leftCnt - rightCnt);
            }
        return ans;
    }
};
```

---

## 6. Complexity Analysis

| Approach | Time | Space | Why it matters |
|----------|------|-------|----------------|
| Brute‑Force (HashSet) | **O(m × n × min(m,n))** | `O(min(m,n))` per cell | Intuitive but slow |
| Optimised (Bit‑Mask) | **O(m × n)** | `O(m × n)` for two 64‑bit masks | Linear, passes all LeetCode test‑cases and contests |

* **Constant‑Factor Gain**:  
  * Java: `Long.bitCount` is a single CPU instruction.  
  * Python: `int.bit_count()` is also highly optimised in CPython 3.10+.  
  * C++: `__builtin_popcountll` is a single machine instruction.

Because grid values are bounded to 50, a 64‑bit mask is a perfect fit.  
If the value range were larger, you’d revert to `HashSet` or use a `HashMap<value, count>` for each diagonal prefix.

---

## 7. Interview‑Ready Tips

1. **Explain the problem in your own words** – “For each cell we look only at the diagonal that goes up‑left and the one that goes down‑right.”  
2. **Ask clarifying questions** – e.g., “Is the diagonal length always the same?”  
3. **Show the brute‑force first** – gives the interviewer a baseline.  
4. **Introduce the bit‑mask trick** – “Because values are ≤ 50, we can treat them as bits.”  
5. **Discuss pop‑count** – many interviewers love you when you mention `__builtin_popcountll` / `Long.bitCount`.  
6. **Edge‑Case check** – highlight how your code handles a single‑cell grid or non‑square matrices.  

---

## 8. Conclusion – Your Next Interview Edge

LeetCode 2711 is more than just a diagonal problem – it’s a showcase of:

* **Set theory** (distinct elements on a diagonal)  
* **Dynamic‑programming flavour** (prefix masks)  
* **Bit‑level optimisation** (64‑bit masks)  
* **Language‑agnostic implementation** (Java, Python, C++)

Drop the brute‑force version to impress, keep the brute‑force in your notes for quick debugging, and always be ready to explain the `O(m*n)` bit‑mask solution in the interview room. Good luck – you’re now one step closer to landing that software engineering role!