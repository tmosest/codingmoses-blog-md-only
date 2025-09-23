---
title: LeetCode 471. Encode String with Shortest Length - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Encode String with Shortest Length – 471  
**Solution in Java, Python & C++ + a SEO‑friendly blog post**

> **Keywords** – encode string, shortest length, DP, LeetCode 471, interview question, coding interview, best practices, time complexity, space complexity, dynamic programming, string manipulation, job interview preparation

---

## 1. Problem Recap

```text
Given a string s (1 ≤ s.length ≤ 150) consisting only of lowercase English letters,
return an encoded string of the *shortest possible length* using the rule:
k[encoded_string]   (k ≥ 1)

If an encoding does not reduce the length, keep the original substring.
If multiple encodings have the same minimal length, any one may be returned.
```

Examples  
| Input | Output | Reason |
|-------|--------|--------|
| `"aaa"` | `"aaa"` | No shorter encoding |
| `"aaaaa"` | `"5[a]"` | 5[a] (4 chars) < 5 chars |
| `"aaaaaaaaaa"` | `"10[a]"` | 10[a] (5 chars) ≤ 10 chars |

---

## 2. Intuition

The string is at most **150** characters long – a classic length for a **dynamic programming** (DP) solution.  
The goal is to explore *all* possible partitions of the string and keep the best one.

For any substring `s[i … j]`:

1. **Split** it into two parts `s[i … k]` + `s[k+1 … j]`.  
   The encoded form is `dp[i][k] + dp[k+1][j]`.

2. **Repeat** – if the whole substring can be expressed as `k` copies of a *smaller* substring `t`.  
   Encode as `k[dp[t]]` (where `dp[t]` is the best encoding of `t`).

The minimal length is the smallest among all candidates.  
DP gives us the optimal sub‑solutions for the smaller substrings.

---

## 3. Detecting a Repeat Pattern

A key sub‑problem: *“Is `s[i … j]` a repetition of some substring `t`?”*  
A convenient way:

1. Let `len = j - i + 1`.  
2. Build a **failure function** (KMP prefix array) for `s[i … j]`.  
3. Let `p = len - failure[len-1]`.  
   If `len % p == 0` and `p < len`, the string repeats `len / p` times with the pattern `s[i … i+p-1]`.

The prefix array is O(`len`); overall DP runs in O(n³) time but is easily fast enough for n ≤ 150.

---

## 4. DP Recurrence

```
dp[i][j]  – best encoded string for s[i … j]

For all i <= j:
    best = s[i … j]                         // the original substring
    for k from i to j-1:                   // split
        cand = dp[i][k] + dp[k+1][j]
        if cand.length < best.length: best = cand

    // check repetition
    if s[i … j] can be written as t repeated m times:
        m = (j-i+1) / len(t)
        cand = m + "[" + dp[i][i+len(t)-1] + "]"
        if cand.length < best.length: best = cand

    dp[i][j] = best
```

Complexities:  
- **Time** – O(n³) (n = 150 → ~3.4 million operations)  
- **Space** – O(n²) for DP table + O(n) for the prefix array

---

## 5. Code Implementations

### 5.1 Java (LeetCode‑ready)

```java
import java.util.*;

public class Solution {
    public String encode(String s) {
        int n = s.length();
        String[][] dp = new String[n][n];
        // length 1 substrings are already minimal
        for (int i = 0; i < n; i++) dp[i][i] = s.substring(i, i+1);

        for (int len = 2; len <= n; len++) {
            for (int i = 0; i + len - 1 < n; i++) {
                int j = i + len - 1;
                String best = s.substring(i, j+1);          // original
                // split
                for (int k = i; k < j; k++) {
                    String cand = dp[i][k] + dp[k+1][j];
                    if (cand.length() < best.length()) best = cand;
                }
                // repeat check
                int repeat = getRepeatCount(s, i, j);
                if (repeat > 1) {
                    int partLen = len / repeat;
                    String part = dp[i][i + partLen - 1];
                    String cand = repeat + "[" + part + "]";
                    if (cand.length() < best.length()) best = cand;
                }
                dp[i][j] = best;
            }
        }
        return dp[0][n-1];
    }

    // KMP prefix to find smallest period
    private int getRepeatCount(String s, int l, int r) {
        int len = r - l + 1;
        int[] pi = new int[len];
        for (int i = 1; i < len; i++) {
            int j = pi[i-1];
            while (j > 0 && s.charAt(l+i) != s.charAt(l+j)) j = pi[j-1];
            if (s.charAt(l+i) == s.charAt(l+j)) j++;
            pi[i] = j;
        }
        int period = len - pi[len-1];
        if (period < len && len % period == 0) return len / period;
        return 1;   // no repetition
    }
}
```

---

### 5.2 Python (3.10+)

```python
from functools import lru_cache
from typing import List

class Solution:
    def encode(self, s: str) -> str:
        n = len(s)
        # memoization table
        dp: List[List[str]] = [[""] * n for _ in range(n)]

        # length 1 substrings
        for i in range(n):
            dp[i][i] = s[i]

        # helper to find repeat count
        def repeat_count(l: int, r: int) -> int:
            length = r - l + 1
            pi = [0] * length
            for i in range(1, length):
                j = pi[i-1]
                while j > 0 and s[l+i] != s[l+j]:
                    j = pi[j-1]
                if s[l+i] == s[l+j]:
                    j += 1
                pi[i] = j
            period = length - pi[-1]
            return length // period if period < length and length % period == 0 else 1

        for length in range(2, n+1):
            for i in range(n - length + 1):
                j = i + length - 1
                best = s[i:j+1]
                # split
                for k in range(i, j):
                    cand = dp[i][k] + dp[k+1][j]
                    if len(cand) < len(best):
                        best = cand
                # repeat
                m = repeat_count(i, j)
                if m > 1:
                    part_len = length // m
                    cand = f"{m}[{dp[i][i+part_len-1]}]"
                    if len(cand) < len(best):
                        best = cand
                dp[i][j] = best

        return dp[0][n-1]
```

