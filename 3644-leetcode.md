---
title: LeetCode 3644. Maximum K to Sort a Permutation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1. Problem Recap – LeetCode 3644: **Maximum K to Sort a Permutation**

You’re given a permutation `nums` of length `n` (`0 … n‑1`).  
You may swap any two positions `i` and `j` **only if**  

```
nums[i] & nums[j] == k
```

(`&` is the bit‑wise AND).  
All swaps in one test case must use the *same* value `k`.  
You can perform any number of such swaps.  
Return the **maximum** `k` that allows you to sort `nums` in non‑decreasing order.  
If `nums` is already sorted, return `0`.

**Constraints**

* `1 ≤ n ≤ 10⁵`
* `nums` is a permutation of `[0, …, n‑1]`

---

## 2. Why This Problem Looks Hard

The constraints force an `O(n)` solution, yet the swap condition looks like a graph‑theory problem:  
`i` can swap with `j` iff `nums[i] & nums[j] = k`.  
A naïve cycle‑detection approach would involve building the graph and then checking connectivity for every candidate `k`. That would be far too slow.

---

## 3. The Secret Sauce – A One‑Pass “Cumulative AND”

The key observation (used by Harshit Sharda’s editorial) is:

> **If you AND all numbers that are not already in the correct position, the resulting mask is always a valid `k`.**

Why?  
* In the final sorted array, every number `i` must end up at index `i`.  
* Any misplaced number must be swapped, directly or indirectly, with another number whose bitwise AND equals that common `k`.  
* The AND of all misplaced numbers guarantees that each of those numbers shares *every* bit set in the mask, so you can always pair any two of them and satisfy the condition.

Thus the algorithm is:

1. Start with `mask = all bits set` (`INT_MAX` in C++/Java, `~0` in Python).
2. For every index `i`:
   * If `nums[i] != i`, set `mask &= nums[i]`.
3. If no number was misplaced (`mask` never changed), return `0`.  
   Otherwise, return `mask`.

The mask is guaranteed to be the maximum possible `k` because any larger `k` would contain a bit that is not present in at least one misplaced number, breaking the AND condition.

---

## 4. Algorithm Summary

| Step | Action | Complexity |
|------|--------|------------|
| 1 | `mask ← all‑ones` | O(1) |
| 2 | For each `i` in `[0, n)` do: <br>‑ If `nums[i] ≠ i`, `mask ← mask & nums[i]` | O(n) |
| 3 | Return `mask == all‑ones ? 0 : mask` | O(1) |

Total time: **O(n)**  
Total extra space: **O(1)**

---

## 5. Code Implementations

Below are ready‑to‑copy implementations in **Java**, **Python**, and **C++**.

### 5.1 Java

```java
import java.util.*;

class Solution {
    public int sortPermutation(int[] nums) {
        int mask = Integer.MAX_VALUE;           // all bits set
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i) {
                mask &= nums[i];
            }
        }
        return (mask == Integer.MAX_VALUE) ? 0 : mask;
    }
}
```

### 5.2 Python

```python
class Solution:
    def sortPermutation(self, nums: List[int]) -> int:
        mask = ~0  # all bits set (for 32‑bit ints this is -1)
        for i, val in enumerate(nums):
            if val != i:
                mask &= val
        return 0 if mask == ~0 else mask
```

> **Python Note:**  
> `~0` evaluates to `-1` (all 1’s in two’s‑complement).  
> The mask stays in the range `[0, n-1]` because every `val` is in that range.

### 5.3 C++

```cpp
class Solution {
public:
    int sortPermutation(vector<int>& nums) {
        int mask = INT_MAX;          // all bits set
        for (int i = 0; i < (int)nums.size(); ++i) {
            if (nums[i] != i) {
                mask &= nums[i];
            }
        }
        return (mask == INT_MAX) ? 0 : mask;
    }
};
```

---

## 6. Edge‑Case Checklist

| Scenario | Expected Output | Why |
|----------|-----------------|-----|
| Already sorted (`nums[i] == i` ∀ i) | `0` | No swaps needed. |
| All numbers misplaced | `nums[0] & nums[1] & ... & nums[n-1]` | Every number contributes to the mask. |
| `n = 1` | `0` | Single element is trivially sorted. |
| Large `n` (10⁵) | Works in O(n) | Linear scan only. |

---

## 7. Alternative (Brute‑Force) Approaches and Why They Fail

| Approach | Complexity | Verdict |
|----------|------------|---------|
| Build swap graph for each candidate `k`, then BFS/DFS | `O(n²)` (worst‑case) | Too slow for `n = 10⁵`. |
| Try all `k` from 0 to `max(nums)` and simulate swaps | `O(n * max(nums))` | `max(nums) ≈ n`, so `O(n²)` again. |
| Use Union‑Find on indices with same bits | Still requires exploring all pairs | Still `O(n²)` in worst case. |

The one‑pass cumulative AND sidesteps any graph construction and delivers optimal performance.

---

## 8. Why This Solution Is a “Good” Interview Answer

1. **Optimal Complexity** – Linear time, constant space.  
2. **Clear Intuition** – The AND of misplaced numbers is a *common divisor* of all allowed swaps.  
3. **Robustness** – Handles all edge cases automatically.  
4. **Minimal Code** – Easy to implement, read, and debug.  
5. **Extensibility** – Works for any `k` defined by a similar bitwise operation.

---

## 9. Common Mistakes (The “Bad” and the “Ugly”)

| Mistake | What went wrong | How to fix |
|---------|-----------------|------------|
| *Assuming any two misplaced numbers can swap directly* | You can only swap if `nums[i] & nums[j] == k`. |
| *Trying to find a k by checking each pair of indices* | Exponential blow‑up. |
| *Returning the AND of all elements instead of only misplaced ones* | Including correctly placed numbers can set bits that *must not* be in `k`. |
| *Using a 64‑bit mask for 32‑bit numbers* | Unnecessary and may produce negative numbers in Java/C++. |
| *Ignoring the “already sorted” case* | Returning `INT_MAX` or `-1` would be incorrect. |

---

## 10. Interview Tips

1. **State your approach first** – Talk about the “cumulative AND” idea before diving into code.  
2. **Show the intuition** – Explain why AND of misplaced numbers works.  
3. **Edge‑case analysis** – Mention the sorted‑array case, `n = 1`, and large `n`.  
4. **Time & space complexity** – Always state them.  
5. **Write clean code** – Use meaningful variable names (`mask`, `isSorted`).  
6. **Optional extension** – Ask how the solution would change if swaps were allowed with *different* `k` values (the answer: you’d need a more complex graph analysis).  

---

## 11. SEO‑Optimized Blog Title & Meta Description

**Title:**  
“Maximum K to Sort a Permutation – The One‑Pass Cumulative AND Trick (LeetCode 3644)”

**Meta Description:**  
Learn the optimal solution for LeetCode 3644 “Maximum K to Sort a Permutation.” This article explains the one‑pass cumulative AND trick, provides Java, Python, and C++ code, and shares interview tips to ace your coding interview.

---

## 12. Conclusion

The “Maximum K to Sort a Permutation” problem looks intimidating because of the swap restriction, but a single linear pass over the array gives you the answer instantly.  
The cumulative AND of all misplaced numbers is not just a clever trick – it’s a mathematically sound proof that guarantees the maximum possible `k`.  

Use the code snippets above in your job interviews, share this blog post, and let the SEO‑friendly title help recruiters find you. Happy coding!