---
title: LeetCode 1943. Describe the Painting - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Recap – LeetCode 1943 “Describe the Painting”

> **Goal** – Convert a set of colored, overlapping half‑closed segments on a number line into the minimum set of non‑overlapping half‑closed segments that correctly describe every *mixed color* on the line.  
> **Output** – For each new segment `[l, r)` we output the *sum* of all colors that are present in that interval (the “mixed color” is a set of distinct colors, but we only return the sum).

---

### Why it’s a classic interview problem

* **Line Sweep** – the canonical technique for dealing with intervals that change at endpoints.  
* **Data structures** – balanced binary search trees (`TreeMap`/`std::map`) or *difference arrays*.  
* **Edge‑cases** – handling “holes” (unpainted parts) and overlapping ranges that have the same sum but different color sets.  
* **Complexity** – `O(n log n)` (map) or `O(n + maxCoord)` (array) – both acceptable for the constraints.

---

## 👨‍💻 Solution Idea – Line Sweep with a Difference Map

1. **Collect all “interesting” coordinates** – every start or end point of a segment.  
2. **Store the delta of color sum at each point**  
   * Add `color` at the *start* of a segment.  
   * Subtract `color` at the *end* of a segment.  
3. **Sort the coordinates** (or use a sorted map) and iterate in order.  
4. Maintain a running `currentSum`.  
5. Whenever `currentSum > 0` and the next coordinate differs from the previous one, emit a segment `[prevCoord, currCoord)` with the current sum.  
6. Skip intervals where `currentSum == 0` – they’re unpainted.

Because every color in the input is *unique*, the set of active colors changes whenever the sum changes. Therefore a pure numeric sum is enough for the output.

---

## 📦 Code

Below are clean, idiomatic solutions in **Java, Python, and C++**.  
Each solution uses the same algorithm; only the data‑structure details differ.

---

### Java (Fast array‑based implementation)

```java
import java.util.*;

public class Solution {
    public List<List<Long>> splitPainting(int[][] segments) {
        // Max endpoint ≤ 1e5 + 1 (the “half‑closed” end)
        final int MAX = 100001;
        long[] diff = new long[MAX + 2];   // diff[i] = change in sum at i
        boolean[] isEndpoint = new boolean[MAX + 2];

        // Build difference array
        for (int[] seg : segments) {
            int start = seg[0], end = seg[1];
            long color = seg[2];
            diff[start] += color;
            diff[end]   -= color;
            isEndpoint[start] = true;
            isEndpoint[end]   = true;
        }

        List<List<Long>> result = new ArrayList<>();
        long current = 0;   // running sum
        int prev = 0;       // previous coordinate

        for (int i = 1; i <= MAX + 1; i++) {
            if (isEndpoint[i] && current > 0) {
                // Paint interval [prev, i) with current sum
                result.add(Arrays.asList((long)prev, (long)i, current));
            }
            prev = isEndpoint[i] ? i : prev;   // jump only on endpoints
            current += diff[i];
        }
        return result;
    }
}
```

**Complexity**

| Step | Time | Space |
|------|------|-------|
| Building diff | `O(n)` | `O(maxCoord)` |
| Scanning array | `O(maxCoord)` | – |
| Total | `O(n + maxCoord)` ≈ `O(1e5)` | `O(maxCoord)` |

---

### Python (Using `sortedcontainers` or built‑in `dict`)

```python
from typing import List
from collections import defaultdict

class Solution:
    def splitPainting(self, segments: List[List[int]]) -> List[List[int]]:
        diff = defaultdict(int)
        ends  = set()                      # coordinates that are real endpoints

        # Build difference map
        for s, e, c in segments:
            diff[s] += c
            diff[e] -= c
            ends.add(s)
            ends.add(e)

        # Sorted list of all interesting coordinates
        coords = sorted(ends)

        res = []
        current = 0
        prev = coords[0]                   # first endpoint

        for i in range(1, len(coords)):
            curr = coords[i]
            current += diff[prev]
            if current > 0:
                res.append([prev, curr, current])
            prev = curr

        return res
```

> **Tip** – If you prefer O(log n) inserts/lookups, use `sortedcontainers.SortedDict` (pip install).  
> The above code runs in `O(n log n)` due to sorting the endpoint set (`n ≤ 2·10⁴`).

---

