---
title: LeetCode 757. Set Intersection Size At Least Two - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Intersection Size At Least Two**  
*LeetCode 1540*

---

### Problem restatement  

Given an array `intervals` of `N` integer intervals `[start, end]` (inclusive), we need to find the smallest size of a set **S** of integers such that

```
∣ S ∩ [start, end] ∣  ≥  2   for every interval
```

In other words, each interval must contain at least two numbers from **S**.



--------------------------------------------------------------------

## 1.  Intuition & Greedy idea

* If we process the intervals **by their finishing time** (smallest `end` first), the “future” intervals will start later or at the same time.
* When we encounter a new interval, the numbers we already placed that are **right‑most** are the best candidates to cover it.  
  *If an interval can be satisfied by two numbers that we have already chosen, we never need to add more.*

So we only have to keep track of the two largest numbers that are currently in the set.  
All other numbers are irrelevant because the intervals are processed in increasing order of their end points.

--------------------------------------------------------------------

## 2.  Algorithm

```
sort intervals by end asc, then start asc
take the first interval:
        add (end-1) and end to the answer set

for each remaining interval [L, R]:
        last  = largest number already chosen
        second = second largest number already chosen

        if L <= second and L <= last:         # both points cover it
                continue
        else if L <= last:                    # exactly one point covers it
                add R
        else:                                 # no point covers it
                add (R-1) and R
```

The answer is the number of added points.

--------------------------------------------------------------------

## 3.  Correctness Proof  

We prove that the algorithm returns the minimum possible size of **S**.

### Lemma 1  
When the intervals are processed in increasing order of their end points, the two largest numbers chosen so far are the **only** numbers that can possibly cover the current interval.

**Proof.**  
All previously chosen numbers are ≤ the current interval’s `end` (because we always add points from the end of processed intervals).  
Since the current interval has the smallest `end` among all unprocessed intervals, any earlier chosen number larger than the current interval’s `end` cannot exist.  
Thus only the two numbers that were most recently added are ≥ the current interval’s start. ∎



### Lemma 2  
If the current interval overlaps at least two of the chosen numbers, the interval is already satisfied and no further numbers are needed.

**Proof.**  
Because of Lemma&nbsp;1, the two largest chosen numbers are the only candidates.  
If both of them are inside the interval, the interval already contains at least two points of **S**. ∎



### Lemma 3  
If the current interval overlaps exactly one of the chosen numbers (the largest one), adding `R` (the current interval’s end) is optimal.

**Proof.**  
The existing largest number already covers one required point of the interval.  
To cover a second point, we must add a number that is ≤ `R` (the interval’s end) and ≥ the start.  
Choosing `R` is greedy but optimal because it maximizes the chance that it will also cover future intervals (which cannot start before `R` due to sorting). ∎



### Lemma 4  
If the current interval overlaps none of the chosen numbers, adding `R-1` and `R` is optimal.

**Proof.**  
No chosen number lies inside the interval, so two new numbers are mandatory.  
Choosing the two rightmost integers `R-1` and `R` is the best possible greedy choice:  
*They are the largest numbers inside the interval, hence they will be useful for the maximum number of following intervals.* ∎



### Theorem  
The algorithm outputs the minimum possible size of a set **S** that satisfies all intervals.

**Proof.**  
We show by induction over the sorted intervals that after processing each interval the set of chosen numbers is minimal for all processed intervals.

*Base:*  
For the first interval we add `end-1` and `end`. Any set that satisfies this interval must contain at least two numbers inside it, so the set is minimal.

*Induction step:*  
Assume after processing the first `k-1` intervals we have a minimal set.  
For interval `k` we consider the three cases described above:

1. **Overlap ≥ 2** – by Lemma&nbsp;2 the interval is already satisfied; the current set remains minimal.
2. **Overlap = 1** – by Lemma&nbsp;3 adding `R` gives the smallest possible set that satisfies the interval (exactly one new number is necessary).
3. **Overlap = 0** – by Lemma&nbsp;4 adding `R-1` and `R` is necessary and optimal (two new numbers are unavoidable).

Thus after processing interval `k` we again have a minimal set for the first `k` intervals.  
By induction, after all intervals are processed the set is minimal for all of them. ∎



--------------------------------------------------------------------

## 4.  Complexity Analysis

| Step | Complexity |
|------|------------|
| Sorting the intervals | `O(N log N)` |
| One pass over intervals (constant work per interval) | `O(N)` |
| **Total time** | `O(N log N)` |
| **Auxiliary space** | `O(N)` for the resulting list of points (≤ `2N`) |
| **If only the size is needed** | `O(1)` additional space (just keep the two largest numbers) |

--------------------------------------------------------------------

## 5.  Reference Implementation (Java)

```java
import java.util.*;

class Solution {
    public int intersectionSizeTwo(int[][] intervals) {
        // Sort by end ascending; if tie, by start ascending.
        Arrays.sort(intervals, (a, b) -> {
            if (a[1] == b[1]) return a[0] - b[0];
            return a[1] - b[1];
        });

        // List that holds the chosen numbers. Only the last two elements matter,
        // but keeping the list makes the logic clear.
        List<Integer> chosen = new ArrayList<>();

        // First interval: we must pick two numbers, greedily pick end-1 and end.
        chosen.add(intervals[0][1] - 1);
        chosen.add(intervals[0][1]);

        // Process remaining intervals
        for (int i = 1; i < intervals.length; i++) {
            int L = intervals[i][0];
            int R = intervals[i][1];

            int last = chosen.get(chosen.size() - 1);           // largest
            int secondLast = chosen.get(chosen.size() - 2);    // second largest

            if (L <= secondLast) {
                // both last two numbers are inside the interval -> nothing to do
                continue;
            } else if (L <= last) {
                // only the largest number is inside -> add one more point
                chosen.add(R);
            } else {
                // no number inside -> add two new points
                chosen.add(R - 1);
                chosen.add(R);
            }
        }

        return chosen.size();   // minimal size of the intersection set
    }
}
```

The same logic can be written in any language (Python, C++, etc.) with equivalent steps.  

**Result:** `intersectionSizeTwo` returns the minimum size of a set `S` such that every interval contains at least two numbers from `S`.