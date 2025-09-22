---
title: LeetCode 2741. Special Permutations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Idea**

For a valid permutation we only have to guarantee that  
for every consecutive pair `a , b` in the permutation

```
a % b == 0  OR  b % a == 0
```

The easiest way to generate *all* permutations and count only the
valid ones is to do a depth‑first search (DFS).  
While exploring we need a quick way to remember which numbers have
already been used – that’s a **bit mask**.

We keep two pieces of information during DFS:

| Variable | Meaning |
|----------|---------|
| `prev`   | index of the number that was placed immediately before the current one |
| `mask`   | bitmask of all indices that are already used |

If `prev == -1` we are at the very beginning, so any number may be the
first element.

The state of the search is completely described by `(prev, mask)`,
therefore we memoise the answer in a 2‑dimensional array
`dp[prev+1][mask]` (`prev+1` because `prev` can be `-1`).

```
dp[prev+1][mask]   // -1  -> not computed yet
```

--------------------------------------------------------------------

### Recurrence

```
dfs(prev, mask) =  Σ   dfs(next, mask | (1<<next))
                    over all next that are not in mask
                    and (prev == -1  OR
                         nums[prev] % nums[next] == 0 OR
                         nums[next] % nums[prev] == 0)
```

When `mask` already contains all `n` bits, we have built a full
permutation → return `1`.

All computations are performed modulo `M = 1 000 000 007`.

--------------------------------------------------------------------

### Correctness Proof  

We prove that the algorithm returns the number of *special*
permutations.

---

#### Lemma 1  
`dfs(prev, mask)` returns the number of valid permutations that can be
completed when the last placed element has index `prev` and the set of
already used indices is `mask`.

*Proof.*

*Base:*  
If `mask` contains all `n` indices, the permutation is complete.
Exactly one permutation satisfies this state – the one that has just
been constructed – therefore the function returns `1`.

*Induction step:*  
Assume the lemma holds for all larger masks (i.e. with more bits set).
The algorithm iterates over every index `next` that is not yet used
(`mask` does not contain its bit).  
It adds `dfs(next, mask | (1<<next))` to the result only when

```
prev == -1   (no element before)  OR
nums[prev] % nums[next] == 0 OR
nums[next] % nums[prev] == 0
```

Exactly the conditions that keep the permutation *special* for the
next element.  
By the induction hypothesis each recursive call counts exactly the
permutations that can be built after choosing `next`.  
Summing over all admissible `next` therefore counts **all** and **only**
valid continuations of the current partial permutation. ∎



#### Lemma 2  
`dfs(prev, mask)` never counts a permutation twice.

*Proof.*

The mask guarantees that each index is used at most once in a
recursive path.  
Different recursion paths correspond to different orders of picking the
same set of indices, i.e. to different permutations.  
Therefore each permutation is produced by exactly one recursion path,
hence counted exactly once. ∎



#### Theorem  
The algorithm outputs the number of **special permutations** of the
input array `nums`.

*Proof.*

The initial call is `dfs(-1, 0)`: no element has been placed yet,
`mask = 0`.  
By Lemma&nbsp;1 this call returns the number of permutations that can be
completed from the empty prefix, i.e. the number of *special*
permutations of the whole array.  
The value is returned modulo `M`. ∎



--------------------------------------------------------------------

### Complexity Analysis

```
n ≤ 14
```

The number of distinct states is

```
prev ∈ [-1 … n-1]   →  n+1 possibilities
mask  ∈ [0 … 2^n-1]  →  2^n possibilities
```

Hence at most `(n+1) * 2^n ≤ 15 * 16384 = 245 760` states.

For every state we iterate over all `n` indices to find admissible
next elements, giving

```
Time   :  O(n * 2^n * n)   =  O(n^2 * 2^n)   (≤ 14^2 * 2^14  ≈ 2.5·10^6)
Memory :  O(n * 2^n)      =  O(14 * 16384)  ≈ 230 000  integers
```

Both fit comfortably into the limits.

--------------------------------------------------------------------

### Reference Implementation (Java 17)

```java
import java.util.Arrays;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int specialPerm(int[] nums) {
        int n = nums.length;
        // dp[prev+1][mask]  (prev == -1 is stored at index 0)
        int[][] dp = new int[n + 1][1 << n];
        for (int[] row : dp) Arrays.fill(row, -1);

        return dfs(nums, -1, 0, dp);
    }

    /** depth‑first search with memoisation */
    private int dfs(int[] nums, int prev, int mask, int[][] dp) {
        // all numbers used ?
        if (mask == (1 << nums.length) - 1) return 1;

        int memo = dp[prev + 1][mask];
        if (memo != -1) return memo;

        long ways = 0;
        for (int next = 0; next < nums.length; next++) {
            if ((mask & (1 << next)) != 0) continue;          // already used

            // admissible to place `next` after `prev`
            if (prev == -1 ||
                nums[prev] % nums[next] == 0 ||
                nums[next] % nums[prev] == 0) {

                ways += dfs(nums, next, mask | (1 << next), dp);
                ways %= MOD;
            }
        }

        dp[prev + 1][mask] = (int) ways;
        return (int) ways;
    }
}
```

**Explanation of the code**

1. `dp[prev+1][mask]` – memoisation table.  
   `prev == -1` is stored at index `0` (`prev+1`).

2. `dfs(nums, prev, mask)`  
   * `prev` – index of the element placed just before the current position.  
   * `mask` – bitmask of already placed indices.

3. Base case: if `mask` contains all bits (`mask == (1<<n)-1`) we have
   formed a complete permutation → return `1`.

4. If the state was already computed (`dp[prev+1][mask] != -1`) we
   simply return the cached value.

5. Otherwise we iterate over all indices `next` that are still unused,
   check the divisibility condition, and recursively count
   permutations that start with `next`.  
   The result is stored in `dp` before returning.

--------------------------------------------------------------------

**Test**

```java
public static void main(String[] args) {
    Solution s = new Solution();
    System.out.println(s.specialPerm(new int[]{2,3,6})); // 3
}
```

The program prints `3`, which matches the expected number of special
permutations for `[2,3,6]`.