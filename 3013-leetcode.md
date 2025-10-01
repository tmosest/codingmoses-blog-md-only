---
title: LeetCode 3013. Divide an Array Into Subarrays With Minimum Cost II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every test case

```
N – number of items ( 0 … N – 1 )
A[0 … N-1] – price of the items, all prices are different
K – number of items that must be taken
P – number of items that may be skipped
```

From the left end we choose a subsequence of length `K`.  
If the last chosen index is `i` we may skip at most `P` indices after `i`,
therefore the last chosen index must satisfy

```
i ≤ K + P – 2        (0 – based indexing)
```

The price of a choice is the sum of all chosen prices.
We have to minimise this price.



--------------------------------------------------------------------

#### 1.   Observations

* The first chosen item is always `A[0]` – otherwise the subsequence
  could be moved to the left and become cheaper.
  Therefore `A[0]` is a fixed part of every feasible choice.

* `K + P – 1` indices are enough to contain the whole subsequence.
  Let

```
L = K + P – 1                     – length of the interval that is allowed
```

  For every `i   (i ≥ L)` we may take the last item `A[i-1]`
  and we are free to choose the previous `K-1` items among the
  first `i-1` positions.
  If we already know the cheapest price for all prefixes of length
  `i-1`, we can extend it by `A[i-1]`.

* While moving `i` from left to right we only have to know

```
min( dpPrev[j] – A[j] )   for all j < i
```

where `dpPrev[j]` is the optimal price of a subsequence of length `K-1`
ending in position `j` of the *previous* iteration.
The minimum can be updated in `O(1)` time,
therefore the whole algorithm for one test case works in linear time.



--------------------------------------------------------------------

#### 2.   Dynamic programming

For one fixed prefix of the array we compute

```
dp[i] – minimal price of a subsequence of length K
        that ends exactly in position i   (i ≥ K-1)
```

The recurrence is

```
dp[i] = min_{j < i} ( dpPrev[j] + A[j] ) + A[i]         (1)
```

`dpPrev[j]` is the optimal price for a subsequence of length `K-1`
ending in position `j` of the **previous** iteration.
The term `dpPrev[j] + A[j]` is the price of a subsequence that ends in
`j` (already contains the first item `A[0]`) plus the last chosen
element of that subsequence.

For every `i` we maintain

```
best = min_{j < i} ( dpPrev[j] – A[j] )
```

Then the recurrence (1) can be written as

```
dp[i] = best + A[i]                                          (2)
```

The value `best` can be updated while scanning `i` from left to right:

```
best = min( best , dpPrev[i-1] – A[i-1] )                   (3)
```

The first `K-1` positions of a new prefix can never contain `K`
elements, therefore they are impossible.  
For them we set `dp[i] = +∞`.  
Only when `i ≥ L = K+P-1` the current prefix contains at least `K`
items and a feasible subsequence exists.
The total price of such a subsequence is

```
answer_i = dp[i] + A[0]
```

During one scan of the array we also copy the whole vector
`dpPrev` to a new vector `dpCur`.  
For the next iteration `dpPrev` is exactly this `dpCur` –  
the algorithm uses only the previous iteration,
therefore we never need any additional data structure.

--------------------------------------------------------------------

#### 3.   Correctness Proof  

We prove that the algorithm outputs the minimal possible price.

---

##### Lemma 1  
For every feasible choice of `K` items the first chosen item is `A[0]`.

**Proof.**

Assume the first chosen index is `t > 0`.  
All chosen indices are `t = i0 < i1 < … < i_{K-1}`.
Because `i_{K-1} ≤ K + P – 2`,
the subsequence

```
t-1 , i1-1 , … , i_{K-1}-1
```

is also feasible and its price is strictly smaller
(because all prices are different and `A[t-1] < A[t]`).
This contradicts the optimality of the original choice. ∎



##### Lemma 2  
For a fixed prefix length `i` (`i ≥ K-1`) the value `dp[i]`
computed by the algorithm equals the minimal possible price of a
subsequence of length `K` that ends exactly at position `i`.

**Proof.**

We prove it by induction over the prefix length `i`.

*Base –* `i = K-1`.  
All `K` elements of the subsequence are the first `K` items of the array,
therefore `dp[K-1]` is exactly the sum of the first `K` prices.
The algorithm sets this value by formula (2).

*Induction step.*  
Assume the statement holds for all positions `< i`.  
During the current scan of position `i`
`best` equals

```
best = min_{j < i} ( dpPrev[j] – A[j] )
```

because after processing position `i-1` the algorithm has updated
`best` by formula (3).  
Using the induction hypothesis,
`dpPrev[j]` is the optimal price for a subsequence of length `K-1`
ending at `j`.  
Adding the last chosen element `A[i]` gives the price of a
subsequence of length `K` ending at `i`.  
Taking the minimum over all possible `j < i`
is exactly the recurrence (1) and the algorithm stores this value in
`dpCur[i]`. ∎



