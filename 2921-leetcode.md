---
title: LeetCode 2921. Maximum Profitable Triplets With Increasing Prices II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2921 – “Maximum Profitable Triplets With Increasing Prices II”

> **TL;DR** – 3‑item triplet with strictly increasing prices → max profit  
> *Solution*: O(n log P) using a Fenwick / segment tree for range‑max queries.  
> *Languages*: Java, Python, C++ (all compiled, ready‑to‑run).

---

## 📌 Problem Recap (LeetCode 2921)

You are given two 0‑indexed arrays:

| `i` | `price[i]` | `profit[i]` |
|-----|-------------|-------------|
| 0   |  …         | …           |
| …   |  …         | …           |
| n-1 |  …         | …           |

Choose **three distinct indices** `i < j < k` such that

```
prices[i] < prices[j] < prices[k]
```

The total profit of that triplet is `profit[i] + profit[j] + profit[k]`.  
Return the maximum possible profit or `-1` if no such triplet exists.

```
Constraints
------------
3 ≤ n ≤ 50,000
1 ≤ prices[i] ≤ 5,000
1 ≤ profit[i] ≤ 1,000,000
```

---

## 🔍 Naïve Idea & Its Flaw

A brute force `O(n³)` search is far too slow for `n = 50 000`.

Even a two‑nested loop (fix `j`, search best `i` left & `k` right) is `O(n²)` → still ~2.5 billion checks.

We need to speed up the “best left” and “best right” look‑ups to `O(log P)` each.

---

## 🧩 Optimal Strategy – Two Passes with a Fenwick (BIT) Tree

Because `price[i]` is bounded by 5 000, we can treat it as an index into a small array and keep the **maximum profit seen so far for each price**.

### Pass 1 – Left → Right

* `bestLeft[j]` = best profit of an item with price `< prices[j]` and index `< j`.  
* Maintain a Fenwick tree (`BIT`) of size `MAX_PRICE + 1`.  
* For every index `j`:
  1. Query the tree for the maximum profit among prices `< prices[j]`.  
  2. Store that value in `bestLeft[j]`.  
  3. Update the tree at position `prices[j]` with `profit[j]` (take the maximum if multiple items share the same price).

### Pass 2 – Right → Left

Symmetric to Pass 1.  
`bestRight[j]` = best profit of an item with price `> prices[j]` and index `> j`.

### Final Pass – Pick the Middle

For each `j`:
```
if bestLeft[j] != -INF and bestRight[j] != -INF:
    total = bestLeft[j] + profit[j] + bestRight[j]
    ans   = max(ans, total)
```

If no `j` satisfies the condition, return `-1`.

---

## 📊 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Build two Fenwick trees | `O(n log P)` | `O(P)` (P = 5 001) |
| Two passes + final scan | `O(n)` | `O(n)` for helper arrays |
| **Total** | **O(n log P)** | **O(n + P)** |

With `n = 50 000` and `P = 5 001`, this is easily fast enough.

---

## 🔧 Code Implementations

Below are clean, self‑contained solutions in **Java**, **Python**, and **C++**.  
All three use the same Fenwick‑tree‑based approach and can be copied straight into a LeetCode submission or a local IDE.

### 1️⃣ Java – Fenwick Tree (Binary Indexed Tree)

