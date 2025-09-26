---
title: LeetCode 1039. Minimum Score Triangulation of Polygon - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 1039 – Minimum Score Triangulation of Polygon  
*Medium* | *Dynamic Programming* | *Matrix‑Chain Multiplication Variant*  

---

### TL;DR  
- **Goal** – Split a convex polygon into `n‑2` triangles such that the sum of  
  `value[i] * value[j] * value[k]` for every triangle is minimal.  
- **Solution** – Classic **interval DP** (a variation of Matrix‑Chain Multiplication).  
- **Complexities** – `O(n³)` time, `O(n²)` space.  

---

## 🔍 Problem Recap  

You are given an array `values` (`3 ≤ n ≤ 50`) where `values[i]` is the weight of vertex `i` of a convex `n`‑sided polygon.  
A triangulation is a set of `n‑2` triangles whose vertices are vertices of the polygon.  
The score of a triangulation = sum of the product of the values of the three vertices of every triangle.  

Return the *minimum* possible score.

> **Example**  
> `values = [3,7,4,5]` → minimal score = **144** (triangulation: `(3,4,5)` + `(3,4,7)`).

---

## 🧠 Intuition – “Matrix‑Chain Multiplication” Meets Geometry  

If you look at a sub‑polygon defined by vertices `i … j`, you can pick a vertex `k` (`i < k < j`) as the third vertex of a triangle that uses the edge `i‑j`.  

```
i ----- k ----- j
|             |
|             |
```

The score of this triangle is `values[i] * values[j] * values[k]`.  
The remaining polygon is split into two independent sub‑polygons: `i … k` and `k … j`.  
Thus, the minimal score for `i … j` can be expressed recursively:

```
dp[i][j] = min over k ( dp[i][k] + dp[k][j] + values[i]*values[j]*values[k] )
```

When the sub‑polygon has only two vertices (`j == i+1`), no triangle can be formed → score `0`.

This is exactly the recurrence used in Matrix‑Chain Multiplication, only the “cost” term is a product of three numbers instead of a matrix multiplication cost.

---

## 🏗️ Algorithm Design  

| Step | Action |
|------|--------|
| 1 | Initialize a 2‑D array `dp[n][n]` with `-1` (or `∞` for bottom‑up). |
| 2 | Recursively compute `dp[i][j]` using memoization (top‑down) or iterate over increasing sub‑polygon lengths (bottom‑up). |
| 3 | For each `i < k < j` evaluate `score = dp[i][k] + dp[k][j] + values[i]*values[j]*values[k]`. |
| 4 | Store the minimum in `dp[i][j]`. |
| 5 | Result is `dp[0][n-1]`. |

---

## 📦 Code in Three Languages  

> **Note:** All implementations follow the same logic – top‑down memoization.  
> Bottom‑up can be added in comments if you prefer an iterative version.

### 1️⃣ Java

```java
import java.util.Arrays;

public class Solution {
    public int minScoreTriangulation(int[] values) {
        int n = values.length;
        int[][] dp = new int[n][n];
        for (int[] row : dp) Arrays.fill(row, -1);
        return dfs(values, 0, n - 1, dp);
    }

    private int dfs(int[] values, int i, int j, int[][] dp) {
        if (i >= j - 1) return 0;              // less than 3 vertices
        if (dp[i][j] != -1) return dp[i][j];

        int best = Integer.MAX_VALUE;
        for (int k = i + 1; k < j; k++) {
            int cost = values[i] * values[j] * values[k]
                    + dfs(values, i, k, dp)
                    + dfs(values, k, j, dp);
            if (cost < best) best = cost;
        }
        dp[i][j] = best;
        return best;
    }
}
```

### 2️⃣ Python

```python
class Solution:
    def minScoreTriangulation(self, values: list[int]) -> int:
        n = len(values)
        memo = [[None] * n for _ in range(n)]

        def dfs(i: int, j: int) -> int:
            if i >= j - 1:                     # < 3 vertices
                return 0
            if memo[i][j] is not None:
                return memo[i][j]

            best = float("inf")
            for k in range(i + 1, j):
                cost = (
                    values[i] * values[j] * values[k]
                    + dfs(i, k)
                    + dfs(k, j)
                )
                best = min(best, cost)

            memo[i][j] = best
            return best

        return dfs(0, n - 1)
```

### 3️⃣ C++

```cpp
#include <vector>
#include <algorithm>
#include <climits>

class Solution {
public:
    int minScoreTriangulation(std::vector<int>& values) {
        int n = values.size();
        std::vector<std::vector<int>> dp(n, std::vector<int>(n, -1));
        return dfs(values, 0, n - 1, dp);
    }

private:
    int dfs(const std::vector<int>& v, int i, int j, std::vector<std::vector<int>>& dp) {
        if (i >= j - 1) return 0;            // less than 3 vertices
        if (dp[i][j] != -1) return dp[i][j];

        int best = INT_MAX;
        for (int k = i + 1; k < j; ++k) {
            int cost = v[i] * v[j] * v[k]
                     + dfs(v, i, k, dp)
                     + dfs(v, k, j, dp);
            best = std::min(best, cost);
        }
        dp[i][j] = best;
        return best;
    }
};
```

---

## 📈 Complexity Analysis  

| Technique | Time | Space |
|-----------|------|-------|
| Top‑down memoization (all 3 codes) | **O(n³)** – `O(n²)` sub‑problems, each scans `O(n)` splits. | **O(n²)** – DP table + recursion stack (`O(n)` depth). |
| Bottom‑up (not shown) | Same | Same |

With `n ≤ 50`, the algorithm easily fits into the limits (≈125 000 iterations).

---

## 📜 “The Good, The Bad, and The Ugly”  

| Category | Discussion |
|----------|------------|
| **Good** | • Intuitive recurrence – easy to understand. <br>• Requires only `O(n²)` memory. <br>• Works for all convex polygons (no hidden cases). |
| **Bad** | • Time is cubic; for very large `n` it would become heavy (though `n=50` is fine). <br>• The product `values[i] * values[j] * values[k]` could overflow 32‑bit int in extreme cases (if values were bigger). In the official problem bounds (`≤100`) it’s safe. |
| **Ugly** | • The DP recurrence is *not* obvious to novices; they may think “matrix multiplication” and get confused. <br>• Memoization needs careful base‑case handling (`i >= j-1`). A typo there leads to stack overflow or wrong answers. <br>• In languages with small int ranges (e.g., JavaScript), you must use BigInt. |

**Take‑away:** Focus on a clear base case, use long integers where needed, and test edge cases (`n=3`, all equal values, ascending/descending arrays).

---

## 📈 SEO‑Optimized Blog Title & Meta Description  

**Title:**  
> “Minimum Score Triangulation of Polygon – LeetCode 1039 | Java, Python, C++ Solutions”

**Meta Description:**  
> “Learn how to solve LeetCode 1039 (Minimum Score Triangulation of Polygon) with top‑down memoization. Full Java, Python, and C++ code, complexity analysis, and a deep dive into the good, bad, and ugly of DP solutions.”

---

## 📚 Final Thoughts  

- **Why DP?** Because the optimal triangulation of a sub‑polygon depends only on its endpoints, a perfect fit for interval DP.  
- **Why memoization?** Recursion keeps the code clean, and the problem size (`n ≤ 50`) guarantees no stack issues.  
- **When to switch to bottom‑up?** If you need to avoid recursion for very deep recursion limits or want to pre‑allocate the DP table explicitly.

With the code snippets and explanation above, you can confidently tackle **LeetCode 1039** in interviews, blog posts, or coding competitions. Happy coding! 🚀