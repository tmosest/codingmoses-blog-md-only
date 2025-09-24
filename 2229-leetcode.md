---
title: LeetCode 2229. Check if an Array Is Consecutive - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Language Implementation

Below you’ll find three *clean, production‑ready* solutions that solve **LeetCode 2229 – “Check if an Array Is Consecutive”** in **O(n)** time and **O(n)** auxiliary space.  
All three use the same idea:

1. Scan the array once to find the minimum and maximum values and to build a `Set` (or `HashSet` / `unordered_set`) of all elements.
2. If the array length equals `max – min + 1` *and* the set size equals the array length, the array contains *every* integer in that range → it is consecutive.

> **Why this is the “good” approach**  
> • Linear time – you only touch each element once.  
> • Constant‑time membership checks via hashing.  
> • No sorting, no extra passes over the data.  

All three snippets are commented and ready to drop into your solution file.

---

### Java – O(n) Time, O(n) Space

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    /**
     * Checks whether the input array is consecutive.
     *
     * @param nums the input array (1 <= nums.length <= 1e5, 0 <= nums[i] <= 1e5)
     * @return true if every number in [min(nums), min(nums)+nums.length-1] is present
     */
    public boolean isConsecutive(int[] nums) {
        // Defensive copy: if the array is empty, it is trivially consecutive
        if (nums == null || nums.length == 0) return true;

        Set<Integer> seen = new HashSet<>(nums.length);
        int min = Integer.MAX_VALUE;
        int max = Integer.MIN_VALUE;

        for (int x : nums) {
            seen.add(x);
            if (x < min) min = x;
            if (x > max) max = x;
        }

        int expectedLength = max - min + 1;
        return nums.length == expectedLength && seen.size() == nums.length;
    }
}
```

---

### Python – O(n) Time, O(n) Space

```python
class Solution:
    def isConsecutive(self, nums: list[int]) -> bool:
        """
        Return True iff `nums` contains every integer from min(nums)
        to min(nums)+len(nums)-1 (inclusive).
        """
        if not nums:          # empty list is considered consecutive
            return True

        min_val = min(nums)
        max_val = max(nums)
        expected_length = max_val - min_val + 1

        # A set guarantees O(1) average lookup; its size tells us if duplicates exist
        seen = set(nums)
        return len(nums) == expected_length and len(seen) == len(nums)
```

---

### C++ – O(n) Time, O(n) Space

```cpp
#include <vector>
#include <unordered_set>
#include <algorithm>
#include <climits>

class Solution {
public:
    bool isConsecutive(std::vector<int>& nums) {
        if (nums.empty()) return true;           // edge case: empty array

        int min_val = INT_MAX, max_val = INT_MIN;
        std::unordered_set<int> seen;

        for (int x : nums) {
            seen.insert(x);
            if (x < min_val) min_val = x;
            if (x > max_val) max_val = x;
        }

        int expected_len = max_val - min_val + 1;
        return nums.size() == expected_len && seen.size() == nums.size();
    }
};
```

---

## 2.  Blog Article – “The Good, The Bad, and the Ugly of Checking If an Array Is Consecutive”

> **Meta Description**  
> Master the LeetCode “Check if an Array Is Consecutive” problem. Learn the *good* linear‑time solution, why the *bad* sorting approach fails, and how to avoid the *ugly* pitfalls. Get ready for interviews in Java, Python, or C++.

### Introduction

“**Check if an Array Is Consecutive**” (LeetCode #2229) looks deceptively simple, but it’s a classic interview staple that tests your ability to reason about ranges, duplicates, and algorithmic efficiency. Many candidates reach for a sorting trick or a two‑pointer sweep, but those approaches can be slower and harder to justify in a timed interview.  

In this article we dissect the problem into three parts:

| Phase | What you’ll discover | Why it matters |
|-------|----------------------|----------------|
| **The Good** | Linear‑time, set‑based solution | Fast, clear, and easy to explain |
| **The Bad** | Sorting or repeated scanning | Extra O(n log n) overhead |
| **The Ugly** | Edge cases and duplicate handling | Subtle bugs that break your solution |

---

### The Problem at a Glance

> **Given** an integer array `nums`.  
> **Return** `true` if `nums` contains **every** integer in the interval `[min(nums), min(nums)+len(nums)-1]`.  
> **Else** return `false`.

**Constraints**  
- `1 <= nums.length <= 10^5`  
- `0 <= nums[i] <= 10^5`

---

## The Good – Linear‑time Set Solution

### Why it’s the best approach

1. **O(n) time** – one pass to find `min`, `max`, and collect elements into a set.  
2. **O(n) space** – the set holds each distinct value.  
3. **No sorting** – avoids the extra `n log n` factor.  
4. **Handles duplicates naturally** – set size equals array length iff there are no duplicates.

### Step‑by‑Step

1. **Find the minimum (`min`) and maximum (`max`) values.**  
2. **Build a hash‑set of all elements.**  
3. **Compute the expected length**: `expected = max - min + 1`.  
4. **Return** `nums.length == expected && set.size() == nums.length`.

### Pseudocode

```
function isConsecutive(nums):
    if nums empty: return true
    minVal = +∞
    maxVal = -∞
    set = empty hash set

    for each x in nums:
        set.add(x)
        minVal = min(minVal, x)
        maxVal = max(maxVal, x)

    expectedLen = maxVal - minVal + 1
    return len(nums) == expectedLen and len(set) == len(nums)
