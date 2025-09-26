---
title: LeetCode 3321. Find X-Sum of All K-Long Subarrays II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3321 – “Find X‑Sum of All K‑Long Subarrays II”  
*Solution in **Java**, **Python**, and **C++** + a SEO‑friendly blog post that explains the good, the bad, and the ugly.*

---

### Problem Recap

> Given an array `nums` (length *n*), and two integers `k` and `x`.  
> For every contiguous subarray of length `k`  
> 1. Count the frequency of each element.  
> 2. Keep the **top `x` most frequent elements**  
>    *If two elements tie in frequency, the larger value wins.*  
> 3. Sum the kept elements (or the whole subarray if it has fewer than `x` distinct values).  
> Return an array of these “X‑Sums” for all sliding windows.

---

## 1. The Core Idea – Two Multisets + Sliding Window

| ✅ Good | ❌ Bad | ⚙️ Ugly |
|--------|--------|---------|
| *Linear‑time sliding window* – only `O(n)` updates per move | *Requires custom comparator* – tricky in some languages | *Lazy deletion for heaps* – can obscure bugs |
| *Exact O(1) sum maintenance* – keep a running sum of the top `x` | *Space usage* – two multisets + hash map (`O(n)` worst case) | *Balancing logic* – careful swapping between the two multisets |
| *Deterministic tie‑breaking* – larger value wins when frequencies tie | *Harder to reason* – especially when `k == x` (whole window is always summed) | *Edge‑case handling* – freq goes to 0, element disappears |

### How it works

| Step | Action |
|------|--------|
| **1. Keep frequencies** – `freq[value]` in a hash map. |
| **2. Two multisets** –  
| | `large`  – contains at most `x` elements, the current “top x”.  
| | `small` – all remaining elements. |
| **3. Order** – multiset is sorted **ascending** by `(frequency, value)`.  
| | *First* (`smallest`) in `large` is the weakest candidate to demote.  
| | *Last* (`largest`) in `small` is the strongest candidate to promote. |
| **4. Maintain `sumLarge`** – sum of all elements in `large`. |
| **5. On every window shift** – add new element, remove old element, then rebalance `large` & `small` to satisfy the invariant `|large| == x` (or all distinct values if fewer). |

Because each element is inserted and removed exactly once per window slide, the whole algorithm is `O(n log n)` (the `log n` comes from multiset operations).

---

## 2. Reference Implementations

Below you’ll find clean, production‑ready code for **Java**, **Python**, and **C++** that you can paste into your editor or use as a reference for your own solutions.

> **Tip:** All solutions use 64‑bit arithmetic (`long` / `long long`) because the sum can exceed `int32`.

---

### 2.1 Java (TreeSet + HashMap)

```java
import java.util.*;

/**
 * LeetCode 3321 – Find X‑Sum of All K‑Long Subarrays II
 */
public class Solution {
    private static class Pair implements Comparable<Pair> {
        final int value;   // element value
        int freq;          // current frequency

        Pair(int value, int freq) {
            this.value = value;
            this.freq = freq;
        }

        @Override
        public int compareTo(Pair o) {
            if (this.freq != o.freq) return Integer.compare(this.freq, o.freq);
            return Integer.compare(this.value, o.value);
        }

        @Override
        public boolean equals(Object o) {
            return o instanceof Pair p &&
                   p.value == this.value && p.freq == this.freq;
        }

        @Override
        public int hashCode() {
            return Objects.hash(value, freq);
        }
    }

    private final Map<Integer, Integer> freq = new HashMap<>();
    private final TreeSet<Pair> large = new TreeSet<>();
    private final TreeSet<Pair> small = new TreeSet<>();
    private long sumLarge = 0;   // sum of elements in 'large'

    // helper to update frequency of a value
    private void changeFreq(int val, int delta) {
        int oldF = freq.getOrDefault(val, 0);
        int newF = oldF + delta;
        if (newF < 0) throw new IllegalStateException();

        Pair oldPair = new Pair(val, oldF);
        Pair newPair = new Pair(val, newF);

        // remove old pair from whichever set it belongs to
        if (large.contains(oldPair)) {
            large.remove(oldPair);
            sumLarge -= (long) oldF * val;
            if (newF > 0) {
                large.add(newPair);
                sumLarge += (long) newF * val;
            } else {
                // freq becomes 0 -> drop completely
                freq.remove(val);
                return;
            }
        } else if (small.contains(oldPair)) {
            small.remove(oldPair);
            if (newF > 0) small.add(newPair);
            else freq.remove(val);
        } else { // element was not present before
            if (newF > 0) {
                small.add(newPair);
            } else {
                return; // nothing to do
            }
        }

        if (newF > 0) freq.put(val, newF);
    }

    // rebalance large/small to keep |large| == x
    private void rebalance(int x) {
        // move from small to large if needed
        while (large.size() < x && !small.isEmpty()) {
            Pair toMove = small.last();   // strongest in small
            small.remove(toMove);
            large.add(toMove);
            sumLarge += (long) toMove.freq * toMove.value;
        }

        // demote weakest large to small
        while (!small.isEmpty() && large.size() > x) {
            Pair toMove = large.first();  // weakest in large
            large.remove(toMove);
            sumLarge -= (long) toMove.freq * toMove.value;
            small.add(toMove);
        }

        // swap if a stronger element is sitting in small
        if (!large.isEmpty() && !small.isEmpty()) {
            Pair weakestLarge = large.first();   // weakest in top‑x
            Pair strongestSmall = small.last(); // strongest among the rest

            if (strongestSmall.freq > weakestLarge.freq ||
                (strongestSmall.freq == weakestLarge.freq &&
                 strongestSmall.value > weakestLarge.value)) {

                // demote weakestLarge
                large.remove(weakestLarge);
                sumLarge -= (long) weakestLarge.freq * weakestLarge.value;
                small.add(weakestLarge);

                // promote strongestSmall
                small.remove(strongestSmall);
                large.add(strongestSmall);
                sumLarge += (long) strongestSmall.freq * strongestSmall.value;
            }
        }
    }

    public int[] getXSum(int[] nums, int k, int x) {
        int n = nums.length;
        int[] ans = new int[n - k + 1];

        for (int i = 0; i < k; i++) changeFreq(nums[i], 1);
        rebalance(x);
        ans[0] = (int) sumLarge;

        for (int i = 1; i <= n - k; i++) {
            changeFreq(nums[i - 1], -1);   // remove leftmost
            changeFreq(nums[i + k - 1], 1); // add new rightmost
            rebalance(x);
            ans[i] = (int) sumLarge;
        }

        return ans;
    }
}
```

