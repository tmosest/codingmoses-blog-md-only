---
title: LeetCode 960. Delete Columns to Make Sorted III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 960. **Delete Columns to Make Sorted III** – Java | Python | C++ Solutions + In‑Depth Blog

> **Goal** – Show the full *production‑ready* solution in three languages, explain the algorithm, and deliver a SEO‑optimized article that will make your resume shine to recruiters.  

> **Why it matters** – “Delete Columns to Make Sorted III” is a *Hard* LeetCode problem that blends string manipulation, dynamic programming, and the classic **Longest Increasing Subsequence (LIS)** idea in a 2‑D context. Mastering it demonstrates strong algorithmic thinking, multi‑language fluency, and an eye for edge‑case handling – all the qualities a hiring manager looks for in a software engineer.

---

## 📌 Problem Recap

```
Given a list of n equal‑length strings `strs`, we may delete any set of columns
from all strings simultaneously. After deletion, each remaining string must
be lexicographically non‑decreasing character by character.

Return the minimal number of deleted columns needed.
```

*Example*  
```
strs = ["babca","bbazb"]
Answer = 3   // delete columns 0,1,4 → ["bc","az"]
```

---

## 🧠 Core Idea – LIS over Columns

Think of each column as a “feature” that can be kept or removed.  
Two columns `j` and `i` (`j < i`) can be kept in order if **every** row
satisfies `strs[row][j] <= strs[row][i]`.  
If that holds for all rows, the relative order of these two columns is
preserved in the final string.

Thus we are looking for the longest chain of columns that can stay
ordered – a **Longest Increasing Subsequence (LIS)** on columns.
The answer is simply `totalColumns – LIS`.

---

## 🛠️ Implementation – Three Languages

### 1. Java (Recommended for interviews)

```java
import java.util.*;

class Solution {
    public int minDeletionSize(String[] strs) {
        int n = strs.length;
        int m = strs[0].length();
        // dp[i] = length of LIS that ends at column i
        int[] dp = new int[m];
        Arrays.fill(dp, 1);

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < i; j++) {
                if (isNonDecreasing(strs, j, i)) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
        }
        int lis = 0;
        for (int v : dp) lis = Math.max(lis, v);
        return m - lis;           // columns to delete
    }

    // true if every row has strs[row][j] <= strs[row][i]
    private boolean isNonDecreasing(String[] strs, int j, int i) {
        for (String s : strs) {
            if (s.charAt(j) > s.charAt(i)) return false;
        }
        return true;
    }
}
```

*Time* `O(n * m²)` *Space* `O(m)`  
*Why this is good* – Uses clear helper method, minimal memory, and follows the classic LIS pattern.

---

### 2. Python 3 (Elegant & concise)

```python
class Solution:
    def minDeletionSize(self, strs: List[str]) -> int:
        n, m = len(strs), len(strs[0])
        dp = [1] * m                          # LIS length ending at each column

        for i in range(m):
            for j in range(i):
                if all(s[j] <= s[i] for s in strs):   # column j <= column i
                    dp[i] = max(dp[i], dp[j] + 1)

        return m - max(dp)                     # columns to delete
```

*Time* `O(n * m²)` *Space* `O(m)`  
*Why this is good* – One‑liners, comprehensible logic, perfect for Python‑centric interviewers.

---

### 3. C++ (Fast & type‑safe)

```cpp
class Solution {
public:
    int minDeletionSize(vector<string>& strs) {
        int n = strs.size();
        int m = strs[0].size();
        vector<int> dp(m, 1);

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < i; ++j) {
                bool ok = true;
                for (int r = 0; r < n; ++r) {
                    if (strs[r][j] > strs[r][i]) { ok = false; break; }
                }
                if (ok) dp[i] = max(dp[i], dp[j] + 1);
            }
        }
        int lis = *max_element(dp.begin(), dp.end());
        return m - lis;                         // delete the rest
    }
};
```

*Time* `O(n * m²)` *Space* `O(m)`  
*Why this is good* – Zero‑allocation, straight‑forward, fits C++ interview style.

---

## 📊 Complexity Analysis

| Approach | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| DP LIS on columns | **O(n · m²)** | **O(m)** |
| (n = rows, m = columns) | | |

With `n, m ≤ 100`, the worst‑case 1,000,000 operations are trivial for modern judges.

---

## 🔍 Edge Cases & Pitfalls

| Edge Case | What to Watch |
|-----------|---------------|
| All strings already sorted | LIS = m → return 0 |
| Single row (`n = 1`) | The problem reduces to classic “Delete Columns to Make Sorted” |
| All columns must be deleted | LIS = 1 → return m - 1 (since at least one column remains) |
| Mixed upper/lower‑case? | Problem guarantees lowercase; else char comparison still works |

---

## 🎯 Why Recruiters Love This Problem

- **Algorithmic Breadth** – Combines *DP*, *LIS*, and *string* operations.
- **Language Flexibility** – Solutions in Java, Python, C++ show cross‑language fluency.
- **Real‑World Relevance** – Data cleaning / column‑reordering problems appear in data‑engineering jobs.
- **Complexity Insight** – Demonstrates ability to reason about `O(n*m²)` constraints.

---

## 📖 Blog Article – “The Good, The Bad, and The Ugly of LeetCode 960”

---

### Introduction

When you land a software‑engineering interview, a handful of problems become “show‑stoppers”.  
One of the most challenging – and surprisingly insightful – is **LeetCode 960: Delete Columns to Make Sorted III**.  
In this article we’ll dissect the problem, walk through the *LIS‑over‑columns* solution, explore the trade‑offs, and deliver polished Java, Python, and C++ code snippets that you can copy‑paste into your interview toolkit.

---

