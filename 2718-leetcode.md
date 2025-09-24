---
title: LeetCode 2718. Sum of Matrix After Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2718 – Sum of Matrix After Queries  
**TL;DR** – The trick is to walk through the queries *backwards*, keep a “seen” flag for each row and column, and add the contribution of every *unique* row/column only once.  
Complexity: **O(queries + n)** time, **O(n)** space.  
Below you’ll find ready‑to‑copy solutions in **Java, Python, and C++** (all annotated), followed by a short SEO‑friendly blog post that explains the idea, why it works, and how you can use it to impress interviewers.

---

## 1. The Code

### 1.1 Java

```java
import java.util.*;

public class Solution {
    /**
     * Returns the sum of all cells in an n x n matrix after applying
     * a list of row/column overwrite queries.
     *
     * @param n      matrix size (n x n)
     * @param queries list of queries, each query = [type, index, val]
     * @return total sum as long
     */
    public long matrixSumQueries(int n, int[][] queries) {
        boolean[] rowSeen = new boolean[n];
        boolean[] colSeen = new boolean[n];
        int rowCount = 0, colCount = 0;
        long sum = 0;

        // Process from last query to first – “last wins”
        for (int i = queries.length - 1; i >= 0; i--) {
            int type = queries[i][0];
            int idx  = queries[i][1];
            int val  = queries[i][2];

            if (type == 0) { // row overwrite
                if (!rowSeen[idx]) {
                    sum += (long) (n - colCount) * val; // only fresh columns
                    rowSeen[idx] = true;
                    rowCount++;
                }
            } else { // column overwrite
                if (!colSeen[idx]) {
                    sum += (long) (n - rowCount) * val; // only fresh rows
                    colSeen[idx] = true;
                    colCount++;
                }
            }
        }
        return sum;
    }
}
```

### 1.2 Python

```python
def matrixSumQueries(n: int, queries: list[list[int]]) -> int:
    """
    Return the sum of an n x n matrix after processing all queries.
    queries: [[type, index, val], ...]
    """
    row_seen = [False] * n
    col_seen = [False] * n
    row_cnt = col_cnt = 0
    total = 0

    for typ, idx, val in reversed(queries):
        if typ == 0 and not row_seen[idx]:
            total += (n - col_cnt) * val
            row_seen[idx] = True
            row_cnt += 1
        elif typ == 1 and not col_seen[idx]:
            total += (n - row_cnt) * val
            col_seen[idx] = True
            col_cnt += 1

    return total
```

### 1.3 C++

```cpp
class Solution {
public:
    long long matrixSumQueries(int n, vector<vector<int>>& queries) {
        vector<bool> rowSeen(n,false), colSeen(n,false);
        int rowCnt = 0, colCnt = 0;
        long long sum = 0;

        // Traverse from end – last operation “wins”
        for (int i = (int)queries.size() - 1; i >= 0; --i) {
            int type = queries[i][0];
            int idx  = queries[i][1];
            int val  = queries[i][2];

            if (type == 0 && !rowSeen[idx]) {
                sum += 1LL * (n - colCnt) * val;
                rowSeen[idx] = true;
                ++rowCnt;
            } else if (type == 1 && !colSeen[idx]) {
                sum += 1LL * (n - rowCnt) * val;
                colSeen[idx] = true;
                ++colCnt;
            }
        }
        return sum;
    }
};
```

> **All three implementations run in O(queries + n) time and O(n) memory** – easily fast enough for the limits `n ≤ 10⁴` and `queries ≤ 5·10⁴`.

---

## 2. Blog Article – “The Good, the Bad, and the Ugly of Sum of Matrix After Queries”

### 2.1 Introduction  

LeetCode 2718 – *Sum of Matrix After Queries* – is a classic interview problem that tests your ability to think about “overwrites” in a 2‑D space.  
The task is simple to state: you start with an all‑zero `n × n` matrix, then a series of **row** or **column** assignments are applied. After all queries, compute the total sum of the matrix.

Why does this matter?  
* In real‑world systems you often *overwrite* entire rows or columns (think spreadsheet updates, GPU shaders, or database bulk operations).  
* Interviewers love problems that require you to avoid an obvious `O(n²)` simulation, because they reveal your awareness of algorithmic optimization.

Below I’ll walk through the **good** (what’s right), the **bad** (common pitfalls), and the **ugly** (the messy, but possible, solutions).

---

### 2.2 The Good – A Clean O(queries + n) Solution  

**Key Insight:**  
The *last* query that touches a particular row or column is the only one that matters for the final matrix.  

Hence, we can process the queries **backwards**.  
While iterating from the end:

1. Keep two boolean arrays – `rowSeen[n]` and `colSeen[n]`.  
2. Maintain counts of how many distinct rows/columns have already been “fixed” (`rowCnt`, `colCnt`).  
3. When you encounter a row query `[0, r, val]` that hasn’t been seen:
   * Every column that **has not** yet been overwritten contributes `val` to the sum.
   * That contribution is `val * (n - colCnt)`.
