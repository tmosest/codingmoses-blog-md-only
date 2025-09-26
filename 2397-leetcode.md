---
title: LeetCode 2397. Maximum Rows Covered by Columns - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2397. Maximum Rows Covered by Columns – 3‑Way Code, Blog, and Job‑Ready SEO

> **TL;DR**  
> *Problem:* Pick exactly `numSelect` columns from an `m × n` binary matrix to cover the maximum number of rows (a row is *covered* if every `1` in that row lies in a chosen column).  
> *Key insight:* The constraints are tiny (`n ≤ 12`), so a **bitmask + backtracking** solution is both simple and fast.  
> *Complexity:* `O( (n choose numSelect) · m · n )` time, `O(1)` extra space.  

Below you’ll find:

1. A **SEO‑optimized blog** explaining the problem, pitfalls, and the algorithm in plain English.  
2. Three working implementations – **Java**, **Python**, and **C++** – that you can copy‑paste into LeetCode.  
3. A quick “what to talk about in an interview” section.  

---

## The Problem (LeetCode 2397)

```text
Input: matrix: List[List[int]], numSelect: int
Output: int   // max number of rows that can be covered
```

- `matrix` is `m × n`, `0 ≤ m,n ≤ 12`, entries are `0` or `1`.  
- You must pick **exactly** `numSelect` distinct columns.  
- A row is *covered* if **every** `1` in that row lies in a chosen column (or the row has no `1`s).  

> Example  
> matrix = [[0,0,0],[1,0,1],[0,1,1],[0,0,1]], numSelect = 2 → **3**

---

## Why Brute Force Works (but is still a Bad Idea)

With `n ≤ 12`, the total number of column subsets is `2^n ≤ 4096`.  
You could enumerate all subsets and pick those of size `numSelect`.  
That is fine **time‑wise**, but the code gets noisy, and it’s hard to explain in an interview.

The **backtracking** approach, however, naturally mirrors the decision tree:  
*pick the current column?* – *or skip it?* – and prune when you’ve already chosen `numSelect` columns.  
This yields clean, recursive code that’s easy to read, test, and extend.

---

## Core Idea – Bitmask + Backtracking

1. **Represent a set of selected columns as a bitmask** (an `int` where bit `j` = 1 ⇔ column `j` selected).  
2. **Recursive DFS**:
   * `idx` – current column index (0 … n-1).  
   * `cnt` – number of columns already chosen.  
   * `mask` – mask of chosen columns.  
3. **Base case**: when `cnt == numSelect` → count covered rows.  
4. **Branch**:
   * *Pick* column `idx` → set its bit, recurse with `cnt+1`.  
   * *Skip* column `idx` → leave bit 0, recurse with same `cnt`.  
5. **Pruning**: if remaining columns are fewer than the columns still needed, stop early.

Counting covered rows for a given `mask` is `O(m · n)` – small enough for our constraints.

---

## Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Enumerating all subsets of size `k` | `O((n choose k) · m · n)` | `O(1)` |
| Counting rows for one mask | `O(m · n)` | `O(1)` |
| Recursion depth | `O(n)` | `O(1)` (besides call stack) |

Because `(12 choose 6) = 924` at worst, the algorithm runs in far less than a millisecond on LeetCode.

---

## 1. Java (LeetCode Style)

```java
class Solution {
    private int m, n, target;
    private int[][] mat;

    public int maximumRows(int[][] matrix, int numSelect) {
        this.mat = matrix;
        this.m = matrix.length;
        this.n = matrix[0].length;
        this.target = numSelect;
        return dfs(0, 0, 0);
    }

    private int dfs(int idx, int cnt, int mask) {
        if (cnt == target)          // we have picked enough columns
            return countCovered(mask);

        if (idx == n)               // no more columns to consider
            return 0;

        int remaining = n - idx;
        if (remaining + cnt < target)   // impossible to reach target
            return 0;

        // Option 1: pick this column
        int pick = dfs(idx + 1, cnt + 1, mask | (1 << idx));

        // Option 2: skip this column
        int skip = dfs(idx + 1, cnt, mask);

        return Math.max(pick, skip);
    }

    private int countCovered(int mask) {
        int covered = 0;
        for (int i = 0; i < m; i++) {
            boolean ok = true;
            for (int j = 0; j < n; j++) {
                if (mat[i][j] == 1 && ((mask & (1 << j)) == 0)) {
                    ok = false;
                    break;
                }
            }
            if (ok) covered++;
        }
        return covered;
    }
}
```

