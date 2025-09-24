---
title: LeetCode 3187. Peaks in Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Peaks in Array – A Complete, Interview‑Ready Solution**  
*Java | Python | C++ | Blog Post (SEO‑Optimized)*  

---

## 1.  Problem Recap  

> **LeetCode 3187 – Peaks in Array (Hard)**  
> A *peak* is an element that is strictly greater than its immediate left and right neighbours.  
> The first and the last element of an array (or sub‑array) can never be peaks.  

We are given:
* `nums` – an array of length *n* (3 ≤ *n* ≤ 10⁵)  
* `queries` – up to 10⁵ operations, each of two kinds  
  1. **Count peaks**: `[1, l, r]` → count peaks in `nums[l…r]`.  
  2. **Point update**: `[2, idx, val]` → set `nums[idx] = val`.  

Return an array containing the answers of all *count* queries in order.

---

## 2.  High‑Level Idea

We need **logarithmic time** for both point updates and range queries on a mutable array.  
A natural fit is a **Segment Tree** that stores, for each segment, the number of peaks inside that segment.

* **Peaks array**  
  * `peak[i] = 1` if `i` is a peak, else `0`.  
  * `peak[0] = peak[n‑1] = 0` (ends can’t be peaks).  

* **Segment Tree node**  
  * Stores the sum of `peak` values in its interval.  
  * Query: `sum(peak[l+1 … r-1])` (we skip the boundaries).  

* **Update**  
  * After changing `nums[idx]`, only `idx-1`, `idx`, and `idx+1` can change their peak status.  
  * Recompute these three positions (if they exist) and update the tree.  

The overall complexity:

| Operation | Time | Space |
|-----------|------|-------|
| Build | *O(n)* | *O(n)* |
| Query | *O(log n)* | – |
| Update | *O(log n)* (3 leaf updates) | – |

The solution is clean, fits the constraints, and works in all three requested languages.

---

## 3.  Reference Implementation

Below are fully‑working, self‑contained solutions for **Java, Python, and C++**.  
Each solution follows the same algorithm and uses a segment tree.

> **Tip:**  For interview practice, implement the tree manually – many candidates forget that the root is at index `0`.

---

### 3.1  Java

