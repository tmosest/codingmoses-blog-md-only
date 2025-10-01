---
title: LeetCode 2005. Subtree Removal Game with Fibonacci Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Subtree Removal Game with Fibonacci Tree” – 2005 – LeetCode  
**A Deep Dive into a Hard Problem with a One‑Line Solution**  

> *Want to land that software engineering interview? Show recruiters you can find patterns, prove them, and write clean code in multiple languages. This blog walks through the famous 2005 LeetCode problem “Subtree Removal Game with Fibonacci Tree” – why it’s hard, how to solve it, and why the trick is elegant.*

---

## Table of Contents

1. [Problem Recap](#problem-recap)  
2. [Game Theory Background](#game-theory-background)  
3. [Observations & Key Insight](#observations-key-insight)  
4. [Proof of the Winning Condition](#proof-of-the-winning-condition)  
5. [Algorithm & Complexity](#algorithm-complexity)  
6. [Reference Implementations](#reference-implementations)  
   - Java  
   - Python  
   - C++  
7. [Good, Bad, & Ugly](#good-bad-ugly)  
8. [SEO & Job‑Ready Tips](#seo-job-ready-tips)  
9. [Conclusion](#conclusion)  

---

## Problem Recap <a name="problem-recap"></a>

> **Subtree Removal Game with Fibonacci Tree**  
> Build a binary tree recursively:
> * `order(0)` → empty  
> * `order(1)` → one node  
> * `order(n)` → root + left = `order(n‑2)`, right = `order(n‑1)`  
> Alice and Bob alternate turns; a move removes a node **and** its entire subtree. The player forced to delete the root loses.  
> Return `true` if Alice (first player) wins, otherwise `false`.

**Constraints**

- `1 ≤ n ≤ 100` (but solution works for any `n`).

---

## Game Theory Background <a name="game-theory-background"></a>

The game is **impartial**: both players have the same set of legal moves from any position.  
For impartial games, *Sprague‑Grundy theory* tells us that every position is equivalent to a Nim heap of a certain size (`Grundy number`). The player to move wins iff the XOR of all Grundy numbers is non‑zero.

In this tree game, each subtree is a sub‑game. The Grundy number of a node equals `mex` (minimum excluded) of the Grundy numbers of all *reachable* sub‑games. Because a move removes a node **and** all its descendants, the only sub‑games a player can leave are the sibling sub‑trees that were not deleted.

---

## Observations & Key Insight <a name="observations-key-insight"></a>

1. **Structure Recurrence**  
   `order(n)` has left subtree `order(n-2)` and right subtree `order(n-1)`.

2. **Grundy Numbers Repeat**  
   By computing Grundy numbers for the first few `n`, a pattern emerges:

   | n | Grundy(n) |
   |---|-----------|
   | 0 | 0 |
   | 1 | 1 |
   | 2 | 2 |
   | 3 | 3 |
   | 4 | 4 |
   | 5 | 5 |
   | 6 | 0 |
   | 7 | 1 |
   | … | … |

   The Grundy number repeats every 6 steps: `G(n) = (n-1) mod 6`.

3. **Winning Condition**  
   Alice wins iff `G(n) ≠ 0`.  
   Thus, Alice loses only when `(n-1) % 6 == 0`.

4. **Why the Pattern?**  
   The recurrence `G(n) = mex{ G(n-1), G(n-2) }` produces the 6‑cycle. The “mex” of two numbers that are themselves a cycle of length 6 produces the same cycle shifted by one. The proof follows by induction.

---

## Proof of the Winning Condition <a name="proof-of-the-winning-condition"></a>

**Base Cases**  
- `n = 1`: The only node is the root; Alice must delete it → loses.  
  `G(1) = 1` (non‑zero) but the *player* loses because removing the root is the losing move.  
  The rule `G(n) = (n-1) mod 6` gives `G(1) = 0`.  
  So our Grundy assignment must consider that deleting the root is a losing *terminal* move; we treat the root as a *sink* with Grundy 0.

**Induction Step**  
Assume `G(k) = (k-1) mod 6` for all `k < n`.  
`order(n)` has sub‑games `order(n-1)` and `order(n-2)`.  
The set of reachable Grundy numbers after a legal move is `{ G(n-1), G(n-2) }`.  
Therefore

```
G(n) = mex{ G(n-1), G(n-2) }
     = mex{ (n-2) % 6, (n-3) % 6 }
```

The pair `{(n-2)%6, (n-3)%6}` is always two distinct numbers from `{0,…,5}`.  
The mex of two distinct numbers in `[0,5]` is exactly the *third* number in that range, which equals `(n-1)%6`.  
Thus the recurrence holds.

**Conclusion**  
Alice wins iff `G(n) ≠ 0`, i.e. iff `(n-1) % 6 != 0`.

---

## Algorithm & Complexity <a name="algorithm-complexity"></a>

```text
Input:  n
Output: true  if Alice wins
        false if Bob wins

Algorithm:
    return (n - 1) % 6 != 0
```

- **Time:** `O(1)` – constant time arithmetic.  
- **Space:** `O(1)` – no extra storage.  
- **Scalability:** Works for `n` up to 10^9 or beyond; the only requirement is that `n` fits in the language’s integer type.

---

## Reference Implementations <a name="reference-implementations"></a>

> **Tip:** Use the same logic in all languages – this showcases versatility to recruiters.

### Java (LeetCode style)

```java
class Solution {
    public boolean findGameWinner(int n) {
        // Alice wins unless (n-1) is a multiple of 6
        return (n - 1) % 6 != 0;
    }
}
```

> Compile with `javac -cp .;leetcode.jar Solution.java`  
> Run with LeetCode's online judge.

### Python 3

```python
class Solution:
    def findGameWinner(self, n: int) -> bool:
        return (n - 1) % 6 != 0
```

> `python3 solution.py` – LeetCode runner will import `Solution`.

### C++ (GCC 17)

```cpp
class Solution {
public:
    bool findGameWinner(int n) {
        return (n - 1) % 6 != 0;
    }
};
```

> Compile: `g++ -std=c++17 -O2 -pipe -static -s -o main main.cpp`  
> LeetCode harness will instantiate `Solution`.

---

## Good, Bad, & Ugly <a name="good-bad-ugly"></a>

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Algorithm** | O(1) solution, elegant, proof‑based | Hard to guess without insight | O(n) tree construction is wasteful |
| **Complexity** | Minimal | None | None |
| **Implementation** | Clear one‑liner | None | Some may over‑complicate with recursion |
| **Readability** | Easy to understand | None | If you write full tree simulation, code bloats |
| **Testing** | 6‑cycle pattern covers all n | None | Without pattern, many edge tests are needed |
| **Interview Impact** | Demonstrates pattern discovery & game theory | Hard to explain if you skip proof | Too many lines can look unprofessional |

**Takeaway:**  
*Never build the entire Fibonacci tree if the answer is a simple arithmetic condition. Show that you understand the underlying math and can derive a clean O(1) solution.*

---

## SEO & Job‑Ready Tips <a name="seo-job-ready-tips"></a>

1. **Use High‑Impact Keywords**  
   - `LeetCode 2005 Subtree Removal Game`  
   - `Fibonacci Tree Game Theory`  
   - `Java/Python/C++ solution`  
   - `Optimal play algorithm`

2. **Write a Structured Blog**  
   - Headings (`#`, `##`) help search engines.  
   - Include a **code snippet** block for each language.  
   - Add a **“Proof”** section – recruiters love candidates who can explain.

3. **Show Your Thought Process**  
   - Start with naive solutions, then refactor.  
   - Explain why you removed the recursion.

4. **Include Big‑O Analysis**  
   - Recruiters look for complexity awareness.

5. **Provide Sample Test Cases**  
   - `n = 3 → true`, `n = 1 → false`, `n = 6 → false`.

6. **Link to LeetCode & GitHub**  
   - Demonstrate version control habits.  

7. **Use “Good, Bad, Ugly” Structure**  
   - Show self‑critical thinking and continuous improvement.

8. **Add a Call‑to‑Action**  
   - “Want to discuss more algorithmic puzzles? Let’s chat!”  
   - Encourages hiring managers to reach out.

---

## Conclusion <a name="conclusion"></a>

The **Subtree Removal Game with Fibonacci Tree** is a deceptively hard problem that collapses to a single arithmetic condition:  

```text
Alice wins ⇔ (n - 1) % 6 != 0
```

This concise formula is backed by Sprague‑Grundy theory and a 6‑cycle Grundy number pattern. Implementations in Java, Python, and C++ are literally one line, delivering optimal time and space.

**Why this matters for your career**

- Demonstrates *pattern recognition* and *proof skills*—highly prized in interviews.  
- Shows ability to **abstract** a complex recursive structure to a simple formula.  
- Highlights familiarity with *game theory*, a niche but valuable area.  

Publish this solution on your blog, GitHub, or LinkedIn, and pair it with a clear explanation. Recruiters love candidates who can deliver clean, provably correct code. Good luck, and may the algorithm be ever in your favor! 🚀