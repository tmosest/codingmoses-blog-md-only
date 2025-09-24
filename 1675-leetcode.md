---
title: LeetCode 1675. Minimize Deviation in Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 1675. **Minimize Deviation in Array** – Full Solution Set (Java, Python, C++)  
### 📚  Blog post: “The Good, the Bad, and the Ugly of LeetCode 1675 – How to Nail It in Your Next Interview”

---

### 📌 Problem Recap

> **Goal** – Given an array `nums` of positive integers, you may repeatedly:
> 1. **Divide** an even number by `2`.
> 2. **Multiply** an odd number by `2`.  
> Find the *minimum* possible deviation (max – min) after any number of such operations.

**Constraints**

|  |  |
|---|---|
|`2 ≤ n ≤ 5 * 10⁴`|`1 ≤ nums[i] ≤ 10⁹`|

---

## 🎯  The “Good” – What We Do

1. **Normalize all numbers to be even** –  
   Every odd element must first be doubled. After that operation, the element is even and can be halved any number of times.
2. **Use a max‑heap** –  
   The largest element dictates the current deviation. By repeatedly halving that largest element, we *greedily* reduce the deviation.
3. **Track the current minimum** –  
   Whenever we push a new value into the heap, keep a running minimum so we can compute `max – min` in O(1).

---

## ❌  The “Bad” – Common Pitfalls

| # | Pitfall | Fix |
|---|---------|-----|
| 1 | Forget to double odd numbers first | `if (num % 2 == 1) num *= 2;` |
| 2 | Stop loop too early | Keep halving **while** the popped value is even |
| 3 | Use a min‑heap and pop the wrong element | Use a **max‑heap** (or invert values) |
| 4 | Not updating `minVal` after pushing the halved number | `minVal = min(minVal, newVal);` |
| 5 | Using `int` for 32‑bit overflow | All intermediate results fit in 32‑bit (`≤ 10⁹`) but use `long` for safety in Java/Python |

---

## 🧙‍♂️  The “Ugly” – Edge Cases & Optimization

| Edge | What Happens | Why It Matters |
|------|--------------|----------------|
| `nums` already has all even numbers | No doubling required | O(n) preprocessing |
| Largest element is a power of two | Keeps halving until it becomes odd | Loop exits gracefully |
| `nums` contains 1s | 1 → 2 → 1? No, 1 is odd, double to 2; after halving to 1 again, but we stop when odd | Avoid infinite loop by breaking on odd |
| Very large values (`10⁹`) | `log₂(10⁹) ≈ 30` iterations | Logarithmic factor is negligible |

> **Performance:**  
> *Time*: `O(n log n)` – building the heap dominates.  
> *Space*: `O(n)` – heap stores all numbers.

---

## 📦  Implementation

Below are clean, ready‑to‑paste solutions for **Java, Python, and C++**. Each one follows the exact same algorithm.

---

### 🔤 Java (PriorityQueue – max heap via comparator)

```java
import java.util.*;

class Solution {
    public int minimumDeviation(int[] nums) {
        // max-heap: highest element on top
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        int minVal = Integer.MAX_VALUE;

        // 1. Normalize all numbers to even and push into heap
        for (int num : nums) {
            if ((num & 1) == 1) {          // odd
                num <<= 1;                 // multiply by 2
            }
            maxHeap.offer(num);
            minVal = Math.min(minVal, num);
        }

        int best = Integer.MAX_VALUE;

        // 2. Greedy loop: keep halving the current maximum
        while (!maxHeap.isEmpty()) {
            int curMax = maxHeap.poll();
            best = Math.min(best, curMax - minVal);

            if ((curMax & 1) == 1) {      // odd -> cannot reduce further
                break;
            }

            int reduced = curMax >> 1;    // divide by 2
            minVal = Math.min(minVal, reduced);
            maxHeap.offer(reduced);
        }

        return best;
    }
}
```

---

### 🔤 Python (heapq – use negative values for max heap)

```python
import heapq
from typing import List

class Solution:
    def minimumDeviation(self, nums: List[int]) -> int:
        # Normalize all to even
        max_heap = []
        min_val = float('inf')
        for x in nums:
            if x % 2 == 1:          # odd
                x <<= 1
            heapq.heappush(max_heap, -x)   # negative for max-heap
            min_val = min(min_val, x)

        best = float('inf')
        while max_heap:
            cur_max = -heapq.heappop(max_heap)
            best = min(best, cur_max - min_val)

            if cur_max % 2 == 1:   # odd -> stop
                break

            reduced = cur_max >> 1
            min_val = min(min_val, reduced)
            heapq.heappush(max_heap, -reduced)

        return best
```

---

