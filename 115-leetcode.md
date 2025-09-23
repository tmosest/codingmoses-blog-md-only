---
title: LeetCode 115. Distinct Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  LeetCode 115 – Distinct Subsequences  
**Hard | Dynamic Programming**  

| Language | Code |
|----------|------|
| **Java** | **Solution.java** |
| **Python** | **solution.py** |
| **C++** | **solution.cpp** |

> All three solutions run in **O(n · m)** time and use **O(min(n, m))** memory (the 2‑D table is optional – a 1‑D rolling array is shown in the Java implementation).  
> The answer is guaranteed to fit into a 32‑bit signed integer.

---

### 1. Java – 1‑D DP (O(1) extra space)

```java
// Solution.java
public class Solution {
    /**
     * Count distinct subsequences of s that equal t.
     * @param s source string
     * @param t target string
     * @return number of distinct subsequences
     */
    public int numDistinct(String s, String t) {
        int n = s.length(), m = t.length();

        // If t is longer than s, impossible to form t
        if (m > n) return 0;

        // dp[j] will hold the number of ways to form t[0..j-1]
        // from the processed prefix of s
        int[] dp = new int[m + 1];
        dp[0] = 1;                           // empty t

        for (int i = 1; i <= n; i++) {       // iterate over s
            char sc = s.charAt(i - 1);
            // iterate backwards so that dp[j-1] refers to the previous row
            for (int j = m; j >= 1; j--) {
                if (sc == t.charAt(j - 1)) {
                    dp[j] += dp[j - 1];
                }
            }
        }
        return dp[m];
    }

    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.numDistinct("rabbbit", "rabbit")); // 3
        System.out.println(sol.numDistinct("babgbag", "bag"));    // 5
    }
}
```

**Why 1‑D works?**  
The transition `dp[j] = dp[j] + dp[j‑1]` uses the value from the previous row (`dp[j]` before the update) and the value from the previous column (`dp[j‑1]`).  
By looping `j` from `m` down to `1`, the “previous row” value is still untouched when it is needed, so a single array suffices.

---

### 2. Python – 1‑D DP (compact & readable)

```python
# solution.py
class Solution:
    def numDistinct(self, s: str, t: str) -> int:
        n, m = len(s), len(t)
        if m > n:                      # early exit
            return 0

        dp = [0] * (m + 1)
        dp[0] = 1

        for sc in s:
            for j in range(m, 0, -1):
                if sc == t[j - 1]:
                    dp[j] += dp[j - 1]
        return dp[m]

if __name__ == "__main__":
    sol = Solution()
    print(sol.numDistinct("rabbbit", "rabbit"))   # 3
    print(sol.numDistinct("babgbag", "bag"))      # 5
```

Python’s `range(m, 0, -1)` gives a reverse iterator, keeping the logic identical to Java.

---

### 3. C++ – 1‑D DP (fast & type‑safe)

```cpp
// solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size(), m = t.size();
        if (m > n) return 0;

        vector<int> dp(m + 1, 0);
        dp[0] = 1;                    // empty t

        for (char sc : s) {
            for (int j = m; j >= 1; --j) {
                if (sc == t[j - 1]) {
                    dp[j] += dp[j - 1];
                }
            }
        }
        return dp[m];
    }
};

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    Solution sol;
    cout << sol.numDistinct("rabbbit", "rabbit") << '\n';   // 3
    cout << sol.numDistinct("babgbag", "bag") << '\n';      // 5
    return 0;
}
```

> `vector<int>` guarantees 32‑bit signed arithmetic and `range`‑style loops keep the code minimal.

---

## 2️⃣  Blog Article – “Cracking LeetCode 115: Distinct Subsequences – The Good, the Bad, and the Ugly”  

> **Target Audience:**  
> * Software‑engineering interview candidates*  
> * Recruiters looking for solid DP knowledge*  
> * Students polishing their coding‑interview toolkit*

> **SEO Keywords:**  
> `Distinct Subsequences`, `Leetcode 115`, `dynamic programming`, `coding interview`, `job interview`, `Java solution`, `Python solution`, `C++ solution`, `algorithmic problem solving`, `job search coding`, `software engineer interview`, `coding challenge`, `competitive programming`.

---

# Cracking LeetCode 115: Distinct Subsequences – The Good, the Bad, and the Ugly

> *Dynamic Programming (DP) × String Manipulation*  
> *Why it matters for your next coding interview*  

---

## 📌 What Is “Distinct Subsequences”?

In this classic LeetCode problem you’re given two strings:  
* **`s`** – the source string,  
* **`t`** – the target string.  

