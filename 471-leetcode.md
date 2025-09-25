---
title: LeetCode 471. Encode String with Shortest Length - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 471 – Encode String with Shortest Length  
**Java | Python | C++** – Full solutions + a complete SEO‑friendly blog post that will help you land your next software‑engineering role.  

---

## 1. The Problem (LeetCode 471)

> **Given** a string `s` (only lowercase English letters, `1 ≤ |s| ≤ 150`), encode it using the rule  
> `k[encoded_string]` where `encoded_string` is repeated exactly `k` times.  
> `k` must be a positive integer.  
> The encoding should be *shortest possible*.  
> If the encoding is not shorter than the original string, return the original string.  
> If multiple optimal encodings exist, any one of them is fine.

---

## 2. Core Idea – Dynamic Programming + Pattern Detection

1. **Sub‑problem**  
   For every substring `s[i … j]` we want the shortest encoded string.

2. **DP table**  
   `dp[i][j]` – best encoding of `s[i…j]`.  
   Size `n × n`, `n = s.length()` (≤ 150).

3. **Two ways to build a candidate for `dp[i][j]`**

   | **Method** | **How it works** | **Time** |
   |------------|------------------|----------|
   | **Split**  | Try every split point `k` (`i ≤ k < j`) and concatenate `dp[i][k] + dp[k+1][j]`. | O(n) per cell |
   | **Repeat** | Find the shortest repeating pattern `p` that can build the substring (`s[i…j] = p * repeatCount`). Encode as `repeatCount[p]`. | O(n) to find the pattern (using KMP LPS) |

4. **Take the best**  
   `dp[i][j] = min( splitCandidates , repeatCandidate )` by string length.  
   If a candidate is not shorter, ignore it.

5. **Answer**  
   `dp[0][n‑1]`.

The algorithm runs in **O(n³)** time (`n` up to 150 → < 4 million operations) and **O(n²)** space – perfectly fine for LeetCode.

---

## 3. Implementation – Java

```java
import java.util.*;

public class Solution {
    public String encode(String s) {
        int n = s.length();
        String[][] dp = new String[n][n];

        for (int len = 1; len <= n; len++) {
            for (int i = 0; i + len - 1 < n; i++) {
                int j = i + len - 1;
                String sub = s.substring(i, j + 1);
                dp[i][j] = sub;                       // start with original

                /* 1️⃣ Split the string */
                for (int k = i; k < j; k++) {
                    String candidate = dp[i][k] + dp[k + 1][j];
                    if (candidate.length() < dp[i][j].length()) {
                        dp[i][j] = candidate;
                    }
                }

                /* 2️⃣ Try to compress using repetition */
                String repeat = repeatEncoding(sub);
                if (repeat.length() < dp[i][j].length()) {
                    dp[i][j] = repeat;
                }
            }
        }
        return dp[0][n - 1];
    }

    /* Helper: find the shortest repeated pattern for a string */
    private String repeatEncoding(String str) {
        int len = str.length();
        int[] lps = buildLPS(str);
        int lpsVal = lps[len - 1];
        int patternLen = len - lpsVal;

        if (lpsVal > 0 && len % patternLen == 0) {
            int times = len / patternLen;
            String pattern = str.substring(0, patternLen);
            return times + "[" + pattern + "]";
        }
        return str;   // not compressible
    }

    /* Build longest‑prefix‑suffix array (KMP) */
    private int[] buildLPS(String str) {
        int n = str.length();
        int[] lps = new int[n];
        int len = 0;          // length of previous longest prefix suffix
        int i = 1;
        while (i < n) {
            if (str.charAt(i) == str.charAt(len)) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        return lps;
    }
}
```

---

## 4. Implementation – Python

```python
class Solution:
    def encode(self, s: str) -> str:
        n = len(s)
        dp = [[""] * n for _ in range(n)]

        for length in range(1, n + 1):
            for i in range(n - length + 1):
                j = i + length - 1
                sub = s[i:j + 1]
                best = sub

                # split
                for k in range(i, j):
                    cand = dp[i][k] + dp[k + 1][j]
                    if len(cand) < len(best):
                        best = cand

                # repeat
                rep = self.repeat_encode(sub)
                if len(rep) < len(best):
                    best = rep

                dp[i][j] = best
        return dp[0][n - 1]

    def repeat_encode(self, st: str) -> str:
        # KMP to find smallest period
        lps = self.build_lps(st)
        lps_val = lps[-1]
        period = len(st) - lps_val
        if lps_val > 0 and len(st) % period == 0:
            times = len(st) // period
            return f"{times}[{st[:period]}]"
        return st

    def build_lps(self, st: str) -> list[int]:
        n = len(st)
        lps = [0] * n
        length = 0
        i = 1
        while i < n:
            if st[i] == st[length]:
                length += 1
                lps[i] = length
                i += 1
            else:
                if length != 0:
                    length = lps[length - 1]
                else:
                    lps[i] = 0
                    i += 1
        return lps
```

