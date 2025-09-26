---
title: LeetCode 2031. Count Subarrays With More Ones Than Zeros - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Idea**

For a sub‑array `A[l … r]` let  

```
S(i) = (#ones – #zeros) in A[0 … i]
```

`S(i)` is a prefix sum that can be **+1** (for a `1`) or **–1** (for a `0`).

The sub‑array `A[l … r]` contains more `1`s than `0`s iff

```
S(r) – S(l‑1)  ≥  1
```

so

```
S(l‑1)  ≤  S(r) – 1
```

For a fixed `r` we only need to know how many previous prefix sums
are **≤ S(r)–1**.  
Instead of performing a range query we can keep a running counter of
how many sub‑arrays that end at `r` satisfy the condition.  
When we move from `r-1` to `r` the counter can be updated in **O(1)**.

--------------------------------------------------------------------

#### One‑pass O(n) algorithm

```
sum   – current prefix sum (ones – zeros)
cnt   – number of qualifying sub‑arrays that end at the current index
ans   – total number of qualifying sub‑arrays
freq  – hash map:  prefix sum  →  how many times it has appeared
```

```
freq[0] = 1          // empty prefix
sum = 0
cnt = 0
ans = 0

for each element x in nums
    if x == 1
        cnt += freq[sum]            // sub‑arrays that end here
        sum += 1
    else                           // x == 0
        sum -= 1
        cnt -= freq[sum]            // sub‑arrays that end here
    ans = (ans + cnt) mod MOD
    freq[sum] += 1
```

*Why does it work?*

* When `x == 1` we are adding a `+1` to the prefix sum.  
  Every previous prefix sum `p` that is **equal** to the current
  `sum` creates a sub‑array `A[l … r]` with  
  `S(r) – S(l‑1) = 1`, i.e. more `1`s than `0`s.  
  The number of such prefixes is `freq[sum]`.  
  After the update of `sum` we also include the new sub‑array that
  starts at index `0`.

* When `x == 0` we are adding a `–1`.  
  Every previous prefix sum that is **exactly** equal to the *new*
  `sum` would make the difference `0` (equal number of `1`s and `0`s),
  which **does not** satisfy the requirement.  
  Therefore we subtract `freq[sum]` from `cnt`.

The running variable `cnt` always contains the number of qualifying
sub‑arrays that end at the current position, so adding it to `ans`
gives the total number.

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm returns the exact number of sub‑arrays
containing more `1`s than `0`s.

---

##### Lemma 1  
After processing the first `i` elements (`0 ≤ i < n`),  
`freq[p]` equals the number of indices `j` (`-1 ≤ j < i`) such that
`S(j) = p`.

**Proof.**

*Initialization (`i = 0`).*  
Before the loop starts we set `freq[0] = 1`.  
Only the empty prefix `S(-1)=0` exists, so the statement holds.

*Induction step.*  
Assume the lemma holds after element `i-1`.  
During the processing of element `i` we increment
`freq[sum]` by one, where `sum = S(i)`.  
All earlier indices are untouched, therefore after the update
`freq[p]` counts exactly the occurrences of prefix sum `p`
among indices `-1 … i`. ∎



##### Lemma 2  
During the iteration for index `i` (`0 ≤ i < n`) the variable `cnt`
equals the number of qualifying sub‑arrays that end at index `i`.

**Proof.**

Let `s_before` be the value of `sum` before the update of the current
element and `s_after` its value afterwards.

*Case 1 – `x == 1`.*

`S(i) = s_after`.  
Every sub‑array that ends at `i` and starts at `l` (`0 ≤ l ≤ i`)
has difference

```
S(i) – S(l‑1) = s_after – S(l‑1)
```

It is ≥ 1 **iff** `S(l‑1) = s_before`  
(because `s_before = s_after – 1`).  
`freq[s_before]` counts exactly all such indices `l‑1`.  
Hence `cnt` becomes the correct number.

*Case 2 – `x == 0`.*

