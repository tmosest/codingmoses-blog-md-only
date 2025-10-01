---
title: LeetCode 2154. Keep Multiplying Found Values by Two - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2154. Keep Multiplying Found Values by Two – LeetCode Solution Guide  
*Java | Python | C++ |  SEO‑Optimized Blog Post | *The Good, the Bad, and the Ugly*

---

### TL;DR
- **Problem** – Repeatedly double `original` while it exists in `nums`.  
- **Solution** – Store `nums` in a hash‑set (`HashSet`/`unordered_set`) and loop while the current value is present.  
- **Complexity** – `O(n)` time, `O(n)` space.  
- **Why it’s great** – One‑liner logic, no nested loops, perfect for an interview.

---

## 1. Problem Recap

LeetCode 2154 – *Keep Multiplying Found Values by Two*  
```text
Input:  nums = [5,3,6,1,12], original = 3
Output: 24
```
The algorithm:  
1. If `original` is in `nums`, set `original = 2 * original`.  
2. Repeat until the current value isn’t in `nums`.  
3. Return the final value.

Constraints  
- `1 <= nums.length <= 1000`  
- `1 <= nums[i], original <= 1000`

---

## 2. The “Good”

| ✅ | Reason |
|----|--------|
| **O(n) time** | One pass to build the set + one pass over potential doublings. |
| **O(n) space** | Extra set for fast lookup. |
| **Simplicity** | Clear, concise, no nested loops. |
| **Scalable** | Works for any array size up to the constraint limit. |

---

## 3. The “Bad”

| ⚠️ | Issue |
|-----|-------|
| **Linear search alternative** | A naïve `for` loop inside a `while` would be `O(n²)` – unacceptable for larger inputs. |
| **Repeated scans** | Without a set, each check forces another scan of the array. |

---

## 4. The “Ugly”

| 😬 | Caveat |
|-----|--------|
| **Overflow risk** | If `original` were unconstrained, multiplying by 2 could overflow `int`. Use `long` or guard against overflow. |
| **Infinite loop guard** | With malformed input (e.g., `original = 0`), the loop would never terminate. Validate input. |
| **Set overhead** | For tiny arrays, the set may seem overkill, but it guarantees linear time regardless of array size. |

---

## 5. Implementation

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

> **Tip:** The code follows the LeetCode signature exactly, so you can paste it into the editor and run tests instantly.

### 5.1 Java (LeetCode signature)

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int findFinalValue(int[] nums, int original) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);

        while (set.contains(original)) {
            original *= 2;
        }
        return original;
    }
}
```

### 5.2 Python (LeetCode signature)

```python
class Solution:
    def findFinalValue(self, nums: List[int], original: int) -> int:
        nums_set = set(nums)
        while original in nums_set:
            original *= 2
        return original
```

### 5.3 C++ (LeetCode signature)

```cpp
class Solution {
public:
    int findFinalValue(vector<int>& nums, int original) {
        unordered_set<int> st(nums.begin(), nums.end());
        while (st.find(original) != st.end()) {
            original *= 2;
        }
        return original;
    }
};
```

---

## 6. Complexity Analysis

| Metric | Explanation |
|--------|-------------|
| **Time** | `O(n)` – building the set is `O(n)`; each doubling checks membership in `O(1)` average. In the worst case, we double at most `log₂(1000)` ≈ 10 times. |
| **Space** | `O(n)` – the hash‑set holds all elements of `nums`. |

---

## 7. Edge‑Case Testing

| Test | Expected |
|------|----------|
| `nums = [1]`, `original = 1` | 2 |
| `nums = [1,2,4,8]`, `original = 1` | 16 |
| `nums = [2,3,4]`, `original = 5` | 5 |
| `nums = [1000]`, `original = 500` | 500 (not present) |
| `nums = [1,1,1]`, `original = 1` | 2 (set removes duplicates) |

---

## 8. Alternatives & Trade‑offs

| Approach | Pros | Cons |
|----------|------|------|
| **Linear search** (`for` inside `while`) | No extra space | `O(n²)` time |
| **Sort + binary search** | Faster lookup without extra space | `O(n log n)` preprocessing |
| **Bit manipulation** | Not applicable – problem is set‑membership, not bitwise operations |

---

## 9. Why This Matters in Interviews

- **Demonstrates knowledge of data structures** (hash sets).  
- **Shows ability to reason about time/space**.  
- **Highlights clean coding style** – minimal lines, clear variable names.  
- **Prepares you for similar “while‑exists‑then‑double” interview questions** (e.g., *Find the longest chain of multiples*).

---

## 10. Final Thoughts

The problem is deceptively simple, but it tests your ability to pick the right data structure.  
**Takeaway:**  
> *If you need fast membership tests, use a hash set.*

Good code, good interview performance, and a solid addition to your portfolio. Happy coding! 🚀

---

## 11. SEO Keywords & Meta Description

- **Keywords**: LeetCode 2154, Keep Multiplying Found Values by Two, Java solution, Python solution, C++ solution, hash set, time complexity, interview prep, software engineer interview, coding challenge, algorithm design.  
- **Meta Description**: “Solve LeetCode 2154 – Keep Multiplying Found Values by Two. Get Java, Python, and C++ solutions, step‑by‑step explanations, complexity analysis, and interview tips. Boost your coding interview game today.”

---

> **Author** – [Your Name], software engineer & algorithm enthusiast.  
> **Date** – 2025‑10‑01  

---