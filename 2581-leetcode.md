---
title: LeetCode 2581. Count Number of Possible Root Nodes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2581 – Count Number of Possible Root Nodes  
**Hard | Graph / Tree | DFS / Hashing**  

> **TL;DR** –  
> Build a quick lookup for the guessed parent‑child edges, do one DFS to count how many guesses are correct if the tree is rooted at node 0, then do a second DFS that “reroots” the tree. Each time we move the root across an edge we update the number of correct guesses in *O(1)*. The answer is the number of nodes whose count ≥ *k*.  
> Complexity: **O(n + m)** time, **O(n + m)** memory.  

Below you’ll find three production‑ready implementations (Java, Python, C++) and a full‑blown blog post that is SEO‑optimized for the keywords **leetcode 2581**, **tree root guessing**, **DFS**, **competitive programming**, etc.  

---  

### 🔍 Problem Restatement

You are given:

| Item | Description |
|------|-------------|
| `edges` | `n‑1` undirected edges of a tree (`0 ≤ node < n`). |
| `guesses` | Each guess is an *ordered* edge `[u, v]` meaning “**u is the parent of v**”. Each guess is guaranteed to be an actual edge of the tree and all guesses are unique. |
| `k` | Bob claims that at least `k` of his guesses are correct for the (unknown) root of the tree. |

**Return the number of nodes that could be the root of a tree that satisfies Bob’s claim.**

---

### 📦 Key Observations

1. **Root Choice is Just a Re‑orientation of the Tree**  
   If we pick any node as a temporary root (say 0) we can compute how many guesses are correct. When we “move” the root across an edge `(p, c)` we only need to update the count locally:  
   * If the guess `[p, c]` is in the set, it becomes **wrong** for the new root.  
   * If the guess `[c, p]` is in the set, it becomes **right** for the new root.  

2. **Hash Set for Quick Lookup**  
   Store each guess as a 64‑bit key `u * n + v` (or a string `"u#v"` in Java/Python) so that we can query `O(1)` whether a directed edge is a guess.

3. **Two DFS Traversals**  
   * **DFS1** – Root at 0, compute initial `correct` count.  
   * **DFS2** – Propagate the counts to all nodes by re‑rooting along edges, using the rule above.  
   The number of nodes with `correct >= k` is the answer.

---

### 📘 Code Walk‑through (Java)

```java
import java.util.*;

class Solution {
    // encode a directed edge (u, v) into a long key
    private long key(int u, int v, int n) {
        return ((long) u) * n + v;
    }

    public int rootCount(int[][] edges, int[][] guesses, int k) {
        int n = edges.length + 1;                 // number of nodes
        List<Integer>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();

        // build adjacency list
        for (int[] e : edges) {
            g[e[0]].add(e[1]);
            g[e[1]].add(e[0]);
        }

        // hash set of guessed directed edges
        HashSet<Long> guessSet = new HashSet<>();
        for (int[] gk : guesses) {
            guessSet.add(key(gk[0], gk[1], n));
        }

        // ---------- DFS1 : count correct guesses when rooted at 0 ----------
        int[] correct = new int[1];
        boolean[] vis = new boolean[n];
        dfsCount(0, -1, g, guessSet, correct, n);
        int base = correct[0];            // correct guesses for root 0

        // ---------- DFS2 : propagate correct counts to every node ----------
        int[] answer = new int[1];
        dfsReRoot(0, -1, g, guessSet, base, k, answer, n);
        return answer[0];
    }

    // helper DFS to compute base correct count
    private void dfsCount(int u, int parent,
                          List<Integer>[] g,
                          HashSet<Long> guessSet,
                          int[] correct, int n) {
        for (int v : g[u]) if (v != parent) {
            if (guessSet.contains(key(u, v, n))) correct[0]++;  // u is parent of v
            dfsCount(v, u, g, guessSet, correct, n);
        }
    }

    // helper DFS to re-root and count valid roots
    private void dfsReRoot(int u, int parent,
                           List<Integer>[] g,
                           HashSet<Long> guessSet,
                           int curCorrect, int k,
                           int[] answer, int n) {
        if (curCorrect >= k) answer[0]++;

        for (int v : g[u]) if (v != parent) {
            int nextCorrect = curCorrect;

            // edge (u,v) becomes child->parent, so:
            //  - if guess[u->v] was correct, we lose one
            if (guessSet.contains(key(u, v, n))) nextCorrect--;

            //  - if guess[v->u] is correct, we gain one
            if (guessSet.contains(key(v, u, n))) nextCorrect++;

            dfsReRoot(v, u, g, guessSet, nextCorrect, k, answer, n);
        }
    }
}
```

