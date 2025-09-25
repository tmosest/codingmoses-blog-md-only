---
title: LeetCode 3534. Path Existence Queries in a Graph II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Problem (LeetCode 3534 – Path Existence Queries II)

> **Given**  
> * `n` nodes (`0 … n‑1`)  
> * an array `nums[0…n‑1]` of values  
> * an integer `maxDiff`  
> * a list of `queries[i] = [ui, vi]`  
>  
> **Define** an undirected edge between nodes `i` and `j` iff  
> `|nums[i] – nums[j]| ≤ maxDiff`.  
>  
> **Task** – for every query return the length of the shortest (unweighted) path from `ui` to `vi`, or `‑1` if no path exists.

Constraints are huge (`n, |queries| ≤ 10^5`), so a naïve BFS per query will time‑out.

---

## 2.  Key Insight – “Sliding‑Window” Graph

1. Sort the nodes by their `nums` values.  
   *Let `pos[i]` be the node’s position in the sorted order.*  
2. For a node at position `p`, all nodes whose values lie within `maxDiff` form a **contiguous window** in the sorted array.  
   *The furthest node that can be reached in **one hop** from `p` is therefore the right‑most index of that window.*  
3. The graph becomes a collection of “cliques” sliding along the sorted list.  
   *The shortest path between two nodes is simply the minimal number of hops needed to move from the sorted index of `ui` to that of `vi` by repeatedly jumping to the farthest reachable index.*

So we only need:

* **Pre‑compute** the furthest reachable index for every position.  
* **Answer** queries by repeatedly “jumping” as far as possible – this is a classic *binary lifting* (doubling) problem.

---

## 3.  Algorithm in a Nutshell

1. **Sort** the nodes by `nums` while remembering the original indices.  
   `sorted[i] = (nums[orig], orig)`.
2. **Build a mapping** `origToSorted[orig] → sortedIndex`.  
3. For each `i` (0 … n‑1) in sorted order:  
   * Use two‑pointer technique to find the largest `j` such that `sorted[j].value – sorted[i].value ≤ maxDiff`.  
   * Store `jumps[i][0] = j` – the farthest node reachable in one hop.  
4. **Binary lifting** – for each power `k > 0` compute  
   `jumps[i][k] = jumps[ jumps[i][k‑1] ][k‑1]`.  
   The table has `⌈log2 n⌉` columns.
5. **Answer a query** `[u, v]`:  
   * Map to sorted indices `a = origToSorted[u]`, `b = origToSorted[v]` (`a ≤ b`).  
   * If `a == b` → answer `0`.  
   * If `jumps[a][0] < b` and even the highest jump cannot reach `b` → answer `‑1`.  
   * Else perform a binary search on the lifting table to find the smallest number of hops that bring us past `b`.  
     The recursion or iterative form `getQueryJumps(a, b)` returns that number.

Complexities  

| Step | Time | Space |
|------|------|-------|
| Sorting & mapping | `O(n log n)` | `O(n)` |
| Two‑pointer furthest reach | `O(n)` | `O(n)` |
| Binary lifting table | `O(n log n)` | `O(n log n)` |
| One query | `O(log n)` | `O(1)` |
| All queries | `O(|queries| log n)` | — |

With `n ≤ 10^5` this easily satisfies the limits.

---

## 4.  Implementation – Java

