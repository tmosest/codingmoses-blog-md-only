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
### LeetCode – Medium

**Problem statement**

> Given an integer array `nums` and an integer `limit`, return the size of the longest *non‑empty* subarray such that the absolute difference between any two elements in that subarray is at most `limit`.

```
Input  : nums = [8,2,4,7] , limit = 4
Output : 2
```

**Why is this interesting?**  
The naive solution (checking every possible subarray) is \(O(n^2)\) and will time‑out on the maximum input size \(n = 10^5\).  
A linear‑time sliding‑window solution using two deques (or a balanced BST/priority queue) runs in \(O(n)\) and is the canonical “greedy + data structure” pattern that interviewers love.

---

## 1. Algorithm – Two‑Deque Sliding Window

1. Keep two deques:
   * `maxDeque` – stores indices of elements in **decreasing** order (front is the current maximum).
   * `minDeque` – stores indices of elements in **increasing** order (front is the current minimum).
2. Expand the right end (`right`) of the window one step at a time.
   * While the new element is smaller than the back of `maxDeque`, pop the back – we keep only useful candidates for the maximum.
   * While the new element is larger than the back of `minDeque`, pop the back – we keep only useful candidates for the minimum.
   * Push the new index onto both deques.
3. After adding the element, check whether the window is **valid**:
   \[
   nums[maxDeque.front] - nums[minDeque.front] \le limit
   \]
   If it’s **invalid**, shrink the window from the left (`left`) until it becomes valid again, removing indices that leave the window from the front of each deque.
4. Update the answer with the current window size (`right - left + 1`).

**Why does this work?**  
Because the deques always hold the current window’s maximum and minimum, the difference can be obtained in \(O(1)\).  
When the window becomes invalid, we know the element that caused the violation must be at the left side – moving `left` forward removes it. This guarantees each index is inserted and removed at most once → linear time.

---

## 2. Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Main loop | \(O(n)\) | \(O(n)\) (deques, but each index stored only once) |
| Final answer | \(O(1)\) | \(O(1)\) |

---

## 3. Reference Implementations

### Java

```java
import java.util.ArrayDeque;

public class Solution {
    public int longestSubarray(int[] nums, int limit) {
        ArrayDeque<Integer> maxQ = new ArrayDeque<>(); // decreasing
        ArrayDeque<Integer> minQ = new ArrayDeque<>(); // increasing
        int left = 0, ans = 0;

        for (int right = 0; right < nums.length; right++) {
            // maintain max deque
            while (!maxQ.isEmpty() && nums[maxQ.peekLast()] < nums[right]) {
                maxQ.pollLast();
            }
            maxQ.offerLast(right);

            // maintain min deque
            while (!minQ.isEmpty() && nums[minQ.peekLast()] > nums[right]) {
                minQ.pollLast();
            }
            minQ.offerLast(right);

            // shrink window if needed
            while (nums[maxQ.peekFirst()] - nums[minQ.peekFirst()] > limit) {
                if (maxQ.peekFirst() == left) maxQ.pollFirst();
                if (minQ.peekFirst() == left) minQ.pollFirst();
                left++;
            }

            ans = Math.max(ans, right - left + 1);
        }
        return ans;
    }
}
```

### Python

```python
from collections import deque
from typing import List

class Solution:
    def longestSubarray(self, nums: List[int], limit: int) -> int:
        max_q = deque()   # decreasing
        min_q = deque()   # increasing
        left = 0
        ans = 0

        for right, num in enumerate(nums):
            while max_q and nums[max_q[-1]] < num:
                max_q.pop()
            max_q.append(right)

            while min_q and nums[min_q[-1]] > num:
                min_q.pop()
            min_q.append(right)

            while nums[max_q[0]] - nums[min_q[0]] > limit:
                if max_q[0] == left:
                    max_q.popleft()
                if min_q[0] == left:
                    min_q.popleft()
                left += 1

            ans = max(ans, right - left + 1)
        return ans
```

### C++

```cpp
#include <vector>
#include <deque>
#include <algorithm>

class Solution {
public:
    int longestSubarray(std::vector<int>& nums, int limit) {
        std::deque<int> maxQ; // decreasing
        std::deque<int> minQ; // increasing
        int left = 0, ans = 0;

        for (int right = 0; right < (int)nums.size(); ++right) {
            while (!maxQ.empty() && nums[maxQ.back()] < nums[right])
                maxQ.pop_back();
            maxQ.push_back(right);

            while (!minQ.empty() && nums[minQ.back()] > nums[right])
                minQ.pop_back();
            minQ.push_back(right);

            while (nums[maxQ.front()] - nums[minQ.front()] > limit) {
                if (maxQ.front() == left) maxQ.pop_front();
                if (minQ.front() == left) minQ.pop_front();
                ++left;
            }
            ans = std::max(ans, right - left + 1);
        }
        return ans;
    }
};
```

