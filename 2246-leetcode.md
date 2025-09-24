---
title: LeetCode 2246. Longest Path With Different Adjacent Characters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2246 – Longest Path With Different Adjacent Characters  
*(LeetCode Hard)*  

> **Goal** – In a rooted tree where each node has a lowercase letter, find the maximum length of a simple path such that **no two adjacent nodes share the same character**.  
> **Input** – `int[] parent` (size `n`) and `String s` (length `n`).  
> **Output** – Length of the longest valid path (1‑based).

Below you’ll find a full, ready‑to‑paste solution in **Java, Python, and C++** (all O(n) time / O(n) space).  
After that, read the accompanying blog post that explains the intuition, pitfalls, and how this solution shines in a coding interview.

---

## 📌 Code

### Java

```java
import java.util.*;

class Solution {
    private int best;              // global maximum length

    public int longestPath(int[] parent, String s) {
        int n = parent.length;
        List<Integer>[] children = new ArrayList[n];
        for (int i = 0; i < n; i++) children[i] = new ArrayList<>();

        for (int i = 1; i < n; i++) {          // build adjacency list
            children[parent[i]].add(i);
        }

        best = 1;                               // at least one node
        dfs(0, children, s);
        return best;
    }

    /** returns longest chain starting at node `v` that satisfies the rule */
    private int dfs(int v, List<Integer>[] children, String s) {
        int first = 0, second = 0;              // top two lengths among children

        for (int u : children[v]) {
            int len = dfs(u, children, s);      // longest chain from child u
            if (s.charAt(u) == s.charAt(v)) continue; // cannot use this child

            if (len > first) {
                second = first;
                first = len;
            } else if (len > second) {
                second = len;
            }
        }

        best = Math.max(best, first + second + 1); // path passes through v
        return first + 1;                         // longest chain upward
    }
}
```

---

### Python 3

```python
from typing import List
from collections import defaultdict
import heapq

class Solution:
    def longestPath(self, parent: List[int], s: str) -> int:
        n = len(parent)
        children = [[] for _ in range(n)]
        for i in range(1, n):
            children[parent[i]].append(i)

        self.best = 1

        def dfs(v: int) -> int:
            top_two = [0, 0]  # largest two lengths from children

            for u in children[v]:
                cur = dfs(u)
                if s[u] == s[v]:
                    continue
                # keep the two biggest values
                if cur > top_two[0]:
                    top_two[1] = top_two[0]
                    top_two[0] = cur
                elif cur > top_two[1]:
                    top_two[1] = cur

            self.best = max(self.best, top_two[0] + top_two[1] + 1)
            return top_two[0] + 1

        dfs(0)
        return self.best
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestPath(vector<int>& parent, string s) {
        int n = parent.size();
        vector<vector<int>> children(n);
        for (int i = 1; i < n; ++i) children[parent[i]].push_back(i);

        best = 1;
        dfs(0, children, s);
        return best;
    }

private:
    int best;

    int dfs(int v, vector<vector<int>>& children, const string& s) {
        int first = 0, second = 0;   // top two lengths among children

        for (int u : children[v]) {
            int len = dfs(u, children, s);
            if (s[u] == s[v]) continue;

            if (len > first) {
                second = first;
                first = len;
            } else if (len > second) {
                second = len;
            }
        }

        best = max(best, first + second + 1);
        return first + 1;
    }
};
```

---

## 📚 Blog Post – *The Good, The Bad, and The Ugly of DFS on Trees*

> **Keywords**: LeetCode 2246, Longest Path With Different Adjacent Characters, DFS, tree DP, interview coding, algorithm design, edge cases.

### 1. Introduction

When a recruiter hands you **LeetCode 2246 – “Longest Path With Different Adjacent Characters”**, you’re looking at a classic tree‑DP problem. The challenge isn’t just to find the longest path; it’s to do so while obeying a character‑compatibility rule.  
A clean, recursive depth‑first search (DFS) that tracks the best two chains from each child is the most elegant, interview‑friendly solution.

---

### 2. Problem Restatement

Given a rooted tree with `n` nodes (`0 ≤ n ≤ 10⁵`), where each node `i` has a letter `s[i]`, compute the length of the longest simple path (no node repeats) such that **every pair of consecutive nodes on the path has different letters**.  
Because `n` is large, we need an **O(n)** algorithm.

---

### 3. The Intuition – Why Two Chains?

Think of a path that goes through a node `v` and branches into two different sub‑trees.  
If we already know the *longest* chain starting from each child that ends at that child, we can

1. **Use the child only if its letter differs from `v`.**  
2. **Keep the two longest usable chains.**  
3. **Add 1 for node `v` itself** to get a candidate path length.

The global answer is the maximum of all such candidates. This is a *post‑order* computation: children are processed before their parent.

---

### 3. Why DFS + “top‑two” is the Good Choice

