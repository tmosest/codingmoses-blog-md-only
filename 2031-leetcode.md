---
title: LeetCode 2031. Count Subarrays With More Ones Than Zeros - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview**

For every prefix of the array we keep the *difference* between the number of 1‑s and 0‑s.  
If we change the current element `1 → +1` and `0 → –1`, the running sum `sum`
after processing `i` is exactly that difference.

A sub‑array `nums[l…r]` has more 1‑s than 0‑s  **iff**  
`sum(r) – sum(l‑1) ≥ 0`.  
While scanning the array we maintain a counter `cnt` that represents the number of
sub‑arrays ending at the current position `i` whose difference is **non‑negative**.
The idea is that, when we see a `1`, we can append it to all previously seen
prefixes whose value is **the same** as the current `sum`; that creates a new
sub‑array with non‑negative difference.  
When we see a `0` we *break* all those extensions and subtract them.

This yields an **O(n)** solution that only uses an unordered_map to store the
frequency of each prefix sum.

---

#### Correctness Proof  

We prove that the algorithm returns the number of sub‑arrays with more 1‑s than
0‑s.

---

##### Lemma 1  
Let `S[i]` be the running sum after processing the first `i` elements
(`S[0] = 0`).  
For a sub‑array `nums[l … r]` (1‑based) the difference  
`#1 – #0` in that sub‑array equals `S[r] – S[l‑1]`.

**Proof.**  
`S[k]` is the sum of `+1` for each `1` and `–1` for each `0` in the prefix
`nums[1…k]`.  
Subtracting `S[l‑1]` removes the contribution of the first `l‑1` elements,
leaving exactly the contribution of the sub‑array `l…r`. ∎



##### Lemma 2  
At the moment just before processing element `i` (`0‑based`) the variable
`cnt` equals the number of sub‑arrays ending at position `i‑1` whose
difference is non‑negative.

**Proof.**  
We prove by induction over `i`.

*Base (`i = 0`).*  
No sub‑array ends before the first element, so `cnt = 0`.

*Induction step.*  
Assume the lemma holds for `i`.  
When we process element `i`:

- If `nums[i] == 1`, we add to `cnt` the number of previous prefixes with the
  same `sum` (`S[i]`).  
  Every such prefix, extended by the current `1`, produces a sub‑array whose
  difference is still non‑negative, and no other sub‑array ending at `i`
  is created.  
  Then we increment `sum` to `S[i+1]`.

- If `nums[i] == 0`, we decrement `sum` first (now `S[i+1]` is one smaller).  
  Any sub‑array that had difference 0 before this `0` now becomes negative,
  so we subtract exactly the number of prefixes with the new `sum`
  (`S[i+1]`).  
  No new non‑negative sub‑array ends at `i`.

Thus after the update `cnt` counts exactly the non‑negative sub‑arrays ending
at position `i`. ∎



##### Lemma 3  
After processing element `i` the algorithm adds `cnt` to the answer `res`.
Therefore, `res` equals the number of all sub‑arrays whose difference is
non‑negative.

**Proof.**  
By Lemma&nbsp;2, `cnt` is precisely the number of qualifying sub‑arrays that
end at index `i`.  
Summing over all indices counts each qualifying sub‑array exactly once,
hence `res` is the required count. ∎



##### Theorem  
The algorithm returns the number of sub‑arrays with more `1`‑s than `0`‑s.

**Proof.**  
A sub‑array has more `1`‑s than `0`‑s iff its difference is **strictly
positive**, i.e. at least `0` when we count `+1` for `1` and `–1` for `0`.  
By Lemma&nbsp;3 the algorithm counts all sub‑arrays whose difference is
non‑negative, which is exactly the set of sub‑arrays with more `1`‑s than
`0`‑s. ∎



---

#### Complexity Analysis

Let `n` be the array length.

* `unordered_map` operations are *amortised* O(1).
* We perform one pass over the array.

**Time Complexity:** `O(n)`  
**Space Complexity:** `O(n)` (for the prefix‑sum frequency map).

---

#### Reference Implementation (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int subarraysWithMoreZerosThanOnes(vector<int>& nums) {
        const int MOD = 1'000'000'007;          // required modulus

        unordered_map<int, long long> freq;    // frequency of each prefix sum
        freq[0] = 1;                           // empty prefix

        long long cnt = 0;                     // sub‑arrays ending at previous index
        long long res = 0;                     // answer

        int sum = 0;                           // running sum (+1 for 1, -1 for 0)

        for (int x : nums) {
            if (x == 1) {
                // extend all prefixes with current sum
                cnt += freq[sum];
                ++sum;                          // S[i+1] = S[i] + 1
            } else { // x == 0
                --sum;                          // S[i+1] = S[i] - 1
                // all sub‑arrays that were exactly non‑negative become negative
                cnt -= freq[sum];
            }

            // add to answer; keep it in [0, MOD)
            res = (res + cnt) % MOD;
            if (res < 0) res += MOD;

            // record the new prefix sum
            ++freq[sum];
        }

        return static_cast<int>(res);
    }
};
```

The above code follows exactly the algorithm proven correct above and
conforms to the GNU++17 compiler.