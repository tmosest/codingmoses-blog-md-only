---
title: LeetCode 3575. Maximum Good Subtree Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Maximum Good Subtree Score – A Tree‑DP with Bitmasks**  
*Java | Python | C++ implementations + SEO‑friendly blog post*  

---

## 1.  Problem Recap  

We are given an undirected tree rooted at node **0**.  
Each node `i` has a value `vals[i]` (1 ≤ `vals[i]` ≤ 10⁹).  
A subset of nodes inside a subtree is called **good** if, when you write all the selected values in decimal, **each digit 0–9 appears at most once**.  
(If a value contains a repeated digit – e.g. 121 – it can never belong to a good subset.)

For every node `u` we need the maximum sum of a good subset that lies entirely in the subtree of `u`.  
Finally we return  

```
(sum over all nodes of that maximum)  mod 1 000 000 007
```

`par[i]` describes the tree:  
`par[0] = -1` and for every `i>0` there is an edge `(par[i] , i)`.

`n = par.length ≤ 500`.

The task is to compute the answer efficiently.



---

## 2.  High‑Level Idea  

1. **Digit mask** – For a value `x` we build a 10‑bit mask.  
   * bit `d` is 1  ⇔  digit `d` occurs in `x`.  
   * If any digit appears twice, the mask is *invalid* (return `-1`).

2. **DP over masks** –  
   For a subtree we keep an array `dp[1024]` where  

```
dp[mask] = maximum sum that uses only the digits set in 'mask'
```

   (If `mask` is a subset of the digits already used, the corresponding sum can be achieved.)

3. **Adding a node** –  
   Suppose the current node has mask `S` (valid).  
   We can extend every previously achievable set `M` that is **disjoint** with `S`.  
   That is, for every sub‑mask `m` of `~S` we do

```
dp[m | S] = max(dp[m | S], dp[m] + vals[node])
```

4. **Small‑to‑Large merging** –  
   Naïvely merging two child DP tables would require a convolution over *disjoint* masks (≈ 3¹⁰ ≈ 59 000 ops per merge).  
   With **small‑to‑large** we keep the DP of the largest child and “add” the rest of the children one node at a time.  
   Every node is re‑processed at most `O(log n)` times, giving a total cost `O(n log n · 2¹⁰)` which is trivial for `n ≤ 500`.

---

## 2.  The DP In‑Depth  

| Symbol | Meaning |
|--------|---------|
| `mask` | 10‑bit integer – set bits represent used digits |
| `dp[mask]` | best sum that uses exactly the digits of `mask` |
| `S` | mask of the current node’s value (or `-1` if it contains a repeated digit) |
| `ALL = 1023` (binary 1111111111) | union of all 10 digits |

### Adding a node with mask `S`

```text
for every sub‑mask 'm' of (ALL ^ S)     // masks that do NOT use digits of S
        dp[m | S] = max( dp[m | S] , dp[m] + val )
```

The enumeration of sub‑masks uses the classic “`for (int m = sub; m; m = (m-1) & sub)`” trick, which costs at most 1024 iterations per node.

### Result for a subtree

After all children have been merged and the current node added, the answer for the subtree is simply `dp[ALL]` – the best sum that may use **any** set of digits.

---

## 3.  Small‑to‑Large Technique

1. **DFS** – Traverse the tree building adjacency lists from `par`.
2. For each node `u`:
   * Recursively compute the DP tables of all children.
   * Pick the child with the largest subtree (`bigChild`) and *reuse* its DP array as the base (`dp`).
   * Add node `u` to `dp`.
   * For every *other* child `v` (the “small” ones) run a helper `addSubtree(v, dp)` that walks through the whole small subtree and adds each node to the same `dp`.  
     This is safe because we are only **appending** nodes, never merging two full DP arrays.

The number of times any node is re‑inserted equals the number of ancestors that chose it as a *small* child, which is bounded by `O(log n)`.

---

## 4.  Correctness Sketch  

* **Digit mask** guarantees that no value with repeated digits ever contributes to a good subset.
* The DP update rule only combines sets of digits that are *disjoint*, so the resulting subset remains good.
* The empty subset is implicitly represented by the initial zeros in the DP array.
* The answer for each node is `dp[ALL]`, which is the maximum over all masks, i.e. the maximum sum of any good subset inside that subtree.

