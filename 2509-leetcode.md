---
title: LeetCode 2509. Cycle Length Queries in a Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview – 2509. Cycle Length Queries in a Tree**

The tree is a *complete binary heap* with nodes numbered 1…2ⁿ–1.  
In a heap every node `v` has

```
left  child  = 2*v
right child  = 2*v + 1
parent       = v / 2   (integer division)
```

When an extra edge is added between two nodes `a` and `b` the only cycle that can appear is:

```
a ──► … ──► LCA(a,b) ──► … ──► b  +  the new edge (a,b)
```

So the cycle length is simply the distance from `a` to `b` in the tree **plus one** for the added edge.

Finding the distance is equivalent to finding the Lowest Common Ancestor (LCA).  
Because `n ≤ 30`, the height of the tree is at most 29, so a linear upward walk is more than fast enough.

---

## Algorithm (per query)

```
cnt = 1                        // the new edge
x = a, y = b
while x != y:
    if x > y:   x = x / 2      // move up the deeper node
    else:       y = y / 2
    cnt += 1
return cnt
```

* The loop runs at most `O(log₂(2ⁿ‑1)) = O(n)` steps – ≤ 30 steps.
* `cnt` starts at 1 because the new edge itself counts as a cycle edge.
* Each iteration moves one of the nodes one level higher, so we eventually hit the LCA.

---

## Correctness Proof  

We prove that the algorithm returns the length of the cycle created by adding an edge between `a` and `b`.

### Lemma 1  
During the loop, the pair `(x, y)` always consists of the current positions of the two nodes after moving zero or more times to their parents.

**Proof.**  
Initially `(x, y) = (a, b)`.  
Each iteration replaces the larger of the two values by its parent (`/2`).  
Thus after any number of iterations, `x` and `y` are the original nodes moved upward some number of times. ∎



### Lemma 2  
The loop terminates exactly when `x` and `y` become equal to the Lowest Common Ancestor (LCA) of `a` and `b`.

**Proof.**  
Because the tree is a binary heap, the parent relation is a strict partial order.  
In each step we move the deeper node up one level, so the depth of the deeper node strictly decreases.  
When `x == y` the two nodes are at the same depth and on the same path to the root, hence they are equal to the LCA.  
Conversely, once the two nodes become equal, they cannot diverge again, so the loop ends. ∎



### Lemma 3  
`cnt` equals the number of edges on the unique simple path between `a` and `b` in the original tree plus one.

**Proof.**  
`cnt` starts at 1 for the added edge.  
Each loop iteration moves one of the two nodes one level upward, which corresponds to traversing one tree edge along the path from the node to the LCA.  
Therefore, after `k` iterations we have traversed exactly `k` edges of the original tree path.  
When the loop terminates, `k` equals the distance between `a` and `b`.  
Hence `cnt = 1 + distance(a,b)`, which is exactly the cycle length. ∎



### Theorem  
For every query `(a,b)` the algorithm outputs the length of the cycle formed by adding the edge `(a,b)` to the tree.

**Proof.**  
By Lemma&nbsp;2 the loop stops when `x` and `y` are the LCA, so the algorithm has counted all edges of the path from `a` to `b` (Lemma&nbsp;3).  
Adding the extra edge contributes one more edge, giving the full cycle length. ∎



---

## Complexity Analysis

For each query:
- **Time:** `O(log a + log b) = O(log n)` (≤ 30 steps)
- **Space:** `O(1)` besides the output array

With `m ≤ 10⁵` the total time is `O(m log n)` which is well below the limits.



---

## Reference Implementation

### Java

```java
public class Solution {
    public int[] cycleLengthQueries(int n, int[][] queries) {
        int m = queries.length;
        int[] res = new int[m];

        for (int i = 0; i < m; i++) {
            int x = queries[i][0];
            int y = queries[i][1];
            int cnt = 1;                         // the added edge

            while (x != y) {
                if (x > y) {
                    x /= 2;
                } else {
                    y /= 2;
                }
                cnt++;
            }
            res[i] = cnt;
        }
        return res;
    }
}
```

### Python

```python
from typing import List

class Solution:
    def cycleLengthQueries(self, n: int, queries: List[List[int]]) -> List[int]:
        ans = []
        for a, b in queries:
            cnt = 1          # the added edge
            while a != b:
                if a > b:
                    a //= 2
                else:
                    b //= 2
                cnt += 1
            ans.append(cnt)
        return ans
```

