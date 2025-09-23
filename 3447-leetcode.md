---
title: LeetCode 3447. Assign Elements to Groups with Constraints - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem 3447 – Assign Elements to Groups with Constraints  
**Difficulty**: Medium | **Tag**: Hash Table, Divisors, Greedy

> **LeetCode**: <https://leetcode.com/problems/assign-elements-to-groups-with-constraints/>

You are given  
* `groups[i]` – the size of the *i*‑th group.  
* `elements[j]` – the *j*‑th element.

For each group we must pick an element that satisfies:

| Rule | Description |
|------|-------------|
| **Divisibility** | `groups[i]` must be divisible by `elements[j]`. |
| **Smallest index** | If several elements work, choose the one with the smallest index `j`. |
| **No match** | If no element satisfies the rule, put `-1`. |

An element may be reused by many groups.  
Return an array `assigned` where `assigned[i]` is the index of the element chosen for group `i` (or `-1`).

> Example  
> `groups = [8,4,3,2,4]`, `elements = [4,2]`  
> `assigned = [0,0,-1,1,0]`  

--------------------------------------------------------------------

## 🚀 The Idea

The brute‑force way would be to try every element for every group – `O(|groups| × |elements|)`, which is far too slow for up to `10⁵` elements.

### Key observation
*We only need to know, for each divisor of a group size, whether that divisor appears in `elements` and, if so, its **smallest index**.*

So the plan:

1. **Hash map** – store the *smallest* index for every unique value in `elements`.  
   `map[value] → index`

2. For each group size `g`  
   * enumerate all divisors `d` of `g` in `O(√g)` time.  
   * if `d` is in the hash map, keep the smallest index seen so far.  
   * answer is that smallest index, otherwise `-1`.

The total work is `∑√groups[i]` which is well within limits (`≈ 3×10⁷` ops worst case).

--------------------------------------------------------------------

## 🧩 Code

Below you’ll find clean, production‑ready implementations in **Java, Python, and C++**.  
Each one follows the same algorithm described above.

### Java

```java
import java.util.*;

public class Solution {
    public int[] assignElements(int[] groups, int[] elements) {
        // 1. map element value → smallest index
        Map<Integer, Integer> indexMap = new HashMap<>();
        for (int i = 0; i < elements.length; i++) {
            int val = elements[i];
            indexMap.putIfAbsent(val, i);      // keep first (smallest) index
        }

        int[] ans = new int[groups.length];

        // 2. process every group
        for (int i = 0; i < groups.length; i++) {
            int g = groups[i];
            int bestIdx = Integer.MAX_VALUE;

            // enumerate divisors up to sqrt(g)
            for (int d = 1; d * d <= g; d++) {
                if (g % d != 0) continue;

                // smaller divisor
                Integer idx = indexMap.get(d);
                if (idx != null) bestIdx = Math.min(bestIdx, idx);

                // paired larger divisor
                int other = g / d;
                if (other != d) {
                    idx = indexMap.get(other);
                    if (idx != null) bestIdx = Math.min(bestIdx, idx);
                }
            }

            ans[i] = (bestIdx == Integer.MAX_VALUE) ? -1 : bestIdx;
        }
        return ans;
    }
}
```

**Complexity**  
*Time*: `O( |groups| × √max(groups) )`  
*Space*: `O( |elements| )`

--------------------------------------------------------------------

### Python

```python
from typing import List

class Solution:
    def assignElements(self, groups: List[int], elements: List[int]) -> List[int]:
        # 1. element value → smallest index
        idx_map = {}
        for i, val in enumerate(elements):
            if val not in idx_map:          # keep first (smallest) index
                idx_map[val] = i

        ans = []

        # 2. process each group
        for g in groups:
            best = float('inf')
            d = 1
            while d * d <= g:
                if g % d == 0:
                    if d in idx_map:
                        best = min(best, idx_map[d])
                    other = g // d
                    if other != d and other in idx_map:
                        best = min(best, idx_map[other])
                d += 1

            ans.append(best if best != float('inf') else -1)

        return ans
```

**Complexity** – identical to the Java version.

--------------------------------------------------------------------

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> assignElements(vector<int>& groups, vector<int>& elements) {
        unordered_map<int, int> idx;          // value -> smallest index
        for (int i = 0; i < (int)elements.size(); ++i)
            idx.emplace(elements[i], i);      // keep first occurrence

        vector<int> ans(groups.size(), -1);

        for (int i = 0; i < (int)groups.size(); ++i) {
            int g = groups[i];
            int best = INT_MAX;

            for (int d = 1; d * d <= g; ++d) {
                if (g % d) continue;

                auto it = idx.find(d);
                if (it != idx.end()) best = min(best, it->second);

                int other = g / d;
                if (other != d) {
                    it = idx.find(other);
                    if (it != idx.end()) best = min(best, it->second);
                }
            }
            ans[i] = (best == INT_MAX) ? -1 : best;
        }
        return ans;
    }
};
```

--------------------------------------------------------------------

## 📈 Test

```bash
# Python
python - <<'PY'
from typing import List
class Solution:
    def assignElements(self, groups: List[int], elements: List[int]) -> List[int]:
        # same as the implementation above
        idx = {}
        for i, val in enumerate(elements):
            if val not in idx:
                idx[val] = i
        res = []
        for g in groups:
            best = float('inf')
            d = 1
            while d*d <= g:
                if g % d == 0:
                    if d in idx: best = min(best, idx[d])
                    other = g//d
                    if other != d and other in idx: best = min(best, idx[other])
                d+=1
            res.append(best if best != float('inf') else -1)
        return res

