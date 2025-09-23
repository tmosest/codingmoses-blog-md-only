---
title: LeetCode 1697. Checking Existence of Edge Length Limited Paths - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 LeetCode 1697 – Checking Existence of Edge‑Length Limited Paths  
### The Good, The Bad, and The Ugly | A Deep‑Dive Blog for Job‑Seekers  

> **Target keyword**: *LeetCode 1697 solution* – *union‑find* – *edge‑length limited paths* – *hard problem* – *coding interview*  

If you’re hunting for that “hard” LeetCode problem that will make recruiters sit up and notice you, you’ll want to master **1697. Checking Existence of Edge Length Limited Paths**.  
Below you’ll find:

| ✅ What you’ll learn | 🔍 Why it matters |
|----------------------|-------------------|
| The optimal **union‑find** (disjoint‑set) strategy | O((E+Q) log(E+Q)) is fast enough for 10⁵ edges & queries |
| Sorting tricks to keep answers in the original order | Avoids O(Q²) query processing |
| Handling multi‑edges & large integer limits | Prevents subtle bugs on LeetCode’s test cases |
| Clean, language‑agnostic implementation | Show recruiters you can code in Java, Python, C++ |

---

## 1. Problem Recap

> **Given**  
> * `n` nodes (0‑based).  
> * `edgeList[i] = [ui, vi, disi]` – undirected edge with distance `disi`.  
> * `queries[j] = [pj, qj, limitj]` – ask if a path exists from `pj` to `qj` **with all edges < limitj**.  

> **Return** `boolean[] answer` – one value per query.  

**Constraints** (worst‑case)

* `2 ≤ n ≤ 10⁵`
* `1 ≤ edgeList.length, queries.length ≤ 10⁵`
* `1 ≤ disi, limitj ≤ 10⁹`
* Multiple edges between the same pair of nodes may exist.

---

## 2. Why a Union‑Find (Disjoint‑Set Union) Works

Think of each edge as a “bridge” that becomes available when its distance is **strictly smaller** than the current query’s `limit`.  
If we process all edges in **increasing order of distance**, we can incrementally “connect” the graph:

1. **Sort** all edges by distance.
2. **Sort** all queries by `limit`.  
   (Remember the original index to put the answer back later.)
3. Maintain a DSU structure over the `n` nodes.
4. While the next edge’s distance `< current query’s limit`, union its two endpoints.
5. After all applicable edges are merged, the two nodes of the query are connected iff they belong to the same DSU set.

Because we process queries in ascending `limit`, each edge is considered **once** – giving an overall complexity of  
**O((E + Q) log (E + Q))** for sorting plus **O(E α(n))** for DSU operations (`α` = inverse Ackermann, practically constant).

---

## 3. The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | O((E+Q) log (E+Q)) → fast for 10⁵ | None | – |
| **DSU Implementation** | Path compression + union‑by‑rank → near‑constant amortized | Requires careful index handling (0‑based) | Forgetting to update `parent` in `find` leads to infinite loops |
| **Sorting Queries** | Keeps answers in original order | Requires extra memory for the index | If you sort by `limit` but forget to store original index, you’ll return wrong answers |
| **Multiple Edges** | Handled automatically – we just process them all | No special handling needed | If you mistakenly deduplicate edges, you lose information |
| **Edge & Limit Size** | 32‑bit ints sufficient, but use `long` if you’re paranoid | None | Using `int` when the sum exceeds 2⁻³¹‑1 can cause overflow in other problems |

---

## 4. Full Code – Java, Python, C++

> All three implementations use the same logic.  
> Each file is self‑contained, includes necessary imports, and is ready to paste into your IDE or LeetCode environment.

### 4.1 Java

```java
import java.util.*;

// ---------- DSU (Disjoint Set Union) ----------
class DSU {
    private final int[] parent;
    private final int[] rank;  // union by rank

    DSU(int n) {
        parent = new int[n];
        rank   = new int[n];
        for (int i = 0; i < n; ++i) parent[i] = i;
    }

    int find(int x) {
        // Path compression
        if (parent[x] != x) parent[x] = find(parent[x]);
        return parent[x];
    }

    void union(int x, int y) {
        int rx = find(x);
        int ry = find(y);
        if (rx == ry) return;

        // Union by rank
        if (rank[rx] < rank[ry]) parent[rx] = ry;
        else if (rank[rx] > rank[ry]) parent[ry] = rx;
        else {
            parent[ry] = rx;
            rank[rx]++;
        }
    }
}

// ---------- Main Solution ----------
public class Solution {
    public boolean[] distanceLimitedPathsExist(int n,
                                                int[][] edgeList,
                                                int[][] queries) {
        // Attach original indices to queries
        int m = queries.length;
        int[][] qWithIdx = new int[m][4];
        for (int i = 0; i < m; ++i) {
            qWithIdx[i][0] = queries[i][0];
            qWithIdx[i][1] = queries[i][1];
            qWithIdx[i][2] = queries[i][2];
            qWithIdx[i][3] = i;          // original position
        }

        // Sort edges by distance
        Arrays.sort(edgeList, Comparator.comparingInt(a -> a[2]));

        // Sort queries by limit
        Arrays.sort(qWithIdx, Comparator.comparingInt(a -> a[2]));

        DSU dsu = new DSU(n);
        boolean[] ans = new boolean[m];
        int eIdx = 0;                    // pointer over sorted edges

        for (int[] q : qWithIdx) {
            int limit = q[2];
            // Add all edges with distance < limit
            while (eIdx < edgeList.length && edgeList[eIdx][2] < limit) {
                dsu.union(edgeList[eIdx][0], edgeList[eIdx][1]);
                eIdx++;
            }
            // If both nodes are connected, answer is true
            ans[q[3]] = dsu.find(q[0]) == dsu.find(q[1]);
        }
        return ans;
    }
}
```

