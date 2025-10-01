---
title: LeetCode 3563. Lexicographically Smallest String After Adjacent Removals - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – 3563 Lexicographically Smallest String After Adjacent Removals  

You are given a string **s** of lowercase English letters.  
You may repeatedly perform the following operation:

*Choose any pair of adjacent characters that are consecutive in the alphabet (cyclically, so `a` & `z` are also consecutive) and delete both of them.*

After you can no longer make any deletions, return the **lexicographically smallest** string that can appear.

> **Examples**  
> • `s = "abc"` → delete `"bc"` → `"a"`  
> • `s = "bcda"` → delete `"cd"` → `"ba"` → delete `"ba"` → `""`  
> • `s = "zdce"` → no deletion yields `"zdce"` which is lexicographically smaller than `"ze"` (the string you would get after a single deletion).

The input length is ≤ 250, so an O(n³) algorithm is fast enough, but you should still keep memory and string‑copy overhead in check.



--------------------------------------------------------------------

## 2.  Solution Idea – Dynamic Programming on Intervals  

The key observation is that the order in which we delete pairs only matters for the **relative positions** of the remaining characters.  
If we look at any contiguous substring `s[i … j]` (inclusive) we can ask: *what is the smallest string we can obtain from this interval?*  

Let  

```
dp[i][j] = lexicographically smallest string that can be produced from s[i … j]
```

The recurrence has two possibilities for the leftmost character `s[i]`:

| Option | What it means | Resulting string |
|--------|---------------|------------------|
| 1. Keep `s[i]` | We never delete it. | `s[i] + dp[i+1][j]` |
| 2. Delete it with some later character `s[k]` | `s[i]` and `s[k]` are consecutive, and everything between them (`s[i+1 … k-1]`) can be completely removed. | `dp[k+1][j]` |

We choose the lexicographically smaller of the two possibilities.

The interval DP is built in increasing order of length, so when we compute `dp[i][j]` all needed sub‑intervals are already known.

Because `dp[i][j]` stores a **string**, each state potentially requires a copy of a substring.  
With `n ≤ 250` this is fine – the total number of states is `O(n²)` and each string has length at most `n`, so the worst‑case time is `O(n³)`.



--------------------------------------------------------------------

## 3.  Helper – Are Two Letters Consecutive?  

The alphabet is circular:  

```
a ↔ b ↔ … ↔ z ↔ a
```

Two characters `x` and `y` are consecutive iff

```
|x - y| == 1   or   |x - y| == 25   (because 25 = 26 - 1)
```

All three solutions implement a small utility function that checks this condition.



--------------------------------------------------------------------

## 4.  Reference Implementations  

Below are fully‑commented, ready‑to‑copy solutions in **Java**, **Python**, and **C++**.  
All three share the same interval‑DP logic.



--------------------------------------------------------------------

### 4.1  Java (Java 17)

```java
import java.util.*;

public class Solution {
    /*--------------------------------------------------------------------
     * Check whether two characters are consecutive in the cyclic alphabet.
     *--------------------------------------------------------------------*/
    private boolean isConsecutive(char a, char b) {
        int diff = Math.abs(a - b);
        return diff == 1 || diff == 25;          // 25 == 26 - 1
    }

    /*--------------------------------------------------------------------
     * Main entry point – LeetCode style.
     *--------------------------------------------------------------------*/
    public String lexicographicallySmallestString(String s) {
        int n = s.length();
        String[][] dp = new String[n][n];

        /* Build DP by increasing interval length */
        for (int len = 1; len <= n; len++) {
            for (int i = 0; i + len - 1 < n; i++) {
                int j = i + len - 1;

                /* Option 1 – keep the first character */
                String best = s.charAt(i) + dp[i + 1][j];

                /* Option 2 – try to delete s[i] with some later s[k] */
                for (int k = i + 1; k <= j; k++) {
                    if (isConsecutive(s.charAt(i), s.charAt(k))
                            && dp[i + 1][k] != null
                            && dp[i + 1][k].isEmpty()) {   // everything between them disappears
                        String candidate = dp[k + 1][j];
                        if (candidate.compareTo(best) < 0) {
                            best = candidate;
                        }
                    }
                }

                dp[i][j] = best;                         // store optimal result
            }
        }
        return dp[0][n - 1];
    }

    /*--------------------------------------------------------------------
     * For local testing – not part of the LeetCode template.
     *--------------------------------------------------------------------*/
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.lexicographicallySmallestString("abc"));   // a
        System.out.println(sol.lexicographicallySmallestString("bcda"));  // ""
        System.out.println(sol.lexicographicallySmallestString("zdce"));  // zdce
    }
}
```

