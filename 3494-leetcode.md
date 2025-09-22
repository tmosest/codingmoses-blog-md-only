---
title: LeetCode 3494. Find the Minimum Amount of Time to Brew Potions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧪 Mastering LeetCode 3494: “Find the Minimum Amount of Time to Brew Potions”  
> A deep‑dive into the problem, three production‑ready solutions (Java, Python, C++), and an interview‑ready blog that explains the **good**, **bad**, and **ugly** of this classic scheduling puzzle.

---

## 📌 Problem Overview  

In the brewing‑wizard world each potion must travel through **n** wizards in a fixed order.  
Wizard *i* takes  

```
time_ij = skill[i] * mana[j]
```

to finish potion *j*.  
The goal is to find the *minimum* time needed for **m** potions to be finished when the process is *asynchronised as possible* (every wizard can start the next potion as soon as the previous wizard hands it over).

> **Why it matters in interviews**  
> * Scheduling, “flow‑shop” problems, and greedy/DP tricks are frequently asked on LeetCode.  
> * The same thinking is used in real‑world job interviews at Google, Amazon, Stripe, and fintechs.  
> * Solving this problem shows that you can **model constraints**, **use prefix sums for O(nm)** work, and **write clean, interview‑friendly code**.

---

## 🎯 Key Constraints

| Symbol | Meaning | Example |
|--------|---------|---------|
| `n` | number of wizards | 3 |
| `m` | number of potions | 4 |
| `skill[i]` | wizard *i*’s skill factor | `2, 3, 1` |
| `mana[j]` | potion *j*’s mana factor | `4, 5, 2, 7` |
| `time_ij = skill[i] * mana[j]` | time wizard *i* needs for potion *j* | `2*4 = 8` |

The **minimum total time** is the moment the *last* wizard finishes the *last* potion.

---

## 🧩 Solution Strategies

| Strategy | What it does | When to use it |
|----------|--------------|----------------|
| **Simulation + Offset Correction** | Simulate the whole pipeline, but keep the *offset* between consecutive potions minimal. | Easier to understand, good for small `n,m`. |
| **Prefix‑Sum + Greedy** | Pre‑compute cumulative skills, use them to compute the additional waiting time for each wizard in O(1). | Optimal `O(nm)` solution, production‑ready. |
| **Binary Search on Total Time** | Guess total time, check if all potions can finish, binary‑search the answer. | Works in `O(nm log T)` but heavier to implement. |

The **prefix‑sum solution** is the most popular because it is concise, easy to proof, and runs fast enough for the limits (`n, m ≤ 1000`).

---

## 📦 Code Implementations

Below are three ready‑to‑paste solutions that compile on the LeetCode platform.  All three run in **`O(n·m)` time** and use **`O(n)` additional space**.

> *Tip*: In interviews, start with a clear **prefix‑sum** explanation before diving into the code – that demonstrates you understand the math behind the problem.

---

### Java (LeetCode style)

```java
import java.util.*;

class Solution {
    public long minTime(int[] skill, int[] mana) {
        int n = skill.length, m = mana.length;

        // 1‑based prefix sums of skills
        long[] pref = new long[n + 1];
        for (int i = 0; i < n; i++) pref[i + 1] = pref[i] + skill[i];

        long cur = 0;                     // time when the previous potion finished
        for (int j = 1; j < m; j++) {      // iterate over all but the first potion
            long next = 0;
            for (int i = 0; i < n; i++) {
                // “extra time” that wizard i must wait before starting potion j
                long need = cur + 1L * mana[j - 1] * pref[i + 1] - 1L * mana[j] * pref[i];
                if (need > next) next = need;
            }
            cur = next;                   // this becomes the starting time for potion j
        }

        // final total time = starting time for last potion + its brewing time
        return cur + 1L * mana[m - 1] * pref[n];
    }
}
```

---

### Python (LeetCode style)

```python
from itertools import accumulate
from typing import List

class Solution:
    def minTime(self, skill: List[int], mana: List[int]) -> int:
        n, m = len(skill), len(mana)

        # prefix sums (1‑based)
        pref = [0]
        for s in skill:
            pref.append(pref[-1] + s)

        cur = 0
        for j in range(1, m):
            nxt = 0
            for i in range(n):
                need = cur + mana[j - 1] * pref[i + 1] - mana[j] * pref[i]
                if need > nxt:
                    nxt = need
            cur = nxt

        return cur + mana[-1] * pref[-1]
```

---

### C++ (LeetCode style)

