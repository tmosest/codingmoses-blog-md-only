---
title: LeetCode 2150. Find All Lonely Numbers in the Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 2150 – Find All Lonely Numbers in the Array  
**Java | Python | C++** solutions + an SEO‑optimized blog article that covers the *good, the bad, and the ugly* of this problem – a perfect post‑mortem for your next coding interview.

> **Keywords:** *LeetCode 2150, Find All Lonely Numbers, coding interview, Java solution, Python solution, C++ solution, algorithm, time complexity, space complexity, hash map, frequency array.*

---

## 🎯 Problem Summary

You’re given an integer array `nums`.  
A number `x` is **lonely** if:

1. `x` occurs **exactly once** in the array.
2. Neither `x-1` nor `x+1` appears in the array.

Return all lonely numbers in any order.

*Example*  
`nums = [10,6,5,8] → [10,8]`  
Both 10 and 8 appear once, and neither has adjacent numbers in the array.

**Constraints**

| Constraint | Value |
|------------|-------|
| `1 ≤ nums.length ≤ 10⁵` | |
| `0 ≤ nums[i] ≤ 10⁶` | |

---

## 🚀 Solution Overview

There are three common strategies:

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| **Sorting** | `O(n log n)` | `O(1)` (in‑place) | Simple but not optimal for the given constraints. |
| **HashMap / HashSet** | `O(n)` | `O(n)` | Handles arbitrary ranges easily. |
| **Frequency Array** | `O(n)` | `O(max(nums)+3)` | Fastest if `max(nums)` is small (≤ 1 e6). |

The **frequency array** is the fastest for this problem because the value range is bounded.  
We’ll illustrate that in the *good* section and contrast it with the *bad* sorting approach.

---

## 🔧 Code Implementations

> All three snippets follow the **frequency‑array** strategy for `O(n)` time and `O(max(nums)+3)` space.

### 1. Java

```java
import java.util.*;

public class Solution {
    public List<Integer> findLonely(int[] nums) {
        // Find the maximum value to size the frequency array.
        int max = 0;
        for (int v : nums) max = Math.max(max, v);

        int[] freq = new int[max + 3];   // +3 to safely access v-1 and v+1

        // Build frequencies.
        for (int v : nums) {
            freq[v]++;          // Count occurrences.
        }

        List<Integer> lonely = new ArrayList<>();

        // Check each unique number.
        for (int v : nums) {
            if (freq[v] == 1 && freq[v - 1] == 0 && freq[v + 1] == 0) {
                lonely.add(v);
            }
        }

        return lonely;
    }
}
```

> **Why `max + 3`?**  
> The array needs to support indices `v-1` and `v+1`. Adding 3 ensures we never hit `ArrayIndexOutOfBoundsException` even for `v = max`.

---

### 2. Python

```python
from typing import List

class Solution:
    def findLonely(self, nums: List[int]) -> List[int]:
        max_val = max(nums)
        freq = [0] * (max_val + 3)          # +3 for safe v-1, v+1 access

        for v in nums:
            freq[v] += 1

        lonely = []
        for v in nums:
            if freq[v] == 1 and freq[v - 1] == 0 and freq[v + 1] == 0:
                lonely.append(v)

        return lonely
```

---

### 3. C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> findLonely(std::vector<int>& nums) {
        int maxVal = *std::max_element(nums.begin(), nums.end());
        std::vector<int> freq(maxVal + 3, 0);   // +3 for v-1 and v+1

        for (int v : nums) {
            freq[v]++;          // Count each occurrence
        }

        std::vector<int> lonely;
        for (int v : nums) {
            if (freq[v] == 1 && freq[v - 1] == 0 && freq[v + 1] == 0) {
                lonely.push_back(v);
            }
        }

        return lonely;
    }
};
```

> In all three languages, we first build a frequency table and then scan the array once more to pick out the lonely numbers.

---

## 📚 The Good, The Bad, and The Ugly

| Phase | What we learned | Why it matters |
|-------|-----------------|----------------|
| **The Good** | *Frequency array* gives **linear time** (`O(n)`) and minimal constant‑factor overhead. Works perfectly when the input domain (`0‑10⁶`) is bounded. | In interviews, showing awareness of input constraints lets interviewers see that you can tailor solutions. |
| **The Bad** | *Sorting* (`O(n log n)`) is simple but wasteful for this problem. It also breaks when numbers exceed `int` range or when you’re asked for `O(1)` space. | Over‑engineering or choosing a suboptimal algorithm can cost you points. |
| **The Ugly** | Relying on a **HashMap** or **unordered_map** works, but you may forget to handle `-1` or `+1` edge‑cases, leading to out‑of‑bounds or logic bugs. | Many interviewers test your attention to detail; always validate neighbors and be careful with array indices. |

### TL;DR

- **Prefer the frequency array** whenever the value range is known and small.
- Avoid sorting unless it’s required (e.g., for a tie‑breaking order).
- Double‑check boundary conditions: `v-1` when `v = 0`, and `v+1` when `v = max(nums)`.

---

## 📈 Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| Frequency Array (ours) | `O(n)` | `O(max(nums)+3)` (≈ 1 e6) |
| HashMap | `O(n)` | `O(n)` |
| Sorting | `O(n log n)` | `O(1)` (in‑place) |

> **Practical note:**  
> Even though the frequency array uses about 4 MB for `max = 1 e6`, that’s trivial on modern machines and well within LeetCode’s limits.

---

## 📝 Final Thoughts & Interview Tips

1. **Ask for constraints** early. If the interviewer mentions a bounded range, propose a frequency array right away.
2. **Explain the logic** clearly: “Count occurrences → check neighbors → collect lonely numbers.”  
   Visual aids (like a simple table) can help.
3. **Edge cases**: empty array (not allowed by constraints), single element, duplicates, boundaries (`0` and `max`).
4. **Testing**: Run a quick sanity test (`[1, 2, 3, 5, 7] → [5, 7]`).
5. **Follow‑up**: “Could we do better in space?” – The answer: not much; a hash map needs `O(n)` space, while the array is already optimal given the input domain.

---

## 🎯 SEO‑Optimized Closing

If you’re preparing for a software engineering interview, mastering *LeetCode 2150 – Find All Lonely Numbers in the Array* demonstrates:

- Your ability to analyze input constraints and choose an optimal data structure.
- Your proficiency in Java, Python, and C++.
- Your skill in explaining algorithmic trade‑offs (good, bad, ugly).

Keep this problem on your radar: it’s a classic interview favorite that tests both **frequency counting** and **neighbor‑checking** logic. Happy coding! 🚀

--- 

*Sources: Official LeetCode problem 2150, and community solutions such as those by “Subrata Jana” and “Kushal Bharadwaj”.*