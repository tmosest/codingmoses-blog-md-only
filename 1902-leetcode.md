---
title: LeetCode 1902. Depth of BST Given Insertion Order - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1902 – Max Depth of a BST Created from an Order Array  

> **Problem number:** 1902  
> **Difficulty:** Medium  
> **Time limit:** 1 s (≈ 10⁵ elements)  
> **Space limit:** 512 MB  

> **Goal:**  
> Given an array `order` that contains a permutation of `[1 … n]`, you insert the elements one‑by‑one into a BST (no duplicates).  
> Find the maximum depth that the BST reaches after all insertions.

> **Constraints**  
> * `1 ≤ n ≤ 10⁵`  
> * `order` is a permutation of `[1 … n]`  

The key insight is that the *nearest already‑inserted smaller* and *nearest already‑inserted larger* values determine the depth of the new node – you only need their depths, nothing else.

Below you’ll find a clean, production‑ready solution in **Java, C++ and Python** (O(n log n) time, O(n) space).  
After the code we provide a short blog‑style explanation that dives into the good, the bad, and the ugly of the problem and the chosen approach – all written with SEO in mind so you can drop it on your personal site, LinkedIn, or Medium and impress hiring managers.



--------------------------------------------------------------------

## 1️⃣  Problem restatement (LeetCode style)

```text
Given an array order of length n where order is a permutation of 1..n.
Insert the numbers in the given order into a BST (no duplicate nodes).
Return the maximum depth of the BST after all insertions.
```

--------------------------------------------------------------------

## 2️⃣  Algorithmic idea

For each new number `x` that is inserted we need to know:

* `pre` – the largest value that has already been inserted and is **≤ x**  
* `suc` – the smallest value that has already been inserted and is **> x**

The depth of the new node is

```
depth[x] = max(depth[pre], depth[suc]) + 1
```

All we have to do is be able to query the two neighbours of `x` in
*log n* time.  
The classic data structure for that is an ordered map (`TreeMap`,
`std::map`, or a balanced binary‑search tree).  
We also keep a sentinel pair `(0,0)` and `(n+1,0)` so that every real
value has a neighbour on both sides – the sentinel’s depth is `0`.

--------------------------------------------------------------------

## 3️⃣  Complexity analysis

| Implementation | Time   | Space  |
|----------------|--------|--------|
| Java  | **O(n log n)** – log n per insertion (TreeMap) | **O(n)** – map + array of depths |
| C++    | **O(n log n)** – log n per insertion (`std::map`) | **O(n)** |
| Python | **O(n log n)** – using `bisect` on `array.array` *and* a `set` implemented by `bisect`? Actually we use `bisect` on a sorted list from `sortedcontainers` (balanced tree). | **O(n)** |

All three variants are linearithmic and easily meet the 10⁵‑element
limit.



--------------------------------------------------------------------

## 4️⃣  Code

### 📄 Java

```java
import java.util.*;

class Solution {
    public int maxDepthBST(int[] order) {
        // Map value -> depth
        TreeMap<Integer, Integer> depthMap = new TreeMap<>();
        // sentinel nodes (they never really exist in the tree)
        depthMap.put(0, 0);
        depthMap.put(order.length + 1, 0);

        int maxDepth = 0;

        for (int val : order) {
            // immediate predecessor and successor already in the tree
            int preDepth = depthMap.floorEntry(val).getValue();
            int sucDepth = depthMap.ceilingEntry(val).getValue();

            int curDepth = Math.max(preDepth, sucDepth) + 1;
            maxDepth = Math.max(maxDepth, curDepth);

            depthMap.put(val, curDepth);
        }
        return maxDepth;
    }
}
```

> **Why it works**  
> `floorEntry(val)` gives the largest key `≤ val`,  
> `ceilingEntry(val)` gives the smallest key `≥ val`.  
> Both operations are O(log n).  
> The sentinel nodes guarantee that we never get a `null` entry.

--------------------------------------------------------------------

### 📄 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxDepthBST(vector<int>& order) {
        int n = order.size();
        // map key -> depth, with sentinels
        map<int,int> depth;
        depth[0] = 0;
        depth[n+1] = 0;

        int ans = 0;
        for (int v : order) {
            auto it = depth.lower_bound(v);   // first key >= v
            int suc = it->second;             // depth of successor
            --it;                              // now it points to predecessor
            int pre = it->second;             // depth of predecessor

            int cur = max(pre, suc) + 1;
            ans = max(ans, cur);
            depth[v] = cur;
        }
        return ans;
    }
};
```

> The code is almost identical to the Java version – the `std::map`
> is a self‑balanced BST and gives the same guarantees.

--------------------------------------------------------------------

### 📄 Python

Below is an O(n log n) implementation that uses the
[`sortedcontainers`](https://github.com/grantjenks/collections) package,
which provides a `SortedList` backed by a balanced binary tree.

```python
# pip install sortedcontainers
from sortedcontainers import SortedList
import sys
from typing import List

