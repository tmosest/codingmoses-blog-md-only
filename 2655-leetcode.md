---
title: LeetCode 2655. Find Maximal Uncovered Ranges - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2655 – Find Maximal Uncovered Ranges  
**Medium | LeetCode | Interviews | Algorithm | Java | Python | C++**

---

### TL;DR  
* Sort the given ranges by left endpoint.  
* Sweep a pointer over the sorted list, collecting the gaps that are not covered.  
* Complexity: **O(m log m)** time, **O(m)** auxiliary space (`m = ranges.length`).  
* Works even when `n` is up to `10⁹` because we never build the whole array.

---

## Why this problem is a “must‑know” interview question

1. **Real‑world relevance** – it’s essentially the same as finding free time slots in calendars, unused IP blocks, or unassigned storage ranges.  
2. **Elegant sweep‑line** – you learn a classic algorithmic pattern: sort, scan, and keep a moving pointer.  
3. **Edge‑case mastery** – the input size (`n`) can be huge, so naive O(n) or O(n × m) solutions are a *trap*.

If you can explain this solution cleanly and code it in **Java, Python, and C++**, you’ll stand out in a technical interview.  

---

## 1. Problem Restatement

Given an integer `n` (length of an array `nums`) and a 2‑D array `ranges` where each `ranges[i] = [l, r]` (inclusive), find **all maximal uncovered ranges**.  
A maximal uncovered range is a sub‑interval `[a, b]` such that:

1. No cell in `[a, b]` lies inside any `ranges[i]`.  
2. The interval cannot be extended by one on either side (i.e., `b + 1 ≠ nextLeft`).

Return the uncovered ranges sorted by starting index.

*Example*  

```
n = 10, ranges = [[3,5],[7,8]]
→ [[0,2],[6,6],[9,9]]
```

---

## 2. Naïve (and doomed) Approaches

| Approach | Why it fails (worst‑case) |
|----------|--------------------------|
| Build a boolean array of length `n` and mark covered cells | `n` can be `10⁹` → impossible memory & time. |
| For every range, mark cells; then scan for gaps | Still `O(n × m)` time. |
| Merge ranges first, then subtract from `[0, n-1]` | Merging is `O(m log m)` but if you also build an array you get back to `O(n)`. |

These approaches blow up because they ignore the fact that **only the ranges themselves matter**, not every index inside `0 … n‑1`.

---

## 3. The Elegant Sweep‑Line Solution

1. **Sort** `ranges` by the left endpoint (ascending).  
2. Maintain a pointer `ptr` that denotes the smallest uncovered index so far. Initially `ptr = 0`.  
3. Iterate over each sorted range `[l, r]`:  
   * If `ptr < l`, the gap `[ptr, l-1]` is uncovered → add it to the answer.  
   * Update `ptr = max(ptr, r + 1)` (we can’t go back to an already covered area).  
4. After the loop, if `ptr < n`, the tail `[ptr, n-1]` is uncovered → add it.  
5. Return the collected list.

Because we only sort the ranges (≤ 10⁶) and perform a single linear pass, the algorithm is optimal.

---

## 4. Code

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

> **Tip:** For interviewers, you can show how each language handles the same logic.  
> If you’re applying, you can paste these snippets into your résumé or GitHub.

---

### 4.1 Java

```java
import java.util.*;

public class Solution {
    public int[][] findMaximalUncoveredRanges(int n, int[][] ranges) {
        // Guard clauses
        if (ranges == null || ranges.length == 0) {
            return n == 0 ? new int[0][0] : new int[][]{{0, n - 1}};
        }

        // Sort by left endpoint
        Arrays.sort(ranges, Comparator.comparingInt(a -> a[0]));

        List<int[]> ans = new ArrayList<>();
        int ptr = 0;

        for (int[] r : ranges) {
            int l = r[0], rgt = r[1];

            if (ptr < l) {                  // uncovered gap found
                ans.add(new int[]{ptr, l - 1});
            }
            // Move pointer beyond the current range
            ptr = Math.max(ptr, rgt + 1);
        }

        if (ptr < n) {                      // tail uncovered
            ans.add(new int[]{ptr, n - 1});
        }

        return ans.toArray(new int[ans.size()][]);
    }
}
```

---

### 4.2 Python

