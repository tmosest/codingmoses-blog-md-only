---
title: LeetCode 1531. String Compression II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – “String Compression II” (LeetCode 1531)

**Goal**  
Given a string `s` (only lowercase letters, `1 ≤ |s| ≤ 100`) and an integer `k` (`0 ≤ k ≤ |s|`), delete *at most* `k` characters from `s` so that the run‑length encoded (RLE) version of the resulting string has **minimum length**.  
RLE rules:
* Consecutive identical characters are replaced by the character followed by the count of that run **only if** the count is ≥ 2.  
* Single characters are left as they are (no trailing `1`).  

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `s = "aaabcccd"`, `k = 2` | `4` | Delete `b` and `d`. Result: `"aaaccc"` → RLE `"a3c3"` |
| `s = "aabbaa"`, `k = 2` | `2` | Delete the two `b`s. Result: `"aaaa"` → RLE `"a4"` |
| `s = "aaaaaaaaaaa"`, `k = 0` | `3` | RLE `"a11"` |

**Constraints**

* `1 ≤ s.length ≤ 100`
* `0 ≤ k ≤ s.length`

The challenge is to decide *which* characters to delete so that the total compressed length is as small as possible.



--------------------------------------------------------------------

## 2. Why a “Suffix‑Based” DP Works

The core idea is to treat the problem **as a 2‑dimensional DP**:

```
dp[i][rem] = minimal compressed length of the suffix s[i … n-1] 
             after removing at most rem characters from that suffix
```

* `i` – current position in the string  
* `rem` – number of deletions that still may be used

For each state we have two possibilities:

| Option | Action | Effect on the state |
|--------|--------|---------------------|
| **Delete** | Remove `s[i]`. | Move to state `(i+1, rem‑1)` |
| **Keep** | Start a new run with `s[i]` and optionally delete some later same‑character letters to grow the run. | We iterate over the next occurrences of the same letter. For each possible end of the run we know:  |
| | | * `occ` – how many letters of this letter we actually keep in the run |
| | | * `deletionsInRun` – how many letters we skip to reach this run length |
| | | New state: `(nextIndexAfterRun, rem – deletionsInRun)` |

The RLE cost contributed by a run of length `occ` is:

```
1                                 // the character itself
+ [occ in {2,10,100}] ? 1 : 0    // an extra digit appears when occ reaches 2, 10 or 100
```

This cost depends **only** on `occ`, not on the exact positions of the kept letters.  
Because the string length is ≤ 100, a straightforward O(n²·k) DP is fast enough.



--------------------------------------------------------------------

## 3. Implementation Details

### 3.1 Pre‑computing next occurrences (`next[]`)

For each position `i` we store the index of the next occurrence of the same character (or `-1` if none).  
This allows us to jump from one occurrence to the next in **O(1)** time.

```text
next[i] = index of the next same letter after i   (or -1)
```

The array is built by scanning the string from right to left and keeping the last seen index of each character.



### 3.2 Bottom‑Up DP (Java/C++)

We use a 2‑dimensional table `dp[rem][i]` (rem first for better cache locality).  
The base case: when `rem` is big enough to delete the entire remaining suffix, the compressed length is 0.

Transition:

```
res = INF
if rem > 0:                    // delete current char
    res = dp[rem-1][i+1]

occ = 0
len = 1                           // character itself
for cur = i; cur != -1; cur = next[cur]:
    occ++
    deletions = (cur - i + 1) - occ     // letters skipped inside the run
    if deletions > rem: break

    if occ==2 or occ==10 or occ==100:
        len++

    res = min(res, len + dp[rem-deletions][cur+1])

dp[rem][i] = res
```

Complexities  
* Time: `O(n² · k)` – at most 100·100·100 ≈ 1 000 000 operations.  
* Memory: `O(n · k)` – about 10 000 integers.



### 3.3 Memoized Recursion (Python/C++)

The same logic can be expressed recursively with memoization (`@lru_cache` in Python or a 2‑D vector in C++).  
The top‑down approach often feels cleaner and naturally handles the “delete current character” branch first.



--------------------------------------------------------------------

## 4. Code

### 4.1 Java

