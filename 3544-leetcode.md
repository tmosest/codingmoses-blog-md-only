---
title: LeetCode 3544. Subtree Inversion Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

> **Subtree Inversion Sum** – LeetCode 3544  
> 
>  You are given an undirected tree rooted at node 0.  
> 
>  * `edges` – the tree edges.  
> 
>  * `nums` – value stored in every node.  
> 
>  * `k` – the minimum distance that must separate two inverted nodes when one is an ancestor of the other.  
> 
>  When a node is *inverted*, the values of **every node in its subtree** are multiplied by −1.  
> 
>  Find the **maximum possible sum** of all node values after performing any set of inversions that obey the distance rule.

The constraints ( `n ≤ 5·10⁴`, `k ≤ 50` ) make a naive brute force impossible.  
The key observation is that the decision “invert this subtree or not” only interacts with *ancestors* that are at most `k–1` edges above – nothing farther down the line.  
That gives a clean DP on trees.



--------------------------------------------------------------------

## 2.  Intuition – 3‑D DP

We walk the tree in a DFS order.  
For each node `u` we remember two things:

| parameter | meaning |
|-----------|---------|
| `dist` (`0 … k‑1`) | how many edges are left until we are allowed to invert again **inside** the subtree of `u`.  
If `dist > 0` we *cannot* invert `u`. If `dist = 0` we may choose to invert `u` or not. |
| `sign` (`+1 / –1`) | the sign that the values of the subtree rooted at `u` currently carry because of inversions performed **above** `u`.  
If a higher ancestor was inverted, all its descendants have the opposite sign. |

`dp[u][dist][sign]` – the maximum achievable sum of the whole subtree of `u`
under those two constraints.

Transitions:

```
if dist > 0          // inversion of u is forbidden
    result = sign * nums[u] + Σ  dp[child][dist-1][sign]
else                 // dist == 0, we can decide
    // 1) do not invert u
    notInvert = sign * nums[u] + Σ  dp[child][0][sign]
    // 2) invert u
    invert = -sign * nums[u] + Σ  dp[child][k-1][-sign]
    result = max(notInvert, invert)
```

The base case is a leaf: the same formulas still work because the loop over children is empty.



--------------------------------------------------------------------

## 3.  Correctness Proof

We prove that the algorithm returns the maximum possible sum.

### Lemma 1  
For any node `u`, any `dist (0≤dist<k)` and any `sign`, `dp[u][dist][sign]` equals the maximum sum that can be achieved inside the subtree of `u` while respecting

* the distance rule for inversions inside the subtree, **and**
* the fact that all values outside the subtree already have the global sign `sign`.

**Proof.**

Induction over the height of the subtree.

*Base (leaf).*  
The subtree of a leaf contains only node `u`.  
If `dist>0` the leaf cannot be inverted, so the only possible sum is `sign·nums[u]`.  
If `dist=0`, the leaf may be inverted or not – the algorithm picks the larger of `sign·nums[u]` and `-sign·nums[u]`.  
Thus the table entry equals the optimum.

*Induction step.*  
Assume the lemma holds for all proper children of `u`.  
When `dist>0`, no node inside `u`'s subtree can be inverted (by definition of `dist`), so the optimal sum is simply the sum of the children's optimal sums with the same `dist-1` and unchanged `sign`.  
By induction hypothesis each child contributes its optimal sum, therefore the algorithm computes the optimal total.

When `dist=0`, we may either leave `u` unchanged or invert it.  
In both cases the optimal value for each child subtree is still the optimal value with the corresponding `(dist, sign)` pair:
* without inversion: children get `dist=0` and same `sign`;
* with inversion: children get `dist=k-1` and flipped `sign`.  
By induction hypothesis each child contributes its optimal value for the chosen pair, so the algorithm’s `max` picks the global optimum.

Thus the lemma holds for `u`. ∎



### Lemma 2  
`dp[0][0][+1]` equals the maximum achievable sum of the whole tree.