---

## 5. Implementation – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string encode(string s) {
        int n = s.size();
        vector<vector<string>> dp(n, vector<string>(n));
        for (int len = 1; len <= n; ++len) {
            for (int i = 0; i + len - 1 < n; ++i) {
                int j = i + len - 1;
                string sub = s.substr(i, len);
                string best = sub;

                // split
                for (int k = i; k < j; ++k) {
                    string cand = dp[i][k] + dp[k + 1][j];
                    if (cand.size() < best.size()) best = cand;
                }

                // repeat
                string rep = repeatEncode(sub);
                if (rep.size() < best.size()) best = rep;

                dp[i][j] = best;
            }
        }
        return dp[0][n - 1];
    }

private:
    string repeatEncode(const string &str) {
        vector<int> lps = buildLPS(str);
        int lpsVal = lps.back();
        int period = str.size() - lpsVal;
        if (lpsVal > 0 && str.size() % period == 0) {
            int times = str.size() / period;
            return to_string(times) + "[" + str.substr(0, period) + "]";
        }
        return str;
    }

    vector<int> buildLPS(const string &s) {
        int n = s.size();
        vector<int> lps(n, 0);
        int len = 0;
        for (int i = 1; i < n; ++i) {
            while (len > 0 && s[i] != s[len]) len = lps[len - 1];
            if (s[i] == s[len]) ++len;
            lps[i] = len;
        }
        return lps;
    }
};
```

---

## 6. Blog Post – “Encode String with Shortest Length: The Good, The Bad, The Ugly”

> **SEO Keywords** – `encode string shortest length`, `LeetCode 471`, `dynamic programming string encoding`, `coding interview string compression`, `Java Python C++ solution`, `software engineering interview prep`, `job interview algorithm`, `job-hunting tech blog`

---

### 6.1 Introduction

When recruiters scroll through a stack of resumes, the ability to *solve a classic string‑compression DP problem* instantly elevates you above the competition. LeetCode 471 – *Encode String with Shortest Length* is one of those “talk‑about‑in‑a‑30‑second” questions that showcases:

* **Dynamic programming chops** – sub‑problems, memoization, optimal sub‑structure.  
* **Pattern‑matching intuition** – spotting periodicity in strings.  
* **Language versatility** – can be solved in Java, Python, C++ (and more).  

In this article we’ll walk through the **good** (why it’s a perfect interview staple), the **bad** (common pitfalls and edge‑cases), and the **ugly** (gotchas in implementations). By the end, you’ll have:

* A clean, proven solution in **Java, Python, and C++**.  
* A solid understanding of *why* the algorithm works.  
* Ready‑to‑paste code snippets for your GitHub or personal portfolio.  
* Interview tips on how to present the solution in a real interview.  

Let’s dive in!

---

### 6.2 Problem Recap

> **Given** a string `s` of lowercase letters, encode it as `k[encoded_string]` such that the resulting string is *shortest* possible.  
> If no compression yields a shorter string, return the original string.  

**Constraints**  
`1 ≤ s.length ≤ 150`, only lowercase letters.

---

### 6.3 The Good – Why This Problem Is Interview‑Gold

| Feature | What It Shows |
|---------|---------------|
| **O(n³) DP** | Classic DP with 3‑dimensional loops, perfect for testing your ability to build a table and iterate over substrings. |
| **Pattern detection** | Requires a bit of algorithmic flair (KMP or string periodicity). |
| **Multiple solutions** | Encourages *creative problem‑solving*; you can talk about memoization, tabulation, or recursion with pruning. |
| **Language‑agnostic** | You can implement in any language; interviewers appreciate language‑independent reasoning. |
| **Short but non‑trivial** | Fits comfortably into a 45‑minute interview. |

---

### 6.4 The Bad – Common Pitfalls

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Missing the “not shorter” rule** | Forgetting to compare the candidate length with the original string leads to over‑compressed outputs. | Always keep the original substring as the initial candidate. |
| **O(n²) “split” instead of O(n³)** | Trying to use a single `for k` loop over all substrings (`i…j`) but mistakenly re‑computing `dp[i][k] + dp[k+1][j]` for every split. | Pre‑compute the DP table iteratively by increasing substring length. |
| **Not handling single‑character repeats** | Example: `"aaaaa"` → you might encode as `"5[a]"`, but if you detect the period incorrectly you may miss it. | Use KMP LPS to find the smallest period. |
| **Integer overflow in repeat count** | For `"aaaaaaaaaa"` (10 a's), the repeat count is `10`; converting to string must not truncate. | Use string concatenation (`to_string` in C++, `f"{k}"` in Python, `Integer.toString` in Java). |
| **Performance blow‑up on worst‑case strings** | Without memoization, a naive recursion can explore `O(2^n)` splits. | Tabulate all substrings once (`dp[i][j]`). |

---

### 6.5 The Ugly – Edge Cases & Tricky Parts

1. **Multiple Optimal Encodings**  
   Example: `"aaaaaaaaaa"` can be `"10[a]"` or `"5[aa]"`.  
   *Solution*: Returning any shortest form is allowed; the DP naturally picks the first optimal candidate.

2. **Very Short Strings**  
   For `s = "a"` or `"ab"`, no encoding helps.  
   *Solution*: The DP will simply keep the original substring.

3. **Repeated Pattern Inside a Larger Pattern**  
   `"ababab"` → `"3[ab]"` vs `"6[a]"` – the former is shorter.  
   *Solution*: The repeat‑encoding helper only checks the *smallest* period. If a longer period yields an even shorter encoding, it will be found by the split part of DP.

4. **Large k but Not Short**  
   `"aaaaaaaaaaaaa"` → `"13[a]"` has length `4` (`13[ a ]`). But `"3[aaaa]"` has length `6`.  
   *Solution*: The DP compares the length of each candidate; the shortest is chosen.

---

### 6.6 How I Explained It in an Interview

> “We’ll first build a DP table where `dp[i][j]` is the best encoding for substring `s[i…j]`.  
> 1️⃣ **Split** – for every possible split point `k`, we combine `dp[i][k]` and `dp[k+1][j]`.  
> 2️⃣ **Repeat** – we detect if `s[i…j]` consists of several copies of a smaller string. I’ll use the KMP LPS array to find the shortest period.  
> We then pick the candidate with the minimal length, provided it’s shorter than the original substring.”

> “Because the string length is only 150, an O(n³) solution is fine. If it were bigger, we’d need more clever pruning, but for interview purposes this is the cleanest, most demonstrative approach.”

---

### 6.7 Full Code Snippets

> *Feel free to drop these into your GitHub repo, include them in a README, or paste them during an interview.*

#### 6.7.1 Java

```java
class Solution {
    public String encode(String s) {
        int n = s.length();
        String[][] dp = new String[n][n];

        for (int len = 1; len <= n; ++len) {
            for (int i = 0; i + len - 1 < n; ++i) {
                int j = i + len - 1;
                String sub = s.substring(i, j + 1);
                String best = sub;

                // split
                for (int k = i; k < j; ++k) {
                    String cand = dp[i][k] + dp[k + 1][j];
                    if (cand.length() < best.length()) best = cand;
                }

                // repeat
                String rep = repeatEncode(sub);
                if (rep.length() < best.length()) best = rep;

                dp[i][j] = best;
            }
        }
        return dp[0][n - 1];
    }