class Solution:
    def maxDepthBST(self, order: List[int]) -> int:
        n = len(order)
        depth = [0]*(n+2)          # depth[0] and depth[n+1] stay 0
        s = SortedList([0, n+1])   # sorted list of already inserted keys

        ans = 0
        for v in order:
            idx = s.bisect_left(v)   # index of first key >= v
            suc = depth[s[idx]]      # depth of successor
            pre = depth[s[idx-1]]    # depth of predecessor

            cur = max(pre, suc) + 1
            ans = max(ans, cur)
            depth[v] = cur
            s.add(v)                 # insert key for future queries

        return ans
```

> **Why we use `sortedcontainers`**  
> Python’s native `list` would need `O(n)` per query;  
> the `SortedList` class internally keeps a balanced tree, giving
> `bisect_left`/`bisect_right` in O(log n).

--------------------------------------------------------------------

## 5️⃣  Blog‑style explanation (SEO‑friendly)

---

### Title  
**“Cracking LeetCode 1902 – Max Depth of a BST from an Order Array”**

### Meta‑description  
> “Learn a clean TreeMap / std::map solution for LeetCode 1902, the Max Depth BST problem. Get the code in Java, C++, Python, plus interview‑style tips.”

---

#### 1.  The *story* of the problem  

LeetCode 1902 is a classic interview‑style question that tests two
things at once:

1. **Tree manipulation** – building a BST from a sequence of inserts.  
2. **Nearest‑neighbour queries** – you only need the depths of the
   nearest left and right nodes to compute the depth of a new node.

It’s a favourite among hiring managers because it blends
data‑structure knowledge with careful reasoning about algorithmic
complexity.

---

#### 2.  The *good*  

| What’s great about the problem | Why it’s useful in interviews |
|--------------------------------|--------------------------------|
| **Simplicity of the statement** | The challenge is to think about the “right” data structure, not to get lost in complicated rules. |
| **Clear optimal solution** | The TreeMap / map approach is almost a textbook example of using an ordered container. |
| **Real‑world relevance** | BST‑style insertions (or balanced BSTs) show up in database indexing, caching, and many other systems. |

### ✅  Code‑review checklist  

* Use sentinels to avoid null‑checks.  
* Keep an auxiliary array (`depth[]`) – it’s O(n) and gives O(1) depth look‑ups.  
* Track the maximum depth in a single pass.  

---

#### 3.  The *bad*  

| Pitfall | How to avoid it |
|---------|-----------------|
| *Assuming the tree is balanced* | The tree **may** be highly unbalanced if you insert in sorted order.  
| *Using a plain Python list for neighbours* | Linear scans give O(n²) worst‑case.  
| *Forgetting sentinels* | You may return `None` and crash on the first query.  

---

#### 4.  The *ugly*  

Sometimes you’ll be given a “trivial”‑looking problem that is actually
just a disguised test of your knowledge of ordered containers.  
LeetCode 1902 is one of those: the correct solution is a one‑liner
with a balanced BST, but many candidates will over‑think and write
quadratic code.

If you’re preparing for a coding interview, practice this pattern:

* **Insert + nearest neighbours** → *TreeMap / map*  
* **Fenwick tree** → *Predecessor / successor in log n*  

You’ll not only solve LeetCode 1902 fast, but you’ll also show that
you know when to pull out the right tool.

---

#### 5.  Interview‑style take‑aways

1. **Mention the sentinels** – It shows you understand edge cases and avoid `None`/null checks.  
2. **Explain the O(n log n) bound** – Interviewers love to hear *why* your solution fits the constraints.  
3. **Show the code in all languages you know** – A full‑stack developer who can write in Java, C++, and Python is a huge plus.  
4. **Use the problem as a segue** – Ask the interviewer how a real‑world system would guarantee balanced inserts (e.g., AVL, Red‑Black, B‑Tree).  

---

#### 6.  Quick‑reference code snippets

```java
TreeMap<Integer,Integer> depth = new TreeMap<>();
depth.put(0,0);
depth.put(n+1,0);            // sentinels
```

```cpp
map<int,int> depth;
depth[0] = 0;
depth[n+1] = 0;              // sentinels
```

```python
from sortedcontainers import SortedList
s = SortedList([0, n+1])     # sentinel keys
```

> **Tip**: In a live interview, ask whether you’re allowed to use
> standard library containers; many companies give you the green light
> because the goal is to demonstrate understanding, not to reinvent a
> BST from scratch.



--------------------------------------------------------------------

## 📌 Final thoughts

LeetCode 1902 is deceptively simple once you spot that the depth of a
new node is governed solely by its two nearest neighbours.  
Using an ordered map (or a balanced BST) lets you solve the problem in
O(n log n) time – the fastest feasible solution under the given limits.
The Java, C++ and Python snippets above are ready to drop into a
coding interview or into your personal portfolio to show that you can
write clean, efficient, and production‑grade code.



---

> **Keywords for SEO** – LeetCode 1902, Max Depth BST, TreeMap, std::map, Interview question, Software Engineering interview, Data Structures, Algorithm, Java, C++, Python.  
> Add a short meta‑description like:  
> “A clear, O(n log n) solution to LeetCode 1902 – Max Depth of a BST. Code in Java, C++ and Python with full commentary and interview‑style tips.”  

Happy coding, and good luck on your next interview!