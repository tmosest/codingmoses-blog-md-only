---
title: LeetCode 1016. Binary String With Substrings Representing 1 To N - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem 1016 – Binary String With Substrings Representing 1 To N  
> **LeetCode** | **Medium** | **Public**  

**Link:** <https://leetcode.com/problems/binary-string-with-substrings-representing-1-to-n>  

---

### 🔍 Problem Statement  
You’re given a binary string `s` (only `0` and `1`) and a positive integer `n`.  
Return **`true`** iff *every* integer from **1 to `n`** (inclusive) has its binary
representation as a substring of `s`. Otherwise return **`false`**.

> **Examples**  
> 1. `s = "0110"`, `n = 3` → `true`  (substrings `"1"`, `"10"`, `"11"`)  
> 2. `s = "0110"`, `n = 4` → `false` (missing `"100"`)

---

## 🎯 Why This Problem Is a Great Interview Question  
* **String manipulation + bit‑math** – tests ability to think outside pure DP.  
* **Large `n` (≤ 1 000 000 000)** – forces an efficient algorithm that doesn’t
  iterate through all numbers.  
* **Length of `s` is tiny (≤ 1000)** – we can use a sliding window approach.  

It’s perfect for showcasing algorithmic design, time‑space trade‑offs, and clean code in multiple languages.

---

## 💡 The Core Idea (Good, Bad, Ugly)

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Brute Force** | Works for tiny inputs. | `O(n · |s|)` → impossible when `n` is 10⁹. | `indexOf` for each number → quadratic. |
| **Optimized** | Scan `s` once, store all binary substrings up to `log₂(n)+1` length. | Still need to check every number up to `n`. | If `n` is huge, scanning 1…n is wasteful, but we’ll early‑exit when `n > setSize`. |
| **Data Structure** | `HashSet<Integer>` for O(1) look‑ups. | Hash collisions rarely a problem. | Using an `int` array of size `n` is impossible (1 GB+). |
| **Edge Cases** | Substrings starting with `0` are invalid. | Numbers with leading zeros ignored. | Need to convert binary substring to integer efficiently. |

The elegant solution keeps the complexity bounded by `O(|s|·log₂(n))` which, for the given constraints, is at most `30 × 1 000 ≈ 30 000` operations – trivially fast.

---

## 🛠️ Implementation

Below are clean, ready‑to‑paste implementations in **Java**, **Python**, and **C++**.

> **Common helper** – Compute the integer value of a binary substring while
> scanning it to avoid repeated `Integer.parseInt`.

---

### 1️⃣ Java (O(S) ≈ 30 000)

```java
import java.util.*;

public class Solution {
    /** Convert binary substring to int while scanning. */
    private int binaryToInt(String s, int start, int len) {
        int val = 0;
        for (int i = start; i < start + len; i++) {
            val = (val << 1) | (s.charAt(i) - '0');
        }
        return val;
    }

    public boolean queryString(String s, int n) {
        // Maximum length of binary representation of n
        int maxLen = Integer.toBinaryString(n).length();

        // Store all valid numbers that appear as substrings
        Set<Integer> seen = new HashSet<>();

        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == '0') continue;     // leading zero -> skip
            int val = 0;
            for (int l = 1; l <= maxLen && i + l <= s.length(); l++) {
                val = (val << 1) | (s.charAt(i + l - 1) - '0');
                if (val > n) break;              // no need to keep larger values
                seen.add(val);
            }
        }

        // Quick fail if we can't possibly cover all numbers
        if (n > seen.size()) return false;

        // Verify each number 1..n is present
        for (int num = 1; num <= n; num++) {
            if (!seen.contains(num)) return false;
        }
        return true;
    }
}
```

---

### 2️⃣ Python (O(S) ≈ 30 000)

```python
class Solution:
    def queryString(self, s: str, n: int) -> bool:
        max_len = n.bit_length()          # length of binary representation
        seen = set()

        for i, ch in enumerate(s):
            if ch == '0': continue        # skip leading zeros
            val = 0
            for l in range(1, max_len + 1):
                if i + l > len(s): break
                val = (val << 1) | (ord(s[i + l - 1]) & 1)
                if val > n: break
                seen.add(val)

        if n > len(seen): return False
        return all(num in seen for num in range(1, n + 1))
```

---

