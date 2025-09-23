---
title: LeetCode 1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1438 – “Longest Continuous Subarray With Absolute Diff ≤ Limit”

Below you’ll find **ready‑to‑copy** solutions in **Java**, **Python** and **C++** that solve the problem in *O(n)* time and *O(n)* extra space using a **sliding‑window + two monotonic deques** approach.  
After the code snippets, read the SEO‑optimized blog article that explains the intuition, the “good / bad / ugly” parts of the problem and how mastering it can boost your interview game.

> **Problem link**: <https://leetcode.com/problems/longest-continuous-subarray-with-absolute-diff-less-than-or-equal-to-limit/>

---

### 🧑‍💻 1️⃣ Java Implementation

```java
import java.util.*;

public class Solution {
    public int longestSubarray(int[] nums, int limit) {
        // Deques to keep the increasing (min) and decreasing (max) values
        Deque<Integer> minDeque = new ArrayDeque<>();
        Deque<Integer> maxDeque = new ArrayDeque<>();

        int left = 0;           // left boundary of the sliding window
        int best = 0;           // longest valid window seen so far

        for (int right = 0; right < nums.length; right++) {
            int cur = nums[right];

            // Maintain minDeque: remove all values larger than cur
            while (!minDeque.isEmpty() && cur < minDeque.peekLast())
                minDeque.pollLast();
            minDeque.offerLast(cur);

            // Maintain maxDeque: remove all values smaller than cur
            while (!maxDeque.isEmpty() && cur > maxDeque.peekLast())
                maxDeque.pollLast();
            maxDeque.offerLast(cur);

            // Shrink window until difference is within limit
            while (maxDeque.peekFirst() - minDeque.peekFirst() > limit) {
                if (nums[left] == minDeque.peekFirst())
                    minDeque.pollFirst();
                if (nums[left] == maxDeque.peekFirst())
                    maxDeque.pollFirst();
                left++;
            }

            best = Math.max(best, right - left + 1);
        }
        return best;
    }
}
```

> **Why it works**  
> - `minDeque` always holds the elements of the current window in *increasing* order, so its front is the minimum.  
> - `maxDeque` holds them in *decreasing* order, so its front is the maximum.  
> - The window is expanded one element at a time (`right`).  
> - If `max - min` exceeds `limit`, the left pointer moves forward, discarding the element that leaves the window.  

---

### 🐍 2️⃣ Python Implementation

```python
from collections import deque
from typing import List

class Solution:
    def longestSubarray(self, nums: List[int], limit: int) -> int:
        min_q, max_q = deque(), deque()
        left = best = 0

        for right, val in enumerate(nums):
            # Keep min_q increasing
            while min_q and val < min_q[-1]:
                min_q.pop()
            min_q.append(val)

            # Keep max_q decreasing
            while max_q and val > max_q[-1]:
                max_q.pop()
            max_q.append(val)

            # Shrink until valid
            while max_q[0] - min_q[0] > limit:
                if nums[left] == min_q[0]:
                    min_q.popleft()
                if nums[left] == max_q[0]:
                    max_q.popleft()
                left += 1

            best = max(best, right - left + 1)
        return best
```

> **Fast & Pythonic** – Uses `deque` (double‑ended queue) for O(1) pops/pushes at both ends.

---