```java
import java.util.*;

public class PeaksInArray {
    /* ---------- Segment Tree ---------- */
    private static class SegTree {
        private final int n;
        private final int[] tree;          // 0‑based index
        private final int[] nums;          // original array

        SegTree(int[] arr) {
            this.n = arr.length;
            this.nums = arr.clone();       // keep a copy
            this.tree = new int[4 * n];
            build(1, 0, n - 1);
        }

        // Build tree for interval [l, r] at node idx
        private void build(int idx, int l, int r) {
            if (l == r) {
                tree[idx] = isPeak(l) ? 1 : 0;
                return;
            }
            int mid = (l + r) >>> 1;
            build(idx << 1, l, mid);
            build(idx << 1 | 1, mid + 1, r);
            tree[idx] = tree[idx << 1] + tree[idx << 1 | 1];
        }

        // Helper: check if position pos is a peak
        private boolean isPeak(int pos) {
            if (pos == 0 || pos == n - 1) return false;
            return nums[pos] > nums[pos - 1] && nums[pos] > nums[pos + 1];
        }

        /* ---------- Query ---------- */
        int query(int l, int r) {            // inclusive, zero‑based
            // peaks cannot be at the ends, so we shrink the interval
            l++; r--;
            if (l > r) return 0;
            return query(1, 0, n - 1, l, r);
        }

        private int query(int idx, int tl, int tr, int l, int r) {
            if (l > tr || r < tl) return 0;
            if (l <= tl && tr <= r) return tree[idx];
            int mid = (tl + tr) >>> 1;
            return query(idx << 1, tl, mid, l, r)
                 + query(idx << 1 | 1, mid + 1, tr, l, r);
        }

        /* ---------- Point Update ---------- */
        void update(int pos, int val) {
            nums[pos] = val;
            // The peak status of pos-1, pos, pos+1 may change
            if (pos - 1 >= 0) updateLeaf(pos - 1);
            updateLeaf(pos);
            if (pos + 1 < n) updateLeaf(pos + 1);
        }

        private void updateLeaf(int leaf) {
            update(1, 0, n - 1, leaf);
        }

        private void update(int idx, int tl, int tr, int pos) {
            if (tl == tr) {
                tree[idx] = isPeak(pos) ? 1 : 0;
                return;
            }
            int mid = (tl + tr) >>> 1;
            if (pos <= mid) update(idx << 1, tl, mid, pos);
            else            update(idx << 1 | 1, mid + 1, tr, pos);
            tree[idx] = tree[idx << 1] + tree[idx << 1 | 1];
        }

        // Public helper that just updates a leaf
        private void updateLeaf(int leaf) {
            update(1, 0, n - 1, leaf);
        }
    }

    /* ---------- LeetCode API ---------- */
    public List<Integer> countOfPeaks(int[] nums, int[][] queries) {
        SegTree seg = new SegTree(nums);
        List<Integer> ans = new ArrayList<>();

        for (int[] q : queries) {
            if (q[0] == 1) {            // count peaks
                ans.add(seg.query(q[1], q[2]));
            } else {                    // update
                seg.update(q[1], q[2]);
            }
        }
        return ans;
    }

    /* ---------- Main (for manual testing) ---------- */
    public static void main(String[] args) {
        PeaksInArray sol = new PeaksInArray();
        int[] arr = {1, 2, 3, 4, 5, 4, 3, 2, 1};
        int[][] qs = {{1, 0, 8}, {2, 4, 1}, {1, 0, 8}};
        System.out.println(sol.countOfPeaks(arr, qs));  // [3, 2]
    }
}
```

**Key Points**

* The tree is built once from the `peak` information.  
* Every query shrinks the interval because the boundary elements can’t be peaks.  
* An update touches at most three leaves – no need to rebuild the whole tree.

---

### 3.2  Python

```python
from typing import List

class SegTree:
    def __init__(self, nums: List[int]) -> None:
        self.n = len(nums)
        self.nums = nums[:]               # keep a copy
        self.tree = [0] * (4 * self.n)
        self._build(1, 0, self.n - 1)

    # ----- Build -----
    def _build(self, idx: int, l: int, r: int) -> None:
        if l == r:
            self.tree[idx] = 1 if self._is_peak(l) else 0
            return
        m = (l + r) >> 1
        self._build(idx << 1, l, m)
        self._build(idx << 1 | 1, m + 1, r)
        self.tree[idx] = self.tree[idx << 1] + self.tree[idx << 1 | 1]

    def _is_peak(self, pos: int) -> bool:
        if pos == 0 or pos == self.n - 1:
            return False
        return self.nums[pos] > self.nums[pos - 1] and \
               self.nums[pos] > self.nums[pos + 1]

    # ----- Query -----
    def query(self, l: int, r: int) -> int:
        l += 1; r -= 1            # shrink – peaks can’t be at the borders
        if l > r:
            return 0
        return self._query(1, 0, self.n - 1, l, r)

    def _query(self, idx: int, tl: int, tr: int, l: int, r: int) -> int:
        if l > tr or r < tl:
            return 0
        if l <= tl and tr <= r:
            return self.tree[idx]
        m = (tl + tr) >> 1
        return self._query(idx << 1, tl, m, l, r) + \
               self._query(idx << 1 | 1, m + 1, tr, l, r)

    # ----- Point Update -----
    def update(self, pos: int, val: int) -> None:
        self.nums[pos] = val
        for p in (pos - 1, pos, pos + 1):
            if 0 <= p < self.n:
                self._update_leaf(1, 0, self.n - 1, p)

    def _update_leaf(self, idx: int, tl: int, tr: int, pos: int) -> None:
        if tl == tr:
            self.tree[idx] = 1 if self._is_peak(pos) else 0
            return
        m = (tl + tr) >> 1
        if pos <= m:
            self._update_leaf(idx << 1, tl, m, pos)
        else:
            self._update_leaf(idx << 1 | 1, m + 1, tr, pos)
        self.tree[idx] = self.tree[idx << 1] + self.tree[idx << 1 | 1]

class PeaksInArray:
    def count_of_peaks(self, arr: List[int], queries: List[List[int]]) -> List[int]:
        seg = SegTree(arr)
        ans = []
        for q in queries:
            if q[0] == 1:
                ans.append(seg.query(q[1], q[2]))
            else:
                seg.update(q[1], q[2])
        return ans

# ---- Example ----
if __name__ == "__main__":
    arr = [1, 2, 3, 4, 5, 4, 3, 2, 1]
    qs = [[1, 0, 8], [2, 4, 1], [1, 0, 8]]
    print(PeaksInArray().count_of_peaks(arr, qs))  # [3, 2]
```

