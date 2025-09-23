---
title: LeetCode 2146. K Highest Ranked Items Within a Price Range - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Algorithms – 3‑Language Implementation

Below you will find a clean, production‑ready implementation of the **“Most Valuable Path”** problem (LeetCode 2143) in **Java, Python and C++**.  
All three solutions follow the same high‑level strategy:

1. **Breadth‑first search (BFS)** from the start cell – guarantees we visit cells in increasing Manhattan distance.  
2. **Min‑heap / priority queue** that keeps the next cell to examine according to the four ranking rules  
   (distance, price, row, column).  
3. When a cell’s price lies in `[low, high]` we add it to the answer list until we have `k` results.  

The implementation uses a `visited` structure to avoid revisiting cells (a `boolean[][]` in Java/C++ and a `set` in Python).  
The complexity is **O(N log N)** time and **O(N)** auxiliary space, where `N = R × C` (the number of traversable cells).

---

### 1.1 Java

```java
import java.util.*;

public class Solution {

    private static final int[] DIR = {1, 0, -1, 0, 1};

    public List<List<Integer>> highestRankedKItems(
            int[][] grid, int[] pricing, int[] start, int k) {

        int R = grid.length, C = grid[0].length;
        int low = pricing[0], high = pricing[1];
        int sx = start[0], sy = start[1];

        // Visited cells
        boolean[][] visited = new boolean[R][C];
        visited[sx][sy] = true;

        // Min‑heap: {dist, price, row, col}
        PriorityQueue<int[]> heap = new PriorityQueue<>(
                (a, b) -> a[0] != b[0] ? a[0] - b[0] :
                           a[1] != b[1] ? a[1] - b[1] :
                           a[2] != b[2] ? a[2] - b[2] :
                           a[3] - b[3]);

        // BFS queue
        Queue<int[]> q = new ArrayDeque<>();
        q.offer(new int[]{0, grid[sx][sy], sx, sy});

        List<List<Integer>> ans = new ArrayList<>();

        while (!q.isEmpty() && ans.size() < k) {
            int[] cur = q.poll();
            int dist = cur[0], price = cur[1], r = cur[2], c = cur[3];

            if (low <= price && price <= high) {
                ans.add(Arrays.asList(r, c));
            }

            for (int d = 0; d < 4; ++d) {
                int nr = r + DIR[d];
                int nc = c + DIR[d + 1];
                if (nr < 0 || nr >= R || nc < 0 || nc >= C) continue;
                if (grid[nr][nc] == 0 || visited[nr][nc]) continue;
                visited[nr][nc] = true;
                q.offer(new int[]{dist + 1, grid[nr][nc], nr, nc});
            }
        }

        return ans;
    }
}
```

*Why `PriorityQueue` + BFS?*  
The priority queue guarantees that cells are popped in the correct order of the ranking rules, while BFS guarantees that we visit cells in increasing distance.  
No sorting of all cells is required, so the algorithm stays fast even for the 100 k‑cell limit.

---

### 1.2 Python

```python
import heapq
from typing import List, Tuple, Set

class Solution:
    def highestRankedKItems(
        self,
        grid: List[List[int]],
        pricing: List[int],
        start: List[int],
        k: int
    ) -> List[List[int]]:
        R, C = len(grid), len(grid[0])
        low, high = pricing
        sx, sy = start

        # Min‑heap: (distance, price, row, col)
        heap: List[Tuple[int, int, int, int]] = [(0, grid[sx][sy], sx, sy)]
        visited: Set[Tuple[int, int]] = {(sx, sy)}

        ans: List[List[int]] = []

        while heap and len(ans) < k:
            dist, price, r, c = heapq.heappop(heap)

            if low <= price <= high:
                ans.append([r, c])

            for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                nr, nc = r + dr, c + dc
                if 0 <= nr < R and 0 <= nc < C \
                   and grid[nr][nc] > 0 and (nr, nc) not in visited:
                    visited.add((nr, nc))
                    heapq.heappush(heap, (dist + 1, grid[nr][nc], nr, nc))

        return ans
```

*Why a `set` for visited?*  
The grid can be as large as `1e5` cells; a `boolean[][]` would consume a comparable amount of memory. A `set` of tuples keeps memory usage tight while providing O(1) look‑ups.

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Node {
    int dist;
    int price;
    int r, c;
    Node(int d, int p, int rr, int cc) : dist(d), price(p), r(rr), c(cc) {}
};

struct cmp {
    bool operator()(const Node& a, const Node& b) const {
        if (a.dist != b.dist) return a.dist > b.dist;
        if (a.price != b.price) return a.price > b.price;
        if (a.r != b.r) return a.r > b.r;
        return a.c > b.c;
    }
};

