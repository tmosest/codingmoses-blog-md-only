---
title: LeetCode 1312. Minimum Insertion Steps to Make a String Palindrome - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Master LeetCode 1312 – *Minimum Insertion Steps to Make a String Palindrome*  
*(A complete, interview‑ready guide for Java, Python & C++)*  

---

### TL;DR  
- **Problem**: Given a string `s`, return the *minimum* number of insertions needed to make it a palindrome.  
- **Optimal Approach**: Longest Palindromic Subsequence (LPS) → `minInsertions = |s| – LPS`.  
- **Complexities**: `O(n²)` time, `O(n²)` space (can be reduced to `O(n)` if you’re comfortable).  
- **Why It Matters**: This is a classic DP problem that shows you can reduce a string‑manipulation problem to LCS/LPS. Recruiters love it.  

---

## 1️⃣ Problem Recap

```text
Input:  s = "mbadm"
Output: 2

Explanation:
  "mbadm" → "mbdadbm" (insert 'd' after 'b', 'a' before 'd')
  Two insertions are sufficient, and you can prove it’s minimal.
```

- Length of `s`: `1 ≤ |s| ≤ 500`  
- Only lowercase English letters.

---

## 2️⃣ Why LPS Works

An insertion only *adds* characters; it never removes or changes existing ones.  
The longest subsequence that is already a palindrome can stay untouched – we only need to insert the missing characters.  
Thus:

```text
minInsertions = |s| – length_of_longest_palindromic_subsequence(s)
```

Finding the LPS is identical to finding the LCS between `s` and its reverse (`rev(s)`).  
That is why we use the classic **Longest Common Subsequence DP**.

---

## 3️⃣ DP Recurrence

Let `dp[i][j]` be the length of the LCS of `s[0…i-1]` and `rev[0…j-1]`.

```
if s[i-1] == rev[j-1]   →  dp[i][j] = 1 + dp[i-1][j-1]
else                    →  dp[i][j] = max(dp[i-1][j], dp[i][j-1])
```

Answer: `n - dp[n][n]`, where `n = s.length()`.

---

## 4️⃣ Complexity

| Metric | Complexity |
|--------|------------|
| Time   | `O(n²)` (`n ≤ 500` → 250 k operations, trivial for any language) |
| Space  | `O(n²)` DP table (≈ 250 k ints ≈ 1 MB) |
| Optional | Reduce space to `O(n)` by rolling rows (not shown below for clarity). |

---

## 5️⃣ Edge Cases & Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Forget to reverse the string | `StringBuilder(s).reverse()` (Java), `s[::-1]` (Python), `reverse()` (C++) |
| Off‑by‑one indexing in DP loops | Use `i <= n`, `j <= n` and access `s[i-1]` |
| Large memory usage on 2‑D array | `vector<vector<int>> dp(n+1, vector<int>(n+1, 0));` is fine for n ≤ 500 |

---

## 6️⃣ Code Implementations

> 💡 **All three versions use the same DP logic – just syntax changes.**

### 6.1 Java

```java
public class Solution {
    public int minInsertions(String s) {
        int n = s.length();
        String rev = new StringBuilder(s).reverse().toString();

        int[][] dp = new int[n + 1][n + 1];

        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (s.charAt(i - 1) == rev.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return n - dp[n][n];
    }
}
```

### 6.2 Python

```python
class Solution:
    def minInsertions(self, s: str) -> int:
        n = len(s)
        rev = s[::-1]
        dp = [[0] * (n + 1) for _ in range(n + 1)]

        for i in range(1, n + 1):
            for j in range(1, n + 1):
                if s[i - 1] == rev[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1] + 1
                else:
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

        return n - dp[n][n]
```

### 6.3 C++

```cpp
class Solution {
public:
    int minInsertions(string s) {
        int n = s.size();
        string rev(s.rbegin(), s.rend());
        vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));

        for (int i = 1; i <= n; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (s[i - 1] == rev[j - 1])
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                else
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
            }
        }
        return n - dp[n][n];
    }
};
```

---

## 7️⃣ Testing Your Solution

| Test | Input | Expected |
|------|-------|----------|
| 1 | `"zzazz"` | `0` |
| 2 | `"mbadm"` | `2` |
| 3 | `"leetcode"` | `5` |
| 4 | `"a"` | `0` |
| 5 | `"ab"` | `1` |
| 6 | `"abca"` | `1` |
| 7 | `"abcda"` | `2` |

Run the provided unit tests in your IDE or online LeetCode editor to verify correctness.

---

## 8️⃣ Real‑World Relevance

- **Text editors & IDEs**: Autocomplete and spell‑checker engines need to compare strings with minimal edits.
- **DNA sequencing**: Finding palindromic subsequences is essential for genome analysis.
- **Compilers**: Palindromic patterns help in syntax highlighting and syntax checking.

---

## 9️⃣ Take‑Home Lessons

| Good | Bad | Ugly |
|------|-----|------|
| ✅ Clear **problem reduction**: LPS → LCS → DP | ❌ Some solutions use *brute‑force* or *recursive* approaches that explode exponentially | ❌ Forgetting the *reverse* string or indexing off by one causes wrong answers |
| ✅ **Modular code**: Separate `minInsertions` method, reusable in interviews | ❌ Over‑engineering: using `HashMap` or `LinkedList` where a simple array suffices | ❌ Using recursion without memoization leads to stack overflow |
| ✅ **Space‑optimised variant**: one‑dimensional DP (if asked) | ❌ Mixing languages in the same repo without clear separation | ❌ Unnecessary printing/debugging statements that clutter logs |

---

## 🔧 Bonus: Space‑Optimised (O(n)) Python Implementation

```python
def minInsertions(s: str) -> int:
    rev = s[::-1]
    n = len(s)
    prev = [0] * (n + 1)
    for i in range(1, n + 1):
        cur = [0] * (n + 1)
        for j in range(1, n + 1):
            cur[j] = prev[j - 1] + 1 if s[i-1] == rev[j-1] else max(prev[j], cur[j-1])
        prev = cur
    return n - prev[n]
```

---

## 🎯 Why This Blog Helps You Land a Job

- **Keyword‑rich content**: “LeetCode 1312”, “minimum insertions palindrome”, “dynamic programming interview”, “software engineer interview prep”.
- **Clear structure**: Intro → Intuition → DP → Code → Tests → Takeaways.
- **Actionable code**: Ready‑to‑copy snippets for Java, Python, C++.
- **Professional tone**: Demonstrates deep understanding of algorithmic concepts.

> 👉 **Want more interview‑ready guides?** Subscribe to our newsletter or check out the full series on *Dynamic Programming, Graphs, and Data Structures*.

---

## 📚 Final Code Cheat‑Sheet

```java
// Java
class Solution {
    public int minInsertions(String s) { /* DP as above */ }
}
```

```python
# Python
class Solution:
    def minInsertions(self, s: str) -> int:  # DP as above
```

```cpp
// C++
class Solution {
public:
    int minInsertions(string s) { /* DP as above */ }
};
```

Copy, paste, run, and you’re ready for your next interview question. Happy coding! 🚀