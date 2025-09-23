---
title: LeetCode 491. Non-decreasing Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 491. Non‑decreasing Subsequences  
**LeetCode | Medium | 1‑15 nums | [-100…100]**  

> **Problem** – Given an integer array `nums`, return **all** the different possible non‑decreasing subsequences of the array that contain **at least two elements**.  
> Example: `nums = [4,6,7,7] → [[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]`.

Below you’ll find a **step‑by‑step guide** that covers:

| What | How |
|------|-----|
| **Why** this problem is a must‑know for any software‑engineering interview | The solution demonstrates backtracking, set‑based de‑duplication, and careful handling of edge‑cases. |
| **The Good** | Fast, clean, O(n · 2ⁿ) worst‑case time, O(n) recursion depth. |
| **The Bad** | Duplicate handling can be tricky; recursion depth may hit limits in some languages. |
| **The Ugly** | Over‑complicated pruning logic; ignoring duplicates → massive memory blow‑up. |

---

## 1. Deep‑Dive: The Backtracking Strategy

1. **Recursion** – Start at each index and decide whether to include `nums[i]` in the current subsequence.
2. **Non‑decreasing constraint** – Only extend the current subsequence if `nums[i] ≥ lastElement`.  
3. **Length ≥ 2** – Once the current path has at least two items, add it to the result set.
4. **Duplicate Avoidance** – Use a `Set` (or `HashSet`) of *stringified* subsequences or a `Set` of *list* objects.  
   *A naive DFS would create the same subsequence many times (e.g., `[7,7]` from both 3rd and 4th 7).*
5. **Pruning** – For each depth, skip duplicate numbers that have already been considered at the same recursion level.

---

## 2. Complexity Analysis

| Metric | Worst‑case | Reason |
|--------|------------|--------|
| Time | **O(n · 2ⁿ)** | Every element can be chosen or skipped, and we have to copy subsequences when storing them. |
| Space | **O(n · 2ⁿ)** | Result set stores all valid subsequences. Recursion stack depth is at most `n`. |

*The constraints (n ≤ 15) keep this feasible.*

---

## 3. Code Implementations

### 3.1 Java (LeetCode‑compatible)

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        Set<List<Integer>> result = new HashSet<>();
        dfs(nums, 0, new ArrayList<>(), result);
        return new ArrayList<>(result);
    }

    private void dfs(int[] nums, int index, List<Integer> path,
                     Set<List<Integer>> result) {
        if (index == nums.length) {
            if (path.size() >= 2) result.add(new ArrayList<>(path));
            return;
        }

        // Include nums[index] if it preserves non-decreasing order
        if (path.isEmpty() || nums[index] >= path.get(path.size() - 1)) {
            path.add(nums[index]);
            dfs(nums, index + 1, path, result);
            path.remove(path.size() - 1);
        }

        // Skip duplicates at this level
        int skipVal = nums[index];
        int next = index + 1;
        while (next < nums.length && nums[next] == skipVal) next++;
        dfs(nums, next, path, result);
    }
}
```

**Key points**

* `HashSet<List<Integer>>` automatically removes duplicate subsequences.  
* `skipVal` + `next` logic prevents exploring the same value twice at the same recursion depth.

---

### 3.2 Python

```python
from typing import List

class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        res = set()
        self.dfs(nums, 0, [], res)
        return [list(seq) for seq in res]

    def dfs(self, nums, idx, path, res):
        if idx == len(nums):
            if len(path) >= 2:
                res.add(tuple(path))
            return

        # 1) take nums[idx] if non‑decreasing
        if not path or nums[idx] >= path[-1]:
            self.dfs(nums, idx + 1, path + [nums[idx]], res)

        # 2) skip this element (and all identical ones)
        skip = nums[idx]
        next_idx = idx + 1
        while next_idx < len(nums) and nums[next_idx] == skip:
            next_idx += 1
        self.dfs(nums, next_idx, path, res)
```

*Why `tuple`?*  
Sets require hashable objects; `tuple` gives us an immutable, hashable representation of a subsequence.

---

### 3.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        set<vector<int>> res;
        vector<int> path;
        dfs(nums, 0, path, res);
        return vector<vector<int>>(res.begin(), res.end());
    }

private:
    void dfs(const vector<int>& nums, int idx,
             vector<int>& path, set<vector<int>>& res) {
        if (idx == nums.size()) {
            if (path.size() >= 2) res.insert(path);
            return;
        }

        // Include nums[idx] if non‑decreasing
        if (path.empty() || nums[idx] >= path.back()) {
            path.push_back(nums[idx]);
            dfs(nums, idx + 1, path, res);
            path.pop_back();
        }

        // Skip all duplicates of nums[idx]
        int skip = nums[idx];
        int next = idx + 1;
        while (next < nums.size() && nums[next] == skip) ++next;
        dfs(nums, next, path, res);
    }
};
```