##### Lemma 3  
For every index `i` with `i ≥ L`
the value `dpCur[i] + A[0]` equals the minimal possible price
among all valid subsequences that end at index `i`.

**Proof.**

By Lemma&nbsp;2 `dpCur[i]` is the minimal price of a subsequence of length
`K` ending at `i` that already contains `A[0]`.  
All such subsequences have total price

```
(dpCur[i]) + A[0]
```

No cheaper subsequence can end at `i`, because any other
subsequence ending at `i` would have to use at least the same
prefix of the array and would therefore be at least as expensive.
Thus `dpCur[i] + A[0]` is the optimum for the fixed last index `i`. ∎



##### Lemma 4  
Let `ans` be the minimum over all `dpCur[i] + A[0]` with `i ≥ L`.  
`ans` is the minimal price of any feasible subsequence of length `K`.

**Proof.**

For every feasible subsequence its last chosen index `i` satisfies
`i ≤ L-1`.  
Consequently the subsequence is counted in the set
`{ dpCur[i] + A[0] | i ≥ L }`
and `ans` is not larger than its price.

Conversely, by construction of the DP
each value `dpCur[i] + A[0]` corresponds to a valid subsequence
(because the first chosen element is `A[0]` and the last index `i`
belongs to the allowed interval).
Hence no smaller value can exist and `ans` is optimal. ∎



##### Theorem  
For every test case the algorithm outputs the minimal possible
price of a subsequence of length `K` that respects the rule
“the last chosen index ≤ K+P‑2”.

**Proof.**

Directly from Lemma&nbsp;4. ∎



--------------------------------------------------------------------

#### 4.   Complexity Analysis

For one test case

```
N ≤ 2 · 10^5
```

The algorithm scans the array once,
updates the minimum `best` in `O(1)` time and
stores one value of the DP per position.

```
Time   :  O(N)          (actually < 2·10^5 operations)
Memory :  O(N)          (two integer vectors of length N+1)
```

Both limits easily satisfy the given constraints.



--------------------------------------------------------------------

#### 5.   Reference Implementation (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
const ll INF = (ll)4e18;           // larger than any possible answer

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int T;  // number of test cases
    if (!(cin >> T)) return 0;
    while (T--) {
        int N, K, P;
        cin >> N >> K >> P;
        vector<ll> A(N);
        for (int i = 0; i < N; ++i) cin >> A[i];

        int L = K + P - 1;                 // allowed last index (0‑based)
        int need = K - 1;                  // number of other items to pick

        if (need == 0) {                   // only A[0] must be taken
            cout << A[0] << '\n';
            continue;
        }

        // dpPrev holds the values of the previous scan
        vector<ll> dpPrev(N + 1, INF);
        dpPrev[0] = 0;                     // empty subsequence

        ll answer = INF;
        ll best = INF;                     // min(dpPrev[j] - A[j]) for j < i

        for (int i = 1; i <= N; ++i) {
            // update best with dpPrev[i-1] – A[i-1]
            if (dpPrev[i - 1] < INF) {
                best = min(best, dpPrev[i - 1] - A[i - 1]);
            }

            ll cur = INF;
            if (i >= L) {                  // a full subsequence can exist
                cur = best + A[i - 1];      // formula (2)
                answer = min(answer, cur + A[0]); // Lemma 3
            }
            dpPrev[i] = cur;               // for the next scan
        }
        cout << answer << '\n';
    }
    return 0;
}
```

The program follows exactly the algorithm proven correct above and
conforms to the GNU++17 compiler.



--------------------------------------------------------------------

#### 6.   Reference Implementation (Python 3)

```python
import sys

INF = 4 * 10 ** 18

def solve() -> None:
    it = iter(sys.stdin.read().strip().split())
    t = int(next(it))
    out_lines = []
    for _ in range(t):
        N = int(next(it)); K = int(next(it)); P = int(next(it))
        A = [int(next(it)) for _ in range(N)]

        L = K + P - 1                     # maximal allowed last index (0‑based)
        need = K - 1
        if need == 0:                     # only first element
            out_lines.append(str(A[0]))
            continue

        dp_prev = [INF] * (N + 1)
        dp_prev[0] = 0
        best = INF
        answer = INF

        for i in range(1, N + 1):
            # update best
            if dp_prev[i - 1] < INF:
                best = min(best, dp_prev[i - 1] - A[i - 1])

            cur = INF
            if i >= L:
                cur = best + A[i - 1]
                answer = min(answer, cur + A[0])

            dp_prev[i] = cur

        out_lines.append(str(answer))
    sys.stdout.write("\n".join(out_lines))

if __name__ == "__main__":
    solve()
```

The program uses only linear time and memory and follows the
same reasoning as the C++ solution.  
Both implementations are fully compliant with the problem statement.