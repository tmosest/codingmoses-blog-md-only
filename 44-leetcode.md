---
title: LeetCode 44. Wildcard Matching - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣  **Wildcard Matching – LeetCode 44**  
> 🔍 *Solve the “?’ and ‘*’ wildcard problem in Java, Python, and C++*.  
> 🚀 *Learn the best‑practice DP & greedy solutions, pitfalls, and how to ace this interview question.*

---

## 📄 Problem Recap

You’re given two strings  

|  | **Input** | **Allowed characters** | **Special tokens** |
|---|-----------|------------------------|--------------------|
| `s` | Text | `a‑z` | – |
| `p` | Pattern | `a‑z`, `?`, `*` | `?` → one arbitrary character, `*` → zero or more arbitrary characters |

**Goal:** Return `true` if `p` matches the **entire** string `s`.

> **Examples**  
> `s = "aa"`, `p = "a"` → `false`  
> `s = "aa"`, `p = "*" ` → `true`  
> `s = "cb"`, `p = "?a"` → `false`

Constraints: `0 ≤ len(s), len(p) ≤ 2000`

---

## 2️⃣  The Good, The Bad, The Ugly

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Algorithmic Approach** | *DP* – guaranteed O(mn), easy to reason about.<br>*Greedy* – O(m+n) with backtracking only when needed. | Pure recursion – exponential blow‑up, stack overflow. | Over‑engineering: custom state machines, regex engines, or excessive optimizations that hurt readability. |
| **Time Complexity** | ✅ DP: `O(m*n)` (≤ 4 million ops).<br>✅ Greedy: `O(m+n)` (linear). | ❌ Recursion: `O(2^(m+n))`. | ❌ Complex backtracking that may hit worst‑case exponential time. |
| **Space Complexity** | ✅ DP can be optimized to `O(min(m, n))` by rolling array.<br>✅ Greedy uses `O(1)`. | ❌ Recursion: `O(m+n)` stack depth + memo table. | ❌ Extra arrays for state tracking that don’t reduce asymptotic complexity. |
| **Edge Cases** | Handles empty `s`/`p`, leading/trailing `*`, consecutive `*`. | Recursion may crash on deep `*` chains. | Implementations that fail when `p` is only `*` or `s` is empty. |
| **Readability & Maintainability** | ✅ Clean loops, minimal branching. | ❌ Deep nested recursion makes debugging hard. | ❌ Mix of regex, DP, and greedy in one file – hard to test. |
| **Interview‑Friendly** | ✅ DP & greedy explanations are short, testable. | ❌ Recursion often gets penalized for inefficiency. | ❌ Over‑optimized code that looks like “magic” may raise eyebrows. |

---

## 3️⃣  The Optimal Solutions

Below are **three** self‑contained snippets – one each in Java, Python, and C++ – that implement:

1. **Bottom‑up DP** (2‑D table, then space‑optimized)
2. **Greedy + backtracking** (O(1) space, linear time)

Feel free to copy‑paste the language you need.

---

### 3.1 ✅ Java – Space‑Optimized DP

```java
public class WildcardMatching {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        // prev and cur store dp[i][*] and dp[i+1][*]
        boolean[] prev = new boolean[n + 1];
        boolean[] cur = new boolean[n + 1];

        // empty string matches pattern of only '*'s
        prev[0] = true;
        for (int j = 1; j <= n; j++) {
            prev[j] = prev[j - 1] && p.charAt(j - 1) == '*';
        }

        for (int i = 1; i <= m; i++) {
            cur[0] = false;                      // non‑empty string can't match empty pattern
            for (int j = 1; j <= n; j++) {
                char pc = p.charAt(j - 1);
                if (pc == '*') {
                    cur[j] = cur[j - 1] || prev[j]; // '*' matches zero (prev[j]) or one+ chars (cur[j-1])
                } else {
                    cur[j] = prev[j - 1] && (pc == '?' || pc == s.charAt(i - 1));
                }
            }
            // swap references for next row
            boolean[] tmp = prev;
            prev = cur;
            cur = tmp;
        }
        return prev[n];
    }
}
```

*Why it’s “good”*  
- Only two boolean arrays → **O(min(m, n))** space.  
- Clear logic, no recursion stack.  
- Handles all edge cases.

---

### 3.2 ✅ Python – Greedy + Backtracking

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        i = j = 0          # indices for s and p
        star = -1          # position of last '*'
        match = 0          # position in s when we hit '*'

        while i < len(s):
            # 1) direct match or '?' matches
            if j < len(p) and (p[j] == s[i] or p[j] == '?'):
                i += 1
                j += 1
            # 2) '*' encountered
            elif j < len(p) and p[j] == '*':
                star = j
                match = i
                j += 1
            # 3) mismatch but we have seen a '*'
            elif star != -1:
                j = star + 1
                match += 1
                i = match
            # 4) mismatch and no previous '*'
            else:
                return False

        # remaining pattern must be all '*'
        while j < len(p) and p[j] == '*':
            j += 1

        return j == len(p)
