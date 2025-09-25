---
title: LeetCode 2076. Process Restricted Friend Requests - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Overview – 2076. Process Restricted Friend Requests  

**Problem**  
You have `n` people (0‑indexed).  
- `restrictions[i] = [x, y]` means *x* and *y* can never become friends, directly **or** indirectly (through any chain of friendships).  
- `requests[j] = [u, v]` is a friend request that must be processed in order.  
  *If the request can be granted, *u* and *v* become direct friends for all future requests.*  

Return a boolean array `result[j]` telling whether the *j*‑th request succeeded.

> **Constraints**  
> - `2 ≤ n ≤ 1000`  
> - `0 ≤ restrictions.length ≤ 1000`  
> - `1 ≤ requests.length ≤ 1000`  

The naive solution (checking every restriction for every request) would be **O(n · m)**, which is perfectly fine for the limits, but we can solve the problem elegantly with **Disjoint Set Union (DSU)**, also known as **Union‑Find**.

---

## 2.  Why Union‑Find?

*Each person belongs to a “friend‑circle” (connected component).  
If two people are in the same component they are already friends, otherwise they are not.*

**Key idea**

When a request `[u, v]` arrives  
1. If `find(u) == find(v)` → already friends → *success*.  
2. Otherwise we **hypothetically** merge the two components and then check all restrictions.  
   *If any restriction `[x, y]` would end up in the same component, we must reject the request.*  

Because the number of requests and restrictions is ≤ 1000, a linear scan over restrictions for every request is fast enough, while still giving us an *O(α(n))* amortized find/union time.

---

## 3.  Complexity

| Step | Complexity |
|------|------------|
| `find` | `O(α(n))` (inverse Ackermann) |
| `union` | `O(α(n))` |
| Process one request | `O(α(n) + r)` where `r` = number of restrictions (≤ 1000) |
| Total | `O(q · r)` with `q ≤ 1000` → ≤ 1 000 000 operations |

Memory: `O(n + r)`.

---

## 4.  Implementation

Below are **fully‑commented** implementations in **Java**, **Python**, and **C++**.

> ⚠️ All solutions assume the input arrays are zero‑indexed, as per the problem statement.

---

### 4.1 Java (Clean & Commented)

```java
import java.util.*;

public class Solution {
    /* ---------- Union‑Find (DSU) ---------- */
    private static class DSU {
        int[] parent, rank;
        DSU(int n) {
            parent = new int[n];
            rank   = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]); // path compression
            return parent[x];
        }
        void union(int a, int b) {
            a = find(a); b = find(b);
            if (a == b) return;
            if (rank[a] < rank[b]) {
                parent[a] = b;
            } else if (rank[a] > rank[b]) {
                parent[b] = a;
            } else {
                parent[b] = a;
                rank[a]++;
            }
        }
    }

    public boolean[] friendRequests(int n, int[][] restrictions, int[][] requests) {
        int q = requests.length;
        boolean[] result = new boolean[q];
        DSU dsu = new DSU(n);

        for (int i = 0; i < q; i++) {
            int u = requests[i][0];
            int v = requests[i][1];

            // already in the same component -> already friends
            if (dsu.find(u) == dsu.find(v)) {
                result[i] = true;
                continue;
            }

            // Temporarily union to test restrictions
            int pu = dsu.find(u);
            int pv = dsu.find(v);
            dsu.union(pu, pv);

            boolean ok = true;
            for (int[] r : restrictions) {
                int a = dsu.find(r[0]);
                int b = dsu.find(r[1]);
                if (a == b) {        // restriction would be violated
                    ok = false;
                    break;
                }
            }

            if (!ok) {
                // rollback: undo the union by re‑creating DSU
                // (since n ≤ 1000, we can rebuild it cheaply)
                dsu = new DSU(n);
                for (int k = 0; k < i; k++) dsu.union(requests[k][0], requests[k][1]);
                result[i] = false;
            } else {
                result[i] = true;
            }
        }
        return result;
    }
}
```

> **Note** – In the above Java code we rebuild the DSU on rejection because a true rollback is tricky.  
> For the problem’s small limits this is perfectly fine; for larger limits you would keep a history stack and revert the union.

---

### 4.2 Python (Clear & Idiomatic)