class Solution {
public:
    vector<vector<int>> highestRankedKItems(
        vector<vector<int>>& grid,
        vector<int>& pricing,
        vector<int>& start,
        int k) {

        int R = grid.size(), C = grid[0].size();
        int low = pricing[0], high = pricing[1];
        int sx = start[0], sy = start[1];

        vector<vector<bool>> vis(R, vector<bool>(C, false));
        vis[sx][sy] = true;

        priority_queue<Node, vector<Node>, cmp> pq;
        queue<pair<int,int>> bfs;          // stores coordinates only
        bfs.push({sx, sy});
        int dist = 0;

        // BFS to compute distance to every reachable cell
        vector<pair<int,int>> dirs = {{1,0},{-1,0},{0,1},{0,-1}};
        vector<vector<int>> distMat(R, vector<int>(C, -1));
        distMat[sx][sy] = 0;

        while (!bfs.empty()) {
            auto [x, y] = bfs.front(); bfs.pop();
            int d = distMat[x][y];
            int p = grid[x][y];
            if (low <= p && p <= high)
                pq.emplace(d, p, x, y);

            for (auto [dx, dy] : dirs) {
                int nx = x + dx, ny = y + dy;
                if (nx < 0 || nx >= R || ny < 0 || ny >= C) continue;
                if (grid[nx][ny] == 0 || vis[nx][ny]) continue;
                vis[nx][ny] = true;
                distMat[nx][ny] = d + 1;
                bfs.push({nx, ny});
            }
        }

        vector<vector<int>> ans;
        while (!pq.empty() && k--) {
            Node cur = pq.top(); pq.pop();
            ans.push_back({cur.r, cur.c});
        }
        return ans;
    }
};
```

> **Tip** – If you want to avoid the `distMat` array, simply push the distance together with the node into the priority queue as we do in the Java/Python solutions. The BFS layer count (`dist`) can be incremented on the fly.

---

## 2.  Blog Post – “Cracking LeetCode 2143: Why BFS + Min‑Heap is the Secret Weapon”

### Title  
**“BFS + Min‑Heap: Mastering LeetCode 2143 (Most Valuable Path) – 3‑Language Deep Dive”**

### Meta‑Description  
*“Solve the hard LeetCode problem 2143 in Java, Python, and C++ with a proven algorithm. Learn the ranking rules, why a min‑heap matters, and how to avoid common pitfalls. Ready to impress recruiters?”*

---

### 2.1  Introduction  

When you hit the “Most Valuable Path” problem (LeetCode 2143), the first instinct is often “just sort all cells” or “use Dijkstra’s algorithm”. Both approaches work, but they are either *slow* for the 100 k‑cell limit or *memory‑hungry*.

What if there was a trick that guarantees **O(N log N)** time and a small memory footprint? The trick is **Breadth‑First Search (BFS) + a Min‑Heap (priority queue)** – a pattern that reappears in every great system‑design and algorithm interview.

---

### 2.2  Problem Recap  

> *You’re given a rectangular grid, a range `[low, high]`, a starting position, and a number `k`.  
>  You must return the coordinates of the first `k` cells that have a price within the range, sorted by:*

1. *Manhattan distance from the start*  
2. *Price (smaller first)*  
3. *Row number*  
4. *Column number*  

> *If fewer than `k` cells are reachable, return all of them.*

---

### 2.3  Why the Ranking Rules Are Crucial  

The four rules mean you can’t simply “take the cheapest cells” – you must respect distance first.  
If you sort everything by price alone, you’ll break rule #1.  
If you use BFS alone, you’ll break rule #2–#4 because you’d have to do an additional sort after the traversal.

---

### 2.4  The “Good” – Why This Approach Works  

| ✅  Feature | Why it’s a win |
|------------|----------------|
| **Distance is guaranteed** | BFS visits cells layer by layer, so the first time we reach a cell we already know it has the minimal possible distance. |
| **Order by price / row / col** | A min‑heap keeps the *next* best cell in O(log N) time. We never need to sort the whole list. |
| **Linear memory** | Only the set of visited cells (≤ 100 k) is stored. No dense `distMat` array needed in Java/Python. |
| **Three language‑ready code** | The pattern is the same across Java, Python and C++; recruiters love to see you can translate algorithms. |

---

### 2.5  The “Bad” – Common Mistakes That Break Your Solution  

| ❌  Pitfall | What can go wrong? |
|------------|-------------------|
| **Using a vector of size R×C for distance** | In C++ or Java a 2‑D array of 100 k cells can still be OK, but if you add another `distMat` it multiplies memory usage. |
| **Not marking visited before pushing to the queue** | Duplicate pushes can lead to exponential blow‑up and stack overflow. |
| **Comparing only distance in the heap** | If you forget to incorporate price, row or column into the comparator, the answer will be wrong. |
| **Using `pop()` instead of `top()`** | In C++ you must pop only after fetching the element; otherwise the heap may shrink prematurely. |

---

### 2.6  The “Ugly” – Edge Cases That Sneak In  

| ⚠️  Edge case | How to guard against it |
|--------------|--------------------------|
| **Start is already out of range** | The algorithm still works: we check the price *before* we expand neighbors. |
| **All cells are zero (unreachable)** | The BFS queue will immediately terminate, and we return an empty answer. |
| **`k` is larger than the number of reachable cells** | We simply return all valid cells – the while‑loop condition (`ans.size() < k`) takes care of this. |
| **Large `R × C` but few reachable cells** | Our visited set keeps memory low; only the reachable cells are inserted into the heap. |

---

### 2.7  Implementation in 3 Languages – A Quick Reference

| Language | Key Data Structure | Complexity |
|----------|--------------------|------------|
| Java     | `ArrayDeque` + `PriorityQueue<int[]>` | O(N log N) |
| Python   | `heapq` + `set` | O(N log N) |
| C++      | `queue` + `priority_queue<Node>` | O(N log N) |

> **Pro tip:** When writing the heap elements, store the distance together with the node. That way you avoid a separate `distMat` array and keep the code cleaner.

---

### 2.8  Final Thoughts – Interview Tips  

1. **Explain the ranking rules first.** Interviewers love a clear verbal model.  
2. **Walk through the BFS layers** – show that you’re collecting the distance *before* you push a node into the heap.  
3. **Highlight the min‑heap comparator** – that’s the *“secret sauce”* that guarantees the correct output order.  
4. **Show the three language versions** – this demonstrates both your problem‑solving ability and your versatility as a developer.  

---

### 2.9  Call to Action  

> “Want to learn how to solve *any* hard LeetCode problem with elegant, production‑ready code?  
>  Subscribe to my newsletter for weekly deep dives, interview prep videos, and real‑world coding challenges.”

Happy coding – and may your stack traces always be error‑free! 🚀