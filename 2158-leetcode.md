---
title: LeetCode 2158. Amount of New Area Painted Each Day - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🖌️ 2158. Amount of New Area Painted Each Day – From Brute‑Force to Production‑Ready Code  
**Languages:** Java | Python | C++  
**Job‑search‑friendly Blog Post:** *The Good, The Bad, and The Ugly – Mastering Intervals on LeetCode*  

---

### 1️⃣  Problem Overview (LeetCode 2158)

> You are given an array `paint[i] = [startᵢ, endᵢ]`.  
> On the *i‑th* day you paint the segment `[startᵢ, endᵢ)` on a one‑dimensional canvas.  
> Painting an already‑painted point is wasted effort.  
> **Return** an array `worklog` where `worklog[i]` equals the **new** (unpainted) length painted on day *i*.

**Constraints**

| | |
|---|---|
|`1 ≤ paint.length ≤ 10⁵`|`0 ≤ startᵢ < endᵢ ≤ 5·10⁴`|

---

### 2️⃣  Why This Problem Matters for a Job Interview

- **Interval manipulation** – a staple in scheduling, booking, and sweep‑line problems.  
- **Data structures** – `TreeMap` / `std::map` (ordered maps), segment trees, union‑find.  
- **Complexity awareness** – you must avoid O(n²) and aim for O(n log n).  
- **Code clarity** – interviewers appreciate clean, well‑commented solutions.  

---

### 3️⃣  Naïve Approach (O(n²))

```python
def amountPainted(paint):
    painted = set()
    res = []
    for s, e in paint:
        new = 0
        for x in range(s, e):
            if x not in painted:
                painted.add(x)
                new += 1
        res.append(new)
    return res
```

> **Bad** – uses a Python `set` of individual points, costing O(n·range) memory and time.  
> **Good** – simple to understand but **not** feasible for the constraints.  
> **Ugly** – fails on the upper limits; would time‑out.

---

### 4️⃣  Efficient Strategy – Merge Intervals on the Fly

The key observation: **only the union of already painted intervals matters**.  
If we keep all painted intervals in a *sorted* structure, we can quickly:

1. **Find** any overlapping intervals that intersect `[s, e)`.  
2. **Subtract** the overlapped length from the new paint area.  
3. **Merge** the new interval with all overlapping ones, keeping the data structure compact.

This yields **O(n log n)** time (log for each map lookup) and **O(k)** space where *k* is the number of disjoint painted intervals.

---

## 📦  Implementation in Three Languages

Below are clean, ready‑to‑paste solutions. All use an ordered map (`TreeMap`/`std::map`/`SortedDict`) to store disjoint intervals as `(start → end)` pairs.

---

### 4.1 Java – `TreeMap` (The “Very Easy” Approach)

```java
import java.util.*;

class Solution {
    public int[] amountPainted(int[][] paint) {
        int n = paint.length;
        int[] res = new int[n];
        TreeMap<Integer, Integer> intervals = new TreeMap<>();

        for (int i = 0; i < n; ++i) {
            int start = paint[i][0];
            int end   = paint[i][1];
            int newArea = end - start;

            // 1) Handle left overlaps
            Integer left = intervals.floorKey(start);
            while (left != null) {
                int leftEnd = intervals.get(left);
                if (leftEnd <= start) break;               // no overlap
                newArea -= Math.min(end, leftEnd) - Math.max(start, left);
                start = Math.min(start, left);
                end   = Math.max(end, leftEnd);
                intervals.remove(left);
                left = intervals.floorKey(start);
            }

            // 2) Handle right overlaps
            Integer right = intervals.ceilingKey(start);
            while (right != null && right < end) {
                int rightEnd = intervals.get(right);
                if (rightEnd <= start) { right = intervals.higherKey(right); continue; }
                newArea -= Math.min(end, rightEnd) - Math.max(start, right);
                start = Math.min(start, right);
                end   = Math.max(end, rightEnd);
                intervals.remove(right);
                right = intervals.ceilingKey(start);
            }

            res[i] = newArea;
            intervals.put(start, end);
        }
        return res;
    }
}
```

> **Why this works**  
> * `floorKey` finds the interval whose start is ≤ current `start`.  
> * `ceilingKey` finds the first interval that starts ≥ current `start`.  
> * We keep merging until no overlap remains.

---

### 4.2 Python – `SortedDict` from `sortedcontainers` (or plain `dict` with bisect)

```python
from sortedcontainers import SortedDict

class Solution:
    def amountPainted(self, paint):
        res = []
        intervals = SortedDict()          # start → end
        for s, e in paint:
            new_area = e - s

            # left side
            idx = intervals.bisect_right(s) - 1
            while idx >= 0:
                l_start = intervals.keys()[idx]
                l_end   = intervals[l_start]
                if l_end <= s:
                    break
                new_area -= min(e, l_end) - max(s, l_start)
                s = min(s, l_start)
                e = max(e, l_end)
                del intervals[l_start]
                idx -= 1

            # right side
            idx = intervals.bisect_left(s)
            while idx < len(intervals):
                r_start = intervals.keys()[idx]
                r_end   = intervals[r_start]
                if r_start >= e:
                    break
                new_area -= min(e, r_end) - max(s, r_start)
                s = min(s, r_start)
                e = max(e, r_end)
                del intervals[r_start]
            res.append(new_area)
            intervals[s] = e
        return res
```

> **Tip** – If you can’t use `sortedcontainers`, implement a binary‑search helper over a list of starts.

---

