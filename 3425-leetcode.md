---
title: LeetCode 3425. Longest Special Path - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🌟 3425 – Longest Special Path  
*LeetCode – “Job‑Interview‑Ready” solution in **Java**, **Python** & **C++***  

---

### 🚀 Why this post will land you a coding interview call

| Keyword | Why it matters |
|---------|----------------|
| **LeetCode 3425** | The exact problem you’ll see on your next interview board. |
| **Longest Special Path** | A concise, buzz‑worthy title that recruiters search for. |
| **Tree DP** | Signals you can solve graph‑theory problems with dynamic programming. |
| **Sliding‑Window DFS** | Demonstrates a clever, linear‑time trick that impresses interviewers. |
| **Java / Python / C++** | Showcases language versatility – perfect for “tech‑stack‑agnostic” roles. |

> **TL;DR** – Build the tree, pre‑compute distances from the root, and run a *single* depth‑first search while maintaining a sliding window over the current root‑to‑node path. The algorithm runs in **O(n)** time and **O(n)** memory.

---

## 📄 Problem Recap

You are given an undirected tree (rooted at node 0) with `n` nodes.  
`edges[i] = [uᵢ, vᵢ, lenᵢ]` describes an undirected edge of weight `lenᵢ`.  
`nums[i]` is the value stored in node `i`.

A **special path** is a *downward* path (from a node to one of its descendants) where all node values on the path are distinct.  
The length of a path is the sum of edge weights; a single node has length 0.

**Return** `[maxLength, minNodes]` where  
* `maxLength` – the largest possible length of a special path,  
* `minNodes`  – the fewest nodes that achieve that length (the path can be a single node).

> Constraints  
> * `1 ≤ n ≤ 5 × 10⁴`  
> * `1 ≤ nums[i] ≤ 5 × 10⁴`  
> * `1 ≤ lenᵢ ≤ 10⁶`  

---

## 🧠 Approach Overview

1. **Root‑to‑node distance** – Pre‑compute the distance from the root (node 0) to every node.  
2. **Sliding window on the root‑to‑node path** – While DFSing, keep a stack of the current root‑to‑node path.  
3. **Last occurrence array** – `last[value]` remembers the deepest node in the current path that has the given value.  
4. **Window start pointer** – When we encounter a repeated value, move the window start past the previous occurrence.  
5. **Update answer** – For every visited node, the segment `[start … current]` is a valid special path. Compute its length and node count, then update the global answer.

The key is that the *sliding window* is never bigger than the current DFS stack, so every node is pushed and popped exactly once → **linear time**.

---

## 📚 Implementation Details  

Below are three reference implementations: **Java**, **Python**, and **C++**.  
All share the same algorithmic skeleton but differ only in syntax.

> **Tip:**  
> *Never* use a brute‑force DFS from every node – it would be `O(n²)` and TLE on the 5 000‑node tests.

---

### ✅ Java Implementation

```java
import java.util.*;

class Solution {
    public int[] longestSpecialPath(int[][] edges, int[] nums) {
        int n = nums.length;
        // Build adjacency list
        List<List<int[]>> g = new ArrayList<>();
        int maxVal = Integer.MIN_VALUE;
        for (int i = 0; i < n; ++i) {
            g.add(new ArrayList<>());
            maxVal = Math.max(maxVal, nums[i]);
        }
        for (int[] e : edges) {
            g.get(e[0]).add(new int[]{e[1], e[2]});
            g.get(e[1]).add(new int[]{e[0], e[2]});
        }

        // Distance from root (node 0) – we need it to compute path lengths
        int[] dist = new int[n];
        dfsDist(0, -1, 0, g, dist);

        // Last occurrence array for node values
        int[] last = new int[maxVal + 1];
        Arrays.fill(last, -1);

        // Stack that holds the current root‑to‑node path
        List<Integer> path = new ArrayList<>();
        int start = 0;               // left end of the window
        int maxLen = 0;              // best length seen so far
        int minNodes = 1;            // min nodes for that length

        // DFS that runs the sliding window
        dfsWindow(0, -1, 0, g, nums, dist, last, path, start, maxLen, minNodes);

        return new int[]{maxLen, minNodes};
    }

    private void dfsDist(int v, int p, int d,
                         List<List<int[]>> g, int[] dist) {
        dist[v] = d;
        for (int[] e : g.get(v)) {
            int u = e[0];
            if (u == p) continue;
            dfsDist(u, v, d + e[1], g, dist);
        }
    }

    private void dfsWindow(int v, int p, int depth,
                           List<List<int[]>> g, int[] nums, int[] dist,
                           int[] last, List<Integer> path,
                           int start, int maxLen, int minNodes) {
        int curIdx = path.size();        // position we’re about to push
        int oldLast = last[nums[v]];     // remember old position of this value
        int oldStart = start;            // remember old start pointer

        // Move window start if this value is already in the window
        if (oldLast != -1 && oldLast >= start) start = oldLast + 1;

        // Push current node and update last occurrence
        path.add(v);
        last[nums[v]] = curIdx;

        // Calculate length of the current window
        int curLen = dist[v];
        if (start > 0) curLen -= dist[path.get(start)];

        int curNodes = curIdx - start + 1;
        if (curLen > maxLen) {
            maxLen = curLen;
            minNodes = curNodes;
        } else if (curLen == maxLen && curNodes < minNodes) {
            minNodes = curNodes;
        }

        // Recurse on children
        for (int[] e : g.get(v)) {
            int u = e[0];
            if (u == p) continue;
            dfsWindow(u, v, depth + e[1], g, nums, dist,
                      last, path, start, maxLen, minNodes);
        }

        // Backtrack: restore previous state
        last[nums[v]] = oldLast;
        path.remove(path.size() - 1);
        start = oldStart;
    }
}
```

