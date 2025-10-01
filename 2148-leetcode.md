---
title: LeetCode 2148. Count Elements With Strictly Smaller and Greater Elements - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2148. Count Elements With Strictly Smaller and Greater Elements  
**The Good, The Bad, and The Ugly – A Complete Interview‑Ready Guide**

| Topic | Details |
|-------|---------|
| **Difficulty** | Easy |
| **Languages** | Java, Python, C++ |
| **Time** | O(n) |
| **Space** | O(1) |
| **Key Insight** | An element has a strictly smaller and a strictly greater element iff it lies **strictly between** the global minimum and the global maximum of the array. |

---

## 📌 Problem Statement (LeetCode 2148)

> Given an integer array `nums`, return the number of elements that have **both** a strictly smaller and a strictly greater element appearing somewhere in `nums`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[11,7,2,15]` | `2` | `7` and `11` each have both a smaller (`2` or `7`) and a greater (`11` or `15`) element. |
| `[-3,3,3,90]` | `2` | Both occurrences of `3` satisfy the condition. |

**Constraints**

- `1 <= nums.length <= 100`
- `-10^5 <= nums[i] <= 10^5`

---

## 🛠️ Solution Idea

1. **Find the global minimum (`minVal`) and maximum (`maxVal`) of the array.**  
   *An element cannot have a strictly smaller value if it equals the minimum, and cannot have a strictly greater value if it equals the maximum.*

2. **Count every element that satisfies** `minVal < num < maxVal`.

That’s it—no sorting, no extra data structures, just a couple of linear passes.

---

## 📦 Code Implementations

Below are concise, production‑ready solutions in **Java, Python, and C++**. All three share the same O(n) time / O(1) space complexity.

### Java

```java
class Solution {
    public int countElements(int[] nums) {
        int minVal = Integer.MAX_VALUE;
        int maxVal = Integer.MIN_VALUE;

        // 1st pass: find min and max
        for (int num : nums) {
            if (num < minVal) minVal = num;
            if (num > maxVal) maxVal = num;
        }

        // 2nd pass: count elements strictly between min and max
        int count = 0;
        for (int num : nums) {
            if (num > minVal && num < maxVal) count++;
        }
        return count;
    }
}
```

> **Why this is optimal**  
> Two passes, no sorting, constant auxiliary space.  
> Works for negative numbers and duplicates without any extra checks.

---

### Python

```python
class Solution:
    def countElements(self, nums: List[int]) -> int:
        min_val = min(nums)
        max_val = max(nums)

        # Count elements strictly between min and max
        return sum(min_val < x < max_val for x in nums)
```

> **Pythonic touches**  
> `min()`/`max()` are highly optimized in CPython, giving the same O(n) performance as the manual loop.

---

### C++

```cpp
class Solution {
public:
    int countElements(vector<int>& nums) {
        int minVal = INT_MAX;
        int maxVal = INT_MIN;

        for (int x : nums) {
            minVal = min(minVal, x);
            maxVal = max(maxVal, x);
        }

        int count = 0;
        for (int x : nums) {
            if (x > minVal && x < maxVal) ++count;
        }
        return count;
    }
};
```

> **Fast and clean**  
> `INT_MAX`/`INT_MIN` from `<climits>` guarantee full range coverage.

---

## 🏆 Complexity Analysis

| Complexity | Explanation |
|------------|-------------|
| **Time**   | Two linear scans → **O(n)** |
| **Space**  | Constant auxiliary variables → **O(1)** |

Even for the maximum `n = 100`, this solution runs in microseconds and is trivially fast on any modern machine.

---

## 🔍 Edge‑Case & Debugging Checklist

| Edge Case | Why it matters | Typical Pitfall |
|-----------|----------------|-----------------|
| Array length = 1 | No element can have both smaller and greater values | Returning 0 automatically handles this |
| All elements equal | min == max → no element satisfies condition | Need to check `minVal < x < maxVal`, not `<=` |
| Negative numbers | Still work because min/max logic is agnostic | Forgetting to handle negatives in manual min/max loop |
| Duplicate min/max values | Only elements strictly between are counted | Counting min or max erroneously |

When debugging, always print `minVal`, `maxVal`, and the intermediate `count` to confirm logic.

---

## 🎯 How This Helps You Land Your Next Software Engineer Role

1. **Clear Problem‑Solving Strategy** – The blog demonstrates how to distill a problem to its core logic (finding min & max).  
2. **Multiple Language Mastery** – Showcasing Java, Python, and C++ implementations proves versatility – a key trait recruiters look for.  
3. **Optimal Complexity** – Highlighting O(n) time and O(1) space showcases algorithmic efficiency.  
4. **Clean, Production‑Ready Code** – Adheres to coding standards (naming, comments, readability) – exactly what hiring managers expect.  
5. **SEO‑Optimized Content** – By targeting keywords such as *LeetCode 2148, Java solution, Python algorithm, C++ interview*, your article becomes a valuable resource for candidates and recruiters alike, driving traffic and establishing authority.

---

## 📚 Takeaway

The “Count Elements With Strictly Smaller and Greater Elements” problem is deceptively simple. A single‑line observation—every qualifying element must lie strictly between the global minimum and maximum—lets you solve it with two linear passes. Implementing this in Java, Python, and C++ demonstrates algorithmic fluency across languages, while the concise, production‑grade code satisfies interviewers’ expectations.

Good luck on your next interview—remember: a solid understanding of core concepts plus clean, cross‑language implementations is your secret weapon. Happy coding! 🚀

---

### 📖 Further Reading & Resources

- LeetCode 2148: <https://leetcode.com/problems/count-elements-with-strictly-smaller-and-greater-elements>
- Java `Math.min`/`Math.max` docs
- Python `min()`/`max()` docs
- C++ `<algorithm>` and `<climits>` documentation

Feel free to adapt the snippets to your own coding style or project. Good luck!