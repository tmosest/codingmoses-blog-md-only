---
title: LeetCode 2508. Add Edges to Make Degrees of All Nodes Even - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2508 – Add Edges to Make Degrees of All Nodes Even  
**LeetCode Hard | Graph Theory | Interview Algorithm**  

> If you’re preparing for an algorithm interview, this LeetCode problem is a must‑do.  
> It tests your ability to think in *parity* and *connectivity* while staying within strict time & space limits.  
> Below you’ll find a full solution in **Java**, **Python**, and **C++**, plus an in‑depth blog‑style walk‑through that you can drop into a portfolio or a LinkedIn post to impress recruiters.

---

## 🔍 Problem Recap

- **Graph**: Undirected, up to `10⁵` nodes and `10⁵` edges.  
- **Goal**: Add *at most two* new edges (no self‑loops, no duplicates) so that every node has an **even** degree.  
- **Return**: `true` if it’s possible, otherwise `false`.

> 👉 **Key observation** – Only nodes with odd degree matter.  
> The total sum of degrees is always even, so the number of odd‑degree nodes is even.

---

## 📌 Solution Intuition (The “Good, the Bad, the Ugly”)

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Algorithmic core** | Simple case‑by‑case logic (0, 2, or 4 odd nodes) | Must check *absence* of an edge, not just existence | Careful handling of large `n` while checking all potential “third” nodes can be tricky |
| **Complexity** | `O(n + m)` time, `O(n + m)` memory | No extra log‑factor; we don’t need heavy data structures | None – the logic is O(1) for the odd‑node count |
| **Coding** | Straightforward loops + adjacency sets | Avoid using `Vector<Set>` in Java (costly) | Need to guard against integer overflow in C++ sets (use `int` only) |
| **Edge cases** | 0 odd nodes → already valid | 2 odd nodes that are already adjacent | 4 odd nodes that can’t be paired because one edge exists in each pairing |

---

## 🧩 Detailed Algorithm

1. **Build adjacency sets** – `O(m)`  
   * Allows constant‑time `edgeExists(u, v)` look‑ups.*

2. **Collect odd‑degree nodes** – `O(n)`  
   * `odd = [v for v in 1..n if degree(v) % 2 == 1]`.*

3. **Case analysis**  
   | #odd | What to do | Why |
   |------|------------|-----|
   | **0** | ✅ Return `true`. | Already even. |
   | **2** | Let them be `a, b`.  
        * If `a` and `b` are *not* connected → add that edge → success.  
        * Else search for a `c` that is **not** adjacent to **both** `a` and `b`.  
          If found → add edges `(a, c)` & `(b, c)`. | With two odd nodes we need one or two new edges. |
   | **4** | Let them be `a,b,c,d`.  
        Try all 3 pairings:  
        `(a,b)` & `(c,d)`  
        `(a,c)` & `(b,d)`  
        `(a,d)` & `(b,c)`  
        If at least one pairing has **both** edges missing → success. | Two edges are enough to fix four odd nodes. |
   | **>4** | ❌ Return `false`. | More than two new edges would be required. |

4. **Return** the result.

> **Why does it work?**  
> Adding an edge flips the parity of its two incident nodes.  
> Thus, any solution must pair up the odd nodes with the new edges.  
> Because we can add at most two edges, the maximum number of odd nodes we can fix is four.

---

## 📦 Code Implementations

### 1️⃣ Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def isPossible(self, n: int, edges: List[List[int]]) -> bool:
        # adjacency sets for O(1) edge lookup
        g = defaultdict(set)
        for a, b in edges:
            g[a].add(b)
            g[b].add(a)

        # collect odd‑degree nodes
        odd = [v for v in range(1, n + 1) if len(g[v]) % 2 == 1]

        if not odd:                      # 0 odd nodes
            return True

        if len(odd) == 2:                # 2 odd nodes
            a, b = odd
            if b not in g[a]:
                return True
            # look for a third node that is not adjacent to a nor b
            for c in range(1, n + 1):
                if c in odd: continue
                if a not in g[c] and b not in g[c]:
                    return True
            return False

        if len(odd) == 4:                # 4 odd nodes
            a, b, c, d = odd
            # three possible pairings
            return (
                (b not in g[a] and d not in g[c]) or
                (c not in g[a] and d not in g[b]) or
                (d not in g[a] and c not in g[b])
            )

        # any other number of odd nodes is impossible
        return False