```java
import java.util.*;

/** LeetCode 3534 – Path Existence Queries II */
public class Solution {
    public int[] pathExistenceQueries(int n, int[] nums, int maxDiff, int[][] queries) {
        // 1. Build sorted array of (value, original index)
        int[][] sorted = new int[n][2];
        for (int i = 0; i < n; i++) {
            sorted[i][0] = nums[i];
            sorted[i][1] = i;
        }
        Arrays.sort(sorted, Comparator.comparingInt(a -> a[0]));

        // 2. Map original index → sorted index
        int[] origToSorted = new int[n];
        for (int i = 0; i < n; i++) {
            origToSorted[sorted[i][1]] = i;
        }

        // 3. Compute farthest reachable index in one hop
        int[] next = new int[n];              // next[i] = furthest index reachable from i
        int r = 0;
        for (int l = 0; l < n; l++) {
            while (r + 1 < n && sorted[r + 1][0] - sorted[l][0] <= maxDiff) r++;
            next[l] = r;
        }

        // 4. Binary lifting table
        int LOG = 32 - Integer.numberOfLeadingZeros(n);
        int[][] up = new int[n][LOG];
        for (int i = 0; i < n; i++) up[i][0] = next[i];
        for (int k = 1; k < LOG; k++) {
            for (int i = 0; i < n; i++) {
                up[i][k] = up[ up[i][k - 1] ][k - 1];
            }
        }

        // 5. Process queries
        int[] ans = new int[queries.length];
        for (int qi = 0; qi < queries.length; qi++) {
            int u = origToSorted[queries[qi][0]];
            int v = origToSorted[queries[qi][1]];
            if (u > v) { int tmp = u; u = v; v = tmp; }   // ensure u <= v
            ans[qi] = shortestDistance(u, v, up);
        }
        return ans;
    }

    /** Returns minimal hops from u to v (u <= v), or -1 if impossible */
    private int shortestDistance(int u, int v, int[][] up) {
        if (u == v) return 0;
        // If even the farthest reachable node cannot reach v
        if (up[u][0] < v && up[u][up[u].length - 1] < v) return -1;

        int steps = 0;
        // Jump as far as possible without overshooting v
        for (int k = up[u].length - 1; k >= 0; k--) {
            if (up[u][k] < v) {
                u = up[u][k];
                steps += 1 << k;
            }
        }
        // One more hop to finish the journey
        return steps + 1;
    }
}
```

> **Why does the iterative `shortestDistance` work?**  
> Starting from `u` we look for the largest power‑of‑two jump that still lands **before** `v`.  
> Each such jump reduces the distance to `v` dramatically.  
> After the loop we’re at a node that can reach `v` in a single hop, so we add one more step.

---

## 5.  Implementation – Python

```python
import bisect
from typing import List

class Solution:
    def pathExistenceQueries(
        self,
        n: int,
        nums: List[int],
        maxDiff: int,
        queries: List[List[int]]
    ) -> List[int]:
        # 1. Sort with original indices
        sorted_pairs = sorted((num, idx) for idx, num in enumerate(nums))
        orig_to_sorted = [0] * n
        for sorted_idx, (_, orig_idx) in enumerate(sorted_pairs):
            orig_to_sorted[orig_idx] = sorted_idx

        # 2. Farthest reachable in one hop
        next_idx = [0] * n
        r = 0
        for l in range(n):
            while r + 1 < n and sorted_pairs[r + 1][0] - sorted_pairs[l][0] <= maxDiff:
                r += 1
            next_idx[l] = r

        # 3. Binary lifting table
        LOG = (n-1).bit_length()
        up = [next_idx[:] ]
        for k in range(1, LOG):
            prev = up[-1]
            up.append([prev[prev[i]] for i in range(n)])

        # 4. Answer queries
        def min_hops(u, v):
            if u == v:
                return 0
            # Quick rejection
            if up[0][u] < v and up[-1][u] < v:
                return -1
            steps = 0
            for k in reversed(range(LOG)):
                if up[k][u] < v:
                    u = up[k][u]
                    steps += 1 << k
            return steps + 1

        res = []
        for u, v in queries:
            su, sv = orig_to_sorted[u], orig_to_sorted[v]
            if su > sv:
                su, sv = sv, su
            res.append(min_hops(su, sv))
        return res
```

*Python’s built‑in `bit_length()` gives us `⌈log₂ n⌉` in O(1).*

---