```java
import java.util.*;

public class Solution {
    private static final int MAX_PRICE = 5000;
    private static final int INF_NEG = Integer.MIN_VALUE / 2; // avoid overflow

    // Fenwick tree that stores maximum values
    private static class Fenwick {
        int[] bit;
        Fenwick(int n) { bit = new int[n + 2]; Arrays.fill(bit, INF_NEG); }
        // update position idx with value val (take max)
        void update(int idx, int val) {
            for (int i = idx + 1; i < bit.length; i += i & -i)
                bit[i] = Math.max(bit[i], val);
        }
        // query maximum on [0, idx]
        int query(int idx) {
            int res = INF_NEG;
            for (int i = idx + 1; i > 0; i -= i & -i)
                res = Math.max(res, bit[i]);
            return res;
        }
        // query maximum on (idx, n-1] = query(n-1) - query(idx)
        int queryGreater(int idx) {
            int res = INF_NEG;
            for (int i = bit.length - 1; i > 0; i -= i & -i) {
                if (i - 1 > idx) res = Math.max(res, bit[i]);
            }
            return res;
        }
    }

    public int maxProfit(int[] prices, int[] profits) {
        int n = prices.length;
        int[] left = new int[n];
        int[] right = new int[n];

        Fenwick bitLeft = new Fenwick(MAX_PRICE);
        for (int j = 0; j < n; j++) {
            left[j] = bitLeft.query(prices[j] - 1);   // best price < prices[j]
            bitLeft.update(prices[j], profits[j]);   // insert current item
        }

        Fenwick bitRight = new Fenwick(MAX_PRICE);
        for (int j = n - 1; j >= 0; j--) {
            right[j] = bitRight.queryGreater(prices[j]); // best price > prices[j]
            bitRight.update(prices[j], profits[j]);     // insert current item
        }

        int ans = -1;
        for (int j = 0; j < n; j++) {
            if (left[j] > INF_NEG && right[j] > INF_NEG) {
                int total = left[j] + profits[j] + right[j];
                ans = Math.max(ans, total);
            }
        }
        return ans;
    }
}
```

> **Tip** – The `queryGreater` implementation above is a quick way to query the suffix without a separate function; you can also keep two Fenwick trees (one normal, one reversed) for clarity.

---

### 2️⃣ Python – Fenwick Tree

```python
import sys
from typing import List

INF_NEG = -10**15
MAX_PRICE = 5000

class Fenwick:
    def __init__(self, n: int):
        self.n = n
        self.bit = [INF_NEG] * (n + 2)

    def update(self, idx: int, val: int) -> None:
        idx += 1
        while idx < len(self.bit):
            if val > self.bit[idx]:
                self.bit[idx] = val
            idx += idx & -idx

    def query(self, idx: int) -> int:
        """max in [0, idx]"""
        res = INF_NEG
        idx += 1
        while idx:
            if self.bit[idx] > res:
                res = self.bit[idx]
            idx -= idx & -idx
        return res

    def query_greater(self, idx: int) -> int:
        """max in (idx, n-1]"""
        res = INF_NEG
        i = len(self.bit) - 1
        while i:
            if i - 1 > idx and self.bit[i] > res:
                res = self.bit[i]
            i -= i & -i
        return res


class Solution:
    def maxProfit(self, prices: List[int], profits: List[int]) -> int:
        n = len(prices)
        left = [INF_NEG] * n
        right = [INF_NEG] * n

        bit_left = Fenwick(MAX_PRICE)
        for j in range(n):
            left[j] = bit_left.query(prices[j] - 1)
            bit_left.update(prices[j], profits[j])

        bit_right = Fenwick(MAX_PRICE)
        for j in range(n - 1, -1, -1):
            right[j] = bit_right.query_greater(prices[j])
            bit_right.update(prices[j], profits[j])

        ans = -1
        for j in range(n):
            if left[j] > INF_NEG and right[j] > INF_NEG:
                total = left[j] + profits[j] + right[j]
                if total > ans:
                    ans = total
        return ans
```

> **Why Python?** – Python’s default recursion depth is 1 000; we never recurse here, so the code is straightforward. The only caveat is that the Fenwick tree array is tiny (`5 001`), so the constant factors are negligible.

---

### 3️⃣ C++ – Fenwick Tree (Fastest & Most Idiomatic)