```

*Why it’s “good”*  
- Linear time **O(m + n)**.  
- Constant space **O(1)**.  
- Handles the worst‑case pattern `"*a*a*a*...a"` gracefully.

---

### 3.3 ✅ C++ – Bottom‑up DP (2‑D) – the “classic”

```cpp
class Solution {
public:
    bool isMatch(const string& s, const string& p) {
        int m = s.size(), n = p.size();
        vector<vector<bool>> dp(m + 1, vector<bool>(n + 1, false));

        dp[0][0] = true;
        for (int j = 1; j <= n; ++j)
            dp[0][j] = dp[0][j - 1] && p[j - 1] == '*';

        for (int i = 1; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (p[j - 1] == '*')
                    dp[i][j] = dp[i][j - 1] || dp[i - 1][j];
                else
                    dp[i][j] = dp[i - 1][j - 1] &&
                               (p[j - 1] == '?' || p[j - 1] == s[i - 1]);
            }
        }
        return dp[m][n];
    }
};
```

*Why it’s “good”*  
- Straightforward DP formulation – great for interview explanation.  
- No special tricks; every sub‑problem is explicitly computed.  
- Space can be reduced to `O(n)` if needed.

---

## 4️⃣  A Quick‑Start Test Harness

| Language | Run Command |
|----------|-------------|
| **Java** | `javac WildcardMatching.java && java WildcardMatching` |
| **Python** | `python3 - <<'PY'\nfrom solution import Solution\nprint(Solution().isMatch('aa','*'))\nPY` |
| **C++** | `g++ -std=c++17 -O2 -Wall -pedantic solution.cpp && ./a.out` |

Feel free to add more unit tests (e.g., `assert` statements or a `main()` function).

---

## 5️⃣  SEO‑Optimized Blog Article

> *Word‑count: ~1,200 words*  
> *Keywords:* wildcard matching, LeetCode 44, interview prep, dynamic programming, greedy algorithm, C++, Java, Python, job interview, algorithm design

---

### Title  
**Wildcard Matching – LeetCode 44: Master the DP & Greedy Solutions in Java, Python & C++**

### Meta Description  
Struggling with LeetCode 44 Wildcard Matching? Learn the optimal dynamic programming and greedy strategies, see clear Java, Python, and C++ implementations, and get interview‑ready. Perfect for aspiring software engineers seeking a tech job.

---

### Table of Contents

1. Problem Statement  
2. Why Wildcard Matching Matters in Interviews  
3. Brute‑Force vs. Efficient Strategies  
4. Dynamic Programming – The Classic Solution  
5. Greedy + Backtracking – The Linear‑Time Trick  
6. Space Optimization Tricks  
7. Edge Cases & Common Pitfalls  
8. Interview Tips & How to Explain Your Approach  
9. Full Code Snippets (Java, Python, C++)  
10. Final Thoughts & Resources

---

## 1️⃣ Problem Statement

> *Given a text `s` and a pattern `p` that may contain `?` and `*`, determine if `p` matches the entire text.*  
> *`?` matches any single character; `*` matches any sequence (including the empty sequence).*

### Why This Question Is Popular

- Appears in **Google, Meta, Amazon, Bloomberg, etc.**  
- Tests understanding of **dynamic programming**, **two‑pointer greedy**, and **edge‑case handling**.  
- Common on **coding interview platforms** (LeetCode, HackerRank, InterviewBit).

---

## 2️⃣ Why Wildcard Matching Matters in Interviews

- **Pattern‑matching** is a foundational concept in **text processing**, **file system globbing**, and **regular expressions**.  
- Mastering this problem shows you can:
  - Translate a real‑world requirement into a formal DP recurrence.
  - Balance time vs. space trade‑offs.
  - Handle ambiguous characters (`*` can represent many possibilities).
  - Keep the solution clean and testable.

---

## 3️⃣ Brute‑Force vs. Efficient Strategies

| Strategy | Complexity | Pros | Cons |
|----------|------------|------|------|
| **Recursive brute‑force** | `O(2^(m+n))` | Simple to code | Exponential time, stack overflow for large inputs |
| **DP (tabulation)** | `O(mn)` time, `O(mn)` space | Polynomial, guaranteed optimal | Uses a 2‑D array; can be optimized |
| **Greedy + backtracking** | `O(m+n)` time, `O(1)` space | Fast, minimal memory | Slightly trickier to prove correctness |

---

## 4️⃣ Dynamic Programming – The Classic Solution

### Recurrence

Let `dp[i][j]` be *true* if the first `i` characters of `s` match the first `j` characters of `p`.

```
dp[0][0] = true
dp[0][j] = dp[0][j-1] && p[j-1] == '*'
dp[i][0] = false   (i>0)

if p[j-1] == '*':
    dp[i][j] = dp[i][j-1] OR dp[i-1][j]
else:
    dp[i][j] = dp[i-1][j-1] AND (p[j-1]=='?' OR p[j-1]==s[i-1])
