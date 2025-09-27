---
title: LeetCode 757. Set Intersection Size At Least Two - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem restated**

Given an array of integer intervals `intervals[i] = [li , ri]`  
find the minimum size of a set `S` of integers such that each interval
contains at least **two** numbers from `S`.

--------------------------------------------------------------------

## Why a greedy strategy works

1.  If we process an interval that ends earliest first, we are free to
    pick points that lie near the *end* of that interval.
2.  Points chosen near an interval’s end are the most likely to be
    inside all **later** intervals that start after the current one.
3.  Therefore, when we go from left to right (after sorting by end),
    we can keep only the two **largest** points that have already been
    chosen; any interval that starts before the second largest point
    is already covered by at least two of those points.

--------------------------------------------------------------------

## Algorithm

```
1.  If the input array is empty → return 0.

2.  Sort intervals by
        • increasing end (interval[i][1])
        • if ends are equal, decreasing start (so the longer interval
          comes first).  This guarantees we process the shortest
          possible ending intervals first.

3.  Initialise an array `ans` (or just two variables) that will keep
    the points added to the answer set.  
    Add the two largest numbers of the first interval:
            ans = [interval[0][1] - 1 , interval[0][1]]

4.  For each remaining interval [l, r] in the sorted order
        let last      = ans[-1]          // largest point added
        let secondLast= ans[-2]          // second largest point added

        • If l <= secondLast:      // both points are inside the interval
                nothing to do

        • Else if l <= last:       // exactly one point inside
                add r to ans

        • Else:                    // no point inside
                add r-1 and r to ans
   (The special case l == last is handled by the second branch
   because the first point already satisfies the interval.)

5.  Return the length of `ans`.
```

--------------------------------------------------------------------

## Correctness sketch

We prove by induction that after processing the first *k* sorted
intervals the set `ans` contains the minimum possible number of
points.

*Base* – After the first interval we must pick two points.  
Choosing `{r-1, r}` is optimal: any set that covers the first interval
must contain two integers inside it; placing one at the very end and
the other immediately before the end maximises the chance that the
next intervals will also contain at least one of these points.

*Induction step* – Assume the claim holds for the first *k-1* intervals
and the two largest points are `last` and `secondLast`.  
For the *k*-th interval `[l, r]` we have four cases:

1. **`l > last`** – No point of `ans` lies in the interval, so we must
   insert two new points.  
   Picking `{r-1, r}` is optimal for the same reason as in the base
   case.

2. **`l == last`** – Exactly one point (`last`) is in the interval,
   so one more point is needed. Adding `r` gives two points inside
   `[l, r]`.

3. **`l > secondLast`** – The interval starts after the second largest
   point but before or at the largest point.  
   Only `last` lies inside, therefore a single additional point
   (`r`) is sufficient.

4. **`l <= secondLast`** – Both `last` and `secondLast` lie inside the
   interval, hence it is already satisfied and no new points are
   required.

In each case we add the **minimal** number of new points that still
ensures the current interval has at least two elements from `ans`.  
Thus after processing the *k*-th interval the set remains minimal.

By induction the final set `ans` is of minimum possible size.

--------------------------------------------------------------------

## Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Sorting intervals | `O(n log n)` | `O(n)` (or `O(log n)` for in‑place sort) |
| Single scan | `O(n)` | `O(1)` additional (or `O(n)` if you keep the list of points) |

Total: **`O(n log n)` time, `O(n)` auxiliary space** (the list of
selected points can be omitted, leaving only a few integer variables).

--------------------------------------------------------------------

## Reference implementation (Java)

```java
class Solution {
    public int intersectionSizeTwo(int[][] intervals) {
        int n = intervals.length;
        // 1. sort by end ascending, then by start descending
        Arrays.sort(intervals, (a, b) -> {
            if (a[1] == b[1]) return b[0] - a[0];
            return a[1] - b[1];
        });

        // 2. keep only the last two chosen points
        int last  = intervals[0][1];        // largest
        int secondLast = intervals[0][1] - 1; // second largest
        int count = 2;

        for (int i = 1; i < n; i++) {
            int l = intervals[i][0];
            int r = intervals[i][1];

            if (l <= secondLast) {           // already covered by 2 points
                continue;
            } else if (l <= last) {          // covered by one point
                count++;                     // add one more
                secondLast = last;
                last = r;
            } else {                         // no point inside
                count += 2;                  // add two new points
                secondLast = r - 1;
                last = r;
            }
        }
        return count;
    }
}
```

The same logic works in any language; the key ideas are:

* sort by increasing right end,
* start with the two largest numbers of the first interval,
* for each next interval, compare its left bound with the two largest
  numbers already chosen and add 0, 1, or 2 new points accordingly.

This gives the minimal size of a set that has at least two elements
in common with every interval.