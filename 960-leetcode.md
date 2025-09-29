---
title: LeetCode 960. Delete Columns to Make Sorted III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem – LeetCode 960: *Delete Columns to Make Sorted III*

**Goal**  
Given an array of `n` strings of equal length `m`, delete the minimum number of columns so that **every** remaining string is in non‑decreasing lexicographic order (i.e. its characters are sorted from left to right).

| Sample | Input | Output | Reasoning |
|--------|-------|--------|-----------|
| 1 | `["babca","bbazb"]` | `3` | Delete columns `0,1,4` → `["bc","az"]`. |
| 2 | `["edcba"]` | `4` | Any column left would break the order. |
| 3 | `["ghi","def","abc"]` | `0` | All rows already sorted. |

Constraints  
```
1 ≤ n ≤ 100
1 ≤ m ≤ 100
strs[i] consists of lowercase English letters
```

---

## 2. Intuition – Longest Valid Column Subsequence

If we keep a subset of columns, they must respect the ordering rule **for all rows**.  
Think of each column as a “character” and we want the longest subsequence of columns that is **compatible**:

```
Column j can precede column i
⇔ for every row r: strs[r][j] ≤ strs[r][i]
```

If we find the longest such subsequence (call it L), then the minimal deletions are `m – L`.

The problem is essentially the **Longest Increasing Subsequence (LIS)** on the set of columns with a custom comparison.  
Because `m ≤ 100`, a simple `O(m² · n)` dynamic‑programming solution is fast enough.

---

## 3. Algorithm (Dynamic Programming)

```
dp[i] = length of the longest valid subsequence that ends at column i
answer = 0

for i = 0 … m-1
    dp[i] = 1                // subsequence of length 1 (just column i)
    for j = 0 … i-1
        if column j can precede column i   // check all rows
            dp[i] = max(dp[i], dp[j] + 1)
    answer = max(answer, dp[i])

return m - answer
```

*Checking “column j can precede column i”*  
Loop over all rows `r`.  
If any `strs[r][j] > strs[r][i]`, the pair is invalid → break.  
If all rows pass, the pair is valid.

Time complexity: `O(m² · n)`  
Space complexity: `O(m)`

---

## 4. Reference Implementations

### 4.1 Java

```java
import java.util.*;

public class Solution {
    public int minDeletionSize(String[] strs) {
        int n = strs.length;          // number of rows
        int m = strs[0].length();     // number of columns

        int[] dp = new int[m];
        int best = 0;

        for (int i = 0; i < m; i++) {
            dp[i] = 1;                // at least this column alone
            for (int j = 0; j < i; j++) {
                if (canPrecede(strs, j, i)) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            best = Math.max(best, dp[i]);
        }
        return m - best;               // columns to delete
    }

    /** Return true if column j can precede column i for all rows */
    private boolean canPrecede(String[] strs, int j, int i) {
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

### 4.2 Python

```python
class Solution:
    def minDeletionSize(self, strs: List[str]) -> int:
        n, m = len(strs), len(strs[0])
        dp = [1] * m
        best = 0

        for i in range(m):
            for j in range(i):
                if all(s[j] <= s[i] for s in strs):
                    dp[i] = max(dp[i], dp[j] + 1)
            best = max(best, dp[i])

        return m - best
```

---

### 4.3 C++

```cpp
class Solution {
public:
    int minDeletionSize(vector<string>& strs) {
        int n = strs.size();
        int m = strs[0].size();
        vector<int> dp(m, 1);
        int best = 0;

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < i; ++j) {
                bool ok = true;
                for (int r = 0; r < n; ++r) {
                    if (strs[r][j] > strs[r][i]) { ok = false; break; }
                }
                if (ok) dp[i] = max(dp[i], dp[j] + 1);
            }
            best = max(best, dp[i]);
        }
        return m - best;
    }
};
```

---

## 5. The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Problem Understanding** | Clear: find minimal deletions → longest valid column subsequence | Mis‑reading “row” vs “column” ordering → wrong solution | Over‑optimizing with fancy data structures when `m ≤ 100` |
| **Algorithm Choice** | Simple DP (`O(m²·n)`) is fast and easy to reason | More complex LIS with segment tree is unnecessary | Trying to use bitmasks or bitset DP → overkill |
| **Edge Cases** | Handles `m = 1`, all rows already sorted | Forgetting to subtract from `m` → wrong answer | Not checking all rows for each pair → silent bugs |
| **Coding Style** | Clean helper `canPrecede` improves readability | Inline loops become hard to debug | Mixing 0‑based and 1‑based indices → off‑by‑one errors |
| **Testing** | Unit tests for samples, random cases | No tests → confidence low | Stress‑testing on 100x100 random data |
| **Complexity** | Linearithmic in practice (tiny constants) | O(m³) DP would TLE | Memory blow‑up with `m²` matrix when not needed |

**Takeaway:** Keep the solution simple, test thoroughly, and remember that the problem’s constraints allow a clean DP solution.

---

## 6. SEO‑Optimized Blog Article

> **Title:**  
> *Delete Columns to Make Sorted III – LeetCode 960 – Java, Python, C++ DP Solution & Interview Tips*

> **Meta Description (≈155 chars):**  
> Master LeetCode 960 “Delete Columns to Make Sorted III” with a clear O(m²·n) DP solution in Java, Python, and C++. Learn the good, bad, and ugly parts of coding interviews.

---

### 1. Introduction

If you’re prepping for software engineering interviews, **LeetCode 960 – Delete Columns to Make Sorted III** is a staple. The challenge tests your ability to think about subsequences, dynamic programming, and careful handling of multi‑dimensional data. Below, we walk through a clean DP solution, provide implementations in **Java**, **Python**, and **C++**, and discuss common pitfalls that can cost you in an interview.

### 2. Problem Recap

Given an array of equal‑length strings, we may delete any set of column indices. After deletions, **every row** must be sorted in non‑decreasing order. Return the minimal number of deletions needed.

> Example  
> `["babca","bbazb"]` → Delete columns `{0,1,4}` → Result `["bc","az"]` → **3 deletions**.

### 3. Why a Longest Increasing Subsequence (LIS) on Columns?

If we keep a subset of columns, they must respect the ordering rule **for all rows**.  
Thus we can treat each column as a “letter” and search for the longest subsequence of columns that is compatible: for any earlier column `j` and later column `i`, all rows satisfy `strs[r][j] ≤ strs[r][i]`.

The maximum length `L` of such a subsequence tells us how many columns we can keep. The answer is simply `m – L`.

Because the constraints (`m, n ≤ 100`) are tiny, a straightforward dynamic programming approach is enough.

### 4. The DP Formula

```
dp[i] = 1
for j in [0, i):
    if column j can precede i (check all rows):
        dp[i] = max(dp[i], dp[j] + 1)
