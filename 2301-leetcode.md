---
title: LeetCode 2301. Match Substring After Replacement - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Leetcode 2024 – **“Match Replacement”**  
**The Good, The Bad, The Ugly**  
*Why a simple brute‑force works, why KMP fails, and a peek at the FFT hack.*

---

## 1. Problem Recap

| # | Title | Link |
|---|-------|------|
| 2024 | **Match Replacement** | https://leetcode.com/problems/match-replacement/ |

> **Definition**  
> You are given three strings: `s`, `sub`, and a list of `mappings` (each mapping is a pair `old → new`).  
> You can **replace each character of `sub` at most once** with any `new` that it maps to (including itself).  
> After all optional replacements, `sub` must appear as a contiguous substring of `s`.  
> Return `true` if such a match exists, otherwise `false`.

**Examples**

| s | sub | mappings | result |
|---|-----|----------|--------|
| `abac` | `aa` | `[ ['a','b'] ]` | `true` (replace both `a`’s with `b` → `bb`, `bb` occurs at index 1) |
| `abac` | `bb` | `[ ['a','b'] ]` | `false` (the `b` in `sub` cannot be replaced to match the `a` in `s`) |

**Constraints**

* `1 ≤ sub.length ≤ s.length ≤ 10⁴`
* `0 ≤ mappings.length ≤ 10⁵`
* All characters are printable ASCII (`0–9`, `A–Z`, `a–z`)

---

## 2. Why the “Good” Brute‑Force is Accepted

- The alphabet is tiny (62 printable characters).  
- We can build a 62×62 boolean matrix `canReplace[old][new]` in `O(mappings)` time.  
- Once we have this matrix, checking a window `[i, i+|sub|)` in `s` is only a linear scan over `sub`.  
- The overall complexity is `O(|s| · |sub|)` = `≤ 10⁸` operations – well within the time limit for the given limits.

**KMP‑style tricks don’t work** because “matching” is *context dependent*.  
If `sub[i] → x` and `sub[j] → x`, the character `x` in `s` could be the result of *either* replacement, so the failure function in KMP can’t know where to back‑track.  

> **Good** – Easy to code, runs fast enough.  
> **Bad** – More clever tricks (FFT, NTT, or fancy KMP) add complexity for little gain.  
> **Ugly** – Most KMP solutions in the discussion fail on subtle test cases.

---

## 3. Implementation Details

### 3.1 Character → Index Mapping

| Index | Char |
|-------|------|
| 0–25 | `a–z` |
| 26–51 | `A–Z` |
| 52–61 | `0–9` |

The mapping is chosen such that a 62×62 boolean matrix fits comfortably in memory (~3.8 kB).

### 3.2 Brute‑Force Core Logic

For each starting position `i` in `s`:

```
for j in 0 … sub.length-1
    if s[i+j] == sub[j]          → ok
    else if canReplace[sub[j]][s[i+j]] → ok
    else break
if j == sub.length → match found
```

The check stops immediately on a mismatch, keeping the algorithm linear in practice.

---

## 4. Code (Three Languages)

> **Note** – All three solutions implement the *brute‑force* idea.  
> They compile with standard versions: Java 17, Python 3.10+, and GNU‑C++17.

### 4.1 Java

```java
// File: Solution.java
import java.util.*;

public class Solution {
    // Map 'a'-'z' → 0..25, 'A'-'Z' → 26..51, '0'-'9' → 52..61
    private static final int ALPHABET = 62;

    public boolean matchReplacement(String s, String sub, char[][] mappings) {
        int n = s.length(), m = sub.length();
        boolean[][] replace = new boolean[ALPHABET][ALPHABET];

        // self‑mapping
        for (int i = 0; i < ALPHABET; ++i) replace[i][i] = true;

        // fill user mappings
        for (char[] mp : mappings) {
            int from = idx(mp[0]);   // sub‑char
            int to   = idx(mp[1]);   // s‑char
            replace[from][to] = true;
        }

        // brute‑force search
        for (int i = 0; i <= n - m; ++i) {
            int j = 0;
            while (j < m) {
                int subIdx = idx(sub.charAt(j));
                int sIdx   = idx(s.charAt(i + j));
                if (!replace[subIdx][sIdx]) break;
                ++j;
            }
            if (j == m) return true;   // full match
        }
        return false;
    }

    // helper: map char to 0..61
    private static int idx(char c) {
        if ('a' <= c && c <= 'z') return c - 'a';
        if ('A' <= c && c <= 'Z') return 26 + c - 'A';
        return 52 + c - '0';          // '0'..'9'
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution sol = new Solution();

        char[][] maps1 = { {'a', 'b'}, {'a', 'c'} };
        System.out.println(sol.matchReplacement("abac", "aa", maps1)); // true

        char[][] maps2 = { {'a', 'b'} };
        System.out.println(sol.matchReplacement("abac", "bb", maps2)); // false

        char[][] maps3 = { {'a', 'b'}, {'b', 'c'} };
        System.out.println(sol.matchReplacement("abac", "ac", maps3)); // true
    }
}
```

### 4.2 Python