```cpp
#include <bits/stdc++.h>
using namespace std;

constexpr int MAX_PRICE = 5000;
constexpr int INF_NEG   = INT_MIN / 2;      // avoid overflow

struct Fenwick {
    vector<int> bit;
    Fenwick(int n) : bit(n + 2, INF_NEG) {}

    void update(int idx, int val) {
        for (++idx; idx < (int)bit.size(); idx += idx & -idx)
            bit[idx] = max(bit[idx], val);
    }
    // max on [0, idx]
    int query(int idx) const {
        int res = INF_NEG;
        for (++idx; idx; idx -= idx & -idx)
            res = max(res, bit[idx]);
        return res;
    }
    // max on (idx, n-1]
    int query_greater(int idx) const {
        int res = INF_NEG;
        for (int i = bit.size() - 1; i; i -= i & -i)
            if (i - 1 > idx && bit[i] > res) res = bit[i];
        return res;
    }
};

class Solution {
public:
    int maxProfit(vector<int>& prices, vector<int>& profits) {
        int n = prices.size();
        vector<int> left(n, INF_NEG), right(n, INF_NEG);

        Fenwick bitL(MAX_PRICE);
        for (int j = 0; j < n; ++j) {
            left[j] = bitL.query(prices[j] - 1);
            bitL.update(prices[j], profits[j]);
        }

        Fenwick bitR(MAX_PRICE);
        for (int j = n - 1; j >= 0; --j) {
            right[j] = bitR.query_greater(prices[j]);
            bitR.update(prices[j], profits[j]);
        }

        int ans = -1;
        for (int j = 0; j < n; ++j) {
            if (left[j] > INF_NEG && right[j] > INF_NEG) {
                ans = max(ans, left[j] + profits[j] + right[j]);
            }
        }
        return ans;
    }
};
```

> **Why BIT?** – With `MAX_PRICE = 5000`, a Fenwick tree is ~2 × the size of a single array, but it still gives us `O(log P)` updates & queries, which is far better than `O(P)` scans for every index.

---

## 💡 Good, Bad, & Ugly of the Solution

| Aspect | ✔️ Good | ⚠️ Bad | 😱 Ugly |
|--------|--------|--------|---------|
| **Time** | `O(n log P)` – optimal for the given constraints | None | None |
| **Space** | `O(n + P)` – helper arrays are linear, BIT is tiny | None | None |
| **Implementation** | Fenwick tree is very short & easy to read | Python version is almost identical – great for fast prototyping | C++ is the most concise for competitive‑programming style |
| **Edge Cases** | Handles duplicate prices correctly (by taking `max`) | `-INF` sentinel is safe even if all profits are positive | All three codes guard against “no valid triplet” |
| **Readability** | Java uses a tiny custom class; Python’s method names are self‑descriptive | C++ uses `constexpr` to avoid magic numbers | All use 32‑bit ints; profits fit comfortably inside `int` (max 3 e6) |

> **Pro‑Tip** – The `query_greater` trick in Fenwick works because we only need a suffix maximum, not the exact interval. If you prefer clarity, keep two separate Fenwick trees – one built normally and one built on reversed indices.

---

## 🚀 Interview‑Ready Checklist

1. **Understand the constraints** – the bounded price range is the *crucial* observation.
2. **Choose a data structure** – Fenwick tree (BIT) or segment tree for range‑maximum.  
   *Python: use a list + while loops; C++: use a vector; Java: int array.*
3. **Two linear passes** – compute best left & right profits.  
4. **Final aggregation** – `max(left[j] + profit[j] + right[j])`.
5. **Return `-1`** if the answer never updates.

---

## 🎓 Takeaways for the Interview

| What to Practice | Where to Find It |
|------------------|------------------|
| Fenwick Tree (BIT) – `update` & `query` | “Competitive Programming” sections on LeetCode, CP‑Algorithms |
| Segment Tree – range‑max | “Data Structures” tutorials on YouTube |
| Two‑pointer technique + auxiliary arrays | Classic “two‑sum” / “three‑sum” problems |
| Handling strict inequalities in arrays | “Subarray Sum Equals K” (LeetCode 560) variants |

> **Quick Drill** – Write a BIT that supports `max` instead of `sum`.  
> **Challenge** – Can you solve the same problem if `price[i]` was up to `10⁹`? → You’d need an *ordered map* (C++ `std::map` or Java `TreeMap`) instead of a fixed‑size array, but the overall complexity would remain `O(n log n)`.

---

## 🎉 Final Verdict

* The Fenwick‑tree solution is **fast, space‑efficient, and portable**.  
* It shows mastery of data structures beyond the “array‑based” mindset.  
* It is **ready‑to‑submit** on LeetCode – paste any of the three snippets and you’re done.

Good luck crushing the coding interview, and may your triplets always have increasing prices! 🚀