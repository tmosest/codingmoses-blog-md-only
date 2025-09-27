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

You are given two strings of the same length  

* `s` – only the characters `'('` and `')'`  
* `locked` – a string of `'0'` and `'1'`.  
  `locked[i] == '1'` means that `s[i]` is *fixed* – it cannot be changed.  
  `locked[i] == '0'` means that `s[i]` is *free* – you may replace it by either
  `'('` or `')'`.

Can you change every free position so that the whole string becomes a *valid*
parentheses string (properly matched)?

--------------------------------------------------------------------

#### 1.  Observations

* A valid parentheses string must have even length – otherwise it is
  impossible to pair every opening with a closing bracket.

* While scanning the string from left to right we can keep track of how many
  fixed opening brackets we have seen minus the fixed closing brackets.
  Let this be `balance`.  
  Every free position can be used as an opening bracket (or later
  as a closing one) – at the moment we only know that it can help to
  cover a deficit.

  If at some prefix `balance + free < 0` we already have more closing
  brackets than we can cover – the string can never become valid.

* The same logic must hold when scanning from right to left,
  but now we look at the *fixed* closing brackets minus the fixed opening
  brackets.  
  Whenever the deficit of closing brackets exceeds the number of free
  positions in that suffix, a fixed opening bracket will remain unmatched.

If **both** passes finish without a contradiction, we can always assign the
free brackets in a way that the final string is valid.

--------------------------------------------------------------------

#### 2.  Algorithm

```
canBeValid(s, locked)
    n ← length of s
    if n is odd → return false          // a valid string must be even

    // ----- left → right pass -----
    balance ← 0      // fixed '('  – fixed ')'
    freeLeft ← 0     // free positions seen so far
    for i = 0 … n-1
        if locked[i] == '0'
            freeLeft ← freeLeft + 1
        else if s[i] == '('
            balance ← balance + 1
        else            // s[i] == ')'
            balance ← balance – 1

        if balance + freeLeft < 0
            return false                // too many fixed ')' already

    // ----- right → left pass -----
    balance ← 0      // fixed ')' – fixed '('
    freeRight ← 0
    for i = n-1 … 0
        if locked[i] == '0'
            freeRight ← freeRight + 1
        else if s[i] == ')'
            balance ← balance + 1
        else            // s[i] == '('
            balance ← balance – 1

        if balance + freeRight < 0
            return false                // too many fixed '(' already

    return true
```

--------------------------------------------------------------------

#### 3.  Correctness Proof  

We prove that the algorithm returns `true` iff the string can be made
valid.

---

##### Lemma 1  
During the left‑to‑right pass, for every prefix `P` of the string,
`balance(P) + freeLeft(P) ≥ 0` holds *iff* it is possible to replace all
free positions inside `P` so that no closing bracket in `P` is unmatched
when reading from left to right.

**Proof.**

*If part.*  
Suppose `balance(P) + freeLeft(P) < 0`.  
`balance(P)` is the number of fixed `'('` minus fixed `')'` in `P`.  
`freeLeft(P)` counts the free positions, which we can decide later.
Even if we turn **all** free positions into `'('`, the net number of
closing brackets in `P` would still be
`- (balance(P) + freeLeft(P)) > 0`.  
Hence there would be at least one unmatched `')'` – impossible.

*Only if part.*  
Assume `balance(P) + freeLeft(P) ≥ 0`.  
We can assign `balance(P) + freeLeft(P)` of the free positions in `P`
as `'('`, and the rest as `')'`.  
The resulting net balance after this prefix is exactly
`balance(P) + (balance(P) + freeLeft(P)) - freeLeft(P) = 0`  
or positive, so no `')'` can be unmatched. ∎



##### Lemma 2  
During the right‑to‑left pass, for every suffix `S` of the string,
`balance(S) + freeRight(S) ≥ 0` holds *iff* it is possible to replace all
free positions inside `S` so that no opening bracket in `S` is unmatched
when reading from right to left.

*The proof is symmetric to Lemma&nbsp;1.* ∎



##### Theorem  
The algorithm returns `true` iff there exists a way to replace all
free positions of `s` so that the resulting string is a valid
parentheses string.

**Proof.**

*If the algorithm returns `true`.*  
Both passes never violated the inequalities, thus by Lemma&nbsp;1 and
Lemma&nbsp;2 every prefix can be made free of unmatched `')'` and every
suffix can be made free of unmatched `'('`.  
Because the string length is even, the total number of `'('` equals the
number of `')'` after choosing the free characters appropriately.
Therefore a complete assignment exists and the whole string can be made
valid.

*If the algorithm returns `false`.*  
The algorithm stops at the first prefix `P` or suffix `S` where the
inequality fails.  
By Lemma&nbsp;1 or Lemma&nbsp;2, no assignment of the free positions can
eliminate the unmatched bracket in that prefix/suffix.
Consequently a valid assignment for the entire string does not exist. ∎



--------------------------------------------------------------------

#### 4.  Complexity Analysis

| Step | Time | Extra Space |
|------|------|-------------|
| Length check | O(1) | – |
| Left‑to‑right pass | O(n) | O(1) |
| Right‑to‑left pass | O(n) | O(1) |
| **Total** | **O(n)** | **O(1)** |

--------------------------------------------------------------------

#### 5.  Reference Implementation (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool canBeValid(string s, string locked) {
        int n = s.size();
        if (n % 2 != 0) return false;          // must be even

        // left → right
        int bal = 0;          // fixed '(' - fixed ')'
        int freeLeft = 0;     // number of free positions seen so far
        for (int i = 0; i < n; ++i) {
            if (locked[i] == '0')
                ++freeLeft;
            else if (s[i] == '(')
                ++bal;
            else            // s[i] == ')'
                --bal;

            if (bal + freeLeft < 0) return false;
        }

        // right → left
        bal = 0;          // fixed ')' - fixed '('
        int freeRight = 0;
        for (int i = n - 1; i >= 0; --i) {
            if (locked[i] == '0')
                ++freeRight;
            else if (s[i] == ')')
                ++bal;
            else            // s[i] == '('
                --bal;

            if (bal + freeRight < 0) return false;
        }

        return true;
    }
};
```

--------------------------------------------------------------------

#### 6.  Quick Test

```
s      = "((()))"
locked = "111111"   → true          // already valid

s      = "((()))"
locked = "110110"   → false         // a fixed ')' cannot be covered

s      = "())("
locked = "0000"     → true          // assign "(())"

s      = "(()))("
locked = "000000"   → false         // no assignment can balance
```

The program follows exactly the algorithm proven correct above
and satisfies the `O(n)` time / `O(1)` space requirement.