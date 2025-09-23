---
title: LeetCode 2039. The Time When the Network Becomes Idle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 2039. The Time When the Network Becomes Idle  
> **Medium – LeetCode** | **Java / Python / C++** | **BFS + Math**  

The problem is a classic interview‑style graph problem that tests your ability to:

* Build an undirected graph from edge lists  
* Compute shortest distances with a single BFS  
* Reason about time‑based event simulation without actually simulating every second  

Below you’ll find clean, production‑ready implementations in **Java, Python, and C++**.  
After the code we dive into a **SEO‑friendly blog post** that explains the algorithm, the edge cases you should watch out for, and why this solution is fast enough for the problem’s limits (up to 10⁵ nodes/edges).

---

## 🚀 1.  Solution Overview

| Step | What we do | Why it works |
|------|------------|--------------|
| 1 | **Build an adjacency list** for the undirected graph. | Allows O(1) neighbor traversal. |
| 2 | **BFS from node 0** to find the shortest hop count (distance) to every other server. | The graph is unweighted; BFS gives exact shortest path lengths in O(N+E). |
| 3 | For each data server *i* (i > 0) compute: <br>• *roundTrip* = 2 × dist[i]  <br>• *lastSend* = ⌊(roundTrip – 1) / patience[i]⌋ × patience[i] <br>• *idleTime* = lastSend + roundTrip | *lastSend* is the timestamp of the last message the server resends *before* the master’s reply arrives. |
| 4 | Answer = **max(idleTime) + 1**. | Network is idle when the **last** reply has arrived; we add 1 because time starts at 0. |

The algorithm runs in **O(N + E)** time and uses **O(N + E)** memory.

---

## 🖥️ 2.  Code

### Java

```java
import java.util.*;

class Solution {
    public int networkBecomesIdle(int[][] edges, int[] patience) {
        int n = patience.length;

        // 1. Build adjacency list
        List<Integer>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();
        for (int[] e : edges) {
            g[e[0]].add(e[1]);
            g[e[1]].add(e[0]);
        }

        // 2. BFS from master (node 0)
        int[] dist = new int[n];
        Arrays.fill(dist, -1);
        Queue<Integer> q = new ArrayDeque<>();
        q.add(0);
        dist[0] = 0;
        while (!q.isEmpty()) {
            int u = q.poll();
            for (int v : g[u]) if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                q.add(v);
            }
        }

        // 3. Compute last idle time
        int answer = 0;
        for (int i = 1; i < n; i++) {
            int roundTrip = 2 * dist[i];
            int lastSend = ((roundTrip - 1) / patience[i]) * patience[i];
            int idle = lastSend + roundTrip;
            answer = Math.max(answer, idle);
        }
        return answer + 1;        // +1 because idle starts after the last reply
    }
}
```

### Python

```python
from collections import deque
from typing import List

class Solution:
    def networkBecomesIdle(self, edges: List[List[int]], patience: List[int]) -> int:
        n = len(patience)

        # 1. adjacency list
        g = [[] for _ in range(n)]
        for u, v in edges:
            g[u].append(v)
            g[v].append(u)

        # 2. BFS distances
        dist = [-1] * n
        dist[0] = 0
        q = deque([0])
        while q:
            u = q.popleft()
            for v in g[u]:
                if dist[v] == -1:
                    dist[v] = dist[u] + 1
                    q.append(v)

        # 3. compute idle times
        ans = 0
        for i in range(1, n):
            round_trip = 2 * dist[i]
            last_send = ((round_trip - 1) // patience[i]) * patience[i]
            idle_time = last_send + round_trip
            ans = max(ans, idle_time)

        return ans + 1
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int networkBecomesIdle(vector<vector<int>>& edges, vector<int>& patience) {
        int n = patience.size();
        vector<vector<int>> g(n);
        for (auto &e : edges) {
            g[e[0]].push_back(e[1]);
            g[e[1]].push_back(e[0]);
        }

        vector<int> dist(n, -1);
        queue<int> q;
        dist[0] = 0; q.push(0);
        while (!q.empty()) {
            int u = q.front(); q.pop();
            for (int v : g[u]) if (dist[v] == -1) {
                dist[v] = dist[u] + 1;
                q.push(v);
            }
        }

        int ans = 0;
        for (int i = 1; i < n; ++i) {
            int roundTrip = 2 * dist[i];
            int lastSend = ((roundTrip - 1) / patience[i]) * patience[i];
            int idle = lastSend + roundTrip;
            ans = max(ans, idle);
        }
        return ans + 1;
    }
};
```

---

## 📄 3.  SEO‑Optimized Blog Post

### Title  
**LeetCode 2039 – “The Time When the Network Becomes Idle”**  
*Fast BFS Solution (Java, Python, C++) – Master Interview Algorithms*

---

### Meta Description  
Solve LeetCode 2039 “The Time When the Network Becomes Idle” with an optimal BFS + math algorithm. Read our step‑by‑step explanation, Java/Python/C++ code, and interview‑ready tips. Perfect for backend developers and network engineers!

---

### Introduction  

