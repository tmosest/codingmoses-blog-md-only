---
title: LeetCode 1438. Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1438. Longest Continuous Subarray With Absolute Diff ≤ Limit  
**Difficulty** – Medium  
**Tags** – Sliding Window, Deque, Two‑Pointer, Data‑Structures  

---

### 🎯 Problem Recap  

> Given an integer array `nums` and an integer `limit`, return the size of the longest **non‑empty** subarray such that the absolute difference between **any** two elements of this subarray is ≤ `limit`.

The naive O(n²) scan is too slow (`n ≤ 10⁵`). The goal is an O(n) algorithm that works for 32‑bit and 64‑bit numbers.

---

## The “Good” – Why the Deque + Sliding Window Works  

| Why it’s Great | Explanation |
|----------------|-------------|
| **Linear time** | Each element is added and removed at most once from each deque → O(n). |
| **Constant extra space** | Only two deques (max & min) that store indices → O(1) per element. |
| **Clear logic** | The window expands while `max - min ≤ limit`. When it breaks, we shrink the left side. |
| **Handles large values** | Works for 1 ≤ `nums[i]` ≤ 10⁹ and `limit` ≤ 10⁹ (no overflow with long/64‑bit). |

---

## The “Bad” – Things to Watch Out For  

1. **Deque implementation bugs** – forgetting to pop from the front when an index leaves the window.  
2. **Overflow** – in Java `int` can hold up to 2³¹‑1; `nums[i]` can be 10⁹, so `max - min` fits in `int`, but be cautious when `limit` is close to 10⁹.  
3. **Edge cases** – single element array, `limit = 0`, all equal numbers, strictly increasing/decreasing sequences.  

---

## The “Ugly” – Harder Tricky Parts  

* Implementing a **monotonic deque** that maintains a *non‑increasing* (max) or *non‑decreasing* (min) sequence of indices.  
* Ensuring that the window correctly **shrinks** when the condition fails – you must move the left pointer only when the difference exceeds `limit`.  
* Managing large input sizes with fast I/O in Java/C++ (important for coding‑interviews).  

---

## Optimal Algorithm – Sliding Window + Two Monotonic Deques  

1. Keep two deques `maxDeque` (indices of elements in **decreasing** order) and `minDeque` (indices in **increasing** order).  
2. Expand `right` pointer from 0 to `n‑1`.  
3. While inserting `nums[right]`, pop from the back of the corresponding deque all indices whose values are *less* (for max) or *greater* (for min) than the new value.  
4. Push `right` into both deques.  
5. If `nums[maxDeque.peekFirst()] - nums[minDeque.peekFirst()] > limit`, shrink the window from the left: move `left` forward and pop from the front of any deque that contains the old left index.  
6. Update `ans = max(ans, right - left + 1)` at every step.  

---

## Code Solutions

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**. All run in O(n) time and O(n) space (two deques).

### 1. Java

```java
import java.util.Deque;
import java.util.ArrayDeque;

public class Solution {
    public int longestSubarray(int[] nums, int limit) {
        int n = nums.length;
        Deque<Integer> maxD = new ArrayDeque<>();
        Deque<Integer> minD = new ArrayDeque<>();
        int left = 0, ans = 0;

        for (int right = 0; right < n; right++) {
            // maintain max deque (decreasing)
            while (!maxD.isEmpty() && nums[maxD.peekLast()] < nums[right]) {
                maxD.pollLast();
            }
            maxD.offerLast(right);

            // maintain min deque (increasing)
            while (!minD.isEmpty() && nums[minD.peekLast()] > nums[right]) {
                minD.pollLast();
            }
            minD.offerLast(right);

            // shrink window until condition holds
            while (nums[maxD.peekFirst()] - nums[minD.peekFirst()] > limit) {
                left++;
                if (maxD.peekFirst() < left) maxD.pollFirst();
                if (minD.peekFirst() < left) minD.pollFirst();
            }

            ans = Math.max(ans, right - left + 1);
        }
        return ans;
    }
}
```

