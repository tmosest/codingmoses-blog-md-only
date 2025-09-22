---
title: LeetCode 44. Wildcard Matching - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯  Wildcard Matching – LeetCode 44  
*Hard | `public boolean isMatch(String s, String p)`*  

| Language | Approach | Time | Space |
|----------|----------|------|-------|
| Java     | DP (O(m·n) / O(n)) | 100 % accurate, worst‑case `O(m·n)` | `O(n)` |
| Python   | DP (O(m·n) / O(n)) | 100 % accurate, worst‑case `O(m·n)` | `O(n)` |
| C++      | DP (O(m·n) / O(n)) | 100 % accurate, worst‑case `O(m·n)` | `O(n)` |

> **Why this matters** – The “wildcard matching” problem is a staple of interview prep.  
> Solving it showcases your understanding of **dynamic programming**, **greedy tricks**, and **string‑matching nuances** that recruiters love.

---

## 1. Problem Recap

| Item | Description |
|------|-------------|
| **Input** | Two strings `s` (the text) and `p` (the pattern). `p` may contain: `?` (match any single char) and `*` (match any sequence, including empty). |
| **Goal** | Return `true` if the pattern covers the *entire* input string, `false` otherwise. |
| **Constraints** | `0 ≤ |s|, |p| ≤ 2000`; only lowercase letters, `?`, `*`. |
| **Examples** | `isMatch("aa","a") ➜ false`<br>`isMatch("aa","*") ➜ true`<br>`isMatch("cb","?a") ➜ false` |

---

## 2. Two Classic Solutions

### 2.1  Dynamic Programming (DP)

Think of a 2‑D table `dp[i][j]` – “does the first `i` characters of `s` match the first `j` characters of `p`?”  

Transition:

1. If `p[j-1]` is a letter or `?` → `dp[i][j] = dp[i-1][j-1] && matches(s[i-1], p[j-1])`
2. If `p[j-1]` is `*` → `dp[i][j] = dp[i][j-1] (star matches empty)` **or** `dp[i-1][j] (star consumes s[i-1])`

Because the table only needs the previous row, we can compress to a 1‑D array of length `|p|+1`.  

*Good*: Exact solution, works for any input size (within constraints).  
*Bad*: Needs `O(m·n)` time and space (though space can be reduced).  
*Ugly*: If you forget the base case for an empty pattern (all stars) you’ll get WA.

### 2.2  Greedy / Two‑Pointer Backtracking

Maintain two indices `i` (on `s`) and `j` (on `p`) and two “backup” indices `starIdx` and `iAfterStar`.  

Algorithm:

1. Iterate while `i < |s|`  
   * If `p[j]` is `?` or matches `s[i]`, advance both.  
   * If `p[j]` is `*`, record `starIdx = j` and `iAfterStar = i`, then advance `j`.  
   * If mismatch and a previous `*` exists, backtrack: set `j = starIdx + 1`, `i = iAfterStar + 1`, `iAfterStar++`.  
   * Else return `false`.  

After the loop, skip trailing `*` in the pattern.  

*Good*: Linear time `O(m+n)`, constant extra memory.  
*Bad*: Harder to reason about correctness; subtle bugs if you forget to handle trailing `*`.  
*Ugly*: If the pattern is heavily star‑dense (`"*a*a*a*"`) the algorithm still runs fast, but code readability suffers.

---

## 3. Code

> **Tip:** All implementations share the same helper `matches(c, pat)` – `c == pat || pat == '?'`.

### 3.1 Java

```java
public class WildcardMatcher {
    public boolean isMatch(String s, String p) {
        // DP solution – O(m*n) time, O(n) space
        int m = s.length(), n = p.length();
        boolean[] prev = new boolean[n + 1];
        prev[0] = true;                          // empty pattern matches empty string

        // initialize first row (empty string vs pattern)
        for (int j = 1; j <= n; j++) {
            prev[j] = prev[j - 1] && p.charAt(j - 1) == '*';
        }

        for (int i = 1; i <= m; i++) {
            boolean[] curr = new boolean[n + 1];
            for (int j = 1; j <= n; j++) {
                char pc = p.charAt(j - 1);
                if (pc == '*') {
                    curr[j] = curr[j - 1] || prev[j];
                } else if (pc == '?' || pc == s.charAt(i - 1)) {
                    curr[j] = prev[j - 1];
                }
            }
            prev = curr;
        }
        return prev[n];
    }
}
```

---

### 3.2 Python

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)
        prev = [False] * (n + 1)
        prev[0] = True

        # first row: empty string vs pattern
        for j in range(1, n + 1):
            prev[j] = prev[j - 1] and p[j - 1] == '*'

        for i in range(1, m + 1):
            curr = [False] * (n + 1)
            for j in range(1, n + 1):
                if p[j - 1] == '*':
                    curr[j] = curr[j - 1] or prev[j]
                elif p[j - 1] == '?' or p[j - 1] == s[i - 1]:
                    curr[j] = prev[j - 1]
            prev = curr

        return prev[n]
