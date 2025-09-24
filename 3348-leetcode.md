---
title: LeetCode 3348. Smallest Divisible Digit Product II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For a number

```
x = d1 d2 … dm          (  di  – decimal digits )
```

its *product* is the product of all its decimal digits

```
product(x) = d1 · d2 · … · dm .
```

For a test case we are given

* a required product `t`   ( `t ≥ 2` )
* a maximum length `n`    ( `1 ≤ n` )
* a lower bound `k`       ( `k ≥ 1` )

We have to find a number

```
x   ( 1 ≤ length(x) ≤ n ,  product(x) = t )
```

with the smallest possible decimal value.  
If no such number exists the answer is **NO SOLUTION**.



--------------------------------------------------------------------

#### 1.  Observations

* The product of a number depends only on its digits.
* The prime factorisation of a decimal digit contains only the primes  
  `2 , 3 , 5 , 7`.  
  All other prime factors do not appear in any decimal digit, consequently
  they can never be part of a product of decimal digits.  
  Therefore

  ```
  if t contains a prime factor other than 2,3,5,7
      →   no number with product t exists at all
  ```

* For the primes `2 , 3 , 5 , 7` the following table shows the
  *most efficient* digit – i.e. the digit that uses the least amount of
  digits for a given amount of prime factors

  | prime factor | possible digit | number of digits used |
  |--------------|----------------|-----------------------|
  | 2³           | 8              | 1                     |
  | 2²           | 4              | 1                     |
  | 2 · 3        | 6              | 1                     |
  | 3²           | 9              | 1                     |
  | 2            | 2              | 1                     |
  | 3            | 3              | 1                     |
  | 5            | 5              | 1                     |
  | 7            | 7              | 1                     |

  Using the digits from this table the number of digits that
  are necessary to realise a given multiset of prime factors is
  minimised.  
  After that, for a minimal numeric value the digits must be sorted
  in *ascending* order.

--------------------------------------------------------------------

#### 2.  Algorithm

For each test case

```
1.  Read k, n, t.
2.  Factorise t into the exponents of 2,3,5,7.
    if t contains another prime factor →   NO SOLUTION
3.  Produce the multiset of decimal digits that realises the factor
    exponents with the smallest possible number of digits.
    (greedy, using the table from section 1)
4.  If the amount of produced digits > n
        →   NO SOLUTION
5.  Construct the number by sorting the produced digits increasingly.
    Let it be cand.
6.  If cand < k
        →   NO SOLUTION
7.  Otherwise   output cand
```

--------------------------------------------------------------------

#### 3.  Correctness Proof  

We prove that the algorithm outputs a correct answer for every test
case.

---

##### Lemma 1  
If `t` contains a prime factor other than `2,3,5,7` then no decimal
number has product `t`.

**Proof.**  
Every decimal digit has prime factors only among `2,3,5,7`.
The product of any number of decimal digits is therefore a product of
these primes only, it never contains another prime factor. ∎



##### Lemma 2  
The multiset of digits produced in step&nbsp;3 realises the factorisation
of `t` and uses the smallest possible number of digits.

**Proof.**  
Consider a fixed amount of prime factors of each type.
The table from section&nbsp;1 lists, for each prime factor, a digit that
needs the smallest number of decimal digits to realise that factor.
Consequently, using the digits from the table
removes prime factors from the factorisation of `t`
exactly as fast as possible.  
Therefore after the greedy replacement step the number of produced
digits is minimal among all digit multisets that realise the factor
exponents of `t`. ∎



##### Lemma 2  
Among all decimal numbers whose digit multiset is the one obtained in
step&nbsp;3 the smallest numeric value is the one obtained by sorting
the digits increasingly.

**Proof.**  
Let `S` be the multiset of digits that realises `t`.  
Any decimal number that uses exactly the multiset `S` consists of the
digits of `S` in some order.
Sorting `S` in ascending order yields a number that is lexicographically
not larger than any other ordering of the same multiset, consequently
it is numerically minimal. ∎



##### Lemma 3  
If the algorithm outputs a number `cand` in step&nbsp;7, then
`cand` satisfies all constraints of the problem.

**Proof.**  

* By construction of the digits (step&nbsp;3) the product of the
  digits equals `t`, hence `product(cand)=t`.
* The number of produced digits never exceeds `n` (step&nbsp;4),
  therefore `length(cand) ≤ n`.
* In step&nbsp;6 we checked `cand ≥ k`.  
  Thus `cand` is a number of length at most `n` and product `t`
  that is not smaller than the required lower bound `k`. ∎



##### Lemma 4  
If the algorithm outputs **NO SOLUTION** then no valid number exists.

**Proof.**  
The algorithm can output **NO SOLUTION** in four different
situations:

1.  A prime factor other than `2,3,5,7` was found.  
    By Lemma&nbsp;1 no number with product `t` exists.
