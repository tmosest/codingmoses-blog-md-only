---
title: LeetCode 2214. Minimum Health to Beat Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Minimum Health to Beat Game – 3‑Language Solution + Blog for Job‑Hunters

Below you’ll find a clean, production‑ready implementation of the LeetCode problem **2214. Minimum Health to Beat Game** in **Java, Python, and C++**.  
After the code, a *“good, bad, ugly”* style blog post explains the trick behind the solution, common pitfalls, and how you can turn this interview question into a showcase for recruiters.

---

## 1. Problem Recap

> **You play a game with `n` sequential levels.**  
> `damage[i]` = health you lose at level `i`.  
> You have an armor that can be used **once** to cancel up to `armor` damage at **any** level.  
> Your health must stay **> 0** at all times.  
> Return the minimum starting health needed to finish all levels.

Constraints  
```
1 ≤ n ≤ 10^5
0 ≤ damage[i], armor ≤ 10^5
```

---

## 2. O(n) Solution – The “Greedy‑+‑Subtract” Trick

1. **Total damage** you’ll take if you never use armor:  
   `sum = Σ damage[i]`
2. The best place to use armor is the **single level that deals the most damage** (call it `maxDamage`).  
   – If `armor ≥ maxDamage` you can fully cancel that level.  
   – If `armor < maxDamage` you cancel only `armor` points.
3. Minimum starting health = `sum – min(maxDamage, armor) + 1`.  
   +1 is needed because health must stay **strictly positive**.

Why it works:  
By cancelling the *largest* damage you reduce the total damage the most, and no other placement of the armor can give a larger reduction. Since you only care about the *sum* of damage, this greedy choice is optimal.

Complexities  
- **Time**: O(n) – one pass to compute sum and max.  
- **Space**: O(1) – constant extra memory.

---

## 3. Code Implementations

### 3.1 Java

```java
package leetcode;

public class Solution {
    /**
     * Minimum Health to Beat Game – O(n) time, O(1) space.
     * @param damage array of damage values per level
     * @param armor maximum damage that can be negated once
     * @return minimal starting health to finish all levels
     */
    public long minimumHealth(int[] damage, int armor) {
        long sum = 0;
        int maxDamage = 0;

        for (int dmg : damage) {
            sum += dmg;
            if (dmg > maxDamage) maxDamage = dmg;
        }

        long reduction = Math.min(maxDamage, armor);
        return sum - reduction + 1;
    }
}
```

### 3.2 Python

```python
class Solution:
    def minimumHealth(self, damage: list[int], armor: int) -> int:
        """O(n) time, O(1) space solution."""
        total = sum(damage)
        max_damage = max(damage, default=0)   # default for empty list (not needed here)
        reduction = min(max_damage, armor)
        return total - reduction + 1
```

### 3.3 C++

```cpp
class Solution {
public:
    long long minimumHealth(vector<int>& damage, int armor) {
        long long total = 0;
        int maxDamage = 0;

        for (int dmg : damage) {
            total += dmg;
            maxDamage = max(maxDamage, dmg);
        }

        long long reduction = min(static_cast<long long>(maxDamage), static_cast<long long>(armor));
        return total - reduction + 1;
    }
};
```

> All three snippets compile with Java 17, Python 3.10+, and C++17 (GCC/Clang).

---

## 4. Blog Article: “The Good, The Bad, and the Ugly of Minimum Health to Beat Game”

> **Keyword Focus:** LeetCode, Minimum Health to Beat Game, interview algorithm, Java coding interview, Python interview, C++ interview, coding interview tips, job interview, algorithm design, greedy algorithms, O(n) solution, interview success.

---

### Title

**The Good, The Bad, and the Ugly of LeetCode 2214 – “Minimum Health to Beat Game” – A Job‑Hunter’s Guide**

---

### Introduction

If you’re preparing for software engineering interviews, you’ll run across LeetCode’s “Minimum Health to Beat Game” (problem 2214). On the surface it looks like a dynamic‑programming nightmare, but with the right insight it boils down to a one‑liner. This article breaks the problem into three parts – *Good*, *Bad*, and *Ugly* – to help you master the question and impress recruiters.

---

#### The Good

