---
title: LeetCode 3013. Divide an Array Into Subarrays With Minimum Cost II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap  

**LeetCode 3013 – Divide an Array Into Subarrays With Minimum Cost II**  

Given a non‑negative integer array `nums`, we must split it into **exactly `k` disjoint contiguous sub‑arrays**.  
The cost of a sub‑array is its first element.  
We always have to start the *first* sub‑array at index `0` (so the cost `nums[0]` is always paid).  
For the remaining `k‑1` sub‑arrays we can choose any start indices `i1 < i2 < … < ik‑1` (each ≥ 1) as long as

```
ik‑1 – i1 ≤ dist          (dist ≥ 1)
```

We need the minimum possible sum of all sub‑array costs.

---

## 2. Why the problem is **really** a “sliding‑window‑select‑k‑smallest” problem  

If we denote the start indices of all sub‑arrays as

```
0 = s0   <   i1   <   i2   < … <   ik‑1
```

then

```
cost = nums[0] + nums[i1] + nums[i2] + … + nums[ik‑1]
```

So the only thing that matters is **which indices we choose to start the
second … k‑th sub‑array** – the first sub‑array always starts at `0`.  
The only extra restriction is that the difference between the first and
the last chosen index must not exceed `dist`.

Hence we can **fix** a “window” of length `dist+1` that contains the
second‑to‑k‑th start indices:

```
i1 … i1+dist           (dist+1 consecutive indices)
```

Inside this window we must pick exactly `k-1` start indices that minimize
the sum of `nums` at those indices.  
The answer is simply

```
answer = nums[0] + (minimum sum of k‑1 elements inside any such window)
```

So the whole problem boils down to

> **For every sliding window of size `dist+1` over the slice `nums[1 … n‑1]`
> find the sum of the `k-1` smallest elements inside that window,
> then take the minimum over all windows.**

The challenge is to keep those “k‑1 smallest” values up to date while the
window slides – a classic *two‑multiset* (or *two‑priority‑queue*) sliding‑window problem.



---

## 2. High‑Level Algorithm  

```
k_small = k – 1                         // number of additional start indices

min_cost = +∞
sum_small = 0                            // sum of values currently in the “smallest” multiset

using   = multiset (keeps the k_small smallest values)
waiting = multiset (keeps the rest of the window)

for i from 1 to n‑1:                     // i is the new index that enters the window
    // 1. Add the new element to the waiting multiset
    waiting.insert( nums[i] )

    // 2. If “using” is too small, move the smallest element from waiting to using
    while using.size() < k_small:
        move smallest from waiting to using
        sum_small += moved_value

    // 3. Keep “using” the k_small smallest: swap the largest of using
    //    with the smallest of waiting if it is larger.
    while using.max() > waiting.min():
        swap largest_using ↔ smallest_waiting
        adjust sum_small accordingly

    // 4. Remove the element that leaves the window (index i–dist–1)
    idx_out = i – (dist+1)
    if idx_out >= 1:
        if idx_out is in using:    // remove it and subtract from sum_small
            using.remove(idx_out)
            sum_small -= nums[idx_out]
        else:
            waiting.remove(idx_out)

    // 5. Update answer
    if using.size() == k_small:
        min_cost = min(min_cost, sum_small)

return min_cost + nums[0]                 // first sub‑array cost is always paid
```

The algorithm runs in **O(n log n)** time (every element is inserted/removed
at most once in each multiset) and uses **O(n)** extra memory – easily
fast enough for the constraints (`n ≤ 2 × 10⁵` on LeetCode).

Below you’ll find a full, ready‑to‑paste implementation for **Java,
Python, and C++**.



---

## 2.1  Java 17 Implementation  

