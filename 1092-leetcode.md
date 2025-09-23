---
title: LeetCode 1092. Shortest Common Supersequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Shortest Common Supersequence (LeetCode 1092) – The Good, The Bad, & The Ugly  
**Keywords:** *Shortest Common Supersequence, LCS, Dynamic Programming, LeetCode 1092, coding interview, Java, Python, C++*  

---

### 1. Problem Overview

| Item | Detail |
|------|--------|
| **Name** | Shortest Common Supersequence |
| **LeetCode ID** | 1092 |
| **Difficulty** | Hard |
| **Input** | Two lowercase strings `str1` and `str2` (1 ≤ len ≤ 1000) |
| **Output** | The *shortest* string that contains both `str1` and `str2` as subsequences. If multiple solutions exist, return any one. |
| **Example** | `str1 = "abac"`, `str2 = "cab"` → `"cabac"` |

A *subsequence* is obtained by deleting zero or more characters from a string without changing the order of the remaining characters.

---

### 2. Why This Problem Rocks Your Interview Portfolio

- **Showcases DP mastery** – You need the classic *Longest Common Subsequence* (LCS) trick, a staple in many interviews.
- **Demonstrates clever reconstruction** – After computing LCS, you must build the supersequence in linear time.
- **Scales nicely** – Handles up to 1000‑character strings comfortably with O(m × n) time & O(m × n) space.

---

### 3. Intuition & The Two‑Step Strategy

1. **Compute LCS**  
   The LCS tells you the longest common “core” of the two strings. Characters in the LCS need to appear *once* in the final answer.

2. **Merge while walking backwards**  
   Starting from the ends of both strings, walk towards the start using the DP table to decide whether to:
   - Take a common character (if equal).
   - Take a character from one string only (if different and the DP value indicates a better choice).

Finally, prepend any remaining characters from either string. The resulting string is the Shortest Common Supersequence (SCS).

---

### 4. Dynamic Programming Table

Let  

- `m = str1.length`, `n = str2.length`.  
- `dp[i][j]` = length of LCS of `str1[0 … i‑1]` and `str2[0 … j‑1]`.

Transition:

```
if str1[i-1] == str2[j-1]:
    dp[i][j] = 1 + dp[i-1][j-1]
else:
    dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

Initialize `dp[0][*] = dp[*][0] = 0`.

---

### 5. Reconstructing the SCS

```text
i = m, j = n, result = []

while i > 0 and j > 0:
    if str1[i-1] == str2[j-1]:
        result.append(str1[i-1])
        i--; j--
    elif dp[i-1][j] > dp[i][j-1]:
        result.append(str1[i-1])
        i--
    else:
        result.append(str2[j-1])
        j--

while i > 0:
    result.append(str1[i-1]); i--
while j > 0:
    result.append(str2[j-1]); j--

return reverse(result)
```

The `reverse` step is necessary because we built the answer from the end backwards.

---

### 6. Complexity Analysis

| Metric | Calculation |
|--------|-------------|
| **Time** | O(m × n) – DP table + linear back‑tracking |
| **Space** | O(m × n) – DP table (can be reduced to O(n) if you only keep two rows, but reconstruction then needs the full table) |

With `m, n ≤ 1000`, this is comfortably fast.

---

### 7. Edge‑Case Checklist

| Edge Case | What to watch for |
|-----------|-------------------|
| Identical strings | Result is the string itself (shortest possible). |
| No common characters | Result is `str1 + str2`. |
| One string empty | Result is the other string. |
| All characters identical but strings differ in length | Result length = max(len1, len2). |

---

### 8. Implementation – Java, Python, & C++

> **Tip:** Keep your code clean and add inline comments for readability – interviewers appreciate clear logic.

---

#### 8.1 Java

```java
/**
 * LeetCode 1092 – Shortest Common Supersequence
 */
class Solution {
    public String shortestCommonSupersequence(String str1, String str2) {
        int m = str1.length(), n = str2.length();
        int[][] dp = new int[m + 1][n + 1];

        // Build LCS DP table
        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (str1.charAt(i - 1) == str2.charAt(j - 1))
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                else
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
            }
        }

        // Reconstruct SCS
        StringBuilder sb = new StringBuilder();
        int i = m, j = n;
        while (i > 0 && j > 0) {
            if (str1.charAt(i - 1) == str2.charAt(j - 1)) {
                sb.append(str1.charAt(i - 1));
                i--; j--;
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                sb.append(str1.charAt(i - 1));
                i--;
            } else {
                sb.append(str2.charAt(j - 1));
                j--;
            }
        }

        while (i > 0) { sb.append(str1.charAt(--i)); }
        while (j > 0) { sb.append(str2.charAt(--j)); }

        return sb.reverse().toString();
    }
}
```

---

#### 8.2 Python

```python
# LeetCode 1092 – Shortest Common Supersequence
class Solution:
    def shortestCommonSupersequence(self, str1: str, str2: str) -> str:
        m, n = len(str1), len(str2)
        # DP table
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if str1[i-1] == str2[j-1]:
                    dp[i][j] = dp[i-1][j-1] + 1
                else:
                    dp[i][j] = max(dp[i-1][j], dp[i][j-1])

        # Backtrack to build SCS
        i, j = m, n
        res = []
        while i and j:
            if str1[i-1] == str2[j-1]:
                res.append(str1[i-1])
                i -= 1; j -= 1
            elif dp[i-1][j] > dp[i][j-1]:
                res.append(str1[i-1]); i -= 1
            else:
                res.append(str2[j-1]); j -= 1

        res.extend(reversed(str1[:i]))
        res.extend(reversed(str2[:j]))
        return ''.join(reversed(res))
