---
title: LeetCode 3558. Number of Ways to Assign Edge Weights I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview

**Leetcode 3558 – Number of Ways to Assign Edge Weights I**  
*Medium*

We are given an undirected tree with nodes numbered `1 … n` and a root at node `1`.  
All edges initially have weight `0`.  Each edge may be assigned a weight of `1` **or** `2`.

Choose **one** node `x` that lies at the maximum depth of the tree (the number of edges from `1` to `x` is as large as possible).  
Only the edges on the unique path from the root to `x` matter – all other edges can be ignored.  
How many assignments of weights to those edges make the *total cost* (sum of the edge weights) **odd**?  
Answer modulo `1 000 000 007`.

The goal is to produce a clean solution in **Python, Java, and C++** and then write a short, SEO‑friendly blog article that discusses the good, the bad and the ugly aspects of this problem and of the chosen solution.



--------------------------------------------------------------------

## 2.  Key Observation

Let the depth of the chosen node be `k` (the number of edges on the path).  
We need the number of binary strings `b1 … bk` where `bi ∈ {1,2}` and  

```
S = b1 + b2 + … + bk   is odd
```

For the first `k‑1` edges we have `2^(k‑1)` possibilities.  
Whatever the partial sum `S' = b1 + … + b(k‑1)` is, the **last** edge weight is forced:

| `S'` even | `S'` odd |
|-----------|----------|
| `wk = 1`  | `wk = 2` |

Thus *once the first `k‑1` edges are fixed there is exactly one choice for the last edge*.  
Therefore the total number of valid assignments equals the number of assignments for the first `k‑1` edges:

```
answer = 2^(k-1)
```

So the whole task boils down to finding the maximum depth `k` of the tree.



--------------------------------------------------------------------

## 3.  Algorithm

1. **Build the adjacency list** of the tree (`O(n)`).
2. **Compute the depth of every node** starting from the root (DFS or BFS).  
   Keep track of the maximum depth `maxDepth`.
3. **Return** `powmod(2, maxDepth-1, MOD)`.

The only non‑trivial part is computing `maxDepth`.  
With `n ≤ 10⁵` a recursive DFS in Python can hit the recursion limit, so we either raise the limit or use an iterative stack.  
In Java and C++ we’ll use an **iterative BFS** (queue) – it’s stack‑safe and gives us the depth array in one pass.



--------------------------------------------------------------------

## 3.  Code

Below are *self‑contained* implementations in Python, Java and C++.
All solutions include:

* adjacency‑list construction
* iterative traversal to obtain depths
* fast modular exponentiation (`pow` in Python, `BigInteger.modPow` in Java, custom binary exponentiation in C++)

### 3.1  Python 3

```python
#!/usr/bin/env python3
"""
Leetcode 3558 – Number of Ways to Assign Edge Weights I
"""
from collections import deque
import sys

MOD = 1_000_000_007

def assign_edge_weights(edges):
    """
    :type edges: List[List[int]]
    :rtype: int
    """
    if not edges:
        return 0  # should never happen (n ≥ 2)

    n = max(max(u, v) for u, v in edges)  # nodes are 1‑based
    adj = [[] for _ in range(n + 1)]
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)

    # BFS to compute depth of every node from the root (node 1)
    depth = [-1] * (n + 1)
    depth[1] = 0
    q = deque([1])
    max_depth = 0

    while q:
        u = q.popleft()
        for v in adj[u]:
            if depth[v] == -1:
                depth[v] = depth[u] + 1
                max_depth = max(max_depth, depth[v])
                q.append(v)

    # answer = 2^(max_depth-1) mod MOD
    return pow(2, max_depth - 1, MOD)


# ------------------------------------------------------------------
# Example usage
# ------------------------------------------------------------------
if __name__ == "__main__":
    edges1 = [[1, 2]]
    print(assign_edge_weights(edges1))          # → 1

    edges2 = [[1, 2], [1, 3], [3, 4], [3, 5]]
    print(assign_edge_weights(edges2))          # → 2^(2-1)=2
```

*Why `pow(2, max_depth-1, MOD)` works* – the built‑in `pow` implements binary exponentiation, so the complexity stays `O(log max_depth)`.



--------------------------------------------------------------------

### 3.2  Java 17

```java
import java.io.*;
import java.util.*;

/**
 * Leetcode 3558 – Number of Ways to Assign Edge Weights I
 */
public class Solution {
    private static final long MOD = 1_000_000_007L;

    public long assignEdgeWeights(List<List<Integer>> edges) {
        if (edges.isEmpty()) return 0L;        // impossible by the statement

        int n = 0;
        for (List<Integer> e : edges) {
            n = Math.max(n, Math.max(e.get(0), e.get(1)));
        }

        @SuppressWarnings("unchecked")
        List<Integer>[] g = new ArrayList[n + 1];
        for (int i = 1; i <= n; i++) g[i] = new ArrayList<>();

        for (List<Integer> e : edges) {
            int u = e.get(0), v = e.get(1);
            g[u].add(v);
            g[v].add(u);
        }

        // BFS from root 1 to compute depths
        int[] depth = new int[n + 1];
        Arrays.fill(depth, -1);
        Queue<Integer> q = new ArrayDeque<>();
        depth[1] = 0;
        q.offer(1);
        int maxDepth = 0;

        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : g[u]) {
                if (depth[v] == -1) {
                    depth[v] = depth[u] + 1;
                    maxDepth = Math.max(maxDepth, depth[v]);
                    q.offer(v);
                }
            }
        }

        return modPow(2, maxDepth - 1, MOD);
    }

    // fast modular exponentiation
    private long modPow(long base, long exp, long mod) {
        long res = 1 % mod;
        long b = base % mod;
        while (exp > 0) {
            if ((exp & 1) == 1) res = (res * b) % mod;
            b = (b * b) % mod;
            exp >>= 1;
        }
        return res;
    }

    // ------------------------------------------------------------------
    // Main method for quick manual testing
    // ------------------------------------------------------------------
    public static void main(String[] args) throws IOException {
        Solution sol = new Solution();
        List<List<Integer>> edges1 = List.of(List.of(1, 2));
        System.out.println(sol.assignEdgeWeights(edges1));          // 1

        List<List<Integer>> edges2 = List.of(
                List.of(1, 2),
                List.of(1, 3),
                List.of(3, 4),
                List.of(3, 5)
        );
        System.out.println(sol.assignEdgeWeights(edges2));          // 2
    }
}
```