```python
from typing import List

class DSU:
    def __init__(self, n: int):
        self.parent = list(range(n))
        self.rank   = [0] * n

    def find(self, x: int) -> int:
        if self.parent[x] != x:
            self.parent[x] = self.find(self.parent[x])      # path compression
        return self.parent[x]

    def union(self, a: int, b: int) -> None:
        a, b = self.find(a), self.find(b)
        if a == b: return
        if self.rank[a] < self.rank[b]:
            self.parent[a] = b
        elif self.rank[a] > self.rank[b]:
            self.parent[b] = a
        else:
            self.parent[b] = a
            self.rank[a] += 1

class Solution:
    def friendRequests(
        self,
        n: int,
        restrictions: List[List[int]],
        requests: List[List[int]]
    ) -> List[bool]:
        res = [False] * len(requests)
        dsu = DSU(n)

        for i, (u, v) in enumerate(requests):
            # already friends
            if dsu.find(u) == dsu.find(v):
                res[i] = True
                continue

            # try to merge and validate restrictions
            dsu.union(u, v)
            ok = True
            for a, b in restrictions:
                if dsu.find(a) == dsu.find(b):
                    ok = False
                    break

            if not ok:                 # rollback by rebuilding DSU
                dsu = DSU(n)
                for j in range(i):
                    dsu.union(*requests[j])
                res[i] = False
            else:
                res[i] = True

        return res
```

---

### 4.3 C++ (Fast & Modern)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct DSU {
    vector<int> parent, rnk;
    DSU(int n = 0) { init(n); }
    void init(int n) {
        parent.resize(n);
        rnk.assign(n, 0);
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        return parent[x] == x ? x : parent[x] = find(parent[x]); // path compression
    }
    void unite(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return;
        if (rnk[a] < rnk[b]) parent[a] = b;
        else if (rnk[a] > rnk[b]) parent[b] = a;
        else { parent[b] = a; ++rnk[a]; }
    }
};

