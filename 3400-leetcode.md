---
title: LeetCode 3400. Maximum Number of Matching Indices After Right Shifts - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 LeetCode 3400 – Maximum Number of Matching Indices After Right Shifts  

> **Problem**  
> Given two integer arrays `nums1` and `nums2` of equal length, you can perform any number of *right shifts* on `nums1`.  
> A right shift moves the element at index `i` to `(i + 1) % n`.  
> An index `i` is **matching** if `nums1[i] == nums2[i]`.  
> Return the **maximum** number of matching indices you can achieve after an arbitrary number of right shifts.

> **Constraints**  
> * `1 ≤ nums1.length = nums2.length ≤ 3000`  
> * `1 ≤ nums1[i], nums2[i] ≤ 10^9`

> **Link** – <https://leetcode.com/problems/maximum-number-of-matching-indices-after-right-shifts/>

---

## 📌 Why This Problem Is a Job‑Interview Gold‑Mine

* **Array manipulation + hashing** – classic topics in data‑structures interviews.  
* **Modular arithmetic** – shows you can think cleanly about circular shifts.  
* **Space–time trade‑offs** – you must pick the right data structure for speed.  
* **Language‑agnostic** – solutions exist in Java, Python, C++, etc.  

Search engines love this topic when combined with the keywords “LeetCode 3400”, “right shift”, “matching indices”, and “job interview”.  This article is fully SEO‑optimized for recruiters looking for algorithmic talent.

---

## 🔍 Brute‑Force Insight

A naive approach is to simulate **every** possible shift (0…n‑1) and count matches.

```text
maxMatches = 0
for shift in 0..n-1:
    matches = 0
    for i in 0..n-1:
        if nums1[(i - shift + n) % n] == nums2[i]:
            matches += 1
    maxMatches = max(maxMatches, matches)
```

*Time*: `O(n²)`  
*Space*: `O(1)`  

With `n ≤ 3000`, this passes LeetCode, but you can do *much better* by noticing that a match only depends on **how many indices in nums1 align with the same value in nums2**.

---

## 🛠️ Optimal Strategy – Count Shift Frequencies

1. **Map each value in `nums1` to the list of indices where it occurs.**  
2. For each index `i` in `nums2`, look up indices of `nums2[i]` in `nums1`.  
3. For each such index `idx`, compute the shift that would bring `idx` to `i`:  

   ```
   shift = (idx - i + n) % n
   ```

4. Increment a counter `shiftCount[shift]`.  
5. The answer is the maximum value in `shiftCount`.

Why does it work?  
If `nums1[idx] == nums2[i]` and we right‑shift `nums1` by `shift`, then `idx` moves to `(idx + shift) % n`.  
Setting this equal to `i` gives exactly the shift formula above.  
All pairs that match *for the same shift* contribute to the same bucket in `shiftCount`.

**Complexities**

| Metric | Brute‑Force | Optimal |
|--------|-------------|---------|
| Time   | `O(n²)`     | `O(n · f)` where `f` is average frequency of values (`≤ n`) → worst `O(n²)` but practically far faster. |
| Space  | `O(1)`      | `O(n)` for the shift counter + mapping of values to indices. |

---

## 📦 Code in 3 Languages

> All solutions are **O(n²) worst‑case** but run comfortably under the constraints.  
> They handle duplicates correctly and avoid negative modulo pitfalls.

### Java

```java
import java.util.*;

class Solution {
    public int maximumMatchingIndices(int[] nums1, int[] nums2) {
        int n = nums1.length;
        // Map value -> list of indices where it appears in nums1
        Map<Integer, List<Integer>> positions = new HashMap<>();
        for (int i = 0; i < n; i++) {
            positions.computeIfAbsent(nums1[i], k -> new ArrayList<>()).add(i);
        }

        int[] shiftCount = new int[n];
        int maxMatches = 0;

        for (int i = 0; i < n; i++) {
            List<Integer> idxList = positions.get(nums2[i]);
            if (idxList == null) continue;        // value not present in nums1
            for (int idx : idxList) {
                int shift = (idx - i + n) % n;   // right‑shift needed to align idx to i
                shiftCount[shift]++;
                if (shiftCount[shift] > maxMatches) {
                    maxMatches = shiftCount[shift];
                }
            }
        }
        return maxMatches;
    }
}
```

### Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def maximumMatchingIndices(self, nums1: List[int], nums2: List[int]) -> int:
        n = len(nums1)
        pos = defaultdict(list)          # value -> list of indices in nums1
        for i, val in enumerate(nums1):
            pos[val].append(i)

        shift_count = [0] * n
        max_matches = 0

        for i, val in enumerate(nums2):
            for idx in pos.get(val, []):
                shift = (idx - i + n) % n
                shift_count[shift] += 1
                if shift_count[shift] > max_matches:
                    max_matches = shift_count[shift]

        return max_matches
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumMatchingIndices(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        unordered_map<int, vector<int>> pos;          // value -> indices in nums1
        for (int i = 0; i < n; ++i) pos[nums1[i]].push_back(i);

        vector<int> shiftCount(n, 0);
        int maxMatches = 0;

        for (int i = 0; i < n; ++i) {
            auto it = pos.find(nums2[i]);
            if (it == pos.end()) continue;
            for (int idx : it->second) {
                int shift = (idx - i + n) % n;
                shiftCount[shift]++;
                maxMatches = max(maxMatches, shiftCount[shift]);
            }
        }
        return maxMatches;
    }
};
```

---

## 📊 Edge Cases & Gotchas

| Case | Why it matters | Fix |
|------|----------------|-----|
| **Duplicate values** | Multiple indices map to same value → many shifts must be counted separately | Use `List<Integer>` (Java), `list` (Python), `vector<int>` (C++) |
| **No matches** | Some values in `nums2` never appear in `nums1` | `get` / `find` returns empty list/iterates to `continue` |
| **Negative modulo** | `(idx - i)` can be negative | Add `n` before `% n` to stay non‑negative |
| **Large `n`** | Even O(n²) is 9 million ops → still fine | Keep code simple; no need for fancy optimizations beyond this logic |

---

## 💡 Interview‑Ready Tips

1. **Explain the observation**: “A match is determined by the relative positions of equal values.”  
2. **Show the shift formula** clearly; recruiters love to see modular math.  
3. **Mention complexity** upfront – O(n²) worst case but practically fast.  
4. **Discuss memory usage** – one array of size `n` + hash map.  
5. **Optional:** Provide a brute‑force version first to show baseline, then improve.  

---

## 🎯 Final Takeaway

- **Problem solved** by mapping each value to its indices, then counting the shift that aligns each pair.  
- **Optimal solution** runs comfortably within LeetCode limits and scales well.  
- **Three clean implementations** (Java, Python, C++) ready for interview prep or coding challenge.  

Deploy this code, run the examples, and you’ll be ready to impress recruiters on “LeetCode 3400” and similar array‑shifting problems. Happy coding! 🚀

--- 

**Keywords**: LeetCode 3400, maximum matching indices, right shift, array matching, interview algorithm, Java solution, Python solution, C++ solution, algorithm interview, job interview, data structure interview, modular arithmetic.