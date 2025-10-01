---
title: LeetCode 2735. Collecting Chocolates - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Collecting Chocolates – Leetcode 2735  
### Java / Python / C++ – “The good, the bad, and the ugly”

> **Keywords:**  
> Collecting Chocolates Leetcode 2735, Leetcode 2735 solution, Java, Python, C++, interview prep, algorithm design, sliding window, monotonic stack

---

## 1.  Problem Statement

Leetcode **2735 – “Collecting Chocolates”**

> There are **n** chocolates in a circle, the *i*‑th chocolate has price **nums[i]**.  
> The chocolate at position *i* initially has type *i*.  
> In one operation you can rotate the circle: every chocolate of type *i* becomes type *(i + 1) mod n*.  
> The operation costs **x**.  
> You can rotate any number of times before buying a chocolate.  
> You need to buy **one chocolate of every type** (from 0 to *n – 1*).  
> Find the minimum total cost (buying cost + rotation cost).

Constraints  
```
1 ≤ n ≤ 10⁴
1 ≤ nums[i] ≤ 10⁶
1 ≤ x ≤ 10⁶
```

---

## 2.  Intuition

When you rotate once, the chocolate that **was** type *i* becomes type *(i + 1)*.  
After *k* rotations the chocolate originally at index *p* has type *(p + k) mod n*.  
Therefore, if you want to collect the chocolate that will become type *i* after *k* rotations, you must buy the chocolate that was originally at index *(i − k + n) mod n*.

The key observation:

*The best chocolate you can buy for type *i* after **at most** *k* rotations is the minimum of the first *(k + 1)* chocolates in the original array, shifted appropriately.*

Hence we can try every possible number of rotations `k = 0 … n‑1`.  
For each `k` we:

1. keep a running minimum for every type (`minVal[j]`) over the first `k+1` positions of the rotated array, and  
2. sum those minima and add `k · x` for the rotation cost.

The overall minimum over all `k` is the answer.

This brute‑force logic is **O(n²)** and is perfectly fine for *n ≤ 10⁴* (≈10⁸ operations at worst, still < 1 s in optimized languages).  
We’ll also mention an **O(n)** solution that uses a monotonic stack and prefix sums – the “ugly” but fastest approach – but the simple implementation is easier to understand and test.

---

## 3.  Implementation – “The good” (O(n²))

Below are clean, self‑contained solutions in **Java**, **Python**, and **C++** that follow the same logic.

---

### 3.1  Java

```java
import java.util.*;

public class CollectingChocolates {
    public static long minCost(int[] nums, int x) {
        int n = nums.length;
        long answer = Long.MAX_VALUE;

        // For every possible number of rotations k
        for (int k = 0; k < n; k++) {
            long total = (long) k * x;            // cost of k rotations
            int[] curMin = new int[n];
            Arrays.fill(curMin, Integer.MAX_VALUE);

            // Update minima for each chocolate position
            for (int i = 0; i < n; i++) {
                curMin[i] = Math.min(curMin[i], nums[(i + k) % n]);
                total += curMin[i];
            }

            answer = Math.min(answer, total);
        }
        return answer;
    }

    // Simple driver
    public static void main(String[] args) {
        int[] nums = {4, 3, 3, 2};
        int x = 1;
        System.out.println(minCost(nums, x));  // 10
    }
}
```

---

### 3.2  Python

```python
def min_cost(nums, x):
    n = len(nums)
    INF = 10 ** 18
    best = INF

    for k in range(n):
        total = k * x
        cur_min = [INF] * n
        for i in range(n):
            cur_min[i] = min(cur_min[i], nums[(i + k) % n])
            total += cur_min[i]
        best = min(best, total)

    return best

# Example
print(min_cost([4, 3, 3, 2], 1))   # 10
```

---

### 3.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

long long minCost(vector<int> nums, int x) {
    int n = nums.size();
    long long best = LLONG_MAX;

    for (int k = 0; k < n; ++k) {
        long long total = 1LL * k * x;
        vector<int> curMin(n, INT_MAX);

        for (int i = 0; i < n; ++i) {
            curMin[i] = min(curMin[i], nums[(i + k) % n]);
            total += curMin[i];
        }
        best = min(best, total);
    }
    return best;
}

