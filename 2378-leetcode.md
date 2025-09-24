---
title: LeetCode 2378. Choose Edges to Maximize Score in a Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ LeetCode 2378 – Choose Edges to Maximize Score in a Tree  
**Java | Python | C++** – Full solutions + **SEO‑optimized blog post** that explains the “good, the bad, and the ugly” of this problem so you can ace your next coding interview.

---

## 1️⃣ Problem Recap

You’re given a **rooted tree** with `n` nodes (0 … n‑1).  
`edges[i] = [parent_i , weight_i]` (root has `[-1,-1]`).  

Choose a subset of edges such that **no two chosen edges share a node** (i.e., they are not adjacent).  
Maximize the sum of the weights of the chosen edges.  
If you choose nothing, the score is `0`.

> **Examples**  
> 1. `edges = [[-1,-1],[0,5],[0,10],[2,6],[2,4]] → 11`  
> 2. `edges = [[-1,-1],[0,5],[0,-6],[0,7]] → 7`  

---

## 2️⃣ Why It’s a Medium Problem

* **Tree structure** – you can’t use greedy on arbitrary graphs; you need a DP that respects the parent‑child hierarchy.  
* **Adjacency constraint** – choosing an edge forbids all its incident edges, so you need to remember *whether* you’re allowed to pick an edge to a child.  
* **Large n (10⁵)** – O(n²) solutions blow up; you need linear time.

---

## 3️⃣ The Core Insight – Tree DP

Think of each node as a “decision point” that can be in **two states**:

| State | Meaning | What is allowed? |
|-------|---------|------------------|
| **0 – “can take”** | The edge from the node’s parent to this node **has NOT** been chosen. | You *may* pick one edge from this node to a child. |
| **1 – “cannot take”** | The edge from the node’s parent to this node **has** been chosen. | You *cannot* pick any edge from this node to a child (the parent already used the node). |

For a node `u`:

```
skip  = sum( child[1] )          // we skip all edges from u to its children
take  = max over children ( child[0] - child[1] + weight(u,child) )
return [skip, take + skip]
```

*`skip`* is the best score when we do **not** take any edge from `u`.  
*`take`* is the best *additional* score if we decide to take *exactly one* edge to a child (hence we compare all children).  
Finally we return `[skip, take+skip]` because when the parent sees this node it will be in the “can take” state (`skip`) and the “cannot take” state is `take+skip`.

The answer for the whole tree is `max(root[0], root[1])`.

The algorithm is **O(n)** time, **O(n)** memory (adjacency list + recursion stack).

---

## 4️⃣ Code Walk‑through – Three Languages

> **Tip**: The same idea works in every language. The only differences are syntax and the way we store the graph.

---

### 4.1 Java (Java 17+)

```java
import java.util.*;

class Solution {
    public long maxScore(int[][] edges) {
        int n = edges.length;
        List<int[]>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();

        int root = 0;
        for (int i = 0; i < n; i++) {
            int p = edges[i][0];
            if (p >= 0) {
                g[p].add(new int[]{i, edges[i][1]}); // child, weight
            } else {
                root = i;
            }
        }

        long[] res = dfs(root, g);               // [canTake, cannotTake]
        return Math.max(res[0], res[1]);         // best possible score
    }

    private long[] dfs(int u, List<int[]>[] g) {
        long skip = 0;      // best if we don't pick an edge from u
        long take = 0;      // best *additional* score when we pick one edge

        for (int[] e : g[u]) {
            long[] child = dfs(e[0], g);
            skip += child[1];                     // child must be in "cannot take"
            take = Math.max(take, child[0] - child[1] + e[1]); // pick this edge
        }
        return new long[]{skip, take + skip};     // return both states
    }
}
```

---

### 4.2 Python (Python 3.10+)

```python
import sys
from collections import defaultdict
from typing import List

class Solution:
    def maxScore(self, edges: List[List[int]]) -> int:
        sys.setrecursionlimit(1 << 25)

        g = defaultdict(list)
        root = 0
        for i, (p, w) in enumerate(edges):
            if p >= 0:
                g[p].append((i, w))
            else:
                root = i

        def dfs(u: int):
            skip, take = 0, 0
            for v, w in g[u]:
                child_can, child_cannot = dfs(v)
                skip += child_cannot
                take = max(take, child_can - child_cannot + w)
            return skip, take + skip

        can, cannot = dfs(root)
        return max(can, cannot)
```

