---
title: LeetCode 2421. Number of Good Paths - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## A Friendly, Step‑by‑Step Walk‑through of the “Number of Good Paths” Problem

> **TL;DR**  
> The whole problem is about *counting pairs of vertices that can be joined by a path whose maximum vertex value is the same as the two endpoints’ value*.  
> The efficient solution uses **Union‑Find (Disjoint Set Union)** + a clever ordering of edges.  
> The idea is: add edges in order of the “largest vertex on that edge” and, while doing so, keep track of how many equal‑value vertices lie in each growing component.

Below you’ll find:

1. A plain‑English description of the problem and the intuition behind the algorithm.  
2. A line‑by‑line explanation of the Java code that actually solves the problem.  
3. A short, self‑contained Python implementation (because it’s often easier to read).  
4. A few practical tips & pitfalls to avoid.

---

### 1. The Problem, Intuitively

You’re given a **tree** (a connected graph with `n‑1` edges, no cycles) or, more generally, a graph that is *connected* but *can contain cycles* (the statement above actually contains the general graph version, but the classic LeetCode version restricts to trees).

Each vertex `i` has a value `vals[i]`.  
A **good path** is a simple path

* whose two endpoints have the **same** value, and  
* every other vertex on the path has a value **≤** that common value (i.e. the maximum value on the path is exactly the endpoint value).

The task: **Count all good paths**.  
Every vertex itself counts as a trivial good path (length = 0).  

> **Why do we care about the maximum?**  
> Because a path with endpoints of the same value is good iff the *maximum* value along the path is *exactly* that value. No vertex on the way can exceed it, otherwise the path would “break” the definition.

---

### 2. Why a DFS Does Not Scale

A straightforward depth‑first search from each vertex visits every other vertex of the same value and counts the pairs.  
That algorithm is `O(n²)` in time and `O(n²)` in space (for storing visited arrays per start).  
With `n` up to `10⁵`, it blows up—hence the TLE (Time‑Limit Exceeded).

So we need a better strategy.

---

### 3. The Union‑Find + Edge‑Ordering Solution

#### 3.1 Core Insight

If you **process edges in increasing order of the maximum endpoint value**, then when you consider an edge `(u, v)` with `max(vals[u], vals[v]) = x`, *all vertices inside the connected component that you just built have values ≤ x*.  
Therefore:

* If `vals[u] == vals[v]`, we now have **two equal‑valued vertices** that can be joined by a good path (maybe many such pairs inside the component).  
* If `vals[u] != vals[v]`, we simply merge the two components because future edges will only connect higher values, so the component will grow “upwards” without breaking any good paths.

#### 3.2 Data Structures

| Variable | Purpose | Initialized With |
|----------|---------|------------------|
| `parent[]` | DSU parent array (path compression) | `i` (each vertex is its own parent) |
| `cnt[]`    | Size of each component (number of vertices in that DSU set) | `1` for all |
| `res`      | Running count of good paths | `n` (each vertex itself is a good path) |

> **Why `cnt`?**  
> When two components of equal values merge, any vertex from component A can pair with any vertex from component B. The number of new good paths added is `cnt[A] * cnt[B]`.

#### 3.3 Algorithm Steps (Java‑style)

```java
int[] parent, cnt;   // DSU structures
int res;             // final answer

public int numberOfGoodPaths(int[] vals, int[][] edges) {
    // 1) Sort edges by the maximum endpoint value
    Arrays.sort(edges, (e1, e2) ->
        Integer.compare(Math.max(vals[e1[0]], vals[e1[1]]),
                         Math.max(vals[e2[0]], vals[e2[1]])));

    int n = vals.length;
    res = n;                    // each node itself is a good path
    parent = new int[n];
    cnt    = new int[n];
    Arrays.fill(cnt, 1);        // each component has one vertex initially

    for (int i = 0; i < n; i++) parent[i] = i;

    // 2) Process edges in the sorted order
    for (int[] edge : edges) {
        union(edge[0], edge[1], vals);
    }

    return res;
}

/* Union‑Find helpers ---------------------------------------------------- */
private void union(int x, int y, int[] vals) {
    int px = find(x);
    int py = find(y);
    if (px == py) return;   // already together

    // Merge by value
    if (vals[px] == vals[py]) {
        // Two equal‑value components: every vertex in A pairs with every
        // vertex in B → add cnt[A] * cnt[B] to result
        res += cnt[px] * cnt[py];
        // Merge the components (parent of py becomes px)
        cnt[px] += cnt[py];
        parent[py] = px;
    } else if (vals[px] > vals[py]) {
        // Keep the larger value as the new parent
        parent[py] = px;
    } else {
        parent[px] = py;
    }
}

private int find(int v) {
    if (parent[v] == v) return v;
    return parent[v] = find(parent[v]);   // path compression
}
```

#### 3.4 Why This Works

