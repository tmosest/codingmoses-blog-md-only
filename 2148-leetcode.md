---
title: LeetCode 2148. Count Elements With Strictly Smaller and Greater Elements - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 LeetCode 2148: Count Elements With Strictly Smaller and Greater Elements  
*The “Good, the Bad, and the Ugly” – Java | Python | C++*  

**Meta‑Description**  
Learn how to solve LeetCode 2148 in O(N) time with a clean O(1) space solution. Get Java, Python, and C++ code, an in‑depth explanation, edge‑case discussion, and SEO‑friendly blog post that’ll help you ace your coding interviews and land your dream job.

---

### 📌 Problem Statement (LeetCode 2148)

> **Count Elements With Strictly Smaller and Greater Elements**  
> **Difficulty:** Easy  
> **Constraints**  
> - 1 ≤ nums.length ≤ 100  
> - -10⁵ ≤ nums[i] ≤ 10⁵  

> **Given** an integer array `nums`, return the number of elements that have *both* a strictly smaller **and** a strictly greater element present somewhere in the array.

> **Example**  
> ```
> Input:  nums = [11, 7, 2, 15]
> Output: 2
> Explanation: 7 and 11 satisfy the condition.
> ```

---

### 🏗️ Why This Problem Is a “Great Interview Bite”

- **Low‑Complexity & Conceptual Depth** – The trick lies in recognizing that *only the global minimum and maximum matter*.  
- **Edge‑Case Handling** – Duplicates, all‑equal arrays, or single‑element arrays test your understanding of “strictly” versus “non‑strict”.  
- **Language‑agnostic Approach** – The same logic translates to Java, Python, C++, or even JavaScript.  

---

## ✅ Solution Overview (Good)

1. **Find the global minimum (`minVal`) and maximum (`maxVal`)** in one pass.  
2. **Count elements that lie strictly between** `minVal` and `maxVal`.  
   - An element `x` qualifies if `minVal < x < maxVal`.  
3. Return the count.

> **Why this is “Good”**  
> *Linear time*, *constant extra space*, and *no sorting* – the most efficient approach.

---

## ⚠️ Common Pitfalls (Bad)

| Pitfall | What It Looks Like | Fix |
|---------|--------------------|-----|
| **Sorting the array** | Using `Arrays.sort()` then iterating – adds O(N log N) time unnecessarily. | Skip sorting; just track min & max. |
| **Using `<=` instead of `<`** | Counts elements equal to `minVal` or `maxVal` erroneously. | Strictly compare with `<` and `>`. |
| **Ignoring duplicates** | Thinking duplicates reduce the answer; they don’t matter. | Treat each occurrence independently. |

---

## 😱 Edge Cases (Ugly)

- **All elements equal** → `count = 0`.  
- **Only one element** → `count = 0`.  
- **Array of two elements** → `count = 0` (no element can have both sides).  
- **Negative numbers** – min and max logic still holds.  
- **Large numbers** (±10⁵) – no overflow risk with `int` in Java/C++ or `int` in Python.

---

## 💻 Code Implementations

### 1️⃣ Java (Optimal + Easy)

```java
public class Solution {
    public int countElements(int[] nums) {
        int minVal = Integer.MAX_VALUE;
        int maxVal = Integer.MIN_VALUE;

        // First pass: find min & max
        for (int num : nums) {
            if (num < minVal) minVal = num;
            if (num > maxVal) maxVal = num;
        }

        // Second pass: count strictly between
        int count = 0;
        for (int num : nums) {
            if (num > minVal && num < maxVal) count++;
        }
        return count;
    }
}
```

> **Complexity** – `O(n)` time, `O(1)` auxiliary space.

---

### 2️⃣ Python

```python
class Solution:
    def countElements(self, nums: List[int]) -> int:
        min_val = min(nums)
        max_val = max(nums)

        return sum(1 for x in nums if min_val < x < max_val)
```

> **Complexity** – `O(n)` time, `O(1)` space (aside from input list).

---

### 3️⃣ C++

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

        int cnt = 0;
        for (int x : nums) {
            if (x > minVal && x < maxVal) cnt++;
        }
        return cnt;
    }
};
```

> **Complexity** – `O(n)` time, `O(1)` auxiliary space.

---

## 📚 How to Explain This in a Job Interview

1. **State the Problem Clearly** – Mention the need for *strictly smaller* and *strictly greater* elements.  
2. **Explain the Observation** – Only the global min and max matter; any element outside that range cannot satisfy the condition.  
3. **Show the Two‑Pass Algorithm** – Talk about time/space trade‑offs.  
4. **Address Edge Cases** – Highlight duplicates, single‑element arrays, etc.  
5. **Optional** – Mention that a single pass is possible with a `minSeen` & `maxSeen` strategy but two passes are simpler and perfectly acceptable for `n ≤ 100`.  

---

## 📈 SEO‑Optimized Summary

| Keyword | Usage |
|---------|-------|
| LeetCode 2148 | Title, headings, intro |
| Count Elements With Strictly Smaller and Greater Elements | Problem description |
| Java solution LeetCode 2148 | Java code block & section |
| Python solution LeetCode 2148 | Python code block & section |
| C++ solution LeetCode 2148 | C++ code block & section |
| LeetCode interview question | Blog intro |
| algorithm time complexity | Complexity section |
| coding interview tips | Conclusion |
| data structures | Mention arrays |

> **Final SEO‑friendly tagline**  
> *“Master LeetCode 2148 with Java, Python, and C++ solutions – quick, clean, and interview‑ready.”*

---

### 🎉 Wrap‑Up

- **Good**: Linear time, constant space, no sorting.  
- **Bad**: Over‑engineering with sorting or misuse of comparison operators.  
- **Ugly**: Forgetting duplicates or handling of edge cases.  

Implement the simple two‑pass approach and you’ll get the perfect solution every time—ready for your next coding interview or to impress your hiring manager.

Happy coding! 🚀