---
title: LeetCode 1851. Minimum Interval to Include Each Query - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Problem

**LeetCode 1838 – Minimum Interval to Include Each Query**

You are given a list of **intervals** `[[l₁,r₁],[l₂,r₂], … , [lₙ,rₙ]]` and a list of **queries** `q₁,q₂,…,qₘ`.  
For each query point `qᵢ` you have to find the *shortest* interval that contains that point.  
If no interval contains the point the answer is `-1`.

```
Example
intervals = [[1,4],[2,2],[3,5]]
queries   = [2,3,4,5]

Answer:  [1,1,1,-1]
```

The constraints are large (`n , m ≤ 2·10⁵`), so a brute‑force solution is far too slow.



--------------------------------------------------------------------

## 2.  The Key Insight

To answer a query `q` we only need intervals that satisfy

```
start ≤ q ≤ end
```

Among those intervals we want the **shortest** one.  
If we sort the intervals by their `start` time and sweep through the queries in
increasing order, we can maintain a *min‑heap* of all intervals that have started but not yet ended.  
The top of the heap gives us the shortest interval that still covers the current query.

This is a classic **sweep‑line + priority‑queue** pattern.



--------------------------------------------------------------------

## 3.  Algorithm

1. **Sort intervals** by `start` (ascending).  
   If two intervals share the same start, the order of the second key is irrelevant because every interval will be inserted exactly once.

2. **Sort the queries** by value, but remember each query’s original index so that we can write back the answer in the original order.

3. **Sweep**  
   For each query `q` (in sorted order):
   * Insert every interval whose start ≤ `q` into a min‑heap.  
     The heap key is the interval length `len = end - start + 1`.  
     We also keep the interval’s `end` so that we can discard intervals that already ended.
   * Remove from the heap all intervals whose `end` < `q`.  
     Those cannot cover `q`.
   * If the heap is non‑empty, the top element is the shortest interval that contains `q`.  
     Store its length as the answer for this query; otherwise the answer is `-1`.

4. **Re‑order answers** into the original query order and return.

--------------------------------------------------------------------

## 4.  Complexity

| Step            | Complexity |
|-----------------|------------|
| Sorting intervals   | `O(n log n)` |
| Sorting queries     | `O(m log m)` |
| Inserting intervals | each interval inserted once → `O(n log n)` |
| Removing intervals  | each interval removed once → `O(n log n)` |
| Processing queries  | `O(m log n)` (heap operations) |
| Final re‑order     | `O(m)` |

Overall: **`O((n + m) log (n + m))` time**  
Space: **`O(n + m)`** (heap + temporary arrays).

--------------------------------------------------------------------

## 5.  Reference Implementations

Below are clean, production‑ready implementations in the three requested languages.

> **Tip** – The priority queue stores pairs `[length, end]` (Python) or `{len, end}` (C++/Java).  
> The comparison is only on the length; if two lengths are equal, the earlier inserted interval stays on top, which is fine because the answer is the same.

---

### 5.1 Java

```java
import java.util.*;

public class MinimumIntervalForQueries {
    public int[] minInterval(int[][] intervals, int[] queries) {
        // 1. Sort intervals by start time
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));

        // 2. Remember original query indices and sort queries
        int[][] sortedQ = new int[queries.length][2];
        for (int i = 0; i < queries.length; i++) {
            sortedQ[i][0] = i;          // original index
            sortedQ[i][1] = queries[i]; // query value
        }
        Arrays.sort(sortedQ, Comparator.comparingInt(a -> a[1]));

        // 3. Min‑heap: key = interval length
        PriorityQueue<int[]> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));

        int[] answer = new int[queries.length];
        int i = 0;  // pointer over intervals

        for (int[] q : sortedQ) {
            int qVal = q[1];
            // insert all intervals that start ≤ qVal
            while (i < intervals.length && intervals[i][0] <= qVal) {
                int len = intervals[i][1] - intervals[i][0] + 1;
                pq.offer(new int[]{len, intervals[i][1]}); // {length, end}
                i++;
            }
            // discard intervals that already ended
            while (!pq.isEmpty() && pq.peek()[1] < qVal) {
                pq.poll();
            }
            // top of heap is the shortest covering interval
            answer[q[0]] = pq.isEmpty() ? -1 : pq.peek()[0];
        }
        return answer;
    }
}
```

---

### 5.2 Python

