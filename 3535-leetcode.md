---
title: LeetCode 3535. Unit Conversion II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 3535. Unit Conversion II – A Complete, SEO‑Optimised Guide (Java / Python / C++)

> **“When you solve a problem once, you’re ready for a thousand more.”** – *LeetCode*  

This article walks through the **Unit Conversion II** problem from LeetCode, gives a working implementation in **Java, Python, and C++**, and then dives into the *good, the bad, and the ugly* of the solution. The post is written for **software‑engineering interview preparation** and is fully optimised for the keywords **Unit Conversion II, LeetCode, job interview, DFS, BFS, Java, Python, C++**.

---

## 1. Problem Recap

```
There are n unit types (0 … n‑1).  
conversions[i] = [source, target, factor]  →  1 unit of “source” = factor units of “target”.
queries[i] = [Ai, Bi] → how many Bi‑units equal 1 Ai‑unit?  Answer as (p / q) mod 1e9+7.
```

* `1 <= factor <= 1e9`
* `1 <= n, q <= 1e5`
* The graph is a tree (exactly `n‑1` edges) and every unit is reachable from unit 0.

The required answer for each query is:

```
answer[i] = (factor of Bi relative to Ai) mod MOD
          = (numerator * inv(denominator)) mod MOD
```

where `inv(x)` is the modular inverse modulo `MOD = 1_000_000_007`.

---

## 2. Observations

1. **Tree structure** – there is exactly one simple path between any two units.
2. **Multiplicative chain** – going from `u` to `v` multiplies the conversion factor of every edge on the path.
3. **Reverse direction** – if edge `u → v` has factor `c`, then `v → u` has factor `1 / c`.  
   In modular arithmetic we store `c` and its modular inverse `c⁻¹`.
4. Once we know the cumulative factor from the root (unit 0) to *every* node, we can answer any query in `O(1)`:

```
factor(Ai → Bi) = (factor(0 → Bi)) * inv(factor(0 → Ai))
```

So the problem reduces to a **single DFS/BFS** that propagates two values for each node:
* `toRoot[u]` – product of edge factors from root to `u`.
* `invToRoot[u]` – product of edge inverses from root to `u`.

---

## 3. Algorithm (DFS version)

```
build adjacency list:
    for each edge (a,b,c):
        add (b, c, c⁻¹) to a
        add (a, c⁻¹, c) to b

DFS(0):
    toRoot[0]   = 1
    invToRoot[0] = 1
    stack/recursion:
        for each neighbor (v, factor, invFactor):
            if not visited:
                toRoot[v]   = toRoot[u] * factor   % MOD
                invToRoot[v] = invToRoot[u] * invFactor % MOD
                DFS(v)

answer queries:
    res = (toRoot[Bi] * invToRoot[Ai]) % MOD
```

**Time complexity** – `O(n + q)`  
**Space complexity** – `O(n)`

The only heavy operation is modular exponentiation used once per edge to compute the inverse `c⁻¹` (`pow(c, MOD-2)`), which is `O(log MOD)` but done `n‑1` times.

---

## 4. Code

### 4.1 Java (DFS)

```java
import java.util.*;

class Solution {
    private static final long MOD = 1_000_000_007L;

    public int[] queryConversions(int[][] conversions, int[][] queries) {
        int n = conversions.length + 1;
        List<long[]>[] adj = new ArrayList[n];
        for (int i = 0; i < n; i++) adj[i] = new ArrayList<>();

        // Build graph
        for (int[] e : conversions) {
            int a = e[0], b = e[1];
            long c = e[2];
            long inv = modPow(c, MOD - 2);
            adj[a].add(new long[]{b, c, inv}); // a -> b
            adj[b].add(new long[]{a, inv, c}); // b -> a
        }

        long[] toRoot   = new long[n];
        long[] invToRoot = new long[n];
        boolean[] vis = new boolean[n];
        toRoot[0] = invToRoot[0] = 1;
        dfs(0, adj, toRoot, invToRoot, vis);

        int[] res = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int a = queries[i][0];
            int b = queries[i][1];
            res[i] = (int) (toRoot[b] * invToRoot[a] % MOD);
        }
        return res;
    }

    private void dfs(int u, List<long[]>[] adj,
                     long[] toRoot, long[] invToRoot, boolean[] vis) {
        vis[u] = true;
        for (long[] e : adj[u]) {
            int v = (int) e[0];
            if (vis[v]) continue;
            long fac  = e[1];
            long invFac = e[2];
            toRoot[v]   = toRoot[u]   * fac  % MOD;
            invToRoot[v] = invToRoot[u] * invFac % MOD;
            dfs(v, adj, toRoot, invToRoot, vis);
        }
    }

    private long modPow(long a, long exp) {
        long res = 1;
        while (exp > 0) {
            if ((exp & 1) == 1) res = res * a % MOD;
            a = a * a % MOD;
            exp >>= 1;
        }
        return res;
    }
}
```

