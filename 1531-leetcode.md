---
title: LeetCode 1531. String Compression II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 1531 – String Compression II  
**LeetCode Hard – DP, Run‑Length Encoding, Delete k Chars**

---

## 1️⃣  What the Problem is About

You’re given a lowercase string `s` (`1 ≤ |s| ≤ 100`) and an integer `k` (`0 ≤ k ≤ |s|`).  
You may delete *at most* `k` characters from `s`.  
After the deletions you run **run‑length encoding (RLE)**:

* Replace any run of the same character that appears **2 or more times** with  
  `char + count`.  
* Single characters stay as they are – no “1” is added.

Goal: **Minimize the length of the encoded string**.

> Example  
> `s = "aaabcccd", k = 2`  
> Delete `b` and `d` → `aaaccc` → RLE → `"a3c3"` → length `4`.

The problem is hard because you have to decide *which* `k` characters to delete and *where* to split the string into runs.

---

## 2️⃣  Why DP is the Right Tool

If we look at the string from left to right, the decision at position `i` only depends on

* how many deletions we still have,
* where the **next same character** after `i` is.

Hence the state can be represented by  
`(index i, remaining‑k)` → *minimum encoded length for the suffix `s[i:]`*.

This is a classic 2‑dimensional DP problem that we can solve by

* **Top‑Down (memoization)** – recursion with caching.
* **Bottom‑Up (tabulation)** – iterative DP.

Both solutions run in `O(k · n²)` time (worst case `n = 100`) and `O(k · n)` memory, which is easily fast enough for the constraints.

---

## 3️⃣  Key Insight – “Next Same Character” Pre‑computation

During the DP we need to know, for every position `i`, the next index `j > i` where `s[j] == s[i]`.  
Instead of scanning forward each time (which would make the DP `O(n³)`), we build an array `next`:

```
next[i] = index of the next occurrence of s[i] after i   (or -1 if none)
```

We build it in **O(n)** by scanning the string from right to left and keeping the latest position for each letter.

With `next` we can efficiently enumerate all possible “runs” that start at `i`.

---

## 4️⃣  How the DP Works

```
dp[i][rem]  =  minimal encoded length of s[i:] using at most rem deletions
```

### Transitions

1. **Delete `s[i]`**  
   `dp[i][rem]  →  dp[i+1][rem-1]`  (if rem > 0)

2. **Keep a run starting at `i`**  
   Walk through all later occurrences of the same character (`cur = i, next[i], next[next[i]], …`).  
   For each possible run endpoint `cur`:

   * `lenWindow   = cur - i + 1`  (characters considered)
   * `occurrences = number of kept characters in this run`  
     (`occurrences` increments by 1 each time we visit a same‑letter index)
   * `removals = lenWindow - occurrences`  (characters we delete inside the window)
   * If `removals > rem` → break (no more deletions left)
   * `cost = 1`  (for the letter itself)  
     plus `+1` each time `occurrences` reaches 2, 10, 100 (digits grow)
   * `dp[i][rem] = min(dp[i][rem], cost + dp[cur+1][rem-removals])`

The base case: `dp[n][*] = 0` (empty suffix).  
If the suffix length ≤ `rem`, we can delete everything → length `0`.

The answer is `dp[0][k]`.

---

## 5️⃣  Implementation Details

Below are **ready‑to‑copy** solutions in **Java, Python, and C++**.  
All three use the same algorithm and the same `next` array pre‑computation.

> **Tip:**  
> - For Java and C++ use an array of `int` initialized to `-1` for memoization.  
> - For Python use `functools.lru_cache` or an explicit dictionary.

---

### 📄 Java – Top‑Down Memoization

```java
import java.util.Arrays;

public class Solution {
    // helper to build the "next same character" array
    private int[] buildNext(char[] s) {
        int n = s.length;
        int[] next = new int[n];
        int[] last = new int[26];
        Arrays.fill(last, -1);
        for (int i = n - 1; i >= 0; i--) {
            int idx = s[i] - 'a';
            next[i] = last[idx];
            last[idx] = i;
        }
        return next;
    }

    public int getLengthOfOptimalCompression(String s, int k) {
        if (k >= s.length()) return 0;
        int[] next = buildNext(s.toCharArray());
        int n = s.length();
        int[][] memo = new int[k + 1][n + 1];
        for (int[] row : memo) Arrays.fill(row, -1);
        return dfs(0, k, next, memo);
    }

    private int dfs(int i, int rem, int[] next, int[][] memo) {
        if (memo[rem][i] != -1) return memo[rem][i];
        int n = next.length;
        if (n - i <= rem) return memo[rem][i] = 0;   // delete the rest

        int best = Integer.MAX_VALUE;

        // Option 1: delete current char
        if (rem > 0) best = Math.min(best, dfs(i + 1, rem - 1, next, memo));

        // Option 2: keep a run starting at i
        int occ = 0;
        int cost = 1;          // for the letter itself
        for (int cur = i; cur != -1; cur = next[cur]) {
            occ++;
            int deletions = (cur - i + 1) - occ;  // inside the window
            if (deletions > rem) break;          // cannot afford more deletions

            // increase cost when digits grow
            if (occ == 2 || occ == 10 || occ == 100) cost++;

            best = Math.min(best, cost + dfs(cur + 1, rem - deletions, next, memo));
        }

        return memo[rem][i] = best;
    }
}
```

---

