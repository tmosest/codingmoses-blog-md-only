---
title: LeetCode 1887. Reduction Operations to Make the Array Elements Equal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ LeetCode 1887 – “Reduction Operations to Make the Array Elements Equal”

| Language | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| **Java** | **O(n log n)** | **O(n)** |
| **Python** | **O(n log n)** | **O(n)** |
| **C++** | **O(n log n)** | **O(n)** |

---

### 🚀 TL;DR (Solution in One Line)

```java
Arrays.sort(nums);
int ans = 0, prev = nums[0];
for (int i = 1; i < nums.length; i++) {
    if (nums[i] != prev) { ans += i; prev = nums[i]; }
}
return ans;
```

---

## 1. Problem Recap

You’re given an integer array `nums`.  
One **operation** is:

1. Find the *largest* value (if several, pick the leftmost).
2. Reduce that element to the next largest *smaller* value.
3. Repeat until every element is equal.

Return the total number of operations needed.

---

## 2. Intuition & Key Observation

Think of the array sorted **increasingly**:

```
[1, 3, 5]   →   sorted: [1, 3, 5]
```

* Each time the value changes, all elements *to the left* are strictly smaller.  
  The element that caused the change will be reduced *exactly once* to become equal to the left value.

So for every index `i` where `nums[i] != nums[i‑1]`, we add `i` operations.  
Adding all those “jump” counts gives the answer.

---

## 3. Edge‑Case Pitfalls

| Case | What can go wrong? | How to avoid it |
|------|--------------------|-----------------|
| All equal elements | Might incorrectly count transitions | Skip the first element; only add when `nums[i] != nums[i‑1]` |
| Array of size 1 | No operation needed | The loop starts at `i = 1` |
| Duplicate values scattered | Sorting handles order; duplicates become contiguous | Use sorting first |

---

## 4. Complexity Analysis

| Step | Cost |
|------|------|
| Sort array | **O(n log n)** |
| Single pass | **O(n)** |
| Extra space | Array copy (in-place sort) → **O(1)** (Java’s `Arrays.sort` is in‑place) |

Overall: **Time** `O(n log n)` **Space** `O(1)` (or `O(n)` if you copy).

---

## 5. Implementation in Three Languages

### 5.1 Java

```java
import java.util.Arrays;

public class Solution {
    public int reductionOperations(int[] nums) {
        Arrays.sort(nums);          // O(n log n)
        int ops = 0;
        int prev = nums[0];
        for (int i = 1; i < nums.length; i++) {
            if (nums[i] != prev) {
                ops += i;          // all elements before i are smaller
                prev = nums[i];
            }
        }
        return ops;
    }
}
```

### 5.2 Python

```python
class Solution:
    def reductionOperations(self, nums: list[int]) -> int:
        nums.sort()                 # O(n log n)
        ops = 0
        prev = nums[0]
        for i in range(1, len(nums)):
            if nums[i] != prev:
                ops += i           # all elements before i are smaller
                prev = nums[i]
        return ops
```

### 5.3 C++

```cpp
#include <algorithm>
#include <vector>

class Solution {
public:
    int reductionOperations(std::vector<int>& nums) {
        std::sort(nums.begin(), nums.end());   // O(n log n)
        int ops = 0;
        int prev = nums[0];
        for (size_t i = 1; i < nums.size(); ++i) {
            if (nums[i] != prev) {
                ops += static_cast<int>(i);    // all elements before i are smaller
                prev = nums[i];
            }
        }
        return ops;
    }
};
```

---

## 6. Alternative View: Using Frequency Map

If you prefer not to sort the entire array, you can:

1. Build a frequency map (`TreeMap` in Java, `SortedDict` in Python, or `std::map` in C++).  
2. Traverse the keys in **descending** order, maintaining a suffix sum of counts, adding it to the answer for every key except the smallest.

This yields the same `O(n log n)` time but may be clearer when you’re already working with a multiset.

---

## 7. Why This Is a Great Interview Problem

1. **Shows Understanding of Sorting & Prefix Sums** – Many interviewers look for candidate’s ability to transform a problem into a sorted‑prefix view.  
2. **Edge‑Case Sensitivity** – Handling arrays with all equal elements or singletons tests attention to detail.  
3. **Multiple Solutions** – You can discuss both the “sorted array jump” approach and the “frequency map suffix” approach, highlighting trade‑offs.  

---

## 8. SEO‑Friendly Summary

- **Keywords**: *Leetcode 1887*, *Reduction Operations*, *Make array elements equal*, *Java solution*, *Python solution*, *C++ solution*, *algorithm interview*, *coding interview prep*, *O(n log n)*, *sorted array*.
- **Meta‑Description**: “Learn how to solve LeetCode 1887 – Reduction Operations to Make the Array Elements Equal. View clear Java, Python, and C++ implementations plus a deep‑dive blog explaining the algorithm, edge cases, and interview tips.”

---

### 🎯 Final Thought

The key to *LeetCode 1887* is recognizing that each “jump” in the sorted array tells us exactly how many reductions are required. A simple single‑pass after sorting gives an elegant, optimal solution that will impress interviewers and get you one step closer to your dream job. Happy coding!