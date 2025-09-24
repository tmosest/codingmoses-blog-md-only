---
title: LeetCode 2128. Remove All Ones With Row and Column Flips - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Remove All Ones with Row and Column Flips” – 2128 LeetCode  
> **Good, Bad & Ugly** – A deep‑dive into a medium‑level interview problem with working Java, Python & C++ solutions.  

---

## Table of Contents  

| # | Section | Link |
|---|---------|------|
| 1 | Problem Overview | #problem |
| 2 | Intuition | #intuition |
| 3 | Formal Algorithm | #algorithm |
| 4 | Complexity | #complexity |
| 5 | Edge Cases & Pitfalls | #pitfalls |
| 6 | Code – Java | #java |
| 7 | Code – Python | #python |
| 8 | Code – C++ | #cpp |
| 9 | Good / Bad / Ugly | #goodbad |
| 10 | How this Helps Your Resume | #resume |
| 11 | Next Steps | #next |

> **SEO Keywords** – LeetCode 2128, Remove All Ones, Row & Column Flips, Java solution, Python solution, C++ solution, coding interview, algorithm design, interview prep.

---

## 1. Problem Overview <a name="problem"></a>

**LeetCode 2128 – Remove All Ones with Row and Column Flips**  

> *You are given an `m × n` binary matrix `grid`. In one operation you may choose any row or column and flip every value in it (0→1, 1→0). Return `true` if it is possible to remove all 1’s from the grid using any number of operations, otherwise return `false`.*

**Constraints**

* `1 ≤ m, n ≤ 300`  
* `grid[i][j] ∈ {0, 1}`  

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[[0,1,0],[1,0,1],[0,1,0]]` | `true` | Flip middle row → middle column |
| `[[1,1,0],[0,0,0],[0,0,0]]` | `false` | No sequence of flips clears all 1’s |
| `[[0]]` | `true` | Already all 0’s |

---

## 2. Intuition <a name="intuition"></a>

The key insight is that **once we decide which columns to flip, we can never flip a column again** – otherwise we would undo our progress in the first row.  

1. **Target the first row.**  
   * If a cell in the first row is `1`, we must flip its column.  
   * After flipping all such columns, the first row becomes all `0`s.

2. **Rows become simple.**  
   * For any remaining row, every element is either `0` or `1`.  
   * If all elements in a row are the same, we can flip the row (if all `1`) or leave it (if all `0`).  
   * If a row contains both `0` and `1`, no sequence of flips can make it all `0`s.

Thus the problem boils down to a single scan of the matrix:  
* Flip columns where the first row has a `1`.  
* Check every other row for homogeneity.

---

## 3. Formal Algorithm <a name="algorithm"></a>

```
1. Let m = number of rows, n = number of columns.
2. For every column j (0 … n-1):
        if grid[0][j] == 1:
                flip column j   // toggle each cell in column j
3. For each row i from 1 to m-1:
        for each column j from 1 to n-1:
                if grid[i][j] != grid[i][j-1]:
                        return false   // row has mixed values
4. Return true
```

*Flipping a column* simply toggles every element: `grid[i][j] = 1 - grid[i][j]`.

---

## 4. Complexity <a name="complexity"></a>

* **Time:**  
  * Step 2 visits each cell once → `O(mn)`  
  * Step 3 visits each cell once → `O(mn)`  
  * Total: `O(mn)` (≤ 90 000 operations for the given limits).

* **Space:**  
  * In‑place modifications, no extra arrays → `O(1)` auxiliary space.

---

## 5. Edge Cases & Common Pitfalls <a name="pitfalls"></a>

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Assuming we can flip columns after rows** | Flipping a column after the first row re‑introduces 1’s in the first row. | Never flip a column after handling the first row. |
| **Off‑by‑one errors when checking rows** | Forgetting to start inner loop at `j=1`. | Compare each cell with its predecessor or simply check `allSame(row)`. |
| **Mutable vs. immutable grid** | In languages like Python, reassigning the grid inside a helper function may not affect the outer scope. | Return the modified grid or modify in place. |
| **Large input handling** | Using recursion or deep copies can exceed stack limits. | Stick to iterative loops and in‑place changes. |

---

## 6. Code – Java <a name="java"></a>

```java
import java.util.stream.IntStream;

public class Solution {
    // Main entry point
    public boolean removeOnes(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;

        // 1. Flip columns where first row has 1
        for (int j = 0; j < n; j++) {
            if (grid[0][j] == 1) {
                flipColumn(j, grid);
            }
        }

        // 2. Every other row must be homogeneous
        for (int i = 1; i < m; i++) {
            int firstVal = grid[i][0];
            for (int j = 1; j < n; j++) {
                if (grid[i][j] != firstVal) {
                    return false;            // mixed values
                }
            }
        }
        return true;                         // all rows homogeneous
    }

    // Helper: flip a whole column
    private void flipColumn(int col, int[][] grid) {
        for (int row = 0; row < grid.length; row++) {
            grid[row][col] = 1 - grid[row][col];
        }
    }

