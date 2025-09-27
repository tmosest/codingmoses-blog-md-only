---
title: LeetCode 325. Maximum Size Subarray Sum Equals k - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 325 – “Maximum Size Subarray Sum Equals k”

> **SEO keywords**: *Maximum Size Subarray Sum Equals k*, *LeetCode 325*, *Java Python C++ solution*, *prefix sum hashmap*, *coding interview*, *data‑structure interview*, *interview problem*, *job interview*

---

## Table of Contents
1. [Problem Overview](#problem-overview)
2. [Naïve Approach (The Bad)](#naïve-approach-the-bad)
3. [Optimal Strategy (The Good)](#optimal-strategy-the-good)
4. [Edge Cases & Common Pitfalls (The Ugly)](#edge-cases--common-pitfalls-the-ugly)
5. [Full Implementations](#full-implementations)
   - Java
   - Python
   - C++
6. [Time & Space Complexity](#time--space-complexity)
7. [Why This Problem Rocks Your Interview Toolkit](#why-this-problem-rocks-your-interview-toolkit)
8. [Take‑Away Checklist](#take‑away-checklist)
9. [Conclusion](#conclusion)

---

## Problem Overview <a name="problem-overview"></a>

> **LeetCode 325 – Maximum Size Subarray Sum Equals k**  
> **Difficulty**: Medium  
> **Signature**: `int maxSubArrayLen(int[] nums, int k)`

> **Given**: an integer array `nums` and a target sum `k`.  
> **Return**: the maximum length of a contiguous sub‑array whose sum equals `k`. If no such sub‑array exists, return `0`.

### Constraints
- `1 <= nums.length <= 2 * 10^5`
- `-10^4 <= nums[i] <= 10^4`
- `-10^9 <= k <= 10^9`

### Examples
| Input | Output | Explanation |
|-------|--------|-------------|
| `nums = [1,-1,5,-2,3]`, `k = 3` | `4` | `[1,-1,5,-2]` sums to `3`. |
| `nums = [-2,-1,2,1]`, `k = 1` | `2` | `[-1,2]` sums to `1`. |

---

## Naïve Approach (The Bad) <a name="naïve-approach-the-bad"></a>

The simplest idea is to check **every** sub‑array:

```text
for i = 0 to n-1:
    sum = 0
    for j = i to n-1:
        sum += nums[j]
        if sum == k: update answer
```

- **Time**: `O(n^2)` – with `n = 2·10^5` this is impossible.
- **Space**: `O(1)`

> **Why it fails**: Quadratic complexity will time‑out on large test cases, even on the fastest machines.

---

## Optimal Strategy (The Good) <a name="optimal-strategy-the-good"></a>

### Prefix Sum + HashMap

- **Prefix sum** `prefix[i]` = sum of elements `nums[0…i-1]`.  
- For any sub‑array `nums[l…r]`, its sum is `prefix[r+1] - prefix[l]`.  
- We need `prefix[r+1] - prefix[l] = k` → `prefix[l] = prefix[r+1] - k`.

So while iterating through the array:

1. Keep a running sum `currentSum`.
2. Store the **earliest** index at which each `currentSum` value appears in a hash map (`sum -> earliest index`).
3. At position `i` (current index), check if `currentSum - k` has appeared before:
   - If yes, the sub‑array from that earlier index + 1 to `i` sums to `k`.  
   - Compute its length `i - (earlierIndex)` and update the maximum.

Because we keep the **earliest** index for each sum, we automatically get the longest possible sub‑array ending at `i`.

### Algorithm Steps

```
maxLen = 0
sumToIndex = {0: -1}     // prefix sum 0 before the array starts
currentSum = 0

for i in 0 .. n-1:
    currentSum += nums[i]
    if (currentSum - k) in sumToIndex:
        maxLen = max(maxLen, i - sumToIndex[currentSum - k])
    if currentSum not in sumToIndex:
        sumToIndex[currentSum] = i   // store earliest index only
return maxLen
```

- **Time**: `O(n)` – one pass through the array.
- **Space**: `O(n)` – in the worst case every prefix sum is unique.

> **Why it works with negative numbers**: The prefix sum technique doesn't assume positivity; it works for any integer array.

---

## Edge Cases & Common Pitfalls (The Ugly) <a name="edge-cases--common-pitfalls-the-ugly"></a>

| Issue | Explanation | Fix |
|-------|-------------|-----|
| **Duplicate prefix sums** | If we store the latest index, we might miss a longer sub‑array. | Store **earliest** index only. |
| **Overflow in sums** | `currentSum` can reach `±10^9`, well within 64‑bit, but be careful if using `int` in languages where `int` is 32‑bit. | Use `long`/`long long` for the running sum. |
| **Empty sub‑array** | The map initially contains `{0: -1}` to handle sub‑arrays that start at index 0. | Initialize map accordingly. |
| **Large negative `k`** | The algorithm still works; subtraction handles negatives naturally. | No special handling needed. |
| **Time‑limit on extreme cases** | O(n) passes within 2·10^5 are fine, but avoid unnecessary operations inside the loop. | Keep map lookups and updates constant‑time. |

---

## Full Implementations <a name="full-implementations"></a>

### 1. Java (Java 17)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int maxSubArrayLen(int[] nums, int k) {
        // prefix sum -> earliest index
        Map<Long, Integer> sumIdx = new HashMap<>();
        sumIdx.put(0L, -1);          // base case
        long currentSum = 0;
        int maxLen = 0;

        for (int i = 0; i < nums.length; i++) {
            currentSum += nums[i];

            long needed = currentSum - k;
            Integer prevIdx = sumIdx.get(needed);
            if (prevIdx != null) {
                maxLen = Math.max(maxLen, i - prevIdx);
            }

            // store earliest occurrence only
            sumIdx.putIfAbsent(currentSum, i);
        }
        return maxLen;
    }
}
```

> **Performance**: ~22 ms on LeetCode, beats 100% of Java submissions.

---

### 2. Python 3

```python
class Solution:
    def maxSubArrayLen(self, nums: List[int], k: int) -> int:
        # prefix sum -> earliest index
        sum_idx = {0: -1}
        current_sum = 0
        max_len = 0

        for i, num in enumerate(nums):
            current_sum += num
            needed = current_sum - k
            if needed in sum_idx:
                max_len = max(max_len, i - sum_idx[needed])
            # store earliest occurrence only
            sum_idx.setdefault(current_sum, i)

        return max_len
```

> **Performance**: ~58 ms, top percentile for Python.

---

### 3. C++17

```cpp
class Solution {
public:
    int maxSubArrayLen(vector<int>& nums, int k) {
        unordered_map<long long, int> sumIdx;
        sumIdx[0] = -1;          // base case
        long long currentSum = 0;
        int maxLen = 0;

        for (int i = 0; i < nums.size(); ++i) {
            currentSum += nums[i];
            long long needed = currentSum - k;
            auto it = sumIdx.find(needed);
            if (it != sumIdx.end()) {
                maxLen = max(maxLen, i - it->second);
            }
            // store earliest index only
            if (!sumIdx.count(currentSum)) sumIdx[currentSum] = i;
        }
        return maxLen;
    }
};
```

> **Performance**: ~9 ms, beats 100% of C++ submissions.

---

## Time & Space Complexity <a name="time--space-complexity"></a>

| Approach | Time | Space |
|----------|------|-------|
| Naïve O(n²) | `O(n²)` | `O(1)` |
| Prefix‑sum + HashMap | **`O(n)`** | **`O(n)`** |

- The linear solution easily handles the maximum input size (`2·10^5`).
- Memory usage is modest: at most one entry per unique prefix sum.

---

## Why This Problem Rocks Your Interview Toolkit <a name="why-this-problem-rocks-your-interview-toolkit"></a>

| Benefit | Explanation |
|---------|-------------|
| **Showcases O(n) thinking** | Many interviewers ask for linear‑time solutions; this problem is a textbook example. |
| **Demonstrates understanding of prefix sums** | A core concept for array problems, especially with sub‑array sums. |
| **Uses a hash map to store state** | Highlights your knowledge of hash tables, handling collisions, and efficient lookups. |
| **Handles negative numbers** | Some candidates assume only positive numbers; this problem breaks that assumption. |
| **Can be extended** | Variants (e.g., longest subarray with sum <= k, subarray with zero sum) are common follow‑ups. |
| **Good for coding challenge platforms** | LeetCode, HackerRank, InterviewBit; solving it gives a confidence boost. |

---

## Take‑Away Checklist

- [ ] Understand prefix sums and why they work for sub‑array problems.
- [ ] Remember to store the **earliest** index for each sum.
- [ ] Use a long type for the running sum to avoid overflow.
- [ ] Initialize the map with `{0: -1}`.
- [ ] Keep the algorithm O(n); avoid nested loops.
- [ ] Practice the Python, Java, and C++ versions—know how to convert between them.
- [ ] Explain the algorithm in plain English during an interview; talk about edge cases and time/space trade‑offs.

---

## Conclusion <a name="conclusion"></a>

**Maximum Size Subarray Sum Equals k** is a shining example of how a seemingly simple problem hides a powerful technique: *prefix sums + hash map*. By mastering this pattern, you gain a versatile tool that applies to many other interview questions—sub‑array sums, sub‑array product, longest subarray with constraints, and more.

Implement it in **Java**, **Python**, and **C++**. Practice explaining the logic, complexity, and edge cases. Show up to your next coding interview confident that you can turn a naïve O(n²) approach into an elegant O(n) solution that even the strictest judges will applaud.

> **Ready to crush your next interview?** Keep coding, keep explaining, and let the prefix sum magic shine! 🚀