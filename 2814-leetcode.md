---
title: LeetCode 2814. Minimum Time Takes to Reach Destination Without Drowning - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2814 – “Minimum Time Takes to Reach Destination Without Drowning”

> **Goal:** Find the minimum number of seconds needed to walk from **S** to **D** on an `n × m` grid while avoiding stone (`X`) and flood cells (`*`).  
>  
> The flood spreads each second to all 4‑neighbour empty cells. You cannot step onto a cell that becomes flooded at the same second you step onto it.  
>  
> **Return** the shortest travel time, or `-1` if it is impossible.

---

## TL;DR

* Compute the time each cell will be flooded by a **multi‑source BFS** that starts from every `*`.  
* Run a **second BFS** from **S** where a move to a neighbour is allowed only if:
  * the neighbour isn’t a stone,
  * the neighbour isn’t flooded *before or at* the arrival second,
  * the neighbour is either empty or the destination.
* The first time we reach **D** is the answer.

Time complexity: `O(n · m)`  
Memory complexity: `O(n · m)`

---

## Why is this a great interview problem?

| Good | Bad | Ugly |
|------|-----|------|
| **Multi‑source BFS** – teaches the candidate how to handle simultaneous spread (like fire, infection). | **Boundary checks** can get messy. | **“Step into a cell that is flooded at the same second”** – a subtle corner case that often trips candidates. |
| **Two‑phase approach** – clear separation of “what will happen” (flood) and “what I can do” (move). | **Large grid (100×100)** → must avoid quadratic or exponential algorithms. | **Off‑by‑one errors** in time calculation when flooding and movement happen at the *same* instant. |
| The solution is **scalable** to bigger constraints (e.g., `10^3 × 10^3`). | Need to think about **visited** state; naive `boolean[][]` may not be enough if you allow revisiting at a later time. | **Multiple data structures** (queues, matrices) may clutter the code for a short interview. |

---

## Full Solutions

Below you will find clean, production‑ready code for **Java**, **Python**, and **C++**.  
Each implementation follows the same logic: compute the flood times, then BFS the player.

---

### 1. Java

```java
import java.util.*;

public class Solution {
    private static final int[][] DIRS = {{-1,0},{1,0},{0,-1},{0,1}};
    private static final int INF = Integer.MAX_VALUE;

    public int minimumSeconds(List<List<String>> land) {
        int n = land.size();
        int m = land.get(0).size();

        // 1️⃣ Flood times: multi‑source BFS from all '*'
        int[][] flood = new int[n][m];
        for (int i = 0; i < n; i++) Arrays.fill(flood[i], INF);

        Queue<int[]> q = new ArrayDeque<>();
        int startR = 0, startC = 0, destR = 0, destC = 0;

        for (int r = 0; r < n; r++) {
            for (int c = 0; c < m; c++) {
                String cell = land.get(r).get(c);
                if ("*".equals(cell)) {
                    flood[r][c] = 0;
                    q.add(new int[]{r, c});
                } else if ("S".equals(cell)) {
                    startR = r; startC = c;
                } else if ("D".equals(cell)) {
                    destR = r; destC = c;
                }
            }
        }

        while (!q.isEmpty()) {
            int[] cur = q.poll();
            int r = cur[0], c = cur[1], t = flood[r][c];
            for (int[] d : DIRS) {
                int nr = r + d[0], nc = c + d[1];
                if (0 <= nr && nr < n && 0 <= nc && nc < m &&
                    (land.get(nr).get(nc).equals(".") || land.get(nr).get(nc).equals("S")) &&
                    flood[nr][nc] == INF) {
                    flood[nr][nc] = t + 1;
                    q.add(new int[]{nr, nc});
                }
            }
        }

        // 2️⃣ Player BFS
        boolean[][] vis = new boolean[n][m];
        Queue<int[]> pq = new ArrayDeque<>();
        pq.add(new int[]{startR, startC, 0}); // r, c, time
        vis[startR][startC] = true;

        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            int r = cur[0], c = cur[1], t = cur[2];
            if (r == destR && c == destC) return t;

            for (int[] d : DIRS) {
                int nr = r + d[0], nc = c + d[1];
                int nt = t + 1;
                if (0 <= nr && nr < n && 0 <= nc && nc < m &&
                    !vis[nr][nc] &&
                    !land.get(nr).get(nc).equals("X") &&
                    (land.get(nr).get(nc).equals(".") || land.get(nr).get(nc).equals("D")) &&
                    flood[nr][nc] > nt) {   // never flooded before arrival
                    vis[nr][nc] = true;
                    pq.add(new int[]{nr, nc, nt});
                }
            }
        }

        return -1; // unreachable
    }
}
```

