---
title: LeetCode 2322. Minimum Score After Removals on a Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 2322 – Minimum Score After Removals on a Tree  
**The Good, The Bad & The Ugly – A Deep‑Dive with Java / Python / C++ Solutions**  

> **SEO Keywords**: *LeetCode 2322*, *Minimum Score After Removals on a Tree*, *Tree DP*, *XOR*, *Hard*, *Interview Prep*, *Java*, *Python*, *C++*, *Algorithm Design*, *Tree Partitioning*

---

## 1. Problem Recap

You are given a rooted tree with `n` nodes (`3 ≤ n ≤ 1000`).  
- `nums[i]` – value of node `i`.  
- `edges` – list of `n‑1` undirected edges.

You must delete **two distinct edges**, splitting the tree into **three connected components**.  
For each component compute the XOR of all node values inside it.  
The **score** of that deletion pair is

```
score = max(XOR1, XOR2, XOR3) – min(XOR1, XOR2, XOR3)
```

Return the **minimum possible score** over all pairs of edge deletions.

---

## 2. Why This Problem is Great for Interviews

| The Good | The Bad | The Ugly |
|----------|---------|----------|
| **Moderate constraints** (`n ≤ 1000`) → lets you think about brute force + clever pruning. | **Two deletions** force you to reason about *nested* components – easy to miss cases. | **XOR trick**: subtraction isn’t available; you need to think about set difference in XOR space. |
| **Classic DFS + Euler tour** for subtree XORs – a pattern seen on many sites. | **Depth‑first search** recursion depth (≤1000) is safe in most languages, but stack‑overflow is a risk in Java. | **Edge‑pair loops** (`O(n²)`) might look expensive; you must justify why it’s fine for the constraints. |
| **Clear sub‑problem**: compute subtree XOR once, reuse. | **Ancestor checks** with entry/exit times – a common source of off‑by‑one bugs. | **Multiple passes** over edges can easily lead to O(n³) if you’re not careful. |

---

## 3. High‑Level Strategy

1. **Root the tree** (any node, we choose `0`).  
2. Run a single DFS to compute:
   * `subXor[u]` – XOR of all values in the subtree of `u`.  
   * `tin[u]`, `tout[u]` – entry / exit times to test ancestor relationships in O(1).  
   * `parent[u]` – parent of each node.  
3. For every unordered pair of *child nodes* `(i, j)` (the edge is `parent[i]-i`), evaluate the resulting component XORs:
   * **Disjoint edges** – three independent subtrees:  
     `c1 = subXor[i]`, `c2 = subXor[j]`, `c3 = totalXor ^ subXor[i] ^ subXor[j]`.
   * **Nested edges** (one inside the other):  
     If `j` is inside `i` →  
     `c1 = subXor[j]`,  
     `c2 = subXor[i] ^ subXor[j]` (XOR of subtree `i` **without** `j`),  
     `c3 = totalXor ^ subXor[i]`.  
     (Symmetric if `i` is inside `j`.)
4. Compute `score = max(c1, c2, c3) – min(c1, c2, c3)` and keep the minimum.

Because `n ≤ 1000`, the pair loop (`~5·10⁵` iterations) is trivial.  
The algorithm runs in **O(n²)** time and **O(n)** space.

---

## 4. Edge Cases & Pitfalls

| Pitfall | How to Avoid |
|---------|--------------|
| **Ancestor test errors** – mixing `<=` and `<` incorrectly | Use **Euler tour** (`tin[u] ≤ tin[v] && tout[v] ≤ tout[u]`) for a robust ancestor check. |
| **XOR difference** – forgetting that `a \ b` in XOR is `a ^ b` if `b ⊆ a` | For nested edges, component XOR is `subXor[ancestor] ^ subXor[descendant]`. |
| **Root edge exclusion** – root has no parent | Only consider nodes `u != root` as children. |
| **Large recursion depth** in Java | Use iterative DFS or increase stack size. |

---

## 5. Code Implementations

Below are clean, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

### 5.1 Java (Java 17)

