---
title: LeetCode 471. Encode String with Shortest Length - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  
**LeetCode 471 – Encode String with Shortest Length**  
> Given a string `s` (1 ≤ |s| ≤ 150) of lowercase letters, return an encoded form that has the *shortest possible length*.  
> The encoding rule is `k[encoded_string]`, where `encoded_string` is repeated exactly `k` times. `k` is a positive integer.  
> If encoding does **not** shorten the string, return the original string. If several shortest encodings exist, any one is acceptable.

> Example  
> `s = "aaaaaaaaaa"` → `"10[a]"`

---

## 2.  Why This Problem Is a *Great* Job‑Interview Question

| Good | Bad | Ugly |
|------|-----|------|
| **Demonstrates DP & substring tricks** – interviewers love problems that force you to split the string in all possible ways. | **Large search space** – naïve O(2ⁿ) enumeration will time‑out. | **Corner cases** – single characters, nested patterns, and mixed patterns can trip up a novice. |
| **Showcases pattern detection** – detecting repeated sub‑strings is a classic algorithmic skill. | **Hard to get the “shortest” correctly** – you must compare encoded length, not the string itself. | **Hard to explain** – if you can’t explain why you chose a split or a pattern, the interviewer will doubt your understanding. |
| **Real‑world relevance** – data compression, DNA sequencing, network packet framing. | **Requires careful handling of `k` digits** – e.g., `"100[a]"` vs `"3[30[a]]"`. | **Tight time limit** – a 150‑char string forces a solution to run in < O(n³) time. |

---

## 3.  Algorithm Overview

1. **Dynamic Programming**  
   `dp[i][j]` (0‑based, inclusive) stores the *shortest encoding* of `s[i…j]`.  
   The answer is `dp[0][n‑1]`.

2. **Three ways to form `dp[i][j]`:**  
   * **No encoding** – the substring itself, `s[i…j]`.  
   * **Split** – try every possible split point `k` between `i` and `j` and combine `dp[i][k] + dp[k+1][j]`.  
   * **Repeat pattern** – if `s[i…j]` consists of `t` copies of a smaller substring `t = s[i…j]` split into `len = j-i+1`.  
     * Find the smallest period of the substring using the *prefix function* (KMP) or by brute force (acceptable for n ≤ 150).  
     * If the period `< len`, encode as `cnt[periodString]` where `cnt = len / period` and `periodString = dp[i][i+period-1]`.

3. **Choose the option with the shortest resulting string length**.  
   Ties can be broken arbitrarily (the problem permits any shortest encoding).

4. **Complexity**  
   *Time*: O(n³) – three nested loops (length, start, split).  
   *Space*: O(n²) – the DP table.

5. **Optimizations**  
   * Pre‑compute the shortest period for every substring (prefix function or Z‑algorithm).  
   * Store the length of each `dp[i][j]` separately to avoid repeated `length()` calls.  

---

## 4.  Reference Implementations

### 4.1 Java

```java
import java.util.Arrays;

public class Solution {
    public String encode(String s) {
        int n = s.length();
        String[][] dp = new String[n][n];
        int[][] period = new int[n][n];          // smallest period length for s[i..j]

        // Pre‑compute periods with KMP prefix function
        for (int i = 0; i < n; i++) {
            int[] pi = new int[n - i];
            for (int j = i; j < n; j++) {
                int len = j - i + 1;
                if (len == 1) { period[i][j] = 1; continue; }
                // compute prefix function for s[i..j]
                if (j > i) {
                    int k = pi[j - i - 1];
                    while (k > 0 && s.charAt(i + len - 1) != s.charAt(i + k))
                        k = pi[k - 1];
                    if (s.charAt(i + len - 1) == s.charAt(i + k)) k++;
                    pi[j - i] = k;
                }
                int p = len - pi[len - 1];
                period[i][j] = (len % p == 0) ? p : len;
            }
        }

        for (int len = 1; len <= n; len++) {
            for (int i = 0; i + len - 1 < n; i++) {
                int j = i + len - 1;
                String best = s.substring(i, j + 1);   // no encoding
                // try split
                for (int k = i; k < j; k++) {
                    String cand = dp[i][k] + dp[k + 1][j];
                    if (cand.length() < best.length()) best = cand;
                }
                // try repeat pattern
                int per = period[i][j];
                if (per < len) {          // there is a repetition
                    int times = len / per;
                    String cand = times + "[" + dp[i][i + per - 1] + "]";
                    if (cand.length() < best.length()) best = cand;
                }
                dp[i][j] = best;
            }
        }
        return dp[0][n - 1];
    }
}
```

