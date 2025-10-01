---
title: LeetCode 436. Find Right Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 436. Find Right Interval – Multi‑Language Implementation & SEO‑Optimized Blog Post

---

### Table of Contents

| Section | Description |
|---------|-------------|
| 📌 Problem Statement | A concise summary of LeetCode #436 |
| 🔧 Solution Idea | The core algorithm in a nutshell |
| 🛠️ Code – Java | Complete, commented solution |
| 🐍 Code – Python | Complete, commented solution |
| 🚀 Code – C++ | Complete, commented solution |
| 📚 Good, Bad & Ugly | What worked, what didn’t, what you can improve |
| 💼 Interview Takeaways | How to talk about this problem in an interview |
| 🎯 SEO Keywords | Target phrases that help your article rank |

---

## 📌 Problem Statement

> **Find Right Interval**  
> You are given an array of intervals, where `intervals[i] = [start_i, end_i]` and each `start_i` is unique.  
> For every interval `i` find the interval `j` whose start is **≥** `end_i` and whose start is minimal.  
> Return an array `answer` where `answer[i]` is the index of that right interval, or `-1` if none exists.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[[1,2]]` | `[-1]` | Only one interval → no right interval |
| `[[3,4],[2,3],[1,2]]` | `[-1,0,1]` | The interval `[2,3]` (index 1) has `[3,4]` (index 0) as the minimal right interval. |
| `[[1,4],[2,3],[3,4]]` | `[-1,2,-1]` | Only `[2,3]` (index 1) has `[3,4]` (index 2) as its right interval. |

**Constraints**

- `1 ≤ intervals.length ≤ 2 * 10⁴`
- `-10⁶ ≤ start_i ≤ end_i ≤ 10⁶`
- All `start_i` are distinct.

---

## 🔧 Solution Idea

The classic way to solve this problem is to:

1. **Record each interval’s start together with its original index.**  
   Example: `[(start, index), …]`.

2. **Sort the list by start values.**  
   After sorting, a binary search on the sorted starts gives the *smallest start that is ≥ a given end*.

3. **For each original interval**  
   - Binary search the sorted list for `end_i`.  
   - If found, return the stored original index; otherwise return `-1`.

The overall complexity is dominated by the sort: `O(n log n)`.  
The binary searches are `O(log n)` each, for a total of `O(n log n)`.

This approach is succinct, uses only standard library containers, and works for all given constraints.

---

## 🛠️ Code – Java

```java
import java.util.*;

public class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[] result = new int[n];
        // 1. Pair start with original index
        int[][] starts = new int[n][2];          // [start, index]
        for (int i = 0; i < n; i++) {
            starts[i][0] = intervals[i][0];
            starts[i][1] = i;
        }

        // 2. Sort by start
        Arrays.sort(starts, Comparator.comparingInt(a -> a[0]));

        // 3. For each interval, binary search for its right interval
        for (int i = 0; i < n; i++) {
            int end = intervals[i][1];
            int idx = binarySearch(starts, end);
            result[i] = idx;            // idx is -1 if not found
        }
        return result;
    }

    /** Binary search on sorted starts to find minimal start >= target. */
    private int binarySearch(int[][] starts, int target) {
        int left = 0, right = starts.length - 1;
        int answer = -1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            if (starts[mid][0] >= target) {
                answer = starts[mid][1];   // store original index
                right = mid - 1;           // keep searching left part
            } else {
                left = mid + 1;
            }
        }
        return answer;
    }
}
```

*Time Complexity:* `O(n log n)`  
*Space Complexity:* `O(n)` (for the `starts` array)

---

## 🐍 Code – Python

```python
from bisect import bisect_left
from typing import List

class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        n = len(intervals)
        # 1. Build list of (start, index)
        starts = [(intervals[i][0], i) for i in range(n)]
        starts.sort()                      # sort by start

        # 2. Separate starts and indices for bisect
        sorted_starts = [s for s, _ in starts]
        indices = [idx for _, idx in starts]

        ans = [-1] * n
        for i, (s, e) in enumerate(intervals):
            pos = bisect_left(sorted_starts, e)
            if pos < n:                   # found right interval
                ans[i] = indices[pos]
        return ans
