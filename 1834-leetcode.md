---
title: LeetCode 1834. Single-Threaded CPU - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1834 – “Single‑Threaded CPU”  
**Java | Python | C++** – Full, production‑ready solutions + a *complete blog post* that talks about the **good, the bad, and the ugly**.  
Use this to ace your next coding interview, boost your résumé and impress recruiters.

---

### 📖 Problem Recap

You’re given an array of tasks:  

```
tasks[i] = [enqueueTimei, processingTimei]
```

* `enqueueTimei` – when the task becomes available.  
* `processingTimei` – how long it takes to finish.

The CPU is single‑threaded:  
1. If it’s idle and no tasks are ready → stay idle.  
2. If it’s idle and there are ready tasks → pick the one with the **shortest processing time**.  
   - Tie → pick the smallest **index**.  
3. Once a task starts it runs to completion.

Return the order of indices the CPU will process.

---

### 🎯 Optimal Solution Outline

1. **Sort** tasks by `enqueueTime`.  
2. Use a **min‑heap** (`PriorityQueue` in Java, `priority_queue` in C++, `heapq` in Python) keyed by `(processingTime, index)`.  
3. Walk through the sorted list while:
   * Add all tasks whose `enqueueTime <= currentTime` to the heap.  
   * If heap empty → jump `currentTime` to next task’s `enqueueTime`.  
   * Pop from heap → process task → update `currentTime += processingTime`.  
4. Store indices in answer array.

The algorithm is `O(n log n)` due to sorting + heap ops, with `O(n)` memory.

---

## ✅ Code Implementations

### Java

```java
import java.util.*;

public class Solution {
    private static class Task {
        int idx, enqueue, process;
        Task(int idx, int enqueue, int process) {
            this.idx = idx;
            this.enqueue = enqueue;
            this.process = process;
        }
    }

    public int[] getOrder(int[][] tasks) {
        int n = tasks.length;
        Task[] arr = new Task[n];
        for (int i = 0; i < n; ++i)
            arr[i] = new Task(i, tasks[i][0], tasks[i][1]);

        // 1️⃣ Sort by enqueue time
        Arrays.sort(arr, Comparator.comparingInt(t -> t.enqueue));

        // 2️⃣ Min‑heap: first by process, then by idx
        PriorityQueue<Task> pq = new PriorityQueue<>(
                Comparator.<Task>comparingInt(t -> t.process)
                          .thenComparingInt(t -> t.idx));

        int[] ans = new int[n];
        int ptr = 0;          // pointer in sorted arr
        long curTime = 0;     // current time, use long to avoid overflow
        int ansIdx = 0;

        while (ptr < n || !pq.isEmpty()) {
            // Push all tasks that have arrived
            while (ptr < n && arr[ptr].enqueue <= curTime) {
                pq.offer(arr[ptr++]);
            }

            if (!pq.isEmpty()) {
                Task cur = pq.poll();
                ans[ansIdx++] = cur.idx;
                curTime += cur.process;
            } else {
                // No task ready → jump to next arrival
                curTime = arr[ptr].enqueue;
            }
        }
        return ans;
    }
}
```

### Python

```python
import heapq
from typing import List

class Solution:
    def getOrder(self, tasks: List[List[int]]) -> List[int]:
        # Attach index
        tasks = [(e, p, i) for i, (e, p) in enumerate(tasks)]
        tasks.sort()                     # sort by enqueue time

        res = []
        pq = []                          # heap of (process, index)
        time = 0
        i = 0
        n = len(tasks)

        while i < n or pq:
            # Add all ready tasks
            while i < n and tasks[i][0] <= time:
                e, p, idx = tasks[i]
                heapq.heappush(pq, (p, idx))
                i += 1

            if pq:
                p, idx = heapq.heappop(pq)
                time += p
                res.append(idx)
            else:
                # Idle – jump to next enqueue time
                time = tasks[i][0]

        return res
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> getOrder(vector<vector<int>>& tasks) {
        struct Task { long long e, p; int idx; };
        vector<Task> v;
        for (int i = 0; i < tasks.size(); ++i)
            v.push_back({tasks[i][0], tasks[i][1], i});

        sort(v.begin(), v.end(),
             [](const Task& a, const Task& b){ return a.e < b.e; });

        priority_queue<pair<long long,int>,
                       vector<pair<long long,int>>,
                       greater<pair<long long,int>>> pq; // (process, idx)

        vector<int> ans;
        long long cur = 0;
        int i = 0, n = v.size();

        while (i < n || !pq.empty()) {
            while (i < n && v[i].e <= cur) {
                pq.emplace(v[i].p, v[i].idx);
                ++i;
            }

            if (!pq.empty()) {
                auto [proc, idx] = pq.top(); pq.pop();
                ans.push_back(idx);
                cur += proc;
            } else {
                cur = v[i].e;
            }
        }
        return ans;
    }
};
```