### The Problem in a Nutshell

> **Goal** – Delete the minimum number of columns from a matrix of strings so that every remaining row is lexicographically sorted.

> **Constraints** – `1 ≤ n, m ≤ 100`; all strings consist of lowercase English letters.

> **Typical Use‑Case** – Data‑cleansing: you have a CSV table, and you want to remove bad columns so that every row is ascending.

---

### The Good – Elegant LIS on Columns

#### 1. Observations

- For columns `j` and `i` (`j < i`) to remain together, every string must satisfy `s[j] ≤ s[i]`.
- This “pairwise comparability” property is transitive: if `j ≤ i` and `i ≤ k`, then `j ≤ k`.

#### 2. LIS Formulation

- Think of each column as an element in a sequence.
- We want the longest sequence of columns that respect the pairwise comparability rule.
- That’s exactly the **Longest Increasing Subsequence (LIS)** problem, except our “increasing” test uses *all rows*.

#### 3. DP Recurrence

```text
dp[i] = 1 + max{ dp[j] | j < i and column j <= column i }
```

Where `column j <= column i` means `s[j] <= s[i]` for every string `s`.

#### 4. Final Answer

```
min deletions = totalColumns - max(dp)
```

---

### The Bad – Naïve Brute‑Force

A common mistake is to try every subset of columns (`2^m` possibilities).  
With `m = 100` this is astronomically impossible (`2^100 ≈ 1e30`).  
The LIS‑based DP reduces the search space drastically to `O(n·m²)`.

---

### The Ugly – Hidden Pitfalls

| Pitfall | Consequence | Fix |
|---------|-------------|-----|
| Forgetting to reset `dp[i] = 1` | Over‑counts columns | Initialize array with `1` |
| Using `<=` vs `<` in comparability | Wrong LIS length | Clarify that equal letters keep order (`<=`) |
| Mixing up rows vs columns in loops | Off‑by‑one errors | Iterate rows inside the column comparison |
| Ignoring single‑row case | Mis‑reported deletions | Handles naturally – LIS becomes `m` |

---

### Code Walkthrough (Java)

```java
public int minDeletionSize(String[] strs) {
    int n = strs.length, m = strs[0].length();
    int[] dp = new int[m];
    Arrays.fill(dp, 1);

    for (int i = 0; i < m; i++) {
        for (int j = 0; j < i; j++) {
            if (isNonDecreasing(strs, j, i)) {
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
    }

    int lis = Arrays.stream(dp).max().orElse(0);
    return m - lis;
}

private boolean isNonDecreasing(String[] strs, int j, int i) {
    for (String s : strs) {
        if (s.charAt(j) > s.charAt(i)) return false;
    }
    return true;
}
```

*Key Points* –  
- `dp[i]` stores the best chain ending at column `i`.  
- `isNonDecreasing` encapsulates the pairwise check.  
- We keep the algorithm **O(n·m²)** and **O(m)** memory.

---

### Python & C++ – Almost the Same

The Python code mirrors the Java logic with a concise list comprehension;  
the C++ version is a straight translation, using `vector<int>` and a nested loop.

---

### Time & Space Complexity Recap

| Language | Time | Space |
|----------|------|-------|
| Java | `O(n·m²)` | `O(m)` |
| Python | `O(n·m²)` | `O(m)` |
| C++ | `O(n·m²)` | `O(m)` |

With `n, m ≤ 100`, all implementations finish in a few milliseconds on LeetCode.

---

### Sample Test Cases

```text
Input:  ["babca","bbazb"]
Output: 3
Explanation: Delete columns 0,1,4 → ["bc","az"]

Input:  ["edcba"]
Output: 4

Input:  ["ghi","def","abc"]
Output: 0
```

---

### Takeaways for Your Interview

1. **Explain the idea**: “We’re looking for the longest chain of columns that can stay sorted; that’s an LIS problem in disguise.”
2. **Show the DP recurrence**: `dp[i] = 1 + max{ dp[j] }`.
3. **Mention complexity**: `O(n·m²)` time, `O(m)` space – easily passes the constraints.
4. **Highlight edge cases**: single row, already sorted, fully unsorted.
5. **Deliver clean code**: comment, use helper functions, keep variable names expressive.

---

### SEO‑Optimized Headline & Meta

- **Title** – “LeetCode 960 – Delete Columns to Make Sorted III (Java, Python, C++) – Master the Hard Problem”
- **Meta Description** – “Learn the optimal O(n·m²) DP solution for LeetCode 960. Get Java, Python, and C++ code, complexity analysis, and interview‑ready tips to impress recruiters.”
- **Keywords** – *Leetcode 960*, *Delete Columns to Make Sorted III*, *Java solution*, *Python solution*, *C++ solution*, *job interview algorithm*, *Longest Increasing Subsequence*, *string sorting problem*.

---

### Final Words

Solving **LeetCode 960** is a badge of honor for any algorithm enthusiast.  
The dynamic‑programming LIS trick showcases your ability to see beyond the surface, recognize patterns, and write production‑ready code in multiple languages.  

Add this problem to your portfolio, share the article on LinkedIn, and you’ll attract recruiters who value *deep technical insight* and *cross‑platform skill*. Good luck—your interviewers won’t know what hit them! 🚀

---



### End of Blog

---



### 📌 Why this Article Is a Game‑Changer

- **Clear, step‑by‑step** – Perfect for readers who need a “cookbook” solution.  
- **Job‑Focused** – Directly connects the problem to hiring needs.  
- **Share‑able** – Good for Medium, HackerRank blogs, or your own GitHub README.

---

With the article, the polished code, and the interview‑ready explanation, you’re fully equipped to tackle LeetCode 960 and walk out of the interview with confidence.

Good luck!