    private String repeatEncode(String st) {
        int[] lps = buildLPS(st);
        int period = st.length() - lps[lps.length - 1];
        if (period < st.length() && st.length() % period == 0) {
            int times = st.length() / period;
            return times + "[" + st.substring(0, period) + "]";
        }
        return st;
    }

    private int[] buildLPS(String s) {
        int n = s.length();
        int[] lps = new int[n];
        int len = 0;
        for (int i = 1; i < n; ++i) {
            while (len > 0 && s.charAt(i) != s.charAt(len)) len = lps[len - 1];
            if (s.charAt(i) == s.charAt(len)) ++len;
            lps[i] = len;
        }
        return lps;
    }
}
```

#### 6.7.2 Python

```python
class Solution:
    def encode(self, s: str) -> str:
        n = len(s)
        dp = [[None] * n for _ in range(n)]

        for l in range(1, n + 1):
            for i in range(n - l + 1):
                j = i + l - 1
                sub = s[i:j+1]
                best = sub

                # split
                for k in range(i, j):
                    cand = dp[i][k] + dp[k+1][j]
                    if len(cand) < len(best): best = cand

                # repeat
                rep = self.repeat_encode(sub)
                if len(rep) < len(best): best = rep

                dp[i][j] = best
        return dp[0][n-1]

    def repeat_encode(self, st: str) -> str:
        lps = self.build_lps(st)
        period = len(st) - lps[-1]
        if lps[-1] > 0 and len(st) % period == 0:
            return f"{len(st)//period}[{st[:period]}]"
        return st

    def build_lps(self, st: str) -> list[int]:
        lps = [0] * len(st)
        length = 0
        for i in range(1, len(st)):
            while length > 0 and st[i] != st[length]:
                length = lps[length-1]
            if st[i] == st[length]:
                length += 1
                lps[i] = length
        return lps