```

---

#### 8.3 C++

```cpp
// LeetCode 1092 – Shortest Common Supersequence
class Solution {
public:
    string shortestCommonSupersequence(string str1, string str2) {
        int m = str1.size(), n = str2.size();
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        // Build LCS table
        for (int i = 1; i <= m; ++i)
            for (int j = 1; j <= n; ++j)
                dp[i][j] = (str1[i-1] == str2[j-1]) ?
                    dp[i-1][j-1] + 1 :
                    max(dp[i-1][j], dp[i][j-1]);

        // Reconstruct SCS
        string res;
        int i = m, j = n;
        while (i && j) {
            if (str1[i-1] == str2[j-1]) {
                res.push_back(str1[i-1]);
                --i; --j;
            } else if (dp[i-1][j] > dp[i][j-1]) {
                res.push_back(str1[i-1]);
                --i;
            } else {
                res.push_back(str2[j-1]);
                --j;
            }
        }
        while (i) res.push_back(str1[--i]);
        while (j) res.push_back(str2[--j]);

        reverse(res.begin(), res.end());
        return res;
    }
};
```

---

### 9. The Good

- **Clear DP foundation** – LCS is a classic interview question; reusing it here demonstrates deep understanding.
- **Clean reconstruction** – Walking backwards keeps the code concise and linear.
- **Scalable** – O(10⁶) operations for the worst case, well below typical time limits.

---

### 10. The “Ugly” – What Could Trip You Up

| Pitfall | Fix |
|---------|-----|
| Forgetting to reverse at the end | The answer is built from the tail; reversing is essential. |
| Off‑by‑one errors in DP indices | Always test on small strings (`"a"`, `"b"`), `i-1` vs `i` is a frequent source of bugs. |
| Not handling ties correctly | If `dp[i-1][j] == dp[i][j-1]`, choosing either string works; your code handles this by picking `str2[j-1]`. |
| Excessive space | Using a full DP table is fine, but you can reduce it to O(n) if you’re careful about reconstruction. |

---

### 11. The Bad (Common Mistakes)

1. **Using only one DP row**  
   - Works for LCS length but you lose the full table needed for back‑tracking → leads to wrong SCS or extra complexity.

2. **Appending before reversing**  
   - Many solutions mistakenly append the characters in reverse order without reversing at the end → answer will be mirrored.

3. **Ignoring the “common” branch**  
   - Some naive merges forget to *remove* duplicates of the LCS, producing a longer supersequence.

---

### 11.1 What to Avoid in Interviews

- **Verbose code** – Keep functions short; avoid nested loops inside loops unless necessary.
- **Uncommented magic numbers** – Always explain the meaning of `dp[i][j] > dp[i][j-1]`.
- **Assume equality** – Edge cases where one string is empty should be handled explicitly for completeness.

---

### 11. The Ugly

- **O(m × n) space** – Though acceptable, some candidates aim for O(n) space by discarding the table early. This is a *good* optimization to show but complicates reconstruction.
- **Back‑tracking pitfalls** – Getting the direction wrong yields a *sub‑optimal* answer; double‑check DP transitions against test cases.

---

### 11. The Ugly – When It’s a Problem

- **Large input** – If the judge imposes strict memory limits, a 1000×1000 table (≈ 4 MB in Java) may still be okay, but for 10⁴ it would blow up.
- **Multiple passes** – Re‑computing DP or reconstructing twice defeats the “hard” tag.

---

### 11. Quick Fix for Space‑Constrained Environments

If you must fit in 1 MB, keep two rows of `dp` while building, but *store* the whole table *only* if you need reconstruction. Otherwise, you can reconstruct on the fly with a greedy two‑pointer approach that might produce a longer answer but still valid for the hard constraints.

---

### 10. Final Thoughts

You’ve just walked through a *hard* problem that beautifully re‑packages an *easy* DP trick (LCS). Delivering any of the three implementations above in an interview will impress hiring managers and prove you’re ready for **Hard‑DP** questions.

> **Pro‑Tip:** Pair this solution with a succinct explanation of the underlying DP logic – that’s the combination that turns a good candidate into a great one.

Happy coding—and happy interviewing! 🚀

---

> *Feel free to copy‑paste the code snippets into your favourite IDE, run them against the provided examples, and share them on your portfolio or LinkedIn to showcase your problem‑solving chops.*