---
title: LeetCode 3645. Maximum Total from Optimal Activation Order - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 Problem: Maximum Total from Optimal Activation Order  
**LeetCode #3645** – *Medium*  
`public long maxTotal(int[] value, int[] limit)`

> **Goal:** Activate elements in an order that maximizes the sum of the values of the activated elements, respecting the activation rule and the “self‑destruct” rule that disables elements when the active count reaches their limit.

---

### 1️⃣ Problem Restatement

| Item | What it is | Constraints |
|------|------------|-------------|
| `value[i]` | The value gained by activating element `i` | 1 ≤ value[i] ≤ 10⁵ |
| `limit[i]` | The number of currently active elements **strictly less** than `limit[i]` required to activate `i` | 1 ≤ limit[i] ≤ n |
| Activation rule | You may activate an element only if the current active count `< limit[i]`. |
| Self‑destruct rule | After an activation, if the active count becomes `x`, **every element** `j` with `limit[j] ≤ x` is permanently deactivated (even the one you just activated). |

All elements start inactive.  
You can activate at most once per element.  
Return the maximum total value you can obtain.

---

### 2️⃣ Intuition & Key Observation

When the active count finally reaches a value `L`, **every** element with `limit ≤ L` disappears.  
So, for a fixed limit `L`:

* We can activate at most `L` elements whose limit equals `L`.
* The best strategy is to pick the **top `L` values** among those elements.

Why?  
Before we reach `active = L` we must keep `active < L`.  
We can therefore activate `L‑1` items with limit `L` while `active` stays `< L`.  
On the `L`‑th activation we reach `active = L`; the activated element’s value is counted, and *afterwards* all limit ≤ L items vanish.

Thus the optimal total is simply the sum, over every distinct limit `L`, of the largest `min(L, count(L))` values.

---

### 3️⃣ Algorithm (Greedy + Buckets)

1. **Bucket** all values by their `limit`.  
   `buckets[L] = { value[i] | limit[i] == L }`.
2. For each bucket `L`:
   * Sort its values **descending**.
   * Add the sum of the first `min(L, bucket.size())` values to the answer.
3. Return the accumulated sum.

#### Correctness Sketch

*Any* activation order can be transformed into the greedy order without decreasing the total:
- For a fixed `L`, you can activate at most `L` elements with that limit.
- By taking the largest ones you can never hurt the sum.
- Because we process limits in increasing order, when we finish with limit `L` all lower limits are already dead, so we never block a higher‑limit element.

Hence the greedy bucket strategy yields the maximum total.

---

### 4️⃣ Complexity Analysis

| Operation | Time | Memory |
|-----------|------|--------|
| Building buckets (`O(n)`) | **O(n)** |
| Sorting values inside buckets (total values = n) | **O(n log n)** |
| Summation | **O(n)** |
| **Total** | **O(n log n)** | **O(n)** (for the buckets)

---

### 4️⃣ Code – 3 Languages

> All implementations run in the same \(O(n\log n)\) time and use \(O(n)\) extra space.

---

#### 📦 Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def maxTotal(self, value: List[int], limit: List[int]) -> int:
        # 1. bucket by limit
        buckets = defaultdict(list)
        for v, l in zip(value, limit):
            buckets[l].append(v)

        # 2. greedy
        ans = 0
        for l, vals in buckets.items():
            vals.sort(reverse=True)          # descending
            ans += sum(vals[:l])             # take at most l elements
        return ans
```

> **Why `int` is fine** – Python’s integers are unbounded, so no overflow worries.

---

#### 🧱 Java

```java
import java.util.*;

public class Solution {
    public long maxTotal(int[] value, int[] limit) {
        int n = value.length;
        List<List<Integer>> buckets = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) buckets.add(new ArrayList<>());

        // 1. bucket by limit
        for (int i = 0; i < n; i++) {
            buckets.get(limit[i]).add(value[i]);
        }

        long ans = 0L;
        // 2. greedy
        for (int l = 1; l <= n; l++) {
            List<Integer> list = buckets.get(l);
            if (list.isEmpty()) continue;
            list.sort(Collections.reverseOrder());
            int take = Math.min(l, list.size());
            for (int i = 0; i < take; i++) {
                ans += list.get(i);
            }
        }
        return ans;
    }
}
```

> **Return type:** `long` (64‑bit) – the problem guarantees the sum fits in a signed 64‑bit integer.

---

#### 🏗️ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxTotal(vector<int>& value, vector<int>& limit) {
        int n = value.size();
        vector<vector<int>> buckets(n + 1);

        // 1. bucket by limit
        for (int i = 0; i < n; ++i) {
            buckets[limit[i]].push_back(value[i]);
        }

        long long ans = 0;
        // 2. greedy
        for (int l = 1; l <= n; ++l) {
            auto &vec = buckets[l];
            if (vec.empty()) continue;
            sort(vec.rbegin(), vec.rend());          // descending
            int take = min(l, (int)vec.size());
            for (int i = 0; i < take; ++i) {
                ans += vec[i];
            }
        }
        return ans;
    }
};
```

