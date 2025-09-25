---
title: LeetCode 3493. Properties Graph - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Three‑Language Solutions

Below are **ready‑to‑copy** solutions for LeetCode 3493 – “Properties Graph”.
They all use the same idea:

1. **Build a bit‑set (or hash‑set) for each row** so that we can count common elements in *O(1)* (or *O(m)*).  
2. **Union‑Find (Disjoint‑Set Union, DSU)** keeps track of connected components.  
3. For every pair `(i , j)` we merge the two nodes if their intersection ≥ `k`.  
4. Finally count how many unique roots exist – that’s the answer.

The implementations below follow the constraints (`n , m ≤ 100`, values ≤ 100) and are heavily commented so you can paste them straight into LeetCode.

---

### 1.1  Java – “BitSet + DSU”

```java
import java.util.BitSet;

class DSU {
    int[] parent, rank;

    DSU(int n) {
        parent = new int[n];
        rank   = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
    }

    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]); // path compression
        return parent[x];
    }

    void union(int a, int b) {
        int ra = find(a), rb = find(b);
        if (ra == rb) return;
        if (rank[ra] < rank[rb]) parent[ra] = rb;
        else if (rank[ra] > rank[rb]) parent[rb] = ra;
        else { parent[rb] = ra; rank[ra]++; }
    }
}

public class Solution {
    public int numberOfComponents(int[][] prop, int k) {
        int n = prop.length;
        // 101 is enough: values are in [1, 100]
        BitSet[] rows = new BitSet[n];

        // Step 1 – turn every row into a BitSet
        for (int i = 0; i < n; i++) {
            rows[i] = new BitSet(101);
            for (int val : prop[i]) rows[i].set(val);
        }

        DSU dsu = new DSU(n);

        // Step 2 – examine every unordered pair
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                // Count common interests quickly:
                BitSet tmp = (BitSet) rows[i].clone();
                tmp.and(rows[j]);                 // intersection
                if (tmp.cardinality() >= k) {      // if ≥ k merge
                    dsu.union(i, j);
                }
            }
        }

        // Step 3 – count distinct roots
        int comps = 0;
        for (int i = 0; i < n; i++)
            if (dsu.find(i) == i) comps++;
        return comps;
    }
}
```

> **Why BitSet?**  
> - All values are ≤ 100 → 101 bits → a Java `BitSet` fits in one machine word.  
> - `cardinality()` is a native popcount; intersection is `O(1)`.

---

### 1.2  Python – “Set Intersection + DSU”

```python
class DSU:
    def __init__(self, n):
        self.parent = list(range(n))
        self.rank   = [0] * n

    def find(self, x):
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])   # path compression
        return self.parent[x]

    def union(self, a, b):
        ra, rb = self.find(a), self.find(b)
        if ra == rb: return
        if self.rank[ra] < self.rank[rb]:
            self.parent[ra] = rb
        elif self.rank[ra] > self.rank[rb]:
            self.parent[rb] = ra
        else:
            self.parent[rb] = ra
            self.rank[ra] += 1

class Solution:
    def numberOfComponents(self, prop: List[List[int]], k: int) -> int:
        n = len(prop)
        # Convert every row to a *set* – set‑intersection is O(min(|a|,|b|))
        rows = [set(row) for row in prop]

        dsu = DSU(n)

        for i in range(n):
            for j in range(i + 1, n):
                common = len(rows[i] & rows[j])   # set intersection
                if common >= k:
                    dsu.union(i, j)

        # Count distinct roots
        roots = {dsu.find(i) for i in range(n)}
        return len(roots)
```

> **Why a `set`?**  
> The values are tiny, so the overhead of a `BitSet` is unnecessary in Python.  
> The `set` intersection is clean, easy to read, and fast enough for `n ≤ 100`.

---

### 1.3  C++ – “`std::bitset` + DSU”

