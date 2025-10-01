---
title: LeetCode 2941. Maximum GCD-Sum of a Subarray - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every prefix `A[0 … i]` we want to know the best product

```
      (sum of the subarray) × (gcd of the subarray)
```

among all subarrays that end at position `i`.  
After we have the best product for the prefix, the answer for the whole
array is the maximum of all these values.

--------------------------------------------------------------------

#### 1.  Observation – only the leftmost start matters

All array values are **positive**.
If two subarrays end at the same position and have the same gcd,
the longer one has the larger sum, therefore the larger product.
Consequently for every possible gcd we only have to keep the
*earliest* start index of a subarray that ends at the current
position.

--------------------------------------------------------------------

#### 2.  Dynamic construction of the gcd list

Let  

```
dp[i] = vector of pairs (g, l)
```

where

* `g` – gcd of a subarray that ends at position `i`
* `l` – the **leftmost** start index among all subarrays that end at `i`
  and have gcd `g`.

The vector contains at most 20 elements because
gcds can only decrease, and once they reach 1 they never increase again.

*Base case*  
`dp[0]` consists of the single subarray `[A[0]]`:
```
dp[0] = { (A[0], 0) }
```

*Transition*  
Assume `dp[i-1]` is already known.
To build `dp[i]` we

```
1. start with (A[i], i)          // subarray consisting only of A[i]
2. for every pair (g, l) in dp[i-1]
       new_g = gcd(A[i], g)
       if new_g equals the last element of the vector
              keep the smaller start index (earlier start)
       else  append (new_g, l)
```

The loop over `dp[i-1]` is performed from the last element to the first,
so that when we encounter a duplicate gcd we can easily keep the
earliest start index with `min`.

--------------------------------------------------------------------

#### 3.  Evaluating candidates

While constructing `dp[i]` we already know the start indices and the gcd
for **every** subarray ending at position `i`.
For each pair `(g, l)` in the vector

```
length  = i - l + 1
if length >= K
        sum = prefix_sum[i+1] - prefix_sum[l]
        answer = max(answer, sum * g)
```

The prefix sums are stored as `long long` to avoid overflow.

--------------------------------------------------------------------

#### 4.  Correctness Proof  

We prove that the algorithm returns the maximum possible
`(subarray sum) × (subarray gcd)`.

---

##### Lemma 1  
For every position `i` the vector `dp[i]` contains, for each possible
gcd of a subarray ending at `i`, the smallest start index of such a
subarray.

**Proof.**

Induction over `i`.

*Base (`i = 0`)*  
`dp[0]` contains exactly one subarray `[A[0]]`.  
Its gcd is `A[0]` and its start index is `0`, which is obviously the
smallest possible start.

*Induction step*  
Assume the statement holds for `i-1`.  
During construction of `dp[i]` we process the pairs of `dp[i-1]`
in reverse order.  
For a pair `(g, l)` in `dp[i-1]` we form the subarray `[A[l … i]]`
whose gcd is `gcd(A[i], g)` and start index `l`.  
If this gcd already appears as the last element of the building
vector we replace its stored start with  
`min(old_start, l)`.  
Thus the stored start is the minimum among all subarrays with that gcd
ending at `i`.  
No other subarray ending at `i` has the same gcd, because every
possible gcd appears exactly once during this construction.
∎



##### Lemma 2  
For every position `i` and for every subarray that ends at `i`,
the algorithm evaluates its product `(sum × gcd)`.

**Proof.**

Consider an arbitrary subarray `A[l … i]`.  
While building `dp[i]` the pair `(g, l)` with
`g = gcd(A[l … i])` is produced from the corresponding pair in
`dp[l]` (by induction) and is kept in the final vector
(because the start index never changes afterwards).
Consequently this subarray’s product is evaluated when the
vector `dp[i]` is processed.
∎



##### Lemma 3  
Among all subarrays ending at a fixed position `i` the maximum product
is attained by one of the pairs stored in `dp[i]`.

**Proof.**

By Lemma&nbsp;2 every subarray ending at `i` appears in `dp[i]`
exactly once (its gcd and start index).  
The algorithm evaluates the product of each of these subarrays and
keeps the maximum.  
Therefore the maximum product for this prefix is found. ∎



##### Theorem  
The algorithm outputs the maximum value of  
`(sum of a subarray) × (gcd of the same subarray)`
over all subarrays with length at least `K`.

**Proof.**

For every position `i` (from `0` to `N-1`) the algorithm
examines every subarray that ends at `i` (Lemma&nbsp;2) and keeps the
largest product that satisfies the length condition
(Lemma&nbsp;3).  
The global answer is the maximum of all these local maxima,
hence it is the maximum over **all** subarrays of length at least `K`.
∎



--------------------------------------------------------------------

#### 5.  Complexity Analysis

`dp[i]` contains at most 20 pairs (the gcd can only decrease and
once it becomes `1` it never changes).

```
time   :  O(N × 20)   ≈ 2·10^6 operations for N = 2·10^5
memory :  O(20)       (plus the prefix sum array)
```

Both limits easily fit into the required constraints.

--------------------------------------------------------------------

#### 6.  Reference Implementation  (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int N, K;
    if (!(cin >> N >> K)) return 0;
    vector<long long> A(N);
    for (int i = 0; i < N; ++i) cin >> A[i];

    /* prefix sums */
    vector<long long> pref(N + 1, 0);
    for (int i = 0; i < N; ++i)
        pref[i + 1] = pref[i] + A[i];

    long long answer = 0;

    /* dp vector for the previous position (initially for i = -1) */
    vector<pair<long long, int>> prev;           // empty for i = -1

    for (int i = 0; i < N; ++i) {
        vector<pair<long long, int>> cur;
        /* step 1: subarray consisting only of A[i] */
        cur.emplace_back(A[i], i);

        /* step 2: extend all subarrays from previous prefix */
        for (int idx = (int)prev.size() - 1; idx >= 0; --idx) {
            long long g = prev[idx].first;
            int start   = prev[idx].second;
            long long new_g = std::gcd(A[i], g);
            if (cur.back().first == new_g) {
                /* duplicate gcd – keep earlier start */
                cur.back().second = min(cur.back().second, start);
            } else {
                cur.emplace_back(new_g, start);
            }
        }

        /* step 3: evaluate all candidates */
        for (const auto &p : cur) {
            long long g   = p.first;
            int start     = p.second;
            int len       = i - start + 1;
            if (len >= K) {
                long long sum = pref[i + 1] - pref[start];
                answer = max(answer, sum * g);
            }
        }

        prev.swap(cur);   // dp[i] becomes the previous vector
    }

    cout << answer << '\n';
    return 0;
}
```

The program follows exactly the algorithm proven correct above
and conforms to the C++17 standard.