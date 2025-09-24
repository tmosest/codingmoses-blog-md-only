---
title: LeetCode 491. Non-decreasing Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🌟 “Non‑decreasing Subsequences” – A LeetCode 491 Masterclass  
**(Java | Python | C++) – Backtracking, Duplicate Prevention, Interview‑Ready)**  

> **SEO Tags:** LeetCode 491, Non‑decreasing Subsequences, Backtracking, Java, Python, C++, Coding Interview, Algorithm, Job Interview, Data Structures, Time Complexity, Space Complexity, Duplicate Handling, Interview Preparation  

---

## 1. Problem Overview

**LeetCode 491 – Non‑decreasing Subsequences**

> **Given** an integer array `nums` (1 ≤ `nums.length` ≤ 15, –100 ≤ `nums[i]` ≤ 100),  
> **Return** *all* the different possible **non‑decreasing** subsequences of `nums` with **at least two elements**.  
> Order of the answer does not matter.  

> **Example**  
> ```text
> Input:  nums = [4,6,7,7]
> Output: [[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]
> ```

The challenge is not just to find the subsequences but also to avoid duplicate sequences that can arise from repeated numbers.

---

## 2. Why This Problem Rocks for Interviews

| Aspect | Why It Matters |
|--------|----------------|
| **Backtracking** | Core CS technique interviewers love to test. |
| **Duplicate handling** | Shows attention to edge cases and algorithmic finesse. |
| **Complexity analysis** | You can discuss worst‑case exponential behavior versus practical pruning. |
| **Multiple language support** | Demonstrates language‑agnostic thinking. |
| **Small input size (≤ 15)** | Encourages exploring exponential solutions without worrying about memory limits. |

---

## 3. High‑Level Strategy

1. **Recursive Backtracking** – Explore every decision to **include** or **skip** each element.  
2. **Maintain a “current” sequence** – As we recurse, we build the subsequence.  
3. **Enforce non‑decreasing rule** – Only add an element if it’s ≥ the last element in the current sequence.  
4. **Avoid duplicates** – Use a `Set` at each recursion depth to remember which numbers have already been tried starting at that index.  
5. **Collect results** – Whenever the current sequence reaches length ≥ 2, push a copy to the answer list.

---

## 4. Algorithm Details

```
backtrack(start, current):
    if current.length >= 2:
        add copy of current to answer

    used = empty set
    for i from start to nums.length-1:
        if nums[i] >= (current.empty? ? -∞ : current.last) AND nums[i] not in used:
            add nums[i] to current
            backtrack(i+1, current)
            remove last element from current
            add nums[i] to used   // mark this value as used for this level
```

*The `used` set is **local** to each recursion level; it ensures that if the same value appears multiple times at the same depth, we only try it once.*

---

## 5. Complexity Analysis

| Metric | Complexity |
|--------|------------|
| **Time** | **O(2ⁿ)** in the worst case (n ≤ 15). The pruning by non‑decreasing rule and duplicate skipping reduces it in practice. |
| **Space** | **O(n)** recursion stack + **O(n·k)** for storing results (`k` = number of valid subsequences). |

Because `n` is at most 15, this exhaustive search is perfectly acceptable.

---

## 6. Edge‑Case Handling

| Edge Case | What to Watch Out For |
|-----------|------------------------|
| **All identical numbers** (e.g., `[7,7,7]`) | Duplicate sequences must be removed – the `used` set handles it. |
| **Strictly decreasing array** (e.g., `[5,4,3]`) | Only subsequences of length 1 exist → answer is empty. |
| **Negative numbers** | Compare normally; no need for special handling. |
| **Maximum size array (15)** | Recursion depth 15 → safe on typical stack limits. |

---

## 7. Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**. Each contains inline comments for clarity.

### 7.1 Java (Java 17)

```java
import java.util.*;

public class NonDecreasingSubsequences {
    public List<List<Integer>> findSubsequences(int[] nums) {
        List<List<Integer>> res = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), res);
        return res;
    }

    private void backtrack(int[] nums, int idx,
                           List<Integer> cur,
                           List<List<Integer>> res) {
        if (cur.size() >= 2) {
            res.add(new ArrayList<>(cur));
        }

        Set<Integer> used = new HashSet<>();   // dedupe at this level
        for (int i = idx; i < nums.length; i++) {
            if ((cur.isEmpty() || nums[i] >= cur.get(cur.size() - 1))
                && !used.contains(nums[i])) {

                used.add(nums[i]);             // mark value used
                cur.add(nums[i]);

                backtrack(nums, i + 1, cur, res);

                cur.remove(cur.size() - 1);     // backtrack
            }
        }
    }
}
```

