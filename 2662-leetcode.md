---
title: LeetCode 2662. Minimum Cost of a Path With Special Roads - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem restatement  

You start at point **S = (sx , sy)** and want to reach point **T = (tx , ty)**.  
The “normal” travel cost between two grid points is the Manhattan distance  

```
cost((x1,y1) , (x2,y2)) = |x1−x2| + |y1−y2|
```

In addition you are given `m` *special roads*.  
Each special road is described by five integers  

```
x1  y1  x2  y2  c
```

If you decide to use this road you can jump from `(x1 , y1)` to `(x2 , y2)` paying only `c` instead of the normal Manhattan distance between these two points.

You may use any special road any number of times, but you will never pay more for a special road than the normal distance – otherwise it would never be useful.

**Goal** – find the minimum possible total cost to get from **S** to **T**.



--------------------------------------------------------------------

## 2.  Observations

* The whole journey can be seen as a graph problem:  
  the only *interesting* vertices are `S`, `T` and the endpoints of the special roads.
  All other points on the grid are irrelevant because you can always move from one
  interesting point to another by the normal Manhattan distance.

* Between any two interesting vertices `u` and `v` we can always walk with cost
  `dist(u,v) = |xu−xv| + |yu−yv|`.

* For each special road we additionally have a **directed** edge  
  from its start to its end with weight `c`.

* The resulting graph has at most `2m+2` vertices – tiny.  
  The classic Dijkstra algorithm will solve it in
  `O(V² log V)` time, which is far below any practical limit.

--------------------------------------------------------------------

## 3.  Algorithm

```
build a list of all interesting vertices:
        start  (sx,sy)
        target (tx,ty)
        every (x1,y1) and (x2,y2) of a special road

index each vertex 0 … N-1

build a dense adjacency matrix A[ i ][ j ]:
        A[i][j] = Manhattan distance between vertex i and j
        (this is the “normal” edge, undirected)

for every special road  (x1,y1)->(x2,y2) with cost c:
        let i = index of (x1,y1)
        let j = index of (x2,y2)
        A[i][j] = min( A[i][j] , c )   // special edge may be cheaper

run Dijkstra from the vertex that represents S
return distance to the vertex that represents T
```

The Dijkstra implementation uses a simple priority queue.
Because the graph is dense it is easiest to keep the adjacency matrix
(and not a list of adjacency lists).

--------------------------------------------------------------------

## 4.  Correctness Proof  

We prove that the algorithm returns the optimal cost.

---

### Lemma 1  
For any two interesting vertices `u` and `v` there exists a path from `u` to `v`
whose total cost is exactly the Manhattan distance `dist(u,v)`.

**Proof.**  
By definition of *normal* travel you can walk on the grid from `u` to `v` with
exactly that cost. ∎



### Lemma 2  
For any special road `R = (x1,y1,x2,y2,c)` the graph contains a directed edge
`(x1,y1) → (x2,y2)` of weight `c`.

**Proof.**  
We add this edge explicitly in the adjacency matrix construction. ∎



### Lemma 3  
Any feasible journey from `S` to `T` can be represented by a walk in the
constructed graph whose weight equals the journey’s cost.

**Proof.**  

*Whenever you move from an interesting point `u` to another interesting point `v`*  
  – you can always walk with the normal cost `dist(u,v)`  
  (Lemma&nbsp;1) – the graph contains an undirected edge with exactly this weight.

*Whenever you use a special road `R`*  
  – you jump from its start to its end paying `c`.  
  The graph contains a directed edge for `R` with the same weight (Lemma&nbsp;2).

*Any concatenation of such moves* is therefore a walk in the graph with the same total weight as the original journey. ∎



### Lemma 4  
Let `d[i]` be the distance computed by Dijkstra for vertex `i`.  
`d[i]` equals the minimum possible cost to reach vertex `i` from `S`.

**Proof.**  
All edges of the graph are non‑negative (Manhattan distances are ≥ 0, and all
special roads are filtered to be cheaper than the Manhattan distance).
Thus Dijkstra’s relaxation property guarantees that after it finishes,
`d[i]` is exactly the shortest path cost from `S` to vertex `i`. ∎



### Theorem  
The algorithm outputs the minimum possible cost to travel from `S` to `T`.

**Proof.**

* By Lemma&nbsp;3 any feasible journey corresponds to a walk in the graph,
  hence its cost is at least the shortest path cost from `S` to `T`.

* By Lemma&nbsp;4 Dijkstra computes exactly this shortest path cost.

Therefore the value returned by the algorithm is both achievable
(and thus a lower bound for the optimum) and no larger than any feasible journey’s cost.
Hence it is optimal. ∎



--------------------------------------------------------------------

## 4.  Implementation (Python 3)