---

## 5.  Complexity Analysis  

| Step | Time | Space |
|------|------|-------|
| Building adjacency list | `O(n)` | `O(n)` |
| DFS + small‑to‑large | `O(n log n · 2¹⁰)` ≈ **4.6 M operations** for `n = 500` | `O(n)` extra arrays, plus the fixed 1024‑long DP per recursion level |

Both Java and C++ need 64‑bit (`long long` / `long`) for intermediate sums; the final answer is taken modulo 1 000 000 007.

---

## 6.  Edge‑Cases & Notes  

| Case | Handling |
|------|----------|
| Value = 0 | Not allowed by constraints, but `getMask` treats it as a single digit 0. |
| All nodes invalid | All DP entries stay 0 → answer 0. |
| Star‑shaped tree | Small‑to‑large re‑processes every leaf only once → still fast. |

---

## 7.  Code Implementations  

Below are clean, copy‑&‑paste ready solutions for **Java 17**, **Python 3.10**, and **C++17**.  
All three follow the exact same logic as described.

---

### 7.1  Java

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;
    private static final int FULL_MASK = (1 << 10) - 1;   // 1023

    public int goodSubtreeSum(int[] vals, int[] par) {
        int n = par.length;
        List<Integer>[] g = new ArrayList[n];
        for (int i = 0; i < n; ++i) g[i] = new ArrayList<>();

        for (int i = 1; i < n; ++i) g[par[i]].add(i);

        long[] ans = new long[n];
        int[] sz = new int[n];
        dfs(0, vals, g, ans, sz);

        long sum = 0;
        for (long v : ans) sum += v;
        return (int) (sum % MOD);
    }

    /** depth‑first search – returns dp array for the subtree rooted at u */
    private long[] dfs(int u, int[] vals, List<Integer>[] g,
                       long[] ans, int[] sz) {
        sz[u] = 1;
        long[] dp = null;          // will hold the DP of the heaviest child
        int heavy = -1;

        for (int v : g[u]) {
            long[] child = dfs(v, vals, g, ans, sz);
            sz[u] += sz[v];
            if (heavy == -1 || sz[v] > sz[heavy]) {
                heavy = v;
                dp = child;        // reuse the heavy child's DP
            }
        }

        if (dp == null) dp = new long[FULL_MASK + 1];

        addNode(u, vals, dp);

        // merge the small children by adding them node‑by‑node
        for (int v : g[u]) {
            if (v != heavy) addSubtree(v, vals, g, dp);
        }

        ans[u] = dp[FULL_MASK];   // best over all masks
        return dp;
    }

    /** add all nodes of the subtree rooted at u into an existing dp array */
    private void addSubtree(int u, int[] vals, List<Integer>[] g, long[] dp) {
        addNode(u, vals, dp);
        for (int v : g[u]) addSubtree(v, vals, g, dp);
    }

    /** add a single node to the dp array */
    private void addNode(int u, int[] vals, long[] dp) {
        int mask = digitMask(vals[u]);
        if (mask < 0) return;          // value has repeated digit → cannot be used

        int free = FULL_MASK ^ mask;   // masks that do NOT use digits of this node
        // iterate over all submasks of 'free' in descending order
        for (int sub = free; sub > 0; sub = (sub - 1) & free) {
            int newMask = sub | mask;
            dp[newMask] = Math.max(dp[newMask], dp[sub] + vals[u]);
        }
        dp[mask] = Math.max(dp[mask], vals[u]);   // case where we take only this node
    }

    /** returns bitmask of digits or -1 if a digit repeats in x */
    private int digitMask(int x) {
        int seen = 0;
        while (x > 0) {
            int d = x % 10;
            if ((seen & (1 << d)) != 0) return -1;
            seen |= (1 << d);
            x /= 10;
        }
        return seen;
    }
}
```

---

### 7.2  Python

```python
from typing import List

MOD = 1_000_000_007
FULL = (1 << 10) - 1   # 1023