*Why `std::set`?*  
`std::set<vector<int>>` stores subsequences in sorted order and deduplicates automatically.

---

## 4. Testing & Edge Cases

| Test | Expected | Reason |
|------|----------|--------|
| `[4,6,7,7]` | 8 subsequences | Full example |
| `[4,4,3,2,1]` | `[[4,4]]` | Only one valid pair |
| `[-1, -1, -1]` | `[[-1,-1],[-1,-1,-1]]` | Works with negatives |
| `[1]` | `[]` | Less than 2 elements → no output |
| `[1,1,1,1,1]` | `[[1,1],[1,1,1],[1,1,1,1],[1,1,1,1,1]]` | All combinations of repeated element |
| `[3,2,1]` | `[]` | No non‑decreasing pairs |

---

## 5. Common Pitfalls & “Ugly” Parts

| Pitfall | How to Fix |
|---------|------------|
| **Duplicate subsequences** | Use a `Set` (HashSet/Set/vector) or dedupe while building. |
| **Too many recursive calls** | Skip identical numbers at the same depth (`nextIdx`). |
| **Stack overflow** | Problem limits n ≤ 15 → safe, but still limit recursion depth if needed. |
| **Large memory usage** | Return `vector<vector<int>>` directly; avoid copying unnecessarily. |
| **Wrong constraint handling** | Remember the subsequence must be **non‑decreasing** and **≥ 2** elements. |

---

## 6. Interview‑Ready Tips

1. **Explain the recursion tree** – talk about "take / skip" decisions.  
2. **Highlight deduplication** – show the `skip` trick.  
3. **Time/Space complexity** – be honest about the exponential nature.  
4. **Optimization** – pre‑filtering duplicates is optional; but for n ≤ 15 it’s fine.  
5. **Edge‑case handling** – small arrays, all identical numbers, strictly decreasing arrays.

---

## 7. SEO‑Optimized Blog Article Outline

Below is a ready‑to‑publish article that balances technical depth with search‑engine keywords. Feel free to copy‑paste it into your blogging platform (WordPress, Medium, dev.to, etc.).

```markdown
# LeetCode 491 – Non‑Decreasing Subsequences: A Complete Java, Python & C++ Guide

**Keywords:** LeetCode 491, Non‑decreasing Subsequences, Backtracking, Java, Python, C++, Interview Preparation, Algorithms, Data Structures

## 🚀 Introduction

LeetCode problem **491 – Non‑decreasing Subsequences** is a classic backtracking challenge that tests your ability to generate all possible subsequences of an array while maintaining order and handling duplicates. Whether you’re a **software engineer** prepping for a **coding interview** or a **programming student** polishing your algorithmic toolbox, this guide delivers:

- **Problem statement** + **examples**  
- **Step‑by‑step solution** (the “good, the bad, and the ugly”)  
- **Optimized code** in **Java, Python, and C++**  
- **Complexity analysis**  
- **Interview‑friendly talking points**

Let’s dive in!

## 📌 Problem Summary

Given an integer array `nums` (1 ≤ n ≤ 15, -100 ≤ nums[i] ≤ 100), return *all* distinct non‑decreasing subsequences that contain at least two elements.  

> *Example:*  
> Input: `[4,6,7,7]`  
> Output: `[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]`

## ✅ Why This Problem Rocks

| Feature | Value |
|--------|-------|
| **Backtracking core** | Reinforces the classic “pick or skip” pattern |
| **Set‑based deduplication** | Teaches efficient duplicate handling |
| **Moderate constraints** | Fast enough for interview settings |

---

## 🔧 The “Good”

- **Clean Recursion** – Pick or skip each element; base case is trivial.  
- **Fast Duplicate Skipping** – Skip over identical values at the same recursion depth.  
- **Compact Set Usage** – A single `HashSet` (Java), `set` (Python), or `std::set` (C++) stores all valid subsequences automatically.

---

## ⚠️ The “Bad”

- **Recursive Depth** – Although `n ≤ 15`, deep recursion can hit limits in some environments.  
- **Memory Footprint** – Storing every subsequence may consume a lot of RAM if many are valid (worst‑case `2ⁿ`).  

---

## 😈 The “Ugly”

- **Over‑complicated Pruning** – Some solutions use bitmasks or extra arrays to avoid duplicates, making the code unreadable.  
- **Inefficient Copying** – Re‑creating the path on every recursive call can lead to quadratic time.  

---

## 📚 Walkthrough: Backtracking + Deduplication

1. **Start at index 0**.  
2. **Decision Point** –  
   - *Take* `nums[i]` **if** it is ≥ last element in current path.  
   - *Skip* `nums[i]` (and all subsequent identical values).  
3. **Record** the path if its length ≥ 2.  
4. **Recurse** to `i + 1`.  

The pseudo‑code:

```
dfs(index, path):
    if index == n:
        if len(path) >= 2: add to result
        return
    # Include current element
    if not path or nums[index] >= path[-1]:
        path.append(nums[index])
        dfs(index+1, path)
        path.pop()
    # Skip duplicates
    skip_val = nums[index]
    next_index = index + 1
    while next_index < n and nums[next_index] == skip_val:
        next_index += 1
    dfs(next_index, path)
