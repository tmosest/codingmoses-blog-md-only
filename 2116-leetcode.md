---
title: LeetCode 2116. Check if a Parentheses String Can Be Valid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem**  
Given two strings `s` and `locked` of equal length `n`

* `s[i]` is a parenthesis (`'('` or `')'`)  
* `locked[i]` is `'1'` if the character is *fixed* (cannot be changed) and `'0'` if it can be changed to either parenthesis

We may change every character with `locked[i] == '0'`.  
Can we obtain a *valid* parentheses string?

A string is valid iff

* the number of opening and closing brackets is equal
* while scanning from left to right no prefix contains more `')'` than `'('` (after we are allowed to replace some `')'` with `'('`)

The same property must hold for the reversed string (otherwise we would have an unmatched `'('` at the end).

--------------------------------------------------------------------

#### 1.  Observations

* A valid string must have an **even** length – otherwise we can never pair the brackets.  
  If `n` is odd → return `false`.

* While scanning from left to right we can keep two counters:

  * `balance` – number of **fixed** `'('` minus number of fixed `')'`.
  * `free` – number of **unfixed** positions we have already seen (`locked[i] == '0'`).

  The string can still be made valid up to the current position iff  
  `balance + free >= 0`.  
  If this becomes negative we already have an unmatched `')'` that
  cannot be compensated → return `false`.

* A completely symmetric argument works for the right‑to‑left scan:
  we count the fixed brackets in the reversed order and allow free
  positions to become `'('`.  
  If at any step `balance + free` is negative, we would have an
  unmatched `'('`.

* If both scans finish without failure, a valid arrangement exists.

--------------------------------------------------------------------

#### 2.  Algorithm

```
if n is odd: return false

balance = 0          // fixed '(' minus fixed ')'
free    = 0          // number of free positions seen so far

for i = 0 .. n-1
    // left‑to‑right
    if locked[i] == '0':   free++
    else if s[i] == '(':   balance++
    else:                  balance--      // s[i] == ')'
    if balance + free < 0: return false

    // right‑to‑left
    j = n-1-i
    if locked[j] == '0':   free++
    else if s[j] == ')':   balance++
    else:                  balance--      // s[j] == '('
    if balance + free < 0: return false

return true
```

--------------------------------------------------------------------

#### 3.  Correctness Proof  

We prove that the algorithm returns `true` **iff** a valid arrangement
exists.

---

##### Lemma 1  
During the left‑to‑right scan, after processing the first `k`
characters (`0 ≤ k ≤ n`), `balance + free` equals the maximum number of
unmatched `'('` that could still be satisfied by the remaining
characters.

**Proof.**

* `balance` counts the fixed parentheses among the first `k` characters:
  +1 for each `'('`, –1 for each `')'`.  
  It is exactly the number of unmatched fixed `'('` after the first `k`
  characters.

* `free` is the number of positions among the first `k` that we can
  freely choose.  Each such position can be turned into `'('` if we
  wish, increasing the available unmatched `'('` by 1.

So `balance + free` is the maximum number of unmatched `'('` that can
be created by optimally assigning the free positions. ∎



##### Lemma 2  
If during the left‑to‑right scan `balance + free < 0`, then no valid
arrangement exists.

**Proof.**

`balance + free < 0` means that even if all free positions among the
first `k` characters were turned into `')'`, the number of unmatched
fixed `')'` would still be larger than the number of unmatched fixed
`'('`.  This is impossible in any correct parentheses string because a
prefix can never contain more `')'` than `'('`. ∎



##### Lemma 3  
The right‑to‑left scan is equivalent to the left‑to‑right scan applied
to the reversed string, therefore it guarantees that the suffix
cannot contain more `'('` than `')'`.

**Proof.**

Reversing the string swaps the roles of opening and closing
parentheses.  The same counting procedure applied to the reversed
string ensures that no suffix contains an unmatched `'('`.  
Hence the second pass guarantees the other necessary condition. ∎



##### Theorem  
The algorithm returns `true` **iff** there exists a replacement of
unlocked positions that makes the string valid.

**Proof.**

*If the algorithm returns `false`*:  
By Lemma&nbsp;2 the left‑to‑right scan shows that at some prefix we have
more unmatched `')'` than can possibly be compensated.  
No assignment of free positions can avoid this mismatch, so no valid
string exists.  
The same holds for the right‑to‑left scan (Lemma&nbsp;3).  
Therefore a valid arrangement does not exist.

*If the algorithm returns `true`*:  
Both scans finished without violating their inequalities.  
By Lemma&nbsp;1, after the whole string has been processed the total
number of free positions equals the number of unmatched brackets,
so we can assign them so that the final balance is zero.  
Thus we can construct a valid string. ∎

--------------------------------------------------------------------

#### 4.  Complexity Analysis

```
Time   :  O(n)   (two linear scans)
Memory :  O(1)   (few integer variables)
```

--------------------------------------------------------------------

#### 5.  Reference Implementation (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

bool canBeValid(string s, string locked) {
    int n = (int)s.size();
    if (n % 2) return false;          // odd length cannot be balanced

    int bal = 0, free = 0;            // for left‑to‑right scan
    for (int i = 0; i < n; ++i) {
        if (locked[i] == '0')          free++;
        else if (s[i] == '(')          bal++;
        else                           bal--;   // s[i] == ')'
        if (bal + free < 0) return false;
    }

    bal = 0; free = 0;                 // for right‑to‑left scan
    for (int i = n - 1; i >= 0; --i) {
        if (locked[i] == '0')          free++;
        else if (s[i] == ')')          bal++;
        else                           bal--;   // s[i] == '('
        if (bal + free < 0) return false;
    }

    return true;
}
```

The program follows exactly the algorithm proven correct above and
conforms to the required C++ compiler (GNU++17).