```

---

### 2️⃣ Java

```java
import java.util.*;

class Solution {
    public boolean isPossible(int n, List<List<Integer>> edges) {
        // adjacency sets
        List<Set<Integer>> g = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) g.add(new HashSet<>());
        for (List<Integer> e : edges) {
            int a = e.get(0), b = e.get(1);
            g.get(a).add(b);
            g.get(b).add(a);
        }

        // collect odd-degree nodes
        List<Integer> odd = new ArrayList<>();
        for (int i = 1; i <= n; i++) {
            if (g.get(i).size() % 2 == 1) odd.add(i);
        }

        if (odd.isEmpty()) return true;        // 0 odd

        if (odd.size() == 2) {                  // 2 odd
            int a = odd.get(0), b = odd.get(1);
            if (!g.get(a).contains(b)) return true;
            for (int c = 1; c <= n; c++) {
                if (c == a || c == b) continue;
                if (!g.get(a).contains(c) && !g.get(b).contains(c)) return true;
            }
            return false;
        }

        if (odd.size() == 4) {                  // 4 odd
            int a = odd.get(0), b = odd.get(1),
                c = odd.get(2), d = odd.get(3);
            // try all 3 pairings
            if (!g.get(a).contains(b) && !g.get(c).contains(d)) return true;
            if (!g.get(a).contains(c) && !g.get(b).contains(d)) return true;
            if (!g.get(a).contains(d) && !g.get(b).contains(c)) return true;
            return false;
        }

        return false; // >4 odd nodes
    }
}
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isPossible(int n, vector<vector<int>>& edges) {
        vector<unordered_set<int>> g(n + 1);
        for (auto &e : edges) {
            int a = e[0], b = e[1];
            g[a].insert(b);
            g[b].insert(a);
        }

        vector<int> odd;
        for (int i = 1; i <= n; ++i)
            if (g[i].size() % 2 == 1) odd.push_back(i);

        if (odd.empty()) return true;                 // 0 odd

        if (odd.size() == 2) {                         // 2 odd
            int a = odd[0], b = odd[1];
            if (!g[a].count(b)) return true;
            for (int c = 1; c <= n; ++c) {
                if (c == a || c == b) continue;
                if (!g[a].count(c) && !g[b].count(c)) return true;
            }
            return false;
        }

        if (odd.size() == 4) {                         // 4 odd
            int a = odd[0], b = odd[1],
                c = odd[2], d = odd[3];
            // 3 pairings
            if (!g[a].count(b) && !g[c].count(d)) return true;
            if (!g[a].count(c) && !g[b].count(d)) return true;
            if (!g[a].count(d) && !g[b].count(c)) return true;
            return false;
        }

        return false;                                 // >4 odd nodes
    }
};
```

> **Note**  
> - All implementations use **`O(n + m)`** time and memory.  
> - We use *hash‑sets* (`unordered_set` / `HashSet`) so that `edgeExists` is constant time even when `n` is large.  

---

## 📚 Portfolio‑Friendly Summary

> **Title**: “Parity & Connectivity – A Clean O(n+m) Solution to LeetCode 2508”  
> **Key take‑aways**:  
> * Parity of degrees → only odd nodes matter.  
> * Two new edges can fix at most four odd nodes.  
> * Constant‑time adjacency sets keep the solution linear.  

Feel free to embed the above code in a GitHub repository, a PDF, or even a short video explanation on LinkedIn. Recruiters love seeing a clean, well‑commented solution that demonstrates *both* algorithmic insight and coding hygiene.

---

## 📣 Bonus: SEO‑Friendly Blog Post

> **Meta‑Title**: 2508 – Add Edges to Make Degrees of All Nodes Even (LeetCode Hard)  
> **Meta‑Description**:  
> Master LeetCode 2508 with a fast, linear‑time solution in Java, Python, and C++.  
> Learn the parity trick, case analysis, and why you can’t add more than two edges.  
> Perfect for CS interviews.

> **Target Keywords**:  
> `LeetCode 2508`, `Add Edges to Make Degrees Even`, `Hard Graph Problem`, `Interview Algorithm`, `Parity Graph`, `O(n+m) Graph`, `Java Graph Solution`, `Python Graph Solution`, `C++ Graph Solution`

---

Happy coding, and good luck nailing that interview! 🚀