**Why this works**

* The array `last` stores the **deepest** node with each value on the current root‑to‑node path.  
* When we encounter a duplicate, we shift the window’s left boundary past the previous occurrence – that’s the classic *sliding‑window* trick.  
* Because the tree is traversed only once and each node is pushed/popped exactly one time, the whole algorithm is linear.

---

### ✅ Python Implementation

```python
import sys
sys.setrecursionlimit(200000)          # safe for n = 5e4

class Solution:
    def longestSpecialPath(self, edges, nums):
        n = len(nums)

        # Build adjacency list
        adj = [[] for _ in range(n)]
        for u, v, w in edges:
            adj[u].append((v, w))
            adj[v].append((u, w))

        # Pre‑compute distance from root
        dist = [0] * n
        def dfs_dist(v, p, d):
            dist[v] = d
            for u, w in adj[v]:
                if u != p:
                    dfs_dist(u, v, d + w)
        dfs_dist(0, -1, 0)

        max_len = 0
        min_nodes = 1

        stack = []                 # stores node indices of current root→node path
        last = {}                  # value -> deepest position in stack
        start = 0                  # left end of the sliding window

        def dfs(v, p):
            nonlocal max_len, min_nodes, start

            idx = len(stack)           # current depth in stack
            old_last = last.get(nums[v], -1)
            old_start = start

            # Move window start if duplicate found inside current window
            if old_last != -1 and old_last >= start:
                start = old_last + 1

            stack.append(v)
            last[nums[v]] = idx

            # Length of current window
            cur_len = dist[v]
            if start > 0:
                cur_len -= dist[stack[start]]

            cur_nodes = idx - start + 1
            if cur_len > max_len:
                max_len = cur_len
                min_nodes = cur_nodes
            elif cur_len == max_len and cur_nodes < min_nodes:
                min_nodes = cur_nodes

            # Recurse
            for u, _ in adj[v]:
                if u != p:
                    dfs(u, v)

            # Backtrack
            stack.pop()
            if old_last == -1:
                del last[nums[v]]
            else:
                last[nums[v]] = old_last
            start = old_start

        dfs(0, -1)
        return [max_len, min_nodes]
```

---

### ✅ C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> longestSpecialPath(vector<vector<int>>& edges, vector<int>& nums) {
        int n = nums.size();

        // Build adjacency list
        vector<vector<pair<int,int>>> adj(n);
        int maxVal = 0;
        for (auto& e : edges) {
            int u = e[0], v = e[1], w = e[2];
            adj[u].push_back({v, w});
            adj[v].push_back({u, w});
            maxVal = max(maxVal, max(nums[u], nums[v]));
        }

        // Distance from root
        vector<int> dist(n, 0);
        function<void(int,int,int)> dfsDist = [&](int v, int p, int d) {
            dist[v] = d;
            for (auto [u, w] : adj[v])
                if (u != p) dfsDist(u, v, d + w);
        };
        dfsDist(0, -1, 0);

        vector<int> last(maxVal + 1, -1);
        int maxLen = 0, minNodes = 1;
        vector<int> path;          // root‑to‑node stack
        int start = 0;

        function<void(int,int)> dfsWin = [&](int v, int p) {
            int idx = path.size();
            int oldLast = last[nums[v]];
            int oldStart = start;

            if (oldLast != -1 && oldLast >= start) start = oldLast + 1;

            path.push_back(v);
            last[nums[v]] = idx;

            int curLen = dist[v];
            if (start > 0) curLen -= dist[path[start]];

            int curNodes = idx - start + 1;
            if (curLen > maxLen) { maxLen = curLen; minNodes = curNodes; }
            else if (curLen == maxLen && curNodes < minNodes) minNodes = curNodes;

            for (auto [u, w] : adj[v]) if (u != p)
                dfsWin(u, v);

            // backtrack
            last[nums[v]] = oldLast;
            path.pop_back();
            start = oldStart;
        };

        dfsWin(0, -1);
        return {maxLen, minNodes};
    }
};
```

---

## 📈 Complexity Summary

| Implementation | Time | Memory |
|----------------|------|--------|
| Java | **O(n)** | **O(n)** |
| Python | **O(n)** | **O(n)** |
| C++ | **O(n)** | **O(n)** |

Because every node is visited only once and each edge is processed once, the runtime is well‑under 1 s even for the upper bound.

---

## 🎯 What recruiters will notice

* **Linear‑time sliding window** – a non‑obvious trick that cuts algorithmic complexity.  
* **Language flexibility** – Same solution in three major languages → you can fit into almost any team.  
* **Pre‑computation + DFS** – Shows an understanding of graph traversal nuances.  

---

## 🎓 Takeaway for the Next Interview

1. **Ask about tree rooting** – Clarify that “downward” means from a node to its descendants.  
2. **Explain the pre‑computation** – It’s the fastest way to get path lengths on the fly.  
3. **Show the sliding‑window logic** – Talk through the `last` array and the `start` pointer.  
4. **Emphasize linearity** – Every node gets pushed/popped exactly once, so you can handle the full constraint set comfortably.

With these reference solutions tucked in your GitHub or your personal coding repo, you’ll walk into an interview with confidence, ready to write the optimal answer on the whiteboard or on a laptop in just a few minutes.

Good luck – your next job call is just a `print("Hello, world!")` away! 👋💼

--- 

> **Follow me** for more *fast‑track* LeetCode solutions, interview‑prep articles, and coding‑culture insights.  

--- 

*Happy coding!* 👨‍💻👩‍💻
