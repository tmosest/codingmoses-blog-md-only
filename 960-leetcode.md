---
title: LeetCode 960. Delete Columns to Make Sorted III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code Solutions

Below are clean, production‑ready implementations of the **LeetCode 960 – Delete Columns to Make Sorted III** solution in **Java**, **Python**, and **C++**.  
All three use the same algorithmic idea (Longest Increasing Subsequence on columns) but are written in idiomatic style for their respective languages.

---

### 1.1 Java (Java 17)

```java
import java.util.*;

public class Solution {
    /**
     *  LeetCode 960. Delete Columns to Make Sorted III
     *  Returns the minimum number of columns that must be deleted
     *  so that every string (row) in the given array is lexicographically sorted.
     *
     *  @param strs array of equal‑length lowercase strings
     *  @return minimal deletions required
     */
    public int minDeletionSize(String[] strs) {
        int n = strs.length;          // number of rows
        int m = strs[0].length();     // number of columns

        // dp[i] = length of longest valid subsequence that ends at column i
        int[] dp = new int[m];
        Arrays.fill(dp, 1);           // every single column is valid on its own
        int best = 1;                 // best LIS length found so far

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < i; j++) {
                if (isNonDecreasing(strs, j, i)) {   // column j <= column i in all rows
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            best = Math.max(best, dp[i]);
        }
        // columns that are not part of the LIS must be deleted
        return m - best;
    }

    /**
     *  Checks whether every row satisfies strs[row][j] <= strs[row][i].
     */
    private boolean isNonDecreasing(String[] strs, int j, int i) {
        for (String s : strs) {
            if (s.charAt(j) > s.charAt(i)) {
                return false;
            }
        }
        return true;
    }
}
```

---

### 1.2 Python (Python 3.11)

```python
class Solution:
    def minDeletionSize(self, strs: List[str]) -> int:
        """Return the minimum number of columns to delete so that
        each string (row) is lexicographically sorted."""
        n, m = len(strs), len(strs[0])
        dp = [1] * m          # longest subsequence ending at column i
        best = 1

        for i in range(m):
            for j in range(i):
                if all(strs[row][j] <= strs[row][i] for row in range(n)):
                    dp[i] = max(dp[i], dp[j] + 1)
            best = max(best, dp[i])

        return m - best
```

---

### 1.3 C++ (C++17)

```cpp
#include <vector>
#include <string>
#include <algorithm>

class Solution {
public:
    int minDeletionSize(std::vector<std::string>& strs) {
        int n = strs.size();          // rows
        int m = strs[0].size();       // columns

        std::vector<int> dp(m, 1);    // LIS length ending at column i
        int best = 1;

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < i; ++j) {
                if (isNonDecreasing(strs, j, i)) {          // column j <= column i
                    dp[i] = std::max(dp[i], dp[j] + 1);
                }
            }
            best = std::max(best, dp[i]);
        }
        return m - best;
    }

private:
    // True iff for all rows strs[row][j] <= strs[row][i]
    bool isNonDecreasing(const std::vector<std::string>& strs, int j, int i) {
        for (const auto& s : strs) {
            if (s[j] > s[i]) return false;
        }
        return true;
    }
};
```

---

## 2. Blog Article – “Delete Columns to Make Sorted III: The Good, The Bad, and the Ugly”

### 2.1 Meta‑Description  
> Master LeetCode 960 – Delete Columns to Make Sorted III. Read the full guide with Java, Python, and C++ solutions, a deep‑dive algorithmic explanation, time‑space complexity, edge‑case pitfalls, and interview‑ready notes. Get your next coding interview job with this ultimate reference!

---

### 2.2 Introduction  

When preparing for a **software engineering interview**, one often encounters the *“delete columns”* family of problems on LeetCode. Among them, **LeetCode 960 – Delete Columns to Make Sorted III** is the hardest variant, and mastering it can significantly boost your algorithmic confidence.

This post walks through **the good, the bad, and the ugly** of the problem, explains why a simple greedy approach fails, shows a clean dynamic‑programming solution, and offers practical interview‑tips. Bonus: we’ll give you full working code in **Java**, **Python**, and **C++** so you can copy‑paste, run, and learn.

---

### 2.3 Problem Recap  

You’re given an array `strs` of `n` equal‑length lowercase strings.  
You may delete any subset of column indices. After deletion, **every remaining string must be lexicographically sorted** (i.e., each row’s characters are non‑decreasing from left to right).  
Return the **minimum** number of columns that must be deleted.

> Example  
> `strs = ["babca","bbazb"]` → Delete columns `{0,1,4}` → Result: `["bc","az"]` (both rows sorted). Minimum deletions = **3**.

---

### 2.4 The Good – Why the Problem Is Worth Solving  

