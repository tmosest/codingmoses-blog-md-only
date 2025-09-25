---
title: LeetCode 1102. Path With Maximum Minimum Value - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code Solutions  

Below are three clean, production‑ready implementations of **LeetCode 1102 – Path With Maximum Minimum Value**.  
All three use a *max‑heap priority‑queue* (Dijkstra‑style) to explore the grid in order of the highest possible “minimum‑value‑so‑far” at each step.  
The approach works in **O(m n log(m n))** time and **O(m n)** space, where *m* and *n* are the grid dimensions.

---

### 1.1  Java – 1‑Liner Style

```java
import java.util.*;

public class Solution {
    private static final int[][] DIRS = {{0,1},{1,0},{0,-1},{-1,0}};

    public int maximumMinimumPath(int[][] grid) {
        int m = grid.length, n = grid[0].length;
        boolean[][] visited = new boolean[m][n];

        // max‑heap: element = {row, col, min‑value‑so‑far}
        PriorityQueue<int[]> pq = new PriorityQueue<>(
            (a, b) -> Integer.compare(b[2], a[2])
        );
        pq.offer(new int[]{0, 0, grid[0][0]});
        visited[0][0] = true;

        while (!pq.isEmpty()) {
            int[] cur = pq.poll();
            int r = cur[0], c = cur[1], score = cur[2];

            // Reached the target
            if (r == m - 1 && c == n - 1) return score;

            for (int[] d : DIRS) {
                int nr = r + d[0], nc = c + d[1];
                if (nr < 0 || nr >= m || nc < 0 || nc >= n || visited[nr][nc]) continue;
                visited[nr][nc] = true;
                int newScore = Math.min(score, grid[nr][nc]);
                pq.offer(new int[]{nr, nc, newScore});
            }
        }
        return -1; // unreachable – should never happen with valid input
    }
}
```

---

### 1.2  Python – 3.10+

```python
import heapq
from typing import List

class Solution:
    def maximumMinimumPath(self, grid: List[List[int]]) -> int:
        m, n = len(grid), len(grid[0])
        visited = [[False] * n for _ in range(m)]

        # Python's heapq is a min‑heap; we store negative values to simulate a max‑heap
        pq = [(-grid[0][0], 0, 0)]          # (neg_score, row, col)
        visited[0][0] = True

        dirs = [(0, 1), (1, 0), (0, -1), (-1, 0)]

        while pq:
            neg_score, r, c = heapq.heappop(pq)
            score = -neg_score

            if r == m - 1 and c == n - 1:
                return score

            for dr, dc in dirs:
                nr, nc = r + dr, c + dc
                if nr < 0 or nr >= m or nc < 0 or nc >= n or visited[nr][nc]:
                    continue
                visited[nr][nc] = True
                new_score = min(score, grid[nr][nc])
                heapq.heappush(pq, (-new_score, nr, nc))

        return -1  # Should never hit this with a valid grid
```

---

### 1.3  C++17 – Modern STL

```cpp
#include <queue>
#include <vector>
#include <algorithm>

class Solution {
public:
    int maximumMinimumPath(std::vector<std::vector<int>>& grid) {
        const std::vector<std::pair<int,int>> dirs{{0,1},{1,0},{0,-1},{-1,0}};
        int m = grid.size(), n = grid[0].size();

        // priority queue holds tuples: (min_value, row, col).  Greater min_value has higher priority.
        struct Node {
            int r, c, score;
            bool operator<(Node const& o) const { return score < o.score; }
        };
        std::priority_queue<Node> pq;
        std::vector<std::vector<bool>> vis(m, std::vector<bool>(n,false));

        pq.push({0,0,grid[0][0]});
        vis[0][0] = true;

        while (!pq.empty()) {
            Node cur = pq.top(); pq.pop();
            if (cur.r == m-1 && cur.c == n-1) return cur.score;

            for (auto [dr, dc] : dirs) {
                int nr = cur.r + dr, nc = cur.c + dc;
                if (nr<0 || nr>=m || nc<0 || nc>=n || vis[nr][nc]) continue;
                vis[nr][nc] = true;
                int ns = std::min(cur.score, grid[nr][nc]);
                pq.push({nr,nc,ns});
            }
        }
        return -1;
    }
};
```

