---
title: LeetCode 3573. Best Time to Buy and Sell Stock V - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every day we know the stock price `A[i]`.  
During the whole period at most `K` *transactions* may be performed.

A transaction can be

* **normal** – buy at day `i` and sell at day `j` (`i < j`);
* **short**   – sell at day `i` and buy back at day `j` (`i < j`).

After a transaction the whole money is in cash again, therefore a new
transaction may only start **after** the previous one has finished.
While a transaction is still in progress we are either

```
holding a bought stock   – we have paid the price, we are waiting to sell
holding a short sold     – we received the price, we are waiting to buy back
```

--------------------------------------------------------------------

#### 1.   States

For every transaction level `t ( 0 … K )`

```
res[t]   – best total profit after completing t transactions          (cash)
bought[t] – best profit if we are currently holding a bought stock in
            the t‑th transaction (waiting to sell)
sold[t]   – best profit if we are currently holding a short sold stock
            in the t‑th transaction (waiting to buy back)
```

`bought` and `sold` are defined only for the *next* transaction  
(`bought[t]` / `sold[t]` belong to transaction number `t+1`), therefore
their size is `K` (not `K+1`).

All values are kept as 64‑bit signed integers – a price can be up to
`10⁹`, `K ≤ 100`, the result fits easily into a signed 64‑bit integer.

--------------------------------------------------------------------

#### 2.   Transition for one price `a = A[i]`

We scan the days from left to right, processing the price `a` once.
For every transaction level `j` (going **backwards** from `K` to `1`) we
update the three arrays:

```
1) Finish a transaction now

   res[j] can stay unchanged,
   or we can finish a normal transaction:
           bought[j-1] + a
   or finish a short transaction:
           sold[j-1]   - a

   res[j] = max( res[j], bought[j-1] + a, sold[j-1] - a )

2) Start a new transaction (normal) on this day
   we may open a "bought" position for transaction j

   bought[j-1] = max( bought[j-1],  res[j-1] - a )

3) Start a new transaction (short) on this day
   we may open a "short sold" position for transaction j

   sold[j-1] = max( sold[j-1],  res[j-1] + a )
```

The loops are executed backwards (`j` decreasing) because
`bought[j-1]` and `sold[j-1]` on the right hand side still belong to the
previous transaction level `j-1`.  
If we used a forward loop we would overwrite values that are still
needed in the same iteration.

--------------------------------------------------------------------

#### 3.   Correctness Proof  

We prove that the algorithm returns the maximum possible profit.

---

##### Lemma 1  
After processing the first `i` prices (`A[0…i]`),  
`res[j]` equals the maximum total profit achievable after at most `j`
completed transactions (normal or short) using only these `i+1` days.

**Proof.**

Induction over `i`.

*Base `i = 0`*  
Before the first day `res[0] = 0` (no money spent, no transaction
completed).  
For `j>0` no transaction can be finished, therefore
`res[j] = 0` which is optimal.  The lemma holds.

*Induction step*  
Assume the lemma holds after day `i-1`.  
During the iteration for day `i` the algorithm considers all ways to
*finish* a transaction on day `i` (case 1).  
All possibilities that finish a transaction on day `i` use the optimal
profit of the previous day (`res[j-1]`), the optimal profit while
holding a bought/short position (`bought[j-1]` /
`sold[j-1]`), and then the new price `a`.  
Thus the maximum over these three possibilities is exactly the best
profit after completing `j` transactions on the first `i+1` days.
`res[j]` cannot be improved in any other way because all other states
either keep an already finished transaction (`res[j]` unchanged) or
perform an unfinished transaction (handled in the other two arrays). ∎



##### Lemma 2  
After processing the first `i` prices,  
`bought[j]` equals the maximum profit that can be achieved
after completing at most `j` transactions **and** still holding a
bought stock that was bought in transaction `j+1` on day `i+1`.

**Proof.**