### 3️⃣ C++ (O(S) ≈ 30 000)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool queryString(string s, int n) {
        int maxLen = 32 - __builtin_clz(n);          // bit length of n
        unordered_set<int> seen;

        for (int i = 0; i < (int)s.size(); ++i) {
            if (s[i] == '0') continue;              // no leading zeros
            int val = 0;
            for (int l = 1; l <= maxLen && i + l <= (int)s.size(); ++l) {
                val = (val << 1) | (s[i + l - 1] - '0');
                if (val > n) break;
                seen.insert(val);
            }
        }

        if (n > (int)seen.size()) return false;     // early exit

        for (int num = 1; num <= n; ++num)
            if (!seen.count(num)) return false;
        return true;
    }
};
```

---

## 📊 Complexity Analysis

| **Metric** | **Time** | **Space** |
|------------|----------|-----------|
| **Java** | `O(|s| · log₂(n))`  ≤ 30 000 operations | `O(|s| · log₂(n))` for the hash set (≤ 30 000 integers) |
| **Python** | Same | Same |
| **C++** | Same | Same |

*Why it’s fast:*  
The outer loop scans the string once.  
The inner loop runs at most `maxLen = ⌊log₂ n⌋ + 1 ≤ 30` times per position.  
Thus the total work is bounded by `30 · |s|`, negligible for |s| ≤ 1000.

---

## 🧪 Edge Cases & Common Pitfalls

| **Case** | **Explanation** | **Fix** |
|----------|-----------------|---------|
| `n = 1` | Only the substring `"1"` needed. | Works automatically. |
| String starts with many zeros | Leading zeros are ignored; only substrings beginning with `1` are considered. | Skip indices where `s[i] == '0'`. |
| `n` larger than possible unique substrings | Return `false` early (`n > seen.size()`). | Helps avoid needless loops over large `n`. |
| Integer overflow when converting | Use bit shifting on `int`; max value ≤ 2³¹‑1. | Works because `n ≤ 10⁹`. |
| Empty string | `s` length ≥ 1 by constraint, but guard anyway. | Return `false`. |

---

## 🤔 Alternative Approaches

1. **Trie / Prefix Tree** – Insert all substrings of `s` into a trie, then walk
   from `1` to `n` in binary to see if the path exists.  
   *Pros:* Clear structure, works for very large `n` if the trie is sparse.  
   *Cons:* Extra memory and complexity, overkill for `s ≤ 1000`.

2. **KMP / Rabin‑Karp** – Build a pattern matcher for each binary string.  
   *Pros:* Good for large patterns.  
   *Cons:* Re‑computing for each `i` is expensive; not needed here.

3. **Dynamic Programming** – Not really applicable because we just need presence, not minimal counts.

The hash‑set approach is the sweet spot: simple, fast, and easy to reason about.

---

## 📋 Testing Strategy

| **Test** | **Expected** |
|----------|--------------|
| `s = "111111", n = 1` | `true` |
| `s = "0000", n = 1` | `false` (no `"1"`). |
| `s = "101010", n = 6` | `true` (binary of 1–6 all appear). |
| `s = "111000111", n = 7` | `true`. |
| `s = "0110", n = 4` | `false`. |
| `s = "1001001", n = 9` | `true`. |
| Very large `n` (`n = 10⁹`) but string short | Should return `false` after early exit. |

Add these cases to your unit tests – they cover minimal, typical, and edge
situations.

---

## 🚀 Wrap‑Up & Takeaway

* The problem forces you to think **logarithmic** in `n` instead of linear.  
* A single pass over `s`, coupled with a `HashSet` of substrings, gives a linear‑time solution with only a few thousand integers stored.  
* Clean code in Java, Python, or C++ demonstrates mastery of bit‑operations and data‑structures, two highly valued interview skills.

---

## 📝 Quick “Cheat Sheet” for Interviewers

- **Why `log₂(n)`?**  
  Any number > `n` can’t be needed, so we ignore longer substrings.

- **Why skip leading zeros?**  
  Binary numbers never start with `0` (unless the number itself is zero).

- **Why early‑exit with `n > seen.size()`?**  
  Guarantees that even if every number were present, you still couldn’t cover all of them.

- **How do you convert a substring to an int efficiently?**  
  Use bit shifting (`val = (val << 1) | bit`) instead of `Integer.parseInt`.

---

## 📣 SEO‑Ready Summary

> **Keywords**: *Binary string, substring search, bit manipulation, hash set, LeetCode 1016, string algorithm, interview question, Java solution, Python solution, C++ solution, time complexity analysis, edge cases, algorithmic design, coding interview*  

This article covers everything you need to land the “Binary String With Substrings Representing 1 To N” interview problem with confidence—multiple languages, clean code, and a deep‑dive into the logic that matters for hiring managers. Happy coding! 🚀