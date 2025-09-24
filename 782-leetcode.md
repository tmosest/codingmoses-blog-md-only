---
title: LeetCode 782. Transform to Chessboard - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 🏰 Transform to Chessboard – LeetCode 782  
### The Good, the Bad, and the Ugly – A Complete, SEO‑Optimized Guide  

> **TL;DR** – In a single line you can turn a binary board into a chessboard by swapping rows and columns. The trick is to validate the pattern, count mismatches, fix the parity, and divide by two.  
> 
> Complexity: **O(n²)** time, **O(1)** auxiliary space.  
> 
> Code: **Java, Python, C++** – fully commented, ready for interview.

---

## Table of Contents

1. [Problem Statement](#problem-statement)  
2. [Why It’s Hard](#why-its-hard)  
3. [Solution Overview](#solution-overview)  
   * ✅ 1‑pass validity check  
   * ✅ Counting swaps for rows & columns  
   * ✅ Handling odd vs even sizes  
   * ✅ Final answer = (rowSwaps + colSwaps) / 2  
4. [Detailed Algorithm](#detailed-algorithm)  
5. [Proof of Correctness](#proof-of-correctness)  
6. [Complexity Analysis](#complexity-analysis)  
7. [Edge Cases & Pitfalls](#edge-cases--pitfalls)  
8. [Java Implementation](#java-implementation)  
9. [Python Implementation](#python-implementation)  
10. [C++ Implementation](#c-implementation)  
11. [Testing & Sample Runs](#testing--sample-runs)  
12. [Takeaway & Interview Tips](#takeaway--interview-tips)  
13. [SEO Keywords & Metadata](#seo-keys)  

---

## 1. Problem Statement

> **LeetCode 782 – Transform to Chessboard**  
> **Difficulty**: Hard  
> 
> Given an `n × n` binary grid `board`, you can swap any two rows or any two columns any number of times.  
> Return the **minimum number of moves** required to transform the board into a **chessboard** (i.e., no two adjacent cells share the same value). If it’s impossible, return `-1`.

---

## 2. Why It’s Hard

* **Constraint:**  `n` ≤ 30 – small enough for an `O(n²)` algorithm, but you still need a *linear‑time* validation trick.  
* **Parity Problem:** Even vs. odd board size changes the optimal number of swaps.  
* **Row/Column Symmetry:** Rows and columns are independent but must satisfy the same feasibility rules.  
* **Counting Swaps Efficiently:** You can’t simulate every swap; you need a formula based on mismatches.  

---

## 3. Solution Overview

1. **Feasibility Check**  
   * Every cell `(r, c)` must satisfy  
     `board[r][c] == board[0][0] ^ board[r][0] ^ board[0][c]`.  
   * If any cell violates this, the board can’t be transformed → return `-1`.

2. **Count Row & Column Mismatches**  
   * For rows: count how many rows match the pattern `[0,1,0,1,…]`.  
   * For columns: count how many columns match the pattern `[0,1,0,1,…]`.  
   * Also compute `rowSwap` = number of rows that already start with the correct parity (i.e., `board[r][0] == r % 2`).  
   * Similarly for `colSwap`.

3. **Validate Frequencies**  
   * The count of `1`s in any row or column must be either `⌊n/2⌋` or `⌈n/2⌉`.  
   * If not, transformation is impossible.

4. **Compute Minimal Swaps**  
   * **Even n:**  
     * `rowSwaps = min(rowSwap, n - rowSwap)`  
     * `colSwaps = min(colSwap, n - colSwap)`
   * **Odd n:**  
     * The starting parity must match the majority.  
     * If `rowSwap` is odd → `rowSwap = n - rowSwap`  
     * Likewise for `colSwap`.

5. **Answer**  
   * Each swap fixes two positions, so the total moves = `(rowSwaps + colSwaps) / 2`.

---

## 4. Detailed Algorithm (Step‑by‑Step)

```
n = board.length
rowSum = colSum = 0
rowSwap = colSwap = 0

// 1. Feasibility check
for r in 0..n-1:
    for c in 0..n-1:
        if (board[0][0] ^ board[r][0] ^ board[0][c] ^ board[r][c]) == 1:
            return -1

// 2. Count rows/cols that are “good” and mismatches
for i in 0..n-1:
    rowSum   += board[0][i]          // number of 1s in first row
    colSum   += board[i][0]          // number of 1s in first column
    rowSwap  += (board[i][0] == i%2) ? 1 : 0
    colSwap  += (board[0][i] == i%2) ? 1 : 0

// 3. Validate 1‑counts
if rowSum not in [n/2, (n+1)/2] or colSum not in [n/2, (n+1)/2]:
    return -1

// 4. Adjust swaps based on parity
if n % 2 == 1:          // odd board size
    if rowSwap % 2 == 1: rowSwap = n - rowSwap
    if colSwap % 2 == 1: colSwap = n - colSwap
else:                  // even board size
    rowSwap = min(rowSwap, n - rowSwap)
    colSwap = min(colSwap, n - colSwap)

// 5. Return minimal moves
return (rowSwap + colSwap) / 2
```

---

## 5. Proof of Correctness

1. **Feasibility Condition**  
   * For any two rows `r1, r2` and two columns `c1, c2`, the four cells `(r1,c1),(r1,c2),(r2,c1),(r2,c2)` must form a checkerboard pattern.  
   * Algebraically, this is equivalent to the XOR condition above.  
   * If the board fails this, no sequence of swaps can fix the pattern.

2. **Row/Column Swap Count**  
   * Swapping rows that are already in the desired pattern with those that aren’t reduces mismatches by exactly one each.  
   * The minimal number of swaps to make all rows alternating is `min(rowSwap, n-rowSwap)` for even `n`; for odd `n`, parity forces one exact orientation.

3. **Division by Two**  
   * A single swap of two rows corrects two positions (the two rows).  
   * Likewise for columns.  
   * Thus the total number of moves equals half the total mismatches.

The algorithm follows the above logical steps, guaranteeing minimal moves if the board is transformable.

---

## 6. Complexity Analysis

* **Time:**  
  * Feasibility check: `O(n²)`  
  * Counting loops: `O(n)` (actually `O(n)` for each of two passes, still `O(n)`)  
  * Overall: **O(n²)** (dominant by feasibility check)

* **Space:**  
  * Only a handful of integer counters → **O(1)** auxiliary space.

With `n ≤ 30`, this runs in microseconds on any modern machine.

---

## 7. Edge Cases & Pitfalls

| Case | What to Watch For | Fix |
|------|-------------------|-----|
| **Odd n** | Parity of `rowSwap`/`colSwap` must be even. | Flip if odd. |
| **Row/Col Count** | Number of 1s must be either `n/2` or `(n+1)/2`. | Return `-1` if violated. |
| **All zeros or all ones** | Feasibility fails immediately. | XOR check catches it. |
| **Large n (30)** | Still O(n²) – safe. | No optimization needed. |
| **Swapping same row twice** | Double counting. | Not needed—formula handles it. |

---

## 8. Java Implementation

```java
/**
 * 782. Transform to Chessboard
 *
 * Java solution that runs in O(n^2) time and O(1) space.
 * Implements the optimal swap counting strategy explained above.
 */
class Solution {
    public int movesToChessboard(int[][] board) {
        int n = board.length;
        int rowSum = 0, colSum = 0;   // number of 1s in first row/column
        int rowSwap = 0, colSwap = 0; // rows/cols already in correct parity

        /* ---------- 1. Feasibility check ---------- */
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                // board[r][c] must equal board[0][0] ^ board[r][0] ^ board[0][c]
                if (((board[0][0] ^ board[r][0] ^ board[0][c] ^ board[r][c]) & 1) == 1) {
                    return -1;
                }
            }
        }

        /* ---------- 2. Count rows/cols that fit ---------- */
        for (int i = 0; i < n; i++) {
            rowSum   += board[0][i];                // 1s in first row
            colSum   += board[i][0];                // 1s in first column
            if (board[i][0] == (i & 1)) rowSwap++;  // row starts with correct parity
            if (board[0][i] == (i & 1)) colSwap++;  // column starts with correct parity
        }

        /* ---------- 3. Validate 1-counts ---------- */
        if (rowSum < n / 2 || rowSum > (n + 1) / 2) return -1;
        if (colSum < n / 2 || colSum > (n + 1) / 2) return -1;

        /* ---------- 4. Adjust swaps based on parity ---------- */
        if ((n & 1) == 1) {   // odd board size
            if ((rowSwap & 1) == 1) rowSwap = n - rowSwap;
            if ((colSwap & 1) == 1) colSwap = n - colSwap;
        } else {              // even board size
            rowSwap = Math.min(rowSwap, n - rowSwap);
            colSwap = Math.min(colSwap, n - colSwap);
        }

        /* ---------- 5. Final answer ---------- */
        return (rowSwap + colSwap) / 2;
    }
}
```

---

## 9. Python Implementation

```python
"""
LeetCode 782 - Transform to Chessboard
Python 3 solution – O(n^2) time, O(1) space
"""

class Solution:
    def movesToChessboard(self, board: list[list[int]]) -> int:
        n = len(board)
        row_sum = col_sum = 0
        row_swap = col_swap = 0

        # 1. Feasibility check
        for r in range(n):
            for c in range(n):
                if (board[0][0] ^ board[r][0] ^ board[0][c] ^ board[r][c]) & 1:
                    return -1

        # 2. Count rows/cols that fit
        for i in range(n):
            row_sum   += board[0][i]
            col_sum   += board[i][0]
            if board[i][0] == (i & 1):  row_swap += 1
            if board[0][i] == (i & 1):  col_swap += 1

        # 3. Validate 1-counts
        if not (n//2 <= row_sum <= (n+1)//2): return -1
        if not (n//2 <= col_sum <= (n+1)//2): return -1

        # 4. Adjust swaps based on parity
        if n % 2:
            if row_swap % 2:  row_swap = n - row_swap
            if col_swap % 2:  col_swap = n - col_swap
        else:
            row_swap = min(row_swap, n - row_swap)
            col_swap = min(col_swap, n - col_swap)

        # 5. Return minimal moves
        return (row_swap + col_swap) // 2
```

---

## 10. C++ Implementation

```cpp
/**
 * 782. Transform to Chessboard
 *
 * C++ solution: O(n^2) time, O(1) space.
 * Uses the same optimal counting strategy.
 */
class Solution {
public:
    int movesToChessboard(vector<vector<int>>& board) {
        int n = board.size();
        int rowSum = 0, colSum = 0;   // number of 1s in first row/column
        int rowSwap = 0, colSwap = 0; // rows/cols already in correct parity

        /* ---------- 1. Feasibility check ---------- */
        for (int r = 0; r < n; ++r) {
            for (int c = 0; c < n; ++c) {
                if ((board[0][0] ^ board[r][0] ^ board[0][c] ^ board[r][c]) & 1) {
                    return -1;
                }
            }
        }

        /* ---------- 2. Count rows/cols that fit ---------- */
        for (int i = 0; i < n; ++i) {
            rowSum   += board[0][i];
            colSum   += board[i][0];
            if (board[i][0] == (i & 1)) ++rowSwap;
            if (board[0][i] == (i & 1)) ++colSwap;
        }

        /* ---------- 3. Validate 1-counts ---------- */
        if (rowSum < n/2 || rowSum > (n+1)/2) return -1;
        if (colSum < n/2 || colSum > (n+1)/2) return -1;

        /* ---------- 4. Adjust swaps based on parity ---------- */
        if (n & 1) {   // odd
            if (rowSwap & 1) rowSwap = n - rowSwap;
            if (colSwap & 1) colSwap = n - colSwap;
        } else {      // even
            rowSwap = min(rowSwap, n - rowSwap);
            colSwap = min(colSwap, n - colSwap);
        }

        /* ---------- 5. Final answer ---------- */
        return (rowSwap + colSwap) / 2;
    }
};
```

---

## 11. C Implementation

```c
/**
 * 782. Transform to Chessboard
 *
 * C solution – O(n^2) time, O(1) space.
 * Written in plain C for maximum portability.
 */
int movesToChessboard(int** board, int n) {
    int rowSum = 0, colSum = 0;
    int rowSwap = 0, colSwap = 0;

    /* ---------- 1. Feasibility check ---------- */
    for (int r = 0; r < n; r++) {
        for (int c = 0; c < n; c++) {
            if ((board[0][0] ^ board[r][0] ^ board[0][c] ^ board[r][c]) & 1)
                return -1;
        }
    }

    /* ---------- 2. Count rows/cols that fit ---------- */
    for (int i = 0; i < n; i++) {
        rowSum   += board[0][i];
        colSum   += board[i][0];
        if (board[i][0] == (i & 1)) rowSwap++;
        if (board[0][i] == (i & 1)) colSwap++;
    }

    /* ---------- 3. Validate 1-counts ---------- */
    if (rowSum < n/2 || rowSum > (n+1)/2) return -1;
    if (colSum < n/2 || colSum > (n+1)/2) return -1;

    /* ---------- 4. Adjust swaps based on parity ---------- */
    if (n & 1) {   // odd
        if (rowSwap & 1) rowSwap = n - rowSwap;
        if (colSwap & 1) colSwap = n - colSwap;
    } else {      // even
        rowSwap = rowSwap < n-rowSwap ? rowSwap : n-rowSwap;
        colSwap = colSwap < n-colSwap ? colSwap : n-colSwap;
    }

    /* ---------- 5. Final answer ---------- */
    return (rowSwap + colSwap) / 2;
}
```

> **Note:** In C, `int** board` is assumed to be a square 2‑D array.

---

## 12. Recap & Takeaway

* **Optimality** – the algorithm yields the *fewest* swaps possible.
* **Simplicity** – just a few integer counters; no heavy data structures.
* **Robustness** – handles odd/even sizes, frequency checks, parity adjustments.
* **Performance** – `O(n²)` is trivial for `n ≤ 30`, yet scales nicely for larger inputs.

This solution is a favorite in my interview repertoire and has proven to be both *fast* and *clean*.

---

### 📌 Final Thoughts

Transforming a board into a checkerboard isn’t just about swapping; it’s about understanding the underlying algebraic constraints and leveraging parity. By treating rows and columns symmetrically and counting mismatches cleverly, we can compute the minimal number of moves with a single pass over the matrix.

Happy coding, and good luck crushing that LeetCode interview! 🚀

---


> *If you liked this deep dive, check out my other algorithm explanations: [160. Intersection of Two Arrays II](https://leetcode.com/problems/intersection-of-two-arrays-ii/), [1729. Swapping Nodes in a Linked List](https://leetcode.com/problems/swapping-nodes-in-a-linked-list/).*