Induction over `i`.  
The update in step&nbsp;2 uses the optimal profit `res[j-1]` from the
previous transaction level and subtracts the current price.
Therefore `bought[j]` is exactly the optimal profit for an open normal
transaction of level `j+1`.  
No other update changes `bought[j]`, hence the lemma holds. ∎



##### Lemma 3  
After processing the first `i` prices,  
`sold[j]` equals the maximum profit that can be achieved after completing
at most `j` transactions and still holding a short sold stock of
transaction `j+1` on day `i+1`.

**Proof.**

Analogous to Lemma&nbsp;2, using step&nbsp;3 of the transition. ∎



##### Lemma 4  
During the update for a fixed price `a` and transaction level `j`,
the assignments

```
res[j] = max(res[j], bought[j-1] + a, sold[j-1] - a)
bought[j-1] = max(bought[j-1], res[j-1] - a)
sold[j-1]   = max(sold[j-1],   res[j-1] + a)
```

maintain the invariant of Lemma&nbsp;1, Lemma&nbsp;2, and Lemma&nbsp;3 for
all indices `< j` unchanged, and correctly update the entries for `j`
and `j-1`.

**Proof.**

*Case 1 – finishing a transaction.*  
The algorithm considers all three possibilities that end a transaction
at this day and keeps the maximum; no other strategy can produce a
higher profit for `res[j]`.

*Case 2 – starting a new normal transaction.*  
`bought[j-1]` is only used if later a transaction is finished in this
day or a later day.  
Taking the maximum of its current value and the profit of a new buy
(`res[j-1] - a`) gives the optimal value for an open bought position.

*Case 3 – starting a new short transaction.*  
Same reasoning for `sold[j-1]`.

The backward order of `j` guarantees that the values on the right hand
side still belong to the previous transaction level, so the updates
are correct. ∎



##### Lemma 5  
After all prices are processed, `res[K]` equals the maximum total profit
achievable after at most `K` transactions.

**Proof.**

By Lemma&nbsp;1 after the last day (`i = N-1`) the array `res` contains
the optimal profit for every transaction count `0…K`.
More transactions can never decrease the profit,
therefore the entry for `K` is the best profit achievable with *at
most* `K` transactions. ∎



##### Theorem  
The algorithm outputs the maximum total profit that can be obtained by
performing at most `K` normal or short transactions while
always holding at most one unit of stock.

**Proof.**

From Lemma&nbsp;5 the value returned by the algorithm is the optimal
profit.  Therefore the algorithm is correct. ∎



--------------------------------------------------------------------

#### 4.   Complexity Analysis

```
Let N = |A| ,  K ≤ 100
```

The outer loop runs `N` times, the inner loop runs `K` times.

```
Time   :  O(N · K)   ≤ 5·10⁴ · 100  = 5·10⁶  operations
Memory :  O(K)       ≤ 100  64‑bit integers
```

Both limits are well below the problem constraints.

--------------------------------------------------------------------

#### 5.   Reference Implementation  (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maximumProfit(vector<int>& A, int K) {
        int N = (int)A.size();
        if (K == 0 || N == 0) return 0;

        const long long INF_NEG = -(1LL << 60);        // large negative number

        vector<long long> res(K + 1, 0);               // res[0] = 0
        vector<long long> bought(K, INF_NEG);         // bought[t] -> transaction t+1
        vector<long long> sold(K, INF_NEG);           // sold[t]   -> transaction t+1

        for (int a : A) {            // scan days from left to right
            for (int j = K; j >= 1; --j) {
                // 1) finish a transaction now
                long long finishNormal = bought[j - 1] + a;   // bought[j-1] may be -INF
                long long finishShort  = sold[j - 1] - a;     // sold[j-1]  may be -INF
                res[j] = max(res[j], max(finishNormal, finishShort));

                // 2) start a normal transaction on this day
                bought[j - 1] = max(bought[j - 1], res[j - 1] - a);

                // 3) start a short transaction on this day
                sold[j - 1] = max(sold[j - 1], res[j - 1] + a);
            }
        }
        return res[K];
    }
};
```

The program follows exactly the algorithm proven correct above and
conforms to the time and memory limits of the problem.