2.  The amount of produced digits exceeds `n`.  
    Any number with product `t` must contain at least the digits of the
    multiset produced in step&nbsp;3, therefore it contains at least
    that many digits, hence longer than `n`.
3.  The constructed candidate `cand` is smaller than `k`.  
    All numbers that realise the same multiset of digits are
    permutations of `cand`; the smallest permutation is `cand`
    itself (Lemma&nbsp;2).  
    Therefore every number with product `t` and at most `n` digits
    is smaller than `k`.  
4.  The multiset contains a prime factor other than `2,3,5,7`.  
    As in 1. the product cannot be achieved.

In each case no number can fulfil the constraints. ∎



##### Lemma 5  
If the algorithm outputs a number `cand` in step&nbsp;7, then
`cand` is the smallest decimal number with product `t` and length
at most `n` that is not smaller than `k`.

**Proof.**  
All decimal numbers with product `t` and at most `n` digits must use
a multiset of digits that realises the factorisation of `t`.  
By Lemma&nbsp;2 the minimal number of digits that any such multiset can
contain is the one produced in step&nbsp;3.  
Consequently every other admissible number has at least as many digits
as `cand`.  
If it has more digits, it is larger than any number with the same
number of digits because its leading digit is at least `1` and the
additional digits add value.  
If it has the same number of digits, then it must be a different
permutation of the same multiset.  
Among all permutations of a fixed multiset the numerically smallest one
is obtained by sorting the digits increasingly (Lemma&nbsp;2).  
Thus `cand` is not larger than any other admissible number.

Finally, in step&nbsp;6 we discarded all numbers smaller than `k`.  
Therefore the remaining number `cand` is the smallest admissible number
that is at least `k`. ∎



##### Theorem  
For every test case the algorithm prints the required number if it
exists, otherwise it prints **NO SOLUTION**.

**Proof.**  
If the algorithm prints a number, Lemma&nbsp;5 guarantees that this
number satisfies all constraints and is the smallest possible one.
If it prints **NO SOLUTION**, Lemma&nbsp;4 shows that no admissible
number exists.  Hence the algorithm is correct. ∎



--------------------------------------------------------------------

#### 4.  Complexity Analysis

Let `P = sqrt(t)`.

*Factorisation* – at most `O(P)` divisions.  
All other operations are linear in the amount of digits that are
produced, i.e. at most `O(n)`.

```
Time   :  O( √t + n )   per test case
Memory :  O( n )
```

--------------------------------------------------------------------

#### 4.  Reference Implementation  (Python 3)

```python
import sys
import math

# ------------------------------------------------------------
#  helper: factorise t into exponents of 2,3,5,7
# ------------------------------------------------------------
def factorize(t):
    cnt = {2:0, 3:0, 5:0, 7:0}
    for p in (2,3,5,7):
        while t % p == 0:
            cnt[p] += 1
            t //= p
    # if something is left → a prime factor outside 2,3,5,7
    return cnt, t

# ------------------------------------------------------------
#  produce the digit multiset with the smallest number of digits
# ------------------------------------------------------------
def produce_digits(cnt):
    res = []
    # use the efficient digits first
    # 2^3
    while cnt[2] >= 3:
        res.append('8')
        cnt[2] -= 3
    # 3^2
    while cnt[3] >= 2:
        res.append('9')
        cnt[3] -= 2
    # 2 * 3
    while cnt[2] >= 1 and cnt[3] >= 1:
        res.append('6')
        cnt[2] -= 1
        cnt[3] -= 1
    # 2^2
    while cnt[2] >= 2:
        res.append('4')
        cnt[2] -= 2
    # remaining 2 and 3
    while cnt[2] >= 1:
        res.append('2')
        cnt[2] -= 1
    while cnt[3] >= 1:
        res.append('3')
        cnt[3] -= 1
    # 5 and 7
    while cnt[5] >= 1:
        res.append('5')
        cnt[5] -= 1
    while cnt[7] >= 1:
        res.append('7')
        cnt[7] -= 1
    return res

# ------------------------------------------------------------
#  main solver
# ------------------------------------------------------------
def solve():
    it = iter(sys.stdin.read().strip().split())
    out_lines = []
    try:
        while True:
            k = int(next(it))
            n = int(next(it))
            t = int(next(it))

            # factorise t
            cnt, rest = factorize(t)
            if rest != 1:
                out_lines.append("NO SOLUTION")
                continue

            digits = produce_digits(cnt)
            if len(digits) > n:
                out_lines.append("NO SOLUTION")
                continue

            cand = ''.join(sorted(digits))
            if int(cand) < k:
                out_lines.append("NO SOLUTION")
            else:
                out_lines.append(cand)
    except StopIteration:
        pass

    sys.stdout.write("\n".join(out_lines))

if __name__ == "__main__":
    solve()
```

The program follows exactly the algorithm proven correct above.