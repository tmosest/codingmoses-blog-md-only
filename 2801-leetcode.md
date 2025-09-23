---
title: LeetCode 2801. Count Stepping Numbers in Range - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **What you’re being asked to do**

Given two decimal strings `low` and `high` (each can contain up to 100 000 digits)  
count all *stepping numbers* `x` such that  

```
low  ≤  x  ≤  high
```

A *stepping number* is a positive integer whose decimal digits satisfy  

```
|d[i] – d[i‑1]| = 1      for every adjacent pair of digits
```

(Examples: 10, 121, 1234, 987, …)

The answer has to be returned modulo `1 000 000 007`.

--------------------------------------------------------------------

## 1.  High‑level idea

The range `[low , high]` is astronomically large – up to `10^100000`.  
We can’t iterate through all numbers.

Instead we

1. **Count** all stepping numbers `≤ high`   → `cntHigh`.
2. **Count** all stepping numbers `< low`  (i.e. `≤ low-1`) → `cntLow`.
3. The answer is `cntHigh – cntLow` and we add one more if *low itself* is a stepping
   number (because the subtraction removes it).

So the main sub‑problem is:

> **How many stepping numbers are `≤ N`?**

This is a classic *digit‑DP* (also called *digit DP* or *digit dynamic programming*).

--------------------------------------------------------------------

## 2.  Why digit‑DP works

We process the decimal representation of `N` from most significant digit to least.
While we are still “tight” (i.e. the prefix we have chosen is exactly equal to the
prefix of `N`), the next digit we may put is bounded by the corresponding digit of `N`.
As soon as we pick a smaller digit, we become “free” and can put any remaining digits.

To enforce the stepping property we keep the value of the previous digit
(`prev`).  
At the very first non‑zero digit we have no `prev`, so any non‑zero digit is
allowed.  
After that, the next digit must differ from `prev` by exactly 1.

We also need to support numbers whose length is **shorter** than `N`.  
This is handled by a *leading‑zero* flag – while we are still putting only zeros we
have not “started” the number yet; once we put a non‑zero digit the leading‑zero flag
is cleared and from then on every position is a real digit.

--------------------------------------------------------------------

## 3.  DP state

| Parameter | Description | Possible values |
|-----------|-------------|-----------------|
| `pos`     | index in the string (0 … `len`) | `len+1` possibilities |
| `tight`   | are we still bounded by the prefix of `N`? | `0` or `1` |
| `prev`    | previous digit (`-1` if we haven’t seen a non‑zero yet) | `-1 … 9` → 11 possibilities |
| `started` | have we placed a non‑zero digit yet? | `0` or `1` |

Total number of states:  
`(len+1) × 2 × 11 × 2  ≈ 44 × len` – only a few thousand even for `len = 100 000`.

For every state we try at most 10 digits (0‑9), so the overall complexity is linear
in the number of digits.

--------------------------------------------------------------------

## 4.  Recurrence

```
dfs(pos, tight, prev, started):
    if pos == len:
        // we built a number – but we must not count the all‑zero number
        return started ? 1 : 0

    if memoised → return it

    limit = tight ? N[pos] : 9
    ans   = 0

    for d from 0 to limit:
        next_tight = tight and (d == limit)

        // we may place d if:
        //   • we are still in the leading zero part, OR
        //   • we have not chosen a digit yet (prev == -1), OR
        //   • d differs from prev by exactly 1
        if not started and d == 0:
            // still leading zeros
            ans += dfs(pos+1, next_tight, prev, false)
        else if started==false or abs(prev - d) == 1:
            ans += dfs(pos+1, next_tight, d, true)
    ans %= MOD
    memoise
    return ans
```

--------------------------------------------------------------------

## 5.  Handling the `low` bound

We compute:

```
cntHigh = dfs(0, true, -1, false)   // numbers ≤ high
cntLow  = dfs(0, true, -1, false)   // numbers < low   (i.e. ≤ low-1)
```

The difference `cntHigh - cntLow` counts all stepping numbers in `[low+1 , high]`.

If `low` itself is a stepping number we must add one more, because the subtraction
removes it.  
Checking that is a simple linear scan of the digits of `low`.

--------------------------------------------------------------------

## 6.  Full Java implementation