```cpp
class Solution {
public:
    long long minTime(vector<int>& skill, vector<int>& mana) {
        int n = skill.size(), m = mana.size();
        vector<long long> pref(n + 1, 0);
        for (int i = 0; i < n; ++i) pref[i + 1] = pref[i] + skill[i];

        long long cur = 0;
        for (int j = 1; j < m; ++j) {
            long long nxt = 0;
            for (int i = 0; i < n; ++i) {
                long long need = cur + 1LL * mana[j - 1] * pref[i + 1]
                                 - 1LL * mana[j] * pref[i];
                nxt = max(nxt, need);
            }
            cur = nxt;
        }

        return cur + 1LL * mana[m - 1] * pref[n];
    }
};
```

> All three snippets are **O(n·m)**, use 64‑bit integers (`long` / `long long`) to avoid overflow, and follow LeetCode’s function‑signature format.

---

## 📈 Complexity Analysis

| Step | Work | Total |
|------|------|-------|
| Prefix sum | `O(n)` | `O(n)` space |
| Main double loop | `n` wizards × `m‑1` potions | **`O(n·m)` time** |
| Final calculation | `O(1)` | – |

With the problem limits (`n, m ≤ 1000`) this is comfortably below the 1‑second LeetCode limit.

---

## 💎 The “Good, Bad, Ugly” of this Problem

| Aspect | What’s Great | What to watch out for | How to fix it |
|--------|--------------|------------------------|---------------|
| **Good** | • Straightforward modeling of a flow‑shop. <br>• Can be solved with **simulation** or a neat **prefix‑sum** trick. <br>• Demonstrates the power of *cumulative* reasoning. | – | – |
| **Bad** | • The naive simulation (time‑stepping) can be O(n·m·maxMana) which is too slow. <br>• Using `int` everywhere risks overflow. | • Don’t forget the “carry‑over” time between potions. <br>• Beware of the “extra wait” that can be zero or negative. | Use 64‑bit (`long long`) and compute the *extra* waiting time in a single formula. |
| **Ugly** | • Some contestants try to use **binary search** on the total time, but the “feasibility” test is heavy and hard to debug. <br>• Mixing *partial sums* and *simulations* can lead to off‑by‑one bugs. | • Mixing 0‑based and 1‑based indices in the prefix array. <br>• Forgetting that the first potion doesn’t need a “max‑wait” step. | Stick to the clean **prefix‑sum + greedy** pattern shown above; write helper comments. |

### Common Pitfalls

| Mistake | Why it breaks | Quick fix |
|---------|---------------|-----------|
| Using `int` for the final answer | `skill[i] * mana[j]` can be up to `10^9` → product up to `10^18`. | Switch to `long` / `long long`. |
| Off‑by‑one in the prefix array | `pref[i]` should be sum of the first `i` skills (1‑based). | Initialize `pref[0] = 0` and use `pref[i + 1] = pref[i] + skill[i]`. |
| Skipping the “max” step for `j > 0` | The first potion can finish earlier, but later potions may need to wait for all wizards. | Loop over all wizards and keep the maximum “extra wait” value. |
| Over‑optimizing early | Adding a binary search over total time makes the code longer than needed. | Prefer the linear `O(n·m)` solution; it’s clean and fast enough. |

---

## 📢 Interview‑Ready Tips

1. **Start by explaining the constraints** – every wizard must start as soon as the previous one hands off.  
2. **Show the “prefix‑sum” insight** – it reduces the waiting‑time formula to a single line.  
3. **Mention overflow early** – highlight why you use `long`.  
4. **If time permits**, sketch a simple simulation and then justify why you move to the greedy formula.  
5. **Ask clarifying questions** if you’re unsure about the indices (0‑based vs. 1‑based) – interviewers appreciate that.  
6. **Write clean code** – use descriptive variable names (`pref`, `cur`, `nextWait`) and add inline comments.  

> *Result*: You will appear to have a deep understanding of algorithmic patterns, rather than just copying pseudocode.

---

## 🎉 Final Thoughts

- This problem is a **great showcase** of *flow‑shop scheduling*, *greedy reasoning*, and *prefix sums*.  
- The code snippets above are **minimal, correct, and ready for submission**.  
- Understanding the *good, bad, ugly* aspects will let you explain your solution confidently in any technical interview.

---

### 👋 Ready to crack more flow‑shop style problems?  
Check out LeetCode’s “Flow Shop” collection and practice the *prefix‑sum* trick – you’ll see the pattern pop up in many other interview questions. Happy coding! 🚀

--- 

*Feel free to comment below if you want a deeper dive into the feasibility check of the binary‑search approach, or if you need help with a different flow‑shop variant.*