```

---

### 3.3 C++

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int m = s.size(), n = p.size();
        vector<bool> prev(n + 1, false);
        prev[0] = true;

        for (int j = 1; j <= n; ++j)
            prev[j] = prev[j - 1] && p[j - 1] == '*';

        for (int i = 1; i <= m; ++i) {
            vector<bool> curr(n + 1, false);
            for (int j = 1; j <= n; ++j) {
                if (p[j - 1] == '*')
                    curr[j] = curr[j - 1] || prev[j];
                else if (p[j - 1] == '?' || p[j - 1] == s[i - 1])
                    curr[j] = prev[j - 1];
            }
            prev.swap(curr);
        }
        return prev[n];
    }
};
```

---

## 4. Blog Article – “Wildcard Matching: The Good, The Bad, and The Ugly”

> **Keywords**: wildcard matching, LeetCode 44, interview, dynamic programming, greedy algorithm, string matching, job interview, software engineer

---

### 4.1 Introduction

If you’ve been scrolling through LeetCode or Glassdoor, you’ll know that **Wildcard Matching** is a *hard* problem that pops up on many interview stacks. It tests your ability to handle:

- **Pattern matching with special symbols** (`?`, `*`)
- **Dynamic programming** (state transition, space optimization)
- **Greedy reasoning** (two‑pointer backtracking)

Getting this right can earn you a “nice interview problem solved” badge on your résumé, and it’s a great talking point in a technical interview.

---

### 4.2 The Good – Why It’s a Worthwhile Problem

| Why it’s good | What it teaches |
|---------------|-----------------|
| **Real‑world patterns** | `*` and `?` are used in file globbing, SQL `LIKE`, and regex. |
| **DP foundation** | The 2‑D DP solution is a classic “match any sequence” problem – a building block for more complex algorithms. |
| **Space‑time trade‑offs** | Compressing the DP table shows how to reduce memory usage without changing correctness. |
| **Performance mindset** | Greedy/linear solution demonstrates how to analyze worst‑case complexity and optimize. |

---

### 4.3 The Bad – Common Pitfalls

| Pitfall | Why it matters | Fix |
|---------|----------------|-----|
| **Ignoring trailing `*`** | An empty pattern should match only if it contains only `*`. | Initialize DP row with `prev[j] = prev[j-1] && p[j-1]=='*'`. |
| **Over‑filling the DP matrix** | `O(m·n)` space may be too high for 2000 × 2000. | Use 1‑D array; keep only current and previous row. |
| **Backtracking bugs** | Forgetting to advance `iAfterStar` leads to infinite loops. | Update `iAfterStar` each time you backtrack. |
| **Misunderstanding `*` semantics** | Thinking `*` matches *exactly* one character. | Remember `*` can match 0, 1, 2, … characters. |

---

### 4.4 The Ugly – When It Breaks

1. **Excessive recursion**  
   A naïve recursive solution quickly explodes (`O(2^n)`), leading to stack overflow.  
   *Tip*: Memoize or convert to iterative DP.

2. **Complexity misestimation**  
   Interviewers may ask, “What if `s` and `p` are 2000 characters long?”  
   A linear greedy solution answers “`O(m+n)`” – a crisp, confident answer.

3. **Hard to explain**  
   Some candidates write code but can’t verbally articulate why it works.  
   Practice explaining the DP transition or the greedy backtrack in plain English.

---

### 4.5 The “Job‑Ready” Approach

1. **Start with the cleanest solution** – DP with space optimization.  
   *Why?* It’s easy to explain and covers edge cases.

2. **Mention the greedy alternative** – If asked, say “I can reduce time to linear if needed.”  
   Interviewers love candidates who know multiple angles.

3. **Talk about test coverage** – Include cases:
   - Empty strings
   - Only `*`
   - Pattern longer than string
   - Many consecutive `*`
   - Mixed `?` and `*`

4. **Show complexity** – `O(m·n)` time, `O(n)` space (DP) vs `O(m+n)` time, `O(1)` space (greedy).

5. **Wrap up** – “I chose the DP approach because it guarantees correctness and is easy to debug. The greedy method is great for large inputs when memory is tight.”

---

### 4.6 Quick Reference: Code Snippets

> Use the code blocks from the “Code” section above. Share them on GitHub with a neat README, and mention the problem number (`#44`).

---

### 4.7 Closing Thoughts

Wildcard matching is *not* just another LeetCode problem; it’s a micro‑cosm of software engineering:  

- You design a robust algorithm.  
- You document edge cases.  
- You optimize for space/time.  

Mastering it gives you a solid talking point for interviews and demonstrates that you can handle *pattern matching*, *dynamic programming*, and *greedy algorithms* – three of the most requested skills in senior software roles.

> **Want more interview prep?** Subscribe to our newsletter for weekly coding problems and interview‑ready explanations.

---

#### 🎯 Final Checklist Before Your Next Interview

- [ ] Understand `?` vs `*` semantics.  
- [ ] Can explain DP transition (`dp[i][j]`).  
- [ ] Know space‑optimised DP (1‑D array).  
- [ ] Understand greedy backtracking (`starIdx`, `iAfterStar`).  
- [ ] Be ready to discuss time/space complexity.  
- [ ] Have a few test cases on hand (empty, all stars, etc.).  

Good luck – you’ve got this!