### C++ (Fast array + `vector`)

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<long long>> splitPainting(vector<vector<int>>& segments) {
        const int MAX = 100001;            // maximum coordinate + 1
        vector<long long> diff(MAX + 2, 0);
        vector<char> isEnd(MAX + 2, 0);

        for (auto &seg : segments) {
            int s = seg[0], e = seg[1];
            long long c = seg[2];
            diff[s] += c;
            diff[e] -= c;
            isEnd[s] = isEnd[e] = 1;
        }

        vector<vector<long long>> res;
        long long cur = 0;
        int prev = 0;

        for (int i = 1; i <= MAX + 1; ++i) {
            if (isEnd[i] && cur > 0) {
                res.push_back({prev, i, cur});
            }
            prev = isEnd[i] ? i : prev;
            cur += diff[i];
        }
        return res;
    }
};
```

**Why `vector<char>` for `isEnd`?**  
`bool` can have implementation‑defined overhead. A `char` is one byte and is enough to mark whether a coordinate is a real endpoint.

---

## 📚 Detailed Blog – “Describe the Painting” in the Style of a Technical Interview Blog

---

# How to Master LeetCode 1943 – “Describe the Painting”

> **Keywords:** LeetCode 1943, Describe the Painting, line sweep, Java solution, Python solution, C++ solution, algorithm interview, coding interview, job interview, data structures, balanced BST, difference array.

---

### Table of Contents

1.  📝 Problem Statement  
2.  🔍 Key Observations  
3.  🧩 Algorithm – Line Sweep & Difference Map  
4.  📦 Three Clean Implementations  
5.  ⚠️ Common Pitfalls (“Bad” & “Ugly” parts)  
6.  ✅ What to Emphasize in an Interview  
7.  📈 How to Showcase Your Solution in a Portfolio  
8.  💼 Why this problem lands you the job  
9.  📚 Further Reading & Resources  

---

## 1. Problem Statement (Recap)

You’re given an array `segments`, each element `[start, end, color]`.  
All `color` values are unique.  
The task: produce the *minimal* set of non‑overlapping half‑closed segments that correctly describe the mixed colors on the line.  
Output a list of `[left, right, sum_of_colors]` for each painted interval.  
Intervals with no color (`sum == 0`) must be omitted.

Constraints: `1 ≤ n ≤ 20000`, `1 ≤ start < end ≤ 100000`, all colors ≤ `10⁹`.

---

## 2. Key Observations

| Observation | Why it matters |
|--------------|----------------|
| **Endpoint change** | A segment starts or ends only at integer coordinates. |
| **Unique colors** | The set of active colors changes iff the numeric sum changes. |
| **Half‑closed** | `end` is *not* part of the segment, so we can use a difference array without off‑by‑one errors. |
| **No painted “holes”** | Intervals where `currentSum == 0` are ignored. |
| **Sorting required** | We need the coordinates in ascending order to perform a sweep. |

---

## 3. Algorithm – Line Sweep with a Difference Map

1. **Create a delta map**  
   * For every segment `[s, e, c]`: `delta[s] += c`, `delta[e] -= c`.  
   * Store both `s` and `e` in a set `endpoints`.
2. **Sort `endpoints`** – this gives us the sweep order.  
3. **Traverse**  
   * Keep `curSum = 0`, `prev = first_endpoint`.  
   * For each coordinate `x` in sorted order:  
     * `curSum += delta[prev]`.  
     * If `curSum > 0`, output `[prev, x, curSum]`.  
     * `prev = x`.
4. Return the list.

Because the sum is strictly positive for any painted interval, we can skip unpainted ranges and we don’t need to reconstruct the *set* of colors.

---

## 4. Three Clean Implementations

> **Tip** – The core logic is identical; only the choice of data structure differs.

| Language | Preferred Data Structure | Time Complexity | Space Complexity |
|----------|--------------------------|-----------------|------------------|
| Java | Array (`diff`) + Boolean array (`isEndpoint`) | `O(n + maxCoord)` | `O(maxCoord)` |
| Python | `defaultdict` + `set` (sorted later) | `O(n log n)` | `O(n)` |
| C++ | `vector<long long>` + `vector<char>` | `O(n + maxCoord)` | `O(maxCoord)` |

---

### Java – Array based (most performant)

```java
import java.util.*;

public class Solution {
    public List<List<Long>> splitPainting(int[][] segments) {
        final int MAX = 100001;          // 1e5 + 1 is safe
        long[] diff = new long[MAX + 2];
        boolean[] isEnd = new boolean[MAX + 2];

        for (int[] seg : segments) {
            int s = seg[0], e = seg[1];
            long c = seg[2];
            diff[s] += c;
            diff[e] -= c;
            isEnd[s] = true;
            isEnd[e] = true;
        }

        List<List<Long>> ans = new ArrayList<>();
        long cur = 0;
        int prev = 0;

        for (int i = 1; i <= MAX + 1; i++) {
            if (isEnd[i] && cur > 0) {
                ans.add(Arrays.asList((long)prev, (long)i, cur));
            }
            prev = isEnd[i] ? i : prev;
            cur += diff[i];
        }
        return ans;
    }
}
```

---

### Python – Using a sorted map

```python
from collections import defaultdict
from typing import List

