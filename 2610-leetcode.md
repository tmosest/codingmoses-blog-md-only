---
title: LeetCode 2610. Convert an Array Into a 2D Array With Conditions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 2610. Convert an Array Into a 2D Array With Conditions  
**Java / Python / C++ – Full Solutions + Blog‑Style Interview Guide**  

> **TL;DR** – The minimum number of rows equals the maximum frequency of any value.  
> Build a hash‑map of frequencies, determine `maxFreq`, then place each element into
> successive rows until its count runs out.

---

## 🚀 Why You Should Know This Problem

- **LeetCode Rank**: 2610 – *Medium* – Often appears in *data‑structure* & *hash‑map* interview rounds.  
- **Real‑World Parallel**: Distributing jobs among workers, seating people at tables, or
  partitioning data for parallel processing.  
- **Skill Show‑case**: O(n) time, O(n) space, clear use of a frequency map, and
  constructive building of a matrix.  

If you nail this one, you’ll get a *thumbs‑up* from recruiters looking for clean algorithmic thinking.

---

## 📑 Problem Statement (LeetCode 2610)

You are given an integer array `nums`.  
Return a 2‑D array that satisfies:

1. Contains **all** elements of `nums`.  
2. Every row contains **distinct** integers.  
3. The number of rows is **minimal**.  
4. Any valid answer may be returned.

*Constraints*

- `1 ≤ nums.length ≤ 200`  
- `1 ≤ nums[i] ≤ nums.length`

---

## 🧠 The Insight – “Maximum Frequency = Minimum Rows”

- If a value `x` appears `k` times, each of those occurrences must be in a *different* row.
- Therefore **no row count can be less than the highest occurrence count**.  
- Conversely, we can always build exactly `maxFreq` rows:  
  *Iteratively put every remaining element into the current row and reduce its count*.

> **Key Idea**  
> ```text
> rows_needed = max frequency of any element
> ```

---

## 🔍 Approaches

| # | Strategy | Time | Space | When to Use |
|---|----------|------|-------|-------------|
| 1 | **Frequency Map + Row Construction** (classic) | O(n) | O(n) | Most interviewers expect this clean solution. |
| 2 | **Direct Frequency Vector** (index‑by‑value) | O(n) | O(n) | Works when `nums[i]` ≤ `n`; keeps code compact. |
| 3 | **No HashMap (O(1) extra)** | O(n²) | O(1) | Rare; useful when memory is *extremely* constrained. |

We’ll implement the *canonical* Approach 1 in all three languages.

---

## 🧩 Code Solutions

> **Tip**: In Java, LeetCode uses the class `Solution`;  
> In Python, the function signature is `def findMatrix(self, nums: List[int]) -> List[List[int]]`;  
> In C++, it is `vector<vector<int>> findMatrix(vector<int>& nums);`.

### 1. Java – `Solution.java`

```java
import java.util.*;

class Solution {
    public List<List<Integer>> findMatrix(int[] nums) {
        // 1️⃣ Count frequencies
        Map<Integer, Integer> freq = new HashMap<>();
        int maxFreq = 0;
        for (int x : nums) {
            int f = freq.getOrDefault(x, 0) + 1;
            freq.put(x, f);
            if (f > maxFreq) maxFreq = f;
        }

        // 2️⃣ Build `maxFreq` rows
        List<List<Integer>> result = new ArrayList<>(maxFreq);
        for (int row = 0; row < maxFreq; ++row) {
            List<Integer> cur = new ArrayList<>();
            for (Map.Entry<Integer,Integer> e : freq.entrySet()) {
                int key = e.getKey(), val = e.getValue();
                if (val > 0) {
                    cur.add(key);
                    freq.put(key, val - 1);
                }
            }
            result.add(cur);
        }
        return result;
    }
}
```

---

### 2. Python – `solution.py`

```python
from typing import List
from collections import Counter

class Solution:
    def findMatrix(self, nums: List[int]) -> List[List[int]]:
        # 1️⃣ Frequency map
        freq = Counter(nums)
        max_freq = max(freq.values())

        # 2️⃣ Build rows
        result: List[List[int]] = []
        for _ in range(max_freq):
            row: List[int] = []
            for num in list(freq):          # iterate over a copy to avoid modification issues
                if freq[num] > 0:
                    row.append(num)
                    freq[num] -= 1
            result.append(row)
        return result
```

---

