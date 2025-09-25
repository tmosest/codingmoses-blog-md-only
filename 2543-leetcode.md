---
title: LeetCode 2543. Check if Point Is Reachable - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2543 – *Check if Point Is Reachable*  
**Target audience** – anyone preparing for a technical interview or looking to master algorithmic math.  
**Languages** – Java, Python, C++  
**Key take‑away** – **You can reach \((targetX, targetY)\) from \((1,1)\) iff \(\gcd(targetX, targetY)\) is a power of 2.**  

Below you’ll find a production‑ready implementation for each language and a blog‑style article that explains the reasoning, pitfalls, and interview‑friendly insights.

---

## 1. Code

### 1.1 Java

```java
import java.util.*;

public class Solution {
    public boolean isReachable(int targetX, int targetY) {
        // gcd from java.lang.Math (Java 17+)
        int g = Math.gcd(targetX, targetY);     // or java.math.BigInteger
        // Check if g is a power of two: only one bit set
        return (g & (g - 1)) == 0;
    }
}
```

> **Why `Math.gcd`?**  
> Java 17+ exposes `Math.gcd`. If you’re on an older JDK, just import `java.math.BigInteger` or write a small Euclid function.

### 1.2 Python 3

```python
from math import gcd

class Solution:
    def isReachable(self, targetX: int, targetY: int) -> bool:
        g = gcd(targetX, targetY)
        # Power‑of‑two test: only one bit set
        return (g & (g - 1)) == 0
```

> **Python 3.9+** already includes `math.gcd`; otherwise `gcd` can be written manually.

### 1.3 C++ (GCC/Clang)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isReachable(int targetX, int targetY) {
        int g = std::gcd(targetX, targetY);   // C++17
        return (g & (g - 1)) == 0;            // power of two?
    }
};
```

> **`std::gcd`** is part of `<numeric>` in C++17; `<bits/stdc++.h>` pulls it in automatically.

> **Tip for interviewers** – If you’re asked to implement the GCD yourself, just drop a classic Euclid loop:

```cpp
int myGcd(int a, int b) {
    while (b) { a %= b; swap(a, b); }
    return a;
}
```

---

## 2. Blog‑style article

> *Feel free to copy‑paste the article into a Markdown blog, a LinkedIn post, or a personal portfolio.*

```markdown
# Check if Point Is Reachable – The Good, the Bad, and the Ugly  
A Deep Dive into LeetCode 2543 for Java, Python & C++

