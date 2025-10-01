---
title: LeetCode 1531. String Compression II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1531. String Compression II  
**Hard** – LeetCode  
<https://leetcode.com/problems/string-compression-ii/>

The task is to delete at most `k` characters from a string `s` ( `|s| ≤ 100` ) so that its
run‑length encoded representation is as short as possible.

A run‑length encoding keeps a *run* of identical characters and replaces it by
`char + count` **only if** the run has length ≥ 2.  
`count` is written in decimal and counts as **one character for each digit**  
(1 → 1, 10 → 2, 100 → 3, …).  
The goal is to minimize the *total* number of characters in the encoded string.

---

## 1. The Good – A clean DP solution

The optimal solution is a classic **2‑dimensional dynamic‑programming** problem.

```
dp[i][rem] = minimal encoded length for s[i … n-1]
             after deleting at most 'rem' characters from that suffix
```

*Transitions*  
From a position `i` we have two possibilities:

1. **Delete `s[i]`**  
   `dp[i][rem] = dp[i+1][rem-1]`  (if `rem > 0`)

2. **Keep a run that starts at `i`**  
   We try to extend the run by taking equal characters that appear later in
   the suffix, possibly deleting the characters that lie in between.

   For every candidate `j` that contains the *same* character as `s[i]` we
   compute

   * `occ` – how many of that character we keep in the run
   * `removed` – characters that must be deleted to obtain a contiguous block
   * `lenInc` – additional encoded characters produced by the run  
     (1 for the letter + digits for `occ` if `occ ≥ 2`)

   Then

   ```
   dp[i][rem] = min( dp[i][rem], lenInc + dp[j+1][rem-removed] )
   ```

Because `|s| ≤ 100`, the quadratic DP is fast enough.

To avoid scanning the suffix for the next occurrence of the same character
many times, we pre‑compute an array `nextIdx[c][i]` that gives the next index
of character `c` after position `i`.  
With this we can jump from one occurrence to the next in O(1).

### 1.1 Complexity

| Factor | Reason | Result |
|--------|--------|--------|
| `n` | string length | ≤ 100 |
| `k` | deletions allowed | ≤ n |
| State | `dp[i][rem]` | `n · (k+1)` |
| Transition | iterate over next occurrences of the same char | O(n) worst case |
| **Total** | `O(n² · k)` | well below 1 ms for the constraints |

---

## 2. The Bad – An over‑complicated approach

A very common pitfall is to try to compress the string first (build the
original run‑length encoding) and then delete characters only inside those
runs.  
This misses many opportunities: deleting a single character outside a run
can merge two runs into one, drastically reducing the encoded length.
Such a greedy strategy cannot guarantee optimality.

---

## 3. The Ugly – A memory‑hungry recursive DFS

A naïve depth‑first search that branches on “delete” / “keep” at every
character leads to `2ⁿ` possibilities, far beyond what is needed.  
Even with memoization that still uses a 3‑dimensional cache (`i, rem, state`)
and consumes large amounts of memory.  
The DP described in section 1 is the clean, optimal version.

---

## 4. Code – Three Implementations

Below are working solutions in **Java**, **Python**, and **C++**.  
All of them use the same DP idea; the only difference is the syntax and
standard library usage.

### 4.1 Java (Top‑down memoization)

