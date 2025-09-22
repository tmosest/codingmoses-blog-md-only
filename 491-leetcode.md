---
title: LeetCode 491. Non-decreasing Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ LeetCode 491 – “Non‑decreasing Subsequences”  
**Language‑agnostic code (Java / Python / C++) + SEO‑friendly interview blog**

---

### Problem Recap  
Given an integer array `nums` (1 ≤ len ≤ 15, -100 ≤ nums[i] ≤ 100), return *all* **different** non‑decreasing subsequences of length **≥ 2**. The order of the output does not matter.

> **Example**  
> Input: `[4,6,7,7]`  
> Output: `[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]`

---

## 🎯 Core Idea  
A classic *backtracking* problem with two twists:

1. **Non‑decreasing condition** → when extending a sequence we only pick numbers ≥ the last element.  
2. **Avoid duplicates** → the array may contain repeated values, so the same subsequence can be formed in many ways. We need to output each unique subsequence only once.

The standard trick is: **at each recursion depth keep a local `Set` of numbers already taken**. This guarantees we never start a new branch with the same value twice, preventing duplicate subsequences.

---

## 📦 Code Snippets

Below are concise, production‑ready solutions in **Java**, **Python**, and **C++**.

> ⚠️ All three solutions run in `O(n * 2^n)` time (worst‑case) and `O(n)` auxiliary space for recursion, which is optimal for `n ≤ 15`.

### 1. Java (Java 17+)

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        List<List<Integer>> ans = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), ans);
        return ans;
    }

    private void backtrack(int[] nums, int idx,
                           List<Integer> cur, List<List<Integer>> ans) {
        if (cur.size() >= 2) ans.add(new ArrayList<>(cur));

        Set<Integer> seen = new HashSet<>();          // avoid duplicate starts
        for (int i = idx; i < nums.length; i++) {
            if (!seen.add(nums[i])) continue;          // already used at this depth
            if (cur.isEmpty() || nums[i] >= cur.get(cur.size() - 1)) {
                cur.add(nums[i]);
                backtrack(nums, i + 1, cur, ans);
                cur.remove(cur.size() - 1);
            }
        }
    }
}
```

### 2. Python (3.9+)

```python
from typing import List

class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        ans = []

        def backtrack(start: int, path: List[int]) -> None:
            if len(path) >= 2:
                ans.append(path.copy())

            used = set()                     # skip duplicate starts
            for i in range(start, len(nums)):
                if nums[i] in used:
                    continue
                used.add(nums[i])
                if not path or nums[i] >= path[-1]:
                    path.append(nums[i])
                    backtrack(i + 1, path)
                    path.pop()

        backtrack(0, [])
        return ans
```

### 3. C++ (C++17)

```cpp
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> cur;
        backtrack(nums, 0, cur, ans);
        return ans;
    }