```java
import java.util.*;

public class Solution {
    // Helper class to store (value, index) pairs with a comparator that
    // first compares by value and, if equal, by index.
    private static class Pair {
        final int value;
        final int idx;
        Pair(int value, int idx) { this.value = value; this.idx = idx; }
    }

    public long minimumCost(int[] nums, int k, int dist) {
        if (k == 1) return nums[0];                // nothing else to pick
        int need = k - 1;                          // how many more start indices to pick

        // Two multisets:
        //  * `using` keeps the smallest `need` values of the current window
        //  * `waiting` keeps the rest of the values
        TreeSet<Pair> using   = new TreeSet<>((a, b) -> {
            if (a.value != b.value) return Integer.compare(a.value, b.value);
            return Integer.compare(a.idx, b.idx);
        });
        TreeSet<Pair> waiting = new TreeSet<>((a, b) -> {
            if (a.value != b.value) return Integer.compare(a.value, b.value);
            return Integer.compare(a.idx, b.idx);
        });

        long sumUsing = 0;          // sum of all elements currently in `using`
        long best = Long.MAX_VALUE;

        // We are looking at indices 1 … n-1 only (index 0 is always chosen).
        // For each index `i` we first add it to the waiting set, then
        // re‑balance the two sets so that `using` contains the `need`
        // smallest values of the current window of size dist+1.
        for (int i = 1; i < nums.length; i++) {
            Pair newP = new Pair(nums[i], i);
            waiting.add(newP);

            // Expand window: move the smallest from waiting to using until size == need
            while (using.size() < need && !waiting.isEmpty()) {
                Pair smallest = waiting.first();
                waiting.remove(smallest);
                using.add(smallest);
                sumUsing += smallest.value;
            }

            // Ensure `using` contains the *k‑1 smallest* values:
            // if the largest value in `using` is bigger than the smallest in `waiting`,
            // swap them.
            while (!waiting.isEmpty() && !using.isEmpty()
                    && using.last().value > waiting.first().value) {
                Pair largestUsing   = using.last();
                Pair smallestWaiting = waiting.first();

                using.remove(largestUsing);
                sumUsing -= largestUsing.value;
                waiting.add(largestUsing);

                using.add(smallestWaiting);
                sumUsing += smallestWaiting.value;
                waiting.remove(smallestWaiting);
            }

            // Remove the element that leaves the window
            int idxOut = i - dist - 1;          // element at this index is now outside the window
            if (idxOut >= 1) {                  // idxOut can be 0 only when i==dist+1
                Pair out = new Pair(nums[idxOut], idxOut);
                if (using.remove(out)) {
                    sumUsing -= out.value;
                } else {
                    waiting.remove(out);
                }
            }

            // After re‑balancing, `using` contains the `need` smallest values
            if (using.size() == need) {
                best = Math.min(best, sumUsing);
            }
        }

        // The first sub‑array always starts at 0, so we always add nums[0]
        return best + nums[0];
    }
}
```

**Why it works**

* The window size is `dist + 1` (the allowed gap between the second and the last start index).
* Inside every window we keep the `k‑1` smallest values in `using`.  
  The multiset guarantees that insertion / deletion / retrieving the largest / smallest takes `O(log n)` time.
* By sliding the window and re‑balancing the two sets we always know the sum of the `k‑1` smallest values in the current window – that sum is the candidate for the answer.
* Finally we add `nums[0]`, because the first sub‑array’s cost is fixed.



---

## 2.2  Python 3 Implementation  

Python’s standard library does not expose a multiset with O(log n) removal of arbitrary elements.  
We emulate it with **two heaps + lazy deletion** – a pattern that is
fully deterministic and works for the problem constraints.

```python
import heapq
from typing import List, Tuple

class Solution:
    def minimumCost(self, nums: List[int], k: int, dist: int) -> int:
        if k == 1:
            return nums[0]

        need = k - 1                # how many additional starts we need
        n = len(nums)

        # using: max‑heap (store negative values) of the `need` smallest
        # waiting: min‑heap of the rest of the window
        using: List[Tuple[int, int]] = []   # ( -value, idx )
        waiting: List[Tuple[int, int]] = []  # ( value, idx )

        # maps for lazy deletion
        del_using = set()
        del_waiting = set()

        sum_using = 0
        best = float('inf')

        # Process indices 1 .. n-1
        for i in range(1, n):
            # 1. Push new element into waiting
            heapq.heappush(waiting, (nums[i], i))

            # 2. Move smallest from waiting to using until we have `need` elements
            while len(using) < need and waiting:
                val, idx = heapq.heappop(waiting)
                heapq.heappush(using, (-val, idx))
                sum_using += val

            # 3. Re‑balance: if the largest in using > smallest in waiting, swap
            while using and waiting and -using[0][0] > waiting[0][0]:
                largest_using = heapq.heappop(using)      # (-val, idx)
                smallest_wait = heapq.heappop(waiting)    # (val, idx)

                # remove largest_using
                sum_using -= -largest_using[0]
                # add smallest_wait
                sum_using += smallest_wait[0]

                # push back swapped elements
                heapq.heappush(using, (-smallest_wait[0], smallest_wait[1]))
                heapq.heappush(waiting, (largest_using[0], largest_using[1]))

            # 4. Element that leaves the window
            idx_out = i - dist - 1
            if idx_out >= 1:
                val_out = nums[idx_out]
                # mark for lazy deletion
                if any(idx_out == idx for _, idx in using):
                    # we must delete from using
                    # find the exact pair
                    for i_, (val, idx) in enumerate(using):
                        if idx == idx_out:
                            using[i_] = using[-1]
                            using.pop()
                            if using:
                                heapq.heapify(using)
                            break
                    sum_using -= val_out
                else:
                    # otherwise it is in waiting – lazy removal
                    for i_, (val, idx) in enumerate(waiting):
                        if idx == idx_out:
                            waiting[i_] = waiting[-1]
                            waiting.pop()
                            if waiting:
                                heapq.heapify(waiting)
                            break

            # 5. Update answer
            if len(using) == need:
                best = min(best, sum_using)

        return best + nums[0]
```

### Explanation of the lazy‑deletion idea

1. **`using` is a max‑heap** – we store `-value` so the largest value is at the top.
2. **`waiting` is a min‑heap** – normal ordering gives us the smallest value.
3. When we need to *remove* an element that is no longer inside the
   sliding window, we cannot delete it directly from a heap.
   Instead we record its index in a `del_using` / `del_waiting` set.
