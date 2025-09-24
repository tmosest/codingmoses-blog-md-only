---
title: LeetCode 780. Reaching Points - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 780 – Reaching Points  
**A Deep‑Dive Blog Post (Java + Python + C++)**  
> *“The good, the bad, and the ugly” of this classic interview puzzle.*

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Why This Problem Rocks Interviewers](#why-this-problem-rocks-interviewers)  
3. [The Naïve Approach & Its Pitfalls](#the-naïve-approach)  
4. [The Optimal Back‑Tracking Algorithm](#optimal-back-tracking)  
5. [Step‑by‑Step Implementation](#step-by-step-implementation)  
6. [Code: Java, Python, C++](#code)  
7. [Time & Space Complexity](#complexity)  
8. [Edge Cases & Common Mistakes](#edge-cases)  
9. [The Good, The Bad, The Ugly](#good-bad-ugly)  
10. [Final Thoughts & Interview Tips](#final-thoughts)  
11. [References & Further Reading](#references)

---

## Problem Overview <a name="problem-overview"></a>

> **LeetCode 780 – Reaching Points**  
> **Difficulty:** Hard  
> **URL:** <https://leetcode.com/problems/reaching-points/>

Given integers `sx, sy, tx, ty`, determine if you can transform point `(sx, sy)` into `(tx, ty)` using only the operations:  
- `(x, y) → (x, x + y)`  
- `(x, y) → (x + y, y)`

All values are in the range `1 … 10^9`.

---

## Why This Problem Rocks Interviewers <a name="why-this-problem-rocks-interviewers"></a>

1. **Mathematical Insight** – Requires recognising a reverse‑engineering pattern.  
2. **Edge‑Case Awareness** – You must think about overflow, divisibility, and early exits.  
3. **Space Efficiency** – Must solve without recursion to avoid stack overflows on large inputs.  
4. **Clear Algorithmic Design** – Demonstrates ability to transform a forward process into a backward process.  

> *Job seekers who nail this problem show a strong grasp of algorithmic thinking.*

---

## The Naïve Approach & Its Pitfalls <a name="the-naïve-approach"></a>

A straightforward breadth‑first‑search (BFS) from `(sx, sy)` exploring all possible moves will explode exponentially.  
- **Time:** Unbounded; may hit millions of states before hitting `10^9`.  
- **Space:** Unmanageable for large input ranges.  
- **Practicality:** Fails on official tests; TL/ML errors.

> *Avoid BFS – it’s the “ugly” of this problem.*

---

## The Optimal Back‑Tracking Algorithm <a name="optimal-back-tracking"></a>

### Core Idea

Instead of growing from `(sx, sy)` outward, shrink from `(tx, ty)` backward to `(sx, sy)`.

- If `tx > ty`, the previous step must have been `(tx - ty, ty)` because the only way to increase `x` is by adding `y`.  
- Symmetrically, if `ty > tx`, the previous step was `(tx, ty - tx)`.  

### Using Modulo to Skip Redundant Steps

Repeated subtraction of the smaller value is equivalent to `tx %= ty` (or `ty %= tx`) when the smaller value is still smaller than the target's counterpart.  
This collapses many steps into one and keeps the loop efficient.

### Termination Conditions

1. **Success:** `tx == sx && ty == sy`  
2. **Impossible:** `tx < sx` or `ty < sy`  
3. **Fast Check:** If `tx == ty`, we can only stop if both start coordinates equal the target or if the other dimension matches after modular reduction.

---

## Step‑by‑Step Implementation <a name="step-by-step-implementation"></a>

```text
while tx >= sx and ty >= sy:
    if tx == ty:   # cannot proceed further; check divisibility
        break
    if tx > ty:
        if ty > sy:
            tx %= ty          # skip many subtractions at once
        else:                 # ty == sy → only one dimension can grow
            return (tx - sx) % ty == 0
    else:  # ty > tx
        if tx > sx:
            ty %= tx
        else:
            return (ty - sy) % tx == 0
return tx == sx and ty == sy
```

---

## Code: Java, Python, C++ <a name="code"></a>

### Java (O(1) space)

```java
// LeetCode 780 - Reaching Points
public class Solution {
    public boolean reachingPoints(int sx, int sy, int tx, int ty) {
        while (tx >= sx && ty >= sy) {
            if (tx == ty) break;
            if (tx > ty) {
                if (ty > sy) tx %= ty;
                else return (tx - sx) % ty == 0;
            } else {
                if (tx > sx) ty %= tx;
                else return (ty - sy) % tx == 0;
            }
        }
        return tx == sx && ty == sy;
    }
}
```

### Python (Python 3)

```python
# LeetCode 780 - Reaching Points
class Solution:
    def reachingPoints(self, sx: int, sy: int, tx: int, ty: int) -> bool:
        while tx >= sx and ty >= sy:
            if tx == ty:
                break
            if tx > ty:
                if ty > sy:
                    tx %= ty
                else:
                    return (tx - sx) % ty == 0
            else:
                if tx > sx:
                    ty %= tx
                else:
                    return (ty - sy) % tx == 0
        return tx == sx and ty == sy
```

### C++ (C++17)

```cpp
// LeetCode 780 - Reaching Points
class Solution {
public:
    bool reachingPoints(int sx, int sy, int tx, int ty) {
        while (tx >= sx && ty >= sy) {
            if (tx == ty) break;
            if (tx > ty) {
                if (ty > sy) tx %= ty;
                else return (tx - sx) % ty == 0;
            } else {
                if (tx > sx) ty %= tx;
                else return (ty - sy) % tx == 0;
            }
        }
        return tx == sx && ty == sy;
    }
};
```

---

## Time & Space Complexity <a name="complexity"></a>

| Language | Time | Space |
|----------|------|-------|
| Java     | **O(log (max(tx,ty)))** | **O(1)** |
| Python   | **O(log (max(tx,ty)))** | **O(1)** |
| C++      | **O(log (max(tx,ty)))** | **O(1)** |

*The logarithmic factor comes from the repeated modulo operations, which drastically reduce the target coordinates.*

---

## Edge Cases & Common Mistakes <a name="edge-cases"></a>

| Edge Case | Why It Matters | Fix |
|-----------|----------------|-----|
| `sx == tx && sy == ty` | Already at the target | Return `true` immediately |
| `tx < sx` or `ty < sy` | Impossible to grow backwards | Loop will terminate; final check returns `false` |
| `tx == ty` but not equal to start | No further move possible | Break loop and perform divisibility check |
| Large inputs near `10^9` | Potential overflow if using subtraction | Use modulo to avoid many subtractions |

---

## The Good, The Bad, The Ugly <a name="good-bad-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Idea** | Elegant reverse‑tracking reduces complexity from exponential to logarithmic | Requires careful handling of divisibility checks | None if you fall back to BFS |
| **Code Complexity** | ~20 lines of clean, readable code | None | None |
| **Performance** | Works for all `10^9` limits | None | BFS will TLE/MLE |
| **Readability** | Modular arithmetic is intuitive for experienced coders | Some may find modulo magic obscure | Recursive solutions can overflow stack |

> *The “ugly” here is mainly the initial misstep of trying BFS. Once you reverse‑track, the solution becomes a joy to read.*

---

## Final Thoughts & Interview Tips <a name="final-thoughts"></a>

1. **Explain the reverse reasoning first.** Interviewers love to hear the thought process.  
2. **Show the modulo trick.** It demonstrates algorithmic maturity.  
3. **Discuss edge cases.** Talk about when `tx == ty`, or when one coordinate equals its start value.  
4. **Mention complexity.** Be ready to explain why your solution is `O(log n)` and not exponential.  

> *Mastering this problem is a solid “proof‑of‑concept” that you can solve hard interview questions with clean, efficient code.*

---

## References & Further Reading <a name="references"></a>

- LeetCode Problem 780: <https://leetcode.com/problems/reaching-points/>  
- Editorial & Solutions (Java/Python/C++):  
  - <https://leetcode.com/problems/reaching-points/solutions/114732/java-simple-solution-with-explanation-by-vb8h/>  
  - <https://leetcode.com/problems/reaching-points/solutions/1774665/best-and-obvious-solutions-by-singhjp006-i2vk/>  
  - <https://leetcode.com/problems/reaching-points/solutions/5204364/java-beats-100-by-deleted_user-aif0/>  

---

### 🚀 Boost Your Interview Score  
> Ready to impress your hiring managers? Practice this problem, share your solution on GitHub, and tag it with **#LeetCode780 #AlgorithmDesign**. Good luck!

--- 

*SEO Keywords: LeetCode 780, Reaching Points solution, Java, Python, C++, algorithm interview, technical interview, job interview tips, coding interview, BFS vs back‑tracking.*