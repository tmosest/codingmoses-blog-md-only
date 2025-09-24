---
title: LeetCode 3231. Minimum Number of Increasing Subsequence to Be Removed - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Solving LeetCode 3231: Minimum Number of Increasing Subsequence to Be Removed  
**Java | Python | C++ – O(n log n) Time, O(n) Space**

---

## 📌 Problem Recap

> Given an integer array `nums`, you can repeatedly **remove a strictly increasing subsequence** (not necessarily contiguous).  
> **Goal:** remove the entire array using as few operations as possible.

```
Example 1
Input:  nums = [5,3,1,4,2]
Output: 3
Explanation:
  1st op: remove [1,2]
  2nd op: remove [3,4]
  3rd op: remove [5]
```

```
Constraints
 1 ≤ nums.length ≤ 10⁵
 1 ≤ nums[i] ≤ 10⁵
```

---

## 🔍 The Insight – Dilworth’s Theorem

The task is equivalent to partitioning the array into the **minimum number of strictly increasing subsequences**.  
By **Dilworth’s theorem**, the minimum number of such subsequences equals the length of the **longest non‑increasing subsequence** (LNIS) in the array.

So the problem reduces to a classic patience‑sorting problem: find the length of the longest non‑increasing subsequence.

---

## 💡 Two Practical Approaches

| Approach | Language | Core Idea | Complexity |
|----------|----------|-----------|------------|
| **TreeMap (Java)** | `O(n log n)` | Maintain a multiset of current subsequence ends. | Time: `O(n log n)`<br>Space: `O(n)` |
| **Patience sorting on negated values** | Python / C++ | Convert the LNIS problem to a standard LIS by negating every element. | Time: `O(n log n)`<br>Space: `O(n)` |

Both methods run in the same asymptotic bounds, but the TreeMap version feels more “Java‑ish”, while the LIS‑on‑negated‑values version is concise in Python and C++.

---

## 🧩 Implementation Details

### 1. Java – TreeMap Version

```java
import java.util.*;

class Solution {
    public int minOperations(int[] nums) {
        // Map<ending value, count of subsequences ending with that value>
        TreeMap<Integer, Integer> map = new TreeMap<>();

        for (int num : nums) {
            // Find the closest smaller key (< num)
            Integer lower = map.lowerKey(num);
            if (lower != null) {
                // We extend a subsequence ending with 'lower'
                int cnt = map.get(lower) - 1;
                if (cnt == 0) map.remove(lower);
                else map.put(lower, cnt);
            }

            // Now start / extend a subsequence ending with 'num'
            map.put(num, map.getOrDefault(num, 0) + 1);
        }

        // Total subsequences left = answer
        int total = 0;
        for (int v : map.values()) total += v;
        return total;
    }
}
```

**Why `lowerKey`?**  
If we have a subsequence ending at value `x` (`x < num`) we can safely append `num` to it. We choose the *closest* smaller value so that we consume one of the subsequences that can be extended.

---

### 2. Python – Patience Sorting on Negated Values

```python
from bisect import bisect_left

class Solution:
    def minOperations(self, nums: List[int]) -> int:
        tails = []          # tails of LIS on negated values
        for x in nums:
            y = -x          # convert LNIS to LIS
            idx = bisect_left(tails, y)
            if idx == len(tails):
                tails.append(y)
            else:
                tails[idx] = y
        return len(tails)
```

**Explanation**  
`tails` keeps the smallest possible tail for an increasing subsequence of each length.  
By negating, a *non‑increasing* sequence in `nums` becomes a *strictly increasing* sequence in `-nums`.  
`bisect_left` guarantees we replace the first tail that is ≥ `y`, maintaining optimal tails.

---

### 3. C++ – Same Patience Sorting Idea

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minOperations(vector<int>& nums) {
        vector<int> tails;                     // tails of LIS on negated values
        for (int x : nums) {
            int y = -x;                        // convert LNIS to LIS
            auto it = lower_bound(tails.begin(), tails.end(), y);
            if (it == tails.end())
                tails.push_back(y);
            else
                *it = y;
        }
        return (int)tails.size();
    }
};
```

---

## 📚 Why Does This Work? (Proof Sketch)

1. **Dilworth’s Theorem** tells us that the minimal number of increasing subsequences that partition a sequence equals the size of its largest antichain (here, a longest non‑increasing subsequence).
2. Our *TreeMap* algorithm essentially builds that longest non‑increasing subsequence: each time we find a smaller number, we “extend” the current subsequence; otherwise we start a new one.
3. In the patience‑sorting approach, we are literally computing the length of the longest non‑increasing subsequence by turning it into an LIS problem.

---

## 🚀 Complexity Breakdown

| Language | Time | Space |
|----------|------|-------|
| Java (TreeMap) | `O(n log n)` | `O(n)` |
| Python / C++ (Patience) | `O(n log n)` | `O(n)` |

- The log‑factor comes from `TreeMap` operations or binary search (`bisect_left` / `lower_bound`).
- `O(n)` auxiliary space is due to the `tails` array / TreeMap.

---

## ⚠️ Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Using `floorKey` instead of `lowerKey` in Java | `floorKey(num-1)` is unnecessary; `lowerKey(num)` is clearer. |
| Confusing `bisect_left` vs `bisect_right` in Python | `bisect_left` ensures strictly increasing subsequence. |
| Forgetting to negate the value in LIS approach | Without negation you compute LIS of the original array, which gives the wrong answer for non‑increasing case. |
| Off‑by‑one errors in C++ `lower_bound` | Always check `if (it == tails.end()) tails.push_back(y);` |

---

## 📈 Takeaway – The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| Intuition | Dilworth’s theorem gives a clean mathematical foundation. | Requires knowledge of posets, which may feel heavy for interviewers. | Thinking in terms of tree maps can be intimidating if you’re not comfortable with `TreeMap`. |
| Implementation | TreeMap version is very readable for Java; LIS version is concise for Python/C++. | TreeMap uses a lot of memory (hash overhead). | Negating values to transform LNIS into LIS may be non‑intuitive to newcomers. |
| Performance | `O(n log n)` works comfortably up to 10⁵. | Binary search on an array is faster in practice than `TreeMap`. | None – both implementations are efficient. |

---

## 🎯 SEO‑Optimized Summary

If you’re hunting for **LeetCode 3231 solutions**, this post delivers:

- **Java, Python, and C++ implementations** that run in `O(n log n)` time.
- A deep dive into **Dilworth’s theorem** and how to turn the problem into a classic **Longest Non‑Increasing Subsequence** task.
- Practical code snippets with **TreeMap** and **patience sorting**.
- Tips on avoiding common pitfalls and **time/space analysis**.

Whether you’re preparing for a hard‑level interview or simply sharpening your algorithmic toolkit, this guide gives you the clean, production‑ready code you need. Happy coding! 🚀

--- 

**Keywords:** LeetCode 3231, Minimum Number of Increasing Subsequence to Be Removed, Java solution, Python solution, C++ solution, Dilworth’s theorem, longest non‑increasing subsequence, LIS on negated values, interview preparation, algorithm analysis.