### 7.2 Python (Python 3.10)

```python
from typing import List

class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        res: List[List[int]] = []
        self._backtrack(nums, 0, [], res)
        return res

    def _backtrack(self, nums: List[int], start: int,
                   cur: List[int], res: List[List[int]]) -> None:
        if len(cur) >= 2:
            res.append(cur.copy())

        used = set()  # dedupe for this depth
        for i in range(start, len(nums)):
            if (not cur or nums[i] >= cur[-1]) and nums[i] not in used:
                used.add(nums[i])
                cur.append(nums[i])
                self._backtrack(nums, i + 1, cur, res)
                cur.pop()
```

### 7.3 C++ (C++17)

```cpp
#include <vector>
#include <unordered_set>

class Solution {
public:
    std::vector<std::vector<int>> findSubsequences(std::vector<int>& nums) {
        std::vector<std::vector<int>> res;
        std::vector<int> cur;
        backtrack(nums, 0, cur, res);
        return res;
    }

private:
    void backtrack(const std::vector<int>& nums, int idx,
                   std::vector<int>& cur,
                   std::vector<std::vector<int>>& res) {
        if (cur.size() >= 2)
            res.push_back(cur);

        std::unordered_set<int> used;            // dedupe per depth
        for (int i = idx; i < (int)nums.size(); ++i) {
            if ((cur.empty() || nums[i] >= cur.back()) &&
                used.find(nums[i]) == used.end()) {

                used.insert(nums[i]);            // mark used
                cur.push_back(nums[i]);

                backtrack(nums, i + 1, cur, res);

                cur.pop_back();                  // backtrack
            }
        }
    }
};
```

---

## 8. “Good, Bad, and Ugly” – The Interview Lens

### Good

| Aspect | Why It’s a Strength |
|--------|---------------------|
| **Clear recursion** | Easy to reason, minimal code. |
| **Deduplication** | Handles repeated numbers gracefully. |
| **No extra data structures** beyond a per‑level set. |

### Bad

| Aspect | Where It Could Slip |
|--------|---------------------|
| **No memoization** | Not necessary for n ≤ 15, but for larger constraints you'd need DP. |
| **Exponential time** | Acceptable here, but interviewers might ask “what if n=30?” |

### Ugly

| Aspect | Pitfall |
|--------|---------|
| **Using a global set** | Would incorrectly dedupe across recursion levels. |
| **Ignoring the ≥ condition** | Would generate invalid sequences. |
| **Forgetting to copy `cur`** | Leads to mutated result lists (common newbie bug). |

---

## 9. Testing Checklist

| Test | Description | Expected |
|------|-------------|----------|
| `[]` | Empty input | `[]` |
| `[1]` | Single element | `[]` |
| `[1,2]` | Simple increasing | `[[1,2]]` |
| `[2,1]` | Decreasing | `[]` |
| `[7,7,7]` | All duplicates | `[[7,7]]` |
| `[4,6,7,7]` | Sample | Provided in prompt |
| `[4,4,3,2,1]` | Sample 2 | `[[4,4]]` |
| `[-5,-5,0,1]` | Negative numbers | `[[ -5,-5 ], [ -5,0 ], [ -5,1 ], [ -5,-5,0 ], [ -5,-5,1 ], [ -5,0,1 ], [ -5,-5,0,1 ]]` |
| `Random 15 numbers` | Stress | Any valid subsequences |  

Run unit tests with JUnit / pytest / Catch2 to ensure correctness.

---

## 10. Career‑Ready Tips

1. **Talk through your plan** – Start by describing “I’ll use backtracking because we need all combinations.”  
2. **Explain duplicate handling** – Interviewers love to see you think about edge cases.  
3. **Discuss complexity** – Mention the exponential nature but note that n is capped at 15, so it’s acceptable.  
4. **Mention alternative solutions** – For larger inputs, you could use DP with bitmasking or memoization.  
5. **Show up-to-date coding style** – Use language‑idiomatic patterns (e.g., Python `typing`, Java generics, C++ `std::vector`).  

---

## 11. Final Takeaway

The “Non‑decreasing Subsequences” problem is a perfect blend of **backtracking** and **duplicate avoidance** that many interviewers use to gauge a candidate’s depth. With the implementations above, you can confidently tackle this LeetCode question in **Java**, **Python**, or **C++**, and you’ll have a solid explanation ready for any interview panel. 🚀  

Happy coding, and best of luck landing that dream job!  

--- 

*If you found this article helpful, drop a comment below, share it, or subscribe for more interview‑ready deep dives.*