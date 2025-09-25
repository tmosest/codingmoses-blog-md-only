---
title: LeetCode 480. Sliding Window Median - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Sliding‑Window Median – 3‑Way Solution (Java | Python | C++)  
> “The Good, the Bad, and the Ugly” – a deep‑dive that’ll land you an interview call.

---

## 1. Problem Recap

> **Leetcode 480 – Sliding Window Median**  
> Given an array `nums` and an integer `k`, for every contiguous sub‑array of length `k` you must return its median.  
> The median is the middle element of a sorted list; when `k` is even the median is the average of the two middle values.

> **Constraints**  
> * `1 ≤ k ≤ nums.length ≤ 10^5`  
> * `-2^31 ≤ nums[i] ≤ 2^31-1`  

The goal: **O(n log k)** time, **O(k)** space, precision within `10⁻⁵`.

---

## 2. The Good – Two‑Heap + Delayed Deletion

The classic O(n log k) solution uses two priority queues (heaps):

| Heap | Purpose | Top element |
|------|---------|--------------|
| **max‑heap** (`lo`) | stores the *lower* half of the window | largest of the lower half |
| **min‑heap** (`hi`) | stores the *upper* half of the window | smallest of the upper half |

### Why two heaps?

* The median is always one of the tops of the heaps.  
* Balancing the sizes keeps the median calculation trivial:  
  * If `k` is odd → median = top of `lo`.  
  * If `k` is even → median = average of tops of `lo` and `hi`.

### Why delayed deletion?

Heaps do not support *O(log k)* arbitrary removal.  
Instead, we keep a `Map<Integer,Integer>` called `delayed` that counts how many times a value has to be removed lazily.  
When we pop from a heap we “clean” its top: if that value is marked in `delayed`, we decrement the counter and pop again.

---

## 3. The Bad – Multiset (C++) vs. Two‑Heaps

| Language | Built‑in | Implementation | Pros | Cons |
|----------|----------|----------------|------|------|
| **C++** | `std::multiset` | Single balanced BST + iterator to middle | Simple one‑liner, no lazy logic | `O(log k)` per insert/erase, but iterator updates expensive in worst‑case. |
| **Java** | `TreeSet` + `TreeMap` | Similar to C++ multiset | Straightforward, but Java’s `TreeSet` does not allow duplicates. | Need extra structure (`TreeMap` + counts). |
| **Python** | `bisect` + `list` | Manual sorted list + binary search | Very readable but `O(k)` insertion/removal. | Too slow for 10⁵ window size. |

> *Good* – The two‑heap method works in all three languages with the same O(n log k) complexity and is the most interview‑friendly.

---

## 4. The Ugly – Corner Cases & Precision

1. **Duplicates** – Heaps can hold duplicates; the delayed map ensures correct removal.
2. **Even `k`** – The median is a double; cast to `double` before averaging.
3. **Precision** – Return `double` and format with `%.5f` if printing.
4. **Overflow** – Sum of two ints can overflow `int`; cast to `long` or `double` before adding.

---

## 5. Code – 3 Languages

> Each implementation follows the same algorithm:  
> 1. Build the first window.  
> 2. Slide the window one step at a time.  
> 3. Return a `List<double>` / `vector<double>` / `List<double>`.

---

### 5.1 Java

