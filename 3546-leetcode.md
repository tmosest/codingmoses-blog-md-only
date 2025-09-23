---
title: LeetCode 3546. Equal Sum Grid Partition I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Equal‑Sum Grid Partition I – The Good, The Bad & The Ugly  
> **SEO Keywords:** Equal Sum Grid Partition, Leetcode 3546, job interview, Java solution, Python solution, C++ solution, dynamic programming, prefix sums, algorithm interview

---

## 1. 🚀 Why This Problem Rocks Your Resume

- **Leetcode 3546** – a *Medium* level problem that many front‑end, back‑end and data‑engineering roles ask in a coding interview.
- The question tests a few hard‑core concepts:  
  - 2‑D array traversal  
  - Prefix / cumulative sums  
  - Edge‑case handling  
  - Optimal time & space complexity  
- It’s short, but it’s a *classic* problem that shows you understand how to convert a brute‑force idea into an **O(m + n)** solution.

If you can nail this, you’ll look like a solid candidate who knows how to solve matrix problems efficiently.

---

## 2. 📝 Problem Restatement

Given an `m × n` grid of **positive** integers:

```
grid[i][j]  (0 ≤ i < m, 0 ≤ j < n)
```

You can make **exactly one** cut – either horizontal (between two rows) or vertical (between two columns) – that divides the grid into two non‑empty parts.  
Return `true` if the two parts can have **equal sums**, otherwise `false`.

Constraints  
- `1 ≤ m, n ≤ 10^5`  
- `2 ≤ m * n ≤ 10^5`  
- `1 ≤ grid[i][j] ≤ 10^5`

---

## 3. 🧠 Intuition – “Don’t Scan the Whole Grid Every Time”

A naive approach would be:

1. Try every horizontal cut, compute the sum of the top half each time → **O(m × n)**  
2. Try every vertical cut similarly → **O(m × n)**  

With `m * n` up to `10^5`, this is fine, but we can do better.

**Key observation** – The sum of the first *k* rows only depends on the sum of each row, not on the individual cells inside those rows.  
Same for columns.

So we can:

1. Compute **row sums** (`rowSum[i] = Σ grid[i][j]`) – `O(m × n)`  
2. Compute **column sums** (`colSum[j] = Σ grid[i][j]`) – `O(m × n)`  
3. Compute prefix sums over `rowSum` and `colSum` – `O(m + n)`  
4. Compare prefix values to the total sum – **O(m + n)**

Total: **O(m × n)** time, **O(m + n)** extra space – optimal.

---

## 4. 📐 Algorithm Walk‑through

1. **Read the grid** and determine `m` (rows) and `n` (columns).
2. **Initialize** two long arrays  
   - `rowSum[m]` – cumulative sum of each row  
   - `colSum[n]` – cumulative sum of each column
3. **First pass** (single loop over all cells):  
   ```java
   for i in 0..m-1:
       for j in 0..n-1:
           val = grid[i][j];
           rowSum[i] += val;
           colSum[j] += val;
   ```
   Using `long` protects us from overflow (`max total sum ≈ 10^10`).
4. Compute **total sum**: `total = Σ rowSum` (same as `Σ colSum`).
5. **Horizontal cut check**:  
   ```java
   long upper = 0;
   for i in 0..m-2:          // cut must leave at least one row below
       upper += rowSum[i];
       if (upper == total - upper) return true;
   ```
6. **Vertical cut check**:  
   ```java
   long left = 0;
   for j in 0..n-2:
       left += colSum[j];
       if (left == total - left) return true;
   ```
7. **Return** `false` if no cut works.

---

## 5. 🔍 Edge Cases & “The Ugly”

| Case | Why it matters | How we handle it |
|------|----------------|------------------|
| `m == 1` (only one row) | Only vertical cuts possible | Loop `m-2` becomes `-1` → no iterations |
| `n == 1` | Only horizontal cuts possible | Same logic |
| `grid` values huge | Overflow | Use `long` (64‑bit) for all sums |
| Grid of size `1×2` or `2×1` | One cut only, check correctly | The loops correctly handle the single possible cut |
| Total sum odd | Impossible to split equally | The loops will never satisfy equality; we could early‑exit if `total % 2 != 0`, but not necessary. |

---

## 6. 📦 Code

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.  
Each version follows the same logic and runs in **O(m × n)** time and **O(m + n)** space.

### 6.1 Java

