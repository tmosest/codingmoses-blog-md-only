---
title: LeetCode 471. Encode String with Shortest Length - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 471. **Encode String with Shortest Length**  
> **Hard** – LeetCode

> **Problem**:  
> You are given a string `s` consisting only of lowercase letters.  
> Encode it using the rule `k[encoded_string]` where the `encoded_string` is repeated exactly `k` times.  
> The encoded string must be **shorter** than the original; otherwise, keep the original.  
> Return *any* shortest possible encoding.

> **Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `"aaa"` | `"aaa"` | No encoding makes it shorter |
| `"aaaaa"` | `"5[a]"` | `5[a]` is shorter |
| `"aaaaaaaaaa"` | `"10[a]"` | `"10[a]"`, `"9[a]a"`, `"a9[a]"` all have the same minimal length |

> **Constraints**  
> `1 ≤ s.length ≤ 150`  
> `s` contains only lowercase English letters.

---

## Why It Matters

- The problem is a **classic interview question** that tests dynamic programming, string manipulation, and creativity in handling edge‑cases.
- It is a good showcase for your ability to design **optimal solutions** for *non‑trivial* string problems—an essential skill for front‑end/back‑end engineers, data scientists, and algorithm‑centric roles.
- A clean, well‑commented implementation of this problem demonstrates proficiency in *Java*, *Python*, and *C++*, the three most searched programming languages by hiring managers.

---

## Solution Overview

The optimal solution is a **3‑dimensional DP** that works in `O(n³)` time (with `n ≤ 150`, this is perfectly fine).

1. **DP table**  
   `dp[i][j]` = *shortest encoded string* for the substring `s[i … j]`.

2. **Initialization**  
   For every single character `dp[i][i] = s[i]`.

3. **Recurrence**  
   For every length `len` from `2` to `n`  
   - **Split** the substring into two parts `s[i … k]` and `s[k+1 … j]`.  
     `dp[i][j] = min(dp[i][k] + dp[k+1][j])`.
   - **Check for repetition**:  
     Let the substring length be `len`.  
     For each divisor `k` of `len` (excluding `len` itself):  
     *If* the substring is made of `len / k` repetitions of its first `k` characters, we can encode it as  
     `cnt[k] + "[" + dp[i][i+k-1] + "]"`.  
     Keep the shortest result.

4. **Return** `dp[0][n-1]`.

**Key helper** – `findPatternLength(sub)`  
Checks the minimal period of a string in `O(len)` using the KMP prefix function. It is used to identify repeated patterns efficiently.

---

## Code

Below are three clean, fully‑commented solutions: one in **Java**, one in **Python**, and one in **C++**.

> **Note**: All three use the same logic and can be copied into your IDE or submission box.

---

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    public String encode(String s) {
        int n = s.length();
        String[][] dp = new String[n][n];

        // 1. single letters
        for (int i = 0; i < n; i++) dp[i][i] = String.valueOf(s.charAt(i));

        // 2. DP for all lengths
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i + len - 1 < n; i++) {
                int j = i + len - 1;
                dp[i][j] = s.substring(i, j + 1);   // initial: the raw substring

                // 2.1 Split
                for (int k = i; k < j; k++) {
                    String candidate = dp[i][k] + dp[k + 1][j];
                    if (candidate.length() < dp[i][j].length())
                        dp[i][j] = candidate;
                }

                // 2.2 Repetition
                int patternLen = findPatternLen(s.substring(i, j + 1));
                if (patternLen < len) {          // only if we found a repetition
                    int times = len / patternLen;
                    String encoded = times + "[" + dp[i][i + patternLen - 1] + "]";
                    if (encoded.length() < dp[i][j].length())
                        dp[i][j] = encoded;
                }
            }
        }
        return dp[0][n - 1];
    }

    // KMP prefix function to find minimal period
    private int findPatternLen(String sub) {
        int n = sub.length();
        int[] lps = new int[n];
        for (int i = 1, len = 0; i < n; ) {
            if (sub.charAt(i) == sub.charAt(len)) {
                lps[i++] = ++len;
            } else if (len != 0) {
                len = lps[len - 1];
            } else {
                lps[i++] = 0;
            }
        }
        int period = n - lps[n - 1];
        return (n % period == 0) ? period : n;
    }
}
```

---

### 2️⃣ Python

```python
class Solution:
    def encode(self, s: str) -> str:
        n = len(s)
        dp = [[""] * n for _ in range(n)]

        # Single letters
        for i in range(n):
            dp[i][i] = s[i]

        # DP for all lengths
        for length in range(2, n + 1):
            for i in range(n - length + 1):
                j = i + length - 1
                sub = s[i:j + 1]
                dp[i][j] = sub  # raw substring

                # Split
                for k in range(i, j):
                    cand = dp[i][k] + dp[k + 1][j]
                    if len(cand) < len(dp[i][j]):
                        dp[i][j] = cand

                # Repetition
                period = self._period(sub)
                if period < length:                     # repetition exists
                    times = length // period
                    cand = f"{times}[{dp[i][i + period - 1]}]"
                    if len(cand) < len(dp[i][j]):
                        dp[i][j] = cand

        return dp[0][n - 1]

    def _period(self, s: str) -> int:
        """Return minimal period of string s (using KMP)."""
        n = len(s)
        lps = [0] * n
        for i in range(1, n):
            j = lps[i - 1]
            while j > 0 and s[i] != s[j]:
                j = lps[j - 1]
            if s[i] == s[j]:
                j += 1
            lps[i] = j
        period = n - lps[-1]
        return period if n % period == 0 else n
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string encode(string s) {
        int n = s.size();
        vector<vector<string>> dp(n, vector<string>(n));

        // single letters
        for (int i = 0; i < n; ++i) dp[i][i] = string(1, s[i]);

        for (int len = 2; len <= n; ++len) {
            for (int i = 0; i + len - 1 < n; ++i) {
                int j = i + len - 1;
                string sub = s.substr(i, len);
                dp[i][j] = sub;                       // raw substring

                // Split
                for (int k = i; k < j; ++k) {
                    string cand = dp[i][k] + dp[k + 1][j];
                    if (cand.size() < dp[i][j].size())
                        dp[i][j] = cand;
                }

                // Repetition
                int period = findPeriod(sub);
                if (period < len) {                     // we found a repetition
                    int times = len / period;
                    string cand = to_string(times) + "[" + dp[i][i + period - 1] + "]";
                    if (cand.size() < dp[i][j].size())
                        dp[i][j] = cand;
                }
            }
        }
        return dp[0][n - 1];
    }