```

The helper condition “column j can precede i” means:
```text
∀ r : strs[r][j] ≤ strs[r][i]
```
If any row violates this, the pair is invalid.

The answer is `m - max(dp)`.

### 5. Code in 3 Languages

#### Java
```java
public class Solution {
    public int minDeletionSize(String[] strs) {
        int n = strs.length, m = strs[0].length();
        int[] dp = new int[m];
        int best = 0;

        for (int i = 0; i < m; i++) {
            dp[i] = 1;
            for (int j = 0; j < i; j++) {
                if (canPrecede(strs, j, i)) {
                    dp[i] = Math.max(dp[i], dp[j] + 1);
                }
            }
            best = Math.max(best, dp[i]);
        }
        return m - best;
    }

    private boolean canPrecede(String[] strs, int j, int i) {
        for (String s : strs) {
            if (s.charAt(j) > s.charAt(i)) return false;
        }
        return true;
    }
}
```

#### Python
```python
class Solution:
    def minDeletionSize(self, strs: List[str]) -> int:
        n, m = len(strs), len(strs[0])
        dp = [1] * m
        best = 0

        for i in range(m):
            for j in range(i):
                if all(s[j] <= s[i] for s in strs):
                    dp[i] = max(dp[i], dp[j] + 1)
            best = max(best, dp[i])

        return m - best
```

#### C++
```cpp
class Solution {
public:
    int minDeletionSize(vector<string>& strs) {
        int n = strs.size(), m = strs[0].size();
        vector<int> dp(m, 1);
        int best = 0;

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < i; ++j) {
                bool ok = true;
                for (int r = 0; r < n; ++r)
                    if (strs[r][j] > strs[r][i]) { ok = false; break; }
                if (ok) dp[i] = max(dp[i], dp[j] + 1);
            }
            best = max(best, dp[i]);
        }
        return m - best;
    }
};
```

### 6. Complexity Analysis

- **Time:** `O(m² · n)` – at most `100 * 100 * 100 = 1,000,000` operations, negligible.
- **Space:** `O(m)` – one DP array.

### 7. Common Interview Mistakes

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Assuming only adjacent rows matter** | The condition must hold for *every* row. | Loop over all rows when checking a pair. |
| **Using `m²` but ignoring `n`** | Neglecting row comparisons makes the DP invalid. | Inside the `j < i` loop, iterate over all rows. |
| **Mis‑indexing** | Off‑by‑one errors are common with 0‑based strings. | Stick to 0‑based indices throughout. |
| **Returning `m - max(dp)` incorrectly** | If you forget to subtract from `m`, answer will be `max(dp)` instead. | Remember that deletions = columns left out. |

### 8. Why This Matters for Your Career

- **Dynamic Programming:** Mastering DP on a 2‑D array demonstrates strong algorithmic thinking—essential for backend, data‑engineering, or full‑stack roles.
- **Multi‑Language Skillset:** Knowing how to implement the same logic in **Java**, **Python**, and **C++** showcases versatility; recruiters value candidates who can switch contexts.
- **Problem Decomposition:** Recognizing the LIS analogy shows your ability to abstract a complex requirement into a simpler subproblem—a prized interview skill.

### 9. Next Steps

1. Run the provided solutions against random test cases (LeetCode’s hidden tests are often large).  
2. Add your own unit tests for edge scenarios (`m=1`, already sorted, completely unsorted).  
3. Practice explaining the DP formula out loud – interviewers want you to think on the spot.

### 9. Conclusion

LeetCode 960 is a gentle yet powerful introduction to dynamic programming on matrices. By treating the problem as a LIS on columns and using a clean `O(m²·n)` DP, you can solve it efficiently in any language. Keep the code simple, handle all rows, and avoid common pitfalls. Good luck cracking this problem—and your next software engineering interview!

---

### 9.1. Quick Checklist for Interviewers

- **Read the prompt carefully** – columns vs rows.  
- **Explain your approach** before coding.  
- **Write helper functions** for readability.  
- **Test your solution** mentally on all samples.  
- **Mention complexity** to show awareness.

---

**Happy coding!** 🚀

--- 

> *Want more interview prep? Subscribe to our weekly newsletter for in‑depth analyses of LeetCode problems, algorithm patterns, and real interview stories.*

---

### 9.2 Keywords

- Delete Columns to Make Sorted III
- LeetCode 960
- DP solution
- Dynamic programming
- Longest increasing subsequence
- Java implementation
- Python solution
- C++ algorithm
- Software engineering interview
- Coding interview patterns

--- 

**End of article.**