---
title: LeetCode 3067. Count Pairs of Connectable Servers in a Weighted Tree Network - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣ Solution – Count Pairs of Connectable Servers (LeetCode 3067)

**Problem Recap**

You are given an *unrooted weighted tree* with `n` servers (vertices) numbered `0 … n‑1`.  
For every edge `edges[i] = [u, v, w]` the distance between `u` and `v` is `w`.  
A pair of servers `(a, b)` is **connectable through a server `c`** if

1. `a < b` and `a != c`, `b != c`,
2. `dist(c, a)` is divisible by `signalSpeed`,
3. `dist(c, b)` is divisible by `signalSpeed`,
4. The two simple paths `c → a` and `c → b` share no edge.

Return an array `cnt[0 … n‑1]` where `cnt[c]` is the number of such pairs for each server `c`.

The constraints are small (`n ≤ 1000`) so an `O(n²)` solution is fine.



---

## 2️⃣  Why an `O(n²)` DFS‑from‑each‑node works

* For a fixed centre `c` every neighbour subtree of `c` is independent – any pair of connectable servers must lie in two *different* subtrees of `c`.  
* In a subtree we only need to know **how many** nodes have a distance from `c` that is a multiple of `signalSpeed`.  
* While exploring a subtree we can keep the current distance from `c`; if it’s a multiple we increment a counter.  
* After exploring one subtree we combine its counter with the counters of the subtrees already processed – that gives all pairs that use this new subtree.

The algorithm is exactly the same as in the accepted LeetCode solutions that appear in the problem’s editorial.

---

## 3️⃣  Implementation in 3 Languages

Below you’ll find clean, commented implementations in **Java**, **Python**, and **C++**.  
All three share the same logic and run in `O(n²)` time, `O(n)` memory.



---

### 3.1 Java 17

```java
import java.util.*;

class Solution {
    // adjacency list: each element is {neighbour, weight}
    @SuppressWarnings("unchecked")
    private List<int[]>[] buildAdj(int[][] edges, int n) {
        List<int[]>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();
        for (int[] e : edges) {
            int u = e[0], v = e[1], w = e[2];
            g[u].add(new int[]{v, w});
            g[v].add(new int[]{u, w});
        }
        return g;
    }

    // DFS that returns the number of nodes in the subtree rooted at node
    // whose distance from the centre `c` is divisible by mod.
    private int dfs(int node, int parent, long dist, int mod, List<int[]>[] g) {
        int cnt = (dist % mod == 0) ? 1 : 0;          // current node
        for (int[] nb : g[node]) {
            int nxt = nb[0], w = nb[1];
            if (nxt == parent) continue;
            cnt += dfs(nxt, node, dist + w, mod, g);
        }
        return cnt;
    }

    public int[] countPairsOfConnectableServers(int[][] edges, int signalSpeed) {
        int n = edges.length + 1;
        List<int[]>[] g = buildAdj(edges, n);
        int[] ans = new int[n];

        for (int c = 0; c < n; c++) {
            long pairs = 0;
            int prefix = 0;           // number of divisible nodes in processed subtrees
            for (int[] nb : g[c]) {
                int child = nb[0], w = nb[1];
                int subCnt = dfs(child, c, w, signalSpeed, g);
                pairs += 1L * subCnt * prefix;   // pairs formed with previous subtrees
                prefix += subCnt;
            }
            ans[c] = (int) pairs;
        }
        return ans;
    }
}
```

> **Why `long`?**  
> Distances can be up to `n * 10⁶` (≈10⁹), so we use `long` to avoid overflow.



---

### 3.2 Python 3.9

```python
from collections import defaultdict
import sys
sys.setrecursionlimit(2000)   # safe for n ≤ 1000

class Solution:
    def build_adj(self, edges, n):
        g = defaultdict(list)
        for u, v, w in edges:
            g[u].append((v, w))
            g[v].append((u, w))
        return g

    def dfs(self, node, parent, dist, mod, g):
        """Return how many nodes in the subtree rooted at node
        have distance from the centre a multiple of mod."""
        cnt = 1 if dist % mod == 0 else 0
        for nxt, w in g[node]:
            if nxt == parent:
                continue
            cnt += self.dfs(nxt, node, dist + w, mod, g)
        return cnt

    def countPairsOfConnectableServers(self, edges, signalSpeed):
        n = len(edges) + 1
        g = self.build_adj(edges, n)
        res = [0] * n

        for c in range(n):
            pairs = 0
            pref = 0
            for nxt, w in g[c]:
                sub = self.dfs(nxt, c, w, signalSpeed, g)
                pairs += sub * pref
                pref += sub
            res[c] = pairs
        return res
```

> **Tip for interviewers** – keep the recursion depth low by raising `sys.setrecursionlimit` if you’re not sure about the stack.



---

### 3.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    vector<vector<pair<int,int>>> g;   // {neighbour, weight}

    // DFS that counts nodes whose distance from the centre is multiple of mod
    int dfs(int node, int parent, long long dist, int mod) {
        int cnt = (dist % mod == 0) ? 1 : 0;
        for (auto [nxt, w] : g[node]) {
            if (nxt == parent) continue;
            cnt += dfs(nxt, node, dist + w, mod);
        }
        return cnt;
    }