```cpp
#include <bits/stdc++.h>
using namespace std;

class DSU {
public:
    vector<int> parent, rank;
    DSU(int n) : parent(n), rank(n,0) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]); // path compression
        return parent[x];
    }
    void unite(int a, int b) {
        int ra = find(a), rb = find(b);
        if (ra == rb) return;
        if (rank[ra] < rank[rb]) parent[ra] = rb;
        else if (rank[ra] > rank[rb]) parent[rb] = ra;
        else { parent[rb] = ra; ++rank[ra]; }
    }
};

class Solution {
public:
    int numberOfComponents(vector<vector<int>>& prop, int k) {
        int n = prop.size();
        // 101 bits are enough – values are 1..100
        vector<bitset<128>> rows(n);
        for (int i = 0; i < n; ++i)
            for (int v : prop[i]) rows[i].set(v);

        DSU dsu(n);

        for (int i = 0; i < n; ++i)
            for (int j = i + 1; j < n; ++j) {
                int common = (rows[i] & rows[j]).count();  // popcount
                if (common >= k) dsu.unite(i, j);
            }

        // Count distinct roots
        int comps = 0;
        for (int i = 0; i < n; ++i)
            if (dsu.find(i) == i) ++comps;
        return comps;
    }
};
```

> **Why `bitset<128>`?**  
> - 128 bits comfortably hold the 100 possible values.  
> - `operator&` and `count()` are implemented in hardware (Hamming weight), giving *O(1)* per pair.  

---

## 2.  The “Good, the Bad, and the Ugly”

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Conceptual Clarity** | Union‑Find is a classic interview staple. | Counting intersections naïvely can be hard to reason about. | Forgetting the *uniqueness* of elements (duplicates) leads to wrong answers. |
| **Implementation Simplicity** | DSU + BitSet/Set is 5‑line logic. | Python set intersection is a single line of code. | Using raw arrays and manual popcount can be error‑prone. |
| **Performance** | Bit operations give *O(n²)* with a tiny constant. | Python’s `set.intersection` is `O(m)` but still < 10⁶ operations for constraints. | Manual loops over 100 numbers per pair are perfectly fine but harder to read. |
| **Readability** | Java `BitSet` + DSU is self‑documenting. | Python sets are almost self‑explanatory. | C++ `bitset<128>` may feel a bit “low‑level” for newcomers. |
| **Edge Cases** | Values outside 1–100 would break bit‑set logic – guard with checks. | Duplicates in a row are harmless because we use `set`. | DSU rank/size heuristics are essential to avoid degenerate trees. |

> **Bottom line:**  
>  The *good* is a fast, clean algorithm.  
>  The *bad* is the temptation to write a heavy‑weight intersection routine.  
>  The *ugly* is missing DSU optimizations (path compression, union by rank), which can turn an *O(n² α(n))* solution into a slow one on bigger inputs.

---

## 3.  SEO‑Optimized Blog Post

> **Title**  
> **Properties Graph: Counting Connected Components with DSU – The Good, The Bad, & The Ugly**  

> **Meta description**  
> Master LeetCode’s “Properties Graph” with an interview‑ready Union‑Find solution. Learn how to optimize intersection counts with bitsets, troubleshoot common pitfalls, and ace your next coding interview.

> **Primary Keywords**  
> `LeetCode 3493`, `properties graph`, `union find`, `DSU algorithm`, `interview question`, `job interview coding`, `algorithm interview prep`, `data structure`, `graph theory`, `C++ bitset`, `Java BitSet`, `Python set intersection`

> **Secondary Keywords**  
> `coding interview`, `software engineering interview`, `tech hiring`, `algorithmic problem solving`, `graph connectivity`

---

### Introduction

> In modern tech interviews, LeetCode problems that blend *graphs* and *data structures* are gold mines for showcasing algorithmic thinking.  
> LeetCode 3493 – *Properties Graph* – asks you to model user profiles as nodes, link them when they share enough interests, and count how many independent “friend circles” exist.  
> Below we dissect the problem, show an optimal solution, and give you a quick cheat‑sheet for the interview day.

---

### Problem Recap

- **Input**  
  `prop` – an `n × m` integer matrix (`1 ≤ n, m ≤ 100`, values ≤ 100).  
  `k` – the minimum number of *common* attributes required to connect two rows.  
- **Graph construction**  
  Nodes: `0 … n‑1`.  
  Edge `(i, j)` exists iff `|prop[i] ∩ prop[j]| ≥ k`.  
