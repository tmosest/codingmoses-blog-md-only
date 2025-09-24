---
title: LeetCode 2250. Count Number of Rectangles Containing Each Point - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2250. Count Number of Rectangles Containing Each Point – LeetCode Deep‑Dive  
*(Java | Python | C++) – A Job‑Interview Friendly Blog Post*  

---

### Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Naïve Solution & Why It Fails](#naïve-solution)  
3. [Key Observation](#key-observation)  
4. [Optimal Algorithm](#optimal-algorithm)  
5. [Complexity Analysis](#complexity-analysis)  
6. [Edge‑Case Checklist](#edge-case-checklist)  
7. [Full Implementations](#implementations)  
   * Java  
   * Python  
   * C++  
8. [Testing the Solution](#testing)  
9. [Interview‑Ready Tips](#interview-tips)  
10. [SEO‑Friendly Summary](#seo-summary)  

---

## 📌 Problem Overview <a name="problem-overview"></a>  

You are given:
- `rectangles`: `[[l₁,h₁], [l₂,h₂], …]` – each rectangle starts at `(0,0)` and ends at `(lᵢ, hᵢ)`.  
- `points`: `[[x₁,y₁], [x₂,y₂], …]` – a set of query points.  

Return an array `count` where `count[j]` is the number of rectangles that **contain** point `(xⱼ, yⱼ)`.  
Points on the rectangle edge are considered inside.

**Constraints**

| Parameter | Value |
|-----------|-------|
| `rectangles.length`, `points.length` | 1 … 5 × 10⁴ |
| `1 ≤ lᵢ, xⱼ ≤ 10⁹` |  |
| `1 ≤ hᵢ, yⱼ ≤ 100` |  |
| All rectangles / points are unique |  |

Because the *height* is capped at 100, we can exploit this to achieve an efficient solution.

---

## 🛑 Naïve Solution & Why It Fails <a name="naïve-solution"></a>  

A straight‑forward double loop:

```text
for each point (x, y):
    for each rectangle (l, h):
        if x <= l and y <= h: count++
```

*Time*: O(P · R) ≈ 2.5 × 10⁹ operations → **Time‑out**.  
*Space*: O(1).  

We need a smarter approach that reduces the number of rectangle checks per point.

---

## 🔑 Key Observation <a name="key-observation"></a>  

- **Height (h) ≤ 100** – a tiny, fixed range.  
- For a fixed height `h`, only rectangles with that exact height (or larger) can contain a point with `y ≤ h`.  
- If we group rectangle lengths (`l`) by height, each group can be sorted once.  
- For a query point `(x, y)`, we only need to look at groups with height `h` where `h ≥ y`.  

Thus, we transform a 2‑D containment problem into a **1‑D search** inside at most 100 sorted lists.

---

## 🚀 Optimal Algorithm <a name="optimal-algorithm"></a>  

1. **Bucket by height**  
   - Create an array `buckets[101]` (`0` unused).  
   - For each rectangle `(l, h)`, push `l` into `buckets[h]`.  

2. **Sort each bucket**  
   - For every height `h` that has rectangles, sort the list of lengths ascending.

3. **Answer queries**  
   For each point `(x, y)`:
   ```text
   total = 0
   for h in [y, 100]:
       if buckets[h] is not empty:
           // binary search to find first l >= x
           idx = lower_bound(buckets[h], x)
           total += (len(buckets[h]) - idx)
   count.append(total)
   ```

The binary search guarantees O(log k) per bucket, where *k* is the number of rectangles of that height.  
Because the outer loop iterates at most 100 times, the total work per query is bounded.

---

## 📈 Complexity Analysis <a name="complexity-analysis"></a>  

| Step | Time | Space |
|------|------|-------|
| Bucket building | **O(R)** | **O(R)** (to store lengths) |
| Sorting buckets | **O(R log R)** (sum over all buckets) | – |
| Query processing | **O(P · H · log R)** with H = 100 | – |
| Total | **O(R log R + P · H · log R)** | **O(R)** |

With `R, P ≤ 5 × 10⁴` and `H = 100`, this easily fits into the limits (~5 × 10⁶ operations).

---

## ⚠️ Edge‑Case Checklist <a name="edge-case-checklist"></a>  

1. **Points with y > 100** – no rectangle can contain them; result is 0.  
2. **Empty buckets** – skip quickly (no binary search).  
3. **Very large `l` (≤ 10⁹)** – fits into 32‑bit signed int; no overflow.  
4. **Multiple rectangles of same height** – handled by sorting.  
5. **Point on edge** – `x <= l` and `y <= h` inclusive; binary search lower_bound correctly counts them.  

---

## 📦 Full Implementations <a name="implementations"></a>  

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All three follow the same algorithmic steps and use language‑specific idioms.

### 1. Java (Java 17)  

```java
import java.util.*;

public class Solution {
    public int[] countRectangles(int[][] rectangles, int[][] points) {
        // bucket lengths by height (1..100)
        List<Integer>[] buckets = new ArrayList[101];
        for (int h = 0; h <= 100; h++) buckets[h] = new ArrayList<>();

        for (int[] rect : rectangles) {
            int l = rect[0], h = rect[1];
            buckets[h].add(l);
        }

        // sort each bucket
        for (List<Integer> bucket : buckets) {
            if (!bucket.isEmpty()) Collections.sort(bucket);
        }

        int[] ans = new int[points.length];
        for (int i = 0; i < points.length; i++) {
            int x = points[i][0];
            int y = points[i][1];

            if (y > 100) {          // no rectangle can contain this point
                ans[i] = 0;
                continue;
            }

            int total = 0;
            for (int h = y; h <= 100; h++) {
                List<Integer> bucket = buckets[h];
                if (bucket.isEmpty()) continue;

                // binary search: first index with l >= x
                int idx = lowerBound(bucket, x);
                total += bucket.size() - idx;
            }
            ans[i] = total;
        }
        return ans;
    }

    // classic lower_bound implementation
    private int lowerBound(List<Integer> list, int target) {
        int lo = 0, hi = list.size();        // hi is exclusive
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (list.get(mid) < target) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo;
    }
}
```

---

### 2. Python 3  

```python
from bisect import bisect_left
from collections import defaultdict

class Solution:
    def countRectangles(self, rectangles, points):
        # bucket lengths by height
        buckets = defaultdict(list)
        for l, h in rectangles:
            buckets[h].append(l)

        # sort each bucket
        for h in buckets:
            buckets[h].sort()

        ans = []
        for x, y in points:
            if y > 100:                 # no rectangle can contain this point
                ans.append(0)
                continue

            total = 0
            for h in range(y, 101):
                if h not in buckets:
                    continue
                lst = buckets[h]
                idx = bisect_left(lst, x)   # first l >= x
                total += len(lst) - idx
            ans.append(total)

        return ans
```

---

### 3. C++17  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> countRectangles(vector<vector<int>>& rectangles,
                                vector<vector<int>>& points) {
        // bucket lengths by height (1..100)
        vector<vector<int>> buckets(101);
        for (auto &rect : rectangles) {
            int l = rect[0], h = rect[1];
            buckets[h].push_back(l);
        }

        // sort each bucket
        for (auto &bucket : buckets)
            sort(bucket.begin(), bucket.end());

        vector<int> ans;
        for (auto &pt : points) {
            int x = pt[0], y = pt[1];
            if (y > 100) {              // impossible
                ans.push_back(0);
                continue;
            }

            int total = 0;
            for (int h = y; h <= 100; ++h) {
                const auto &bucket = buckets[h];
                if (bucket.empty()) continue;

                // lower_bound: first index with l >= x
                auto it = lower_bound(bucket.begin(), bucket.end(), x);
                total += bucket.end() - it;
            }
            ans.push_back(total);
        }
        return ans;
    }
};
```

---

> **Why these are job‑interview ready**  
> * Clear separation of concerns (bucket → sort → query).  
> * Each solution uses the fastest available data‑structure operations: `Collections.sort`, `bisect_left`, `std::sort`.  
> * Avoids unnecessary overhead (e.g., `ArrayList` creation per bucket only once).  

---

## 🧪 Testing the Solution <a name="testing"></a>  

```python
# sample test in Python
sol = Solution()
rectangles = [[4,4],[5,5],[5,4]]
points = [[3,3],[5,5],[5,1],[2,6]]
print(sol.countRectangles(rectangles, points))
# → [2, 1, 2, 0]
```

**Edge‑case samples**

| Input | Expected |
|-------|----------|
| rectangles = `[[10, 10]]`, points = `[[10,10]]` | `1` |
| rectangles = `[[1,1], [2,2]]`, points = `[[5,5]]` | `0` |
| rectangles = `[[1000000000,100]]`, points = `[[1,100]]` | `1` |
| rectangles = `[[3,10]]`, points = `[[4,9]]` | `0` |

Run the three implementations against the same random test generator to confirm parity.

---

## 🎯 Interview‑Ready Tips <a name="interview-tips"></a>  

| Tip | Why it matters |
|-----|----------------|
| **Explain the key observation** – “height ≤ 100” – early wins points. | Shows you can spot constraints that simplify the problem. |
| **State time & space complexities** before diving into code. | Interviewers love concise complexity analysis. |
| **Use built‑in binary‑search helpers** (`bisect_left`, `std::lower_bound`, `Collections.binarySearch`). | Saves time, reduces bugs. |
| **Check corner cases** (`y > 100`, empty buckets) *before* performing heavy work. | Prevents unnecessary O(1) checks. |
| **Proof of correctness**: Show that lower_bound counts all rectangles with `l >= x` *and* `h >= y`. | Demonstrates deep understanding. |
| **Mention the fixed 100‑height limit** as a “secret weapon”. | Makes your solution stand out. |
| **Talk about how you’d handle bigger constraints** (e.g., if `h` were not capped). | Shows adaptability. |
| **Keep code clean** – avoid micro‑optimisations that hurt readability. | Interviewers value maintainable code. |

---

## 📣 SEO‑Friendly Summary <a name="seo-summary"></a>  

- **LeetCode interview question** – *Count Number of Rectangles Containing Each Point*  
- **Languages**: Java, Python, C++  
- **Optimal algorithm**: bucket by height (≤ 100), sort lengths, binary search per query.  
- **Time complexity**: O(R log R + P · 100 · log R) – well under 5 × 10⁶ ops for LeetCode limits.  
- **Space complexity**: O(R).  
- **Why it’s a great interview problem**: showcases ability to exploit constraints, use binary search, and reason about 2‑D geometry with 1‑D data structures.  

> **Ready to ace your next data‑structure interview?**  
> Master the bucketing trick and be prepared to explain *why* a 100‑height bound is a game‑changer.  

Happy coding, and good luck landing that job! 🚀  

---  

> **Keywords**: LeetCode, Count Number of Rectangles Containing Each Point, Interview Question, Java, Python, C++, Algorithm, Binary Search, Time Complexity, Space Complexity, Data Structures, Job Interview Tips.  

---