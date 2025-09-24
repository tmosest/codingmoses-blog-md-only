---
title: LeetCode 3439. Reschedule Meetings for Maximum Free Time I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every test case we are given

* `n`   – number of meetings  
* `k`   – the manager has to delete `k` meetings  
* `m`   – the day has length `m` (time is `0 … m`)  
* `n`  intervals `[l i , r i ]` –  `0 ≤ l i < r i ≤ m` –  
  a meeting occupies the half–open segment `[l i , r i )`.

The whole day is free except for the intervals that stay.  
The manager may delete any `k` meetings.  
After the deletion the remaining meetings can be moved (their order may
be changed) as long as they still do not overlap and they stay inside
`[0 , m)`.  
We have to find the maximum possible length of a continuous free
interval in the resulting schedule.



--------------------------------------------------------------------

#### 1.   Observations

```
If all remaining meetings are merged into one continuous block,
the free time is the length of the empty part that is outside this block.
```

Because the meetings may be reordered,
the optimal schedule for the remaining meetings is always to put
them together in one block (the “tightest” arrangement).  
All other arrangements can only enlarge the total occupied part and
therefore never increase the free interval.

For a fixed set `S` of meetings  
(let `S` contain `t` meetings after sorting by start time)

```
   block   =  [   min l in S ,   max r in S   ]
   length  =  max r – min l            (occupied part)

   the free part outside this block is
          (   m – length   )   –   (   length of the gap between the
                                     block and the nearest meeting outside
                                     this block   )
```

When we delete `k` meetings, all remaining meetings form a contiguous
segment in the sorted list – otherwise we could delete some meeting
inside this segment and enlarge the free part.  
Therefore the remaining meetings are exactly a sliding window of size
`n‑k` on the sorted list.



--------------------------------------------------------------------

#### 2.   Precomputation

```
intervals   –  array of pairs (l , r) sorted by l
leftGap[i]  –  l[i] – r[i‑1]   (gap between i‑1 and i)
rightGap[i] –  l[i+1] – r[i]   (gap between i and i+1)
```

`leftGap` and `rightGap` are `0` when the corresponding index does not
exist.  
From them we build two helper arrays

```
prefMax[i]   – maximum of leftGap[0 … i]      (best free part on the left)
suffMax[i]   – maximum of rightGap[i … n-1]  (best free part on the right)
```

All of them are built in `O(n)` time.



--------------------------------------------------------------------

#### 3.   Sliding window

For a window that starts at position `p` and contains `n‑k` meetings
(`p … p + n‑k – 1`) we know

```
   blockLength  =  r[p + n-k – 1] – l[p]          (occupied part)
   leftFree     =  prefMax[p‑1]  (gap to the left of the window)
   rightFree    =  suffMax[p + n-k] (gap to the right of the window)
```

The maximum free time achievable with this window is

```
   best = max( leftFree , rightFree )
```

We slide the window through all `n‑k+1` positions and keep the maximum
value – this is the answer for the test case.



--------------------------------------------------------------------

#### 4.   Correctness Proof  

We prove that the algorithm prints the required maximum free time.

---

##### Lemma 1  
After deleting `k` meetings the remaining meetings are a contiguous
segment in the sorted order of all meetings.

**Proof.**

Assume the contrary – there exists a meeting `A` outside the remaining
segment that lies strictly between two remaining meetings `B` and `C`
in the sorted order.
Delete `B` instead of `A`.  
The interval of `B` is smaller or equal to the interval of `A` in
terms of start time,
hence the gap between `B` and `C` is not smaller than the gap between
`A` and `C`.  
Consequently the free time obtainable by this new set of remaining
meetings is at least as large as the original one – contradiction. ∎



##### Lemma 2  
For a fixed set of remaining meetings the optimal schedule is to
merge all of them into one continuous block.

**Proof.**

Take any schedule of the remaining meetings that is not a single block.
Move the meetings to the left until a meeting touches its left
neighbour.  The occupied part does not grow, hence the free part does
not shrink.  
Repeating this step for all meetings produces exactly one block,
therefore a schedule with at least as large a free interval. ∎



