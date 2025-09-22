---
title: LeetCode 3346. Maximum Frequency of an Element After Performing Operations I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every test case

* `n` – number of friends  (`1 ≤ n ≤ 2·10^5`)
* `k` – maximal distance that a friend can change (`k` may be large)
* `numOps` – number of operations that can be performed
* `a[1 … n]` – distance of the i‑th friend from the station

During one operation we choose a friend and change his distance.
After all operations all distances have to be an integer.



--------------------------------------------------------------------

#### 1.   Observation – what does “making a group” mean?

All friends that belong to one group must finally have the same distance.
For a group that will become the value `x`

```
for every friend in the group : |original distance – x| ≤ k
```

Hence all distances inside the group must lie inside an interval of
length `2k` (the difference between the smallest and the largest
original distance in the group is at most `2k`).

For a fixed target value `x` we can choose *any* subset of friends
inside this interval – we are allowed to leave some friends unchanged
and we may change only the others.

--------------------------------------------------------------------

#### 2.   Two kinds of optimal groups

*1.  Target value is one of the initial distances*  
If the final value is already present in the input, every friend whose
distance differs from this value by at most `k` can be moved to it
(1 operation per friend).  
The size of such a group equals

```
    number of friends inside [x – k , x + k]       (1)
```

and we need one operation for each friend that is not already equal to
`x`.

*2.  Target value is **not** an initial distance*  
The target distance may be any integer.  
All friends inside an interval of length `2k` can be moved to the same
value (1 operation per friend that differs from this value).  
The size of the group is

```
    number of friends inside some interval of length 2k      (2)
```

and we can use at most `numOps` of these friends.

The final answer is the larger of the best groups of type (1) and
type (2).

--------------------------------------------------------------------

#### 3.   How to find the best group of type (1)

Sort all distances.  
For each distinct distance `v`

```
cnt[v] = number of occurrences of v          (pre‑computed)
```

Let the sorted array be `b[0 … n-1]`.

For a fixed `v` we need the number of elements inside
`[v – k , v + k]`.  
With two pointers (a sliding window) we can obtain this in `O(n)`.

```
right – first index with b[right] > v + k          (exclusive)
left  – first index with b[left]  ≥ v – k
total = right – left                               (size of the window)
```

`total – cnt[v]` friends differ from `v` and need to be changed.
The maximum possible size of a group that finally equals `v` is

```
cnt[v] + min(numOps , total – cnt[v])               (3)
```

--------------------------------------------------------------------

#### 4.   How to find the best group of type (2)

Again with a sliding window we search for the longest interval of
length `2k`.  
Now the window is defined by

```
b[right] – b[left] ≤ 2k
```

The window size is `right – left + 1`.  
The maximum window size over all positions is stored in
`maxWindow`.  
The best group of type (2) has size

```
min(numOps , maxWindow)                               (4)
```

--------------------------------------------------------------------

#### 4.   Correctness Proof  

We prove that the algorithm described above outputs the maximum
possible number of friends that can finally have the same distance.

---

##### Lemma 1  
For a fixed distance `v` the algorithm computes exactly the number of
friends whose original distance lies in the interval `[v – k , v + k]`.

**Proof.**

The array is sorted.
`right` is moved forward while `b[right] ≤ v + k`.  
Therefore when the loop stops, `b[right]` is the first element larger
than `v + k`; all previous elements belong to the interval.
Analogously `left` is moved forward while `b[left] < v – k`,
hence `b[left]` is the first element not smaller than `v – k`.  
All indices in `[left , right-1]` belong to `[v – k , v + k]`,
no other index does.  
Thus the size of the window `right – left` is exactly the number of
friends in that interval. ∎



##### Lemma 2  
For a fixed distance `v` the value computed by formula (3)
is the maximum number of friends that can be moved to distance `v`
using at most `numOps` operations.

**Proof.**