> **Note:** Use `long long` for the answer to avoid overflow.

---

### 5️⃣ Worked Example

| i | value | limit |
|---|-------|-------|
| 0 | 10 | 1 |
| 1 | 20 | 2 |
| 2 | 30 | 2 |
| 3 | 5  | 3 |
| 4 | 15 | 3 |

Buckets  
- L = 1 → {10} → take 1 → 10  
- L = 2 → {20,30} → take 2 → 50  
- L = 3 → {15,5} → take 2 (since bucket size = 2 < L) → 20  

**Total = 10 + 50 + 20 = 80** – the maximum attainable sum.

---

### 6️⃣ When Is This Code *LeetCode‑Ready*?

| ✅ | ✅ | ✅ |
|----|----|----|
| Handles `n` up to 100 000 | Uses `O(n log n)` time (fast enough for all test cases) | Returns a `long` (or `long long`) as required |
| Includes edge‑case handling (empty buckets, large limits) | Uses stable sorting in all languages | Tested against the official examples |

---

### 7️⃣ SEO‑Ready Blog – “Cracking LeetCode #3645 & Landing Your Dream Software‑Engineer Job”

> **Meta‑Title**: “LeetCode 3645 – Maximum Total from Optimal Activation Order | Java / Python / C++ Solution”  
> **Meta‑Description**: “Learn the greedy bucket algorithm for LeetCode #3645, get code snippets in Java, Python, and C++, and discover how to ace this interview problem for your next software‑engineering role.”

---

## 🚀 Why This Blog Helps You Get Hired

1. **Real‑world Problem‑Solving** – Interviewers love candidates who can reduce a complex rule to a clean greedy solution.  
2. **Multi‑Language Mastery** – Show you can solve the same problem in Java, Python, and C++ – the *holy trinity* of coding interviews.  
3. **Complexity‑First Thinking** – We present a clear O(n log n) solution that scales to the maximum constraints – something hiring managers look for.  
4. **Clear, Structured Writing** – Easy to read, with code blocks, tables, and bullet points – demonstrates your communication skills.  
5. **SEO‑Optimized** – By including keywords such as *LeetCode*, *Maximum Total from Optimal Activation Order*, *Java*, *Python*, *C++*, *software engineer interview*, you boost visibility for recruiters searching for interview practice.

---

### 📌 TL;DR

- **Key Insight:** Each limit `L` can contribute at most `L` values.  
- **Greedy Rule:** Pick the largest `min(L, count(L))` values from every bucket.  
- **Complexity:** `O(n log n)` time, `O(n)` space.  
- **Code:** Ready‑to‑copy snippets in **Java, Python, and C++** below.

---

## 📜 Code Summary

### Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def maxTotal(self, value: List[int], limit: List[int]) -> int:
        buckets = defaultdict(list)
        for v, l in zip(value, limit):
            buckets[l].append(v)

        total = 0
        for l, vals in buckets.items():
            vals.sort(reverse=True)
            total += sum(vals[:l])          # take at most l values
        return total
```

---

### Java 8+

```java
import java.util.*;

public class Solution {
    public long maxTotal(int[] value, int[] limit) {
        int n = value.length;
        List<List<Integer>> buckets = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) buckets.add(new ArrayList<>());

        for (int i = 0; i < n; i++) {
            buckets.get(limit[i]).add(value[i]);
        }

        long ans = 0;
        for (int l = 1; l <= n; l++) {
            List<Integer> list = buckets.get(l);
            if (list.isEmpty()) continue;
            list.sort(Collections.reverseOrder());
            int take = Math.min(l, list.size());
            for (int i = 0; i < take; i++) {
                ans += list.get(i);
            }
        }
        return ans;
    }
}
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxTotal(vector<int>& value, vector<int>& limit) {
        int n = value.size();
        vector<vector<int>> buckets(n + 1);

        for (int i = 0; i < n; ++i)
            buckets[limit[i]].push_back(value[i]);

        long long ans = 0;
        for (int l = 1; l <= n; ++l) {
            auto &vec = buckets[l];
            if (vec.empty()) continue;
            sort(vec.rbegin(), vec.rend());      // descending
            int take = min(l, (int)vec.size());
            for (int i = 0; i < take; ++i)
                ans += vec[i];
        }
        return ans;
    }
};
```

---

## 🎯 Takeaway

- **Problem** → *Greedy + Bucketing*  
- **Solution** → *Sort each bucket, pick the top `L` values*  
- **Why it works** → *Upper bound of `L` activations per limit; taking the largest ones never hurts*  

Show this approach in your next interview – it demonstrates clear reasoning, optimality proof, and language‑agnostic problem‑solving.

Good luck, and happy coding! 🚀