---
title: LeetCode 2858. Minimum Edge Reversals So Every Node Is Reachable - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  Three‑Language Implementation (Java, Python, C++)

Below are clean, production‑ready solutions that run in **O(n)** time and **O(n)** memory for all three languages.

> **Assumptions**  
> * `n` ≤ 10⁵  
> * `edges` is a list of `n‑1` directed edges that, if made undirected, form a tree.  

---

## Java

```java
import java.util.*;

public class Solution {
    // adjacency list: node -> list of (neighbor, cost)
    // cost = 0 if the edge goes in the original direction,
    // cost = 1 if we need to reverse it.
    private List<int[]>[] graph;
    private int[] answer;

    public int[] minEdgeReversals(int n, int[][] edges) {
        buildGraph(n, edges);

        // 1️⃣  Find the cost from an arbitrary root (0) to all nodes.
        boolean[] visited = new boolean[n];
        answer = new int[n];
        answer[0] = dfs(0, visited);

        // 2️⃣  Re‑root the tree – propagate the answer to all nodes.
        Arrays.fill(visited, false);
        reroot(0, visited);

        return answer;
    }

    /* ----------  Helper  ---------- */

    private void buildGraph(int n, int[][] edges) {
        graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();

        for (int[] e : edges) {
            int u = e[0], v = e[1];
            graph[u].add(new int[]{v, 0}); // keep the edge as‑is
            graph[v].add(new int[]{u, 1}); // reverse the edge – costs 1
        }
    }

    /** Sum of costs on the path from `node` to all its descendants. */
    private int dfs(int node, boolean[] vis) {
        vis[node] = true;
        int cost = 0;
        for (int[] nxt : graph[node]) {
            int to = nxt[0], w = nxt[1];
            if (!vis[to]) cost += w + dfs(to, vis);
        }
        return cost;
    }

    /**
     * Propagate answer from parent to child.
     * When we move the root from `node` to `to`:
     *  - if edge was node→to (w=0) → need to reverse: +1
     *  - if edge was to→node (w=1) → no reverse needed: -1
     * The change is 1‑2*w.
     */
    private void reroot(int node, boolean[] vis) {
        vis[node] = true;
        for (int[] nxt : graph[node]) {
            int to = nxt[0], w = nxt[1];
            if (!vis[to]) {
                answer[to] = answer[node] + 1 - 2 * w;
                reroot(to, vis);
            }
        }
    }
}
```

---

## Python

```python
import sys
from collections import defaultdict
sys.setrecursionlimit(1_000_000)

class Solution:
    def minEdgeReversals(self, n: int, edges: list[list[int]]) -> list[int]:
        # Build adjacency list with costs
        g = defaultdict(list)
        for u, v in edges:
            g[u].append((v, 0))   # keep direction
            g[v].append((u, 1))   # reverse direction costs 1

        ans = [0] * n
        visited = [False] * n

        def dfs(u: int) -> int:
            """Return total cost in the subtree rooted at u."""
            visited[u] = True
            total = 0
            for v, w in g[u]:
                if not visited[v]:
                    total += w + dfs(v)
            return total

        ans[0] = dfs(0)

        # Re‑root: propagate answers to all nodes
        visited = [False] * n

        def reroot(u: int):
            visited[u] = True
            for v, w in g[u]:
                if not visited[v]:
                    # change = +1 if edge was u->v, -1 if v->u
                    ans[v] = ans[u] + 1 - 2 * w
                    reroot(v)

        reroot(0)
        return ans
```

*Tip for Python interviewers:*  
If you worry about recursion depth, you can replace the DFS with an explicit stack – the logic stays the same.

---