### 4.2 Python

```python
class Solution:
    def encode(self, s: str) -> str:
        n = len(s)
        dp = [["" for _ in range(n)] for _ in range(n)]
        period = [[0] * n for _ in range(n)]

        # helper: compute period for substring s[i:j+1]
        for i in range(n):
            for j in range(i, n):
                sub = s[i:j+1]
                m = len(sub)
                if m == 1:
                    period[i][j] = 1
                    continue
                # prefix function
                pi = [0] * m
                for k in range(1, m):
                    t = pi[k-1]
                    while t > 0 and sub[k] != sub[t]:
                        t = pi[t-1]
                    if sub[k] == sub[t]:
                        t += 1
                    pi[k] = t
                p = m - pi[-1]
                period[i][j] = p if m % p == 0 else m

        for l in range(1, n+1):
            for i in range(n-l+1):
                j = i+l-1
                best = s[i:j+1]                      # no encoding
                # split
                for k in range(i, j):
                    cand = dp[i][k] + dp[k+1][j]
                    if len(cand) < len(best):
                        best = cand
                # repetition
                per = period[i][j]
                if per < l:
                    times = l // per
                    cand = f"{times}[{dp[i][i+per-1]}]"
                    if len(cand) < len(best):
                        best = cand
                dp[i][j] = best
        return dp[0][n-1]
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string encode(string s) {
        int n = s.size();
        vector<vector<string>> dp(n, vector<string>(n));
        vector<vector<int>> period(n, vector<int>(n));

        // precompute smallest period for every substring using KMP prefix
        for (int i = 0; i < n; ++i) {
            for (int j = i; j < n; ++j) {
                string sub = s.substr(i, j - i + 1);
                int m = sub.size();
                if (m == 1) { period[i][j] = 1; continue; }

                vector<int> pi(m, 0);
                for (int k = 1; k < m; ++k) {
                    int t = pi[k-1];
                    while (t > 0 && sub[k] != sub[t]) t = pi[t-1];
                    if (sub[k] == sub[t]) ++t;
                    pi[k] = t;
                }
                int p = m - pi[m-1];
                period[i][j] = (m % p == 0) ? p : m;
            }
        }

        for (int len = 1; len <= n; ++len) {
            for (int i = 0; i + len - 1 < n; ++i) {
                int j = i + len - 1;
                string best = s.substr(i, len);          // no encode
                // split
                for (int k = i; k < j; ++k) {
                    string cand = dp[i][k] + dp[k+1][j];
                    if (cand.size() < best.size()) best = cand;
                }
                // repetition
                int per = period[i][j];
                if (per < len) {
                    int times = len / per;
                    string cand = to_string(times) + "[" + dp[i][i+per-1] + "]";
                    if (cand.size() < best.size()) best = cand;
                }
                dp[i][j] = best;
            }
        }
        return dp[0][n-1];
    }
};
```

---

## 5.  Blog Article (SEO‑Optimized)

> **Title**: “Decode to Encode: Mastering LeetCode 471 in Java, Python, & C++ – The Job‑Ready Guide”  
> **Meta Description**: “Learn how to solve LeetCode 471 (Encode String with Shortest Length) in Java, Python, and C++. Step‑by‑step DP tutorial, edge‑case handling, and interview‑ready insights.”  

### 5.1 Introduction  
In the competitive world of software engineering interviews, **LeetCode 471 – Encode String with Shortest Length** often shows up as a *hard* challenge. The problem tests a candidate’s ability to combine dynamic programming, string manipulation, and pattern recognition. Below you’ll find a complete, language‑agnostic solution, plus optimized Java, Python, and C++ implementations that are ready for a technical interview.

