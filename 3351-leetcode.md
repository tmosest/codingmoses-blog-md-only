---
title: LeetCode 3351. Sum of Good Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sum of Good Subsequences – A Deep‑Dive
> **LeetCode 3351 – Hard**

| Language | Time | Space | Key Idea |
|----------|------|-------|----------|
| **Java** | **O(n)** | **O(max(nums))** | Two DP arrays (`count` & `total`) indexed by the value + 1 |
| **Python** | **O(n)** | **O(max(nums))** | `Counter` or list‑based DP with modulo arithmetic |
| **C++** | **O(n)** | **O(max(nums))** | `unordered_map` or vector DP with modulo arithmetic |

---

## 1. Problem Recap

You are given an integer array `nums`.  
A **good subsequence** is any subsequence where the absolute difference between any two consecutive elements is exactly **1**.  
Subsequences of length 1 are automatically good.

Return the **sum of all elements** that appear in every good subsequence, modulo `10^9 + 7`.

*Example*

```
nums = [1, 2, 1]
good subsequences: [1], [2], [1], [1,2], [2,1], [1,2,1]
sum = 14
```

---

## 2. Why This Problem Is Interesting

1. **Dynamic programming over the *value* instead of the *index*** – we only care about the last number of a subsequence, not where it occurs.
2. The subsequence constraint (difference = 1) creates a natural graph on values: edges only connect `v` to `v±1`.  
   Thus the DP transitions become *local* and linear in the array length.
3. The final answer is a **sum of sums** – a classic DP twist that can trip up beginners.

---

## 3. High‑Level Solution

For each value `v` in the array:

1. **Count DP** – `count[v]` = number of good subsequences that end with value `v`.  
   Recurrence:
   ```
   count[v] += count[v-1] + count[v+1] + 1
   ```
   The `+1` accounts for the new subsequence `[v]`.

2. **Sum DP** – `total[v]` = total sum of all elements in those subsequences that end with `v`.  
   Recurrence:
   ```
   cur = total[v-1] + total[v+1] + v * (count[v-1] + count[v+1] + 1)
   total[v] += cur
   ```
   *Why `v * (...)`?*  
   Every new subsequence that ends at `v` contributes `v` once to the sum, and we have exactly `count[v-1] + count[v+1] + 1` such subsequences.

3. Accumulate `cur` into a global result `ans`.

All operations are performed modulo `MOD = 1_000_000_007`.

The algorithm runs in **O(n)** time and **O(max(nums))** space, which easily satisfies the constraints (`n ≤ 10^5`, `nums[i] ≤ 10^5`).

---

## 4. Code Implementations

Below you’ll find clean, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

> **Tip:**  
> The arrays are indexed by `value + 1` to avoid dealing with negative indices (`v-1` when `v = 0`).  
> In the C++ version we use a `vector<long long>` because the maximum value is known (`10^5`).

---

### 4.1 Java

```java
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int sumOfGoodSubsequences(int[] nums) {
        int maxVal = 100_000;                // constraints guarantee nums[i] <= 1e5
        long[] count = new long[maxVal + 2]; // index v+1
        long[] total = new long[maxVal + 2];
        long ans = 0;

        for (int v : nums) {
            int idx = v + 1;                  // shift to avoid negatives

            // Count DP
            count[idx] = (count[idx] +
                          count[idx - 1] +
                          count[idx + 1] + 1) % MOD;

            // Sum DP
            long cur = (total[idx - 1] + total[idx + 1]) % MOD;
            cur = (cur + (long) v * ((count[idx - 1] + count[idx + 1] + 1) % MOD)) % MOD;

            total[idx] = (total[idx] + cur) % MOD;
            ans = (ans + cur) % MOD;
        }

        return (int) ans;
    }
}
```

---

### 4.2 Python