class Solution:
    def goodSubtreeSum(self, vals: List[int], par: List[int]) -> int:
        n = len(par)
        g = [[] for _ in range(n)]
        for i in range(1, n):
            g[par[i]].append(i)

        ans = [0] * n
        sz = [0] * n
        self.dfs(0, vals, g, ans, sz)

        total = sum(ans) % MOD
        return total

    def dfs(self, u: int, vals: List[int], g: List[List[int]],
            ans: List[int], sz: List[int]) -> List[int]:
        sz[u] = 1
        dp = None
        heavy = -1

        for v in g[u]:
            child = self.dfs(v, vals, g, ans, sz)
            sz[u] += sz[v]
            if heavy == -1 or sz[v] > sz[heavy]:
                heavy = v
                dp = child

        if dp is None:
            dp = [0] * (FULL + 1)

        self.add_node(u, vals, dp)

        for v in g[u]:
            if v != heavy:
                self.add_subtree(v, vals, g, dp)

        ans[u] = dp[FULL]  # best over all masks
        return dp

    def add_subtree(self, u: int, vals: List[int], g: List[List[int]], dp: List[int]):
        self.add_node(u, vals, dp)
        for v in g[u]:
            self.add_subtree(v, vals, g, dp)

    def add_node(self, u: int, vals: List[int], dp: List[int]):
        mask = self.digit_mask(vals[u])
        if mask < 0:   # repeated digit – cannot be part of a good subset
            return
        free = FULL ^ mask
        sub = free
        while sub:
            new = sub | mask
            dp[new] = max(dp[new], dp[sub] + vals[u])
            sub = (sub - 1) & free
        dp[mask] = max(dp[mask], vals[u])

    def digit_mask(self, x: int) -> int:
        seen = 0
        while x:
            d = x % 10
            if seen & (1 << d):
                return -1
            seen |= 1 << d
            x //= 10
        return seen
```

---

### 7.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int goodSubtreeSum(vector<int>& vals, vector<int>& par) {
        const int MOD = 1'000'000'007;
        int n = par.size();
        vector<vector<int>> g(n);
        for (int i = 1; i < n; ++i) g[par[i]].push_back(i);

        vector<long long> best(n);
        vector<int> sz(n);
        dfs(0, vals, g, best, sz);

        long long sum = 0;
        for (auto v : best) sum += v;
        return int(sum % MOD);
    }

private:
    static constexpr int FULL = (1 << 10) - 1;   // 1023

    vector<long long> dfs(int u, vector<int>& vals,
                          vector<vector<int>>& g,
                          vector<long long>& best, vector<int>& sz) {
        sz[u] = 1;
        vector<long long> dp;   // will be set to heavy child’s DP
        int heavy = -1;

        for (int v : g[u]) {
            auto child = dfs(v, vals, g, best, sz);
            sz[u] += sz[v];
            if (heavy == -1 || sz[v] > sz[heavy]) {
                heavy = v;
                dp = std::move(child);
            }
        }

        if (dp.empty()) dp.assign(FULL + 1, 0);

        addNode(u, vals, dp);

        for (int v : g[u]) if (v != heavy) addSubtree(v, vals, g, dp);

        best[u] = dp[FULL];
        return dp;
    }

    void addSubtree(int u, vector<int>& vals,
                    vector<vector<int>>& g,
                    vector<long long>& dp) {
        addNode(u, vals, dp);
        for (int v : g[u]) addSubtree(v, vals, g, dp);
    }

    void addNode(int u, vector<int>& vals, vector<long long>& dp) {
        int mask = digitMask(vals[u]);
        if (mask < 0) return;           // repeated digit – skip

        int free = FULL ^ mask;
        for (int sub = free; sub; sub = (sub - 1) & free) {
            int newMask = sub | mask;
            dp[newMask] = max(dp[newMask], dp[sub] + vals[u]);
        }
        dp[mask] = max(dp[mask], (long long)vals[u]);
    }

    int digitMask(int x) {
        int seen = 0;
        while (x) {
            int d = x % 10;
            if (seen & (1 << d)) return -1;
            seen |= 1 << d;
            x /= 10;
        }
        return seen;
    }
};
```

---

## 8.  Summary & Takeaway  

* **Digit masks + DP over masks** gives an elegant way to enforce the “unique digit” rule.
* **Small‑to‑large** keeps the solution fast even on dense trees.
* The algorithm runs comfortably within limits for `n ≤ 500` while being easy to understand and implement.

Feel free to copy the snippet into your LeetCode submission or use it as a teaching example for “DP on trees + bitmask + small‑to‑large” patterns.



---



---

**Happy coding!** 🚀