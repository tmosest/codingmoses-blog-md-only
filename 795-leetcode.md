---
title: LeetCode 795. Number of Subarrays with Bounded Maximum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 795. Number of Subarrays with Bounded Maximum  
## The Good, The Bad, and The Ugly – A Complete Interview‑Ready Guide

> **Target keywords**: *LeetCode 795, Number of Subarrays with Bounded Maximum, interview preparation, sliding window, two pointers, Java solution, Python solution, C++ solution, O(N) time, 32‑bit integer*  

---

## Table of Contents
1. [Problem Restatement](#problem-restatement)  
2. [Intuition & Approach](#intuition--approach)  
3. [Why the Sliding‑Window Solution Wins](#why-the-sliding-window-solution-wins)  
4. [Implementation Details (Java, Python, C++)](#implementation-details)  
5. [Edge Cases & Pitfalls](#edge-cases--pitfalls)  
6. [Complexity Analysis](#complexity-analysis)  
7. [What Makes This Problem “The Ugly”?](#what-makes-this-problem-the-ugly)  
8. [How to Talk About It in an Interview](#how-to-talk-about-it-in-an-interview)  
9. [Takeaway & Resources](#takeaway--resources)

---

## Problem Restatement

> **LeetCode 795** – *Number of Subarrays with Bounded Maximum*  
> Given an integer array `nums` and two integers `left` and `right`, return the number of contiguous, non‑empty subarrays such that the maximum element in the subarray lies in the range `[left, right]`.  
> All answers fit in a 32‑bit signed integer.

### Example
```
nums  = [2,1,4,3],  left = 2, right = 3
output: 3   (subarrays: [2], [2,1], [3])
```

---

## Intuition & Approach

### 1. The “Two‑Pointer” View
A subarray satisfies the condition **iff**:

* **No element > right** – otherwise the max would be > right.  
* **At least one element ≥ left** – otherwise the max would be < left.

So a subarray is valid when it is a *segment* of the array that contains **no out‑of‑range elements** and **contains at least one “good” element** (≥ left).

### 2. Count Valid Subarrays Efficiently
Instead of enumerating every subarray (O(n²)), we traverse the array once while maintaining:

| Variable | Meaning | How it is updated |
|----------|---------|-------------------|
| `lastInvalid` | index of the **last** element that is > right | reset to current index when such an element is seen |
| `lastGood` | index of the **last** element that is ≥ left | updated whenever a good element appears |

At index `i`, every subarray ending at `i` that starts **after** `lastInvalid` and **includes** `lastGood` is valid.  
Number of such subarrays = `lastGood - lastInvalid` (if `lastGood > lastInvalid`, else 0).

Accumulate this over the whole array → **O(n)** time, **O(1)** space.

---

## Why the Sliding‑Window Solution Wins

| Criterion | Sliding‑Window | Brute‑Force | DP (prefix sums) |
|-----------|----------------|------------|------------------|
| **Time** | **O(n)** | O(n²) | O(n) but with heavier constant |
| **Space** | O(1) | O(1) | O(n) |
| **Implementation Simplicity** | ★★ | ★ | ★ |
| **Intuition** | Easy to explain | Hard to grasp | Needs proof |
| **Edge‑Case Handling** | Robust | Needs nested loops | Needs careful indices |

The sliding‑window approach is *the most interview‑friendly* – concise, proven, and easy to test.

---

## Implementation Details

Below are three clean, production‑ready implementations.  
All are **public**, **thread‑safe**, and handle all edge cases.

> **Tip:** Use `long` for the accumulator only if you anticipate values near `Integer.MAX_VALUE`.  
> The problem guarantees the result fits in 32‑bit, so `int` is fine.

### Java

```java
// Java 17+ (Lombok not required)
public class Solution {
    /**
     * Counts subarrays whose maximum is in [left, right].
     *
     * @param nums  the input array
     * @param left  lower bound of the maximum
     * @param right upper bound of the maximum
     * @return number of valid subarrays
     */
    public int numSubarrayBoundedMax(int[] nums, int left, int right) {
        int count = 0;
        int lastInvalid = -1; // last index where nums[i] > right
        int lastGood = -1;    // last index where nums[i] >= left

        for (int i = 0; i < nums.length; i++) {
            if (nums[i] > right) {
                lastInvalid = i;           // reset window
            }
            if (nums[i] >= left) {
                lastGood = i;              // new good element
            }
            // number of subarrays ending at i that are valid
            if (lastGood > lastInvalid) {
                count += lastGood - lastInvalid;
            }
        }
        return count;
    }
}
```

---

### Python

```python
# Python 3.9+
class Solution:
    def numSubarrayBoundedMax(self, nums, left, right):
        """
        :type nums: List[int]
        :type left: int
        :type right: int
        :rtype: int
        """
        count = 0
        last_invalid = -1  # index of last > right
        last_good = -1     # index of last >= left

        for i, val in enumerate(nums):
            if val > right:
                last_invalid = i
            if val >= left:
                last_good = i

            if last_good > last_invalid:
                count += last_good - last_invalid

        return count
```

---

### C++

```cpp
// C++17
class Solution {
public:
    int numSubarrayBoundedMax(vector<int>& nums, int left, int right) {
        long long count = 0;
        int last_invalid = -1; // last index where nums[i] > right
        int last_good = -1;    // last index where nums[i] >= left

        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] > right) last_invalid = i;
            if (nums[i] >= left) last_good = i;

            if (last_good > last_invalid)
                count += last_good - last_invalid;
        }
        return static_cast<int>(count);   // fits into 32‑bit per problem
    }
};
```

> **Why use `long long` for `count` in C++?**  
> To guard against intermediate overflow when the array length is 10⁵ and all elements are in range – the number of subarrays can reach ~5 × 10⁹ temporarily, but the final answer is still within `int`.

---

## Edge Cases & Pitfalls

| Situation | What to Watch For | Fix |
|-----------|-------------------|-----|
| `nums` contains only values > right | Loop will keep resetting `last_invalid`; `last_good` never updated → answer 0 | Code already handles this – just ensure no division by zero. |
| All values within `[left, right]` | Every subarray is valid | Code accumulates correctly: `count += i - (-1)` each iteration. |
| Very large `left` & `right` (e.g., 10⁹) | Comparison is still fine | Use 64‑bit literals if you write constants. |
| Negative numbers? | Problem constraints say `nums[i] >= 0` | No changes needed. |
| Empty array (not allowed by constraints) | Code would return 0 | Not required, but safe guard: `if(nums.empty()) return 0;` |

---

## Complexity Analysis

| Metric | Value |
|--------|-------|
| Time | **O(n)** – one pass over the array |
| Space | **O(1)** – only a few integer variables |

---

## What Makes This Problem “The Ugly”?

1. **Misleading Brute‑Force Intuition**  
   Many interviewees think they must check every subarray – O(n²).  
   The key is realizing that “bad” elements (> right) act as **breakpoints**.

2. **Hidden “Two‑Pointer” Insight**  
   The sliding‑window idea is not obvious until you see the *relationship* between *invalid* and *valid* elements.

3. **Edge‑Case Overlook**  
   Forgetting to reset `last_invalid` or mis‑calculating `last_good - last_invalid` yields subtle bugs.

4. **Testing Complexity**  
   Small test cases look fine; stress tests (e.g., 10⁵ elements all within range) expose integer overflow if you’re not careful.

---

## How to Talk About It in an Interview

1. **Clarify the Conditions**  
   *“We need subarrays where the maximum lies in [left, right]. That means: no element > right, and at least one element ≥ left.”*

2. **Explain the Key Observation**  
   *“Indices with values > right split the array into independent segments. Inside each segment, we just need to know how many subarrays contain a good element.”*

3. **Derive the O(n) Solution**  
   *“Maintain two indices: `last_invalid` (most recent > right) and `last_good` (most recent ≥ left). For each position i, the number of valid subarrays ending at i is `max(0, last_good - last_invalid)`.”*

4. **Discuss Edge Cases**  
   *“If there’s no good element yet (`last_good == -1`) or the last good is before the last invalid, we add 0.”*

5. **Mention Complexity**  
   *“We visit each element once, so O(n) time and O(1) space.”*

6. **Optional: Provide a Second Approach**  
   *“You could also do a prefix‑sum DP, but it’s heavier.”*

---

## Takeaway & Resources

* **Key idea:** Sliding‑window with two indices that track the latest “bad” and “good” positions.  
* **Time‑saver:** Only one pass, O(n) time.  
* **Interview hack:** Explain the *breakpoint* concept – it shows deep insight.

> **Practice:**  
> 1. LeetCode 795 (Easy to Medium).  
> 2. Similar problems: *Number of Subarrays with Sum ≤ K*, *Maximum subarray of size K*.  
> 3. Build a small unit test harness in each language to verify against random data.

---

### References

- LeetCode problem page: https://leetcode.com/problems/number-of-subarrays-with-bounded-maximum/
- Top solutions (Java, Python, C++): https://leetcode.com/problems/number-of-subarrays-with-bounded-maximum/solutions/
- My own sliding‑window solution: (Insert your GitHub repo link if available)

---

### Final Thought

The beauty of this problem lies in the fact that a seemingly “hard” subarray counting question collapses to two simple indices. Master this pattern, and you’ll have a powerful tool for many interview problems that involve **max/min constraints** or **“no‑bad‑elements”** conditions. Happy coding!