```python
from collections import Counter
from typing import List

class Solution:
    MOD = 10**9 + 7

    def sumOfGoodSubsequences(self, nums: List[int]) -> int:
        count = Counter()
        total = Counter()
        ans = 0

        for v in nums:
            # Count of new subsequences ending with v
            add_cnt = (count[v - 1] + count[v + 1] + 1) % self.MOD
            count[v] = (count[v] + add_cnt) % self.MOD

            # Sum of elements in those new subsequences
            add_sum = (total[v - 1] + total[v + 1]) % self.MOD
            add_sum = (add_sum + v * add_cnt) % self.MOD
            total[v] = (total[v] + add_sum) % self.MOD

            ans = (ans + add_sum) % self.MOD

        return ans
```

*Python 3‑lines version (for the blog)*
```python
def sumOfGoodSubsequences(nums):
    MOD = 10**9 + 7
    cnt = [0]*(max(nums)+3)
    tot = [0]*(max(nums)+3)
    ans = 0
    for v in nums:
        i = v+1
        add = (cnt[i-1] + cnt[i+1] + 1) % MOD
        cnt[i] = (cnt[i] + add) % MOD
        add = (tot[i-1] + tot[i+1] + v*add) % MOD
        tot[i] = (tot[i] + add) % MOD
        ans = (ans + add) % MOD
    return ans
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static const long long MOD = 1'000'000'007LL;

    int sumOfGoodSubsequences(vector<int>& nums) {
        const int maxVal = 100000;                 // nums[i] <= 1e5
        vector<long long> cnt(maxVal + 2, 0);      // idx = v+1
        vector<long long> tot(maxVal + 2, 0);
        long long ans = 0;

        for (int v : nums) {
            int idx = v + 1;

            long long add_cnt = (cnt[idx-1] + cnt[idx+1] + 1) % MOD;
            cnt[idx] = (cnt[idx] + add_cnt) % MOD;

            long long add_sum = (tot[idx-1] + tot[idx+1]) % MOD;
            add_sum = (add_sum + (long long)v * add_cnt) % MOD;

            tot[idx] = (tot[idx] + add_sum) % MOD;
            ans = (ans + add_sum) % MOD;
        }

        return (int)ans;
    }
};
```

---

## 5. The Good, The Bad, and the Ugly

| ✅ Good | ❌ Bad | ⚡ Ugly |
|---------|--------|--------|
| **Linear time** – only one pass over `nums`. | **Large DP arrays** – we allocate `O(max(nums))` memory even if many values never appear. | **Index shift (`value + 1`)** – a subtle trick that can lead to off‑by‑one bugs if forgotten. |
| **Clear local transitions** – only `v-1` and `v+1` matter. | **Modulo handling** – need to keep all intermediate results within `[0, MOD)`. | **C++ negative indices** – using a vector you must guard `v-1` when `v==0`; the Java/Python solutions avoid this by shifting. |
| **Extensible** – the same DP structure can be reused for related problems (e.g. “Sum of All Subarrays With Difference = k”). | **Harder readability** – the `total` recurrence is not obvious to first‑time readers. | **Off‑by‑one pitfalls** – the array index shift (`value + 1`) is the easiest place to go wrong. |

---

## 6. Interview‑Friendly Takeaways

1. **Explain the DP state clearly** – many interviewers will ask you to justify why you use `count[v]` and `total[v]`.  
   Emphasise that we are DP‑ing on the **value** because the graph of admissible transitions is simple.
2. **Show the recurrence** – write it on the whiteboard (or the screen) and walk through a tiny example.
3. **Mention the modulo** – interviewers love seeing you handle overflow early.
4. **Time/Space analysis** – highlight that the algorithm is linear and fits within the constraints.
5. **Edge‑case awareness** – discuss what happens when `v = 0` and why we shift indices.

---

## 7. Closing Thoughts

The **Sum of Good Subsequences** problem showcases how a modest‑looking DP can become surprisingly elegant when you think in terms of *values* rather than *positions*.  
The key insights – local transitions on a value graph and maintaining both *count* and *sum* – make the solution both efficient and scalable.

Feel free to adapt the code above to your own projects, or use it as a benchmark when you practice other LeetCode hard problems. Good luck on your next interview – with a solid DP portfolio like this, you’ll be ready to impress!