---
title: LeetCode 2662. Minimum Cost of a Path With Special Roads - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Idea**

For any two points `p1` and `p2` we can always walk by the grid and the cost is their Manhattan distance

```
dist(p1,p2) = |x1-x2| + |y1-y2|
```

A *special road* `(a,b,c,d,w)` gives us a *directed* shortcut from `(a,b)` to `(c,d)` with cost `w`.  
If `w` is not cheaper than the normal Manhattan distance we will never use that road – we can simply walk directly.

The problem is therefore a shortest‑path problem on a graph:

* **Vertices** – all distinct coordinates that appear:
  * start point
  * target point
  * every `(a,b)` and `(c,d)` from all special roads

  Let the total number of vertices be `N` (`N ≤ 2 + 2·k`, `k ≤ 100`).

* **Edges**  
  * For every pair of vertices `u , v` add an undirected edge with weight `dist(u,v)` (grid walking).  
  * For every special road add a directed edge from its start to its end with weight `w`.

Now the answer is simply the length of the shortest path from the start vertex to the target vertex.  
Because the graph is dense (≈ `N²` edges) we can run **Dijkstra** directly on it – its complexity  
`O(N² log N)` is more than fast enough for `N ≤ 202`.

---

### Algorithm
1. **Collect all vertices**  
   Map each unique coordinate to an integer id.  
2. **Pre‑compute all Manhattan distances**  
   `mdist[i][j] = |x_i-x_j| + |y_i-y_j|`.
3. **Build adjacency lists**  
   * For every pair `i < j` add the undirected edge `(i,j)` with weight `mdist[i][j]`.  
   * For every special road `(a,b,c,d,w)` add a directed edge from id(a,b) to id(c,d) with weight `w`.  
4. **Run Dijkstra** from the start vertex.  
5. Return the distance found to the target vertex.

---

### Correctness Proof  

We prove that the algorithm returns the minimal possible travel cost.

**Lemma 1**  
For any two vertices `u` and `v` the graph contains an edge of weight equal to the minimum
possible cost of travelling from `u` to `v` using only normal grid moves.

*Proof.*  
By construction we added an undirected edge between every pair of vertices with weight
`|x_u-x_v| + |y_u-y_v|`. This is exactly the Manhattan distance, which is the minimum cost
to walk between `u` and `v` without using a special road. ∎



**Lemma 2**  
For any special road `(a,b)->(c,d,w)` the graph contains a directed edge whose weight
equals the cost of using that road.

*Proof.*  
We added a directed edge from id(a,b) to id(c,d) with weight `w`. ∎



**Lemma 3**  
For any walk in the original problem statement there exists a path in the constructed
graph with exactly the same total cost, and vice‑versa.

*Proof.*  
A walk consists of alternating segments of grid walking and (possibly) special roads.  
* A grid‑walking segment between two consecutive vertices `u,v` of the walk
  can be replaced by the edge `(u,v)` of Lemma&nbsp;1 – the costs match.  
* A special‑road segment is represented by the corresponding directed edge of
  Lemma&nbsp;2 – the costs match.  
Because we inserted edges for *every* pair of vertices that can appear in a walk,
the whole walk maps to a graph path of equal cost.  
The inverse direction is trivial: any graph path translates back into a walk in the
original setting because every graph edge is realizable either by walking or by a special
road. ∎



**Lemma 4**  
Dijkstra’s algorithm returns the length of the shortest path in the graph from the start
to the target.

*Proof.*  
All edge weights are non‑negative, so the standard correctness argument for Dijkstra applies. ∎



**Theorem**  
The algorithm outputs the minimum possible travel cost from the start point to the
target point.

*Proof.*  
By Lemma&nbsp;3 the set of feasible walks in the original problem is in one‑to‑one
correspondence with the set of paths in the constructed graph, preserving total cost.  
By Lemma&nbsp;4 Dijkstra finds the minimum‑cost path in the graph.  
Therefore the cost returned by the algorithm equals the optimum in the original problem. ∎



---

### Complexity Analysis  

Let `k` be the number of special roads (`k ≤ 100`).  
Number of vertices `N = 2 + 2·k ≤ 202`.

* Pre‑computing Manhattan distances: `O(N²)` time, `O(N²)` memory.  
* Building adjacency lists: also `O(N²)` edges.  
* Dijkstra: `O(E log V)` with `E = Θ(N²)` → `O(N² log N)` time.  
* Memory consumption: adjacency lists + distance array → `O(N²)`.

With `N ≤ 202` this is far below the limits for a 2 s time limit.

---

### Reference Implementation (Python 3)

```python
import sys
import heapq

def solve() -> None:
    data = sys.stdin.read().strip().split()
    if not data:
        return
    it = iter(data)
    sx, sy, tx, ty = map(int, (next(it), next(it), next(it), next(it)))
    k = int(next(it))

    # collect all distinct coordinates
    coord_to_id = {}
    coords = []

    def get_id(x, y):
        if (x, y) not in coord_to_id:
            coord_to_id[(x, y)] = len(coords)
            coords.append((x, y))
        return coord_to_id[(x, y)]

    start_id = get_id(sx, sy)
    target_id = get_id(tx, ty)

    special_edges = []          # (u_id, v_id, weight)
    for _ in range(k):
        a, b, c, d, w = map(int, (next(it), next(it), next(it), next(it), next(it)))
        if w < abs(a - c) + abs(b - d):   # useful road only
            u = get_id(a, b)
            v = get_id(c, d)
            special_edges.append((u, v, w))

    n = len(coords)            # number of vertices

    # pre‑compute Manhattan distances
    mdist = [[0] * n for _ in range(n)]
    for i in range(n):
        xi, yi = coords[i]
        for j in range(i + 1, n):
            xj, yj = coords[j]
            d = abs(xi - xj) + abs(yi - yj)
            mdist[i][j] = mdist[j][i] = d

    # build adjacency lists
    adj = [[] for _ in range(n)]
    # grid walking edges (undirected)
    for i in range(n):
        for j in range(i + 1, n):
            w = mdist[i][j]
            adj[i].append((j, w))
            adj[j].append((i, w))
    # special road edges (directed)
    for u, v, w in special_edges:
        adj[u].append((v, w))

    # Dijkstra
    INF = 10**18
    dist = [INF] * n
    dist[start_id] = 0
    pq = [(0, start_id)]

    while pq:
        d, u = heapq.heappop(pq)
        if d != dist[u]:
            continue
        if u == target_id:
            break
        for v, w in adj[u]:
            nd = d + w
            if nd < dist[v]:
                dist[v] = nd
                heapq.heappush(pq, (nd, v))

    print(dist[target_id])

if __name__ == "__main__":
    solve()
```

**Input format**

```
sx sy tx ty
k
a1 b1 c1 d1 w1
...
ak bk ck dk wk
```

**Output**

```
minimum_cost
```

The program follows exactly the algorithm proven correct above and runs comfortably
within the limits.