---

### 2. Python

```python
from collections import deque
from typing import List

class Solution:
    DIRS = [(-1, 0), (1, 0), (0, -1), (0, 1)]
    INF = 10 ** 9

    def minimumSeconds(self, land: List[List[str]]) -> int:
        n, m = len(land), len(land[0])

        # 1️⃣ Flood times
        flood = [[self.INF] * m for _ in range(n)]
        q = deque()
        sr = sc = dr = dc = 0

        for i in range(n):
            for j in range(m):
                if land[i][j] == '*':
                    flood[i][j] = 0
                    q.append((i, j))
                elif land[i][j] == 'S':
                    sr, sc = i, j
                elif land[i][j] == 'D':
                    dr, dc = i, j

        while q:
            r, c = q.popleft()
            t = flood[r][c]
            for drc, dcc in self.DIRS:
                nr, nc = r + drc, c + dcc
                if 0 <= nr < n and 0 <= nc < m and \
                   (land[nr][nc] in {'.', 'S'}) and flood[nr][nc] == self.INF:
                    flood[nr][nc] = t + 1
                    q.append((nr, nc))

        # 2️⃣ Player BFS
        vis = [[False] * m for _ in range(n)]
        q = deque([(sr, sc, 0)])  # r, c, time
        vis[sr][sc] = True

        while q:
            r, c, t = q.popleft()
            if r == dr and c == dc:
                return t
            for drc, dcc in self.DIRS:
                nr, nc = r + drc, c + dcc
                nt = t + 1
                if 0 <= nr < n and 0 <= nc < m and \
                   not vis[nr][nc] and \
                   land[nr][nc] != 'X' and \
                   (land[nr][nc] in {'.', 'D'}) and \
                   flood[nr][nc] > nt:            # safe to step in
                    vis[nr][nc] = True
                    q.append((nr, nc, nt))

        return -1
```

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    const int INF = 1e9;
    vector<pair<int,int>> dirs = {{-1,0},{1,0},{0,-1},{0,1}};

    int minimumSeconds(vector<vector<char>>& land) {
        int n = land.size(), m = land[0].size();

        // ---------- 1️⃣ Flood Times ----------
        vector<vector<int>> flood(n, vector<int>(m, INF));
        queue<pair<int,int>> q;
        int sr=0, sc=0, dr=0, dc=0;

        for (int r=0; r<n; ++r) {
            for (int c=0; c<m; ++c) {
                if (land[r][c]=='*') {
                    flood[r][c]=0;
                    q.emplace(r,c);
                } else if (land[r][c]=='S') {
                    sr=r; sc=c;
                } else if (land[r][c]=='D') {
                    dr=r; dc=c;
                }
            }
        }

        while (!q.empty()) {
            auto [r,c] = q.front(); q.pop();
            int t = flood[r][c];
            for (auto [drc,dcc]: dirs) {
                int nr=r+drc, nc=c+dcc;
                if (nr>=0 && nr<n && nc>=0 && nc<m &&
                    (land[nr][nc]=='.' || land[nr][nc]=='S') &&
                    flood[nr][nc]==INF) {
                    flood[nr][nc]=t+1;
                    q.emplace(nr,nc);
                }
            }
        }

        // ---------- 2️⃣ Player BFS ----------
        vector<vector<bool>> vis(n, vector<bool>(m,false));
        queue<tuple<int,int,int>> pq;   // r,c,time
        pq.emplace(sr,sc,0);
        vis[sr][sc]=true;

        while (!pq.empty()) {
            auto [r,c,t] = pq.front(); pq.pop();
            if (r==dr && c==dc) return t;
            for (auto [drc,dcc]: dirs) {
                int nr=r+drc, nc=c+dcc;
                int nt=t+1;
                if (nr>=0 && nr<n && nc>=0 && nc<m &&
                    !vis[nr][nc] &&
                    land[nr][nc]!='X' &&
                    (land[nr][nc]=='.' || land[nr][nc]=='D') &&
                    flood[nr][nc]>nt) {
                    vis[nr][nc]=true;
                    pq.emplace(nr,nc,nt);
                }
            }
        }
        return -1;
    }
};
```

---

## Common Pitfalls & How to Avoid Them

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Off‑by‑one when comparing flood time** | Flood spreads *after* you move in the same second | Use `flood[nr][nc] > nt` (arrival time) instead of `>=`. |
| **Staying on a flooded cell** | The player may wait on a cell that will flood next second | We never enqueue the same cell twice; the first arrival is always the earliest. |
| **Handling the destination** | Some candidates forget that stepping into **D** is always allowed, even if flood time is `0` | Explicitly allow `cell == 'D'` regardless of flood time. |
| **Large INF value** | `INT_MAX` can overflow when adding 1 | Use a sentinel value (`INF = 1e9` or `INT_MAX / 2`) that can safely be incremented. |
| **Visited array** | Re‑entering a cell later may be optimal (but never needed here) | A simple `bool vis[n][m]` suffices because the second BFS is *shortest‑path first* – any later entry would be slower. |

---

## Complexity Analysis

| Step | Operations | Complexity |
|------|------------|------------|
| Flood BFS | Each cell is dequeued once, 4 neighbours checked → `4·n·m` checks | `O(n·m)` |
| Player BFS | Same reasoning as flood BFS | `O(n·m)` |
| Memory | Two `int` matrices (`flood` and `vis`) | `O(n·m)` |

For the maximum constraints (`100 × 100`), the algorithm finishes in a few milliseconds and uses < 1 MB of memory – perfectly acceptable for an interview.

---

## Ready‑to‑Use Template for Interviews

During a timed interview you don’t want to spend time building a UI, writing input parsers, etc.  
Below is a **minimal template** you can paste into the LeetCode editor (Java, Python, or C++).

| Language | Template |
|----------|----------|
| **Java** | `public class Solution { public int minimumSeconds(List<List<String>> land) { /* … */ } }` |
| **Python** | `class Solution: def minimumSeconds(self, land: List[List[str]]) -> int: /* … */` |
| **C++** | `class Solution { public: int minimumSeconds(vector<vector<char>>& land) { /* … */ } };` |

Just replace the comment `/* … */` with the full code shown above.

---

## Interview‑Friendly Checklist

1. **State the two‑phase idea** (first flood, then move).  
2. **Show BFS** – you can walk through how to initialise the queue and visit neighbours.  
3. **Explain the time condition**: `floodTime[neighbour] > currentTime + 1`.  
4. **Talk about corner cases**:  
   * If the start cell is surrounded by flood cells → impossible.  
   * If **D** is on the same row/col as a `*` → you must arrive *before* it floods.  
5. **Complexity**: `O(n · m)` is the best you can do – the grid is the limiting factor.  
6. **Ask clarifying questions** (e.g., “Can we walk diagonally?” – they can’t).  

---

## SEO‑Friendly Takeaway

If you’re looking to *land that next tech job* or *crush your coding interview*, highlight LeetCode 2814 in your résumé:

- **Keywords:** `LeetCode 2814`, `minimum time to reach destination without drowning`, `multi‑source BFS`, `Java BFS flood spread`, `Python BFS solution`, `C++ flood BFS`, `coding interview problem`, `coding challenge interview`.

- **Why it matters:** Demonstrates *problem decomposition*, *algorithmic thinking*, and *clean implementation* skills—all of which interviewers value highly.

---

## 🎯 Final Thought

LeetCode 2814 is more than a flood‑escape puzzle; it’s a **mini‑case study** of how to combine **simulation** (the flood) and **search** (your path) in a single, efficient solution.  

**Show the interviewers** that you can:

1. Break a problem into **clear phases**.  
2. Use **BFS** – the workhorse of many interview problems.  
3. Handle subtle **time‑synchronisation** constraints.

Good luck, and remember – the flood is only a metaphor for real‑world constraints; keep calm and code on! 🌊🛤️