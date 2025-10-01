---
title: LeetCode 1906. Minimum Absolute Difference Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 1906. Minimum Absolute Difference Queries – Code + SEO‑Optimized Blog

Below you’ll find **three full, production‑ready solutions** – Java, Python and C++ – that solve LeetCode 1906 in *O((n+q)·100)* time and *O(n·100)* memory.  
After the code you’ll find a **blog article** that explains the problem, the “good, bad, and ugly” parts of the solution, and why it’s a great interview topic for a software‑engineering role.

---

### 📚 Java – Prefix‑Count Solution

```java
import java.util.*;

class Solution {
    public int[] minDifference(int[] nums, int[][] queries) {
        int n = nums.length;
        int q = queries.length;

        /* 1‑based prefix counts: count[i][v] = occurrences of value v (1‑100) in nums[0…i-1] */
        int[][] count = new int[n + 1][101];

        for (int i = 0; i < n; i++) {
            System.arraycopy(count[i], 0, count[i + 1], 0, 101);
            count[i + 1][nums[i]]++;
        }

        int[] ans = new int[q];
        for (int i = 0; i < q; i++) {
            int L = queries[i][0];
            int R = queries[i][1];

            int prev = -1;            // previous present value
            int minDiff = Integer.MAX_VALUE;
            int distinct = 0;

            for (int v = 1; v <= 100; v++) {
                int occ = count[R + 1][v] - count[L][v];
                if (occ > 0) {
                    distinct++;
                    if (prev != -1) {
                        minDiff = Math.min(minDiff, v - prev);
                    }
                    prev = v;
                }
            }

            ans[i] = (distinct <= 1) ? -1 : minDiff;
        }

        return ans;
    }
}
```

> **Why it works**  
> The value range is only `1…100`, so we can keep a running prefix count for each number.  
> For each query we get the frequency difference in O(100) and scan the numbers once to compute the minimum gap.

---

### 🐍 Python – Prefix‑Count Solution

```python
from typing import List

class Solution:
    def minDifference(self, nums: List[int], queries: List[List[int]]) -> List[int]:
        n = len(nums)
        # prefix counts: pref[i][v] = occurrences of v in nums[0:i]
        pref = [[0] * 101 for _ in range(n + 1)]
        for i, val in enumerate(nums, 1):
            pref[i][:] = pref[i - 1][:]
            pref[i][val] += 1

        ans = []
        for L, R in queries:
            prev = -1
            min_diff = 10**9
            distinct = 0

            for v in range(1, 101):
                occ = pref[R + 1][v] - pref[L][v]
                if occ:
                    distinct += 1
                    if prev != -1:
                        min_diff = min(min_diff, v - prev)
                    prev = v

            ans.append(-1 if distinct <= 1 else min_diff)
        return ans
```

> The Python code follows the same idea – a 2‑D prefix table of size `(n+1) × 101` and a single linear scan of the 100 possible values per query.

---

