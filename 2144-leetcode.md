---
title: LeetCode 2144. Minimum Cost of Buying Candies With Discount - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 2144

> **Title** – Minimum Cost of Buying Candies With Discount  
> **Difficulty** – Easy  
> **Tag** – Array, Greedy  

A shop sells candies.  
For every **two** candies you buy, the shop gives you **one** additional candy for free.  
You may choose which candy is free, but **its cost must not exceed the cheaper of the two candies you paid for**.

Given an array `cost[]` where `cost[i]` is the price of the *i‑th* candy, compute the *minimum total amount* you have to pay to obtain **all** candies.

---

## 2.  Core Idea – Greedy + Sorting

The optimal strategy is:

1. **Sort the costs in non‑increasing order** (`high → low`).  
2. **Buy the first two candies (the most expensive ones)** and skip the third one (free).  
3. **Repeat**: take the next two expensive candies, skip the next, and so on.

Why does this work?  
If you pair two expensive candies, the free candy is guaranteed to be *cheaper* than the cheaper of the two paid ones (because all later candies are even cheaper).  
Any other pairing can only increase the amount you pay because you would be paying for cheaper candies while potentially getting a more expensive candy for free—violating the “free candy ≤ min(bought candies)” rule.

---

## 3.  Algorithm

```
sort(cost) descending
total = 0
for i = 0; i < cost.length; i += 3:
        total += cost[i]          // most expensive of the group
        if i+1 < cost.length:
              total += cost[i+1] // second most expensive of the group
        // i+2 (the cheapest) is free
return total
```

**Complexities**

| Complexity | Explanation |
|------------|-------------|
| `O(n log n)` | Sorting dominates |
| `O(1)` | Only a few integer variables are used |

With `n ≤ 100`, this is far more than fast enough.

---

## 4.  Reference Implementations

Below are clean, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

> **Tip** – All three snippets use the same greedy idea; the only differences are syntax and standard library calls.

### 4.1 Java (LeetCode style)

```java
import java.util.Arrays;

class Solution {
    public int minimumCost(int[] cost) {
        // Sort in decreasing order
        Arrays.sort(cost);
        int n = cost.length;
        int total = 0;

        // Traverse in steps of 3
        for (int i = n - 1; i >= 0; i -= 3) {
            total += cost[i];          // most expensive in this group
            if (i - 1 >= 0) {
                total += cost[i - 1];  // second most expensive
            }
            // The candy at i-2 is free
        }
        return total;
    }
}
```

### 4.2 Python

```python
class Solution:
    def minimumCost(self, cost: List[int]) -> int:
        # Sort descending
        cost.sort(reverse=True)
        total = 0

        # Pick two, skip one
        for i in range(0, len(cost), 3):
            total += cost[i]                 # most expensive
            if i + 1 < len(cost):
                total += cost[i + 1]         # second most expensive
        return total
```

### 4.3 C++

```cpp
class Solution {
public:
    int minimumCost(vector<int>& cost) {
        // Sort descending
        sort(cost.rbegin(), cost.rend());   // rbegin/rend = reverse iterators
        int total = 0;
        for (int i = 0; i < cost.size(); i += 3) {
            total += cost[i];               // most expensive
            if (i + 1 < cost.size())
                total += cost[i + 1];       // second most expensive
            // i+2 is free
        }
        return total;
    }
};
```

> **Why `rbegin()/rend()`?**  
> It gives you a descending view of the vector without allocating a copy.

---

## 5.  Blog Article – “The Good, the Bad, and the Ugly” of the Candies Discount Problem

> **SEO Keywords**: *Minimum Cost of Buying Candies With Discount*, *LeetCode 2144 solution*, *greedy algorithm*, *candies discount problem*, *Java Python C++ code*, *coding interview problem*, *array sorting*, *dynamic programming*, *algorithm interview*

---

### 5.1 Introduction

In a world where **coding interviews** are the gatekeepers of your next job, problems that blend *array manipulation* and *greedy thinking* are ubiquitous. LeetCode 2144, “Minimum Cost of Buying Candies With Discount,” is a textbook example of how a seemingly simple retail promotion turns into a neat coding challenge.

Let’s dissect this problem, explore the pros and cons of various strategies, and uncover the pitfalls that may trip up even seasoned interviewees.

---

### 5.2 The Problem in Plain English