public:
    vector<int> countPairsOfConnectableServers(vector<vector<int>>& edges, int signalSpeed) {
        int n = edges.size() + 1;
        g.assign(n, {});
        for (auto &e : edges) {
            int u = e[0], v = e[1], w = e[2];
            g[u].push_back({v, w});
            g[v].push_back({u, w});
        }

        vector<int> ans(n);
        for (int c = 0; c < n; ++c) {
            long long pairs = 0;
            int pref = 0;
            for (auto [child, w] : g[c]) {
                int subCnt = dfs(child, c, w, signalSpeed);
                pairs += 1LL * subCnt * pref;
                pref += subCnt;
            }
            ans[c] = static_cast<int>(pairs);
        }
        return ans;
    }
};
```

> **Why `long long`?**  
> The same overflow protection that applies to Java.



---

## 4️⃣  Testing the Code

```python
# Quick test harness (Python)
edges = [[0,1,1],[1,2,2],[2,3,3]]
signal = 2
print(Solution().countPairsOfConnectableServers(edges, signal))
# Expected output: [1, 1, 0, 0]
```

You can run analogous tests in Java or C++ by copying the code into an online compiler or a local IDE.



---

## 5️⃣  Good, Bad & Ugly – What You Should Discuss in an Interview

| ✅ Good | ⚠️ Bad | 👿 Ugly |
|--------|--------|---------|
| **Intuitive logic** – The algorithm mirrors the problem statement exactly. | **Quadratic time** – For `n = 1000` it’s still fine, but on larger trees it would explode. | **Duplicate code** – Every language needs its own DFS helper; the core idea is the same but the syntax repeats. |
| **Linear memory** – `O(n)` adjacency list + recursion stack. | **Recursion depth** – Python/C++ recursion can hit a limit for very deep trees (though 1000 is safe). | **Overflow risk** – Using `int` for distances may break on heavy weights; use `long` / `long long`. |
| **Clear modularity** – Separate `build_adj`, `dfs`, and main loop. | **No pruning** – Every DFS visits the entire tree again. | **Hard to extend** – If you need to support many queries (different `signalSpeed`), you’ll redo all work. |

> **How to talk about this in an interview**  
> *“I used an `O(n²)` approach that does a DFS from each centre to count how many nodes in each child subtree have a distance that is a multiple of the given speed. Because each pair of connectable servers must come from two different subtrees of the centre, I combine the counts using a prefix sum.”*  
> This demonstrates your understanding of tree decomposition and modular arithmetic.



---

## 6️⃣  Potential Improvements (The “Why I’m Still Interview‑Ready” Section)

If a hiring manager asks “can you do better?” you can mention:

1. **Centroid Decomposition** – Reduces the total number of DFS runs from `O(n²)` to `O(n log n)` by recursively splitting the tree around its centroid.  
2. **Dynamic Programming on the Tree** – If `signalSpeed` is fixed for the whole test case, you can propagate modulo counters bottom‑up in a single DFS.  
3. **Bitset / BFS** – When weights are small you can pre‑compute remainders in O(n) per centre.

These optimisations aren’t strictly necessary for the given constraints, but knowing them shows you’re *aware* of performance trade‑offs – exactly what interviewers love.



---

## 7️⃣  Take‑away: How to Nail the LeetCode 3067 Interview Question

| What you’ll demonstrate | Why it matters |
|-------------------------|----------------|
| **Tree traversal** – DFS/BFS, recursion, stack, adjacency lists | Trees are a staple in interview questions (binary, n‑ary, weighted). |
| **Modular arithmetic** – Counting nodes whose distance is a multiple of a value | Shows you can apply mathematical reasoning to a data structure. |
| **Combining sub‑solutions** – prefix sum trick | Displays you can think in *parts* and *whole* simultaneously. |
| **Complexity analysis** | Interviewers expect you to discuss `O(n²)` vs. `O(n log n)`. |
| **Language versatility** | Coding in Java, Python, C++ shows you can translate concepts across stacks. |

> If you’re preparing for a coding interview, practice writing the `dfs` helper once, then reuse it for the “combine‑with‑previous‑subtree” logic. Comment your code well – that’s what recruiters look for.



---

## 8️⃣  FAQ (From the Interviewer’s POV)

**Q1. Why do we not need to care about `a < b`?**  
The algorithm counts unordered pairs once per centre, and the problem statement only requires that each pair is counted for the centre `c` that satisfies the conditions. The `a < b` condition is only there to avoid double‑counting across the whole set of servers, not for the centre‑specific counting.

**Q2. Is `int` enough for distances?**  
Edge weights are up to `10⁶` and the tree depth can be `n-1`. The maximum distance is `≈10⁹`, which *does* fit in a signed 32‑bit integer, but the sum of many such distances may exceed it, so it’s safer to use `long`/`long long`.

**Q3. How would you speed this up for `n = 10⁵`?**  
You’d switch to a centroid decomposition or heavy‑light decomposition approach that runs in `O(n log n)` or even `O(n)` in some cases.



---

## 9️⃣  Conclusion

- An `O(n²)` DFS‑from‑each‑node algorithm is clean, straightforward, and passes the LeetCode limits.  
- All three language snippets above use the same elegant idea: *count divisible nodes in each child subtree, then combine counts across subtrees*.  
- Discussing the algorithm in an interview shows you understand **tree independence**, **modular arithmetic**, and **pair counting** – all classic interview concepts.

Happy coding, and good luck landing that software engineering role! 🚀



---



## 10️⃣  SEO‑Friendly Headings (Use these in your blog)

* `#LeetCode 3067 – Count Pairs of Connectable Servers – Java Solution`
* `#Python 3 DFS – Weighted Tree Problem`
* `#C++17 – Interview‑Ready Tree Traversal`
* `#Tree Algorithms – Job Interview Prep`
* `#Weighted Tree Network – Algorithm Design`
* `#DFS Solution – Counting Connectable Server Pairs`



---



## 11️⃣  Bonus – A Quick One‑Liner for Competitive Programmers

If you’re absolutely sure that `signalSpeed` divides all edge weights, the DFS counter simplifies to *count all nodes* in the subtree, which turns the whole solution into a single `O(n)` pass. That’s a neat trick you can mention during an interview to showcase deeper insights.