---

## 📝 Blog Article – “The Good, The Bad, and the Ugly of LeetCode 1834”

> **SEO Keywords**: *Single‑Threaded CPU LeetCode 1834*, *Java solution*, *Python solution*, *C++ solution*, *fastest task scheduler*, *interview coding interview*, *job interview preparation*, *algorithm explanation*, *min‑heap*, *priority queue*.

---

### 1️⃣ The Good

| ✅ Feature | Why it’s great |
|------------|----------------|
| **Linear scan + heap** | Handles up to 10⁵ tasks in `O(n log n)`. |
| **Deterministic tie‑breaking** | Sorting by index guarantees the “smallest index” rule. |
| **Jump‑ahead idle logic** | Skips useless idle time, making runtime optimal. |
| **Readable code** | Java, Python, and C++ versions are concise yet clear. |

- **Time complexity**: `O(n log n)`  
- **Space complexity**: `O(n)` (for heap + sorted list)

---

### 2️⃣ The Bad

| ⚠️ Issue | Fix / Mitigation |
|----------|------------------|
| **Overflow risk** | Use `long long`/`long` for current time because enqueue/processing times can be up to 10⁹. |
| **Sorting stability** | Ensure tasks are sorted strictly by enqueue time; index tie doesn’t matter after heap sorts by processing time. |
| **Edge cases** | - All tasks arrive at same time. <br>- CPU idle forever at the end? (never happens because tasks are finite). |

---

### 3️⃣ The Ugly

| 😈 Pitfall | What can go wrong? |
|------------|-------------------|
| **Mis‑ordered heap comparator** | Mixing `processingTime` and `index` in the wrong order gives wrong results. |
| **O(n²) naive loop** | Some solutions push all tasks into a heap and then pop them all, even when CPU would idle—this still works but is sub‑optimal. |
| **Using `queue` instead of `priority_queue`** | A FIFO queue ignores the “shortest processing time” rule. |
| **Not advancing time when idle** | Leads to infinite loop because `currentTime` never increases. |

---

### 4️⃣ Interview‑Ready Tips

1. **Explain the intuition first** – talk about “sort by arrival, use a min‑heap for ready tasks”.  
2. **Discuss time/space** – recruiters love seeing the `O(n log n)` argument.  
3. **Mention edge cases** – show that you thought about all tasks arriving at once or CPU idling.  
4. **Show up‑to‑date language features** – e.g., Java’s `Comparator.comparingInt`, Python’s `heapq`.  
5. **Provide test cases** – the two given examples + a few custom ones (e.g., all same enqueue, single task).  

---

### 5️⃣ Final Thoughts

LeetCode 1834 is a classic *priority‑queue* problem.  
The trick is not to over‑think the data structure; once you see “always pick the shortest job” you’re essentially doing *Shortest‑Job‑First (SJF)* scheduling.  
With the code snippets above, you’re ready to drop the correct solution in your next coding interview and walk away with a confidence boost.

---

### 🔗 Quick Links

- [LeetCode 1834 – Single‑Threaded CPU](https://leetcode.com/problems/single-threaded-cpu/)
- [Java Solution (GitHub Gist)](https://gist.github.com/yourusername/1234567)
- [Python Solution (Pastebin)](https://pastebin.com/yourcode)
- [C++ Solution (GitHub)](https://github.com/yourusername/leetcode-1834)

Good luck, and may the CPU be ever in your favor! 🚀