**Why this is the “good” Java implementation**

- `TreeSet` guarantees *log‑n* insertion / removal in **O(1)** time per operation.  
- `Pair` holds the *current* frequency, so we can recompute the sum on the fly.  
- The invariant `|large| == x` is restored with a small while‑loop, keeping the logic readable.

---

### 2.2 Python (two heaps + lazy deletion)

```python
import heapq
from collections import defaultdict
from typing import List

# ----------------------------------------
# LeetCode 3321 – Python (heap + lazy deletion)
# ----------------------------------------
class Solution:
    def xSum(self, nums: List[int], k: int, x: int) -> List[int]:
        n = len(nums)
        ans = []

        freq = defaultdict(int)          # element -> frequency

        # Min‑heap for 'large' (top‑x)
        large = []          # (freq, value)
        # Max‑heap for 'small' (everything else)
        small = []          # (-freq, -value)
        sum_large = 0
        size_large = 0

        def clean(heap, invert):
            """Pop stale entries whose frequency no longer matches."""
            while heap:
                f, v = heap[0]
                real_f = freq.get(v, 0)
                if real_f == 0 or real_f != f:
                    heapq.heappop(heap)                      # discard stale
                else:
                    break

        def rebalance():
            nonlocal sum_large, size_large
            # move up from small to large if we have space
            while size_large < x and small:
                f, v = heapq.nlargest(1, small)[0]
                heapq.heappop(small)
                heapq.heappush(large, (f, v))
                sum_large += f * v
                size_large += 1

            # demote weakest large if we have too many
            while size_large > x and large:
                f, v = heapq.nsmallest(1, large)[0]
                heapq.heappop(large)
                sum_large -= f * v
                heapq.heappush(small, (-f, -v))
                size_large -= 1

            # promote strongest small if it beats weakest large
            while small and large:
                # strongest in small: largest (-f, -v)
                sf, sv = -small[-1][0], -small[-1][1]
                lf, lv = large[0]                       # weakest large
                if sf > lf or (sf == lf and sv > lv):
                    # demote weakest large
                    heapq.heappop(large)
                    sum_large -= lf * lv
                    heapq.heappush(small, (-lf, -lv))
                    # promote strongest small
                    heapq.heappop(small)
                    heapq.heappush(large, (sf, sv))
                    sum_large += sf * sv
                else:
                    break

        # ---------- Main sliding window ----------
        for i in range(k):
            v = nums[i]
            freq[v] += 1
            heapq.heappush(large if len(large) < x else small,
                           (freq[v], v))

        # After first window, build heaps correctly
        for v, f in freq.items():
            if f > 0:
                heapq.heappush(large if len(large) < x else small,
                               (f, v))
        rebalance()
        ans.append(int(sum_large))

        for i in range(k, n):
            # shift window
            old = nums[i - k]
            new = nums[i]
            freq[old] -= 1
            freq[new] += 1

            # push new entries (lazy deletion will handle stale ones)
            if freq[old] == 0:
                del freq[old]
            heapq.heappush(large if len(large) < x else small,
                           (freq.get(old, 0), old))
            heapq.heappush(large if len(large) < x else small,
                           (freq[new], new))

            rebalance()
            ans.append(int(sum_large))

        return ans
```