---

### 4.2 Python (DFS)

```python
MOD = 1_000_000_007

class Solution:
    def queryConversions(self, conversions: List[List[int]],
                          queries: List[List[int]]) -> List[int]:
        n = len(conversions) + 1
        adj = [[] for _ in range(n)]

        for a, b, c in conversions:
            inv = pow(c, MOD - 2, MOD)
            adj[a].append((b, c, inv))     # a -> b
            adj[b].append((a, inv, c))     # b -> a

        to_root = [0] * n
        inv_root = [0] * n
        visited = [False] * n
        to_root[0] = inv_root[0] = 1
        self.dfs(0, adj, to_root, inv_root, visited)

        res = []
        for a, b in queries:
            res.append(to_root[b] * inv_root[a] % MOD)
        return res

    def dfs(self, u, adj, to_root, inv_root, visited):
        visited[u] = True
        for v, fac, inv_fac in adj[u]:
            if visited[v]:
                continue
            to_root[v] = to_root[u] * fac % MOD
            inv_root[v] = inv_root[u] * inv_fac % MOD
            self.dfs(v, adj, to_root, inv_root, visited)
```

---

### 4.3 C++ (DFS)

```cpp
#include <bits/stdc++.h>
using namespace std;
const long long MOD = 1'000'000'007LL;

class Solution {
public:
    vector<int> queryConversions(vector<vector<int>>& conversions,
                                 vector<vector<int>>& queries) {
        int n = conversions.size() + 1;
        vector<vector<array<long long,3>>> adj(n);
        for (auto &e : conversions) {
            int a = e[0], b = e[1];
            long long c = e[2];
            long long inv = modpow(c, MOD-2);
            adj[a].push_back({b, c, inv});
            adj[b].push_back({a, inv, c});
        }

        vector<long long> toRoot(n), invRoot(n);
        vector<int> vis(n,0);
        toRoot[0] = invRoot[0] = 1;
        dfs(0, adj, toRoot, invRoot, vis);

        vector<int> ans;
        for (auto &q : queries) {
            int a = q[0], b = q[1];
            ans.push_back((int)(toRoot[b] * invRoot[a] % MOD));
        }
        return ans;
    }

private:
    void dfs(int u,
             vector<vector<array<long long,3>>> &adj,
             vector<long long> &toRoot,
             vector<long long> &invRoot,
             vector<int> &vis) {
        vis[u] = 1;
        for (auto &e : adj[u]) {
            int v = e[0];
            if (vis[v]) continue;
            long long fac = e[1], invFac = e[2];
            toRoot[v]   = toRoot[u]   * fac  % MOD;
            invRoot[v]  = invRoot[u]  * invFac % MOD;
            dfs(v, adj, toRoot, invRoot, vis);
        }
    }

    long long modpow(long long a, long long e) {
        long long r = 1;
        while (e) {
            if (e & 1) r = r * a % MOD;
            a = a * a % MOD;
            e >>= 1;
        }
        return r;
    }
};
```

All three solutions are **`O(n + q)`** and fit comfortably within the constraints (`n, q ≤ 10^5`).

---

## 5. Blog Article – “Unit Conversion II: The Good, The Bad, and The Ugly”

### 5.1 Introduction

Unit conversion is a classic interview problem that hides a subtle algebraic twist. **LeetCode 3535 – Unit Conversion II** asks you to convert between arbitrary units when only a tree of pairwise conversion factors is given. In a job‑hunting context, mastering this problem demonstrates your grasp of graph traversal, modular arithmetic, and optimisation under tight time limits.

**Keywords**: *Unit Conversion II, LeetCode, DFS, BFS, job interview, modular inverse, Java, Python, C++*

### 5.2 The Good – Why the DFS/BFS Solution is Elegant

