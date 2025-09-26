---
title: LeetCode 1906. Minimum Absolute Difference Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1906 – Minimum Absolute Difference Queries  
### The Good, The Bad, & The Ugly – Plus a Full‑stack Solution in Java, Python & C++

> **Keywords** – *LeetCode 1906, Minimum Absolute Difference Queries, interview coding problem, prefix sums, time complexity, job interview, algorithm design*  

---

### 1. Problem Recap

Given

* `nums` – an array of `n` integers (`2 ≤ n ≤ 10⁵`, `1 ≤ nums[i] ≤ 100`)  
* `queries` – an array of `m` ranges, each `queries[i] = [lᵢ, rᵢ]` (`1 ≤ m ≤ 2·10⁴`, `0 ≤ lᵢ < rᵢ < n`)

For every query compute the **minimum absolute difference** between any two *different* elements in the sub‑array `nums[lᵢ … rᵢ]`.  
If the sub‑array contains only one distinct value, the answer is `-1`.

> **Examples**  
> ```text
> nums = [1,3,4,8]
> queries = [[0,1],[1,2],[2,3],[0,3]]
> output = [2,1,4,1]
> ```

---

### 2. Why This Problem Is Interview‑worthy

* **Edge‑case heavy** – you have to remember that identical elements do **not** yield a difference of `0`.  
* **Large input sizes** – a naive O(`n·m`) solution would blow up.  
* **Limited value range** – `nums[i]` is capped at `100`, which hints at a counting‑based trick.  
* **Multiple queries** – encourages the use of **prefix sums** or *offline* techniques.

These constraints make the problem a perfect playground for demonstrating algorithmic thinking and coding style – exactly what recruiters love.

---

## 3. The “Good” – Prefix‑Sum Count Matrix

Because each `nums[i]` is in `[1, 100]`, we can build a **frequency prefix matrix**:

| i | count[1] | count[2] | … | count[100] |
|---|----------|----------|---|-------------|
| 0 | 0        | 0        | … | 0           |
| 1 | …        | …        | … | …           |
| … | …        | …        | … | …           |

* `count[i][v]` = how many times the value `v` appears in the first `i` elements (`nums[0 … i‑1]`).  
* `count[n][v]` holds the full frequency of `v`.

**Query Processing**

For a query `[l, r]` (inclusive):

1. `freq[v] = count[r+1][v] - count[l][v]` – frequency of `v` inside the sub‑array.  
2. Collect all `v` with `freq[v] > 0` (i.e., the distinct values present).  
3. If only one distinct value → answer `-1`.  
4. Otherwise, the distinct values are already in ascending order (1…100).  
   The minimum absolute difference is the minimum difference between consecutive distinct values.

This approach is **O(100)** per query – independent of sub‑array length.

---

## 4. The “Bad” – What to Avoid

| Issue | Why It’s Bad |
|-------|--------------|
| **Quadratic loops** – `O(n·m)` or `O(n·m·100)` naïve scans | For `n = 100 000`, `m = 20 000` this is astronomical. |
| **Large memory per query** – building a hash map for each query | Hash maps have overhead and slowdowns; unnecessary given the tiny value domain. |
| **Ignoring the value bound** – treating `nums` as arbitrary ints | Missing the opportunity for a `O(100)` solution. |

---

## 5. The “Ugly” – Trying to Force a Generic Solution

Some interviewees might attempt:

* **Mo’s algorithm** (offline processing with block decomposition) – works but is overkill for this value‑bounded problem.  
* **Segment tree with bitset** – a clever data structure but adds implementation complexity and memory overhead.  
* **Sorting each sub‑array** – `O((r-l+1) log(r-l+1))` per query; far slower than the prefix‑sum trick.

While these “ugly” solutions can pass, they sacrifice readability and performance – both of which are important in production code interviews.

---

## 6. Full Implementations

Below are clean, ready‑to‑run solutions in **Java, Python, and C++**.  
All share the same prefix‑sum counting logic.

### 6.1 Java

```java
import java.util.*;

public class Solution {
    public int[] minDifference(int[] nums, int[][] queries) {
        int n = nums.length;
        // count[i][v] -> freq of value v (1‑based) in first i elements
        int[][] count = new int[n + 1][101]; // 101 because values are 1…100

        for (int i = 0; i < n; i++) {
            System.arraycopy(count[i], 0, count[i + 1], 0, 101);
            count[i + 1][nums[i]]++;          // increment the value that appears
        }

        int m = queries.length;
        int[] ans = new int[m];

        for (int qi = 0; qi < m; qi++) {
            int l = queries[qi][0];
            int r = queries[qi][1];
            int prev = -1;
            int best = Integer.MAX_VALUE;
            int distinct = 0;

            for (int val = 1; val <= 100; val++) {
                int freq = count[r + 1][val] - count[l][val];
                if (freq > 0) {
                    distinct++;
                    if (prev != -1) {
                        best = Math.min(best, val - prev);
                    }
                    prev = val;
                }
            }

            ans[qi] = (distinct <= 1) ? -1 : best;
        }
        return ans;
    }

    // Driver to test the implementation
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[] nums = {1,3,4,8};
        int[][] queries = {{0,1},{1,2},{2,3},{0,3}};
        System.out.println(Arrays.toString(sol.minDifference(nums, queries)));
        // Expected: [2, 1, 4, 1]
    }
}
```