```java
/**
 * Leetcode 3546 – Equal Sum Grid Partition I
 * Java 17 implementation using prefix sums.
 */
public class Solution {
    public boolean canPartitionGrid(int[][] grid) {
        int m = grid.length;        // rows
        int n = grid[0].length;     // columns

        long[] rowSum = new long[m];
        long[] colSum = new long[n];
        long total = 0L;

        // First pass: build row and column sums
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                long val = grid[i][j];
                rowSum[i] += val;
                colSum[j] += val;
                total += val;          // total can be computed here
            }
        }

        // Horizontal cuts
        long upper = 0L;
        for (int i = 0; i < m - 1; i++) {
            upper += rowSum[i];
            if (upper == total - upper) {
                return true;
            }
        }

        // Vertical cuts
        long left = 0L;
        for (int j = 0; j < n - 1; j++) {
            left += colSum[j];
            if (left == total - left) {
                return true;
            }
        }

        return false;
    }
}
```

> **Why `long`?**  
> The product `m * n` can be 10⁵ and each cell up to 10⁵ → max sum 10¹⁰ < 2⁶³, but still exceeds 32‑bit.

### 6.2 Python

```python
"""
Leetcode 3546 – Equal Sum Grid Partition I
Python 3 implementation using prefix sums.
"""

def can_partition_grid(grid):
    m, n = len(grid), len(grid[0])
    row_sum = [0] * m
    col_sum = [0] * n
    total = 0

    # Build row and column sums
    for i in range(m):
        for j in range(n):
            val = grid[i][j]
            row_sum[i] += val
            col_sum[j] += val
            total += val

    # Horizontal cuts
    upper = 0
    for i in range(m - 1):
        upper += row_sum[i]
        if upper == total - upper:
            return True

    # Vertical cuts
    left = 0
    for j in range(n - 1):
        left += col_sum[j]
        if left == total - left:
            return True

    return False
```

> **Why not use `numpy`?**  
> The problem constraints are small enough for pure Python loops, and this keeps the solution platform‑agnostic.

### 6.3 C++

```cpp
/* Leetcode 3546 – Equal Sum Grid Partition I
   C++17 implementation using prefix sums
*/
class Solution {
public:
    bool canPartitionGrid(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<long long> rowSum(m, 0), colSum(n, 0);
        long long total = 0;

        // Build row and column sums
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                long long val = grid[i][j];
                rowSum[i] += val;
                colSum[j] += val;
                total += val;
            }
        }

        // Horizontal cuts
        long long upper = 0;
        for (int i = 0; i < m - 1; ++i) {
            upper += rowSum[i];
            if (upper == total - upper) return true;
        }

        // Vertical cuts
        long long left = 0;
        for (int j = 0; j < n - 1; ++j) {
            left += colSum[j];
            if (left == total - left) return true;
        }

        return false;
    }
};
```

> **64‑bit integers** (`long long`) are required for the same reason as Java’s `long`.

---

## 7. 📦 Test Harness (Optional)

You can quickly verify the solution in any language:

```javascript
// Java
int[][] g = {{1,2},{3,4}};
Solution s = new Solution();
System.out.println(s.canPartitionGrid(g)); // false

// Python
print(can_partition_grid([[5,1,1],[1,1,5]])) # true
```

---

## 7. 🚀 The “Good, The Bad & The Ugly”

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Algorithmic clarity** | Prefix sums → O(m + n) | A lot of interviewers still code the O(m × n) brute‑force version because it’s “quick & dirty”. | Forgetting to handle the *odd‑total* case is a classic slip‑up. |
| **Language choice** | Java, Python, C++ all look great on a resume | Using `int` for sums may lead to overflow bugs – interviewers will spot that. | Storing the entire grid in memory when you only need sums is wasteful. |
| **Edge‑case handling** | Loops `m-2` and `n-2` naturally skip impossible cuts | Missing the `-1` in the loop bounds causes an out‑of‑bounds error. | Forgetting to use 64‑bit integers breaks hidden tests. |
| **Readability** | Small, self‑explanatory helper arrays | Inline comments are essential. | Adding debug prints *inside* the loops will ruin performance. |

---

## 8. 🎉 What We Learned

- **Prefix sums** can turn a seemingly “matrix‑heavy” problem into a one‑dimensional scan.  
- Use **64‑bit integers** whenever you sum thousands of numbers that can each be up to `10⁵`.  
- A good interview answer isn’t just correct – it’s *explainable*, *optimised*, and *robust*.

---

## 9. 🌟 Final Take‑away

If you can walk an interviewer through this problem with:

1. The **intuitive observation** (row/column prefix sums)  
2. A clear **O(m + n)** algorithm diagram  
3. A brief **time‑space analysis**  
4. Handling of all edge cases  

you’ll demonstrate that you’re not just a coder, you’re a *problem‑solver*.

---

## 10. 🔗 Further Reading

- **Leetcode 3546**: [Problem on Leetcode](https://leetcode.com/problems/equal-sum-grid-partition-i/)
- **Prefix sums in 2‑D**: <https://www.geeksforgeeks.org/2d-prefix-sum/>
- **Interview‑style matrix problems**: <https://www.indeed.com/career-advice/interviewing/2d-array-problems>

Good luck, and may your next interview be as smooth as this solution!