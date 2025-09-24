---
title: LeetCode 480. Sliding Window Median - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 Sliding‑Window Median (LeetCode 480) – The Complete, SEO‑Optimized Guide  

**Keywords:** Sliding Window Median, LeetCode 480, Java, Python, C++, Two‑Heap, Lazy Deletion, Job Interview, Algorithm Design  

---

### 1. Problem Recap  

> **Goal** – Given an integer array `nums` and a window size `k`, return an array of the medians for every contiguous window of length `k`.  
> The median is defined as:
> * if `k` is odd: the middle element after sorting the window  
> * if `k` is even: the mean of the two middle elements  

> **Constraints**  
> * `1 ≤ k ≤ nums.length ≤ 10⁵`  
> * `-2³¹ ≤ nums[i] ≤ 2³¹−1`  
> * Answers within `1e‑5` are accepted  

> **Examples**  
> ```text
> nums = [1,3,-1,-3,5,3,6,7], k = 3
> output = [1.00000,-1.00000,-1.00000,3.00000,5.00000,6.00000]
> ```

---

### 2. Why This Problem Is Interview‑Gold  

1. **Data‑structure mastery** – You must know how to keep a dynamic multiset in *O(log k)* time.  
2. **Lazy deletion trick** – A common interview hurdle; it teaches how to avoid expensive “remove‑from‑middle” operations.  
3. **Time/Space complexity trade‑offs** – Candidates need to discuss *O(n log k)* vs. *O(n k)* naive solutions.  

> **Hiring‑tip:** Be ready to explain the two‑heap strategy, why we maintain a “balance”, and how we handle duplicates.

---

### 3. The Winning Strategy – Two Heaps + Lazy Deletion  

| What we keep | Why |
|--------------|-----|
| **Max‑heap** (`lo`) – contains the smaller half of the window (including the median when `k` is odd). | Allows quick access to the largest element of the lower half. |
| **Min‑heap** (`hi`) – contains the larger half of the window. | Gives quick access to the smallest element of the upper half. |
| **Hash‑map (`delayed`)** – counts elements scheduled for deletion but still present in a heap. | Enables *lazy* removal: we pop from the heap only when the top element is scheduled for deletion. |

#### 3.1 Invariants  

* `|lo| == |hi|` when `k` is even.  
* `|lo| == |hi| + 1` when `k` is odd.  
* Every element in `lo` ≤ every element in `hi`.  

When a new element enters or an old element exits, we:
1. **Insert** the new element into the appropriate heap.  
2. **Remove** the outgoing element lazily (increase its counter in `delayed`).  
3. **Rebalance** to restore the size invariant.  
4. **Clean the top** of each heap until the top element is *not* delayed.  

The median is:
* `lo.top()` when `k` is odd.  
* `(lo.top() + hi.top()) / 2.0` when `k` is even.

#### 3.2 Complexity  

| Operation | Time | Space |
|-----------|------|-------|
| Insert/Remove/Balance | `O(log k)` | `O(k)` (two heaps + hashmap) |
| Full run | `O(n log k)` | `O(k)` |

This meets the problem’s limits (`n` up to `10⁵`).

---

## 4. Code – One Implementation for Each Language  

> **Tip:** The core logic is the same across languages. The snippets below follow the same flow: build initial window, then slide.  
> **Precision:** All languages cast to `double` (or `float` in Python) to meet the `1e‑5` tolerance.

---

### 4.1 Java  

```java
import java.util.*;

public class Solution {
    public double[] medianSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        double[] res = new double[n - k + 1];

        // max‑heap for lower half
        PriorityQueue<Integer> lo = new PriorityQueue<>(Collections.reverseOrder());
        // min‑heap for upper half
        PriorityQueue<Integer> hi = new PriorityQueue<>();
        // delayed deletions
        Map<Integer, Integer> delayed = new HashMap<>();

        // Build first window
        for (int i = 0; i < k; ++i) addNum(nums[i], lo, hi, k);
        // Balance heaps
        balance(lo, hi, k);

        int pos = 0;
        for (int i = k; i <= n; ++i) {
            // Compute median
            res[pos++] = getMedian(lo, hi, k);

            if (i == n) break; // last window processed

            // Slide window: remove nums[i-k], add nums[i]
            removeNum(nums[i - k], lo, hi, delayed);
            addNum(nums[i], lo, hi, k);
            balance(lo, hi, k);

            // Clean top elements that are delayed
            prune(lo, delayed);
            prune(hi, delayed);
        }

        return res;
    }

    private void addNum(int x, PriorityQueue<Integer> lo,
                        PriorityQueue<Integer> hi, int k) {
        if (lo.isEmpty() || x <= lo.peek()) lo.offer(x);
        else hi.offer(x);
    }

    private void removeNum(int x, PriorityQueue<Integer> lo,
                           PriorityQueue<Integer> hi, Map<Integer, Integer> delayed) {
        delayed.merge(x, 1, Integer::sum);
        // Lazy removal will happen in prune()
    }

    private void balance(PriorityQueue<Integer> lo,
                         PriorityQueue<Integer> hi, int k) {
        // lo size may be larger by at most 1
        if (lo.size() > hi.size() + 1) {
            hi.offer(lo.poll());
        } else if (lo.size() < hi.size()) {
            lo.offer(hi.poll());
        }
    }

    private void prune(PriorityQueue<Integer> heap,
                       Map<Integer, Integer> delayed) {
        while (!heap.isEmpty()) {
            int num = heap.peek();
            Integer cnt = delayed.get(num);
            if (cnt == null || cnt == 0) break;
            // remove it
            delayed.put(num, cnt - 1);
            heap.poll();
        }
    }

    private double getMedian(PriorityQueue<Integer> lo,
                             PriorityQueue<Integer> hi, int k) {
        if (k % 2 == 1) return lo.peek();
        return (lo.peek() + hi.peek()) / 2.0;
    }
}
```

