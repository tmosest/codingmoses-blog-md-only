---
title: LeetCode 952. Largest Component Size by Common Factor - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 952 – *Largest Component Size by Common Factor*  
**Language‑agnostic solution + SEO‑friendly interview‑blog**  

---

## 1. Problem Summary

You’re given an array `nums` of **unique** positive integers.  
Consider a graph whose nodes are the numbers in `nums`.  
An undirected edge connects two numbers if they share a common factor greater than 1.  
Return the size of the **largest connected component** of this graph.

> **Examples**  
> `nums = [4,6,15,35] → 4`  
> `nums = [20,50,9,63] → 2`  
> `nums = [2,3,6,7,4,12,21,39] → 8`  

> **Constraints**  
> * `1 ≤ nums.length ≤ 2·10⁴`  
> * `1 ≤ nums[i] ≤ 10⁵`  
> * All values are unique

---

## 2. Core Idea

1. **Prime factorization** – Two numbers are connected iff they share a prime factor.  
2. **Union–Find (Disjoint Set Union)** –  
   * Treat every integer in `nums` **and** every prime factor as nodes in the DSU.  
   * For each number, union it with all of its prime factors.  
3. **Component size** – After all unions, the numbers that belong to the same root belong to the same connected component.  
   Count the frequency of each root among the original numbers and return the maximum.

> **Why DSU?**  
> * Almost constant‑time union and find (`α(n)` – inverse Ackermann).  
> * Perfect for merging groups on the fly while scanning `nums`.  

---

## 3. Complexity Analysis

| Step | Cost | Explanation |
|------|------|-------------|
| Factorizing all numbers | `O(Σ sqrt(nums[i]))` | `nums[i] ≤ 10⁵`, so √`nums[i] ≤ 316`. With 2 × 10⁴ numbers the total cost is well below 10⁷ operations. |
| Union / Find | `O(α(max(nums)) · N)` | `α(n)` is < 5 for all practical `n`. |
| Counting roots | `O(N)` | Simple hash map. |
| **Total** | **≈ O(N·√maxVal)** | ~ few × 10⁶ ops – comfortably fast. |

Space:  
* DSU arrays sized `max(nums) + 1` → ≤ 10⁵ + 1 integers.  
* Hash map for counts → ≤ N entries.  
* Factorization auxiliary → O(1).

---

## 4. Reference Implementations

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

> **Note** – All solutions use the same approach:  
> * Build a DSU over the range `[0 … max(nums)]`.  
> * For each number, factorize it and union with every prime factor.  
> * Finally, count component sizes and return the maximum.

---

### 4.1 Java 17

```java
import java.util.*;

public class Solution {
    /* ---------- DSU ---------- */
    private static class DSU {
        int[] parent, size;

        DSU(int n) {
            parent = new int[n + 1];
            size   = new int[n + 1];
            for (int i = 0; i <= n; i++) {
                parent[i] = i;
                size[i] = 1;
            }
        }

        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }

        void union(int a, int b) {
            int pa = find(a), pb = find(b);
            if (pa == pb) return;
            if (size[pa] < size[pb]) { int t = pa; pa = pb; pb = t; }
            parent[pb] = pa;
            size[pa] += size[pb];
        }
    }

    /* ---------- Solution ---------- */
    public int largestComponentSize(int[] nums) {
        int max = 0;
        for (int v : nums) if (v > max) max = v;

        DSU dsu = new DSU(max);

        for (int x : nums) {
            int val = x;
            for (int p = 2; p * p <= val; p++) {
                if (val % p == 0) {
                    dsu.union(x, p);
                    while (val % p == 0) val /= p;
                }
            }
            if (val > 1) dsu.union(x, val);   // remaining prime factor
        }

        Map<Integer, Integer> freq = new HashMap<>();
        int best = 0;
        for (int x : nums) {
            int root = dsu.find(x);
            int cnt = freq.merge(root, 1, Integer::sum);
            if (cnt > best) best = cnt;
        }
        return best;
    }
}
```

---

### 4.2 Python 3.10

```python
from collections import defaultdict
import math
from typing import List

class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n + 1))
        self.size   = [1] * (n + 1)

    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])
        return self.parent[x]

    def union(self, a: int, b: int) -> None:
        pa, pb = self.find(a), self.find(b)
        if pa == pb:
            return
        if self.size[pa] < self.size[pb]:
            pa, pb = pb, pa
        self.parent[pb] = pa
        self.size[pa] += self.size[pb]

class Solution:
    def largestComponentSize(self, nums: List[int]) -> int:
        max_val = max(nums)
        dsu = DSU(max_val)

        for num in nums:
            x = num
            for p in range(2, int(math.isqrt(x)) + 1):
                if x % p == 0:
                    dsu.union(num, p)
                    while x % p == 0:
                        x //= p
            if x > 1:          # remaining prime factor
                dsu.union(num, x)

        cnt = defaultdict(int)
        best = 0
        for num in nums:
            root = dsu.find(num)
            cnt[root] += 1
            best = max(best, cnt[root])
        return best
```

---

