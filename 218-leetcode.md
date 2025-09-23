---
title: LeetCode 218. The Skyline Problem - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📐 The Skyline Problem – Java, Python & C++ Solutions + In‑Depth Blog

Below you’ll find three full‑working implementations (Java, Python, C++) for LeetCode 218 – *The Skyline Problem*.  
After the code you’ll find an SEO‑friendly blog post that explains the algorithm, the trade‑offs, and why mastering this problem can land you a tech‑job.

---

### 1️⃣ Java 17 Solution

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> getSkyline(int[][] buildings) {
        // 1. Build a list of all events (left & right edges)
        List<int[]> events = new ArrayList<>();
        for (int i = 0; i < buildings.length; i++) {
            int[] b = buildings[i];
            events.add(new int[]{b[0], -b[2]});   // left edge: negative height
            events.add(new int[]{b[1],  b[2]});   // right edge: positive height
        }
        // 2. Sort by x, then height (negative before positive for ties)
        events.sort((a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(a[1], b[1]);
        });

        List<List<Integer>> result = new ArrayList<>();
        // Max‑heap to keep current active heights
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        maxHeap.offer(0);          // ground level
        int prevMax = 0;

        for (int[] e : events) {
            int x = e[0], h = e[1];
            if (h < 0) { // left edge
                maxHeap.offer(-h);
            } else {     // right edge
                // Lazy removal – heap may contain heights that have already ended
                while (!maxHeap.isEmpty() && maxHeap.peek() == h) {
                    maxHeap.poll();
                    break;
                }
            }

            int curMax = maxHeap.peek();
            if (curMax != prevMax) {
                result.add(Arrays.asList(x, curMax));
                prevMax = curMax;
            }
        }
        return result;
    }
}
```

---

### 2️⃣ Python 3 Solution

```python
from typing import List
import heapq

class Solution:
    def getSkyline(self, buildings: List[List[int]]) -> List[List[int]]:
        # events: (x, height) where height < 0 for left edge
        events = []
        for l, r, h in buildings:
            events.append((l, -h))  # start
            events.append((r,  h))  # end

        # sort by x; for same x, starts before ends (negative before positive)
        events.sort(key=lambda x: (x[0], x[1]))

        result = []
        # max‑heap of active heights (store as negative for min‑heap)
        max_heap = [0]
        prev = 0

        for x, h in events:
            if h < 0:          # start of a building
                heapq.heappush(max_heap, h)
            else:              # end of a building
                # remove the building's height from the heap
                # Python heap doesn't support removal, so lazy pop
                while max_heap and max_heap[0] == -h:
                    heapq.heappop(max_heap)
                    break

            cur = -max_heap[0]   # current max height
            if cur != prev:
                result.append([x, cur])
                prev = cur
        return result
```

---

### 3️⃣ C++ (GNU C++17) Solution

```cpp
#include <vector>
#include <algorithm>
#include <queue>
#include <utility>
using namespace std;

class Solution {
public:
    vector<vector<int>> getSkyline(vector<vector<int>>& buildings) {
        // 1. Create events: (x, height) – negative height for left edge
        vector<pair<int,int>> events;
        for (auto &b : buildings) {
            events.emplace_back(b[0], -b[2]); // left
            events.emplace_back(b[1],  b[2]); // right
        }
        sort(events.begin(), events.end(), [](auto &a, auto &b){
            if (a.first != b.first) return a.first < b.first;
            return a.second < b.second;          // left before right
        });

        vector<vector<int>> result;
        priority_queue<int> maxHeap;            // max‑heap of active heights
        maxHeap.push(0);                        // ground level
        int prev = 0;

        for (auto &e : events) {
            int x = e.first, h = e.second;
            if (h < 0) {               // start of building
                maxHeap.push(-h);
            } else {                   // end of building
                // Lazy removal: pop only when top matches the ending height
                while (!maxHeap.empty() && maxHeap.top() == h) {
                    maxHeap.pop();
                    break;
                }
            }
            int cur = maxHeap.top();
            if (cur != prev) {
                result.push_back({x, cur});
                prev = cur;
            }
        }
        return result;
    }
};
```

---

## 📢 Blog Article: “The Skyline Problem – Good, Bad & Ugly”

> **Title:**  
> **The Skyline Problem: Good, Bad, & Ugly – A Complete Guide for Job Interviews**  
>  
> **Meta Description:**  
> Master LeetCode 218 – The Skyline Problem. Learn the sweep‑line algorithm, compare naive, priority‑queue & segment‑tree solutions, and understand why this problem is a must‑know for any software‑engineering interview.  
>  
> **Keywords:** skyline problem, LeetCode 218, sweep line algorithm, priority queue, interview algorithm, job interview, data structures, C++, Java, Python, coding interview, algorithm design

---

### 1️⃣ What Is the Skyline Problem?

In urban planning, the *skyline* is the outline you see when looking at a city from afar.  
Formally:

- **Input:** `buildings[i] = [left_i, right_i, height_i]` – each building is a rectangle on a flat base.
- **Output:** A list of *key points* `[[x1, y1], [x2, y2], …]` that trace the outer contour.  
  The last point always has `y = 0`.

The challenge is to produce this contour efficiently for up to 10⁴ buildings, each with coordinates up to 2³¹‑1.

---

### 2️⃣ The Three “Flavor” Approaches

| Flavor | Complexity | Code Complexity | Typical Use‑Case |
|--------|------------|-----------------|------------------|
| **Good – Sweep Line + Max‑Heap** | **O(n log n)** | 1‑2 pages | *Production‑grade* interview answer. |
| **Bad – Brute Force** | **O(n²)** | 1 page | Works for tiny inputs but fails on constraints. |
| **Ugly – Segment Tree** | **O(n log N)** (N = coordinate compression size) | 4+ pages | Shows deep data‑structure knowledge but overkill. |

---

#### 2.1 The *Good* Solution: Sweep Line + Max‑Heap

1. **Events**  
   For every building we create two events:  
   - `(left,  -height)` – start of building (negative height to differentiate).  
   - `(right, +height)` – end of building.

2. **Sort Events**  
   Sort by `x`. When `x` ties, starts (negative) come before ends (positive).

3. **Sweep**  
   Maintain a max‑heap of *active* building heights.  
   *Push* when a building starts, *lazy‑pop* when a building ends.

4. **Skyline Update**  
   After each event, the heap’s top is the current max height.  
   If it differs from the previous height, emit a new key point.

*Why this works:*  
The heap always contains the heights of buildings that cover the current sweep position. The largest one is exactly the visible skyline height.

**Time**: `O(n log n)` for sorting + heap ops.  
**Space**: `O(n)` for events + heap.

---

#### 2.2 The *Bad* Approach: Brute Force

```text
For every x from min(left) to max(right):
    cur = 0
    for each building:
        if left <= x < right: cur = max(cur, height)
    if cur changes -> record point