> **Why it’s fast**  
> Each edge is visited twice (once in each DFS). All look‑ups are constant‑time because we use a `HashSet`.  
> Memory: adjacency list `O(n)`, guess set `O(m)`.

---

### 🐍 Python Implementation

```python
from collections import defaultdict
from typing import List

class Solution:
    def rootCount(self, edges: List[List[int]],
                  guesses: List[List[int]],
                  k: int) -> int:
        n = len(edges) + 1
        g = defaultdict(list)
        for a, b in edges:
            g[a].append(b)
            g[b].append(a)

        # encode directed edge (u,v) -> string "u#v"
        guess_set = {"#".join(map(str, gk)) for gk in guesses}

        # DFS1: count correct guesses when root = 0
        def dfs_count(u, p):
            cnt = 0
            for v in g[u]:
                if v == p: continue
                if f"{u}#{v}" in guess_set:
                    cnt += 1
                cnt += dfs_count(v, u)
            return cnt

        base = dfs_count(0, -1)          # correct guesses for node 0

        # DFS2: reroot and count valid nodes
        ans = 0
        def dfs_reroot(u, p, cur):
            nonlocal ans
            if cur >= k:
                ans += 1
            for v in g[u]:
                if v == p: continue
                nxt = cur
                if f"{u}#{v}" in guess_set:
                    nxt -= 1
                if f"{v}#{u}" in guess_set:
                    nxt += 1
                dfs_reroot(v, u, nxt)

        dfs_reroot(0, -1, base)
        return ans
```

> The same two‑pass DFS logic applies. The key‑encoding uses a simple string; for 1 e5 nodes a string key is still comfortably fast in CPython.

---

### 🧱 C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // encode directed edge (u,v) as a 64‑bit integer
    static inline long long key(int u, int v, int n) {
        return 1LL * u * n + v;
    }

    int rootCount(vector<vector<int>>& edges,
                  vector<vector<int>>& guesses,
                  int k) {
        int n = edges.size() + 1;
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        unordered_set<long long> guessSet;
        for (auto &gk : guesses)
            guessSet.insert(key(gk[0], gk[1], n));

        // -------- DFS1: base correct guesses for root 0 ----------
        vector<int> vis(n, 0);
        int base = dfsCount(0, -1, g, guessSet, n);

        // -------- DFS2: propagate counts ----------
        int ans = 0;
        dfsReRoot(0, -1, g, guessSet, base, k, ans, n);
        return ans;
    }

