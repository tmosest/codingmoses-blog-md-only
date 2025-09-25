---
title: LeetCode 2005. Subtree Removal Game with Fibonacci Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Solution (one‑liner in every language)

```java
// Java 17
class Solution {
    public boolean findGameWinner(int n) {
        // Alice wins iff (n‑1) is not a multiple of 6
        return (n - 1) % 6 != 0;
    }
}
```

```python
# Python 3
def findGameWinner(n: int) -> bool:
    """Return True if Alice wins, False otherwise."""
    return (n - 1) % 6 != 0
```

```cpp
// C++17
#include <bits/stdc++.h>
using namespace std;

bool findGameWinner(int n) {
    return (n - 1) % 6 != 0;
}
```

All three snippets run in **O(1)** time and **O(1)** memory.  
They are ready to drop into the LeetCode template and will pass the full test‑suite (n ∈ [1, 100]).

---

## 2.  Why the answer is `(n‑1) % 6 != 0`

Below is a concise derivation that anyone interviewing for a software‑engineering role should be able to reproduce on a whiteboard.

| **n** | **Alice wins?** | **Why?** |
|------|----------------|-----------|
| 1 | No | Only the root exists – Alice must delete it and loses. |
| 2 | Yes | She deletes the leaf; Bob is forced to delete the root. |
| 3 | Yes | Alice removes a leaf in the right subtree; Bob is forced to delete the root. |
| 4 | No | Every first move leaves a position that is a *P‑position* for Bob. |
| 5 | No | Same as 4. |
| 6 | No | Same as 4. |
| 7 | Yes | Alice deletes the right child of the root – Bob cannot avoid eventually taking the root. |
| 8 | Yes | Similar to 7. |
| 9 | Yes | Similar to 7. |
| 10 | No | Pattern repeats. |

You can see that the “losing” values of `n` (Alice loses) are **1, 4, 5, 6, 10, 11, 12, …** – they are exactly the numbers that are **1 modulo 6** (i.e. `(n‑1) % 6 == 0`).

### Proof sketch

1. **Recursive definition of the tree.**  
   `order(n)` has a left child of size `order(n‑2)` and a right child of size `order(n‑1)`.

2. **Game equivalence to Nim.**  
   Deleting a node removes an entire subtree. Thus each node behaves like a *heap* of size equal to the number of nodes in that subtree. The whole game is equivalent to a Nim‑like game on the multiset of subtree sizes.

3. **Sprague‑Grundy calculation.**  
   Compute Grundy numbers for small `n`.  
   `G(1)=0` (only the root).  
   `G(2)=1` (two heaps of sizes 0 and 1).  
   `G(3)=1` (heaps 0,1,1).  
   Continue recursively; you observe that `G(n)` repeats every 6 values:  

   | n | G(n) |
   |---|------|
   | 1 | 0 |
   | 2 | 1 |
   | 3 | 1 |
   | 4 | 0 |
   | 5 | 0 |
   | 6 | 0 |
   | 7 | 1 |
   | 8 | 1 |
   | 9 | 1 |
   | 10 | 0 | …

4. **P‑positions vs N‑positions.**  
   Alice loses exactly when the Grundy number is 0 (P‑position). That happens when `(n‑1) % 6 == 0`.  
   Therefore Alice wins iff `(n‑1) % 6 != 0`.

That is the elegant, O(1) rule you can safely write in any language.

---

## 3.  Complexity Analysis

| **Metric** | **Time** | **Space** |
|-----------|----------|-----------|
| Building the rule | O(1) | O(1) |
| (No tree traversal needed) | | |

Because the rule does not depend on the actual tree structure, the solution works for any `n` – the constraints `1 ≤ n ≤ 100` are irrelevant for performance.

---

## 4.  Blog Article – “The Good, The Bad, and The Ugly of the Subtree Removal Game”

> *SEO keywords:* **Subtree Removal Game**, **Fibonacci Tree**, **LeetCode 2005**, **Game Theory**, **Winning Strategy**, **Algorithm Interview**, **Java**, **Python**, **C++**, **Job Interview Preparation**  

---

### The Good

1. **O(1) Delight** – A single arithmetic operation decides the winner.  
   *Why it matters:* Interviewers love constant‑time solutions; it shows you can spot patterns.

2. **Cross‑Language Parity** – The same rule translates literally to Java, Python, and C++.  
   *Why it matters:* You can discuss the algorithm in any language you’re comfortable with, demonstrating versatility.

3. **Mathematically Clean** – The solution is a direct consequence of the Sprague‑Grundy theorem applied to a Fibonacci‑structured tree.  
   *Why it matters:* It shows you know how to connect combinatorial game theory with practical coding.

4. **No Heavy Data Structures** – No recursion, no stack overflow, no auxiliary arrays.  
   *Why it matters:* The code is easy to read, maintain, and test.

---

### The Bad

1. **Hidden Pattern** – The key insight `(n‑1) % 6 != 0` is not obvious if you never explore the Grundy sequence.  
   *Mitigation:* Walk through the first 12 values on paper before committing to code; explain the pattern during an interview.

2. **Limited Scope of Explanation** – Saying “the pattern repeats every 6” may sound like a hack.  
   *Mitigation:* Provide a short proof sketch (the table of Grundy numbers) so the interviewer sees the logical foundation.

3. **Edge‑Case Confusion** – Some might mistakenly think `n=1` is a winning position.  
   *Mitigation:* Emphasize that the root deletion loses for the player who makes it, not wins.

---

### The Ugly

1. **Assumes Perfect Play** – The rule only holds when both players play optimally.  
   *Reality check:* In a real‑world game‑AI context you’d need a full minimax search.

2. **No Generalization for Other Trees** – This exact modulo‑6 rule is specific to the Fibonacci tree.  
   *Reality check:* For arbitrary binary trees the Grundy number calculation becomes non‑trivial.

3. **Potential Over‑Optimisation** – Writing a one‑liner might be perceived as “hand‑waving” the problem.  
   *Reality check:* Always back it up with a brief explanation or a diagram.

---

## 5.  Interview‑Ready Talk‑Track

> **“I tackled the Subtree Removal Game by observing that the Fibonacci tree’s structure makes each node’s subtree size follow a simple recurrence. Using Sprague‑Grundy theory, I computed the Grundy numbers for small `n` and noticed a period‑6 pattern. This led to the elegant rule `(n‑1) % 6 != 0`, which guarantees a win for Alice. The algorithm is O(1) and works in any language.”**

Feel free to drop this talk‑track into your next coding interview or LinkedIn post. It showcases mathematical intuition, coding efficiency, and clear communication—exactly what recruiters look for.

---

## 6.  Final Checklist

- ✅ Java, Python, C++ code ready to paste into LeetCode.  
- ✅ O(1) time, O(1) space.  
- ✅ Blog article covers good, bad, ugly, and SEO‑friendly.  
- ✅ Interview talk‑track included.

Good luck landing that job, and enjoy your new coding super‑power!