### 4.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class DSU {
public:
    vector<int> parent, sz;
    DSU(int n) {
        parent.resize(n + 1);
        sz.assign(n + 1, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        if (parent[x] == x) return x;
        return parent[x] = find(parent[x]);
    }
    void unite(int a, int b) {
        int pa = find(a), pb = find(b);
        if (pa == pb) return;
        if (sz[pa] < sz[pb]) swap(pa, pb);
        parent[pb] = pa;
        sz[pa] += sz[pb];
    }
};

class Solution {
public:
    int largestComponentSize(vector<int>& nums) {
        int max_val = *max_element(nums.begin(), nums.end());
        DSU dsu(max_val);

        for (int num : nums) {
            int x = num;
            for (int p = 2; p * p <= x; ++p) {
                if (x % p == 0) {
                    dsu.unite(num, p);
                    while (x % p == 0) x /= p;
                }
            }
            if (x > 1) dsu.unite(num, x);   // remaining prime
        }

        unordered_map<int, int> freq;
        int best = 0;
        for (int num : nums) {
            int root = dsu.find(num);
            int cnt = ++freq[root];
            best = max(best, cnt);
        }
        return best;
    }
};
```

---

## 5. SEO‑Optimized Blog Post

> **Title**: *“Cracking LeetCode 952: Largest Component Size by Common Factor – A Deep Dive into Union‑Find”*  

> **Meta Description**: Learn how to solve LeetCode 952 using Disjoint Set Union. Read our full guide, code snippets in Java, Python, C++, and get interview‑ready tips.

---

### 5.1 Introduction

When you’re prepping for software‑engineering interviews, graph problems are a common hurdle.  
**LeetCode 952 – *Largest Component Size by Common Factor*** is a prime example (pun intended!).  
It tests your ability to:

* **Factorize numbers efficiently**  
* **Implement a fast Union‑Find**  
* **Think in terms of connected components**

In this post we’ll:

1. Walk through the problem statement  
2. Explain why **Union‑Find** is the optimal data structure  
3. Show a step‑by‑step solution (with factorization)  
4. Provide clean, production‑grade code in **Java, Python, and C++**  
5. Offer interview hacks to impress hiring managers

---

### 5.2 Problem Recap

The challenge is to connect numbers that share a common factor > 1.  
Think of the array as a *graph of numbers* where edges are hidden behind prime factors.  
Our goal is to find the size of the largest group (component).

---

### 5.3 Why This Problem Is a Good Interview Question

| Skill Tested | Why It Matters |
|--------------|----------------|
| **Prime factorization** | Demonstrates mathematical thinking and handling of edge‑case values. |
| **Graph theory** | Shows you can model real problems as graphs. |
| **Disjoint Set Union (DSU)** | A must‑know data structure for merging groups on the fly. |
| **Optimization mindset** | You’ll discuss time/space trade‑offs, which recruiters love. |

---

### 5.4 Step‑by‑Step Walkthrough

#### 1. Prime Factorization  
Every number can be written as a product of primes.  
If two numbers share **any** prime factor, they’re in the same component.

*Why not just test pairwise?*  
Pairwise checks would be `O(N²)` (≈ 4 × 10⁸ ops for 2 × 10⁴ numbers) – impossible.

#### 2. Build a Disjoint Set Union (DSU)  
We treat every *integer* in `nums` **and** every *prime factor* as nodes.  
Why? Because we can union a number with a prime factor directly.  
After processing all numbers, all numbers that belong to the same DSU root are connected.

#### 3. Merge Groups  
For each `num`:

```
factorize num → {p1, p2, …}
for each prime pi:
    union(num, pi)
```

The DSU will automatically propagate connections across primes.

#### 4. Count Component Sizes  
After all unions:

```
for each num in nums:
    root = find(num)
    ++cnt[root]
max(cnt)
```

---

### 5.5 Full Code Snippets

We’ve provided **Java**, **Python**, and **C++** solutions in the previous section.  
Copy & paste into your IDE, run, and you’re done!

---

### 5.6 Interview‑Ready Tips

| Tip | How It Helps |
|-----|--------------|
| **Explain DSU internals** | Show you understand path‑compression & union‑by‑size. |
| **Discuss factorization strategy** | Mention the limit (`≤ 10⁵`) and why trial division up to √`n` is enough. |
| **Mention alternative approaches** | E.g., BFS/DFS over the graph, but highlight DSU’s speed advantage. |
| **Ask clarifying questions** | “Are all values guaranteed unique?” – ensures you’re not missing a hidden constraint. |
| **Talk about complexity** | Recruiters appreciate you can quantify performance. |

---

### 5.7 Final Checklist

✅ **Understand the problem** – graph edges ↔ common prime factor  
✅ **Know DSU** – `find`, `union`, path compression, union by size/rank  
✅ **Factorization** – trial division (or pre‑computed primes) works for `nums[i] ≤ 10⁵`  
✅ **Component counting** – hash map from DSU root to frequency  
✅ **Explain everything** – walk through each step to the interviewer  

---

### 6. Closing Thought

LeetCode 952 is more than just a number‑graph puzzle; it’s a showcase of:

* **Mathematical insight** (prime factorization)  
* **Algorithmic elegance** (Disjoint Set Union)  
* **Practical coding skills** (clean, cross‑language implementations)

Mastering it puts you in the top tier of graph‑related interview questions.  
Use the code snippets, run them, and then try the “harder” variants – you’ll feel confident and ready for any follow‑up questions on graph connectivity or DSU.  

Happy coding! 🚀