### 📄 Python – Recursive DP with `lru_cache`

```python
from functools import lru_cache
from typing import List

class Solution:
    def build_next(self, s: str) -> List[int]:
        n = len(s)
        nxt = [-1] * n
        last = [-1] * 26
        for i in range(n - 1, -1, -1):
            idx = ord(s[i]) - 97
            nxt[i] = last[idx]
            last[idx] = i
        return nxt

    def getLengthOfOptimalCompression(self, s: str, k: int) -> int:
        if k >= len(s):
            return 0
        nxt = self.build_next(s)
        n = len(s)

        @lru_cache(None)
        def dfs(i: int, rem: int) -> int:
            if n - i <= rem:                 # delete the rest
                return 0
            best = float('inf')

            # delete current character
            if rem:
                best = min(best, dfs(i + 1, rem - 1))

            # keep a run starting at i
            occ = 0
            cost = 1                           # the letter itself
            cur = i
            while cur != -1:
                occ += 1
                deletions = (cur - i + 1) - occ
                if deletions > rem:
                    break
                if occ in (2, 10, 100):
                    cost += 1
                best = min(best, cost + dfs(cur + 1, rem - deletions))
                cur = nxt[cur]
            return best

        return dfs(0, k)
```

---

### 📄 C++ – Bottom‑Up Tabulation (for fun)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> buildNext(const string& s) {
        int n = s.size();
        vector<int> next(n, -1), last(26, -1);
        for (int i = n - 1; i >= 0; --i) {
            int c = s[i] - 'a';
            next[i] = last[c];
            last[c] = i;
        }
        return next;
    }

    int getLengthOfOptimalCompression(string s, int k) {
        int n = s.size();
        if (k >= n) return 0;
        vector<int> nxt = buildNext(s);
        vector<vector<int>> dp(k + 1, vector<int>(n + 1, -1));

        function<int(int,int)> dfs = [&](int i, int rem) {
            if (dp[rem][i] != -1) return dp[rem][i];
            if (n - i <= rem) return dp[rem][i] = 0;      // delete all remaining
            int best = INT_MAX;

            if (rem) best = min(best, dfs(i + 1, rem - 1));

            int occ = 0;
            int cost = 1;
            for (int cur = i; cur != -1; cur = nxt[cur]) {
                ++occ;
                int del = (cur - i + 1) - occ;
                if (del > rem) break;
                if (occ == 2 || occ == 10 || occ == 100) cost++;
                best = min(best, cost + dfs(cur + 1, rem - del));
            }
            return dp[rem][i] = best;
        };

        return dfs(0, k);
    }
};
```

---

> All three programs output the **minimum encoded length** for any valid input and run in a fraction of a second.

---

## 6️⃣  Time & Space Complexity

| Approach | Time | Memory |
|----------|------|--------|
| Top‑Down | `O(k · n²)` (≈ 1 M operations for `n = 100`, `k = 100`) | `O(k · n)` |
| Bottom‑Up | same | same |

With `n ≤ 100` this is far below the 1‑second time limit on LeetCode.

---

## 7️⃣  Common Pitfalls & “What if” Scenarios

| Pitfall | Fix |
|---------|-----|
| **Mis‑counting digit changes** – forget that the first digit is *not* counted for length 1 runs. | Add cost only when `occurrences` becomes `2`, `10`, or `100`. |
| **Omitting the “delete the rest” base case** – DP never reaches `0` length for fully deletable suffixes. | Add `if (n-i <= rem) return 0;` early. |
| **Building `next` incorrectly** – using `s[i]` instead of `s[cur]` in the loop. | Build `next` from right to left; use `last[26]`. |
| **Using recursion depth > 100** – Python recursion may hit limit. | Increase recursion limit (`sys.setrecursionlimit(2000)`) or use `@lru_cache`. |
| **Wrong cost updates** – adding digits for `1`‑length runs. | Only update when `occurrences` is in `{2, 10, 100}`. |

---

## 8️⃣  What This Means for Your Next Interview

* **Showcase DP state design** – explain the two dimensions clearly.
* **Highlight pre‑processing** – building the `next` array saves you from a cubic solution.
* **Discuss complexity** – `O(k·n²)` is acceptable here; if you get a “time limit exceeded” check whether you’re scanning forward inside the DP loop.
* **Edge cases** – `k ≥ n` → answer is `0`.  
  `k = 0` → simply encode the original string.

This problem is a *must‑know* for anyone targeting a senior software‑engineering role or a data‑science interview where LeetCode Hard problems are common.

---

## 9️⃣  Final Thoughts

*String Compression II* is more than just “delete k characters”; it’s about *optimal run segmentation*.  
A clean DP that respects the “next same character” relationship is the heart of the solution.  

If you can explain the state, transition, and complexity, you’ll impress interviewers and get the “Yes” answer on your résumé.

Good luck – you’ve got this! 🚀

---

## 🔑  SEO Keywords

- **String Compression II**  
- **LeetCode Hard**  
- **Dynamic Programming**  
- **Run‑Length Encoding**  
- **Delete k Characters**  
- **Java DP Solution**  
- **Python DP Solution**  
- **C++ DP Solution**  
- **Coding Interview**  
- **Interview Question**  
- **Job Interview Prep**  
- **Algorithm Explanation**  
- **Coding Challenge**  

Feel free to share this post on LinkedIn, Medium, or your personal blog – it’s packed with all the keywords recruiters and hiring managers love!