### 6.2 Python

```python
from typing import List

class Solution:
    def minDifference(self, nums: List[int], queries: List[List[int]]) -> List[int]:
        n = len(nums)
        # count[i][v] : freq of value v in first i elements
        count = [[0] * 101 for _ in range(n + 1)]
        for i, num in enumerate(nums, 1):
            # copy previous row
            count[i] = count[i-1].copy()
            count[i][num] += 1

        ans = []
        for l, r in queries:
            prev = -1
            best = 101
            distinct = 0
            for val in range(1, 101):
                freq = count[r+1][val] - count[l][val]
                if freq:
                    distinct += 1
                    if prev != -1:
                        best = min(best, val - prev)
                    prev = val
            ans.append(-1 if distinct <= 1 else best)
        return ans


if __name__ == "__main__":
    sol = Solution()
    nums = [1, 3, 4, 8]
    queries = [[0, 1], [1, 2], [2, 3], [0, 3]]
    print(sol.minDifference(nums, queries))
    # Output: [2, 1, 4, 1]
```

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> minDifference(vector<int>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        // count[i][v] : frequency of value v in first i elements
        vector<array<int, 101>> count(n + 1);
        for (int i = 0; i < n; ++i) {
            count[i + 1] = count[i];          // copy previous row
            count[i + 1][nums[i]]++;         // increment the present value
        }

        vector<int> ans;
        for (auto &q : queries) {
            int l = q[0], r = q[1];
            int prev = -1;
            int best = 101;
            int distinct = 0;
            for (int val = 1; val <= 100; ++val) {
                int freq = count[r + 1][val] - count[l][val];
                if (freq > 0) {
                    ++distinct;
                    if (prev != -1) best = min(best, val - prev);
                    prev = val;
                }
            }
            ans.push_back(distinct <= 1 ? -1 : best);
        }
        return ans;
    }
};

int main() {
    Solution sol;
    vector<int> nums = {1, 3, 4, 8};
    vector<vector<int>> queries = {{0, 1}, {1, 2}, {2, 3}, {0, 3}};
    auto res = sol.minDifference(nums, queries);
    for (int x : res) cout << x << ' ';
    // Expected: 2 1 4 1
}
```

> **Why this works**  
> * `array<int, 101>` keeps the value domain fixed, giving O(100) time and O(n·100) memory.  
> * All language versions copy the row in O(100) time, not using any heavy data structures.

---

## 7. Alternative “Nice” Approaches

| Approach | When It Makes Sense | Trade‑off |
|----------|---------------------|-----------|
| **Mo’s Algorithm** (offline) | If you’re given an *unbounded* value domain. | More code, but still `O((n+m)√n)` ≈ `O(2·10⁵)` for this input – still fine but unnecessary. |
| **Segment Tree + Bitset** | If you need fast *range OR* queries on larger values. | Each node stores a 100‑bit bitset → `O(log n)` per query; great for contests but verbose for interviews. |
| **Binary Indexed Tree + Bitset** | Similar to the segment tree but uses fenwick trees. | Same trade‑off: more complex than a prefix sum. |

For most interview scenarios, the **prefix‑sum counting matrix** is the sweet spot:  
*Fast*, *memory‑efficient*, *easy to read*, and *guaranteed correct* because it leverages the hard‑coded bound on `nums[i]`.

---

## 8. Quick‑Reference: Complexity Summary

| Step | Complexity (Java / Python / C++) | Memory |
|------|----------------------------------|--------|
| Build prefix matrix | **O(n·100)** |  `(n+1)·101` integers ≈ **40 MB** |
| Each query | **O(100)** | – |
| Total | **O((n + m)·100)** | – |

Given the constraints (`n ≤ 10⁵`, `m ≤ 2·10⁴`, `nums[i] ≤ 100`), this solution runs in well under a second and uses less than 50 MB – perfect for a coding interview.

---

## 9. Take‑away for Job Interviews

1. **Spot the hidden constraint** – the value bound of `100` is your “golden key”.  
2. **Use prefix sums** – they’re a classic tool for answering range queries quickly.  
3. **Keep it simple** – avoid over‑engineering; a straightforward counting array beats a fancy segment tree in readability.  
4. **Test edge cases** – identical elements, single distinct value, and queries that span the whole array.  

By presenting this problem with the prefix‑sum trick, you demonstrate:

* **Problem decomposition** (value bound → counting)  
* **Space–time trade‑off awareness** (prefix matrix vs. hash maps)  
* **Clean code** that would shine in a real‑world setting.

---

### 🚀 Bonus: Quick Test Harness (All Languages)

```text
Input:
nums    = [1, 3, 4, 8]
queries = [[0,1], [1,2], [2,3], [0,3]]

Output:
[2, 1, 4, 1]
```

You can paste the Java, Python, or C++ snippet above into your IDE or an online compiler and run the test harness. All three will output the expected array.

---

Good luck with your interview! Remember:  
**Spot the bound ➜ build a prefix matrix ➜ process queries in O(100)** – that’s the recipe for a clean, interview‑ready solution.  

Happy coding! 🧑‍💻💡