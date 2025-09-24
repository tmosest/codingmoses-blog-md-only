---
title: LeetCode 2092. Find All People With Secret - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2092 – Find All People With Secret  
### Solution in **Java**, **Python**, and **C++** (Union‑Find + Time Batching)  
---

### TL;DR  
The secret spreads only **at the exact time of a meeting**.  
If several meetings happen at the same time, everyone who already has the secret can instantly spread it to **all** participants of that *time‑batch*.  
The problem can be solved in **O(n + m log m)** time and **O(n + m)** memory by:

1. **Sorting** meetings by time.  
2. **Processing** each time‑batch together using a **Disjoint Set Union (DSU)** (Union‑Find).  
3. At the end, all nodes whose representative is `0` (person 0) know the secret.

The DSU is reset after each batch so that people who did **not** receive the secret during that time are isolated again.

---

## 1️⃣ Java Implementation

```java
import java.util.*;

public class Solution {
    // ---------- Disjoint Set Union ----------
    private static class DSU {
        int[] parent, rank;
        DSU(int n) {
            parent = new int[n];
            rank   = new int[n];
            for (int i = 0; i < n; i++) parent[i] = i;
        }
        int find(int x) {
            if (parent[x] != x) parent[x] = find(parent[x]);
            return parent[x];
        }
        void union(int a, int b) {
            int pa = find(a), pb = find(b);
            if (pa == pb) return;
            if (rank[pa] < rank[pb]) parent[pa] = pb;
            else if (rank[pb] < rank[pa]) parent[pb] = pa;
            else { parent[pb] = pa; rank[pa]++; }
        }
    }

    public List<Integer> findAllPeople(int n, int[][] meetings, int firstPerson) {
        // 1️⃣ Sort by time
        Arrays.sort(meetings, Comparator.comparingInt(a -> a[2]));

        DSU dsu = new DSU(n);
        dsu.union(0, firstPerson);          // person 0 gives the secret at time 0

        int i = 0, m = meetings.length;
        while (i < m) {
            int curTime = meetings[i][2];
            // keep all participants of this time‑batch
            List<Integer> participants = new ArrayList<>();

            // 2️⃣ Union everyone that meets at this time
            while (i < m && meetings[i][2] == curTime) {
                int a = meetings[i][0];
                int b = meetings[i][1];
                dsu.union(a, b);
                participants.add(a);
                participants.add(b);
                i++;
            }

            // 3️⃣ Reset DSU for people who did NOT get the secret
            for (int p : participants) {
                if (dsu.find(p) != dsu.find(0))
                    dsu.parent[p] = p;  // isolated again
            }
        }

        // 4️⃣ Collect all who are connected to 0
        List<Integer> res = new ArrayList<>();
        for (int i1 = 0; i1 < n; i1++)
            if (dsu.find(i1) == dsu.find(0)) res.add(i1);

        return res;
    }
}
```

**Complexity**  
- `O((n + m) log m)` time (sorting dominates)  
- `O(n + m)` memory (DSU + meetings array)

---

## 2️⃣ Python Implementation

```python
from collections import defaultdict
import sys
sys.setrecursionlimit(1 << 25)

class DSU:
    def __init__(self, n):
        self.par = list(range(n))
        self.rnk = [0] * n

    def find(self, x):
        if self.par[x] != x:
            self.par[x] = self.find(self.par[x])
        return self.par[x]

    def union(self, a, b):
        pa, pb = self.find(a), self.find(b)
        if pa == pb: return
        if self.rnk[pa] < self.rnk[pb]:
            self.par[pa] = pb
        elif self.rnk[pb] < self.rnk[pa]:
            self.par[pb] = pa
        else:
            self.par[pb] = pa
            self.rnk[pa] += 1

def findAllPeople(n: int, meetings: list[list[int]], firstPerson: int) -> list[int]:
    meetings.sort(key=lambda x: x[2])          # sort by time
    dsu = DSU(n)
    dsu.union(0, firstPerson)                 # initial secret transfer

    i = 0
    while i < len(meetings):
        cur_time = meetings[i][2]
        participants = []

        # union all meetings that happen at the same time
        while i < len(meetings) and meetings[i][2] == cur_time:
            a, b, _ = meetings[i]
            dsu.union(a, b)
            participants.extend([a, b])
            i += 1

        # reset nodes that didn't get the secret during this batch
        for p in participants:
            if dsu.find(p) != dsu.find(0):
                dsu.par[p] = p

    return [i for i in range(n) if dsu.find(i) == dsu.find(0)]

# ------------------------------------------------------------------
# Example usage (uncomment to test)
# n = 6
# meetings = [[1,2,5],[2,3,8],[1,5,10]]
# print(findAllPeople(n, meetings, 1))   # -> [0, 1, 2, 3, 5]
```

