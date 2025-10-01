---
title: LeetCode 210. Course Schedule II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 210. Course Schedule II – From Theory to Practice  
**(Java, Python, C++ – Kahn’s Algorithm + DFS Alternatives)**  

> 🚀 **Why this problem matters for your next interview**  
> The Course Schedule II problem is a *classic* graph‑traversal interview question that surfaces on every major tech‑company interview (Google, Amazon, Meta, Microsoft, etc.). Mastering it demonstrates that you understand **directed acyclic graphs (DAGs), topological sorting, cycle detection, and BFS/DFS trade‑offs** – all skills that recruiters actively seek.  

---

## Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [When to Use Topological Sort](#when-to-use-topological-sort)  
3. [Good – Kahn’s Algorithm (BFS)](#good-kahns-algorithm-bfs)  
4. [Bad – DFS + Recursion (Cycle Detection)](#bad-dfs-recursion-cycle-detection)  
5. [Ugly – Edge‑Case Pitfalls & Optimizations](#ugly-edge-case-pitfalls-optimizations)  
6. [Code Implementations](#code-implementations)  
   - Java  
   - Python  
   - C++  
7. [Complexity Analysis](#complexity-analysis)  
8. [Interview Tips & FAQs](#interview-tips-faqs)  
9. [SEO‑Friendly Summary](#seo-friendly-summary)

---

## 1. Problem Recap

> **Course Schedule II** – **LeetCode 210**  
> **Goal**: Return *any* ordering of courses that satisfies all prerequisite pairs. If impossible (i.e., a cycle exists), return an empty array.

| Variable | Meaning |
|----------|---------|
| `numCourses` | Number of courses (0 … `numCourses-1`) |
| `prerequisites` | `[[ai, bi], …]` meaning *take* `bi` *before* `ai` |

---

## 2. When to Use Topological Sort

- **Directed Graph** – Each prerequisite pair is a directed edge `bi → ai`.  
- **No cycles** – Only DAGs have a valid topological order.  
- **Need an ordering** – Not just feasibility: we need an *actual sequence*.

Topological sorting gives the required ordering in linear time relative to the graph size.

---

## 3. Good – Kahn’s Algorithm (BFS)

Kahn’s algorithm is the *most readable* approach for interviewers.  
It uses **in‑degrees** and a **queue** to process nodes with zero prerequisites.

### Why it’s *good*

| Property | Explanation |
|----------|-------------|
| **Iterative** | No recursion stack – safer for large graphs (up to 2000 nodes). |
| **Clear Cycle Detection** | If processed nodes < `numCourses`, a cycle exists. |
| **Deterministic** | Order of processing may differ, but any valid order is acceptable. |
| **Time‑Efficient** | O(V + E) – optimal for this problem. |

### Intuition

1. Build adjacency list and compute `inDegree` for each node.  
2. Enqueue all nodes with `inDegree == 0`.  
3. Repeatedly dequeue, add to result, and decrement `inDegree` of neighbors.  
4. If a neighbor’s `inDegree` drops to zero, enqueue it.  
5. After processing, if result size ≠ `numCourses`, return `[]`.

---

## 4. Bad – DFS + Recursion (Cycle Detection)

DFS can also perform topological sorting, but it tends to be **harder to explain** in an interview and may trigger stack overflow for deep graphs.

### DFS approach

- Use a state array: `0 = unvisited`, `1 = visiting`, `2 = visited`.  
- On recursion, if you hit a node already in the *visiting* state → cycle.  
- After visiting all neighbors, mark `visited` and push node onto stack.  
- Reverse the stack to get the order.

### Why it’s *bad* (for interviews)

- Requires **three‑state tracking** – easy to mis‑implement.  
- Recursion depth may exceed call stack limits.  
- Less intuitive for interviewers to see cycle detection at a glance.

> **TL;DR**: Use Kahn’s algorithm unless the interview explicitly asks for DFS.

---

## 5. Ugly – Edge‑Case Pitfalls & Optimizations

| Pitfall | How to avoid |
|---------|--------------|
| **Duplicate edges** | Problem guarantees distinct pairs, but if not, use `Set` to dedupe. |
| **Self‑loops** | Problem guarantees `ai != bi`. Still, validate input in production code. |
| **Large adjacency lists** | Use `ArrayList<Integer>[]` in Java, `vector<vector<int>>` in C++, `defaultdict(list)` in Python. |
| **Memory over‑allocation** | Pre‑allocate adjacency list size `numCourses`. |
| **Queue vs Deque** | In Python use `collections.deque` for O(1) pop/append. |

---

## 6. Code Implementations

Below you’ll find clean, production‑ready code for **Java, Python, and C++**.  
All three use **Kahn’s algorithm** and are heavily commented.

> 👉 **Tip:** Test with the sample cases and edge cases (no prerequisites, all courses linked, single node).

---

### Java

```java
import java.util.*;

/**
 * LeetCode 210 – Course Schedule II
 * Uses Kahn's algorithm (BFS) for topological sorting.
 */
public class Solution {
    public int[] findOrder(int numCourses, int[][] prerequisites) {
        // Adjacency list: graph[prereq] -> list of courses that depend on prereq
        List<List<Integer>> graph = new ArrayList<>(numCourses);
        for (int i = 0; i < numCourses; i++) graph.add(new ArrayList<>());

        // inDegree[i] = number of prerequisites of course i
        int[] inDegree = new int[numCourses];

        // Build graph
        for (int[] edge : prerequisites) {
            int next = edge[0];
            int prev = edge[1];
            graph.get(prev).add(next);
            inDegree[next]++;
        }

        // Queue for courses with no remaining prerequisites
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) queue.offer(i);
        }

        int[] order = new int[numCourses];
        int idx = 0;

        // Process nodes
        while (!queue.isEmpty()) {
            int curr = queue.poll();
            order[idx++] = curr;

            for (int neighbor : graph.get(curr)) {
                if (--inDegree[neighbor] == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        // If we couldn't process all courses, a cycle exists
        return idx == numCourses ? order : new int[0];
    }
}
```

---

### Python

```python
from collections import deque
from typing import List

class Solution:
    def findOrder(self, numCourses: int, prerequisites: List[List[int]]) -> List[int]:
        # Build adjacency list and indegree array
        graph = [[] for _ in range(numCourses)]
        indeg = [0] * numCourses

        for dest, src in prerequisites:
            graph[src].append(dest)
            indeg[dest] += 1

        # Queue of nodes with zero indegree
        q = deque([i for i in range(numCourses) if indeg[i] == 0])

        order = []
        while q:
            node = q.popleft()
            order.append(node)
            for nxt in graph[node]:
                indeg[nxt] -= 1
                if indeg[nxt] == 0:
                    q.append(nxt)

        # If not all courses are in order, a cycle was detected
        return order if len(order) == numCourses else []
```

---

### C++

```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        // Build graph & indegree
        vector<vector<int>> graph(numCourses);
        vector<int> indeg(numCourses, 0);
        for (const auto& edge : prerequisites) {
            int next = edge[0];
            int prev = edge[1];
            graph[prev].push_back(next);
            ++indeg[next];
        }

        // Queue for nodes with zero indegree
        queue<int> q;
        for (int i = 0; i < numCourses; ++i)
            if (indeg[i] == 0) q.push(i);

        vector<int> order;
        while (!q.empty()) {
            int cur = q.front(); q.pop();
            order.push_back(cur);
            for (int nxt : graph[cur]) {
                if (--indeg[nxt] == 0)
                    q.push(nxt);
            }
        }

        // Detect cycle
        return order.size() == numCourses ? order : vector<int>();
    }
};
```

---

## 7. Complexity Analysis

| Metric | Formula | Explanation |
|--------|---------|-------------|
| **Time** | `O(V + E)` | One pass to build the graph + one pass to process all nodes. |
| **Space** | `O(V + E)` | Adjacency list + indegree array + queue. |

With `numCourses ≤ 2000` and up to `numCourses*(numCourses-1)` edges, this is well within limits.

---

## 8. Interview Tips & FAQs

| Question | Answer |
|----------|--------|
| **Why Kahn’s algorithm?** | It's iterative, easy to understand, and provides explicit cycle detection via queue emptiness. |
| **Can we return a different order?** | Yes, any topological ordering works. The order produced by Kahn’s algorithm depends on the queue’s iteration order. |
| **What if I use DFS?** | DFS works but requires careful state management (`visiting/visited`) and may cause recursion depth issues for large graphs. |
| **How to handle multiple prerequisites for a single course?** | The indegree counts each prerequisite. A node is only processed when all its incoming edges are removed. |
| **Does the order matter for test cases?** | No. LeetCode accepts any valid order. In interviews, you can ask if the interviewer prefers a particular order. |

---

### 9. SEO‑Friendly Summary

**Course Schedule II – Topological Sorting**  
- *Problem*: Find any course order given prerequisites.  
- *Key Insight*: Build a DAG and perform a topological sort.  
- *Optimal Solution*: Kahn’s algorithm (BFS) – O(V + E) time, O(V + E) space.  
- *Alternatives*: DFS with three‑state visitation (less recommended).  
- *Edge Cases*: Empty prerequisites, full cycle, single course.  
- *Coding Language Examples*: Java, Python, C++ (all use Kahn’s algorithm).  
- *Interview Takeaway*: Focus on clarity, cycle detection, and explain the queue logic.

> **Final Thought**: Mastering Kahn’s algorithm for Course Schedule II demonstrates your ability to apply *graph theory* efficiently—a must‑have skill for *software engineering interviews* and a valuable asset for your next tech role.  

---

## 9. SEO‑Friendly Summary

- **Course Schedule II** (LeetCode 210)  
- **Topological Sort**: Kahn’s algorithm (BFS) – time O(V+E), space O(V+E).  
- **Alternatives**: DFS – valid but interview‑challenging.  
- **Java / Python / C++ code** – ready to run.  
- **Cycle detection**: If processed < `numCourses`, return empty list.  
- **Use in interviews**: Show iterative logic, queue, indegree, and cycle check.

> 📌 **Keywords**: Course Schedule II, LeetCode 210, topological sort, Kahn's algorithm, graph cycle detection, BFS, DFS, time complexity, space complexity, interview coding.

--- 

**Good luck on your next interview!**  
Feel free to bookmark this page, share with your peers, and ask any follow‑up questions.