### C++

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> cycleLengthQueries(int n, std::vector<std::vector<int>>& queries) {
        std::vector<int> ans;
        ans.reserve(queries.size());

        for (auto &q : queries) {
            int a = q[0], b = q[1];
            int cnt = 1;                       // the added edge
            while (a != b) {
                if (a > b) a /= 2;
                else       b /= 2;
                ++cnt;
            }
            ans.push_back(cnt);
        }
        return ans;
    }
};
```

All three snippets follow the same algorithmic idea and respect the function signatures used on LeetCode.



---

# Blog Post – “Cycle Length Queries in a Tree: The Good, The Bad, and The Ugly”

> *“When a tree meets an extra edge, a cycle appears. Find its length in O(log n) per query.”*  
> *— LeetCode 2509, Cycle Length Queries in a Tree*

## TL;DR
- **Problem:** Add a single edge between two nodes of a complete binary tree and return the length of the resulting cycle.
- **Key insight:** The cycle is the path from one node to the other *plus* the new edge.  
  The path length is the distance between the two nodes, which equals the number of steps to bring them together at their LCA.
- **Solution:** Repeatedly move the deeper node up to its parent until both nodes meet. Count the steps plus one.
- **Complexity:** `O(log n)` time and `O(1)` extra space per query.

---

## 1. Why This Problem is a Goldmine for Your Resume

- **Tree fundamentals** – You’ll prove your grasp of parent/child relationships and depth calculations.
- **Binary heap tricks** – The parent is simply `node/2`; knowing this saves time on the interviewer’s mind.
- **Algorithmic thinking** – Turning an LCA problem into a straight‑line walk demonstrates problem‑decomposition.
- **Scalable design** – Even with 100 k queries, the solution stays within strict time limits – interviewers love clean, scalable code.

Add it to your LeetCode portfolio, and be ready to talk about the *O(log n)* pattern. Many interviewers will expect a more complex LCA algorithm (segment trees, binary lifting, RMQ). Show them that you can solve it in the simplest possible way.

---

## 2. The Good – How the Problem is Designed for Success

| Aspect | Why it’s Good |
|--------|---------------|
| **Tree is a heap** | `parent = node/2` → constant‑time parent lookup. No need to store adjacency lists. |
| **Small depth (≤ 29)** | A simple while‑loop with at most 30 iterations per query is more than fast enough. |
| **Deterministic edge** | Only *one* extra edge is added; there’s only one possible cycle. The solution is unambiguous. |
| **Straightforward implementation** | One `while` loop and a counter – minimal boilerplate, perfect for coding interviews. |

---

## 3. The Bad – Edge Cases You Must Guard Against

| Edge Case | Why it matters | What to do |
|-----------|----------------|------------|
| **`a == b`** | Adding an edge from a node to itself produces a self‑loop (cycle of length 1). | Initialize `cnt` to 1 and skip the loop if `x == y` immediately. |
| **Large values** (`2ⁿ‑1` up to ~2 B) | Ensure you use integer division and avoid overflow. | In Java use `int` (fits), in Python use `//`, in C++ use `int`. |
| **Non‑heap numbering** | If the tree were not a heap you’d need an adjacency list and BFS/DFS. | Problem guarantees the heap property; ignore this scenario. |

---

## 4. The Ugly – Common Pitfalls

1. **Starting the counter at 0**  
   Forgetting the added edge leads to an answer off by one.  
   *Tip:* Remember the “extra edge” is part of the cycle.

2. **Moving both nodes up simultaneously**  
   Some implementers erroneously do `x /= 2; y /= 2;` each iteration.  
   That *does* eventually reach LCA but **doubles** the step count – the cycle length becomes `2*distance + 1`, which is wrong.

3. **Using recursion for LCA**  
   A naive recursive LCA on a heap still works, but it’s unnecessary overhead.  
   Since the depth is ≤ 29, a while‑loop is cleaner.

4. **Assuming the tree height is `n`**  
   The tree has `2ⁿ‑1` nodes, so its height is `n‑1`.  
   The loop will never need more than 30 iterations, even for `n = 30`.  
   Mis‑calculating the upper bound could make you over‑engineer the solution.

---

## 5. Step‑by‑Step Walkthrough (Java Code Highlight)

```java
int cnt = 1;          // the new edge
int x = a, y = b;
while (x != y) {
    if (x > y) x /= 2;  // deeper node climbs one level
    else       y /= 2;
    cnt++;              // one more cycle edge
}
```

*Why does this work?*  
- Each division by 2 is a parent hop.
- The deeper node is always moved, so the overall depth decreases.
- When `x == y`, we’re at the LCA and the loop stops.
- `cnt` counts every edge on the original path plus the new edge.

---

## 6. Testing Your Solution

| Test | Input | Expected Output |
|------|-------|-----------------|
| 1 | `n=2`, `queries=[[2,3]]` | 4 (2→1→3 plus edge 2‑3) |
| 2 | `n=3`, `queries=[[4,5],[1,6]]` | 4, 5 |
| 3 | `n=5`, `queries=[[10,21],[12,13],[1,2ⁿ‑1]]` | 6, 5, 30 |

Run the sample tests in all three languages to be confident before the interview.

---

## 7. Interview‑Ready Summary

- **What I learned:** LCA via parent traversal, cycle construction in trees, heap arithmetic.
- **Why it matters:** The algorithm is *O(log n)* and uses constant space – a classic interview win.
- **How to present it:** Walk through the loop, explain why it ends at the LCA, and highlight the time/space trade‑offs.

If you’re preparing for coding interviews, include this problem in your study list. It’s short, elegant, and showcases a deep understanding of trees and algorithmic optimization.

---

## 8. Final Thoughts

Cycle Length Queries in a Tree is deceptively simple.  
It teaches you that sometimes the best solution is the *most direct* one – just walk up the tree until the two nodes meet, count the steps, and don’t forget the extra edge.

**Takeaway:** When a problem looks complex, identify the underlying structure (here, a tree + an edge → a path + one) and reduce it to the simplest operations you already know. That’s exactly what interviewers are looking for. Good luck!