**Complexity** – identical to the Java solution.

---

## 3️⃣ C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class DSU {
public:
    DSU(int n): parent(n), rank(n, 0) {
        iota(parent.begin(), parent.end(), 0);
    }
    int find(int x) {
        return parent[x]==x ? x : parent[x]=find(parent[x]);
    }
    void unite(int a, int b) {
        int pa=find(a), pb=find(b);
        if(pa==pb) return;
        if(rank[pa]<rank[pb]) parent[pa]=pb;
        else if(rank[pb]<rank[pa]) parent[pb]=pa;
        else { parent[pb]=pa; rank[pa]++; }
    }
private:
    vector<int> parent, rank;
};

class Solution {
public:
    vector<int> findAllPeople(int n, vector<vector<int>>& meetings, int firstPerson) {
        sort(meetings.begin(), meetings.end(),
             [](const auto &a, const auto &b){ return a[2] < b[2]; });

        DSU dsu(n);
        dsu.unite(0, firstPerson);          // secret starts at person 0

        int i = 0;
        while(i < (int)meetings.size()) {
            int cur = meetings[i][2];
            vector<int> part;              // participants in this time‑batch

            while(i < (int)meetings.size() && meetings[i][2] == cur) {
                int a = meetings[i][0];
                int b = meetings[i][1];
                dsu.unite(a, b);
                part.push_back(a);
                part.push_back(b);
                ++i;
            }

            for(int p : part)                // reset those who didn't get secret
                if(dsu.find(p) != dsu.find(0))
                    dsu.unite(p, p);          // set parent to itself
        }

        vector<int> ans;
        for(int j=0;j<n;++j)
            if(dsu.find(j) == dsu.find(0))
                ans.push_back(j);
        return ans;
    }
};
```

> ⚡️ **Tip**: In C++ you can skip the “reset” step by using a *stack* of nodes that change parents during a batch, but the above is easier to read.

---

## 📚 Blog Article – “The Secret Spread: A Deep Dive into LeetCode 2092”

### Title
**“The Secret Spread: 2092 – From BFS to Union‑Find – The Good, The Bad, The Ugly”**

### Meta Description
Learn how to crack LeetCode 2092 in O(n+m log m) time. Read the deep dive into BFS, DSU, and time‑batching strategies. Perfect for interview prep, coding interviews, and job‑searching engineers.

---

### Introduction

> **Problem Recap**  
> You’re given `n` people, a list of meetings `(x, y, time)`, and a `firstPerson`. Person `0` has a secret at time `0` and passes it to `firstPerson`. Whenever a meeting takes place, anyone who already knows the secret shares it with the other attendee instantaneously.  
> **Goal** – return all people who know the secret after all meetings.

> **Why it matters** –  
> 1. **Concurrency** – multiple meetings at the same time.  
> 2. **Graph propagation** – a classic “reachability” problem, but with a twist: propagation happens *only* at specific timestamps.  
> 3. **Interview challenge** – requires careful thought about *when* you can spread the secret, not just *who* can.

---

### The Naïve View: Breadth‑First Search (BFS)

A first instinct is to treat every meeting as an edge in a graph and run a BFS from the source `(0, firstPerson)`.

```text
time   0: 0 -> firstPerson
time   t: BFS from all who know the secret at this time
```

**Problem** – BFS assumes a *static* graph. In our problem, the edges only become relevant **once** at the meeting time, and they can be “turned off” if the secret never reached that edge. Running BFS once for each time stamp would lead to a **O(n · m)** algorithm – far too slow for the constraints (`m` up to `2·10⁵`).

---

### The Good – Union‑Find + Time‑Batching

#### 1️⃣ Sort & Batch

Sort all meetings by `time`. All meetings that share the same `time` form a *batch*.

#### 2️⃣ DSU within a Batch

During a batch, if **any** participant knows the secret, the secret propagates to **every** connected component of that batch instantly.  
Union‑Find allows us to *merge* all participants of a batch in near‑constant amortized time.

#### 3️⃣ Reset After the Batch

After we processed the batch, we **must isolate** those who did **not** get the secret.  
In code this looks like:

```pseudo
for each participant p in this batch:
    if find(p) != find(0):
        parent[p] = p          // reset to itself