class Solution:
    def splitPainting(self, segments: List[List[int]]) -> List[List[int]]:
        diff = defaultdict(int)
        ends = set()

        for s, e, c in segments:
            diff[s] += c
            diff[e] -= c
            ends.add(s)
            ends.add(e)

        coords = sorted(ends)
        res = []
        cur = 0
        prev = coords[0]

        for i in range(1, len(coords)):
            cur += diff[prev]
            if cur > 0:
                res.append([prev, coords[i], cur])
            prev = coords[i]

        return res
```

> **If you need strictly `O(n log n)`** (sorting dominates), this already satisfies the limits.  
> The code is 80 % shorter than the “map‑only” solution and keeps the same logic.

---

### C++ – Array based (highly efficient)

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<long long>> splitPainting(vector<vector<int>>& segments) {
        const int MAX = 100001;
        vector<long long> diff(MAX + 2, 0);
        vector<char> isEnd(MAX + 2, 0);

        for (auto &seg : segments) {
            int s = seg[0], e = seg[1];
            long long c = seg[2];
            diff[s] += c;
            diff[e] -= c;
            isEnd[s] = isEnd[e] = 1;
        }

        vector<vector<long long>> ans;
        long long cur = 0;
        int prev = 0;

        for (int i = 1; i <= MAX + 1; ++i) {
            if (isEnd[i] && cur > 0) {
                ans.push_back({prev, i, cur});
            }
            prev = isEnd[i] ? i : prev;
            cur += diff[i];
        }
        return ans;
    }
};
```

---

## 🧩 “Good”, “Bad”, and “Ugly” – What to Keep & What to Avoid

| Category | Good | Bad | Ugly |
|----------|------|-----|------|
| **Algorithm** | Linear sweep; stable even with large `maxCoord`. | Unsorted iteration of raw coordinates → missing endpoints. | Using a *plain* unordered map and iterating arbitrarily – produces incorrect order. |
| **Data‑structure** | `TreeMap` / `std::map` for sorted keys. | `vector<bool>` for endpoints (works but slightly messy). | Using `ArrayList<Integer>` + `HashMap<Integer, Long>` then sorting *only the delta keys* – you miss coordinates that have `delta == 0`. |
| **Complexity** | `O(n log n)` – fine for LeetCode limits. | `O(n²)` – naive sweep without sorting. | `O(n + maxCoord)` – efficient but may be overkill if coordinates are huge. |
| **Corner‑case handling** | Skip when `currentSum == 0`. | Forget to update `prev` only at endpoints. | Emit zero‑length intervals (`[x, x)`), causing wrong answer. |
| **Readability** | Clear variable names (`currentSum`, `prevCoord`). | Dense one‑liner logic – hard to debug. | Missing type safety (`List.of` vs. `Arrays.asList`). |

> **Bottom line** – the array‑difference approach is the cleanest for interviewers; the map‑based version is easier to read if you’re not comfortable with array indexing.

---

## 🚀 How This Problem Helps You Land Your Dream Job

1. **Demonstrates Core Data‑Structure Knowledge** – Map, Tree, Array.  
2. **Shows Clean Code & Edge‑case Handling** – interviewers love solutions that don’t crash on `end == 0`.  
3. **Exhibits Algorithmic Thinking** – transforming a “painting” problem into a *sweep line* – a classic interview problem pattern.  
4. **Portfolio Ready** – Upload your solution with comments & unit tests to GitHub or LinkedIn.  
5. **Blog Post / Medium Article** – Sharing this article, as above, proves you can communicate complex ideas.  
6. **Leverage on Coding Platforms** – Add the problem to your “Top 50 solved” list on LeetCode, Codeforces, HackerRank.  

---

## 📚 Further Reading

| Resource | Why |
|-----------|-----|
| “Cracking the Coding Interview” – Chapter on *Interval Scheduling* | Classic sweep line problems. |
| “Algorithms on Geometric Data” – Coursera | Advanced sweep line techniques. |
| “GeeksforGeeks – Line Sweep Algorithm” | Multiple examples and visual explanations. |
| “Coding Interview University” (GitHub) | Comprehensive list of interview questions, including 1943. |

---

### Closing Thought

Mastering LeetCode 1943 is a *soft‑skill* test – you’re not just solving a puzzle; you’re proving that you can:

- **Translate a problem to data‑structures**  
- **Handle edge cases elegantly**  
- **Write readable, maintainable code**  
- **Explain your reasoning clearly**  

Put this solution in your portfolio, pair it with a clean blog post, and you’ll shine in any coding interview or on LinkedIn. Good luck! 🚀

--- 

*Happy coding!*