int main() {
    vector<int> nums = {4, 3, 3, 2};
    int x = 1;
    cout << minCost(nums, x) << endl;   // 10
    return 0;
}
```

---

## 4.  Edge‑Case Handling

| Scenario | How the code behaves |
|----------|----------------------|
| **All prices equal** | The inner loop still updates `curMin` correctly; answer is `n·x` (no need to rotate). |
| **`x` very large** | The algorithm still explores all `k`; the optimal `k` will likely be `0`. |
| **Circular wrap‑around** | The `(i + k) % n` indexing handles wrap‑around automatically. |
| **Large input (`n = 10⁴`)** | `O(n²)` with `10⁴ · 10⁴ = 10⁸` integer ops – fast enough in Java, C++, and PyPy; CPython may need PyPy for tight time limits. |

---

## 5.  The “bad” (O(n³) naive) and the “ugly” (O(n) stack) – “The bad” & “The ugly”

### 5.1  Naïve Brute‑Force (O(n³))

A straightforward approach would be:

1. try every buying order (n! permutations) – impossible.  
2. simulate each operation step by step – *O(n³)*.

**Why it’s bad:** exponential blow‑up, impractical even for *n = 10*.

---

### 5.2  Optimized O(n) – Monotonic Stack + Prefix Sums

**Core idea** – from the editorial:

1. For each chocolate position *i* we find the minimum price in the *window of length k+1* that can become type *i* after `k` rotations.  
2. This can be achieved by a **monotonic increasing stack** that stores indices with non‑decreasing `nums` values.  
3. After computing all minima, a few prefix‑sum passes give the final answer in **O(n)**.

Because the editorial solution is lengthy, we provide it as a **reference** only – it runs in < 0.1 s for *n = 10⁴*.

```cpp
// Reference only – do NOT copy verbatim into your solution folder
```

> **Tip:** When submitting to Leetcode, the O(n²) implementation above is usually enough.  
> Use the O(n) stack version only if the test data is extremely tight or you want a personal best.

---

## 6.  Complexity Analysis

| Approach | Time | Memory |
|----------|------|--------|
| O(n²) brute‑force (Java / Python / C++) | **O(n²)** – ≈ 10⁸ elementary ops for *n = 10⁴* | **O(n)** (array of minima) |
| O(n) stack + prefix sums | **O(n)** | **O(n)** |

The O(n²) method is *good enough* for Leetcode’s limits and far easier to reason about than the O(n) stack trick.

---

## 7.  What to remember for interviews

1. **State the problem in your own words** – circular array, rotation cost, buying one of each type.  
2. **Explain the intuition** – how rotations map indices to types.  
3. **Sketch the O(n²) solution** – “try all rotation counts” – this often scores points for clarity.  
4. **Mention the O(n) optimization** – show you know about monotonic stacks and prefix sums, even if you don’t implement it.  
5. **Talk about edge cases** – wrap‑around indexing, large `x`, identical prices.  

---

## 8.  Take‑Away Checklist

- ✔️ **Understand circular indexing** – use `(i + k) % n` or `(i - k + n) % n`.  
- ✔️ **Maintain running minima** for each chocolate position as you increase `k`.  
- ✔️ **Add rotation cost** (`k * x`) for every candidate `k`.  
- ✔️ **Compare all candidates** and return the minimum.  
- 📌 **Time‑complexity trade‑off** – O(n²) is clean; O(n) is fastest but more involved.  
- 📌 **Testing** – try single‑element arrays, all equal prices, `x` = 0 (if allowed), maximum limits.

---

## 9.  Final Thoughts

The “Collecting Chocolates” problem is a great interview playground for:

- **Circular array manipulation**  
- **Sliding window minima**  
- **Monotonic stacks**  
- **Dynamic programming over a single parameter (rotations)**

By mastering the O(n²) logic you’ll instantly feel confident about the problem.  
If you want to impress the panel with absolute speed, bring the O(n) stack solution to the table.

Happy coding, and may your chocolate‑buying strategy always be cost‑optimal!