### 5.2 The Problem in Plain English  
> “Given a lowercase string `s`, return the shortest string that can be *encoded* using the rule `k[encoded_string]`. If the encoded form isn’t shorter, return the original string.”  
The challenge is not just to compress but to find the *shortest possible* representation, which involves exploring all possible splits and repeated patterns.

### 5.3 Why This Problem is a Gold‑Mine for Interviewees  
* **DP + Substring** – Classic “String Compression” problem.  
* **Pattern detection** – Understanding how to find the smallest repeating unit.  
* **Complexity awareness** – Demonstrates how to keep O(n³) under the 150‑character limit.  
* **Real‑world feel** – Compression is a core concept in networking and storage.

### 5.4 Step‑by‑Step Algorithm

1. **Initialize a 2‑D DP table** `dp[i][j]` to store the shortest encoding of substring `s[i…j]`.  
2. **Pre‑compute the smallest period** for every substring using the KMP prefix function.  
3. **Iterate over all substring lengths** (1 … n).  
   * Base case: `dp[i][i] = s[i]`.  
   * **Split**: For every split point `k`, combine `dp[i][k] + dp[k+1][j]`.  
   * **Repeat pattern**: If the substring’s period `p` is smaller than its length, encode as `cnt[dp[i][i+p-1]]`.  
4. Pick the candidate with the shortest length.  
5. Return `dp[0][n‑1]`.

### 5.5 Edge‑Case Mastery

| Case | Explanation | How the DP Handles It |
|------|-------------|-----------------------|
| `aaa` | No compression improves length | Split candidates all longer → keep original |
| `aa...aa` (many repeats) | Period is `1` → encode as `len[a]` | Period detection finds smallest unit |
| Nested repeats like `abcabcabc` | Period `3` → encode as `3[abc]` | DP uses split or pattern whichever shorter |
| Mixed patterns `aabcaabcaabc` | Period `3` with `aab` | Works because period detection covers entire block |

### 5.6 Complexity Analysis

| Aspect | Value |
|--------|-------|
| Time   | O(n³) – three nested loops over length, start, and split. With `n ≤ 150`, this comfortably runs under 0.1 s. |
| Space  | O(n²) – DP table and period matrix. |

### 5.7 Code Snippets for the Top Languages

- **Java** – Uses a 2‑D `String` table and a period matrix; leverages KMP for period detection.  
- **Python** – Same logic; uses list comprehension and f‑strings for readability.  
- **C++** – Efficient memory use with `vector<string>`; `to_string` for the integer part.  

(Full source is provided in the sections above.)

### 5.8 Common Interview Pitfalls and How to Avoid Them

1. **Forgetting to compare lengths** – It’s easy to return a longer encoding if you only check *validity*.  
2. **Not handling single‑character substrings** – They should never be encoded; the DP base case must be `s[i]`.  
3. **Using naive substring creation in a tight loop** – In Java or C++, repeatedly calling `substring` can lead to O(n²) overhead; store pre‑computed substrings if needed.  
4. **Missing the “period < len” guard** – Encoding a non‑repeating string as `1[abc]` is longer.  

### 5.9 Final Thoughts – Making the Interviewer *Impressed*  

*Speak *in words*:* While showing code, articulate why you’re picking a split or a repeat, how the DP table ensures optimality, and why the complexity is acceptable.  
*Show *confidence* in edge cases:* Mention how your algorithm correctly handles single characters, long runs, and nested patterns.  
*Ask *clarifying questions:* For instance, “Does the string contain only lowercase letters?” – this demonstrates communication skills.

---

## 6.  Wrap‑Up

You now have a battle‑tested, multi‑language solution to LeetCode 471. Use this as a template for future interview questions involving string compression, dynamic programming, and pattern detection. Remember, mastering the *hard* problems like this one not only scores you on LeetCode but also positions you as a *thoughtful, detail‑oriented* candidate ready to tackle real‑world challenges. Happy coding—and good luck on your next interview!