```

### Intuition

- `*` can consume **zero** characters (`dp[i][j-1]`) or **one/more** (`dp[i-1][j]`).
- Other characters must match exactly or via `?`.

### Space Optimisation

Only the previous row is required → use two 1‑D boolean arrays (`prev`, `cur`).  
This reduces memory to `O(min(m,n))`.

---

## 5️⃣ Greedy + Backtracking – The Linear‑Time Trick

### Two‑Pointer Idea

- Scan `s` and `p` simultaneously.
- Remember the **last `*`** position (`star`) and the **match index** (`match`).
- When encountering a mismatch:
  - If a previous `*` exists, backtrack to it and let `*` consume one more character.
  - Otherwise the pattern fails.

### Pseudocode (Python)

```
while i < len(s):
    if p[j] matches s[i] or '?':  move both
    elif p[j] == '*': remember star and move pattern pointer
    elif star seen: backtrack – extend '*' by one char
    else: mismatch – return False
```

### Why It Works

`*` can always *expand* to cover any unmatched character, so we can safely shift `i` back to the position after the last `*` and try again. The process guarantees each character of `s` is examined at most twice.

---

## 6️⃣ Space Optimization Tricks

1. **Swap two rows** – DP uses only `prev` and `cur`.  
2. **Use a 1‑D array** – compute `dp[j]` in place, updating from right to left for `*`.  
3. **Bitset** – for very large inputs, store booleans as bits to reduce constant factors.

---

## 7️⃣ Edge Cases & Common Pitfalls

| Case | What to Check |
|------|---------------|
| Empty string & pattern | `dp[0][0]` must be true. |
| Pattern starts/ends with `*` | Pre‑compute `dp[0][j]`. |
| Consecutive `*` | Treat as a single `*` (already handled by DP). |
| Pattern only `*` | Should match any string, even empty. |
| `s` longer than `p` but `p` contains many `*` | Greedy algorithm handles this by backtracking. |

---

## 8️⃣ Interview Tips & How to Explain Your Approach

1. **State the problem clearly.**  
   *“We need to match entire strings; `?` matches one character, `*` matches any sequence.”*

2. **Choose a solution early.**  
   *“I’ll use DP because it’s easier to reason about; I’ll also mention a linear greedy alternative.”*

3. **Draw the DP table (optional).**  
   - Show a 3×4 example.  
   - Highlight how `*` expands.

4. **Talk through the greedy logic.**  
   - “Remember the last `*`; if a mismatch occurs, we let that `*` consume one more character and try again.”

5. **Address time/space complexity upfront.**  
   - “DP: O(mn) time, O(mn) space; can be reduced to O(n) space. Greedy: O(m+n) time, O(1) space.”

6. **Show sample test cases on the board.**  
   - Empty strings, all `*`, alternating patterns.

7. **Mention real‑world analogy**  
   - “Think of `*` like a wildcard file glob; you can skip or consume characters as needed.”

---

## 9️⃣ Full Code Snippets

(Insert the three snippets from Section 3.1–3.3 here, each with a brief header.)

---

## 🔟 Final Thoughts & Resources

Wildcard Matching is a **gateway problem** – once you nail it, you’re ready for more advanced pattern‑matching questions (e.g., regex, backtracking with multiple wildcards, or NFA simulation).

**Recommended Resources**

- LeetCode: [Wildcard Matching – Problem 44](https://leetcode.com/problems/wildcard-matching/)
- Cracking the Coding Interview – DP chapter
- “Programming Pearls” – section on pattern matching
- YouTube: *Dynamic Programming in C++* – by **Gaurav Sen**

> *Ready to land that software engineering job? Focus on clean solutions, test thoroughly, and practice explaining your reasoning. Good luck!*

---

## 6️⃣ Final Thoughts

Wildcard Matching is more than a coding puzzle – it’s a test of **clarity**, **efficiency**, and **problem‑solving rigor**. By mastering the DP recurrence and the greedy trick, you’ll demonstrate both algorithmic depth and practical coding skill. Use the code snippets above to validate your understanding, add unit tests, and prepare for the interview questions that come next.

Happy coding, and best of luck landing your next tech role! 🚀

---


---

### End of Article

---


**Feel free to share this article with your peers or adapt it for a portfolio.** 

---


## 6️⃣  Bonus Resources

| Resource | Link |
|----------|------|
| LeetCode Discussion – DP solutions | https://leetcode.com/problems/wildcard-matching/discuss/ |
| Cracking the Coding Interview – DP section | https://www.amazon.com/Cracking-Coding-Interview-Programming-Excellence/dp/0984782850 |
| YouTube: DP vs. Greedy – LeetCode 44 | https://youtu.be/xyz123 |
| Medium: “Wildcard Matching in 12 Lines of Code” | https://medium.com/@gk/wildcard-matching |

Happy interviewing! 🎉

---


**End of Blog Article**

---

> *Tip:* When using the article as a portfolio piece, host it on a personal blog (GitHub Pages, Dev.to, Medium) and link it in your résumé under “Technical Blog”. This demonstrates both coding skill and communication ability – two key attributes recruiters look for.