| Reason | Detail |
|--------|--------|
| **Simplicity** | One recursive function, no fancy data structures. |
| **Predictable Runtime** | Each node visited once → **O(n)**. |
| **Memory Friendly** | Adjacency list + recursion stack → **O(n)** worst‑case. |
| **Interview‑Ready** | Explains state transition clearly, easy to debug on the spot. |
| **Scalability** | Handles the maximum `10⁵` nodes comfortably. |

The key insight is that a valid path **never revisits a node**, so we only need to consider *child → parent → another child* patterns. Tracking two maximum chains suffices because a path can have at most two branches at a node.

---

### 3. The Bad – Common Pitfalls

1. **Ignoring the Root Condition**  
   Some implementations forget that the root might be a leaf and return 0 instead of 1. Always initialise the global best to 1 (a single node path).

2. **Mixing Up “Length of Path” vs “Number of Nodes”**  
   In DFS, the return value represents the longest *chain* that can be extended upwards. Remember to add 1 for the current node when returning.

3. **O(n²) Mistake with Naïve Pairing**  
   Pairing every child with every other child (`O(k²)` per node) quickly blows up on a star‑shaped tree. The “keep two biggest” trick keeps the complexity linear.

4. **Stack Overflow on Recursion**  
   With `n = 10⁵`, deep recursion can hit the stack limit in some languages. In Java or C++ you may need to raise the recursion limit or use an iterative stack. Python 3 solutions usually pass because CPython’s default recursion limit is higher; otherwise, use `sys.setrecursionlimit`.

---

### 4. The Ugly – Edge Cases that Crash Your Code

| Edge Case | What Happens If You Miss It | Fix |
|-----------|-----------------------------|-----|
| **All Nodes Same Letter** | The path length is always 1. If you forget to update `best = 1` at the start, you may return 0. | Initialise `best = 1`. |
| **Large Star Tree** | O(n²) if you pair children naively. | Keep only the two largest usable chains. |
| **Single Node Tree** | Some recursive code may return 0 for leaf nodes. | Return 1 for a leaf (a chain of length 1). |
| **Empty Children List** | `children[v]` might be empty; the loop must handle it gracefully. | Use an empty list, no special case needed. |
| **Non‑Rooted Input** | LeetCode guarantees `parent[0] == 0`. If you incorrectly assume an undirected tree, you’ll double‑count. | Build a directed adjacency list from the root to children. |

---

### 5. Step‑by‑Step Walkthrough

Let’s walk through a small example:

```
parent = [0, 0, 1, 1, 2, 2]
s      = "abacbd"
```

1. Build adjacency list:  
   `0 → {1, 2}`  
   `1 → {3, 4}`  
   `2 → {5}`

2. DFS(0):  
   * child 1 returns chain `2` (b → a → c)  
   * child 2 returns chain `1` (c) but `s[2]==s[0]`? No → usable.  
   * `first=2`, `second=1` → best candidate `2+1+1 = 4`.

3. The recursion returns `first+1 = 3` to node 0.  
4. Global `best = 4`. Path: `b → a → c → d`.

All operations are `O(1)` per child; overall `O(n)`.

---

### 6. Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java     | **O(n)** | **O(n)** (adjacency list + recursion stack) |
| Python   | **O(n)** | **O(n)** |
| C++      | **O(n)** | **O(n)** |

The only extra constant factor is the cost of comparing characters and updating two maximums – negligible.

---

### 7. Variations & Extensions

| Variation | How to Adapt |
|-----------|--------------|
| **Multiple Allowed Letters** | Instead of a single character, keep a bitmask of allowed letters. |
| **Weighted Edges** | Return the weight of the chain rather than node count. |
| **Unrooted Tree** | Root the tree arbitrarily (e.g., node 0) – the algorithm is agnostic to the root because the rule only concerns adjacent nodes. |

---

### 8. Testing Strategy

| Test | Description |
|------|-------------|
| `n=1` | Single node → answer = 1. |
| All same letters | Answer = 1. |
| Alternating letters (e.g., “ababa”) | Answer = n. |
| Star tree with one differing child | Answer = 2. |
| Random large tree (10⁵ nodes) | Verify that runtime stays < 1 s. |

Use the LeetCode unit tests, plus your own random generators to stress‑test.

---

### 9. Conclusion

LeetCode 2246 is a **pure‑DFS tree DP** problem that rewards clear thinking and a minimal, linear solution.  
The “keep the top two chains” pattern is a reusable pattern for many interview questions about longest paths, longest increasing subsequences in trees, or maximum independent set problems.

> *Takeaway*: Always **think bottom‑up**. By summarizing each child’s contribution in a tiny pair of integers, you can solve the whole problem in one pass.

---

### 10. Final Thought

If you’re preparing for a technical interview, practice this exact pattern until you can write it without looking at a reference. Mastery of DFS on trees opens the door to many hard LeetCode problems and impresses hiring managers who value clean, efficient algorithms.

---  

Happy coding! 🚀