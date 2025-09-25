---
title: LeetCode 1906. Minimum Absolute Difference Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1906 – *Minimum Absolute Difference Queries*  
**A complete, production‑ready solution in Java, Python & C++ + an SEO‑friendly interview‑blog**

---

### 1. 📌 Problem Recap  

> **Minimum absolute difference** of an array `a` is the smallest `|a[i] - a[j]|` for `i < j` where `a[i] != a[j]`.  
> If all elements are identical, the answer is `-1`.

You are given:  
- `nums[0 … n‑1]` (1 ≤ `n` ≤ 10⁵, 1 ≤ `nums[i]` ≤ 100)  
- `queries[0 … q‑1]`, each query is `[l, r]` (0 ≤ `l` < `r` < `n`)

For every query you must return the minimum absolute difference of the sub‑array `nums[l … r]`.

**Constraints**

|   |  |   |
|---|---|---|
| `n` | 2 … 10⁵ | |
| `q` | 1 … 2 × 10⁴ | |
| `nums[i]` | 1 … 100 | (small domain) |

---

### 2. 🤔 Naïve & Sub‑optimal Approaches  

| Approach | Time per query | Total time | Comments |
|----------|----------------|------------|----------|
| Scan sub‑array, sort, check adjacent pairs | `O(k log k)` (`k = r-l+1`) | `O(q · n log n)` | Works only for tiny `n`. |
| Sliding window with balanced BST (multiset) | `O(k log k)` | Same | Still too slow for worst case. |
| Segment tree + bitset | `O(log n)` per query (bitset operations) | `O(q log n)` | Requires 100‑bit bitsets; more code. |

All of the above are unnecessary because **`nums[i]` is bounded by 100**. That small range allows a *prefix‑sum* trick that turns every query into a handful of integer operations.

---

### 3. ✅ The Optimal Idea – Prefix Count Matrix  

For each value `v` in `[1, 100]` we pre‑compute how many times it appears up to every index:

```
cnt[i+1][v] = cnt[i][v] + (nums[i] == v ? 1 : 0)
```

`cnt` has dimensions `(n+1) × 101`.  
With this matrix, the number of occurrences of `v` in sub‑array `[l, r]` is:

```
occ(v, l, r) = cnt[r+1][v] - cnt[l][v]
```

**Answering a query**

1. For each `v` (1 … 100) check if `occ(v, l, r) > 0`.  
   *If yes* → `v` is present in the sub‑array.  
2. Keep the present values in increasing order (we naturally scan from 1 to 100).  
3. The minimum absolute difference is the minimum gap between consecutive present values.  
   *If only one value exists* → answer `-1`.

Because we only touch 100 numbers per query, the work is *O(100 · q)* ≈ *O(q)*.

**Space**: `(n+1) × 101` integers ≈ 40 MB in Java/C++ (fine for the limits).  
**Time**:  
- Pre‑processing: `O(n · 100)`  
- Each query: `O(100)`  
Total: `O((n + q) · 100)` – easily fast enough.

---

### 4. 📦 Code Implementations  

Below are clean, self‑contained implementations in **Java, Python, and C++**.  
All use the same prefix‑count logic described above.

---

#### 4.1 Java

```java
import java.util.*;

public class Solution {
    public int[] minDifference(int[] nums, int[][] queries) {
        int n = nums.length;
        int[][] pref = new int[n + 1][101]; // 1‑based values

        // Build prefix counts
        for (int i = 0; i < n; i++) {
            for (int v = 1; v <= 100; v++) {
                pref[i + 1][v] = pref[i][v];
            }
            pref[i + 1][nums[i]]++;      // increment count of current value
        }

        int[] ans = new int[queries.length];

        for (int qi = 0; qi < queries.length; qi++) {
            int l = queries[qi][0];
            int r = queries[qi][1];
            int prev = -1;        // previous present value
            int best = 101;       // large sentinel

            for (int v = 1; v <= 100; v++) {
                if (pref[r + 1][v] - pref[l][v] > 0) {
                    if (prev != -1) {
                        best = Math.min(best, v - prev);
                    }
                    prev = v;
                }
            }
            ans[qi] = (prev == -1 || best == 101) ? -1 : best;
        }

        return ans;
    }

    // For quick local testing
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4,5,2,2,7,10};
        int[][] queries = {{2,3},{0,2},{0,5},{3,5}};
        System.out.println(Arrays.toString(s.minDifference(nums, queries)));
        // → [-1, 1, 1, 3]
    }
}
```

---

#### 4.2 Python

```python
from typing import List

class Solution:
    def minDifference(self, nums: List[int], queries: List[List[int]]) -> List[int]:
        n = len(nums)
        # prefix counts: (n+1) x 101, 1-indexed values
        pref = [[0] * 101 for _ in range(n + 1)]
        for i, v in enumerate(nums, 1):
            pref[i][:] = pref[i - 1][:]          # copy previous row
            pref[i][v] += 1

        ans = []
        for l, r in queries:
            prev = -1
            best = 101
            for v in range(1, 101):
                if pref[r + 1][v] - pref[l][v]:
                    if prev != -1:
                        best = min(best, v - prev)
                    prev = v
            ans.append(-1 if prev == -1 or best == 101 else best)
        return ans

# quick demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.minDifference([4,5,2,2,7,10], [[2,3],[0,2],[0,5],[3,5]]))
    # → [-1, 1, 1, 3]
```

