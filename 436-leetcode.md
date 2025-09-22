---
title: LeetCode 436. Find Right Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 436. Find Right Interval – Multi‑Language Solution & Interview‑Ready Blog

Below you’ll find:

1. A clean, production‑ready implementation in **Java, Python, and C++**  
2. A comprehensive, SEO‑optimized blog post that explains the problem, walks through the “good, bad, ugly” parts of common solutions, and gives interview‑friendly take‑aways.  
3. The post is crafted to help you rank for keywords like *“Find Right Interval LeetCode 436”*, *“right interval interview”*, and *“binary search interview questions”*.

> **Note** – All solutions use a *sorted array + binary search* approach, the most common and efficient solution for this problem.

---

## 1. Code

### 1.1 Java – `Solution.java`

```java
import java.util.Arrays;

public class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[][] sorted = new int[n][3];          // [start, index, end]
        for (int i = 0; i < n; i++) {
            sorted[i][0] = intervals[i][0];      // start
            sorted[i][1] = i;                    // original index
            sorted[i][2] = intervals[i][1];      // end
        }

        // Sort by start value (unique per constraints)
        Arrays.sort(sorted, (a, b) -> Integer.compare(a[0], b[0]));

        int[] result = new int[n];
        Arrays.fill(result, -1);

        for (int[] interval : sorted) {
            int end = interval[2];
            int idx  = interval[1];

            int pos = binarySearch(sorted, end);
            result[idx] = (pos < n) ? sorted[pos][1] : -1;
        }
        return result;
    }

    /** Binary search for the smallest start >= target */
    private int binarySearch(int[][] arr, int target) {
        int l = 0, r = arr.length - 1;
        int ans = arr.length;                // default “not found”
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (arr[mid][0] >= target) {
                ans = mid;
                r = mid - 1;                // keep searching left
            } else {
                l = mid + 1;
            }
        }
        return ans;
    }
}
```

> **Complexity**  
> *Time*: `O(n log n)` – sorting + `n` binary searches  
> *Space*: `O(n)` – auxiliary array for sorted intervals

---

### 1.2 Python – `solution.py`

```python
from bisect import bisect_left
from typing import List

class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        # Pair (start, index, end)
        sorted_ints = sorted([(s, i, e) for i, (s, e) in enumerate(intervals)])
        starts = [s for s, _, _ in sorted_ints]
        n = len(intervals)
        res = [-1] * n

        for s, idx, e in sorted_ints:
            pos = bisect_left(starts, e)
            res[idx] = sorted_ints[pos][1] if pos < n else -1

        return res
```

> **Complexity**  
> *Time*: `O(n log n)`  
> *Space*: `O(n)`

---

### 1.3 C++ – `solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findRightInterval(vector<vector<int>>& intervals) {
        int n = intervals.size();
        vector<array<int, 3>> sorted(n);        // {start, originalIndex, end}
        for (int i = 0; i < n; ++i) {
            sorted[i] = {intervals[i][0], i, intervals[i][1]};
        }

        sort(sorted.begin(), sorted.end(),
             [](const auto& a, const auto& b){ return a[0] < b[0]; });

        vector<int> starts(n);
        for (int i = 0; i < n; ++i) starts[i] = sorted[i][0];

        vector<int> res(n, -1);
        for (const auto& cur : sorted) {
            int pos = lower_bound(starts.begin(), starts.end(), cur[2]) - starts.begin();
            res[cur[1]] = (pos < n) ? sorted[pos][1] : -1;
        }
        return res;
    }
};
```

> **Complexity**  
> *Time*: `O(n log n)`  
> *Space*: `O(n)`

---

## 2. Blog Post – “Find Right Interval (LeetCode 436): The Good, The Bad, and The Ugly”

> **SEO Keywords**: *Find Right Interval LeetCode 436*, *right interval interview*, *binary search LeetCode*, *interval problems coding interview*, *job interview algorithm*

---

### 2.1 Introduction

In the world of coding interviews, *interval problems* are a staple. One classic challenge that frequently appears on LeetCode, HackerRank, and even real‑world interview screens is **“Find Right Interval” (LeetCode 436)**. Mastering this problem not only shows your grasp of binary search and sorting but also demonstrates your ability to translate a real‑world requirement into efficient code.

In this article, we’ll:

1. **Explain the problem** in plain English and give intuition behind why binary search is the right tool.
2. **Show the “good” solution** that runs in *O(n log n)* time and *O(n)* space.
3. **Highlight common pitfalls** – the “bad” approaches that people often fall into.
4. **Discuss edge cases and tricky inputs** – the “ugly” part that can trip even seasoned developers.
5. **Provide interview‑ready take‑aways**: how to talk through the solution, what to ask the interviewer, and how to extend the idea.

Let’s dive in.

---

### 2.2 Problem Recap

> **Given** an array of `intervals`, where each `intervals[i] = [start_i, end_i]` and all `start_i` values are unique,  
> **Return** an array `answer` such that `answer[i]` is the index of the interval with the smallest `start_j` that satisfies `start_j >= end_i`.  
> If no such interval exists, `answer[i] = -1`.

**Example**

| intervals | answer |
|-----------|--------|
| `[[3,4],[2,3],[1,2]]` | `[-1,0,1]` |

Here, the right interval for `[2,3]` is `[3,4]` because its start (`3`) is the smallest start ≥ the end of `[2,3]` (`3`).

---

### 2.3 The “Good” Solution – Binary Search + Sorting

#### Why Binary Search?

- We need, for each interval, to find the *next* interval whose start is **just** larger or equal to the current end.  
- If we sort the intervals by their start, this becomes a classic “find the first element ≥ target” problem, which is precisely what binary search solves efficiently (`O(log n)`).

#### Algorithm Steps

1. **Create a list of tuples** `[(start, index, end)]` to keep track of the original positions.  
2. **Sort** this list by `start`.  
3. **Build a separate array `starts`** that contains just the sorted starts.  
4. **For each interval** in the sorted list:
   - Use `bisect_left` (Python) / `lower_bound` (C++) / custom binary search (Java) to find the smallest start ≥ `end`.  
   - If the index is within bounds, map back to the original index; otherwise, set `-1`.  
5. Return the `answer` array.

#### Complexity

- **Time**: `O(n log n)` – `O(n log n)` for sorting + `n` binary searches each `O(log n)`.  
- **Space**: `O(n)` – auxiliary array for sorted tuples and the answer.

#### Code (Python – short version)

```python
from bisect import bisect_left
class Solution:
    def findRightInterval(self, intervals):
        sorted_ints = sorted([(s, i, e) for i,(s,e) in enumerate(intervals)])
        starts = [s for s,_,_ in sorted_ints]
        ans = [-1]*len(intervals)
        for s,i,e in sorted_ints:
            pos = bisect_left(starts, e)
            ans[i] = sorted_ints[pos][1] if pos < len(intervals) else -1
        return ans