class Solution {
public:
    vector<bool> friendRequests(int n,
                                const vector<vector<int>>& restrictions,
                                const vector<vector<int>>& requests) {
        vector<bool> ans(requests.size(), false);
        DSU dsu(n);

        for (int i = 0; i < (int)requests.size(); ++i) {
            int u = requests[i][0];
            int v = requests[i][1];

            if (dsu.find(u) == dsu.find(v)) {          // already friends
                ans[i] = true;
                continue;
            }

            dsu.unite(u, v);                           // tentative merge
            bool ok = true;
            for (auto &p : restrictions) {
                if (dsu.find(p[0]) == dsu.find(p[1])) { // restriction violated
                    ok = false;
                    break;
                }
            }

            if (!ok) {                                 // rollback by rebuilding DSU
                dsu.init(n);
                for (int j = 0; j < i; ++j)
                    dsu.unite(requests[j][0], requests[j][1]);
                ans[i] = false;
            } else
                ans[i] = true;
        }
        return ans;
    }
};
```

> 👉 **Why rebuild on rollback?**  
>  For the given constraints rebuilding is cheap (`O(n)`), but if you want a real rollback you would keep a stack of pairs that were merged and reverse the operation.  

---

## 5.  “Good – Bad – Ugly” of the DSU Approach  

| Aspect | What’s **Good** | What’s **Bad** | What’s **Ugly** |
|--------|-----------------|----------------|-----------------|
| **Simplicity** | Clear mapping of friend‑circles to DSU components | Must still scan restrictions for every request | Re‑building the DSU on rejection feels “hacky” – you lose true undo semantics |
| **Scalability** | Works for `n, q, r ≤ 10⁶` if you replace the linear restriction scan with a hash‑set of forbidden pairs | Linear restriction scan becomes a bottleneck at 10⁶ | Path‑compression alone does not prevent restriction violation – you still need the scan |
| **Edge‑cases** | Handling “already friends” early avoids extra work | If a restriction pair is already violated before any request (contradictory input) – the algorithm simply rejects all relevant requests | Re‑building the DSU on every failure may lead to subtle bugs if you forget to re‑apply all previous successful merges |

---

## 6.  Interview‑Ready Checklist

| ✔️ | Why it matters in a tech interview |
|---|------------------------------------|
| ✅  **Use DSU** – shows you know a classic data‑structure and can apply it to a connectivity problem. |
| ✅  **Explain the “hypothetical merge” trick** – many candidates get stuck on how to test restrictions. |
| ✅  **Discuss rollback** – demonstrate awareness that union‑find doesn’t naturally support undo, and explain the rebuild‑as‑fallback strategy (or stack‑based undo for larger data). |
| ✅  **Analyze complexity** – show you can reason about amortised `α(n)` and why the solution is fast enough. |
| ✅  **Mention potential optimisations** – e.g., mapping each restriction to a set of neighbours, or storing the “forbidden pairs” in a hash‑set for O(1) look‑up after a merge. |

---

## 7.  Final Thoughts & Next Steps

- **Good** – DSU gives a clear, near‑linear solution that scales well.  
- **Bad** – The rebuild‑on‑reject technique is a workaround; a real‑world system would need a proper undo stack.  
- **Ugly** – Scanning all restrictions for every request is simple but can become a hotspot if constraints grow to 10⁵.  
  → *Optimisation tip:* keep for each component the set of “restricted partners”; when merging, quickly test whether the intersection is non‑empty.  

---

## 8.  🎯  SEO‑Optimised Blog Post  

Below is a polished article you can drop on your GitHub README, blog, or LinkedIn to showcase your algorithmic chops.

---

# Process Restricted Friend Requests – Leetcode 2076  
*A union‑find solution that’s perfect for your next tech‑interview.*

---

### Table of Contents  
1. [What You’ll Learn](#what-youll-learn)  
2. [The Problem at a Glance](#the-problem-at-a-glance)  
3. [Why Union‑Find Rocks](#why-union‑find-rocks)  
4. [From Pseudocode to Production‑Ready Code](#from-pseudocode-to-production‑ready-code)  
5. [Complexity & Performance](#complexity‑performance)  
6. [Corner‑Case Handling](#corner‑case-handling)  
7. [Optimisations for Larger Inputs](#optimisations-for-larger-inputs)  
8. [Job‑Interview Nuggets](#job‑interview-nuggets)  
9. [Wrap‑Up](#wrap‑up)  

---

## 1. What You’ll Learn  

- **Union‑Find (Disjoint Set Union)** – the go‑to data structure for connectivity problems.  
- How to handle **restricted pair constraints** while merging components.  
- Code in **Java, Python, and C++** – ready for a Leetcode submission or a technical interview.  
- A job‑interview‑ready explanation that highlights your **algorithmic thinking** and **code‑quality**.

---

## 2. The Problem at a Glance  

```
n = 5
restrictions = [[0, 1], [2, 3]]
requests     = [[0, 2], [1, 3], [0, 4]]
```

*Process each request in order, but never allow a restricted pair to share a component.*

Output: `[true, false, true]`.

---

## 3. Why Union‑Find Rocks  

- **Fast component queries**: `find(x)` tells you whether two people are already connected.  
- **Amortised O(α(n))** time for `union` and `find`.  
- **Clean rollback**: On rejection you can rebuild the DSU from scratch (limits are small).  

> **Key insight** – *A restriction is violated iff its two endpoints end up in the same DSU set after a merge.*

---

## 4. From Pseudocode to Production‑Ready Code  

### 4.1 Java  

```java
public class Solution {
    // ---------- DSU ----------
    private static class DSU {
        int[] p, r;
        DSU(int n){ p=new int[n]; r=new int[n]; for(int i=0;i<n;i++) p[i]=i;}
        int find(int x){ return p[x]==x?x:p[x]=find(p[x]);}
        void union(int a,int b){
            a=find(a); b=find(b);
            if(a==b) return;
            if(r[a]<r[b]) p[a]=b;
            else if(r[a]>r[b]) p[b]=a;
            else{ p[b]=a; r[a]++; }
        }
    }

    public boolean[] friendRequests(int n, int[][] restrictions, int[][] requests){
        int q=requests.length;
        boolean[] ans=new boolean[q];
        DSU dsu=new DSU(n);

        for(int i=0;i<q;i++){
            int u=requests[i][0], v=requests[i][1];

            if(dsu.find(u)==dsu.find(v)){ ans[i]=true; continue; }

            // Tentatively merge
            dsu.union(u,v);
            boolean ok=true;
            for(int[] r:restrictions){
                if(dsu.find(r[0])==dsu.find(r[1])){ ok=false; break; }
            }

            if(!ok){                 // rollback: rebuild DSU
                dsu=new DSU(n);
                for(int j=0;j<i;j++) dsu.union(requests[j][0],requests[j][1]);
                ans[i]=false;
            } else ans[i]=true;
        }
        return ans;
    }
}
```

### 4.2 Python  

```python
class DSU:
    def __init__(self,n): self.p=list(range(n)); self.r=[0]*n
    def find(self,x): return x if self.p[x]==x else self._set(x,self.find(self.p[x]))
    def _set(self,x,val): self.p[x]=val; return val
    def union(self,a,b):
        a,b=self.find(a),self.find(b)
        if a==b: return
        if self.r[a]<self.r[b]: self.p[a]=b
        elif self.r[a]>self.r[b]: self.p[b]=a
        else: self.p[b]=a; self.r[a]+=1

