---
title: LeetCode 436. Find Right Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 436 – **Find Right Interval**  
> **LeetCode** | **Medium** | **Job‑ready interview question**

> <https://leetcode.com/problems/find-right-interval/description/>

---

## Table of Contents  

| Section | What you’ll learn |
|---------|--------------------|
| [Problem recap](#problem-recap) | The exact question and constraints |
| [High‑level strategy](#high-level-strategy) | Why sorting + binary search is the sweet spot |
| [Solution in 3 languages](#solution-in-3-languages) | Java, Python, C++ implementations |
| [Complexity & trade‑offs](#complexity-tradeoffs) | Time, space, why this beats naive O(n²) |
| [The good, the bad, the ugly](#good-the-bad-the-ugly) | Design strengths, pitfalls, hidden traps |
| [Interview tips & job‑boosting takeaways](#interview-tips-job-boosting-takeaways) | How to present the answer in a real interview |
| [References & further reading](#references-further-reading) | Links to LeetCode solutions & community discussion |

> **SEO Keywords**: LeetCode Find Right Interval, Java Find Right Interval, Python Find Right Interval, C++ Find Right Interval, interview algorithm, job interview preparation, software engineer interview, coding challenge, binary search, interval scheduling, algorithm design

---

## Problem Recap

You’re given an array `intervals`, where `intervals[i] = [start_i, end_i]` and all `start_i` are distinct.

For every interval `i`, find the *right interval* – an interval `j` such that  

```
start_j ≥ end_i  AND  start_j is the smallest possible
```

If no such interval exists, return `-1` for that position.  
Return an integer array of the same length as `intervals` containing these indices.

*Examples*  
| Input | Output | Explanation |
|-------|--------|-------------|
| `[[1,2]]` | `[-1]` | Only one interval. |
| `[[3,4],[2,3],[1,2]]` | `[-1,0,1]` | `[2,3]` → `[3,4]`; `[1,2]` → `[2,3]`. |
| `[[1,4],[2,3],[3,4]]` | `[-1,2,-1]` | Only `[2,3]` has a right interval. |

**Constraints**  

- `1 ≤ intervals.length ≤ 2 × 10⁴`
- `-10⁶ ≤ start_i ≤ end_i ≤ 10⁶`
- All `start_i` are unique.

---

## High‑Level Strategy

1. **Map each interval’s start to its original index**  
   Because starts are unique, a hash‑map (`start → index`) lets us look up the original position in O(1).

2. **Sort the starts**  
   By sorting the `start` values we create an ascending list that we can binary‑search over.

3. **For each interval, binary‑search the first start ≥ end_i**  
   The index returned by binary search is the *right interval*’s start in the sorted array; we map it back to the original index via the hash‑map.

4. **If binary search fails** (all starts < end_i), answer is `-1`.

**Why this works**  

- Sorting gives us an ordered set of candidate starts.  
- Binary search is O(log n) for each query, so the whole solution is O(n log n).  
- The hash‑map keeps the mapping from sorted start back to original index in O(1).  

The naive O(n²) approach (compare each pair) is far slower for n = 20 000.  

---

## Implementation in Three Languages

### 1. Java

```java
import java.util.*;

public class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        int[] result = new int[n];

        // 1. map start -> original index
        Map<Integer, Integer> startToIndex = new HashMap<>(n);
        for (int i = 0; i < n; i++) {
            startToIndex.put(intervals[i][0], i);
        }

        // 2. sorted list of starts
        int[] starts = new int[n];
        for (int i = 0; i < n; i++) starts[i] = intervals[i][0];
        Arrays.sort(starts);

        // 3. binary search for each interval
        for (int i = 0; i < n; i++) {
            int end = intervals[i][1];
            int idx = Arrays.binarySearch(starts, end);
            if (idx < 0) idx = -idx - 1;  // insertion point
            if (idx == n) result[i] = -1;
            else result[i] = startToIndex.get(starts[idx]);
        }
        return result;
    }
}
```

> **Key points**  
> - `Arrays.binarySearch` returns `-(insertionPoint)-1` when not found.  
> - The map guarantees O(1) mapping back to original index.

### 2. Python

```python
from typing import List

class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        n = len(intervals)
        # 1. start -> index
        start_to_index = {start: i for i, (start, _) in enumerate(intervals)}

        # 2. sorted starts
        starts = sorted(start for start, _ in intervals)

        # 3. binary search
        import bisect
        res = []
        for _, end in intervals:
            pos = bisect.bisect_left(starts, end)
            if pos == n:
                res.append(-1)
            else:
                res.append(start_to_index[starts[pos]])
        return res
```

> **Python niceties**  
> - `bisect.bisect_left` gives the insertion point directly.  
> - List comprehensions keep code concise.

### 3. C++

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

class Solution {
public:
    std::vector<int> findRightInterval(std::vector<std::vector<int>>& intervals) {
        int n = intervals.size();
        std::vector<int> res(n);
        std::unordered_map<int, int> startIdx;          // start -> original index
        for (int i = 0; i < n; ++i)
            startIdx[intervals[i][0]] = i;

        std::vector<int> starts(n);
        for (int i = 0; i < n; ++i) starts[i] = intervals[i][0];
        std::sort(starts.begin(), starts.end());

        for (int i = 0; i < n; ++i) {
            int end = intervals[i][1];
            auto it = std::lower_bound(starts.begin(), starts.end(), end);
            if (it == starts.end())
                res[i] = -1;
            else
                res[i] = startIdx[*it];
        }
        return res;
    }
};
```

> **C++ style**  
> - `lower_bound` is the idiomatic binary search.  
> - `unordered_map` gives constant‑time index lookup.

---

## Complexity & Trade‑offs

| Metric | Calculation |
|--------|-------------|
| Time | `O(n log n)` – sorting + n binary searches |
| Space | `O(n)` – map + sorted array |
| Worst‑case | The map and sorted array hold n elements each. |

**Why this beats alternatives**

| Approach | Time | Practicality |
|----------|------|--------------|
| Naïve double loop | `O(n²)` | 400 million ops at n=20 000 → ~seconds to minutes |
| HashMap + linear scan | `O(n²)` | Same as above |
| Sorting + two‑pointer sweep | `O(n log n)` (if intervals sorted by end) | Works only if we can guarantee a sweep order; not generic |

Thus the sorted‑starts + binary‑search approach is the canonical, interview‑friendly solution.

---

## The Good, The Bad, and The Ugly

### The Good  
- **Simplicity**: Three lines of code (plus a map).  
- **Deterministic performance**: `O(n log n)` is predictable.  
- **Versatility**: Works for any interval size, negative numbers, or large ranges.

### The Bad  
- **Memory overhead**: Two `O(n)` data structures (map + array).  
- **Sorting cost**: If you already have intervals sorted by start, you can skip sorting; but you still need the map.  

### The Ugly  
- **Edge cases**: When `end_i` equals a start of an interval, you must use `lower_bound`/`bisect_left`, not `>`.  
- **Index mapping pitfalls**: Forgetting to preserve the original indices leads to wrong answers.  
- **Overflow**: In languages with 32‑bit ints, be cautious if the range goes beyond `Integer.MAX_VALUE` (not an issue here, but good to mention).  

---

## Interview Tips & Job‑Boosting Takeaways

| Topic | What to say |
|-------|-------------|
| **Explain the problem clearly** | Restate constraints, show a diagram if possible. |
| **Outline the algorithm** | Mention hash‑map + sorted starts + binary search. |
| **Justify complexity** | “Sorting is O(n log n). Each binary search is O(log n). Overall O(n log n).” |
| **Discuss edge cases** | `end` equal to an existing start, no right interval, negative values. |
| **Show code** | Provide clean, commented snippets (you can share the language of your choice). |
| **Mention alternatives** | Discuss why you chose this approach over naïve O(n²). |
| **Ask clarifying questions** | “Do we care about memory? Are we allowed to modify the input?” |
| **Show readiness for follow‑ups** | “If we needed to support queries after initial preprocessing, we could keep the sorted array and answer in O(log n) per query.” |

> **Why this matters for your job hunt**  
> - Interviewers value *clearly articulated reasoning*.  
> - Demonstrating a balanced trade‑off analysis shows you’re not just coding, you’re architecting.  
> - Mastery of classic data structures (maps, arrays, binary search) is a must‑know for software engineering roles.  

---

## References & Further Reading

- **LeetCode Discussion (Multiple top solutions)**  
  - Java: [Easy‑peasy solution by arunsainarla](https://leetcode.com/problems/find-right-interval/solutions/7048561/easy-peasy-java-code-by-arunsainarla-9gvy/)  
  - Python: [Binary‑search solution by MohitSing](https://leetcode.com/problems/find-right-interval/solutions/5141975/best-binary-search-solution-by-mohitsing-pjte/)  
  - C++: [Two‑heap approach by omars_le](https://leetcode.com/problems/find-right-interval/solutions/1817689/java-two-heaps-with-comments-by-omars_le-3ot3/)  

- **Algorithmic concepts**  
  - *Binary Search* – CLRS, 3rd Ed.  
  - *HashMap vs. TreeMap* – LeetCode Blog on “When to use a HashMap”.  

- **Interview prep resources**  
  - *Cracking the Coding Interview* (7th Edition) – Chapter on “Sorting and Searching”.  
  - *LeetCode Weekly Challenges* – Keep practicing similar interval problems.

---

### Final Thought

Mastering **LeetCode 436 – Find Right Interval** gives you a clean showcase of:

- *Problem comprehension*  
- *Algorithm design*  
- *Efficient coding* in Java, Python, and C++  

Use the code snippets, complexity analysis, and interview checklist above to write a compelling blog post or portfolio entry. Highlight the *good, the bad, and the ugly* to demonstrate a deep, honest understanding of the problem—exactly what hiring managers look for. Good luck landing that interview!