```

*Time Complexity:* `O(n log n)`  
*Space Complexity:* `O(n)`

---

## 🚀 Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findRightInterval(vector<vector<int>>& intervals) {
        int n = intervals.size();
        vector<int> ans(n, -1);

        // 1. Store starts with original indices
        vector<pair<int,int>> starts;
        starts.reserve(n);
        for (int i = 0; i < n; ++i)
            starts.emplace_back(intervals[i][0], i);

        // 2. Sort by start value
        sort(starts.begin(), starts.end());

        // 3. For each interval, binary search
        for (int i = 0; i < n; ++i) {
            int end = intervals[i][1];
            auto it = lower_bound(starts.begin(), starts.end(),
                                  make_pair(end, -1),
                                  [](const pair<int,int>& a,
                                     const pair<int,int>& b){
                                      return a.first < b.first;
                                  });
            if (it != starts.end())
                ans[i] = it->second;          // right interval found
        }
        return ans;
    }
};
```

*Time Complexity:* `O(n log n)`  
*Space Complexity:* `O(n)`

---

## 📚 Good, Bad & Ugly

| Aspect | What Worked | What Didn’t Work | Improvements |
|--------|-------------|------------------|--------------|
| **Good** | • Using a sorted list of starts + binary search gives optimal `O(n log n)` performance.<br>• Clear separation of concerns: build data structure → query. | | |
| **Bad** | • Some solutions use an unordered map to store start→index, but then use a priority queue or two heaps; that’s unnecessary complexity for this problem. | | Avoid extra data structures unless required. |
| **Ugly** | • Implementing a custom binary search by hand and missing the edge case `start >= end` can lead to off‑by‑one bugs.<br>• Forgetting that `start` values are unique allows you to avoid ties but you must still handle negative numbers correctly. | • Use library functions (`bisect`, `lower_bound`) to prevent such bugs.<br>• Always test on edge cases: one interval, all overlapping, no overlaps. | |

### Common Pitfalls

1. **Using `> end` instead of `>= end`** – the problem explicitly requires `start >= end`.  
2. **Assuming `start` values are sorted** – they are unique but not sorted in the input.  
3. **Not handling negative numbers** – the range is `-10⁶` to `10⁶`.  
4. **Time‑limit exceed** – sorting `O(n log n)` is fine, but nested loops (`O(n²)`) will fail for `n = 20,000`.

---

## 💼 Interview Takeaways

| Topic | How to discuss it |
|-------|-------------------|
| **Problem restatement** | “Given intervals with unique starts, for each find the smallest starting interval that begins at or after its end.” |
| **Edge cases** | “I’ll check single‑interval, fully overlapping, and disjoint scenarios.” |
| **Algorithm** | “I’ll pair each start with its original index, sort the pairs, and binary‑search for each end value.” |
| **Complexity** | “Sorting takes `O(n log n)`, and each binary search is `O(log n)`, giving overall `O(n log n)`.” |
| **Alternative** | “A hash map plus two‑heap solution exists but is overkill; the sorted list approach is simplest and fastest.” |
| **Code structure** | “I’ll keep the algorithm concise, use built‑in binary search, and document each step.” |

> **Tip:** When explaining your solution, walk through a small example on paper or a whiteboard. This demonstrates clarity and helps interviewers see you’re thinking through edge cases.

---

## 🎯 SEO Keywords

- `find right interval leetcode 436`
- `leetcode 436 solution`
- `right interval algorithm`
- `binary search interview question`
- `interview algorithm examples`
- `c++ python java solutions`
- `time complexity interview`
- `coding interview interview prep`
- `unique start interval problem`

Incorporate these phrases naturally into headings, sub‑headings, and the article body to improve search visibility for recruiters searching for interview problem solutions.

---

### Closing Thoughts

*Find Right Interval* is a classic example of turning a seemingly complicated interval problem into a straightforward sorted‑array binary‑search puzzle. By focusing on the uniqueness of the start values and leveraging built‑in search utilities, you can craft clean, efficient solutions in any language. Use this problem to demonstrate your understanding of sorting, binary search, and careful handling of edge cases—skills that every hiring manager looks for in a strong software engineer. Happy coding!