```java
import java.util.*;

public class Solution {
    private List<Integer>[] g;
    private int[] parent, tin, tout, subXor;
    private int timer;
    private int totalXor;

    public int minimumScore(int[] nums, int[][] edges) {
        int n = nums.length;
        g = new ArrayList[n];
        for (int i = 0; i < n; ++i) g[i] = new ArrayList<>();
        for (int[] e : edges) {
            g[e[0]].add(e[1]);
            g[e[1]].add(e[0]);
        }

        parent = new int[n];
        tin = new int[n];
        tout = new int[n];
        subXor = new int[n];
        timer = 0;

        dfs(0, -1, nums);

        totalXor = subXor[0];
        int best = Integer.MAX_VALUE;

        // Collect all child nodes (edges we may delete)
        List<Integer> children = new ArrayList<>();
        for (int i = 1; i < n; ++i) children.add(i);

        // Iterate over unordered pairs
        int m = children.size();
        for (int a = 0; a < m; ++a) {
            int i = children.get(a);
            for (int b = a + 1; b < m; ++b) {
                int j = children.get(b);
                int[] comps = compsXor(i, j);
                int score = Math.max(Math.max(comps[0], comps[1]), comps[2])
                          - Math.min(Math.min(comps[0], comps[1]), comps[2]);
                if (score < best) best = score;
            }
        }
        return best;
    }

    private void dfs(int u, int p, int[] nums) {
        parent[u] = p;
        tin[u] = ++timer;
        int xr = nums[u];
        for (int v : g[u]) if (v != p) {
            dfs(v, u, nums);
            xr ^= subXor[v];
        }
        subXor[u] = xr;
        tout[u] = ++timer;
    }

    // Returns {c1, c2, c3} for deleting edges (parent[i]-i) and (parent[j]-j)
    private int[] compsXor(int i, int j) {
        // Check ancestor relation
        boolean iInsideJ = tin[j] >= tin[i] && tout[j] <= tout[i];
        boolean jInsideI = tin[i] >= tin[j] && tout[i] <= tout[j];

        int c1, c2, c3;
        if (iInsideJ) {                    // j inside i
            c1 = subXor[j];
            c2 = subXor[i] ^ subXor[j];     // i \ j
            c3 = totalXor ^ subXor[i];
        } else if (jInsideI) {             // i inside j
            c1 = subXor[i];
            c2 = subXor[j] ^ subXor[i];     // j \ i
            c3 = totalXor ^ subXor[j];
        } else {                           // disjoint
            c1 = subXor[i];
            c2 = subXor[j];
            c3 = totalXor ^ subXor[i] ^ subXor[j];
        }
        return new int[]{c1, c2, c3};
    }
}
```

**Key Points**  
- One DFS fills `subXor` & Euler tour times.  
- `timer` guarantees `O(1)` ancestor checks.  
- All arithmetic is on `int`; LeetCode guarantees `nums[i]` fits in `int`.

---

### 5.2 Python 3

```python
from typing import List

class Solution:
    def minimumScore(self, nums: List[int], edges: List[List[int]]) -> int:
        n = len(nums)
        g = [[] for _ in range(n)]
        for a, b in edges:
            g[a].append(b)
            g[b].append(a)

        parent = [-1] * n
        tin, tout = [0] * n, [0] * n
        sub = [0] * n
        timer = 0

        def dfs(u, p):
            nonlocal timer
            parent[u] = p
            timer += 1
            tin[u] = timer
            xr = nums[u]
            for v in g[u]:
                if v == p:
                    continue
                dfs(v, u)
                xr ^= sub[v]
            sub[u] = xr
            timer += 1
            tout[u] = timer

        dfs(0, -1)
        total = sub[0]
        best = float('inf')

        # child nodes (edge we may cut)
        children = [i for i in range(1, n)]

        for a in range(len(children)):
            i = children[a]
            for b in range(a + 1, len(children)):
                j = children[b]
                # Ancestor checks
                if tin[i] <= tin[j] <= tout[j] <= tout[i]:   # j inside i
                    c1 = sub[j]
                    c2 = sub[i] ^ sub[j]
                    c3 = total ^ sub[i]
                elif tin[j] <= tin[i] <= tout[i] <= tout[j]:  # i inside j
                    c1 = sub[i]
                    c2 = sub[j] ^ sub[i]
                    c3 = total ^ sub[j]
                else:  # disjoint
                    c1, c2, c3 = sub[i], sub[j], total ^ sub[i] ^ sub[j]

                score = max(c1, c2, c3) - min(c1, c2, c3)
                best = min(best, score)

        return best
```

