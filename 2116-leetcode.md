---
title: LeetCode 2116. Check if a Parentheses String Can Be Valid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every position of the string

```
s[i]  ∈  {'(', ')'}
locked[i] ∈ {'0','1'}        // '0' → unlocked, can be turned into '(' or ')'
```

We may change *any* unlocked character into the other parenthesis.  
The question: *Is there a way to obtain a **valid** parenthesis string?*

--------------------------------------------------------------------

#### Observations

* A valid parenthesis string is always of **even** length.  
  If the length is odd → impossible.

* While scanning the string from the left, at any position the number of
  `(` that are *fixed* (locked `'('`) minus the number of fixed `)` (locked `')'`)
  may be compensated by some of the unlocked positions (`'0'`).  
  If at some point

```
fixedOpen + unlocked < 0
```

  we would have more `)` than we can still match → impossible.

* Symmetrically, scanning from the right we must never have
  more fixed `'('` than fixed `')'` that can still be compensated by
  unlocked positions.

If both scans finish without contradiction, a valid assignment exists.

--------------------------------------------------------------------

#### Algorithm
```
canBeValid(s, locked):
    n = len(s)
    if n is odd: return false

    // left → right
    open  = 0          // locked '(' – locked ')'
    freeL = 0          // unlocked positions so far
    for i from 0 to n-1:
        if locked[i] == '0': freeL++
        else if s[i] == '(': open++
        else if s[i] == ')': open--
        if open + freeL < 0: return false

    // right → left
    close = 0          // locked ')' – locked '('
    freeR = 0          // unlocked positions so far
    for i from n-1 down to 0:
        if locked[i] == '0': freeR++
        else if s[i] == ')': close++
        else if s[i] == '(': close--
        if close + freeR < 0: return false

    return true
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm returns `true` iff the string can be made
valid.

---

##### Lemma 1  
During the left‑to‑right scan `open + freeL` is the maximum possible
balance (number of unmatched `'('`) that could exist at that position
using only the unlocked characters seen so far.

**Proof.**

* `open` counts the difference `locked('(') – locked(')')` up to this
  position.  
* `freeL` counts all unlocked positions seen so far; each of them can be
  turned into `'('` if needed, thus increasing the balance by at most
  `freeL`.  
Therefore `open + freeL` is the largest balance achievable with the
information available so far. ∎



##### Lemma 2  
If during the left‑to‑right scan the algorithm encounters
`open + freeL < 0`, then the string cannot be made valid.

**Proof.**

`open` is the exact balance contributed by the *locked* parentheses.
`freeL` can compensate at most one `)` per unlocked position.  
If even after using all currently available unlocked characters the
balance becomes negative, there are more `)` than possible `(` before
this point, which will stay unmatched no matter how we change later
unlocked positions.  
Thus no valid assignment exists. ∎



##### Lemma 3  
If the left‑to‑right scan finishes without violating the condition
in Lemma&nbsp;2, then for every prefix of the string there exists a
choice of the unlocked positions inside this prefix that keeps the
balance non‑negative.

**Proof.**

The scan ensures that after processing each prefix

```
open + freeL >= 0
```

which means the prefix can be completed (by turning some unlocked
positions into `'('`) to a balanced prefix. ∎



##### Lemma 4  
The right‑to‑left scan is analogous to Lemma&nbsp;3 but for the suffix
ending at each position. Therefore, if it finishes without violation,
every suffix can be completed to a balanced suffix.

∎



##### Theorem  
`canBeValid` returns `true` **iff** the string can be turned into a
valid parenthesis string by changing only the unlocked positions.

**Proof.**

*If the algorithm returns `true`*  
  - Length is even.  
  - By Lemma&nbsp;3 every prefix can be completed to a balanced prefix.  
  - By Lemma&nbsp;4 every suffix can be completed to a balanced suffix.  
  Since the whole string has even length, these two properties together
  guarantee that we can assign the unlocked positions so that the
  resulting string is a correct parenthesis sequence.

*If the algorithm returns `false`*  
  - Either the length is odd → impossible.  
  - Or one of the scans violates its condition.  
    By Lemma&nbsp;2 or its symmetric version, a prefix or suffix would
    contain more locked `)` than possible `'('` even after using all
    unlocked characters.  
    Thus no assignment can make the whole string valid.

Hence the algorithm is correct. ∎



--------------------------------------------------------------------

#### Complexity Analysis

*Time* – Two linear scans: `O(n)`  
*Space* – A handful of integer counters: `O(1)`

--------------------------------------------------------------------

#### Reference Implementation (Go 1.17)

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func canBeValid(s string, locked string) bool {
	n := len(s)
	if n%2 != 0 { // must be even
		return false
	}

	// left‑to‑right
	open := 0   // locked '(' minus locked ')'
	freeL := 0  // number of unlocked positions seen so far
	// right‑to‑left
	close := 0  // locked ')' minus locked '('
	freeR := 0  // number of unlocked positions seen so far

	for i := 0; i < n; i++ {
		// ---------- left to right ----------
		if locked[i] == '0' {
			freeL++
		} else {
			if s[i] == '(' {
				open++
			} else { // ')'
				open--
			}
		}
		if open+freeL < 0 {
			return false
		}

		// ---------- right to left ----------
		j := n - i - 1
		if locked[j] == '0' {
			freeR++
		} else {
			if s[j] == ')' {
				close++
			} else { // '('
				close--
			}
		}
		if close+freeR < 0 {
			return false
		}
	}

	return true
}

func main() {
	in := bufio.NewReader(os.Stdin)
	var s, locked string
	if _, err := fmt.Fscan(in, &s); err != nil {
		return
	}
	if _, err := fmt.Fscan(in, &locked); err != nil {
		return
	}
	if canBeValid(s, locked) {
		fmt.Println("true")
	} else {
		fmt.Println("false")
	}
}
```

The program reads two strings from standard input (`s` and `locked`),
calls `canBeValid`, and prints `true` or `false`.  
It follows exactly the algorithm proven correct above and is fully
compatible with Go 1.17.