### 🧪 C++ – Prefix‑Count Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> minDifference(vector<int>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        vector<array<int, 101>> pref(n + 1);
        for (int i = 0; i < n; ++i) {
            pref[i + 1] = pref[i];
            ++pref[i + 1][nums[i]];
        }

        vector<int> ans;
        ans.reserve(queries.size());

        for (auto &qr : queries) {
            int L = qr[0];
            int R = qr[1];
            int prev = -1;
            int minDiff = INT_MAX;
            int distinct = 0;

            for (int v = 1; v <= 100; ++v) {
                int occ = pref[R + 1][v] - pref[L][v];
                if (occ) {
                    ++distinct;
                    if (prev != -1)
                        minDiff = min(minDiff, v - prev);
                    prev = v;
                }
            }
            ans.push_back(distinct <= 1 ? -1 : minDiff);
        }

        return ans;
    }
};
```

> Using `array<int,101>` keeps the inner array small and cache‑friendly.

---

## 📖 Blog Article: “Minimum Absolute Difference Queries – The Good, The Bad, and The Ugly”

> **Keywords:** LeetCode 1906, Minimum Absolute Difference Queries, Interview Questions, Java, Python, C++, Algorithm Design, Time Complexity, Space Complexity, Prefix Sum, Mo’s Algorithm, Segment Tree, Job Interview Tips

### 1. Introduction

LeetCode’s **1906. Minimum Absolute Difference Queries** is a mid‑level interview problem that tests your ability to balance *time complexity* with *space efficiency*. While the statement looks simple—find the smallest absolute difference in a sub‑array—it hides a subtle twist: the numbers are small (`1‑100`), but the queries can be huge (`up to 20 000`). This makes naive solutions impractical.

In this article, we’ll walk through:

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Brute‑Force** | Easy to understand | `O(q·n²)` → too slow | Fails on large inputs |
| **Sliding Window / Two‑Pointer** | Works for sorted sub‑arrays | Requires sorting each sub‑array | Extra log factor |
| **Prefix Count (our solution)** | Linear in `q` * 100 | Requires careful handling of 1‑based indices | O(n·100) memory but trivial constants |
| **Segment Tree + BitSet** | Good for larger value ranges | Harder to implement | Still heavy for 100 values |
| **Mo’s Algorithm** | Offline query optimization | Requires block decomposition | Complex coding effort |

### 2. Problem Statement (Simplified)

Given an array `nums[0…n-1]` (`1 ≤ nums[i] ≤ 100`) and `q` queries `[l, r]`, compute for each query the minimum absolute difference between two *distinct* values inside `nums[l…r]`. If the sub‑array contains only one distinct value, answer `-1`.

### 3. Brute‑Force Insight

The simplest approach:
1. Extract `nums[l…r]`.
2. Sort it.
3. Scan adjacent pairs for the smallest difference.

Complexity: `O(q · (r-l+1) log(r-l+1))`. For `n = 10⁵` and `q = 2·10⁴`, this is *hundreds of millions* of operations—completely unacceptable.

### 4. The Prefix‑Count Trick (Our “Good” Solution)

Because the values are bounded between `1` and `100`, we can pre‑compute how many times each value appears up to each index:

```
pref[i][v] = number of times value v occurs in nums[0 … i-1]
```

Construction: `O(n·100)`.

Answering a query `[L, R]`:

1. For each `v = 1…100`, the count in the sub‑array is  
   `cnt[v] = pref[R+1][v] - pref[L][v]`.
2. Scan the 100 possible values once:
   * Keep the last seen value (`prev`).
   * Update `minDiff` as `v - prev` whenever two consecutive values exist.
   * Count distinct numbers.

Because we loop over a *fixed* 100 values, each query costs `O(100)` regardless of sub‑array size. Thus the whole algorithm runs in `O((n+q)·100)` time and `O(n·100)` memory.

#### 4.1 Handling Edge Cases

* **Only one distinct number** → answer `-1`.  
* **No distinct number** (empty sub‑array) → also `-1`.  
* **Multiple identical numbers** → they’re ignored because we need *distinct* values.

#### 4.2 Why Is 100 Small?

The inner loop is bounded, so even with a *tiny* constant factor, the runtime is linear in `q`. The `100` value array is so small that copying it each time (as in the Java `System.arraycopy`) or using `array<int,101>` in C++ keeps the algorithm cache‑friendly.

### 5. Alternative “Bad” Approaches

| Technique | When it shines | Why it’s still bad for this problem |
|-----------|----------------|------------------------------------|
| **Segment Tree + BitSet** | Handles larger ranges (e.g., 10⁶) | Overkill for 100 values; heavier memory |
| **Mo’s Algorithm** | Good for `q` ≈ `n` and unsorted data | Requires block size tuning; coding complexity |
| **Two‑Pointer after Sorting** | Linear in sub‑array size after sort | Sorting each query again adds a log factor |

If you ever get asked to extend this problem to `nums[i] ≤ 10⁶`, you might need a *dynamic* segment tree or a *bitset* per node. But for LeetCode 1906, the prefix‑count is the cleanest.

### 6. Implementation Pitfalls (“Ugly”)

| Pitfall | Fix |
|---------|-----|
| Off‑by‑one indices (0‑based vs 1‑based) | Keep prefix arrays 1‑based (`pref[0]` is all zeros). |
| Forgetting to check distinct count | Always count `distinct` before deciding `-1`. |
| Using large data types unnecessarily | Use `int` (Java) or `array<int,101>` (C++) – no `long long` needed. |

### 7. Complexity Analysis (Recap)

| Operation | Time | Space |
|-----------|------|-------|
| Prefix construction | `O(n·100)` | `O(n·100)` |
| Query answering | `O(q·100)` | — |
| **Total** | **`O((n+q)·100)`** | **`O(n·100)`** |

With `n = 10⁵` and `q = 20 000`, that’s about **2 million** integer operations for the queries, plus **10 million** for the prefix table – easily handled by modern judges in < 0.5 seconds.

### 8. How to Validate Your Solution

| Test | Purpose |
|------|---------|
| **Small array, many queries** | Ensure constant‑time queries |
| **All values same** | Verify `-1` output |
| **Alternating values** | Check gap calculations |
| **Maximum size** (`n=10⁵`, `q=20 000`) | Stress‑test performance |

Using `assert` or unit‑tests in your language of choice keeps your code robust.

### 9. Interview‑Ready Tips

1. **Explain the value bound early.**  
   “Because `nums[i]` ≤ 100, we can treat the array as a histogram.”  
2. **Show the prefix construction diagram.**  
   Visualizing `pref[i][v]` helps the interviewer follow the logic.
3. **Mention alternative solutions.**  
   “If the range were larger, we could use a segment tree with a bitset, or Mo’s algorithm for offline queries.”  
4. **Walk through edge cases.**  
   “Only one distinct value → -1.”  
5. **Highlight the complexity trade‑off.**  
   “We sacrifice a moderate amount of memory (`O(n·100)`) for a dramatic speed‑up.”

### 10. Conclusion

LeetCode 1906 is a *classic* interview problem because it forces you to:

- Recognize hidden constraints (small value range).  
- Think outside the box (prefix counts).  
- Balance time and space.

Your **Java, Python, and C++** implementations above are clean, idiomatic, and ready for a production interview. Mastering this problem not only boosts your algorithmic toolkit but also demonstrates to hiring managers that you can craft efficient solutions for large‑scale data.

---

### 📌 Final Thought

When preparing for a software‑engineering interview, always ask yourself:

> *“Is there a hidden property of the input I can exploit?”*

For LeetCode 1906 the answer is **yes**—the small value range. Use that to your advantage, keep your code clear, and you’ll land the job.

Good luck, and happy coding!