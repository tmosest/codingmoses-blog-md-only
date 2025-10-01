---
title: LeetCode 3009. Maximum Number of Intersections on the Chart - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 3009 – Maximum Number of Intersections on the Chart  
> **A deep dive into the hard LeetCode challenge with Java, Python & C++ solutions, a “good‑bad‑ugly” analysis, and an SEO‑optimized blog post that will get you noticed by hiring managers.**

---

## 1.  Problem Recap

You’re given a 1‑indexed array `y` (`y[1] … y[n]`).  
The `k`‑th point of a line chart is `(k, y[k])` and consecutive points are **never** on the same horizontal line (`y[i] ≠ y[i+1]`).

> **Task** – Draw a single horizontal line anywhere.  
> Find the **maximum number of intersection points** between that line and the chart.

---

## 2.  Intuition

For a particular height `H`:

1. **Vertices** – Every point `y[k] == H` is an intersection.
2. **Crossings** – Every segment that *strictly* crosses `H` (its two endpoints lie on opposite sides of `H`) is also an intersection.

So, for each distinct y‑value `H`, the answer is

```
count_of_points_equal_to_H  +  count_of_segments_crossing_H
```

The challenge is to compute the second term for *all* distinct heights in **O(n log n)** time.

---

## 3.  Optimal Approach – Sweep Line + Difference Array

1. **Collect & sort all distinct y‑values** → array `vals[0…m-1]`.
2. **Count vertices** – frequency map `freq[H]`.
3. **Process every segment (y[i], y[i+1])**  
   * Let `low  = min(y[i], y[i+1])`, `high = max(y[i], y[i+1])`.
   * All heights strictly between `low` and `high` receive **+1** crossing.
   * Using a difference array `diff[0…m]` we can mark the range in O(1):
     ```
     diff[index(low)+1] += 1
     diff[index(high)]   -= 1
     ```
4. **Prefix sum over `diff`** → `cross[i]` = number of crossings for `vals[i]`.
5. **Answer** – maximum over all `i` of `freq[i] + cross[i]`.

Why it works  
*Every segment that has its two endpoints on different sides of `H` adds exactly one to all heights inside `(low, high)`. The difference array trick guarantees that we add +1 to the correct range without touching the endpoints themselves.*

### Complexity  

*Sorting distinct heights* – `O(m log m)` ( `m ≤ n` )  
*Processing segments* – `O(n)`  
*Prefix sum* – `O(m)`  

Overall **O(n log n)** time, **O(n)** auxiliary memory.

---

## 4.  Code

Below are clean, fully‑commented implementations in **Java, Python, and C++**.  
All three follow the same algorithm.

### 4.1 Java (O(n log n))

```java
import java.util.*;

public class Solution {
    public int maxIntersectionCount(int[] y) {
        int n = y.length;
        // 1. Collect distinct values & frequency
        TreeMap<Integer, Integer> freqMap = new TreeMap<>();
        for (int val : y) {
            freqMap.put(val, freqMap.getOrDefault(val, 0) + 1);
        }

        // Convert to list/array for index lookup
        int m = freqMap.size();
        Integer[] vals = freqMap.keySet().toArray(new Integer[0]);

        // 2. Diff array for crossings
        int[] diff = new int[m + 1];          // one extra slot for easy suffix
        for (int i = 0; i < n - 1; i++) {
            int low  = Math.min(y[i], y[i + 1]);
            int high = Math.max(y[i], y[i + 1]);

            // indices of low and high in sorted vals
            int il = Arrays.binarySearch(vals, low);
            int ir = Arrays.binarySearch(vals, high);

            // Add +1 to all heights strictly between low and high
            diff[il + 1] += 1;
            diff[ir]     -= 1;
        }

        // 3. Prefix sum to get crossings per height
        int[] cross = new int[m];
        int running = 0;
        for (int i = 0; i < m; i++) {
            running += diff[i];
            cross[i] = running;
        }

        // 4. Find maximum intersections
        int answer = 0;
        for (int i = 0; i < m; i++) {
            int intersections = freqMap.get(vals[i]) + cross[i];
            answer = Math.max(answer, intersections);
        }
        return answer;
    }
}
```

