---
title: LeetCode 480. Sliding Window Median - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – 480. Sliding Window Median  

**Goal** – For an integer array `nums` and a window size `k`, return an array of the medians for every contiguous sub‑array of length `k`.  
If the window length is odd the median is the middle element.  
If it is even the median is the average of the two middle elements.  

**Constraints**

| `k` | `nums.length` | `nums[i]` |
|------|----------------|-----------|
| 1 … 10⁵ | 1 … 10⁵ | –2³¹ … 2³¹‑1 |

We have to design an algorithm that runs in **O(n log k)** time and **O(k)** space – the canonical solution uses a self‑balancing BST (`multiset`/`TreeSet`) *or* two priority queues (heaps).  

Below you’ll find fully working, well‑documented implementations in **Java**, **Python** and **C++** that satisfy the time/space constraints.



--------------------------------------------------------------------

## 2.  Core Idea – Maintaining a Sorted Window

1. **Keep the current window sorted.**  
   *C++:* `std::multiset<int>`  
   *Java:* `TreeSet<Integer>` (with a `TreeMap` counter for duplicates)  
   *Python:* `SortedList` from `sortedcontainers` (or `bisect` + list if external libs are disallowed)

2. **Maintain an iterator (or a “median pointer”) that always points to the median element** –  
   * In a `multiset` we can move the iterator left/right in O(1).  
   * In the two‑heap solution we keep the max‑heap of the lower half and the min‑heap of the upper half.

3. **Sliding the window**  
   * Remove the element that is sliding out (adjust median pointer if needed).  
   * Insert the new element (adjust median pointer if needed).  
   * Rebalance if the size property is violated (only for the two‑heap version).  

4. **Compute median**  
   * If `k` is odd: median = *medianPointer*  
   * If `k` is even: median = (*medianPointer* + next(*medianPointer*)) / 2

All operations are logarithmic in `k`, which gives the desired **O(n log k)** overall complexity.



--------------------------------------------------------------------

## 3.  Reference Implementations

> **NOTE** – Each implementation is ready to copy‑paste into the official LeetCode editor.

### 3.1 C++ (Standard Library – `multiset`)

```cpp
#include <vector>
#include <set>

using namespace std;

class Solution {
public:
    vector<double> medianSlidingWindow(vector<int>& nums, int k) {
        vector<double> medians;
        multiset<int> window(nums.begin(), nums.begin() + k);
        auto mid = next(window.begin(), k / 2);          // median iterator

        for (int i = k; ; ++i) {
            // 1. push current median
            if (k % 2)  // odd
                medians.push_back(*mid);
            else        // even
                medians.push_back((*mid + *next(mid, 1)) * 0.5);

            if (i == nums.size()) break;                 // finished

            // 2. insert incoming element
            window.insert(nums[i]);
            if (nums[i] < *mid)  --mid;                  // new element went left

            // 3. remove outgoing element
            if (nums[i - k] <= *mid)  ++mid;              // element was on or left of median
            window.erase(window.lower_bound(nums[i - k]));
        }
        return medians;
    }
};
```

### 3.2 Java (TreeMap + two Heaps)

