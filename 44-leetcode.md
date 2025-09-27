---
title: LeetCode 44. Wildcard Matching - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Wildcard Matching – LeetCode 44  
**Hard | 2000 ≤ |s|, |p| ≤ 2000 | DP / Greedy | Java / Python / C++**

> “The best interview questions are the ones you can answer confidently and explain clearly.” – *Job‑hunt 101*

---

### 1.  Problem Overview

| Field | Details |
|-------|---------|
| **Name** | Wildcard Matching |
| **LeetCode ID** | 44 |
| **Difficulty** | Hard |
| **Input** | Two strings `s` (text) and `p` (pattern) |
| **Output** | `true` if `p` matches the *entire* `s`, `false` otherwise |
| **Wildcard rules** | `?` → any single character<br>`*` → any sequence (including empty) |
| **Constraints** | `0 ≤ s.length, p.length ≤ 2000`<br>`s` only lowercase English letters<br>`p` only lowercase English letters, `?`, `*` |

> Example  
> `s = "aa"`, `p = "*" → true`  
> `s = "cb"`, `p = "?a" → false`

---

### 2.  Intuition

We need to know **whether the two strings can “align”** when we replace `?` and `*` with the appropriate characters.

The problem is a classic dynamic‑programming on strings:

```
dp[i][j]  = does s[0..i) match p[0..j)?
```

* `i` is the length of the considered prefix of `s`
* `j` is the length of the considered prefix of `p`

With this recurrence we can explore all possibilities **once** and get linear‑time space.

---

### 3.  Four Ways to Solve

| Approach | Idea | Complexity | Pros | Cons |
|----------|------|------------|------|------|
| **Recursive** | Try every choice when encountering `*`. | O(2^n) time | Very intuitive | Exponential time, stack overflow |
| **Memoized** | Same as recursive + cache | O(n m) time, O(n m) space | Fast, still readable | Still uses recursion stack |
| **Tabulation** | Bottom‑up DP table | O(n m) time, O(n m) space | No recursion, easier to debug | Memory heavy for 2000 × 2000 |
| **Space‑Optimized DP** | Keep only the previous row | O(n m) time, **O(m)** space | 4 MB in C++/Java, 2000 * 2000 ≈ 4 MB | Slightly more logic for the first column |

> **Bonus** – a *greedy* linear‑time, constant‑space solution that scans from left to right and backtracks when needed.  
> In practice, the DP solution is what most interviewers expect, but the greedy trick can be a neat “gotcha” answer.

Below are the three code versions you’ll want on your resume and in your GitHub portfolio.

---

## 4.  Reference Implementations

> **Tip** – Keep a copy of the “clean” DP code on your GitHub, and mention that you *understood* the recurrence when asked.

---

#### 4.1  Java – Bottom‑Up DP (2‑D)  

```java
// 44. Wildcard Matching – LeetCode
public class Solution {
    /**
     * Classic DP: dp[i][j] == true if s[0..i) matches p[0..j)
     * We use (n+1) x (m+1) boolean table, where n = s.length(), m = p.length()
     */
    public boolean isMatch(String s, String p) {
        int n = s.length(), m = p.length();
        boolean[][] dp = new boolean[n + 1][m + 1];
        dp[0][0] = true;          // empty pattern matches empty string

        /* First row: s is empty – only leading '*' in p can match */
        for (int j = 1; j <= m; j++) {
            if (p.charAt(j - 1) == '*') dp[0][j] = dp[0][j - 1];
        }

        /* Fill the table */
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                char pc = p.charAt(j - 1);
                if (pc == '*') {
                    dp[i][j] = dp[i - 1][j] || dp[i][j - 1];
                } else if (pc == '?' || pc == s.charAt(i - 1)) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = false;
                }
            }
        }
        return dp[n][m];
    }
}
```

*Time*: **O(n m)**  
*Space*: **O(n m)** – for 2000 × 2000 this is ~4 MB, fine for LeetCode.

---

#### 4.2  Python – Space‑Optimized DP  

```python
# 44. Wildcard Matching – Python 3
def is_match(s: str, p: str) -> bool:
    n, m = len(s), len(p)

    # prev[j] is dp[i-1][j] from the previous row
    prev = [False] * (m + 1)
    prev[0] = True

    # Initialize the first row (empty s)
    for j in range(1, m + 1):
        if p[j - 1] == '*':
            prev[j] = prev[j - 1]

    for i in range(1, n + 1):
        curr = [False] * (m + 1)
        curr[0] = False          # non‑empty s cannot match empty pattern
        for j in range(1, m + 1):
            if p[j - 1] == '*':
                curr[j] = prev[j] or curr[j - 1]
            elif p[j - 1] == '?' or p[j - 1] == s[i - 1]:
                curr[j] = prev[j - 1]
            else:
                curr[j] = False
        prev = curr

    return prev[m]
```

*Space*: **O(m)** (≈ 2001 booleans ≈ 2 KB)  
*Time*: **O(n m)** (≈ 4 million operations – trivial for CPython).

---

#### 4.3  C++ – Bottom‑Up DP with 1‑D Arrays  

