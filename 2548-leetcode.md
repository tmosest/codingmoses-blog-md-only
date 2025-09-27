---
title: LeetCode 2548. Maximum Price to Fill a Bag - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2548 – *Maximum Price to Fill a Bag*  
**Java / Python / C++** solutions + an SEO‑friendly interview‑prep blog

---

## TL;DR

| Language | Time | Space | Link |
|----------|------|-------|------|
| **Java** | **O(n log n)** | **O(1)** | <https://leetcode.com/problems/maximum-price-to-fill-a-bag/description/> |
| **Python** | **O(n log n)** | **O(1)** | |
| **C++** | **O(n log n)** | **O(1)** | |

> **The greedy idea:** sort items by *price / weight* in descending order, then fill the bag starting from the best ratio.  
> If you can’t reach the exact capacity, return **-1**.

---

## Problem Recap

> **Maximum Price to Fill a Bag**  
> You’re given `items[i] = [priceᵢ, weightᵢ]`.  
> Each item may be split into any two parts (fractions).  
> Fill a bag of capacity `C` to its exact weight with a subset or fractions of items, maximizing total price.  
> Return the maximum price (within `1e‑5`) or `-1` if impossible.

- `1 ≤ items.length ≤ 10⁵`
- `1 ≤ priceᵢ, weightᵢ ≤ 10⁴`
- `1 ≤ C ≤ 10⁹`

---

## Why a Greedy Strategy Works

Because each item can be fractioned arbitrarily, the problem reduces to a continuous knapsack.  
In continuous knapsack the optimal solution is to pick items with the highest *price per unit weight* first.  
If we pick any item with a lower ratio before a higher one, we can swap a portion and increase total price.  

Hence:

1. **Sort** by `price / weight` descending.  
2. **Take whole items** while capacity remains.  
3. **Take a fraction** of the first item that doesn’t fit.  
4. If all items are used and capacity still > 0 → **impossible**.

---

## Pitfalls & Edge Cases

| ✅ Good | ❌ Bad | 🤬 Ugly |
|--------|-------|--------|
| Simple O(n log n) sort | Integer overflow in comparator → use 64‑bit multiplication | Floating‑point precision: use `double` and compare diff against 0 |
| Handles splitting automatically | Not checking for capacity > 0 after loop → wrong `-1` | Forgetting `reverse=True` in Python sort → wrong order |
| O(1) auxiliary space | Using `int` for total weight → overflow | Using `int` for price accumulation → overflow for huge sums |

---

## Solution Code

### 1. Java – Greedy + Custom Comparator

```java
import java.util.Arrays;

class Solution {
    public double maxPrice(int[][] items, int capacity) {
        // Sort by price/weight ratio in descending order.
        Arrays.sort(items, (a, b) -> {
            long lhs = (long) b[0] * a[1]; // b.price * a.weight
            long rhs = (long) a[0] * b[1]; // a.price * b.weight
            return Long.compare(lhs, rhs);
        });

        double totalPrice = 0.0;
        long remaining = capacity;

        for (int[] item : items) {
            if (remaining == 0) break;
            long w = item[1];
            double p = item[0];
            if (remaining >= w) {
                // Take whole item
                remaining -= w;
                totalPrice += p;
            } else {
                // Take fraction of the item
                double fraction = (double) remaining / w;
                totalPrice += p * fraction;
                remaining = 0;
                break;
            }
        }

        return remaining == 0 ? totalPrice : -1.0;
    }
}
```

**Key points**

- Use `long` in the comparator to avoid `int` overflow (`1e4 * 1e4 = 1e8`, still fits but safe).  
- `remaining` tracks weight left; stops once it reaches 0.  
- If `remaining` > 0 after the loop, the bag can’t be filled → `-1`.

---

### 2. Python – Greedy + `sorted`

```python
from typing import List

class Solution:
    def maxPrice(self, items: List[List[int]], capacity: int) -> float:
        # Sort by price/weight ratio (descending)
        items.sort(key=lambda x: x[0] / x[1], reverse=True)

        total_price = 0.0
        remaining = capacity

        for price, weight in items:
            if remaining == 0:
                break
            if remaining >= weight:
                remaining -= weight
                total_price += price
            else:
                fraction = remaining / weight
                total_price += price * fraction
                remaining = 0
                break

        return total_price if remaining == 0 else -1.0
```