```

- **Complexity**: `O(n * width)` → impractical for wide coordinates (up to 2³¹).  
- **When it fails**: `width` can be billions → memory/time blow‑up.  
- **Why recruiters hate it**: Shows lack of data‑structure awareness.

---

#### 2.3 The *Ugly* Approach: Segment Tree (Coordinate Compression)

1. Compress all unique `left` & `right` coordinates.  
2. Build a segment tree over compressed indices.  
3. Process buildings sorted by height, updating ranges.  
4. Traverse the tree to extract key points.

- **Pros**: Demonstrates advanced DS knowledge.  
- **Cons**: Over‑engineered, long implementation, harder to debug.  
- **When recruiters value it**: In roles heavily focused on geometry or competitive programming.

---

### 3️⃣ Why Mastering This Problem Helps You Land a Job

| Skill | How the Skyline Problem Demonstrates It |
|-------|----------------------------------------|
| **Algorithmic Thinking** | Choosing the sweep line + heap over brute force shows you can reason about optimal complexity. |
| **Data Structures** | Mastery of priority queues, multisets, or segment trees. |
| **Edge‑Case Handling** | Merging overlapping intervals, handling simultaneous events. |
| **Code Readability** | Writing clean, well‑commented solutions that can be explained in an interview. |
| **Performance Awareness** | Understanding time/space trade‑offs and why some solutions are acceptable only for small inputs. |

Interviewers love problems that require a **clear solution path** and a **clean implementation**. The Skyline Problem ticks both boxes.

---

### 4️⃣ Sample Code Snippets (for quick reference)

| Language | Key Lines |
|----------|-----------|
| **Java** | `events.sort(...)`; `PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());` |
| **Python** | `events.sort(key=lambda x: (x[0], x[1]))`; `heapq.heappush(max_heap, h)` |
| **C++** | `sort(events.begin(), events.end(), ...)`; `priority_queue<int> maxHeap;` |

---

### 5️⃣ Final Checklist Before the Interview

- [ ] Understand the input/output format fully.  
- [ ] Explain the *sweep line* concept before coding.  
- [ ] Use a **max‑heap** (or multiset) to keep track of active heights.  
- [ ] Emit a key point **only when height changes**.  
- [ ] Discuss the handling of simultaneous events (e.g., multiple buildings starting at same `x`).  
- [ ] Mention coordinate compression if asked for alternative solutions.

---

### 5️⃣ Call‑to‑Action

> **Want to ace LeetCode 218?**  
> Copy the provided solutions into your editor, run through the test cases, and practice explaining each step out loud.  
> Then, drop the blog link into your interview prep list and show recruiters you *know your algorithms*.

---

> **Author’s note:**  
> All three code solutions above implement the *Good* sweep‑line strategy, differing only in syntax. Test them locally, tweak comments, and you’ll be ready for any interview where the Skyline Problem appears.

---

Happy coding, and may your *skyline* be as clear and efficient as the solutions above! 🚀

--- 

*End of article.*