---
title: LeetCode 3004. Maximum Subtree of the Same Color - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3004 – “Maximum Subtree of the Same Color”  
**Solution in Java | Python | C++**  
**+ A full‑blown SEO‑friendly blog post that tells the story of the “Good, the Bad & the Ugly”**

---

### TL;DR  

* **Goal** – Find the largest subtree (rooted at any node) where **every node has the same color**.  
* **Idea** – DFS + back‑tracking.  
* **Complexity** – `O(n)` time, `O(n)` space (recursion stack).  
* **Code** – 3 ready‑to‑paste implementations (Java, Python, C++).  

---

## 1. Problem Recap  

> **LeetCode 3004 – Maximum Subtree of the Same Color**  
> You’re given a rooted tree (node 0 is the root).  
> `edges[i] = [u, v]` denotes an undirected edge.  
> `colors[i]` is the color of node i.  
> Return the size of the largest subtree whose nodes are all the same color.

| Example | Output |
|---------|--------|
| `edges = [[0,1],[0,2],[0,3]], colors = [1,1,2,3]` | `1` |
| `edges = [[0,1],[0,2],[0,3]], colors = [1,1,1,1]` | `4` |
| `edges = [[0,1],[0,2],[2,3],[2,4]], colors = [1,2,3,3,3]` | `3` |

---

## 2. Intuition & Why DFS Works  

A subtree is *homogeneous* only if every child subtree is also homogeneous **and** shares the same color as the root.  
During DFS we can **return two pieces of information** for each node:

1. **Is the subtree rooted here homogeneous?**  
   * If yes → size of the subtree (for potential answer).  
   * If no  → `-1` (means “break the chain”).
2. **Maximum homogeneous subtree seen so far in the recursive call**.

When a parent processes a child:

* If the child’s subtree is homogeneous *and* the child’s color equals the parent’s, we can safely merge their sizes.  
* Otherwise the parent’s subtree becomes “mixed” (`-1`).  

This **back‑tracking** guarantees we inspect every node only once → `O(n)`.

---

## 3. The “Good, the Bad & the Ugly”

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Algorithm** | Simple DFS, intuitive, linear time | Recursion depth can hit stack limit for 5×10⁴ nodes | None if implemented with iterative stack |
| **Space** | O(n) adjacency list + O(n) recursion stack | Recursion stack may blow up on degenerate trees | Use explicit stack to avoid recursion |
| **Code readability** | Very compact, two‑value array return | Two‑value array can be confusing | Some implementations use global vars which hurt clarity |
| **Edge cases** | Works for single node, all same color, all different | Must handle root with no children | None if logic is correct |

---

## 4. Reference Implementations  

Below you’ll find three polished implementations.  
All share the same core idea but are idiomatic to their language.

> **Tip**: The adjacency list is built in `O(n)` time.  
> DFS is implemented recursively (feel free to convert to iterative if stack depth is a concern).  
> The returned pair `[subtreeSize, maxSize]` is packed as an array / tuple / struct.

### 4.1 Java (≤ Java 17)

```java
import java.util.*;

public class Solution {
    // public method as LeetCode expects
    public int maximumSubtreeSize(int[][] edges, int[] colors) {
        int n = edges.length + 1;
        List<Integer>[] graph = buildGraph(n, edges);
        return dfs(0, -1, graph, colors)[1];
    }

    // DFS returns {subtreeSize, bestSize}
    private int[] dfs(int u, int parent, List<Integer>[] graph, int[] colors) {
        int current = 1;               // size if homogeneous so far
        int best = 0;                  // best seen in children

        for (int v : graph[u]) {
            if (v == parent) continue;

            int[] res = dfs(v, u, graph, colors);
            int childSize = res[0];
            best = Math.max(best, res[1]);

            if (childSize != -1 && colors[v] == colors[u]) {
                current += childSize;          // merge child
            } else {
                current = -1;                   // subtree becomes mixed
            }
        }
        return new int[]{current, Math.max(current, best)};
    }

    private List<Integer>[] buildGraph(int n, int[][] edges) {
        List<Integer>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();
        for (int[] e : edges) {
            g[e[0]].add(e[1]);
            g[e[1]].add(e[0]);
        }
        return g;
    }
}
```