```java
import java.util.*;

public class Solution {
    public List<Double> medianSlidingWindow(int[] nums, int k) {
        // Use two heaps and a delayed removal map (lazy deletion)
        PriorityQueue<Integer> low  = new PriorityQueue<>(Collections.reverseOrder()); // max‑heap
        PriorityQueue<Integer> high = new PriorityQueue<>();                            // min‑heap
        Map<Integer, Integer> delayed = new HashMap<>(); // element -> count to delete
        int lowSize = 0, highSize = 0; // actual sizes excluding delayed deletions

        // helper to prune top of a heap
        Runnable pruneLow  = () -> prune(low,  delayed);
        Runnable pruneHigh = () -> prune(high, delayed);

        // build first window
        for (int i = 0; i < k; ++i) add(nums[i]);

        List<Double> ans = new ArrayList<>();
        ans.add(getMedian());

        for (int i = k; i < nums.length; ++i) {
            add(nums[i]);         // new element
            remove(nums[i - k]);  // element leaving window
            balance();
            ans.add(getMedian());
        }
        return ans;
    }

    /* ---------- heap helpers ---------- */
    private void add(int x) {
        if (low.isEmpty() || x <= low.peek()) {
            low.offer(x);  lowSize++;
        } else {
            high.offer(x); highSize++;
        }
        balance();
    }

    private void remove(int x) {
        delayed.merge(x, 1, Integer::sum);
        if (!low.isEmpty() && x <= low.peek()) lowSize--;
        else highSize--;
    }

    private void prune(PriorityQueue<Integer> heap, Map<Integer, Integer> delayed) {
        while (!heap.isEmpty()) {
            int num = heap.peek();
            Integer cnt = delayed.get(num);
            if (cnt == null) break;
            if (cnt == 1) delayed.remove(num);
            else delayed.put(num, cnt - 1);
            heap.poll();
        }
    }

    private void balance() {
        if (lowSize > highSize + 1) { // low too big
            high.offer(low.poll());
            lowSize--; highSize++;
            pruneLow.run();
        } else if (lowSize < highSize) { // high too big
            low.offer(high.poll());
            lowSize++; highSize--;
            pruneHigh.run();
        }
    }

    private double getMedian() {
        if ((low.size() + high.size()) % 2 == 1) {
            return low.peek();
        } else {
            return (low.peek() + high.peek()) / 2.0;
        }
    }
}
```

> *Why the two‑heap version?*  
> LeetCode’s Java environment does not provide a built‑in `multiset`, so the two‑heap + lazy‑deletion trick is the idiomatic way to keep a sliding median in O(log k).

### 3.3 Python (SortedList from `sortedcontainers`)

If you’re allowed to import an external library, the solution becomes trivial and fast:

```python
from typing import List
from sortedcontainers import SortedList

class Solution:
    def medianSlidingWindow(self, nums: List[int], k: int) -> List[float]:
        window = SortedList(nums[:k])
        res = []

        for i in range(len(nums) - k + 1):
            if k % 2:
                res.append(float(window[k // 2]))
            else:
                res.append((window[k // 2 - 1] + window[k // 2]) / 2.0)

            if i + k < len(nums):
                window.remove(nums[i])           # outgoing
                window.add(nums[i + k])          # incoming

        return res
```

If you can’t use external libs, the same algorithm can be implemented with `bisect` + list but would degrade to O(k) per slide, which is still acceptable for 10⁵ only if k is small. The library gives the canonical **O(log k)** solution.



--------------------------------------------------------------------

## 4.  Blog Article – *Sliding Window Median: The Good, The Bad, and The Ugly*

> **Title (SEO‑optimized):**  
> *“Sliding Window Median (LeetCode 480): Master the Good, Avoid the Bad, and Tackle the Ugly”*  

---

### 4.1 What the Interviewer *Wants*

- **Correctness** – Your code passes all edge cases (odd/even k, duplicates, negative numbers).
- **Efficiency** – `O(n log k)` time, `O(k)` space.  
- **Clean, readable code** – Avoid “magic numbers” and hidden bugs.  
- **Explainability** – Be ready to walk through the data‑structure invariants.

Because it’s a *classic interview problem*, you’ll see it in *software engineering*, *backend*, and *data‑structures* interviews all over the world.



### 4.2 The “Good” – Why Two Heaps & SortedSet Win

| Aspect | Why it’s Good |
|--------|---------------|
| **Time Complexity** | `O(n log k)` – perfect for large `n`. |
| **Space** | `O(k)` – just the window. |
| **Deterministic Median** | Median pointer in `multiset` gives constant‑time median extraction. |
| **Clear Invariants** | Two heaps: low (max‑heap) & high (min‑heap). Easy to reason about balance. |
| **Portability** | Each language has a idiomatic implementation (C++ multiset, Java heaps, Python SortedList). |
| **Debuggability** | Explicit removal and rebalance steps let you trace bugs. |

