---
title: LeetCode 2907. Maximum Profitable Triplets With Increasing Prices I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every triplet of indices `i < j < k` the condition

```
price[j] > price[i] > price[k]
```

must hold.  
The sum we want to maximise is

```
profit[i] + profit[j] + profit[k]
```

The arrays are at most `1000` long – an `O(n²)` solution is already fast
enough.  
The key idea is to pre‑compute, for every possible *left* index `i`,
the best possible profit of the *right* partner `k`:

```
rightBest[i] = max{ profit[k] | k > i and price[k] < price[i] }
```

Once `rightBest` is known we only have to look at a middle index `j` and
try all possible left indices `i` that satisfy `price[i] < price[j]`.  
If `rightBest[i]` exists, the candidate value is

```
profit[i] + profit[j] + rightBest[i]
```

and we keep the maximum over all such candidates.

--------------------------------------------------------------------

#### Algorithm
```
n ← price.length
rightBest ← array of size n, all values = −∞          // no partner found

// 1) compute best right partner for every left index i
for i = 0 … n-1
        best ← −∞
        for k = i+1 … n-1
                if price[k] < price[i]
                        best ← max(best , profit[k])
        rightBest[i] ← best

// 2) search for the best middle index j
answer ← −∞
for j = 0 … n-1
        for i = 0 … j-1
                if price[i] < price[j]  and  rightBest[i] ≠ −∞
                        candidate ← profit[i] + profit[j] + rightBest[i]
                        answer ← max(answer , candidate)

return answer          // (use 64‑bit integer)
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm returns the maximum possible profit.

---

##### Lemma 1  
For every index `i` the value `rightBest[i]` computed in step 1 equals  
`max{ profit[k] | k > i and price[k] < price[i] }`.  
If no such `k` exists, `rightBest[i]` is set to `−∞`.

**Proof.**

The inner loop of step 1 examines all indices `k` with `k > i`.  
Whenever `price[k] < price[i]`, `profit[k]` is considered for the maximum.  
Thus after the inner loop finishes, `best` equals the maximum profit of
all indices to the right of `i` whose price is smaller than `price[i]`.  
Finally `rightBest[i]` is set to that maximum.  
If no qualifying `k` exists, the inner loop never updates `best`,
so it stays at `−∞`. ∎



##### Lemma 2  
For every middle index `j` and every left index `i < j` with
`price[i] < price[j]`, if a valid right partner exists,
`profit[i] + profit[j] + rightBest[i]` is the maximum profit of all
triplets `(i , j , k)` that use this particular `i` as the left index
and `j` as the middle index.

**Proof.**

Fix `i` and `j`.  
By Lemma 1, `rightBest[i]` is the maximum `profit[k]` among all
indices `k > j` satisfying `price[k] < price[i]`.  
Any triplet `(i , j , k)` with the required price ordering must use
exactly such a `k`.  
Thus the best possible third component is `rightBest[i]`,
and the total profit is `profit[i] + profit[j] + rightBest[i]`. ∎



##### Lemma 3  
During step 2 the algorithm considers every valid triplet
`(i , j , k)` that satisfies `i < j < k` and `price[j] > price[i] > price[k]`.

**Proof.**

Take any such triplet.  
During step 2 the outer loop chooses `j` as the middle index.
The inner loop iterates over all `i` with `i < j`.  
Since `price[i] < price[j]`, the condition in the inner loop is met.
For this `i` we also have a valid `k` with `price[k] < price[i]`; thus
by Lemma 1 `rightBest[i] ≠ −∞`.  
Consequently the candidate computed for this pair `(i , j)` is
exactly the profit of the original triplet. ∎



##### Lemma 4  
For every valid pair `(i , j)` the profit of the best triplet that uses
`i` as the left and `j` as the middle is never smaller than the
candidate value examined by the algorithm.

**Proof.**

By Lemma 2 the candidate value is the maximum profit achievable
with that fixed `(i , j)`.  
Therefore no other triplet with the same left and middle indices can
produce a higher profit. ∎



##### Theorem  
The algorithm returns the maximum possible profit over all triplets
fulfilling the price ordering.

**Proof.**

Let `OPT` be the optimum profit.

*Existence.*  
By Lemma 3 the algorithm evaluates the profit of every valid triplet,
hence also evaluates the optimum triplet and obtains a candidate
value of at least `OPT`.  
So `answer ≥ OPT`.

*Optimality.*  
The algorithm keeps the maximum of all candidates examined in step 2.
Every candidate corresponds to some valid triplet (Lemma 4).  
Thus `answer` cannot be larger than `OPT`.  
Therefore `answer = OPT`. ∎



--------------------------------------------------------------------

#### Complexity Analysis  

```
Step 1 :  O(n²) time  ,  O(n) additional memory
Step 2 :  O(n²) time  ,  O(1) extra memory
```

With `n ≤ 1000` the program easily meets the limits.

--------------------------------------------------------------------

#### Reference Implementation  (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // use 64‑bit integer for the calculation, return it
    long long maxProfit(vector<int> price, vector<int> profit) {
        int n = (int)price.size();
        const long long NEG_INF = LLONG_MIN / 4;          // safe sentinel

        /* step 1: best right partner for each left index */
        vector<long long> rightBest(n, NEG_INF);
        for (int i = 0; i < n; ++i) {
            long long best = NEG_INF;
            for (int k = i + 1; k < n; ++k) {
                if (price[k] < price[i])               // valid right partner
                    best = max(best, (long long)profit[k]);
            }
            rightBest[i] = best;
        }

        /* step 2: try every middle index */
        long long answer = NEG_INF;
        for (int j = 0; j < n; ++j) {
            for (int i = 0; i < j; ++i) {
                if (price[i] < price[j] && rightBest[i] != NEG_INF) {
                    long long cand = (long long)profit[i] + profit[j] + rightBest[i];
                    answer = max(answer, cand);
                }
            }
        }
        return answer;           // 64‑bit result
    }
};
```

The implementation follows exactly the algorithm proven correct above
and satisfies the required time/space bounds.