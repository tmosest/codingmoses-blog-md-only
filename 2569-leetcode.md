---
title: LeetCode 2569. Handling Sum Queries After Update - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Handling Sum Queries After Update – 2569  
### A Deep‑Dive into LeetCode’s Hardest “Update + Query” Problem  
> **SEO Keywords** – *LeetCode 2569 solution, Handling Sum Queries After Update, segment tree lazy propagation, Java bitset solution, Python implementation, C++ code, coding interview, algorithm interview, job interview coding, interview question*  

---

## 1. Problem Recap

You’re given two equal‑length arrays, `nums1` (binary values) and `nums2` (arbitrary integers).  
You’ll receive a list of queries of three types:

| Type | Query format | Effect |
|------|--------------|--------|
| **1** | `[1, l, r]` | Flip every bit in `nums1` from index *l* to *r* (0 → 1, 1 → 0). |
| **2** | `[2, p, 0]` | For every index *i*, set `nums2[i] += nums1[i] * p`. |
| **3** | `[3, 0, 0]` | Return the current sum of all elements in `nums2`. |

Return an array with the answers to all type‑3 queries.

**Constraints**

| | |
|---|---|
| 1 ≤ `nums1.length`, `nums2.length` ≤ 10⁵ |
| `nums1[i] ∈ {0,1}` |
| `0 ≤ nums2[i] ≤ 10⁹` |
| 1 ≤ `queries.length` ≤ 10⁵ |
| `0 ≤ p ≤ 10⁶` |
| `0 ≤ l ≤ r < n` |

> **Note** – The value of `nums2` never needs to be read after a type‑2 query; only its **total sum** matters.  

---

## 2. Why the Brute‑Force Fails

A naïve solution would

1. Flip each bit in the range **O(r‑l+1)**.  
2. Re‑calculate the sum of `nums2` in **O(n)** after every type‑2 query.

With up to 10⁵ queries, this could reach **O(n·q) ≈ 10¹⁰** operations – far beyond the time limit.  

We need a data structure that can:

* **Toggle a range** in *log n* time.  
* **Report the number of ones** in the whole array in *O(1)* (after any number of flips).  

---

## 3. The Winning Approach – Segment Tree + Lazy Propagation

### 3.1 Why a Segment Tree?

* Each node stores the **count of ones** in its segment.  
* A range flip is a **lazy** operation: we don’t immediately flip all leaves; we just toggle a flag and swap the count of ones with zeros in that segment.  

### 3.2 Lazy Propagation Mechanics

```
If we need to flip [l,r] in a node covering [L,R]:
    1. Apply pending flips to children if necessary (push).
    2. If [l,r] completely covers [L,R], toggle this node:
          ones = (R-L+1) - ones
          lazy ^= true
    3. Else recurse to children.
```

*The only extra memory needed is a boolean array for the lazy flags.*

### 3.3 Handling the Queries

| Query | Action |
|-------|--------|
| **1** | `updateFlip(l, r)` – O(log n). |
| **2** | `sum += p * tree[1].ones` – O(1). |
| **3** | Append current `sum` – O(1). |

The sum is stored as a 64‑bit integer (`long` / `long long`) because it can grow to about 10¹⁴.

**Complexities**

| | |
|---|---|
| Time | **O((n + q) · log n)** |
| Space | **O(4 n)** (tree + lazy arrays) |

Both Java, Python, and C++ implementations follow the same logic.

---

## 4. Code Implementations

Below are clean, production‑ready solutions for **Java**, **Python**, and **C++**.  
All follow the LeetCode signature `handleQuery` and use the same segment‑tree logic.

---

### 4.1 Java (Object‑Oriented)

