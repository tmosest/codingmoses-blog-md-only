---
title: LeetCode 44. Wildcard Matching - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Wildcard Matching – LeetCode 44  
> “Good, Bad & Ugly” – A deep‑dive into the classic pattern‑matching problem  

---

## 1. Problem Recap

You are given two strings:

| String | Meaning |
|--------|---------|
| `s` | the text you want to match |
| `p` | the pattern that can contain two wildcards |
| `?` | matches **exactly one** arbitrary character |
| `*` | matches **zero or more** arbitrary characters |

Your task: **return `true` iff the whole string `s` matches the whole pattern `p`.**

```
Examples
---------
s = "aa",  p = "a"   → false
s = "aa",  p = "*"   → true
s = "cb",  p = "?a"  → false
```

Constraints: `0 ≤ s.length, p.length ≤ 2000`, only lower‑case English letters and the two wildcards appear.

---

## 2. Why is this problem hard?

- `*` can match *any* length, so a naive recursion explores an exponential number of possibilities.
- You need to decide whether *every* character of `s` is consumed, which forces a global view of the match.
- The pattern may contain long runs of `*`, which are essentially “skip‑or‑consume” decisions.

The classic “dynamic programming” solution reduces the problem from exponential to quadratic time, but the DP itself can be written in several different ways.  
Below we present four styles:

| Approach | Complexity | Space | Pros / Cons |
|----------|------------|-------|-------------|
| **Recursive** (exponential) | `O(2^n)` | `O(n+m)` | Very clear, but impractical for big inputs. |
| **Memoized Recursion** | `O(n*m)` | `O(n+m) + O(n*m)` | Removes repetition, still recursion overhead. |
| **Bottom‑Up DP (tabulation)** | `O(n*m)` | `O(n*m)` | Easy to understand, works well for 2000×2000. |
| **Space‑Optimized DP** | `O(n*m)` | `O(n)` | Saves memory, still linear in pattern length. |

We’ll focus on the *Space‑Optimized DP* – the most production‑ready approach – but the code for the other variants is also provided (see the appendix).

---

## 3. Intuition & Step‑by‑Step Walk‑through

### 3.1  What does a DP cell mean?

Let `dp[i][j]` be `true` if the **prefix** of the text `s[0…i‑1]` matches the **prefix** of the pattern `p[0…j‑1]`.  
The final answer is `dp[s.length()][p.length()]`.

We build this table from the back (suffixes) because `*` refers to “consuming the next text character” or “moving to the next pattern character”.

```
   j = 0  1  2  3 ...
i = 0  X  X  X  X ...
   1  X  X  X  X ...
   2  X  X  X  X ...
```

### 3.2  Transition Rules

| Condition | Transition | Explanation |
|-----------|------------|-------------|
| `s[i-1] == p[j-1]` **or** `p[j-1] == '?'` | `dp[i][j] = dp[i-1][j-1]` | Consume one character from both strings. |
| `p[j-1] == '*'` | `dp[i][j] = dp[i-1][j] OR dp[i][j-1]` | *Consume* a character (`dp[i-1][j]`) **or* *skip* a character (`dp[i][j-1]`). |
| otherwise | `dp[i][j] = false` | Characters differ and pattern contains no wildcard. |

### 3.3  Handling the “empty text” row

For `i = 0` (empty `s`) we can only match if the pattern is also empty or consists entirely of `*`.  
So we initialise the first row:

```
dp[0][0] = true
dp[0][j] = dp[0][j-1]  if p[j-1] == '*'
```

This sets the base for the rest of the DP.

---

## 4. The Final, Space‑Optimised DP (C++ / Java / Python)

Below you’ll find a single‑source‑of‑truth implementation that:

1. Runs in `O(n*m)` time – well below the 4 million‑cell limit for 2000×2000.  
2. Uses only `O(p.length)` memory – a single `vector<bool>` per row in C++ / two `boolean[]` in Java / two `List[bool]` in Python.

> **Tip** – Before you compile, copy the file into your IDE, paste the `Solution` class, and run `main` with test cases.  
> **Caution** – The code uses `0‑based` indices, so be careful when you switch from the textbook description to implementation.

---

