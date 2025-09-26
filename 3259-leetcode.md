---
title: LeetCode 3259. Maximum Energy Boost From Two Drinks - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🏁 1. Quick‑start Code (Java / Python / C++)

```java
/*  Java 17  */
public class Solution {
    public long maxEnergyBoost(int[] energyDrinkA, int[] energyDrinkB) {
        long a = 0, b = 0;              // best total if we finish on A / B
        for (int i = 0; i < energyDrinkA.length; i++) {
            long newA = Math.max(a + energyDrinkA[i], b);
            long newB = Math.max(b + energyDrinkB[i], a);
            a = newA;  b = newB;
        }
        return Math.max(a, b);
    }
}
```

```python
#  Python 3.10
def max_energy_boost(energyDrinkA: list[int], energyDrinkB: list[int]) -> int:
    a, b = 0, 0                     # best sum ending on A / B
    for a_val, b_val in zip(energyDrinkA, energyDrinkB):
        new_a = max(a + a_val, b)
        new_b = max(b + b_val, a)
        a, b = new_a, new_b
    return max(a, b)
```

```cpp
//  C++17
class Solution {
public:
    long long maxEnergyBoost(vector<int>& energyDrinkA,
                            vector<int>& energyDrinkB) {
        long long a = 0, b = 0;
        for (size_t i = 0; i < energyDrinkA.size(); ++i) {
            long long newA = max(a + energyDrinkA[i], b);
            long long newB = max(b + energyDrinkB[i], a);
            a = newA; b = newB;
        }
        return max(a, b);
    }
};
```

All three solutions run in **O(n)** time and use **O(1)** extra space (besides the input arrays).

---

## 📖 2. Blog‑style Interview Guide

### 📌 Title  
**“Mastering LeetCode #1662: Maximum Energy Boost From Two Drinks – A DP‑in‑O(1) Space Tutorial (Java, Python, C++)”**

### 📑 Why this matters for your next interview  
- **LeetCode** problems like *Maximum Energy Boost From Two Drinks* are a staple in coding interviews.  
- Demonstrating a clean, optimal solution shows you understand **Dynamic Programming**, **time/space trade‑offs**, and can write production‑ready code in **Java, Python, or C++**.  
- The article is optimized for recruiters searching “Dynamic Programming interview questions”, “LeetCode solutions”, “Python DP interview”, etc.  

---

### 🔍 Problem Recap

> You have two arrays `A` and `B` of equal length `n`.  
> In each hour you can **drink** one of the two drinks.  
> If you switch the drink type (from A to B or vice‑versa) you must **skip the next hour** (i.e., you drink at hour *i*, then take a break at *i+1*, and can drink the other type at *i+2*).  
> Maximize the total energy gained.

---

### ✅ 2.1 What Makes the Problem “Good”

| ✅ Good | Description |
|---------|-------------|
| **Linear input** | Only one pass over the arrays is needed. |
| **Clear greedy DP** | You can think of it as a “keep‑on” vs “switch” decision at each hour. |
| **Easy to implement** | The O(1) space version uses just two running totals. |

---

### ⚠️ 2.2 Common Pitfalls (“Bad”)

| ⚠️ Bad | Why it fails |
|--------|--------------|
| **O(n²) brute force** | Trying every subset of hours is exponential. |
| **O(n) DP but using a full 2‑D array** | Works but wastes memory (≈ 2 × n × 8 bytes). |
| **Ignoring the “skip one hour” rule** | Many naive solutions treat the decision as “drink A vs drink B” only, missing the mandatory rest after a switch. |
| **Using 32‑bit int for the result** | Summing up to `10⁵` elements each up to `10⁶` can overflow a 32‑bit integer. Use `long`/`int64`. |

---

### 🚀 2.3 The “Good” Algorithm – O(1) Space DP

We maintain two variables:

| Variable | Meaning |
|----------|---------|
| `a` | Maximum energy that can be collected **if we finish the current hour drinking A**. |
| `b` | Same but finished on B. |

When we look at hour `i`, there are two possibilities for each drink:

| Action | New value |
|--------|-----------|
| **Keep the same drink** | Add current hour’s value to the same‑drink total (`a + A[i]` or `b + B[i]`). |
| **Switch drinks** | Take the *other* drink’s total (which already includes the mandatory rest) and add 0 (because we drink **at hour i** but we **skip hour i‑1**). In practice this translates to `max(a + A[i], b)` for finishing on A and `max(b + B[i], a)` for finishing on B. |

The recurrence is:

```
newA = max(a + A[i], b)
newB = max(b + B[i], a)
```