> **Note**: `sys.setrecursionlimit` is needed because `n` can reach 10⁵.

---

### 4.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxScore(vector<vector<int>>& edges) {
        int n = edges.size();
        vector<vector<pair<int,int>>> g(n);   // child, weight
        int root = 0;
        for (int i = 0; i < n; ++i) {
            int p = edges[i][0];
            if (p >= 0) g[p].push_back({i, edges[i][1]});
            else root = i;
        }

        auto dfs = [&](auto&& self, int u) -> pair<long long,long long> {
            long long skip = 0, take = 0;
            for (auto [v,w] : g[u]) {
                auto [child_can, child_cannot] = self(self, v);
                skip += child_cannot;
                take = max(take, child_can - child_cannot + w);
            }
            return {skip, take + skip};
        };

        auto [can, cannot] = dfs(dfs, root);
        return max(can, cannot);
    }
};
```

---

## 5️⃣ Complexity Analysis

| **Metric** | **Time** | **Space** |
|------------|----------|-----------|
| Building adjacency | `O(n)` | `O(n)` |
| DFS (one pass) | `O(n)` | `O(n)` (recursion stack + adjacency) |

Both are optimal for the constraints (`n ≤ 10⁵`).

---

## 6️⃣ Edge‑Case Mastery

| **Case** | **Pitfall** | **Fix** |
|----------|-------------|---------|
| **All negative weights** | Returning negative numbers can be wrong if you don’t remember “0 is always allowed” | The DP automatically takes `max(0, …)` via `skip = 0`. |
| **Root depth > 10⁵** | Recursion stack overflow | Increase recursion limit (Python) or use an explicit stack. |
| **Very dense tree (many children)** | The loop over children stays linear because each child is visited exactly once. | No extra cost. |
| **Large weights (up to 10⁹)** | Use `long long` / `long` | Same as above. |

---

## 6️⃣ Common Interview Pitfalls

1. **Mis‑interpreting “adjacent”** – People often think only *two* edges cannot be chosen, but in a tree *every* edge that touches a chosen one is forbidden.  
2. **Forgetting the root** – If you build the graph with wrong directions, you’ll end up picking an edge that’s already taken by its parent.  
3. **O(n²) greedy** – Trying to sort edges by weight and pick greedily fails on a star graph.  
4. **Stack overflow** – Many interviewers run the code with 10⁵ nodes; a naive recursion in Java/Python may hit the stack limit.

---

## 6️⃣ “The Good, The Bad, and The Ugly”

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Algorithmic efficiency** | Linear DP on a tree – fast and clean | Recursion depth can be high | No memoization → exponential blow‑up |
| **Readability** | Two‑state DP is concise | The pair `[skip, take]` can feel opaque at first | Confusing variable names or missing the “cannot take” state |
| **Scalability** | Works for n=10⁵ | Requires increased recursion limit | Using adjacency lists wrongly (e.g., `HashMap<Integer, Integer>`) may waste memory |
| **Robustness** | Handles negative weights automatically | Needs careful handling of 0‑score case | Forgetting that “skip” is already the best for “cannot take” can lead to wrong answer |

---

## 6️⃣ Tips for Interview Success

1. **Start with a diagram** – Draw the tree, label the root, and annotate a few nodes with the two DP states.  
2. **Explain the two states up front** – Interviewers love seeing you break the problem into clear sub‑problems.  
3. **Show the transition formula** – Write the pseudo‑code of the DFS, then translate it.  
4. **Run through a small example** – Step through the DP on a 5‑node tree to prove you understand the mechanics.  
5. **Mention the recursion limit** (Python) or “tail‑recursion / iterative” options for very deep trees.  
6. **Discuss edge cases** – All‑negative, single node, star shape, balanced vs. skewed tree.  

---

## 7️⃣ Quick‑Reference Cheat‑Sheet

```text
DP[u] = [skip, take+skip]
skip = sum(child[1])                // skip all children
take = max(child[0] - child[1] + w) // pick one child edge
answer = max(DP[root][0], DP[root][1])
```

- **skip** = “node not used by parent”  
- **take** = “node already used by parent”  
- Only *one* child can be chosen because picking an edge blocks all others.

---

## 8️⃣ Final Thought

This problem is a textbook example of **tree DP with adjacency constraints**.  
If you can articulate the two‑state DP and translate it into code, you’ll not only solve the problem in O(n) time, you’ll also impress interviewers with your *clean*, *correct*, and *well‑commented* solution.

Good luck! 🚀  

---