---
title: LeetCode 491. Non-decreasing Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem recap

**LeetCode 491 – Non‑decreasing Subsequences**

> Given an integer array `nums`, return **all the different possible non‑decreasing subsequences** of the given array with at least two elements.  
> The answer may be returned in any order.

```
Input : nums = [4,6,7,7]
Output: [[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]
```

*Length of `nums` ≤ 15, so an exponential backtracking solution is fine.*

---

## 2.  Core idea

* **Backtracking** – at every position we decide whether to include the current element in the current subsequence.
* We only **append** a new element if it is **≥** the last element already in the current subsequence (to keep it non‑decreasing).
* **Duplicate subsequences** must be avoided.  
  *Option 1 –* keep a global `Set` of all found subsequences.  
  *Option 2 –* during the recursion, remember which numbers have already been used at the current depth (`seen` set) and skip duplicates immediately.  
  Both approaches give the same result; the second one is a little faster because it prunes earlier.

The recursion stops when the array is fully scanned.  
Every time the current subsequence has length ≥ 2 it is inserted into the answer set.

---

## 3.  Complexity

Let `n` be the length of `nums`.

*Time* – In the worst case we explore every subset of the array (`2^n`).  
Checking the non‑decreasing condition takes `O(1)` (just compare the last element).  
So the time is **O(2^n · n)** (the `· n` comes from copying the vector into the result set).

*Space* –  
The recursion depth is `n`, each level stores at most `n` integers → **O(n)**.  
The answer set may contain up to `2^n` subsequences, each of length `O(n)`, so the overall space can be **O(2^n · n)** in the worst case.  
For `n ≤ 15` this is perfectly fine.

---

## 4.  Reference implementations

Below are clean, production‑ready implementations in **Java, Python, and C++**.  
All three use the *seen‑at‑depth* trick to avoid duplicate work.

---

### 4.1 Java (Java 17+)

```java
import java.util.*;

public class Solution {

    public List<List<Integer>> findSubsequences(int[] nums) {
        List<List<Integer>> result = new ArrayList<>();
        backtrack(nums, 0, new ArrayList<>(), result, new HashSet<>());
        return result;
    }

    private void backtrack(int[] nums, int start, List<Integer> path,
                           List<List<Integer>> result, Set<List<Integer>> globalSet) {
        if (path.size() >= 2) {
            // store only unique subsequences
            if (globalSet.add(new ArrayList<>(path))) {
                result.add(new ArrayList<>(path));
            }
        }

        Set<Integer> seen = new HashSet<>();
        for (int i = start; i < nums.length; i++) {
            // skip duplicates at the same depth
            if (seen.contains(nums[i])) continue;
            if (path.isEmpty() || nums[i] >= path.get(path.size() - 1)) {
                seen.add(nums[i]);
                path.add(nums[i]);
                backtrack(nums, i + 1, path, result, globalSet);
                path.remove(path.size() - 1);
            }
        }
    }
}
```

*Why the `globalSet`?*  
It guarantees that each distinct subsequence appears only once, even if it can be built in several ways (e.g. `[7,7]` in the example).

---

### 4.2 Python 3

```python
from typing import List

class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        result = []
        seen_global = set()          # keep global uniqueness

        def backtrack(start: int, path: List[int]):
            if len(path) >= 2:
                t = tuple(path)
                if t not in seen_global:
                    seen_global.add(t)
                    result.append(path.copy())

            seen_local = set()      # avoid using same number twice at this level
            for i in range(start, len(nums)):
                if nums[i] in seen_local:
                    continue
                if not path or nums[i] >= path[-1]:
                    seen_local.add(nums[i])
                    path.append(nums[i])
                    backtrack(i + 1, path)
                    path.pop()

        backtrack(0, [])
        return result
```

Python’s `set` of tuples is an elegant way to keep the subsequences unique.

---

### 4.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        vector<vector<int>> ans;
        unordered_set<string> globalSet;           // for uniqueness

        vector<int> path;
        function<void(int)> dfs = [&](int idx) {
            if (path.size() >= 2) {
                string key = serialize(path);
                if (globalSet.insert(key).second)   // inserted successfully
                    ans.push_back(path);
            }

            unordered_set<int> localSeen;           // dedupe at this depth
            for (int i = idx; i < nums.size(); ++i) {
                if (localSeen.count(nums[i])) continue;
                if (path.empty() || nums[i] >= path.back()) {
                    localSeen.insert(nums[i]);
                    path.push_back(nums[i]);
                    dfs(i + 1);
                    path.pop_back();
                }
            }
        };

        dfs(0);
        return ans;
    }