private:
    int findPeriod(const string &s) {
        int n = s.size();
        vector<int> lps(n, 0);
        for (int i = 1, len = 0; i < n; ) {
            if (s[i] == s[len]) lps[i++] = ++len;
            else if (len) len = lps[len - 1];
            else lps[i++] = 0;
        }
        int period = n - lps[n - 1];
        return (n % period == 0) ? period : n;
    }
};
```

---

## Blog Post – “Encode String with Shortest Length: The Good, the Bad, and the Ugly”

### 1. Introduction

When interviewing for a **software engineering** role, you’ll inevitably stumble on string‑encoding problems. One of the most popular is **LeetCode 471 – Encode String with Shortest Length**. It asks you to compress a string into its shortest possible representation using a simple `k[encoded_string]` syntax.

Why is this problem a *must‑know*?

- It blends **dynamic programming** with **string processing**.
- It reveals how you deal with **edge cases** and **optimization**.
- It demonstrates your ability to produce **clean, maintainable code** across multiple languages—exactly what recruiters search for on LinkedIn and GitHub.

This article walks through the problem’s **good** (what works well), the **bad** (common pitfalls), and the **ugly** (edge cases that trip even seasoned programmers). By the end, you’ll have a battle‑tested solution ready for any coding interview.

---

### 2. The Good: What Makes This Problem Solvable

| Feature | Why it helps |
|---------|--------------|
| **Deterministic Output** | You always need the *shortest* encoded string. No randomness. |
| **Small Input Size** (`≤ 150`) | Allows an `O(n³)` DP solution without timeout worries. |
| **Fixed Syntax** (`k[... ]`) | No ambiguity; you can reliably split or encode. |
| **Self‑Containment** | The string is only lowercase letters – no special characters to escape. |

These constraints make a *bottom‑up DP* natural:

1. **Subproblem** – encode a substring `s[i..j]`.
2. **Transition** – split the substring or compress it if it’s a repetition.
3. **Base** – single characters are already encoded.

Because you can compute every smaller substring first, the DP table fills from small to large substrings. The resulting algorithm is both intuitive and efficient.

---

### 3. The Bad: Common Pitfalls & “Why It Fell Apart”

| Pitfall | What Goes Wrong | Fix |
|---------|-----------------|-----|
| **Using `int` for count** | If you encode “aaaa…(1000 times)”, converting the count to a string incorrectly (overflow) | Always convert the *int* to a string (`String.valueOf(count)` or `to_string(count)`). |
| **Naïve repetition check** | Checking every substring against all possible prefixes leads to `O(n⁴)` | Use the *prefix function (KMP)* to find the minimal period in `O(n)`. |
| **Ignoring already optimal substrings** | If a split gives the same length as the raw substring, you might overwrite it unnecessarily | Keep the *original* substring as a fallback and only replace when the new encoding is strictly shorter. |
| **Index off‑by‑one** | DP tables often use `dp[i][j]` meaning `i` inclusive, `j` exclusive; mismatching leads to bugs | Pick a convention (inclusive‑inclusive or inclusive‑exclusive) and stick to it throughout. |
| **Recursive with memoization but missing the “no‑encoding” rule** | You may return a compressed string that’s *not shorter*, violating the problem statement | Compare lengths explicitly; if `encoded.length() >= original.length()`, keep the original. |

---

### 4. The Ugly: Edge Cases That Can Trip You Up

1. **Very short strings**  
   `s = "a"` – should return `"a"`.  
   **Why?** A DP solution may try to split into empty parts. Guard against `i == j`.

2. **Multiple optimal solutions**  
   `"aaaaaaaaaa"` can be encoded as `"10[a]"`, `"9[a]a"`, `"a9[a]"`.  
   **Why?** The DP only needs *any* shortest one, but your implementation might unintentionally return a longer encoded string if the comparison is not strict (`<=` instead of `<`).

3. **Large repetition counts**  
   `"ababababab..."` repeated 50 times – count becomes 50.  
   **Why?** Converting the integer to a string can take multiple digits, making the encoded string longer than the raw substring. Always compare full lengths.

4. **Nested encoding**  
   `"aaaaaa"` → `“6[a]”` is optimal, but `“3[2[a]]”` is also 6 characters.  
   **Why?** DP must consider nested patterns correctly. The minimal period detection handles this automatically.

5. **Non‑divisible lengths**  
   `"abcabcab"` – not a full repetition.  
   **Why?** Period detection must return the original length; otherwise, you might incorrectly encode it as `1[abcabcab]`.

---

### 5. The DP Walk‑through

```text
dp[i][j] – shortest encoding for s[i…j]
```

1. **Initialization**  
   `dp[i][i] = s[i]`

2. **Length loop** (`len` from 2 to n)

   For each `i`, set `j = i + len - 1`

   - **Start with the raw substring**  
     `dp[i][j] = s[i…j]`
   - **Try every split** `k` (`i ≤ k < j`)  
     `cand = dp[i][k] + dp[k+1][j]`  
     If `cand` shorter → `dp[i][j] = cand`
   - **Check repetition**  
     `period = minimalPeriod(s[i…j])`  
     If `period < len` → repetition exists  
     `cand = count + '[' + dp[i][i+period-1] + ']'`  
     Replace if strictly shorter.

3. **Result** – `dp[0][n-1]`

The *period* is found using KMP in `O(len)`. Thus, the overall complexity is `O(n³)` (length × splits) with an additional `O(n)` repetition check inside each iteration.

---

### 6. Multi‑Language Mastery

Recruiters often ask: *“What’s your preferred language for a coding interview?”*  
A strong candidate demonstrates competence in at least:

- **Java** (or Kotlin) – enterprise stack
- **Python** – rapid prototyping, data science
- **C++** – systems programming, algorithm contests

The solutions we presented earlier are nearly identical across languages, emphasizing:

- **Reusability** – same logic, just language syntax changes.
- **Readability** – clear variable names, consistent DP indices.
- **Modularity** – helper functions for KMP period detection and string conversion.

When you present these solutions on GitHub or during an interview, you’ll show that you can *adapt* rather than *copy‑paste*.

---

### 6. Take‑away Checklist

- [ ] **Base case**: `dp[i][i] = s[i]`
- [ ] **Always keep the raw substring** if nothing shorter is found.
- [ ] **Use KMP** (prefix function) to find minimal period.
- [ ] **Compare lengths** after generating an encoded candidate.
- [ ] **Avoid integer overflow** – convert count to string before comparing.
- [ ] **Test edge cases**: single char, nested patterns, high digit counts.

Run your implementation against LeetCode’s *hard‑coded test cases* and a few *custom* edge cases to be sure.

---

### 7. Final Words

LeetCode 471 is more than a fun puzzle; it’s a litmus test for your algorithmic thinking. Master it, and you’ll:

- Earn **Top Interviewer** badges on platforms like LeetCode.
- Have a **ready‑to‑paste solution** for your next coding round.
- Impress recruiters searching for “dynamic programming + string manipulation” on LinkedIn.

Happy coding, and may your next interview be as *short* and *optimal* as your encoding!

---

### 8. Related Resources

- [Dynamic Programming Primer](https://leetcode.com/discuss/interview-question/12620/DP-in-10-minutes)
- [KMP Algorithm Explained](https://www.geeksforgeeks.org/knuth-morris-pratt-algorithm/)
- [LeetCode 472 – Concatenated Words](https://leetcode.com/problems/concatenated-words/)
- [LeetCode 451 – Sort Characters By Frequency](https://leetcode.com/problems/sort-characters-by-frequency/)

--- 

### 9. Call to Action

Got your own implementation? **Share** it on GitHub, tag it with `#leetcode471`, and let the community know. Want to practice more string problems? **Join our 30‑day interview challenge** and start with this one today!

---

### 10. References

- LeetCode Problem 471
- KMP Algorithm on GeeksforGeeks
- Dynamic Programming – *Introduction to Algorithms* (CLRS)

---

> *Remember: in coding interviews, **clarity** beats *cleverness*. The DP approach above is a testament to that. Good luck!*

---

## 6. Conclusion

You now have:

- A **battle‑tested solution** in Java, Python, and C++.
- A **road‑map** to avoid the usual pitfalls.
- A **blog post** that showcases your deep understanding – perfect for LinkedIn or your portfolio.

When recruiters search for “dynamic programming string encoding”, they’ll find your code, your language versatility, and your polished problem explanation. That’s exactly what makes a candidate *stand out*.

Happy interviewing! 🚀

--- 

*Feel free to comment, share, or fork the code. Let’s keep the conversation going!*