### C++ (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isMatch(string s, string p) {
        int n = s.size(), m = p.size();
        vector<bool> prev(m + 1, false), curr(m + 1, false);
        prev[0] = true;                                 // empty pattern matches empty text

        // initial row for empty text
        for (int j = 1; j <= m; ++j)
            if (p[j-1] == '*')
                prev[j] = prev[j-1];
            else
                break;                                  // first non-* stops the chain

        for (int i = 1; i <= n; ++i) {
            curr[0] = false;                            // empty pattern cannot match non‑empty text
            for (int j = 1; j <= m; ++j) {
                if (p[j-1] == '*')
                    curr[j] = curr[j-1] || prev[j];    // skip or consume
                else if (p[j-1] == '?' || p[j-1] == s[i-1])
                    curr[j] = prev[j-1];                // exact match
                else
                    curr[j] = false;                    // mismatch
            }
            prev.swap(curr);                            // move to next row
        }
        return prev[m];
    }
};
```

---

### Java (Java 17)

```java
class Solution {
    public boolean isMatch(String s, String p) {
        int n = s.length(), m = p.length();
        boolean[] prev = new boolean[m + 1];
        boolean[] curr = new boolean[m + 1];

        prev[0] = true;                                   // empty pattern vs empty text

        // initialise for empty text
        for (int j = 1; j <= m; ++j) {
            if (p.charAt(j - 1) == '*')
                prev[j] = prev[j - 1];
            else
                break;
        }

        for (int i = 1; i <= n; ++i) {
            curr[0] = false;                               // non‑empty text vs empty pattern
            for (int j = 1; j <= m; ++j) {
                char pc = p.charAt(j - 1);
                if (pc == '*') {
                    curr[j] = curr[j - 1] || prev[j];
                } else if (pc == '?' || pc == s.charAt(i - 1)) {
                    curr[j] = prev[j - 1];
                } else {
                    curr[j] = false;
                }
            }
            // swap references for next iteration
            boolean[] tmp = prev;
            prev = curr;
            curr = tmp;
        }
        return prev[m];
    }
}
```

---

### Python (Python 3.9+)

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        n, m = len(s), len(p)
        prev = [False] * (m + 1)
        curr = [False] * (m + 1)
        prev[0] = True

        # init for empty string s
        for j in range(1, m + 1):
            if p[j - 1] == '*':
                prev[j] = prev[j - 1]
            else:
                break

        for i in range(1, n + 1):
            curr[0] = False
            for j in range(1, m + 1):
                pc = p[j - 1]
                if pc == '*':
                    curr[j] = curr[j - 1] or prev[j]
                elif pc == '?' or pc == s[i - 1]:
                    curr[j] = prev[j - 1]
                else:
                    curr[j] = False
            prev, curr = curr, prev        # reuse the arrays

        return prev[m]
```

---

## 4. What’s “Good”, “Bad” & “Ugly” about the DP?

| Style | What’s Good | What’s Bad | Where it gets Ugly |
|-------|-------------|------------|--------------------|
| **Recursive** | *Clear, almost a straight translation of the problem*. | Exponential time, stack overflow for 2000‑character strings. | “It works on paper” but dies on real data. |
| **Memoized Recursion** | Avoids recomputation, still simple. | Recursion overhead, still harder to understand for newcomers. | The recursion stack can blow up for deeply nested patterns. |
| **Bottom‑Up DP** | **Deterministic, predictable** – no recursion, all indices are explicit. | 4 million booleans ≈ 4 MB – fine but not optimal. | Easy to debug but the 2‑D array is a bit heavy for tight‑memory interviews. |
| **Space‑Optimised DP** | **Lean** – only two rows of `bool` (≈ 2000 bits). | Still O(n*m) time, but the constant factor is lower. | Some people argue it “obscures” the logic a bit; but the code is short and very fast. |

> **Bottom‑Line:**  
> If you’re prepping for an interview, the *Space‑Optimised DP* is the sweet spot – it runs in the worst‑case limits of LeetCode 44, uses a tiny amount of memory, and is straightforward to write and test.

---

## 5. Bonus – A Greedy “One‑Pass” Alternative

It turns out there is a linear‑time, constant‑space solution that is **not** dynamic programming:

1. Keep two pointers: `i` for the text, `j` for the pattern.  
2. When you hit a `*`, remember its position (`starIdx`) and the position in `s` (`sTmpIdx`).  
3. If a mismatch occurs and you previously saw a `*`, rewind `j` to the character after the last `*` and increment `sTmpIdx`.  
4. If you hit the end of the pattern and `*` was not seen, return `false`.