---

## 2. Python (3.8+)

```python
class Solution:
    def maximumRows(self, mat: List[List[int]], numSelect: int) -> int:
        self.mat = mat
        self.m = len(mat)
        self.n = len(mat[0])
        self.target = numSelect
        return self._dfs(0, 0, 0)

    def _dfs(self, idx: int, cnt: int, mask: int) -> int:
        if cnt == self.target:
            return self._count(mask)

        if idx == self.n:
            return 0

        remaining = self.n - idx
        if remaining + cnt < self.target:
            return 0

        # Pick
        pick = self._dfs(idx + 1, cnt + 1, mask | (1 << idx))
        # Skip
        skip = self._dfs(idx + 1, cnt, mask)
        return max(pick, skip)

    def _count(self, mask: int) -> int:
        covered = 0
        for row in self.mat:
            ok = True
            for j, val in enumerate(row):
                if val and not (mask & (1 << j)):
                    ok = False
                    break
            if ok:
                covered += 1
        return covered
```

---

## 3. C++ (GNU C++17)

```cpp
class Solution {
public:
    int maximumRows(vector<vector<int>>& matrix, int numSelect) {
        m = matrix.size();
        n = matrix[0].size();
        mat = matrix;
        target = numSelect;
        return dfs(0, 0, 0);
    }

private:
    vector<vector<int>> mat;
    int m, n, target;

    int dfs(int idx, int cnt, int mask) {
        if (cnt == target)          // already selected enough columns
            return countCovered(mask);
        if (idx == n) return 0;     // ran out of columns

        int remaining = n - idx;
        if (remaining + cnt < target)   // cannot reach target
            return 0;

        // Pick current column
        int pick = dfs(idx + 1, cnt + 1, mask | (1 << idx));

        // Skip current column
        int skip = dfs(idx + 1, cnt, mask);

        return max(pick, skip);
    }

    int countCovered(int mask) {
        int covered = 0;
        for (int i = 0; i < m; ++i) {
            bool ok = true;
            for (int j = 0; j < n; ++j) {
                if (mat[i][j] == 1 && !(mask & (1 << j))) {
                    ok = false;
                    break;
                }
            }
            if (ok) covered++;
        }
        return covered;
    }
};
```

---

## “What to Talk About in an Interview”

1. **State the constraints** (`n ≤ 12`) → allows exponential algorithms.  
2. **Explain the bitmask representation** – this instantly shows you can encode column sets in `O(1)` space.  
3. **Show the DFS recursion tree** – “pick or skip” – and why pruning is safe.  
4. **Complexity** – highlight that the algorithm is *optimal for the given constraints*.  
5. **Mention alternatives** – a simple loop over combinations or a `next_permutation` solution – just to show you know the trade‑offs.  
6. **Why it’s a good interview problem** – tests recursion, bit manipulation, and careful counting.

---

## SEO Keywords (for job‑search pages)

- LeetCode 2397  
- Maximum Rows Covered by Columns  
- Backtracking algorithm  
- Bitmask solution  
- Java 17 LeetCode solution  
- Python 3 LeetCode solution  
- C++ LeetCode solution  
- Software engineer interview questions  
- Algorithm interview preparation  
- Data structure interview  

Include these tags on your LinkedIn post or personal blog to attract recruiters looking for candidates who can solve LeetCode problems efficiently.

---

## Final Words

- The **backtracking + bitmask** approach gives you clean, maintainable code that is easy to explain.  
- The time complexity is well within the problem limits – you’ll finish in milliseconds.  
- Highlight the algorithm in your next interview: “We treat columns as bits, walk the decision tree, and count covered rows only once per leaf.”

Good luck with the interview, and happy coding!