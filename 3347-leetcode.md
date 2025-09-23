---
title: LeetCode 3347. Maximum Frequency of an Element After Performing Operations II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 3347. Maximum Frequency of an Element After Performing Operations II  
> **Hard | Public**  
> *Java • Python • C++ – 3‑language solution + SEO‑friendly interview‑prep article*

---

### TL;DR  
You have an array `nums`, a range `[-k, k]` and a limited number of operations `numOperations`.  
Each operation lets you pick an *unused* index and add any value from `[-k, k]` to it.  
You want to maximise the number of equal elements that can end up in the array.

The optimal strategy is a **sweep‑line over all interval endpoints**.  
Time: `O(n log n)` – sort 3 n endpoints.  
Memory: `O(n)` – counters + event map.

Below you’ll find full working solutions in **Java, Python, and C++**, plus a complete **blog article** that explains the problem, the algorithm, pitfalls, and why it’s a great interview question.

---

## 🧩  Problem Restatement

```
Input
nums           – array of size 1 … 10^5
k              – 0 … 10^9
numOperations  – 0 … nums.length

Operation (repeated numOperations times)
  1. Pick an index i that hasn't been chosen before.
  2. Add an integer v where -k ≤ v ≤ k to nums[i].
  
Goal
  Return the maximum possible frequency (i.e. how many elements are equal) of any value after performing all operations.
```

> **Example**  
> `nums = [1,4,5] , k = 1 , numOperations = 2`  
> We can transform the array into `[1,4,4]` → frequency of `4` = 2.

---

## 🔍  Key Observations

1. **Reachable target**  
   For an element `a` you can reach any target `t` in the closed interval `[a-k , a+k]` with *one* operation (or 0 if `a == t`).

2. **Intervals on the number line**  
   Every array entry gives us an interval of possible final values:
   ```
   a  →  [a-k , a+k]
   ```
   The problem becomes: find a point `t` that is covered by the most intervals.

3. **Operations budget**  
   - Elements already equal to `t` are “free”.
   - We can convert at most `numOperations` other elements that cover `t`.

   Therefore the best frequency at a point `t` is  
   ```
   cnt[t] + min(numOperations , cover[t] - cnt[t])
   ```
   where  
   * `cnt[t]` – how many elements are already `t`  
   * `cover[t]` – how many intervals contain `t` (i.e. how many elements are within `k` of `t`).

4. **All useful points are endpoints**  
   The function `cover(t)` changes only at the left or right end of an interval.  
   It’s enough to sweep over every distinct coordinate that appears as  
   * `a - k`  (interval start)  
   * `a + k + 1` (one past the interval end, for a classic “+1 / -1” sweep).

5. **Off‑by‑one safety**  
   The event at `a + k + 1` removes the interval **after** the last integer that can reach `t`.  
   That’s why we add **+1** to the right endpoint.

---

## ✅  Sweep‑Line Algorithm (Illustrated)

```
for each value a in nums:
    cnt[a] += 1
    events[a - k]   += 1          // interval starts
    events[a + k + 1] -= 1        // interval ends (after +1)

sort all keys in events  →  points[]
active = 0
answer = 0

for p in points[] (ascending order):
    active += events[p]                 // #intervals that cover p
    already = cnt.get(p, 0)
    extra   = min(active - already, numOperations)
    candidate = already + extra
    answer   = max(answer, candidate)
```

*The sweep guarantees that at any point we know exactly how many indices can be turned into that point.*

---

## 🚀  Three Working Implementations

### 1️⃣  Java

```java
import java.util.*;

public class Solution {
    public int maxFrequency(int[] nums, int k, int numOperations) {
        Map<Integer, Integer> count = new HashMap<>();      // existing value frequencies
        Map<Long, Integer> events = new HashMap<>();        // +1 at start, -1 after end

        for (int a : nums) {
            count.merge(a, 1, Integer::sum);

            long left  = (long) a - k;
            long right = (long) a + k + 1;     // one past the interval
            events.merge(left, 1, Integer::sum);
            events.merge(right, -1, Integer::sum);
        }

        List<Long> points = new ArrayList<>(events.keySet());
        Collections.sort(points);

        long active = 0;          // current #intervals covering the point
        int best = 0;

        for (long p : points) {
            active += events.get(p);
            int already = count.getOrDefault((int) p, 0);

            // how many of the other indices covering p we can still change?
            int extra = (int) Math.min(active - already, numOperations);
            int candidate = already + extra;
            best = Math.max(best, candidate);
        }
        return best;
    }
}
```

> **Complexity**  
> *Time* `O(n log n)` – sorting at most `3n` endpoints  
> *Memory* `O(n)` – two hash maps

---

### 2️⃣  Python

