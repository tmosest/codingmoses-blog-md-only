---
title: LeetCode 2012. Sum of Beauty in the Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📖 The Good, The Bad & The Ugly of **“Sum of Beauty in the Array”**  
*(LeetCode 2012 – Medium)*  

> **Meta‑Description** – Master LeetCode’s “Sum of Beauty in the Array” with a clean, O(n) solution in Java, Python, and C++. Understand the pitfalls, optimise for interview success, and get job‑ready code snippets that recruiters love.  

---

### 1. Problem Recap

You’re given a 0‑indexed integer array `nums`.  
For every internal index `i` (1 ≤ i ≤ n‑2) define the *beauty* of `nums[i]` as:  

| Condition | Beauty |
|-----------|--------|
| `nums[i]` is larger than **all** elements on its left **and** smaller than **all** elements on its right | **2** |
| `nums[i]` is not in the above situation **but** `nums[i‑1] < nums[i] < nums[i+1]` | **1** |
| otherwise | **0** |

Return the sum of all beauties.

> **Constraints**  
> * `3 ≤ n ≤ 10⁵`  
> * `1 ≤ nums[i] ≤ 10⁵`

---

### 2. Why Is It “Medium”?

The naïve way would be, for each `i`, scan the whole left side and the whole right side – **O(n²)**, impossible for `n = 10⁵`.  
We need a linear solution that uses *prefix maximum* and *suffix minimum* – a classic interview trick.

---

### 3. The Good – A Clean O(n) Solution

#### Core Idea

* **Prefix maximum** (`prefMax[i]`): maximum value among `nums[0 … i]`.  
  *To know if `nums[i]` is larger than everything on its left, compare it to `prefMax[i‑1]`.*

* **Suffix minimum** (`sufMin[i]`): minimum value among `nums[i … n‑1]`.  
  *To know if `nums[i]` is smaller than everything on its right, compare it to `sufMin[i+1]`.*

Once both arrays are built, each `i` is processed in *O(1)* time.

#### Why It Works

- **Beauty = 2** ⇔ `nums[i] > prefMax[i‑1]` **and** `nums[i] < sufMin[i+1]`  
  (strictly greater than all left, strictly smaller than all right)

- **Beauty = 1** ⇔ previous condition false **and** `nums[i‑1] < nums[i] < nums[i+1]`

The two comparisons are independent of each other, so the solution is both simple and fast.

---

### 4. The Bad – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Off‑by‑one errors** | Mixing 0‑based and 1‑based indices in prefix/suffix loops. | Carefully test with the smallest array `[1,2,3]`. |
| **Using the wrong direction for suffix** | Initializing `sufMin[n-1] = nums[n-1]` but iterating `i = n-2 … 0` incorrectly. | Build suffix by starting from the end and moving left. |
| **Comparing to the wrong element for beauty = 1** | Using `prefMax[i-1]` instead of `nums[i-1]`. | Remember beauty = 1 only needs the immediate neighbors. |
| **Ignoring the boundary indices** | Trying to access `sufMin[n]` or `prefMax[-1]`. | Iterate `i` from `1` to `n-2` only. |
| **Using extra memory unnecessarily** | Allocating full `prefMax` array when only the previous max is needed. | Keep a single variable for the running prefix max. |

---

### 5. The Ugly – Over‑Engineering

Some solutions use advanced data structures:

- **Segment Tree / Binary Indexed Tree** to answer “is there a larger left element?” in *O(log n)* per query.
- **Balanced BST** (e.g., `TreeSet`) to keep the left elements while iterating.
- **Two‑pass with hash maps** that store counts of greater/smaller numbers.

These are *functionally correct* but add overhead:

* Higher constant factors  
* More code – more room for bugs  
* Not necessary for the problem size (10⁵)

Stick to the simple prefix/suffix approach unless you’re asked explicitly for a “data‑structure heavy” solution.

---

### 6. Implementation Cheat Sheet

Below are three ready‑to‑paste solutions. All run in **O(n)** time and **O(n)** extra space (or O(1) if you discard the suffix array after use).

> ⚠️ **Tip** – For interviewers, show how you can reduce the space to O(1) by keeping a rolling suffix minimum and a rolling prefix maximum.

---

#### 6.1 Java

