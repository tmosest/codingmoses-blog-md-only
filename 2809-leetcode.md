---
title: LeetCode 2809. Minimum Time to Make Array Sum At Most x - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem**

You are given two arrays of the same length  

```
nums1[0 … n-1]   – current values
nums2[0 … n-1]   – “growth” values
```

At every second `t` the whole array `nums1` grows by its
corresponding `nums2` values – i.e. at time `t`

```
value[i] = nums1[i] + t * nums2[i]
```

During any second you may **pick one index and set `nums1[i]` to `0`**.
Once it is zero the growth still happens, but the value is always
`0 + (t–chosen_time) * nums2[i]`.

Your goal is to find the smallest number of seconds `t`
(`0 ≤ t ≤ n`) such that after applying at most `t` zero‑operations the
sum of all values is ≤ `x`.  
If this is impossible return `-1`.

--------------------------------------------------------------------

#### 1.  Observations

* Zeroing an element with a **large** `nums2[i]` should be done **late**,
  because the earlier you zero it the more times its growth is added
  to the sum.  
  Therefore we first sort the pairs `(nums1[i], nums2[i])`
  by increasing `nums2`.

* For a fixed number of operations `t` we would like to know
  **how much we can reduce the sum** compared to the case where we do
  nothing.

* If we have already considered the first `i‑1` pairs and we still want
  to use `j` operations, we can:

  1. **Ignore** the `i`‑th pair – the reduction is `dp[i‑1][j]`.
  2. **Use** an operation on the `i`‑th pair – we then use
     `j‑1` operations on the first `i‑1` pairs and spend the last
     operation on this pair.  
     The reduction contributed by this pair is  

     ```
     nums1[i]               // we zeroed its initial value
     + nums2[i] * j         // we removed j future growths
     ```

* These two options give a classic 2‑dimensional DP.

--------------------------------------------------------------------

#### 2.  DP formulation

Let

```
dp[i][j]  – maximum possible reduction in the sum
           if we use at most j zero‑operations
           among the first i pairs (after sorting).
```

`dp[0][*] = 0` – with no pairs we can’t reduce anything.

Recurrence for `1 ≤ i ≤ n` and `1 ≤ j ≤ i` :

```
without_i = dp[i-1][j]                                 // we do not zero pair i
with_i    = dp[i-1][j-1] + nums1[i] + nums2[i] * j     // we zero pair i
dp[i][j]  = max( without_i , with_i )
```

Why `nums2[i] * j`?  
If we zero this pair at time `j`, its value at that moment would have
been increased `j` times by `nums2[i]`.  
Zeroing it removes all of those future increases **plus** its
original value `nums1[i]`.

After filling the table, `dp[n][t]` tells us the *maximum* reduction
we can achieve if we are allowed exactly `t` zero‑operations.

--------------------------------------------------------------------

#### 3.  Finding the answer

The sum of all elements after `t` seconds **without** any zero
operations is

```
S(t) = sum(nums1) + sum(nums2) * t
```

If we use the best reductions given by `dp[n][t]`, the real sum is

```
sum_after_t = S(t) – dp[n][t]
```

We need the smallest `t` such that `sum_after_t ≤ x`.
If no such `t` exists (i.e. even with `t = n` it’s still > `x`) we
return `-1`.

--------------------------------------------------------------------

#### 4.  Memory optimisation

The DP uses only the previous row, so we can keep a single
1‑dimensional array and update it from back to front:

```
int[] dp = new int[n+1];      // dp[j] = dp[i][j] after finishing pair i

for (int i = 1; i <= n; ++i) {
    for (int j = i; j >= 1; --j) {
        dp[j] = Math.max(dp[j], dp[j-1] + nums1[i] + nums2[i]*j);
    }
}
```

--------------------------------------------------------------------

#### 4.  Complexity

*Sorting* : `O(n log n)`  
*DP*       : `O(n²)` time, `O(n)` memory  
*Finding t* : `O(n)` time

--------------------------------------------------------------------

#### 5.  Reference implementation

Below is a concise Java implementation that follows the above
procedure.  
(It is equivalent to the classic solution for LeetCode 2781,
but written from scratch for clarity.)

```java
import java.util.*;

public class Solution {
    public int minimumSeconds(int[] nums1, int[] nums2, int x) {
        int n = nums1.length;

        // 1. sort indices by increasing nums2
        Integer[] idx = new Integer[n];
        for (int i = 0; i < n; i++) idx[i] = i;
        Arrays.sort(idx, (a, b) -> Integer.compare(nums2[a], nums2[b]));

        // 2. prefix sums of the original arrays
        long sum1 = 0, sum2 = 0;
        for (int i = 0; i < n; i++) {
            sum1 += nums1[i];
            sum2 += nums2[i];
        }

        // trivial case
        if (sum1 <= x) return 0;

        // 3. 1‑D DP
        int[] dp = new int[n + 1];          // dp[j] – best reduction with j ops
        for (int id = 0; id < n; id++) {
            int a = nums1[idx[id]];
            int b = nums2[idx[id]];
            // update from high j to low j to avoid overwriting
            for (int j = id + 1; j >= 1; j--) {
                int with = dp[j - 1] + a + b * j;
                int without = dp[j];
                dp[j] = Math.max(with, without);
            }
        }

        // 4. find minimal t
        for (int t = 0; t <= n; t++) {
            long cur = sum1 + sum2 * t - dp[t];
            if (cur <= x) return t;
        }
        return -1;    // even with all n operations it is not enough
    }
}
```

--------------------------------------------------------------------

#### 4.  Why this works

*Sorting guarantees that whenever we decide to zero a pair at time
`j`, all pairs with larger `nums2` will be considered later in the DP
and therefore we are allowed to spend the operation on them at a
later second, which is always optimal.*

*The DP considers every possible choice of zero‑operations
(“skip” vs “use”) for every pair, so `dp[n][t]` is the true maximum
reduction obtainable with `t` operations.*

*Finally we simply check the resulting sums for `t = 0 … n`; the first
time we can reach `x` is the required minimal number of seconds.*

--------------------------------------------------------------------

#### 5.  Final note

*If you prefer a memory‑saving version you can keep only the
1‑dimensional array as shown above – it still needs `O(n²)` time
because every element must be processed for every `t`.  
The algorithm is easily ported to Python or C++ as well; the key
recurrence stays the same.*

Happy coding!