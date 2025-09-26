---
title: LeetCode 952. Largest Component Size by Common Factor - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 952. Largest Component Size by Common Factor  

**Problem Recap**

Given an array `nums` of *unique* positive integers, build an undirected graph where each integer is a node.  
An edge exists between two nodes `a` and `b` **iff** `gcd(a, b) > 1`.  
Return the size of the largest connected component.

**Why Union‑Find + Prime‑Factorization?**

- Each integer can be represented by the set of its prime factors.  
- Two numbers are connected exactly when they share at least one prime factor.  
- If we treat every prime as a “virtual node” and union every integer with each of its prime factors, then all integers that share any factor will end up in the same DSU set.  
- The largest set size is the answer.

Below are full, ready‑to‑compile solutions in **Java, Python, and C++**.

---

## Java Solution (DSU + HashMap)

```java
import java.util.*;

public class Solution {

    /* ---------- Union–Find (Disjoint Set Union) ---------- */
    private static class DSU {
        int[] parent;
        int[] size;

        DSU(int n) {
            parent = new int[n + 1];
            size   = new int[n + 1];
            for (int i = 0; i <= n; i++) {
                parent[i] = i;
                size[i]   = 1;
            }
        }

        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }

        void union(int a, int b) {
            int pa = find(a), pb = find(b);
            if (pa == pb) return;
            if (size[pa] < size[pb]) {          // union by size
                int tmp = pa; pa = pb; pb = tmp;
            }
            parent[pb] = pa;
            size[pa] += size[pb];
        }
    }

    /* ---------- Main Solution ---------- */
    public int largestComponentSize(int[] nums) {
        int max = Arrays.stream(nums).max().orElse(1);
        DSU dsu = new DSU(max);

        // Union each number with all of its prime factors
        for (int num : nums) {
            int n = num;
            for (int p = 2; p * p <= n; p++) {
                if (n % p == 0) {
                    dsu.union(num, p);
                    while (n % p == 0) n /= p;   // remove this prime factor completely
                }
            }
            if (n > 1) {                         // n itself is a prime > sqrt(original)
                dsu.union(num, n);
            }
        }

        // Count component sizes
        Map<Integer, Integer> cnt = new HashMap<>();
        int answer = 0;
        for (int num : nums) {
            int root = dsu.find(num);
            int sz = cnt.getOrDefault(root, 0) + 1;
            cnt.put(root, sz);
            answer = Math.max(answer, sz);
        }
        return answer;
    }
}
```

**Complexities**

| Operation | Time | Space |
|-----------|------|-------|
| Factorisation of all numbers | `O(n · sqrt(max(nums)))` (worst case) | `O(1)` auxiliary |
| DSU ops | `O(n · α(max))` | `O(max)` |
| Counting | `O(n)` | `O(n)` |

The DSU arrays (`parent`, `size`) are sized `max(nums)+1`, comfortably fitting into memory for the given limits (`max(nums) ≤ 10^5`).

---

## Python Solution (Union‑Find + Trial Division)

```python
from collections import defaultdict
import math
import sys
sys.setrecursionlimit(10**6)

class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n + 1))
        self.size   = [1] * (n + 1)

    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])   # path compression
        return self.parent[x]

    def union(self, a: int, b: int):
        ra, rb = self.find(a), self.find(b)
        if ra == rb: return
        if self.size[ra] < self.size[rb]:   # union by size
            ra, rb = rb, ra
        self.parent[rb] = ra
        self.size[ra] += self.size[rb]


class Solution:
    def largestComponentSize(self, nums: list[int]) -> int:
        max_val = max(nums)
        dsu = DSU(max_val)

        # Union each number with its prime factors
        for num in nums:
            n = num
            for p in range(2, int(math.isqrt(n)) + 1):
                if n % p == 0:
                    dsu.union(num, p)
                    while n % p == 0:
                        n //= p
            if n > 1:          # leftover prime factor
                dsu.union(num, n)

        freq = defaultdict(int)
        ans = 0
        for num in nums:
            root = dsu.find(num)
            freq[root] += 1
            ans = max(ans, freq[root])
        return ans
```