```cpp
// 44. Wildcard Matching – C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        vector<bool> prev(m + 1, false), curr(m + 1, false);
        prev[0] = true;                 // empty pattern matches empty string

        // Empty text, only leading '*' can match
        for (int j = 1; j <= m; ++j)
            if (p[j - 1] == '*') prev[j] = prev[j - 1];

        for (int i = 1; i <= n; ++i) {
            curr[0] = false;            // non‑empty text cannot match empty pattern
            for (int j = 1; j <= m; ++j) {
                if (p[j - 1] == '*')
                    curr[j] = prev[j] || curr[j - 1];
                else if (p[j - 1] == '?' || p[j - 1] == s[i - 1])
                    curr[j] = prev[j - 1];
                else
                    curr[j] = false;
            }
            prev.swap(curr);
        }
        return prev[m];
    }
};
```

*Space*: **O(m)**  
*Time*: **O(n m)** – again ~4 million ops, fast enough even in a tight interview.

---

### 4.4  Greedy Linear‑Time, Constant‑Space Solution

> **Why talk about greedy?**  
> In many interview panels, a *single‑pass* solution is a “wow” factor. The greedy algorithm is also the *canonical* interview answer for this problem.

```text
Walk through s and p once.
Keep two pointers:
    i – current index in s
    j – current index in p
Keep track of:
    starIndex – the most recent '*' in p (or -1 if none)
    iAfterStar – index in s when the last '*' was matched

If characters match or p[j] == '?', advance both.
If p[j] == '*', remember the positions and skip '*'.
If mismatch and we have a previous '*', backtrack:
    j = starIndex + 1  (reuse the '*')
    i = iAfterStar + 1  (consume one more char from s)
    iAfterStar = i
If mismatch and no '*', return false.
```

*Time*: **O(n + m)**  
*Space*: **O(1)**  
*Good for interviews*: you can explain the reasoning in 3‑5 minutes.

> **Caveat** – Greedy only works for this exact wildcard grammar (`?` and `*`). For other wildcards it may fail.

---

### 4.  Trade‑offs – Good, Bad, Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Recursive memoization is simplest | DP table can be huge | Greedy needs careful pointer juggling |
| **Performance** | Greedy is linear | DP uses O(n m) memory (≈ 4 MB) | Exponential recursion can stack‑overflow |
| **Edge‑cases** | All `*` in pattern → matches empty string | Need to handle empty strings separately | Off‑by‑one bugs are common in manual DP |
| **Interview perception** | DP shows understanding of string DP | Greedy demonstrates algorithmic creativity | Recursive can be seen as “lazy” and not optimal |

> **Key Takeaway** – *Always start with the simplest working solution (recursive), then “upgrade” it to a production‑ready DP. In interviews, the upgrade step (adding memoization or tabulation) demonstrates problem‑solving skills.*

---

### 5.  Interview‑Ready Checklist

1. **Clarify “entire match”** – pattern must cover *all* of `s`.
2. **Explain wildcard semantics** – write them out explicitly.
3. **Ask about time/space constraints** – confirm 2000×2000 fits in memory.
4. **Show the DP recurrence** – draw the `dp[i][j]` grid.
5. **Write code in the interview language** – use Java/Python/C++ as requested.
6. **Mention the greedy trick** – sometimes interviewers want you to think outside DP.
7. **Walk through a non‑trivial example** – e.g., `s="abbcd", p="*b?d"` to demonstrate state transitions.
8. **Ask clarifying questions** – e.g., “What about patterns with multiple consecutive `*`?” – to show deep understanding.
9. **Discuss edge cases** – empty strings, all `*`, or pattern starts with `*`.
10. **Time‑space complexity** – always state them and compare.

---

### 6.  Final Thoughts

Wildcard Matching is one of those “Hard” problems that **shifts from brute force to elegant DP**. Mastering it gives you:

- A solid understanding of **string DP** that applies to many LeetCode problems (e.g., “Regular Expression Matching”, “Decode Ways”).
- A ready‑to‑use **space‑optimized DP** template you can drop into any interview.
- The ability to pitch a **greedy solution** when you need to impress.

Add the reference implementations to your portfolio, write a quick blog post or a short video explaining the algorithm, and you'll be well‑positioned to score high on both technical screens and coding tests.

Good luck with your coding journey and the next interview!

---

## 7.  Bibliography & Further Reading

- **LeetCode Problem 44** – [Wildcard Matching](https://leetcode.com/problems/wildcard-matching/)
- **Cracking the Coding Interview – String DP** – Chapters on “Dynamic Programming” and “Regular Expressions”.
- **GeeksforGeeks – Wildcard Matching** – provides the greedy walkthrough.

---

**Meta‑note** – If you’re preparing a video or blog post, embed the code snippets with syntax highlighting. Recruiters love seeing a clean, well‑commented solution.  

Happy coding! 🚀

--- 

**SEO Keywords**: LeetCode 44 Wildcard Matching, Java DP solution, Python wildcard regex, C++ 1D array DP, greedy algorithm for wildcard, interview tips, string dynamic programming, time complexity, space optimization. 

--- 

*(End of reference guide – feel free to adapt or extend with unit tests or JUnit in Java.)*