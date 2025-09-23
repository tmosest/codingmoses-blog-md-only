---
title: LeetCode 3242. Design Neighbor Sum Service - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview  

**Design Neighbor Sum Service** – a LeetCode‑style problem that asks you to support two constant‑time queries on an *n × n* grid:

| **Query** | **What to compute** |
|-----------|---------------------|
| `adjacentSum(value)` | Sum of the four cardinal neighbours (top, bottom, left, right) that exist. |
| `diagonalSum(value)` | Sum of the four diagonal neighbours (top‑left, top‑right, bottom‑left, bottom‑right) that exist. |

The grid contains **distinct** integers, `0 ≤ value < n²`, and `n` satisfies `3 ≤ n ≤ 50`.  
You will receive up to `2·10⁵` queries – the service must answer each query in **O(1)** time.

> **Why this problem matters for your interview prep**  
>  Many data‑structure questions in tech‑interviews revolve around *grid* traversal and *hash‑mapping*. Mastering this problem gives you a reusable pattern for “lookup by value” problems, a must‑know for any Java, Python or C++ interview.

---

## 2.  Design – “Good, Bad & Ugly”  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Pre‑processing** | Build a hash‑map `value → (row, col)`.  This converts a naïve O(n²) search per query into O(1). | Do nothing – each query would scan the entire grid. | Build a gigantic 3‑D array (row, col, value) that is wasteful. |
| **Memory** | Store the grid as-is (`vector<vector<int>>` / `int[][]`) plus a map (`unordered_map<int, pair<int,int>>`). | Allocate temporary coordinates every query. | Recursively search the grid on every call – stack overflow risk on large grids. |
| **Edge‑case handling** | Explicit bounds checks (`i>0`, `i<n-1`, …). | Forget to handle borders → `IndexOutOfBounds`. | Use 1‑based indexing but the grid is 0‑based → off‑by‑one errors. |
| **Readability** | Clear method names, comments, and a simple data‑class for coordinate pairs. | Too many global variables, hidden state. | Over‑complicated recursion or lambda hacks that hurt maintainability. |

**Bottom line** – The “good” design uses a **hash‑map** for instant lookup, keeps the code clean, and handles borders safely. The “bad” variant is the brute‑force search; the “ugly” variant mixes recursive logic with shared mutable state, making it hard to reason about.

---

## 3.  Implementation  

### 3.1  Java (O(1) Query with `HashMap`)  

```java
import java.util.HashMap;
import java.util.Map;

/**
 *  Design Neighbor Sum Service
 *  https://leetcode.com/problems/design-neighbor-sum-service
 *
 *  Time Complexity
 *      Construction : O(n²)
 *      Queries      : O(1)
 *
 *  Space Complexity
 *      O(n²) for the grid + O(n²) for the hash map
 *
 *  Author:  [Your Name]
 *  Date:    2024‑08‑31
 */
public class NeighborSum {

    /** original matrix */
    private final int[][] matrix;
    /** size of the grid */
    private final int n;
    /** value -> (row, col) */
    private final Map<Integer, int[]> indexMap;

    /** Constructor: pre‑process grid in O(n²) */
    public NeighborSum(int[][] grid) {
        this.n = grid.length;
        this.matrix = new int[n][n];
        this.indexMap = new HashMap<>(n * n);

        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int val = grid[i][j];
                matrix[i][j] = val;
                indexMap.put(val, new int[]{i, j});
            }
        }
    }

    /** Sum of adjacent neighbours (up, down, left, right) */
    public int adjacentSum(int value) {
        int[] pos = indexMap.get(value);
        int i = pos[0], j = pos[1];
        int sum = 0;

        if (i > 0)     sum += matrix[i - 1][j]; // top
        if (i + 1 < n) sum += matrix[i + 1][j]; // bottom
        if (j > 0)     sum += matrix[i][j - 1]; // left
        if (j + 1 < n) sum += matrix[i][j + 1]; // right
        return sum;
    }

    /** Sum of diagonal neighbours (4 diagonals) */
    public int diagonalSum(int value) {
        int[] pos = indexMap.get(value);
        int i = pos[0], j = pos[1];
        int sum = 0;

        if (i > 0 && j > 0)           sum += matrix[i - 1][j - 1]; // top‑left
        if (i > 0 && j + 1 < n)       sum += matrix[i - 1][j + 1]; // top‑right
        if (i + 1 < n && j > 0)       sum += matrix[i + 1][j - 1]; // bottom‑left
        if (i + 1 < n && j + 1 < n)   sum += matrix[i + 1][j + 1]; // bottom‑right
        return sum;
    }

    /** ---------------- Demo ---------------- */
    public static void main(String[] args) {
        int[][] grid = {
            {0, 1, 2},
            {3, 4, 5},
            {6, 7, 8}
        };
        NeighborSum ns = new NeighborSum(grid);

        System.out.println("adjacentSum(4) = " + ns.adjacentSum(4));   // 16
        System.out.println("diagonalSum(4) = " + ns.diagonalSum(4));   // 16
    }
}
```

---

### 3.2  Python 3 (Dictionary Lookup)  