### 4.2 Python (Python 3.8+)

```python
from collections import defaultdict
from typing import List

class Solution:
    def maximumSubtreeSize(self, edges: List[List[int]], colors: List[int]) -> int:
        n = len(colors)
        g = defaultdict(list)
        for u, v in edges:
            g[u].append(v)
            g[v].append(u)

        def dfs(u: int, parent: int):
            cur = 1          # size if homogeneous
            best = 0         # best seen in subtrees
            for v in g[u]:
                if v == parent: continue
                child_size, child_best = dfs(v, u)
                best = max(best, child_best)

                if child_size != -1 and colors[v] == colors[u]:
                    cur += child_size
                else:
                    cur = -1
            return cur, max(cur, best)

        _, ans = dfs(0, -1)
        return ans
```

### 4.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumSubtreeSize(vector<vector<int>>& edges, vector<int>& colors) {
        int n = colors.size();
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        function<pair<int,int>(int,int)> dfs = [&](int u, int p) {
            int cur = 1;          // size if homogeneous
            int best = 0;         // best in children
            for (int v : g[u]) {
                if (v == p) continue;
                auto [childSize, childBest] = dfs(v, u);
                best = max(best, childBest);

                if (childSize != -1 && colors[v] == colors[u])
                    cur += childSize;
                else
                    cur = -1;
            }
            return make_pair(cur, max(cur, best));
        };

        return dfs(0, -1).second;
    }
};
```

---

## 5. Complexity Analysis  

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` | `O(n)` | `O(n)` |
| **Space** | `O(n)` adjacency + recursion stack | `O(n)` + recursion | `O(n)` + recursion |

> **Note**: The recursion depth equals the tree height. In worst‑case (linked list) depth = `n`. For `n ≤ 5·10⁴` it fits in most runtime environments, but if you hit stack limits switch to an iterative stack.

---

## 6. Variations & Extensions  

| Variation | How to tweak |
|-----------|--------------|
| **Multiple roots** | Build a forest; run DFS from each component. |
| **Weighted colors** | Keep track of colors as strings or objects. |
| **Dynamic queries** | Pre‑compute subtree sizes; answer queries in `O(1)`. |
| **Iterative DFS** | Use explicit stack & pair of (node, iterator state). |
| **Parallel DFS** | For huge trees, split subtrees across threads. |

---

## 7. SEO‑Friendly Takeaway  

- **Keywords**: `LeetCode 3004`, `Maximum Subtree of the Same Color`, `Tree DFS`, `Tree DP`, `Java solution`, `Python solution`, `C++ solution`, `Job interview algorithms`, `Graph theory`, `Data structures`, `Competitive programming`.  
- **Meta Description** (for blog search engines):  
  > “Master LeetCode 3004 – Maximum Subtree of the Same Color. Read our full solution in Java, Python & C++ with step‑by‑step explanation, complexity analysis, and interview‑ready insights. Perfect for recruiters and self‑study.”

- **Heading Structure** (H1‑H3) ensures search engines see the flow:  
  ```
  # LeetCode 3004 – Maximum Subtree of the Same Color  
  ## Problem Statement  
  ## Intuition & DFS  
  ## Good, Bad & Ugly  
  ## Java/Python/C++ Code  
  ## Complexity Analysis  
  ```

- **Backlinks & Social**: Share the article on LinkedIn, Reddit r/cscareerquestions, and relevant programming forums. Mention “ready to use interview code” to catch recruiter interest.

---

## 8. Conclusion  

The crux of **LeetCode 3004** is a *simple* DFS that returns a pair of values.  
The algorithm is optimal (`O(n)`) and easy to understand, making it an excellent talking‑point in a technical interview.  

> **Pro tip**: If you’re preparing for an interview, remember to **explain the back‑tracking** and why a homogeneous subtree is broken as soon as a mismatch is found. Recruiters love seeing you think in terms of *state* and *memoization* even in an “O(n)” problem.

Happy coding and good luck on your next job interview! 🚀

---