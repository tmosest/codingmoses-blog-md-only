---
title: LeetCode 2907. Maximum Profitable Triplets With Increasing Prices I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem Overview**

You’re given two integer arrays of the same length `n`:

* `prices[i]` – the price of the *i‑th* item  
* `profits[i]` – the profit you would earn if you “take” that item

The items are fixed in the order they appear in the arrays.  
You may pick **exactly three items** that satisfy a *monotonic* price pattern and you want to maximise the total profit of the three items.

---

### 1.  What does “monotonic price pattern” mean?

You have to pick indices `i < j < k` such that the prices are either

* strictly increasing:  
  `prices[i] < prices[j] < prices[k]`

  *or*

* strictly decreasing:  
  `prices[i] > prices[j] > prices[k]`

If **either** of the two patterns can be formed with the three indices, the triple is *valid*.

---

### 2.  What is the goal?

For every valid triple `(i, j, k)` compute

```
totalProfit = profits[i] + profits[j] + profits[k]
```

Return the **maximum** `totalProfit` over **all** valid triples.

If no triple satisfies either pattern, return `-1`.

---

### 3.  Input / Output format

| Input | Type | Description |
|-------|------|-------------|
| `prices` | `int[]` | length `n` (1 ≤ n ≤ 10⁵) |
| `profits` | `int[]` | length `n`, same `n` as `prices` |

| Output | Type | Description |
|--------|------|-------------|
| `int` | maximum total profit or `-1` if impossible |

---

### 4.  Illustrative Example

```text
prices  = [1, 2, 3, 4, 5]
profits = [1, 2, 3, 4, 5]
```

All three patterns are possible.  
One optimal choice is indices `(0, 2, 4)`:

```
prices:  1 < 3 < 5   (strictly increasing)
profits: 1 + 3 + 5 = 9
```

The answer is `9`.

If the arrays were:

```text
prices  = [3, 1, 4]
profits = [5, 2, 7]
```

No three indices can form a strictly increasing *or* strictly decreasing sequence of prices, so the answer is `-1`.

---

### 5.  Key constraints to remember

* `n` can be up to `100,000`, so an `O(n³)` brute force is infeasible.  
* `prices[i]` and `profits[i]` fit in a 32‑bit signed integer.  
* You must consider **both** the increasing and decreasing patterns.

---

### 6.  Summary

1. **Pick three indices** `i < j < k`.  
2. **Verify** one of the two strict monotonic conditions on their prices.  
3. **Sum** the profits of the three chosen items.  
4. **Return** the maximum possible sum, or `-1` if no such triple exists.

With this concise problem statement your colleague should be able to understand *what* needs to be solved without diving into any particular implementation.