| Strength | Explanation |
|----------|-------------|
| **Linear time** | `O(n + q)` – one traversal to pre‑compute all ratios. |
| **Space efficient** | Only adjacency list (`O(n)`) and two `long` arrays. |
| **Single pass** | After DFS, every query is answered in constant time. |
| **Modular-friendly** | Store edge factor and its inverse; avoids floating point errors. |
| **Language‑agnostic** | The same algorithm works in Java, Python, or C++ with minimal changes. |

In an interview, you can present this as “build a map of conversion factors from root to each node, then compute `root→Bi * inv(root→Ai)`”. The interviewer appreciates the idea of rooting the tree and propagating cumulative products.

### 5.3 The Bad – Common Pitfalls

| Pitfall | How to avoid |
|---------|--------------|
| **Missing modular inverses** | Remember `pow(c, MOD-2)` in a prime field; using `c` alone gives wrong results when reversing edges. |
| **Stack overflow (Java)** | Recursive DFS depth may hit `10^5`; switch to iterative stack or increase recursion limit. |
| **Large intermediate values** | Multiply before modulo; use `long`/`int64` to stay within 64‑bit. |
| **Repeated exponentiation** | Compute inverse only once per edge; pre‑computing all inverses via `pow` can cost `n log MOD`. |
| **Integer overflow in Python** | Use `pow(c, MOD-2, MOD)` to keep the result in the modulus. |

These pitfalls are typical of “gotchas” interviewers love: they test whether you can spot hidden errors.

### 5.4 The Ugly – What Makes This Problem Tricky

| Challenge | Why it's Ugly |
|-----------|---------------|
| **Implicit reversals** | Converting from `Bi` to `Ai` requires inverse factors; if you ignore this you’ll get division by zero or float errors. |
| **Modular division** | Division isn’t straightforward in modulo arithmetic; you must remember Fermat’s little theorem or pre‑compute inverses. |
| **Large graph size** | A naive DFS for each query would be `O(q * n)` – infeasible. |
| **Precision issues** | Using doubles can lead to rounding errors; modulo guarantees exactness but you must implement it correctly. |

In the interview, a candidate might first write a naive approach: for every query perform a DFS on the fly. That would time out on large inputs and show a lack of foresight. The *ugly* part is learning to recognise that **once you root the tree, the rest of the work is a simple multiplication and inversion**.

### 5.5 How to Talk Through the Problem in an Interview

1. **Clarify the model**  
   “We’re given a tree of pairwise conversion factors; going forward multiplies by `c`, going backward multiplies by `1/c`.”

2. **Explain modular arithmetic**  
   “Since the answer must be modulo `10^9+7`, we replace division by multiplication with modular inverses (`pow(c, MOD-2)`), because the modulus is prime.”

3. **Outline the preprocessing**  
   “Do a DFS/BFS from node 0. For each node, keep `factorRoot[u]` (product from root to `u`) and `invRoot[u]` (product of inverses).”

4. **Show query formula**  
   “Any query `Ai → Bi` = `factorRoot[Bi] * invRoot[Ai] (mod MOD)`. So we only need the two pre‑computed arrays.”

5. **Edge cases**  
   * Verify that all nodes are visited; handle 1‑based vs 0‑based indices.
   * For each edge compute inverse only once.

6. **Time‑budget**  
   “The DFS is linear; each query is `O(1)`. We can easily solve 10^5 queries in under 100 ms in Java/Python/C++ on LeetCode’s environment.”

### 5.6 Take‑away for Job‑Hunters

*Unit Conversion II* is a small problem that touches on many important concepts. By implementing a single DFS and answering queries in constant time, you show:
* **Algorithmic depth** – turning a multiplicative chain into a simple root‑based multiplication.
* **Language proficiency** – code works identically in Java, Python, and C++.
* **Time‑management** – your solution runs within 1 s for the worst case (`10^5` nodes, `10^5` queries).

When explaining your solution in an interview, walk through the intuition first, then detail the DFS traversal, modular inverse pre‑computation, and finally the query resolution. Highlight that this is the *canonical* solution – no clever DP tricks or heavy libraries needed.

---

## 6. Final Thoughts

The **Unit Conversion II** problem is a gold‑mine for candidates preparing for technical interviews. It blends a simple graph traversal with modular arithmetic, demanding careful handling of inverses. The DFS solution above is straightforward, efficient, and can be implemented in any of the major languages. Master it, and you’ll impress interviewers with both correctness and clean, performant code. Good luck on your job hunt!