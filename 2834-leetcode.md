---
title: LeetCode 2834. Find the Minimum Possible Sum of a Beautiful Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every number `x` the pair that gives the sum `target` is  
`(x , target‑x)`.

```
1 + (target-1)
2 + (target-2)
…
⌊target/2⌋ + (target-⌊target/2⌋)
```

If we already used all numbers `1 … ⌊target/2⌋` we cannot use any
number in the range `⌊target/2⌋+1 … target-1` – every one of them has
a partner that is already taken and together would sum to `target`.

Therefore

* All numbers `< target` that we can use are exactly `1 … ⌊target/2⌋`.  
  They form an arithmetic sequence.
* Every number `≥ target` is safe – the sum with any positive integer
  is larger than `target`.  
  They form the second arithmetic sequence starting at `target`.

The goal is to pick the smallest `n` numbers that satisfy the above
restriction, i.e. take all possible small numbers first and then
continue from `target` if more numbers are still needed.

The two sequences are

```
low  : 1 … half   (half = target/2)
high : target … target+remaining-1
```

where

```
remaining = n - min(n, half)
```

Both sums can be computed by the well‑known formula

```
sum(1 … k) = k*(k+1)/2
```

and the sum of a general range `[a … b]` is

```
sum(1 … b) – sum(1 … a-1)
```

All calculations are performed modulo `MOD = 1 000 000 007`.

--------------------------------------------------------------------

#### Algorithm
```
half       = target / 2
lowCount   = min(n, half)            // how many numbers from the first part
lowSum     = lowCount*(lowCount+1)/2            (mod MOD)

remaining  = n - lowCount
if remaining > 0
    highStart = target
    highEnd   = target + remaining - 1
    highSum   = (highEnd*(highEnd+1)/2
                - (highStart-1)*highStart/2)   (mod MOD)
else
    highSum = 0

answer = (lowSum + highSum) mod MOD
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm returns the minimum possible sum of an
anti‑two‑sum array of length `n`.

---

##### Lemma 1  
If a number `x` satisfies `x < target` and `x ≤ target/2`, then
any number `y` with `target/2 < y < target` has the property
`x + y = target` and therefore cannot be chosen together with `x`.

**Proof.**

`y` is in the interval `(target/2 , target)` which implies `target-y`
belongs to `(0 , target/2]`.  
Because `x ≤ target/2`, we have `target-y ≥ target-x ≥ 0`.  
Thus `x + y = target`.  ∎



##### Lemma 2  
Any integer `z ≥ target` can be chosen together with all numbers
`1 … target-1` (i.e. with any number smaller than `target`).

**Proof.**

For every `x < target` we have `x + z ≥ target + 1 > target`.  
Therefore the sum can never equal `target`.  ∎



##### Lemma 3  
The smallest possible anti‑two‑sum set of size `n` consists of

* the first `k = min(n, ⌊target/2⌋)` numbers `1 … k`
* if `n > k`, the next `n-k` consecutive numbers starting from `target`

**Proof.**

From Lemma&nbsp;1 the set `{1,…,⌊target/2⌋}` contains the smallest
possible elements.  
No element in `(⌊target/2⌋+1 , target-1)` can be added,
otherwise it would pair with a chosen element to sum to `target`.  
Hence every element of the final set must be either in the first part
or at least `target`.  
Lemma&nbsp;2 shows that all integers `≥ target` are valid.  
Taking the smallest available numbers yields the global minimum sum,
and this is exactly the described construction. ∎



##### Lemma 4  
`lowSum` computed by the algorithm equals the sum of the first `k`
integers (`k = min(n, ⌊target/2⌋)`).

**Proof.**

By the arithmetic series formula

```
sum(1 … k) = k(k+1)/2
```
The algorithm applies this formula modulo `MOD`. ∎



##### Lemma 5  
`highSum` computed by the algorithm equals the sum of the consecutive
integers `target … target+remaining-1`, where `remaining = n-k`.

**Proof.**

The sum of a range `[a … b]` equals

```
sum(1 … b) – sum(1 … a-1)
          = b(b+1)/2 – (a-1)a/2
```

The algorithm computes exactly this expression (modulo `MOD`).
Thus `highSum` is the correct sum of the required numbers. ∎



##### Theorem  
The algorithm returns the minimum possible sum of an array of length
`n` that avoids any pair of elements summing to `target`.

**Proof.**

Let `S` be the set chosen by the algorithm.  
By Lemma&nbsp;3 `S` is exactly the optimal set of `n` smallest
elements respecting the constraint.  
`lowSum` and `highSum` are, by Lemma&nbsp;4 and Lemma&nbsp;5, the
sums of the two parts of `S`.  
Therefore the algorithm outputs `sum(S)` (modulo `MOD`), which is the
required minimum sum. ∎



--------------------------------------------------------------------

#### Complexity Analysis

All operations are simple arithmetic; no loops or recursion are used.

```
Time   :  O(1)
Memory :  O(1)
```

--------------------------------------------------------------------

#### Reference Implementation  (Java 17)

```java
import java.io.*;

public class Main {
    static final int MOD = 1_000_000_007;

    public static int minimumPossibleSum(int n, int target) {
        int half = target / 2;
        int lowCount = Math.min(n, half);                // numbers taken from 1 … half

        // sum of the first lowCount positive integers
        long lowSum = ((long) lowCount * (lowCount + 1) / 2) % MOD;

        int remaining = n - lowCount;                    // numbers still needed
        long highSum = 0;
        if (remaining > 0) {
            long start = target;                         // first number from the high side
            long end   = target + remaining - 1;         // last number needed

            // sum of 1 … end
            long sumEnd = end * (end + 1) / 2;
            // sum of 1 … (start-1)
            long sumStartMinus1 = (start - 1) * start / 2;

            highSum = (sumEnd % MOD - sumStartMinus1 % MOD + MOD) % MOD;
        }

        return (int) ((lowSum + highSum) % MOD);
    }

    /* ------------------------------------------------------------ */

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int n = Integer.parseInt(st.nextToken());
        int target = Integer.parseInt(st.nextToken());
        System.out.println(minimumPossibleSum(n, target));
    }
}
```

The program follows exactly the algorithm proven correct above and
conforms to Java 17.