```

### Java / Python / C++ Snippets

*(See Section 1 above for fully commented code in each language.)*

### Interview Tip

Explain that the set guarantees no duplicates. If the set size differs from the array length, you know there’s a duplicate and the array cannot be consecutive.

---

## The Bad – Sorting or Double Scanning

### Common Pitfall

A lot of candidates will sort the array first:

```java
Arrays.sort(nums);
int min = nums[0];
for (int i = 1; i < nums.length; i++) {
    if (nums[i] != min + i) return false;   // detect gap or duplicate
}
```

### Why it’s suboptimal

- **O(n log n)** time due to sorting, which is unnecessary given the linear‑time solution exists.
- Sorting destroys the original order, which might be a requirement in other contexts.
- Requires an extra pass to validate gaps and duplicates, adding hidden constant factors.

### When It Might Be Acceptable

If you’re certain the interviewer only cares about *correctness* and not optimality, or if `n` is extremely small, a sorting approach can be fine. But for a 10^5 array, the overhead is measurable.

---

## The Ugly – Edge Cases & Duplicate Handling

### Duplicate Numbers

The problem statement allows duplicates (e.g., `[1,1,2,3]`), but a valid consecutive array must contain *every* integer exactly once.  
**Buggy code**:  
```python
if len(set(nums)) == len(nums) and max(nums) - min(nums) + 1 == len(nums):
    return True
```
This code works, but if you forget the set size check, you’ll incorrectly return `True` for `[1,2,2,3]`.

### Empty Array

The constraints guarantee at least one element, but a defensive implementation should handle `[]`.  
**Good practice**: `if nums.empty() return true;` (or `false` if you prefer).

### Large Numbers & Overflow

With `int` values up to `10^5`, the expression `max - min + 1` will not overflow in 32‑bit integers.  
However, if the constraints were larger, use a 64‑bit integer (`long` in Java, `long long` in C++).

### Performance Testing

If you’re unsure about the linearity, run a micro‑benchmark:

```java
int[] big = new int[100_000];
for (int i = 0; i < 100_000; i++) big[i] = i;
new Solution().isConsecutive(big);   // should be true
```

---

## Take‑Away Checklist

| ✅ | Item |
|----|------|
| ✅ | Use a hash‑set to guarantee O(1) lookup and duplicate detection. |
| ✅ | Compute `min` and `max` in the same pass as set insertion. |
| ✅ | Validate `nums.length == (max - min + 1)` and `set.size() == nums.length`. |
| ❌ | Avoid sorting unless you have a good reason. |
| ❌ | Don’t forget to handle duplicates. |
| ❌ | Don’t assume the array is non‑empty unless constraints guarantee it. |

---

## Closing Thoughts

The “Check if an Array Is Consecutive” problem is a micro‑treatment of *range logic* and *set‑based uniqueness*. By mastering the linear solution you not only score points on LeetCode but also demonstrate to interviewers that you:

- **Know complexity** – can pick O(n) over O(n log n) when appropriate.  
- **Understand data structures** – leverage hash sets for membership checks.  
- **Write clean, testable code** – each part of the algorithm is isolated and easy to reason about.

Good luck crushing your coding interviews, and keep practicing – every edge case you handle now will pay dividends later!  

---  

**Keywords:** LeetCode 2229, Check if an Array Is Consecutive, interview question, algorithm, Java, Python, C++, O(n), hash set, sorting pitfalls, coding interview tips, job interview, software engineering, data structures, time complexity.