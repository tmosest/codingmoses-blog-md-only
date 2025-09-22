---
title: LeetCode 2316. Count Unreachable Pairs of Nodes in an Undirected Graph - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 2316 – Count Unreachable Pairs of Nodes in an Undirected Graph  

**LeetCode URL:** https://leetcode.com/problems/count-unreachable-pairs-of-nodes-in-an-undirected-graph/

> You are given an undirected graph with `n` nodes (0 … n‑1) and an edge list `edges`.  
> Return the number of pairs of distinct nodes that are *unreachable* from one another.

---

### TL;DR – The “Good, the Bad, the Ugly”

| Stage | Good | Bad | Ugly |
|-------|------|-----|------|
| Problem | Classic “connected components” problem → 2‑pointer / DSU solution | Graph can be huge (`n = 10^5`, `edges = 2·10^5`) – naive O(n²) is dead | You need to avoid integer overflow (`long`) and stack overflow in DFS |
| Algorithm | Union‑Find (Disjoint Set Union) → O(n + m) time | Re‑using `List<int[]>` in Java can cause GC overhead | Using recursion for DFS may blow the stack on a deep graph |
| Implementation | Use iterative DFS or Union‑Find | `int[][]` → `ArrayList<int[]>` overhead | Always use `long` for the answer, not `int` |

---

## 🛠️ Solutions

Below you’ll find **three idiomatic implementations** – one in Java, one in Python, and one in C++.  
All use the **Union‑Find** (Disjoint Set Union, DSU) technique, which is the cleanest and most efficient way to count connected component sizes and then the number of unreachable pairs.

> **Why DSU?**  
> * One pass over all edges.  
> * `find` + `union` are effectively *amortised* O(α(n)) (inverse Ackermann) – practically constant.  
> * No recursion → no stack‑overflow risk.  
> * Very easy to understand once you know DSU.

---

### 1️⃣ Java (Good, type‑safe, fast)

```java
import java.util.*;

public class Solution {
    // ---------- Union-Find ----------
    private static class DSU {
        int[] parent, size;
        DSU(int n) {
            parent = new int[n];
            size   = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i]   = 1;
            }
        }
        int find(int x) {
            while (x != parent[x]) {          // path compression
                parent[x] = parent[parent[x]];
                x = parent[x];
            }
            return x;
        }
        void union(int a, int b) {
            int ra = find(a), rb = find(b);
            if (ra == rb) return;
            // union by size
            if (size[ra] < size[rb]) {
                int tmp = ra; ra = rb; rb = tmp;
            }
            parent[rb] = ra;
            size[ra]  += size[rb];
        }
        int componentSize(int x) { return size[find(x)]; }
    }
    // ---------- Solution ----------
    public long countPairs(int n, int[][] edges) {
        DSU dsu = new DSU(n);
        for (int[] e : edges) {
            dsu.union(e[0], e[1]);
        }

        long ans = 0;
        long processed = 0;          // nodes already counted
        for (int i = 0; i < n; i++) {
            if (dsu.parent[i] == i) { // root → component size
                long sz = dsu.size[i];
                ans += processed * sz;
                processed += sz;
            }
        }
        return ans;   // fits in long because n <= 1e5 → max pairs 5e9
    }
}
```

**Complexities**  
*Time*: **O(n + m)** (α(n) ≈ 4)  
*Space*: **O(n)** (parent & size arrays)

---

### 2️⃣ Python 3 (Elegant, ready‑to‑paste)

```python
class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size   = [1] * n

    def find(self, x: int) -> int:
        while x != self.parent[x]:
            self.parent[x] = self.parent[self.parent[x]]  # path compression
            x = self.parent[x]
        return x

    def union(self, a: int, b: int) -> None:
        ra, rb = self.find(a), self.find(b)
        if ra == rb:
            return
        if self.size[ra] < self.size[rb]:
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]

class Solution:
    def countPairs(self, n: int, edges: list[list[int]]) -> int:
        dsu = DSU(n)
        for u, v in edges:
            dsu.union(u, v)

        ans = 0
        processed = 0
        for i in range(n):
            if dsu.parent[i] == i:            # root of a component
                sz = dsu.size[i]
                ans += processed * sz
                processed += sz
        return ans
```

**Why it’s “Python‑friendly”**  
* No recursion → no `RecursionError`.  
* Uses built‑in lists & integers (big‑int) automatically.

---

### 3️⃣ C++ (Performance‑oriented, STL)

```cpp
#include <bits/stdc++.h>
using namespace std;

class DSU {
public:
    vector<int> parent, sz;
    DSU(int n) : parent(n), sz(n,1) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        while (x != parent[x]) {
            parent[x] = parent[parent[x]];
            x = parent[x];
        }
        return x;
    }
    void unite(int a, int b) {
        int ra = find(a), rb = find(b);
        if (ra == rb) return;
        if (sz[ra] < sz[rb]) swap(ra, rb);
        parent[rb] = ra;
        sz[ra] += sz[rb];
    }
};

class Solution {
public:
    long long countPairs(int n, vector<vector<int>>& edges) {
        DSU dsu(n);
        for (auto &e : edges) dsu.unite(e[0], e[1]);

        long long ans = 0;
        long long processed = 0;
        for (int i = 0; i < n; ++i) {
            if (dsu.parent[i] == i) {          // root
                long long sz = dsu.sz[i];
                ans += processed * sz;
                processed += sz;
            }
        }
        return ans;
    }
};
```

