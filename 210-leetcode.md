---
title: LeetCode 210. Course Schedule II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏆 210. Course Schedule II – The Ultimate Topological‑Sort Show‑down  
**Languages:** Java | Python | C++  
**Interview‑ready** – walk through the good, the bad, and the ugly.  
**SEO tags:** *Course Schedule II, LeetCode, topological sort, Kahn's algorithm, DFS, BFS, job interview, Java, Python, C++*  

---

### 1️⃣ Problem Recap  

> **Given**  
> - `numCourses` (0 … `numCourses-1`)  
> - `prerequisites` – an array of pairs `[ai, bi]` meaning *you must finish `bi` before taking `ai`*  
>  
> **Return** a valid order to finish all courses, or an empty array if impossible.  
>   
> **Constraints**  
> - `1 ≤ numCourses ≤ 2000`  
> - `0 ≤ prerequisites.length ≤ numCourses*(numCourses-1)`  
> - No duplicate pairs, `ai ≠ bi`.  

---

### 2️⃣ Why It’s a Classic Topological‑Sort Problem  

Each course is a **vertex**; each prerequisite is a **directed edge** (`bi → ai`).  
We must find a linear ordering of vertices that respects all directed edges.  
If a cycle exists, no ordering exists → answer is `[]`.  

Two standard ways:  
1. **Kahn’s algorithm (BFS)** – maintain indegree counts.  
2. **DFS** – post‑order traversal with cycle detection.  

We’ll focus on Kahn’s algorithm because it is linear, easy to reason about, and the “breadth‑first” flavour often looks cleaner in interview settings.

---

### 3️⃣ The “Good” – Why Kahn’s Algorithm Rocks  

| Aspect | Why it’s great |
|--------|----------------|
| **Time** | `O(V + E)` – one pass over vertices and edges. |
| **Space** | `O(V + E)` – adjacency list + indegree array. |
| **Determinism** | Produces *a* topological order, not all permutations – exactly what LeetCode expects. |
| **Cycle Detection** | If queue empties before all nodes are processed → cycle. |
| **Readability** | Two small loops: build graph, then BFS. |

---

### 4️⃣ The “Bad” – Where You Can Trip Over  

| Issue | How to avoid |
|-------|--------------|
| **Duplicate edges** | Problem guarantees none, but in real code you might `HashSet` per adjacency list. |
| **Large `numCourses` with sparse edges** | Adjacency list still good; avoid O(V^2) adjacency matrix. |
| **Stack overflow with DFS** | Recursion depth could hit 2000 – safe in Java/Python, but still watch out. |
| **Queue choice** | `LinkedList` is fine, but `ArrayDeque` is slightly faster. |

---

### 5️⃣ The “Ugly” – Common Gotchas & Hidden Pitfalls  

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| **Mis‑interpreting edge direction** | Returning `[1,0]` for prerequisites `[0,1]` fails test. | Remember: edge is *prerequisite → course* (`b → a`). |
| **Off‑by‑one in indices** | `numCourses` not included in loops. | Loop `0 .. numCourses-1`. |
| **Returning wrong type** | In Java, returning `new int[0]` is fine, but returning `null` causes `NullPointerException`. | Always return array. |
| **Large input, memory leak** | Building list of lists for 2000 vertices is trivial, but in languages with manual memory you’d free unused edges. | Not needed here, but keep in mind for >10^5 nodes. |

---

## 6️⃣ Code – Ready to Copy & Paste  

### 6.1 Java 17  

```java
import java.util.*;

class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        // 1️⃣ Build graph + indegree array
        List<List<Integer>> graph = new ArrayList<>(numCourses);
        for (int i = 0; i < numCourses; i++) graph.add(new ArrayList<>());

        int[] indegree = new int[numCourses];
        for (int[] edge : prerequisites) {
            int course = edge[0];
            int prereq = edge[1];
            graph.get(prereq).add(course);   // prereq → course
            indegree[course]++;
        }

        // 2️⃣ Collect all zero‑indegree vertices
        Deque<Integer> queue = new ArrayDeque<>();
        for (int i = 0; i < numCourses; i++)
            if (indegree[i] == 0) queue.add(i);

        // 3️⃣ Process queue
        int[] order = new int[numCourses];
        int idx = 0;
        while (!queue.isEmpty()) {
            int cur = queue.poll();
            order[idx++] = cur;
            for (int nxt : graph.get(cur)) {
                if (--indegree[nxt] == 0) queue.add(nxt);
            }
        }

        // 4️⃣ Cycle detection
        return idx == numCourses ? order : new int[0];
    }
}
```