**Proof.**

Node 0 is the root, so no ancestor inversion influences it → global `sign = +1`.  
The first possible inversion at the root is allowed, therefore `dist = 0`.  
By Lemma 1, `dp[0][0][+1]` is exactly the best sum attainable for the entire tree. ∎



### Theorem  
The algorithm returns the maximum possible sum after any admissible set of inversions.

**Proof.**

The algorithm returns `dp[0][0][+1]`.  
By Lemma 2 this value equals the optimum for the whole tree. ∎



--------------------------------------------------------------------

## 4.  Complexity Analysis

| Item | Cost |
|------|------|
| Building adjacency lists | `O(n)` |
| DP table size | `n · k · 2`  (≈ 5 × 10⁶ for the worst case) |
| Each DP cell computed once | `O(k · 2)` per node (children loop only once) |
| Total time | `O(n · k)` ≈ 2.5 × 10⁶ operations |
| Extra memory | `O(n · k)` for the DP table + adjacency lists |

Both time and memory fit comfortably in LeetCode limits.



--------------------------------------------------------------------

## 5.  Reference Implementations

Below you will find **Java**, **Python**, and **C++** implementations that follow the same DP logic.  
All three codes expose the same public method:

```java
public long subtreeInversionSum(int[][] edges, int[] nums, int k);
```

```python
class Solution:
    def subtreeInversionSum(self, edges: List[List[int]], nums: List[int], k: int) -> int:
```

```cpp
class Solution {
public:
    long long subtreeInversionSum(vector<vector<int>>& edges, vector<int>& nums, int k);
};
```

---


### 5.1  Java

```java
import java.util.*;

public class Solution {
    // dp[node][dist][signIndex]  signIndex: 0 -> +1, 1 -> -1
    private long[][][] dp;
    private int k;
    private int[] nums;
    private List<Integer>[] tree;

    public long subtreeInversionSum(int[][] edges, int[] nums, int k) {
        int n = nums.length;
        this.k = k;
        this.nums = nums;

        // build adjacency list
        tree = new ArrayList[n];
        for (int i = 0; i < n; i++) tree[i] = new ArrayList<>();
        for (int[] e : edges) {
            tree[e[0]].add(e[1]);
            tree[e[1]].add(e[0]);
        }

        // initialize dp with a sentinel value
        dp = new long[n][k][2];
        for (int i = 0; i < n; i++)
            for (int d = 0; d < k; d++)
                Arrays.fill(dp[i][d], Long.MIN_VALUE);

        // start from root, no ancestor inversion, dist = 0, sign = +1
        return dfs(0, -1, 0, 0);
    }

    // dfs returns dp[node][dist][signIndex]
    private long dfs(int u, int parent, int dist, int signIdx) {
        if (dp[u][dist][signIdx] != Long.MIN_VALUE) {
            return dp[u][dist][signIdx];
        }

        long sign = (signIdx == 0) ? 1L : -1L;
        long nodeVal = sign * nums[u];

        long without = nodeVal;          // sum if we do not invert u (or cannot)
        long with = Long.MIN_VALUE;      // only used when dist==0

        // iterate over children
        for (int v : tree[u]) {
            if (v == parent) continue;
            if (dist > 0) {
                without += dfs(v, u, dist - 1, signIdx);
            } else {
                // child when we do NOT invert u
                without += dfs(v, u, 0, signIdx);
            }
        }

        // If inversion is allowed (dist == 0), consider inverting u
        if (dist == 0) {
            long invertedVal = -sign * nums[u];   // value after inversion
            long invertedSum = invertedVal;
            int oppositeSignIdx = 1 - signIdx;    // flip sign

            for (int v : tree[u]) {
                if (v == parent) continue;
                invertedSum += dfs(v, u, k - 1, oppositeSignIdx);
            }

            without = Math.max(without, invertedSum);
        }

        dp[u][dist][signIdx] = without;
        return without;
    }
}
```

**Notes**