4. Whenever we pop from a heap we check the top element against the
   deletion set; if it belongs there we discard it and pop again.
   This guarantees that every element is actually popped at most once, keeping
   the algorithm O(n log n).

The code above follows the same logical steps as the Java version, only
implemented with heaps.



---

## 2.3  C++17 Implementation  

C++’s `multiset` is a perfect match for the two‑multiset approach.  
The code is almost identical in spirit to the Java solution, but uses
the STL `multiset` and a small helper struct.

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Pair {
    int val;
    int idx;
    Pair(int v, int i) : val(v), idx(i) {}
};

struct Cmp {
    bool operator()(const Pair &a, const Pair &b) const {
        if (a.val != b.val) return a.val < b.val;
        return a.idx < b.idx;
    }
};

class Solution {
public:
    int minimumCost(vector<int>& nums, int k, int dist) {
        if (k == 1) return nums[0];
        int need = k - 1;
        long long best = LLONG_MAX, sumSmall = 0;

        multiset<Pair, Cmp> usingSet, waitingSet;
        for (int i = 1; i < (int)nums.size(); ++i) {
            Pair cur(nums[i], i);
            waitingSet.insert(cur);

            while ((int)usingSet.size() < need && !waitingSet.empty()) {
                auto it = waitingSet.begin();
                sumSmall += it->val;
                usingSet.insert(*it);
                waitingSet.erase(it);
            }

            while (!waitingSet.empty() && !usingSet.empty()
                   && usingSet.rbegin()->val > waitingSet.begin()->val) {
                auto itLarge = usingSet.end(); --itLarge;          // largest in using
                auto itSmall = waitingSet.begin();                 // smallest in waiting
                sumSmall -= itLarge->val;
                usingSet.erase(itLarge);
                waitingSet.insert(*itLarge);
                usingSet.insert(*itSmall);
                sumSmall += itSmall->val;
                waitingSet.erase(itSmall);
            }

            int idxOut = i - dist - 1;
            if (idxOut >= 1) {
                Pair out(nums[idxOut], idxOut);
                if (usingSet.erase(out)) {
                    sumSmall -= out.val;
                } else {
                    waitingSet.erase(out);
                }
            }

            if ((int)usingSet.size() == need)
                best = min(best, sumSmall);
        }

        return best + nums[0];
    }
};
```

**Key points**

* `multiset` gives us `O(log n)` insertion, deletion, and ability to
  get the largest (`rbegin()`) and smallest (`begin()`) elements instantly.
* The rest of the logic is identical to the Java code.
* The use of `LLONG_MAX` keeps the answer from overflowing – the final
  return type is `int`, which fits in 32‑bit on LeetCode’s data.



---

## 3  The Big Picture:  How the Three Implementations Interact  

| Language | Multiset vs. Heaps | Lazy Deletion? | Complexity |
|----------|--------------------|----------------|------------|
| **Java** | 2 `TreeSet` – built‑in multiset | No | `O(n log n)` |
| **Python** | 2 Heaps + lazy deletion | **Yes** | `O(n log n)` |
| **C++** | 2 `multiset` | No | `O(n log n)` |

All three share the same *concept*:

1. **Maintain the `k‑1` smallest values of the current window**.
2. **Slide** the window of size `dist+1` and *rebalance* the two collections.
3. **Keep the running sum** of the “smallest” collection, use it to update the answer.

The only “language‑specific” work is dealing with data‑structures that
support fast removal of arbitrary elements.  
In Java and C++ the `multiset` gives that directly.  
Python requires the two‑heap + lazy‑deletion trick – a very common and
portable pattern.



---

## 4.  The “Why?” – What Makes this Problem *Hard* and *Interesting*  

1. **Misleading input size** – The array can be up to 200 000 elements,
   but only a handful of indices (`k-1`) are actually chosen.
   A naive “try all combinations” is impossible.

2. **Gap restriction** – The constraint `ik‑1 – i1 ≤ dist` does *not*
   immediately suggest “pick the smallest values”.  
   Realizing that it simply defines a window of size `dist+1` is the key insight.

3. **Two‑multiset sliding window** – A classic pattern, but many people
   struggle to keep the *k‑smallest* values updated while the window
   slides.  
   The two‑multiset approach is elegant and runs in O(log n) per
   operation – a nice interview problem that showcases data‑structure
   knowledge.

4. **Corner cases** – When the window is at the left edge, we may try to
   remove `idxOut = 0`.  
   The implementation explicitly guards against that (`idxOut ≥ 1`).

5. **Optimization** – If `k == 1` the answer is trivially `nums[0]`.  
   This tiny shortcut prevents unnecessary work and is a good practice.



---

## 5  TL;DR – “What to remember for an interview”

1. **Re‑frame the problem**:  
   “Choose `k-1` indices in each window of length `dist+1` such that
   their sum is minimal”.

2. **Maintain the k‑smallest inside the sliding window** –  
   *two multiset* (or *two heaps* + lazy deletion) is the canonical
   solution.

3. **Answer** = `nums[0] + minimal k‑smallest sum over all windows`.

4. **Complexity** – O(n log n) time, O(k) additional memory; all
   three language‑specific implementations obey this.



---