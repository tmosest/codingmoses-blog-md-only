---
title: LeetCode 858. Mirror Reflection - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Mirror Reflection – LeetCode 858  
### The Good, The Bad, and The Ugly (Java | Python | C++)

---

### TL;DR  
- **Problem** – Find the first receptor a laser ray hits in a square room with mirrored walls.  
- **Key idea** – Reduce the problem to a number‑theory puzzle using *gcd* and *lcm*.  
- **Complexity** – **O(log min(p,q))** time, **O(1)** memory.  
- **Why it matters** – A classic interview favourite that tests math intuition, greedy thinking, and code elegance.

---

## 1. Problem Statement (Short & Sweet)

In a square room of side length `p` a laser starts from the southwest corner and moves toward the east wall.  
The first time it hits the east wall it is at a distance `q` from the 0‑receptor (the corner on the east wall).  

Receptors are at the three other corners (0, 1, 2).  
**Return the number of the receptor the ray meets first.**

*Constraints*  
`1 ≤ q ≤ p ≤ 1000` – the ray always meets a receptor eventually.

---

## 2. The Good – Intuition & Elegant Solution

### 2.1 Visualizing Reflections

Think of “unfolding” the room.  
Each time the ray hits a wall, instead of reflecting, we mirror the room and let the ray continue straight.  
After unfolding, the ray travels in a straight line from `(0,0)` to the first *virtual* corner that lies on the grid of size `p × p`.

The vertical coordinate when the ray first reaches a vertical boundary (either east or west wall) is a multiple of `p`.  
The horizontal coordinate is a multiple of `p` as well – but only when `m × q` is a multiple of `p`, where `m` is the number of times we hit a vertical wall.

Thus we need the smallest integer `m` such that:

```
m × q ≡ 0 (mod p)
```

### 2.2 Using GCD / LCM

`m` is simply the least common multiple of `p` and `q` divided by `q`:

```
lcm(p, q) = p × q / gcd(p, q)
m        = lcm(p, q) / q = p / gcd(p, q)
```

Let

```
p_div = p / gcd(p, q)
q_div = q / gcd(p, q)
```

Now:

- If `q_div` is even → the ray hits the **west wall** first → receptor **2**.
- If `q_div` is odd:
  - If `p_div` is odd → receptor **1**.
  - Else (`p_div` even) → receptor **0**.

That’s it! A single GCD calculation and a few parity checks.

### 2.3 Code Snippets

| Language | Code |
|----------|------|
| **Java** | ```java<br>public class Solution {<br>    public int mirrorReflection(int p, int q) {<br>        int g = gcd(p, q);<br>        int pDiv = p / g;<br>        int qDiv = q / g;<br>        if (qDiv % 2 == 0) return 2;   // hits west wall first<br>        return (pDiv % 2 == 1) ? 1 : 0; // odd→1, even→0<br>    }<br>    private int gcd(int a, int b) {<br>        while (b != 0) {<br>            int t = a % b;<br>            a = b;<br>            b = t;<br>        }<br>        return a;<br>    }<br>}<br>``` |
| **Python** | ```python<br>class Solution:<br>    def mirrorReflection(self, p: int, q: int) -> int:<br>        from math import gcd<br>        g = gcd(p, q)<br>        p_div, q_div = p // g, q // g<br>        if q_div % 2 == 0:<br>            return 2<br>        return 1 if p_div % 2 == 1 else 0<br>``` |
| **C++** | ```cpp<br>#include <bits/stdc++.h><br>using namespace std;<br><br>class Solution {<br>public:<br>    int mirrorReflection(int p, int q) {<br>        int g = gcd(p, q);<br>        int pDiv = p / g;<br>        int qDiv = q / g;<br>        if (qDiv % 2 == 0) return 2; // west wall<br>        return (pDiv % 2 == 1) ? 1 : 0; // east or south wall<br>    }<br>};<br>``` |

> **SEO Tip:** Use the exact class name `Solution` – LeetCode expects it.

---

## 3. The Bad – Common Pitfalls

| Mistake | Why It Breaks | Fix |
|---------|---------------|-----|
| **Using `lcm` directly** | `lcm` can overflow if `p*q` exceeds int range (unlikely with given limits but still). | Compute `p / gcd(p, q)` instead of `p * q / gcd(p, q)`. |
| **Ignoring parity** | Many people output `p_div % 2` or `q_div % 2` in the wrong order. | Remember: even `q_div` → receptor 2. |
| **Off‑by‑one in indexing** | The problem uses 0‑based receptor numbering; a 1‑based mental model can cause wrong outputs. | Stick to the rules above. |
| **Brute force simulation** | Simulating reflections step‑by‑step is O(p/q) and can be slow for large values. | Use the math shortcut. |

---

## 4. The Ugly – What Happens When We Over‑Optimize

- **Micro‑optimizations** such as inline `gcd` for every test case (if many test cases) can make the code unreadable.
- **Over‑engineering** – using complex number theory (modular inverses, extended Euclid) adds noise.
- **Code obfuscation** – one‑liner “ternary hell” looks slick but is hard to debug.

> *Lesson*: Keep it simple, readable, and correct. Interviewers value clean code more than clever tricks.

---

## 5. Complexity Analysis

- **Time**: `O(log min(p,q))` – the time for the Euclidean GCD algorithm.  
- **Space**: `O(1)` – constant auxiliary space.

---

## 6. How This Helps Your Job Hunt

1. **Demonstrates mathematical insight** – Many interviewers love candidates who turn geometry into algebra.  
2. **Shows clean coding habits** – Your solution is short, readable, and covers edge cases.  
3. **Highlights algorithmic thinking** – You moved from simulation to number theory efficiently.  
4. **Suits multiple languages** – You can switch between Java, Python, C++ with ease – great for multi‑language teams.

---

## 7. SEO‑Optimized Blog Checklist

| SEO Element | Implementation |
|-------------|----------------|
| **Title** | *Mirror Reflection LeetCode 858 – Java, Python, C++ Solution (GCD & LCM)* |
| **Meta Description** | *Solve LeetCode 858 – Mirror Reflection. Understand the math behind GCD/Lcm, see clean Java, Python, C++ code, and learn why this problem is perfect for interview prep.* |
| **Headers** | Use H1 for title, H2 for sections (Problem, Intuition, Code, etc.). |
| **Keywords** | `Mirror Reflection`, `LeetCode 858`, `GCD`, `LCM`, `algorithm interview`, `Java solution`, `Python solution`, `C++ solution`. |
| **Internal Links** | Link to related LeetCode problems (e.g., 87, 994). |
| **External Links** | Cite official LeetCode problem page, and reference the links you provided. |
| **Images/Diagrams** | A small diagram of the unfolded room can boost engagement. |
| **Readability** | Keep paragraphs short, bullet lists, and code blocks highlighted. |
| **Call to Action** | “Try this on LeetCode now and tell me how many points you earned!” |

---

## 8. Final Takeaway

Mirror Reflection is more than a geometry puzzle – it’s a micro‑case study in *simplifying* a problem.  
By mapping the reflections to an unfolded grid and using the gcd/lcm relationship, we turn a seemingly complex physics problem into a handful of arithmetic operations.

**Good**: Elegant math, minimal code.  
**Bad**: Common parity mistakes.  
**Ugly**: Over‑engineering and unreadable one‑liners.

Master this, and you’ll not only ace LeetCode 858 but also impress interviewers with your ability to distill problems into clean, efficient solutions. Happy coding!