> **Why division is fine**: `price` and `weight` are ≤ 10⁴, so the ratio is a precise double; no precision loss for sorting.

---

### 3. C++ – Greedy + Custom Comparator

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    double maxPrice(vector<vector<int>>& items, int capacity) {
        // Sort by price/weight ratio descending
        sort(items.begin(), items.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 return 1LL * a[0] * b[1] > 1LL * b[0] * a[1];
             });

        double totalPrice = 0.0;
        long long remaining = capacity;

        for (auto &it : items) {
            if (remaining == 0) break;
            long long w = it[1];
            double p = it[0];
            if (remaining >= w) {
                remaining -= w;
                totalPrice += p;
            } else {
                double fraction = static_cast<double>(remaining) / w;
                totalPrice += p * fraction;
                remaining = 0;
                break;
            }
        }
        return remaining == 0 ? totalPrice : -1.0;
    }
};
```

> **1LL** guarantees 64‑bit multiplication for the comparator.

---

## Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sorting (`n log n`) | **O(n log n)** | **O(1)** |
| Linear scan | **O(n)** | **O(1)** |
| **Total** | **O(n log n)** | **O(1)** |

---

## Interview‑Ready Summary

- **Key Insight**: Continuous knapsack → greedy by ratio.
- **Implementation**: Sort by `price/weight`; take whole items until capacity left; take fractional part of first unfit item.
- **Edge Handling**: If after consuming all items capacity remains > 0 → return `-1`.
- **Language‑Specific Tips**:
  - **Java**: Use `long` in comparator, avoid `int` overflow.
  - **Python**: Sorting by float ratio is safe; keep track with `remaining`.
  - **C++**: 64‑bit multiplication (`1LL * a[0] * b[1]`) in comparator.

---

## SEO‑Optimized Blog Post

### Title
**Master LeetCode 2548 – Maximum Price to Fill a Bag: Java, Python & C++ Solutions + Interview Tips**

### Meta Description
Learn how to crack LeetCode 2548 with greedy sorting. Detailed Java, Python, and C++ solutions plus a job‑ready interview guide. Perfect for software engineers preparing for coding interviews.

### Headings

1. **Problem Overview**  
2. **Why Greedy Works** – Continuous Knapsack Explained  
3. **Implementation Walk‑through**  
   - Java Solution  
   - Python Solution  
   - C++ Solution  
4. **Complexity & Edge Cases**  
5. **Common Pitfalls (Good, Bad & Ugly)**  
6. **Interview Prep Checklist**  
7. **Takeaway & Career Advice**

### Sample Intro

> *“Ever stared at a seemingly impossible coding challenge and wondered how to approach it? LeetCode 2548 – *Maximum Price to Fill a Bag* – is a perfect example. Below you’ll find clean, production‑ready solutions in Java, Python, and C++, a deep dive into why greedy sorting is optimal, and a quick cheat‑sheet to impress recruiters during your next interview.”*

### Keywords

- LeetCode 2548  
- Maximum Price to Fill a Bag  
- continuous knapsack  
- greedy algorithm  
- Java greedy solution  
- Python knapsack  
- C++ sort comparator  
- interview prep  
- coding interview  
- algorithmic trading  
- data structures and algorithms  

### Call‑to‑Action

> **Ready to ace your next interview?**  
> Download our free “30‑Day Algorithm Prep” PDF, or subscribe to our newsletter for weekly coding challenges and interview hacks.

### Footer

> © 2025 YourName — All rights reserved.  
> *This article is intended for educational purposes only.*

---

## 🎯 Final Thoughts

- The **greedy strategy** is both *intuitive* and *efficient* for this continuous knapsack problem.  
- Pay attention to *data type limits*—especially in Java and C++ where `int` overflow can silently break your solution.  
- In interviews, articulate the *optimality proof* (swap argument) to show deep understanding.  

Use this guide to polish your coding skills, impress interviewers, and land your dream software engineering role!