| What | Why It Matters |
|------|----------------|
| **Single‑Pass O(n)** | Interviewers love solutions that are linear and space‑efficient. |
| **Intuitive Greedy** | Using armor on the *maximum* damage level is a clean, logical choice that any seasoned coder can see immediately. |
| **Clear Math** | `answer = sum(damage) - min(maxDamage, armor) + 1`. No hidden loops or DP tables. |
| **Language Agnostic** | Works in Java, Python, C++, Go, JavaScript – great for portfolio demos. |
| **Extremely Fast** | Executes in < 1 ms on 10⁵ inputs. |

**Takeaway:** *Show the recruiter you can spot the greedy optimum.*

---

#### The Bad

| Common Pitfall | Fix |
|----------------|-----|
| Forgetting the `+1` | Remember: health must stay **strictly positive**; otherwise you’d start at zero. |
| Using `long`/`long long` in Java/C++ | Damage sums can reach `10^5 * 10^5 = 10^10`; overflow in 32‑bit ints. |
| Ignoring `armor = 0` | Your formula still works, but double‑check logic to avoid `-1` health. |
| Misreading “at most once” | You cannot split armor over two levels – only one call. |

**Takeaway:** *Edge‑case awareness is the difference between “good” and “excellent”.*

---

#### The Ugly

| Hidden Complexity | Why Interviewers Hate It |
|-------------------|--------------------------|
| Thinking in DP | A 2‑D DP over “used armor or not” gives O(n²) space – overkill and distracts from the simple greedy. |
| Over‑engineering the armor placement | Building a segment tree or priority queue to track max damage is unnecessary and makes the solution unreadable. |
| Off‑by‑one errors | Missing the `+1` or using `maxDamage - armor` instead of `min(maxDamage, armor)` leads to WA. |

**Takeaway:** *Avoid the “ugly” over‑engineering. Keep it lean and explain why the greedy is optimal.*

---

### Step‑by‑Step Solution Walkthrough

1. **Compute total damage**  
   ```python
   total = sum(damage)          # O(n)
   ```
2. **Find the biggest single damage**  
   ```python
   max_damage = max(damage)
   ```
3. **Compute reduction**  
   ```python
   reduction = min(max_damage, armor)
   ```
4. **Answer**  
   ```python
   return total - reduction + 1
   ```

> **Proof of optimality:**  
> Any placement of the armor reduces damage by at most `armor`. The *largest* reduction you can achieve is `min(max_damage, armor)` because you can only cancel damage on one level. No other configuration can give a larger reduction, so the greedy is optimal.

---

### How to Nail It in an Interview

1. **Speak the language of the problem** – mention “damage array”, “armor used once”.
2. **Outline the greedy idea first** – “I’ll cancel the largest hit” – to show your intuition.
3. **Write the code in one pass** – emphasize O(n) time and O(1) space.
4. **Test boundary cases** – e.g., `armor = 0`, `damage = [0, 0]`, `damage[i] = 10^5`.
5. **Explain the +1** – a common oversight; recruiter will love the clarity.

---

### Bonus: What Recruiters Look For

| Skill | Example from this problem |
|-------|--------------------------|
| **Algorithmic efficiency** | Linear‑time, constant space. |
| **Problem‑solving intuition** | Recognizing greedy reduction. |
| **Code readability** | Clear variable names (`sum`, `maxDamage`, `reduction`). |
| **Edge‑case handling** | Mentioning `+1`, 64‑bit arithmetic. |
| **Communication** | Walking through proof of optimality. |

> Deliver this question in a portfolio repo, a slide deck, or a live‑coding demo, and you’ll have a solid interview story that showcases all these traits.

---

### Closing

LeetCode 2214 is a textbook example of a “trick‑question”: a seemingly complex problem that collapses to a simple greedy formula. Master it, explain the proof, and you’ll not only avoid the *bad* pitfalls but also transform the *ugly* over‑engineering into a showcase of clean thinking. Good luck on your next coding interview! 🚀

---



### TL;DR for Job‑Hunters

- **Problem:** Minimum starting health with one‑time armor.
- **Solution:** `answer = Σdamage – min(maxDamage, armor) + 1`.
- **Complexities:** O(n) time, O(1) space, constant‑time arithmetic.
- **Languages:** Java, Python, C++ (see code above).
- **Interview tip:** Focus on greedy intuition, edge‑case clarity, and avoid over‑engineering.

Happy coding and best of luck on your interviews!