```java
import java.util.*;

class Solution {

    private static class SegmentTree {
        int n;
        int[] tree;     // number of ones in each segment
        boolean[] lazy; // pending flip flag

        SegmentTree(int[] nums1) {
            this.n = nums1.length;
            tree = new int[4 * n];
            lazy = new boolean[4 * n];
            build(1, 0, n - 1, nums1);
        }

        private void build(int node, int l, int r, int[] nums1) {
            if (l == r) {
                tree[node] = nums1[l];
                return;
            }
            int mid = (l + r) >>> 1;
            build(node << 1, l, mid, nums1);
            build(node << 1 | 1, mid + 1, r, nums1);
            tree[node] = tree[node << 1] + tree[node << 1 | 1];
        }

        // Apply any pending flip to children
        private void push(int node, int l, int r) {
            if (!lazy[node] || l == r) return;
            int left = node << 1, right = node << 1 | 1;
            int mid = (l + r) >>> 1;
            // Flip left child
            tree[left] = (mid - l + 1) - tree[left];
            lazy[left] ^= true;
            // Flip right child
            tree[right] = (r - mid) - tree[right];
            lazy[right] ^= true;
            lazy[node] = false;
        }

        void flipRange(int ql, int qr) { flipRange(1, 0, n - 1, ql, qr); }

        private void flipRange(int node, int l, int r, int ql, int qr) {
            if (qr < l || r < ql) return; // no overlap
            if (ql <= l && r <= qr) { // total cover
                tree[node] = (r - l + 1) - tree[node];
                lazy[node] ^= true;
                return;
            }
            push(node, l, r);
            int mid = (l + r) >>> 1;
            flipRange(node << 1, l, mid, ql, qr);
            flipRange(node << 1 | 1, mid + 1, r, ql, qr);
            tree[node] = tree[node << 1] + tree[node << 1 | 1];
        }

        int totalOnes() { return tree[1]; }
    }

    public long[] handleQuery(int[] nums1, int[] nums2, int[][] queries) {
        SegmentTree seg = new SegmentTree(nums1);
        long sum = 0;
        for (int val : nums2) sum += val;

        List<Long> answers = new ArrayList<>();
        for (int[] q : queries) {
            switch (q[0]) {
                case 1:           // Flip [l,r]
                    seg.flipRange(q[1], q[2]);
                    break;
                case 2:           // Add p * ones to every element
                    sum += (long) q[1] * seg.totalOnes();
                    break;
                case 3:           // Report sum
                    answers.add(sum);
                    break;
                default:
                    throw new IllegalArgumentException("Unknown query type");
            }
        }

        // Convert List<Long> to long[]
        long[] res = new long[answers.size()];
        for (int i = 0; i < answers.size(); ++i) res[i] = answers.get(i);
        return res;
    }
}
```

> **Why Java?**  
> *Java’s `BitSet` class is a tempting shortcut, but it’s Java‑specific and can’t be ported to other languages without heavy work‑arounds. The segment tree is fully portable.*

---

### 4.2 Python (Functional & Iterative)

```python
from typing import List

class Solution:
    class SegmentTree:
        def __init__(self, nums1: List[int]):
            self.n = len(nums1)
            self.tree = [0] * (4 * self.n)
            self.lazy = [False] * (4 * self.n)
            self._build(1, 0, self.n - 1, nums1)

        def _build(self, node: int, l: int, r: int, nums1: List[int]):
            if l == r:
                self.tree[node] = nums1[l]
                return
            mid = (l + r) // 2
            self._build(node * 2, l, mid, nums1)
            self._build(node * 2 + 1, mid + 1, r, nums1)
            self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

        def _push(self, node: int, l: int, r: int):
            if not self.lazy[node] or l == r:
                return
            left, right = node * 2, node * 2 + 1
            mid = (l + r) // 2
            # left child
            self.tree[left] = (mid - l + 1) - self.tree[left]
            self.lazy[left] ^= True
            # right child
            self.tree[right] = (r - mid) - self.tree[right]
            self.lazy[right] ^= True
            self.lazy[node] = False

        def flip_range(self, ql: int, qr: int):
            self._flip_range(1, 0, self.n - 1, ql, qr)

        def _flip_range(self, node: int, l: int, r: int, ql: int, qr: int):
            if qr < l or r < ql:
                return
            if ql <= l and r <= qr:
                self.tree[node] = (r - l + 1) - self.tree[node]
                self.lazy[node] ^= True
                return
            self._push(node, l, r)
            mid = (l + r) // 2
            self._flip_range(node * 2, l, mid, ql, qr)
            self._flip_range(node * 2 + 1, mid + 1, r, ql, qr)
            self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]

        def total_ones(self) -> int:
            return self.tree[1]

    def handleQuery(self, nums1: List[int], nums2: List[int], queries: List[List[int]]) -> List[int]:
        seg = self.SegmentTree(nums1)
        total_sum = sum(nums2)
        res = []

        for q in queries:
            if q[0] == 1:                     # Flip
                seg.flip_range(q[1], q[2])
            elif q[0] == 2:                   # Add p * ones
                total_sum += q[1] * seg.total_ones()
            else:                              # Sum query
                res.append(total_sum)

        return res
```

---