### 🔤 C++ (std::priority_queue – default max heap)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumDeviation(vector<int>& nums) {
        priority_queue<int> pq;      // max-heap by default
        int minVal = INT_MAX;

        // 1. Convert odds to even and push
        for (int x : nums) {
            if (x & 1) x <<= 1;      // double odd
            pq.push(x);
            minVal = min(minVal, x);
        }

        int best = INT_MAX;

        // 2. Greedy halving
        while (!pq.empty()) {
            int curMax = pq.top(); pq.pop();
            best = min(best, curMax - minVal);

            if (curMax & 1) break;  // odd – cannot halve

            int half = curMax >> 1;
            minVal = min(minVal, half);
            pq.push(half);
        }

        return best;
    }
};
```

---

## 📝 Blog Post – SEO Optimized

> **Title**  
> *Minimize Deviation in Array – LeetCode 1675 | Java | Python | C++ | Interview Strategy | The Good, The Bad, and The Ugly*

> **Meta Description**  
> Master LeetCode 1675 (Minimize Deviation in Array) with clean Java, Python, and C++ solutions. Learn the greedy + heap approach, pitfalls to avoid, and interview insights that can land you your next coding job.

> **Keywords**  
> LeetCode 1675, Minimize Deviation in Array, coding interview, Java solution, Python solution, C++ solution, priority queue, greedy algorithm, job interview prep, algorithm interview question

---

### 1️⃣ Problem Overview

*What you’re asked to do:*  
Transform the array by doubling odds and halving evens, so that the final **deviation** (max – min) is as small as possible.  
Why it matters:  
It tests your ability to use greedy strategies, heaps, and to reason about number properties.

---

### 2️⃣ The “Good” – Strategy That Works

1. **Normalize to even** – double odd numbers.  
2. **Keep the largest value in a max‑heap** – this gives O(log n) extraction.  
3. **Track the minimum** – update it whenever a smaller number enters the heap.  
4. **Iteratively halve the current maximum** – this is the only move that can reduce deviation.

> **Why it’s greedy?**  
> Each halving strictly decreases the maximum, and we never increase the minimum except when we halve a number that becomes smaller. The process stops when the maximum becomes odd, meaning it can’t be reduced further.

---

### 3️⃣ The “Bad” – Common Mistakes

| Mistake | Fix | Hint |
|---------|-----|------|
| Not double all odds first | `if (num % 2 == 1) num *= 2;` | Missed “odd → even” step. |
| Using a min‑heap for the *max* element | Use a max‑heap or push negatives | Extracting the wrong element. |
| Breaking on the wrong condition | Continue while `curMax % 2 == 0` | The loop must run until the largest element is odd. |
| Forgetting to update `minVal` | `minVal = min(minVal, newVal);` | Deviation calculation fails. |

---

### 4️⃣ The “Ugly” – Edge Cases & Optimization

| Edge | What to watch for | Solution tip |
|------|-------------------|--------------|
| Array all even | No doubling needed | Still run the same code – the `if` guard skips. |
| Largest number is a power of two | Keeps halving until odd | `log₂(10⁹) ≈ 30` iterations – trivial overhead. |
| Contains `1` | `1 → 2 → 1` would loop infinitely | Break when odd after halving. |
| Very large numbers (`10⁹`) | 30 halvings at most | Log factor negligible, algorithm still `O(n log n)`.

---

### 4️⃣ Full Solutions – Ready for Copy‑Paste

(Show the Java/Python/C++ code snippets above – one for each language.)

---

### 5️⃣ Interview Tips

| Tip | Reason |
|-----|--------|
| Explain the **odd → even → halving** transformation before coding | Sets the problem context. |
| Talk about **heap operations** and why a max‑heap is required | Demonstrates data‑structure knowledge. |
| Discuss **early stopping** (when max is odd) | Shows you think about algorithm termination. |
| Mention the **time/space complexity** up front | Signals you understand performance trade‑offs. |
| Show a small test case by hand | Proves you understand the logic. |

---

### 6️⃣ Wrap‑Up & Job‑Ready Takeaways

- The algorithm is a textbook **greedy + heap** problem – perfect for “coding interview” questions.  
- Mastering it shows you can *transform* number properties into data‑structure actions, a key skill for backend, systems, and algorithmic roles.  
- The code above (Java, Python, C++) is concise, well‑commented, and passes all LeetCode tests.

> **Want to impress hiring managers?**  
> Include this problem (and its variations) in your GitHub repo, write a quick blog post (like the one above) and reference it in your résumé. Recruiters love clean, self‑contained solutions that demonstrate a deep understanding of fundamentals.

---

💡 **Final Thought** – The real interviewers are not just looking for the *correct* answer, they’re looking for **how you reason**. Show them that you can:

- Identify a greedy invariant.  
- Pick the right data structure (max‑heap).  
- Handle all edge cases without over‑complicating.

Good luck, and may your next interview be a *minimum deviation* away from success! 🚀

---