1. **Real‑world relevance**: The core idea is about *finding a longest non‑decreasing subsequence across multiple sequences*—a concept that appears in database column selection, bio‑informatics, and data compression.
2. **Interview‑heavy**: It’s a *hard* LeetCode question, often used in tech‑company interview pipelines. Demonstrating a clear solution shows deep understanding of DP and LIS concepts.
3. **Learning cross‑language transfer**: Solving the problem in Java, Python, and C++ showcases your ability to adapt algorithms across languages, a valuable interview skill.

---

### 2.5 The Bad – Common Pitfalls and Misconceptions  

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Greedy deletion** (delete every column where any row is decreasing) | Assumes local decisions are globally optimal. | Recognize that columns can “compensate” each other. |
| **Treating the problem like LeetCode 944** (making entire array sorted) | The objective differs: we care about each row, not relative row order. | Restate the goal: *row‑wise* sortedness. |
| **O(n·m²) DP with early break** | Misinterpreting the break condition leads to missing valid columns. | Verify the comparison for **all** rows before breaking. |
| **Ignoring edge cases** (single string, all equal, strictly decreasing) | These test the boundaries of LIS logic. | Include unit tests covering `n==1`, `m==1`, and fully decreasing sequences. |

---

### 2.6 The Ugly – Why a Simple O(n·m) Greedy Fails  

A natural approach is to scan columns left‑to‑right, keep a column if it maintains non‑decreasing order for every row *given the previously kept columns*, else delete it.  
This greedy works for LeetCode 944 (entire array sorted) but **fails for 960**.  

**Counterexample**  
```
strs = ["cba", "abc"]
```
Greedy deletes column 0 (`'c'` vs `'a'` decreases), keeps column 1 (`'b'` vs `'b'` equal), deletes column 2 (`'a'` vs `'c'` decreases).  
Resulting strings: `["b", "b"]` – still sorted, but we performed **2 deletions**.  
Optimal solution keeps columns 0 and 2 (delete column 1), yielding `"ca"` and `"ac"` – both sorted with only **1 deletion**.  
Thus, decisions in earlier columns can unlock better outcomes later.

---

### 2.7 The Elegant Solution – LIS on Columns  

**Idea**:  
- View each column as a *vector* of characters, one per row.  
- Find the **longest subsequence of columns** such that for every row, the characters are non‑decreasing.  
- Minimum deletions = `total_columns - LIS_length`.

**Dynamic Programming**  
Let `dp[i]` be the length of the longest valid subsequence ending at column `i`.  
Transition:
```
dp[i] = 1 (base case)
for each j < i:
    if column j <= column i in every row:
        dp[i] = max(dp[i], dp[j] + 1)
```
Finally, `answer = m - max(dp)`.

**Complexity**  
- Time: `O(n · m²)` (n ≤ 100, m ≤ 100 → 1e6 operations, fine).  
- Space: `O(m)` for the DP array.

---

### 2.8 Code Walk‑through (Python)  

```python
class Solution:
    def minDeletionSize(self, strs: List[str]) -> int:
        n, m = len(strs), len(strs[0])
        dp = [1] * m
        best = 1

        for i in range(m):
            for j in range(i):
                if all(strs[row][j] <= strs[row][i] for row in range(n)):
                    dp[i] = max(dp[i], dp[j] + 1)
            best = max(best, dp[i])

        return m - best
```

*Explanation*  
- `all(...)` checks that the column at index `j` is **not greater** than column `i` for **every** row.  
- `dp[i]` stores the length of the best subsequence that ends at column `i`.  
- We update the global `best` to keep track of the overall longest subsequence.  
- The answer is the number of columns that **cannot** be part of this subsequence.

The same logic applies verbatim to Java and C++—just adjust syntax and string access.

---

### 2.9 Interview‑Ready Checklist  

| ✅ | Item |
|---|------|
| ✅ | Understand the *row‑wise* sortedness requirement. |
| ✅ | Explain why greedy fails with a concrete counterexample. |
| ✅ | Derive the LIS‑on‑columns DP formulation. |
| ✅ | State time/space complexity (`O(n·m²)` / `O(m)`). |
| ✅ | Show how to implement in the language of the interview (Java, Python, C++). |
| ✅ | Run edge‑case tests: single string, fully decreasing, all equal, mixed patterns. |
| ✅ | Discuss possible optimizations (e.g., early break when a row breaks the condition). |

---

### 2.10 Takeaway  

- **LeetCode 960** is a *hard* question that forces you to think beyond greedy.  
- The clean dynamic‑programming solution uses a classic **longest non‑decreasing subsequence** idea applied to **column vectors**.  
- Providing code in **three major languages** demonstrates language agility—an attractive trait for recruiters.  

Equip yourself with this algorithm, practice the code, and walk into your next coding interview *with confidence* that you can tackle the hardest “delete columns” problems.

Happy coding, and good luck landing that next job! 🚀

--- 

*End of Post*  

--- 

Feel free to **share** your own test cases, suggestions, or interview stories in the comments below!