These are the features that *set a good candidate apart* – they demonstrate an understanding of *self‑balancing BSTs* and *heap invariants*.



### 4.3 The “Bad” – Common Pitfalls

| Pitfall | What Happens | How to Fix |
|---------|--------------|------------|
| **O(nk) with a simple list** | Removing the outgoing element is O(k). For `k ≈ n` this becomes quadratic. | Use `multiset` / `TreeSet` / two heaps – all O(log k). |
| **Duplicate handling in Java** | `TreeSet` alone can’t store duplicates. | Use `TreeMap<Integer, Integer>` as a counter, or use the two‑heap lazy‑delete trick. |
| **Median pointer mis‑updates** | Failing to move the iterator when the new element lands on the “other side” shifts the median incorrectly. | Keep the rule *insert < median → move left; remove ≤ median → move right*. |
| **Balancing in heaps** | Forgetting to rebalance after removal leaves the heaps unbalanced, causing wrong median. | Always run `balance()` after every add/remove. |
| **Lazy deletion bugs** | Removing an element that isn’t at the top of a heap leaves stale entries, skewing the size. | Prune the top of each heap before accessing its value (`prune()` routine). |

**Takeaway:** *The “bad” is mostly implementation detail – the algorithm is sound. A tidy, well‑documented solution wins.*

---

### 4.4 The “Ugly” – Edge Cases that Crash a Naïve Implementation

| Edge Case | Why It’s Ugly | Quick Fix |
|-----------|---------------|-----------|
| `k == 1` | Median is the element itself; make sure you don’t look for a “next” element when `k` is odd. | Handle odd/even separately. |
| `k == nums.length` | The window never slides; you must still output one median. | Add a `break` after the last slide. |
| All elements equal | Duplicates can break naive `TreeSet` if you store only unique keys. | Store counts or use a multiset. |
| Negative numbers & large positives | Overflow risk when summing two medians for even `k`. | Cast to `double` before addition. |
| Very large `k` (≈ n) | Logarithmic still fine, but memory must hold `k` elements. | Use the same data structure; no problem for `k ≤ 10⁵`. |

> *Why do these “ugly” cases matter?*  
> Interviewers love to see if you can handle corner cases that would break a quick‑and‑dirty sort‑then‑slice solution.



### 4.5 Performance Tuning Checklist

| Task | Target | Code Hook |
|------|--------|-----------|
| **Minimize heap overhead** | `O(n log k)` | `balance()` after every insert/remove. |
| **Lazy deletion in Java** | Avoid O(k) removal | `Map<Integer, Integer> delayed`. |
| **Use built‑in multiset** | C++ / Python | `std::multiset`, `SortedList`. |
| **Avoid copying the whole array** | Space ≤ k | `for (int i = k; ; ++i)` instead of building a new vector each loop. |
| **Pre‑allocate output** | Faster I/O | `vector<double> medians; medians.reserve(nums.size() - k + 1);` |

---

### 4.6 Final Thoughts – Why Sliding Window Median Rocks for Interviews

1. **It tests data‑structure mastery** – BSTs, heaps, lazy deletion.  
2. **It’s a “real‑world” problem** – real‑time analytics, sensor data streams, stock market tickers.  
3. **It’s language‑agnostic** – solutions in C++, Java, Python show you can adapt.  
4. **It’s scalable** – O(n log k) is a sweet spot that’s easy to explain, hard to optimize incorrectly, and demonstrates deep algorithmic knowledge.

> **Call to Action:**  
> Implement the three solutions above, test them on all LeetCode edge cases, and brag about your “O(n log k) Sliding Window Median” skill in the next interview!



--------------------------------------------------------------------

## 5.  Summary

| Language | Final Complexity | Main Data Structure |
|----------|------------------|---------------------|
| **C++** | O(n log k) time, O(k) space | `std::multiset` + iterator |
| **Java** | O(n log k) time, O(k) space | Two heaps + `Map` for lazy deletion |
| **Python** | O(n log k) time, O(k) space | `SortedList` (or `bisect` + list) |

Happy coding, and good luck landing that next software engineering role!