After updating `a` and `b` for every hour, the answer is `max(a, b)`.

This formulation is a classic “rolling DP” and is **optimal**:

- **Time complexity:** `O(n)` – one pass over the arrays.  
- **Space complexity:** `O(1)` – only two 64‑bit counters.

---

### 🧠 2.4 Why the Recurrence Works

Consider the optimal strategy up to hour `i`.

*If we drink A at hour `i`:*  
- Either we **kept** drinking A in the previous hour – total `a + A[i]`.  
- Or we **switched** from B at hour `i-1` (which forces us to have rested at `i-1`) – total `b` (the best ending on B up to hour `i-1`).

The maximum of these two gives `newA`.  
The same logic applies for `newB`.  
Because each state only depends on the immediately preceding two states (`a` and `b`), we can discard older values – that’s the “constant‑space” property.

---

### 🔍 2.5 Edge‑Case Checklist

| Edge case | What to watch out for | How our code handles it |
|-----------|-----------------------|------------------------|
| `n = 1` | Only one drink can be taken. | The loop runs once; `newA = max(0 + A[0], 0) = A[0]`. |
| `A[i] = B[i]` | Symmetric case – any strategy is fine. | The algorithm naturally keeps both totals equal. |
| Large numbers (`10⁶` × `10⁵`) | 32‑bit overflow. | All solutions use 64‑bit (`long` in Java, `int` in Python 3, `long long` in C++). |
| All zeros | Result zero. | The algorithm outputs 0. |

---

### 📈 2.6 Variations You Might Hear in Interviews

1. **Multiple drink types** – extend the DP to `k` states.  
2. **Cost for switching** – add a penalty instead of a mandatory rest.  
3. **Circular schedule** – first and last hour are adjacent.  

In every case the core idea is the same: **“keep same drink” vs “switch drink”** decisions, stored in a small rolling table.

---

## 📜 3. SEO‑Friendly Blog Article

### Title  
**“How to Crack LeetCode 1662 – Maximum Energy Boost From Two Drinks (Java, Python, C++)”**

### Meta Description  
> Learn the optimal O(n) DP solution for LeetCode 1662 “Maximum Energy Boost From Two Drinks”. See Java, Python, and C++ implementations, time & space analysis, interview pitfalls, and how mastering this problem can boost your coding interview performance.

### Table of Contents  