### 3. C++ – `solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> findMatrix(vector<int>& nums) {
        // 1️⃣ Frequency map
        unordered_map<int, int> freq;
        int maxFreq = 0;
        for (int x : nums) {
            int f = ++freq[x];
            maxFreq = max(maxFreq, f);
        }

        // 2️⃣ Build rows
        vector<vector<int>> result;
        for (int r = 0; r < maxFreq; ++r) {
            vector<int> cur;
            for (auto &p : freq) {
                if (p.second > 0) {
                    cur.push_back(p.first);
                    --p.second;
                }
            }
            result.push_back(move(cur));
        }
        return result;
    }
};
```

> **Note**: In C++ we *do not* need to clone the map each iteration; we just reduce the
> counts as we go. The loop will stop when all counts reach zero.

---

## 🧾 Complexity Analysis

| Operation | Java | Python | C++ |
|-----------|------|--------|-----|
| **Time** | `O(n)` | `O(n)` | `O(n)` |
| **Space** | `O(n)` (hash map + output) | `O(n)` | `O(n)` |

> *Why linear?*  
> We traverse the input once to count. Then we perform at most `maxFreq` passes, each
> touching each distinct element once – `maxFreq * distinct ≤ n`.

---

## 🛠️ “Good, the Bad, and the Ugly” in Interviews

### Good

| ✔️ | ✅ |
|----|----|
| Clean frequency‑map logic | LeetCode‑style solution |
| Handles any value range (`1..n`) | O(n) time |
| Works for duplicates and distinct elements alike |  |

### Bad

| ❌ | ❌ |
|---|---|
| *Using `ArrayList` inside loops* – unnecessary memory allocations. Use `ArrayList<>(maxFreq)` instead. |
| *Iterating over `freq.keySet()` in Python* – may modify while iterating. Use `list(freq)` or a copy. |
| *Ignoring `maxFreq`* – leads to more rows than necessary and a higher complexity. |

### Ugly

- **No extra data structures** (O(1) space) – requires scanning the array repeatedly,
  which yields `O(n²)` time. Rarely used in interviews due to memory‑time trade‑off.
- **Sorting‑by‑frequency approach** – first sort the array, then distribute in a
  “snake‑like” fashion. Works but does *not* guarantee minimal rows in the worst case.

---

## 🤓 Interview Tips

| Topic | How to Nail It |
|-------|----------------|
| **Explain the Core Insight** | Start by saying “The minimum rows equal the maximum occurrence.” |
| **Walkthrough with Example** | Show a quick demo (`[1,1,2,2,2,3]` → rows = 3). |
| **Edge Cases** | *All elements unique* → single row. *All same* → `n` rows. |
| **Complexity Proof** | Explain why `maxFreq` rows are sufficient. |
| **Follow‑up Questions** | *What if we want each row to have the same length?* – Discuss greedy vs. balancing. |
| **STAR Format** | *Situation*: Distribute `nums`. *Task*: Build minimal rows. *Action*: Frequency map + iterative build. *Result*: O(n) solution. |

> **Pro‑Tip** – After the solution, ask the interviewer if they’d like an *in‑place*
> or *vector‑by‑value* variant. This demonstrates flexibility.

---

## 📦 Quick Self‑Test

```text
Input:  [1,1,2,2,2,3]
Max freq = 3

Row 1: [1,2,3]
Row 2: [1,2]
Row 3: [2]
```

```text
Input:  [1,2,3,4]
Max freq = 1

Row 1: [1,2,3,4]
```

```text
Input:  [1,1,1,1]
Max freq = 4

Rows:
1: [1]
2: [1]
3: [1]
4: [1]
```

Feel free to run the snippet in your IDE or LeetCode Playground!

---

## 🎓 How to Prepare for the Next Interview

1. **Practice Variants**  
   - `Maximum Frequency` (LeetCode 2684)  
   - `Distribute & Reduce` (LeetCode 2613)  
2. **Time‑Space Trade‑Off**  
   - Show the “no‑hashmap” O(n²) solution when asked to use minimal extra space.  
3. **Discuss Trade‑Offs**  
   - When is a frequency vector better than a hash‑map?  
   - When do you prefer `unordered_map` vs. `Map` vs. `Counter`?

4. **Show Problem‑Solving Process**  
   - Start from constraints, derive `maxFreq`, then construct.  
   - This “clear thought” pattern impresses interviewers.

---

## 📈 SEO‑Ready Blog Snippet

> **Title**: *Convert an Array Into a 2D Array With Conditions – Java, Python, C++ Solutions for LeetCode 2610*  
> **Meta Description**: Learn how to solve LeetCode 2610 in O(n) time with a frequency map. Full Java, Python, and C++ code plus an interview guide.  
> **Keywords**: `LeetCode 2610`, `Convert array to 2D array`, `Java interview problem`, `Python algorithm`, `C++ LeetCode solution`, `hash map matrix`, `data structure interview`, `minimum rows`, `maximum frequency`.

---

## 🎓 Final Takeaway

- **Good**: Clean O(n) solution using a frequency map; demonstrates mastery of hash‑maps and matrix construction.  
- **Bad**: Over‑complicating with sorting or unnecessary data structures; wastes O(n log n) time.  
- **Ugly**: Trying to avoid any extra space; leads to O(n²) time and is rarely requested.

Master this pattern, practice a few edge cases, and you’ll be ready to impress recruiters with a concise, efficient solution. Happy coding! 🚀