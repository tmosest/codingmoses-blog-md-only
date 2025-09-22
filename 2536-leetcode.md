---
title: LeetCode 2536. Increment Submatrices by One - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 2536 – **Increment Submatrices by One**  
### The Good, the Bad, and the Ugly

> **Keywords**: LeetCode 2536, Increment Submatrices by One, 2‑D prefix sum, difference array, range addition, coding interview, algorithm analysis, job interview

---

### 1. Problem Recap

You’re given an `n × n` zero‑initialised matrix `mat`.  
You receive an array of queries `queries[i] = [row1i, col1i, row2i, col2i]`.  
For every query you must add **1** to each element inside the sub‑matrix defined by the four indices.  
Return the final matrix after applying all queries.

> **Constraints**  
> * `1 ≤ n ≤ 500`  
> * `1 ≤ queries.length ≤ 10⁴`  
> * `0 ≤ row1i ≤ row2i < n`  
> * `0 ≤ col1i ≤ col2i < n`

---

### 2. Why the Brute‑Force Fails (The Bad)

A straightforward implementation iterates over every cell affected by a query:

```java
for (int[] q : queries) {
    for (int r = q[0]; r <= q[2]; r++)
        for (int c = q[1]; c <= q[3]; c++)
            mat[r][c]++;
}
```

* **Time complexity** – O(`q * n²`) in the worst case (every query covers the whole matrix).  
  With `q = 10⁴` and `n = 500`, that’s 2.5 × 10⁹ operations → TLE.  
* **Space** – O(1) extra, but the heavy runtime dominates.

So we need a *range addition* trick.

---

### 3. The Two‑Dimensional Difference Array (The Good)

The 1‑D trick for range updates is to mark the start and the element after the end, then take a prefix sum once at the end.  
In 2‑D we do the same with four “corner” updates:

| Operation | Effect |
|-----------|--------|
| `diff[r1][c1] += 1` | start of the rectangle |
| `diff[r1][c2+1] -= 1` | end‑right boundary |
| `diff[r2+1][c1] -= 1` | end‑bottom boundary |
| `diff[r2+1][c2+1] += 1` | counter‑balance |

After processing all queries we perform a **prefix sum over rows** then over **columns** (or vice‑versa).  
The resulting matrix is exactly the matrix after all increments.

#### Complexity

* **Time** – `O(n² + q)`  
  * Each query is processed in O(1).  
  * Two nested loops over the matrix for the prefix sums: `n²` operations.
* **Space** – `O(n²)` (the difference array, can reuse the result array).

With `n ≤ 500`, `n² = 250 000`, easily under the limits.

---

### 4. Edge Cases & Gotchas

| Situation | What to Watch Out For |
|-----------|-----------------------|
| Query touching the right/bottom edge | `c2 + 1 == n` or `r2 + 1 == n` – don’t go out of bounds. Use an `(n+1) × (n+1)` array or guard with `if` checks. |
| Negative indices (none in this problem) | Not needed, but remember the idea when extending to general range additions. |
| Large `n` but small `q` | Still O(`n²`) for the prefix sums – unavoidable because the output itself is `n²`. |
| Memory limits | `500 × 500` integers ≈ 1 MB – safe on most platforms. |

---

### 5. Alternative Optimisations (The Ugly)

| Approach | When It Helps |
|----------|---------------|
| **Row‑level diff** – store a list of “updates” per row, accumulate a 1‑D diff for that row | If you have many more rows than queries, you can avoid the second dimension entirely. |
| **Lazy prefix** – build prefix sums incrementally while applying queries | Saves a pass, but more code and harder to reason about. |
| **Sparse representation** – use hash maps for very sparse updates | Useful if `n` can be as large as 10⁶ and queries are few. |

These variants trade code complexity for marginal speed gains and are rarely needed for the LeetCode limits.

---

### 6. Reference Implementations

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All use the `(n+1) × (n+1)` difference array trick and two passes of prefix sums.

---

#### Java (LeetCode‑style)

```java
/**
 * LeetCode 2536: Increment Submatrices by One
 */
public class Solution {
    public int[][] rangeAddQueries(int n, int[][] queries) {
        // diff has an extra row/col to avoid boundary checks
        int[][] diff = new int[n + 1][n + 1];

        for (int[] q : queries) {
            int r1 = q[0], c1 = q[1];
            int r2 = q[2], c2 = q[3];
            diff[r1][c1] += 1;
            diff[r1][c2 + 1] -= 1;
            diff[r2 + 1][c1] -= 1;
            diff[r2 + 1][c2 + 1] += 1;
        }

        // Prefix sum over rows
        for (int r = 0; r < n; r++) {
            for (int c = 1; c < n; c++) {
                diff[r][c] += diff[r][c - 1];
            }
        }

        // Prefix sum over columns
        for (int r = 1; r < n; r++) {
            for (int c = 0; c < n; c++) {
                diff[r][c] += diff[r - 1][c];
            }
        }

        // Trim the extra row/col and return
        int[][] res = new int[n][n];
        for (int r = 0; r < n; r++) {
            System.arraycopy(diff[r], 0, res[r], 0, n);
        }
        return res;
    }
}
```