4. Do the symmetric logic for a column query.

Because each row or column is counted **once** (the first time you see it in reverse order), the algorithm is linear.

**Why it’s “good”:**  
* Linear time, linear space – well within LeetCode limits.  
* Intuitive once you understand “last wins”.  
* Works for the maximum constraints (`n = 10⁴`, `queries = 5·10⁴`).  

---

### 2.3 The Bad – Naïve Approaches That Die

| Approach | Time | Space | Why it fails |
|----------|------|-------|--------------|
| **Full matrix simulation** | O(n² · q) | O(n²) | `n²` can be 10⁸ cells – impossible. |
| **Track per‑cell last writer** | O(n²) | O(n²) | Same issue – too many cells. |
| **Forward scan with set** | O(q · n) | O(n) | Still quadratic in worst case because you recompute each row/column many times. |
| **Ignoring duplicate queries** | O(q · n) | O(n) | Still too slow; you’re doing 5×10⁴ × 10⁴ ≈ 5×10⁸ ops – borderline or over. |

Interviewers often give the “full matrix” solution as an example of what *not* to do.  
Even a Python implementation that naively writes to the matrix cell‑by‑cell will TLE on the bigger tests.

---

### 2.4 The Ugly – Messy Variants That Work but Are Hard to Explain

1. **Using `unordered_map` to record last write time** –  
   *Store for every row/column the *time stamp* of its last assignment and then reconstruct the matrix.*  
   * Still requires `O(n²)` for the final sum unless you do a clever math trick.  

2. **Bitset compression + BFS** –  
   *Use a bitset to mark seen rows/columns and BFS to propagate values.*  
   * Works, but code becomes a labyrinth of bit manipulations that are hard to debug.

These solutions are technically correct but bring **verbosity** and **hidden bugs** (e.g., off‑by‑one errors in the multiplier `(n - colCnt)`).

> **Bottom line:** If you can finish the reverse‑iteration solution in < 2 minutes, you’ve out‑performed the “bad” ones by a wide margin.  

---

### 2.4 Why the Reverse Trick Works (Proof by Induction)

Let’s prove the backwards scan works:

- **Base case:** The very last query in the list touches some row `r`. In the forward simulation, after this assignment all cells `(r, c)` for all `c` become `val`. Since no later query overwrites `r`, the final value for row `r` is exactly `val`.  

- **Inductive step:** Assume that for every row/column processed so far (from the end) the sum contributed by *unique* rows/columns is correct.  
  When we next hit a new row `r`, every column `c` *not yet processed* (i.e., not seen in reverse) is still at its original “previous” value.  
  Thus, adding `val * (n - colCnt)` captures exactly the fresh cells that row `r` will own in the final matrix.  

By induction, each unique row/column contributes correctly, and duplicates are ignored.

---

### 2.5 Variations You Might Hear in an Interview

| Variant | Strategy |
|---------|----------|
| **Queries that add to a row/column instead of overwrite** | Keep *cumulative* sums for rows/columns, apply them in order, use prefix‑sums to compute final values. |
| **Sparse matrix (n is huge but few active rows/columns)** | Only keep dictionaries for rows/columns that actually appear; the complexity reduces to O(queries). |
| **Dynamic matrix size** | Use *hash maps* for seen flags; still O(q) time, O(#distinct rows/cols). |

If you can explain how the reverse‑iteration idea adapts to any of these, you’ll be a star candidate.

---

### 2.6 How to Use This Solution in an Interview

1. **Explain the “last query wins” property** – it demonstrates you’re not just writing code, you’re reasoning about the problem.  
2. **Show the O(n + q) complexity** – ask the interviewer if that is acceptable for the constraints.  
3. **Walk through the sample input** – step‑by‑step (e.g., `[0,1,5]`, `[1,3,2]`, …) – to show how the multiplier works.  
4. **Mention edge cases** – e.g., duplicate queries, all rows then all columns, single‑cell matrix.  
5. **Bonus:** Offer a Python or Java snippet on the spot.  

> Interviewers often look for *explainability* more than *speed*, so keep the explanation concise but complete.

---

### 2.7 Conclusion – A Takeaway for Your Next Job

LeetCode 2718 is a great “algorithm‑plus‑story” problem.  
- **Good** – The reverse‑iteration solution is a textbook O(queries + n) algorithm.  
- **Bad** – Don’t fall into the naive matrix simulation trap.  
- **Ugly** – There are many messy ways to hack the problem, but they’re rarely necessary.

When you land on this problem, aim for the clean, backward‑scan solution, mention its linear guarantees, and you’ll not only get the correct answer but also impress the hiring manager with your efficiency mindset.

> **SEO Keywords**  
> Sum of Matrix After Queries | LeetCode 2718 | Java solution | Python solution | C++ solution | reverse iteration | O(n) time complexity | technical interview | coding interview | algorithm design

Happy coding, and may your future interviews be *filled with clean, efficient solutions!* 🎯