---
title: LeetCode 1101. The Earliest Moment When Everyone Become Friends - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏗️ The Earliest Moment When Everyone Becomes Friends – 1101 (LeetCode)

| Language | Time | Space | ✅  |
|----------|------|-------|-----|
| **Java** | O(m log m) | O(n+m) | ✔ |
| **Python** | O(m log m) | O(n+m) | ✔ |
| **C++** | O(m log m) | O(n+m) | ✔ |

> **Key Insight** – Use **Union‑Find (Disjoint‑Set Union)** to merge friendship groups in chronological order.  
> When the size of the group that contains any member equals *n*, everybody is connected.

> **Why Union‑Find?**  
> • Fast merges (`α(n)` time)  
> • Simple to maintain the count of connected components  
> • Perfect for “when did the whole network become connected” type questions

---

## 📚 Problem Statement

> There are **n** people labeled `0 … n‑1`.  
> `logs[i] = [timestampᵢ, xᵢ, yᵢ]` means that at time `timestampᵢ`, people `xᵢ` and `yᵢ` become friends.  
> Friendship is transitive – if `a` is a friend of `b`, and `b` is a friend of `c`, then `a` is acquainted with `c`.  
> **Return the earliest timestamp at which every person is acquainted with every other person.**  
> If that never happens, return `-1`.

> **Constraints**  
> * `2 ≤ n ≤ 100`  
> * `1 ≤ logs.length ≤ 10⁴`  
> * All `timestampᵢ` are unique and `0 ≤ timestampᵢ ≤ 10⁹`  
> * Each pair `(xᵢ, yᵢ)` appears at most once

---

## ⚙️ Algorithm – Union‑Find (Disjoint Set Union)

1. **Sort the logs by timestamp** – we need to process friendships chronologically.  
2. **Initialize a DSU** with `n` nodes.  
   * `parent[i] = i`  
   * `size[i] = 1` (number of nodes in the component)
3. **Process each log** in sorted order:
   * Union the two people `x` and `y`.  
   * After a successful union (i.e., they were previously in different components), check if the size of the new component equals `n`.  
   * If yes, return the current `timestamp`.  
4. If the loop ends without reaching a size of `n`, return `-1`.

---

### Pseudocode

```
sort(logs by timestamp)
init DSU(n)
for each [t, x, y] in logs:
    if union(x, y):
        if componentSize(x) == n:
            return t
return -1
```

---

## 📈 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Sorting logs | **O(m log m)** (`m = logs.length`) | O(m) |
| Union‑Find operations | **O(m α(n))** (α is inverse Ackermann, practically constant) | O(n) |
| Total | **O(m log m)** | **O(n+m)** |

With `n ≤ 100` and `m ≤ 10⁴`, this runs in a few milliseconds.

---

## 💡 Edge Cases & Pitfalls

| Issue | Fix |
|-------|-----|
| Duplicate friendships? | Problem guarantees unique pairs. |
| Already connected pair? | `union` returns `false`; no size change. |
| All people already connected before any log? | Not possible because no friendships exist initially. |
| Very large timestamps? | Use 64‑bit (`long` / `int64_t` / `int` in Python). |

---

## 🛠️ Code – Java (Union‑Find)

```java
import java.util.Arrays;

class Solution {
    // ---------- DSU ----------
    private static class DSU {
        int[] parent;
        int[] size;
        DSU(int n) {
            parent = new int[n];
            size   = new int[n];
            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i]   = 1;
            }
        }
        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]); // path compression
            return parent[x];
        }
        // returns true if merge happened
        boolean union(int a, int b) {
            int pa = find(a), pb = find(b);
            if (pa == pb) return false;
            // union by size
            if (size[pa] < size[pb]) {
                int tmp = pa; pa = pb; pb = tmp;
            }
            parent[pb] = pa;
            size[pa] += size[pb];
            return true;
        }
        int compSize(int x) { return size[find(x)]; }
    }
    // ---------- solution ----------
    public int earliestAcq(int[][] logs, int n) {
        // 1️⃣ sort by timestamp
        Arrays.sort(logs, (a, b) -> Integer.compare(a[0], b[0]));
        DSU dsu = new DSU(n);
        for (int[] log : logs) {
            int t = log[0], x = log[1], y = log[2];
            if (dsu.union(x, y) && dsu.compSize(x) == n) {
                return t;          // everyone connected
            }
        }
        return -1;                 // never fully connected
    }
}
```

---

