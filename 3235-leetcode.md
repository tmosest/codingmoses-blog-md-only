---
title: LeetCode 3235. Check if the Rectangle Corner Is Reachable - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ### Union‑Find (Disjoint‑Set Union, DSU)

Union‑Find is a very fast data structure that keeps track of a partition of a set into **disjoint (non‑overlapping) subsets**.  
You can

1. **Find** which subset an element belongs to.
2. **Union** (merge) two subsets into one.

It is the backbone of many algorithms: Kruskal’s MST, cycle detection in graphs, network connectivity, image processing, etc.

---

## 1.  What the structure stores

| Array | Meaning |
|-------|---------|
| `parent[i]` | For each element `i`, the parent of `i` in a forest. If `i` is a root of a tree, `parent[i] == i`. |
| `rank[i]` (or `size[i]`) | Roughly the height (or size) of the tree whose root is `i`. Used to keep trees shallow. |

Initially, every element is its own set:

```text
parent[i] = i
rank[i]   = 0     (or size[i] = 1)
```

All sets are represented as a forest of trees.

---

## 2.  Operations

### `find(x)` – locate the set containing `x`

```pseudo
function find(x):
    if parent[x] != x:
        parent[x] = find(parent[x])   // Path compression
    return parent[x]
```

*Recursively walk up to the root.*  
During the walk, we **compress the path**: each visited node points directly to the root.  
After a `find`, the tree depth becomes at most 1 for the visited nodes.

### `union(x, y)` – merge the sets containing `x` and `y`

```pseudo
function union(x, y):
    rootX = find(x)
    rootY = find(y)
    if rootX == rootY: return          // already in same set

    // Union by rank (or by size)
    if rank[rootX] < rank[rootY]:
        parent[rootX] = rootY
    else if rank[rootX] > rank[rootY]:
        parent[rootY] = rootX
    else:
        parent[rootY] = rootX
        rank[rootX] += 1
```

If the ranks are equal we arbitrarily pick one as new root and increment its rank.

---

## 3.  Why it’s fast

- Each `find` does path compression → the tree becomes a single level on next visits.
- Each `union` attaches the smaller tree under the larger (by rank) → the tree depth stays low.

The amortised time per operation is **inverse Ackermann**:  
`α(n) ≈ 4` for all practical `n`. In short, it is essentially *O(1)* for all realistic inputs.

---

## 4.  Quick example

Suppose we have elements 0…5. We’ll do unions:

```
union(0,1)   // sets: {0,1} {2} {3} {4} {5}
union(2,3)   // sets: {0,1} {2,3} {4} {5}
union(1,2)   // merge the two sets
```

After those operations:

```
find(0) → root r
find(5) → root 5   (different)
```

The structure after compression may look like:

```
parent = [r, r, r, r, 4, 5]
rank   = [1, 0, 0, 0, 0, 0]
```

All nodes in the merged set now point directly to `r`.

---

## 5.  Common use‑case: Kruskal’s Minimum Spanning Tree

```pseudo
sort edges by weight
for each edge (u,v) in order:
    if find(u) != find(v):
        add edge to MST
        union(u,v)
```

Because `find` quickly tells whether adding an edge would create a cycle, we can build an MST in `O(E log V)` time.

---

## 6.  C++/Python snippets

### C++ (compact)

```cpp
struct DSU {
    vector<int> parent, rank;
    DSU(int n=0) { init(n); }

    void init(int n){
        parent.resize(n);
        rank.assign(n,0);
        iota(parent.begin(), parent.end(), 0);
    }

    int find(int x){
        return parent[x]==x ? x : parent[x]=find(parent[x]);   // path compression
    }

    void unite(int a, int b){
        a=find(a); b=find(b);
        if(a==b) return;
        if(rank[a]<rank[b]) swap(a,b);
        parent[b]=a;
        if(rank[a]==rank[b]) rank[a]++;
    }
};
```

### Python (clear)

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank = [0]*n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]

    def union(self, a, b):
        a, b = self.find(a), self.find(b)
        if a == b: return
        if self.rank[a] < self.rank[b]:
            a, b = b, a
        self.parent[b] = a
        if self.rank[a] == self.rank[b]:
            self.rank[a] += 1
```

---

## 7.  Take‑away

*Union‑Find* is a nearly constant‑time structure for maintaining a partition of elements under union operations.  
With **path compression** and **union by rank/size** it guarantees amortised `O(α(n))` per operation, which is practically *O(1)*.  
Its simplicity and speed make it indispensable in graph algorithms and many other applications.