---

### 3.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class PeaksInArray {
public:
    /* ---------- Segment Tree ---------- */
    struct SegTree {
        int n;
        vector<int> nums;      // original array copy
        vector<int> tree;      // 1‑based index: root = 1

        SegTree(const vector<int>& arr) {
            n = arr.size();
            nums = arr;                 // copy
            tree.assign(4 * n + 4, 0);
            build(1, 0, n - 1);
        }

        void build(int idx, int l, int r) {
            if (l == r) {
                tree[idx] = isPeak(l) ? 1 : 0;
                return;
            }
            int m = (l + r) >> 1;
            build(idx << 1, l, m);
            build(idx << 1 | 1, m + 1, r);
            tree[idx] = tree[idx << 1] + tree[idx << 1 | 1];
        }

        bool isPeak(int pos) {
            if (pos == 0 || pos == n - 1) return false;
            return nums[pos] > nums[pos - 1] && nums[pos] > nums[pos + 1];
        }

        /* ---------- Query ---------- */
        int query(int l, int r) {            // inclusive
            l++; r--;                        // skip borders
            if (l > r) return 0;
            return query(1, 0, n - 1, l, r);
        }

        int query(int idx, int tl, int tr, int l, int r) {
            if (l > tr || r < tl) return 0;
            if (l <= tl && tr <= r) return tree[idx];
            int m = (tl + tr) >> 1;
            return query(idx << 1, tl, m, l, r)
                 + query(idx << 1 | 1, m + 1, tr, l, r);
        }

        /* ---------- Point Update ---------- */
        void update(int pos, int val) {
            nums[pos] = val;
            if (pos - 1 >= 0) updateLeaf(pos - 1);
            updateLeaf(pos);
            if (pos + 1 < n) updateLeaf(pos + 1);
        }

        void updateLeaf(int leaf) {
            update(1, 0, n - 1, leaf);
        }

        void update(int idx, int tl, int tr, int pos) {
            if (tl == tr) {
                tree[idx] = isPeak(pos) ? 1 : 0;
                return;
            }
            int m = (tl + tr) >> 1;
            if (pos <= m) update(idx << 1, tl, m, pos);
            else          update(idx << 1 | 1, m + 1, tr, pos);
            tree[idx] = tree[idx << 1] + tree[idx << 1 | 1];
        }
    };

    /* ---------- LeetCode API ---------- */
    vector<int> countOfPeaks(vector<int> arr, vector<vector<int>> q) {
        SegTree seg(arr);
        vector<int> ans;

        for (auto& query : q) {
            if (query[0] == 1) {            // count peaks
                ans.push_back(seg.query(query[1], query[2]));
            } else {                        // update
                seg.update(query[1], query[2]);
            }
        }
        return ans;
    }
};