print(Solution().assignElements([8,4,3,2,4],[4,2]))
PY
```

Output:

```
[0, 0, -1, 1, 0]
```

The same test will pass for the Java and C++ versions.

--------------------------------------------------------------------

## 🧪 Edge Cases

| Edge | Why it matters | How we handle it |
|------|----------------|------------------|
| Duplicate values in `elements` | We need the **smallest** index | `putIfAbsent` / `if (not in map) map[value] = idx` |
| Group size = 1 | Only divisor is 1 | Divisor loop still works (`1*1 <= 1`) |
| All elements larger than group size | No divisor will match | `bestIdx` stays `∞` → `-1` |

--------------------------------------------------------------------

## 🎖️ Why This Matters for Interviews

* **Greedy + Math** – demonstrates you can blend number theory with data‑structures.  
* **Complexity analysis** – LeetCode judges time & memory; showing a clear `O(√n)` bound is a plus.  
* **Code readability** – Clean loops and short helper methods make your solution easy to read—a key factor in many coding interviews.

--------------------------------------------------------------------

## 📚 Further Optimisations

If you want an `O(|groups| + maxGroup)` solution you can build a *divisor map*:

```text
for each value v in elements:
    for multiple m of v up to maxGroup:
        record the smallest index of v that divides m
```

This is essentially a sieve.  
The complexity becomes `∑(maxGroup / v)` which can be cheaper when the elements are large but is heavier when elements are small.  
The factor‑enumeration solution presented above is usually the simplest and fastest for the given constraints.

--------------------------------------------------------------------

## ✍️ Blog Article – “The Good, the Bad, and the Ugly of LeetCode 3447”

> **SEO Keywords**:  
> *Leetcode 3447*, *Assign Elements to Groups*, *Java solution*, *Python solution*, *C++ solution*, *Coding interview*, *SDE interview*, *Tech interview tips*, *Divisors algorithm*, *Hash table interview problem*, *FAANG interview prep*

---

### The Good: Why 3447 is a Masterclass in Interviewing

- **Pure logic + data‑structures** – No magic tricks; just a clean greedy approach that shows you understand divisors and hash maps.
- **Clear constraints** – Values ≤ `10⁵`, making a √n divisor enumeration practical.
- **Reusable elements** – Tests your ability to think about the problem’s constraints (one index per group but many groups per element).
- **Real‑world applicability** – The same idea (reverse indexing + divisor enumeration) shows up in problems like “Smallest Missing Value”, “Smallest Divisor” or “Minimum Element in Subarrays”.

### The Bad: The TLE Pitfall

Many candidates fall into the classic O(n²) trap:

```text
for g in groups:
    for e in elements:
        if g % e == 0:
            answer = first e
            break
```

This naïve approach gives you a *Time‑Limit Exceeded* verdict even with a modest `10⁴` input size.  
Because LeetCode’s evaluation environment is strict, a single TLE makes the whole submission fail.  
The trick is to avoid iterating over all elements for every group.

### The Ugly: Over‑Optimised, Hard‑to‑Read Code

A few people try to get fancy with bit‑set tricks or a custom sieve.  
While technically fast, those solutions:

- **Lose readability** – Hard for interviewers to audit quickly.
- **Use more memory** – Building a full sieve up to `max(groups)` can use up to `10⁵` integers for each element.
- **Risk subtle bugs** – Mixing loops, modulo operations, and map updates in one dense block often leads to off‑by‑one errors.

**Bottom line**: Striking a balance between speed and clarity is the hallmark of a great interview answer.

--------------------------------------------------------------------

## 🏁 Summary

| Language | Complexity | Take‑away |
|----------|------------|-----------|
| **Java** | `O(|groups| √maxGroup)` | Use `HashMap` + divisor loop. |
| **Python** | `O(|groups| √maxGroup)` | List comprehension + `dict`. |
| **C++** | `O(|groups| √maxGroup)` | `unordered_map` + fast loops. |

All three snippets above are ready to paste into LeetCode’s editor.  
Run them against the sample tests and you’ll see identical results.

--------------------------------------------------------------------

### TL;DR

1. Store the *first* (smallest) index of every element value in a hash map.  
2. For each group size enumerate all divisors in `O(√g)` and pick the smallest index from the map.  
3. Complexity: `O(∑√groups[i])`, Space: `O(|elements|)`.

> 🚀 **Pro Tip** – For extra practice, implement the *sieve* variant that pre‑computes the smallest index for every multiple. It runs in `O(|elements| × maxGroup / value)` and answers each group in `O(1)` time, but uses more memory.

Happy coding, and good luck slaying that **LeetCode 3447** in your next FAANG interview!