```java
import java.util.Arrays;

class Solution {
    public int getLengthOfOptimalCompression(String s, int k) {
        if (k >= s.length()) return 0;          // delete everything

        // pre‑compute next occurrence of the same char
        int[] next = nextIndices(s.toCharArray());

        int[][] memo = new int[k + 1][s.length() + 1];
        for (int[] row : memo) Arrays.fill(row, -1);

        return dfs(0, k, next, memo);
    }

    // recursive DP
    private int dfs(int i, int rem, int[] next, int[][] memo) {
        if (i == next.length) return 0;          // finished
        if (rem > next.length - i) return 0;    // we can delete everything left
        if (memo[rem][i] != -1) return memo[rem][i];

        int best = Integer.MAX_VALUE;

        // 1. delete s[i]
        if (rem > 0) best = Math.min(best, dfs(i + 1, rem - 1, next, memo));

        // 2. keep a run starting at i
        int occ = 0;        // how many same chars we keep
        int lenInc = 1;     // length added by this run (at least the letter)
        int j = i;          // current index of the same character
        while (j != -1) {
            occ++;
            int removed = (j - i + 1) - occ;  // characters between i and j that are not kept
            if (removed > rem) break;         // cannot delete more than we have

            if (occ >= 2 && (occ == 2 || occ == 10 || occ == 100))
                lenInc++;                     // a new digit appears

            best = Math.min(best, lenInc + dfs(j + 1, rem - removed, next, memo));

            j = next[j];                      // next occurrence of the same char
        }

        return memo[rem][i] = best;
    }

    // nextIndices[i] = next position of s[i] after i, or -1
    private int[] nextIndices(char[] a) {
        int n = a.length;
        int[] next = new int[n];
        int[] pos = new int[26];
        Arrays.fill(pos, -1);
        for (int i = n - 1; i >= 0; i--) {
            int c = a[i] - 'a';
            next[i] = pos[c];
            pos[c] = i;
        }
        return next;
    }
}
```

### 4.2 Python (Bottom‑up DP)

```python
class Solution:
    def getLengthOfOptimalCompression(self, s: str, k: int) -> int:
        if k >= len(s): return 0

        # next index of the same char
        nxt = [-1] * len(s)
        last = [-1] * 26
        for i in range(len(s) - 1, -1, -1):
            c = ord(s[i]) - 97
            nxt[i] = last[c]
            last[c] = i

        INF = 10 ** 9
        dp = [[INF] * (k + 1) for _ in range(len(s) + 1)]
        for rem in range(k + 1):
            dp[len(s)][rem] = 0

        for i in range(len(s) - 1, -1, -1):
            for rem in range(k + 1):
                # delete s[i]
                if rem:
                    dp[i][rem] = dp[i + 1][rem - 1]

                # keep a run
                occ, inc = 0, 1
                j = i
                while j != -1:
                    occ += 1
                    removed = (j - i + 1) - occ
                    if removed > rem: break
                    if occ >= 2 and (occ == 2 or occ == 10 or occ == 100):
                        inc += 1
                    dp[i][rem] = min(dp[i][rem],
                                      inc + dp[j + 1][rem - removed])
                    j = nxt[j]
        return dp[0][k]
```

### 4.3 C++ (Iterative DP)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int getLengthOfOptimalCompression(string s, int k) {
        int n = s.size();
        if (k >= n) return 0;

        // pre‑compute next index of the same character
        vector<int> nextIdx(n, -1);
        vector<int> last(26, -1);
        for (int i = n - 1; i >= 0; --i) {
            int c = s[i] - 'a';
            nextIdx[i] = last[c];
            last[c] = i;
        }

        const int INF = 1e9;
        vector<vector<int>> dp(n + 1, vector<int>(k + 1, INF));
        for (int rem = 0; rem <= k; ++rem) dp[n][rem] = 0;

        for (int i = n - 1; i >= 0; --i) {
            for (int rem = 0; rem <= k; ++rem) {
                // 1) delete s[i]
                if (rem) dp[i][rem] = dp[i + 1][rem - 1];

                // 2) keep a run starting at i
                int occ = 0, inc = 1;
                for (int j = i; j != -1; j = nextIdx[j]) {
                    occ++;
                    int removed = (j - i + 1) - occ;
                    if (removed > rem) break;
                    if (occ >= 2 && (occ == 2 || occ == 10 || occ == 100))
                        inc++;
                    dp[i][rem] = min(dp[i][rem],
                                     inc + dp[j + 1][rem - removed]);
                }
            }
        }
        return dp[0][k];
    }

