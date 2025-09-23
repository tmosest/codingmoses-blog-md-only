---
title: LeetCode 2279. Maximum Bags With Full Capacity of Rocks - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2279. **Maximum Bags With Full Capacity of Rocks** – A Complete Guide  
*(Java | Python | C++)*  

> **SEO Keywords:** LeetCode 2279, Maximum Bags With Full Capacity of Rocks, Greedy algorithm, Java solution, Python solution, C++ solution, interview prep, algorithm interview, job interview

---

## Table of Contents  

1. [Problem Statement](#problem-statement)  
2. [Intuition & Greedy Idea](#intuition---greedy-idea)  
3. [Edge Cases & Pitfalls](#edge-cases--pitfalls)  
4. [Complexity Analysis](#complexity-analysis)  
5. [Implementation](#implementation)  
   - Java  
   - Python  
   - C++  
6. [Good, Bad, and Ugly](#good-bad-and-ugly)  
7. [Why This Problem Rocks Your Interview Prep](#why-this-problem-rocks-your-interview-prep)  
8. [Conclusion](#conclusion)  

---

## Problem Statement

You have **n** bags indexed from **0** to **n‑1**.  
- `capacity[i]` – the maximum number of rocks the *i*‑th bag can hold.  
- `rocks[i]` – the current number of rocks in the *i*‑th bag (`0 ≤ rocks[i] ≤ capacity[i]`).  

You also have an integer `additionalRocks` – the total number of extra rocks you can distribute among the bags (you may leave some unused).

**Return the maximum number of bags that can reach full capacity after distributing the extra rocks optimally.**

> **Constraints**  
> * `n == capacity.length == rocks.length`  
> * `1 ≤ n ≤ 5·10⁴`  
> * `1 ≤ capacity[i] ≤ 10⁹`  
> * `0 ≤ rocks[i] ≤ capacity[i]`  
> * `1 ≤ additionalRocks ≤ 10⁹`

---

## Intuition — Greedy Idea

The amount of rocks needed to fill bag *i* is `need[i] = capacity[i] – rocks[i]`.  
If we want to maximize the number of *full* bags, we should first fill the bags that need the **fewest** rocks.  
Why? Because each bag we fill consumes some amount of `additionalRocks`.  
Choosing the smallest `need[i]` values first gives us the largest count of bags that can be completed.

Thus:

1. Compute `need[i]` for all bags.  
2. Sort the array of `need`.  
3. Traverse the sorted list, subtracting each `need` from `additionalRocks` while it remains non‑negative.  
4. The number of times we could subtract before running out is the answer.

This is a classic **greedy** strategy: locally optimal choices (filling the easiest bag first) lead to a globally optimal solution.

---

## Edge Cases & Pitfalls

| # | Issue | Why it matters | How to handle |
|---|-------|----------------|---------------|
| 1 | **Large numbers** – capacities and rocks up to `10⁹`. | Integer overflow in some languages if you use `int`. | Use 64‑bit (`long`/`long long`) for intermediate sums, but the final answer fits in `int`. |
| 2 | **Zero needs** – some bags may already be full. | They should be counted immediately without consuming rocks. | Filter out `need == 0` or treat them as zero‑cost items that never reduce `additionalRocks`. |
| 3 | **Unnecessary distribution** – you may not need to use all rocks. | The algorithm stops when `additionalRocks < nextNeed`. | The loop condition naturally handles this. |
| 4 | **Large `n` (5·10⁴)** – sorting must be efficient. | Sorting O(n log n) is acceptable. | Use efficient library sort (TimSort / introsort). |
| 5 | **Language differences** – e.g., Python’s default int is unbounded, Java’s `int` is 32‑bit. | Make sure you use the right type. | Use `long` in Java & C++ for the needs array. |

---

## Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Compute `need` array | **O(n)** | **O(n)** (or O(1) if we sort in place) |
| Sort | **O(n log n)** | **O(n)** (Java & C++ sort copies; Python uses Timsort in place) |
| Greedy sweep | **O(n)** | **O(1)** |

**Total**:  
- **Time**: `O(n log n)`  
- **Space**: `O(n)` (can be reduced to `O(1)` if you sort a copy of the original array).

---

## Implementation

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++**.  
Each follows the same greedy algorithm described above.

### 1. Java

```java
import java.util.Arrays;

public class Solution {
    public int maximumBags(int[] capacity, int[] rocks, int additionalRocks) {
        int n = capacity.length;
        long[] need = new long[n];
        int full = 0;                     // bags already full

        for (int i = 0; i < n; i++) {
            if (rocks[i] == capacity[i]) {
                full++;                   // no rocks needed
            } else {
                need[i] = (long)capacity[i] - rocks[i];
            }
        }

        // Sort only the needed elements; unused slots are zero
        Arrays.sort(need);

        long rocksLeft = additionalRocks;
        int added = 0;

        for (int i = 0; i < n; i++) {
            long req = need[i];
            if (req == 0) continue;       // already full
            if (rocksLeft >= req) {
                rocksLeft -= req;
                added++;
            } else {
                break;
            }
        }
        return full + added;
    }
}
```

> **Key points**  
> * Use `long` for the `need` array to avoid overflow.  
> * Skip zero needs after sorting; they correspond to already full bags.

### 2. Python

```python
class Solution:
    def maximumBags(self, capacity: List[int], rocks: List[int],
                    additionalRocks: int) -> int:
        # Compute how many more rocks each bag needs
        need = [c - r for c, r in zip(capacity, rocks) if c - r > 0]
        need.sort()

        count = 0
        for req in need:
            if additionalRocks >= req:
                additionalRocks -= req
                count += 1
            else:
                break
        # All zero‑need bags are already full
        count += len(capacity) - len(need) - count
        return count
```

> **Why we filter `> 0`**  
> Keeps the list small and skips already full bags in the loop.

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumBags(vector<int>& capacity, vector<int>& rocks, int additionalRocks) {
        int n = capacity.size();
        vector<long long> need;
        int alreadyFull = 0;

        for (int i = 0; i < n; ++i) {
            if (rocks[i] == capacity[i])
                ++alreadyFull;              // no rocks needed
            else
                need.push_back(static_cast<long long>(capacity[i]) - rocks[i]);
        }

        sort(need.begin(), need.end());

        long long rocksLeft = additionalRocks;
        int added = 0;
        for (long long req : need) {
            if (rocksLeft >= req) {
                rocksLeft -= req;
                ++added;
            } else break;
        }
        return alreadyFull + added;
    }
};
```

> **Notes**  
> * We store only the *positive* needs to reduce memory.  
> * `long long` protects against overflow.

---

## Good, Bad, and Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Greedy correctness** | Intuitively clear: fill easiest bags first. | Requires proof that local optimality yields global optimum. | Some might mistakenly think we can fill any bag; ignoring the order can lead to sub‑optimal counts. |
| **Time complexity** | `O(n log n)` – fast for n ≤ 5·10⁴. | Sorting can be costly if you also need extra data structures. | Using a priority queue with repeated deletions adds unnecessary overhead. |
| **Space usage** | `O(n)` minimal; can reduce to `O(1)` by sorting in place. | Storing extra arrays for needs increases memory footprint. | Over‑engineering (e.g., building a segment tree) is overkill. |
| **Overflow risk** | Long‑integer arithmetic prevents overflow. | Forgetting to use 64‑bit types in Java/C++ leads to subtle bugs. | Mixing signed/unsigned types in C++ may produce wrong results. |
| **Readability** | Simple loops, clear variable names. | Verbose code or excessive comments can obscure the logic. | Minified or heavily macro‑based code is hard to maintain. |

---

## Why This Problem Rocks Your Interview Prep

1. **Classic Greedy Pattern** – Many interviewers love problems where sorting + greedy yields the answer. Mastering this pattern gives you confidence for similar “optimal distribution” questions.

2. **Large Input Constraints** – Demonstrates you can think about time & space. Sorting `O(n log n)` for 5 × 10⁴ is acceptable, but you must recognize the boundary.

3. **Data Types & Overflow** – A subtle error with 32‑bit integers can cause wrong answers on edge cases. Showing awareness of numeric limits impresses interviewers.

4. **Scalable Code** – The provided solutions are concise yet production‑ready. You can adapt them for unit tests, performance profiling, or code‑review contexts.

5. **Versatile Language Support** – Having the solution in Java, Python, and C++ shows versatility—valuable for teams working in polyglot environments.

---

## SEO‑Optimized Takeaway

If you’re preparing for a **software engineer interview** and want to **impress recruiters**, tackle LeetCode 2279—**Maximum Bags With Full Capacity of Rocks**. The greedy strategy is both elegant and efficient.  
Show your code in **Java**, **Python**, and **C++**; highlight the importance of using 64‑bit arithmetic. Discuss the **good, bad, and ugly** aspects—this depth demonstrates critical thinking.  

> **Keyword-rich title:** *“Master LeetCode 2279: Java/Python/C++ Greedy Solution – The Good, the Bad, and the Ugly”*  
> **Meta description:** *“Learn how to solve LeetCode 2279 in Java, Python, and C++ using a greedy algorithm. Understand edge cases, avoid overflow, and boost your interview prospects.”*

---

## Conclusion

- The problem boils down to *filling bags that need the fewest rocks first*.
- Greedy + sorting yields an optimal solution in `O(n log n)` time.
- Mindful handling of large numbers and edge cases ensures correctness.
- Presenting the solution in multiple languages showcases adaptability—an attractive trait for recruiters.

Happy coding, and good luck landing that dream job!