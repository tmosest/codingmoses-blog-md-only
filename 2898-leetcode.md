---
title: LeetCode 2898. Maximum Linear Stock Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2898 – “Maximum Linear Stock Score”  
### 1️⃣ Problem Recap  
You’re given a 1‑indexed array `prices`.  
You must choose a **linear** subsequence – meaning for every consecutive pair in the chosen indices

```
prices[idx[j]] - prices[idx[j‑1]] == idx[j] - idx[j‑1]
```

The score of the subsequence is simply the sum of the selected prices.  
Return the **maximum possible score**.

> **Constraints**  
> * `1 ≤ prices.length ≤ 10⁵`  
> * `1 ≤ prices[i] ≤ 10⁹`  

The challenge is to do it in **O(n)** time and O(n) extra memory – ideal for a coding‑interview setting.

---

## 2️⃣ Intuition & “Good, the Bad, and the Ugly”

| Aspect | What to Do | Why It Matters |
|--------|------------|----------------|
| **Good – Reduce the condition** | Transform the linearity constraint `p[j] - p[i] = idx[j] - idx[i]` into a single key: `key = price - index`. | All elements that belong to the same linear subsequence will have **identical `key` values**. |
| **Bad – Beware of overflow** | Scores can reach `10⁹ × 10⁵ = 10¹⁴`. | Use 64‑bit (`long` / `long long` / Python `int`) everywhere. |
| **Ugly – Edge‑case indexing** | The array is 1‑indexed in the statement but Java/C++ arrays are 0‑indexed. | Subtract 1 (or adjust the key formula) to keep the math clean. |

---

## 3️⃣ The Core Algorithm – One‑pass HashMap

1. Iterate over the array once.  
2. For each element `prices[i]` (0‑based), compute `diff = prices[i] - (i + 1)` (because the statement uses 1‑based indices).  
3. Add `prices[i]` to the running sum of that `diff` in a hashmap.  
4. Keep a global `maxSum` that tracks the largest value ever seen in the hashmap.  
5. Return `maxSum` after the loop.

The hashmap key `diff` groups all prices that can appear in the same linear subsequence.  
The hashmap value is the total score for that group so far.  
Because we always add the current price to its group, the value in the map is exactly the score of a linear subsequence ending at the current day.

**Time Complexity:** `O(n)`  
**Space Complexity:** `O(n)` (worst case every `diff` is unique)

---

## 4️⃣ Full Code Samples

### 4.1 Java

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    public long maxScore(int[] prices) {
        Map<Integer, Long> diffSum = new HashMap<>();
        long best = 0;
        for (int i = 0; i < prices.length; i++) {
            // 1‑based index = i+1
            int diff = prices[i] - (i + 1);
            long newSum = diffSum.getOrDefault(diff, 0L) + prices[i];
            diffSum.put(diff, newSum);
            best = Math.max(best, newSum);
        }
        return best;
    }
}
```

### 4.2 Python

```python
from typing import List
from collections import defaultdict