private:
    // helper: convert vector to a string key (e.g. "4,6,7")
    static string serialize(const vector<int>& v) {
        string s;
        for (int x : v) s += to_string(x) + ',';
        return s;
    }
};
```

The C++ version uses a helper to serialize each subsequence into a string key so it can be stored in an `unordered_set`.  
(If you prefer, you can use `std::set<vector<int>>` for the global set – the compiler will provide the ordering operator.)

---

## 5.  Blog article

> **Title**: “Mastering LeetCode 491 – Non‑Decreasing Subsequences: The Good, The Bad, and The Ugly (With Code in Java, Python, C++)”

---

### 5.1 Introduction

- Briefly introduce LeetCode 491, why it’s a common interview question (backtracking, pruning, handling duplicates).  
- Mention that the solution is “medium” but a great showcase of algorithmic thinking.  
- State that the article will walk through the *good* design, the *bad* pitfalls to avoid, and the *ugly* edge‑cases that trip up many novices.  
- End the intro with a promise of 3 clean code snippets and a discussion on how this problem demonstrates depth that hiring managers love.

### 5.2 The “Good” – A clean, production‑ready approach

- Backtracking: Explain the recursion tree, why we can stop early.  
- Duplicate handling: Show two ways: a global set or per‑depth seen set.  
- Complexity discussion.  
- Show the Java code as a full solution, pointing out key lines (e.g. `seen.add(nums[i])` to dedupe).  
- Emphasise readability: good variable names, inline comments, using `List<Integer>` instead of raw arrays, etc.

### 5.3 The “Bad” – Common mistakes

- **Forgetting to copy the list before adding to the result** – leads to all entries pointing to the same list object.  
- **Using a simple `HashSet<List<Integer>>` without a proper `equals`/`hashCode`** – Java’s default works for lists but not for arrays.  
- **Not pruning duplicate numbers at the same recursion depth** – results in exponential blow‑up.  
- **Using a global set but not using a tuple/serialized key** in Python/C++ – results in `TypeError` or slow lookups.  
- Provide quick code snippets of the buggy version and explain why it fails.

### 5.4 The “Ugly” – Edge cases that bite

- **All equal numbers** – e.g. `[7,7,7,7]` – many repeated subsequences. Show how the per‑depth seen set solves it.  
- **Strictly decreasing array** – only one valid subsequence if any (two same numbers).  
- **Maximum length (15) with mixed signs** – discuss memory usage, but reassure that `O(2^15)` is ~32k subsequences, well within limits.  
- **Negative numbers** – no special handling needed, but good to test.

### 5.5 Why this problem is a hiring‑star

- Demonstrates *backtracking* mastery – a core interview topic.  
- Shows *deduplication* techniques – essential for production code.  
- Exposes ability to *optimize* recursion with per‑depth sets – a subtle improvement that recruiters love to spot.  
- Encourages clean code, comments, and test‑driven design.

### 5.6 SEO‑friendly keywords

- “LeetCode 491 solution”
- “non‑decreasing subsequences”
- “backtracking with duplicates”
- “Java Python C++ solution”
- “coding interview prep”
- “software engineer interview questions”

### 5.7 Call to action

- Encourage readers to implement the solution themselves in the language of their choice.  
- Invite them to submit their own test cases or ask for help in the comments.  
- Suggest adding the code to their GitHub README for a quick showcase.  
- Close with a friendly reminder: “The real test is writing clean, bug‑free code – keep practicing!”

---

## 6.  Conclusion

- Recap the algorithm and highlight the key takeaways: backtracking, duplicate handling, and time/space analysis.  
- Point out that the provided Java, Python, and C++ snippets are ready for copy‑paste into your LeetCode editor.  
- End with a motivational note: “Every interview is a chance to showcase your problem‑solving chops. Good luck!”

--- 

### 7.  TL;DR

| Language | Key Trick | Complexity |
|----------|-----------|------------|
| Java     | `HashSet<List<Integer>>` + per‑depth `seen` | `O(2^n · n)` |
| Python   | `set(tuple)` + local `seen` | `O(2^n · n)` |
| C++      | `unordered_set<string>` + local `seen` | `O(2^n · n)` |

Happy coding! 🚀