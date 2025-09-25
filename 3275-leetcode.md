---
title: LeetCode 3275. K-th Nearest Obstacle Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 **The Good, the Bad, and the Ugly of LeetCode 3275 – K‑th Nearest Obstacle Queries**

> *“If you can solve this in under 2 minutes, you’ll impress any hiring manager.”*  
> — *Some very experienced interviewers*

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Why This Problem Rocks Your Interview Portfolio](#why-this-problem-rocks-your-interview-portfolio)  
3. [The “Good” – The Elegant Max‑Heap Solution](#the-good)  
4. [The “Bad” – Common Pitfalls & How to Avoid Them](#the-bad)  
5. [The “Ugly” – Advanced Tweaks & Performance Hints](#the-ugly)  
6. [Full Code (Java / Python / C++)](#full-code)  
7. [Wrapping Up – How to Nail the Interview](#wrapping-up)  
8. [SEO‑Friendly Takeaway](#seo-takeaway)

---

## Problem Overview <a name="problem-overview"></a>

> **LeetCode 3275 – K‑th Nearest Obstacle Queries**  
> **Difficulty:** Medium  

You’re on an infinite 2‑D plane.  
- Initially, no obstacles exist.  
- You receive `queries[i] = [x, y]` – add an obstacle at that coordinate.  
- After each insertion, you must return the distance of the **k‑th nearest** obstacle from the origin.  
- The distance is the Manhattan distance: `|x| + |y|`.  
- If there are fewer than `k` obstacles, return `-1`.

**Constraints**

| Item | Value |
|------|-------|
| `1 ≤ queries.length ≤ 2·10⁵` | |
| `-10⁹ ≤ x, y ≤ 10⁹` | |
| `1 ≤ k ≤ 10⁵` | |
| All queries are unique | |

**Example**

```text
queries = [[1,2],[3,4],[2,3],[-3,0]], k = 2
Output: [-1,7,5,3]
```

---

## Why This Problem Rocks Your Interview Portfolio <a name="why-this-problem-rocks-your-interview-portfolio"></a>

- **Classic data‑structure pattern**: heap (priority queue) – a staple in coding interviews.  
- **Large input size**: requires `O(log k)` updates, not `O(n)` per query.  
- **Space‑efficient**: only keep the top `k` distances.  
- **Cross‑language proof**: you can solve it in Java, Python, or C++ – showing versatility.

---

## The “Good” – The Elegant Max‑Heap Solution <a name="the-good"></a>

### Intuition

Keep a **max‑heap** (priority queue) that stores the **k smallest** distances seen so far:

- The heap’s top is the **largest** among those k smallest distances.  
- That value is precisely the **k‑th nearest obstacle**.  
- If we insert a new distance and the heap size becomes `k+1`, we pop the top (the largest) – thus keeping only the smallest k.

### Algorithm

```text
for each query (x, y):
    d = |x| + |y|
    push d into max-heap
    if heap.size > k:
        pop the top (largest)
    if heap.size == k:
        result = heap.top()   // kth nearest distance
    else:
        result = -1
```

### Correctness Proof (Sketch)

- **Invariant**: After processing the first `i` queries, the heap contains the `min(k, i)` smallest distances among the first `i` obstacles.
- **Base**: `i = 0` → heap empty → invariant holds.
- **Step**: When adding distance `d`:
  - If `i < k`: heap size `≤ k`, simply push `d`.  
  - If `i ≥ k`: push `d`; heap now has `k+1` elements. Popping the largest restores the invariant.
- Therefore, the top of the heap is the `k`‑th smallest distance whenever the heap size equals `k`.

---

## The “Bad” – Common Pitfalls & How to Avoid Them <a name="the-bad"></a>

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| Using a **min‑heap** instead of a max‑heap | Thinking “smallest first” but we need the `k`‑th smallest | Switch to a max‑heap (`reverse` order in Java, `-value` in Python, `greater<int>` in C++) |
| Forgetting to **pop** when heap size exceeds `k` | Heap grows to `O(n)`, causing memory blow and `O(log n)` updates | Always `if (heap.size() > k) heap.pop()` |
| Using `int` for distance when coordinates can be `±10⁹` | `|x| + |y|` fits in 32‑bit? `|10⁹| + |10⁹| = 2·10⁹` < 2³¹, but still safer to use `long`/`long long` | Use `long` in Java, `int` in Python (unbounded), `long long` in C++ |
| Returning **-1** when heap size < `k` but not updating `results` array | Off‑by‑one indexing, or not initializing result array | Explicitly set `-1` in the `else` clause |
| Not handling **duplicate coordinates** | Problem guarantees uniqueness but forgetting to rely on it may cause wrong logic | Trust the input; no need for a set |

---

## The “Ugly” – Advanced Tweaks & Performance Hints <a name="the-ugly"></a>

1. **Batch Insertions**  
   If many queries are processed offline, you can first sort all distances and use a Fenwick tree or segment tree to answer each query in `O(log n)` time. Not needed for LeetCode but useful in competitions.

2. **Avoid Repeated Absolute Calls**  
   Pre‑compute `abs` once per query; in tight loops, inline the operation.

3. **Memory‑Optimized Heap**  
   For languages like Java, use `IntPriorityQueue` (fast‑util) or `PriorityQueue<Integer>` with a custom comparator.  
   In C++, `std::priority_queue<int, vector<int>, greater<int>>` is already a min‑heap; use `less<int>` for max‑heap.

4. **Parallelization**  
   Not applicable for LeetCode, but if you have multi‑core, you could split queries and merge partial heaps.

5. **Profile for Worst‑Case**  
   Worst‑case time: `O(n log k)`; worst‑case memory: `O(k)`.  
   For `n = 2·10⁵`, `k = 10⁵`, this is comfortably fast in all three languages.

---

## Full Code (Java / Python / C++) <a name="full-code"></a>

Below are clean, well‑commented implementations in **Java 17**, **Python 3.10**, and **C++17**.

### 1️⃣ Java

```java
import java.util.*;

/**
 * LeetCode 3275 – K‑th Nearest Obstacle Queries
 */
public class Solution {
    public int[] resultsArray(int[][] queries, int k) {
        int n = queries.length;
        // Max‑heap to keep the k smallest distances
        PriorityQueue<Long> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

        int[] res = new int[n];
        for (int i = 0; i < n; i++) {
            long dist = Math.abs((long) queries[i][0]) + Math.abs((long) queries[i][1]);
            maxHeap.offer(dist);

            // Keep only k elements
            if (maxHeap.size() > k) {
                maxHeap.poll();
            }

            res[i] = (maxHeap.size() == k) ? maxHeap.peek().intValue() : -1;
        }
        return res;
    }

    // Driver (optional)
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[][] queries = {{1,2},{3,4},{2,3},{-3,0}};
        int k = 2;
        System.out.println(Arrays.toString(sol.resultsArray(queries, k)));
        // Output: [-1, 7, 5, 3]
    }
}
```

### 2️⃣ Python

```python
from typing import List
import heapq

class Solution:
    def resultsArray(self, queries: List[List[int]], k: int) -> List[int]:
        # Max‑heap using negative distances
        max_heap = []
        res = []

        for x, y in queries:
            dist = abs(x) + abs(y)
            heapq.heappush(max_heap, -dist)          # push negative to simulate max‑heap

            if len(max_heap) > k:                    # keep size <= k
                heapq.heappop(max_heap)

            res.append(-max_heap[0] if len(max_heap) == k else -1)

        return res

# Demo
if __name__ == "__main__":
    sol = Solution()
    queries = [[1,2],[3,4],[2,3],[-3,0]]
    k = 2
    print(sol.resultsArray(queries, k))  # [-1, 7, 5, 3]
```

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> resultsArray(vector<vector<int>>& queries, int k) {
        // Max‑heap: largest distance on top
        priority_queue<long long> maxHeap;
        vector<int> res;
        res.reserve(queries.size());

        for (auto &q : queries) {
            long long dist = llabs(q[0]) + llabs(q[1]);
            maxHeap.push(dist);

            if (maxHeap.size() > k)                // keep only k smallest
                maxHeap.pop();

            res.push_back((int)(maxHeap.size() == k ? maxHeap.top() : -1));
        }
        return res;
    }
};

// Optional main() for local testing
int main() {
    Solution s;
    vector<vector<int>> queries = {{1,2},{3,4},{2,3},{-3,0}};
    int k = 2;
    vector<int> ans = s.resultsArray(queries, k);
    for (int v : ans) cout << v << ' ';   // -1 7 5 3
}
```

> **Tip**: Compile with `-O2 -std=c++17` for LeetCode.

---

## Wrapping Up – How to Nail the Interview <a name="wrapping-up"></a>

1. **Start with a clear statement** – rewrite the problem in your own words.  
2. **Explain the data‑structure choice**: heap, why max‑heap, what operations we need.  
3. **Walk through one example** on the whiteboard.  
4. **Handle edge cases**: fewer than `k` elements, large coordinates.  
5. **Discuss complexity** (`O(n log k)`, `O(k)` space).  
6. **Mention possible extensions** (batch, segment tree) to show depth.  
7. **Test locally** – use the demo snippets to verify.

---

## SEO‑Friendly Takeaway <a name="seo-takeaway"></a>

> **Title:** Master LeetCode 3275 (K‑th Nearest Obstacle Queries) – Java, Python, C++  
> **Keywords:** LeetCode 3275, K‑th nearest obstacle, Manhattan distance, heap, priority queue, coding interview, Java solution, Python solution, C++ solution, time complexity, space complexity, coding interview patterns.  

**Why this matters:**  
- Recruiters scan titles for patterns – your article contains the exact problem name and language tags.  
- Search engines rank articles with high keyword density and useful structure.  
- By including this content, you’ll appear in top results for “K‑th nearest obstacle queries solution”.

---

## How to Nail the Interview <a name="wrapping-up"></a>

- **Show your thought process**: start with a simple (wrong) idea, then refine it.  
- **Ask clarifying questions**: e.g., “Is the plane Manhattan distance?” – demonstrates communication.  
- **Write clean, self‑contained code**; don’t rely on hidden libraries.  
- **Talk about complexity** before writing code.  
- **If time allows**, mention the advanced tweaks we covered.

Good luck—you’ve got this! 🚀

--- 

**Ready for the next challenge?**  
Check out **LeetCode 3275** today and let the heap work for you.  

--- 

**#LeetCode3275 #CodingInterview #Heap #PriorityQueue #Java #Python #C++**  
--- 

### 📌 Final Thought

By mastering the **max‑heap trick** in this problem, you’ll demonstrate solid algorithmic thinking and a strong grasp of core data structures—exactly what recruiters look for. Happy coding!