---

## 2.  Blog Article – “The Good, the Bad & the Ugly of LeetCode 1102”

> **Title**:  
> **“LeetCode 1102 Explained: Path With Maximum Minimum Value – Dijkstra, Union‑Find, & Binary Search Strategies”**

> **Meta‑description**:  
> Master the interview‑favorite grid‑path problem with clear code snippets, complexity breakdown, and real‑world pitfalls. Perfect for developers preparing for top‑tech interviews.  

---

### 2.1  Introduction

In software interviews, *grid‑based path problems* are a recurring theme.  
**LeetCode 1102** asks for the **maximum possible “minimum value”** along any path from the top‑left to the bottom‑right cell.  
While the statement looks simple, the subtlety of *maximizing the minimum* pushes candidates to think beyond straight‑forward BFS.

This article walks you through three mainstream solutions, evaluates their trade‑offs, and shares interview‑savvy tips that can land you a role at the likes of Google, Amazon, or Microsoft.

---

### 2.2  Problem Recap

> **Input**: 2‑D integer array `grid` (`1 ≤ grid[i][j] ≤ 10⁶`)  
> **Goal**: Find a path from `(0, 0)` to `(m‑1, n‑1)` that **maximizes** the minimum cell value encountered.  
> **Output**: The value of that maximum “minimum” along an optimal path.  

The four cardinal moves (up, down, left, right) are allowed; the grid is always reachable.

---

### 2.3  Three Winning Strategies

| Strategy | Core Idea | Pros | Cons | When to Pick |
|----------|-----------|------|------|--------------|
| **1️⃣ Dijkstra / Max‑Heap** | Keep a priority‑queue of cells ordered by the highest *minimum‑value‑so‑far*. When a cell is popped, we know we have reached it with the best possible score. | Simple to implement, no extra data structures beyond the heap & visited array. | Requires O(m n log(m n)) – heavy for huge grids. | Small‑to‑medium grids, when you want clean, readable code. |
| **2️⃣ Binary Search + Union‑Find (Disjoint Set)** | Binary‑search over possible “minimum” values `x`. For each `x`, flood‑fill all cells `≥ x` using DSU. If start & end belong to the same set, `x` is feasible. | **O(m n α(m n))** time per binary‑search step; overall **O(m n log V)** where *V* is the max cell value. Good for dense grids where union‑find is fast. | More involved; extra bookkeeping for DSU and BFS to find connected components. | Very large grids, tight time constraints, or when interviewers explicitly ask for Union‑Find. |
| **3️⃣ BFS + Priority‑Queue (Lazy Dijkstra)** | Same as (1) but you only push a cell when you discover a *better* score for it. | Avoids pushing duplicate states → fewer heap operations. | Requires an auxiliary score matrix. | When you want to tighten performance slightly, or want to show understanding of “relaxation” concept. |

---

### 2.4  Detailed Walk‑Through of the Dijkstra Solution

1. **Why Dijkstra Works**  
   The path score is defined as `min(value of all cells along the path)`.  
   If you always expand the frontier with the **highest current score**, any alternative path that could yield a better final score must pass through a cell with a higher or equal score.  
   Therefore, the first time you pop the destination, you have found the optimal path.

2. **Data Structures**  
   * **Max‑heap** – keeps the next best frontier.  
   * **Visited/Score matrix** – guarantees we never process a cell with a sub‑optimal score twice.  

3. **Pseudo‑code**  

```
push (0,0, grid[0][0]) into max‑heap
while heap not empty:
    r,c,curScore = pop max
    if (r,c) is goal: return curScore
    for each neighbor (nr,nc):
        if inside grid and not visited:
            newScore = min(curScore, grid[nr][nc])
            push (nr,nc,newScore)
            mark visited
```

4. **Complexity**  
   *Time*: Each of the *m n* cells is inserted/popped once → **O(m n log(m n))**.  
   *Space*: Heap + visited → **O(m n)**.

---

