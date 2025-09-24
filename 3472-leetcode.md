---
title: LeetCode 3472. Longest Palindromic Subsequence After at Most K Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🏆 LeetCode 3472 – Longest Palindromic Subsequence After At‑Most K Operations  
> **Interview‑Ready Solution (Java / Python / C++) + In‑Depth Blog Post**

---

## Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Why This Problem Matters](#why-this-problem-matters)  
3. [Intuition & High‑Level Idea](#intuition)  
4. [Dynamic Programming Solution](#dp-solution)  
   - 4.1. State & Transition  
   - 4.2. Bottom‑Up Implementation (O(n²·k))  
5. [Complexity Analysis](#complexity)  
6. [The Good, The Bad, The Ugly](#good-bad-ugly)  
7. [Full Code (Java / Python / C++)](#code)  
8. [SEO Checklist & How It Helps Your Job Search](#seo)  
9. [Conclusion & Further Reading](#conclusion)

---

## 1. Problem Recap <a name="problem-recap"></a>

**LeetCode #3472 – Longest Palindromic Subsequence After At Most K Operations**

You’re given a string `s` of lowercase English letters (`1 ≤ s.length ≤ 200`) and an integer `k` (`1 ≤ k ≤ 200`).  
In **one** operation you may replace any character by its next **or** previous letter in the alphabet (wrapping around: after `z` comes `a`, before `a` comes `z`).  
Example: `'a' → 'b'` or `'a' → 'z'`.

**Task**  
Return the maximum possible length of a palindromic subsequence of `s` after performing **at most** `k` operations.

> **Examples**  
> *Input*: `s = "abced"`, `k = 2`  
> *Output*: `3` (change to `"accec"` → subsequence `"ccc"`)

> *Input*: `s = "aaazzz"`, `k = 4`  
> *Output*: `6` (change to `"zaaaaz"` → whole string is palindrome)

---

## 2. Why This Problem Matters <a name="why-this-problem-matters"></a>

- **String & DP Mastery** – Combines classic *Longest Palindromic Subsequence (LPS)* with an extra *resource‑bounded transformation* dimension.
- **Interview Signal** – Shows you can:
  - Convert a string transformation into an *optimal‑matching* DP.
  - Manage an additional *cost* dimension (the `k` operations) without blowing up the state space.
  - Optimize pre‑computation (`cost(a, b)`).
- **Real‑World Analogy** – Think of it as editing a DNA sequence within a limited mutation budget to maximize symmetry—a pattern that appears in bioinformatics and text processing.

---

## 3. Intuition & High‑Level Idea <a name="intuition"></a>

1. **Base Problem** – For a fixed `k = 0`, the answer is the classic LPS: `dp[i][j]` = length of longest palindrome in `s[i…j]`.
2. **Extra Dimension** – Allow `k` operations. We need to know *how many operations remain* while exploring substrings.
3. **Transition**  
   - If `s[i] == s[j]` → we can keep both ends → `dp[i][j][k] = 2 + dp[i+1][j-1][k]`.
   - Else, we can either **skip** one end or **change** both ends to match each other.  
     Changing costs `cost(s[i], s[j])`.  
     If `k >= cost`, we can take `2 + dp[i+1][j-1][k - cost]`.
4. **Bottom‑Up** – Iterate lengths from 1 to `n`, fill DP tables. Complexity stays manageable because `n ≤ 200`, `k ≤ 200`.

---

## 4. Dynamic Programming Solution <a name="dp-solution"></a>

### 4.1. State & Transition

- `dp[i][j][t]` – *maximum* length of a palindrome that can be formed from the substring `s[i…j]` using at most `t` operations.
- **Base**  
  - `dp[i][i][t] = 1` (single character is a palindrome of length 1).  
  - `dp[i][j][t] = 0` for `i > j` (empty substring).
- **Transition**  
  ```
  if s[i] == s[j]:
      dp[i][j][t] = 2 + dp[i+1][j-1][t]
  else:
      dp[i][j][t] = max(
                      dp[i+1][j][t],          // skip left
                      dp[i][j-1][t],          // skip right
                      (t >= cost(s[i],s[j])) ? 2 + dp[i+1][j-1][t - cost] : 0
                   )
  ```

- **Cost Function**  
  ```
  cost(a,b) = min(|a-b|, 26 - |a-b|)
  ```
  because moving forward or backward around the alphabet yields the same minimal number of operations.

### 4.2. Bottom‑Up Implementation (O(n²·k))

We pre‑compute the cost matrix for all 26×26 letter pairs to avoid recomputation.

Pseudo‑code:

```
for len = 1 .. n:
    for i = 0 .. n-len:
        j = i + len - 1
        for t = 0 .. k:
            if i == j:
                dp[i][j][t] = 1
            else if s[i] == s[j]:
                dp[i][j][t] = 2 + dp[i+1][j-1][t]
            else:
                ans = max(dp[i+1][j][t], dp[i][j-1][t])
                c = cost[s[i]][s[j]]
                if t >= c:
                    ans = max(ans, 2 + dp[i+1][j-1][t - c])
                dp[i][j][t] = ans
```

Answer: `dp[0][n-1][k]`.

---

## 5. Complexity Analysis <a name="complexity"></a>

| Metric | Formula | Result |
|--------|---------|--------|
| **Time** | `O(n² · k)` | ≤ `200² · 200` ≈ 8 million operations – easily fits in 1 s. |
| **Space** | `O(n² · k)` | ≈ 8 million integers ≈ 32 MB (Java int) – acceptable. |
| **Optimization** | Use two 2‑D layers (`prev`, `curr`) to reduce memory to `O(n·k)` if desired. |

---

## 6. The Good, The Bad, The Ugly <a name="good-bad-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **DP Idea** | Clean separation of substring and operation budget. Works for any `k`. | None – the algorithm is optimal. |
| **Pre‑Computation** | Cost matrix speeds up inner loops. | If forgotten, `cost` inside 3 nested loops becomes a bottleneck. |
| **Memory Footprint** | 32 MB is fine for LeetCode’s limits. | 3‑D array is heavy; interviewers may ask you to *streamline* it. |
| **Recursive (Top‑Down)** | Elegant code; fewer loops. | Stack depth may blow (`n` up to 200) → recursion limit problems. |
| **Tail‑Recursive / Memoized** | Reuse results. | Still `O(n²·k)` calls; risk of `O(n³·k)` if not memoized. |
| **Practical** | Substring indices + budget → natural for interviewers. | Some candidates over‑optimize early and lose clarity. |
| **“Ugly”** | None | A naïve solution that tries every possible set of operations (`k`‑subsets) is exponential – *do NOT* attempt. |

**Takeaway**  
- Use the *bottom‑up DP* as your go‑to solution.  
- If you’re pressed for space, keep only two layers.  
- Avoid recursive memoization unless you’re sure your language handles deep recursion well.

---

## 7. Full Code (Java / Python / C++) <a name="code"></a>

### 7.1. Java (Optimized Bottom‑Up)

```java
import java.io.*;

public class Solution {
    // Pre‑compute cost between all 26 letters
    private static final int[][] COST = new int[26][26];

    static {
        for (int a = 0; a < 26; a++) {
            for (int b = 0; b < 26; b++) {
                int diff = Math.abs(a - b);
                COST[a][b] = Math.min(diff, 26 - diff);
            }
        }
    }

    public int longestSubsequence(String s, int k) {
        int n = s.length();
        char[] ch = s.toCharArray();

        // dp[i][j][t]  (i <= j)
        int[][][] dp = new int[n][n][k + 1];

        for (int len = 1; len <= n; len++) {
            for (int i = 0; i + len - 1 < n; i++) {
                int j = i + len - 1;
                for (int t = 0; t <= k; t++) {
                    if (i == j) {
                        dp[i][j][t] = 1;
                    } else if (ch[i] == ch[j]) {
                        dp[i][j][t] = 2 + dp[i + 1][j - 1][t];
                    } else {
                        int best = Math.max(dp[i + 1][j][t], dp[i][j - 1][t]);
                        int cost = COST[ch[i] - 'a'][ch[j] - 'a'];
                        if (t >= cost) {
                            best = Math.max(best, 2 + dp[i + 1][j - 1][t - cost]);
                        }
                        dp[i][j][t] = best;
                    }
                }
            }
        }
        return dp[0][n - 1][k];
    }

    /* ---------- For LeetCode ---------- */
    public static void main(String[] args) throws Exception {
        Solution sol = new Solution();
        System.out.println(sol.longestSubsequence("abced", 2));   // 3
        System.out.println(sol.longestSubsequence("aaazzz", 4));   // 6
    }
}
```

> **Tip** – Replace `int[][][] dp` with `int[][] prev = new int[n][k+1]` and `int[][] curr` to halve memory usage.

---

### 7.2. Python (Bottom‑Up)

```python
class Solution:
    def longestSubsequence(self, s: str, k: int) -> int:
        n = len(s)
        # cost matrix 26x26
        cost = [[0]*26 for _ in range(26)]
        for a in range(26):
            for b in range(26):
                diff = abs(a - b)
                cost[a][b] = min(diff, 26 - diff)

        # dp[i][j][t] – 3‑D list
        dp = [[[0]*(k+1) for _ in range(n)] for _ in range(n)]

        for length in range(1, n+1):
            for i in range(n-length+1):
                j = i + length - 1
                for t in range(k+1):
                    if i == j:
                        dp[i][j][t] = 1
                    elif s[i] == s[j]:
                        dp[i][j][t] = 2 + dp[i+1][j-1][t]
                    else:
                        best = max(dp[i+1][j][t], dp[i][j-1][t])
                        c = cost[ord(s[i]) - 97][ord(s[j]) - 97]
                        if t >= c:
                            best = max(best, 2 + dp[i+1][j-1][t-c])
                        dp[i][j][t] = best
        return dp[0][n-1][k]
```

**Fastest Python version** – Replace the 3‑D array with two 2‑D layers (rolling DP) and pre‑compute `cost` as a 26×26 list for O(n·k) memory.

---

### 7.3. C++ (Bottom‑Up)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestSubsequence(string s, int k) {
        int n = s.size();
        // Pre‑compute cost matrix
        int cost[26][26];
        for (int a = 0; a < 26; ++a)
            for (int b = 0; b < 26; ++b) {
                int diff = abs(a - b);
                cost[a][b] = min(diff, 26 - diff);
            }

        // dp[i][j][t] – 3‑D vector
        vector<vector<vector<int>>> dp(n,
            vector<vector<int>>(n, vector<int>(k+1, 0)));

        for (int len = 1; len <= n; ++len) {
            for (int i = 0; i + len - 1 < n; ++i) {
                int j = i + len - 1;
                for (int t = 0; t <= k; ++t) {
                    if (i == j) {
                        dp[i][j][t] = 1;
                    } else if (s[i] == s[j]) {
                        dp[i][j][t] = 2 + dp[i+1][j-1][t];
                    } else {
                        int best = max(dp[i+1][j][t], dp[i][j-1][t]);
                        int c = cost[s[i]-'a'][s[j]-'a'];
                        if (t >= c)
                            best = max(best, 2 + dp[i+1][j-1][t-c]);
                        dp[i][j][t] = best;
                    }
                }
            }
        }
        return dp[0][n-1][k];
    }
};
```

---

## 7.4. (Optional) Memory‑Optimized C++ (Two Layers)

```cpp
// Only keep current length and previous length slices
int cur[n][k+1], prev[n][k+1];
// Fill cur using prev (dp[i+1][j-1] becomes prev[j-1] after shifting)
// Swap cur/prev at each length iteration
```

---

## 7.  The Good, The Bad, The Ugly <a name="good-bad-ugly"></a>

| **Category** | **Good** | **Bad** | **Ugly** |
|--------------|----------|---------|----------|
| **Readability** | Clear 3‑D DP with comments | 3‑D array name `dp` is verbose but self‑documenting | Deep recursive memoization without base cases → confusing |
| **Performance** | O(n²·k) – 8 M ops | Still memory‑heavy (≈ 32 MB) but acceptable | Brute‑force search of all operation subsets → exponential |
| **Maintainability** | Pre‑computed cost matrix | Requires careful indexing (`'a' → 0`) | Hard‑coded formulas inside loops, risk of off‑by‑one |
| **Space** | 3‑D array is clear | Not cache‑friendly when `k` is large | Recursion stack may overflow on large `n` |

---

## 7. Full Code (Java / Python / C++) <a name="code"></a>

### 7.1. Java (LeetCode‑Ready)

```java
public class Solution {
    private static final int[][] COST = new int[26][26];

    static {
        for (int a = 0; a < 26; a++)
            for (int b = 0; b < 26; b++) {
                int diff = Math.abs(a - b);
                COST[a][b] = Math.min(diff, 26 - diff);
            }
    }

    public int longestSubsequence(String s, int k) {
        int n = s.length();
        char[] ch = s.toCharArray();

        int[][][] dp = new int[n][n][k + 1];

        for (int len = 1; len <= n; len++) {
            for (int i = 0; i + len - 1 < n; i++) {
                int j = i + len - 1;
                for (int t = 0; t <= k; t++) {
                    if (i == j) {
                        dp[i][j][t] = 1;
                    } else if (ch[i] == ch[j]) {
                        dp[i][j][t] = 2 + dp[i + 1][j - 1][t];
                    } else {
                        int best = Math.max(dp[i + 1][j][t], dp[i][j - 1][t]);
                        int cost = COST[ch[i] - 'a'][ch[j] - 'a'];
                        if (t >= cost)
                            best = Math.max(best, 2 + dp[i + 1][j - 1][t - cost]);
                        dp[i][j][t] = best;
                    }
                }
            }
        }
        return dp[0][n - 1][k];
    }
}
```

---

### 7.2. Python (LeetCode‑Ready)

```python
class Solution:
    def longestSubsequence(self, s: str, k: int) -> int:
        n = len(s)
        # Pre‑compute 26x26 cost matrix
        cost = [[0]*26 for _ in range(26)]
        for a in range(26):
            for b in range(26):
                diff = abs(a-b)
                cost[a][b] = min(diff, 26-diff)

        dp = [[[0]*(k+1) for _ in range(n)] for _ in range(n)]
        for length in range(1, n+1):
            for i in range(n-length+1):
                j = i + length - 1
                for t in range(k+1):
                    if i == j:
                        dp[i][j][t] = 1
                    elif s[i] == s[j]:
                        dp[i][j][t] = 2 + dp[i+1][j-1][t]
                    else:
                        best = max(dp[i+1][j][t], dp[i][j-1][t])
                        c = cost[ord(s[i])-97][ord(s[j])-97]
                        if t >= c:
                            best = max(best, 2 + dp[i+1][j-1][t-c])
                        dp[i][j][t] = best
        return dp[0][n-1][k]
```

---

### 7.3. C++ (LeetCode‑Ready)

```cpp
class Solution {
public:
    int longestSubsequence(string s, int k) {
        int n = s.size();
        int cost[26][26];
        for (int a=0; a<26; ++a)
            for (int b=0; b<26; ++b) {
                int diff = abs(a-b);
                cost[a][b] = min(diff, 26-diff);
            }

        vector<vector<vector<int>>> dp(n, vector<vector<int>>(n, vector<int>(k+1,0)));

        for (int len=1; len<=n; ++len) {
            for (int i=0; i+len-1<n; ++i) {
                int j=i+len-1;
                for (int t=0; t<=k; ++t) {
                    if (i==j) dp[i][j][t]=1;
                    else if (s[i]==s[j]) dp[i][j][t]=2+dp[i+1][j-1][t];
                    else {
                        int best=max(dp[i+1][j][t], dp[i][j-1][t]);
                        int c=cost[s[i]-'a'][s[j]-'a'];
                        if (t>=c) best=max(best, 2+dp[i+1][j-1][t-c]);
                        dp[i][j][t]=best;
                    }
                }
            }
        }
        return dp[0][n-1][k];
    }
};
```

---

## 8.  Conclusion

- The *bottom‑up 3‑D DP* solution is the **optimal** approach to LeetCode problem #2414.  
- Pre‑compute the alphabet cost matrix; keep loops tight.  
- If space is a concern, roll the DP into two layers.  
- Avoid recursive solutions that ignore memoization – they’re **ugly** and exponential.

**Happy coding!** 🚀

---


---

**Meta‑information for LeetCode’s HTML page (optional)**  

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>LeetCode 2414 – Longest Subsequence</title>
  <meta name="description" content="Implement the longest palindrome‑like subsequence with limited alphabet changes.">
</head>
<body>
  <h1>2414. Longest Subsequence</h1>
  <p>Java, Python, C++ solutions based on 3‑D DP with pre‑computed alphabet change cost.</p>
</body>
</html>
```

---


**Enjoy solving, and good luck in your interview!**