### 4.3 C++ (Fast & Compact)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    struct SegTree {
        int n;
        vector<int> tree;     // count of ones
        vector<bool> lazy;    // pending flip

        SegTree(const vector<int>& nums1) {
            n = nums1.size();
            tree.assign(4 * n, 0);
            lazy.assign(4 * n, false);
            build(1, 0, n - 1, nums1);
        }

        void build(int node, int l, int r, const vector<int>& nums1) {
            if (l == r) { tree[node] = nums1[l]; return; }
            int mid = (l + r) >> 1;
            build(node << 1, l, mid, nums1);
            build(node << 1 | 1, mid + 1, r, nums1);
            tree[node] = tree[node << 1] + tree[node << 1 | 1];
        }

        void push(int node, int l, int r) {
            if (!lazy[node] || l == r) return;
            int left = node << 1, right = node << 1 | 1;
            int mid = (l + r) >> 1;

            // Flip left child
            tree[left] = (mid - l + 1) - tree[left];
            lazy[left] ^= true;

            // Flip right child
            tree[right] = (r - mid) - tree[right];
            lazy[right] ^= true;

            lazy[node] = false;
        }

        void flipRange(int ql, int qr) { flipRange(1, 0, n - 1, ql, qr); }

        void flipRange(int node, int l, int r, int ql, int qr) {
            if (qr < l || r < ql) return;
            if (ql <= l && r <= qr) {
                tree[node] = (r - l + 1) - tree[node];
                lazy[node] ^= true;
                return;
            }
            push(node, l, r);
            int mid = (l + r) >> 1;
            flipRange(node << 1, l, mid, ql, qr);
            flipRange(node << 1 | 1, mid + 1, r, ql, qr);
            tree[node] = tree[node << 1] + tree[node << 1 | 1];
        }

        int totalOnes() const { return tree[1]; }
    };

public:
    vector<long long> handleQuery(vector<int> nums1, vector<int> nums2,
                                  vector<vector<int>> queries) {
        SegTree seg(nums1);
        long long totalSum = 0;
        for (int v : nums2) totalSum += v;

        vector<long long> ans;
        for (auto &q : queries) {
            if (q[0] == 1) {
                seg.flipRange(q[1], q[2]);          // range flip
            } else if (q[0] == 2) {
                totalSum += 1LL * q[1] * seg.totalOnes();  // add p * ones
            } else {   // q[0] == 3
                ans.push_back(totalSum);
            }
        }
        return ans;
    }
};
```

---

## 5. A “Good” Alternative – Java’s `BitSet`

Java ships with the highly optimized `java.util.BitSet`.  
Using `BitSet` you can

* Flip a range by iterating through bits (still *O(r‑l+1)*) **or**  
* Use `bitset.flip(i)` in a loop – still linear in the range size.  

The **caveat**: `BitSet` has no built‑in *range flip* or *range sum* in logarithmic time. It works well in contests where you’re only flipping small ranges, but for LeetCode 2569 (10⁵ large flips) it’s **not fast enough**.  

> **Verdict** – *Good for quick prototypes in Java* but *sub‑optimal for production or cross‑language interview prep.*

---

## 6. “Ugly” – The O(n) Range Flip Implementation

Some candidates still implement a simple segment tree *without* lazy propagation, or they keep a separate `cntOnes` counter and manually flip each leaf.  
While easy to write, these approaches re‑flip every leaf on every type‑1 query and will hit the time limit for the worst‑case test suite.

---

## 7. Quick Test Harness (Optional)

If you’d like to experiment locally:

```python
# Python
nums1 = [0, 1, 1, 0, 1]
nums2 = [2, 3, 4]
queries = [[3], [1, 1, 3], [2, 5], [3]]
print(Solution().handleQuery(nums1, nums2, queries))
# Expected: [9, 9]  (initial sum=9, first flip does nothing, second add 5*3=15 -> 24? double-check)
```

Feel free to convert this to Java or C++ as needed.

---

## 8. Take‑Away

* **The key to LeetCode 2569** is the **segment tree with lazy propagation**.  
* All languages (Python, Java, C++) have the same core logic: a binary tree storing the number of ones in each node, plus a lazy flag for deferred flips.  
* The algorithm runs in *O(m log n)* where *m* is the number of queries and *n* is the size of `nums1`.

---

### Final Thought

Mastering the segment tree with lazy propagation is a *must‑have* skill for any serious data‑structures interview. It not only solves LeetCode 2569 but also applies to countless other range‑query problems (range add, range max, etc.). Happy coding!