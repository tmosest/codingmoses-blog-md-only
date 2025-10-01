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

For every position `j` we need two numbers

* `L[j]` – the maximum profit of a valid *earlier* position  
  (`i < j` and `price[i] < price[j]`)

* `R[j]` – the maximum profit of a valid *later* position  
  (`k > j` and `price[k] > price[j]`)

If both `L[j]` and `R[j]` are known, the best triple that ends at `j`
is

```
best(j) = L[j] + profit[j] + R[j]
```

The answer is the maximum of all `best(j)` that exist
(when no triple exists the answer is `0`).

--------------------------------------------------------------------

#### 1.   Pre–processing – coordinate compression

The prices can be as large as 10^9, but we only need their relative
order.  
Let

```
values = sorted(set(price[0 … n‑1]))
rank[price] = index in values   (1‑based)
```

`rank[x]` is in `[1 … m]` where `m` is the number of distinct prices
(`m ≤ n`).  
After compression the “price <” / “price >” condition becomes
`rank[i] < rank[j]` / `rank[i] > rank[j]`, i.e. a normal range on
indices `1 … m`.

--------------------------------------------------------------------

#### 2.   Fenwick tree for range maximum

A Fenwick tree (Binary Indexed Tree) is normally used for prefix sums,
but it can also store a prefix *maximum* if we replace the “add” by
“take the larger of the two values”.

```
update(idx, val)      // val will replace the old value if it is larger
query(idx)            // maximum on prefix [1 … idx]
```

All tree values are initialised to **negative infinity** because
profits may be negative.

--------------------------------------------------------------------

#### 3.   Compute `L[j]`  (left side)

```
bit = array[1 … m] initialised to -INF
for j = 0 … n-1
        r = rank[price[j]]
        L[j] = query(r-1)          // maximum profit with smaller price
        update(r, profit[j])
```

`query(r-1)` looks only at ranks `< r`, i.e. prices `< price[j]`.

--------------------------------------------------------------------

#### 4.   Compute `R[j]`  (right side)

We use a *reverse* traversal.
For “later price > current price” we need a suffix query.
A Fenwick tree can give a suffix query if we map the rank
`r` to a new index `rev = m - r + 1` :

```
rev(r) = m - r + 1          // now  price >  ⇔  rev > rev_current
```

```
bit = array[1 … m] initialised to -INF
for j = n-1 … 0
        r = rank[price[j]]
        rev = m - r + 1
        R[j] = query(rev-1)        // maximum profit with larger price
        update(rev, profit[j])
```

Now `query(rev-1)` returns the best profit among all
later positions with `price > price[j]`.

--------------------------------------------------------------------

#### 5.   Final answer

```
answer = 0
for j = 0 … n-1
        if L[j] > -INF  and  R[j] > -INF
                answer = max(answer, L[j] + profit[j] + R[j])
print(answer)
```

If no triple exists both `L[j]` or `R[j]` are `-INF` and the loop
does nothing – the answer stays `0`.

--------------------------------------------------------------------

#### 5.   Correctness Proof  

We prove that the algorithm outputs the maximum possible profit.

---

##### Lemma 1  
During the left‑to‑right scan, after processing position `j`
the Fenwick tree contains the profit of **every** position `i ≤ j`
with its compressed rank `rank[i]`, and the stored value for a rank
is the maximum profit among all positions having that rank.

**Proof.**

*Initialization* – before the first iteration all values are `-INF`;
the lemma holds trivially.

*Induction step.*  
Assume the lemma is true before processing `j`.
During the iteration we execute  
`update(rank[price[j]], profit[j])`.  
If the tree already stores a value `old` for this index, we keep the
larger one (`max(old, profit[j])`).  
All other indices keep their old value.  
Hence after the update the tree still satisfies the lemma for all
indices `≤ j`. ∎



##### Lemma 2  
For every position `j`, `L[j]` equals the maximum profit among all
valid earlier positions `i` (`i < j` and `price[i] < price[j]`).

**Proof.**

During the scan at step `j` the tree contains only positions
`i < j` (Lemma&nbsp;1).  
`query(r-1)` returns the maximum value among ranks `< r`,
i.e. among prices `< price[j]`.  
Therefore `L[j]` is exactly the maximum profit of a valid earlier
position. ∎



##### Lemma 3  
For every position `j`, `R[j]` equals the maximum profit among all
valid later positions `k` (`k > j` and `price[k] > price[j]`).

**Proof.**

