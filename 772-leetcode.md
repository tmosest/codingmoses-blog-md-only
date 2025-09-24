---
title: LeetCode 772. Basic Calculator III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Overview**

We evaluate the expression in one linear scan.  
While scanning we keep

* `num`   – the number currently being read (may contain several digits)  
* `pre`   – the *last* value that already considered multiplication/division  
  (it will be added to the result only after we see a plus/minus or the end).  
* `res`   – the sum of all terms that are already finished.  
* `sign`  – the operator that will be applied to the next `num`.  

When we encounter a `(` we recursively evaluate the sub‑expression that follows
until its matching `)`.  
When we see an operator (or `)` or the end of the string) we “commit” the current
`num` to `res` / `pre` according to the previous operator.  
If the character that caused the commit is `)` we immediately return
`res + pre` – this finishes the current level of recursion.

The recursive depth is bounded by the number of nested parentheses, so the
auxiliary stack size is `O(#brackets)`.

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm returns the value of the expression `s`.

---

##### Lemma 1  
At any time during the evaluation of a level (between a matching pair of
parentheses or the whole string) `res` equals the sum of all fully finished
terms and `pre` equals the value of the *current* pending term (the one that
will be added to `res` after the next operator is processed).

**Proof.**  
Initially `res = 0` and `pre = 0`; the lemma holds.

Whenever we encounter an operator `op` (or the closing `)` or end of the string)
the algorithm executes:

```
switch (sign) {
    case '+': res += pre; pre =  num; break;
    case '-': res += pre; pre = -num; break;
    case '*': pre *= num; break;
    case '/': pre /= num; break;
}
```

`sign` is the operator that was pending before `num` was read.
- For `+` or `-` the pending term `pre` is finished and added to `res`,
  and `pre` is set to the new term (`+num` or `-num`).
- For `*` or `/` the pending term is *extended* by multiplication/division,
  so `pre` is updated but not added to `res` yet.

Thus after the switch, `res` contains all finished terms, and `pre` contains
the last unfinished term (possibly already multiplied/divided).  
If the operator is `)`, the function returns `res + pre`, which by the lemma
equals the value of the sub‑expression.  
Therefore the invariant holds inductively for every step. ∎



##### Lemma 2  
When a sub‑expression inside parentheses is evaluated recursively, its return
value equals the mathematical value of that sub‑expression.

**Proof.**  
The recursive call is entered only when a `(` is seen.  
Inside the call the same invariant (Lemma&nbsp;1) holds, because the very
same code is executed.  
When the matching `)` is processed, the function returns `res + pre`,
which by Lemma&nbsp;1 equals the value of that sub‑expression. ∎



##### Theorem  
`calculate(s)` returns the value of the arithmetic expression represented by
` s`.

**Proof.**  
The top–level call processes the whole string.
Every number is read completely (multi‑digit numbers are handled by the
`num = num*10 + digit` step).  
When the string ends, the final `num` is committed (because `idx == n`), so all
terms are added to `res`.  
By Lemma&nbsp;1 the returned value is `res + pre`, which is the value of the
entire expression.  
Lemma&nbsp;2 ensures that every parenthesised sub‑expression is evaluated
correctly.  
Thus the function returns the correct result. ∎



--------------------------------------------------------------------

#### Complexity Analysis

Let `n` be the length of the input string.

* **Time** – Each character is examined exactly once (`idx` only moves forward).  
  Therefore the running time is `Θ(n)`.

* **Space** – The auxiliary recursion stack depth equals the maximum nesting
  of parentheses, at most `n/2`.  
  Thus the auxiliary space is `O(#brackets)` ≤ `O(n)`.

--------------------------------------------------------------------

#### Reference Implementation  (Java 17)

```java
class Solution {
    // shared index – the position in the string that is currently processed
    private int idx;

    public int calculate(String s) {
        idx = 0;               // start from the beginning for every call
        return eval(s);
    }

    /**
     * Evaluates the expression represented by the string `s`
     * starting at the current value of `idx`.  It stops when the string ends
     * or when a matching ')' is encountered.  The return value is the value
     * of the processed part.
     */
    private int eval(String s) {
        int res = 0;        // accumulated sum of finished terms
        int pre = 0;        // current pending term (after *, /)
        int num = 0;        // current number being read
        char sign = '+';    // operator that will be applied to the next num

        int n = s.length();
        while (idx < n) {
            char c = s.charAt(idx++);

            if (c == '(') {               // start of a sub‑expression
                num = eval(s);            // recursively evaluate it
            } else if (c >= '0' && c <= '9') {   // number (possibly >1 digit)
                num = num * 10 + (c - '0');
            }

            /* Commit the current number when an operator, ')', or end is seen */
            if (c == '+' || c == '-' || c == '*' || c == '/' || c == ')' || idx == n) {
                switch (sign) {
                    case '+': res += pre;   pre =  num;      break;
                    case '-': res += pre;   pre = -num;      break;
                    case '*': pre *= num;                    break;
                    case '/': pre /= num;                    break;
                }

                if (c == ')') {             // finished a parenthesised sub‑expr
                    return res + pre;        // return its value to the caller
                }

                num = 0;                     // reset for the next term
                sign = c;                    // remember the pending operator
            }
        }
        return res + pre;                    // final value of the whole level
    }
}
```

The program follows exactly the algorithm proven correct above and satisfies
the constraints of LeetCode 227.