Your task is to count **how many distinct ways** you can delete characters from **`s`** so that the remaining string equals **`t`**.  
Every position in `s` can be either **used** (if it matches the current character in `t`) or **skipped**. The order of characters in `t` must be preserved, but they don’t need to be contiguous in `s`.  

---

## ⚙️ The Classic DP Recurrence

Let  

`dp[i][j]` = number of ways to form the first **`j`** characters of `t` using the first **`i`** characters of `s`.

Transition:

* If `s[i‑1] == t[j‑1]`:  
  `dp[i][j] = dp[i‑1][j‑1]`  (pick this char)  
  `+ dp[i‑1][j]`            (skip it)
* Else:  
  `dp[i][j] = dp[i‑1][j]`  (cannot pick)

Base cases:

| `j == 0` | → `dp[i][0] = 1`  (empty `t` can always be formed by taking nothing) |
| `i == 0` | → `dp[0][j] = 0` for `j ≥ 1` (non‑empty `t` cannot be formed from an empty `s`) |

Answer: **`dp[n][m]`** where `n = |s|` and `m = |t|`.

---

## 🧮 Complexity

| Approach | Time | Extra Space |
|----------|------|-------------|
| 2‑D DP (full table) | **O(n · m)** | **O(n · m)** |
| 1‑D rolling array | **O(n · m)** | **O(min(n, m))** |

> In all cases, the algorithm is linear in the product of string lengths and uses only 32‑bit integers because the problem guarantees the answer fits in a signed `int`.

---

## 🚀 Why This Problem Is a **Must‑Know** Interview Question

1. **String DP mastery** – Interviewers often ask variations: “Number of ways to form a string from another?” or “Copy a file system.”  
2. **Space optimisation** – The 1‑D solution demonstrates a classic DP trick (reverse iteration) that keeps memory low.  
3. **Edge‑case awareness** – Early exit if `|t| > |s|` shows careful boundary checking, a trait interviewers look for.  
4. **Language agnosticism** – You can deliver the solution in **Java, Python, or C++** – all three languages are frequently used in technical interviews.  

---

## 🎯 How to Use These Solutions in Your Interview

| Step | What to Show |
|------|--------------|
| **Explain the DP table** | Show `dp[i][j]` and the recurrence. |
| **Show the 1‑D optimisation** | Explain why iterating backwards preserves “previous row” values. |
| **Time‑Space analysis** |  Provide Big‑O notation and discuss why 32‑bit fits. |
| **Mention edge cases** | Empty strings, `t` longer than `s`. |
| **Talk about testing** | Provide sample tests (`rabbbit → rabbit` and `babgbag → bag`). |
| **Optional** | Share a small unit test or `main` routine for each language. |

---

## 🎉 The Good, The Bad, and The Ugly of the DP Approach

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | Transition `dp[j] += dp[j-1]` is intuitive once you think of “pick” vs “skip.” | 2‑D table looks clean but can hide the 1‑D optimisation. | Forgetting to iterate `j` backwards leads to **wrong** answers—this subtle bug is often the culprit for failing the test. |
| **Performance** | O(n · m) is optimal; no hidden quadratic overhead. | None – the algorithm is proven optimal for this problem. | In languages with slow string access (e.g., old C), you might need to cache `s` and `t` chars to avoid repeated indexing. |
| **Memory** | 1‑D uses **O(min(n, m))** memory – ideal for interview constraints. | 2‑D is easier to read but doubles memory usage. | Using 64‑bit integers when not needed can waste space; but safe if the answer may exceed 32‑bit. |
| **Edge‑cases** | Empty `t` always yields 1, empty `s` yields 0 for non‑empty `t`. | Early exit for `m > n` speeds up trivial cases. | Forgetting to set `dp[0] = 1` leads to zero counts. |
| **Readability** | Variable names `dp`, `sc` are clear; comments explain the loop direction. | Python’s `range(m, 0, -1)` hides the reverse loop. | C++ `vector<int>` ensures type safety but needs `#include <bits/stdc++.h>` for brevity. |

---

## 📢 SEO‑Friendly Take‑away

> *If you’re prepping for a software‑engineering interview, “Distinct Subsequences” is a problem you’ll almost certainly encounter. Mastering it demonstrates your grasp of dynamic programming, string manipulation, and space optimisation. Use the Java, Python, and C++ snippets above as a reference in your practice repo, and remember to highlight the algorithmic insight when you explain it to interviewers.*

---

## 🔗 Further Reading

- [LeetCode 115 – Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/)
- [Dynamic Programming: From Theory to Practice](https://www.geeksforgeeks.org/dynamic-programming/)
- [Interview Questions You Must Master for 2024](https://example.com/interview-prep-2024)

> **Good luck on your coding journey!** 🚀  

---