---

### 5.3 C++ (GNU++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string encode(string s) {
        int n = s.size();
        vector<vector<string>> dp(n, vector<string>(n));

        // length 1
        for (int i = 0; i < n; ++i) dp[i][i] = string(1, s[i]);

        // helper: KMP prefix for substring [l, r]
        auto repeatCount = [&](int l, int r) -> int {
            int len = r - l + 1;
            vector<int> pi(len, 0);
            for (int i = 1; i < len; ++i) {
                int j = pi[i-1];
                while (j > 0 && s[l+i] != s[l+j]) j = pi[j-1];
                if (s[l+i] == s[l+j]) ++j;
                pi[i] = j;
            }
            int period = len - pi[len-1];
            if (period < len && len % period == 0) return len / period;
            return 1;
        };

        for (int len = 2; len <= n; ++len) {
            for (int i = 0; i + len - 1 < n; ++i) {
                int j = i + len - 1;
                string best = s.substr(i, len);

                // split
                for (int k = i; k < j; ++k) {
                    string cand = dp[i][k] + dp[k+1][j];
                    if (cand.size() < best.size()) best = cand;
                }

                // repetition
                int m = repeatCount(i, j);
                if (m > 1) {
                    int partLen = len / m;
                    string part = dp[i][i+partLen-1];
                    string cand = to_string(m) + "[" + part + "]";
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

## 6. Blog Post – *The Good, The Bad, and the Ugly of String Encoding*

> **Title:** *Decode the Interview – Mastering LeetCode 471 (Encode String with Shortest Length)*
>
> **Meta Description:** Learn the DP approach for LeetCode 471, compare Java/Python/C++ solutions, and get SEO‑friendly interview prep content that can help land your dream job.

---

### 6.1 Why is This Problem Interview‑Gold?

- **String manipulation + DP** – two core CS concepts combined.
- **Optimisation mindset** – you’re asked not just to encode, but to *shorten*.
- **Scalability** – the algorithm must work fast for n ≤ 150, a realistic interview constraint.
- **Edge‑case mastery** – you must think of “no repetition” vs “repetition with nested patterns”.

Hiring managers love candidates who can balance *correctness* and *efficiency*, and LeetCode 471 is a perfect showcase.

---

### 6.2 The Good – What Works Well

| Aspect | Why it’s good |
|--------|---------------|
| **O(n³) DP** | Acceptable for n = 150; keeps code clean. |
| **KMP prefix** | Detects repeat patterns in linear time per substring, avoiding brute‑force checks. |
| **Modular design** | Separate `repeatCount` function makes the DP logic readable. |
| **Language‑agnostic** | The same algorithm is expressed in Java, Python, and C++ – handy for multi‑stack interviews. |

---

### 6.3 The Bad – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Not checking repetition when the encoded form is not shorter** | Compare lengths before committing to `k[encoded]`. |
| **Using `StringBuilder` incorrectly** | In Java, string concatenation in loops is costly – use `StringBuilder` or pre‑allocate. |
| **Mis‑computing the period** | Remember that the period is `len - pi[len-1]`. A missing check for `period < len` can return a false repeat. |
| **O(n³) memory blowup** | Store only the best string for each substring; avoid caching the full DP table if memory is tight. |

---

### 6.4 The Ugly – Edge Cases & Hidden Tricks

| Edge Case | Explanation |
|-----------|-------------|
| **Single character strings** | Return the same character – DP base case. |
| **Large numbers (e.g., “aaaaaaaa…”)** | `k` can be > 9; printing the integer itself adds digits – the algorithm handles it automatically. |
| **Nested encodings** | For strings like `"ababab"` the best might be `"3[ab]"` – the DP naturally builds nested solutions via splits. |
| **Overlapping patterns** | `"aaaaa"` can be `"5[a]"` or `"2[a]a"`. Our DP returns the shortest; ties can be arbitrary. |

---

### 6.5 How to Use This in Your Job Hunt

1. **Show the code** – copy the language you’re most comfortable with, run it on LeetCode, and push the repo to GitHub.  
2. **Explain the algorithm** – in your resume or portfolio, add a bullet: *“Implemented optimal O(n³) DP solution for LeetCode 471 using KMP to detect repeats.”*  
3. **Highlight the SEO angle** – write a blog (like this one) that demonstrates deep understanding, which can surface in Google searches by recruiters.  
4. **Practice variations** – tweak the problem: *“Encode with a maximum allowed nesting depth”* or *“Add wildcard characters”*. Show how DP adapts.  

---

## 7. Final Takeaway

- **Dynamic programming** + **KMP** = **Shortest encoding**.  
- Keep the solution clean, modular, and language‑agnostic.  
- Use the blog to demonstrate mastery, turning a coding interview problem into a portfolio asset.

Good luck—your next coding interview could just be a string away!