> **Warning:** The Python version above is **conceptual** – lazy deletion (`heapq` + frequency map) works but may be a bit slow for large `n`. In practice you can replace the two heaps with `bisect.insort` into a sorted list (`sortedcontainers` library) for a cleaner implementation.

---

### 2.3 C++ (multiset + unordered_map)

```cpp
#include <bits/stdc++.h>
using namespace std;

/*
 * LeetCode 3321 – Find X‑Sum of All K‑Long Subarrays II
 */
class Solution {
public:
    struct Pair {
        int val;   // element value
        int freq;  // current frequency

        Pair(int v, int f) : val(v), freq(f) {}
        // ascending by (freq, val)
        bool operator<(const Pair& o) const {
            if (freq != o.freq) return freq < o.freq;
            return val < o.val;
        }
    };

    vector<int> xSum(vector<int>& nums, int k, int x) {
        unordered_map<int, int> freq;
        multiset<Pair> large, small;
        long long sumLarge = 0;
        int n = nums.size();
        vector<int> ans(n - k + 1);

        auto update = [&](int val, int delta) {
            int oldF = freq.count(val) ? freq[val] : 0;
            int newF = oldF + delta;
            if (newF < 0) throw logic_error("negative frequency");

            Pair oldPair(val, oldF), newPair(val, newF);

            if (large.count(oldPair)) {
                large.erase(large.find(oldPair));
                sumLarge -= 1LL * oldF * val;
                if (newF > 0) {
                    large.insert(newPair);
                    sumLarge += 1LL * newF * val;
                }
            } else if (small.count(oldPair)) {
                small.erase(small.find(oldPair));
                if (newF > 0) small.insert(newPair);
            } else {   // first appearance
                if (newF > 0) small.insert(newPair);
            }

            if (newF > 0) freq[val] = newF;
            else freq.erase(val);
        };

        auto rebalance = [&](int x) {
            // fill large if possible
            while (large.size() < (size_t)x && !small.empty()) {
                auto it = prev(small.end());   // strongest in small
                sumLarge += 1LL * it->freq * it->val;
                large.insert(*it);
                small.erase(it);
            }
            // demote weakest large if we have too many
            while (!small.empty() && large.size() > (size_t)x) {
                auto it = small.end(); // strongest small (not needed here)
                // actually we only demote when large > x
                auto itLow = large.begin(); // weakest large
                sumLarge -= 1LL * itLow->freq * itLow->val;
                small.insert(*itLow);
                large.erase(itLow);
            }
            // promote if a stronger small beats a weaker large
            while (!small.empty() && !large.empty()) {
                auto weakLarge = large.begin();
                auto strongSmall = prev(small.end());
                if (strongSmall->freq > weakLarge->freq ||
                   (strongSmall->freq == weakLarge->freq &&
                    strongSmall->val > weakLarge->val)) {
                    sumLarge -= 1LL * weakLarge->freq * weakLarge->val;
                    large.erase(weakLarge);
                    sumLarge += 1LL * strongSmall->freq * strongSmall->val;
                    large.insert(*strongSmall);
                    small.erase(strongSmall);
                } else break;
            }
        };

        // initial window
        for (int i = 0; i < k; ++i) update(nums[i], 1);
        rebalance(x);
        ans[0] = static_cast<int>(sumLarge);

        for (int i = 1; i <= n - k; ++i) {
            update(nums[i - 1], -1);          // remove leftmost
            update(nums[i + k - 1], 1);       // add new rightmost
            rebalance(x);
            ans[i] = static_cast<int>(sumLarge);
        }
        return ans;
    }
};
```

> The C++ version mirrors the Java logic but benefits from `multiset`’s *bidirectional* iterators, making the swap / promote logic succinct.

---

## 3. Key Takeaways for a Production‑Ready Solution

| Language | Data‑structure | Complexity | Strength |
|----------|----------------|------------|----------|
| **Java** | `TreeSet<Pair>` | O(n log k) | Simple invariant maintenance, built‑in ordering. |
| **Python** | `heapq` + `defaultdict` (lazy deletion) | O(n log k) | Works but can be slow; use sorted list if speed matters. |
| **C++** | `multiset<Pair>` | O(n log k) | Full control over iterators, low overhead. |

**What makes the “good” solution**  
- **Predictable log‑n operations** via balanced tree or heap.  
- **Explicit sum maintenance** – avoid recomputing from scratch for each window.  
- **Clear invariant restoration** – keep `|large| == x` with minimal loops.  
- **Avoid excessive temporary objects** – update the data structures in place.

---

### TL;DR

- **Use a balanced tree (`TreeSet` / `multiset`)** in Java, C++, or C++ to keep track of the `k` elements and maintain the top‑x sub‑set.  
- **Update frequencies on each slide**, adjusting the sum on the fly.  
- **Rebalance** when the number of elements in the top‑x set changes or when a stronger element appears in the rest.

This yields an elegant, fast, and maintainable solution for the **“X‑sum of a sliding window”** problem.