    // -------------------------------------------------------
    // Optional: quick test harness
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[][] grid1 = {{0,1,0},{1,0,1},{0,1,0}};
        int[][] grid2 = {{1,1,0},{0,0,0},{0,0,0}};
        System.out.println(sol.removeOnes(grid1)); // true
        System.out.println(sol.removeOnes(grid2)); // false
    }
}
```

> **Why this works**  
> The first loop guarantees the first row is all zeros.  
> The second loop verifies every remaining row is either all zeros or all ones; we can flip those rows if needed.  
> Complexity is `O(mn)` and the algorithm is fully in‑place (`O(1)` auxiliary space).

---

## 7. Code – Python <a name="python"></a>

```python
from typing import List

class Solution:
    def removeOnes(self, grid: List[List[int]]) -> bool:
        m, n = len(grid), len(grid[0])

        # 1. Flip columns where first row has 1
        for j in range(n):
            if grid[0][j] == 1:
                for i in range(m):
                    grid[i][j] ^= 1  # toggle 0↔1

        # 2. Check homogeneity of each row (except first)
        for i in range(1, m):
            first = grid[i][0]
            for j in range(1, n):
                if grid[i][j] != first:
                    return False

        return True

# -------------------------------------------------------
if __name__ == "__main__":
    sol = Solution()
    print(sol.removeOnes([[0,1,0],[1,0,1],[0,1,0]]))  # True
    print(sol.removeOnes([[1,1,0],[0,0,0],[0,0,0]]))  # False
```

> **Pythonic Notes**  
> • `^= 1` is a clean toggle.  
> • The algorithm is identical to the Java version – linear time, constant space.

---

## 8. Code – C++ <a name="cpp"></a>

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool removeOnes(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();

        // 1. Flip columns where first row has 1
        for (int j = 0; j < n; ++j) {
            if (grid[0][j] == 1) {
                for (int i = 0; i < m; ++i) {
                    grid[i][j] ^= 1;   // toggle
                }
            }
        }

        // 2. Each remaining row must be homogeneous
        for (int i = 1; i < m; ++i) {
            int first = grid[i][0];
            for (int j = 1; j < n; ++j) {
                if (grid[i][j] != first) return false;
            }
        }
        return true;
    }
};

// -------------------------------------------------------
// quick test
/*
#include <iostream>
int main() {
    Solution sol;
    vector<vector<int>> g1 = {{0,1,0},{1,0,1},{0,1,0}};
    vector<vector<int>> g2 = {{1,1,0},{0,0,0},{0,0,0}};
    cout << boolalpha << sol.removeOnes(g1) << endl; // true
    cout << boolalpha << sol.removeOnes(g2) << endl; // false
}
*/
```

> **C++ Highlights**  
> • Uses bitwise XOR for toggling.  
> • Keeps memory footprint minimal (`O(1)` extra).  
> • Same linear time complexity as the other solutions.

---

## 9. Good / Bad / Ugly <a name="goodbad"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Conceptual Clarity** | The “first row → columns → rows” decomposition is elegant and hard‑to‑miss. | If you mis‑order operations you get tangled logic. | Trying to brute‑force every subset of flips is insane (exponential). |
| **Implementation** | Straightforward loops, no recursion, easy to read. | Mistakes in index bounds (especially the inner row check) are common. | Using mutable state incorrectly (e.g., copying the grid) can lead to subtle bugs. |
| **Performance** | `O(mn)` fits comfortably into 300×300 limits. | None – the algorithm is already optimal. | Any solution that allocates an extra `mn` boolean array for visited cells is wasteful but still works. |
| **Space** | In‑place modifications → `O(1)` aux. | None. | If you store all possible flip masks you blow up memory. |
| **Proof** | Simple invariant: “first row stays all zeros”. | Proving that rows must be homogeneous is easy but often forgotten. | Skipping the proof leads to incorrect “answers” that happen to work on samples. |

---

## 10. Final Thoughts <a name="final"></a>

LeetCode 3078 is a great exercise in **problem decomposition**. Once you understand the first row strategy, the rest of the matrix behaves almost like a trivial pattern recognition task. All three languages—Java, Python, C++—share the same minimal footprint and linear runtime, making them suitable for interviewers who expect clean, efficient code.

If you’re preparing for technical interviews, remember: **focus on the core invariant** (first row → columns) and avoid over‑engineering. This simple algorithm will impress both human and machine judges alike.

Happy coding! 🚀

---

### References & Further Reading

1. LeetCode Problem 3078 – “Clear All Bits by Flipping Rows or Columns”.  
2. Common interview patterns: *“row‑by‑row or column‑by‑column”* reductions.  
3. Bit‑wise operations for toggling: XOR (`^`) is the idiomatic trick across languages.

---

> **Author** – Your friendly LeetCode problem‑solver.  
> Follow me for more concise explanations, clean code, and interview‑ready solutions.