---
title: LeetCode 1852. Distinct Numbers in Each Subarray - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1852 – Distinct Numbers in Every Sub‑Array  
**Java / Python / C++ implementations + a “Good‑Bad‑Ugly” interview blog**

> **Keywords**: LeetCode 1852, distinct numbers, sub‑array, sliding window, Java, Python, C++, interview prep, algorithm, time complexity, space complexity, coding interview, job interview, data structure, hashmap, dictionary, vector.

---

## 1. Problem Recap

> **Given** an integer array `nums` (length `n`) and an integer `k` (`1 ≤ k ≤ n`).  
> **Task**: For every contiguous window of size `k`, output the count of **distinct** elements.

```
Input   : nums = [1,2,3,2,2,1,3],  k = 3
Output  : [3,2,2,2,3]
```

The array length can be up to `10^5`, so an `O(n·k)` brute‑force solution will time‑out.

---

## 2. Solution Strategy – Sliding Window + Hash Map

1. **Window**: Keep a window `[left, right)` of size `k`.  
2. **Hash Map**: Store each number’s frequency inside the window.  
3. **Move**:
   * Add `nums[right]` to the map (increase its count).  
   * If the window size reaches `k`, record `map.size()` (number of keys).  
   * Slide the window: remove `nums[left]` (decrease its count, erase if zero).  
4. **Repeat** until `right` reaches `n`.

This runs in `O(n)` time and uses `O(k)` (at most `k` distinct numbers) space.

---

## 3. Code Implementations

> All implementations follow the same logic but use language‑specific constructs.

### 3.1 Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int[] distinctNumbers(int[] nums, int k) {
        if (nums == null || nums.length < k) return new int[0];

        int n = nums.length;
        int[] ans = new int[n - k + 1];
        Map<Integer, Integer> freq = new HashMap<>();

        int left = 0, right = 0, idx = 0;

        while (right < n) {
            freq.put(nums[right], freq.getOrDefault(nums[right], 0) + 1);
            right++;

            if (right - left == k) {
                ans[idx++] = freq.size();

                int leftVal = nums[left++];
                if (--freq.get(leftVal) == 0) freq.remove(leftVal);
            }
        }
        return ans;
    }
}
```

> **Complexities** – Time `O(n)`, Space `O(k)`.

---

### 3.2 Python

```python
from typing import List
from collections import defaultdict

class Solution:
    def distinctNumbers(self, nums: List[int], k: int) -> List[int]:
        if not nums or len(nums) < k:
            return []

        n = len(nums)
        ans = []
        freq = defaultdict(int)

        left = 0
        for right, val in enumerate(nums):
            freq[val] += 1

            if right - left + 1 == k:
                ans.append(len(freq))
                left_val = nums[left]
                freq[left_val] -= 1
                if freq[left_val] == 0:
                    del freq[left_val]
                left += 1
        return ans
```

> **Complexities** – Time `O(n)`, Space `O(k)`.

---

### 3.3 C++

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    vector<int> distinctNumbers(vector<int>& nums, int k) {
        if (nums.empty() || nums.size() < k) return {};

        int n = nums.size();
        vector<int> ans;
        unordered_map<int, int> freq;

        int left = 0;
        for (int right = 0; right < n; ++right) {
            ++freq[nums[right]];

            if (right - left + 1 == k) {
                ans.push_back(freq.size());

                int leftVal = nums[left++];
                if (--freq[leftVal] == 0) freq.erase(leftVal);
            }
        }
        return ans;
    }
};
```

> **Complexities** – Time `O(n)`, Space `O(k)`.

---

## 4. “Good, Bad, Ugly” Interview Take‑aways

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Concept** | Sliding window + frequency map – classic, interview‑friendly. | None (easy to implement). | None. |
| **Time Complexity** | `O(n)` – optimal for `n = 10^5`. | ❌ Brute‑force `O(n·k)` fails on large input. | ❌ Re‑computing distinct counts from scratch for every window. |
| **Space Complexity** | `O(k)` – only need counts for current window. | ❌ Storing all prefix sums or pre‑computed distinct sets consumes `O(n·k)`. | ❌ Using an array of size `max(nums)+1` (`10^5`) when only `k` distinct needed – wasted memory. |
| **Edge Cases** | Handles `k = 1`, `k = n`, repeated numbers. | ❌ Forgetting to delete key when count goes to zero → inflated distinct count. | ❌ Off‑by‑one errors when sliding window indices. |
| **Language Nuances** | Java’s `HashMap.getOrDefault`, Python’s `defaultdict`, C++’s `unordered_map`. | ❌ In Java: using `Map` instead of `HashMap` may cause unnecessary overhead. | ❌ In C++: using `map` (red‑black tree) → `O(log k)` per operation, slower. |

> **Key Take‑away**: Use an *unordered* hash structure, update counts in `O(1)` per step, and always remove entries when the count reaches zero to keep the map size accurate.

---

## 5. Testing & Edge‑Case Checklist

| Test | Description | Expected |
|------|-------------|----------|
| `nums = [1,1,1], k = 1` | All windows of size 1 | `[1,1,1]` |
| `nums = [1,2,3,4], k = 4` | Entire array | `[4]` |
| `nums = [5], k = 1` | Single element | `[1]` |
| `nums = [2,2,2,2,2], k = 3` | All duplicates | `[1,1,1]` |
| `nums = [1,2,1,2,1,2], k = 2` | Alternating | `[2,2,2,2,2]` |

Run the provided code with a unit test harness or `main` method to validate.

---

## 6. Performance Tips

1. **Avoid unnecessary object creation** – re‑use the same map/dict; clear only when needed.
2. **Prefer `unordered_map` over `map` in C++** for `O(1)` average lookups.
3. **In Java, use `HashMap` with initial capacity** `k` to reduce rehashing (`new HashMap<>(k)`).
4. **Early return** for edge cases (`k > n`, `nums == null`) to save time.

---

## 7. Interview Story – “The Sliding Window Saga”

> *“I once faced a leetcode question asking for distinct counts in every subarray. My first instinct was a nested loop; after a few minutes of stack overflow, I remembered the sliding window trick. I coded a hash map to keep frequencies and slid the window in linear time. The interviewer was impressed by the `O(n)` complexity and the clean removal of keys when counts dropped to zero. That small detail about deleting zero‑count keys saved my solution from being wrong on test cases with many repeats.”*

---

## 8. Final Thoughts

- **Why this matters for a job interview**:  
  * Demonstrates mastery of two core data structures: hash tables and sliding windows.  
  * Shows awareness of time‑space trade‑offs.  
  * Indicates careful handling of edge cases and memory management.

- **Next steps**:  
  * Practice variations – “Count distinct values in a range” (offline queries).  
  * Explore more advanced data structures: `Fenwick Tree` or `Segment Tree` for dynamic updates.  
  * Build a personal repo of sliding window solutions for quick recall during interviews.

---

### 🎯 SEO Meta Description

> “Solve LeetCode 1852 – Distinct Numbers in Each Subarray. Learn Java, Python, and C++ sliding‑window solutions, time & space analysis, and interview‑ready tips. Boost your coding interview prep and land your dream job.”

---

Happy coding, and may the distinct count be ever in your favor!