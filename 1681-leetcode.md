---
title: LeetCode 1681. Minimum Incompatibility - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1681 – Minimum Incompatibility  
**Languages** : Java | Python | C++  
**Tags** : Leetcode, Dynamic Programming, Bitmask DP, Job Interview, Algorithm Interview, Software Engineer

---

### 1.  Problem Recap  

> **Leetcode 1681 – Minimum Incompatibility**  
> You are given an integer array `nums` of length `n` and an integer `k`.  
> `n` is guaranteed to be a multiple of `k`.  
> Partition `nums` into `k` groups of equal size `n / k`.  
> The **incompatibility** of a group is the difference between its maximum and minimum element.  
> The goal is to minimize the sum of incompatibilities of the `k` groups.  
> Return `-1` if such a partition is impossible.

> **Constraints**  
> - `2 ≤ n ≤ 16`  
> - `1 ≤ k ≤ n`  
> - `1 ≤ nums[i] ≤ 16`  
> - `n` is divisible by `k`

Because `n` is tiny (≤ 16) we can safely enumerate subsets of the array using bit masks.  
The classic solution is a **bit‑mask dynamic programming** that pre‑computes all *valid* groups and then builds the answer by combining them.

---

### 2.  High‑Level Idea

1. **Pre‑compute every valid subset of size `bucket = n / k`.**  
   *A subset is valid if it contains no duplicate numbers.*  
   For each valid subset we also store its *incompatibility* (`max - min`).

2. **Dynamic programming over bit masks**  
   `dp[mask]` = minimal total incompatibility to partition the elements whose indices are set in `mask`.  
   We only need to consider masks whose number of bits is a multiple of `bucket`.  
   For a mask `b`, we iterate over all *valid* sub‑masks `s` of size `bucket` and update

   ```
   dp[b] = min(dp[b], dp[b ^ s] + incompatibility[s])
   ```

3. **Result**  
   The answer is `dp[(1 << n) - 1]`.  
   If it is still `INF`, the partition is impossible → return `-1`.

The algorithm runs in `O( (n choose bucket) * 2^n )` time, which is far below the limit for `n ≤ 16`.  
Memory usage is `O(2^n)`.

---

### 3.  Implementation

Below are clean, fully‑commented solutions in **Java**, **Python**, and **C++**.  
All use the same algorithmic core but are adapted to the idioms of each language.

---

#### 3.1 Java Solution

```java
// Java 17
import java.util.*;

class Solution {
    public int minimumIncompatibility(int[] nums, int k) {
        int n = nums.length;
        int bucketSize = n / k;                     // size of each group

        /* Pre‑compute all valid subsets of size bucketSize.
         * store the incompatibility in an array where
         *   -1 indicates that the mask is NOT a valid group
         */
        int maxMask = 1 << n;
        int[] goodMaskVal = new int[maxMask];
        Arrays.fill(goodMaskVal, -1);

        for (int mask = 0; mask < maxMask; mask++) {
            if (Integer.bitCount(mask) != bucketSize) continue;

            int seen = 0;          // bitset of values (1..16) already used
            int mn = 17, mx = 0;   // because nums[i] <= 16
            boolean ok = true;

            for (int i = 0; i < n; i++) {
                if ((mask & (1 << i)) == 0) continue;
                int val = nums[i];
                if ((seen & (1 << val)) != 0) {   // duplicate inside group
                    ok = false;
                    break;
                }
                seen |= (1 << val);
                mn = Math.min(mn, val);
                mx = Math.max(mx, val);
            }

            if (ok) goodMaskVal[mask] = mx - mn;
        }

        /* DP over masks */
        int INF = Integer.MAX_VALUE / 2;   // avoid overflow
        int[] dp = new int[maxMask];
        Arrays.fill(dp, INF);
        dp[0] = 0;

        for (int mask = 0; mask < maxMask; mask++) {
            if (dp[mask] == INF) continue;
            // iterate over sub‑masks of size bucketSize
            for (int sub = mask; sub > 0; sub = (sub - 1) & mask) {
                if (goodMaskVal[sub] == -1) continue;   // not a valid group
                int newMask = mask ^ sub;
                dp[mask] = Math.min(dp[mask], dp[newMask] + goodMaskVal[sub]);
            }
        }

        int answer = dp[maxMask - 1];
        return answer == INF ? -1 : answer;
    }
}
```

---

#### 3.2 Python Solution

