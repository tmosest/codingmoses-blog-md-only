---
title: LeetCode 239. Sliding Window Maximum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem Statement**  
Given an integer array `nums` and an integer `k`, return an array that contains the maximum value of each sliding window of length `k` that moves from left to right across the array.

---

### 1.  Algorithm – Monotonic Deque

We keep a deque that stores **indices** of array elements.  
The indices in the deque are always in **decreasing order of the corresponding values**:

```
nums[q[0]] >= nums[q[1]] >= ... >= nums[q[-1]]
```

The element at the front (`q[0]`) is therefore the maximum of the current window.

For every new element `nums[i]`:

1. **Remove smaller elements from the back** –  
   while the last element in the deque is smaller than `nums[i]` pop it.  
   Those elements can never become a maximum while the current element is in the window.

2. **Append the current index** – `q.append(i)`.

3. **Remove the front element if it leaves the window** –  
   if `q[0] <= i - k` pop it from the front.

4. **Record the maximum** – once we have processed at least one full window  
   (`i >= k-1`) append `nums[q[0]]` to the result.

---

### 2.  Correctness Proof  

We prove that the algorithm outputs the maximum for every window.

**Lemma 1**  
The deque always contains indices of elements that are inside the current window.

*Proof.*  
When `i` increases, the window is `[i-k+1 , i]`.  
Step 3 removes the front element if its index `<= i-k`.  
No other indices can become out of the window because indices increase by 1 each step. ∎

**Lemma 2**  
The deque is always in decreasing order of values:  
`nums[q[0]] >= nums[q[1]] >= …`.

*Proof.*  
When a new index `i` is processed, we pop from the back while `nums[q[-1]] < nums[i]`.  
Thus after the loop, all remaining elements in the deque are **not smaller** than `nums[i]`.  
Since `i` is appended at the back, the order is maintained. ∎

**Lemma 3**  
The element at the front of the deque is the maximum value of the current window.

*Proof.*  
By Lemma&nbsp;2, all elements in the deque are in decreasing order.  
By Lemma&nbsp;1, all of them lie inside the current window.  
Therefore the front element is the largest among the window elements. ∎

**Theorem**  
The algorithm outputs the correct maximum for every sliding window.

*Proof.*  
For each position `i ≥ k-1` the algorithm appends `nums[q[0]]`.  
By Lemma&nbsp;3 this value equals the maximum of the window `[i-k+1 , i]`.  
Thus each output entry is correct, and all windows are processed exactly once. ∎

---

### 3.  Complexity Analysis

*Time* – Each index is inserted and removed at most once: **O(n)**.  
*Space* – The deque holds at most `k` indices; result array holds `n-k+1` values: **O(k)** additional space.

---

### 4.  Reference Implementation (Python)

```python
from collections import deque
from typing import List

class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        if not nums or k == 0:
            return []

        n = len(nums)
        q = deque()          # store indices of potential maxima
        res = []

        for i, num in enumerate(nums):
            # Remove indices whose corresponding values are smaller than num
            while q and nums[q[-1]] < num:
                q.pop()
            q.append(i)

            # If the front index is out of the window, pop it
            if q[0] <= i - k:
                q.popleft()

            # Record maximum once the first full window is processed
            if i >= k - 1:
                res.append(nums[q[0]])

        return res
```

This solution follows exactly the algorithm proven correct above and runs in linear time with linear output space.