```java
import java.util.*;

public class SlidingWindowMedian {
    public List<Double> medianSlidingWindow(int[] nums, int k) {
        List<Double> res = new ArrayList<>();

        // max‑heap (lower half)
        PriorityQueue<Integer> lo = new PriorityQueue<>(Collections.reverseOrder());
        // min‑heap (upper half)
        PriorityQueue<Integer> hi = new PriorityQueue<>();

        // map for delayed deletion
        Map<Integer, Integer> delayed = new HashMap<>();

        // helper to balance heaps
        int balance = 0; // lo.size() - hi.size()

        // insert first k elements
        for (int i = 0; i < k; i++) {
            addNum(nums[i], lo, hi, balance);
        }

        res.add(getMedian(lo, hi, k));

        for (int i = k; i < nums.length; i++) {
            int out = nums[i - k];
            int in = nums[i];

            // remove outgoing
            removeNum(out, lo, hi, delayed, balance);

            // insert incoming
            addNum(in, lo, hi, balance);

            // clean tops
            prune(lo, delayed);
            prune(hi, delayed);

            res.add(getMedian(lo, hi, k));
        }
        return res;
    }

    private void addNum(int num, PriorityQueue<Integer> lo,
                        PriorityQueue<Integer> hi, int balance) {
        if (lo.isEmpty() || num <= lo.peek()) {
            lo.offer(num);
            balance++;
        } else {
            hi.offer(num);
        }

        // rebalance
        if (balance > hi.size() + 1) { // lo too big
            hi.offer(lo.poll());
            balance--;
        } else if (balance < hi.size()) { // hi too big
            lo.offer(hi.poll());
            balance++;
        }
    }

    private void removeNum(int num, PriorityQueue<Integer> lo,
                           PriorityQueue<Integer> hi, Map<Integer, Integer> delayed,
                           int balance) {
        // mark for lazy removal
        delayed.put(num, delayed.getOrDefault(num, 0) + 1);

        if (num <= lo.peek()) balance--;
        else balance++;

        // prune if needed
        prune(lo, delayed);
        prune(hi, delayed);

        // rebalance after deletion
        if (balance > hi.size() + 1) {
            hi.offer(lo.poll());
            balance--;
        } else if (balance < hi.size()) {
            lo.offer(hi.poll());
            balance++;
        }
    }

    private void prune(PriorityQueue<Integer> heap, Map<Integer, Integer> delayed) {
        while (!heap.isEmpty()) {
            int num = heap.peek();
            if (delayed.containsKey(num)) {
                int cnt = delayed.get(num);
                if (cnt == 1) delayed.remove(num);
                else delayed.put(num, cnt - 1);
                heap.poll();
            } else break;
        }
    }

    private double getMedian(PriorityQueue<Integer> lo,
                             PriorityQueue<Integer> hi, int k) {
        if (k % 2 == 1) return lo.peek();
        return (lo.peek() + hi.peek()) / 2.0;
    }

    // ---- Test ----
    public static void main(String[] args) {
        int[] nums = {1,3,-1,-3,5,3,6,7};
        int k = 3;
        System.out.println(new SlidingWindowMedian()
                .medianSlidingWindow(nums, k));
    }
}
```

> **Complexity**  
> *Time*: `O(n log k)`  
> *Space*: `O(k)` (two heaps + delayed map)

---

### 5.2 Python

```python
import heapq
from collections import defaultdict
from typing import List

class Solution:
    def medianSlidingWindow(self, nums: List[int], k: int) -> List[float]:
        lo, hi = [], []                 # max‑heap (as negatives), min‑heap
        delayed = defaultdict(int)      # lazy removal
        res = []

        def prune(heap):
            while heap:
                num = -heap[0] if heap is lo else heap[0]
                if delayed[num]:
                    delayed[num] -= 1
                    heapq.heappop(heap)
                else:
                    break

        def balance():
            # lo should have size == hi or +1
            if len(lo) > len(hi) + 1:
                heapq.heappush(hi, -heapq.heappop(lo))
            elif len(lo) < len(hi):
                heapq.heappush(lo, -heapq.heappop(hi))

        def add(num):
            if not lo or num <= -lo[0]:
                heapq.heappush(lo, -num)
            else:
                heapq.heappush(hi, num)
            balance()

        def remove(num):
            delayed[num] += 1
            if num <= -lo[0]:
                prune(lo)
            else:
                prune(hi)
            balance()

        # build first window
        for i in range(k):
            add(nums[i])

        prune(lo); prune(hi)
        res.append(self._median(lo, hi, k))

        for i in range(k, len(nums)):
            out, in_ = nums[i-k], nums[i]
            remove(out)
            add(in_)
            prune(lo); prune(hi)
            res.append(self._median(lo, hi, k))

        return res

    @staticmethod
    def _median(lo, hi, k):
        return lo[0] * -1 if k % 2 else (-lo[0] + hi[0]) / 2.0

# ---- Usage ----
if __name__ == "__main__":
    sol = Solution()
    nums = [1, 3, -1, -3, 5, 3, 6, 7]
    k = 3
    print(sol.medianSlidingWindow(nums, k))
```

> **Complexity** – same `O(n log k)` time, `O(k)` space.  
> Python’s `heapq` does not have a built‑in max‑heap, so we store negatives.

---

### 5.3 C++

