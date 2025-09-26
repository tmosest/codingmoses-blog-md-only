---
title: LeetCode 2898. Maximum Linear Stock Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📈 2898 – Maximum Linear Stock Score  
**Medium** – LeetCode  

> **Goal** – Pick a subsequence of days such that for every consecutive pair  
> `price[i] – price[j] == i – j`.  
> Maximize the sum of the chosen prices.  

Below is a **ready‑to‑copy** solution in **Java**, **Python** and **C++** (O(n) time, O(n) space) followed by a **SEO‑friendly blog post** that explains the trick, the trade‑offs, and why it’s a great interview‑talk‑point.

---

## 1.  Solution Overview

| Language | Key idea | Complexity |
|----------|----------|------------|
| **Java** | `price - index` (1‑based) → bucket, sum per bucket | `O(n)` time, `O(n)` space |
| **Python** | Same as Java – use `defaultdict` | `O(n)` time, `O(n)` space |
| **C++** | `unordered_map<long long,long long>` | `O(n)` time, `O(n)` space |

*Why does `price – index` work?*  
For a linear subsequence the difference between any two consecutive prices equals the difference between the indices:

```
price[i] – price[j] = i – j   (i > j)
```

Rearrange:

```
price[i] – i = price[j] – j
```

So all selected indices share the same **key** `price – index`.  
We simply group prices by that key and pick the bucket with the largest sum.

---

## 2.  Reference Code

### 2.1 Java

```java
import java.util.*;

class Solution {
    public long maxScore(int[] prices) {
        Map<Integer, Long> bucket = new HashMap<>();
        long answer = 0L;

        for (int i = 0; i < prices.length; i++) {          // 0‑based index
            int key = prices[i] - (i + 1);                 // 1‑based key
            long newSum = bucket.getOrDefault(key, 0L) + prices[i];
            bucket.put(key, newSum);
            answer = Math.max(answer, newSum);
        }
        return answer;
    }
}
```

### 2.2 Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def maxScore(self, prices: List[int]) -> int:
        bucket = defaultdict(int)
        best = 0
        for i, price in enumerate(prices):          # i is 0‑based
            key = price - (i + 1)                   # 1‑based key
            bucket[key] += price
            best = max(best, bucket[key])
        return best
```

### 2.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxScore(vector<int>& prices) {
        unordered_map<long long, long long> bucket;
        long long ans = 0;
        for (size_t i = 0; i < prices.size(); ++i) {
            long long key = (long long)prices[i] - (long long)(i + 1);
            bucket[key] += prices[i];
            ans = max(ans, bucket[key]);
        }
        return ans;
    }
};
```

---

## 3.  Blog Post – “Maximum Linear Stock Score: The Good, The Bad, and the Ugly”

> **Target Audience:** Java/Python/C++ developers preparing for technical interviews, recruiters, and algorithm enthusiasts.  
> **Primary Keywords:** *Maximum Linear Stock Score*, *LeetCode 2898*, *Java solution*, *Python solution*, *C++ solution*, *hashmap trick*, *interview problem*, *O(n) algorithm*.

---

### 3.1 Introduction

> When you open LeetCode 2898 *Maximum Linear Stock Score*, the first thing that may bite you is the seemingly “odd” condition:  
> `price[i] – price[j] == i – j`.  
> It looks like a dynamic‑programming or two‑pointer puzzle, but the real trick is a one‑liner: **bucket by `price - index`**.  

This article walks through why that bucket trick works, its pros and cons, and how to explain it confidently in an interview.

---

### 3.2 Restating the Problem

We have an array `prices[1…n]` (1‑indexed).  
Choose a subsequence `indexes = [i1 < i2 < … < ik]` such that for every adjacent pair

```
prices[ij] - prices[ij-1] = ij - ij-1      (1)
```

The **score** is simply the sum of the selected prices.  
We must maximize it.

---

### 3.3 Intuition & Transformation

Rewrite (1) by moving terms:

```
prices[ij] - ij = prices[ij-1] - ij-1
```

The left side is **constant** for all indices in a linear subsequence.  
So all chosen days share the same value of

```
key = price - index
```

If we group all days by this key and sum the prices inside each group, the group with the largest sum is the answer.