The right‑to‑left scan processes the positions in reverse order.
When position `j` is processed, the tree already contains all positions
`k > j` (Lemma&nbsp;1 applied in reverse).  
`rev = m - rank[price[j]] + 1` is the compressed rank of `price[j]`
mirrored on `[1 … m]`.  
`query(rev-1)` looks only at indices `< rev`, which correspond to
original prices `> price[j]`.  
Thus `R[j]` is the maximum profit of a valid later position. ∎



##### Lemma 4  
For any valid triple `(i, j, k)` the algorithm considers the value
`profit[i] + profit[j] + profit[k]` in the loop over `j`.

**Proof.**

For the middle index `j` we have

* `i < j` and `price[i] < price[j]` – by Lemma&nbsp;2
  `L[j] ≥ profit[i]` (the maximum over all such `i`).
* `k > j` and `price[k] > price[j]` – by Lemma&nbsp;3
  `R[j] ≥ profit[k]` (the maximum over all such `k`).

Consequently

```
L[j] + profit[j] + R[j] ≥ profit[i] + profit[j] + profit[k]
```

The algorithm takes the maximum over all `j`, so the value of the
triple is considered. ∎



##### Theorem  
The algorithm outputs the maximum possible total profit of a triple
`(i, j, k)` satisfying the required constraints.
If no such triple exists the output is `0`.

**Proof.**

*Existence:*  
For every valid triple `(i, j, k)` Lemma&nbsp;4 shows that the
corresponding sum is evaluated by the algorithm.
Therefore the algorithm’s maximum is **at least** the optimum.

*Optimality:*  
For every `j` for which both `L[j]` and `R[j]` are defined, the sum
`L[j] + profit[j] + R[j]` is a feasible triple (by construction of
`L` and `R`).  
Hence the algorithm never reports a value that cannot be achieved,
so its maximum is **at most** the optimum.

Since it is both at least and at most the optimum, it equals the
optimum.  
If no triple exists all `L[j]` or `R[j]` are `-INF`, the loop does not
update `answer`, which stays `0` – exactly the required output. ∎



--------------------------------------------------------------------

#### 5.   Complexity Analysis

```
Coordinate compression      :  O(n log n)
Left scan (Fenwick updates) :  O(n log m)   ≤ O(n log n)
Right scan (Fenwick updates):  O(n log m)   ≤ O(n log n)
Memory consumption          :  O(n)  (price, profit, L, R, Fenwick tree)
```

--------------------------------------------------------------------

#### 6.   Reference Implementation  (Python 3)

```python
import sys
from bisect import bisect_left

INF_NEG = -10**30      # "negative infinity" that safely fits the limits


class FenwickMax:
    """Fenwick tree that keeps the maximum on a prefix."""
    def __init__(self, size: int):
        self.n = size
        self.bit = [INF_NEG] * (size + 1)   # 1‑based indexing

    def update(self, idx: int, val: int) -> None:
        """Set bit[idx] = max(bit[idx], val) and propagate upwards."""
        while idx <= self.n:
            if val > self.bit[idx]:
                self.bit[idx] = val
            idx += idx & -idx

    def query(self, idx: int) -> int:
        """Return maximum on prefix [1 … idx]."""
        res = INF_NEG
        while idx > 0:
            if self.bit[idx] > res:
                res = self.bit[idx]
            idx -= idx & -idx
        return res


def solve() -> None:
    data = sys.stdin.read().strip().split()
    if not data:
        return
    it = iter(data)
    n = int(next(it))
    price = [int(next(it)) for _ in range(n)]
    profit = [int(next(it)) for _ in range(n)]

    # ----- coordinate compression of prices -----
    values = sorted(set(price))
    rank = {v: i + 1 for i, v in enumerate(values)}   # 1‑based
    m = len(values)

    # ----- compute L[j] -----
    left_tree = FenwickMax(m)
    L = [INF_NEG] * n
    for j in range(n):
        r = rank[price[j]]
        L[j] = left_tree.query(r - 1)     # only smaller prices
        left_tree.update(r, profit[j])

    # ----- compute R[j] -----
    right_tree = FenwickMax(m)
    R = [INF_NEG] * n
    for j in range(n - 1, -1, -1):
        r = rank[price[j]]
        rev = m - r + 1                   # mirrored index
        R[j] = right_tree.query(rev - 1)  # only larger prices
        right_tree.update(rev, profit[j])

    # ----- combine and output -----
    answer = 0
    for j in range(n):
        if L[j] > INF_NEG and R[j] > INF_NEG:
            total = L[j] + profit[j] + R[j]
            if total > answer:
                answer = total

    print(answer)


if __name__ == "__main__":
    solve()
```

The program follows exactly the algorithm proven correct above and
conforms to the required input / output format.