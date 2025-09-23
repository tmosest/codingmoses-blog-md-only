---
title: LeetCode 97. Interleaving String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Interleaving String – LeetCode 97  
**The Good, the Bad, and the Ugly – A Complete Guide for Your Next Coding Interview**

> **SEO Title**: Interleaving String LeetCode 97 – Dynamic Programming Solution, 1D DP, Recursion, Interview Tips  
> **Meta Description**: Master LeetCode 97 “Interleaving String” with a 1‑D dynamic programming solution. Learn the pros/cons, common pitfalls, and interview‑ready explanation. Boost your coding interview skills!

---

## Table of Contents  

| Section | Link |
|---------|------|
| Problem Overview | #problem-overview |
| Good: Optimal DP | #good |
| Bad: Naïve Recursion | #bad |
| Ugly: Over‑engineering | #ugly |
| Follow‑Up: O(min(m,n)) Space | #follow-up |
| Interview Strategy | #interview-strategy |
| Code Snippets | #code |
| Testing Tips | #testing |
| Takeaway | #takeaway |

---

## Problem Overview

**LeetCode #97 – Interleaving String**  
> **Given** three strings `s1`, `s2`, and `s3`, determine if `s3` can be formed by *interleaving* `s1` and `s2`.  
> *Interleaving* means you can merge the two strings while preserving the relative order of characters from each original string.

> **Examples**  
> ```text
> s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac" → true
> s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc" → false
> ```

**Why it matters in interviews**  
- Classic *dynamic programming* (DP) problem.  
- Demonstrates ability to reason about state transitions and space/time trade‑offs.  
- Commonly appears in coding interview questions for positions that value algorithmic thinking (Backend Engineer, Data Scientist, etc.).

---

## The Good – Optimal 1‑D DP Solution

### Why 1‑D DP?

- **Time complexity**: `O(m · n)` where `m = len(s1)`, `n = len(s2)` – the best possible for this problem.  
- **Space complexity**: `O(min(m, n))` – we only keep one row of the DP table.  
- **Simplicity**: Easier to explain and implement correctly.

### State Definition

```
dp[j] = true  ⇔  the prefix s3[0 … i + j - 1] can be formed
                by interleaving s1[0 … i - 1] and s2[0 … j - 1]
```

`i` iterates over characters of `s1`, `j` over `s2`.

### Transition

```
dp[j] = (dp[j]      and s1[i-1] == s3[i+j-1])   // take from s1
     OR (dp[j-1]    and s2[j-1] == s3[i+j-1])   // take from s2
```

### Pseudocode

```
if m + n != len(s3): return False
if m < n: swap s1 and s2   // keep the smaller string in s2
dp[0] = True
for j in 1 … n:
    dp[j] = dp[j-1] and s2[j-1] == s3[j-1]
for i in 1 … m:
    dp[0] = dp[0] and s1[i-1] == s3[i-1]
    for j in 1 … n:
        dp[j] = (dp[j] and s1[i-1] == s3[i+j-1]) or
                (dp[j-1] and s2[j-1] == s3[i+j-1])
return dp[n]
```

---

## The Bad – Naïve Recursion (TLE)

A straight‑forward recursive approach that tries both possibilities at every step can explore up to `2^(m+n)` states.  
While it’s conceptually simple, it easily exceeds time limits on large inputs.

**Key pitfalls**  
- Exponential time (`O(2^(m+n))`).  
- Stack overflow for long strings.  
- No memoization → repeated work.

---

## The Ugly – Over‑engineering (Multiple HashMaps, Custom Classes)

Some candidates create custom `State` objects, use hash tables with composite keys, or attempt to compress the DP table using bit‑operations.  
Although technically correct, this often:

- Obscures the algorithmic idea.  
- Makes it harder for interviewers to follow.  
- Increases the risk of bugs (off‑by‑one errors, incorrect key hashing).

---

## Follow‑Up: O(min(m, n)) Space

The 1‑D DP already achieves `O(min(m, n))` space.  
If you want to be extra concise, you can always store the smaller of the two strings in the DP array to save even more memory.

---

## Interview Strategy

1. **Ask clarifying questions**  
   - Confirm whether interleaving is *strictly* preserving relative order.  
   - Clarify if `s1` and `s2` can be empty.

2. **Early exit**  
   ```python
   if len(s1) + len(s2) != len(s3):
       return False
   ```

3. **Explain DP table dimensions**  
   - `dp[i][j]` or one‑dimensional `dp[j]`.  
   - Visualize as a grid where each cell depends on the top and left neighbors.

4. **Complexity**  
   - Time: `O(m · n)`  
   - Space: `O(min(m, n))` (optimal)

5. **Edge cases**  
   - Empty strings.  
   - All characters equal in both strings.

