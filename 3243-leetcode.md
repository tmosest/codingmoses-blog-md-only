---
title: LeetCode 3243. Shortest Distance After Road Addition Queries I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every integer `i (0 ≤ i < n)` there is an edge `i → i + 1`
(the length of the edge is `1`).  
Initially the only path from node `0` to node `i` is

```
0 → 1 → 2 → … → i        (distance = i)
```

After each query an additional edge `u → v` is inserted.
`u` is always smaller than `v`, therefore all edges of the graph
go from a smaller index to a larger one – the graph is a DAG and all
edges have weight `1`.

For every query we have to output the current distance
`dist[n‑1]` from node `0` to node `n‑1`
(or `-1` if it is unreachable).

--------------------------------------------------------------------

#### 1.  Observation

The only way to improve the distance of a vertex `x ≥ v`
after inserting the edge `u → v` is by reaching `v` first and then
moving along the linear chain `v → v+1 → v+2 → … → x`.

The distance of `x` after the improvement is

```
dist[v] + (x – v)
```

because all intermediate edges have weight `1`.

Therefore after an improvement we only have to walk forward on the
chain starting from `v` and update the vertices that can still be
shortened.



--------------------------------------------------------------------

#### 2.  Skipping already optimal vertices – DSU “next”

During a single query we may have to walk over many vertices of the
chain, but after we have updated the distance of a vertex `i` we will
never need to visit it again while this distance stays the same.
Hence we can skip it by storing a “next” pointer.

We use a disjoint set union (DSU) where

```
parent[i] – the smallest index j ≥ i that still needs to be checked
```

Initially every vertex points to its successor:

```
parent[i] = i + 1          (0 ≤ i < n)
parent[n]   = n            // sentinel
```

`find(i)` returns the current `parent[i]` with path compression.
When a vertex `i` is processed we set

```
parent[i] = find(i + 1)   // union i with i+1
```

and consequently `find(i)` will skip `i` next time.



--------------------------------------------------------------------

#### 3.  Algorithm

```
dist[0] = 0
dist[i] = INF   for i > 0
answer = []

for each query (u , v)
        // 1. Can we improve the distance of v ?
        if dist[u] + 1 < dist[v]        // better path through u → v
                dist[v] = dist[u] + 1
                // 2. Propagate the improvement forward on the chain
                var x = find(v)                // first unchecked vertex ≥ v
                while x < n-1
                        let newDist = dist[v] + (x - v)
                        if newDist >= dist[x]          // no further improvement
                                break
                        dist[x] = newDist
                        parent[x] = find(x + 1)       // skip x next time
                        x = find(x)                    // next unchecked vertex
        // 3. Record answer
        answer.append(dist[n-1] == INF ? -1 : dist[n-1])

return answer
```

`INF` can be `Int.max/4` (large enough to avoid overflow).

**Correctness proof** is given below.

--------------------------------------------------------------------

#### 4.  Correctness Proof  

We prove that the algorithm outputs the correct distance after each
query.

---

##### Lemma 1  
Before processing any query all distances satisfy  
`dist[i] = i` (the length of the initial chain) and the DSU structure
satisfies `parent[i] = i + 1`.

**Proof.**

The initial graph consists only of the edges `i → i+1` for
`0 ≤ i < n-1`.  
The unique shortest path from `0` to `i` uses `i` edges,
hence the distance is `i`.  
All vertices are initially unchecked, therefore
`parent[i] = i+1`. ∎



##### Lemma 2  
During a query, whenever the algorithm updates a vertex `x` with a
shorter distance, the value written to `dist[x]` is the length of a
valid shortest path from `0` to `x` that uses the new edge `u → v`.

**Proof.**

The new edge is `u → v`.  
Assume we have already updated `dist[v]` to the value `dist[u] + 1`.  
Every vertex `x ≥ v` that is reachable from `v` by following only the
original chain edges has a path of length

```
dist[v] + (x - v)
```

because we need exactly `x - v` additional edges `i → i+1`.  
The algorithm writes exactly this value to `dist[x]`.  
All edges of the graph are directed from a smaller to a larger
vertex, thus any path from `0` to `x` that uses the new edge must
first go through `v`.  
Therefore the written distance is the length of a valid shortest
path that uses the new edge. ∎



##### Lemma 3  
If during a query the algorithm stops the `while` loop at vertex `x`,
then `dist[x]` cannot be improved by any path that uses the new edge
`u → v`.

**Proof.**

The loop stops when  
`newDist = dist[v] + (x - v) >= dist[x]`.  
Any path from `0` to `x` that uses `u → v` must first reach `v`,
then travel the remaining `x - v` edges of the chain.  
Its length is at least `newDist`.  
Because `newDist ≥ dist[x]`, the current distance is already no larger
than any path that uses the new edge, hence cannot be improved. ∎



##### Lemma 4  
During a query each vertex is processed by the `while` loop at most
once.

**Proof.**

When a vertex `x` is processed, the algorithm executes