### 2.5  Common Pitfalls & “Ugly” Code Patterns

| Pitfall | Why it’s ugly | Fix |
|---------|---------------|-----|
| **Using a min‑heap without negating values (Python)** | Accidentally finds *minimum* rather than *maximum* scores → wrong answer. | Store negative values or use `heapq.nlargest`. |
| **Not marking visited before pushing** | Duplicate pushes inflate heap size dramatically → TLE. | Set `visited[nr][nc] = true` immediately after pushing. |
| **Assuming the grid is always reachable** | In real interview settings you might be given an unreachable grid. | Add a guard: return `-1` or throw an exception. |
| **Using recursion for BFS** | Stack overflow on large grids. | Use an explicit queue / heap. |
| **Over‑complicating with Union‑Find but not resetting DSU per binary‑search step** | Bugs when re‑using DSU across iterations. | Re‑initialize DSU each time or use a fresh DSU per binary search iteration. |

---

### 2.6  “Show‑off” Interview Tips

1. **Explain the “max‑heap” rationale** – highlight that we’re effectively maximizing the *bottleneck* value.  
2. **Mention edge‑cases** – e.g., single‑cell grid, all cells equal, very large values.  
3. **Discuss time‑space trade‑offs** – for 1000×1000 grid, log factor ≈ 20, still fine in practice.  
4. **Talk about alternate solutions** – Union‑Find + Binary Search if you want to impress with algorithmic diversity.  

---

### 2.7  Quick One‑Liner (Optional)

> ```java
> // Java: priority‑queue with visited array (O(mn log mn))
> public int maximumMinimumPath(int[][] g){...}
> ```

---

## 3.  Blog Post – SEO‑Optimized

> **Title**: *LeetCode 1102 – Path With Maximum Minimum Value: A Deep Dive into Dijkstra, Binary Search & Union‑Find*  

> **Keywords**: LeetCode 1102, Path With Maximum Minimum Value, grid path problems, Dijkstra algorithm interview, Union‑Find interview, binary search interview problems, software interview preparation, top tech company interview questions, algorithm interview questions, coding interview tips.  

---

### 3.1  Introduction

If you’re reading this, you’re probably a developer or data scientist gearing up for **software engineering interviews** at **Google, Amazon, Facebook, Microsoft** or similar.  
One of the most frequently asked interview questions is **LeetCode 1102** – “Path With Maximum Minimum Value”.  
It’s not just a coding exercise; it’s a **bottleneck path** problem that tests your understanding of graph traversal, greedy strategies, and advanced data structures.

This article delivers **clear, concise code snippets in Java, Python, and C++**, an in‑depth complexity analysis, and a “real‑world” perspective on the trade‑offs every candidate should know.

---

### 3.2  The Problem in a Nutshell

You’re given a grid of integers.  
You can move up, down, left, or right.  
Your **path score** is the **minimum** number you encounter along that path.  
You’re asked to **maximize** that minimum.  
In other words: *“Find a path that keeps the lowest cell value as high as possible.”*

This is a classic **bottleneck path problem**.

---

### 3.3  The Classic Dijkstra Solution (Your First Go‑To)

**Why Dijkstra?**  
Because we’re looking for a **maximum bottleneck** value.  
A max‑heap ensures we always explore the frontier with the highest possible score.  
The first time we pop the destination cell, we’re guaranteed optimality.

**Code Snapshot (Java)**

```java
public int maximumMinimumPath(int[][] grid) {
    int m = grid.length, n = grid[0].length;
    boolean[][] visited = new boolean[m][n];
    PriorityQueue<int[]> pq = new PriorityQueue<>((a,b) -> b[2]-a[2]); // [r,c,score]
    pq.offer(new int[]{0,0,grid[0][0]});
    visited[0][0] = true;
    int[] dirs = {0,1,0,-1,0};
    while(!pq.isEmpty()){
        int[] cur = pq.poll();
        if(cur[0]==m-1 && cur[1]==n-1) return cur[2];
        for(int k=0;k<4;k++){
            int nr=cur[0]+dirs[k], nc=cur[1]+dirs[k+1];
            if(nr>=0&&nr<m&&nc>=0&&nc<n&&!visited[nr][nc]){
                visited[nr][nc]=true;
                pq.offer(new int[]{nr,nc,Math.min(cur[2],grid[nr][nc])});
            }
        }
    }
    return -1;
}
```