> **How to run**  
> *Paste the `Solution` class into LeetCode, then call `distanceLimitedPathsExist(...)`.*  

---

### 4.2 Python (Python 3)

```python
from typing import List

# ---------- DSU ----------
class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank = [0] * n

    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])   # Path compression
        return self.parent[x]

    def union(self, x: int, y: int) -> None:
        xr, yr = self.find(x), self.find(y)
        if xr == yr: return
        if self.rank[xr] < self.rank[yr]:
            self.parent[xr] = yr
        elif self.rank[xr] > self.rank[yr]:
            self.parent[yr] = xr
        else:
            self.parent[yr] = xr
            self.rank[xr] += 1

# ---------- Main ----------
class Solution:
    def distanceLimitedPathsExist(
        self, n: int, edgeList: List[List[int]], queries: List[List[int]]
    ) -> List[bool]:
        # Attach original index to each query
        q_with_idx = [q + [i] for i, q in enumerate(queries)]

        # Sort edges & queries
        edgeList.sort(key=lambda x: x[2])
        q_with_idx.sort(key=lambda x: x[2])

        dsu = DSU(n)
        ans = [False] * len(queries)
        e_idx = 0

        for q in q_with_idx:
            limit = q[2]
            while e_idx < len(edgeList) and edgeList[e_idx][2] < limit:
                dsu.union(edgeList[e_idx][0], edgeList[e_idx][1])
                e_idx += 1
            ans[q[3]] = dsu.find(q[0]) == dsu.find(q[1])

        return ans
```

> **Run**  
> *Upload the `Solution` class to LeetCode and run.*  

---

### 4.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

// ---------- DSU ----------
class DSU {
public:
    vector<int> parent, rnk;
    DSU(int n) : parent(n), rnk(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        return parent[x] == x ? x : parent[x] = find(parent[x]); // path compression
    }
    void unite(int x, int y) {
        int xr = find(x), yr = find(y);
        if (xr == yr) return;
        if (rnk[xr] < rnk[yr]) parent[xr] = yr;
        else if (rnk[xr] > rnk[yr]) parent[yr] = xr;
        else {
            parent[yr] = xr;
            ++rnk[xr];
        }
    }
};

// ---------- Main ----------
class Solution {
public:
    vector<bool> distanceLimitedPathsExist(int n,
                                           vector<vector<int>>& edgeList,
                                           vector<vector<int>>& queries) {
        int m = queries.size();
        // store queries as [u, v, limit, original_idx]
        vector<array<int,4>> qs;
        qs.reserve(m);
        for (int i = 0; i < m; ++i)
            qs.push_back({queries[i][0], queries[i][1], queries[i][2], i});

        sort(edgeList.begin(), edgeList.end(),
             [](const auto &a, const auto &b){ return a[2] < b[2]; });

        sort(qs.begin(), qs.end(),
             [](const auto &a, const auto &b){ return a[2] < b[2]; });

        DSU dsu(n);
        vector<bool> ans(m);
        size_t eIdx = 0;

        for (auto &q : qs) {
            int limit = q[2];
            while (eIdx < edgeList.size() && edgeList[eIdx][2] < limit) {
                dsu.unite(edgeList[eIdx][0], edgeList[eIdx][1]);
                ++eIdx;
            }
            ans[q[3]] = (dsu.find(q[0]) == dsu.find(q[1]));
        }
        return ans;
    }
};
```

> **Compile**  
> *`g++ -std=c++17 -O2 solution.cpp && ./a.out`*  

---

## 5. How Recruiters Will Judge Your Submission

1. **Code quality** – All three languages use clear naming, in‑line comments, and no magic constants.  
2. **Edge‑case handling** – The solutions correctly handle **multi‑edges** and **large limits** (no integer overflow).  
3. **Time complexity** – The interview will notice you’re not doing a naive BFS/DFS for each query.  
4. **Language versatility** – You can present all three solutions in an interview or technical test.  

---

## 6. Next Steps: Polish Your Interview Skills

| ✅ Resource | 📖 Suggested read |
|-------------|-------------------|
| *Graph algorithms* | *Union‑Find* in *Cracking the Coding Interview* |
| *LeetCode discuss* | Thread on *1697 hard* for extra edge cases |
| *C++ STL* | `iota`, `sort`, `vector` |
| *Python* | `functools.lru_cache` for memoizing `find` if you prefer recursion |
| *Java* | `Arrays.sort` + `Comparator.comparingInt` |

---  

### 🎯 Final Thought

With this **union‑find** solution, you can solve LeetCode 1697 in under a second on the hardest input.  
Show recruiters you can translate a slick algorithm into clean code across **Java, Python, C++**, and you’ll be a step closer to that dream interview.  

Good luck, and happy coding! 🚀

--- 

**Feel free to copy/paste any of the three implementations above, run them locally, and brag on your interview day!**