### 4.2 Python (O(n log n))

```python
from bisect import bisect_left
from collections import Counter
from typing import List

class Solution:
    def maxIntersectionCount(self, y: List[int]) -> int:
        n = len(y)
        # 1. frequency map
        freq = Counter(y)
        # sorted unique heights
        vals = sorted(freq)
        m = len(vals)
        # 2. diff array
        diff = [0] * (m + 1)

        for i in range(n - 1):
            low, high = (y[i], y[i + 1]) if y[i] < y[i + 1] else (y[i + 1], y[i])
            il = bisect_left(vals, low)
            ir = bisect_left(vals, high)
            diff[il + 1] += 1
            diff[ir] -= 1

        # 3. prefix sum for crossings
        cross = [0] * m
        running = 0
        for i in range(m):
            running += diff[i]
            cross[i] = running

        # 4. maximum intersections
        return max(freq[h] + cross[i] for i, h in enumerate(vals))
```

### 4.3 C++ (O(n log n))

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxIntersectionCount(vector<int>& y) {
        int n = y.size();
        // frequency of each height
        unordered_map<int, int> freq;
        for (int v : y) freq[v]++;

        // sorted distinct heights
        vector<int> vals;
        for (auto &p : freq) vals.push_back(p.first);
        sort(vals.begin(), vals.end());
        int m = vals.size();

        vector<int> diff(m + 1, 0);

        for (int i = 0; i < n - 1; ++i) {
            int low  = min(y[i], y[i + 1]);
            int high = max(y[i], y[i + 1]);

            int il = lower_bound(vals.begin(), vals.end(), low) - vals.begin();
            int ir = lower_bound(vals.begin(), vals.end(), high) - vals.begin();

            diff[il + 1] += 1;
            diff[ir]     -= 1;
        }

        // prefix sum to get crossings
        vector<int> cross(m, 0);
        int running = 0;
        for (int i = 0; i < m; ++i) {
            running += diff[i];
            cross[i] = running;
        }

        int answer = 0;
        for (int i = 0; i < m; ++i) {
            answer = max(answer, freq[vals[i]] + cross[i]);
        }
        return answer;
    }
};
```

---

## 5.  “Good – Bad – Ugly” Analysis

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n log n) – optimal for this problem | O(n²) brute‑force would time‑out | None |
| **Space Complexity** | O(n) auxiliary (maps, diff array) | O(1) *in‑place* attempts ignore crossings | Unnecessary duplication of data |
| **Clarity** | Clear separation of frequency & crossings | Complex sweep line implementations (e.g., priority queues) | Using floating‑point midpoints (e.g., 1.5) leads to precision bugs |
| **Maintainability** | Small, well‑documented functions | Deep nested loops with side‑effects | Mixing data structures with index logic (mixing lists, dicts, and arrays) |
| **Robustness** | Handles all integer heights, no float math | Over‑using `binarySearch` inside a tight loop without caching indices | Mutable global state, static variables – race conditions |

### What to Avoid (the “Ugly” part)

1. **Floating‑point hacks** – LeetCode tests are all integers. If you try to evaluate “height = 1.5” you’ll get rounding errors for very large numbers.
2. **Segment‑by‑segment priority queue** – Works, but adds `O(n log n)` *overhead* for an `O(n)` solution that we can do in O(1) per segment.
3. **In‑place modification of the input array** – You might think it saves memory, but you lose the original frequency map.

---

## 6.  SEO‑Optimized Blog Post (Target Keywords)

> *3009 LeetCode, Maximum Number of Intersections on the Chart, Java O(n log n), Python solution, C++ solution, hard LeetCode problem, algorithm interview, data structure, frequency map, difference array, sweep line, job interview preparation, coding interview, LeetCode 3009, interview algorithm, algorithmic thinking.*

---

### 6.1 Title

**“3009 – Maximum Number of Intersections on the Chart – A Step‑by‑Step O(n log n) Solution in Java, Python & C++”**

---

### 6.2 Meta Description

> Learn the **hardest** LeetCode problem (3009) with an **O(n log n)** solution.  
> Get Java, Python, and C++ code, performance analysis, pitfalls to avoid, and interview‑ready explanations.  

---

### 6.3 Full Post

```markdown
# 3009 – Maximum Number of Intersections on the Chart  
**Hard LeetCode Problem | Java O(n log n) | Python | C++ | Interview Strategy**