```

This trick guarantees that the next time‑batch starts from scratch for everyone who didn’t receive the secret.

---

### The Bad – Why BFS is a Dead End

| Approach | Complexity | Why it Fails |
|----------|------------|--------------|
| Pure BFS | `O(n · m)` | Too slow when `m` is huge. |
| BFS + Sorting | `O(m log m + n)` | Still `O(n·m)` because we need to recompute the reachability from scratch for every time‑stamp. |

In short, BFS ignores the *time* dimension and therefore cannot handle simultaneous meetings efficiently.

---

### The Ugly – Edge Cases that Break Simple Code

| Edge Case | Problem | Fix |
|-----------|---------|-----|
| **Meetings with the same pair at different times** | DSU can incorrectly remember past unions. | Reset parents *after* every batch. |
| **Large input size** | Recursion depth in DSU `find` overflows in Java/Python. | Use path compression and iterative loops or raise recursion limits. |
| **Multiple visits to the same person in one batch** | Adding the same participant twice can inflate the `participants` list unnecessarily. | Keep a `std::unordered_set<int>` (or `HashSet`) per batch to store unique nodes. |

---

### Complexity Breakdown

| Step | Operation | Time |
|------|-----------|------|
| Sort meetings | `m log m` | Dominant |
| DSU operations | `α(n)` amortized per union | `O(n + m)` |
| Reset loops | at most `2 · m` per batch | `O(n + m)` |
| Final scan | `n` finds | `O(n)` |

> **Result** – `O((n+m) log m)` time, `O(n+m)` space.

---

### Take‑away Tips for Interviews

1. **Explain the time‑batch idea first.** Interviewers love to see you understood why “simultaneous meetings” are special.
2. **Show the DSU reset trick** – it’s the *Easter egg* that turns a naive DSU solution into the optimal one.
3. **Mention alternatives** (BFS with a queue of time‑stamped events, or a graph per timestamp). This shows you’re aware of the landscape.
4. **Discuss space optimization** – e.g., re‑using the meetings vector, or using a vector of “changed nodes” instead of resetting the whole parent array.
5. **Practice on similar problems** – e.g., *BFS with constraints*, *Union‑Find with time windows*.

---

### Conclusion

LeetCode 2092 teaches you how to blend two classic concepts:

- **BFS** (reachability)  
- **Union‑Find** (connected components)

…and to combine them with a **time‑batching trick** that respects the problem’s concurrency rules.  
Mastering this pattern will give you confidence for questions about *temporal networks*, *event‑driven propagation*, and *dynamic connectivity* in technical interviews.

---

### References & Further Reading

- [Union‑Find (DSU) – Wikipedia](https://en.wikipedia.org/wiki/Disjoint-set_data_structure)
- [BFS on graphs – LeetCode](https://leetcode.com/problems/breadth-first-search/)
- [Time‑Batched Graph Problems – Codeforces EDU](https://codeforces.com/blog/entry/11978)

Happy coding! 🚀