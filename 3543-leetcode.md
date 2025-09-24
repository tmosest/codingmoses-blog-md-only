---
title: LeetCode 3543. Maximum Weighted K-Edge Path - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📌 LeetCode 3543 – *Maximum Weighted K‑Edge Path*  
**DP on DAG – A Job‑Interview Power‑Move**

> **Problem ID**: 3543  
> **Difficulty**: Medium  
> **Tags**: Dynamic Programming, DAG, Topological Sort, Bitset, Graph  

---

## 1. The Problem in a Nutshell

You’re given a **Directed Acyclic Graph** (DAG) with `n` nodes (0 … n‑1).  
Each edge is `[u, v, w]` → a directed edge `u → v` with weight `w`.

Parameters:

| Variable | Meaning |
|----------|---------|
| `k` | **Exactly** `k` edges must be used |
| `t` | The total weight must be **strictly less** than `t` |

**Goal**:  
Return the **maximum possible sum of weights** for any path that satisfies the two constraints.  
If no such path exists, return `-1`.

> **Constraints**  
> * 1 ≤ n ≤ 300  
> * 0 ≤ edges.length ≤ 300  
> * 1 ≤ w ≤ 10  
> * 0 ≤ k ≤ 300  
> * 1 ≤ t ≤ 600  
> * The graph is guaranteed to be a DAG.

---

## 2. Why This Problem Matters

- **Graph + DP combo** – Many interviewees stumble on combining graph traversal with DP state transitions.
- **Weight bound (`t ≤ 600`)** – Allows a *bitset* or *boolean DP* trick that runs fast even for the worst case.
- **DAG property** – Lets us relax the need for cycle‑checking and use simple edge‑by‑edge propagation.

---

## 3. Intuition & Approach

### 3.1 DP State

Let  

```
dp[u][e][s] = true
```

⇔ There exists a path that ends at node `u`, uses exactly `e` edges, and has total weight `s` (and `s < t`).

* `u` – node index (`0 … n‑1`)
* `e` – number of edges used (`0 … k`)
* `s` – current weight (`0 … t-1`)

We only keep states with `s < t` – everything else is irrelevant.

### 3.2 Base Case

```
dp[u][0][0] = true   for every node u
```

A path that starts and ends at the same node with zero edges has weight 0.

### 3.3 Transition

For every edge `u → v` with weight `w`:

```
for e = 0 … k-1:
    for s = 0 … t-1-w:
        if dp[u][e][s]:
            dp[v][e+1][s+w] = true
```

We “extend” every reachable state by one edge.

### 3.4 Result

After processing all edges and all depths:

```
answer = max { s | dp[u][k][s] == true for any u }
```

If no such `s` exists → answer = -1.

### 3.5 Complexity

| Operation | Cost |
|-----------|------|
| 3‑D DP array | `O(n * k * t)` ≈ 54 M booleans → ~54 MB |
| Transition loops | `O(edges * k * t)` ≤ 300 × 300 × 600 ≈ 54 M operations |
| Overall | **O(n·k·t + edges·k·t)** – well within limits |

Memory & time are easily handled in Java, Python (with sets) and C++.

---

## 4. Code Implementations

Below are clean, ready‑to‑copy solutions in **Java**, **Python**, and **C++**.  
Feel free to drop them into your LeetCode submission.

---

### 4.1 Java (Bitset‑Optimized)