##### Lemma 3  
Let the remaining meetings form a window `W` of length `t = n‑k`.  
Let `lmin` and `rmax` be respectively the smallest start and the
largest finish among all meetings in `W`.  
Let `gapL` be the largest gap to the left of `W`
(`prefMax[p‑1]` in the algorithm) and `gapR` the largest gap to the
right of `W` (`suffMax[p+t]`).
Then the maximal free interval obtainable with this window equals
`max(gapL , gapR)`.

**Proof.**

The block that contains all meetings in `W` occupies the segment
`[lmin , rmax)` of length `len = rmax – lmin`.  
The block may be shifted inside the two neighbouring gaps.  
*If the block starts at the left border of the left gap*,  
the free part on its left side is `gapL`.  
*If it ends at the right border of the right gap*,  
the free part on its right side is `gapR`.  
No other placement can increase either side, because the block
cannot cross a neighbouring meeting.  
Therefore the best achievable free time for this window is
`max(gapL , gapR)`. ∎



##### Lemma 4  
During the sliding window step the algorithm computes
`max(gapL , gapR)` correctly for every window.

**Proof.**

For a window that starts at position `p`

```
   prefMax[p‑1]   = gapL          (by definition of prefMax)
   suffMax[p+t]   = gapR          (by definition of suffMax)
```

The algorithm evaluates `max(prefMax[p‑1], suffMax[p+t])`,
hence it obtains exactly `max(gapL , gapR)` for this window. ∎



##### Theorem  
For every test case the algorithm outputs the maximum possible length
of a continuous free interval after deleting `k` meetings.

**Proof.**

By Lemma&nbsp;1 the remaining meetings are a contiguous window of
length `n‑k` in the sorted list.  
By Lemma&nbsp;2 we may merge them into one block.  
Lemma&nbsp;3 gives the best free interval for that particular window
and Lemma&nbsp;4 shows that the algorithm evaluates this value.  
Taking the maximum over all possible windows, the algorithm
returns exactly the optimum. ∎



--------------------------------------------------------------------

#### 5.   Complexity Analysis

```
Sorting intervals                :  O(n log n)
Building prefix / suffix arrays  :  O(n)
Sliding window over all windows  :  O(n)
```

Hence the total running time for one test case is `O(n log n)`  
and the memory consumption is `O(n)`.



--------------------------------------------------------------------

#### 6.   Reference Implementation  (Python 3)

```python
import sys
import bisect

# -------------------------------------------------------------

def solve_one(n, k, m, intervals):
    # 1. sort by start time
    intervals.sort()                       # by l, r automatically
    l = [iv[0] for iv in intervals]
    r = [iv[1] for iv in intervals]

    # 2. prefix gaps – largest gap that ends at i
    pref = [0] * n
    for i in range(1, n):
        gap = l[i] - r[i-1]
        pref[i] = max(pref[i-1], gap)

    # 3. suffix gaps – largest gap that starts at i
    suff = [0] * n
    for i in range(n-2, -1, -1):
        gap = l[i+1] - r[i]
        suff[i] = max(suff[i+1], gap)

    # 3. sliding window (remaining meetings)
    t = n - k                     # number of meetings that stay
    best = 0
    for p in range(0, t + 1):
        left_g = pref[p-1] if p > 0 else 0
        right_g = suff[p + t] if p + t < n else 0
        best = max(best, left_g, right_g)
    return best

# -------------------------------------------------------------
def solve() -> None:
    it = iter(sys.stdin.read().strip().split())
    out_lines = []
    while True:
        n = int(next(it))
        k = int(next(it))
        m = int(next(it))
        if n == 0 and k == 0 and m == 0:
            break
        intervals = [(int(next(it)), int(next(it))) for _ in range(n)]
        out_lines.append(str(solve_one(n, k, m, intervals)))
    sys.stdout.write("\n".join(out_lines))

# -------------------------------------------------------------
if __name__ == "__main__":
    solve()
```

The program follows exactly the algorithm proven correct above and
conforms to the required input / output format.