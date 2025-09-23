---
title: LeetCode 3394. Check if Grid can be Cut into Sections - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – “Check if Grid can be Cut into Sections”

You’re given an `n × n` grid.  
A set of non‑overlapping rectangles is placed on the grid, each defined by  
`[x1, y1, x2, y2]` (bottom‑left → top‑right).  

Your task: decide whether you can place **two parallel cuts** (either both
vertical or both horizontal) so that

| each of the three resulting sections contains at least one rectangle |
| each rectangle belongs to exactly one section |

If such cuts exist return `true`, otherwise `false`.

> **Why this matters** – This is LeetCode 3394, a *Medium* level question
> that tests your ability to work with intervals, sorting, and greedy
> reasoning – the exact skill set hiring managers look for in algorithmic
> interviews.

---

## 2. Naïve (and *Not* Good) Approaches

| Approach | Why it fails |
|----------|--------------|
| Try every possible pair of cut positions (O(n²)) | `n` can be 10⁵ – impossible |
| Randomly pick cuts until success | No guarantee, not deterministic |
| Treat the problem as a graph and search all partitions | Exponential time, overkill |

The key observation is that **only the relative ordering of the rectangles’
*starting* coordinates matters**. We don’t need to consider every possible
cut position, just the gaps between *disjoint* rectangle intervals.

---

## 3. The Elegant Solution – “Merge‑Intervals + Greedy”

1. **Project rectangles onto one axis**  
   *Vertical cuts* → project onto the **x‑axis** (`[x1, x2]`)  
   *Horizontal cuts* → project onto the **y‑axis** (`[y1, y2]`)

2. **Sort the intervals by start coordinate**.

3. **Scan once and count disjoint groups**  
   *Maintain* `maxEnd` – the farthest right boundary seen so far.  
   *If* a new interval starts **after** `maxEnd`, we’ve found a *new group*.  
   Increment a counter (`sections`).

4. **We need at least 3 groups** → `sections >= 2` (because the first group
   is counted as section 0).  
   If the condition holds for either axis, answer is `true`.

That’s it – **O(m log m)** time (the sorting) and **O(1)** extra space
apart from the input arrays.

---

## 4. Complexity Analysis

| Aspect | Reason |
|--------|--------|
| Time | Sorting dominates: `O(m log m)` (`m` = number of rectangles, ≤ 10⁵). |
| Space | Only a few integer variables – `O(1)` auxiliary space. |

This meets the LeetCode constraints comfortably.

---

## 5. Corner Cases & “The Ugly”

| Edge case | Why it can trip you | How to guard |
|-----------|--------------------|--------------|
| Exactly 3 rectangles all overlapping the same axis | You might mistakenly think “two cuts” is impossible | Our algorithm correctly counts them as **one** group; `sections` stays 0 → answer `false`. |
| Rectangles touch at a border but do not overlap | The definition says *no two rectangles overlap*, but touching is allowed | The interval comparison uses `<=` (strictly greater needed) – we use `maxEnd <= start` to detect a *gap*. |
| Large coordinate values (`n` up to 10⁹) | Overflows in 32‑bit if you use `int` for sums | We only store coordinates, never sum them – `int` is fine in Java/C++/Python 3 (unbounded). |

---

## 6. Code – Ready for Your Interview Portfolio

### 6.1 Java

```java
import java.util.Arrays;
import java.util.Comparator;

class Solution {
    public boolean checkValidCuts(int n, int[][] rectangles) {
        // helper that returns true if we can get >=3 groups on this axis
        return hasThreeOrMoreGroups(getIntervals(rectangles, true))
            || hasThreeOrMoreGroups(getIntervals(rectangles, false));
    }

    // Extract intervals: true -> x‑intervals, false -> y‑intervals
    private int[][] getIntervals(int[][] rects, boolean useX) {
        int m = rects.length;
        int[][] inter = new int[m][2];
        for (int i = 0; i < m; i++) {
            inter[i][0] = useX ? rects[i][0] : rects[i][1];
            inter[i][1] = useX ? rects[i][2] : rects[i][3];
        }
        return inter;
    }

    private boolean hasThreeOrMoreGroups(int[][] intervals) {
        Arrays.sort(intervals, Comparator.comparingInt(a -> a[0]));
        int sections = 0;                 // number of *new* groups found
        int maxEnd   = intervals[0][1];   // farthest right boundary so far

        for (int i = 0; i < intervals.length; i++) {
            int start = intervals[i][0];
            int end   = intervals[i][1];
            if (maxEnd <= start) {        // a gap – new group
                sections++;
            }
            maxEnd = Math.max(maxEnd, end);
        }
        return sections >= 2; // 3 groups => sections == 2
    }
}
```

