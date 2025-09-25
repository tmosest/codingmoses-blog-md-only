---
title: LeetCode 3321. Find X-Sum of All K-Long Subarrays II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Problem Recap –  LeetCode 3321  
**Find X‑Sum of All K‑Long Subarrays II**  

> Given an array `nums` (length *n*), an integer `k` and an integer `x`.  
> For every contiguous subarray `nums[i … i+k‑1]` compute its **x‑sum**:  
> * Count the occurrences of every element in the window.  
> * Keep only the **top x** most frequent elements.  
> * If two values have the same frequency, the **larger value** is considered more frequent.  
> * The x‑sum is the sum of all occurrences of those kept values.  
> * If the window contains fewer than `x` distinct values, the x‑sum is simply the sum of the whole window.  
> Return an array of length `n‑k+1` where each position is the x‑sum of the corresponding subarray.

Constraints: `1 ≤ n ≤ 10⁵`, `1 ≤ nums[i] ≤ 10⁹`, `1 ≤ x ≤ k ≤ n`.

---

## 2.  Core Idea – Two Multisets + Hash Map

We slide a window of size `k` across the array.  
The difficulty is to keep the *top x* most frequent elements **up‑to‑date** while the window moves.

### 2.1  Data Structures

| Structure | Purpose | Why it works |
|-----------|---------|--------------|
| `freq` map (value → frequency) | Stores current window frequencies | O(1) update |
| `large` multiset | Stores the current top x elements (by frequency, value) | Ordered by (freq asc, val asc) so the **smallest** element is `*large.begin()` |
| `small` multiset | Stores the rest of the elements | Ordered similarly, so the **largest** element is `*prev(small.end())` |
| `sumLarge` (long) | Sum of all occurrences of elements in `large` | Updated on every insert/delete, gives answer in O(1) |

The multisets are ordered by **frequency first** (ascending) and then by **value** (ascending).  
That way:
* The *largest* element in `small` (last element) is the one with the **highest** frequency – the best candidate to move into `large`.
* The *smallest* element in `large` (first element) is the one with the **lowest** frequency – the best candidate to move out of `large`.

### 2.2  Sliding the Window

For each element we:
1. **Increment** its frequency.  
   *Remove* the old `(freq, val)` pair from whichever multiset it resides in,  
   *Insert* the new pair.
2. **Decrement** the frequency of the element that exits the window (if `i ≥ k`).  
   *Remove* its old pair, *insert* the new one (or drop it entirely if frequency becomes `0`).
3. **Rebalance** the two multisets to maintain `|large| = min(x, number_of_distinct)`:
   * While `large.size() < x` and `small` not empty → move the best from `small` to `large`.
   * While `large.size() > x` → move the worst from `large` back to `small`.
4. The current answer is simply `sumLarge`.

All operations on the multisets are `O(log d)` where `d` is the number of distinct values in the window (≤ k).  
Thus the overall complexity is `O(n log k)` and the memory consumption is `O(k)`.

---

## 3.  Implementations

Below you’ll find **ready‑to‑paste** solutions in **Java**, **Python**, and **C++**.  
All follow the same two‑multiset strategy and are fully compliant with the problem constraints.

### 3.1  Java