```

---

## 📦 Code Snippets

### Java

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        Set<List<Integer>> result = new HashSet<>();
        dfs(nums, 0, new ArrayList<>(), result);
        return new ArrayList<>(result);
    }

    private void dfs(int[] nums, int idx, List<Integer> path, Set<List<Integer>> res) {
        if (idx == nums.length) {
            if (path.size() >= 2) res.add(new ArrayList<>(path));
            return;
        }
        if (path.isEmpty() || nums[idx] >= path.get(path.size() - 1)) {
            path.add(nums[idx]);
            dfs(nums, idx + 1, path, res);
            path.remove(path.size() - 1);
        }
        int skip = nums[idx];
        int next = idx + 1;
        while (next < nums.length && nums[next] == skip) next++;
        dfs(nums, next, path, res);
    }
}
```

### Python

```python
from typing import List

class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        res = set()
        def dfs(i, path):
            if i == len(nums):
                if len(path) >= 2:
                    res.add(tuple(path))
                return
            if not path or nums[i] >= path[-1]:
                dfs(i + 1, path + [nums[i]])
            skip = nums[i]
            j = i + 1
            while j < len(nums) and nums[j] == skip:
                j += 1
            dfs(j, path)
        dfs(0, [])
        return [list(t) for t in res]
```

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        set<vector<int>> res;
        vector<int> path;
        dfs(nums, 0, path, res);
        return vector<vector<int>>(res.begin(), res.end());
    }
private:
    void dfs(const vector<int>& nums, int i, vector<int>& path, set<vector<int>>& res) {
        if (i == nums.size()) {
            if (path.size() >= 2) res.insert(path);
            return;
        }
        if (path.empty() || nums[i] >= path.back()) {
            path.push_back(nums[i]);
            dfs(nums, i + 1, path, res);
            path.pop_back();
        }
        int skip = nums[i];
        int next = i + 1;
        while (next < nums.size() && nums[next] == skip) ++next;
        dfs(nums, next, path, res);
    }
};
```

---

## ⏱️ Complexity Analysis

| Language | Time | Space |
|---------|------|-------|
| Java / Python / C++ | **O(2ⁿ)** (worst‑case) | **O(2ⁿ)** (result storage) |

With `n ≤ 15`, `2ⁿ` is only **32,768**, which comfortably fits in interview‑time limits.

---

## 🎤 Talking Points for the Interview

1. **Base‑Case Explanation** – “When do we stop?”  
2. **Duplicate Skipping** – “We skip all identical elements at the same depth.”  
3. **Set Deduplication** – “A single set automatically removes duplicates.”  
4. **Complexity Discussion** – “Exponential, but constraints are small.”  

---

## 📌 Final Takeaway

LeetCode **491** is more than just a coding puzzle; it’s a micro‑ecosystem of backtracking, data‑structure design, and careful edge‑case handling. Mastering the solution in **Java, Python, and C++** not only lands you a high‑score on LeetCode but also equips you with a **talk‑able algorithm** for your next **software engineer interview**.

Happy coding! 🚀
```

---

## 📅 Publishing Tips

1. **Add a cover image** (e.g., a diagram of the recursion tree).  
2. **Embed code blocks** with syntax highlighting.  
3. **Include a “Read More”** link to your GitHub repository.  
4. **SEO meta‑tags** – set the title and description in the page’s meta section.  

---

## 📣 Final Word

Congratulations! You now have a **ready‑to‑go** guide covering LeetCode 491 with **Java, Python, and C++** solutions, a **detailed interview cheat‑sheet**, and an **SEO‑friendly blog article** that’s ready to attract readers and boost your online presence.

Happy interviewing—and remember: *good code is clean; bad code is fast; ugly code is unreadable.* Keep writing, keep optimizing, and keep interviewing! 💪
```

---

## 8. Closing Thoughts

Problem 491 is a **sweet spot**: the constraints are tight enough for a production‑grade solution, yet the logic is rich enough to discuss during a technical interview. The duplicate‑skipping trick is the heart of a **clean, efficient implementation**—apply it in your own projects to handle similar “all‑subsets‑with‑unique‑results” scenarios.

**Good luck** on your next coding challenge! Happy coding! 🚀

---

Feel free to tweak the article to match your personal voice or add your own commentary. Good luck with the interview prep, and happy coding!