```python
# File: solution.py
from typing import List

class Solution:
    ALPHABET = 62  # 26 lower + 26 upper + 10 digits

    @staticmethod
    def _idx(c: str) -> int:
        """Map 'a'-'z', 'A'-'Z', '0'-'9' → 0..61."""
        if 'a' <= c <= 'z':
            return ord(c) - ord('a')
        if 'A' <= c <= 'Z':
            return 26 + ord(c) - ord('A')
        return 52 + ord(c) - ord('0')

    def matchReplacement(
        self,
        s: str,
        sub: str,
        mappings: List[List[str]],
    ) -> bool:
        n, m = len(s), len(sub)

        # replace[old][new] == True  (old = char from `sub`, new = char from `s`)
        replace = [[False] * self.ALPHABET for _ in range(self.ALPHABET)]
        for i in range(self.ALPHABET):
            replace[i][i] = True                      # self mapping

        for old, new in mappings:
            replace[self._idx(old)][self._idx(new)] = True

        # brute‑force search
        for i in range(n - m + 1):
            j = 0
            while j < m:
                if not replace[self._idx(sub[j])][self._idx(s[i + j])]:
                    break
                j += 1
            if j == m:
                return True
        return False

# ----------------------------------------------------------------
# Quick manual tests (you can paste into Leetcode's interactive runner)
if __name__ == "__main__":
    sol = Solution()

    maps1 = [['a', 'b'], ['a', 'c']]
    print(sol.matchReplacement("abac", "aa", maps1))  # True

    maps2 = [['a', 'b']]
    print(sol.matchReplacement("abac", "bb", maps2))  # False

    maps3 = [['a', 'b'], ['b', 'c']]
    print(sol.matchReplacement("abac", "ac", maps3))  # True
```

### 4.3 C++17

```cpp
// File: solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr int ALPHA = 62;   // a-z, A-Z, 0-9

    // helper: map char to 0..61
    static int idx(char c) {
        if ('a' <= c && c <= 'z') return c - 'a';
        if ('A' <= c && c <= 'Z') return 26 + c - 'A';
        return 52 + c - '0';
    }

    bool matchReplacement(string s, string sub, vector<vector<char>> mappings) {
        int n = s.size(), m = sub.size();
        vector<array<bool, ALPHA>> replace(ALPHA);
        for (int i = 0; i < ALPHA; ++i) replace[i].fill(false);

        // self‑mapping
        for (int i = 0; i < ALPHA; ++i) replace[i][i] = true;

        // user mappings
        for (auto &mp : mappings) {
            int from = idx(mp[0]);          // sub‑char
            int to   = idx(mp[1]);          // s‑char
            replace[from][to] = true;
        }

        // brute‑force search
        for (int i = 0; i <= n - m; ++i) {
            int j = 0;
            while (j < m) {
                int subIdx = idx(sub[j]);
                int sIdx   = idx(s[i + j]);
                if (!replace[subIdx][sIdx]) break;
                ++j;
            }
            if (j == m) return true;   // full match
        }
        return false;
    }

    // Simple test harness
    static void runTests() {
        Solution sol;

        vector<vector<char>> maps1 = { {'a', 'b'}, {'a', 'c'} };
        cout << boolalpha << sol.matchReplacement("abac", "aa", maps1) << '\n'; // true

        vector<vector<char>> maps2 = { {'a', 'b'} };
        cout << sol.matchReplacement("abac", "bb", maps2) << '\n';              // false

        vector<vector<char>> maps3 = { {'a', 'b'}, {'b', 'c'} };
        cout << sol.matchReplacement("abac", "ac", maps3) << '\n';              // true
    }
};

int main() {
    Solution::runTests();
    return 0;
}
```

---

## 5. Complexity Summary

| Approach | Time | Space | Remarks |
|----------|------|-------|---------|
| **Brute‑Force** | `O(|s| · |sub|)`   (≤ 10⁸ comparisons) | `O(ALPHABET²)` = 62×62 booleans (~4 kB) | Fast enough, trivial to implement |
| **KMP (any variant)** | `O(|s| + |sub|)` | `O(ALPHABET²)` | *Fails* on cases where a replacement can produce the same character in `s`. |
| **FFT/NTT** | `O((|s|+|sub|) log(|s|+|sub|))` | `O(ALPHABET²)` | Over‑engineering; accepted only for fun. |

---

## 6. Final Thoughts

* **The Good** – A single 62×62 matrix + linear window scan is *exactly what the problem demands* and it is **fast, safe, and easy to understand**.  
* **The Bad** – Over‑engineering (FFT, NTT, fancy back‑tracking) only hides the *true* difficulty: the “matching” relation is *non‑deterministic* on a per‑character basis.  
* **The Ugly** – Many discussion posts show KMP or greedy attempts that *slip on edge cases* because they ignore that `sub[i] → x` and `sub[j] → x` create *two* distinct replacement possibilities for the same `x` in `s`.  

If you’re a Leetcode beginner or a production engineer, go straight to the brute‑force matrix approach.  
If you’re curious about theory, dig into the FFT implementation (see the Leetcode discussion) – it’s a great “look‑at‑the‑code” exercise but you’ll never need it for this problem.

Happy coding, and may your substrings always line up!