---
title: LeetCode 3416. Subsequences with a Unique Middle Mode II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For a subarray `a[l … r]` (contiguous segment, 0‑based indices) its sum is

```
sum(l, r) = prefix[r+1] – prefix[l]
```

where `prefix[i]` is the sum of the first `i` elements (`prefix[0] = 0`).

The sum is divisible by `m` iff

```
prefix[r+1] % m == prefix[l] % m
```

So for every remainder `x (0 … m-1)` all indices with this remainder form
valid pairs `(l, r+1)`.  
If the remainder `x` occurs `cnt[x]` times, the number of pairs of indices is

```
C(cnt[x], 2) = cnt[x] * (cnt[x] – 1) / 2
```

These pairs correspond to all subarrays whose sum is a multiple of `m`
**including** subarrays of length `1`.

A subarray of length `1` is exactly an element `a[i]` itself.
It is counted above iff `a[i] % m == 0`.  
We have to exclude those.

--------------------------------------------------------------------

#### Algorithm
```
read n, m and array a[0 … n-1]
if n < 2:  output 0

cnt = array of length m, all zeros
rem = 0
cnt[0] = 1                      // prefix[0] % m

for each element x in a:
    rem = (rem + x) % m
    cnt[rem] += 1

total_pairs = 0
for each remainder r:
    c = cnt[r]
    total_pairs += c * (c-1) // 2

single_divisible = number of elements x in a with x % m == 0

answer = total_pairs - single_divisible
output answer
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm outputs the number of subarrays of length at
least `2` whose sum is divisible by `m`.

---

##### Lemma 1  
For every subarray `a[l … r]` (length ≥ 1) its sum is divisible by `m`
iff `prefix[r+1] % m == prefix[l] % m`.

**Proof.**

```
sum(l, r) = prefix[r+1] – prefix[l]
```

`sum(l, r)` is divisible by `m`  
⇔ `prefix[r+1] – prefix[l]` is divisible by `m`  
⇔ `prefix[r+1] % m == prefix[l] % m`. ∎



##### Lemma 2  
For each remainder `x`, the number of pairs `(l, r+1)` with
`prefix[l] % m = prefix[r+1] % m = x` equals `C(cnt[x], 2)`.

**Proof.**

All indices `i` with `prefix[i] % m = x` are distinct and unordered.
Any two distinct indices form exactly one pair `(l, r+1)` with the required
property.  
The number of unordered pairs of `cnt[x]` items is the binomial coefficient
`C(cnt[x], 2)`. ∎



##### Lemma 3  
`total_pairs` computed by the algorithm equals the number of all
subarrays (length ≥ 1) whose sum is divisible by `m`.

**Proof.**

By Lemma&nbsp;1 a subarray is valid iff its endpoints in the prefix array
have the same remainder.  
By Lemma&nbsp;2 all such pairs for a fixed remainder `x` are `C(cnt[x], 2)`.  
Summation over all remainders gives the total number of valid subarrays. ∎



##### Lemma 4  
`single_divisible` equals the number of subarrays of length `1`
whose sum is divisible by `m`.

**Proof.**

A subarray of length `1` is a single element `a[i]`.  
Its sum equals `a[i]`, which is divisible by `m` iff `a[i] % m == 0`.  
The algorithm counts all such elements in `single_divisible`. ∎



##### Lemma 5  
`answer = total_pairs – single_divisible` equals the number of
subarrays of length at least `2` whose sum is divisible by `m`.

**Proof.**

`total_pairs` counts all valid subarrays of length ≥ 1
(Lemma&nbsp;3).  
Among them exactly `single_divisible` subarrays have length `1`
(Lemma&nbsp;4).  
Subtracting removes all length‑1 subarrays and leaves precisely the
valid subarrays of length ≥ 2. ∎



##### Theorem  
The algorithm outputs the correct answer.

**Proof.**

If `n < 2` the correct answer is `0`, which the algorithm outputs.  
Otherwise, by Lemma&nbsp;5 the computed `answer` is exactly the number of
subarrays of length at least `2` with a sum divisible by `m`. ∎



--------------------------------------------------------------------

#### Complexity Analysis  

`cnt` is built in one linear pass over the array.  
All other operations are linear in `m` (≤ 200 000).  
Hence

```
Time   :  O(n + m)   ≤ 4·10^5 operations
Memory :  O(m)       ≤ 200 001 integers
```

Both bounds easily satisfy the limits.



--------------------------------------------------------------------

#### Reference Implementation (Python 3)

```python
import sys

def solve() -> None:
    data = sys.stdin.buffer.read().split()
    if not data:
        return
    it = iter(data)
    n = int(next(it))
    m = int(next(it))
    a = [int(next(it)) for _ in range(n)]

    if n < 2:
        print(0)
        return

    cnt = [0] * m
    rem = 0
    cnt[0] = 1                     # prefix[0] % m

    for x in a:
        rem = (rem + x) % m
        cnt[rem] += 1

    total_pairs = 0
    for c in cnt:
        total_pairs += c * (c - 1) // 2

    single_divisible = 0
    for x in a:
        if x % m == 0:
            single_divisible += 1

    answer = total_pairs - single_divisible
    print(answer)

if __name__ == "__main__":
    solve()
```