---

### 5.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    vector<vector<int>> g;
    vector<int> parent, tin, tout, subXor;
    int timer, totalXor;

    void dfs(int u, int p, const vector<int>& nums) {
        parent[u] = p;
        tin[u] = ++timer;
        int xr = nums[u];
        for (int v : g[u]) if (v != p) {
            dfs(v, u, nums);
            xr ^= subXor[v];
        }
        subXor[u] = xr;
        tout[u] = ++timer;
    }

public:
    int minimumScore(vector<int>& nums, vector<vector<int>>& edges) {
        int n = nums.size();
        g.assign(n, {});
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        parent.resize(n);
        tin.resize(n);
        tout.resize(n);
        subXor.resize(n);
        timer = 0;

        dfs(0, -1, nums);
        totalXor = subXor[0];

        int best = INT_MAX;
        vector<int> children;
        for (int i = 1; i < n; ++i) children.push_back(i);

        for (size_t a = 0; a < children.size(); ++a) {
            int i = children[a];
            for (size_t b = a + 1; b < children.size(); ++b) {
                int j = children[b];
                int c1, c2, c3;
                if (tin[i] <= tin[j] && tout[j] <= tout[i]) {           // j inside i
                    c1 = subXor[j];
                    c2 = subXor[i] ^ subXor[j];
                    c3 = totalXor ^ subXor[i];
                } else if (tin[j] <= tin[i] && tout[i] <= tout[j]) {    // i inside j
                    c1 = subXor[i];
                    c2 = subXor[j] ^ subXor[i];
                    c3 = totalXor ^ subXor[j];
                } else {                                                // disjoint
                    c1 = subXor[i];
                    c2 = subXor[j];
                    c3 = totalXor ^ subXor[i] ^ subXor[j];
                }
                int mx = max({c1, c2, c3});
                int mn = min({c1, c2, c3});
                best = min(best, mx - mn);
            }
        }
        return best;
    }
};
```

All three codes share the same core logic – just different idioms.

---

## 6. Why the `O(n²)` Brute‑Force Works

- **Maximum iterations**: `C(n-1, 2) = (n-1)(n-2)/2 ≈ 5·10⁵` for `n = 1000`.  
- Each iteration does only a handful of integer operations → < 1 ms in C++ / Python, < 5 ms in Java.  
- Memory usage is linear: `O(n)` for adjacency list, DFS metadata, and a few integer arrays.

**If you’re worried about performance**, you can prove the following:  
`5·10⁵` loops × ~10 primitive operations ≈ 5 M operations – well below the 1‑second limit on LeetCode.  
No need for bit‑parallel tricks or advanced data structures.

---

## 7. Final Thoughts – What Makes This Problem *Interview‑Ready*

1. **Clear Sub‑Problem** – Compute subtree XOR *once*.  
2. **Euler Tour Ancestor Test** – a reusable pattern for any “subtree vs. subtree” logic.  
3. **Handling Set Difference in XOR Space** – a subtlety that trips many candidates; mastering it demonstrates strong bit‑wise reasoning.  
4. **Complexity Analysis** – show you can reason about `O(n²)` vs. `O(n³)` and justify your design choices.  

If you can explain this solution, walk through the pair loop, and show why the code is correct, you’ll demonstrate mastery of tree traversals, bit manipulation, and algorithmic thinking – exactly the skill set hiring managers look for in a *Hard* LeetCode problem.

---

## 8. Meta Description

> *Learn how to solve LeetCode 2322 – Minimum Score After Removals on a Tree – with step‑by‑step Java, Python, and C++ code. Understand the ancestor‑difference trick, time‑interval checks, and why this “Hard” interview problem is a perfect showcase of tree DP and XOR skills.*

---

Happy coding, and good luck slaying that interview! 🚀