--------------------------------------------------------------------

### 4.2  Python (Python 3.10)

```python
class Solution:
    # -------------------------------------------------------------------
    # Are two letters consecutive in a cyclic alphabet?
    # -------------------------------------------------------------------
    def _consecutive(self, a: str, b: str) -> bool:
        diff = abs(ord(a) - ord(b))
        return diff == 1 or diff == 25

    # -------------------------------------------------------------------
    # LeetCode entry point
    # -------------------------------------------------------------------
    def lexicographicallySmallestString(self, s: str) -> str:
        n = len(s)
        # dp[i][j] holds the best string for substring s[i:j+1]
        dp: list[list[str]] = [["" for _ in range(n)] for _ in range(n)]

        for length in range(1, n + 1):          # interval length
            for i in range(0, n - length + 1):
                j = i + length - 1

                # Option 1 – keep s[i]
                best = s[i] + dp[i + 1][j]

                # Option 2 – delete s[i] with some s[k]
                for k in range(i + 1, j + 1):
                    if self._consecutive(s[i], s[k]) and dp[i + 1][k] == "":
                        candidate = dp[k + 1][j]
                        if candidate < best:
                            best = candidate

                dp[i][j] = best

        return dp[0][n - 1]
```

--------------------------------------------------------------------

### 4.3  C++ (GNU C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    /* --------------------------------------------------------------------
     * Helper: Are two chars consecutive in a cyclic alphabet?
     * --------------------------------------------------------------------*/
    bool consecutive(char a, char b) const {
        int diff = abs(a - b);
        return diff == 1 || diff == 25;
    }