---

## 4. Blog Article – *The Good, The Bad, and The Ugly of Sliding Window Solutions*

### Introduction

When you see a LeetCode problem titled “Longest Continuous Subarray …”, your mind immediately runs to *sliding window*. Sliding windows are the bread‑and‑butter of interview prep: they are fast, intuitive, and often the only acceptable solution for large input constraints.  

In this post we’ll walk through the **good**, **bad**, and **ugly** aspects of sliding‑window solutions for LeetCode 1438, *Longest Continuous Subarray With Absolute Diff ≤ Limit*. I’ll break down the algorithm, show clean Java/Python/C++ code, and give SEO‑friendly tips that’ll help you shine in a technical interview or a job‑search blog.

> **SEO headline**: “Master LeetCode 1438: Sliding‑Window Deques to Pass in O(n) – Java, Python & C++”

---

### The Good

| ✅ | Feature | Why it matters |
|----|---------|----------------|
| **Linear time** | \(O(n)\) | Scales to \(n = 10^5\) in < 1 ms. |
| **O(1) amortized window updates** | Deques maintain max/min | Avoids sorting or heap ops. |
| **Readability** | Two deques + pointers | Easy to explain in an interview. |
| **Memory friendly** | Each index stored once | Extra \(O(n)\) worst‑case, but deques are tiny. |

The *good* is obvious: the algorithm is optimal for the problem’s constraints and uses a clean data‑structure pattern that interviewers love.

---

### The Bad

| ⚠️ | Issue | Mitigation |
|----|-------|------------|
| **Edge‑case complexity** | Handling empty deques when shrinking | Carefully check `front()` before popping. |
| **Language quirks** | Java’s `ArrayDeque` vs. `LinkedList` | Use `ArrayDeque` for O(1) ops. |
| **Large integer values** | `int` vs. `long` | In Java, use `int` because problem limits fit, but watch out if you extend the logic. |
| **Readability pitfalls** | Over‑commenting vs. over‑abstracting | Keep a balance: comment only the non‑obvious parts. |

These are minor annoyances that appear if you copy‑paste a template without understanding the nuance of each language’s deque implementation.

---

### The Ugly

| 👹 | Pitfall | Fix |
|----|---------|-----|
| **Off‑by‑one bugs** | Shrinking loop may leave window invalid | Verify after loop with an assert or unit test. |
| **O(n²) in bad cases** | Incorrect deques that do not pop back | Ensure `while (maxQ.back < new)` logic is correct. |
| **Stack overflow on recursion** | None in this problem | But be mindful of stack usage in recursive solutions. |
| **Time limit exceed on large input** | Extra `Math.max` inside loop | Move it out if you can, but here it’s cheap. |

The *ugly* part reminds us that sliding window is powerful **only** if implemented correctly. A single typo in the deque logic can turn an optimal \(O(n)\) solution into a slow one.

---

## 5. How to Showcase This on Your Blog or Resume

1. **Title** – Make it keyword‑rich: “Longest Continuous Subarray (LeetCode 1438) – Sliding Window Deques in Java, Python, C++”.
2. **Intro** – Briefly state the problem and the challenge of large input.
3. **Algorithm section** – Use pseudo‑code and diagram your deques.
4. **Complexity analysis** – Big‑O notation with explanations.
5. **Code snippets** – Include all three language implementations. Mention that deques are the linchpin.
6. **Discussion** – Good, bad, ugly – shows you’re not just copying; you understand trade‑offs.
7. **Link** – Provide the LeetCode URL and a link to a GitHub repo with the full solution.
8. **SEO tags** – “LeetCode 1438”, “Sliding Window”, “Java Deque”, “Python Deque”, “C++ Deque”, “O(n) solution”, “Technical interview”.

Adding a small unit‑test section or a performance benchmark (e.g., `timeit` in Python) gives the reader extra confidence.

---

## 6. Take‑away Checklist

- ✅ Use **two deques**: one for max, one for min.
- ✅ Maintain deques in **monotonic** order.
- ✅ Shrink the window until the difference ≤ limit.
- ✅ Update answer after every expansion.
- ✅ Avoid off‑by‑one errors by testing with edge cases.

---

### TL;DR

- Problem: Longest subarray with |max‑min| ≤ limit.
- Solution: Sliding window + two deques (monotonic queues).
- Complexity: \(O(n)\) time, \(O(n)\) space.
- Code ready for Java, Python, C++.

> **Pro tip**: When you explain this to an interviewer, start with “I keep the maximum and minimum of the current window in deques so I can know the difference in O(1). Then I slide the window until the difference is ≤ limit.” That’s a 10‑second “elevator pitch” that instantly shows you know the pattern.

Happy coding—and good luck landing that job!