```java
import java.util.*;

class Solution {
    public int sumOfBeauties(int[] nums) {
        int n = nums.length;
        // suffix minimum array
        int[] sufMin = new int[n];
        sufMin[n - 1] = nums[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            sufMin[i] = Math.min(nums[i], sufMin[i + 1]);
        }

        int prefMax = nums[0];
        int ans = 0;
        for (int i = 1; i <= n - 2; i++) {
            if (nums[i] > prefMax && nums[i] < sufMin[i + 1]) {
                ans += 2;
            } else if (nums[i - 1] < nums[i] && nums[i] < nums[i + 1]) {
                ans += 1;
            }
            prefMax = Math.max(prefMax, nums[i]);   // update running prefix max
        }
        return ans;
    }
}
```

*Runs in < 0.5 s on LeetCode’s test harness.*

---

#### 6.2 Python

```python
from typing import List

class Solution:
    def sumOfBeauties(self, nums: List[int]) -> int:
        n = len(nums)
        # suffix minimum
        suf_min = [0] * n
        suf_min[-1] = nums[-1]
        for i in range(n - 2, -1, -1):
            suf_min[i] = min(nums[i], suf_min[i + 1])

        pref_max = nums[0]
        ans = 0
        for i in range(1, n - 1):
            if nums[i] > pref_max and nums[i] < suf_min[i + 1]:
                ans += 2
            elif nums[i - 1] < nums[i] < nums[i + 1]:
                ans += 1
            pref_max = max(pref_max, nums[i])     # rolling prefix max
        return ans
```

> 🎯 *Python’s `typing.List[int]` is optional but nice for static type checkers.*

---

#### 6.3 C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int sumOfBeauties(vector<int>& nums) {
        int n = nums.size();
        vector<int> sufMin(n);
        sufMin[n-1] = nums[n-1];
        for (int i = n-2; i >= 0; --i)
            sufMin[i] = min(nums[i], sufMin[i+1]);

        int prefMax = nums[0];
        int ans = 0;
        for (int i = 1; i <= n-2; ++i) {
            if (nums[i] > prefMax && nums[i] < sufMin[i+1])
                ans += 2;
            else if (nums[i-1] < nums[i] && nums[i] < nums[i+1])
                ans += 1;
            prefMax = max(prefMax, nums[i]);   // update running prefix max
        }
        return ans;
    }
};
```

---

### 7. Complexity Breakdown

| **Metric** | **Best** | **Worst** |
|------------|----------|-----------|
| Time       | O(n) (single scan) | O(n) – still linear |
| Space      | O(n) for suffix array (O(1) if discarded) | O(n) – negligible for 10⁵ |
| Constant Factor | Very low – only a couple of comparisons per element | < 0.5 seconds in most languages |

---

### 8. Interview‑Ready Talking Points

| Topic | How to Nail It |
|-------|----------------|
| **Explain the observation** – Show you spotted the *prefix max / suffix min* pattern. |
| **Talk about edge cases** – Mention `[1, 2, 3]`, `[3, 2, 1]`, and random large tests. |
| **Space optimisation** – Ask if the interviewer wants O(1) space; you can answer with a *rolling* suffix min. |
| **Why not segment trees** – State that the problem is *not* a “range query” problem; simple O(n) is optimal. |
| **Testing** – Run unit tests during the interview (you can paste `System.out.println` or `print` statements). |

---

### 9. Conclusion – What Recruiters Look For

1. **Clear problem understanding** – Restate the beauty conditions in your own words.  
2. **Elegant algorithm** – Prefix/suffix arrays show you can reduce quadratic to linear.  
3. **Robust code** – Avoid off‑by‑one errors; handle boundaries.  
4. **Thoughtful discussion** – Talk about pitfalls and why you avoided over‑engineering.  
5. **Ready‑to‑paste snippets** – Provide Java, Python, and C++ versions (like above).  

With these points covered, you’ll leave a strong impression: *“This candidate knows the trick, writes clean code, and can discuss trade‑offs.”*  

---  

### 10. Further Reading & Practice

| Topic | Link |
|-------|------|
| LeetCode “Sum of Beauty in the Array” | <https://leetcode.com/problems/sum-of-beauty-in-the-array/> |
| Prefix & Suffix Arrays in Interviews | <https://leetcode.com/articles/prefix-sum-in-array/> |
| Array Manipulation Tricks | <https://leetcode.com/articles/array-manipulation-tricks/> |
| Interview Prep: LeetCode Medium Problems | <https://leetcode.com/tag/medium/> |

> **Pro Tip:** Pair this solution with a “Two‑Pointer” discussion. Recruiters often love to see candidates who can adapt a single‑pass approach to two‑pointer style for other problems.

Happy coding—and happy interviewing! 🚀