---

#### 4.3 C++ (GNU++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> minDifference(vector<int>& nums, vector<vector<int>>& queries) {
        int n = nums.size();
        // prefix counts: (n+1) x 101
        vector<array<int, 101>> pref(n + 1);
        pref[0].fill(0);
        for (int i = 0; i < n; ++i) {
            pref[i + 1] = pref[i];
            pref[i + 1][nums[i]]++;
        }

        vector<int> ans;
        for (auto &q : queries) {
            int l = q[0], r = q[1];
            int prev = -1, best = 101;
            for (int v = 1; v <= 100; ++v) {
                if (pref[r + 1][v] - pref[l][v]) {
                    if (prev != -1) best = min(best, v - prev);
                    prev = v;
                }
            }
            ans.push_back((prev == -1 || best == 101) ? -1 : best);
        }
        return ans;
    }
};

int main() {
    Solution s;
    vector<int> nums = {4,5,2,2,7,10};
    vector<vector<int>> queries = {{2,3},{0,2},{0,5},{3,5}};
    auto res = s.minDifference(nums, queries);
    for (int x : res) cout << x << ' ';
    // Output: -1 1 1 3
}
```

---

> **Tip:**  
> In Java the inner loop `for (int v = 1; v <= 100; ++v) pref[i + 1][v] = pref[i][v];` can be replaced with a `System.arraycopy` for a tiny speed boost.  
> In C++ you can also store the prefix matrix as `vector<vector<int>> pref(n + 1, vector<int>(101));` – the array version just saves one dynamic allocation per row.

---

### 5. 📊 Complexity Summary

| Stage | Operation | Complexity |
|-------|-----------|------------|
| Build prefix matrix | `n × 100` | **O(n · 100)** |
| Query answer | `100` | **O(q · 100)** |
| **Total** | | **O((n + q) · 100)** |
| **Memory** | `(n + 1) × 101` ints | ≈ **40 MB** (Java) / **4 MB** (Python) / **4 MB** (C++) |

All constraints are comfortably satisfied.

---

### 6. 🚧 Common Pitfalls & “What if?”  

| Issue | Fix |
|-------|-----|
| Forget the “all‑identical → –1” rule | After the scan, if `prev == -1` or `best == 101` push `-1`. |
| Using 0‑based value indices in the prefix matrix | Stick to a 1‑based index for the values (1…100). |
| Memory overload when `n` is 10⁵ in Java | `pref` is `int[n+1][101]` → ≈ 40 MB; safe but test on the platform’s memory limit. |
| If the domain grew beyond 100 | The approach still works but the constant factor rises linearly (`value_range`). |
| Edge case: `l == r` | Problem guarantees `l < r`, but you can guard against it: return `-1`. |

---

### 7. 🔗 Extending the Idea  

| Scenario | Adaptation |
|----------|------------|
| **Domain larger (e.g., 10⁴)** | Replace the 101‑length array with a `bitset<MAX>` per prefix. Each query becomes a bitwise OR‑plus‑shift operation – still *O(log n)*. |
| **Need to support updates** | Use a Fenwick/BIT per value or a segment tree of 100‑bit bitsets. |
| **Return the actual pair** | While scanning keep the two values with minimal gap. |

---

### 8. 🗣️ How to Pitch This in an Interview  

1. **Explain the domain constraint first** – “Notice `nums[i] ≤ 100`, we can treat the values as buckets.”  
2. **State the prefix‑sum approach** – “We’ll build a `cnt` matrix so that each query reduces to checking 100 counts.”  
3. **Show a sketch** (hand‑drawn 2‑row example) so the interviewer sees you understand the math.  
4. **Mention complexity** – pre‑processing `O(n·100)`, query `O(100)`.  
5. **Code** – provide the skeleton; talk through the nested loops and the gap‑finding logic.  
6. **Edge‑cases** – highlight `-1` and the single‑value situation.  
7. **Testing** – give a few hand‑crafted tests (e.g., all identical, all distinct, mixed).  
8. **If pressed for memory** – propose using a compressed representation (`short` array) or a bitset trick.

---

### 9. 📚 Take‑away Checklist

- **Domain insight** is often the secret to O(1)/O(log n) solutions.  
- **Prefix counts** transform “count in range” into constant‑time queries.  
- **Keep code clean** – avoid over‑engineering unless the constraints truly demand it.  
- **Document your logic** on paper or whiteboard; interviewers love a clear explanation.  
- **Test edge cases** – identical elements, single distinct value, whole array query, overlapping queries.  

---

### 10. 🚀 Final Thought  

With the prefix‑count matrix you can answer any number of queries in milliseconds, even on the largest allowed inputs.  

> **Ready for the next job interview?**  
> Keep this pattern in your toolbox for any problem where the value domain is small – it will impress interviewers and win you a job!  

---

## 🔑 SEO‑Friendly Keywords (for LinkedIn / Medium posts)

- Minimum Absolute Difference Queries  
- LeetCode 1906  
- Java solution for LeetCode 1906  
- Python solution for LeetCode 1906  
- C++ solution LeetCode 1906  
- Competitive programming segment tree bitset  
- Interview algorithm interview  
- Prefix sum technique  
- Array difference problem  
- Coding interview prep  

Happy coding, and good luck on your next interview! 🚀