```java
import java.util.*;

public class Solution {
    public int maxWeight(int n, int[][] edges, int k, int t) {
        // dp[u][e] -> bitset of achievable sums (< t) for node u using e edges
        @SuppressWarnings("unchecked")
        BitSet[][] dp = new BitSet[n][k + 1];
        for (int u = 0; u < n; u++) {
            for (int e = 0; e <= k; e++) {
                dp[u][e] = new BitSet(t);   // only indices [0, t-1] are used
            }
        }

        // Base case: 0 edges, weight 0
        for (int u = 0; u < n; u++) {
            dp[u][0].set(0);
        }

        // Edge list for easy iteration
        List<int[]> edgeList = new ArrayList<>();
        for (int[] e : edges) edgeList.add(e);

        // Transition
        for (int e = 0; e < k; e++) {
            for (int[] ed : edgeList) {
                int u = ed[0], v = ed[1], w = ed[2];
                // For every achievable sum s at (u, e)
                // shift left by w and OR into (v, e+1)
                BitSet shifted = (BitSet) dp[u][e].clone();
                shifted.shiftLeft(w);
                // We must keep only indices < t
                BitSet mask = new BitSet(t);
                mask.set(0, t);            // all valid indices
                shifted.and(mask);
                dp[v][e + 1].or(shifted);
            }
        }

        // Find maximum sum for exactly k edges
        int best = -1;
        for (int u = 0; u < n; u++) {
            for (int s = t - 1; s >= 0; s--) {
                if (dp[u][k].get(s)) {
                    best = Math.max(best, s);
                    break;              // first (largest) true is optimal
                }
            }
        }
        return best;
    }
}
```

> **Why BitSet?**  
> It stores `t` bits compactly and lets us perform a “shift‑and‑or” in ~O(t) time.

---

### 4.2 Python (Set‑Based)

```python
class Solution:
    def maxWeight(self, n: int, edges: List[List[int]], k: int, t: int) -> int:
        # dp[u][e] = set of achievable sums (< t) for node u with e edges
        dp = [[set() for _ in range(k + 1)] for _ in range(n)]
        for u in range(n):
            dp[u][0].add(0)                    # base case

        for e in range(k):
            for u, v, w in edges:
                for s in list(dp[u][e]):     # list() to avoid mutation during loop
                    ns = s + w
                    if ns < t:
                        dp[v][e + 1].add(ns)

        best = -1
        for u in range(n):
            for s in sorted(dp[u][k], reverse=True):
                best = max(best, s)
                break
        return best
```

> The weight limit `t ≤ 600` guarantees the set sizes stay tiny.  
> Using `list(dp[u][e])` prevents “runtime error: set changed size during iteration”.

---

