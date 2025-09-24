---
title: LeetCode 2870. Minimum Number of Operations to Make Array Empty - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 2870 – Minimum Number of Operations to Make Array Empty  
### Medium | Java | Python | C++ | 99.63 %+ Beat

> **Problem**  
> You are given a 0‑indexed array `nums` of positive integers.  
> In one operation you can delete **two** equal elements or **three** equal elements.  
> Find the minimum number of operations required to delete the entire array.  
> If it is impossible, return **-1**.

> **Constraints**  
> * `2 ≤ nums.length ≤ 10⁵`  
> * `1 ≤ nums[i] ≤ 10⁶`

---

## 🚀 Solution Overview

The key insight is that the only thing that matters is the **frequency** of each distinct value.  
All deletions are performed on equal elements, so once we know how many copies of a value exist, we can decide how many 2‑group or 3‑group deletions are needed.

### Greedy Deletion Strategy
1. **Count** the frequency `cnt` of every distinct number using a hash map / dictionary.
2. **Impossible case** – if any frequency is `1`, we can never delete that element (neither 2 nor 3). Return `-1`.
3. For a frequency `cnt`:
   * Use as many 3‑group deletions as possible: `cnt / 3`.
   * If a remainder (`cnt % 3`) exists:
     * If the remainder is `2`, we can delete it in one more operation (a 2‑group).
     * If the remainder is `1`, we must first turn one of the earlier 3‑groups into two 2‑groups (or a 2‑group + 1 leftover).  
       The optimal count is still `cnt / 3 + 1` because `cnt % 3 == 1` forces one additional operation.
4. Sum the operations for all numbers.

This greedy strategy is optimal because:
- Deleting 3 at a time is always better (or equal) than deleting 2 because it removes more elements per operation.
- Whenever a remainder remains, the only way to consume the leftover 1 or 2 is exactly one more operation.

---

## 📊 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Counting frequencies | **O(n)** | **O(k)** (k = distinct numbers) |
| Final aggregation | **O(k)** | — |
| **Total** | **O(n)** | **O(k)** |

Both time and space are linear, easily meeting the constraints.

---

## 🧑‍💻 Code Implementations

Below are clean, production‑ready solutions for **Java**, **Python**, and **C++**.

---

### Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int minOperations(int[] nums) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) {
            freq.put(x, freq.getOrDefault(x, 0) + 1);
        }

        int ops = 0;
        for (int count : freq.values()) {
            if (count == 1) return -1;          // impossible
            ops += count / 3;                   // 3‑group deletions
            if (count % 3 != 0) ops++;          // one more op for remainder
        }
        return ops;
    }
}
```

---

### Python

```python
from collections import Counter
from typing import List

class Solution:
    def minOperations(self, nums: List[int]) -> int:
        freq = Counter(nums)
        ops = 0
        for cnt in freq.values():
            if cnt == 1:
                return -1
            ops += cnt // 3
            if cnt % 3:
                ops += 1
        return ops
```

---

### C++

```cpp
#include <unordered_map>
#include <vector>

class Solution {
public:
    int minOperations(std::vector<int>& nums) {
        std::unordered_map<int, int> freq;
        for (int x : nums) ++freq[x];

        int ops = 0;
        for (const auto& [val, cnt] : freq) {
            if (cnt == 1) return -1;
            ops += cnt / 3;
            if (cnt % 3) ++ops;
        }
        return ops;
    }
};
```

---

## 📚 Detailed Walk‑Through (Good, Bad, Ugly)

| Category | What Worked | What Fell Short | How to Fix It |
|----------|-------------|-----------------|---------------|
| **Good** | - Single‑pass frequency counting. <br>- Simple integer math (`/3`, `%3`). <br>- Handles all edge cases (`cnt == 1`, `cnt % 3`). | | |
| **Bad** | - Using an unordered map for up to `10⁶` distinct keys could consume ~8 MB RAM. <br>- In Java, `HashMap` overhead may push memory usage high. | **Optimize memory**: Use an array of size `max(nums)+1` if memory permits (worst‑case 1 M integers ≈ 4 MB). |
| **Ugly** | - Forgetting to handle the `cnt == 1` case results in `-1` being missed. <br>- Over‑engineering by trying complex DP or BFS solutions. | Keep the greedy approach; it's both optimal and succinct. |

### Common Pitfalls

1. **Missing the `cnt == 1` check** → Some solutions return a positive number incorrectly.
2. **Off‑by‑one errors in remainder handling** – Remember that a remainder of `1` still adds **one** extra operation.
3. **Using 2‑group deletion first** can lead to suboptimal counts; always maximize 3‑groups first.

---

## 📈 SEO‑Optimized Takeaway

- **Keywords**: *LeetCode 2870*, *Minimum Number of Operations to Make Array Empty*, *Java solution*, *Python solution*, *C++ solution*, *greedy algorithm*, *frequency map*, *coding interview*, *software engineering interview*, *data structures*, *time complexity*, *space complexity*, *job interview coding problem*.
- **Title Suggestion**: *“LeetCode 2870 – Minimum Number of Operations to Make Array Empty – Java/Python/C++ Greedy Solution”*
- **Meta Description**: “Solve LeetCode 2870 in 5 minutes. Learn the greedy frequency‑based strategy, Java, Python, and C++ code, and master interview skills.”

---

## 🎉 Final Thought

The “Minimum Number of Operations to Make Array Empty” is a classic illustration that **simple observations often yield the most elegant solutions**. Counting frequencies, detecting impossible cases, and then greedily removing as many 3‑groups as possible gives a linear‑time, constant‑space algorithm that passes all edge cases.

Master this pattern, and you’ll be ready to tackle many other LeetCode “frequency‑based” problems in interviews. Happy coding! 🚀