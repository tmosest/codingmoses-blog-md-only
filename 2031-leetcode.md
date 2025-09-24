---
title: LeetCode 2031. Count Subarrays With More Ones Than Zeros - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every element

```
0  →  -1
1  →  +1
```

Let  

```
pref[i] = Σ (-1 or +1) of the first i elements   (pref[0] = 0)
```

For a sub‑array `i … j-1` its difference

```
(#ones – #zeros) = pref[j] – pref[i]
```

The sub‑array is *valid* iff this difference is **positive**.

```
pref[j] – pref[i] > 0   →   pref[i] < pref[j]
```

So for every `j` we only have to know how many of the already processed
prefix sums are strictly smaller than `pref[j]`.
The answer is the sum of these counts for all `j`.

The task is therefore a standard “count pairs with a strict inequality”.
We can solve it in `O(n log n)` with a Fenwick tree (Binary Indexed Tree)
after coordinate compression of all prefix sums.



--------------------------------------------------------------------

#### Algorithm
```
pref[0] = 0
for i = 0 … n-1
        pref[i+1] = pref[i] + (nums[i]==1 ? 1 : -1)

compress all values in pref to indices 0 … m-1   (m ≤ n+1)

BIT bit(m)           // supports add(idx,1) and sum(idx) = # of values ≤ idx
answer = 0
MOD = 1_000_000_007

bit.add(index(pref[0]), 1)          // pref[0] is seen before any element

for j = 1 … n
        idx = index(pref[j])
        less = bit.sum(idx-1)       // # of prefix sums < pref[j]
        answer = (answer + less) % MOD
        bit.add(idx, 1)             // now pref[j] becomes available for later indices

return answer
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm outputs the number of sub‑arrays with
more ones than zeros.

---

##### Lemma 1  
For any indices `i < j` the sub‑array `i … j-1` contains more ones than
zeros **iff** `pref[i] < pref[j]`.

**Proof.**

`pref[j] – pref[i]` is exactly `#ones – #zeros` in that sub‑array.
The sub‑array has more ones than zeros ⇔ this value is > 0.
Rearranging gives `pref[i] < pref[j]`. ∎



##### Lemma 2  
When the loop processes position `j (≥1)`,  
`bit.sum(idx-1)` equals the number of indices `i` with `0 ≤ i < j`
and `pref[i] < pref[j]`.

**Proof.**

Before the `j`‑th iteration the BIT contains exactly all indices `i`
with `0 ≤ i < j` because every prefix sum is inserted immediately after
its index is processed.
The BIT stores the frequency of each compressed prefix sum.
`bit.sum(idx-1)` counts all indices whose compressed value is `< idx`,
i.e. all `pref[i]` strictly smaller than `pref[j]`. ∎



##### Lemma 2.1  
`bit.sum(idx-1)` is well defined for all `j`.  
If `idx` is the compressed value of `pref[j]`,
then the set `{ i | 0 ≤ i < j , pref[i] < pref[j] }` is empty
iff `idx` is the smallest compressed value, in which case
`bit.sum(idx-1) = 0`. ∎



##### Lemma 3  
During the `j`‑th iteration the variable `less` added to the answer
equals the number of valid sub‑arrays ending at position `j-1`.

**Proof.**

By Lemma&nbsp;1 a sub‑array ending at `j-1` and starting at `i` is
valid exactly when `pref[i] < pref[j]`.  
All such `i` are already inserted into the BIT before the current
iteration (because we insert `pref[j]` only after the query).  
Consequently `less = bit.sum(idx-1)` counts *all* indices `i < j`
with `pref[i] < pref[j]`.  
These are precisely the valid sub‑arrays that end at `j-1`. ∎



##### Lemma 4  
After finishing the loop the variable `answer` equals the total number
of valid sub‑arrays.

**Proof.**

`answer` starts at 0.  
In each iteration we add `less` – the number of valid sub‑arrays ending
at the current position (Lemma&nbsp;3).  
Every valid sub‑array has a unique right end `j-1`, therefore it is
counted exactly once.  
Summation over all `j` gives the total number of valid sub‑arrays. ∎



##### Theorem  
`subarraysWithMoreZerosThanOnes(nums)` (the function implemented
below) returns the number of sub‑arrays that contain more ones than
zeros, modulo `1 000 000 007`.

**Proof.**

By Lemma&nbsp;4, after the loop `answer` equals that number.
The function returns `answer` (modulo `MOD`). ∎



--------------------------------------------------------------------

#### Complexity Analysis

*Computing the prefix sums* – `O(n)`  
*Compression* – sorting `n+1` values, `O(n log n)`  
*Loop* – each iteration performs two Fenwick operations,
each `O(log n)`.

```
Time   :  O(n log n)
Memory :  O(n)
```

--------------------------------------------------------------------

#### Reference Implementation (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Fenwick {
    vector<int> bit;
    int n;
public:
    explicit Fenwick(int n = 0) : n(n), bit(n + 1, 0) {}
    void add(int idx, int val = 1) {            // idx: 0‑based
        for (int i = idx + 1; i <= n; i += i & -i) bit[i] += val;
    }
    int sum(int idx) const {                    // # of values with index ≤ idx
        int r = 0;
        for (int i = idx + 1; i > 0; i -= i & -i) r += bit[i];
        return r;
    }
};

int subarraysWithMoreZerosThanOnes(const vector<int> &nums) {
    const int MOD = 1'000'000'007;
    int n = (int)nums.size();

    // prefix sums: 0 → -1, 1 → +1
    vector<long long> pref(n + 1, 0);
    for (int i = 0; i < n; ++i)
        pref[i + 1] = pref[i] + (nums[i] == 1 ? 1 : -1);

    // coordinate compression
    vector<long long> all = pref;
    sort(all.begin(), all.end());
    all.erase(unique(all.begin(), all.end()), all.end());

    auto getIdx = [&](long long v) -> int {
        return (int)(lower_bound(all.begin(), all.end(), v) - all.begin());
    };

    Fenwick bit((int)all.size());
    long long ans = 0;

    bit.add(getIdx(pref[0]), 1);                 // pref[0] is already seen

    for (int j = 1; j <= n; ++j) {
        int idx = getIdx(pref[j]);
        int less = (idx > 0) ? bit.sum(idx - 1) : 0;
        ans = (ans + less) % MOD;
        bit.add(idx, 1);
    }
    return (int)ans;
}
```

The program follows exactly the algorithm proven correct above
and is fully compliant with the GNU++17 compiler.