---
title: LeetCode 3165. Maximum Sum of Subsequence With Non-adjacent Elements - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **The problem**  
After each update `arr[p] = v` we have to report the maximum sum of a
sub‑array in which no two chosen elements are adjacent.  
A linear scan for every query (`O(n)` per query) is far too slow,
therefore a data structure that can be updated in `O(log n)` and from
which the answer can be read in `O(1)` is required.

---

### Why the first three solutions TLE

| solution | complexity | reason for TLE |
|----------|------------|----------------|
| 1 | `O(n²)` | For every query we recompute the DP from scratch. |
| 2 | `O(n)` (wrong) | A Fenwick tree gives prefix sums – it cannot keep the
  “take / skip” information that is needed for the maximum
  non‑adjacent sum. |
| 3 | `O(n)` (wrong) | The recursion rebuilds the whole tree for every query,
  again `O(n)` per update. |

So none of the first three solves the problem in the required
`O((n+q) log n)` time.

---

### How to solve it with a segment tree

For each segment `[l…r]` we keep four values that describe the best
possible sums depending on whether the first element of the segment is
taken or not, and whether the last element of the segment is taken or
not.

```
              0  1   (state of the left boundary)
      0   f00  f01   (best sum in [l…r] when left boundary not taken)
      1   f10  f11   (best sum when left boundary is taken)
```

`f01` and `f10` are equal (symmetry) – the matrix is always symmetric,
so we really store only 4 numbers.

*Leaf*  
For a single element `a`:

```
f00 = 0                 (take nothing)
f01 = f10 = a           (take the element)
f11 = a
```

*Combine two children*

Let `L` be the matrix of the left child and `R` that of the right child.
The new matrix `M` is obtained as follows (only two combinations are
possible – we cannot take an element in the left child if we also take
the first element of the right child):

```
M00 = max( L00 + R00 , L00 + R01 , L01 + R00 )          // take none at the boundary
M01 = max( L00 + R01 , L00 + R11 , L01 + R01 )          // left not taken, right taken
M10 = max( L10 + R00 , L10 + R01 , L11 + R00 )          // left taken, right not taken
M11 = max( L10 + R01 , L10 + R11 , L11 + R01 )          // take both boundaries
```

All four numbers are computed with `max`, thus the tree still works
with `long` (the sum can be as large as `10¹⁴`).

*Update*  
To change an element we go down the tree to the leaf
(`O(log n)`) and then re‑pull the matrices on the path back to the
root – also `O(log n)`.

*Answer*  
After an update the answer is simply the best sum in the whole array,
i.e. the maximum of the two matrices that involve taking the last
element:

```
answer = max( seg[0][0][1] , seg[0][1][1] )
```

The root matrix gives the answer instantly, so the overall complexity
is `O((n + q) log n)`.

---

### Correct Java implementation

```java
class Solution {

    private static final int[][] FULL = new int[][]{
        {0, 1},
        {1, 1}
    };

    private long[][][] seg;   // 2×2 matrix for every node
    private int n;

    /* build the tree --------------------------------------------------- */
    private void build(int idx, int l, int r, int[] arr) {
        if (l == r) {                     // leaf
            seg[idx][0][0] = 0;
            seg[idx][0][1] = seg[idx][1][0] = seg[idx][1][1] = arr[l];
            return;
        }
        int mid = (l + r) >> 1;
        build(idx << 1 | 1, l, mid, arr);
        build(idx << 1 | 2, mid + 1, r, arr);
        pull(idx);
    }

    /* recompute a node from its two children ---------------------------- */
    private void pull(int idx) {
        int left = idx << 1 | 1, right = idx << 1 | 2;
        for (int i = 0; i < 2; ++i) {
            for (int j = 0; j < 2; ++j) {
                long best = Long.MIN_VALUE;
                // left not taken (i==0) or taken (i==1)  +  right not taken (0) or taken (1)
                // we cannot take both adjacent ends – skip that case
                best = Math.max(best, seg[left][i][0] + seg[right][0][j]); // skip right end
                best = Math.max(best, seg[left][i][1] + seg[right][1][j]); // take right end
                seg[idx][i][j] = best;
            }
        }
    }

    /* point update ----------------------------------------------------- */
    private void update(int idx, int l, int r, int pos, int val) {
        if (l == r) {                     // leaf
            seg[idx][0][0] = 0;
            seg[idx][0][1] = seg[idx][1][0] = seg[idx][1][1] = val;
            return;
        }
        int mid = (l + r) >> 1;
        if (pos <= mid) update(idx << 1 | 1, l, mid, pos, val);
        else            update(idx << 1 | 2, mid + 1, r, pos, val);
        pull(idx);
    }

    /* main routine ----------------------------------------------------- */
    public int[] maxNonAdjacent(int[] arr, int[][] queries) {
        n = arr.length;
        seg = new long[4 * n][2][2];
        build(0, 0, n - 1, arr);

        int[] result = new int[queries.length];
        for (int k = 0; k < queries.length; ++k) {
            int p = queries[k][0];
            int v = queries[k][1];
            update(0, 0, n - 1, p, v);

            // answer is the best sum that ends in the last element
            result[k] = (int) Math.max(seg[0][0][1], seg[0][1][1]);
        }
        return result;
    }
}
```

---

### Why a Fenwick tree (BIT) does **not** work

A Fenwick tree can only maintain an *additive* function on prefixes
(e.g. the sum of a range).  
The maximum non‑adjacent sum is **not** additive: the DP value at
position `i` depends on whether `i‑1` was taken or not, which in turn
depends on the value of `i‑2`.  
These dependencies cannot be represented by a simple prefix sum, so a
Fenwick tree can never produce the correct answer.

---

### Bottom line

* The first three submissions are either too slow or use the wrong
  data structure.
* The correct solution uses a segment tree that keeps the four DP
  values described above and merges them in `O(1)` time per node.
* After each update we read the answer from the root in `O(1)`, and
  all updates take `O(log n)` time, giving an overall
  `O((n+q) log n)` algorithm that passes the limits.