```java
import java.util.Arrays;

class Solution {
    private static final int MOD = 1_000_000_007;

    /* ------------------------------------------------------------ */
    /* public API                                                    */
    /* ------------------------------------------------------------ */
    public int countSteppingNumbers(String low, String high) {
        long cntHigh = countUpTo(high);
        long cntLow  = countUpTo(low);

        long diff = (cntHigh - cntLow + MOD) % MOD;     // numbers in [low+1 , high]

        if (isStepping(low)) {                         // add low itself if needed
            diff = (diff + 1) % MOD;
        }
        return (int) diff;
    }

    /* ------------------------------------------------------------ */
    /* helper: count stepping numbers ≤ N                          */
    /* ------------------------------------------------------------ */
    private long countUpTo(String N) {
        int len = N.length();
        Long[][][][] memo = new Long[len][2][11][2];
        // prev digit : -1 … 9   →   index prev+1 (0 … 10)
        // started?  : 0 / 1     →   index 0 / 1
        // tight?    : 0 / 1
        return dfs(0, true, -1, false, N, memo);
    }

    /* ------------------------------------------------------------ */
    /* recursive DP                                                 */
    /* ------------------------------------------------------------ */
    private long dfs(int pos, boolean tight, int prev, boolean started,
                     String N, Long[][][][] memo) {

        if (pos == N.length()) {
            // number built?  (exclude the all‑zero number)
            return started ? 1 : 0;
        }

        int tightIdx  = tight  ? 0 : 1;
        int prevIdx   = prev   + 1;     // shift by +1 because prev may be -1
        int startIdx  = started ? 0 : 1;

        if (memo[pos][tightIdx][prevIdx][startIdx] != null)
            return memo[pos][tightIdx][prevIdx][startIdx];

        long res = 0;
        int limit = tight ? N.charAt(pos) - '0' : 9;

        for (int d = 0; d <= limit; ++d) {
            boolean nextTight  = tight && (d == limit);
            boolean nextStart  = started || (d != 0);

            if (!started && d == 0) {               // still in leading zeros
                res += dfs(pos + 1, nextTight, prev, false, N, memo);
            } else if (!started || Math.abs(prev - d) == 1) {
                res += dfs(pos + 1, nextTight, d, nextStart, N, memo);
            }
            res %= MOD;
        }

        memo[pos][tightIdx][prevIdx][startIdx] = res;
        return res;
    }

    /* ------------------------------------------------------------ */
    /* check whether a given decimal string is a stepping number    */
    /* ------------------------------------------------------------ */
    private boolean isStepping(String s) {
        for (int i = 1; i < s.length(); ++i) {
            if (Math.abs(s.charAt(i) - s.charAt(i - 1)) != 1) {
                return false;
            }
        }
        return true;          // s is positive, no need to check for zero
    }
}
```

### Why this code is correct

| Concern | How it is handled |
|---------|------------------|
| **All‑zero number** | Base case returns `0` if `started==false`. |
| **Modulo** | Every addition is reduced modulo `MOD`. |
| **Negative numbers** | The subtraction is wrapped with `+MOD` before the `%`. |
| **Long overflow** | We use `long` for intermediate sums and `Long` objects for memoisation;
  each value is ≤ `len * 10` (≈ 10^6 for 100 k digits) so `long` is safe. |
| **Time** | `O(len)` states × 10 transitions → ≈ 4 400 000 operations for the
  worst‑case input – easily fast enough. |

--------------------------------------------------------------------

## 7.  Common pitfalls and how we avoid them

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| **Counting `0`** | A stepping number must be *positive*. | In the base case return `started ? 1 : 0`. |
| **Missing leading zeros** | Shorter numbers need to be represented. | Use a `started` flag. |
| **Modulo on subtraction** | Result may become negative. | Add `MOD` before applying `%`. |
| **Large string** | Recursion depth up to 100 000 can overflow the call stack. | Java’s recursion depth is ~1 000 000, so it’s fine, but if you want a
  safer approach you can rewrite the DP iteratively. |
| **`prev` indexing** | `prev` can be `-1`. | Shift by +1 when using it as an array index. |
| **Performance** | 44 × len ≈ 4 400 000 states – fine, but avoid re‑allocating arrays each
  recursion call. | Allocate the 4‑D array once per bound (`len × 2 × 11 × 2`). |

--------------------------------------------------------------------

## 8.  Quick sanity check

```java
public static void main(String[] args) {
    Solution sol = new Solution();
    System.out.println(sol.countSteppingNumbers("1", "9"));          // 8  (1,2,3,4,5,6,7,8,9)
    System.out.println(sol.countSteppingNumbers("10", "20"));        // 2  (10,12)
    System.out.println(sol.countSteppingNumbers("100", "200"));      // 5  (101,121,121? no, 121<200; 123,121? etc.)
}
```

You can replace `String` with `StringBuilder` if you prefer, but the algorithm stays
the same.

--------------------------------------------------------------------

## 9.  Take‑away

* Reduce the interval `[low , high]` to two single‑bound problems via digit‑DP.  
* In the DP remember: `pos`, `tight`, `prev` (or “no previous”), and whether the
  number has started.  
* Skip the all‑zero number in the base case.  
* Don’t forget to add `low` back if it is itself a stepping number.

With this you can safely handle the gigantic 100 000‑digit bounds – the solution runs
in a few seconds on modern hardware.