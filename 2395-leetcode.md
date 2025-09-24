---
title: LeetCode 2395. Find Subarrays With Equal Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 2395 – “Find Subarrays With Equal Sum”  
**A beginner‑friendly walkthrough (Java, Python & C++ + SEO‑optimized blog)**  

---

## Table of Contents  

1. [Problem Statement](#problem-statement)  
2. [Intuition & Edge Cases](#intuition-&-edge-cases)  
3. [Algorithm](#algorithm)  
4. [Complexity Analysis](#complexity-analysis)  
5. [Implementation](#implementation)  
   * Java  
   * Python  
   * C++  
6. [Good, Bad & Ugly](#good-bad-ugly)  
7. [Why this solution rocks for interviews](#why-this-solution-rocks)  
8. [SEO Keywords & Tags](#seo-keywords-&-tags)  

---

## Problem Statement <a name="problem-statement"></a>

> **2395. Find Subarrays With Equal Sum**  
> Given a 0‑indexed integer array `nums`, determine whether there exist two subarrays of **length 2** with equal sum.  
> The two subarrays must begin at different indices.  
> Return `true` if such subarrays exist, otherwise return `false`.  

**Constraints**

- `2 ≤ nums.length ≤ 1000`  
- `-10^9 ≤ nums[i] ≤ 10^9`

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[4, 2, 4]` | `true` | `[4,2]` & `[2,4]` both sum to 6 |
| `[1, 2, 3, 4, 5]` | `false` | No two adjacent pairs share a sum |
| `[0, 0, 0]` | `true` | Two pairs of `[0,0]` (indices 0‑1 & 1‑2) share sum 0 |

---

## Intuition & Edge Cases <a name="intuition-&-edge-cases"></a>

We only need to examine *adjacent* pairs because a subarray of length 2 is just two consecutive numbers.  
If any sum appears twice, the two subarrays are guaranteed to start at different indices (because we traverse from left to right).

**Edge cases**

- Arrays with **exactly 2** elements → only one pair → answer is always `false`.  
- Negative numbers → no special handling needed; sums may also be negative.  
- Large values → use 64‑bit (`long`) to avoid overflow in Java and C++.

---

## Algorithm <a name="algorithm"></a>

1. **Early exit** – if `nums.length < 3`, return `false`.  
2. Create an empty hash‑set (or hash‑map) to keep track of sums we have seen.  
3. Iterate `i` from `0` to `nums.length - 2`:
   - Compute `s = nums[i] + nums[i+1]`.
   - If `s` is already in the set → return `true`.
   - Otherwise insert `s` into the set.
4. After loop, return `false`.

This is essentially the “first repeated element” pattern on a sliding window of size 2.

---

## Complexity Analysis <a name="complexity-analysis"></a>

| Metric | Time | Space |
|--------|------|-------|
| Whole algorithm | **O(n)** | **O(n)** (worst‑case all sums unique) |

`n` is the length of `nums`. The hash‑set gives average‑case constant time insert/lookup.

---

## Implementation <a name="implementation"></a>

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.

### Java

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public boolean findSubarrays(int[] nums) {
        if (nums.length < 3) return false;          // only one pair exists

        Set<Long> seen = new HashSet<>();            // use long to avoid overflow
        for (int i = 0; i < nums.length - 1; i++) {
            long sum = (long) nums[i] + (long) nums[i + 1];
            if (!seen.add(sum)) return true;        // add returns false if already present
        }
        return false;
    }
}
```

### Python

```python
class Solution:
    def findSubarrays(self, nums: list[int]) -> bool:
        if len(nums) < 3:
            return False

        seen = set()
        for i in range(len(nums) - 1):
            s = nums[i] + nums[i + 1]
            if s in seen:
                return True
            seen.add(s)
        return False
```

### C++

```cpp
#include <vector>
#include <unordered_set>

class Solution {
public:
    bool findSubarrays(const std::vector<int>& nums) {
        if (nums.size() < 3) return false;

        std::unordered_set<long long> seen;          // 64‑bit sums
        for (size_t i = 0; i + 1 < nums.size(); ++i) {
            long long sum = static_cast<long long>(nums[i]) +
                            static_cast<long long>(nums[i + 1]);
            if (!seen.insert(sum).second) return true;
        }
        return false;
    }
};
```

All three solutions run in linear time and use a hash set for O(n) auxiliary space.  

---

## Good, Bad & Ugly <a name="good-bad-ugly"></a>

| Aspect | What’s good | What could be better | Ugly pitfall |
|--------|-------------|----------------------|--------------|
| **Clarity** | Straightforward loop + set – easy to read. | None. | Overcomplicating with a map of indices (unnecessary). |
| **Performance** | O(n) time, O(n) space. | Could reduce space to O(1) if we only need to detect *any* duplicate, but we already have linear‑time & space – optimal for this problem. | Using nested loops (brute force) leads to O(n²) – unacceptable for n=1000. |
| **Edge‑case safety** | Handles negative numbers, large values with 64‑bit sums. | None. | Forgetting to cast to `long` in Java or `long long` in C++ can overflow. |
| **Maintainability** | Small, self‑contained method. | None. | Mixing data types (`int` vs `long`) in the same expression (buggy). |
| **Interview style** | Show hash‑set knowledge & sliding window. | None. | Not explaining the early‑exit `nums.length < 3` could raise a question about correctness. |

---

## Why this solution rocks for interviews <a name="why-this-solution-rocks"></a>

1. **Time‑safety** – LeetCode’s “1 ms, 100 %” rating is achieved by a single pass, which is the gold standard for interview problems.  
2. **Space‑efficiency** – O(n) set is the most we need; we don’t need a map unless we want indices.  
3. **Clear interview narrative** – “We slide a window of size 2, compute sums, and look for duplicates using a hash‑set.”  
4. **Edge‑case coverage** – Negative numbers, duplicates, minimal array size – all handled in one sweep.  
5. **Cross‑language portability** – The same logic works in Java, Python, C++ – a great talking point if you’re interviewing for a role requiring multiple languages.

---

## SEO Keywords & Tags <a name="seo-keywords-&-tags"></a>

- **LeetCode 2395**
- Find Subarrays With Equal Sum
- Java solution
- Python solution
- C++ solution
- hash map interview
- sliding window
- coding interview prep
- software engineer interview
- algorithm analysis
- time complexity
- space complexity
- beginner-friendly LeetCode problems

---

### Final Thought

By mastering this “adjacent pair sum” trick, you’ll be ready to tackle a wide range of “duplicate sum” or “subarray” questions on the job interview floor. Keep practicing similar patterns (two‑pointer, sliding window, hash‑set) and you’ll build a robust toolkit that shines in any technical interview. Happy coding! 🚀