**Fast I/O tip (optional):** For interviews use `BufferedInputStream` / `FastScanner` to read integers quickly.

### 2. Python

```python
from collections import deque
from typing import List

class Solution:
    def longestSubarray(self, nums: List[int], limit: int) -> int:
        max_d = deque()  # decreasing
        min_d = deque()  # increasing
        left = 0
        ans = 0

        for right, val in enumerate(nums):
            # update max deque
            while max_d and nums[max_d[-1]] < val:
                max_d.pop()
            max_d.append(right)

            # update min deque
            while min_d and nums[min_d[-1]] > val:
                min_d.pop()
            min_d.append(right)

            # shrink if invalid
            while nums[max_d[0]] - nums[min_d[0]] > limit:
                left += 1
                if max_d[0] < left:
                    max_d.popleft()
                if min_d[0] < left:
                    min_d.popleft()

            ans = max(ans, right - left + 1)
        return ans
```

Python’s `deque` operations are O(1). Use `sys.stdin.read()` for large input.

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        deque<int> maxD, minD;  // store indices
        int left = 0, ans = 0;

        for (int right = 0; right < (int)nums.size(); ++right) {
            // maintain decreasing deque for max
            while (!maxD.empty() && nums[maxD.back()] < nums[right])
                maxD.pop_back();
            maxD.push_back(right);

            // maintain increasing deque for min
            while (!minD.empty() && nums[minD.back()] > nums[right])
                minD.pop_back();
            minD.push_back(right);

            // shrink window if needed
            while (nums[maxD.front()] - nums[minD.front()] > limit) {
                ++left;
                if (maxD.front() < left) maxD.pop_front();
                if (minD.front() < left) minD.pop_front();
            }

            ans = max(ans, right - left + 1);
        }
        return ans;
    }
};
```

Compile with `-std=c++17 -O2`.

---

## Testing & Edge Cases

| Input | Output | Reason |
|-------|--------|--------|
| `[8,2,4,7]`, `4` | `2` | Longest subarray `[2,4]` or `[4,7]`. |
| `[10,1,2,4,7,2]`, `5` | `4` | Subarray `[2,4,7,2]`. |
| `[4,2,2,2,4,4,2,2]`, `0` | `3` | Only equal numbers allowed, longest stretch of 3. |
| `[1]`, `0` | `1` | Single element always valid. |
| `[1, 3, 6, 7, 10]`, `3` | `2` | `[6,7]` or `[1,3]`. |

---

## Blog Article: “The Good, The Bad, & The Ugly of LeetCode 1438”

> **Title:**  
> *“Mastering LeetCode 1438: Longest Continuous Subarray with Absolute Diff ≤ Limit – The Good, The Bad, and The Ugly”*  
> *Your Ultimate Interview Power‑Move (Java / Python / C++)*

### Introduction  
If you’re eyeing a software‑engineering role, LeetCode’s *Longest Continuous Subarray With Absolute Diff Less Than or Equal to Limit* (Problem 1438) is a must‑solve. It tests your ability to combine two classic concepts: *sliding window* and *monotonic deque*. In this article, we dissect the problem into its **good**, **bad**, and **ugly** facets, walk through an optimal solution, and deliver ready‑to‑copy code in Java, Python, and C++.

> **SEO Keywords**: LeetCode 1438, Longest Continuous Subarray, sliding window, monotonic deque, Java solution, Python solution, C++ solution, algorithm interview, data structures, job interview, algorithmic challenge.

---

### The Problem (Restated)  
You’re given an array `nums` and a limit `limit`. Return the length of the longest subarray where the difference between the maximum and minimum element is no more than `limit`.  

- `1 ≤ nums.length ≤ 10⁵`  
- `1 ≤ nums[i] ≤ 10⁹`  
- `0 ≤ limit ≤ 10⁹`

---

#### Why It’s Worth Solving  
- **Interview Relevance** – Most technical hiring processes cover sliding windows and deques.  
- **Scalable Logic** – The technique scales to thousands of elements, making it ideal for big‑data use cases.  
- **Language‑agnostic** – The same algorithm works in any language; you just swap data structures.

---

### The “Good” – What Makes It Elegant  

| Feature | Why It’s Good |
|---------|---------------|
| **Linear O(n)** | Each element enters and leaves the deques once. |
| **Monotonic Deques** | Maintain current max/min in O(1) per update. |
| **No Extra Overhead** | Only two deques of indices – constant memory. |
| **Handles Edge Cases Naturally** | Works even if all elements are the same or the array is strictly increasing/decreasing. |

---

### The “Bad” – Common Pitfalls  

1. **Deque Indexing Errors** – Forgetting to remove stale indices when the left pointer advances.  
2. **Integer Overflow** – In languages with 32‑bit integers, be careful when computing `max - min`.  
3. **Complexity Misunderstanding** – Implementing a naive O(n²) approach gets rejected in interviews.

---

### The “Ugly” – Tricky Implementation Details  

- **Maintaining Two Deques Simultaneously** – You must update both the `maxDeque` and `minDeque` for each new element.  
- **Shrinking the Window** – The condition `nums[maxFront] - nums[minFront] > limit` must be re‑evaluated after each shrink.  
- **Fast I/O** – For coding competitions, the default input streams may be too slow for 10⁵ integers.

---

### Walk‑through of the Optimal Algorithm  

1. **Initialize** two empty deques, `maxD` and `minD`.  
2. **Iterate** with a `right` pointer over the array.  
3. **Push** `right` into both deques, popping from the back while maintaining decreasing (for `maxD`) or increasing (for `minD`) order.  
4. **While** the difference between the front elements exceeds `limit`, increment `left` and pop any deque front that equals `left‑1`.  
5. **Record** `right - left + 1` as a candidate answer.  

This *sliding window* strategy ensures that the window is always valid, and the two deques always provide the current extremes.

---

### Code Snippets (Ready to Copy)

#### Java

```java
public class Solution {
    public int longestSubarray(int[] nums, int limit) {
        // ... (see code above)
    }
}
```

#### Python

```python
class Solution:
    def longestSubarray(self, nums, limit):
        # ... (see code above)