```python
from collections import Counter
from typing import List

class Solution:
    def maxFrequency(self, nums: List[int], k: int, numOperations: int) -> int:
        cnt = Counter(nums)          # existing frequencies
        events = Counter()           # +1 at left, -1 at right+1

        for a in nums:
            left  = a - k
            right = a + k + 1        # exclusive
            events[left]  += 1
            events[right] -= 1

        points = sorted(events)
        active = 0
        best = 0

        for p in points:
            active += events[p]
            already = cnt.get(p, 0)
            extra = min(active - already, numOperations)
            candidate = already + extra
            best = max(best, candidate)

        return best
```

> **Why `right = a + k + 1`?**  
> It keeps the interval *closed* at `a + k`.  
> After this coordinate we stop counting that element.

---

### 3️⃣  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxFrequency(vector<int>& nums, int k, int numOperations) {
        unordered_map<long long, int> cnt;      // value → freq
        unordered_map<long long, int> ev;       // event point → delta

        for (int a : nums) {
            cnt[a]++;

            long long l = static_cast<long long>(a) - k;
            long long r = static_cast<long long>(a) + k + 1; // exclusive
            ev[l] += 1;
            ev[r] -= 1;
        }

        vector<long long> points;
        points.reserve(ev.size());
        for (auto &p : ev) points.push_back(p.first);
        sort(points.begin(), points.end());

        long long active = 0;
        int best = 0;

        for (long long p : points) {
            active += ev[p];
            int already = cnt.count(p) ? cnt[p] : 0;
            int extra = static_cast<int>(min(active - already, static_cast<long long>(numOperations)));
            best = max(best, already + extra);
        }
        return best;
    }
};
```

> **Tip** – always cast to `long long` when adding `k` to avoid accidental overflow, even though 2 × 10^9 fits in `int`.

---

## 📚  The Good, The Bad, The Ugly

|  | What’s Great | What’s Hard | Common Pitfalls |
|---|---------------|-------------|-----------------|
| ✅ | • `O(n log n)` – fast enough for 10^5. <br>• Uses only integer arithmetic (no FFT). <br>• Clear geometric intuition. | • The number line is huge (up to 10^9), so we must **compress** coordinates. | • Off‑by‑one in event creation (`+1` on the right). <br>• Mixing `int` and `long long` can lead to overflow. <br>• Forgetting that indices already equal to the target cost **0** operations. |
| 🚨 | • Sweep line is a classic interview‑style pattern. <br>• Demonstrates knowledge of prefix sums, event counting, and map‑based compression. | • You must handle **unused‑index** constraint – many candidates forget that elements already equal to the target are free. | • Using a `TreeMap` instead of a vector+sort leads to `O(n log n)` events but an extra factor of log for each update – still fine but less clean. |
| ❌ | • If `k` is 0, the algorithm degenerates to “max existing frequency” – trivial to test. | • If you try a naive “try every possible `t`” loop, you’ll hit `O(n * range)` – impossible. | • Mis‑handling negative numbers in `long long` keys when casting back to `int` (watch for `INT_MIN`). |

---

## 🏗️  Alternative Solutions (Why Sweep is Better)

| Approach | Complexity | Notes |
|----------|------------|-------|
| **Binary Search + Prefix Sum** | `O(n log M)` (M = max value) | Works if you only need *max* frequency but *without* `numOperations` limit. |
| **Two‑Pointer / Sliding Window** | `O(n)` | Only applicable when `k` is small enough that the target must be an existing value; fails when `k` is large. |
| **Segment Tree** | `O(n log M)` | Heavy weight; unnecessary when the sweep is easier. |

The sweep‑line is the cleanest and fastest general solution for the full problem statement.

---

## 📈  Complexity Analysis

```
Let n = nums.length
```

*Events generated*: `3 n` (start, end+1, and existing value points)  
*Sorting*: `O(3n log 3n)` ≈ `O(n log n)`  
*Sweep*: linear in number of distinct points, `O(n)`  

Memory:  
- `cnt` – at most `n` distinct keys  
- `events` – at most `2n` keys  
→ `O(n)`.

---

## 🎤  Why This Problem Rocks for Interviews

1. **Geometric thinking** – visualises values as intervals, making the solution intuitive.  
2. **Coordinate compression** – teaches how to handle huge ranges.  
3. **Event counting** – classic prefix‑sum trick on the fly.  
4. **Operations budget** – forces candidates to think about *free* versus *paying* indices.  
5. **Edge‑case handling** – `k = 0`, negative values, large `k` all test robustness.

If you can explain the algorithm in *minutes*, you’re showing depth of understanding that goes beyond surface‑level DP or sorting tricks.

---

## 📌  Takeaway

*Use a sweep‑line with “+1 / -1” events to count how many array entries can be turned into each possible final value. Add the operations budget into the computation and you’ll get the optimal final frequency in `O(n log n)` time.*

---

Happy coding! 🧑‍💻🚀

---

*If you have any questions, drop a comment below or share your own solutions. Let's keep the discussion going!*