## Table of Contents
- [Problem Overview](#problem-overview)
- [The Mathematical Insight](#the-mathematical-insight)
- [Algorithmic Walk‑through](#algorithmic-walk-through)
- [Complexity & Edge Cases](#complexity--edge-cases)
- [Common Pitfalls (The Ugly)](#common-pitfalls)
- [Interview Tips & Take‑aways](#interview-tips)
- [Why It Matters in a Hiring Process](#why-it-matters)
- [Further Reading & Practice](#further-reading)
```

### Problem Overview

```
You’re standing at coordinate (1, 1) on an infinite integer grid.  
You may repeatedly apply one of the following moves:

1️⃣ (x, y) → (x, y - x)
2️⃣ (x, y) → (x - y, y)
3️⃣ (x, y) → (2*x, y)
4️⃣ (x, y) → (x, 2*y)

Given two integers targetX and targetY (1 ≤ targetX, targetY ≤ 1e9), determine whether it’s possible to reach (targetX, targetY) from (1, 1) using any sequence of moves.
```

At first glance this looks like a graph‑search problem, but it’s a classic *number‑theory* puzzle. A naïve BFS would blow up – the state space is unbounded. The real magic comes from understanding how the operations affect the greatest common divisor (GCD) of the two coordinates.

### The Good – A One‑Line GCD Check

The whole problem collapses to a **single GCD operation** and a *power‑of‑two* test. That’s why the “One‑Line GCD Solution” is popular on LeetCode:  
```gcd & (gcd-1) == 0```  
works in O(log min(x, y)) time and O(1) space. The three key steps are:

1. Compute `g = gcd(targetX, targetY)` (Euclidean algorithm).  
2. Strip every factor of 2: `while (g % 2 == 0) g /= 2;` (optional).  
3. If the remaining `g` is 1 → reachable; otherwise → not reachable.

The `g & (g-1)` trick is a bit‑count trick that tells you whether `g` has exactly one bit set – i.e., `g` is 2^k.

### The Bad – Why Naïve BFS Fails

* **Infinite state space** – The moves double coordinates, so the search tree explodes exponentially.  
* **Memory blow‑up** – Even a breadth‑first search that keeps a `HashSet<(x, y)>` will hit the 256 MB limit quickly for values near 1e9.  
* **Time‑out** – Repeating subtractions `(x, y-x)` and `(x-y, y)` is equivalent to the Euclidean algorithm, but you’d be writing it by hand in the BFS loop, wasting constant factors.

> **Take‑away:** In an interview, start by asking *“How does the GCD change under each operation?”* This question naturally steers the discussion toward Euclid’s algorithm and powers of two.

### The Ugly – Common Misconceptions

| Misconception | Why it’s wrong | What to watch for |
|---------------|----------------|-------------------|
| *“If one coordinate is even, divide it by 2 and keep going.”* | You must also consider the other coordinate’s parity and its GCD. Skipping the GCD step can lead to wrong answers when both numbers are odd. | Always compute the GCD first; it automatically accounts for all powers of two in both coordinates. |
| *“Use (x‑y) ↔ (x+y) when reversing 2*x ↔ x/2.”* | `x±y` keeps the GCD invariant; reversing `2*x` as `x/2` is fine, but `x±y` stays valid because GCD(x±y, y) = GCD(x, y). | Don’t overthink the “+” vs “‑”; the GCD remains unchanged regardless of sign. |
| *“Power of two = only even factors.”* | A power of two means *all* prime factors are 2, but the number can be odd (i.e., 1). | Use `bitCount` or the `x & (x-1)` trick to check *only one bit is set*, not just “evenness.” |

### Why Power of 2?

Every time you double a coordinate, the GCD gets multiplied by 2. Starting from \((1,1)\) where \(\gcd=1\), the only way to get a target GCD other than 1 is by repeatedly applying the *doubling* operations. Therefore, the final GCD must be \(2^k\). If any odd prime factor appears, you’ll never be able to produce it from \((1,1)\).

---

## 3. Interview‑Ready Discussion

### 3.1 Why This Problem is Interview Gold

* **Mathematical insight** – Demonstrates how to translate a problem into number theory (GCD, Euclidean algorithm).  
* **Algorithmic elegance** – One‑liner in any language, but the reasoning is non‑trivial.  
* **Edge‑case mastery** – Handles the extremes of the constraints cleanly (1 ≤ value ≤ 1e9).  
* **Cross‑language consistency** – Shows you can solve it in Java, Python, C++ – a plus for roles that require multiple tech stacks.

### 3.2 Sample Interview Conversation

> **Interviewer:** “We’re standing at \((1,1)\). We can double X, double Y, subtract Y from X, or subtract X from Y. Is it always possible to reach an arbitrary point \((a,b)\)?”

> **Candidate:** “We should look at how each operation changes the GCD. Subtractions keep the GCD unchanged; doubling multiplies it by 2. So from \((1,1)\) the GCD can only be \(1,2,4,\dots\). Therefore, we can reach \((a,b)\) iff \(\gcd(a,b)\) is a power of 2.”

> **Interviewer:** “Excellent. How would you code that?”

> **Candidate:** [Hands over the one‑liner shown above.]

> **Interviewer:** “What about time‑complexity?”

> **Candidate:** “Euclid’s algorithm runs in \(O(\log(\min(a,b)))\). The power‑of‑two test is constant time. Overall \(O(\log n)\) time and \(O(1)\) space.”

### 3.3 Common Mistakes to Highlight

| Mistake | How to Spot It | Fix |
|---------|----------------|-----|
| Using `x / 2` when `x` is odd during reverse BFS | Will produce non‑integer states | Stop reverse BFS when both coordinates are odd; check GCD instead |
| Checking only that `g % 2 == 0` | Misinterprets “power of two” as “even” | Use bit‑count or `g & (g-1) == 0` |
| Forgetting that subtraction moves keep the GCD | Will think GCD changes after `x-y` | Prove that `gcd(x±y, y) = gcd(x, y)` using Euclid’s theorem |

---

## 4. Blog Article (SEO‑Optimized)

```markdown
# Check if Point Is Reachable – The Good, the Bad, and the Ugly  
**LeetCode 2543, GCD, Power of 2, Algorithmic Thinking, Java, Python, C++**

> *Prepared for software‑engineering interview prep, algorithmic math, and coding‑challenge lovers.*

## 🎯 Problem Statement

You’re at coordinate **(1, 1)** on an infinite integer grid.  
You may perform any of the following moves **any number of times**:

1. `(x, y) → (x, y – x)`
2. `(x, y) → (x – y, y)`
3. `(x, y) → (2·x, y)`
4. `(x, y) → (x, 2·y)`

Given integers `targetX` and `targetY` (1 ≤ targetX, targetY ≤ 10⁹), can you reach `(targetX, targetY)`?

> **Goal:** Output `True` if reachable, otherwise `False`.

## 🔍 The Good – A One‑Liner Solution

If you think this is a graph traversal problem, you’re missing the **math hidden behind the moves**.  
A clean, single‑line solution is:

```python
return math.gcd(targetX, targetY) & (math.gcd(targetX, targetY) - 1) == 0
```

In Java, Python, and C++ this runs in *O(log min(x, y))* time and *O(1)* space.  
The magic lies in how each operation affects the **greatest common divisor (GCD)**.

## 📐 The Mathematical Insight

* Subtractions (`x±y`) **do not change** the GCD.  
* Doubling a coordinate multiplies the GCD by **2**.  

From `(1, 1)` the GCD starts at 1 and can only become `2, 4, 8, …`.  
Thus, the GCD of any reachable point must be a **power of two**.

### Proving GCD Invariance

`gcd(x ± y, y) = gcd(x, y)` is a corollary of Euclid’s algorithm.  
Subtracting `y` from `x` is equivalent to one step of Euclid’s algorithm; the GCD never changes.

### Power of 2 Test

A number `n` is a power of 2 iff it has **exactly one set bit**.  
The classic bit trick:

```
(n & (n-1)) == 0  →  true if n is 1, 2, 4, 8, …
```

or in modern C++/Java/Python:

```
bitCount(n) == 1
```

## ⚠️ The Bad – Why Naïve BFS Fails

* **State explosion** – Each double move can push you two times farther.  
* **Memory limit** – Even with a `HashSet`, storing all reachable pairs up to 1e9 is impossible.  
* **Time‑out** – An unoptimized BFS would easily hit the 1 s limit on LeetCode.

> *Lesson for interviewers:* Always ask *“What invariant can we track?”* and you’ll arrive at the GCD quickly.

## 💥 The Ugly – Common Pitfalls

| Mistake | Why it breaks | How to correct |
|---------|---------------|----------------|
| Dividing an odd number by 2 during reverse search | Generates non‑integer states | Abort when both numbers are odd; use GCD instead |
| Checking `g % 2 == 0` | Interprets “power of 2” as “even” | Use `bitCount` or `n & (n-1) == 0` |
| Ignoring that subtraction preserves GCD | Misleading intermediate values | Show Euclid’s theorem in the solution |

## 🚀 Interview Tips

1. **Explain the GCD intuition first.**  
2. **Show the power‑of‑two condition** using bit tricks.  
3. **Discuss time complexity**: Euclid’s algorithm runs in *logarithmic* time.  
4. **Mention edge cases**: `targetX` or `targetY` equals 1; both coordinates odd but GCD 1 → reachable.

## 🎁 Why It Matters in Hiring

* **Demonstrates depth** – Number theory shows you can think beyond brute force.  
* **Language‑agnostic** – Solvable in Java, Python, and C++.  
* **Efficient** – Meets LeetCode’s strict time and memory limits.  
* **Stand‑out skill** – A candidate who explains this elegantly earns extra brownie points.

## 📚 Further Practice

- LeetCode: [Number of Ways to Rearrange a String](https://leetcode.com/problems/number-of-ways-to-rearrange-a-string/)  
- HackerRank: [GCD LCM Challenge](https://www.hackerrank.com/challenges/gcdlcm)  
- Codeforces: [Subtractions and Multiplications](https://codeforces.com/problemset/problem/1472/C)

```

### Why It Matters in a Hiring Process

* **Quantitative ability** – Roles that involve data‑structures, cryptography, or performance‑critical code look for candidates who can identify invariants.  
* **Clean code** – The one‑liner demonstrates mastery over built‑in functions like `math.gcd`, `std::gcd`, or Java’s `BigInteger`.  
* **Time‑management** – You spend minutes proving the theorem and seconds coding; that shows efficiency under interview pressure.

> **Pro Tip:** Pair the article with a live coding session on a portfolio website. Show the runtime test against random 1e9 values to convince recruiters of your confidence.

---

## 5. Further Reading

1. **“Number Theory for Programmers”** – A series of articles explaining GCD, LCM, and modular arithmetic in a programming context.  
2. **“Euclid’s Algorithm in O(log n)”** – Classic algorithm explanation with pseudocode.  
3. **LeetCode discuss** – Search “2543 GCD” to see community discussions on variations.  

---

> **End of article.**
```

---

## 5. Summary

* **Three languages, same algorithm** – the one‑liner GCD check.  
* **Avoid naïve BFS** – state space is infinite; the math gives you a constant‑time solution.  
* **Interview success** – shows mathematical reasoning, algorithmic elegance, and cross‑stack competency.  
* **Hiring impact** – Demonstrates a candidate’s ability to turn a brute‑force problem into a clean, theoretical solution—exactly what tech recruiters want.

---

### Final Words

- *If you’re building a personal website or a blog post, copy the Markdown above.*  
- *Feel free to tweak the problem description to match the style of your portfolio.*  
- *Remember to include the three code snippets and the interview dialogue; recruiters love seeing both code and thought process.*

Happy coding, and good luck on your interview prep!
```

---

Feel free to adjust the headings, add images (e.g., a diagram of the grid moves), or embed the solution snippets in code blocks. The goal is to show depth of understanding while keeping the explanation concise and interview‑friendly.
``` 

---

With the article and the three solution snippets you’re fully equipped to showcase **LeetCode 2543** as a testament to your algorithmic and mathematical skillset. Happy coding and best of luck on your next interview!