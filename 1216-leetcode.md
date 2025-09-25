---
title: LeetCode 1216. Valid Palindrome III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # ✅ LeetCode 1216 – Valid Palindrome III  
## Full‑stack solution in **Java, Python, and C++**  
> **TL;DR** –  
> *A string `s` is a *k‑palindrome* if you can delete at most `k` characters and the remainder becomes a palindrome.*  
> The optimal way to decide this is a classic **dynamic programming** problem that asks for the minimum number of deletions required to make `s` a palindrome.  
> If that minimum ≤ `k` → **true**, otherwise **false**.

---

## 1.  Problem Recap

| Item | Description |
|------|-------------|
| **Input** | `String s` (1 ≤ |s| ≤ 1000), `int k` (1 ≤ k ≤ |s|) |
| **Output** | `boolean` – whether `s` can become a palindrome by deleting ≤ k characters |
| **Examples** | `("abcdeca", 2)` → `true` (delete `'b'` and `'e'`) <br> `("abbababa", 1)` → `true` |

---

## 2.  Why Dynamic Programming?

The problem is a variant of the classic “Minimum Deletions to Form a Palindrome”:

1. If `s[l] == s[r]`, nothing needs to be deleted for the ends → solve the sub‑problem `l+1 … r-1`.  
2. If they differ, we have two options: delete `s[l]` or delete `s[r]`.  
   * `1 + min( solve(l+1, r), solve(l, r-1) )`  

Thus, the sub‑problem is **overlapping** – many calls compute the same `(l, r)` pair.  
**Memoization** or a **bottom‑up DP table** gives an `O(n²)` time & `O(n²)` space solution (or `O(n)` space with a clever 1‑D table).

---

## 3.  Code – Three Languages

Below are clean, production‑ready implementations in Java, Python, and C++.  
All share the same logic: memoized recursion that returns the minimum deletions needed for a substring.

---

### 3.1 Java

```java
import java.util.Arrays;

public class Solution {

    public boolean isValidPalindrome(String s, int k) {
        int n = s.length();
        int[][] dp = new int[n][n];
        for (int[] row : dp) Arrays.fill(row, -1);
        return minDeletions(s, 0, n - 1, dp) <= k;
    }

    private int minDeletions(String s, int l, int r, int[][] dp) {
        if (l >= r) return 0;               // base case
        if (dp[l][r] != -1) return dp[l][r]; // memoized

        if (s.charAt(l) == s.charAt(r)) {
            dp[l][r] = minDeletions(s, l + 1, r - 1, dp);
        } else {
            int delLeft  = minDeletions(s, l + 1, r, dp);
            int delRight = minDeletions(s, l, r - 1, dp);
            dp[l][r] = 1 + Math.min(delLeft, delRight);
        }
        return dp[l][r];
    }

    // --- driver for quick local testing ---
    public static void main(String[] args) {
        System.out.println(new Solution().isValidPalindrome("abcdeca", 2)); // true
        System.out.println(new Solution().isValidPalindrome("abbababa", 1)); // true
    }
}
```

---

### 3.2 Python

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


# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.isValidPalindrome("abcdeca", 2))   # True
    print(sol.isValidPalindrome("abbababa", 1))  # True
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isValidPalindrome(string s, int k) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n, -1));
        return minDel(s, 0, n - 1, dp) <= k;
    }

private:
    int minDel(const string &s, int l, int r, vector<vector<int>> &dp) {
        if (l >= r) return 0;
        if (dp[l][r] != -1) return dp[l][r];
        if (s[l] == s[r]) {
            dp[l][r] = minDel(s, l + 1, r - 1, dp);
        } else {
            int delLeft  = minDel(s, l + 1, r, dp);
            int delRight = minDel(s, l, r - 1, dp);
            dp[l][r] = 1 + min(delLeft, delRight);
        }
        return dp[l][r];
    }
};

// ---------- demo ----------
int main() {
    Solution sol;
    cout << boolalpha;
    cout << sol.isValidPalindrome("abcdeca", 2) << endl; // true
    cout << sol.isValidPalindrome("abbababa", 1) << endl; // true
    return 0;
}
```

---

## 4.  Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Memoized Recursion (all three languages) | **O(n²)** | **O(n²)** (DP table) | `n` = `s.length` (≤ 1000) |
| Bottom‑up DP | O(n²) | O(n²) | Same asymptotic but no recursion overhead |
| Optimized 1‑D DP | O(n²) | O(n) | Uses only the last row of the DP table |

Because `n ≤ 1000`, the `O(n²)` solution runs comfortably within limits (≈ 1 million states).

---

## 5.  Good, Bad, and Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Insight** | DP is natural & guarantees optimality | Over‑engineering: naive brute force would explode | Forgetting base cases → stack overflow or infinite recursion |
| **Readability** | Clear base case & memoization | Excessive inline comments can clutter | Using global variables for DP makes code fragile |
| **Performance** | `O(n²)` fast for 1000‑length strings | Recursion depth ≤ 1000 may hit stack limit in some languages | Forgetting to memoize leads to `O(2^n)` blow‑up |
| **Space** | `O(n²)` is fine for 1000 | DP table of 1 M ints is ~4 MB – acceptable | Not cleaning `dp` → memory leaks in large scale usage |
| **Edge Cases** | Empty string, `k` = 0, already palindrome | Not handling `l >= r` properly | Wrong memo key (e.g., mixing `l` & `r`) |
| **Language Nuances** | Java’s `Arrays.fill`, Python’s `lru_cache`, C++ vectors | Recursion limits in Python (`sys.setrecursionlimit`) | C++ stack overflow on deep recursion |

---

## 6.  SEO‑Optimized Blog Article (Excerpt)

> **Title:** *“Mastering LeetCode 1216 – Valid Palindrome III: Java, Python, & C++ DP Solutions”*  
> **Meta Description:**  
> Learn how to solve LeetCode’s “Valid Palindrome III” in Java, Python, and C++ using dynamic programming. This guide covers the algorithm, code snippets, edge cases, and interview tips. Perfect for coding interview prep.

### 6.1 Why This Problem Rocks Your Interview

- **Algorithmic Depth**: It tests *dynamic programming*, a staple for mid‑ to senior‑level roles.  
- **Edge‑Case Handling**: Requires careful base cases (empty string, single character).  
- **Language Flexibility**: Solvable in multiple languages, showcasing your cross‑platform coding skills.

### 6.2 Quick Win: The Minimum Deletion DP

Explain the recurrence and show the code in each language. Highlight how memoization saves the day.

### 6.3 Common Pitfalls & How to Avoid Them

- Forgetting to compare `l` and `r` (`l >= r`).
- Re‑computing sub‑problems – why caching matters.
- Stack depth in Python – use `sys.setrecursionlimit` if necessary.

### 6.4 Interview Hacks

- **Tell the story**: “We start from the ends of the string and shrink inward, either matching or deleting.”
- **Discuss complexity**: `O(n²)` time, `O(n²)` space – acceptable for 1000‑character strings.
- **Mention variations**: “If you needed space‑optimized, you can collapse the DP table to one dimension.”

### 6.5 Wrap‑Up & Resources

Provide links to LeetCode discussion threads, other DP problems, and a “Build your own test harness” section.

---

## 7.  Final Take‑away

*Valid Palindrome III* is a classic DP challenge that blends theory and practicality.  
The three code snippets above show how to nail it in **Java**, **Python**, and **C++** – perfect for your interview arsenal.  
Use the blog article to showcase your understanding, share on LinkedIn, or add to your portfolio – it’s a proven signal of problem‑solving chops that recruiters love.

Happy coding and good luck landing that dream job! 🚀