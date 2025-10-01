---
title: LeetCode 2548. Maximum Price to Fill a Bag - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 🚀 “Maximum Price to Fill a Bag” – The Good, the Bad, and the Ugly  
**How to ace LeetCode 2548 with a clean greedy solution in Java, Python, & C++**

> **SEO keywords:** *LeetCode, Maximum Price to Fill a Bag, 2548, greedy algorithm, price/weight, Java solution, Python solution, C++ solution, coding interview, technical interview, job interview, algorithm design*

---

## Table of Contents
1. [Problem Overview](#problem-overview)
2. [Why Greedy Works](#why-greedy-works)
3. [Algorithm in Plain English](#algorithm-in-plain-english)
4. [Complexity Analysis](#complexity-analysis)
5. [Corner‑Case Pitfalls](#corner-case-pitfalls)
6. [Code Implementations](#code-implementations)
   - Java
   - Python
   - C++
7. [Testing & Edge Cases](#testing--edge-cases)
8. [Wrap‑Up & Job‑Interview Tips](#wrap-up--job-interview-tips)

---

## 1. Problem Overview <a name="problem-overview"></a>

**LeetCode 2548 – Maximum Price to Fill a Bag**

> Given an array `items[i] = [price_i, weight_i]` and a bag capacity `C`, you can split any item into two fractional parts.  
> **Goal:** Fill the bag *exactly* to capacity `C` while maximizing total price.  
> **Return** the maximum price; return `-1` if it’s impossible.

**Constraints**

| | |
|---|---|
| 1 ≤ items.length ≤ 10<sup>5</sup> | 1 ≤ price<sub>i</sub>, weight<sub>i</sub> ≤ 10<sup>4</sup> |
| 1 ≤ capacity ≤ 10<sup>9</sup> | Answers within 10⁻⁵ are accepted |

---

## 2. Why Greedy Works <a name="why-greedy-works"></a>

When you can split an item arbitrarily, the only thing that matters is **price per unit weight** (`price / weight`).  
To maximize total price under a weight limit, you should always take the *most valuable* weight first.

This is exactly the same principle as the classic **fractional knapsack** problem, which has a well‑known greedy solution: sort items by `price/weight` descending, then take as much as you can from each until the bag is full.

**Why does this work?**  
Any fractional part of an item has the same price/weight ratio as the whole. If you ever replaced a portion of a higher‑ratio item with a lower‑ratio item, you would lose value while keeping the same weight. Thus, the greedy choice is optimal.

---

## 3. Algorithm in Plain English <a name="algorithm-in-plain-english"></a>

1. **Sort** `items` by descending `price / weight`.  
   *Because we can’t use floating division in a custom comparator for strict integer languages, we compare using cross‑multiplication: `a[0]*b[1]` vs. `b[0]*a[1]`.*

2. **Iterate** over sorted items, maintaining:
   - `filledWeight` – how much weight we’ve already taken.
   - `totalPrice` – accumulated price.

3. For each item:
   - If we can take the **entire** item without exceeding capacity:
     - Add its weight and price.
   - Otherwise:
     - Take a **fraction** that exactly fills the remaining capacity.
     - Add the corresponding fractional price.

4. **Check** if we filled the bag:
   - If `filledWeight < capacity` → return `-1`.
   - Else → return `totalPrice`.

Because we can split items, **as long as the sum of all weights ≥ capacity, we can always fill the bag**. The greedy algorithm will always reach exactly the capacity if it’s possible.

---

## 4. Complexity Analysis <a name="complexity-analysis"></a>

| Step | Complexity |
|------|------------|
| Sorting | `O(n log n)` (n ≤ 10⁵) |
| Iteration | `O(n)` |
| **Total** | **`O(n log n)` time, `O(1)` auxiliary space** |

---

## 5. Corner‑Case Pitfalls <a name="corner-case-pitfalls"></a>

| Pitfall | Fix |
|---------|-----|
| **Integer overflow** in the comparator (`a[0]*b[1]`) | Use `long` (64‑bit) for cross multiplication or cast to `long` first. |
| **Rounding errors** in fractional part | Use `double` for price and fractional calculations; final answer tolerates 1e‑5 error. |
| **Capacity larger than total weight** | Return `-1` immediately or after the loop. |
| **All items have the same price/weight** | The algorithm still works; order doesn’t matter. |

---

## 6. Code Implementations <a name="code-implementations"></a>

Below are clean, production‑ready solutions in **Java, Python, and C++**.

---

### 6.1 Java (Greedy + Custom Sort)

```java
import java.util.Arrays;

class Solution {
    public double maxPrice(int[][] items, int capacity) {
        // Sort by price/weight ratio descending using cross multiplication
        Arrays.sort(items, (a, b) ->
                Long.compare((long) b[0] * a[1], (long) a[0] * b[1]));

        double totalPrice = 0.0;
        long filledWeight = 0;   // use long to avoid overflow

        for (int[] item : items) {
            int price = item[0];
            int weight = item[1];

            if (filledWeight + weight <= capacity) {
                // take whole item
                filledWeight += weight;
                totalPrice += price;
            } else {
                // take fraction to fill the remaining capacity
                long remaining = capacity - filledWeight;
                totalPrice += (double) price * remaining / weight;
                filledWeight = capacity;
                break; // bag is full
            }
        }

        return filledWeight == capacity ? totalPrice : -1.0;
    }
}
```

**Key Points**

- `Long.compare` prevents overflow.
- `double` for price calculations.
- Early exit once the bag is full.

---

### 6.2 Python (Simple & Readable)

```python
class Solution:
    def maxPrice(self, items: List[List[int]], capacity: int) -> float:
        # Sort by price/weight descending
        items.sort(key=lambda x: x[0] / x[1], reverse=True)

        total_price = 0.0
        filled = 0

        for price, weight in items:
            if filled + weight <= capacity:
                filled += weight
                total_price += price
            else:
                remain = capacity - filled
                total_price += price * (remain / weight)
                filled = capacity
                break

        return total_price if filled == capacity else -1.0
```

**Why Python?**  
- The `sort` with a lambda is clean and fast enough for 10⁵ elements.
- Python’s `float` (64‑bit IEEE‑754) handles the precision requirement.

---

### 6.3 C++ (Fast & Memory‑Efficient)

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    double maxPrice(vector<vector<int>>& items, int capacity) {
        // sort by price/weight ratio descending
        sort(items.begin(), items.end(),
            [](const vector<int>& a, const vector<int>& b) {
                // cross multiplication to avoid floating point
                return (long long)b[0] * a[1] < (long long)a[0] * b[1];
            });

        double total = 0.0;
        long long filled = 0;

        for (auto& it : items) {
            int price = it[0], weight = it[1];
            if (filled + weight <= capacity) {
                filled += weight;
                total += price;
            } else {
                long long remain = capacity - filled;
                total += static_cast<double>(price) * remain / weight;
                filled = capacity;
                break;
            }
        }

        return filled == capacity ? total : -1.0;
    }
};
```

**Highlights**

- `long long` for cross multiplication to avoid overflow.
- Early `break` keeps the loop minimal.

---

## 7. Testing & Edge Cases <a name="testing--edge-cases"></a>

| Test | Input | Expected | Reason |
|------|-------|----------|--------|
| 1 | `items=[[50,1],[10,8]], capacity=5` | `55.0` | Example from problem statement. |
| 2 | `items=[[100,30]], capacity=50` | `-1.0` | Total weight < capacity. |
| 3 | `items=[[1,1],[1,1],[1,1]], capacity=3` | `3.0` | All items same ratio; no fraction needed. |
| 4 | `items=[[100,1],[1,1000]], capacity=10` | `1000.0` | Pick 10 units of the first item. |
| 5 | `items=[[5,3],[7,6]], capacity=9` | `12.0` | Fraction of second item to fill. |
| 6 | `items=[[10,1]], capacity=1000000000` | `-1.0` | Weight too low. |

> **Tip:** Always verify that `totalWeight` of all items is at least `capacity` before running the greedy loop. This short‑circuit check saves time for impossible cases.

---

## 8. Wrap‑Up & Job‑Interview Tips <a name="wrap-up--job-interview-tips"></a>

- **Show the Greedy Insight**  
  Mention the *fractional knapsack* analogy and why sorting by ratio guarantees optimality.

- **Edge‑Case Handling**  
  Discuss overflow, rounding, and impossible‑to‑fill cases. Interviewers love candidates who anticipate hidden pitfalls.

- **Complexity Trade‑Off**  
  Emphasize that `O(n log n)` is acceptable given the constraints, and no DP or exponential search is needed.

- **Ask Smart Questions**  
  If the interviewer provides additional constraints (e.g., no item can be split), quickly adjust your approach: you would need a different DP solution.

- **Explain the Code**  
  Walk through your comparator carefully. Use `long long` or `Long.compare` to show awareness of data type limits.

- **Time‑Efficient Input**  
  For languages like Java/C++, demonstrate knowledge of fast input techniques if the interviewer asks you to handle large input streams.

---

**Congratulations!**  
You now have a robust, mathematically proven solution for LeetCode 2548, and you’ve learned how to translate that solution into a compelling interview narrative. Happy coding—and good luck on your next job interview! 🚀

--- 

**Search Keywords for the Blog:**

`fractional knapsack, LeetCode 2548, greedy algorithm, price per weight, custom comparator, integer overflow, job interview strategy, knapsack problem, algorithm design`.