All friends inside the interval `[v – k , v + k]` can be moved to `v`
(1 operation per friend that is not already at `v`).  
The number of such friends is `total – cnt[v]`.  
We can use at most `numOps` of them, therefore the size of the group
cannot exceed `cnt[v] + min(numOps , total – cnt[v])`,
which is exactly the value (3).  
On the other hand, we can achieve this size by moving
`min(numOps , total – cnt[v])` friends to `v`.  
Hence the value (3) is optimal for target `v`. ∎



##### Lemma 3  
`maxWindow` computed by the second sliding window is the maximum
possible number of friends that can belong to a group whose final
distance lies inside an interval of length `2k`.

**Proof.**

While the right pointer moves from left to right,
the left pointer is always the smallest index such that  
`b[right] – b[left] ≤ 2k`.  
Consequently the window `[left , right]` always contains all elements
inside some interval of length `2k` and no larger window ending at
`right` is possible.  
The algorithm keeps the largest window size over all positions,
hence `maxWindow` is the maximum number of friends that can be put
into the same group using an arbitrary final distance. ∎



##### Lemma 4  
The optimal number of friends that can finally have the same distance
is `max( best_type1 , best_type2 )`,
where

```
best_type1 = max over all v of  (cnt[v] + min(numOps , total(v)-cnt[v]))
best_type2 = min(numOps , maxWindow)
```

**Proof.**

*Type (1)* groups: by Lemma&nbsp;2 the best group for each `v` is
exactly the value in (3).  
Taking the maximum over all `v` yields `best_type1`.

*Type (2)* groups: by Lemma&nbsp;3 the largest possible group inside
any interval of length `2k` contains `maxWindow` friends.  
Only `numOps` of them can be changed, therefore the maximal achievable
size is `min(numOps , maxWindow) = best_type2`.

Any valid final arrangement must belong to one of the two types,
hence the overall optimum is the larger of the two values. ∎



##### Theorem  
The algorithm outputs the maximum possible number of friends that can
be moved to the same integer distance using at most `numOps` operations.

**Proof.**

The algorithm evaluates the two expressions derived in
Lemma&nbsp;4 and outputs their maximum.
By Lemma&nbsp;4 this value is exactly the optimal number of friends
that can share the same distance. ∎



--------------------------------------------------------------------

#### 4.   Complexity Analysis

```
Sorting          :  O(n log n)
Two sliding windows :  O(n)
Memory consumption :  O(n)
```

With `n ≤ 2·10^5` the program easily satisfies the limits.



--------------------------------------------------------------------

#### 5.   Reference Implementation  (Python 3)

```python
import sys
from collections import Counter

def solve() -> None:
    data = sys.stdin.read().strip().split()
    if not data:
        return
    it = iter(data)
    n = int(next(it))
    k = int(next(it))
    num_ops = int(next(it))
    a = [int(next(it)) for _ in range(n)]

    a.sort()
    cnt = Counter(a)          # number of occurrences of each value

    # --------- best group with target equal to an initial distance ----------
    left = 0
    right = 0
    best1 = 0
    for v in set(a):                    # distinct values
        if right < a.index(v):          # right is always ahead of left
            right = a.index(v)
        while right < n and a[right] <= v + k:
            right += 1
        while left < n and a[left] < v - k:
            left += 1
        total = right - left            # number of elements in [v-k , v+k]
        need = total - cnt[v]           # friends that are not already at v
        possible = cnt[v] + min(num_ops, need)
        if possible > best1:
            best1 = possible

    # --------- best group with target NOT in the initial distances -------
    left = 0
    max_window = 0
    for right in range(n):
        while a[right] - a[left] > 2 * k:
            left += 1
        window = right - left + 1
        if window > max_window:
            max_window = window
    best2 = min(num_ops, max_window)

    print(max(best1, best2))

if __name__ == "__main__":
    solve()
```

The program follows exactly the algorithm proven correct above
and conforms to the required input/output format.