> **Tip:** Use `ArrayDeque` instead of `LinkedList` for faster enqueue/dequeue.

---

### 6.2 Python 3 (3.10+)  

```python
from collections import deque
from typing import List

class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        # Build adjacency list & indegree
        graph = [[] for _ in range(numCourses)]
        indegree = [0] * numCourses
        for a, b in prerequisites:          # b → a
            graph[b].append(a)
            indegree[a] += 1

        # Initialize queue with all zero‑indegree nodes
        q = deque([i for i in range(numCourses) if indegree[i] == 0])
        order = []

        while q:
            node = q.popleft()
            order.append(node)
            for nxt in graph[node]:
                indegree[nxt] -= 1
                if indegree[nxt] == 0:
                    q.append(nxt)

        return order if len(order) == numCourses else []
```

> **Why `deque`?** – O(1) pop from left, perfect for BFS.

---

### 6.3 C++17 (GNU ++17)  

```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        // 1️⃣ Build graph + indegree
        vector<vector<int>> graph(numCourses);
        vector<int> indegree(numCourses, 0);
        for (const auto &e : prerequisites) {          // e = {a, b}
            int a = e[0], b = e[1];                   // b → a
            graph[b].push_back(a);
            ++indegree[a];
        }

        // 2️⃣ Queue of zero indegree vertices
        queue<int> q;
        for (int i = 0; i < numCourses; ++i)
            if (indegree[i] == 0) q.push(i);

        // 3️⃣ Process
        vector<int> order;
        while (!q.empty()) {
            int cur = q.front(); q.pop();
            order.push_back(cur);
            for (int nxt : graph[cur]) {
                if (--indegree[nxt] == 0) q.push(nxt);
            }
        }

        // 4️⃣ Check cycle
        return (int)order.size() == numCourses ? order : vector<int>();
    }
};
```

> **Why `queue`?** – Standard FIFO, good for BFS.

---

## 7️⃣ Quick Test Harness (Optional)

```python
if __name__ == "__main__":
    s = Solution()
    print(s.findOrder(2, [[1,0]]))          # [0,1]
    print(s.findOrder(4, [[1,0],[2,0],[3,1],[3,2]]))  # e.g., [0,1,2,3]
    print(s.findOrder(1, []))               # [0]
    print(s.findOrder(2, [[0,1],[1,0]]))    # []  (cycle)
```

---

## 8️⃣ What Interviewers Like to Hear  

1. **Clarify the model** – explain graph, vertices, edges.  
2. **State the algorithm** – “I’ll use Kahn’s algorithm because…”.  
3. **Analyze** – time `O(V+E)`, space `O(V+E)`.  
4. **Show cycle detection** – why an empty array indicates impossibility.  
5. **Mention alternatives** – DFS + post‑order (also linear).  
6. **Talk about edge cases** – no prerequisites, single course, full cycle.

---

## 9️⃣ SEO‑Friendly Takeaway  

- **Keywords**: *Course Schedule II*, *LeetCode 210*, *Topological Sort*, *Kahn’s Algorithm*, *Graph Traversal*, *DFS*, *BFS*, *Job Interview*, *Java*, *Python*, *C++*, *Coding Interview Problem*.  
- **Meta‑Description**: “Solve LeetCode Course Schedule II (210) in Java, Python, and C++ using Kahn’s algorithm. Learn topological sort, cycle detection, and interview‑ready explanations.”  
- **Headers**: `# Course Schedule II – Topological Sort`, `## Good`, `## Bad`, `## Ugly`, `## Code Examples`.  

---

### 🚀 Final Checklist Before You Hit “Submit”

- [ ] Graph built correctly (`b → a`).  
- [ ] Indegree counted right.  
- [ ] Queue initialized with all zero indegree nodes.  
- [ ] Cycle detection (`order.size() == numCourses`).  
- [ ] Clean, commented code.  
- [ ] Run through all LeetCode examples.  
- [ ] Think of a real‑world analogy (e.g., building a city from foundation to roof).  

Good luck—you’ve got the algorithm, the code, and the interview talking points! 🌟