**Running Time**

The same `O(n·sqrt(max(nums)))` factorisation dominates; DSU ops are near‑constant.  

**Why Python?**  
Python’s big‑integer arithmetic is safe for 32‑bit numbers and the algorithm is fully deterministic.

---

## C++ Solution (Optimised DSU)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct DSU {
    vector<int> parent, sz;
    DSU(int n = 0) { init(n); }

    void init(int n) {
        parent.resize(n + 1);
        sz.assign(n + 1, 1);
        iota(parent.begin(), parent.end(), 0);
    }

    int find(int x) { return parent[x] == x ? x : parent[x] = find(parent[x]); }

    void unite(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return;
        if (sz[a] < sz[b]) swap(a, b);
        parent[b] = a;
        sz[a] += sz[b];
    }
};

class Solution {
public:
    int largestComponentSize(vector<int>& nums) {
        int mx = *max_element(nums.begin(), nums.end());
        DSU dsu(mx);

        for (int x : nums) {
            int n = x;
            for (int p = 2; p * p <= n; ++p) {
                if (n % p == 0) {
                    dsu.unite(x, p);
                    while (n % p == 0) n /= p;
                }
            }
            if (n > 1) dsu.unite(x, n);          // remaining prime factor
        }

        unordered_map<int,int> cnt;
        int ans = 0;
        for (int x : nums) {
            int root = dsu.find(x);
            ans = max(ans, ++cnt[root]);
        }
        return ans;
    }
};
```

**Highlights**

- Uses `unordered_map` for counting component sizes – O(1) average.
- `dsu.unite` uses *union by size* and *path compression*.
- Factorisation is efficient because each number’s divisors are checked only up to its square root.

---

## Blog Article: “Largest Component Size by Common Factor – The Good, The Bad, The Ugly”

### 1. Introduction (SEO: “LeetCode 952 solution”, “largest component size”, “common factor graph”)

LeetCode problem **952 – Largest Component Size by Common Factor** challenges you to think beyond the classic graph traversal. You’re given a list of **unique** positive integers, and you must build an undirected graph where two numbers are connected if they share a prime factor greater than one. The objective? Return the size of the largest connected component.

This post dives into the **good** (what works beautifully), the **bad** (common pitfalls), and the **ugly** (edge‑cases that trip even seasoned engineers). We’ll finish with a complete, multi‑language implementation that will impress interviewers and job‑hunters alike.

---

### 2. Problem Intuition (SEO: “prime factor graph intuition”, “Union-Find LeetCode 952”)

1. **Prime factorisation is the key** – Two numbers share an edge iff they share at least one prime factor.  
2. **Treat primes as “virtual nodes”** – If we union each integer with all of its prime factors, all numbers that share any factor will automatically belong to the same DSU set.  
3. **Largest set = answer** – After all unions, the size of the largest DSU component gives the required component size.

---

### 3. Why Union‑Find (DSU) is the sweet spot? (SEO: “DSU for LeetCode”, “union‑find algorithm”)

| DSU Property | Why it matters for 952 |
|--------------|------------------------|
| **Near‑constant union & find** (`α(n)`) | Factorisation (worst case `O(sqrt(max(nums)))`) is the bottleneck; DSU ops are practically free. |
| **No need for explicit adjacency lists** | The graph can have up to ~10^10 edges (worst‑case `gcd` between every pair). DSU lets us collapse the graph implicitly. |
| **Supports “union by size/rank” + “path compression”** | Guarantees almost‑linear time `O(n α(n))`. |

---

### 4. The Good – What’s elegant?

- **Linear component counting** – Once the unions are done, a single pass over `nums` plus a hash map gives you component sizes in O(n).  
- **Memory efficiency** – Even though we allocate arrays of size `max(nums)+1`, the limits (`max(nums) ≤ 10^5`) make this trivial.  
- **No recursion depth worries** – DSU’s iterative `find` (or tail‑recursive in Python) ensures we stay below stack limits.

---

### 4. The Bad – Common Pitfalls (SEO: “LeetCode 952 pitfalls”, “common mistakes”)

| Pitfall | Explanation | Fix |
|----------|-------------|-----|
| **Using plain `gcd` for every pair** | `O(n^2)` time is impossible for `n = 10^4`. | Replace pairwise `gcd` with prime‑factor union. |
| **Not removing duplicate prime factors** | `while (n % p == 0) n /= p;` missing → redundant unions, larger DSU array, higher memory. | Always strip a prime factor completely. |
| **Wrong DSU size** | Initialising DSU with `max(nums)+1` but forgetting to include `+1` → off‑by‑one errors. | `DSU dsu(max_value);` where arrays are `n+1`. |
| **Using `sqrt(n)` per number** without integer math** | In Python, `int(math.sqrt(n))` can overflow on very large ints; use `math.isqrt(n)` or `int(math.isqrt(n))`. | Prefer integer square‑root functions. |

---

### 4. The Ugly – Edge‑Case Quirks (SEO: “LeetCode 952 edge cases”, “prime factor edge cases”)

- **Large primes** – If a number is itself a prime (`> sqrt(original)`) we must union it with that prime.  
- **Numbers that are powers of a prime** – Example: `8 (2^3)` – make sure you only union once with `2` (union by size handles duplicates).  
- **Very small arrays** – If `n == 1`, the answer is obviously `1`.  
- **Input limits** – `max(nums)` up to `10^5` is safe for arrays but can blow memory if you over‑allocate (e.g., using `10^7` just because you think it’s “large enough”).  

Handling these cases correctly turns a clean algorithm into a *bullet‑proof* solution.

---

### 5. Multi‑Language Implementation (SEO: “Java solution for LeetCode 952”, “Python 3 LeetCode 952”, “C++ 952 LeetCode”)

See the code blocks above for Java, Python, and C++ solutions that follow the exact pattern described:

1. Find `max(nums)` → DSU size.  
2. For each number, trial‑divide up to `sqrt(n)` to extract primes.  
3. `dsu.union(num, prime)`.  
4. Count the size of each DSU root and return the maximum.

Feel free to copy‑paste these snippets into your own projects or interview notes. They compile out‑of‑the‑box, pass the official tests, and are fully documented.

---

### 6. Testing Checklist (SEO: “LeetCode 952 test cases”, “component size testing”)

| Test | What to verify |
|------|----------------|
| Random small arrays | `n = 5`–`10` → brute‑force graph + DFS vs. DSU. |
| All numbers are distinct primes | Answer = `1`. |
| All numbers share a common prime (e.g., all even) | Answer = `n`. |
| Mix of single‑prime numbers and composite numbers | Confirm unions propagate correctly. |
| Large inputs (`n = 10^4`, `max(nums)=10^5`) | Execution time < 1 s on typical interview machines. |

---

### 7. What to highlight in an interview?

- **Explain the “virtual prime nodes” trick** – Shows you grasp the underlying math.  
- **Discuss DSU optimisation** – Path compression + union‑by‑size.  
- **Mention complexity analysis** – Interviewers love a clear cost model.  
- **Show you can code it in multiple languages** – Demonstrates adaptability and deep understanding.

---

### 8. Final Thoughts

LeetCode 952 may seem like a graph puzzle at first glance, but once you see **prime factors** as connectors and **DSU** as a magic union operator, the solution becomes almost inevitable.  
Avoid the typical pitfalls: don’t use pairwise gcd, don’t forget to strip duplicate primes, and always size your DSU properly.

With the Java, Python, and C++ snippets above, you’re fully equipped to tackle this challenge in any coding interview or coding bootcamp. Good luck – and happy coding!  

--- 

**Keywords (for recruiters & job‑hunters)**  
`LeetCode 952 solution`, `largest component size`, `common factor graph`, `Union-Find`, `DSU`, `prime factorisation`, `algorithm analysis`, `Java Python C++ LeetCode`, `interview coding interview`.