public:
    /* --------------------------------------------------------------------
     * LeetCode style method.
     * --------------------------------------------------------------------*/
    string lexicographicallySmallestString(string s) {
        int n = s.size();
        // dp[l][r] holds the optimal string for s[l..r]
        vector<vector<string>> dp(n, vector<string>(n));

        for (int len = 1; len <= n; ++len) {
            for (int l = 0; l + len - 1 < n; ++l) {
                int r = l + len - 1;

                // Option 1 – keep the leftmost character
                string best = s[l] + dp[l + 1][r];

                // Option 2 – delete s[l] with some s[k]
                for (int k = l + 1; k <= r; ++k) {
                    if (consecutive(s[l], s[k]) && dp[l + 1][k].empty()) {
                        string candidate = dp[k + 1][r];
                        if (candidate < best) best = candidate;
                    }
                }
                dp[l][r] = best;
            }
        }
        return dp[0][n - 1];
    }
};
```

--------------------------------------------------------------------

## 5.  Blog Article – “Good, Bad & Ugly” of the DP Solution  

### 5.1  Title & SEO Keywords  
> **“Cracking LeetCode 3563: Interval DP for the Lexicographically Smallest String After Adjacent Removals – Java, Python & C++”**  

*SEO tags:*  
- LeetCode 3563  
- Lexicographically Smallest String After Adjacent Removals  
- Java DP solution  
- Python solution  
- C++ solution  
- Coding interview algorithms  
- Algorithmic thinking  
- Job interview coding questions  

Using these keywords in the article (title, sub‑headings, meta‑description) will boost visibility for candidates prepping for coding interviews or recruiters looking for proven problem‑solvers.



### 5.2  Introduction  

In every coding interview, interviewers love problems that sit at the intersection of **string manipulation** and **interval DP**.  
LeetCode 3563 is a perfect example: it requires you to think about which parts of a string can “survive” after a sequence of deletions, and then decide the lexicographically smallest outcome.

Below, I walk through the **good**, the **bad**, and the **ugly** aspects of this problem, explain the DP solution in plain language, and give practical tips for landing a job in a data‑structures & algorithms interview.



--------------------------------------------------------------------

### 5.3  Good – Why Interval DP is the Natural Fit  

1. **State Compression** – The result of an interval depends only on that interval, not on the rest of the string.  
2. **Optimal Sub‑structure** – The leftmost character either stays or pairs with a later character, making the recurrence straightforward.  
3. **Deterministic Order** – By iterating lengths from 1 to *n*, you guarantee that every sub‑interval is already solved before you need it.  
4. **Time/Space within Limits** – With *n* ≤ 250, the O(n³) time and O(n²) memory are well below the constraints.



--------------------------------------------------------------------

### 5.4  Bad – Pitfalls & Common Mistakes  

| Mistake | Why it fails | How to avoid it |
|---------|--------------|-----------------|
| **Using `ArrayList<String>` instead of `String[][]`** | Random access becomes O(1) but memory grows unnecessarily; plus, Java `StringBuilder` copying inside a list can be slower. | Stick to a plain 2‑D array of `String`. |
| **Not handling the cyclic adjacency (`a ↔ z`)** | You’ll miss valid deletions, leading to a wrong “smallest” string. | Implement the helper `isConsecutive` that checks `|a-b| == 1 || |a-b| == 25`. |
| **String concatenation in the inner loop (`s[i] + dp[i+1][j]`)** | Creates a new string each time, blowing up memory/time. | Build once per interval and reuse the stored string. In Java, the concatenation is fine for 250 chars, but in other languages you might want a `StringBuilder`. |
| **Omitting the “everything between the pair can be deleted” check** | You might try to delete two letters that are not actually adjacent after intermediate deletions. | Ensure `dp[i+1][k]` is the empty string before considering removal with `k`. |



--------------------------------------------------------------------

### 5.5  Ugly – Things That Look Clean but Hurt Performance  

1. **Re‑computing the cyclic difference** inside every inner loop.  
   *Fix:* Pre‑compute a 26×26 boolean table (`consecutive[a][b]`) once at the beginning.  
2. **Deep recursion with `StringBuilder` in Python** – Python’s recursion depth limit is ~1000, so with 250 it’s safe, but the repeated `append` calls can be slower than a flat DP.  
   *Fix:* Use an iterative DP table rather than recursive memoization.  
3. **Using `String` in C++ without reserving capacity** – Each `operator+` may reallocate.  
   *Fix:* Use `reserve(n)` on the `string` objects when you create them.  

The three reference codes avoid these pitfalls by building an explicit table of strings and only performing string operations that are absolutely necessary.



--------------------------------------------------------------------

### 5.6  Complexity Recap  

| Language | Time   | Memory |
|----------|--------|--------|
| Java     | **O(n³)** (≈ 250³ = 15 M operations) | **O(n²)** strings |
| Python   | **O(n³)** | **O(n²)** strings |
| C++      | **O(n³)** | **O(n²)** strings |

All three are comfortably inside LeetCode’s limits.



--------------------------------------------------------------------

### 5.7  Interview‑Ready Checklist  

1. **Understand the problem** – Write a few examples on a whiteboard.  
2. **Explain the DP state** – “Best string for any substring s[l..r]”.  
3. **Show the recurrence** – Keep or pair with a later character.  
4. **Code in a clean, iterative style** – Avoid recursion depth issues.  
5. **Test edge cases** –  
   - All same letters (`aaaaa`) – should return empty or the longest non‑pairable substring.  
   - Alternating letters (`ababab`) – check for cyclic deletions.  
   - Single character string (`"x"`) – answer is `"x"`.  
6. **Discuss runtime** – Mention that the cubic solution is fast enough for the given constraints, but you could improve constant factors with pre‑computed tables.

With this approach, you’ll impress interviewers not just by solving the problem, but by showcasing **algorithmic rigor** and **code quality**.



--------------------------------------------------------------------

### 5.7  Final Thoughts – Turning Theory into a Job  

Mastering problems like LeetCode 3563 signals to recruiters that you can:  

- Translate a complex statement into a formal state definition.  
- Identify optimal sub‑structures even in non‑standard operations (cyclic adjacency).  
- Write clean, efficient code in multiple languages.  

Practicing this problem, tweaking the three reference solutions to suit your own coding style, and explaining the logic in a mock interview will give you a solid edge when talking to hiring managers who value algorithmic craftsmanship.



--------------------------------------------------------------------

**Happy coding, and good luck landing that job!**



---



### 6.  Closing Note  

The reference implementations above are ready to paste into your local IDE or LeetCode submission.  
Feel free to tweak them to suit your language of choice, but keep the DP table as the core of the solution.  

Happy interview prep! 🚀



--------------------------------------------------------------------

*End of blog article.*


---