```

> **Why this is “good”** – clear logic, minimal extra memory, and leverages built‑in binary search for reliability.

---

### 2.4 The “Bad” Approaches

| Approach | Why It’s Problematic | Typical Complexity |
|----------|---------------------|--------------------|
| **Brute‑Force Double Loop** | For each interval, scan all others to find the right interval. | `O(n²)` – too slow for `n = 20,000`. |
| **HashMap of Starts → Indices + Sorting Only Ends** | Some solutions build a map of `start → index` but forget that multiple intervals may share the same end. | Still `O(n log n)` if you sort ends, but the map lookup can become a bottleneck if not used properly. |
| **Priority Queue of Ends** | Using a heap to pop the smallest end that’s >= current start leads to confusion because the end list isn’t sorted in the same order as the starts. | Hard to maintain correctness; may need additional structures → `O(n log n)` but complex. |

**Takeaway:** Whenever the problem asks for *the next* element in a sorted order, binary search is almost always the most straightforward and efficient approach. Avoid over‑engineering with heaps unless there’s a clear advantage.

---

### 2.5 The “Ugly” Edge Cases

1. **Large Negative/Positive Numbers**  
   - Intervals can have `start` and `end` as low as `-10⁶` or high as `10⁶`.  
   - Using integer types that can overflow (e.g., 32‑bit in some languages) might be risky; use `long` or `int64_t` if your language has a stricter type system.

2. **Intervals That Touch Exactly**  
   - `[1,2]` and `[2,3]` – the second interval’s start equals the first’s end.  
   - Binary search with `>=` handles this correctly, but make sure you don’t mistakenly use `>`.

3. **Singleton List**  
   - `[[1, 2]]` – should return `[-1]`.  
   - The algorithm still works because the binary search will return an index equal to the length of the list, which triggers the `-1` case.

4. **Unordered Input**  
   - Intervals can be given in any order; sorting ensures we’re working with a deterministic sequence.

5. **Duplicate Ends**  
   - Even though starts are unique, multiple intervals can share the same end.  
   - The algorithm still works because binary search only cares about start values.

---

### 2.6 Interview‑Ready Discussion Points

1. **Explain Your Data Structure Choice**  
   > “I’ll sort by start because each interval’s start is unique, making binary search safe.”

2. **Ask Clarifying Questions**  
   - “Are start values guaranteed to be unique?”  
   - “What should we return if an interval’s end is larger than all starts?”

3. **Time/Space Trade‑offs**  
   - Mention that an alternative O(n) approach exists using a balanced BST (Java `TreeMap`) but it’s more code‑heavy.

4. **Show Pseudocode Before Coding**  
   - “We’ll create a list of (start, index, end) tuples, sort it, then for each interval perform a binary search.”

5. **Discuss Edge Cases**  
   - Highlight that we handle cases where no right interval exists by returning `-1`.

6. **Testing**  
   - “I’ll test with intervals that touch, intervals that are far apart, a single interval, and a large random set.”

---

### 2.7 Final Thoughts & Job‑Ready Take‑aways

- **Binary search + sorting** is the canonical solution for “Find Right Interval.”  
- It demonstrates a solid understanding of **algorithmic thinking**, **time‑space trade‑offs**, and **language‑specific utilities** (like `bisect_left` in Python).  
- By explaining your approach clearly, you showcase communication skills—crucial for any software engineering role.  
- Practice explaining this problem to a friend or mentor; you’ll be surprised how quickly you can articulate the solution once you’ve internalized the reasoning.

Good luck, and may your next interview be a clean `O(n log n)` success! 🚀

---

## 3. How to Use This Post

1. **Copy the code** into your IDE and run it against the LeetCode test cases.  
2. **Paste the blog article** into Medium, Dev.to, or your personal tech blog.  
3. **Add meta tags**: `Find Right Interval`, `LeetCode 436`, `Binary Search Interview`, `Job Interview Prep`.  
4. **Share** on LinkedIn with a brief headline: “Cracked LeetCode 436 in 3 Minutes – Here’s How”.  

By providing both the “good” solution and an engaging explanation, you’ll attract recruiters and interviewers alike—turning a single problem into a job‑landing asset. Happy coding!