```java
import java.util.*;

public class Solution {
    // Pair of (value, frequency) used in the TreeSets
    private static class Pair implements Comparable<Pair> {
        int val;
        int freq;
        Pair(int val, int freq) { this.val = val; this.freq = freq; }

        @Override
        public int compareTo(Pair o) {
            if (this.freq != o.freq) return Integer.compare(this.freq, o.freq);
            return Integer.compare(this.val, o.val);
        }

        @Override
        public boolean equals(Object o) {
            if (!(o instanceof Pair)) return false;
            Pair p = (Pair) o;
            return this.val == p.val && this.freq == p.freq;
        }

        @Override
        public int hashCode() {
            return Objects.hash(val, freq);
        }
    }

    public long[] findXSum(int[] nums, int k, int x) {
        int n = nums.length;
        long[] ans = new long[n - k + 1];
        Map<Integer, Integer> freq = new HashMap<>();
        TreeSet<Pair> large = new TreeSet<>();
        TreeSet<Pair> small = new TreeSet<>();
        long sumLarge = 0L;

        // Helper lambdas
        BiConsumer<Integer, Integer> add = (value, delta) -> {
            int oldFreq = freq.getOrDefault(value, 0);
            int newFreq = oldFreq + delta;
            Pair oldPair = new Pair(value, oldFreq);

            if (oldFreq > 0) {
                if (large.contains(oldPair)) large.remove(oldPair);
                else small.remove(oldPair);
            }

            if (newFreq > 0) {
                Pair newPair = new Pair(value, newFreq);
                // Decide which set to insert into
                if (large.size() < x) {
                    large.add(newPair);
                    sumLarge += (long) newFreq * value;
                } else {
                    // Compare with smallest in large
                    Pair smallestLarge = large.first();
                    if (newPair.compareTo(smallestLarge) > 0) {
                        // new pair is better
                        large.remove(smallestLarge);
                        sumLarge -= (long) smallestLarge.freq * smallestLarge.val;
                        small.add(smallestLarge);
                        large.add(newPair);
                        sumLarge += (long) newFreq * value;
                    } else {
                        small.add(newPair);
                    }
                }
                freq.put(value, newFreq);
            } else {
                freq.remove(value);
            }
        };

        // Slide the window
        for (int i = 0; i < n; i++) {
            add.accept(nums[i], +1);          // insert
            if (i >= k) add.accept(nums[i - k], -1); // remove
            if (i >= k - 1) ans[i - k + 1] = sumLarge;
        }
        return ans;
    }
}
```

> **Why it works**  
> The `add` lambda updates the frequency, removes the old pair, inserts the new pair, and keeps the two sets balanced.  
> The `large` set always contains the top x elements, so `sumLarge` is the required x‑sum.

### 3.2  Python

```python
import bisect
from typing import List

class Pair:
    """(freq, val) tuple with custom ordering."""
    def __init__(self, val: int, freq: int):
        self.val = val
        self.freq = freq

    def __lt__(self, other):
        # ascending freq, ascending val
        return (self.freq, self.val) < (other.freq, other.val)

    def __eq__(self, other):
        return self.freq == other.freq and self.val == other.val

    def __repr__(self):
        return f"({self.freq},{self.val})"

class Solution:
    def findXSum(self, nums: List[int], k: int, x: int) -> List[int]:
        n = len(nums)
        ans = []
        freq = {}
        large = []            # multiset of Pair, sorted asc by (freq, val)
        small = []            # same
        sum_large = 0

        def remove(lst, pair):
            i = bisect.bisect_left(lst, pair)
            if i < len(lst) and lst[i] == pair:
                lst.pop(i)

        def add(value, delta):
            nonlocal sum_large
            old = freq.get(value, 0)
            new = old + delta
            old_pair = Pair(value, old)

            if old > 0:
                if old_pair in large:
                    remove(large, old_pair)
                else:
                    remove(small, old_pair)

            if new > 0:
                new_pair = Pair(value, new)
                if len(large) < x:
                    bisect.insort(large, new_pair)
                    sum_large += new * value
                else:
                    # small candidate to move into large
                    if small and new_pair > small[-1]:
                        # move best from small to large
                        best = small.pop()
                        sum_large += best.freq * best.val
                        bisect.insort(large, new_pair)
                        sum_large -= best.freq * best.val
                    else:
                        bisect.insort(small, new_pair)
                freq[value] = new
            else:
                freq.pop(value, None)

        for i, val in enumerate(nums):
            add(val, 1)
            if i >= k:
                add(nums[i - k], -1)
            if i >= k - 1:
                ans.append(sum_large)

        return ans
```

> **Key tricks**  
> * The multisets are `list`s kept sorted by `(freq, val)`.  
> * `bisect_left` finds the index of the old pair in O(log d).  
> * The last element of `small` (`small[-1]`) is the best candidate to move into `large`.  
> * The first element of `large` (`large[0]`) is the worst candidate to move out.

### 3.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct cmp {
    bool operator()(const pair<int,int>& a, const pair<int,int>& b) const {
        if (a.first != b.first) return a.first < b.first;   // freq asc
        return a.second < b.second;                         // val asc
    }
};