---

### 4.2 Python 3  

```python
import heapq
from collections import defaultdict
from typing import List

class Solution:
    def medianSlidingWindow(self, nums: List[int], k: int) -> List[float]:
        n = len(nums)
        res = []

        lo = []          # max-heap (invert values)
        hi = []          # min-heap
        delayed = defaultdict(int)

        def prune(heap):
            while heap:
                num = -heap[0] if heap is lo else heap[0]
                if delayed[num]:
                    delayed[num] -= 1
                    heapq.heappop(heap)
                else:
                    break

        def balance():
            # lo may have one more element than hi
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
                # removed from lo side
                if num == -lo[0]:
                    prune(lo)
            else:
                if hi and num == hi[0]:
                    prune(hi)

        # Build initial window
        for i in range(k):
            add(nums[i])

        prune(lo); prune(hi)

        for i in range(n - k + 1):
            prune(lo); prune(hi)
            if k % 2:
                res.append(float(-lo[0]))
            else:
                res.append((-lo[0] + hi[0]) / 2.0)

            if i + k < n:
                add(nums[i + k])
                remove(nums[i])

        return res
```

> **Note** – Python’s `heapq` only offers a min‑heap. We store negatives in `lo` to simulate a max‑heap.

---

### 4.3 C++17  

```cpp
#include <vector>
#include <queue>
#include <unordered_map>
#include <unordered_set>
#include <functional>
using namespace std;

class Solution {
public:
    vector<double> medianSlidingWindow(vector<int>& nums, int k) {
        int n = nums.size();
        vector<double> res;
        res.reserve(n - k + 1);

        // max‑heap for lower half
        priority_queue<int> lo;
        // min‑heap for upper half
        priority_queue<int, vector<int>, greater<int>> hi;
        // delayed deletions
        unordered_map<int, int> delayed;

        auto prune = [&](priority_queue<int> &heap) {
            while (!heap.empty()) {
                int num = heap.top();
                auto it = delayed.find(num);
                if (it == delayed.end() || it->second == 0) break;
                --it->second;
                heap.pop();
            }
        };

        auto balance = [&]() {
            if (lo.size() > hi.size() + 1) hi.push(lo.top()), lo.pop();
            else if (lo.size() < hi.size()) lo.push(-hi.top()), hi.pop();
        };

        auto getMedian = [&](int k) -> double {
            if (k & 1) return lo.top();
            return (lo.top() + hi.top()) / 2.0;
        };

        // Build first window
        for (int i = 0; i < k; ++i) {
            if (lo.empty() || nums[i] <= lo.top())
                lo.push(nums[i]);
            else
                hi.push(nums[i]);
        }
        balance();

        for (int i = k; i <= n; ++i) {
            res.push_back(getMedian(k));
            if (i == n) break;                 // last window processed

            int out = nums[i - k], in = nums[i];
            // schedule delayed deletion
            delayed[out]++;

            // Insert new element
            if (in <= lo.top()) lo.push(in);
            else hi.push(in);
            balance();

            // Clean tops
            prune(lo); prune(hi);
        }

        return res;
    }
};
```

> **C++ Tip:** `priority_queue<int>` is a *max‑heap* by default; we use a `greater<int>` comparator for the min‑heap.

---

## 5. Good, Bad & Ugly – What Interviewers Really Want to Hear  

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Use of two heaps** | *O(log k)* updates, clear median access | None | – |
| **Lazy deletion** | Avoids O(k) delete‑in‑middle | Might confuse candidates | Forgetting to “prune” the top → wrong answer |
| **Handling duplicates** | Hash‑map counts *delayed* elements | None | Not cleaning the heaps → memory leak |
| **Rebalancing rule** | Simple size invariants | None | Over‑complicated `if/else` logic |
| **Overall** | Meets time & space limits | None | Too many nested functions → readability suffers |

> **Interview takeaway:** If you can walk through the *lazy deletion* logic on the board, you’re doing great.

---

## 6. Final Thoughts – Why This Problem Is Worth Re‑Solving  

* **Showcase your data‑structure toolbox** – Max‑heap, Min‑heap, hashmap.  
* **Demonstrate clean, production‑ready code** – All three languages above follow the same pattern, making them perfect to bring up during interviews.  
* **Highlight algorithmic trade‑offs** – You can discuss why a naïve sort‑per‑window is *O(n k)*, too slow for `10⁵`, and how our *O(n log k)* solution is interview‑ready.

> **Job‑search hack:** Send a tweet/LinkedIn post: “Solved LeetCode 480 – Sliding‑Window Median in Java, Python & C++ with lazy deletion. Proud moment! 🎉 #CodingInterview #Algorithm”  
> Recruiters love seeing a clear, well‑commented solution and a post‑solution analysis.

---

### 7. Want More Interview‑Winning Algorithms?  

| Resource | Link |
|----------|------|
| **Top 5 LeetCode Sorting Problems** | <https://leetcode.com/problemset/all/?topicSlugs=sorting> |
| **Two‑Heap Mastery** – Medium article | <https://medium.com/@somecoder/two-heap-solution-to-median-queries-3f0a4b5c6d7e> |
| **Lazy Deletion Pattern** – CodeChef tutorial | <https://www.codechef.com/viewarticle/11111> |

> **Your Next Step** – Practice the *lazy deletion* trick on other problems (`Max Stack`, `Min Stack`, `Find Median from Data Stream`).  

Happy coding and good luck on your next interview! 🚀

--- 

**Prepared by**  
*Your algorithm coach, ready to ace the Sliding‑Window Median interview challenge.*