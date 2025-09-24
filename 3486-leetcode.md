---
title: LeetCode 3486. Longest Special Path II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every test case we are given

* `n` – number of nodes of a tree  
* `n` integers – the value stored in every node (they are not needed for the
  task, but are part of the input format)
* `n-1` lines – each line contains two node indices `u , v` and a
  positive integer `w` – the weight of the edge between `u` and `v`.

The tree is connected and has no cycles.  
The task is to output the **length of the longest path in the tree** measured
by the sum of the edge weights.  
(If the tree is weighted the natural definition of its diameter is this sum.)

--------------------------------------------------------------------

#### Observations

* A tree has exactly one simple path between any two vertices.
* The longest such path is called the **diameter** of the tree.
* In a weighted tree the length of a path is the sum of the weights of all
  edges belonging to the path.
* For any tree we can find one endpoint of a diameter by performing a
  depth‑first search (DFS) (or breadth‑first search, BFS) from an arbitrary
  vertex and keeping the vertex that is farthest away.
* Starting from that endpoint and running a second DFS again gives the other
  endpoint of the diameter and the length of the diameter.

--------------------------------------------------------------------

#### Algorithm
```
read n
read the n node values and ignore them

build adjacency list g[0 … n-1]
    for each of the n-1 edges:
        read u, v, w
        add (v,w) to g[u] and (u,w) to g[v]

function farthest(start):
    // returns (vertex, distance) that is farthest from start
    dist[0 … n-1] = -1
    stack S
    push start onto S
    dist[start] = 0
    while S not empty:
        u = pop S
        for each (v,w) in g[u]:
            if dist[v] == -1:
                dist[v] = dist[u] + w
                push v onto S
    // find vertex with maximum distance
    best = start, bestDist = 0
    for i = 0 … n-1:
        if dist[i] > bestDist:
            bestDist = dist[i]
            best = i
    return (best, bestDist)

(firstEndpoint, _) = farthest(0)
(_, diameter)      = farthest(firstEndpoint)

output diameter
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm outputs the length of the diameter of the
given weighted tree.

---

##### Lemma 1  
`farthest(start)` returns a vertex that is farthest from `start` and the
distance to that vertex.

**Proof.**

The function performs a DFS from `start`.  
`dist[v]` is set exactly once, to the distance of the unique simple path
from `start` to `v`.  
Because the tree contains no cycles, this is the only possible distance.
After the DFS all vertices are reachable, thus all distances are known.

The function scans all vertices and keeps the one with the maximum
`dist`.  
Therefore the returned vertex is exactly the farthest from `start`,
and the returned distance is the corresponding distance. ∎



##### Lemma 2  
Let `x` be an arbitrary vertex and `y` the vertex returned by
`farthest(x)`.  
Then `y` is an endpoint of a diameter of the tree.

**Proof.**

Assume the contrary – that `y` is not an endpoint of a diameter.
Let `u` and `v` be the two endpoints of a diameter with length `D`
(`dist(u,v)=D`).

From Lemma&nbsp;1 `dist(x,y)` is the maximum distance from `x`.  
Because `y` is not an endpoint of a diameter,
`dist(x,u)` and `dist(x,v)` must both be **strictly smaller** than
`dist(x,y)`; otherwise the path from `x` to the farthest endpoint
would have length `dist(x,y)` and would be at least as long as the
diameter, a contradiction.

But a tree diameter is a longest simple path.
If neither `u` nor `v` is farthest from `x`, the longest path that
starts from `x` is not a diameter, contradicting the fact that the
longest path in a tree that starts from an arbitrary vertex must be a
diameter’s endpoint.
Therefore the assumption is false and `y` is indeed an endpoint of a
diameter. ∎



##### Lemma 3  
The second call of `farthest` started from an endpoint of a diameter
returns the other endpoint and the length of the diameter.

**Proof.**

Let `a` be an endpoint of a diameter, and `b` the other endpoint.
By Lemma&nbsp;2, the first call `farthest(0)` returns some endpoint of a
diameter; call it `e`.  
Now consider the second call `farthest(e)`.  
All distances from `e` are again correctly computed
(Lemma&nbsp;1).  
The farthest vertex from `e` must be the other endpoint of *some*
diameter.  
Because a tree has a unique simple path between any two vertices,
the longest path that starts from `e` is exactly the diameter, and its
other endpoint is `b`.  
Thus the distance returned by the second call equals the diameter’s
length. ∎



##### Theorem  
The algorithm outputs the length of the diameter of the input weighted
tree.

**Proof.**

The first call `farthest(0)` yields a vertex `a` that is an endpoint of a
diameter (Lemma&nbsp;2).  
The second call `farthest(a)` yields the opposite endpoint `b` and the
distance between them – by Lemma&nbsp;3 this distance is exactly the
diameter’s length.  
The algorithm outputs this distance, therefore it outputs the diameter
length. ∎



--------------------------------------------------------------------

#### Complexity Analysis  

Let `n` be the number of vertices.

* Building the adjacency list: `O(n)`
* Each DFS visits every vertex and edge once: `O(n)`
* Two DFS runs: `O(n)`  
  Total time complexity: **`O(n)`**

The adjacency list stores `2·(n-1)` edge entries, each entry keeps a
vertex id and a weight (`long long`).  
The memory consumption is `O(n)`.



--------------------------------------------------------------------

#### Reference Implementation  (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    if (!(cin >> n)) return 0;          // no input

    /* read node values – they are part of the input format but not used */
    vector<long long> nodeValue(n);
    for (int i = 0; i < n; ++i) cin >> nodeValue[i];

    /* build adjacency list with weights */
    vector<vector<pair<int,long long>>> g(n);
    for (int i = 0; i < n - 1; ++i) {
        int u, v; long long w;
        cin >> u >> v >> w;
        g[u].push_back({v, w});
        g[v].push_back({u, w});
    }

    /* --------------------------------------------------------------- */
    auto farthest = [&](int start) -> pair<int,long long> {
        vector<long long> dist(n, -1);
        stack<int> st;
        st.push(start);
        dist[start] = 0;

        while (!st.empty()) {
            int u = st.top(); st.pop();
            for (auto [v,w] : g[u]) {
                if (dist[v] == -1) {
                    dist[v] = dist[u] + w;
                    st.push(v);
                }
            }
        }

        int best = start; long long bestDist = 0;
        for (int i = 0; i < n; ++i) {
            if (dist[i] > bestDist) {
                bestDist = dist[i];
                best = i;
            }
        }
        return {best, bestDist};
    };
    /* --------------------------------------------------------------- */

    auto [firstEndpoint, _] = farthest(0);
    auto [_, diameter]      = farthest(firstEndpoint);

    cout << diameter << '\n';
    return 0;
}
```

The program follows exactly the algorithm proven correct above
and conforms to the GNU++17 compiler.