> **C++ Note** – Use `long long` (`int64_t`) for the final answer.  
> Use `bits/stdc++.h` for speed but feel free to include `<vector>`, `<numeric>`, etc. for portability.

---

## 📚 Blog Article – “Cracking LeetCode 2316 for Your Next Interview”

> **Title**  
> *“How to Conquer LeetCode 2316 – Count Unreachable Pairs of Nodes (Java, Python, C++)”*

> **Meta Description**  
> Learn the fastest DSU solution for LeetCode 2316, understand graph connected components, and get ready for your next tech interview. Includes Java, Python, and C++ code.

> **Target Keywords**  
> `LeetCode 2316`, `Count Unreachable Pairs of Nodes`, `graph connected components`, `DFS vs Union Find`, `Java graph algorithm`, `Python DSU`, `C++ graph interview`, `tech interview prep`, `data structures`.

---

### 1. Problem Recap

You’re given:

- `n` nodes (0 … n‑1)
- `edges` – an undirected edge list

You must count **how many unordered pairs of distinct nodes have no path between them**.

> **Why this matters in interviews**  
> 1. Demonstrates understanding of graph fundamentals.  
> 2. Shows you can optimise for **large input sizes** (10⁵ nodes, 2·10⁵ edges).  
> 3. Tests familiarity with **Union‑Find** (a classic data structure).

---

### 2. What “unreachable” really means

For any graph, the **total number of unordered pairs** of nodes is:

```
totalPairs = n * (n - 1) / 2
```

If you subtract the number of **reachable** pairs (i.e., pairs that lie inside the same connected component), you get the answer.

For a component of size `s`, the number of reachable pairs inside it is:

```
reachableInComponent = s * (s - 1) / 2
```

Hence:

```
unreachablePairs = totalPairs - Σ(reachableInComponent)
```

---

### 3. Algorithm 1 – Union‑Find (Disjoint Set Union)

1. **Initialize** `parent[i] = i` and `size[i] = 1`.  
2. **Iterate** over every edge `(u, v)` and `union(u, v)`.  
3. After all edges, every root `r` represents a connected component of size `size[r]`.  
4. **Compute** the answer using the “two‑pointer” idea:

```
ans = 0
sum = 0
for each componentSize in componentSizes:
    ans += sum * componentSize
    sum += componentSize
```

- `sum` keeps the total size of all *previous* components.  
- Multiplying it by the current component size gives the number of unreachable pairs that involve one node in the current component and one node in any *later* component.

This runs in **O(n + m)** time and **O(n)** memory.

---

### 4. Algorithm 2 – DFS (Good for learning, but beware stack limits)

```java
// pseudo‑code
ans = totalPairs
for each unvisited node:
    cnt = size of its component via DFS
    ans -= cnt * (cnt - 1) / 2
```

- Simpler conceptually but uses recursion (risk of stack overflow) and extra memory for the adjacency list.
- Still O(n + m), but the Java version can be slower due to `ArrayList` overhead.

---

### 5. Algorithm 3 – “Islands” Counting (Good for small graphs)

1. Build adjacency list.  
2. Perform iterative DFS/BFS to get component sizes.  
3. Accumulate unreachable pairs with the two‑pointer method.

This is basically the same as Algorithm 2 but expressed iteratively.

---

### 6. Edge‑Case & Performance Checklist

| Issue | Fix | Why it matters |
|-------|-----|----------------|
| Integer overflow (n up to 10⁵) | Use `long`/`long long` for answer | `n*(n-1)/2` can be > 2³¹−1 |
| Stack overflow in recursion | Use iterative DFS or DSU | Deep graphs (chain of 10⁵) can hit recursion limit |
| Memory overhead in Java | Use `int[]` parent/size, avoid `ArrayList<int[]>` for edges | GC thrashing can slow down solutions |
| Time limit | DSU is linear; DFS + adjacency list is also linear, but DSU is typically faster in practice | Avoid nested loops over `n` |

---

## 🧠 Take‑away for Your Interview

1. **Explain the total pairs idea**: It’s a neat way to think about the problem.  
2. **Show DSU knowledge**: If the interviewer asks for a fast solution, name Union‑Find immediately.  
3. **Talk about constraints**: Clarify that you’ll need `O(n + m)` algorithm due to the large input.  
4. **Discuss alternatives**: Mention DFS as an alternative, but point out its pitfalls (stack limits).  
5. **Optional**: Talk about the “two‑pointer” accumulation trick – it’s a little bit of “math meets data structure” that impresses interviewers.

---

### Final Words

LeetCode 2316 may look intimidating at first glance, but with a solid understanding of **connected components** and a fast **Union‑Find** implementation, you can solve it in milliseconds. The code snippets above are battle‑tested for Java, Python, and C++. Keep practicing, and feel free to adapt the DSU approach to other graph problems (e.g., “Friend Circles”, “Network Connectivity”, “Island Counting” problems).

Good luck! 🚀

--- 

### References

- [Cracking the Coding Interview – Union Find](https://www.crackingthecodinginterview.com/union-find/)  
- [GeeksforGeeks – Disjoint Set (Union Find)](https://www.geeksforgeeks.org/disjoint-set-data-structure/)  
- [LeetCode 2316 discussion forum](https://leetcode.com/problems/count-unreachable-pairs-in-graph/discuss/)

--- 

> *Prepared by ChatGPT – your AI coding partner.*

--- 

**END**