```python
# Python 3.10+
from typing import List

class Solution:
    def minimumIncompatibility(self, nums: List[int], k: int) -> int:
        n = len(nums)
        bucket = n // k

        max_mask = 1 << n
        # invalid group: -1, else incompatibility value
        good_val = [-1] * max_mask

        # Pre‑compute all valid groups of size `bucket`
        for mask in range(max_mask):
            if bin(mask).count("1") != bucket:
                continue

            seen = 0
            mn, mx = 17, -1   # nums[i] <= 16
            ok = True
            for i in range(n):
                if not (mask >> i) & 1:
                    continue
                val = nums[i]
                if (seen >> val) & 1:
                    ok = False
                    break
                seen |= 1 << val
                mn = min(mn, val)
                mx = max(mx, val)

            if ok:
                good_val[mask] = mx - mn

        INF = 10 ** 9
        dp = [INF] * max_mask
        dp[0] = 0

        for mask in range(max_mask):
            if dp[mask] == INF:
                continue
            # iterate over all sub‑masks of `mask`
            sub = mask
            while sub:
                if good_val[sub] != -1 and dp[mask ^ sub] != INF:
                    dp[mask] = min(dp[mask], dp[mask ^ sub] + good_val[sub])
                sub = (sub - 1) & mask

        return -1 if dp[max_mask - 1] == INF else dp[max_mask - 1]
```

---

#### 3.3 C++ Solution

```cpp
// C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimumIncompatibility(vector<int>& nums, int k) {
        int n = nums.size();
        int bucket = n / k;
        int maxMask = 1 << n;
        const int INF = 1e9;

        /* Pre‑compute valid groups of size `bucket`.
         * `incomp[mask]` = incompatibility if mask is valid, otherwise -1
         */
        vector<int> incomp(maxMask, -1);
        for (int mask = 0; mask < maxMask; ++mask) {
            if (__builtin_popcount(mask) != bucket) continue;

            int seen = 0;
            int mn = 17, mx = -1;
            bool ok = true;
            for (int i = 0; i < n; ++i) {
                if (!(mask >> i) & 1) continue;
                int val = nums[i];
                if (seen & (1 << val)) {  // duplicate inside the group
                    ok = false;
                    break;
                }
                seen |= 1 << val;
                mn = min(mn, val);
                mx = max(mx, val);
            }
            if (ok) incomp[mask] = mx - mn;
        }

        /* DP over masks */
        vector<int> dp(maxMask, INF);
        dp[0] = 0;
        for (int mask = 0; mask < maxMask; ++mask) {
            if (dp[mask] == INF) continue;
            // iterate over sub‑masks of size `bucket`
            for (int sub = mask; sub > 0; sub = (sub - 1) & mask) {
                if (incomp[sub] == -1) continue;          // not a valid group
                int newMask = mask ^ sub;
                dp[mask] = min(dp[mask], dp[newMask] + incomp[sub]);
            }
        }

        int ans = dp[maxMask - 1];
        return ans == INF ? -1 : ans;
    }
};
```

---

### 4.  “Good” vs. “Ugly” Implementations

| Category | What you should **do** | What you should **avoid** |
|----------|------------------------|---------------------------|
| **Good** | *Pre‑compute* all valid groups → `O(2^n)` time instead of exploring every partition recursively. |  |
| **Good** | Use *sub‑mask enumeration* (`sub = (sub-1) & mask`) → linear in the number of sub‑masks. |  |
| **Good** | Store the incompatibility in an array, not a `map`, to avoid overhead. |  |
| **Ugly** | A naïve back‑tracking that checks all `k!` permutations. |  |
| **Ugly** | Forget that duplicates inside a group make the group invalid. |  |
| **Ugly** | Use an exponential DP (`dp[mask] = min(dp[mask], dp[mask^sub] + …)`), but forget to limit to masks whose bit‑count is a multiple of `bucket`. |  |
| **Ugly** | Keep `INF` as `INT_MAX`; adding two such values overflows. |  |

---

### 5.  Why This Matters for a Job Interview

* **Time‑complexity**:  
  Your recruiter will want to see that you can reason about the worst‑case and choose an algorithm that fits the constraints.  
  A solution that naively enumerates all `k`‑partitions is \(O(k! \cdot n^k)\) – obviously **impossible** for `n = 16`.

* **Space‑efficiency**:  
  Using a 32‑bit mask (`1 << n`) lets you store the state compactly and perform bitwise operations in constant time.

* **Language‑specific tricks**:  
  - In **Java** use `Integer.bitCount` and `Arrays.fill`.  
  - In **Python** use `bin(mask).count('1')` or `__builtin_popcount` (via `int.bit_count` in 3.8+).  
  - In **C++** use `__builtin_popcount` and bit manipulation macros.

* **Explain the logic clearly**:  
  In an interview, walk through the pre‑computation, the DP transition, and the final answer.  
  Mention why duplicates matter and why the incompatibility is simply `max - min`.

* **Demonstrate confidence**:  
  Talk about the `INF` guard against overflow, and about early pruning (`if dp[mask] == INF continue`).  
  These small details often make the difference between a clean answer and a TLE.

---

### 6.  Final Take‑away

- **Pre‑compute** valid groups → removes a huge amount of work.  
- **Bit‑mask DP** + **sub‑mask enumeration** → guarantees `O(2^n)` performance.  
- **Avoid** naive recursion or repeated sorting inside loops.  

With the above templates you’ll be ready to hit the interview “Minimum Incompatibility” question head‑on – in Java, Python, or C++ – and show that you can turn a brute‑force nightmare into a neat, optimal solution. Happy coding!