```python
import heapq
from typing import List

class Solution:
    def minInterval(self, intervals: List[List[int]], queries: List[int]) -> List[int]:
        # 1. Sort intervals by start
        intervals.sort(key=lambda x: x[0])

        # 2. Keep original indices and sort queries
        indexed = [(q, i) for i, q in enumerate(queries)]
        indexed.sort(key=lambda x: x[0])

        # 3. Min‑heap: (length, end)
        heap = []          # python's heapq is a min‑heap
        ans = [0] * len(queries)
        idx = 0            # pointer over intervals

        for q, orig_idx in indexed:
            # add intervals that start before or at q
            while idx < len(intervals) and intervals[idx][0] <= q:
                start, end = intervals[idx]
                length = end - start + 1
                heapq.heappush(heap, (length, end))
                idx += 1

            # remove intervals that ended before q
            while heap and heap[0][1] < q:
                heapq.heappop(heap)

            # answer for this query
            ans[orig_idx] = heap[0][0] if heap else -1

        return ans
```

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> minInterval(vector<vector<int>>& intervals, vector<int>& queries) {
        // 1. Sort intervals by start
        sort(intervals.begin(), intervals.end(),
             [](const vector<int>& a, const vector<int>& b){ return a[0] < b[0]; });

        // 2. Store original indices of queries and sort
        vector<pair<int,int>> sortedQ;          // {query value, original index}
        for (int i = 0; i < (int)queries.size(); ++i)
            sortedQ.emplace_back(queries[i], i);
        sort(sortedQ.begin(), sortedQ.end());

        // 3. Min‑heap: store pairs {length, end}
        auto cmp = [](const pair<int,int>& a, const pair<int,int>& b){
            return a.first > b.first;           // min‑heap on length
        };
        priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);

        vector<int> answer(queries.size());
        size_t idx = 0;   // pointer over intervals

        for (const auto& [qVal, origIdx] : sortedQ) {
            // push intervals that have started
            while (idx < intervals.size() && intervals[idx][0] <= qVal) {
                int len = intervals[idx][1] - intervals[idx][0] + 1;
                pq.emplace(len, intervals[idx][1]);  // {length, end}
                ++idx;
            }

            // pop those that already ended
            while (!pq.empty() && pq.top().second < qVal)
                pq.pop();

            answer[origIdx] = pq.empty() ? -1 : pq.top().first;
        }
        return answer;
    }
};
```

All three snippets follow the same sweep‑line logic and run comfortably within the limits of the problem.



--------------------------------------------------------------------

## 6.  Common Pitfalls & How to Avoid Them

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Brute‑force** (checking every interval for every query) | O(n·m) → 4×10¹⁰ operations | Use the sweep‑line algorithm above |
| **Incorrect heap key** | Storing only `end` in the heap can lead to a wrong answer – you would get the *first* interval, not necessarily the shortest | Use a composite key: `(length, end)` and compare only on `length` |
| **Not remembering original indices** | You cannot place the answers back into the original query order | Keep an `index` array or tuple while sorting |
| **Failing to discard ended intervals** | The heap will grow unbounded, leading to memory blow‑up | Pop while `end < current_query` |

If you’re just learning this pattern, start with a very small toy example, print the heap after each insertion/deletion, and watch how the algorithm behaves.



--------------------------------------------------------------------

## 7.  What Makes This Solution Great

1. **Linear‑time processing** – each interval is pushed/popped once.  
2. **No auxiliary data structures beyond a heap** – keeps memory usage minimal.  
3. **Language‑agnostic** – the same pattern works in Java, Python, C++ and many other languages.  
4. **Extensible** – if later you need the *actual interval* instead of just its length, simply store the whole interval in the heap.

--------------------------------------------------------------------

## 8.  Quick Takeaway

- *If you need to find the shortest interval covering a set of points, sort by start, sweep through the points, keep a min‑heap of intervals that haven’t ended yet, and you’re done.*

The pattern is a powerful tool in algorithm design – use it whenever you see “process events in order” and “pick the best candidate among all already started events”.  

Happy coding! 🚀



--------------------------------------------------------------------

### 9.  SEO & Final Thought

> **Keywords**: LeetCode 1838, Minimum Interval, sweep line, priority queue, Python, Java, C++ solution, algorithm, time complexity, space complexity, coding interview, data structures.  

> **Meta‑description**: Solve LeetCode 1838 – “Minimum Interval to Include Each Query” – with a sweep‑line + priority‑queue approach. Read the full guide, code in Java, Python, C++, complexity analysis, and common pitfalls.  

> **Target audience**: Software engineers preparing for interviews, competitive programmers, and anyone curious about efficient interval queries.  

Feel free to copy, adapt, and share these snippets – and drop a ⭐ on GitHub if you found the explanation helpful!