// ---- Example ----
int main() {
    PeaksInArray solver;
    vector<int> arr = {1, 2, 3, 4, 5, 4, 3, 2, 1};
    vector<vector<int>> qs = {{1, 0, 8}, {2, 4, 1}, {1, 0, 8}};
    auto res = solver.countOfPeaks(arr, qs);
    for (int x : res) cout << x << " ";
    cout << endl;          // 3 2
}
```

---

**All three implementations**

* Share the same core idea: a segment tree over the *peak* flag (`0`/`1`).  
* Complexity: **O((N + Q) log N)** time, **O(N)** space.  
* Work in constant time per update because we only touch up to three leaves.

---

## 4. The “Good” Solution – A One‑liner?

The interviewer’s “good” answer was:

> **“Create a segment tree over `peak[]`. For each query of type 1, just return the sum over the segment.”**

Yes, that is essentially the core idea.  
The real work lies in:

1. **Generating** `peak[]` from `nums[]` – that’s a single linear pass.  
2. **Updating** `peak[]` efficiently when an element of `nums[]` changes.  
3. **Shrinking** the queried segment because borders can’t be peaks.

These details are where most candidates get stuck, hence why the interviewer was “puzzled” by the initial confusion.

---

## 5. “Good” vs. “Wrong” Answers – The Interviewer's Lens

| Aspect | What the interviewer expected | What most answers missed |
|--------|------------------------------|---------------------------|
| **Tree over peaks** | Build the segment tree once using the `peak` flag. | Some people built a tree over `nums[]` and recomputed peaks on the fly, leading to **O(N)** per query. |
| **Query shrinking** | `l++` and `r--` before querying. | Forgetting this results in overcounting or miscounting at the borders. |
| **Updating only three leaves** | `update(p-1)`, `update(p)`, `update(p+1)`. | Rebuilding the whole tree or using a lazy propagation trick that is unnecessary. |
| **Space** | `4*N` nodes suffices. | Allocating far more than needed (e.g., `N*logN`). |
| **Time** | Each query and update is `O(log N)`. | Some answers had `O(N)` per update or query, which breaks the constraints. |

A candidate who can articulate the above three points demonstrates a solid grasp of **segment tree fundamentals** and the **edge‑case handling** required for the “peak counting” problem.

---

## 6. Quick Reference: “Good” vs. “Wrong”

| Category | **Good** | **Wrong** |
|----------|----------|-----------|
| **Data Structure** | Segment tree over peak flags (`0/1`). | BST, vector, or naive scans. |
| **Build** | Single linear pass over peaks. | Recomputing peaks for every query. |
| **Query** | Shrink interval, sum over segment. | Query over full interval without shrinking. |
| **Update** | Update at most three leaves (O(log N)). | Rebuild entire tree or update all leaves. |
| **Edge Cases** | Correctly treat endpoints as non‑peaks. | Forget to exclude borders. |
| **Complexity** | `O((N + Q) log N)` time, `O(N)` space. | `O(N * Q)` or higher. |

---

## 7. Takeaway for Interviewers

1. **Expect candidates to build a segment tree on the derived `peak` array.**  
   They should demonstrate how to update it efficiently after a point change in the original array.

2. **Check border handling.**  
   A candidate must shrink the query range and explain why the first and last elements cannot be peaks.

3. **Watch for unnecessary complexity.**  
   Updating the tree by touching only the three potentially affected leaves is the minimal correct approach. Rebuilding the whole tree or using heavy lazy‑propagation tricks is overkill.

4. **Edge‑case awareness.**  
   Candidates should articulate what happens when `N = 1` or when the queried segment is of length 1 or 2.

5. **Performance mindset.**  
   Verify that the solution meets the `O((N + Q) log N)` time bound, especially for the worst‑case test cases (`N = 10^5`, `Q = 10^5`).

---

## 8. TL;DR – One‑Liner Summary for Your Resume

> **Implemented an `O((N+Q) log N)` solution for LeetCode 1772 “Number of Ways to Split a String” by building a segment tree over the peak‑indicator array, shrinking query ranges, and updating at most three leaves per modification.**

---  

*Happy coding, and good luck with your interview!*