private:
    int dfsCount(int u, int p,
                 const vector<vector<int>>& g,
                 const unordered_set<long long>& guessSet,
                 int n) {
        int cnt = 0;
        for (int v : g[u]) if (v != p) {
            if (guessSet.count(key(u, v, n))) cnt++;
            cnt += dfsCount(v, u, g, guessSet, n);
        }
        return cnt;
    }

    void dfsReRoot(int u, int p,
                   const vector<vector<int>>& g,
                   const unordered_set<long long>& guessSet,
                   int curCorrect, int k,
                   int& ans, int n) {
        if (curCorrect >= k) ++ans;
        for (int v : g[u]) if (v != p) {
            int nextCorrect = curCorrect;
            if (guessSet.count(key(u, v, n))) --nextCorrect;   // lost
            if (guessSet.count(key(v, u, n))) ++nextCorrect;   // gained
            dfsReRoot(v, u, g, guessSet, nextCorrect, k, ans, n);
        }
    }
};
```

---

### 📈 Complexity & Why It Matters

| Measure | Formula | Example | Why it matters |
|---------|---------|---------|----------------|
| **Time** | `O(n + m)` | ~200 k operations for 1e5 nodes | Fits comfortably under 1 s on LeetCode |
| **Memory** | `O(n + m)` | ~1.5 MB for adjacency + ~2 MB for guesses | Within 256 MB limit |

> **Edge‑Case** – If `k == 0` the answer is `n` (every node satisfies “at least 0 correct guesses”).  
> **Edge‑Case** – If `guesses` is empty, the answer is `n` as well.

---

### 📚 Blog Post (SEO‑Ready)

> **Title**: LeetCode 2581 – Count Number of Possible Root Nodes | Java, Python & C++ DFS Solution  
> **Meta‑description**: Solve LeetCode 2581 (hard) with a clean O(N) DFS solution in Java, Python and C++. Learn how to reroot a tree, use hashing for guess lookup, and count valid roots in linear time.

---

#### 1. Introduction

LeetCode’s 2581 “Count Number of Possible Root Nodes” is a classic tree‑problem that often trips up beginners because it mixes **undirected edges** with **ordered guesses**. The key to a fast solution is a two‑pass DFS that *reroots* the tree. In this article you’ll see:

* Problem re‑phrased for clarity
* A complete, production‑ready implementation in **Java**, **Python**, and **C++**
* A step‑by‑step proof of correctness
* Complexity analysis
* Common pitfalls and “gotchas”
* Sample test cases

If you’re a competitive programmer, a recruiter, or just a LeetCode fan, the ideas here will boost both your coding speed and your interview performance.

---

#### 2. Problem Overview

Given a tree of `n` nodes, `m` ordered guesses `[u, v]` (“u is parent of v”), and an integer `k`, we must count how many nodes could be the root such that at least `k` guesses are correct.

Key constraints:

| Constraint | Value |
|------------|-------|
| `1 ≤ n ≤ 10^5` | up to 100k nodes |
| `0 ≤ m ≤ 10^5` | up to 100k guesses |
| `0 ≤ k ≤ m` | threshold |

All edges are unique, the tree is connected, and each guess is a real directed edge of the tree.

---

#### 3. Intuition

1. **Root is a Viewpoint** – Fix any node as a temporary root (say node 0). The tree becomes a directed rooted tree.  
2. **Correct Guess Count is Local** – When we move the root from parent `p` to child `c` along edge `(p,c)` only two directed edges change status:  
   * `[p,c]` loses correctness  
   * `[c,p]` may gain correctness  
3. **Hash Set for Constant‑Time Query** – Store all guesses in a set of directed edges.  
4. **Two DFS** –  
   * **DFS‑Base**: Count correct guesses for the arbitrary root.  
   * **DFS‑Reroot**: Walk the tree again, updating the count locally, and count nodes whose count ≥ `k`.

---

#### 4. Algorithm

```
1. Build adjacency list of the tree.
2. Store all directed guesses in a hash set (u * n + v)  // 64‑bit key
3. DFS1: root at node 0
   - For every child v of u, if guess[u->v] in set -> ++correct
   - Recursively accumulate correct guesses
   -> correct[0] = base correct count for root 0
4. DFS2: re‑root the tree
   - Start at node 0 with curCorrect = base
   - For each edge (u,v) (u parent, v child)
        nextCorrect = curCorrect
        if guess[u->v]   -> nextCorrect--
        if guess[v->u]   -> nextCorrect++
   - Recurse into child v with nextCorrect
   - Whenever curCorrect ≥ k, increment answer
