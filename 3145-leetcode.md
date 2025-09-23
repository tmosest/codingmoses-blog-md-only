---
title: LeetCode 3145. Find Products of Elements of Big Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For a query `l , r , m` we have to output  

```
big_nums[l] · big_nums[l+1] · … · big_nums[r]  (mod m)
```

`big_nums` is the list of all natural numbers written in increasing order

```
big_nums[0] = 1,  big_nums[1] = 2,  big_nums[2] = 3, …
big_nums[i] = i + 1
```

so the required product is the product of all integers from `l+1` to `r+1`
(inclusive) taken modulo `m`.

--------------------------------------------------------------------

#### 1.  Observations

* `m` is at most `10^5`.  
  In any sequence of `m` consecutive integers there is a multiple of `m`
  (the Pigeon‑hole principle).  
  Hence if the interval length `r – l + 1` is **≥ m** the product is a
  multiple of `m` and the answer is `0`.

* When the interval length is smaller than `m` the product contains at
  most `m‑1 (≤ 10^5)` factors.  
  We can compute the product directly, each factor modulo `m`, because
  the loop size is at most `10^5`.

--------------------------------------------------------------------

#### 2.  Algorithm
For every test case

```
len = r - l + 1
if len >= m          → answer = 0
else
    prod = 1 (mod m)
    for i from 0 to len-1
        prod = prod * ((l + 1 + i) mod m)  (mod m)
    answer = prod
output answer
```

--------------------------------------------------------------------

#### 3.  Correctness Proof  

We prove that the algorithm outputs the correct value for each query.

---

##### Lemma 1  
If `len = r – l + 1 ≥ m` then  
`big_nums[l] · … · big_nums[r]` is divisible by `m`.

**Proof.**  
`big_nums[i] = i + 1`.  
The set `{l+1, l+2, …, r+1}` contains `m` consecutive integers.
Among any `m` consecutive integers there is exactly one that is a multiple
of `m`.  
Therefore the product contains this multiple and is a multiple of `m`,
hence congruent to `0` modulo `m`. ∎



##### Lemma 2  
If `len < m` the algorithm’s loop computes

```
prod = ( (l+1) · (l+2) · … · (r+1) )  (mod m)
```

**Proof.**  
The loop iterates over `i = 0 … len-1`.  
In the `i`‑th iteration it multiplies the current product by
`(l+1+i) mod m`.  
All operations are performed modulo `m`, so after the last iteration
`prod` equals the product of all numbers from `l+1` to `r+1` taken modulo
`m`. ∎



##### Theorem  
For every query `l , r , m` the algorithm outputs

```
big_nums[l] · big_nums[l+1] · … · big_nums[r]  (mod m)
```

**Proof.**  

*If `len ≥ m`*:  
By Lemma&nbsp;1 the product is a multiple of `m`; the algorithm outputs
`0`, which equals the desired value modulo `m`.

*If `len < m`*:  
By Lemma&nbsp;2 the loop calculates exactly the product of all factors
from `l+1` to `r+1` modulo `m`.  
This is the required value. ∎



--------------------------------------------------------------------

#### 4.  Complexity Analysis

For one query

* If `len ≥ m` the answer is computed in `O(1)`.
* Otherwise at most `m-1 ≤ 10^5` multiplications are performed,  
  each `O(1)`.

Hence the time per query is `O(min(len, m)) ≤ O(10^5)` and the memory
consumption is `O(1)`.

--------------------------------------------------------------------

#### 5.  Reference Implementation (Python 3)

```python
import sys

def solve() -> None:
    data = list(map(int, sys.stdin.read().split()))
    t = data[0]
    out = []
    p = 1
    for _ in range(t):
        l, r, m = data[p:p+3]
        p += 3
        if m == 1:                 # everything is 0 modulo 1
            out.append(0)
            continue

        length = r - l + 1
        if length >= m:            # a multiple of m occurs
            out.append(0)
            continue

        prod = 1 % m
        start = l + 1
        for i in range(length):
            prod = (prod * ((start + i) % m)) % m
        out.append(prod)

    sys.stdout.write("\n".join(map(str, out)))

if __name__ == "__main__":
    solve()
```

The program follows exactly the algorithm proven correct above and
conforms to the required input / output format.