---
title: LeetCode 491. Non-decreasing Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 491 – Non‑decreasing Subsequences  
### LeetCode | Medium | Backtracking + HashSet  

---

### TL;DR  
- Find **all** distinct non‑decreasing subsequences of length ≥ 2.  
- Use **DFS + backtracking** and a `Set` at every recursion level to avoid duplicate branches.  
- Time: **O(n · 2ⁿ)** (worst‑case), Space: **O(n · 2ⁿ)** for the result + recursion stack.  
- Works for `1 ≤ n ≤ 15` and `-100 ≤ nums[i] ≤ 100`.

---

## 1️⃣ Problem Recap  

```text
Given an integer array nums, return all the different possible non‑decreasing
subsequences of the given array with at least two elements.
```

Examples  

| Input | Output |
|-------|--------|
| `[4,6,7,7]` | `[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]` |
| `[4,4,3,2,1]` | `[[4,4]]` |

> **Why is the “duplicate” part hard?**  
> Because the same number may appear multiple times in the array, and picking
> different positions can lead to *identical* subsequences.  
> We must return each subsequence **once**.

---

## 2️⃣ Core Idea  

1. **Depth‑First Search (DFS)**  
   Start from each index and try to extend the current subsequence with a
   larger or equal element that comes later.

2. **Avoid duplicates at each depth**  
   While exploring a particular depth (`start` index), keep a local `HashSet`
   of numbers already tried.  
   If we already used `5` as the next element, skip any other `5` at the same
   depth – otherwise we would generate the same subsequence again.

3. **Collect results only when length ≥ 2**  
   As soon as a subsequence reaches 2 or more elements we add a copy of it to
   the answer.

4. **Recursive backtracking**  
   After the recursive call returns, pop the last element to restore the
   current path for the next branch.

---

## 3️⃣ Implementation

Below are three clean, production‑ready solutions in **Java**, **Python** and **C++**.

> **Tip:** All three use the same recursive strategy – the code differences
> are only syntax and minor language idioms.

---

### 3.1 Java  

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), result);
        return result;
    }

    private void backtrack(int[] nums, int start,
                           List<Integer> path,
                           List<List<Integer>> result) {
        // Add path if we have at least 2 numbers
        if (path.size() >= 2) {
            result.add(new ArrayList<>(path));
        }

        Set<Integer> used = new HashSet<>(); // prevent duplicates at this level
        for (int i = start; i < nums.length; i++) {
            if ((path.isEmpty() || nums[i] >= path.get(path.size() - 1))
                && !used.contains(nums[i])) {

                used.add(nums[i]);          // mark this value as used
                path.add(nums[i]);          // choose
                backtrack(nums, i + 1, path, result); // explore deeper
                path.remove(path.size() - 1); // un‑choose
            }
        }
    }
}
```

---

### 3.2 Python  

```python
from typing import List

class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        ans = []

        def dfs(start: int, path: List[int]):
            if len(path) >= 2:
                ans.append(path.copy())

            used = set()           # avoid duplicates at this depth
            for i in range(start, len(nums)):
                if (not path or nums[i] >= path[-1]) and nums[i] not in used:
                    used.add(nums[i])
                    path.append(nums[i])
                    dfs(i + 1, path)
                    path.pop()

        dfs(0, [])
        return ans
```

---

### 3.3 C++ (C++17)  

```cpp
#include <vector>
#include <unordered_set>

class Solution {
public:
    std::vector<std::vector<int>> findSubsequences(const std::vector<int>& nums) {
        std::vector<std::vector<int>> res;
        std::vector<int> path;
        dfs(nums, 0, path, res);
        return res;
    }

private:
    void dfs(const std::vector<int>& nums, int start,
             std::vector<int>& path,
             std::vector<std::vector<int>>& res) {
        if (path.size() >= 2)
            res.emplace_back(path);

        std::unordered_set<int> used;          // avoid duplicates
        for (int i = start; i < nums.size(); ++i) {
            if ((path.empty() || nums[i] >= path.back()) && used.find(nums[i]) == used.end()) {
                used.insert(nums[i]);
                path.push_back(nums[i]);
                dfs(nums, i + 1, path, res);
                path.pop_back();
            }
        }
    }
};
```

---

## 4️⃣ What Works (Good)  

| ✔ | Explanation |
|---|-------------|
| **Backtracking** | Natural for “all subsequences” problems. |
| **Local HashSet** | Prevents duplicate work, keeps algorithm fast. |
| **Iterative `used` set** | Works even when array contains many identical numbers. |
| **Short Code** | Easy to read and maintain. |
| **Time‑Complexity** | `O(n·2ⁿ)` – optimal for the problem size (`n ≤ 15`). |

---

## 5️⃣ What Could Be Messier (Bad)  

| ❌ | Issue |
|---|-------|
| **Using a global `Set`** | Would incorrectly discard valid subsequences that share the same value but differ in indices. |
| **Ignoring the “≥ 2” rule** | You might return single‑element sequences, which the judge will reject. |
| **Not copying the path** | Returning a reference to the same list leads to mutation issues. |
| **Deep recursion for large `n`** | Stack overflow is unlikely for `n ≤ 15`, but worth noting for bigger inputs. |

---

## 6️⃣ Edge‑Case “Ugly” Scenarios  

| Scenario | Why it’s Ugly | Fix |
|----------|---------------|-----|
| `nums = [1,1,1,1]` | Every subsequence is just `[1,1]`. | Use `used` set to skip duplicates at each depth. |
| `nums = [-1,-1,-2,-2]` | Negative numbers & duplicates | The algorithm is value‑agnostic; it still works. |
| All strictly decreasing, e.g., `[5,4,3]` | No valid subsequence | The result is empty – handle gracefully. |

---

## 7️⃣ Complexity Analysis  

| Metric | Result |
|--------|--------|
| **Time** | `O(n · 2ⁿ)` (worst‑case when array is non‑decreasing). |
| **Space** | `O(n · 2ⁿ)` for storing all subsequences + recursion stack `O(n)`. |

---

## 8️⃣ SEO‑Friendly Summary  

- **Title**: *Master LeetCode 491 – Non‑decreasing Subsequences | Java, Python, C++ Solutions*  
- **Meta Description**: “Learn how to solve LeetCode 491 in Java, Python, and C++. Explore a backtracking solution that handles duplicates, includes code snippets, and explains the algorithm step‑by‑step.”  
- **Keywords**: LeetCode 491, Non‑decreasing Subsequences, Backtracking, Java solution, Python solution, C++ solution, Interview coding, Algorithm explanation, Duplicate handling, DFS.  

---

## 9️⃣ Final Takeaway  

The **key to a clean solution** is:

1. **DFS** to explore all subsequence paths.  
2. **Local deduplication** (`used` set) at each recursion depth.  
3. **Copy** the path only when you have a valid subsequence (length ≥ 2).  

With these tricks, the code stays concise, efficient, and easy to understand across Java, Python, and C++.  

Happy coding—and good luck on your next technical interview! 🚀