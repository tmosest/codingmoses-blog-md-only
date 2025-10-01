---
title: LeetCode 1216. Valid Palindrome III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Valid Palindrome III – 1216 (Hard)  
**Java | Python | C++** – Full working solutions  
**SEO‑optimized blog article** – “How to ace the interview question “Valid Palindrome III””

---

### 1. Problem Recap

> **LeetCode 1216 – Valid Palindrome III**  
> Given a string `s` and an integer `k`, determine whether `s` can be turned into a palindrome by deleting at most `k` characters.

**Examples**

| s           | k | result |
|-------------|---|--------|
| `"abcdeca"` | 2 | `true`  |
| `"abbababa"`| 1 | `true`  |

**Constraints**

* `1 ≤ s.length ≤ 1000`
* `s` consists of lowercase English letters
* `1 ≤ k ≤ s.length`

The challenge is to compute the *minimum* number of deletions needed to make `s` a palindrome and compare it to `k`.

---

### 2. High‑Level Strategy

The problem is equivalent to **finding the length of the longest palindromic subsequence (LPS)** of `s`.  
If the LPS has length `len`, we need `s.length() – len` deletions.  
So the problem reduces to a classic **dynamic‑programming (DP)** sub‑problem.

#### DP definition

Let  

```
dp[i][j] = minimum deletions needed to make s[i…j] a palindrome
```

Recurrence:

```
If s[i] == s[j]          →  dp[i][j] = dp[i+1][j-1]
Else                     →  dp[i][j] = 1 + min(dp[i+1][j], dp[i][j-1])
```

Base cases: `dp[i][i] = 0` (single character) and `dp[i][i-1] = 0` (empty substring).

We fill the DP table from shorter to longer substrings (bottom‑up) or use memoised recursion (top‑down).  
The answer is `dp[0][n-1] <= k`.

---

### 3. Why This Works (Good, Bad, Ugly)

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | `O(n²)` – acceptable for `n ≤ 1000` | No improvement over O(n²) needed | Quadratic time is fine, but naïve recursion without memoisation is exponential |
| **Space Complexity** | `O(n²)` – two‑dimensional table | Can be reduced to `O(n)` by rolling arrays, but code gets trickier | Using only `int[][]` is simplest and still fast enough |
| **Intuition** | “Delete the cheaper side” | Requires careful handling of indices | People may think of LPS directly, but forgetting the `+1` for mismatches can break logic |
| **Edge Cases** | Handles empty/one‑char strings automatically | Must check for `k` equal to string length | If `k ≥ s.length()` → always true; if `k == 0` → check if already palindrome |

---

### 4. Reference Implementations

Below you’ll find clean, ready‑to‑copy solutions in **Java**, **Python**, and **C++**.  
All three follow the same DP logic and run in `O(n²)` time and space.

---

#### 4.1 Java

```java
import java.util.Arrays;

public class Solution {
    public boolean isValidPalindrome(String s, int k) {
        int n = s.length();
        // dp[l][r] = min deletions for s[l..r]
        int[][] dp = new int[n][n];

        // sub‑strings of length 1 are already palindrome
        for (int i = 0; i < n; i++) {
            dp[i][i] = 0;
        }

        // build up for lengths 2 .. n
        for (int len = 2; len <= n; len++) {
            for (int l = 0; l + len - 1 < n; l++) {
                int r = l + len - 1;
                if (s.charAt(l) == s.charAt(r)) {
                    dp[l][r] = dp[l + 1][r - 1];
                } else {
                    dp[l][r] = 1 + Math.min(dp[l + 1][r], dp[l][r - 1]);
                }
            }
        }

        return dp[0][n - 1] <= k;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.isValidPalindrome("abcdeca", 2)); // true
        System.out.println(sol.isValidPalindrome("abbababa", 1)); // true
    }
}
```

---

#### 4.2 Python (Recursion + LRU Cache)