class Solution:
    def maxScore(self, prices: List[int]) -> int:
        diff_sum = defaultdict(int)
        best = 0
        for i, price in enumerate(prices):
            # 1‑based index = i+1
            diff = price - (i + 1)
            diff_sum[diff] += price
            best = max(best, diff_sum[diff])
        return best
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxScore(vector<int>& prices) {
        unordered_map<long long, long long> diffSum; // key: diff, value: sum
        long long best = 0;
        for (size_t i = 0; i < prices.size(); ++i) {
            long long diff = static_cast<long long>(prices[i]) - static_cast<long long>(i + 1);
            diffSum[diff] += prices[i];
            best = max(best, diffSum[diff]);
        }
        return best;
    }
};
```

> **Note on C++ unordered_map:**  
> `unordered_map<long long, long long>` keeps the key and value in 64‑bit to avoid overflow.

---

## 5️⃣ Blog Post: “Maximum Linear Stock Score – The Good, the Bad, and the Ugly”

### 📝 Table of Contents  
1. [What Is “Maximum Linear Stock Score”?](#what-is)  
2. [Why This Problem Appears in Interviews](#why)  
3. [Algorithm Walkthrough](#algorithm)  
   * 3.1 Transforming the Condition  
   * 3.2 One‑Pass HashMap Strategy  
4. [Edge Cases & Gotchas](#edge)  
5. [Alternative Approaches (and Why They’re Not Ideal)](#alternative)  
6. [Time & Space Complexity](#complexity)  
7. [Code Samples – Java / Python / C++](#samples)  
8. [Interview Tips & How to Nail This Problem](#tips)  
9. [Conclusion](#conclusion)

---

### 1️⃣ What Is “Maximum Linear Stock Score”? <a name="what-is"></a>

LeetCode 2898 asks you to pick a subsequence of stock prices such that the difference between consecutive prices **exactly equals** the difference between their indices.  
The goal is to maximize the sum of the chosen prices.  
Think of it as finding the tallest mountain where the slope between each step is *exactly* the horizontal distance you move.

---

### 2️⃣ Why This Problem Appears in Interviews <a name="why"></a>

* **Linear time is expected.**  
  Interviewers love problems that can be solved in **O(n)** – it shows you understand the input size and can craft efficient solutions.

* **HashMap skills are tested.**  
  The trick is to map `price - index` to a running sum, a classic “difference array” trick.

* **Edge‑case awareness.**  
  The need to use 64‑bit integers and handle 1‑ vs 0‑based indexing is a subtle but crucial point.

---

### 3️⃣ Algorithm Walkthrough <a name="algorithm"></a>

#### 3.1 Transforming the Condition

The linearity requirement:

```
prices[idx[j]] - prices[idx[j-1]] == idx[j] - idx[j-1]
```

Rearrange:

```
prices[idx[j]] - idx[j] == prices[idx[j-1]] - idx[j-1]
```

So **every element in a valid subsequence has the same value of `price - index`**.  
We can call this value the *diff*.

#### 3.2 One‑Pass HashMap Strategy

1. **Iterate once** over the array.  
2. Compute `diff = price - (index + 1)` (index is 0‑based).  
3. Add the current price to `diffSum[diff]`.  
4. Track the maximum value seen in `diffSum`.  

Because all prices with the same `diff` can be chained together, the sum stored in `diffSum[diff]` is precisely the score of a valid linear subsequence ending at the current day.

---

### 4️⃣ Edge Cases & Gotchas <a name="edge"></a>

| Issue | Fix |
|-------|-----|
| 1‑based vs 0‑based indices | Subtract `i + 1` from `price` when computing `diff`. |
| Large sums up to 10¹⁴ | Use `long` (Java), `long long` (C++), or Python’s built‑in `int`. |
| Negative `diff` values | HashMap / unordered_map keys can be negative – no special handling needed. |
| Duplicate `diff` values | `diffSum[diff] += price` merges them correctly, building the optimal subsequence. |

---

### 5️⃣ Alternative Approaches (and Why They’re Not Ideal) <a name="alternative"></a>

| Approach | Complexity | Verdict |
|----------|------------|---------|
| Brute‑force over all subsequences | O(2ⁿ) | Exponential – infeasible for n = 10⁵. |
| DP over indices with nested loops | O(n²) | Too slow. |
| Sorting by `price - index` then greedy | O(n log n) | Works but uses extra sorting and is unnecessary; the hashmap approach is simpler and faster. |

---

### 6️⃣ Time & Space Complexity <a name="complexity"></a>

| Measure | Result |
|---------|--------|
| Time | **O(n)** – single pass, constant‑time hashmap ops. |
| Space | **O(n)** – worst case every element has a distinct `diff`. |

---

### 7️⃣ Code Samples – Java / Python / C++ <a name="samples"></a>

*(See the “Full Code Samples” section above for language‑specific implementations.)*

---

### 8️⃣ Interview Tips & How to Nail This Problem <a name="tips"></a>

1. **Clarify the indexing.** Ask the interviewer whether the input is 0‑based or 1‑based – this saves a common mistake.  
2. **Explain the transformation early.** Show that you’re turning a pairwise condition into a single key.  
3. **Mention overflow early.** Highlight the need for 64‑bit integers – it demonstrates awareness of constraints.  
4. **Talk about the hashmap strategy.** Mention that it’s a classic “difference” trick used in many problems (e.g., longest subarray with sum zero).  
5. **Verify with examples.** Run the algorithm mentally on a small array to prove correctness.  

---

### 9️⃣ Conclusion <a name="conclusion"></a>

LeetCode 2898 is a textbook example of how a clever mathematical reformulation (`price - index`) turns a seemingly complex condition into a simple hash grouping problem.  
With an **O(n)** time, **O(n)** space solution and careful handling of 64‑bit arithmetic, you can confidently tackle this problem in an interview and impress recruiters with your algorithmic efficiency.

Happy coding! 🚀

--- 

## 🎯 SEO‑Optimized Keywords

* “Maximum Linear Stock Score”  
* “LeetCode 2898 solution”  
* “Java hash map linear stock score”  
* “Python maximum linear stock score”  
* “C++ 2898 LeetCode solution”  
* “Interview coding problems 2025”  
* “Dynamic programming linear subsequence”  
* “Optimize stock price subsequence”  
* “Job interview algorithm tips”  

Use these keywords naturally throughout blog headings, meta descriptions, and the article body to boost visibility for recruiters searching for algorithmic expertise.