* `signIdx` keeps the DP small – two possible signs.  
* The sentinel value `Long.MIN_VALUE` guarantees that an uninitialised cell is always recomputed.  
* The function returns a `long` – LeetCode uses 64‑bit for the answer.



### 5.2  Python

```python
from functools import lru_cache
from typing import List

class Solution:
    def subtreeInversionSum(self, edges: List[List[int]], nums: List[int], k: int) -> int:
        n = len(nums)

        # adjacency list
        g = [[] for _ in range(n)]
        for a, b in edges:
            g[a].append(b)
            g[b].append(a)

        @lru_cache(maxsize=None)
        def dfs(u: int, parent: int, dist: int, sign: int) -> int:
            """
            sign : +1 or -1
            dist : 0 .. k-1  (edges left until we may invert inside this subtree)
            """
            val = sign * nums[u]
            if dist > 0:            # inversion forbidden
                return val + sum(dfs(v, u, dist - 1, sign) for v in g[u] if v != parent)

            # dist == 0 – we may choose to invert or not
            not_invert = val
            for v in g[u]:
                if v == parent: continue
                not_invert += dfs(v, u, 0, sign)

            invert = -val
            for v in g[u]:
                if v == parent: continue
                invert += dfs(v, u, k - 1, -sign)

            return max(not_invert, invert)

        return dfs(0, -1, 0, 1)
```

**Why `lru_cache` works**

`dfs` is called with a fixed tuple `(u, parent, dist, sign)` – exactly the
states of the DP table.  
The cache guarantees each state is computed once, so the complexity stays `O(n·k)`.



### 5.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long subtreeInversionSum(vector<vector<int>>& edges,
                                  vector<int>& nums, int k) {
        int n = nums.size();
        this->k = k;
        this->nums = nums;

        // adjacency list
        tree.assign(n, {});
        for (auto& e : edges) {
            tree[e[0]].push_back(e[1]);
            tree[e[1]].push_back(e[0]);
        }

        dp.assign(n, vector<vector<long long>>(k, vector<long long>(2, LLONG_MIN)));

        return dfs(0, -1, 0, +1);
    }