```
parent[x] = find(x + 1)
```

which unites `x` with its successor.  
Afterwards `find(x)` immediately returns an index larger than `x`,
so vertex `x` will never be returned by `find` again in the same
query. ∎



##### Lemma 5  
After processing a query, for every vertex `i` the DSU invariant
`parent[i] = i + 1` holds for all vertices that still have distance
`INF`.

**Proof.**

Vertices that never received a finite distance keep their original
parent value `i + 1`.  
Vertices that did receive a finite distance are united with their
successor (Lemma&nbsp;4) and will never be needed again while the
distance remains unchanged. ∎



##### Theorem  
For every query the algorithm appends the length of the shortest
path from node `0` to node `n-1` after inserting the new edge
(or `-1` if no such path exists).

**Proof.**

We prove by induction over the queries.

*Base – first query.*

Before the first query the distances satisfy Lemma&nbsp;1.
The algorithm first checks whether `dist[v]` can be improved.
If not, nothing changes and the answer is simply the old distance of
`n-1`.  
If `dist[v]` is improved, the `while` loop updates all vertices
reachable from `v` that still benefit from the new edge.
By Lemma&nbsp;2 all distances written by the loop are valid shortest
paths using the new edge, and by Lemma&nbsp;3 no other vertex can be
improved by such a path.
Hence the distance of `n-1` is correct after the first query.

*Induction step.*

Assume after processing queries `1 … k-1` all distances are correct.
Consider query `k`.  
The algorithm checks whether a better path to `v` exists.
If it does, `dist[v]` is updated to the optimal value
`dist[u] + 1` (Lemma&nbsp;2).
The `while` loop then walks forward on the chain.
By Lemma&nbsp;2 all distances written during the loop are lengths of
valid shortest paths that use the new edge.
By Lemma&nbsp;3 the loop stops exactly at the first vertex that cannot
be improved by a path using the new edge, thus no further improvement
is possible.
Vertices that are not reachable from `v` keep their old distances,
which are already optimal because the graph is a DAG with edges
directed to the right.
Consequently after the loop finishes all distances are the true
shortest distances in the updated graph.
The value appended to the answer is therefore correct. ∎



--------------------------------------------------------------------

#### 5.  Complexity Analysis

Let `α(n)` be the inverse Ackermann function (very small).

```
initialisation          :  O(n)
each query
        distance check    :  O(1)
        while loop        :  visits each vertex at most once in the whole run
        DSU operations    :  α(n) per call
```

Total time over all queries

```
O( n + Q · α(n) )
```

where `Q` is the number of queries.

The algorithm uses two arrays of size `n + 1`:

```
dist[0 … n-1]   (Int)
parent[0 … n]   (Int)
```

So the memory consumption is `O(n)`.



--------------------------------------------------------------------

#### 6.  Reference Implementation  (Swift 5.5)

```swift
import Foundation

// ------------------------------------------------------------
//  Shortest Path with Edge Insertion (Swift 5.5)
// ------------------------------------------------------------

final class Solution {
    // Very large value – large enough to represent INF
    private let INF = Int.max / 4
    
    // Main function called by the judge
    // n : number of vertices
    // queries : array of [u, v]
    func shortestDistance(_ n: Int, _ queries: [[Int]]) -> [Int] {
        // distances
        var dist = [Int](repeating: INF, count: n)
        dist[0] = 0
        
        // DSU structure – parent[i] is the first unchecked vertex ≥ i
        // We use n+1 elements to have a sentinel value
        var parent = [Int](repeating: 0, count: n + 1)
        for i in 0..<n { parent[i] = i + 1 }
        parent[n] = n                 // sentinel
        
        // helper: DSU find with path compression
        func find(_ x: Int) -> Int {
            if parent[x] == x { return x }
            parent[x] = find(parent[x])
            return parent[x]
        }
        
        var result: [Int] = []
        
        // Process every query
        for q in queries {
            let u = q[0]
            let v = q[1]
            
            // 1. Can we improve dist[v] ?
            if dist[u] + 1 < dist[v] {
                dist[v] = dist[u] + 1
                // 2. Propagate improvement forward on the chain
                var x = find(v)
                while x < n - 1 {
                    let newDist = dist[v] + (x - v)
                    if newDist >= dist[x] {      // no further improvement
                        break
                    }
                    dist[x] = newDist
                    parent[x] = find(x + 1)      // skip x next time
                    x = find(x)                  // next unchecked vertex
                }
            }
            
            // 3. Append answer
            result.append(dist[n - 1] == INF ? -1 : dist[n - 1])
        }
        return result
    }
}
```

**How to use**

The judge calls

```swift
let sol = Solution()
let answer = sol.shortestDistance(n, queries)
```

where `n` is the number of vertices and `queries` is a `[[Int]]` array
containing all queries in the given order.

--------------------------------------------------------------------

The algorithm runs in `O(n + Q · α(n))` time,
uses `O(n)` memory, and satisfies the correctness proof above.