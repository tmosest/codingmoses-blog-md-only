---
title: LeetCode 1135. Connecting Cities With Minimum Cost - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Connecting Cities With Minimum Cost – A Job‑Ready Guide  
*(LeetCode 1135 – Minimum Spanning Tree – Java, Python, C++)*

---

## Why This Problem Matters

- **Classical interview star** – It tests graph fundamentals, greedy algorithms and data‑structure knowledge.  
- **Real‑world relevance** – Network design, road planning, and cloud‑infrastructure cost minimization are all “connect‑all‑cities” problems.  
- **Three angles to show off** – You can solve it with **Prim**, **Kruskal**, or even a hybrid **Union‑Find + Min‑Heap**.  
- **SEO‑ready** – “minimum spanning tree”, “connecting cities”, “Java MST”, “Python MST”, “C++ MST”, “LeetCode 1135” are all high‑traffic keywords that recruiters search for.

---

## The Good

| Aspect | Why it’s good |
|--------|---------------|
| **Clear problem statement** | `n` cities, `m` bidirectional roads with a cost → find the minimum cost to connect all. |
| **Bounded constraints** | `n, m ≤ 10^4` → a `O(m log m)` solution is comfortably fast. |
| **One‑liner intuition** | *“Build the cheapest tree that spans all nodes.”* |
| **Rich interview discussion** | You can talk about greedy choice, cycle detection, and optimality proofs. |

---

## The Bad

| Issue | What it can bite you |
|-------|---------------------|
| **Disconnected graphs** | You must detect that no spanning tree exists and return `-1`. |
| **Large edge weights** | Use `long` or `int` carefully to avoid overflow. |
| **Memory limits** | Storing all edges as a list of 3‑int arrays is fine for 10^4, but be mindful if you tweak the constraints. |

---

## The Ugly

| Pain point | Fix |
|------------|-----|
| **Off‑by‑one indices** | Cities are 1‑based – keep `parent[0]` unused. |
| **Union‑Find path compression bug** | Recursive `find()` with path compression or iterative loop; always test on a simple cycle. |
| **PriorityQueue comparator pitfalls (Java)** | A lambda `(a, b) -> a[1] - b[1]` can overflow; use `Integer.compare(a[1], b[1])`. |
| **C++ STL pitfalls** | `std::sort` on vector of tuples; use `std::priority_queue` with `greater<>`. |
| **Python list sorting overhead** | Use `key=lambda e: e[2]` and `heapq` for Prim. |

---

## Solution Overview

Two canonical MST algorithms work perfectly here:

1. **Kruskal** – Sort all edges by cost, add the cheapest edge that doesn’t create a cycle (Union‑Find).  
2. **Prim** – Start from an arbitrary city, always take the cheapest edge leaving the visited set (min‑heap).

Both give the same minimum total cost.  
Below we provide clean, production‑ready implementations in **Java, Python, and C++**.

---

## Java Implementation – Kruskal with Union‑Find

```java
import java.util.*;

public class Solution {
    // ---------- Union-Find ----------
    private static class UnionFind {
        private final int[] parent;
        private final int[] size;
        private int components;          // number of disjoint sets

        public UnionFind(int n) {
            parent = new int[n + 1];     // 1‑based
            size   = new int[n + 1];
            components = n;
            for (int i = 1; i <= n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }

        public int find(int x) {
            if (parent[x] != x) {
                parent[x] = find(parent[x]);   // path compression
            }
            return parent[x];
        }

        public boolean union(int a, int b) {
            int ra = find(a), rb = find(b);
            if (ra == rb) return false;          // already connected
            if (size[ra] < size[rb]) {           // union by size
                int tmp = ra; ra = rb; rb = tmp;
            }
            parent[rb] = ra;
            size[ra] += size[rb];
            components--;
            return true;
        }

        public boolean allConnected() {
            return components == 1;
        }
    }

    // ---------- Main function ----------
    public int minimumCost(int n, int[][] connections) {
        // Sort edges by cost (k[2] = weight)
        Arrays.sort(connections, (a, b) -> Integer.compare(a[2], b[2]));

        UnionFind uf = new UnionFind(n);
        long total = 0;                     // use long to avoid overflow

        for (int[] e : connections) {
            int u = e[0], v = e[1], w = e[2];
            if (uf.union(u, v)) {
                total += w;
            }
        }

        return uf.allConnected() ? (int) total : -1;
    }

    // ---------- Simple test harness ----------
    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] conn1 = {{1,2,5},{1,3,6},{2,3,1}};
        System.out.println(s.minimumCost(3, conn1));   // → 6

        int[][] conn2 = {{1,2,3},{3,4,4}};
        System.out.println(s.minimumCost(4, conn2));   // → -1
    }
}
```

**Key Points**

- `UnionFind` implements *path compression* and *union by size* for `α(n)` amortised time.  
- Edge sorting gives `O(m log m)`.  
- Complexity: `O(m log m + m α(n)) ≈ O(m log m)`; Space: `O(n + m)`.

---

## Python Implementation – Prim with Min‑Heap

