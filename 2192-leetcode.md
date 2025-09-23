---
title: LeetCode 2192. All Ancestors of a Node in a Directed Acyclic Graph - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  All Ancestors of a Node in a Directed Acyclic Graph  
**LeetCode 2192 – Medium**

> **Problem:**  
> Given a DAG with `n` nodes (0‑based) and an edge list `edges`, return a list where  
> `answer[i]` contains *all* ancestors of node `i` in ascending order.  
> A node `u` is an ancestor of `v` if there is a directed path from `u` to `v`.

---

### 1.1  Why this problem matters for interviews  

-  *Graph fundamentals* – topological sort, DFS, BFS  
-  *Dynamic programming on DAGs* – merging ancestor sets  
-  *Time‑space trade‑offs* – `O(n^2)` vs. `O(n·m)`  
-  Demonstrates **clean coding**: adjacency lists, sorting, modular helper functions  

> **SEO keyword focus**: *LeetCode 2192 solution*, *DAG ancestor problem*, *graph algorithm interview*, *Java Python C++ example*, *topological sort implementation*  

---

## 2.  Intuition & Core Idea  

The graph is a DAG, so we can process nodes in **topological order**.  
When we visit a node `v`, we already know the ancestor sets of all of its predecessors.  
The ancestor set of `v` is the union of:

1. Every predecessor’s ancestor set  
2. The predecessor itself

We store the ancestor set of each node as a `bitset` (or a `Set<int>`) to allow fast union and to keep the data small.  
Finally, we convert each bitset into a sorted list.

---

## 3.  Algorithm (Topological‑DP)

```text
1. Build adjacency list `adj[v] = list of children`.
2. Compute indegree of each node.
3. Kahn’s algorithm → array `topo` with a topological order.
4. For each node `v` in `topo`:
      for every child `c` of `v`:
          ancestors[c]  ←  ancestors[c] ∪ ancestors[v]
          ancestors[c]  ←  ancestors[c] ∪ {v}
5. For each node `v`: convert `ancestors[v]` into a sorted vector.
```

### Complexity  

| Step | Time | Space |
|------|------|-------|
| Build graph | `O(n + m)` | `O(n + m)` |
| Topological sort | `O(n + m)` | `O(n)` |
| Ancestor merging | `O(n²)` (worst‑case `n` ancestors per node) | `O(n²)` |
| Final conversion | `O(n² log n)` (sorting each list) | `O(n²)` |

With the given limits (`n ≤ 1000`, `m ≤ 2000`) this easily runs in < 200 ms.

---

## 4.  Reference Implementations  

Below are clean, commented, and ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

---

### 4.1  Java

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> getAncestors(int n, int[][] edges) {
        // 1. Build adjacency list
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        int[] indeg = new int[n];
        for (int[] e : edges) {
            adj.get(e[0]).add(e[1]);
            indeg[e[1]]++;
        }

        // 2. Topological order via Kahn
        Queue<Integer> q = new ArrayDeque<>();
        for (int i = 0; i < n; i++) if (indeg[i] == 0) q.offer(i);
        List<Integer> topo = new ArrayList<>(n);
        while (!q.isEmpty()) {
            int v = q.poll();
            topo.add(v);
            for (int nb : adj.get(v))
                if (--indeg[nb] == 0) q.offer(nb);
        }

        // 3. Ancestor sets
        @SuppressWarnings("unchecked")
        BitSet[] anc = new BitSet[n];
        for (int i = 0; i < n; i++) anc[i] = new BitSet(n);

        // 4. DP over topo order
        for (int v : topo) {
            for (int nb : adj.get(v)) {
                anc[nb].or(anc[v]);   // inherit all ancestors
                anc[nb].set(v);       // add the direct ancestor
            }
        }

        // 5. Convert to List<List<Integer>>
        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            List<Integer> tmp = new ArrayList<>();
            for (int j = anc[i].nextSetBit(0); j >= 0; j = anc[i].nextSetBit(j + 1))
                tmp.add(j);
            res.add(tmp);          // already sorted because of bit order
        }
        return res;
    }
}
```

---

### 4.2  Python

```python
from collections import deque, defaultdict
from typing import List

class Solution:
    def getAncestors(self, n: int, edges: List[List[int]]) -> List[List[int]]:
        # 1. Adjacency + indegree
        adj = [[] for _ in range(n)]
        indeg = [0] * n
        for u, v in edges:
            adj[u].append(v)
            indeg[v] += 1

        # 2. Topo order
        q = deque([i for i in range(n) if indeg[i] == 0])
        topo = []
        while q:
            v = q.popleft()
            topo.append(v)
            for nb in adj[v]:
                indeg[nb] -= 1
                if indeg[nb] == 0:
                    q.append(nb)

        # 3. Ancestor sets – use Python set for clarity
        anc = [set() for _ in range(n)]

        # 4. DP
        for v in topo:
            for nb in adj[v]:
                anc[nb].update(anc[v])  # inherit
                anc[nb].add(v)          # direct ancestor

        # 5. Sort each list
        return [sorted(list(s)) for s in anc]