## C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> minEdgeReversals(int n, vector<vector<int>>& edges) {
        // adjacency list: node -> vector of {neighbor, cost}
        vector<vector<pair<int,int>>> g(n);
        for (auto &e : edges) {
            int u = e[0], v = e[1];
            g[u].push_back({v, 0});   // keep direction
            g[v].push_back({u, 1});   // reverse costs 1
        }

        vector<int> ans(n);
        vector<int> vis(n, 0);

        // 1️⃣ DFS from node 0
        function<int(int)> dfs = [&](int u) -> int {
            vis[u] = 1;
            int cost = 0;
            for (auto [v, w] : g[u]) {
                if (!vis[v]) cost += w + dfs(v);
            }
            return cost;
        };
        ans[0] = dfs(0);

        // 2️⃣ Re‑root – propagate to every node
        fill(vis.begin(), vis.end(), 0);
        function<void(int)> reroot = [&](int u) {
            vis[u] = 1;
            for (auto [v, w] : g[u]) {
                if (!vis[v]) {
                    ans[v] = ans[u] + 1 - 2 * w;   // 1‑2*w is the cost delta
                    reroot(v);
                }
            }
        };
        reroot(0);
        return ans;
    }
};
```

---

# 2.  Blog Article – “The Re‑Rooting Trick Behind LeetCode 2858”

> **SEO Keywords** – *LeetCode 2858, Minimum Edge Reversals, Tree DP, Re‑Rooting, Graph Traversal, O(n) solution, Java, Python, C++, interview algorithm, job interview, coding interview, algorithm design*

---

## Minimum Edge Reversals so Every Node Is Reachable  
### The Re‑Rooting Magic That Turns a Hard Problem into O(n)

When you first read the problem statement, you might think you’re dealing with a classic “make every node reachable from any other” graph problem. But the input guarantees that the underlying undirected structure is a **tree**. That single property unlocks a powerful dynamic‑programming technique: **re‑rooting**.

Below I’ll walk you through why re‑rooting works, how to implement it in Java, Python and C++, and what pitfalls interviewers will look for. By the end, you’ll have a story you can tell in your next technical interview.

---

### 1.  Problem Recap

Given `n` nodes and `n‑1` directed edges that, when treated as undirected, form a tree, we want for **every** node `s`:

> *the minimum number of edges that must be reversed so that, starting from `s`, we can reach every other node.*

The naive solution runs a BFS/DFS from each node and checks all edges, giving **O(n²)** time – too slow for `n = 10⁵`.

---

### 2.  The Core Insight – Edge Cost = Reversal Needed

Think of each directed edge `(u → v)` as **two** undirected edges:

| Direction | Edge in the original graph | Reversal cost |
|-----------|----------------------------|---------------|
| `u → v`   | keep it                     | `0`           |
| `v → u`   | reverse it                  | `1`           |

With this view, the problem reduces to **sum of costs** on all paths from the chosen root to the rest of the tree.

---

### 3.  First DFS – “Rooted” Cost

Pick an arbitrary root (node `0`). Do a DFS:

```
cost(u) = Σ (edge‑cost + cost(child))
```

The recursion returns the **total cost** to reach all nodes in `u`'s subtree while treating `u` as the starting point. This is the first pass of the algorithm.

*Why it works:*  
All edges with cost `0` are already directed away from the root, so we never need to reverse them. For every edge that must be reversed (cost `1`), we simply add `1` to the total.

---

### 4.  Second DFS – “Re‑Rooting”

Now we have the answer for node `0`. We need the answer for **every** node without re‑running the whole DFS.

When we move the root from node `x` to an adjacent node `y`, only **one** edge’s direction relative to the new root changes.  

* If the original edge was `x → y` (cost `0`), the new root must reverse it: **+1** to the answer.  
* If the original edge was `y → x` (cost `1`), we no longer need to reverse it: **–1**.

Hence the update rule is:

```
answer[y] = answer[x] + 1 – 2 * cost(x→y)
```

`cost(x→y)` is `0` for a forward edge, `1` for a reversed edge. The formula `1‑2*cost` yields `+1` for a forward edge and `‑1` for a backward edge.

This second DFS runs in **O(n)** because each edge is visited twice (once per direction).

---

### 5.  Complexity Analysis

| Step | Time | Memory |
|------|------|--------|
| Build graph (with costs) | **O(n)** | **O(n)** |
| DFS from root | **O(n)** | **O(n)** (call stack) |
| Re‑root DFS | **O(n)** | **O(n)** |
| **Total** | **O(n)** | **O(n)** |

The algorithm is linear in the number of nodes – the best possible because we have to touch every node at least once.

---

### 6.  Common Pitfalls (Bad & Ugly)

| Pitfall | What it is | How to avoid it |
|---------|------------|-----------------|
| **Naïve O(n²)** | Running a fresh DFS/BFS from every node. | Use re‑rooting; don't forget to mark visited nodes. |
| **Recursion depth** | Python’s default recursion limit (`1000`) fails for deep trees. | `sys.setrecursionlimit(1_000_000)` or use an explicit stack. |
| **Off‑by‑one in cost update** | Mixing up `+1` vs `‑1` when re‑rooting. | Derive the formula `answer[child] = answer[parent] + 1 - 2*cost` and double‑check with small examples. |
| **Memory blow‑up** | Using an adjacency matrix or storing unnecessary copies. | Stick to adjacency lists (`vector<vector<>>`, `list`, `defaultdict`). |

---

### 7.  Why Interviewers Love This Approach

* **Elegance** – a single DFS to compute the “root‑0” answer, followed by a very simple re‑rooting pass.  
* **Speed** – linear time for 10⁵ nodes is a strong selling point.  
* **Portability** – works out‑of‑the‑box in Java, Python, and C++ (all three code snippets above).  
* **Theoretical Depth** – the method is a classic **tree DP disguised as graph traversal**; interviewers appreciate seeing that you can spot dynamic‑programming opportunities in graph problems.

---

## 3.  Summary – What the Interviewer Should Hear

> “I approached LeetCode 2858 by turning each directed edge into a weighted undirected edge, performed a DFS to compute the cost from an arbitrary root, and then propagated those costs to every node using a re‑rooting formula `ans[child] = ans[parent] + 1 - 2*cost`. The solution runs in O(n) time and O(n) space, and is stable across Java, Python, and C++.”

Feel free to copy the code blocks into your favorite IDE, run them against the provided examples, and add them to your portfolio.

---  

# 2.  SEO‑Optimized Blog Post

> **Title**  
> **Minimum Edge Reversals for Every Node Is Reachable (LeetCode 2858) – A Re‑Rooting Solution That Works in O(n)**  

> **Meta Description**  
> Learn the O(n) tree‑DP solution for LeetCode 2858. Master re‑rooting, avoid O(n²) pitfalls, and see Java, Python, C++ implementations for interview success.  

> **Keywords** – LeetCode 2858, Minimum Edge Reversals, tree DP, re‑rooting, graph traversal, O(n) solution, interview algorithm, Java, Python, C++, coding interview, job interview, algorithm design, dynamic programming on trees.  

---

## 📖 Introduction

When the LeetCode crowd first stumbled upon **Problem 2858 – “Minimum Edge Reversals so Every Node Is Reachable,”** many attempted brute‑force solutions that quickly hit the `O(n²)` wall. But because the underlying graph is a *tree* (once all edges are undirected), there is a far more elegant linear‑time technique: **re‑rooting**.

This article walks through the intuition, presents a step‑by‑step derivation, shows why the naïve approach fails, and finally gives clean, interview‑ready code in **Java, Python, and C++**.

> **Why is this useful?**  
> - You’ll ace LeetCode 2858 and similar “tree DP” questions.  
> - You’ll demonstrate mastery of graph transformation and dynamic programming on trees.  
> - You’ll have a story to tell interviewers that shows you can spot hidden structure and design efficient algorithms.  

---

## 🎯 Problem Recap

> **Input** – `n` nodes, `n-1` directed edges that would form a tree if undirected.  
> **Goal** – For every node `s`, compute the minimal number of edges that must be reversed so that you can start at `s` and visit every other node.

> **Constraints**  
> - `1 ≤ n ≤ 100,000`  
> - Time limit is tight; `O(n²)` is a no‑go.

---

## 🚀 The Core Insight

### Edge ⇢ Two Undirected Edges with a Cost

Every directed edge `(u → v)` can be represented as two *undirected* edges:

| Undirected direction | In the original graph | Reversal needed? |
|----------------------|-----------------------|------------------|
| `u → v`              | keep it                | `0`              |
| `v → u`              | reverse it             | `1`              |

Now, **reversal cost** is just a weight.  
The task becomes: *“From a chosen root, sum up the weights on all paths to reach the rest of the tree.”*  

That’s the hallmark of a dynamic‑programming trick we’ll exploit.

---

## 🧠 Step‑by‑Step Derivation

### 1️⃣ First Pass – Rooted DP (DFS from an Arbitrary Root)

1. **Pick a root** (any node, conventionally node 0).  
2. **DFS** to compute:
   ```
   cost(u) = Σ (edge‑cost + cost(child))
   ```
   - If an edge already points away from the root (cost 0), no reversal needed.  
   - If the edge must be reversed (cost 1), add 1.  

> **Result** – `cost(0)` is the answer for node 0.  

---

### 2️⃣ Second Pass – Re‑Rooting

The magic of re‑rooting is that **only one edge’s status changes** when moving the root to an adjacent node.  

Consider moving from node `x` to its neighbor `y`:

| Original direction | New root’s view | Δ in answer |
|--------------------|-----------------|-------------|
| `x → y` (cost 0)   | must reverse   | `+1` |
| `y → x` (cost 1)   | no longer reversed | `‑1` |

So:

```
answer[y] = answer[x] + 1 – 2 * cost(x→y)
```

`cost(x→y)` is simply `0` or `1`. The expression `1‑2*cost` cleanly encodes the `+1` / `‑1` logic.

> **Why two passes?**  
> - The first pass gives a global baseline.  
> - The second pass propagates that baseline to all nodes, each edge being examined twice (once per direction).  

---

## 💡 Why the Naïve O(n²) Fails

The straightforward approach would:

1. For each node `s`, run a DFS/BFS.  
2. Count how many edges need reversing on each path.  

With `n = 10⁵`, this becomes **billions of operations**. Interviewers will ask you to justify your complexity, and a naïve solution will raise a red flag.

---

## 🛠️ Implementation – One Algorithm, Three Languages

Below you’ll find a *single* algorithm that I coded in:

| Language | Highlights |
|----------|------------|
| **Java** | Uses `ArrayList<ArrayList<Pair>>` and `int[] visited`. |
| **Python** | Uses `defaultdict(list)` and a recursion limit bump (`sys.setrecursionlimit`). |
| **C++** | Leverages `vector<vector<pair<int,int>>>` and lambda‑based recursion. |

All three use the same graph transformation and re‑rooting formula. Pick the one that matches your interview stack.

---

### Java Snippet (Key Lines)

```java
// cost from root 0
int dfsRoot(int u) {
    visited[u] = true;
    int cost = 0;
    for (Pair e : g[u]) {
        if (!visited[e.v]) cost += e.w + dfsRoot(e.v);
    }
    return cost;
}