## Problem Overview
LeetCode 3009 asks you to draw a single horizontal line on a chart defined by a 1‑indexed array `y`.  
The goal is to maximize the number of intersection points between that line and the chart.

### Key Insight
For a height `H`:
- Every point `y[i] == H` is an intersection.
- Every segment that straddles `H` (its endpoints are on opposite sides) adds one more intersection.

So we need **vertex counts** *plus* **crossing counts** for every distinct height.

### Optimal O(n log n) Approach
1. **Collect all distinct heights** → `vals[0…m-1]`.  
2. **Count vertices** with a frequency map.  
3. **Sweep every segment** `(y[i], y[i+1])` and use a **difference array** to increment all heights strictly inside `(low, high)` in O(1).  
4. **Prefix sum** gives the crossing count for each height.  
5. The answer is `max(freq[H] + cross[H])`.

> **Why is this optimal?**  
> Any other solution (e.g., priority queue based sweep line) ends up with the same asymptotic complexity but adds constant factors. This difference‑array trick reduces per‑segment work to *constant* time.

### Complexity
- **Time**: `O(n log n)` (sorting distinct heights)  
- **Space**: `O(n)` (frequency map + diff array)

### Java Implementation
```java
public int maxIntersectionCount(int[] y) {
    // ... (code from section 4.1)
}
```

### Python Implementation
```python
class Solution:
    def maxIntersectionCount(self, y: List[int]) -> int:
        # ... (code from section 4.2)
```

### C++ Implementation
```cpp
class Solution {
public:
    int maxIntersectionCount(vector<int>& y) {
        // ... (code from section 4.3)
    }
};
```

### “Good – Bad – Ugly”
| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Complexity** | O(n log n) – optimal | O(n²) brute‑force times out | No performance gains |
| **Clarity** | Clean frequency + crossings separation | Nested loops + priority queue complicate reasoning | Using floating‑point midpoints leads to precision bugs |
| **Maintainability** | Small, well‑commented | Deep stateful sweeps | Duplicate data, over‑engineering |

### Takeaway for Interviewers
- **Explain the intuition** first (vertices + crossings).  
- **Show the difference‑array trick** – it’s the *aha* moment many candidates miss.  
- **Discuss edge cases** – crossing *strictly* inside a segment, not on the endpoints.  

### TL;DR
*3009 is a perfect showcase of algorithmic thinking:  
- A clear mathematical decomposition,  
- A sweep‑line implementation that’s both fast and easy to reason about,  
- And clean code that a hiring manager can read and review in minutes.*

---

## 6.4 Call to Action

> **Want to impress recruiters?**  
> Post this solution on your portfolio, write a blog (like this one), or add it to your GitHub readme.  
> Include the tags `#LeetCode #3009 #AlgorithmInterview #Java #Python #C++` and watch recruiters start pinging you!

--- 

## 6.5  Final Thoughts

*LeetCode 3009 is not just a hard problem – it’s a showcase problem.  
A concise, optimal solution demonstrates mastery of data structures (maps, binary search), algorithmic techniques (sweep line, difference array), and the ability to write production‑ready code.  

With the code above and a well‑structured blog post, you’ll stand out as a candidate who can solve hard problems *efficiently* and *elegantly*. Happy coding, and good luck on your next interview!*