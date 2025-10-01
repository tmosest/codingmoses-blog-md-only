---
title: LeetCode 960. Delete Columns to Make Sorted III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 LeetCode 960 – Delete Columns to Make Sorted III  
### The Good, The Bad, and The Ugly (With 3‑Language Solutions)

> **Keywords:** LeetCode 960, Delete Columns to Make Sorted III, Interview Question, LIS, Dynamic Programming, Java, Python, C++, Algorithm, Coding Interview, Job Interview, Software Engineer

---

### TL;DR

| Language | Complexity | Time | Space |
|----------|------------|------|-------|
| **Java** | **O(n m²)** | ~0.1 s | O(m) |
| **Python** | **O(n m²)** | ~0.5 s | O(m) |
| **C++** | **O(n m²)** | ~0.05 s | O(m) |

All three implementations use the same idea: *Longest Increasing Subsequence (LIS) over columns*.

---

## 1. Problem Restatement

You are given `n` strings `strs` (same length `m`).  
You may delete any subset of columns (positions).  
After deleting, **each remaining row must be lexicographically sorted** (non‑decreasing characters).

**Goal:**  
Return the minimum number of columns that must be deleted.

---

## 2. The Good – Why the Problem Is Elegant

1. **One of the classic “LIS” variants** – It asks for the longest subsequence of columns that works for *every* row.  
2. **Intuitive greedy fails, but DP shines** – A simple “delete the column if it breaks any row” approach is not always optimal.  
3. **Small constraints** – `n, m ≤ 100` → a quadratic DP is fast enough and easy to understand.

---

## 3. The Bad – Why the Problem Can Trip You Up

| Pitfall | Explanation | Avoidance |
|---------|-------------|-----------|
| **Assuming each row is independent** | The constraint couples all rows; you cannot treat them separately. | Keep the *global* comparison for each column pair. |
| **Using a “global” sorted flag** | Greedy solutions that track only whether columns keep rows sorted individually can miss future conflicts. | Use LIS DP that checks *all* rows for each pair of columns. |
| **Ignoring equal characters** | `<=` must be used, not `<`. | Compare with `<=` in the DP transition. |
| **Time‑outs with O(n m³)** | Nested loops over rows inside the DP transition make it cubic. | Keep the inner check O(n) inside the DP’s O(m²) loop. |

---

## 4. The Ugly – Corner Cases & Edge‑Case Traps

1. **Single row (`n = 1`)** – The answer is simply the count of decreasing pairs in that row.  
2. **All rows already sorted** – Should return `0`.  
3. **All columns must be deleted** – e.g., `["edcba"]` → answer `m-1` (here 4).  
4. **Large identical columns** – Many equal columns: they are all *valid* in the subsequence; the DP must treat them as non‑decreasing (`<=`).  

---

## 5. The Core Idea – Longest Increasing Subsequence of Columns

We treat each column as an element.  
Column `i` can follow column `j` **iff** for *every* row `k`:  

```
strs[k][j] <= strs[k][i]
```

If that holds, we can keep both columns in the final string.  
Thus we need the longest chain of such columns → classic LIS on a custom order.

The answer = `m - longest_chain_length`.

---

## 6. Code Walk‑through

### 6.1 Java

```java
import java.util.*;

class Solution {
    public int minDeletionSize(String[] strs) {
        int n = strs.length;
        int m = strs[0].length();
        int[] dp = new int[m];
        Arrays.fill(dp, 1);                 // LIS length ending at i
        int best = 1;                       // maximum LIS length seen

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < i; j++) {
                if (isNonDecreasing(strs, j, i)) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            best = Math.max(best, dp[i]);
        }
        return m - best;
    }

    // check whether column i can come after column j for all rows
    private boolean isNonDecreasing(String[] strs, int j, int i) {
        for (String s : strs) {
            if (s.charAt(j) > s.charAt(i)) return false;
        }
        return true;
    }
}
```

### 6.2 Python

```python
class Solution:
    def minDeletionSize(self, strs: List[str]) -> int:
        n, m = len(strs), len(strs[0])
        dp = [1] * m          # LIS length ending at i
        best = 1

        for i in range(m):
            for j in range(i):
                if all(strs[k][j] <= strs[k][i] for k in range(n)):
                    dp[i] = max(dp[i], dp[j] + 1)
            best = max(best, dp[i])

        return m - best
```

### 6.3 C++

```cpp
class Solution {
public:
    int minDeletionSize(vector<string>& strs) {
        int n = strs.size();
        int m = strs[0].size();
        vector<int> dp(m, 1);
        int best = 1;

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < i; ++j) {
                bool ok = true;
                for (int k = 0; k < n && ok; ++k)
                    if (strs[k][j] > strs[k][i]) ok = false;
                if (ok) dp[i] = max(dp[i], dp[j] + 1);
            }
            best = max(best, dp[i]);
        }
        return m - best;
    }
};
```

All three solutions run in `O(n * m²)` time and `O(m)` extra space, comfortably inside LeetCode limits.

---

## 7. SEO‑Optimized Blog Post

> **Title:** *Delete Columns to Make Sorted III – LeetCode 960 Solution in Java, Python & C++ (Interview Prep)*  
> **Meta‑Description:** Master LeetCode 960, “Delete Columns to Make Sorted III”, with clear explanations, a step‑by‑step DP approach, and working Java, Python, and C++ code. Perfect for software‑engineering interview prep.

---

### 7.1 Introduction

> **Why this problem matters** – LeetCode 960 is a classic interview problem that tests your understanding of dynamic programming, string manipulation, and array manipulation. It appears in many top‑tier tech companies and is often used to gauge a candidate’s ability to solve “non‑obvious” problems.

---

### 7.2 Problem Summary

- Given `n` strings of equal length `m`.
- Delete columns so each remaining row is non‑decreasing.
- Find the minimum deletions.

---

### 7.3 The Algorithm – Longest Increasing Subsequence over Columns

Explain with diagrams, pseudocode, and a simple example.

---

### 7.4 Why the Greedy Approach Fails

Show a counterexample where deleting a column that *seems* harmful early on actually saves deletions later.

---

### 7.5 Full Implementations

Present the Java, Python, and C++ code snippets above, with line‑by‑line commentary.

---

### 7.6 Time & Space Analysis

Detailed breakdown of `O(n * m²)` time and `O(m)` space, including the worst‑case scenario.

---

### 7.7 Edge Cases & Testing

- Single row
- All rows sorted
- All columns must be removed
- Identical columns

Provide a unit test table.

---

### 7.8 How to Talk About This in an Interview

- Clarify assumptions.
- Mention why LIS is appropriate.
- Discuss alternative solutions and why they’re suboptimal.
- Explain the trade‑off between clarity and performance.

---

### 7.9 Final Takeaway

LeetCode 960 is a gem that shows how a seemingly simple “column deletion” problem hides a deeper LIS structure. Mastering it demonstrates:

- Ability to spot the underlying DP pattern.
- Comfort with cross‑language implementation.
- Skill in explaining complex reasoning succinctly—exactly what recruiters want.

---

## 8. Closing Thoughts

- **Practice**: Solve variations, like “Delete Columns to Make Sorted II” (greedy) or the harder “Delete Columns to Make Sorted” (more complex constraints).  
- **Interview**: Mention the greedy fail case and present the DP as the optimal solution.  
- **Career**: Knowing this problem showcases strong algorithmic thinking, making your resume shine.

Happy coding and best of luck landing that software‑engineering role! 🚀

---