You own a candy‑shop that has a quirky offer: *Buy any two candies and you can take one more candy for free, but that free candy cannot be more expensive than the cheaper of the two you bought*.  
Given a list of candy prices, how do you pay the least amount to get **every** candy?

---

### 5.3 The “Good”: A Simple Greedy Solution

1. **Sort the prices descending** – so we always consider the most expensive candies first.  
2. **Take the first two as paid** – they’re the most expensive and satisfy the free‑candy rule.  
3. **Skip the third** – it’s guaranteed to be cheaper than the second paid candy.  
4. **Repeat** until all candies are processed.

Why is this the *good* part?

| Benefit | Explanation |
|---------|-------------|
| **Time‑efficient** | Sorting dominates: `O(n log n)`. With `n ≤ 100`, that’s instant. |
| **Space‑cheap** | Only a few integers. |
| **Easy to reason** | The greedy choice is locally optimal and, due to the ordering, also globally optimal. |
| **Versatile** | Works for arrays of any size (1 to 100). |

---

### 5.4 The “Bad”: What Can Go Wrong

| Pitfall | Why it fails |
|---------|--------------|
| **Skipping the sort** | Without ordering, you might pair a cheap candy with a moderately expensive one, leaving a *more expensive* candy to be paid later. |
| **Off‑by‑one errors** | In the loop, forgetting to check bounds for the second candy (`i + 1 < n`) can throw an exception for odd‑length arrays. |
| **Assuming the free candy is always the cheapest remaining** | That’s only guaranteed after sorting descending. In unsorted arrays, the third candy might still be expensive enough to violate the rule. |
| **Using an expensive data structure** | E.g., a `PriorityQueue` that removes items individually is unnecessary and slows the solution. |

Avoiding these mistakes turns a solid greedy solution into a fragile one.

---

### 5.5 The “Ugly”: Alternative Approaches and Why They’re a Bad Idea

1. **Dynamic Programming** – One might try to treat it as a *knapsack‑style* problem.  
   *Ugly because* the state space explodes (`2^n` possibilities) and the problem’s constraints (100 items) make it overkill.

2. **Brute‑Force Pairing** – Enumerate all possible pairings of candies.  
   *Ugly because* the number of pairings is factorial in `n`, far beyond feasible limits.

3. **Greedy with Wrong Sorting (ascending)** – Buying the cheapest candies first seems intuitive.  
   *Ugly because* you end up paying more: the expensive candies end up as free items, but you’re still forced to pay for them later when you pair them with even cheaper ones.

In short, the simple *sort‑and‑take‑two‑skip‑one* strategy is the only clean, efficient, and correct approach for this problem.

---

### 5.6 Implementation Highlights

| Language | Key Points |
|----------|------------|
| **Java** | Use `Arrays.sort(cost)` and iterate from the end (most expensive). |
| **Python** | `cost.sort(reverse=True)`; `for i in range(0, len(cost), 3)`. |
| **C++** | `sort(cost.rbegin(), cost.rend());` and iterate with `i += 3`. |

All three versions run in the same `O(n log n)` time and `O(1)` extra space, making them ideal for interviews.

---

### 5.7 Edge Cases to Test

| Test | Explanation |
|------|-------------|
| `[1]` | Only one candy, no discount. |
| `[5, 5]` | Two candies, no free candy. |
| `[1, 2, 3]` | Classic example from the problem. |
| `[9, 7, 6, 5, 2, 2]` | Mixed values; ensures correct grouping. |
| `[10, 9, 8, 7, 6, 5, 4]` | Odd number of candies; last candy must be paid. |

Writing unit tests for these cases guarantees robustness.

---

### 5.8 Final Thoughts – Why This Matters

Mastering LeetCode 2144 demonstrates:

- **Greedy thinking**: Recognizing that the most expensive candies should be paid first.  
- **Array manipulation**: Sorting and stepping through the array in fixed strides.  
- **Attention to detail**: Handling off‑by‑one boundaries correctly.  

These skills translate directly to real‑world coding interviews. They also help you become more efficient in production code where *sorting* and *greedy selection* are common patterns (e.g., memory allocation, job scheduling).

---

## 6.  Ready to Impress

- **Show off the solution** in Java, Python, and C++ during your interview.  
- **Explain the greedy rationale** clearly – that’s what interviewers love.  
- **Discuss edge cases** and why your code is safe.  

With this knowledge, you’ll tackle LeetCode 2144—and similar greedy problems—with confidence. Good luck in your next coding interview!