```

#### C++

```cpp
class Solution {
public:
    int longestSubarray(vector<int>& nums, int limit) {
        // ... (see code above)
    }
};
```

> **Tip**: Use `Arrays.sort()` or `list.sort()` if you’re testing the solution locally; but never rely on sorting for the actual algorithm.

---

### Final Thoughts  

- Mastering Problem 1438 demonstrates your command over sliding windows and monotonic deques, two staples in the **algorithm interview toolbox**.  
- The **good** parts of the solution—linear time, monotonic deques—are often the very concepts recruiters expect.  
- Understanding the **bad** and **ugly** aspects helps you avoid common traps that would otherwise cost you in a live interview.

Give the code a try on LeetCode, integrate fast I/O for large tests, and you’re all set to impress. Good luck with your next interview!

---

### Conclusion  
LeetCode 1438 is a perfect blend of *conceptual clarity* and *implementation nuance*. By mastering the sliding window + two monotonic deques strategy, you’ll be equipped to solve this problem in **Java**, **Python**, and **C++**, and you’ll have a solid, interview‑ready answer ready to drop into your next coding challenge. Happy coding!

---  

> *Want more interview‑style articles? Subscribe to our newsletter and receive weekly algorithmic challenges delivered straight to your inbox.*  

--- 

### Author  
*Tech Interviewer & Algorithm Enthusiast*  
*Specializing in Java, Python, and C++ interview prep.*  

---

> **Contact**: author@example.com | **LinkedIn**: /in/author | **GitHub**: /author  

--- 

**Happy Solving!**

--- 

### Closing

Feel free to adapt the article style to your brand or blog platform. Keep the *“good, bad, ugly”* structure—it’s a proven way to keep readers engaged while teaching key algorithmic insights. Good luck cracking LeetCode 1438 and landing that software‑engineering role!