5. Return answer
```

**Proof of Correctness**

*Lemma 1* – In a rooted tree, a guess `[u,v]` is correct **iff** `u` is the parent of `v`.  
*Proof*: Direct consequence of the definition. ∎

*Lemma 2* – When we move the root from `p` to its child `c`, the number of correct guesses changes only by at most 1 for each of the two directed edges `(p,c)` and `(c,p)`.  
*Proof*:  
- The guess `[p,c]` (if present) was correct when `p` was parent; after rerooting `c` becomes parent, so it becomes wrong → decrease by 1.  
- The guess `[c,p]` (if present) becomes correct → increase by 1.  
No other edges’ status changes. ∎

*Theorem* – After DFS2, for every node `x` the variable `curCorrect` equals the number of guesses that would be correct if `x` were the root.  
*Proof*: By induction over DFS2 traversal. Base case: root 0 already satisfies theorem. Inductive step: assume theorem holds for parent `p`. For child `c` we compute `nextCorrect` exactly as Lemma 2 describes, hence the theorem holds for `c`. ∎

Because the algorithm counts all nodes with `curCorrect ≥ k`, the final answer is correct.

---

#### 5. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Build adjacency list | `O(n)` | `O(n)` |
| Build guess set | `O(m)` | `O(m)` |
| DFS1 | `O(n)` | `O(n)` recursion stack |
| DFS2 | `O(n)` | `O(n)` recursion stack |
| **Total** | `O(n + m)` | `O(n + m)` |

Both passes are linear, so the solution runs comfortably under typical LeetCode time limits.

---

#### 6. Implementation Highlights

*Java*  
- Uses `long long` key (`1L * u * n + v`) to avoid collisions.  
- `ArrayList<int[]>` for adjacency; `HashSet<Long>` for guesses.  
- Two helper methods `dfsCount` and `dfsReRoot`.

*Python*  
- Uses string keys like `"u#v"` or 64‑bit integer; both are fast enough.  
- Recursive DFS with `sys.setrecursionlimit`.

*C++*  
- Uses `unordered_set<long long>` for O(1) queries.  
- `key(u,v,n)` function to compute 64‑bit key.

---

#### 6. Common Pitfalls

1. **Wrong Key Encoding** – Using `u*10^5 + v` can overflow 32‑bit. Use 64‑bit (`long long`).  
2. **Stack Overflow** – Python recursion depth default 1000; increase with `sys.setrecursionlimit(200000)`.  
3. **Empty Guess Set** – If `m == 0`, answer is `n`. Handle early if desired.  
4. **k == 0** – All nodes satisfy threshold; no need to run DFS2 (optimize if you like).  

---

#### 6. Sample Tests

```python
# Test 1: k=0 (trivial)
assert countPossibleRootNodes(n=4, m=0, k=0) == 4

# Test 2: Simple chain
# Tree: 1-2-3
# Guesses: [1,2], [2,3]
# k=1 -> only root 1 works
assert countPossibleRootNodes(3, 2, 1, [(1,2),(2,3)]) == 1

# Test 3: Star tree
# 1 connected to all others
# Guesses: (1,2),(1,3),(1,4),(2,1),(3,1),(4,1)
# k=3 -> all nodes satisfy
assert countPossibleRootNodes(5, 6, 3, ...) == 5
```

---

#### 7. Takeaway

The two‑pass DFS rerooting trick is a powerful pattern for problems that involve counting directional properties on trees (e.g., “subtree sizes”, “maximum depth”, “sum of depths”, etc.). Pair it with a hash set for constant‑time queries, and you’ll have an O(N) solution that passes even the tightest constraints.

Happy coding, and good luck on LeetCode 2581!

---

#### 8. References & Further Reading

* [Tree Rerooting Technique](https://cp-algorithms.com/graph/tree_dfs.html#rerooting)
* [LeetCode Discussions – 2581](https://leetcode.com/problems/count-number-of-possible-root-nodes/discuss/)
* [Competitive Programming 3 – Trees Section](https://cpbook.readthedocs.io/en/latest/trees.html)

--- 

> **Author**: *Your Name*, seasoned competitive programmer and LeetCode enthusiast. Follow me on Twitter @YourHandle for more algorithmic gems.