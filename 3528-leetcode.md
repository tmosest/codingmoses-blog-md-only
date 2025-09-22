---
title: LeetCode 3528. Unit Conversion I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code

Below are **three fully‑working, production‑ready** solutions for LeetCode 3528 – Unit Conversion I.  
Each solution runs in **O(n)** time and **O(n)** memory, easily handling the limits (`n ≤ 10⁵`, `factor ≤ 10⁹`).  

| Language | Key Idea | Complexity |
|----------|----------|------------|
| **Java** | Iterative DFS on a directed tree (root 0) – product modulo *M* | **O(n)** time, **O(n)** memory |
| **Python** | Iterative DFS / stack – product modulo *M* | **O(n)** time, **O(n)** memory |
| **C++** | Stack‑based DFS – product modulo *M* | **O(n)** time, **O(n)** memory |

> **NOTE**  
> The problem guarantees that `conversions` form a **directed tree rooted at 0** (unique path from 0 to every node).  
> Therefore a simple depth‑first propagation of the product is sufficient – no need for BFS or Dijkstra.

---

### 1.1 Java – `Solution.java`

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int[] baseUnitConversions(int[][] conversions) {
        int n = conversions.length + 1;
        List<int[]>[] graph = new ArrayList[n];
        for (int i = 0; i < n; i++) graph[i] = new ArrayList<>();

        // Build the directed adjacency list
        for (int[] e : conversions) {
            int src = e[0], tgt = e[1], factor = e[2];
            graph[src].add(new int[]{tgt, factor});
        }

        long[] ans = new long[n];
        ans[0] = 1L;          // 1 unit of type 0 equals 1 unit of type 0
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(0);

        while (!stack.isEmpty()) {
            int u = stack.pop();
            for (int[] edge : graph[u]) {
                int v = edge[0];
                long f = edge[1];
                ans[v] = (ans[u] * f) % MOD;
                stack.push(v);
            }
        }

        // Convert to int array before returning
        int[] result = new int[n];
        for (int i = 0; i < n; i++) result[i] = (int) ans[i];
        return result;
    }
}
```

---

### 1.2 Python – `solution.py`

```python
from typing import List

MOD = 10**9 + 7

class Solution:
    def baseUnitConversions(self, conversions: List[List[int]]) -> List[int]:
        n = len(conversions) + 1
        g = [[] for _ in range(n)]
        for src, tgt, f in conversions:
            g[src].append((tgt, f))

        ans = [0] * n
        ans[0] = 1
        stack = [0]

        while stack:
            u = stack.pop()
            for v, f in g[u]:
                ans[v] = (ans[u] * f) % MOD
                stack.append(v)

        return ans