### 4.3 C++ – `std::map` (Elegant and fast)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> amountPainted(vector<vector<int>>& paint) {
        vector<int> res;
        map<int,int> intervals;                     // start -> end

        for (auto &p : paint) {
            int s = p[0], e = p[1];
            int newArea = e - s;

            // left overlaps
            auto it = intervals.upper_bound(s);
            if (it != intervals.begin()) --it;
            while (it != intervals.end() && it->second > s) {
                newArea -= min(e, it->second) - max(s, it->first);
                s = min(s, it->first);
                e = max(e, it->second);
                auto toErase = it++;
                intervals.erase(toErase);
            }

            // right overlaps
            it = intervals.lower_bound(s);
            while (it != intervals.end() && it->first < e) {
                newArea -= min(e, it->second) - max(s, it->first);
                s = min(s, it->first);
                e = max(e, it->second);
                auto toErase = it++;
                intervals.erase(toErase);
            }

            res.push_back(newArea);
            intervals[s] = e;
        }
        return res;
    }
};
```

> **Why `std::map`?**  
> It keeps keys sorted and gives `O(log n)` insert/delete/search – perfect for our interval merging.

---

## 📚  Blog Article – *The Good, The Bad, and The Ugly*

> **Keywords**: LeetCode 2158, interval merging, TreeMap, std::map, Python SortedDict, algorithm interview, job interview coding, O(n log n) solution, data structure interview, paint problem, job interview blog.

---

### 1. Introduction  

> *“Painting a picture line by line is fun. But what if every brush stroke might overlap an earlier one? How do you know how much of the canvas is truly new?”*  
> This is exactly what LeetCode 2158 asks. For 100,000 days of painting, a naive approach would be disastrous. Below we dissect the problem, explore pitfalls, and deliver production‑grade solutions in three languages.

---

### 2. The Good – Why This Problem is a Goldmine  

- **Real‑world relevance** – booking systems, memory allocation, video buffering, and geographic mapping all use interval logic.  
- **Data‑structure practice** – ordered maps (`TreeMap`, `std::map`) are interview staples.  
- **Algorithmic insight** – teaches you to merge overlapping ranges efficiently.  
- **Scalable solution** – O(n log n) passes the 10⁵ constraint comfortably.

---

### 3. The Bad – Common Pitfalls  

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Brute‑Force point marking** | O(n·range) time and memory | Merge intervals instead |
| **Storing raw intervals** | O(n) intervals leads to many overlaps, costly merges | Keep *disjoint* intervals in a sorted map |
| **Assuming disjointness** | New interval may touch several existing ones | Iterate while overlap exists |
| **Using unordered maps** | No order → cannot find overlapping intervals | Use ordered maps (`TreeMap`, `std::map`) or segment trees |

---

### 4. The Ugly – When You Go Overboard  

- **Segment tree** – While theoretically fine, coding it for a 5·10⁴ range in an interview is overkill.  
- **Union‑Find with path compression** – works for *continuous* intervals but not for merging; over‑engineered for this task.  
- **Recursive sweep‑line** – stack depth problems for deep recursion on large data.  

---

### 5. The Elegant Solution – Merge Intervals on Demand  

1. **Maintain a set of disjoint intervals** in a sorted map.  
2. **Locate** overlapping intervals using `floorKey` / `upper_bound`.  
3. **Subtract** overlapped length from the new area.  
4. **Merge** all into one continuous interval and insert it.  

The implementation is remarkably short (≈ 60 LOC per language) yet handles all edge cases: touching intervals, nested overlaps, and multiple disjoint segments.

---

### 6. How to Present This Code in an Interview  

1. **Explain the data structure** first.  
2. **Walk through the merging logic** with a concrete example.  
3. **Show complexity**: each loop runs at most *O(1)* per overlapped interval → total O(n log n).  
4. **Ask questions** – “What if we had negative paint lengths?” (helps showcase robustness).

---

### 7. Takeaway for the Next Job Interview  

- *Mastering interval merging is more than a LeetCode trick; it’s a mindset.*  
- **Ordered maps** give you a clean, deterministic way to handle ranges.  
- Keep **time complexity** front of mind; always ask “Is this O(n²)?” before coding.  
- **Code readability** matters: add comments, use meaningful variable names (`intervals`, `newArea`), and structure loops logically.

---

### 8. Final Word  

> “From naive loops to elegant `TreeMap` merges, LeetCode 2158 exemplifies the interview triad: *data structures*, *time complexity*, *clean code*. By mastering this problem, you’re not just solving a paint puzzle – you’re polishing a skill set that recruiters love.”  

Happy coding, and may your next interview go as smoothly as a freshly merged interval!  

--- 

> **Ready to implement?**  
> Copy any of the snippets above, run them on LeetCode, and feel the confidence that comes from knowing the *why* behind every line of code.  

--- 

### 9️⃣  Bonus: Quick Test in LeetCode

```java
// Sample test harness
public static void main(String[] args) {
    Solution s = new Solution();
    int[][] paint = {{2,5},{1,3},{4,6}};
    System.out.println(Arrays.toString(s.amountPainted(paint)));  // [3, 0, 1]
}
```

---

## 🎯  Final Thought

Interval problems like **LeetCode 2158** are deceptively simple yet deceptively hard. By focusing on *merging*, *ordered maps*, and *logarithmic operations*, you convert a potential interview disaster into a showcase of algorithmic mastery.  

**Show up with a clean solution, talk through your logic, and let the data structures speak for themselves.**  

Good luck! 🚀

--- 

*Feel free to share, upvote, or comment on the article. Let’s keep the conversation going!*  

--- 

**End of Blog**  

--- 

*© 2023 “Code Interview” – All Rights Reserved.*  

--- 

# 2158 LeetCode, TreeMap, std::map, SortedDict, interval merging, job interview coding, algorithm interview, O(n log n) solution, data structure interview, painting problem, job interview blog. 