### 🧱 3️⃣ C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        deque<int> minD, maxD;          // monotonic deques
        int left = 0, best = 0;

        for (int right = 0; right < (int)nums.size(); ++right) {
            int cur = nums[right];

            // Build min deque (increasing)
            while (!minD.empty() && cur < minD.back())
                minD.pop_back();
            minD.push_back(cur);

            // Build max deque (decreasing)
            while (!maxD.empty() && cur > maxD.back())
                maxD.pop_back();
            maxD.push_back(cur);

            // Window shrink
            while (maxD.front() - minD.front() > limit) {
                if (nums[left] == minD.front())
                    minD.pop_front();
                if (nums[left] == maxD.front())
                    maxD.pop_front();
                ++left;
            }

            best = max(best, right - left + 1);
        }
        return best;
    }
};
```

> **Note**  
> - `deque` from `<deque>` gives amortised *O(1)* insert/delete from either end.  
> - `vector<int>&` is used for LeetCode’s C++ signature.

---

## 📚 3️⃣ SEO‑Optimized Blog Article

> **Title:**  
> *LeetCode 1438 – Master the “Longest Continuous Subarray” Problem with Sliding Window & Monotonic Deques (Java, Python, C++)*  

> **Meta description:**  
> Learn the O(n) solution to LeetCode 1438. Understand sliding‑window with two deques, debug edge cases, and see how this problem can sharpen your interview skills.  

> **Keywords:**  
> LeetCode 1438, Longest Continuous Subarray, Absolute Difference, Sliding Window, Monotonic Deque, Java solution, Python solution, C++ solution, Interview prep, Data Structures, Algorithms, O(n) time, O(n) space, Array problems, job interview tips.

---

### 🎯 Problem Overview

> **Goal**: Find the length of the longest contiguous subarray such that the difference between its maximum and minimum elements is ≤ `limit`.

| Input | Output |
|-------|--------|
| `nums = [8,2,4,7]`, `limit = 4` | `2` (subarray `[2,4]`) |
| `nums = [10,1,2,4,7,2]`, `limit = 5` | `4` (subarray `[2,4,7,2]`) |

---

### 🔍 Good / Bad / Ugly – What Makes This Problem Challenging?

| **Good** | **Bad** | **Ugly** |
|----------|---------|----------|
| **Clear constraints** – O(n) expected. | **Large value range** – `nums[i]` up to 10⁹, so you cannot rely on bucket/counting sort. | **Subtle window shrink** – need to correctly remove elements that leave the window, not just slide the pointer. |
| **Nice DP / two‑pointer potential** – classic interview theme. | **Requires careful use of data structures** – you need two priority queues or deques, not one. | **Many failing tests** – off‑by‑one bugs, wrong front/back logic, or forgetting to pop deques when window shrinks. |
| **Opportunity to show mastery of monotonic queues** – highly valuable skill. | **Hard to debug** – difference can exceed limit in many ways, making it hard to reason about the window size. | **Time‑complexity traps** – using heaps gives O(n log n) and will TLE on LeetCode. |

---

### 🧠 Intuition

The absolute difference of a subarray equals `max(subarray) - min(subarray)`.  
Thus we need to keep track of the **maximum** and **minimum** values in the current window while sliding it over the array.

A **monotonic deque** keeps the window’s elements sorted in either increasing or decreasing order.  
The front of the deque is always the current min (or max).  
With two deques we can get both extremes in *O(1)* time.

---

### 📈 Algorithm (Sliding Window + Two Monotonic Deques)

1. **Initialize**  
   * `minDeque` – increasing order (front = min).  
   * `maxDeque` – decreasing order (front = max).  
   * `left = 0`, `best = 0`.

2. **For each element `right` (0 … n‑1)**  
   * **Insert** `nums[right]` into both deques, removing all elements that violate monotonicity (back of each deque).  
   * **While** `maxDeque.front() - minDeque.front() > limit`:  
     * Move `left` forward.  
     * If the element leaving the window equals the front of a deque, pop it from that deque.  
   * **Update** `best = max(best, right - left + 1)`.

3. **Return** `best`.

---

### 🏗️ Implementation Highlights

| Language | Key Features |
|----------|--------------|
| **Java** | `Deque<Integer>` via `ArrayDeque`, `peekFirst()`, `peekLast()`, `pollFirst()`, `pollLast()`. |
| **Python** | `collections.deque`, `append()`, `pop()`, `popleft()`. |
| **C++** | `std::deque<int>`, `push_back()`, `pop_back()`, `front()`, `back()`. |

All three solutions run in **O(n)** time because each element is inserted and removed from each deque at most once.  
Space usage is **O(n)** in the worst case (e.g., strictly increasing array for `minDeque`).

---

### 📊 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | *O(n)* | *O(n)* | *O(n)* |
| **Space** | *O(n)* | *O(n)* | *O(n)* |
| **Worst‑case window** | `limit` never satisfied, window shrinks to 1 | Same | Same |

---

### 🛠️ Edge‑Case Checklist

| Edge Case | Why it matters | Solution’s handling |
|-----------|----------------|---------------------|
| Empty array | Should return 0 | Loop never runs → `best` remains 0 |
| `limit` = 0 | Need equal values only | Deques hold identical fronts → difference 0 |
| Single element | Window size 1 | Works naturally |
| All elements equal | Max diff 0 | Deques keep single value → window never shrinks |
| Very large numbers (10⁹) | Potential overflow? | Use `int` in Java/Python, `long long` not required; subtraction stays within 32‑bit signed range |

---

### 🧪 Sample Unit Tests (Python)

```python
if __name__ == "__main__":
    s = Solution()
    assert s.longestSubarray([8,2,4,7], 4) == 2
    assert s.longestSubarray([10,1,2,4,7,2], 5) == 4
    assert s.longestSubarray([4,2,2,2,4,4,2,2], 0) == 3
    assert s.longestSubarray([], 10) == 0
    print("All tests passed!")
