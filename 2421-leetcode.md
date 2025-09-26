---
title: LeetCode 2421. Number of Good Paths - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Python – Union‑Find solution for “Number of Good Paths”**

The goal is to count the number of *good* paths in an undirected graph.

*   A good path is a simple (no repeated vertex) path whose endpoints have the same value, and the maximum value on the whole path equals that endpoint value.
*   Every single vertex is a trivial good path – that contributes one to the answer.

The classic solution runs in  
`O( (n + m) · α(n) )` time (`α` = inverse Ackermann function) and
`O(n + m)` memory, where `n` is the number of nodes and `m` the number of edges.

---

### 1.  Idea

1. **Adjacency list** – store the graph as a list of neighbours for every node.
2. **Value → nodes map** – for each distinct node value keep a list of nodes having that value.
3. **Process values in ascending order** –  
   For a current value `v` we only need to merge nodes that can be connected by a path that does **not** contain a value larger than `v`.  
   That means we may safely union a node with a neighbour *iff* the neighbour’s value ≤ `v`.
4. **Component counting** – after all unions for the current value are done, look only at the nodes that actually have value `v`.  
   Count how many of them belong to each connected component (root).  
   If a component contains `k` such nodes, all unordered pairs of them give an additional `k·(k‑1)/2` good paths.  
   Add those to the answer.
5. **Initial answer** – each node alone contributes one good path.  
   So we start with `ans = n` and add the pair counts as we go.

---

### 2.  Code

```python
from collections import defaultdict
from typing import List

# ---------- Union‑Find (Disjoint Set Union) ----------
class DSU:
    """
    Classic DSU with path compression and union by size.
    parent[x] == x  →  x is a root
    size[root]     →  number of nodes in the component
    """
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size   = [1] * n

    def find(self, x: int) -> int:
        """Return the root of x with path compression."""
        while self.parent[x] != x:
            self.parent[x] = self.parent[self.parent[x]]
            x = self.parent[x]
        return x

    def union(self, a: int, b: int) -> None:
        """Merge the components containing a and b."""
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return
        # union by size – attach the smaller tree to the larger one
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]

# ---------- Main solution ----------
def number_of_good_paths(vals: List[int], edges: List[List[int]]) -> int:
    """
    Count all good paths in an undirected graph.

    Parameters
    ----------
    vals : List[int]
        Node values.  Node i has value vals[i].
    edges : List[List[int]]
        Undirected edges, each edge is [u, v] (0‑based indices).

    Returns
    -------
    int
        The total number of good paths.
    """
    n = len(vals)
    # 1. Build adjacency list
    adj = defaultdict(list)
    for u, v in edges:
        adj[u].append(v)
        adj[v].append(u)

    # 2. Map value → list of nodes having that value
    val_to_nodes = defaultdict(list)
    for node, val in enumerate(vals):
        val_to_nodes[val].append(node)

    # 3. Prepare DSU
    dsu = DSU(n)

    # 4. Initial answer: each single node is a good path
    ans = n

    # 5. Process values in ascending order
    for val in sorted(val_to_nodes):
        nodes = val_to_nodes[val]

        # a) Merge nodes with neighbours of value <= current value
        for node in nodes:
            for nb in adj[node]:
                if vals[nb] <= val:
                    dsu.union(node, nb)

        # b) Count nodes of this value inside each component
        comp_cnt = defaultdict(int)
        for node in nodes:
            root = dsu.find(node)
            comp_cnt[root] += 1

        # c) Each unordered pair of such nodes forms a good path
        for cnt in comp_cnt.values():
            if cnt > 1:
                ans += cnt * (cnt - 1) // 2

    return ans
```

---

### 3.  How it works – a quick walk‑through

* **Sorting the values** guarantees that when we process a value `v`, every node that can lie on a good path to another node of value `v` has already been merged with all strictly smaller values.
* **Union only with neighbours of value ≤ v** preserves the condition that the maximum on the path is `v`.  
  Any neighbour with value `> v` would violate the good‑path rule and therefore must not be merged for this step.
* **Counting pairs per component** – once the unions are done, all nodes of value `v` that share the same root belong to a single connected component that contains only nodes ≤ `v`.  
  Every pair of them can be connected by a good path, hence the binomial coefficient.

---

### 4.  Complexity analysis

*Building adjacency list* – `O(n + m)`  
*Sorting unique values* – `O(k log k)`, where `k` ≤ `n`  
*Processing each node once for each of its neighbours* – `O(m)` unions, each `α(n)` amortised  
*Component counting* – `O(n)` over all values

Total time: **`O((n + m) · α(n))`** (essentially linear).  
Memory consumption: **`O(n + m)`**.

---

### 5.  Example

```python
if __name__ == "__main__":
    vals   = [1, 3, 2, 1, 3]
    edges  = [[0,1], [1,2], [2,3], [2,4]]
    print(number_of_good_paths(vals, edges))   # Output: 6
```

The 6 good paths are:

1. `[0]` (value 1)
2. `[1]` (value 3)
3. `[2]` (value 2)
4. `[3]` (value 1)
5. `[4]` (value 3)
6. `[1, 2, 4]` – endpoints both 3, intermediate node 2 has value 2 ≤ 3.

---

Feel free to drop the function into your own codebase or use it as a reference for implementing the *Number of Good Paths* problem in any Python environment.