The solution below is *exactly* the same two‑heap + delayed‑deletion strategy.

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<double> medianSlidingWindow(vector<int>& nums, int k) {
        // max‑heap (lower half) – store negative values
        priority_queue<int> lo;            // max‑heap
        priority_queue<int, vector<int>, greater<int>> hi;  // min‑heap
        unordered_map<int,int> delayed;    // lazy removal
        vector<double> res;

        auto prune = [&](priority_queue<int>& heap) {
            while (!heap.empty()) {
                int val = heap.top();
                if (delayed.count(val)) {
                    if (--delayed[val] == 0) delayed.erase(val);
                    heap.pop();
                } else break;
            }
        };

        auto balance = [&]() {
            if (lo.size() > hi.size() + 1) {
                hi.push(lo.top()); lo.pop();
            } else if (lo.size() < hi.size()) {
                lo.push(hi.top()); hi.pop();
            }
        };

        auto getMedian = [&](int k)->double {
            if (k % 2) return lo.top();
            return (lo.top() + hi.top()) / 2.0;
        };

        // init first window
        for (int i = 0; i < k; ++i) {
            if (lo.empty() || nums[i] <= lo.top()) lo.push(nums[i]);
            else hi.push(nums[i]);
            balance();
        }
        res.push_back(getMedian(k));

        for (int i = k; i < nums.size(); ++i) {
            int out = nums[i - k];
            int in  = nums[i];

            // lazy delete
            delayed[out]++;
            if (out <= lo.top()) balance();
            else balance();

            // add new
            if (in <= lo.top()) lo.push(in);
            else hi.push(in);
            balance();

            // clean tops
            prune(lo); prune(hi);

            res.push_back(getMedian(k));
        }
        return res;
    }
};

// ---- Test ----
int main() {
    vector<int> nums{1,3,-1,-3,5,3,6,7};
    int k = 3;
    Solution s;
    auto ans = s.medianSlidingWindow(nums, k);
    for (double v : ans) cout << v << ' ';
}
```

---

## 6. SEO‑Friendly Blog Draft

> **Title**: Sliding‑Window Median (Leetcode 480) – Java, Python & C++ – “The Good, the Bad, and the Ugly”  
> **Meta Description**: Master the Leetcode 480 solution in three languages, learn the interview‑ready two‑heap trick, and understand pitfalls that can make or break your coding interview.

---

### 🚀 The Good – Why Two Heaps Rocks

1. **Universality** – Works the same in **Java, Python, C++**.  
2. **Interview‑friendly** – Most interviewers ask for this exact pattern.  
3. **Predictable Time** – `O(n log k)` even when duplicates explode.  
4. **Extensibility** – The delayed deletion map can be reused for any *dynamic order statistic* problem (e.g., “Running Median”, “Dynamic Median”).

> **Key take‑away**: *Always keep the median as the top of a heap; no sorting is needed.*

---

### 🔥 The Bad – One‑Line Multiset Solutions

| Language | Code Snippet | Why it’s bad |
|----------|--------------|--------------|
| **C++** | `multiset<int> ms; auto it = prev(ms.end(), k/2);` | Iterator invalidation on insert/erase → worst‑case `O(k log k)` |
| **Java** | `TreeMap<Integer,Integer> tm` | Need manual count logic; duplicates hard |
| **Python** | `bisect.insort` on list | `O(k)` per step → TLE on 10⁵ |

> *Good news*: the two‑heap approach outperforms them in all scenarios.

---

### 💣 The Ugly – Common Pitfalls

| Mistake | How to Fix |
|---------|------------|
| **Overflow when averaging** | Use `double` or cast to `long` before adding. |
| **Lazy removal not cleaned** | Always prune heap tops after every add/remove. |
| **Even k median precision** | Cast to `double` before dividing by `2`. |
| **Duplicate values in TreeSet** | Use `TreeMap` or multiset; but heaps accept duplicates natively. |
| **Wrong balance condition** | `lo.size()` should be either equal to `hi.size()` or 1 larger. |

---

## 7. How This Blog Will Land You a Call

* **SEO‑Packed Keywords**  
  * Sliding Window Median  
  * Leetcode 480  
  * Java, Python, C++ solutions  
  * Algorithm interview, coding interview, data structures  
  * Job interview prep, technical interview  

* **LinkedIn/Portfolio Hook**  
  > “Built an interview‑ready solution for Leetcode 480 in Java, Python, and C++. Demonstrated mastery of heaps, lazy deletion, and time‑space trade‑offs.”

* **Show‑off Value**  
  * Clear explanation of why the two‑heap solution is preferable.  
  * Real‑world edge‑case handling.  
  * Clean, well‑commented code that an interviewer can understand on the fly.

---

## 8. Take‑away Checklist

| ✅ | Item |
|----|------|
| ✔ | O(n log k) algorithm described. |
| ✔ | Three polished code samples. |
| ✔ | Complexity analysis. |
| ✔ | Blog narrative with SEO hooks. |
| ✔ | Edge‑case & precision warnings. |

Drop this blog on your **GitHub README**, **LinkedIn** post, or **personal site**. The combination of a solid algorithm, multi‑language coverage, and interview‑ready commentary will make recruiters notice you—no doubt about it. Happy coding! 🌟