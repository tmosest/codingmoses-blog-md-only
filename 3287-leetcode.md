---
title: LeetCode 3287. Find the Maximum Sequence Value of Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3287 – Find the Maximum Sequence Value of Array  
**Hard – LeetCode**  

| Language | Complexity | Code |
|----------|------------|------|
| **Java** | O(n · k · 32)  ≈ 2.6 M ops | <details><summary>Java 17</summary>... </details> |
| **Python** | O(n · k · 32) | <details><summary>Python 3.11</summary>... </details> |
| **C++** | O(n · k · 32) | <details><summary>C++ 23</summary>... </details> |

> **Why this matters for your interview portfolio**  
> The problem forces you to combine **subsequence selection** with **bitwise OR/XOR** – two staples in coding interviews.  
> Mastering this DP‑bitmask pattern shows you can reason about constraints, state space, and efficient enumeration – a must‑have skill for senior backend roles.

---

## TL;DR

* The goal: pick a subsequence of length `2k` (k elements, then k elements) to maximize  
  `(OR of first k) XOR (OR of last k)`.
* Because every `nums[i]` is `< 27`, the OR of any group fits in 5 bits → **32 possible OR masks**.
* Use two DP tables:
  * **prefix DP** – earliest index where a given OR can be achieved with exactly `t` elements.
  * **suffix DP** – latest index where a given OR can be achieved with exactly `t` elements.
* After filling both tables, iterate over all mask pairs, check that the prefix index < suffix index, and take the maximum XOR.

---

## Full Solution (Java)

```java
import java.util.*;

public class Solution {
    public int maxValue(int[] nums, int k) {
        int n = nums.length;
        final int MAX_MASK = 1 << 5;   // 0..31 (since nums[i] < 27)
        final int INF = n + 1;         // impossible index marker

        // ---- Prefix DP: earliest index to get OR==mask with t elements ----
        int[][] pref = new int[k + 1][MAX_MASK];
        for (int t = 0; t <= k; t++) Arrays.fill(pref[t], INF);
        pref[0][0] = -1;                       // 0 elements before the array

        for (int i = 0; i < n; i++) {
            int val = nums[i];
            // iterate t in reverse to avoid re‑using the same element
            for (int t = Math.min(k, i + 1); t >= 1; t--) {
                for (int mask = 0; mask < MAX_MASK; mask++) {
                    if (pref[t-1][mask] <= i - 1) {     // we can take this element
                        int newMask = mask | val;
                        pref[t][newMask] = Math.min(pref[t][newMask], i);
                    }
                }
            }
        }

        // record the earliest index for each OR mask
        int[] first = new int[MAX_MASK];
        Arrays.fill(first, INF);
        for (int mask = 0; mask < MAX_MASK; mask++) first[mask] = pref[k][mask];

        // ---- Suffix DP: latest index to get OR==mask with t elements ----
        int[][] suff = new int[k + 1][MAX_MASK];
        for (int t = 0; t <= k; t++) Arrays.fill(suff[t], -INF);
        suff[0][0] = n;                        // 0 elements after the array

        for (int i = n - 1; i >= 0; i--) {
            int val = nums[i];
            for (int t = Math.min(k, n - i); t >= 1; t--) {
                for (int mask = 0; mask < MAX_MASK; mask++) {
                    if (suff[t-1][mask] >= i + 1) {
                        int newMask = mask | val;
                        suff[t][newMask] = Math.max(suff[t][newMask], i);
                    }
                }
            }
        }

        // record the latest index for each OR mask
        int[] last = new int[MAX_MASK];
        Arrays.fill(last, -INF);
        for (int mask = 0; mask < MAX_MASK; mask++) last[mask] = suff[k][mask];

        // ---- Scan all mask pairs to get the best XOR ----
        int answer = 0;
        for (int m1 = 0; m1 < MAX_MASK; m1++) {
            if (first[m1] == INF) continue;          // cannot get this OR in prefix
            for (int m2 = 0; m2 < MAX_MASK; m2++) {
                if (last[m2] == -INF) continue;      // cannot get this OR in suffix
                if (first[m1] < last[m2])            // indices do not overlap
                    answer = Math.max(answer, m1 ^ m2);
            }
        }
        return answer;
    }
}
```

### Why it Works

* **State** – `pref[t][mask]` stores *the smallest index* where we can pick `t` elements up to that point whose OR equals `mask`.  
  If it is still `INF`, that OR is unreachable with exactly `t` elements.  
* Transition – include the current element `nums[i]`: `newMask = oldMask | nums[i]`.  
  We keep the earliest index because we will later compare it with the *latest* suffix index.
* The suffix DP is symmetric – we store the *latest* index, therefore we iterate backwards.

The final answer is the maximum over all mask pairs that respect the order constraint (`pref[m1] < last[m2]`).

---

## Full Solution (Python)