LeetCode 2039 asks you to find the earliest second when a network of servers becomes idle after a series of message exchanges. Though the problem description looks like a simulation puzzle, a clean mathematical approach with BFS gives an O(N+E) solution. This post walks through that approach, the intuition behind the key formula, and why the code runs fast enough for the upper limits (10⁵ nodes and edges).

---

## 🚀 Problem Recap

* **Graph** – Undirected, unweighted.  
* **Master server** – node 0.  
* **Data servers** – nodes 1…n‑1.  
* **Messages** – each data server sends its first message at time 0.  
* **Resend rule** – every *patience[i]* seconds after the last send, if no reply has arrived.  
* **Goal** – earliest second when no messages are in transit.

---

## 📌 Core Insight

1. **Shortest distance matters** – because every message follows the *shortest* path.  
2. **Round‑trip time** = `2 * dist[i]`.  
3. **Last resend before reply** – the largest multiple of `patience[i]` that is *strictly* before the reply arrives.  
   ```
   lastSend = ⌊(roundTrip – 1) / patience[i]⌋ * patience[i]
   ```
4. **Idle time of server i** = `lastSend + roundTrip`.  
5. **Network idle** = `max(idleTimes) + 1`.

Why the floor formula?  
If a reply would arrive at time `roundTrip`, any resend at `roundTrip` or later is useless.  
Thus we find the greatest send time that is `< roundTrip`.  
The `(roundTrip – 1)` trick guarantees that if `roundTrip` is exactly a multiple of `patience[i]` we *do not* count that last send.

---

## 🛠️ Algorithm Walk‑through

### 1. Build the Graph  
Adjacency lists are the simplest way to represent sparse undirected graphs.  
```cpp
for (auto &e : edges) {
    g[e[0]].push_back(e[1]);
    g[e[1]].push_back(e[0]);
}
```

### 2. BFS for Distances  
Because edges are unweighted, a single BFS from node 0 gives `dist[v]` for every server `v`.  
```python
while q:
    u = q.popleft()
    for v in g[u]:
        if dist[v] == -1:
            dist[v] = dist[u] + 1
            q.append(v)
```

### 3. Compute Idle Times  
For each server `i` > 0:
```text
roundTrip  = 2 * dist[i]
lastSend   = floor((roundTrip - 1) / patience[i]) * patience[i]
idleTime   = lastSend + roundTrip
```
Take the maximum over all servers and add 1.

---

## ⏱️ Complexity Analysis

| Complexity | Java | Python | C++ |
|------------|------|--------|-----|
| Time | O(N+E) | O(N+E) | O(N+E) |
| Space | O(N+E) | O(N+E) | O(N+E) |

All three implementations use a single queue, an adjacency list, and a distance array. Even on the worst case (`N = E = 10⁵`), the BFS visits each edge twice – far below the 1‑second runtime limit of LeetCode.

---

## ⚠️ “Good, Bad, Ugly” Edge Cases

| Situation | What to Watch For | Fix / Mitigation |
|-----------|-------------------|------------------|
| **patience[i] = 0** | Division by zero – not allowed by constraints (`patience[i] >= 1`). | Add a guard or rely on problem guarantee. |
| **roundTrip = 1** | `roundTrip-1` becomes 0; formula still works. | The floor division handles zero correctly. |
| **Very large patience** | `patience[i] > roundTrip` → server never resends. | The formula yields `lastSend = 0`. |
| **Disconnected graphs** | Problem guarantees connectivity to node 0. | Still check `dist[i] != -1` to be safe. |
| **Huge N/E** | Stack recursion (DFS) would overflow. | Use iterative BFS – no recursion. |

---

## 🎯 Why This Passes LeetCode’s Limits

* **BFS** is linear in edges, not in seconds.  
* All arithmetic is constant time per node.  
* No simulation of each second means the algorithm runs in micro‑seconds even for 10⁵ nodes.

---

## 📚 Takeaway Checklist

- [ ] Build adjacency list (O(N+E))
- [ ] BFS from master to get shortest hops
- [ ] Compute `roundTrip`, `lastSend`, `idleTime`
- [ ] Return `max(idleTime) + 1`
- [ ] No recursive DFS → avoid stack overflow
- [ ] Test with:
  * All servers have `patience = 1` (worst‑case resends)  
  * Some servers with enormous patience (no resends)  
  * Large tree vs dense graph

---

### 🔗 Final Words

LeetCode 2039 is a *network‑theory* interview question disguised as a simulation. By focusing on **shortest paths** and a neat arithmetic trick, you can solve it in a single pass. The Java, Python, and C++ implementations above are ready for a code‑interview submission and will pass the problem’s strict time/memory limits.

Happy coding! 🚀

---

### 📌 Keywords for SEO

- LeetCode 2039  
- Time When the Network Becomes Idle  
- BFS solution  
- Java LeetCode 2039  
- Python LeetCode 2039  
- C++ LeetCode 2039  
- Network idle algorithm  
- Interview graph algorithm  
- Shortest path BFS  
- Backend interview questions  

Feel free to copy the code snippets, tweak them for your personal coding style, or use them as a study reference. Good luck on your next interview!