1. [Problem Overview](#problem-overview)  
2. [Intuitive Insight](#intuitive-insight)  
3. [Dynamic Programming Formulation](#dp-formulation)  
4. [Rolling‑Array (O(1) Space) Implementation](#rolling-array)  
5. [Complexity & Trade‑offs](#complexity)  
6. [Common Mistakes & How to Avoid Them](#mistakes)  
7. [Follow‑Up Variants](#follow-up)  
8. [Interview‑Ready Tips](#interview-tips)  
9. [Wrap‑Up](#wrap-up)  

---

### 📝 3.1 Problem Overview  
> **Maximum Energy Boost From Two Drinks** (LeetCode #1662) challenges you to maximize the sum of values from two parallel arrays when you can drink one per hour but must rest an hour after a type switch. Think of two energy‑boosting drinks A and B, each with an hourly value, and a rule: *switching costs a rest hour*.  

**Why it matters**  
- It tests your understanding of **dynamic programming** and **state management**.  
- The same pattern appears in other interview questions like “House Robber” or “Maximum Subsequence Sum with Skips”.  
- A clean, O(1) space solution is a brag‑worthy answer on your résumé.

---

### 🔍 3.2 Intuitive Insight  
Imagine you’re standing at hour `i`.  
You have two options:

| Option | What happens | How it affects your total? |
|--------|--------------|-----------------------------|
| **Keep the same drink** | Drink A/B and move to `i+1`. | Add `A[i]` (or `B[i]`) to the total ending on the same drink. |
| **Switch drinks** | Drink A/B, then **rest** at `i+1` (no drink), and can drink the other type at `i+2`. | The rest means you can’t add any energy for `i+1`; the next contribution is `0`. But you’ve already “took a break”, so the other drink’s total is already valid. |

The catch: when you switch, you *cannot* use the total that **just drank the other type in the previous hour** because you’d still be resting.  
Hence, after a switch the “fresh” total comes from the **other drink’s best up to hour `i-1`**.

---

### 📐 3.3 Dynamic Programming Formulation  
Let  

```
dpA[i] – max energy till hour i, finishing with drink A
dpB[i] – same but finishing with drink B
```

Recurrence:

```
dpA[i] = max(dpA[i-1] + A[i],   dpB[i-1])   // keep or switch
dpB[i] = max(dpB[i-1] + B[i],   dpA[i-1])   // keep or switch
```

Because each `dp*` only looks at the two previous values, we can **roll** the DP:

```
newA = max(a + A[i], b)
newB = max(b + B[i], a)
```

where `a` and `b` are the previous `dpA` and `dpB`.  
After processing all hours, answer = `max(a, b)`.

---

### 📦 3.4 Rolling‑Array (O(1) Space) Implementation  
*Show code snippets for Java, Python, and C++.*  

```java
long a = 0, b = 0;
for (int i = 0; i < n; i++) {
    long newA = Math.max(a + A[i], b);
    long newB = Math.max(b + B[i], a);
    a = newA; b = newB;
}
return Math.max(a, b);
```

The same pattern appears in Python (`int` is arbitrary‑precision) and C++ (`long long`).  

**Why O(1) space?**  
- `dpA` and `dpB` only depend on the *latest* totals.  
- We discard older states, saving memory and making the algorithm elegant.

---

### ⏱️ 3.5 Complexity & Trade‑offs  
| Metric | Value | Why it’s acceptable? |
|--------|-------|-----------------------|
| **Time** | `O(n)` | One linear scan, ideal for interviewers. |
| **Space** | `O(1)` | Only two 64‑bit integers – perfect for large inputs. |
| **Alternative** | Full 2‑D array: `O(n)` time, `O(n)` space | Works but unnecessary; use the rolling version to impress. |

---

### ❌ 3.6 Common Mistakes & How to Avoid Them  

1. **Omitting the rest hour** – leads to incorrect states.  
   *Fix:* Always consider the “switch” path (`max(a + A[i], b)` instead of `max(a + A[i], b + B[i])`).  
2. **Using 32‑bit integer for the sum** – risk of overflow.  
   *Fix:* Use `long`/`long long`.  
3. **Brute‑forcing all combinations** – exponential time.  
   *Fix:* Recognize the problem as a 1‑D DP with two states.  

---

### 📘 3.7 Follow‑Up Variants  

| Variant | How the solution changes | Implementation hint |
|---------|--------------------------|---------------------|
| **More than two drinks** | DP with `k` states; use a small array of size `k`. | `vector<long long> dp(k, 0);` roll it. |
| **Cost for switching** | Add penalty `p` to `newA`/`newB`. | `newA = max(a + A[i], b - p)` etc. |
| **Circular array** | First hour follows last hour. | Perform DP twice: once assuming we start with A, once with B; pick the max while handling wrap‑around. |

---

### 🎯 3.8 Interview‑Ready Tips  

| Tip | Explanation |
|-----|-------------|
| **State‑diagram first** | Before coding, draw two nodes (A, B) and annotate transitions. |
| **Explain the “skip hour” rule** | Clarify how it eliminates certain transitions. |
| **Discuss time/space** | Recruiters like to see you discuss both. |
| **Show the O(1) trick** | Mention “rolling DP” and why it works. |
| **Test on edge cases** | Always walk through `n = 1`, all zeros, large numbers. |
| **Use 64‑bit** | Avoid accidental overflow; mention it in your solution notes. |

---

### 🚀 3.9 Wrap‑Up  

Mastering **Maximum Energy Boost From Two Drinks** demonstrates that you can:

- Translate a real‑world constraint (rest after a switch) into a clean DP recurrence.  
- Optimize for both time and space.  
- Implement the same logic in your language of choice.

Use the snippets above as your reference or drop them into your GitHub repository under `leetcode/1662`. When the next recruiter asks about LeetCode DP questions, you’ll have a polished answer ready to shine.

Happy coding! 🚀

--- 

### 🔗 Resources  

- LeetCode Problem 1662 – [link](https://leetcode.com/problems/maximum-energy-boost-from-two-drinks/)  
- “House Robber” – similar DP with skip rule.  
- *Dynamic Programming – Hard & Medium* playlist on YouTube for visual learners.  

--- 

**Happy Interviewing!** 🏆

--- 

### 📞 Need Help?  
Reach out via LinkedIn or Twitter, or book a one‑on‑one prep session with a senior mentor.  

--- 

*End of article.*  

--- 

## 🎉 4. Final Thoughts  

- The linear DP recurrence is the heart of the solution; the O(1) space rolling version is both elegant and interview‑friendly.  
- The three code snippets provide you with ready‑to‑copy, production‑grade code in the languages most recruiters love.  
- Use this knowledge to showcase **state‑based thinking**, **space optimization**, and **problem‑domain modeling** on your next coding interview or in your résumé.

--- 

Happy coding! 🚀