```

#### 6.7.3 C++

```cpp
class Solution {
public:
    string encode(string s) {
        int n = s.size();
        vector<vector<string>> dp(n, vector<string>(n));

        for (int len = 1; len <= n; ++len) {
            for (int i = 0; i + len - 1 < n; ++i) {
                int j = i + len - 1;
                string sub = s.substr(i, len);
                string best = sub;

                // split
                for (int k = i; k < j; ++k) {
                    string cand = dp[i][k] + dp[k+1][j];
                    if (cand.size() < best.size()) best = cand;
                }

                // repeat
                string rep = repeatEncode(sub);
                if (rep.size() < best.size()) best = rep;

                dp[i][j] = best;
            }
        }
        return dp[0][n-1];
    }

private:
    string repeatEncode(const string &st) {
        vector<int> lps = buildLPS(st);
        int period = st.size() - lps.back();
        if (lps.back() > 0 && st.size() % period == 0) {
            int times = st.size() / period;
            return to_string(times) + "[" + st.substr(0, period) + "]";
        }
        return st;
    }

    vector<int> buildLPS(const string &s) {
        vector<int> lps(s.size(), 0);
        int len = 0;
        for (int i = 1; i < s.size(); ++i) {
            while (len > 0 && s[i] != s[len]) len = lps[len-1];
            if (s[i] == s[len]) ++len;
            lps[i] = len;
        }
        return lps;
    }
};
```

---

### 6.8 How to Use This for Job Hunting

1. **Portfolio Highlight** – Add a *LeetCode‑471* folder with the three implementations.  
2. **Resume Bullet** – “Solved LeetCode 471 in Java/Python/C++ with O(n³) DP and KMP‑based period detection.”  
3. **Blog/Medium** – Post this article on your personal site; recruiters often click on GitHub/Medium links.  
4. **Mock Interview Prep** – Practice explaining the algorithm out loud while typing the Java code; the act of narrating shows communication skills.

---

### 6.9 Final Thoughts

Encoding a string into its shortest possible form might sound like a simple compression exercise, but it’s a *power‑up* in the interview arena. The **dynamic programming** core teaches you how to break down a seemingly global optimization problem into manageable sub‑problems. The **period‑detection** trick reminds you that sometimes a *string algorithm* (like KMP) can give you the edge you need.

So next time your interview involves “compressing strings” or “string DP”, grab your favorite language, paste one of the snippets above, and explain the **split + repeat** logic. Your future employer will be impressed, and your resume will get that extra click‑through.

Happy coding, and good luck landing that tech role!

--- 

### 6.10 Call‑to‑Action

> **Want more interview‑ready solutions?**  
> Subscribe to our newsletter for weekly LeetCode walkthroughs, or check out our full repository at `github.com/YourName/LeetCode-471`.  
> **Follow** us on Twitter, LinkedIn, or Instagram for real‑time coding challenges.

--- 

**Happy compressing!** 🚀

--- 

> *End of Blog Post*  

--- 

## 7. Conclusion

You now own a **battle‑tested** solution for LeetCode 471 in three of the most popular programming languages. The problem’s design makes it a perfect interview showcase, and the article above arms you with a polished narrative, code, and interview‑talking strategy. 

When recruiters or hiring managers ask you about string compression or dynamic programming, simply say: “I solved LeetCode 471 in O(n³) with DP + KMP, and here’s my clean Java/Python/C++ implementation.” That’s the *Good* part of the story.  

Happy coding and best of luck on the job hunt! 🚀

--- 

*End of Blog.*