This greedy method is concise, runs in `O(n+m)`, and uses `O(1)` memory.  
However, many people overlook it, and the DP solutions are often the “canonical” LeetCode answer.  

> **Use‑Case:** When you’re in a hurry or when the pattern is *known* to have at most a handful of wildcards.  

---

## 6. Summary & Take‑Home Points

| Question | Answer |
|----------|--------|
| **What is the fastest DP?** | Space‑Optimised DP – `O(n*m)` time, `O(p.length)` memory. |
| **Is the greedy solution viable?** | Yes – `O(n+m)` time, `O(1)` memory, but can be subtle. |
| **Why do interviewers prefer DP?** | It demonstrates understanding of state‑transitions and careful handling of edge‑cases. |
| **What’s the simplest to understand?** | Bottom‑Up DP – no recursion, explicit table. |

When you’re coding on LeetCode, remember:

- Initialise the “empty text” row properly.  
- Use `0‑based` indices throughout.  
- Always check the boundary conditions (`'*'` chain at the start).  

Happy coding, and good luck cracking LeetCode 44!

---

## 7. Appendix – Unused DP Variants (for reference)

> In the “Appendix” below, we provide the other DP variants that you can drop in to experiment.  
> They are **fully working** but are not the chosen final answer in the main section.

> **C++ – Full 2‑D DP**  
> **Java – Full 2‑D DP**  
> **Python – Full 2‑D DP**  
> (All three use a 2‑D `vector<vector<bool>>` / `boolean[][]` / `List[List[bool]]` respectively.)

Feel free to copy, paste, or even create a `main` to test them.  
They’re great for visualising the DP process.

---

## 7. Appendix – Full 2‑D DP for Learning Purposes

> **C++**

```cpp
vector<vector<bool>> dp(n+1, vector<bool>(m+1, false));
dp[0][0] = true;
for (int j = 1; j <= m; ++j)
    dp[0][j] = dp[0][j-1] && p[j-1] == '*';

for (int i = 1; i <= n; ++i)
    for (int j = 1; j <= m; ++j) {
        if (p[j-1] == '*')
            dp[i][j] = dp[i][j-1] || dp[i-1][j];
        else if (p[j-1] == '?' || p[j-1] == s[i-1])
            dp[i][j] = dp[i-1][j-1];
        else
            dp[i][j] = false;
    }
return dp[n][m];
```

> **Java**

```java
boolean[][] dp = new boolean[n+1][m+1];
dp[0][0] = true;
for (int j = 1; j <= m; ++j)
    dp[0][j] = dp[0][j-1] && p.charAt(j-1) == '*';

for (int i = 1; i <= n; ++i) {
    for (int j = 1; j <= m; ++j) {
        char pc = p.charAt(j-1);
        if (pc == '*')
            dp[i][j] = dp[i][j-1] || dp[i-1][j];
        else if (pc == '?' || pc == s.charAt(i-1))
            dp[i][j] = dp[i-1][j-1];
        else
            dp[i][j] = false;
    }
}
return dp[n][m];
```

> **Python**

```python
dp = [[False]*(m+1) for _ in range(n+1)]
dp[0][0] = True
for j in range(1, m+1):
    dp[0][j] = dp[0][j-1] and p[j-1] == '*'

for i in range(1, n+1):
    for j in range(1, m+1):
        pc = p[j-1]
        if pc == '*':
            dp[i][j] = dp[i][j-1] or dp[i-1][j]
        elif pc == '?' or pc == s[i-1]:
            dp[i][j] = dp[i-1][j-1]
        else:
            dp[i][j] = False
return dp[n][m]
```

---

### Final Words

> **If you’re reading this for interview preparation**, run the space‑optimised solution, add the greedy one to your “toolbox”, and memorize the transition rules.  
> **If you’re coding for a competition** or a production system that uses pattern matching, the greedy method is a great choice – it’s literally “O(1)” in memory and “O(n+m)” in time.

Good luck, and may your `*` always be greedy enough!

--- 

*This article blends theoretical explanation, concrete code, and interview‑style insights to help you master LeetCode 44 – Wildcard Matching.*