```python
# Design Neighbor Sum Service – Python implementation
# Time Complexity: Construction O(n²), Queries O(1)
# Space Complexity: O(n²)

class NeighborSum:
    def __init__(self, grid):
        """
        :param grid: List[List[int]] – n x n matrix of distinct integers
        """
        self.n = len(grid)
        self.grid = grid
        # Map value -> (row, col)
        self.idx = {grid[i][j]: (i, j) for i in range(self.n) for j in range(self.n)}

    def adjacentSum(self, value: int) -> int:
        i, j = self.idx[value]
        s = 0
        if i > 0:        s += self.grid[i - 1][j]   # top
        if i + 1 < self.n: s += self.grid[i + 1][j] # bottom
        if j > 0:        s += self.grid[i][j - 1]   # left
        if j + 1 < self.n: s += self.grid[i][j + 1] # right
        return s

    def diagonalSum(self, value: int) -> int:
        i, j = self.idx[value]
        s = 0
        if i > 0 and j > 0:            s += self.grid[i - 1][j - 1]   # top‑left
        if i > 0 and j + 1 < self.n:   s += self.grid[i - 1][j + 1]   # top‑right
        if i + 1 < self.n and j > 0:   s += self.grid[i + 1][j - 1]   # bottom‑left
        if i + 1 < self.n and j + 1 < self.n: s += self.grid[i + 1][j + 1] # bottom‑right
        return s

# Demo
if __name__ == "__main__":
    grid = [[0, 1, 2], [3, 4, 5], [6, 7, 8]]
    ns = NeighborSum(grid)
    print(ns.adjacentSum(4))  # 16
    print(ns.diagonalSum(4))  # 16
```

---

### 3.3  C++17 (Vector + Unordered_map)  

```cpp
// Design Neighbor Sum Service – C++17 implementation
// Compile with: g++ -std=c++17 -O2 -Wall neighbor_sum.cpp -o neighbor_sum

#include <bits/stdc++.h>
using namespace std;

class NeighborSum {
public:
    NeighborSum(const vector<vector<int>>& grid)
        : n(grid.size()), matrix(grid)
    {
        // Build hash map: value -> (row, col)
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < n; ++j)
                pos[grid[i][j]] = {i, j};
    }

    int adjacentSum(int value) const {
        auto [i, j] = pos.at(value);
        int sum = 0;
        if (i > 0)        sum += matrix[i - 1][j];   // top
        if (i + 1 < n)    sum += matrix[i + 1][j];   // bottom
        if (j > 0)        sum += matrix[i][j - 1];   // left
        if (j + 1 < n)    sum += matrix[i][j + 1];   // right
        return sum;
    }

    int diagonalSum(int value) const {
        auto [i, j] = pos.at(value);
        int sum = 0;
        if (i > 0 && j > 0)           sum += matrix[i - 1][j - 1]; // top‑left
        if (i > 0 && j + 1 < n)       sum += matrix[i - 1][j + 1]; // top‑right
        if (i + 1 < n && j > 0)       sum += matrix[i + 1][j - 1]; // bottom‑left
        if (i + 1 < n && j + 1 < n)   sum += matrix[i + 1][j + 1]; // bottom‑right
        return sum;
    }

private:
    int n;
    vector<vector<int>> matrix;
    unordered_map<int, pair<int,int>> pos;   // value -> coordinates
};

/* ---------- Demo ---------- */
int main() {
    vector<vector<int>> grid = {
        {0, 1, 2},
        {3, 4, 5},
        {6, 7, 8}
    };

    NeighborSum ns(grid);
    cout << "adjacentSum(4) = " << ns.adjacentSum(4) << '\n';   // 16
    cout << "diagonalSum(4) = " << ns.diagonalSum(4) << '\n';   // 16
}
```

---

## 4.  Common Pitfalls & How to Avoid Them  

| Pitfall | Fix |
|---------|-----|
| **O(n²) scan per query** | Store coordinates in a hash‑map (`value → (row, col)`). |
| **Missing boundary checks** | Always guard with `if (row>0)`, `if (col<n-1)` … before accessing `matrix[row][col]`. |
| **Off‑by‑one errors** | Stick to 0‑based indexing for both the grid and the map. |
| **Mutable shared state in recursion** | Avoid recursion; use the map to keep the logic iterative and pure. |

---

## 5.  Interview Tips  

1. **Explain the map first** – “I’ll pre‑compute a lookup table so that every query is O(1).”  
2. **Show the boundary logic** – “Notice that the top row has no ‘up’ neighbour, so we guard with `row>0`.”  
3. **Talk about memory trade‑offs** – “The grid itself is O(n²), the map is also O(n²). Given `n ≤ 50`, that’s trivial for modern servers.”  
4. **Use a clean, testable function** – write a small `demo()` or `main()` that prints expected values; interviewers love to see quick correctness checks.  
5. **Time‑complexity math** – “Construction takes 2,500 steps for a 50×50 grid; each query is just 4 or 8 lookups, i.e., constant time.”

---

## 6.  Final Takeaway  

The *Design Neighbor Sum Service* problem is a **pattern‑builder** for interviewers and interviewees alike:

* **Pre‑processing + hash‑map** = instant lookup, safe borders, clean code.  
* Brute‑force search = too slow and error‑prone.  
* Recursive “ugly” solutions hurt readability and can break on large inputs.

By mastering the “good” solution in **Java, Python and C++**, you’ll have a reusable toolkit for **value‑by‑value lookup** questions that appear in almost every tech interview. Good luck—your next coding round is now a little easier to crack!