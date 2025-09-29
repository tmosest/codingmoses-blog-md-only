---
title: LeetCode 1906. Minimum Absolute Difference Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1906. Minimum Absolute Difference Queries  
### From “good” to “ugly” – a full‑stack guide for the interview‑ready coder  

---

## TL;DR  
- **Problem** – For each query `[l, r]` return the smallest absolute difference between any two *different* numbers in the sub‑array `nums[l…r]`. If all numbers are identical, return `-1`.  
- **Key Insight** – `nums[i]` is bounded by **1…100**. This tiny domain lets us build a **prefix count table** of size `n × 100`.  
- **Complexity** – `O((n + q) · 100)` time, `O(n · 100)` space. Works comfortably for `n ≤ 1e5`, `q ≤ 2·10⁴`.  
- **Why it matters** – Demonstrates mastery of **prefix sums**, **offline queries**, and **range counting**—all must‑know topics for data‑structure interviews.

---

## 1. Problem Recap  

> Given `nums` (size `n`) and `queries` (`q` pairs `[l, r]`), for each query find  
> ```  
> min |nums[i] – nums[j]|   with l ≤ i < j ≤ r  and nums[i] ≠ nums[j]  
> ```  
> Return `-1` if every element in `[l, r]` is equal.  

`n` can be up to `10⁵`, `q` up to `2·10⁴`.  
`nums[i]` lives in `[1, 100]`.

---

## 2. Naïve Approaches  

| Approach | Time | Space | Verdict |
|----------|------|-------|---------|
| Brute‑force double loop per query | `O(q · (r-l+1)²)` | `O(1)` | Too slow – worst case ~ `10¹⁴`. |
| Sort the sub‑array per query, then scan neighbours | `O(q · (r-l+1) log (r-l+1))` | `O(r-l+1)` | Still too slow. |
| Use a balanced BST or multiset per query | `O(q · (r-l+1) log (r-l+1))` | `O(r-l+1)` | Unnecessary complexity. |
| **Prefix count + scan 1‑100** | `O((n+q)·100)` | `O(n·100)` | **Fast enough** for constraints. |

Because the *value domain* is tiny, we can avoid sorting or BSTs entirely.

---

## 3. The Optimal Solution – Prefix Count Table  

### 3.1 Core Idea  

- Build a 2‑D array `pref[i][v]` = number of occurrences of value `v` (0‑based index, `v ∈ [0,99]`) in the prefix `nums[0…i-1]`.  
- For a query `[l, r]` the count of value `v` in the range is  
  ```text
  count_v = pref[r+1][v] - pref[l][v]
  ```  
- Walk through all `v` from `0` to `99` and collect the values that appear at least once in the range.  
- Since the list is already sorted by `v`, the minimal absolute difference is simply the minimum gap between consecutive present values.  
- If only one value is present → answer `-1`.  

### 3.2 Why This Works  

- **Constant‑time range count**: The prefix table gives `O(1)` per value.  
- **Fixed number of values**: We always iterate over 100 numbers – independent of `n` or `q`.  
- **No sorting needed**: The values are processed in ascending order.  

### 3.3 Complexity  

- **Time**: `O(n·100 + q·100)` = `O((n+q)·100)` → about 1.2 × 10⁷ operations for the worst case.  
- **Space**: `O(n·100)` integers (~ 40 MB) – acceptable in typical interview environments.  

---

## 4. Implementation Highlights  

### 4.1 Java  

```java
class Solution {
    public int[] minDifference(int[] nums, int[][] queries) {
        int n = nums.length;
        int[][] pref = new int[n + 1][100];          // prefix counts

        // build prefix
        for (int i = 0; i < n; i++) {
            System.arraycopy(pref[i], 0, pref[i + 1], 0, 100);
            pref[i + 1][nums[i] - 1]++;               // shift to 0‑based
        }

        int q = queries.length;
        int[] ans = new int[q];

        for (int qi = 0; qi < q; qi++) {
            int l = queries[qi][0];
            int r = queries[qi][1] + 1;              // exclusive

            int prev = -1, minDiff = Integer.MAX_VALUE, distinct = 0;

            for (int v = 0; v < 100; v++) {
                int cnt = pref[r][v] - pref[l][v];
                if (cnt > 0) {
                    distinct++;
                    if (prev != -1) {
                        minDiff = Math.min(minDiff, v - prev);
                    }
                    prev = v;
                }
            }

            ans[qi] = (distinct <= 1) ? -1 : minDiff;
        }
        return ans;
    }
}
```

### 4.2 Python  

```python
from typing import List

class Solution:
    def minDifference(self, nums: List[int], queries: List[List[int]]) -> List[int]:
        n = len(nums)
        pref = [[0] * 100 for _ in range(n + 1)]

        for i, val in enumerate(nums, 1):
            pref[i] = pref[i - 1][:]              # copy previous row
            pref[i][val - 1] += 1

        res = []
        for l, r in queries:
            r += 1
            prev = None
            distinct = 0
            min_diff = 101
            for v in range(100):
                if pref[r][v] - pref[l][v]:
                    distinct += 1
                    if prev is not None:
                        min_diff = min(min_diff, v - prev)
                    prev = v
            res.append(-1 if distinct <= 1 else min_diff)
        return res
```