```python
import sys
import heapq
from typing import List, Tuple

def min_cost(
    start: List[int],
    target: List[int],
    special: List[List[int]]
) -> int:
    """Return the minimal cost from start to target using special roads."""

    # 1. collect all interesting vertices
    vertices = [(start[0], start[1]), (target[0], target[1])]
    for x1, y1, x2, y2, _ in special:
        vertices.append((x1, y1))
        vertices.append((x2, y2))

    n = len(vertices)                    # <= 2*m + 2
    idx_start = 0
    idx_target = 1

    # 2. adjacency matrix – normal (undirected) edges
    #    we store it as a 2‑D list for convenience
    dist_norm = [[0] * n for _ in range(n)]
    for i in range(n):
        xi, yi = vertices[i]
        for j in range(i + 1, n):
            xj, yj = vertices[j]
            d = abs(xi - xj) + abs(yi - yj)
            dist_norm[i][j] = d
            dist_norm[j][i] = d

    # 3. add directed edges for special roads (may be cheaper than normal)
    #    we keep them in a separate list for faster relaxation
    special_edges: List[Tuple[int, int, int]] = []   # (u_start, u_end, weight)
    for x1, y1, x2, y2, c in special:
        u = vertices.index((x1, y1)) if (x1, y1) in vertices else None
        v = vertices.index((x2, y2)) if (x2, y2) in vertices else None
        # The two endpoints are guaranteed to be in `vertices`
        u = vertices.index((x1, y1))
        v = vertices.index((x2, y2))
        special_edges.append((u, v, c))

    # 4. Dijkstra
    INF = 10 ** 18
    d = [INF] * n
    d[idx_start] = 0
    pq = [(0, idx_start)]

    while pq:
        cur_cost, u = heapq.heappop(pq)
        if cur_cost != d[u]:
            continue          # stale entry

        # relax all normal edges (undirected)
        for v in range(n):
            if v == u:
                continue
            w = dist_norm[u][v]
            if d[v] > cur_cost + w:
                d[v] = cur_cost + w
                heapq.heappush(pq, (d[v], v))

        # relax all special roads that start at this vertex
        for (su, sv, c) in special_edges:
            if su == u:
                # cost: walk to the start of the special road (already at su),
                # then take the special road to sv
                if d[sv] > cur_cost + c:
                    d[sv] = cur_cost + c
                    heapq.heappush(pq, (d[sv], sv))

    return d[idx_target]
    

# ------------------------------------------------------------------
# Example usage
# ------------------------------------------------------------------
if __name__ == "__main__":
    # sample input
    sx, sy = 1, 1
    tx, ty = 3, 3
    special = [
        [1, 1, 2, 2, 1],   # cheaper than normal (2 -> 2)
        [2, 2, 3, 3, 1],
    ]
    print(min_cost([sx, sy], [tx, ty], special))   # output: 2
```

### Explanation of the code

* We first build a list of *all* interesting vertices and index them.
* The adjacency matrix `dist_norm` contains the normal Manhattan distance between any two vertices.
* `special_edges` stores the directed special roads.
* During Dijkstra we relax **both** types of edges:
  * `dist_norm[u][v]` – walk normally.
  * for every special road that starts at `u`, we relax the edge to its end
    with weight `c`.
* The priority queue guarantees that the shortest distance to each vertex
  is found exactly once.

--------------------------------------------------------------------

## 5.  Complexity analysis

Let `n = 2*m + 2` be the number of interesting vertices (`m` is the number of special roads).

* Building the adjacency matrix: `O(n²)`
* Dijkstra on a dense graph:  
  * Each vertex is popped once – `O(n log n)`  
  * For each popped vertex we relax `n` normal edges and up to `m` special edges – `O(n)` per pop  
  * Overall: `O(n² log n)`.

With the typical constraints (`m ≤ 10`) this is
**O(100 log 12)** – trivial in practice.



--------------------------------------------------------------------

## 6.  Why the provided Java snippet works

The Java code in the question does not explicitly add all Manhattan edges between
special nodes.  
Instead, during Dijkstra it considers *every* special road from the *current* node:

```java
// current node = (x,y)
// consider special road (a,b) -> (c,d) with cost = cost
newDist = dist(current) + Manhattan((x,y),(a,b)) + cost;
```

Because the loop iterates over **all** special roads, at any moment we are able to
walk from the current node to the start of *any* special road with the normal
Manhattan cost, then pay the special‑road cost to reach its end.  
Thus every necessary edge of the dense graph is explored implicitly, and the
algorithm is correct.

--------------------------------------------------------------------

### Bottom line

*Model the problem as a graph on the handful of interesting points, add the
directed special edges, run Dijkstra, and you’re done.*  
The solution above is a clean, language‑agnostic version of that idea.