- **Goal**  
  Return the number of connected components in this undirected graph.

---

### Why Union‑Find?

1. **Natural fit for “merge if connected”** – once we see an edge, we join two components.  
2. **Amortised `α(n)` (inverse Ackermann) time** – virtually constant for practical input sizes.  
3. **Classic interview skill** – recruiters often ask you to explain DSU; having a working solution demonstrates depth.

---

### Optimizing Intersection Counting

The naive approach would scan both rows for each pair, costing `O(n² m)`.  
Given the tiny value range, we can do better:

| **Language** | **Technique** | **Explanation** |
|--------------|---------------|-----------------|
| **C++ / Java** | `std::bitset` / `BitSet` | Represent each row as a bitmask of 101 bits. Intersection is a bitwise AND, cardinality a popcount. |
| **Python** | `set` | `row & other` is a set intersection; Python’s built‑in hash sets give excellent constant‑time behavior for small sets. |

> **Tip**: In Python, if you want maximum speed and you know `m` is large, you can still use `array('I')` and bit‑pack the values.  

---

### Full Solution Walkthrough (C++)

```cpp
vector<bitset<128>> rows(n);
for (int i=0;i<n;i++)
    for (int v: prop[i]) rows[i].set(v);  // set bit v

DSU dsu(n);
for (int i=0;i<n;i++)
    for (int j=i+1;j<n;j++) {
        if ((rows[i] & rows[j]).count() >= k)
            dsu.unite(i,j);
    }
```

- **`rows[i] & rows[j]`** – hardware‑accelerated intersection.  
- **`.count()`** – popcount; O(1).  
- **DSU** – path compression + union by rank guarantees almost linear behaviour.

---

### Common Pitfalls & How to Avoid Them

| **Pitfall** | **Effect** | **Fix** |
|-------------|------------|---------|
| **Duplicate attributes in a row** | Over‑counts intersection | Convert each row to a *set* (Python) or use `BitSet` (duplicates don't matter). |
| **Zero‑indexed vs one‑indexed values** | Wrong bit position | Always offset by 1 when using `BitSet` / `bitset`. |
| **Missing DSU rank** | O(n²) worst‑case on bad inputs | Always implement union by rank or size. |
| **Using slow loops** | Timeouts on larger tests | Replace `for` loops over 100 values with bitset intersection or set intersection. |

---

### Quick Interview Cheat‑Sheet

- **Data structures to mention**  
  - `UnionFind (DSU)`  
  - `BitSet` (Java/C++) or `set` (Python)  
- **Time complexity**  
  `O(n²)` edges *popcount* → ~10⁴ operations for `n=100`.  
- **Space complexity**  
  `O(n)` for DSU + `O(n * bitset)` ≈ `O(n)` bits → negligible.  
- **Edge‑Case Note**  
  - Empty matrix? Return `0`.  
  - `k == 0` → every node connected → 1 component.  

---

### Final Thought

> Mastering *Properties Graph* means mastering the synergy between *graph connectivity* and *efficient merging*.  
> If you can explain this solution in under 5 minutes and code it flawlessly, you’ll impress interviewers across the tech spectrum – from startups to Fortune 500.

---

### FAQ

**Q1. Can I use recursion in DSU `find`?**  
- In interviews, an iterative approach is safer – avoids stack overflow on deep recursion.

**Q2. Why not use BFS/DFS?**  
- BFS/DFS would work, but you’d need to build all edges first.  
- DSU merges on the fly and uses *O(n)* memory, while DFS would use `O(n + edges)`.

**Q3. What if `k` is large, say 50?**  
- The algorithm still works; intersection counts naturally shrink the number of unions.

---

### Takeaway

> The Properties Graph problem is an excellent showcase of DSU’s power in a graph context.  
> By using bitset‑based intersections, you guarantee a blazing‑fast solution that scales comfortably.  
> Next time you’re asked a “connect profiles” question, remember: **merge if connected → DSU**.

---

**Happy coding, and may your interview be filled with clean algorithms and zero bugs!**

--- 

*End of post* 

---

> **Feel free to copy the code snippets above** – they’re ready to paste into LeetCode, Codeforces, or your local IDE. Good luck on your next interview!