6. **Testing**  
   - Provide unit tests or a small `main` method that runs sample cases.

---

## Full Code Snippets

Below are clean, production‑ready implementations in three popular languages.  
Feel free to copy, paste, and run.

### Python 3 – 1‑D DP

```python
class Solution:
    def isInterleave(self, s1: str, s2: str, s3: str) -> bool:
        m, n, l = len(s1), len(s2), len(s3)
        if m + n != l:
            return False

        # Ensure s2 is the shorter string to keep the DP array minimal
        if m < n:
            return self.isInterleave(s2, s1, s3)

        dp = [False] * (n + 1)
        dp[0] = True

        # Initialize first row (i = 0)
        for j in range(1, n + 1):
            dp[j] = dp[j - 1] and s2[j - 1] == s3[j - 1]

        # Iterate over s1
        for i in range(1, m + 1):
            dp[0] = dp[0] and s1[i - 1] == s3[i - 1]
            for j in range(1, n + 1):
                dp[j] = (dp[j] and s1[i - 1] == s3[i + j - 1]) or \
                        (dp[j - 1] and s2[j - 1] == s3[i + j - 1])

        return dp[n]
```

### Java – 1‑D DP

```java
public class Solution {
    public boolean isInterleave(String s1, String s2, String s3) {
        int m = s1.length(), n = s2.length(), l = s3.length();
        if (m + n != l) return false;

        // Keep the smaller string in s2 for minimal DP array
        if (m < n) return isInterleave(s2, s1, s3);

        boolean[] dp = new boolean[n + 1];
        dp[0] = true;

        // Initialize first row
        for (int j = 1; j <= n; j++) {
            dp[j] = dp[j - 1] && s2.charAt(j - 1) == s3.charAt(j - 1);
        }

        // Iterate over s1
        for (int i = 1; i <= m; i++) {
            dp[0] = dp[0] && s1.charAt(i - 1) == s3.charAt(i - 1);
            for (int j = 1; j <= n; j++) {
                dp[j] = (dp[j] && s1.charAt(i - 1) == s3.charAt(i + j - 1)) ||
                        (dp[j - 1] && s2.charAt(j - 1) == s3.charAt(i + j - 1));
            }
        }
        return dp[n];
    }
}
```

### C++ – 1‑D DP (GNU‑C++17)

```cpp
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        int m = s1.size(), n = s2.size(), l = s3.size();
        if (m + n != l) return false;

        // Keep s2 shorter
        if (m < n) return isInterleave(s2, s1, s3);

        vector<bool> dp(n + 1, false);
        dp[0] = true;

        // First row
        for (int j = 1; j <= n; ++j) {
            dp[j] = dp[j - 1] && (s2[j - 1] == s3[j - 1]);
        }

        for (int i = 1; i <= m; ++i) {
            dp[0] = dp[0] && (s1[i - 1] == s3[i - 1]);
            for (int j = 1; j <= n; ++j) {
                dp[j] = (dp[j] && (s1[i - 1] == s3[i + j - 1])) ||
                        (dp[j - 1] && (s2[j - 1] == s3[i + j - 1]));
            }
        }
        return dp[n];
    }
};
```

---

## Testing Tips

1. **Write unit tests**  
   ```java
   public static void main(String[] args) {
       Solution sol = new Solution();
       System.out.println(sol.isInterleave("aabcc", "dbbca", "aadbbcbcac")); // true
       System.out.println(sol.isInterleave("aabcc", "dbbca", "aadbbbaccc")); // false
   }
   ```

2. **Edge‑case scenarios**  
   - `s1 = ""`, `s2 = ""`, `s3 = ""` → `true`  
   - `s1 = "abc"`, `s2 = ""`, `s3 = "abc"` → `true`  
   - `s1 = "abc"`, `s2 = "def"`, `s3 = "abdcef"` → `true`

3. **Stress test**  
   ```python
   import random, string
   s1 = ''.join(random.choice('abc') for _ in range(2000))
   s2 = ''.join(random.choice('abc') for _ in range(2000))
   # Build a valid s3 by shuffling s1 and s2 while preserving order
   s3 = ''.join(random.sample(s1 + s2, len(s1)+len(s2)))
   ```

   Run the solution to confirm it finishes in milliseconds.

---

## Takeaway

- **Use the 1‑D DP**: Fast, memory‑efficient, and interview‑friendly.  
- **Avoid pure recursion** unless you add memoization (`O(m·n)` time).  
- **Never over‑engineer**: Simplicity helps both you and your interviewer.  
- **Know the follow‑up**: Being able to explain `O(min(m,n))` space shows you understand optimization.

Mastering this problem gives you a solid foundation for *any* interview that tests DP skills—whether you’re applying to a Backend Engineer, a Data Engineer, or a Machine Learning role.

Happy coding and good luck on your next interview! 🚀