---

#### Python 3

```python
"""
LeetCode 2536: Increment Submatrices by One
"""
class Solution:
    def rangeAddQueries(self, n: int, queries: List[List[int]]) -> List[List[int]]:
        # +1 to safely perform corner updates
        diff = [[0] * (n + 1) for _ in range(n + 1)]

        for r1, c1, r2, c2 in queries:
            diff[r1][c1] += 1
            diff[r1][c2 + 1] -= 1
            diff[r2 + 1][c1] -= 1
            diff[r2 + 1][c2 + 1] += 1

        # Prefix over rows
        for r in range(n):
            for c in range(1, n):
                diff[r][c] += diff[r][c - 1]

        # Prefix over columns
        for r in range(1, n):
            for c in range(n):
                diff[r][c] += diff[r - 1][c]

        # Return the trimmed matrix
        return [row[:n] for row in diff[:n]]
```

---

#### C++ (GNU C++17)

```cpp
/**
 * LeetCode 2536: Increment Submatrices by One
 */
class Solution {
public:
    vector<vector<int>> rangeAddQueries(int n, vector<vector<int>>& queries) {
        // diff has one extra row/col for safety
        vector<vector<int>> diff(n + 1, vector<int>(n + 1, 0));

        for (const auto& q : queries) {
            int r1 = q[0], c1 = q[1];
            int r2 = q[2], c2 = q[3];
            diff[r1][c1] += 1;
            diff[r1][c2 + 1] -= 1;
            diff[r2 + 1][c1] -= 1;
            diff[r2 + 1][c2 + 1] += 1;
        }

        // Prefix sum over rows
        for (int r = 0; r < n; ++r)
            for (int c = 1; c < n; ++c)
                diff[r][c] += diff[r][c - 1];

        // Prefix sum over columns
        for (int r = 1; r < n; ++r)
            for (int c = 0; c < n; ++c)
                diff[r][c] += diff[r - 1][c];

        // Trim the matrix to n x n
        vector<vector<int>> res(n, vector<int>(n));
        for (int r = 0; r < n; ++r)
            for (int c = 0; c < n; ++c)
                res[r][c] = diff[r][c];

        return res;
    }
};
```

---

### 7. Testing & Validation

| Test | Expected |
|------|----------|
| `n = 1`, one query `[0,0,0,0]` | `[[1]]` |
| `n = 3`, queries covering disjoint sub‑matrices | Sum of individual rectangles |
| `n = 500`, each query covers entire matrix | Matrix filled with `q` (e.g., 10 000) |
| Queries touching the border | No overflow; result is correct |

Run the provided implementations against these cases – they all pass instantly on LeetCode’s test harness.

---

### 8. What Interviewers Are Looking For

1. **Understanding of 2‑D prefix sums** – shows you can generalise the 1‑D range update trick.  
2. **Edge‑handling** – correctly dealing with `+1` bounds is a common source of bugs.  
3. **Complexity analysis** – being able to explain `O(n² + q)` is crucial.  
4. **Clean code** – readable comments and proper variable names help the evaluator read your solution quickly.

If you’re aiming for a software‑engineering interview, explain the *difference array* concept and maybe even sketch the algorithm on a whiteboard. That demonstrates clear thinking and solid algorithmic fundamentals.

---

### 9. FAQ

| Question | Answer |
|----------|--------|
| **Can I use a 1‑D diff array only?** | No – a single 1‑D array cannot capture two independent dimensions of a rectangle. |
| **What if the value to add isn’t 1?** | Same idea, just add the value instead of `1`. |
| **Can I use `int64` to avoid overflow?** | For this problem the maximum value is `10⁴`, so `int` is fine. For generalised problems you may switch to `long`. |
| **Is there a better than O(n²) algorithm?** | Not for dense outputs. If you could skip outputting the matrix, a sweep‑line or sparse diff could help. |

---

### 10. Final Take‑Away

> *Increment Submatrices by One* is a textbook example of how a simple 2‑D difference array turns an otherwise impossible runtime into an elegant `O(n² + q)` solution.  
> Master this pattern, and you’ll be prepared for many “range‑update” interview questions that pop up in system‑design and data‑structure rounds.

Good luck, and may your interviewers love your clean Java/Python/C++ code!