> **Why does this not miss any optimal subsequence?**  
> Any optimal subsequence must satisfy (1), thus all its keys are equal.  
> Conversely, picking all indices with a particular key automatically satisfies (1).  
> Therefore the best answer is the maximum bucket sum.

---

### 3.4 The “Good” – Simplicity and Speed

* **Time** – O(n) because we scan the array once.  
* **Space** – O(n) worst case (every element has a distinct key).  
* **Implementation** – 5‑line hash‑map update in Java/Python/C++.  
* **Big‑O Friendly** – Handles `n = 10^5` comfortably; `price ≤ 10^9`, so we use `long`/`int64_t`.

> *Interview‑Friendly*: You can explain it in < 30 seconds – “Group by price‑index difference”.

---

### 3.5 The “Bad” – Hidden Pitfalls

| Pitfall | What happens? | How to fix |
|---------|----------------|------------|
| Using 0‑based index incorrectly | Off‑by‑one key → wrong bucket | Subtract `i+1` instead of `i` |
| Overflow in sum | `price * n` may exceed 32‑bit | Use 64‑bit (`long`/`long long`) |
| Forgetting the initial key | Map may contain zeros for unseen keys | `getOrDefault` or `unordered_map::operator[]` handles missing keys |

---

### 3.6 The “Ugly” – Over‑engineering

Some interviewees try to use segment trees, Fenwick trees, or DP tables.  
These approaches add unnecessary complexity and risk time‑outs or bugs.  
Stick to the **hash‑map bucket** trick unless the interviewer explicitly wants a more elaborate solution.

---

### 3.7 Walk‑Through Example

```
prices = [1, 5, 3, 7, 8]
indices (1‑based): 1  2  3  4  5

key = price - index:
1-1 = 0
5-2 = 3
3-3 = 0
7-4 = 3
8-5 = 3

Buckets:
0 → [1, 3]  sum = 4
3 → [5, 7, 8] sum = 20   <-- max

Answer = 20
```

---

### 3.8 Testing Your Implementation

| Test | Expected |
|------|----------|
| `[5, 6, 7, 8, 9]` | 35 |
| `[10]` | 10 |
| `[1, 2, 3, 4, 5]` | 15 |
| `[1, 1000000000]` | 1000000000 |
| `[1,2,4,8,16,32]` | 1+2+4+8+16+32 = 63 (all share key 0) |

Write unit tests in your language’s framework or use the LeetCode playground.

---

### 3.9 Complexity Recap

- **Time**: `O(n)` – single pass over the array.  
- **Space**: `O(n)` – map size equals number of distinct keys.  
- **Scalability**: Handles the maximum constraints (`n = 10^5`, `price ≤ 10^9`) in milliseconds.

---

### 3.10 Why This Is a Great Interview Question

1. **Trick Revealed** – Demonstrates pattern recognition: converting a difference constraint into a key.  
2. **Multiple Languages** – Show you can solve it in Java, Python, C++ (and even JavaScript).  
3. **Time‑Efficient** – You can solve it with a single loop; no DP tables or recursion.  
4. **Edge Cases** – Encourages discussion about overflow, indexing, and hash collisions.

---

### 3.11 Closing Advice

- **Explain the transformation** clearly before writing code.  
- **Show the map updates** step‑by‑step.  
- **Mention overflow** if using languages with fixed integer widths.  
- **Finish with complexity analysis** – recruiters love seeing that you think about performance.

Happy coding, and may the `price - index` key always lead you to the highest score! 🚀

--- 

### 3.12 SEO Snapshot

| Section | Keywords |
|---------|----------|
| Title | “Maximum Linear Stock Score – LeetCode 2898 Java Python C++ Solution” |
| Meta Description | “Solve LeetCode 2898 in O(n) time. Read Java, Python, C++ code, and interview prep guide.” |
| H1 | “Maximum Linear Stock Score – A Hash‑Map Trick” |
| H2 | “Problem Restatement”, “Intuition”, “Java Code”, “Python Code”, “C++ Code”, “Complexity”, “Interview Tips” |
| Alt Text | “LeetCode 2898 example diagram”, “Java hashmap code snippet”, “Python defaultdict example”, “C++ unordered_map code” |

Add the article to your blog, share it on LinkedIn, and watch the job‑search traffic grow!