```java
import java.util.Arrays;

public class Solution {
    public int getLengthOfOptimalCompression(String s, int k) {
        if (k >= s.length()) return 0;           // delete everything

        int n = s.length();
        int[] next = buildNextIndices(s.toCharArray());   // suffix pre‑computation

        int[][] dp = new int[k + 1][n + 1];
        for (int[] row : dp) Arrays.fill(row, Integer.MAX_VALUE / 2);

        // Base case: empty suffix → 0 length
        for (int rem = 0; rem <= k; rem++) dp[rem][n] = 0;

        // Bottom‑up from right to left
        for (int i = n - 1; i >= 0; i--) {
            for (int rem = 0; rem <= k; rem++) {
                int best = Integer.MAX_VALUE / 2;

                // 1. delete current char
                if (rem > 0) best = dp[rem - 1][i + 1];

                // 2. keep current char and form a run
                int occ = 0;
                int len = 1;                     // the character itself
                for (int cur = i; cur != -1; cur = next[cur]) {
                    occ++;
                    int deletions = (cur - i + 1) - occ;
                    if (deletions > rem) break; // no more deletions allowed

                    if (occ == 2 || occ == 10 || occ == 100) len++;

                    best = Math.min(best, len + dp[rem - deletions][cur + 1]);
                }
                dp[rem][i] = best;
            }
        }
        return dp[k][0];
    }

    /** Build next[i] = index of next same char after i, or -1 if none */
    private int[] buildNextIndices(char[] a) {
        int n = a.length;
        int[] next = new int[n];
        int[] pos = new int[26];
        Arrays.fill(pos, -1);

        for (int i = n - 1; i >= 0; i--) {
            int ch = a[i] - 'a';
            next[i] = pos[ch];
            pos[ch] = i;
        }
        return next;
    }
}
```

### 4.2 Python

```python
from functools import lru_cache
import sys

class Solution:
    def getLengthOfOptimalCompression(self, s: str, k: int) -> int:
        if k >= len(s):
            return 0

        n = len(s)
        next_occ = self.build_next_indices(s)

        @lru_cache(None)
        def dfs(i: int, rem: int) -> int:
            if i == n:
                return 0
            if n - i <= rem:
                return 0

            best = sys.maxsize

            # delete current character
            if rem:
                best = dfs(i + 1, rem - 1)

            # keep current character and build a run
            occ = 0
            cur = i
            length = 1
            while cur != -1:
                occ += 1
                deletions = (cur - i + 1) - occ
                if deletions > rem:
                    break
                if occ in {2, 10, 100}:
                    length += 1
                best = min(best, length + dfs(cur + 1, rem - deletions))
                cur = next_occ[cur]
            return best

        return dfs(0, k)

    @staticmethod
    def build_next_indices(s: str):
        n = len(s)
        nxt = [-1] * n
        last = [-1] * 26
        for i in range(n - 1, -1, -1):
            ch = ord(s[i]) - 97
            nxt[i] = last[ch]
            last[ch] = i
        return nxt
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int getLengthOfOptimalCompression(string s, int k) {
        int n = s.size();
        if (k >= n) return 0;

        vector<int> next = buildNextIndices(s);

        const int INF = 1e9;
        vector<vector<int>> dp(k + 1, vector<int>(n + 1, INF));

        for (int rem = 0; rem <= k; ++rem) dp[rem][n] = 0;   // empty suffix

        for (int i = n - 1; i >= 0; --i) {
            for (int rem = 0; rem <= k; ++rem) {
                int best = INF;

                // 1. delete current char
                if (rem) best = dp[rem - 1][i + 1];

                // 2. keep current char
                int occ = 0;
                int len = 1;                   // character itself
                for (int cur = i; cur != -1; cur = next[cur]) {
                    ++occ;
                    int deletions = (cur - i + 1) - occ;
                    if (deletions > rem) break;

                    if (occ == 2 || occ == 10 || occ == 100) ++len;

                    best = min(best, len + dp[rem - deletions][cur + 1]);
                }
                dp[rem][i] = best;
            }
        }
        return dp[k][0];
    }

private:
    vector<int> buildNextIndices(const string &s) {
        int n = s.size();
        vector<int> next(n), pos(26, -1);
        for (int i = n - 1; i >= 0; --i) {
            int ch = s[i] - 'a';
            next[i] = pos[ch];
            pos[ch] = i;
        }
        return next;
    }
};
```

> **Tip:** For the Python solution set `sys.setrecursionlimit(10000)` if you prefer recursion over a bottom‑up loop.