```python
import heapq
from collections import defaultdict
from typing import List

class Solution:
    def minimumCost(self, n: int, connections: List[List[int]]) -> int:
        # Build adjacency list
        graph = defaultdict(list)
        for u, v, w in connections:
            graph[u].append((w, v))
            graph[v].append((w, u))

        visited = [False] * (n + 1)
        min_heap = [(0, 1)]          # (cost, vertex) – start from city 1
        total_cost = 0
        edges_used = 0

        while min_heap and edges_used < n:
            w, u = heapq.heappop(min_heap)
            if visited[u]:
                continue

            visited[u] = True
            total_cost += w
            edges_used += 1

            for cost, v in graph[u]:
                if not visited[v]:
                    heapq.heappush(min_heap, (cost, v))

        return total_cost if edges_used == n else -1

# ---------- Example ----------
if __name__ == "__main__":
    s = Solution()
    conn1 = [[1, 2, 5], [1, 3, 6], [2, 3, 1]]
    print(s.minimumCost(3, conn1))   # 6

    conn2 = [[1, 2, 3], [3, 4, 4]]
    print(s.minimumCost(4, conn2))   # -1
```

**Why Prim?**

- No need to sort the whole edge list – you get the next cheapest edge in `O(log m)` as you traverse.  
- Handles disconnected graphs automatically: if the heap empties before all vertices are visited, return `-1`.  
- Complexity: `O(m log m)` (heap operations) – same asymptotic cost as Kruskal.  
- Uses only built‑in libraries, so it’s easy to ship to a platform.

---

## C++ Implementation – Kruskal with `std::vector` & DSU

```cpp
#include <bits/stdc++.h>
using namespace std;

// ---------- Disjoint Set Union ----------
class DSU {
public:
    vector<int> parent, sz;
    int comps;                           // number of components

    DSU(int n) : parent(n + 1), sz(n + 1, 1), comps(n) {
        iota(parent.begin(), parent.end(), 0);    // 1‑based, 0 unused
    }

    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);          // path compression
        return parent[x];
    }

    bool unite(int a, int b) {
        int ra = find(a), rb = find(b);
        if (ra == rb) return false;
        if (sz[ra] < sz[rb]) swap(ra, rb);       // union by size
        parent[rb] = ra;
        sz[ra] += sz[rb];
        --comps;
        return true;
    }

    bool connected() const { return comps == 1; }
};

// ---------- Solution ----------
int minimumCost(int n, vector<vector<int>>& connections) {
    sort(connections.begin(), connections.end(),
         [](const auto& a, const auto& b){ return a[2] < b[2]; });

    DSU dsu(n);
    long long total = 0;

    for (auto& e : connections) {
        int u = e[0], v = e[1], w = e[2];
        if (dsu.unite(u, v)) total += w;
    }

    return dsu.connected() ? static_cast<int>(total) : -1;
}

// ---------- Main for quick testing ----------
int main() {
    vector<vector<int>> conn1 = {{1,2,5},{1,3,6},{2,3,1}};
    cout << minimumCost(3, conn1) << endl;      // 6

    vector<vector<int>> conn2 = {{1,2,3},{3,4,4}};
    cout << minimumCost(4, conn2) << endl;      // -1
    return 0;
}
```

**Highlights**

- Uses `std::vector` of vectors to hold edges → `O(m)` memory.  
- `std::sort` gives the required `O(m log m)`.  
- The DSU implementation is idiomatic: iterative `find()` + union by size.  
- Works on 1‑based city indices without extra adjustments.

---

## Testing & Edge‑Case Checklist

| Test | Expected Result | Why it matters |
|------|-----------------|----------------|
| **All cities already connected** (e.g., a single road between every pair) | The minimum cost equals the sum of the cheapest `n-1` edges. | Shows that you’re not over‑counting. |
| **Disconnected graph** – e.g., two clusters with no link | Return `-1`. | Guarantees you handle the “no spanning tree” case. |
| **Single city (`n = 1`)** | Cost = `0`. | Simple boundary to avoid array out‑of‑range. |
| **Large weights** (up to `10^6`) | No overflow when summing (use `long`/`int64`). | Keeps the solution correct under all inputs. |
| **Duplicate edges** | Pick the cheapest one; duplicates don’t affect Union‑Find. | Avoids unnecessary memory usage. |
| **Cycle creation** | Union‑Find will reject the edge. | Guarantees acyclicity of the final tree. |

---

## How to Sell This Problem in an Interview

1. **Explain the core idea first**  
   > “We need the cheapest tree that touches every city – that’s exactly a Minimum Spanning Tree.”

2. **Choose an algorithm**  
   - If you prefer *global sorting* → talk about Kruskal.  
   - If you like *local greedy choices* → talk about Prim.

3. **Show the DSU proof of correctness**  
   - “By adding edges in increasing order and rejecting any that would close a cycle, we maintain the optimality property proven by the cut‑set theorem.”

4. **Highlight edge‑case handling**  
   - “If the number of connected components is more than one after all edges are considered, the graph is disconnected; we return `-1`.”

5. **Discuss time/space trade‑offs**  
   - `O(m log m)` is the theoretical lower bound for sorting edges, well within the 10^4 limits.  
   - Memory footprint is linear: `O(n + m)`.

6. **Give a clean code snippet** – paste the Java (or your preferred language) code, walk through the DSU, and let the recruiter see that you can write production‑grade code.

---

## Final Thought

Mastering *Connecting Cities With Minimum Cost* demonstrates that you:

- Understand **graph theory** and **greedy algorithms**.  
- Can implement **efficient data structures** (Union‑Find, Min‑Heap).  
- Can anticipate pitfalls (disconnected graphs, 1‑based indexing, overflow).  

Bring this problem to your next technical interview, and you’ll be talking about a proven algorithm that solves real‑world network‑optimization challenges – all while using the keywords recruiters search for. Good luck, and happy coding! 🚀

---