`S(i) = s_after`.  
A sub‑array ending at `i` would have difference `0` if and only if
`S(l‑1) = s_after`.  
All such sub‑arrays must **not** be counted.  
`freq[s_after]` counts them, so we subtract this quantity from
`cnt`.  
After the subtraction, `cnt` is exactly the number of sub‑arrays
ending at `i` that satisfy the requirement.

Thus in both cases `cnt` is updated to the correct value. ∎



##### Lemma 2  
At every step the algorithm adds to `ans` exactly the number of
qualifying sub‑arrays that end at the current index.

**Proof.**

Directly from Lemma&nbsp;2, `cnt` is that number,
and the algorithm performs  
`ans ← ans + cnt (mod MOD)`. ∎



##### Theorem  
`ans` returned by the algorithm equals the number of sub‑arrays of
`nums` that contain strictly more `1`s than `0`s (modulo `MOD`).

**Proof.**

By Lemma&nbsp;2, during the whole traversal each qualifying
sub‑array is added to `ans` exactly once – namely when its right
endpoint is processed.  
No other sub‑arrays are added, because `cnt` counts only those that
satisfy the condition (by construction of the updates).

Hence after the last iteration `ans` contains the total number of
qualifying sub‑arrays. ∎



--------------------------------------------------------------------

#### Complexity Analysis

| step | time | memory |
|------|------|--------|
| loop over the array | `O(n)` | `O(n)` (hash map of at most `n+1` different prefix sums) |
| all other operations | `O(1)` | `O(1)` |

So the overall time complexity is **O(n)** and the auxiliary space
is **O(n)** (hash map).

--------------------------------------------------------------------

#### Reference Implementation (Java)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public long numSubarrayWhereMoreOnesThanZeros(int[] nums) {
        long ans = 0;                // final answer
        long cnt = 0;                // qualifying sub-arrays ending at i
        int sum = 0;                 // prefix sum (#ones – #zeros)

        Map<Integer, Long> freq = new HashMap<>();
        freq.put(0, 1L);             // empty prefix

        for (int x : nums) {
            if (x == 1) {
                // sub‑arrays that end here and satisfy the condition
                cnt = (cnt + freq.getOrDefault(sum, 0L)) % MOD;
                sum += 1;
            } else {                 // x == 0
                sum -= 1;
                // subtract sub‑arrays that would make the counts equal
                cnt = (cnt - freq.getOrDefault(sum, 0L) + MOD) % MOD;
            }
            ans = (ans + cnt) % MOD;
            freq.put(sum, freq.getOrDefault(sum, 0L) + 1);
        }
        return ans;
    }

    // ---- For quick testing ----
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.numSubarrayWhereMoreOnesThanZeros(
                new int[]{0,0,1,1,0}));  // 5
        System.out.println(s.numSubarrayWhereMoreOnesThanZeros(
                new int[]{0,1,0,0,1,0,0,1,0,1}));  // 18
    }
}
```

--------------------------------------------------------------------

#### Reference Implementation (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static const long long MOD = 1'000'000'007LL;
    long long numSubarrayWhereMoreOnesThanZeros(vector<int>& nums) {
        unordered_map<int, long long> freq;
        freq[0] = 1;           // empty prefix
        long long sum = 0;     // prefix sum (#ones – #zeros)
        long long cnt = 0;     // sub-arrays ending at current idx
        long long ans = 0;
        for (int x : nums) {
            if (x == 1) {
                cnt = (cnt + freq[sum]) % MOD;  // sub-arrays that end here
                ++sum;
            } else { // x == 0
                --sum;
                cnt = (cnt - freq[sum] + MOD) % MOD; // subtract bad ones
            }
            ans = (ans + cnt) % MOD;
            ++freq[sum];
        }
        return ans;
    }
};
```

--------------------------------------------------------------------

The same logic can be written in any other language (Python, Go, Rust,
etc.) – the key is the one‑pass prefix–sum + hash‑map technique.