**Complexity**  
*Time*: **O(m n log(m n))**  
*Space*: **O(m n)** (heap + visited array)

**Why this is *good*:**  
- **Readability**: The algorithm is almost self‑documenting.  
- **Universality**: Works for all grid sizes where `m n log(m n)` fits in 1 second.  
- **Interview‑friendly**: Most interviewers expect a priority‑queue based solution.

---

### 3.4  Binary Search + Union‑Find – The “Efficient” Alternative

Sometimes, the grid can be **very large** (e.g., 10⁴×10⁴), or the interviewer is specifically looking for a DSU based answer.

**Idea**  
1. Binary‑search over answer `x` in `[1, max(grid)]`.  
2. For each `x`, union‑all neighboring cells whose value ≥ x.  
3. After the flood, check if the top‑left and bottom‑right cells are in the same set.  

**Why it’s efficient**  
- DSU operations are nearly **O(1)** (α‑inverse‑Ackermann).  
- The BFS/DFS that builds components runs in **O(m n)** for each binary‑search step.  
- Overall **O(m n log V)** where *V* ≤ 10⁶ → log V ≈ 20, so about 20 full scans.

**Pitfalls**  
- Re‑initializing DSU each iteration.  
- Managing the frontier of cells with values ≥ x.

---

### 3.5  Union‑Find Implementation Sketch

```java
class DSU {
    int[] parent, rank;
    DSU(int N){ parent=new int[N]; rank=new int[N]; for(int i=0;i<N;i++) parent[i]=i;}
    int find(int x){ return parent[x]==x?x:parent[x]=find(parent[x]); }
    void union(int a,int b){ a=find(a); b=find(b); if(a==b) return; if(rank[a]<rank[b]){parent[a]=b;} else if(rank[a]>rank[b]){parent[b]=a;} else{parent[b]=a;rank[a]++;}}
}
```

- Convert 2‑D indices to 1‑D with `idx = r*n + c`.  
- While scanning cells `≥ x`, union them with adjacent eligible cells.  
- After processing all, check `find(start)==find(end)`.

---

### 3.6  “Lazy” Dijkstra – Cutting Heap Duplication

A more advanced variation only pushes a cell when you find a **better** bottleneck for it.  

**Benefits**  
- Fewer pushes → smaller heap → faster runtime.  
- Showcases understanding of “relaxation” from classic shortest‑path theory.

**When to Use**  
If the interview question asks for *optimal space/time*, this “lazy” approach is a good way to demonstrate depth.

---

### 3.7  Final Takeaway

- **LeetCode 1102** is a **grid bottleneck problem** that can be solved cleanly with a max‑heap.  
- For **larger inputs or specialized interview scenarios**, Binary Search + Union‑Find is a powerful competitor.  
- Always be ready to **explain the intuition** behind the chosen data structure; interviewers value conceptual clarity as much as correct code.

---

> **Author**: *Jane Doe – Software Engineer & Interview Coach*  
> **Contact**: `janedoe@example.com` | **LinkedIn**: `linkedin.com/in/janedoe`  

--- 

**End of Blog Post**

--- 

> **Tip**: Use this article in your portfolio or GitHub readme to showcase not only coding skills but also algorithmic thinking – a critical asset for top‑tech roles.  

--- 

> **Call‑to‑Action**:  
> *Ready to tackle your next interview? Grab the code snippets, practice on LeetCode, and share your solutions on Twitter with #LeetCode1102 for community feedback.*  

--- 

*Happy coding!* 🚀

--- 

> *Note*: The article content is fully original and crafted to highlight both the **good** clear code, the **bad** typical mistakes, and the **ugly** pitfalls that can derail your interview. Adjust the level of detail based on your target audience.  

--- 

This completes the *complete set* of answers: efficient code, a deep dive article, and an SEO‑ready blog post.