```python
from functools import lru_cache

class Solution:
    def isValidPalindrome(self, s: str, k: int) -> bool:
        n = len(s)

        @lru_cache(None)
        def dp(l: int, r: int) -> int:
            if l >= r:
                return 0
            if s[l] == s[r]:
                return dp(l + 1, r - 1)
            return 1 + min(dp(l + 1, r), dp(l, r - 1))

        return dp(0, n - 1) <= k

# Demo
sol = Solution()
print(sol.isValidPalindrome("abcdeca", 2))   # True
print(sol.isValidPalindrome("abbababa", 1)) # True
```

---

#### 4.3 C++ (Bottom‑Up DP)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isValidPalindrome(string s, int k) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));

        // substrings of length 1 are already palindrome
        for (int i = 0; i < n; ++i) dp[i][i] = 0;

        for (int len = 2; len <= n; ++len) {
            for (int l = 0; l + len - 1 < n; ++l) {
                int r = l + len - 1;
                if (s[l] == s[r]) {
                    dp[l][r] = dp[l + 1][r - 1];
                } else {
                    dp[l][r] = 1 + min(dp[l + 1][r], dp[l][r - 1]);
                }
            }
        }
        return dp[0][n - 1] <= k;
    }
};

int main() {
    Solution sol;
    cout << boolalpha;
    cout << sol.isValidPalindrome("abcdeca", 2) << endl;   // true
    cout << sol.isValidPalindrome("abbababa", 1) << endl; // true
}
```

---

### 5. Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Forgetting `dp[i+1][j-1]` when `s[i]==s[j]` | Keep the recurrence separate: `dp[l][r] = dp[l+1][r-1]` |
| Index out‑of‑range for `l+1` or `r-1` | Initialize base cases (`dp[i][i]` and `dp[i][i-1]`) or use boundary checks |
| Using `n` as a negative number | Always take `s.length()` not `-1` |
| Using `int` when string length can reach 1000 | `int` is fine, but remember `dp[l][r]` can be up to 1000 |

---

### 6. Alternative Approaches

| Approach | Complexity | When to Use |
|----------|------------|-------------|
| **Longest Palindromic Subsequence** (`LPS`) via DP | `O(n²)` | If you’re more comfortable with LPS |
| **Two‑Pointer + Memoization** | `O(n²)` | Cleaner recursive code, but be careful with stack depth |
| **Greedy** (remove mismatch from left or right) | `O(n)` | Wrong in general – does not always find optimal deletions |

---

### 7. How This Helps You Land a Job

1. **Interview Warm‑Up** – LeetCode 1216 is a canonical “hard” DP problem that many tech‑companies use in interviews (Google, Amazon, Meta, etc.). Demonstrating a clean, efficient solution shows you understand DP fundamentals.
2. **Coding Style** – The Java, Python, and C++ snippets above are production‑ready: clear variable names, no magic numbers, comments, and a `main`/`demo` for quick testing.
3. **Time & Space Efficiency** – You’ll impress interviewers by explaining why `O(n²)` is optimal for `n ≤ 1000` and how you could optimize memory if needed.
4. **Discussion Ready** – The blog article covers edge cases, pitfalls, and alternative solutions, giving you talking points for behavioral or technical rounds.

---

### 8. SEO Keywords (to rank in search results)

* `Valid Palindrome III solution`
* `LeetCode 1216 solution`
* `Java DP palindrome deletion`
* `Python LCS palindrome`
* `C++ LeetCode interview question`
* `dynamic programming interview questions`
* `how to solve Valid Palindrome III`
* `job interview coding problem`

---

### 9. Final Takeaway

Valid Palindrome III is a classic DP exercise.  
- **Bottom‑up** or **top‑down with memoisation** gives an optimal `O(n²)` solution.  
- The code is simple enough for a quick interview demo, yet powerful enough to impress hiring managers.

Download the snippets, practice on LeetCode, and you’ll be ready to ace the question and land that dream job! 🚀

---