```python
from typing import List

class Solution:
    def maxValue(self, nums: List[int], k: int) -> int:
        n = len(nums)
        MAX_MASK = 1 << 5          # 0..31
        INF = n + 1                # impossible marker

        # ---- prefix dp : earliest index ----
        pref = [[INF] * MAX_MASK for _ in range(k + 1)]
        pref[0][0] = -1  # before first element

        for i, val in enumerate(nums):
            for t in range(min(k, i + 1), 0, -1):
                for mask in range(MAX_MASK):
                    if pref[t - 1][mask] <= i - 1:          # can use this element
                        new_mask = mask | val
                        pref[t][new_mask] = min(pref[t][new_mask], i)

        first = [pref[k][m] for m in range(MAX_MASK)]

        # ---- suffix dp : latest index ----
        suff = [[-INF] * MAX_MASK for _ in range(k + 1)]
        suff[0][0] = n   # after the last element

        for i in range(n - 1, -1, -1):
            val = nums[i]
            for t in range(min(k, n - i), 0, -1):
                for mask in range(MAX_MASK):
                    if suff[t - 1][mask] >= i + 1:
                        new_mask = mask | val
                        suff[t][new_mask] = max(suff[t][new_mask], i)

        last = [suff[k][m] for m in range(MAX_MASK)]

        # ---- Scan all mask pairs ----
        ans = 0
        for m1 in range(MAX_MASK):
            if first[m1] == INF: continue
            for m2 in range(MAX_MASK):
                if last[m2] == -INF: continue
                if first[m1] < last[m2]:
                    ans = max(ans, m1 ^ m2)
        return ans
```

---

## Full Solution (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxValue(vector<int> nums, int k) {
        const int n = nums.size();
        const int MAX_MASK = 1 << 5;          // 32 masks because nums[i] < 27
        const int INF = n + 1;                // impossible index

        /* prefix DP : earliest index for OR==mask with t elements */
        vector<array<int, 32>> pref(k + 1);
        for (int t = 0; t <= k; ++t) pref[t].fill(INF);
        pref[0][0] = -1;                      // 0 elements before the array

        for (int i = 0; i < n; ++i) {
            int val = nums[i];
            for (int t = min(k, i + 1); t >= 1; --t) {
                for (int mask = 0; mask < MAX_MASK; ++mask) {
                    if (pref[t-1][mask] <= i-1) {          // can pick this element
                        int newMask = mask | val;
                        pref[t][newMask] = min(pref[t][newMask], i);
                    }
                }
            }
        }
        vector<int> first(MAX_MASK, INF);
        for (int mask = 0; mask < MAX_MASK; ++mask) first[mask] = pref[k][mask];

        /* suffix DP : latest index for OR==mask with t elements */
        vector<array<int, 32>> suff(k + 1);
        for (int t = 0; t <= k; ++t) suff[t].fill(-INF);
        suff[0][0] = n;                       // 0 elements after the array

        for (int i = n - 1; i >= 0; --i) {
            int val = nums[i];
            for (int t = min(k, n - i); t >= 1; --t) {
                for (int mask = 0; mask < MAX_MASK; ++mask) {
                    if (suff[t-1][mask] >= i + 1) {
                        int newMask = mask | val;
                        suff[t][newMask] = max(suff[t][newMask], i);
                    }
                }
            }
        }
        vector<int> last(MAX_MASK, -INF);
        for (int mask = 0; mask < MAX_MASK; ++mask) last[mask] = suff[k][mask];

        /* Scan all mask pairs to find the best XOR */
        int ans = 0;
        for (int m1 = 0; m1 < MAX_MASK; ++m1) {
            if (first[m1] == INF) continue;
            for (int m2 = 0; m2 < MAX_MASK; ++m2) {
                if (last[m2] == -INF) continue;
                if (first[m1] < last[m2]) ans = max(ans, m1 ^ m2);
            }
        }
        return ans;
    }
};
```

---

## Good, Bad, & Ugly of the DP‑Bitmask Pattern

| Aspect | What we learned | How it shows skill |
|--------|-----------------|-------------------|
| **Good** | • **Tiny state space** (32 masks) – shows you can reduce the problem via constraints.<br>• **Linear DP** over positions – easy to implement, debug, and test. | Demonstrates *clever optimization* – turning an exponential idea into a linear‑time routine. |
| **Bad** | • The statement talks about *subsequences* (not subarrays) – many naïve coders will try a brute‑force combination of indices (O(n²·k²)).<br>• The OR mask size is *not obvious*; you need to notice the “< 27” trick. | Reveals *attention to detail*: catching a hidden constraint and turning it into a huge speed‑up. |
| **Ugly** | • Managing 2‑dimensional DP arrays (`t × mask`) can be messy in languages that don’t support large multidimensional arrays out‑of‑the‑box.<br>• Some solutions keep the full DP table even though we only need the `k`‑th row. | Shows *code maintainability*: how you refactor a dense DP into two one‑dimensional arrays (`first` & `last`) for clarity. |

---

## Takeaway for the Interview

* **Show your constraint‑driven mindset** – identify that `nums[i] < 27` means only 32 possible OR values.  
* **Explain the DP** – what each dimension means, why we keep *earliest* vs. *latest* indices, and why the final scan yields the optimal answer.  
* **Talk about edge cases** – zero‑length prefixes/suffixes, negative infinity handling, and how the “impossible marker” is chosen.

This problem is a perfect example to illustrate that an interviewee can translate a combinatorial description into a clean, efficient algorithm that runs in *linear time*. Mastery of the DP‑Bitmask pattern is a strong signal of *algorithmic maturity* and *problem‑solving intuition* – traits highly prized by hiring managers looking for top‑tier software engineers.