class Solution:
    def friendRequests(self,n,restrictions,requests):
        ans=[False]*len(requests)
        dsu=DSU(n)
        for i,(u,v) in enumerate(requests):
            if dsu.find(u)==dsu.find(v):
                ans[i]=True; continue
            dsu.union(u,v)
            ok=True
            for a,b in restrictions:
                if dsu.find(a)==dsu.find(b): ok=False; break
            if not ok:
                dsu=DSU(n)
                for j in range(i): dsu.union(*requests[j])
                ans[i]=False
            else: ans[i]=True
        return ans
```

### 4.3 C++  

```cpp
class DSU{
    vector<int> p,r;
public:
    DSU(int n){ p.resize(n); r.assign(n,0); iota(p.begin(),p.end(),0);}
    int find(int x){ return p[x]==x?x:p[x]=find(p[x]);}
    void unite(int a,int b){
        a=find(a); b=find(b);
        if(a==b)return;
        if(r[a]<r[b]) p[a]=b;
        else if(r[a]>r[b]) p[b]=a;
        else{ p[b]=a; r[a]++; }
    }
};

class Solution{
public:
    vector<bool> friendRequests(int n,
                                const vector<vector<int>>& restrictions,
                                const vector<vector<int>>& requests){
        vector<bool> ans(requests.size());
        DSU dsu(n);
        for(int i=0;i<(int)requests.size();++i){
            int u=requests[i][0],v=requests[i][1];
            if(dsu.find(u)==dsu.find(v)){ ans[i]=true; continue; }
            dsu.unite(u,v);
            bool ok=true;
            for(auto &p:restrictions)
                if(dsu.find(p[0])==dsu.find(p[1])){ ok=false; break; }
            if(!ok){ dsu=DSU(n);
                     for(int j=0;j<i;j++) dsu.unite(requests[j][0],requests[j][1]); 
                     ans[i]=false; }
            else ans[i]=true;
        }
        return ans;
    }
};
```

---

## 5. Complexity & Performance  

| Operation | Time | Space |
|-----------|------|-------|
| `find` | O(α(n)) | O(n) |
| `union` | O(α(n)) | O(n) |
| Restriction scan | O(r) per merge | O(r) |
| Full algorithm | O((q + r) · α(n)) | O(n) |

With **α(n) ≤ 4** even for `n = 10⁶`, the algorithm runs in a few milliseconds on modern hardware.

---

## 6. Corner‑Case Handling  

| Input | Expected Behaviour |
|-------|--------------------|
| Already connected restricted pair (`restrictions = [[0,1]]`, `requests = [[0,1]]`) | Reject – output `false`. |
| Duplicate requests | Each handled independently; already‑connected check ensures no extra work. |
| No restrictions | All requests succeed – DSU reduces to classic friend‑graph. |

---

## 7. Optimisations for Larger Inputs  

1. **Hash‑set of forbidden partners** per component.  
2. On merge, compute `intersection = partnersA ∩ partnersB`; if non‑empty → reject.  
3. Use **Union by Rank** + **Path Compression** to keep tree depth shallow.  

---

## 8. Job‑Interview Nuggets  

- **Explain the “why” before the “how”**: Start by describing the connectivity graph, then bring in DSU.  
- **Rollback**: Acknowledge that union‑find isn’t undo‑friendly; discuss either rebuild‑or‑stack‑undo options.  
- **Time‑space trade‑offs**: Show you can switch from a linear restriction scan to a hash‑set if constraints increase.  
- **Testing**: Provide unit tests for “already connected” and “restriction violation” scenarios.  

---

## 9. Wrap‑Up  

You now have:

- A **concise, readable** DSU solution in **three major languages**.  
- A clear explanation of how to **respect restrictions** while merging components.  
- Interview‑ready talking points that demonstrate **data‑structure mastery** and **complexity analysis**.

Drop this article into your portfolio, use it to impress hiring managers, or just for fun – your friends will thank you for demystifying a tricky Leetcode problem!

---

> 🎓 **Happy coding and good luck in your next technical interview!** 🎓

---  

*Feel free to comment below or open a PR if you spot improvements. Let’s keep the algorithmic conversation going!*

--- 

*Tags: #Leetcode #2076 #UnionFind #DisjointSetUnion #AlgorithmDesign #Java #Python #C++ #TechnicalInterview* 

--- 

And that’s it! You’re all set to demonstrate that you can **solve** and **explain** the *Process Restricted Friend Requests* problem like a pro. Happy interviewing! 🚀