private:
    void backtrack(const vector<int>& nums, int idx,
                   vector<int>& cur, vector<vector<int>>& ans) {
        if (cur.size() >= 2) ans.push_back(cur);

        unordered_set<int> used;          // avoid duplicate starts
        for (int i = idx; i < (int)nums.size(); ++i) {
            if (!used.insert(nums[i]).second) continue;
            if (cur.empty() || nums[i] >= cur.back()) {
                cur.push_back(nums[i]);
                backtrack(nums, i + 1, cur, ans);
                cur.pop_back();
            }
        }
    }
};
```

---

## 📚 Blog Article – “The Good, the Bad, and the Ugly of LeetCode 491”

> **SEO Tags**:  
> *LeetCode 491*, *Non‑decreasing Subsequences*, *Java solution*, *Python solution*, *C++ solution*, *interview coding*, *software engineering interview*, *job interview coding tips*, *dynamic programming*, *backtracking*, *duplicate handling*, *coding interview tricks*

---

### Introduction  
When prepping for a coding interview, LeetCode’s “Non‑decreasing Subsequences” (Problem 491) is a **must‑do**. It tests:

- **Backtracking** – how you prune the search space.  
- **Set usage** – handling duplicates.  
- **Time‑space trade‑offs** – small input but exponential blow‑up.

Below, we dissect the problem through three lenses: the *good*, the *bad*, and the *ugly*.

---

### The Good – Why This Problem is a Goldmine

| Benefit | Why It Matters |
|---------|----------------|
| **Clear recursion structure** | You can explain backtracking “step by step”—an interviewers love a neat flow. |
| **Small constraints** | `n ≤ 15` allows exhaustive search; you can discuss why `O(2^n)` is acceptable. |
| **Common interview theme** | Many candidates hit the “duplicate subsequences” issue; demonstrating the set trick shows maturity. |
| **Cross‑language** | You can present the same logic in Java, Python, and C++—showing versatility. |

**Takeaway**: Mastering this problem shows you can reason about recursion depth, pruning, and handling duplicates—skills any hiring manager values.

---

### The Bad – Pitfalls That Trip Up Candidates

| Pitfall | What it looks like | Fix |
|---------|--------------------|-----|
| **Omitting duplicate guard** | You generate duplicate subsequences, causing Wrong Answer or TLE. | Add a *local* `Set` (or `HashSet`) to track values already used at the current recursion level. |
| **Wrong base case** | Only add when `idx == n`, but forget subsequences shorter than 2. | Check `if path.size() >= 2` *before* continuing deeper. |
| **Over‑pruning** | Using `if (!cur.empty() && nums[i] < cur.back()) continue;` but not accounting for equality. | Allow `>=` to maintain non‑decreasing property. |
| **Memory leak in Java** | Returning a reference to the mutable list instead of a copy. | Clone the current list when adding to the answer. |

**Pro Tip**: Write a quick unit test for `[4,4,3,2,1]` to see if your code returns only `[[4,4]]`. It’s a quick sanity check.

---

### The Ugly – Edge Cases & Advanced Tweaks

1. **All elements equal**  
   Input: `[5,5,5,5]` → Expected `[[5,5],[5,5,5],[5,5,5,5]]`.  
   *Bug*: If you skip duplicates with a global set, you’ll miss longer subsequences.

2. **Negative numbers**  
   No special handling needed; the `>=` condition works for negatives.

3. **Large duplicates with varying positions**  
   Input: `[1,2,2,3]` → All subsequences that include the two `2`s can be formed in multiple ways.  
   *Fix*: The *local* set ensures each unique value is used only once per depth.

4. **Space‑optimised variant**  
   If you’re asked to avoid `O(n)` recursion stack, consider an iterative DFS using a manual stack, but this is rarely needed for `n ≤ 15`.

---

### How to Discuss This in an Interview

1. **State the problem in your own words** – this shows comprehension.  
2. **Explain the recursion tree** – describe why you start a new subsequence only when `nums[i] >= last`.  
3. **Show the duplicate‑avoidance trick** – mention the local `Set` and why it’s essential.  
4. **Time & Space analysis** – `O(n·2^n)` time, `O(n)` recursion stack.  
5. **Code walkthrough** – talk through the base case, loop, pruning, and backtracking step.  

> **Interview Tip**: After writing the code, walk through the example `[4,6,7,7]` on the whiteboard. Highlight how the set stops `[4,7]` from being re‑added via the second `7`.

---

### SEO‑Optimized Headline & Meta Description

- **Headline**: “LeetCode 491 – Master Non‑decreasing Subsequences (Java / Python / C++) + Interview Tips”
- **Meta Description**: “Struggling with LeetCode 491? Discover the perfect backtracking solution in Java, Python, and C++, learn how to avoid duplicates, and ace your coding interview with our job‑ready guide.”

---

### Final Words

LeetCode 491 is a **micro‑saga** of backtracking brilliance. By mastering it, you’ll be able to:

- Explain recursion cleanly.  
- Handle duplicates elegantly with local sets.  
- Discuss time/space trade‑offs.  

This is exactly the type of problem recruiters love to see candidates tackle confidently. Good luck in your next interview—and don’t forget to brag about your new “duplicate‑handling” super‑power! 🚀

---