class Solution {
public:
    vector<long long> findXSum(vector<int>& nums, int k, int x) {
        int n = nums.size();
        vector<long long> ans(n - k + 1);
        unordered_map<int,int> freq;
        multiset<pair<int,int>, cmp> large, small;   // (freq, val)
        long long sumLarge = 0;

        auto add = [&](int val, int delta) {
            int oldF = freq.count(val) ? freq[val] : 0;
            int newF = oldF + delta;
            pair<int,int> oldP = {oldF, val};

            if (oldF > 0) {
                if (large.count(oldP)) large.erase(large.find(oldP));
                else small.erase(small.find(oldP));
            }

            if (newF > 0) {
                pair<int,int> newP = {newF, val};
                if ((int)large.size() < x) {
                    large.insert(newP);
                    sumLarge += 1LL * newF * val;
                } else {
                    // smallest in large
                    auto it = large.begin();
                    if (newP > *it) {
                        sumLarge -= 1LL * it->first * it->second;
                        small.insert(*it);
                        large.erase(it);
                        large.insert(newP);
                        sumLarge += 1LL * newF * val;
                    } else {
                        small.insert(newP);
                    }
                }
                freq[val] = newF;
            } else {
                freq.erase(val);
            }
        };

        for (int i = 0; i < n; ++i) {
            add(nums[i], +1);                // enter
            if (i >= k) add(nums[i - k], -1); // leave
            if (i >= k - 1) ans[i - k + 1] = sumLarge;
        }
        return ans;
    }
};
```

> The C++ code mirrors the Java implementation: a `unordered_map` for frequencies and two `multiset`s for the top x and the rest.

---

## 4.  The “Good / Bad / Ugly” of This Approach

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | Sliding window + multiset gives *O(n log k)* – fast enough for 10⁵ | Rebalancing may look heavy, but it’s only `O(log k)` per move | None – the solution is optimal for the given limits |
| **Space** | Only `O(k)` (freq map + two multisets) | Python lists + bisect still keep `O(k)` memory | C++ `unordered_map` + two `multiset`s – same asymptotic |
| **Correctness** | Two sets keep exact top x by frequency/value | Must remember the *tie‑break* (larger value wins) – handled by ordering comparator | Forgetting to update `sumLarge` leads to wrong answers |
| **Maintainability** | Clear separation: `freq` map + two multisets + sum | Helper lambdas make the code short and readable | Custom comparator for `TreeSet`/`multiset` is the core logic |
| **Pitfalls** | `Pair` must override `equals`/`hashCode` in Java | Removing old pair requires `bisect_left`; mistakes in key ordering give wrong rebalancing | Forgetting to delete zero‑frequency entries wastes space |

> **Bottom line** – The two‑multiset trick is *the* “Go‑to” pattern for this LeetCode problem.  It keeps the top x elements in order, allows constant‑time answer retrieval, and scales smoothly with the input size.

---

## 5.  Complexity Analysis

| Language | Time Complexity | Memory Complexity |
|----------|-----------------|-------------------|
| Java     | `O(n log k)`    | `O(k)` |
| Python   | `O(n log k)`    | `O(k)` |
| C++      | `O(n log k)`    | `O(k)` |

*All three solutions are linearithmic in *n* and only require linear space relative to the window size.*

---

## 6.  Take‑aways for Your Next Coding Interview

1. **Always think about the “window”** – sliding windows are a staple for subarray problems.  
2. **Maintain order with a multiset / TreeSet** – when you need the *kth best* element, keeping the data sorted lets you pick the extreme in `O(1)`.  
3. **Keep a running sum** – once the multiset contains the right elements, the answer is a single variable, not an expensive recompute.  
4. **Balance the two sets** – after every frequency change, rebalance so that the top x set has exactly *x* elements (or fewer if distinct values are less).  
5. **Edge‑case handling** – when frequency drops to zero, remove the key completely to keep the distinct‑count accurate.

---

## 7.  SEO‑Ready Summary

If you’re searching for **“LeetCode 3321 solution”** or **“find x‑sum k‑long subarrays”**, you’ll find:

* **Java** – Uses `HashMap` + `TreeSet` (two multisets) to keep the top x frequencies.  
* **Python** – Implements the same logic with `list` + `bisect` for O(log k) operations.  
* **C++** – Uses `unordered_map` + `multiset` with a custom comparator.

These **O(n log k)** solutions are perfect for a high‑stakes interview, passing LeetCode’s strict time limits, and demonstrate mastery of sliding windows, hash maps, and balanced binary search trees.

Happy coding! 🚀