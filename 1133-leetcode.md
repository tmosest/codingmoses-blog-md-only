---
title: LeetCode 1133. Largest Unique Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1133. Largest Unique Number  
### ✅ Java | ✅ Python | ✅ C++ Solutions + SEO‑Optimized Blog Post  

---

## 1. Quick Code Reference (Three Languages)

```java
// ---------- Java ----------
import java.util.*;

class Solution {
    public int largestUniqueNumber(int[] nums) {
        Map<Integer,Integer> freq = new HashMap<>();
        for (int n : nums) freq.merge(n, 1, Integer::sum);

        int ans = -1;
        for (Map.Entry<Integer,Integer> e : freq.entrySet()) {
            if (e.getValue() == 1 && e.getKey() > ans) ans = e.getKey();
        }
        return ans;
    }
}
```

```python
# ---------- Python ----------
from collections import Counter
from typing import List

class Solution:
    def largestUniqueNumber(self, nums: List[int]) -> int:
        freq = Counter(nums)
        ans = -1
        for num, cnt in freq.items():
            if cnt == 1 and num > ans:
                ans = num
        return ans
```

```cpp
// ---------- C++ ----------
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int largestUniqueNumber(vector<int>& nums) {
        unordered_map<int,int> freq;
        for (int n : nums) ++freq[n];

        int ans = -1;
        for (auto &p : freq)
            if (p.second == 1 && p.first > ans) ans = p.first;
        return ans;
    }
};
```

> **Time Complexity**: `O(n)` – one pass to count, one pass over the map.  
> **Space Complexity**: `O(n)` – at most one entry per unique element.

---

## 2. SEO‑Optimized Blog Article  

### Title  
**“LeetCode 1133 – Largest Unique Number: Java, Python & C++ Solutions, Time‑Space Analysis & Interview Tips”**

---

### Meta Description  
Discover the fastest way to solve LeetCode 1133 “Largest Unique Number” in Java, Python, and C++. Understand the algorithm, explore alternative solutions, and learn interview‑ready tricks to impress hiring managers.

---

### Table of Contents  
1. 📌 Problem Statement  
2. 🚀 Constraints & Edge Cases  
3. 🎯 The Straightforward Hash‑Map Approach  
4. 🧠 Alternative: Counting Sort (When 0 ≤ nums[i] ≤ 1000)  
5. 🏎️ Performance Analysis (Time & Space)  
6. ❌ Common Pitfalls & “The Ugly”  
7. 🤝 Interview Tips & Talking Points  
8. 📚 Further Reading & Resources  

---

## 2.1 Problem Statement  

**LeetCode 1133 – Largest Unique Number**

> Given an integer array `nums`, return the **largest integer that only occurs once**.  
> If no integer occurs only once, return `-1`.  

Examples  
| Input | Output | Explanation |
|-------|--------|-------------|
| `[5,7,3,9,4,9,8,3,1]` | `8` | `9` repeats, `8` is the largest unique number. |
| `[9,9,8,8]` | `-1` | All numbers repeat. |

---

## 2.2 Constraints & Edge Cases  

| Constraint | Value | Why It Matters |
|------------|-------|----------------|
| `1 <= nums.length <= 2000` | Small | Brute‑force still viable, but hash‑map is clean. |
| `0 <= nums[i] <= 1000` | Limited | Allows counting‑sort optimization. |
| Negative numbers? | No | Simplifies array‑based counting. |
| All duplicates? | Possible | Must return `-1`. |
| All unique? | Possible | Return max element. |

---

## 2.3 The Straightforward Hash‑Map Approach  

### Why It Works  

1. **Frequency Count** – Map each value to how many times it appears.  
2. **Scan for Largest Unique** – Iterate through the map; keep the maximum key whose count is `1`.

### Step‑by‑Step Pseudocode  

```
function largestUniqueNumber(nums):
    freq = empty hash map
    for num in nums:
        freq[num] = freq.get(num, 0) + 1

    ans = -1
    for (num, count) in freq:
        if count == 1 and num > ans:
            ans = num
    return ans
```

### Implementation Highlights  

| Language | Key Feature | Code Snippet |
|----------|-------------|--------------|
| **Java** | `merge()` simplifies counting | `freq.merge(n, 1, Integer::sum);` |
| **Python** | `Counter` from `collections` | `freq = Counter(nums)` |
| **C++** | `unordered_map` | `++freq[n];` |

---

## 2.4 Alternative: Counting Sort (When 0 ≤ nums[i] ≤ 1000)  

Because every element is bounded by `1000`, we can use an array of size `1001` to count frequencies in **O(n)** time **and** **O(1)** additional space.

```python
def largestUniqueNumber(nums):
    count = [0] * 1001
    for n in nums:
        count[n] += 1
    for i in range(1000, -1, -1):
        if count[i] == 1:
            return i
    return -1
```

**Pros**  
- Constant‑time array access.  
- No hash‑map overhead.

**Cons**  
- Requires knowledge of value bounds.  
- Memory wasted if bounds are huge.

---

## 2.5 Performance Analysis  

| Approach | Time | Space | Suitability |
|----------|------|-------|-------------|
| Hash‑Map | `O(n)` | `O(u)` (`u` = unique values) | General case, handles negative values. |
| Counting Array | `O(n)` | `O(1)` (fixed 1001) | Optimal when bounds are tight and known. |
| Brute‑Force | `O(n²)` | `O(1)` | Acceptable for `n <= 2000`, but slower. |

---

## 2.6 Common Pitfalls & “The Ugly”  

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| **Using `HashMap` but forgetting to handle duplicates** | May return wrong maximum | Check `count == 1` before updating `ans`. |
| **Returning `0` when all numbers are duplicates** | Wrong answer | Initialize `ans = -1` (per spec). |
| **Iterating in arbitrary order (e.g., `for (int key : freq.keySet())`) and not comparing to `ans`** | Might return a smaller unique number | Always keep `ans = max(ans, key)` when `count == 1`. |
| **Using `ArrayList` of `Integer` for counting instead of `int[]`** | Extra boxing overhead | Prefer primitive arrays or `HashMap<Integer, Integer>`. |
| **Assuming input fits in `int` when using `long`** | Unnecessary memory | Stick to `int` as per constraints. |

---

## 2.7 Interview Tips & Talking Points  

1. **Clarify Constraints** – Ask if values can be negative or if bounds are fixed.  
2. **Explain Complexity Early** – Show `O(n)` time and `O(n)` space.  
3. **Discuss Edge Cases** – All duplicates, all unique, single element array.  
4. **Show Trade‑Offs** – Hash‑map vs counting array.  
5. **Mention Code Reusability** – The same logic can be adapted to “largest number that appears exactly k times”.  

---

## 2.8 Further Reading & Resources  

- LeetCode Problem: [Largest Unique Number](https://leetcode.com/problems/largest-unique-number/)  
- Discussion Thread: “Best solution for LeetCode 1133” – community‑rated ideas.  
- HashMap vs HashTable – Understanding Java collections.  
- Counting Sort – Why it's linear for bounded integers.  

---

## 3. Final Thoughts  

Solving LeetCode 1133 is a great interview starter that teaches you to:  

- Use hash tables for frequency counting.  
- Optimize with counting sort when constraints allow.  
- Translate a concise algorithm into clean Java, Python, or C++ code.  

Whether you’re prepping for a hiring pipeline at a big tech company or a startup, the pattern of “count → filter → maximize” is widely applicable. Keep the code simple, the comments clear, and always double‑check edge cases. Good luck on your next coding interview!

---