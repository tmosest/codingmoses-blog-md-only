---
title: LeetCode 3275. K-th Nearest Obstacle Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3275 – K‑th Nearest Obstacle Queries  
**LeetCode, Interview Prep, Heap, Priority Queue, Java / Python / C++**  

---

## TL;DR  

| Language | Complexity | Code |
|----------|------------|------|
| **Java** | `O(n log k)` time, `O(k)` space | <details open><summary>View Code</summary>…</details> |
| **Python** | `O(n log k)` time, `O(k)` space | <details open><summary>View Code</summary>…</details> |
| **C++** | `O(n log k)` time, `O(k)` space | <details open><summary>View Code</summary>…</details> |

> **Why it matters for your job hunt**  
> Mastering a classic *max‑heap* pattern is a must‑know for any mid‑level backend or systems role.  
> Demonstrating a clean solution in Java, Python, and C++ shows you can adapt quickly – a huge plus for interviewers.

---

## Problem Restatement  

You are given an infinite 2‑D plane and a positive integer `k`.  
`queries` is a list of `n` unique points `[x, y]`.  
After each insertion you must report the Manhattan distance of the **k‑th nearest obstacle** from the origin `(0,0)`.  
If there are fewer than `k` obstacles so far, return `-1`.

Manhattan distance = `|x| + |y|`.

> **Constraints**  
> * `1 ≤ n ≤ 2·10⁵`  
> * `-10⁹ ≤ x, y ≤ 10⁹`  
> * `1 ≤ k ≤ 10⁵`  

---

## Intuition  

The task is a classic “maintain the k‑smallest (or k‑largest) values” problem.

* **If we keep a **max‑heap** of size at most `k`**:  
  * The heap contains the current `k` smallest distances.  
  * The root (`top`) is the largest among them, i.e. the *k‑th nearest* distance.  
  * When a new distance comes in:  
    * Push it onto the heap.  
    * If the heap size exceeds `k`, pop the root – the farthest of the `k+1` distances.  
    * After this step, the heap again contains the `k` nearest distances.

* If the heap has fewer than `k` elements, we output `-1`.

This gives `O(log k)` per query, which is fast enough for `2·10⁵` queries.

---

## The Good  

| What | Why it’s great |
|------|----------------|
| **Simplicity** | A single `priority_queue`/`heapq` and a few lines of logic. |
| **Scalability** | `O(n log k)` is optimal for online queries. |
| **Memory‑wise** | Only `k` distances are stored, no need to keep all obstacles. |

---

## The Bad  

| Issue | Remedy |
|-------|--------|
| **Large `k` (≈ 10⁵)** | Memory footprint is about 400 KB (int) – fine, but watch for 64‑bit overhead in Python. |
| **Absolute value overflow** | `|x| + |y|` fits in 32‑bit signed int (`≤ 2·10⁹`), but using `long` guarantees safety in all languages. |
| **Input order** | Because queries are processed sequentially, you *must* handle “less than k” cases immediately. |

---

## The Ugly  

1. **TLE on very tight systems** – if you use a slow heap implementation (e.g. custom binary tree in Python), the log factor can dominate.  
2. **Mis‑using a min‑heap** – pushing distances into a min‑heap and popping when the size exceeds `k` will give you the *k‑th largest* distance, the wrong answer.  
3. **Neglecting negative coordinates** – forgetting the `abs()` when computing distance leads to wrong results.

---

## Implementation

Below are clean, ready‑to‑paste solutions for **Java**, **Python**, and **C++**.

> All three use the same core idea: a max‑heap of size `k`.

### Java

```java
import java.util.*;

class Solution {
    public int[] resultsArray(int[][] queries, int k) {
        int n = queries.length;
        // Max‑heap: largest distance on top
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        int[] res = new int[n];

        for (int i = 0; i < n; i++) {
            int dist = Math.abs(queries[i][0]) + Math.abs(queries[i][1]);

            pq.offer(dist);
            if (pq.size() > k) {
                pq.poll();               // remove farthest
            }

            res[i] = (pq.size() == k) ? pq.peek() : -1;
        }
        return res;
    }
}
```

### Python

```python
import heapq
from typing import List

class Solution:
    def resultsArray(self, queries: List[List[int]], k: int) -> List[int]:
        # Python heapq is a min‑heap, so store negative distances
        max_heap: List[int] = []
        res: List[int] = []

        for x, y in queries:
            dist = abs(x) + abs(y)
            heapq.heappush(max_heap, -dist)     # push negative
            if len(max_heap) > k:
                heapq.heappop(max_heap)         # pop farthest (most negative)

            res.append(-max_heap[0] if len(max_heap) == k else -1)

        return res
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> resultsArray(vector<vector<int>>& queries, int k) {
        priority_queue<int> maxHeap;   // default is max‑heap
        vector<int> res;
        for (auto &q : queries) {
            int dist = abs(q[0]) + abs(q[1]);
            maxHeap.push(dist);
            if (maxHeap.size() > k) maxHeap.pop();

            res.push_back(maxHeap.size() == k ? maxHeap.top() : -1);
        }
        return res;
    }
};
```

---

## Complexity Analysis  

| Step | Time | Space |
|------|------|-------|
| Push / pop per query | `O(log k)` | |
| Total for `n` queries | `O(n log k)` | `O(k)` |

With `n ≤ 2·10⁵` and `k ≤ 10⁵`, this comfortably fits within typical LeetCode limits.

---

## Testing & Edge Cases  

| Test | Expected | Reason |
|------|----------|--------|
| `k = 1`, queries = `[[0,0]]` | `[0]` | First obstacle is the nearest. |
| `k = 3`, queries = `[[1,1], [2,2], [3,3]]` | `[-1,-1,6]` | Only after 3 obstacles we have a valid answer. |
| `k = 2`, queries = `[[5,5], [-5,-5]]` | `[10,10]` | Distances are equal; max‑heap holds both. |
| Large coordinates `[-10⁹, 10⁹]` | Works without overflow. | `|x| + |y| ≤ 2·10⁹` < `INT_MAX`. |

---

## Interview Tips

1. **Explain the heap invariant** – keep the top as the k‑th nearest.
2. **Why a max‑heap?** – We need to discard the farthest when exceeding `k`. A min‑heap would keep the farthest at the bottom.
3. **Edge handling** – explicitly return `-1` when heap size < `k`.
4. **Complexity justification** – show `O(log k)` per operation and total `O(n log k)`.

---

## Final Thoughts  

* **The good**: A clean, single‑data‑structure solution that scales.  
* **The bad**: Need to be mindful of input size and type limits.  
* **The ugly**: Forgetting the sign of the heap or mis‑handling the `k`‑th threshold can silently break the solution.

This pattern shows up everywhere – from “k‑th smallest number” problems to “top‑k frequency” tasks. Master it, and you’ll be ready to tackle the next LeetCode challenge or interview question with confidence.

Happy coding, and best of luck landing that job! 🚀

--- 

> **SEO keywords**: LeetCode 3275, K-th Nearest Obstacle Queries, heap solution, priority queue, interview algorithm, Java priority queue, Python heapq, C++ priority_queue, job interview preparation, coding interview patterns.