```

---

## 📚 4️⃣ SEO‑Optimized Blog Article

---

### **Title**  
*Master LeetCode 1438: Longest Continuous Subarray with Absolute Diff ≤ Limit – Java, Python & C++ Solutions + Interview Insights*

---

### **Meta Description**  
Discover the fastest O(n) solution to LeetCode 1438. Learn how sliding windows and monotonic deques solve the “Longest Continuous Subarray” problem in Java, Python, and C++. Perfect for software‑engineering interview prep and boosting your résumé.

---

### **Keywords**  
LeetCode 1438, Longest Continuous Subarray, Absolute Difference, Sliding Window, Monotonic Deque, Java solution, Python solution, C++ solution, Data Structures, Algorithms, Interview Prep, Software Engineer Interview, Coding Interview Tips

---

### 📖 Introduction

If you’re preparing for a software‑engineering interview, you’ve likely seen array problems that sound simple but require a nuanced understanding of data structures. LeetCode 1438 – “Longest Continuous Subarray” – is a prime example. It asks you to find the maximum length of a contiguous slice of an array such that the difference between the slice’s largest and smallest element is bounded by a given limit.

This problem is deceptively tricky: it blends classic two‑pointer logic with the subtlety of maintaining two extremes efficiently. In this article, we’ll walk through:

1. The core idea behind the optimal solution.  
2. How two monotonic deques maintain the min and max.  
3. A step‑by‑step guide to implementing the algorithm in Java, Python, and C++.  
4. Common pitfalls and debugging strategies.  
5. Why this problem is a golden ticket for interview success.

---

### 1️⃣ The Big Picture

At its heart, LeetCode 1438 is a question of **balance**.  
We want a subarray that stays within a “tolerance band” defined by `limit`.  
The key insight:  
> *The absolute difference of any subarray equals its maximum minus its minimum.*

Hence, if we can track both extremes while sliding a window over the array, we can decide in constant time whether the window is valid.

---

### 2️⃣ Why Monotonic Deques are the Secret Sauce

A **deque** (double‑ended queue) allows insertion/removal at both ends in constant time.  
By enforcing a **monotonic** order:

- **Increasing deque**: The front holds the minimum; all other values are larger.  
- **Decreasing deque**: The front holds the maximum; all other values are smaller.

When we push a new element to the back, we pop from the back until the deque stays monotonic.  
When we slide the window forward, we pop from the front if the outgoing element equals the front of a deque.

This guarantees we always have the correct `min` and `max` for the current window **without any expensive look‑ups**.

---

### 3️⃣ Step‑by‑Step Walkthrough

Consider `nums = [10, 1, 2, 4, 7, 2]` and `limit = 5`.

| Step | `right` | `nums[right]` | Deques after insert | Check | Shrink? |
|------|---------|----------------|---------------------|-------|---------|
| 1 | 0 | 10 | `minD=[10]`, `maxD=[10]` | 0 ≤ 5 → OK | No |
| 2 | 1 | 1 | `minD=[1]`, `maxD=[10]` | 9 > 5 → shrink | Yes (move left) |
| 3 | 2 | 2 | `minD=[1,2]`, `maxD=[10]` | 9 > 5 → shrink | Yes |
| 4 | 3 | 4 | `minD=[1,2,4]`, `maxD=[10]` | 9 > 5 → shrink | Yes |
| 5 | 4 | 7 | `minD=[1,2,4,7]`, `maxD=[10]` | 9 > 5 → shrink | Yes |
| 6 | 5 | 2 | `minD=[2,2,4,7]`, `maxD=[7]` | 5 ≤ 5 → OK | No |

The longest valid window ends up with length 4: `[2,4,7,2]`.

---

### 🔍 Debugging Common Pitfalls

1. **Off‑by‑one errors** – Remember that `right - left + 1` gives the window length.  
2. **Deque pop logic** – Only pop from the front if the outgoing element matches the front.  
3. **Large values** – Avoid `int` overflow; however, with 10⁹ difference stays within 32‑bit signed range.  
4. **Empty array** – Ensure your loop handles `n == 0` gracefully.  
5. **Non‑monotonic insert** – Use `while (deque back > newVal)` for `minD` and `while (deque back < newVal)` for `maxD`.

---

### 🌟 Interview Take‑aways

- **Show Data‑Structure Fluency** – Explaining why two deques beat two heaps demonstrates deep understanding.  
- **Explain Amortized Complexity** – Each element is processed O(1) times → overall O(n).  
- **Edge‑Case Mastery** – Be prepared to talk through all corner cases and how your code covers them.  
- **Talk Through the Code** – While writing, narrate your decisions: “I’m using a monotonic deque to keep the min constant‑time accessible.”

---

### 🚀 Wrap‑Up

LeetCode 1438 is a textbook example of turning a seemingly simple array problem into a sophisticated algorithmic challenge. The O(n) sliding window solution with two monotonic deques is not only efficient but also a showcase of:

- **Data‑structure skill** (deques).  
- **Algorithmic thinking** (maintaining invariants).  
- **Problem‑solving under constraints**.

Mastering this problem boosts your confidence for a range of array and string questions in interviews. Grab your IDE, run the Java/Python/C++ code, and practice writing clean, bug‑free solutions. Happy coding!

--- 

### 👋 Closing

Thanks for reading!  
If you found this article helpful, drop a comment or share it on LinkedIn to help others crack LeetCode 1438.

--- 

--- 

*End of article.*