*What the Java code does*  
* BFS guarantees we never overflow the call stack.  
* `modPow` is a classic binary exponentiation – `O(log maxDepth)` time and `O(1)` memory.



--------------------------------------------------------------------

### 3.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1'000'000'007LL;

// fast modular exponentiation
long long modPow(long long base, long long exp) {
    long long res = 1;
    base %= MOD;
    while (exp > 0) {
        if (exp & 1LL) res = res * base % MOD;
        base = base * base % MOD;
        exp >>= 1LL;
    }
    return res;
}

class Solution {
public:
    long long assignEdgeWeights(vector<vector<int>>& edges) {
        if (edges.empty()) return 0;               // never happens by constraints

        int n = 0;
        for (auto &e : edges) n = max(n, max(e[0], e[1]));

        vector<vector<int>> adj(n + 1);
        for (auto &e : edges) {
            adj[e[0]].push_back(e[1]);
            adj[e[1]].push_back(e[0]);
        }

        // BFS to compute depths from root 1
        vector<int> depth(n + 1, -1);
        queue<int> q;
        depth[1] = 0;
        q.push(1);
        int maxDepth = 0;

        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : adj[u]) if (depth[v] == -1) {
                depth[v] = depth[u] + 1;
                maxDepth = max(maxDepth, depth[v]);
                q.push(v);
            }
        }

        // answer = 2^(maxDepth-1) mod MOD
        return modPow(2LL, maxDepth - 1);
    }
};

/* -------------------------------------------------------------
   Example usage (not required by Leetcode but handy for quick testing)
   ------------------------------------------------------------- */
int main() {
    Solution sol;
    vector<vector<int>> edges1 = {{1,2}};
    cout << sol.assignEdgeWeights(edges1) << '\n';   // 1

    vector<vector<int>> edges2 = {{1,2},{1,3},{3,4},{3,5}};
    cout << sol.assignEdgeWeights(edges2) << '\n';   // 2

    return 0;
}
```

--------------------------------------------------------------------

## 3.  Complexity Analysis (for all three implementations)

| Step                         | Time   | Memory |
|------------------------------|--------|--------|
| Build adjacency list         | `O(n)` | `O(n)` |
| Compute depths (BFS/DFS)     | `O(n)` | `O(n)` |
| Modular exponentiation        | `O(log maxDepth)` | `O(1)` |
| **Total**                    | `O(n + log n)` ≈ `O(n)` | `O(n)` |

`maxDepth` is at most `n‑1`, so the logarithmic factor is negligible compared to the linear pass.



--------------------------------------------------------------------

## 4.  Why This Solution Is Efficient

* The formula `2^(k-1)` removes the need for any combinatorial DP.  
* BFS/DFS visits each edge exactly once.  
* Modular exponentiation is logarithmic, so even for a chain of 10⁵ nodes the answer is computed instantly.



--------------------------------------------------------------------

## 4.  FAQ

* **Q**: *Can the tree have only 2 nodes?*  
  **A**: Yes. Then `maxDepth = 1`, answer = `2^0 = 1`.

* **Q**: *Is there any hidden overflow risk?*  
  **A**: All arithmetic is done modulo `MOD` – the helper functions (`pow`, `modPow`) ensure intermediate values stay within 64‑bit signed integers.

* **Q**: *Why not just use `DFS` in all languages?*  
  **A**: In C++ and Java an iterative BFS is safer for very deep trees.  
  In Python you can also do a recursive DFS by increasing the recursion limit (`sys.setrecursionlimit`), but the iterative version is more portable.



--------------------------------------------------------------------

## 4.  Final Remarks

The key insight is that **only the maximum depth matters** – the combinatorics collapses to a single exponentiation.  
All three solutions reflect that: after a clean traversal we simply raise 2 to the appropriate power modulo `1e9+7`.



--------------------------------------------------------------------

# 4.  “Leetcode 3558 – Number of Ways to Assign Edge Weights I”  
**An Efficient, Multi‑Language Solution**

**Why it matters**  
You’ll see the same pattern – *find the longest path from the root* – in many tree problems.  
Once you know the maximum depth, the rest is a one‑liner:

```
answer = 2^(maxDepth-1)  (mod 1e9+7)
```

Happy coding!  



--------------------------------------------------------------------

**Author** – *A seasoned competitive programmer, always eager to share clean, language‑agnostic solutions.*



--------------------------------------------------------------------
*Feel free to drop a ⭐ on the repo if you found the code helpful!*



--- 

*End of answer.*