--------------------------------------------------------------------

## 5. Blog‑Style Walk‑through

> **Title**  
> *String Compression II – The Good, The Bad, The Ugly, and How to Nail It (LeetCode 1531)*

> **Meta Description**  
> Dive deep into LeetCode 1531 “String Compression II”. Learn the DP trick, why suffix jumps help, handle RLE cost changes, and read full solutions in Java, Python, and C++.

---

### 5.1 The “Good” – Why the Problem Is Exciting

| What’s Good | Why It Matters |
|-------------|----------------|
| **Clear Objective** – minimize RLE length. | Gives a single, objective function. |
| **Limited String Size** – `|s| ≤ 100`. | Lets us use an `O(n²·k)` DP comfortably. |
| **Intuitive RLE Rules** – only counts ≥ 2, single characters stay. | Avoids hidden corner cases that plague some compression problems. |
| **Real‑World Analogy** – removing noisy data to get a cleaner representation. | Makes the problem relatable. |

These aspects let you focus on the *algorithmic heart* rather than wrestling with data‑structure noise.



### 5.2 The “Bad” – What Can Go Wrong

| Potential Pitfall | How It Pops Up |
|-------------------|----------------|
| **Mis‑counting RLE digits** – forgetting that digits appear at 2, 10, 100. | Leads to over‑optimistic or over‑pessimistic lengths. |
| **Assuming a linear deletion cost** – deleting inside a run vs. across runs. | Over‑simplifies the DP transition. |
| **Using a naive O(n·k) loop** – iterating over all characters for each state. | Turns out to be `O(n³·k)` which is too slow for 100. |
| **Index mis‑management** – off‑by‑one when computing deletions inside a run. | Small bugs that make the whole DP wrong. |

A careful reading of the problem statement and a clear DP state diagram help you sidestep these.



### 5.3 The “Ugly” – Subtle Implementation Details

| Ugly Element | How to Tame It |
|--------------|----------------|
| **Suffix pre‑computation** (`next[]`) | Build it once in `O(n)` and store in an array. |
| **Cost of a run** – the jump from length 1 → 2 (1 digit), 9 → 10 (another digit), 99 → 100 (another digit) | Check only three thresholds; no need to compute logarithms. |
| **Handling “delete current char” vs. “keep current char”** | In the transition loop, the delete branch is handled first (if `rem > 0`), then we iterate over the next same‑letter positions. |
| **Avoid integer overflow** | Use `INF = INT_MAX/2` or `sys.maxsize/2` to keep sums safe. |

These details, though small, are the glue that makes the algorithm robust across languages.



--------------------------------------------------------------------

## 6. Final Thoughts

* **Simplicity + Power** – The suffix‑based DP turns a seemingly complicated “delete‑and‑compress” problem into a classic 2‑D DP.  
* **Fast Enough** – With the pre‑computed `next[]` array, each transition is linear in the number of same‑letter occurrences, giving `O(n²·k)` runtime.  
* **Language‑Agility** – The same logic applies in Java, Python, and C++ with almost identical code; the only differences are language‑specific idioms (e.g. `@lru_cache` vs. a 2‑D vector).  

Happy coding! 🚀



--------------------------------------------------------------------

## 7. SEO Checklist for the Blog Post

1. **Primary Keywords** – *String Compression II, LeetCode 1531, RLE, run‑length encoding, delete k characters, dynamic programming solution*  
2. **Secondary Keywords** – *Java solution, Python solution, C++ solution, suffix array, DP optimisation, compressed string length*  
3. **Meta Tags** – Add `<meta name="description" content="Solve LeetCode 1531 – String Compression II in Java, Python, and C++ with a suffix‑based DP. Learn the algorithm, code, and pitfalls.">`  
4. **Header Hierarchy** – Use `<h1>` for the title, `<h2>` for sections, `<h3>` for sub‑sections, and `<pre><code>` blocks for code.  
5. **Internal Linking** – Reference other LeetCode DP problems (e.g., “Word Break II”, “Minimum Deletions to Make a String Palindrome”) to improve dwell time.  
6. **Call‑to‑Action** – End with “Try this on LeetCode and share your solution!” to encourage comments.  

With this structure, the post should rank well for both novices and seasoned interviewees looking for a clean, production‑ready solution to String Compression II.