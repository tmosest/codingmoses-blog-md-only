---
title: LeetCode 2050. Parallel Courses III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Parallel Courses III – LeetCode 2050  
### From Concept to Code – Java, Python & C++

Below you’ll find a **ready‑to‑copy** solution in three of the most popular interview languages, a deep‑dive blog article that explains the algorithm, highlights the “good, the bad, the ugly”, and is fully SEO‑optimised for anyone looking to ace this LeetCode problem or a graph‑theory interview.

---

## Table of Contents

| Section | Link |
|---------|------|
| 1️⃣ Problem Overview | #problem-overview |
| 2️⃣ Solution Idea | #solution-idea |
| 3️⃣ Code | #code |
| 4️⃣ Complexity | #complexity |
| 5️⃣ Edge Cases & Pitfalls | #edge-cases |
| 6️⃣ Blog Article | #blog-article |
| 7️⃣ Takeaway | #takeaway |

---

## 1️⃣ Problem Overview  
**Parallel Courses III (LeetCode 2050)**  
*Hard – Directed Acyclic Graph (DAG) + Dynamic Programming*  

> *You’re given `n` courses, a list of prerequisite pairs `relations`, and an array `time` where `time[i]` is the duration (in months) of course `i+1`.  
> Find the minimum months required to finish all courses if you may take any number of courses in parallel as long as their prerequisites are satisfied.*

### Constraints
- `1 ≤ n ≤ 5·10⁴`  
- `0 ≤ relations.length ≤ 5·10⁴`  
- `1 ≤ time[i] ≤ 10⁴`  
- The graph is a DAG – every course can eventually be finished.

---

## 2️⃣ Solution Idea  
The problem is essentially a **“earliest finish time”** computation on a DAG.

1. **Topological order** – Process courses only after all their prerequisites are finished.  
2. **Dynamic programming on the DAG** –  
   * `start[i]`  – earliest month you can start course *i*.  
   * `finish[i]` – earliest month you can finish course *i* (`start[i] + time[i]`).  

During a Kahn‑style BFS:
- For each course `u` popped from the queue, propagate its finish time to every neighbor `v`.  
- Update `start[v] = max(start[v], finish[u])`.  
- Decrease indegree of `v`; when it hits zero, compute `finish[v] = start[v] + time[v]` and enqueue `v`.

The answer is the maximum `finish[i]` over all courses.

---

## 3️⃣ Code

### Java 17

```java
import java.util.*;

public class Solution {
    public int minNumberOfMonths(int n, int[][] relations, int[] time) {
        // Build adjacency list and indegree array
        List<List<Integer>> adj = new ArrayList<>(n);
        for (int i = 0; i < n; i++) adj.add(new ArrayList<>());
        int[] indeg = new int[n];
        for (int[] r : relations) {
            int u = r[0] - 1, v = r[1] - 1;
            adj.get(u).add(v);
            indeg[v]++;
        }

        // DP arrays
        int[] start = new int[n];
        int[] finish = new int[n];
        Deque<Integer> dq = new ArrayDeque<>();

        // Initialise courses with no prerequisites
        for (int i = 0; i < n; i++) {
            if (indeg[i] == 0) {
                start[i] = 0;
                finish[i] = time[i];
                dq.offer(i);
            }
        }

        int answer = 0;
        while (!dq.isEmpty()) {
            int u = dq.poll();
            answer = Math.max(answer, finish[u]);

            for (int v : adj.get(u)) {
                start[v] = Math.max(start[v], finish[u]);   // earliest start for v
                indeg[v]--;
                if (indeg[v] == 0) {                       // all prerequisites done
                    finish[v] = start[v] + time[v];
                    dq.offer(v);
                }
            }
        }
        return answer;
    }
}
```

### Python 3.10