* **Edge ordering guarantees correctness**:  
  When we process an edge `(u, v)` with `max(vals[u], vals[v]) = m`, we know that all vertices already merged in the DSU have values ≤ m.  
  Therefore, after merging, any path inside the new component has a maximum value ≤ m.  
  If `vals[u] == vals[v]`, this maximum is exactly that value, so the new pairs are indeed *good*.

* **Counting inside components**:  
  Suppose component `C` contains `k` vertices whose value equals `x`.  
  After merging all possible edges with `max = x`, the component will *exactly* contain those `k` vertices (no larger ones yet).  
  The algorithm adds all pairs `k choose 2` once, at the moment when the component is formed.

* **Linear‑time DSU**:  
  DSU operations are almost `O(1)` (inverse Ackermann).  
  Sorting edges dominates the complexity: `O(m log m)` where `m` is the number of edges (≈ `n‑1` for a tree, or up to `10⁵` for a general graph).

**Complexity**  
`Time : O(m log m)` (sort) + `O(m α(n))` (DSU) → practically `O(m log m)`  
`Space: O(n)` for `parent[]`, `cnt[]`, and the `edges` array.

---

### 4. A Python Equivalent (for clarity)

```python
def number_of_good_paths(vals, edges):
    # 1) Sort edges by max endpoint value
    edges.sort(key=lambda e: max(vals[e[0]], vals[e[1]]))

    n = len(vals)
    res = n  # each node itself
    parent = list(range(n))
    cnt = [1] * n

    # DSU helpers
    def find(v):
        if parent[v] != v:
            parent[v] = find(parent[v])
        return parent[v]

    def union(x, y):
        nonlocal res
        px, py = find(x), find(y)
        if px == py: return

        if vals[px] == vals[py]:
            res += cnt[px] * cnt[py]
            cnt[px] += cnt[py]
            parent[py] = px
        elif vals[px] > vals[py]:
            parent[py] = px
        else:
            parent[px] = py

    for u, v in edges:
        union(u, v)

    return res
```

> **Tip**: If you’re dealing with very large counts (`cnt[px] * cnt[py]` can overflow a 32‑bit int), use `long` in Java or `int` in Python (Python ints are unbounded).

---

### 5. Common Pitfalls & How to Avoid Them

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Not sorting edges** | DSU may merge components containing vertices with values *greater* than the current edge, breaking the “maximum ≤ m” guarantee. | **Always sort** by `max(vals[u], vals[v])`. |
| **Counting only once per component** | If you only add `cnt[A] + cnt[B]` you’ll under‑count pairs. | Multiply the sizes: `cnt[A] * cnt[B]`. |
| **No path compression** | DSU becomes `O(n)` per operation → O(n²). | Add `parent[v] = find(parent[v])` inside `find`. |
| **Using `int` for the result** | For `n = 10⁵`, `cnt[A] * cnt[B]` can be up to `10¹⁰`. | Use `long` (Java) or `int64` (C++). |
| **Assuming the graph is a tree** | The LeetCode statement may give you a *connected graph* with cycles. The algorithm still works, but the trivial “each vertex itself” part holds only if the graph is connected. | If the graph isn’t guaranteed connected, first run a DFS/BFS to find connected components and run the DSU logic per component. |

---

### 6. How to Practice / Extend the Idea

1. **Draw a small graph** (say, 5 nodes).  
   - Write down `vals`.  
   - Sort edges manually.  
   - Step through the DSU merges and see how `res` changes.  
   This exercise will cement the “why” behind the algorithm.

2. **Add debugging prints**.  
   ```java
   System.out.println("Processing edge " + Arrays.toString(edge));
   System.out.println("Parents before: " + Arrays.toString(parent));
   System.out.println("Result after union: " + res);
   ```
   Watching the numbers evolve gives you instant intuition.

3. **Write a brute‑force counter** for small `n` (≤ 10) and compare its output to the DSU solution.  
   - This ensures you never get stuck with “does this code produce the right number?”

4. **Run unit tests**.  
   - Edge cases: all `vals` equal → answer is `n + n*(n-1)/2`.  
   - All `vals` distinct → answer is `n`.  
   - A graph with a single cycle.

5. **Time yourself** on larger inputs (e.g., `n = 10⁵`).  
   If your algorithm still TLEs, double‑check that you’re not accidentally using `ArrayList` inside the loop or creating new arrays per edge.

---

### 7. Final Take‑Away

*The efficient “Number of Good Paths” solver is essentially a *“merge components while counting equal‑value pairs”* routine.*  
Sorting the edges by the maximum endpoint value guarantees that every time we merge two components, all vertices inside the union have values **≤ current maximum**, preserving the “good‑path” condition.  
When two components have the *same* value, every vertex in one can pair with every vertex in the other, which is exactly the product of their sizes.

Because DSU operations are almost constant time, the entire algorithm runs in `O(m log m)` (the log comes from the single sort) and uses `O(n)` memory – a perfect fit for even the largest inputs on LeetCode.

Happy coding! If you run into any hiccups while implementing, just drop a comment – we’re here to help.