```

---

### 4.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> getAncestors(int n, vector<vector<int>>& edges) {
        // 1. Graph + indegree
        vector<vector<int>> adj(n);
        vector<int> indeg(n, 0);
        for (auto &e : edges) {
            adj[e[0]].push_back(e[1]);
            indeg[e[1]]++;
        }

        // 2. Kahn
        queue<int> q;
        for (int i = 0; i < n; ++i) if (indeg[i] == 0) q.push(i);
        vector<int> topo;
        while (!q.empty()) {
            int v = q.front(); q.pop();
            topo.push_back(v);
            for (int nb : adj[v]) {
                if (--indeg[nb] == 0) q.push(nb);
            }
        }

        // 3. Ancestor bitsets
        vector<bitset<1000>> anc(n); // 1000 is the max n

        // 4. DP
        for (int v : topo) {
            for (int nb : adj[v]) {
                anc[nb] |= anc[v];
                anc[nb].set(v);
            }
        }

        // 5. Convert to sorted vectors
        vector<vector<int>> ans(n);
        for (int i = 0; i < n; ++i) {
            for (int j = anc[i]._Find_first(); j < n; j = anc[i]._Find_next(j))
                ans[i].push_back(j);
        }
        return ans;
    }
};
```

> **Note:**  
> *C++* uses a compile‑time constant `1000` for the bitset size. If you prefer dynamic sizing, switch to `vector<unordered_set<int>>` or `vector<set<int>>`, but be aware of higher overhead.

---

## 5.  Alternate Brute‑Force DFS (O(n·m))  

If you prefer a straight‑forward DFS that follows the original “reach all descendants” idea, here is a compact version that still fits the limits.

### 5.1  Java (DFS version)

```java
public class Solution {
    public List<List<Integer>> getAncestors(int n, int[][] edges) {
        List<List<Integer>> adj = new ArrayList<>();
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        for (int[] e : edges) adj.get(e[0]).add(e[1]);

        List<List<Integer>> res = new ArrayList<>();
        for (int i = 0; i < n; i++) res.add(new ArrayList<>());

        for (int src = 0; src < n; src++) {
            boolean[] vis = new boolean[n];
            dfs(src, src, adj, res, vis);
        }

        for (List<Integer> list : res) Collections.sort(list);
        return res;
    }

    private void dfs(int src, int cur, List<List<Integer>> adj,
                     List<List<Integer>> res, boolean[] vis) {
        vis[cur] = true;
        for (int nxt : adj.get(cur)) {
            if (!vis[nxt]) {
                res.get(nxt).add(src);          // src is an ancestor of nxt
                dfs(src, nxt, adj, res, vis);
            }
        }
    }
}
```

### 5.2  Python

```python
class Solution:
    def getAncestors(self, n: int, edges: List[List[int]]) -> List[List[int]]:
        adj = [[] for _ in range(n)]
        for u, v in edges:
            adj[u].append(v)

        res = [[] for _ in range(n)]

        def dfs(src, cur, visited):
            visited[cur] = True
            for nb in adj[cur]:
                if not visited[nb]:
                    res[nb].append(src)
                    dfs(src, nb, visited)

        for src in range(n):
            dfs(src, src, [False] * n)

        return [sorted(lst) for lst in res]
```

### 5.3  C++

```cpp
vector<vector<int>> getAncestors(int n, vector<vector<int>>& edges) {
    vector<vector<int>> adj(n);
    for (auto &e : edges) adj[e[0]].push_back(e[1]);

    vector<vector<int>> ans(n);
    for (int src = 0; src < n; ++src) {
        vector<bool> vis(n, false);
        function<void(int)> dfs = [&](int cur) {
            vis[cur] = true;
            for (int nb : adj[cur]) {
                if (!vis[nb]) {
                    ans[nb].push_back(src);
                    dfs(nb);
                }
            }
        };
        dfs(src);
    }
    for (auto &vec : ans) sort(vec.begin(), vec.end());
    return ans;
}
```

> **Performance** – These DFS‑based solutions are `O(n²)` in the worst case, but they are often faster in practice when the graph is sparse (`m` small). They are great to show *simple logic* and *recursion* to interviewers.

---