```python
from collections import deque
from typing import List

class Solution:
    def minNumberOfMonths(self, n: int, relations: List[List[int]],
                          time: List[int]) -> int:
        adj = [[] for _ in range(n)]
        indeg = [0] * n
        for a, b in relations:
            adj[a-1].append(b-1)
            indeg[b-1] += 1

        start = [0] * n
        finish = [0] * n
        q = deque([i for i, d in enumerate(indeg) if d == 0])

        for i in q:
            finish[i] = time[i]

        answer = 0
        while q:
            u = q.popleft()
            answer = max(answer, finish[u])
            for v in adj[u]:
                start[v] = max(start[v], finish[u])
                indeg[v] -= 1
                if indeg[v] == 0:
                    finish[v] = start[v] + time[v]
                    q.append(v)

        return answer
```

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minNumberOfMonths(int n, vector<vector<int>>& relations, vector<int>& time) {
        vector<vector<int>> adj(n);
        vector<int> indeg(n, 0);
        for (auto &p : relations) {
            int u = p[0] - 1, v = p[1] - 1;
            adj[u].push_back(v);
            ++indeg[v];
        }

        vector<int> start(n, 0), finish(n, 0);
        queue<int> q;
        for (int i = 0; i < n; ++i)
            if (indeg[i] == 0) {
                finish[i] = time[i];
                q.push(i);
            }

        int answer = 0;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            answer = max(answer, finish[u]);

            for (int v : adj[u]) {
                start[v] = max(start[v], finish[u]);   // earliest start
                if (--indeg[v] == 0) {                 // all prereqs finished
                    finish[v] = start[v] + time[v];
                    q.push(v);
                }
            }
        }
        return answer;
    }
};
```

All three snippets run in **O(n + m)** time and **O(n + m)** memory, easily fitting the problem constraints.

---

## 4️⃣ Complexity  

| Operation | Complexity |
|-----------|------------|
| Building graph | **O(n + m)** |
| Topological BFS & DP | **O(n + m)** |
| Total | **O(n + m)** |
| Memory | **O(n + m)** (adjacency list + DP arrays) |

`m = relations.length`, `n ≤ 5·10⁴`, so the solution is comfortably fast.

---

## 5️⃣ Edge Cases & Pitfalls  

| Scenario | What to Watch |
|----------|---------------|
| **No prerequisites** (`relations` empty) | Every course can start at month 0. Our initial queue handles this. |
| **Multiple independent chains** | The answer is the maximum finish time among all chains. |
| **Large `time` values** (`≤ 10⁴`) | Use `int` or `long` if you prefer safety; Java, Python, C++ int handles up to ~2·10⁹. |
| **Graph size close to limits** | Avoid recursion (DFS) to prevent stack overflow. Our BFS solution is iterative. |
| **Zero‑based vs one‑based indices** | Convert all course labels (`1…n`) to `0…n‑1` when building adjacency lists. |

---

## 6️⃣ Blog Article  
> **Title**: “Parallel Courses III – Mastering DAG DP for Your Next Software‑Engineer Interview”

---

### 📌 Introduction  
In software‑engineering interviews, problems that involve **graphs**, **topological ordering**, and **dynamic programming** are common. One of the most beloved challenges on LeetCode is **Parallel Courses III (Problem 2050)** – a *hard* problem that forces you to combine Kahn’s algorithm with DP on a DAG.

In this article, we walk through the problem statement, illustrate the algorithmic idea, compare “good” vs “bad” implementations, and deliver production‑ready solutions in **Java, Python, and C++**. We'll also provide SEO‑friendly keywords so that recruiters who search for “Parallel Courses III solution” or “DAG topological sort interview” find this post instantly.

---

### 🔍 Problem Overview (SEO‑keywords: Parallel Courses III, LeetCode 2050, DAG)

> **Goal**: Determine the minimum months required to finish `n` courses where each course takes a specified number of months and may have prerequisite courses.  
> **Constraints**:  
> * `1 ≤ n ≤ 5·10⁴`  
> * `relations` is a list of directed edges, size `≤ 5·10⁴`.  
> * Graph is a DAG – every course can be finished eventually.  

---

### 💡 Solution Idea (SEO‑keywords: topological sort, dynamic programming, Kahn’s algorithm)

The heart of the solution is **“earliest finish time on a DAG”**:

1. **Topological order** – ensures prerequisites are processed before dependent courses.  
2. **DP on DAG** – propagate finish times from prerequisites to dependents, tracking the earliest start month for each course.  
3. **Answer** – the maximum finish time across all courses.

*Why this is good*:  
- **Linear time** – no backtracking or exponential blow‑up.  
- **Iterative** – safe for huge inputs, no risk of recursion stack overflow.  
- **Memory‑efficient** – adjacency list + two integer arrays.

*Why naive DFS with recursion is bad*:  
- Recursion depth can hit 50 000 → stack overflow in Java or C++.  
- Harder to reason about overlapping sub‑problems – you’d have to memoise manually.

---

### 🧠 Good vs. Bad Implementations

| Implementation | Strengths | Weaknesses |
|----------------|-----------|------------|
| **Kahn + DP (our BFS)** | O(n + m), iterative, clear separation of “start” and “finish” times. | None, if indices are correctly converted. |
| **DFS + Memoisation** | Straightforward to code, uses recursion. | Risk of stack overflow; harder to justify complexity to interviewers. |
| **Brute‑Force DP** (nested loops over all pairs) | Conceptually simple, but O(n²). | Not acceptable for `n = 5·10⁴`. |

---

### 📦 Code in Three Languages  

*The three code snippets above are fully commented and ready for a white‑board interview or a coding test. You can paste them into your favourite IDE or the LeetCode editor.*

---

### ⏱ Complexity (SEO‑keywords: time complexity, space complexity, O(n+m))

- **Time**: `O(n + m)` – linear in the number of courses and prerequisite pairs.  
- **Space**: `O(n + m)` – adjacency list + DP arrays.

Because the problem limits `n` and `m` to 50 000, the solution comfortably fits within the 1 s/512 MB limits of LeetCode.

---

### 🚨 Edge Cases & “Ugly” Pitfalls (SEO‑keywords: edge cases, bug fixing, interview pitfalls)

| Edge case | Fix |
|-----------|-----|
| No prerequisites | Initial queue seeds all courses with `start = 0`. |
| 1‑based labels | Convert to 0‑based indices immediately. |
| Very large `time` | Keep data types large enough; in Java use `long` if you wish. |
| Recursion depth | Avoid DFS; use BFS (Kahn) to stay within stack limits. |

---

### 📄 Conclusion (SEO‑keywords: graph interview questions, DAG DP, LeetCode solutions, Java‑Python‑C++ interview)

Parallel Courses III is an *iconic* graph‑DP problem that showcases two essential interview skills:

1. **Topological sorting (Kahn)** – ensuring you respect prerequisites.  
2. **Dynamic programming on DAGs** – computing earliest start/finish times.

Mastering this problem proves you can handle **hard‑level DAGs** in a production setting, a skill highly prized by recruiters at Google, Amazon, Microsoft, and fintechs alike.

Add the snippet to your GitHub repo, practice on random DAGs, and you’ll feel confident in any “parallel courses” or “course scheduling” interview question.

---

### 📈 Takeaway

- **Iterative BFS + DP** → **O(n + m)**, safe for 50 k nodes.  
- Avoid recursion → no stack overflow.  
- Convert indices to 0‑based for clarity.  
- Highlight this solution on your résumé or portfolio as a “Graph DP Expert”.

Happy coding! 🎉

---

## 7️⃣ Takeaway (SEO‑keywords: interview preparation, graph problems, coding interview)

*Parallel Courses III is a textbook example of **DAG + DP** in action. By presenting iterative solutions in Java, Python, and C++, you demonstrate both language mastery and deep algorithmic insight—exactly what recruiters look for.*

Feel free to copy, tweak, and run these solutions on LeetCode or during mock interviews. And remember: **graph algorithms are the bread‑and‑butter of senior software‑engineering roles**. Good luck!