// re‑rooting
void reroot(int u) {
    visited[u] = true;
    for (Pair e : g[u]) {
        if (!visited[e.v]) {
            ans[e.v] = ans[u] + 1 - 2 * e.w;
            reroot(e.v);
        }
    }
}
```

---

### Python Snippet (Key Lines)

```python
sys.setrecursionlimit(10**6)

def dfs_root(u):
    visited[u] = True
    cost = 0
    for v, w in g[u]:
        if not visited[v]:
            cost += w + dfs_root(v)
    return cost

def reroot(u):
    visited[u] = True
    for v, w in g[u]:
        if not visited[v]:
            ans[v] = ans[u] + 1 - 2*w
            reroot(v)
```

---

### C++ Snippet (Key Lines)

```cpp
int dfs_root(int u) {
    vis[u] = 1;
    int cost = 0;
    for (auto [v,w] : g[u]) if (!vis[v]) cost += w + dfs_root(v);
    return cost;
}

void reroot(int u) {
    vis[u] = 1;
    for (auto [v,w] : g[u]) if (!vis[v]) {
        ans[v] = ans[u] + 1 - 2*w;
        reroot(v);
    }
}
```

---

## 🔑 Take‑away Checklist

1. **Turn directed edges into weighted undirected edges.**  
2. **DFS from an arbitrary root** → one pass, `O(n)` time.  
3. **Re‑Root** → propagate with `ans[child] = ans[parent] + 1 - 2*cost`.  
4. **Avoid recursion limits** (Python) or use iterative stack.  
5. **Test on small trees** to catch off‑by‑one errors.  

When an interviewer asks about “tree DP” or “graph transformation,” you can say:

> *“I realized the graph is a tree, so I converted edges into weighted undirected edges, ran a single DFS for the root‑0 answer, and used a re‑rooting step that updates each neighbor by just +1 or –1. The final solution runs in O(n) and I’ve implemented it in Java, Python, and C++.”*

---

## 🎉 Final Thoughts

LeetCode 2858 is a great example of how **structure can save you**. The re‑rooting technique isn’t limited to this problem; it applies to many others such as “minimum number of edges to delete to make a tree directed away from a root” or “find the cost to orient edges to satisfy constraints.”  

By mastering re‑rooting, you’ll turn seemingly complex graph problems into elegant DP solutions. Keep the code snippets handy for your portfolio and bring the story to your next interview – and you’ll have a solid chance of landing that job.

---

*Happy coding, and good luck on your next interview!*