private:
    // helper: compute next index of the same character
    vector<int> nextIdx = {};   // not used outside constructor
};
```

---

## 5. Interview‑Ready Tips

| What to remember | Why it matters | How to show it |
|------------------|----------------|----------------|
| **State definition** – `dp[i][rem]` keeps the suffix perspective | Makes recursion/iteration simple | Write the recurrence on a whiteboard first |
| **Jump to the next same character** – pre‑computed `next` array | Saves time, avoids nested loops | Explain that this is a common trick for “next occurrence” problems |
| **Count digits** – only when `occ` crosses 2, 10, 100 | Prevents off‑by‑one errors | Show a helper `lenAdd(occ)` if you prefer |
| **Edge cases** – `k ≥ n`, deleting everything | O(1) shortcut | Don’t forget to handle it early |
| **Complexity** – `O(n²k)` | Fit easily in LeetCode’s time limits | Mention it in the interview for reassurance |

---

## 6. SEO‑Friendly Blog Title

> **String Compression II – LeetCode 1531 | DP Solution in Java, Python & C++**

---

## 7. Full Blog Post

> **(You can copy‑paste the whole article into your favourite blog platform –  
>   add your own header image, Markdown or HTML formatting, and publish.)**

---

### 7.1 Introduction

*Problem recap:*  
Deleting up to `k` characters from a string to produce the shortest possible
run‑length encoding.  
Why is it a hard problem?  
Because the effect of a single deletion can cascade across the whole string
(e.g., merging two runs).

> *Keywords:* string compression, run‑length encoding, LeetCode 1531, dynamic programming, interview problem, algorithm design, time‑space complexity, coding interview tips.

---

### 7.2 Why “Good” DP Works

1. **Linear structure** – we always look at a suffix (`i … n-1`).  
2. **Bounded deletions** – only `k` deletions, so we can keep track of them in the DP state.  
3. **Local decision** – at each position we decide *delete* or *extend a run*.  
4. **Optimal substructure** – the best solution for a suffix depends only on
   the best solution for the remaining part of the suffix.  

All of the above are textbook DP ingredients.

---

### 7.3 What the Transition Means

| Symbol | Meaning | Example |
|--------|---------|---------|
| `i` | current index | `s = "aabbaa"` → start at the first `a` |
| `rem` | how many deletions we still have | 3 |
| `occ` | number of kept `a`s in the run | 3 (first `a`, second `a`, third `a`) |
| `removed` | characters between the kept `a`s that must be deleted | 1 (the `b` between them) |
| `inc` | how many encoded characters the run contributes | `1 + digits(occ)` |

---

### 7.4 Common Pitfalls

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| Delete only inside existing runs | Merges can be missed | Explore deletions outside runs too |
| Forget that *count* digits depend on decimal representation | Wrong length increase | Add 1 when `occ == 2`, `10`, or `100` |
| Use a greedy “delete first run” strategy | Not optimal | Use DP |

---

### 7.5 Interview‑Ready Checklist

| Question | What to ask | Answer hint |
|----------|-------------|-------------|
| What is the state of the DP? | `dp[i][rem]`? | Keep suffix and deletions. |
| How do you compute the additional encoded length of a run? | `1` for the char + `digits(count)` | Remember that `count` only matters if `≥2`. |
| Why do we pre‑compute `nextIdx`? | To skip unrelated characters efficiently | Use an array of size `n` that jumps to the next same char. |
| What is the complexity? | `O(n² k)` | Under 1 ms for the given limits. |
| Can we do it with fewer states? | No – we need both position and remaining deletions | 2‑D DP is optimal. |

---

### 7.6 Conclusion

*String Compression II* is a great showcase of a DP trick that is
easy to describe but hard to spot in a rush.  
By focusing on the suffix, using a `nextIdx` table, and exploring both
“delete” and “keep a run” branches, we obtain an optimal and fast solution.
Avoid greedy shortcuts and over‑complicated DFS – stick to the clean DP.

Good luck on your interview! 🚀

--- 

**Meta‑data for SEO**  
- Title tag: “String Compression II – DP Solution (Java / Python / C++)”  
- Description: “Solve LeetCode 1531 (String Compression II) in Java, Python, and C++ with a clear dynamic‑programming approach. Read a step‑by‑step guide, complexity analysis, and interview tips.”  
- Keywords: `Leetcode 1531`, `string compression`, `run-length encoding`, `dynamic programming`, `coding interview`, `Java DP`, `Python DP`, `C++ DP`.

Happy coding!