### 4.3 C++  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> minDifference(vector<int>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        vector<array<int, 100>> pref(n + 1);
        for (int i = 1; i <= n; ++i) {
            pref[i] = pref[i - 1];                // copy
            pref[i][nums[i - 1] - 1]++;           // shift to 0‑based
        }

        vector<int> ans;
        ans.reserve(queries.size());

        for (auto &q : queries) {
            int l = q[0], r = q[1] + 1;           // make r exclusive
            int prev = -1, minDiff = 101, distinct = 0;

            for (int v = 0; v < 100; ++v) {
                if (pref[r][v] - pref[l][v]) {
                    ++distinct;
                    if (prev != -1) minDiff = min(minDiff, v - prev);
                    prev = v;
                }
            }
            ans.push_back(distinct <= 1 ? -1 : minDiff);
        }
        return ans;
    }
};
```

---

## 5. Edge Cases & Pitfalls  

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| Forgetting to make `r` exclusive when using prefix sums | Off‑by‑one error → wrong counts | Use `r + 1` when indexing the prefix array. |
| Using `int[][]` in Java without copying rows | `System.arraycopy` is necessary to avoid shared references | Copy each row or use `Arrays.copyOf`. |
| Assuming the domain can change | If `nums[i]` > 100, the algorithm breaks | For larger ranges, use `TreeSet`/segment tree or compress values. |
| Not handling the `-1` case | Returning `0` when all elements equal | Track number of distinct values and return `-1` if ≤ 1. |
| Using `int[][]` for prefix in Python | Lists of lists are mutable; copying per row needed | Use `pref[i] = pref[i-1][:]` inside the loop. |

---

## 6. Variations of the Problem  

| Variant | Suggested Solution |
|---------|--------------------|
| **Unbounded `nums[i]`** (e.g., `1…10⁶`) | Compress the values, then build a 2‑D prefix table or use a **BIT / Fenwick** on the compressed indices. |
| **Large query count** (`q ≈ 1e6`) | The `100`‑iteration still works, but you might hit memory limits | Use a **bitset** (`std::bitset<101>`) per prefix to cut space. |
| **Online queries** | Need data‑structure that supports insert/delete on the fly | Balanced BST (`TreeMap`) per prefix, or maintain a multiset for the sliding window. |
| **Dynamic updates to `nums`** | Prefix table becomes stale | Rebuild after every update, or use a **segment tree** that supports point updates. |

---

## 6. Why This Solution Looks “Good” in Interviews  

1. **Shows Problem‑Solving Skills** – You spotted the bound on `nums[i]` and leveraged it.  
2. **Uses Classic Data‑Structures** – Prefix sums are a staple; you turned them into a 2‑D range counting table.  
3. **Handles Large Inputs Gracefully** – Time and memory stay linear in `n` and `q`.  
4. **Cross‑Language Readiness** – We supplied clean, idiomatic code in Java, Python, and C++.  
5. **Extensible** – The same pattern (prefix table + fixed domain scan) works for many “range‑min‑diff” style problems.

---

## 6. Take‑away Checklist for Interviewers  

- [ ] Build a `pref` table correctly (copy rows, shift values).  
- [ ] Convert query `[l, r]` to `[l, r+1)` before subtracting prefixes.  
- [ ] Scan `v` from `0` to `99` – no extra sorting needed.  
- [ ] Return `-1` when `distinct <= 1`.  
- [ ] Verify with corner cases:  
  - Single element queries.  
  - All equal values.  
  - Full array query.  

---

## 6. Closing Thoughts  

The **tiny value range** transforms a seemingly heavy‑weight range query problem into a simple, linear‑time algorithm.  
Mastering this pattern demonstrates:

- **Creative use of constraints** – turning a problem that looks like it needs a segment tree into a `O(1)` counting problem.  
- **Effective offline processing** – building data structures once and re‑using them for many queries.  
- **Clear, maintainable code** – no need for obscure hacks, just a clean prefix table and a single pass over 100 values.  

> *If you can explain this solution in an interview and write the Java/Python/C++ code on the spot, you’ll pass the “hard” data‑structure question with flying colors.*

---

## SEO & Social Tags  

- **Meta description**: “Solve LeetCode 1906 – Minimum Absolute Difference Queries in O((n+q)·100). Prefix count table, Java/Python/C++ solutions, interview tips, edge cases.”  
- **Keywords**: `minimum absolute difference queries`, `prefix sums`, `range counting`, `LeetCode 1906`, `data structure interview`, `segment tree`, `Mo's algorithm`, `C++ solution`, `Java solution`, `Python solution`, `offline queries`.  

Happy coding, and good luck on your next interview!