## 🐍 Code – Python (Union‑Find)

```python
from typing import List

class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.size   = [1] * n

    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])  # path compression
        return self.parent[x]

    def union(self, a: int, b: int) -> bool:
        pa, pb = self.find(a), self.find(b)
        if pa == pb:                     # already in same set
            return False
        if self.size[pa] < self.size[pb]:  # union by size
            pa, pb = pb, pa
        self.parent[pb] = pa
        self.size[pa] += self.size[pb]
        return True

    def comp_size(self, x: int) -> int:
        return self.size[self.find(x)]

class Solution:
    def earliestAcq(self, logs: List[List[int]], n: int) -> int:
        logs.sort(key=lambda x: x[0])  # 1️⃣ chronological order
        dsu = DSU(n)
        for t, x, y in logs:
            if dsu.union(x, y) and dsu.comp_size(x) == n:
                return t
        return -1
```

---

## 🏗️ Code – C++ (Union‑Find)

```cpp
#include <vector>
#include <algorithm>

class DSU {
public:
    DSU(int n) : parent(n), sz(n, 1) {
        for (int i = 0; i < n; ++i) parent[i] = i;
    }
    int find(int x) {
        if (parent[x] != x) parent[x] = find(parent[x]); // path compression
        return parent[x];
    }
    bool unite(int a, int b) {
        int pa = find(a), pb = find(b);
        if (pa == pb) return false;                     // already connected
        if (sz[pa] < sz[pb]) std::swap(pa, pb);        // union by size
        parent[pb] = pa;
        sz[pa] += sz[pb];
        return true;
    }
    int compSize(int x) const { return sz[find(x)]; }

private:
    std::vector<int> parent, sz;
};

class Solution {
public:
    int earliestAcq(std::vector<std::vector<int>>& logs, int n) {
        std::sort(logs.begin(), logs.end(),
                  [](const std::vector<int>& a,
                     const std::vector<int>& b) {
                      return a[0] < b[0];
                  });                            // 1️⃣ sort by timestamp
        DSU dsu(n);
        for (const auto& e : logs) {
            int t = e[0], x = e[1], y = e[2];
            if (dsu.unite(x, y) && dsu.compSize(x) == n)
                return t;                           // all connected
        }
        return -1;                                 // never fully connected
    }
};
```

---

## 🔀 Alternative – Depth‑First Search (DFS)

If you prefer a more “brute‑force” approach:

1. **Build an adjacency list** from the logs as you go.  
2. After each union, **run a DFS** from any member to count how many nodes are reachable.  
3. When that count equals `n`, return the timestamp.

> **Why not use DFS?**  
> * Easier to understand for beginners  
> * Time‑complexity: O(m·n) in the worst case – still fine for the given constraints  
> * The DSU version is cleaner for interviewers and scales to larger `n`

---

## 🎤 Interview Talk‑Point

- **Explain the “transitivity”** early – many candidates forget it.  
- **Show the DSU helper** clearly: `find`, `unite`, `size`.  
- **Mention sorting** – a common gotcha that makes the solution O(m log m) instead of O(m).  
- **Talk about path compression & union by size** – interviewers appreciate the nuance.  
- **Explain why you return after a successful union** – not every `union` changes the component size.  

> **STAR Tip** – “*While merging friendship groups, I used Union‑Find so that each union takes almost constant time. As soon as the size of the component reaches n, I know everyone is connected. I sorted the logs by timestamp to keep the chronological invariant.*”

---

## 🚀 Job‑Interview Takeaway

1. **DSU is a “magic bullet”** for “connectivity over time” problems.  
2. **Keep the component size** inside the DSU; otherwise you’d need a separate counter.  
3. **Avoid O(n²) DFS after each log** – the DSU is orders of magnitude faster.  
4. **Show clean code** – variable names like `parent`, `size`, `union`/`unite`, `find`.  
5. **Mention edge‑case handling** (duplicate pairs, already connected pair) – demonstrates robustness.

> **If you nail this problem, you’ll ace the “graph connectivity” segment in almost any systems‑design or data‑structures interview.** 🎯

---

## 🎉 Final Thought

> The “earliest moment everyone becomes friends” problem is a textbook Union‑Find question.  
> Master the DSU pattern and you’ll be ready for any interview that asks *“when did this graph become connected?”*  
> And the best part? The solution is only a few lines of code – perfect for your LeetCode portfolio and your next job‑hunt! 🚀

Happy coding, and good luck landing that dream tech role!