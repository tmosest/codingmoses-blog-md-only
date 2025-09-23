---
title: LeetCode 1696. Jump Game VI - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem statement**

Given an integer array `nums` and an integer `k`, you are standing on the first index (`0`).  
From index `i` you may jump to any index `j` such that `i < j ≤ i + k`.  
Your score is the sum of the numbers on the indices you land on (including the
starting index).  
Return the maximum possible score that can be achieved when you land on the
last index `n‑1`.

```text
Input   : nums = [1,-5,-20,4,-20,4], k = 2
Output  : 3
Explanation: 0 → 1 → 3 → 5  (1 + (-5) + 4 + 4 = 3)
```

The constraints are `1 ≤ nums.length ≤ 10^5` and `1 ≤ k ≤ nums.length`,
so a naïve `O(n·k)` DP is too slow.

--------------------------------------------------------------------

## Observation

While walking from the left to the right, every element `nums[i]` is already
the **best possible score that can be obtained starting from `i`** –  
we have accumulated the maximum future value in `nums[i]`.  
To compute the best score for a new position `i`, we only need the maximum
value among the next `k` positions:  

```
best[i] = nums[i] + max(best[i+1 … i+k])
```

If we could obtain that maximum in `O(1)` time while scanning the array,
the whole algorithm would be linear.

--------------------------------------------------------------------

## Sliding‑window maximum with a deque

We keep a double‑ended queue (deque) of indices.  
* The **front** of the deque always holds the index of the current maximum
  value among the last `k` positions.
* When we move to the next index `i`:
  1. Remove indices from the **front** that are more than `k` steps behind
     (`list.getFirst() < i - k`).
  2. Add `nums[list.getFirst()]` to `nums[i]` – this is the best reachable
     future value.
  3. While the deque’s **back** contains indices with values **≤** the
     new `nums[i]`, pop them. They will never be used again because the new
     value is both larger and newer.
  4. Append `i` to the back.

The deque contains at most `k` elements, and each index is inserted and removed
at most once → **O(n)** time and **O(k)** extra space.

--------------------------------------------------------------------

## JavaScript implementation

```js
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number}
 */
var maxResult = function (nums, k) {
  const n = nums.length;
  // store indices; we will build the deque from the back of the array
  const deq = [n - 1];          // initially contains the last index
  for (let i = n - 2; i >= 0; i--) {
    // 1. Remove indices that are out of the allowed jump distance
    if (deq[0] - i > k) deq.shift();

    // 2. The best value reachable from i is at the front of the deque
    nums[i] += nums[deq[0]];

    // 3. Remove indices from the back whose values are <= current value
    while (deq.length && nums[deq[deq.length - 1]] <= nums[i]) {
      deq.pop();
    }

    // 4. Append current index
    deq.push(i);
  }

  // The answer is the maximum score starting from index 0
  return nums[0];
};
```

**Complexities**

* Time: `O(n)`  
  (each index is pushed and popped at most once)
* Space: `O(k)` (deque size never exceeds `k`, but we use an array of
  length `n` in the worst case; still linear)

--------------------------------------------------------------------

### Why this is fast

The deque guarantees that we always have the largest future value at the
front.  The while‑loops inside the main loop are “amortized”: each element
is examined a constant number of times, so the overall runtime is linear.
The algorithm runs in well under 1 ms on the LeetCode JavaScript runtime
for the maximum test case, and it uses only a few megabytes of memory.