```python
from typing import List

class Solution:
    def findMaximalUncoveredRanges(self, n: int, ranges: List[List[int]]) -> List[List[int]]:
        if not ranges:
            return [[0, n - 1]] if n > 0 else []

        # Sort by left endpoint
        ranges.sort(key=lambda x: x[0])

        ans = []
        ptr = 0

        for l, r in ranges:
            if ptr < l:                     # uncovered segment
                ans.append([ptr, l - 1])
            ptr = max(ptr, r + 1)

        if ptr < n:                         # trailing segment
            ans.append([ptr, n - 1])

        return ans
```

---

### 4.3 C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<std::vector<int>> findMaximalUncoveredRanges(int n, std::vector<std::vector<int>>& ranges) {
        if (ranges.empty()) {
            return n > 0 ? std::vector<std::vector<int>>{{0, n - 1}} : std::vector<std::vector<int>>();
        }

        // Sort by left endpoint
        std::sort(ranges.begin(), ranges.end(),
                  [](const std::vector<int>& a, const std::vector<int>& b) {
                      return a[0] < b[0];
                  });

        std::vector<std::vector<int>> ans;
        int ptr = 0;

        for (const auto& seg : ranges) {
            int l = seg[0], r = seg[1];

            if (ptr < l) {                    // uncovered part
                ans.push_back({ptr, l - 1});
            }
            ptr = std::max(ptr, r + 1);       // move pointer
        }

        if (ptr < n) {                        // trailing part
            ans.push_back({ptr, n - 1});
        }

        return ans;
    }
};
```

---

## 5. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sorting `ranges` | **O(m log m)** | **O(1)** (in‑place) |
| Single linear scan | **O(m)** | **O(m)** (list of answers) |
| Total | **O(m log m)** | **O(m)** |

With `m ≤ 10⁶`, this runs comfortably in under a second on modern machines.

---

## 6. Good, Bad, & Ugly

| Category | What to highlight | Pitfalls to avoid |
|----------|-------------------|-------------------|
| **Good** | *Sorting + sweep* – linear time after sorting, no array construction. | None |
| **Bad** | `O(n)` solutions (building the whole array) – will get rejected on large `n`. | Memory blow‑up, cache misses |
| **Ugly** | Over‑merging or deduplication when ranges overlap heavily; you must keep the **minimal** gaps. | Unnecessary extra pass, subtle off‑by‑one bugs when `ptr = r + 1` |

### Common Interview Traps

1. **Off‑by‑one errors** – remember that ranges are inclusive.  
2. **Empty input** – don’t forget the case where all indices are uncovered.  
3. **Large `n`** – avoid creating an array of size `n`.  
4. **Duplicate ranges** – sorting handles them naturally; you never need to explicitly merge unless you want to compress memory further.

---

## 7. Practical Applications

| Domain | Why the algorithm matters |
|--------|--------------------------|
| Calendar scheduling | Find free slots between events. |
| Network IP allocation | Compute unused IP blocks from allocated ranges. |
| File system fragmentation | Detect large free blocks for quick allocation. |
| Game level design | Identify areas not yet populated by enemies or items. |

Understanding this problem equips you to tackle any “interval‑gap” question that pops up in real‑world scenarios.

---

## 8. SEO‑Friendly Takeaway

If you’re targeting recruiters or technical interview boards, this keyword‑rich write‑up will help you get discovered:

* **Find Maximal Uncovered Ranges** – LeetCode 2655  
* **Interval merging sweep line algorithm** – interview strategy  
* **Java, Python, C++ solutions** – code snippets  
* **Complexity analysis** – O(m log m) time, O(m) space  

Search engines love structured content. By using bold headings, code fences, and a clean, conversational tone, your blog will rank high for developers preparing for LeetCode 2655 or any interview that demands interval‑based reasoning.

---

## 9. Final Words

- **Remember**: `n` is just a *boundary*; only the ranges matter.  
- **Show** the sweep‑line pattern; interviewers love clean, generalizable solutions.  
- **Practice** coding it in all languages you’re comfortable with; highlight differences (e.g., `Arrays.sort` vs. `ranges.sort`).  

Good luck cracking LeetCode 2655 – it’s the same skill set that turns interview questions into real‑world problem‑solving. Happy coding!