## 6.  Good, Bad & Ugly – What Interviewers Care About  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Use of adjacency lists** | Keeps memory low and operations `O(1)` per edge | ❌ Using adjacency matrix would blow up memory | ❌ Mixing 0‑based and 1‑based indices will trip you up |
| **Topological sorting** | Guarantees predecessor data is ready | ❌ Forgetting indegree → infinite loop | ❌ Recursion depth > 1000 → stack overflow |
| **Bitset / Set merging** | Fast union, low overhead | ❌ Using `vector<int>` for merging → `O(n³)` | ❌ Manual memory management in C++ (missing `std::unique`) |
| **Sorting result** | Needed for the statement | ❌ Omitting `.sort()` → wrong answer | ❌ Sorting inside loops → quadratic overhead |

> **Takeaway:**  
> The DP‑on‑DAG solution (topological + bitset) is *the cleanest* and *fastest* for the constraints.  
> The DFS‑from‑each‑node version is simpler to understand but can be slower on dense DAGs.  
> **Always remember**: in interview coding, a correct *simple* solution often beats a clever but buggy one.

---

## 7.  “What if…” – Edge Cases & Extensions  

| Edge Case | Handling |
|-----------|----------|
| **No edges** (`m == 0`) | Topo order is `[0,1,…,n‑1]`; every ancestor list stays empty. |
| **Chain graph** (`0→1→2→…`) | Each node accumulates `O(n)` ancestors; bitset handles union in `O(n)` each. |
| **Large ancestor sets** | `BitSet` ensures we only store a single integer per ancestor; union is a single machine word operation per 64 ancestors. |
| **Multiple test cases** | Wrap solution in a loop that reads `n` & `edges` per case. |

---

## 8.  How to Use These Solutions for *Job Interview* Prep  

1. **Read the problem carefully** – Understand the definition of an ancestor.  
2. **Draw a topological order** – Write it down on paper; this will help you verify the DP step.  
3. **Write code in your preferred language** – Use the snippets above as a template.  
4. **Run local tests** – Use the sample chain graph & no‑edge cases.  
5. **Explain your logic** – In an interview, walk through the algorithm, not just the code.  
5. **Highlight the efficiency** – Show how using a bitset or adjacency lists saves time & space.

> 👉 *Practice:* Try implementing a variation where you also need to output the *longest path* that uses only ancestor edges.  
> 👉 *Mock interview:* Pair‑program with a friend and explain each step in plain English.

---

## 9.  Further Reading & Resources  

| Resource | Why It Helps |
|----------|--------------|
| [LeetCode 1736 – 1736. Find in Mountain Array](https://leetcode.com/problemset/problem/1736) | Practice binary search + DP on arrays. |
| [GFG – “Topological Sort”](https://www.geeksforgeeks.org/topological-sorting/) | Classic explanation of Kahn’s algorithm. |
| [cppreference – `std::bitset`](https://en.cppreference.com/w/cpp/utility/bitset) | Deep dive into bitset operations. |
| [Stack Overflow – “Memory efficient graph representation”](https://stackoverflow.com/questions/1045924/graph-representation-in-c) | Discusses adjacency lists vs matrices. |
| [YouTube – “Algorithms: DP on DAGs”](https://www.youtube.com/watch?v=YzZgZt9qGvA) | Visual walkthrough of DP on DAGs. |

---

## 9.  TL;DR – One Sentence Summary  

*The fastest, memory‑efficient solution for LeetCode 1736 is to perform a topological sort, use a bitset/`std::bitset` to merge ancestor sets in linear time, and then output sorted lists; the DFS‑from‑each‑node variant is simpler but may be slower on dense graphs.*

---

### 10.  🎉 Final Words  

*You’re now equipped with both the *optimal* and *simple* ways to solve LeetCode 1736.  
Remember: the key to acing coding interviews is a **clear, correct algorithm** and *explain‑your‑thoughts* that demonstrate you truly understand the problem domain.*  

Happy coding, and good luck on your next interview! 🚀

---



---

### 11.  FAQ

**Q1: Why not use a union‑find data structure?**  
A1: Union‑find is great for *connected components*, not for directed ancestor relationships. The DP‑on‑DAG approach is the natural fit for directed acyclic graphs.

**Q2: Will the C++ bitset code compile with `-std=c++17`?**  
A2: Yes, but you must define the size at compile time (e.g., `bitset<1000>`). For dynamic `n`, consider `vector<bitset<1000>>` or `vector<unordered_set<int>>`.

**Q3: What if the input uses 1‑based indices?**  
A3: Convert all vertices to 0‑based before building the graph, or adjust the result accordingly (e.g., add 1 to every index in the output).

**Q4: Is there a greedy algorithm?**  
A4: No greedy algorithm will produce the correct ancestor sets because you must consider all paths. The DP approach is the greedy solution in the sense of “take all ancestors that have already been taken.”

---

## 12.  Closing Thought  

> **“In graph algorithms, always think of a topological order. Once you have that, many problems become embarrassingly simple.”**  

Happy coding and enjoy cracking the DAG ancestor puzzle! 🧩💻

---