```

---

### 1.3 C++ – `solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static const int MOD = 1'000'000'007;

    vector<int> baseUnitConversions(vector<vector<int>>& conversions) {
        int n = conversions.size() + 1;
        vector<vector<pair<int,long long>>> g(n);
        for (auto &e : conversions) {
            int src = e[0], tgt = e[1];
            long long f = e[2];
            g[src].push_back({tgt, f});
        }

        vector<long long> ans(n, 0);
        ans[0] = 1;
        stack<int> st;
        st.push(0);

        while (!st.empty()) {
            int u = st.top(); st.pop();
            for (auto &p : g[u]) {
                int v = p.first;
                long long f = p.second;
                ans[v] = (ans[u] * f) % MOD;
                st.push(v);
            }
        }

        vector<int> res(n);
        for (int i = 0; i < n; ++i) res[i] = static_cast<int>(ans[i]);
        return res;
    }
};
```

All three implementations use **iterative DFS** (stack) to avoid stack overflow for deep trees (up to 100 000 nodes).  
They propagate the product of conversion factors from the root (unit 0) to every other node, taking the modulo `1 000 000 007` as required.

---

## 2. Blog Article – “Unit Conversion I on LeetCode: The Good, The Bad, and The Ugly”

> **SEO Keywords:** *LeetCode 3528*, *Unit Conversion*, *Java solution*, *Python solution*, *C++ solution*, *DFS*, *BFS*, *graph tree*, *coding interview*, *job interview*, *algorithm analysis*, *software engineer interview*, *coding interview questions*

---

### 2.1 Introduction

When you’re prepping for a software‑engineering interview, the LeetCode problem set is your playground. Among the many “medium” challenges, **Unit Conversion I (LeetCode 3528)** stands out: it tests graph traversal, modular arithmetic, and the ability to spot hidden structure in the input.

In this post we’ll dissect the problem, walk through the most elegant solutions in **Java, Python, and C++**, and discuss:

* **The Good** – why the problem is a great interview question  
* **The Bad** – common pitfalls that trip candidates  
* **The Ugly** – edge‑case nuances that can silently break your code  

By the end you’ll have a production‑ready solution and a clear narrative to share with interviewers.

---

### 2.2 Problem Recap

You’re given `n` unit types (0 … n‑1) and an array `conversions` of length `n-1`.  
Each element `conversions[i] = [source, target, factor]` means *1 unit of `source` equals `factor` units of `target`*.

**Goal:** For every unit type `i`, compute how many units of type `i` are equivalent to *one* unit of type `0`. Return the results modulo `1 000 000 007`.

**Constraints**

* `2 ≤ n ≤ 10⁵`  
* `1 ≤ factor ≤ 10⁹`  
* The graph is a **directed tree rooted at 0** (unique path from 0 to any node).  
* The answer may be huge, so modulo `MOD = 1 000 000 007` is mandatory.

---

### 2.3 Spotting the Hidden Structure – The Good

The problem looks like a generic “weighted graph” problem, but the extra line in the constraints is a *golden ticket*:  

> *It is guaranteed that unit 0 can be converted into any other unit through a unique combination of conversions.*

That means the input is actually a **directed tree** (no cycles, no branching from the root back to itself). In graph theory, trees are the simplest connected structures. Once you recognize this, the whole problem reduces to a single DFS/BFS that multiplies the edge weights along the unique path.

Why is that a good interview insight?

1. **Time‑savings** – you avoid generic shortest‑path algorithms (Dijkstra, Bellman‑Ford) that would otherwise be overkill.  
2. **Clarity** – a clear problem statement → a clear solution strategy → a confident interviewee.  
3. **Optimization** – you can implement an *O(n)* algorithm that comfortably fits into 1 s time limit.

---

### 2.4 The Algorithm – DFS Propagation

**Core idea**  
Starting from the root (unit 0), traverse the tree.  
For every edge `u → v` with weight `w`, propagate

```
value[v] = value[u] * w mod MOD
```

Because the path from 0 to any node is unique, there’s no need for “best‑path” logic or priority queues.

**Implementation details**

| Step | What to do | Why it matters |
|------|------------|----------------|
| Build adjacency list | `List<int[]>[] g = new ArrayList[n];` | Fast neighbor lookup (`O(1)` per edge). |
| Use an iterative stack/queue | `Deque<Integer> stack = new ArrayDeque<>();` | Avoids recursion depth limits (100 000 nodes). |
| Maintain `long` intermediate values | `long val = ans[u] * w % MOD;` | `factor` can be up to `10⁹`; product may overflow `int`. |
| Return `int[]` | Convert the `long[] ans` to `int[]` before returning | LeetCode’s signature requires `int[]`. |

> **BFS vs. DFS**  
> Both are fine. DFS (stack) is a little more memory‑friendly for trees because you only need to store the parent values. BFS (queue) would do the same with essentially the same overhead.

---

### 2.5 Code Walkthrough – The Ugly Edge Cases

| Language | Common mistake | Fix |
|----------|----------------|-----|
| **Java** | Returning `long[]` directly | LeetCode expects `int[]`. The final conversion is mandatory. |
| **Python** | Using `stack = [0]` and `stack.pop()` | Works, but a `deque` gives slightly better performance for large n. |
| **C++** | Using `int` for weights | Convert to `long long` before multiplication. |

#### Edge‑Case 1: Modulo “Zero”  
When `factor` is a multiple of `MOD`, `factor % MOD` becomes 0 and propagates zeros downstream. That’s *correct* because modulo arithmetic behaves that way. Make sure you apply the modulo **after** each multiplication, not only at the end.

#### Edge‑Case 2: Overflow on 32‑bit Integers  
`1 000 000 007 × 10⁹` exceeds the 32‑bit signed integer range (`≈ 2.1×10⁹`).  
Using `long` (64‑bit) for the intermediate product and then taking `mod` guarantees correctness.

#### Edge‑Case 3: Stack Size in Recursion  
A naive recursive DFS can blow the JVM/CPython/C++ call stack on a deep tree. Using an explicit stack (`ArrayDeque` / `std::stack`) is a *must*.

---

### 2.5 Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| Iterative DFS | **O(n)** (every node & edge visited once) | **O(n)** (graph + answer array) |
| BFS / queue | Same as DFS | Same |
| Generic Dijkstra (if you ignored the tree property) | **O(n log n)** | **O(n)** |

The optimal solution is the simplest and fastest, giving you a **10‑point advantage** in an interview.

---

### 2.6 Interview‑Ready Tips

1. **Explain the tree property first** – ask the interviewer if the graph is guaranteed to be a tree. It shows you’re looking for hidden constraints.  
2. **Clarify the modulo operation** – make sure the interviewer knows you’ll take `mod` at *every multiplication*, not just the final answer.  
3. **Discuss stack vs recursion** – mention why an iterative approach is safer for LeetCode’s 100 000 limit.  
4. **Show the code in multiple languages** – you can say “I have a clean, O(n) DFS in Java/Python/C++” and drop the snippets as a hand‑out.  

---

### 2.7 What the Recruiter Will Notice

* **Pattern Recognition** – spotting the tree structure is a hallmark of a seasoned coder.  
* **Attention to Detail** – handling the modulo correctly, using `long` to avoid overflow, and choosing an iterative traversal all demonstrate production‑ready coding.  
* **Communication** – walking through the algorithm step‑by‑step shows that you can explain complex ideas simply—a critical soft‑skill for senior roles.

---

### 2.8 Bonus: How to Use This Problem for a “Real‑World” Pitch

If you’re applying for a **Backend Engineer** or **Systems Engineer** role, you can frame your solution as a “modular conversion engine”.  
* “I built a lightweight tree‑based propagation system that can be extended to arbitrary directed graphs.”  
* “The modular arithmetic pattern I used here is exactly what we employ in token‑based access controls.”

These connections help you turn a coding problem into a **story about architecture** that hiring managers love.

---

### 2.9 Final Thought

Unit Conversion I is deceptively simple once you read the constraints carefully.  
A clean DFS/BFS that multiplies weights along a tree is the *optimal* strategy.  
With the three code samples above, you’re ready to impress any interviewer – whether they ask for **Java**, **Python**, or **C++**.

> **Next Steps**  
> 1. Run the solutions on LeetCode’s test harness.  
> 2. Add them to your personal “interview cheat sheet.”  
> 3. Practice explaining the tree property and the propagation algorithm – the most frequent question will be *“Why did you choose DFS over Dijkstra?”*  

Good luck, and may your interview be as smooth as a perfectly balanced tree!  

--- 

### 2.10 TL;DR

| ✅ What you’ll learn | 🎯 Who it helps |
|----------------------|-----------------|
| Tree recognition in graph problems | **Interviewers** – shows insight |
| O(n) DFS propagation with modulo | **Candidates** – easy to code, fast |
| Production‑ready Java/Python/C++ code | **All** – copy‑paste into your IDE |

Drop these snippets into your portfolio, run them against the LeetCode tests, and bring the **story** of “the hidden tree” into your next interview. Good luck—and enjoy converting units of time into interview wins!