private:
    vector<vector<int>> tree;
    vector<vector<vector<long long>>> dp;
    int k;
    vector<int> nums;

    long long dfs(int u, int parent, int dist, int sign) {
        long long &res = dp[u][dist][sign == -1];
        if (res != LLONG_MIN) return res;

        long long cur = (long long)sign * nums[u];

        // gather children first
        vector<long long> childSum(k, 0); // helper is not necessary – we just iterate

        // if dist > 0 inversion is forbidden
        if (dist > 0) {
            for (int v : tree[u]) {
                if (v == parent) continue;
                cur += dfs(v, u, dist - 1, sign);
            }
            res = cur;
        } else { // dist == 0  → we may invert or not
            long long notInvert = cur;
            for (int v : tree[u]) {
                if (v == parent) continue;
                notInvert += dfs(v, u, 0, sign);
            }

            long long invert = -(long long)sign * nums[u];
            for (int v : tree[u]) {
                if (v == parent) continue;
                invert += dfs(v, u, k - 1, -sign);
            }

            res = max(notInvert, invert);
        }

        return res;
    }
};
```

--------------------------------------------------------------------

## 6.  Blog Post – “Subtree Inversion Sum: The Good, the Bad, the Ugly”

> **[SEO TAGS]** `Subtree Inversion Sum`, `LeetCode 3544`, `DP on Trees`, `Coding Interview`, `Tree Algorithms`, `Algorithm Design`, `Java`, `Python`, `C++`, `Interview Question`, `Interview Preparation`

---

### 6.1  Hook – Why this Question Rocks

In a typical coding interview the interviewer loves *trees* because they are a “real world” data structure – you see them in file systems, in DOM trees, and even in organizational charts.  
The “Subtree Inversion Sum” adds a twist: you can flip the sign of an entire branch, but only if you keep a minimum distance from an ancestor flip.  
It’s a great blend of **graph theory**, **recursion**, and **dynamic programming** – exactly the kind of problem that turns a candidate from “average” to “star” in a hiring call.

---

### 6.2  The Good – A Clean 3‑D DP

* **Intuitive state machine** – the `dist` counter remembers “how far above we are still banned from inverting”, while the `sign` state keeps track of the global parity of inversions above the current node.  
* **Linear time** – `O(n·k)` is only about 2.5 million operations for the worst‑case input, which is a breeze for any modern CPU.  
* **No ad‑hoc tricks** – the algorithm does not rely on heuristics or randomisation; it follows from a rigorous DP formulation.

Because every node’s choice only depends on the state of its parent, the DP is *exactly* a tree‑DP. That’s why the solution is straightforward once you recognise the “look‑ahead only up to `k–1` edges” fact.

---

### 6.3  The Bad – A Flawed Brute‑Force

A naïve approach might try:

1. Enumerate every subset of nodes (2ⁿ possibilities) and apply inversions.
2. For each subset, check the distance rule by walking every ancestor pair.

Both steps explode exponentially. Even with pruning, the combinatorial explosion of “inverting any subtree” is intractable for `n = 5·10⁴`.  
People sometimes think that because “inversion flips signs”, one can simply *greedily* flip the negative‑value nodes – but that greedy rule fails because flipping a parent negates an *entire** subtree and may destroy many small positive values.

---

### 6.4  The Ugly – Handling Signs & Distances

Implementing the DP correctly is not “trivial”, but it is *manageable*:

* **Sign bookkeeping** – you need to remember whether an ancestor was inverted. A clean way is to encode the sign as `0 → +1` and `1 → –1`.  
* **Distance counter** – keep a counter that decrements as you go deeper. Reset it to `k–1` after an inversion.  
* **Cache / Memoisation** – because each node may be visited for many `(dist, sign)` pairs, a memoisation table or a `@lru_cache` in Python is essential; otherwise you would recompute millions of sub‑subproblems.

The algorithm’s core formulas hide a lot of algebraic detail:  
`sign * nums[u]` vs. `-sign * nums[u]` and the two different child calls.  
Getting the indices and the recursion order wrong is a common source of bugs.

---

### 6.5  Takeaway – Master the State

When you sit in an interview and see “Subtree Inversion Sum”, your first instinct should be to ask clarifying questions:

* “Is `k` guaranteed to be small?” – It can be up to `n`, but we only care up to `k–1`.  
* “What does an inversion do to a node’s descendants?” – It flips the sign of every descendant exactly once.  

From there you can define the DP states, prove that each node’s optimal value depends only on its parent’s state, and then write the recursive function.  
The interviewer will love the fact that you’ve turned a seemingly confusing rule set into a **mathematically sound DP**.

---

### 6.6  Final Thought – Write, Test, Repeat

* Write the recursive helper first (no memo yet).  
* Add the memoisation table or `@lru_cache`.  
* Test on small handcrafted cases (`n=4, k=2`) to ensure the sign flips work.  
* Scale up and verify against the official LeetCode solution.

Once you’ve done this, you’ll not only solve the problem but also demonstrate a **deep understanding of dynamic programming on trees** – a skill that’s valuable in many domains: from backend services to AI decision trees.

---

> **Want to impress recruiters?**  
> Add “Subtree Inversion Sum” to your portfolio and practice the clean DP approach in Java, Python, or C++.  
> Remember: good algorithms *look clean* – and clean algorithms *look great* on your résumé. 

--- 

**End of Blog Post**

--- 

## 7.  Closing

The “Subtree Inversion Sum” problem is an exemplar of a well‑designed interview question.  
A clean DP solution is both **efficient** and **conceptually elegant**.  
Whether you code it in Java, Python, or C++, the same state machine underpins the logic – the difference is in the implementation details and the language‑specific memoisation tricks.  

Happy coding!