### 4.3 C++ (bitset\<601\>)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxWeight(int n, vector<vector<int>>& edges, int k, int t) {
        // dp[u][e] : bitset of achievable sums (< t) for node u with e edges
        vector<vector<bitset<601>>> dp(n, vector<bitset<601>>(k + 1));

        // Base case: 0 edges, weight 0
        for (int u = 0; u < n; ++u) dp[u][0].set(0);

        for (int e = 0; e < k; ++e) {
            for (auto &ed : edges) {
                int u = ed[0], v = ed[1], w = ed[2];
                // shift dp[u][e] left by w and OR into dp[v][e+1]
                bitset<601> shifted = dp[u][e] << w;
                // keep only bits < t
                for (int s = t; s < 601; ++s) shifted.reset(s);
                dp[v][e + 1] |= shifted;
            }
        }

        int best = -1;
        for (int u = 0; u < n; ++u) {
            for (int s = t - 1; s >= 0; --s) {
                if (dp[u][k].test(s)) {
                    best = max(best, s);
                    break;          // largest found
                }
            }
        }
        return best;
    }
};
```

> **Why `bitset<601>`?**  
> The weight bound (`t ≤ 600`) allows us to keep exactly `t` bits – a perfect fit for bitset.

---

## 5. The Good, the Bad & the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Base‑case initialization** | Simple: every node can start a zero‑length path. | ❌ Many people forget to set all nodes – leads to “false” answers. | ❌ Forgetting to clear the DP array (reuse across test cases). |
| **Transition loops** | Clean triple‑nested loop. | ❌ O(edges·k·t) is acceptable but can be heavy if you forget to limit `s` to `t‑1‑w`. | ❌ Writing manual recursion with memoization can blow up stack depth (k can be 300). |
| **Result extraction** | Scan sums descending – first hit is optimal. | ❌ Searching all nodes for `dp[u][k]` without descending may return the **first** valid sum (not maximum). | ❌ Using a priority queue or DFS to keep track of the maximum weight can be over‑engineering. |
| **Memory usage** | 54 MB (Java bitset) / 54 MB (C++ bitset) / sets (Python). | ❌ Using `List[List[List[bool]]]` in Java may waste memory (3D array). | ❌ Python sets can be slower if not capped by `t`. |

---

## 6. Interview‑Ready Tips

1. **Explain the DP dimension first** – interviewers love a clear state diagram.  
2. **Mention the DAG property** – “Because there are no cycles we can safely iterate over edges in any order.”  
3. **Show the weight cap trick** – “t ≤ 600, so we can use a bitset of 601 bits and shift it by the edge weight.”  
4. **Talk about time & space** – “54 M operations, 54 MB memory – far below the limits.”  
5. **Edge Cases** – “k = 0 → answer is 0 (if t > 0). k > edges.length → -1.”  
6. **Testing** – “Add a custom test harness that prints the topological order and DP snapshot to debug complex cases.”

---

## 7. When and How to Use This Problem

| Scenario | Why It Helps |
|----------|--------------|
| **Graph traversal interview** | Demonstrates you can combine *graph edges* with *DP depth*. |
| **DP on a bounded dimension** | Shows mastery of *bitset* or *boolean DP* techniques. |
| **Hiring for large‑scale systems** | DAG & weight constraints mirror real‑world *dependency resolution* (e.g., build systems, job schedulers). |

> **Pro tip**: In a live interview, sketch the DP matrix on the whiteboard first.  
> Then write the triple‑nested loop in pseudo‑code before typing the final solution.

---

## 8. Final Words & Call to Action

- ✅ *Master the DP transition.*  
- ✅ *Know the weight bound trick.*  
- ✅ *Be able to explain every line of your code.*  

This problem is a **golden ticket** to show recruiters that you can:

* Understand complex constraints  
* Design efficient DP over graphs  
* Deliver clean, production‑ready code in multiple languages

**Ready to land that software‑engineering interview?**  
Practice this problem, add the solutions to your portfolio, and share them on LinkedIn with the hashtag **#LeetCode3543**. Recruiters love seeing *problem‑solving* with a *clear narrative*.

Happy coding! 🚀

---



---



### 📌 SEO Keywords (for the article title & body)

* LeetCode 3543 solution  
* Maximum Weighted K‑Edge Path  
* DP on DAG  
* Graph dynamic programming  
* Interview coding problem  
* Job interview programming  
* Code in Java/Python/C++  
* Bitset DP graph  

---



### 📄 Article Structure (HTML‑style)

```html
<h1>LeetCode 3543 – Master the Maximum Weighted K‑Edge Path (Java, Python, C++)</h1>

<h2>1. Problem Overview</h2>
<p>…</p>

<h2>2. Why It Matters for Interviews</h2>
<ul>
  <li>Graph + DP combo</li>
  <li>Bounded weight → bitset trick</li>
  <li>DAG property</li>
</ul>

<h2>3. Intuition & DP Approach</h2>
<ol>
  <li>State definition</li>
  <li>Base case</li>
  <li>Transition</li>
  <li>Result extraction</li>
</ol>

<h2>4. Complexity Analysis</h2>
<table>…</table>

<h2>5. Code Solutions</h2>
<h3>Java</h3>
<pre>…</pre>
<h3>Python</h3>
<pre>…</pre>
<h3>C++</h3>
<pre>…</pre>

<h2>6. Good, Bad & Ugly Patterns</h2>
<table>…</table>

<h2>7. Interview Tips</h2>
<ol>…</ol>

<h2>8. Final Thoughts</h2>
<p>…</p>
```

> Use this skeleton when publishing on Medium, dev.to, or your personal blog.

---



That’s all. Enjoy the write‑up!