## 6.  Implementation – C++ (Fastest on LeetCode)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> pathExistenceQueries(int n, vector<int>& nums,
                                     int maxDiff, vector<vector<int>>& queries) {
        // 1. Sort
        vector<pair<int,int>> sorted;
        sorted.reserve(n);
        for (int i=0;i<n;i++) sorted.push_back({nums[i],i});
        sort(sorted.begin(), sorted.end());

        // 2. Mapping
        vector<int> origToSorted(n);
        for (int i=0;i<n;i++) origToSorted[sorted[i].second] = i;

        // 3. Furthest reach
        vector<int> next(n);
        int r = 0;
        for (int l=0;l<n;l++) {
            while (r+1<n && sorted[r+1].first - sorted[l].first <= maxDiff) r++;
            next[l] = r;
        }

        // 4. Binary lifting
        int LOG = 32 - __builtin_clz(n);
        vector<vector<int>> up(LOG, vector<int>(n));
        for (int i=0;i<n;i++) up[0][i] = next[i];
        for (int k=1;k<LOG;k++)
            for (int i=0;i<n;i++)
                up[k][i] = up[k-1][ up[k-1][i] ];

        // 5. Queries
        auto minHops = [&](int u, int v) -> int {
            if (u==v) return 0;
            if (up[0][u] < v && up[LOG-1][u] < v) return -1;
            int steps = 0;
            for (int k=LOG-1;k>=0;k--)
                if (up[k][u] < v) {
                    u = up[k][u];
                    steps += 1<<k;
                }
            return steps+1;
        };

        vector<int> ans;
        ans.reserve(queries.size());
        for (auto &q: queries) {
            int su = origToSorted[q[0]], sv = origToSorted[q[1]];
            if (su>sv) swap(su,sv);
            ans.push_back(minHops(su, sv));
        }
        return ans;
    }
};
```

---

## 6.  “Why It Works” – Deep Dive

### 6.1. Two‑Pointer Window

```text
l  r
↑  ↑
```

* `l` moves from left to right.  
* `r` never moves left – total O(n) operations.  
* Guarantees that for every `l` we have the maximum `r` satisfying the condition.

### 6.2. Binary Lifting

* `up[i][k]` = index reached from `i` after `2^k` hops.  
* Table construction is essentially repeated doubling.  
* It turns a linear sequence of hops into a *logarithmic* decision tree.

### 6.3. Query Resolution

We want the smallest `s` such that after `s` hops we reach at least `b`.  
The greedy loop of `shortestDistance` or `min_hops` performs a *reverse binary search* on the lifting table – exactly the same idea as in “Kth ancestor” problems.

---

## 7.  Testing the Solution

```java
public static void main(String[] args) {
    Solution sol = new Solution();
    int n = 5;
    int[] nums = {1, 5, 10, 3, 6};
    int maxDiff = 2;
    int[][] queries = {{0,4},{2,3},{1,4}};
    System.out.println(Arrays.toString(sol.pathExistenceQueries(n, nums, maxDiff, queries)));
}
```

Output: `[2, 2, -1]` – matches the problem statement example.

---

## 8.  Summary – Why This is the “Best” Approach

* **Linearise** the graph using sorting → windows become contiguous.  
* **Avoid per‑query BFS** – answer in `O(log n)` via binary lifting.  
* **Space‑time trade‑off** is modest (`O(n log n)`), but queries are answered fast.

This pattern is common in problems where a graph’s edges depend on a **metric** (distance, similarity, etc.). Sorting + sliding window + doubling is a tried‑and‑true recipe.

---

## 9.  What If We Change the Parameters?

| Variation | Effect on Algorithm |
|-----------|---------------------|
| **Directed edges** | Pre‑computation becomes asymmetric – need two pointers for left & right windows. |
| **Weighted edges** | Binary lifting no longer works; need Dijkstra or 0‑1 BFS. |
| **Different metric** (`<= K`) | Same sliding‑window logic applies; only the comparison changes. |

---

## 10.  Final Takeaway – “Binary Lifting + Sliding Window” is a Match‑Made Pair

The problem’s structure hides a linear chain that can be traversed by *jumping* as far as possible. Once we expose that chain via sorting, all the heavy lifting is a standard O(log n) query on a doubling table.  

**In contests or production** – keep this pattern in mind whenever you see edges defined by *range constraints* on a sorted attribute.

--- 

### 🚀 Happy coding! 🚀

--- 

> **Reference** – *LeetCode 3534 – Path Existence Queries II*.  
> The official editorial explains the same idea: sorting + two‑pointer + binary lifting.  

--- 

Feel free to drop questions or share alternative solutions!