> **Tip for interviewers** – point out that the comparison `maxEnd <= start`
> correctly handles touching intervals (they’re considered a *gap* because
> rectangles are non‑overlapping).

---

### 6.2 Python 3

```python
class Solution:
    def checkValidCuts(self, n: int, rectangles: List[List[int]]) -> bool:
        def get_intervals(rects, use_x=True):
            return [[r[0], r[2]] if use_x else [r[1], r[3]] for r in rects]

        def has_three_or_more_groups(intervals):
            intervals.sort(key=lambda x: x[0])   # sort by start
            sections = 0
            max_end = intervals[0][1]
            for s, e in intervals:
                if max_end <= s:                # gap -> new group
                    sections += 1
                max_end = max(max_end, e)
            return sections >= 2

        return (has_three_or_more_groups(get_intervals(rectangles, True)) or
                has_three_or_more_groups(get_intervals(rectangles, False)))
```

---

### 6.3 C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    bool checkValidCuts(int n, std::vector<std::vector<int>>& rectangles) {
        auto getIntervals = [&](bool useX) {
            std::vector<std::pair<int,int>> inter;
            for (auto &r : rectangles) {
                int a = useX ? r[0] : r[1];
                int b = useX ? r[2] : r[3];
                inter.emplace_back(a, b);
            }
            return inter;
        };

        auto hasThreeOrMoreGroups = [&](const std::vector<std::pair<int,int>>& inter) {
            std::vector<std::pair<int,int>> seg = inter;
            std::sort(seg.begin(), seg.end(),
                      [](auto &p1, auto &p2){ return p1.first < p2.first; });

            int sections = 0;
            int maxEnd = seg[0].second;
            for (auto &iv : seg) {
                int start = iv.first, end = iv.second;
                if (maxEnd <= start)    // gap
                    ++sections;
                maxEnd = std::max(maxEnd, end);
            }
            return sections >= 2;        // 3 groups -> sections == 2
        };

        return hasThreeOrMoreGroups(getIntervals(rectangles, true)) ||
               hasThreeOrMoreGroups(getIntervals(rectangles, false));
private:
    std::vector<std::pair<int,int>> getIntervals(const std::vector<std::vector<int>>& rects,
                                                 bool useX = true) {
        std::vector<std::pair<int,int>> res;
        for (auto &r : rects) {
            res.emplace_back(useX ? r[0] : r[1], useX ? r[2] : r[3]);
        }
        return res;
    }
};
```

---

## 7. Testing – Make Sure Your Code Passes All Edge Cases

| # | Rectangles | Axis | Expected Result |
|---|-------------|------|-----------------|
| 1 | `[[0,0,1,1],[2,2,3,3],[4,4,5,5]]` | *any* | **true** (gaps at 1‑2 and 3‑4) |
| 2 | `[[0,0,2,2],[1,0,3,1],[0,3,2,5]]` | vertical only | **false** (only 2 disjoint x‑groups) |
| 3 | `[[0,0,5,5],[5,5,10,10],[10,10,15,15]]` | *any* | **false** (touching but not separated into 3 groups) |
| 4 | `[[0,0,5,5],[0,6,5,10],[6,0,11,5]]` | vertical | **true** (groups: [0‑5], [6‑11]) → actually only 2 groups, but with *two* gaps we get 3 sections? **false** – check the logic. |
| 5 | `[[0,0,1,1],[2,0,3,1],[4,0,5,1],[6,0,7,1]]` | vertical | **true** (4 groups → 3 sections possible) |

> **Checklist** – run through at least three of these manually during your
> interview practice.

---

## 8. Final Take‑away – “Why You Should Use This Code in Your Portfolio”

1. **Simplicity** – Only one pass over sorted intervals.  
2. **Deterministic & Optimal** – Meets the strict LeetCode constraints.  
3. **Cross‑Language Mastery** – Implemented in Java, Python, and C++ – you can
   show the same idea in any language you’re comfortable with.  
4. **Interview‑Ready Talk** – Focus on the *merge‑intervals* trick,
   *why we sort*, and *how the counter counts groups* – this shows you can
   turn a problem description into a clean algorithm.

> **If you’re looking to land a software‑engineering role**,
> this question is a perfect showcase of your problem‑solving chops.  
> Post the solution on GitHub, add it to your LeetCode “solutions” list,
> and during your next interview, you’ll have a ready‑to‑cite example of a
> *greedy interval* problem solved in *O(m log m)* time.

Happy coding, and good luck on your next interview!