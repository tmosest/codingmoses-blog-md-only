---
title: LeetCode 1239. Maximum Length of a Concatenated String with Unique Characters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1. LeetCode #1547 – **Maximum Length of a Concatenated String with Unique Characters**  
> **Languages**: Java | Python | C++  

The classic LeetCode challenge that many interviewers love to throw at candidates. Below you’ll find three clean, production‑ready solutions – one for each of the most common interview languages.  
Each implementation uses the *bit‑mask DP* trick to keep the runtime fast while still exploring all possible subsequences.  

---

### 1.1  Problem Recap (for the code comments)

| Parameter | Meaning | Range |
|-----------|---------|-------|
| `arr` | List/Vector of strings | `1 ≤ |arr| ≤ 16` |
| Characters | Lowercase English letters only | `a–z` |
| Goal | Choose a subsequence of `arr`, concatenate the strings, **without any repeated character**, and return the maximum possible length. |

Because `|arr| ≤ 16`, an exponential solution (`O(2^n)`) is acceptable, but we’ll still aim for the fastest possible approach.

---

## 2. Java Implementation (Bit‑Mask DP)

```java
/**
 * LeetCode 1547 – Maximum Length of a Concatenated String with Unique Characters
 *
 * @author YourName
 */
class Solution {
    /**
     * Returns the maximum possible length of a concatenated string with all unique characters.
     *
     * @param arr List of input strings (only lowercase letters, 1‑16 strings)
     * @return maximum length
     */
    public int maxLength(List<String> arr) {
        // dp keeps all valid masks that we have discovered so far.
        // Each mask is a 26‑bit integer where bit i means that character 'a'+i is present.
        List<Integer> masks = new ArrayList<>();
        masks.add(0);                     // start with an empty mask

        int maxLen = 0;

        for (String s : arr) {
            // skip strings that already contain duplicates internally
            int mask = stringToMask(s);
            if (mask == 0) continue;      // mask==0 => duplicate inside s

            int curSize = masks.size();   // snapshot size to avoid concurrent modification
            for (int i = 0; i < curSize; i++) {
                int existing = masks.get(i);
                if ((existing & mask) == 0) {          // no overlap
                    int combined = existing | mask;
                    masks.add(combined);
                    maxLen = Math.max(maxLen, Integer.bitCount(combined));
                }
            }
        }

        // Also consider the empty string (length 0) if no valid string exists
        return maxLen;
    }

    /**
     * Convert a string to a 26‑bit mask.
     * If the string has duplicate letters, return 0 to indicate invalid.
     */
    private int stringToMask(String s) {
        int mask = 0;
        for (char ch : s.toCharArray()) {
            int bit = 1 << (ch - 'a');
            if ((mask & bit) != 0) return 0;  // duplicate inside s
            mask |= bit;
        }
        return mask;
    }
}
```

> **Why this Java version is interview‑ready:**  
> * Uses only primitive ints → O(1) space for the mask.  
> * `Integer.bitCount()` gives the number of unique letters instantly.  
> * No recursion → no stack‑overflow risk for the worst case of 16 strings.  

---

## 3. Python Implementation (Bit‑Mask DP)

```python
"""
LeetCode 1547: Maximum Length of a Concatenated String with Unique Characters
Author: YourName
"""

from typing import List


class Solution:
    def maxLength(self, arr: List[str]) -> int:
        """
        Return the maximum length of a concatenated string with all unique characters.
        Uses bit‑mask DP to keep a list of all valid character sets.
        """
        masks = [0]          # start with empty set
        max_len = 0

        for s in arr:
            mask = self._string_to_mask(s)
            if mask == 0:        # string contains duplicate letters internally
                continue

            cur_len = len(masks)   # snapshot to avoid concurrent modification
            for i in range(cur_len):
                existing = masks[i]
                if (existing & mask) == 0:
                    combined = existing | mask
                    masks.append(combined)
                    max_len = max(max_len, combined.bit_count())

        return max_len

    def _string_to_mask(self, s: str) -> int:
        """
        Convert a string to a 26‑bit mask.
        Return 0 if the string has any duplicate characters.
        """
        mask = 0
        for ch in s:
            bit = 1 << (ord(ch) - ord('a'))
            if mask & bit:
                return 0          # duplicate inside the string
            mask |= bit
        return mask
```

> **Python tips for interviewers**  
> * Uses `bit_count()` (Python 3.10+) for speed; older versions can use `bin(mask).count('1')`.  
> * No recursion → no risk of hitting Python’s recursion depth limit.  

---

## 4. C++ Implementation (Bit‑Mask DP)

```cpp
/*
 * LeetCode 1547 – Maximum Length of a Concatenated String with Unique Characters
 * Author: YourName
 */

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxLength(vector<string>& arr) {
        // vector of masks representing all valid partial concatenations
        vector<int> masks = {0};          // start with empty mask
        int maxLen = 0;

        for (const string& s : arr) {
            int mask = stringToMask(s);
            if (mask == 0) continue;     // skip strings with duplicate letters

            int currentSize = masks.size();   // snapshot
            for (int i = 0; i < currentSize; ++i) {
                int existing = masks[i];
                if ((existing & mask) == 0) {      // no overlap
                    int combined = existing | mask;
                    masks.push_back(combined);
                    maxLen = max(maxLen, __builtin_popcount(combined));
                }
            }
        }
        return maxLen;
    }

private:
    // Convert string to bitmask; return 0 if string has internal duplicates
    int stringToMask(const string& s) const {
        int mask = 0;
        for (char ch : s) {
            int bit = 1 << (ch - 'a');
            if (mask & bit) return 0;   // duplicate inside s
            mask |= bit;
        }
        return mask;
    }
};
```

> **Why C++ shines here**  
> * `__builtin_popcount()` is a compiler‑intrinsic, blazing‑fast.  
> * Uses `vector<int>` only – no heap fragmentation, O(1) auxiliary memory.  

---

## 5. Blog Article – “The Good, the Bad, and the Ugly of LeetCode #1547”

### Title  
**Mastering LeetCode 1547: The Good, the Bad & the Ugly of “Maximum Length of a Concatenated String with Unique Characters” – A Deep‑Dive for Interview Success**

### Meta Description  
Learn the fastest bit‑mask DP solution for LeetCode #1547, walk through the pros and cons of DFS vs. DP, and get job‑ready coding skills in Java, Python, and C++. Perfect study guide for software‑engineering interviews.

---

## 5.1  Introduction

When recruiters spot the keyword **“Maximum Length of a Concatenated String with Unique Characters”** in your portfolio, they immediately know you’re comfortable with string manipulation, bitwise tricks, and exponential‑time dynamic programming.  
LeetCode’s **#1547** is a go‑to problem for many backend interview rounds, and mastering it shows you can:

- Translate a combinatorial problem into a clean algorithm  
- Optimize space/time using bit‑masking  
- Write production‑ready code in Java, Python, and C++

Let’s break down the **good**, **bad**, and **ugly** aspects of this problem and its common solutions.

---

## 5.2  Problem Overview

> **Goal:**  
> Pick a subsequence of the given strings, concatenate them, and ensure *no* character repeats.  
> Return the maximum possible length of such a concatenated string.

**Constraints**

| | |
|---|---|
|`1 ≤ arr.length ≤ 16`|small enough to allow exponential search |
|All strings contain only lowercase English letters (`a–z`) | 26 distinct symbols |
|Internal duplicates in a single string invalidate that string |

---

## 5.3  The Good – Why This Problem is a Learning Goldmine

| Feature | Why it’s beneficial |
|---|---|
| **Bit‑masking** | Converts a set of 26 letters into a single `int`.  `1 << (c - 'a')` maps each character to a bit.  Operations like “no overlap” become simple AND/OR checks. |
| **O(1) popcount** | Languages like Java (`Integer.bitCount`) or C++ (`__builtin_popcount`) give instant cardinality, no loops over the string again. |
| **Avoids Recursion** | The DP approach iterates over masks and strings in a double‑loop, eliminating risk of stack overflows when dealing with 16 strings. |
| **Clear separation of concerns** | `stringToMask()` isolates the duplicate‑check logic.  Keeps the main algorithm readable and testable. |

---

## 5.4  The Bad – What Makes the Problem Hard

| Issue | Consequence |
|---|---|
| **Exponential Subset Space** | The naive “try every subset” solution is `O(2^n)` and will *timeout* if you implement it with nested loops over all subsets incorrectly. |
| **Memory Footprint** | Storing all valid masks can grow to `2^16 = 65 536` entries in the worst case.  Still manageable, but many candidates ignore this and write a recursive DFS that blows the stack or duplicates work. |
| **Hidden Duplicate Checks** | A string like `"aa"` instantly invalidates itself.  If you overlook this pre‑filter, the algorithm will waste time and potentially produce wrong results. |

Understanding these pitfalls is half the battle. Interviewers love to test whether you spot them before coding.

---

## 5.5  The Ugly – Common Traps & Debugging Struggles

1. **Recursive DFS + Global State**  
   * A typical DFS solution keeps a global `visited` set.  With 16 strings, the recursion depth is small, but the **call‑stack overhead** can add up and produce hard‑to‑trace bugs.  
   * Forgetting to prune on duplicate letters inside a string often yields incorrect answers like `-1` or a length that’s too large.

2. **Bit‑Mask Mis‑Usage**  
   * Using a 32‑bit int for 26 letters is fine, but shifting a character out of range (`'z'` → bit 25) and then AND‑ing with an existing mask incorrectly can cause signed‑integer bugs in Java or undefined behaviour in C++ if you forget the `unsigned` type.  
   * Mixing `String.toCharArray()` with `ord(ch)` in Python incorrectly if you accidentally use uppercase letters – a subtle test‑case trick.

3. **Performance Overhead in LeetCode’s Environment**  
   * LeetCode runs each submission in a constrained sandbox.  A solution that compiles but uses `std::map` instead of `vector<int>` in C++ can trigger a “Time Limit Exceeded” verdict even if the asymptotic complexity is the same.

---

## 5.6  The Good – How the Bit‑Mask DP Wins

| Advantage | Explanation |
|---|---|
| **Linear Over Subsets** | For each input string we iterate over the *current* list of masks, producing new combinations only when the masks don’t overlap.  The inner loop is `O(m)` where `m` is the number of discovered masks, never `2^n`. |
| **Constant‑time Overlap Check** | `(existingMask & newMask) == 0` is a single CPU instruction.  No expensive set operations or string scans. |
| **Built‑in Popcount** | `Integer.bitCount()` / `__builtin_popcount()` / `int.bit_count()` gives you the length instantly, no extra counting loop. |
| **No Recursion** | Eliminates stack‑overflow worries; makes the solution trivially portable to LeetCode’s sandbox. |

---

## 5.7  The Bad – When the Simple Solution Fails

| Issue | What to watch for |
|---|---|
| **Exponential Time** | Even though `|arr| ≤ 16`, a bad implementation that duplicates work (`O(2^n * 2^n)`) will TLE on the hidden test set.  Always snapshot the mask list size before mutating it. |
| **Memory Overhead** | Storing every valid mask can balloon memory usage if you’re naïve.  Keep the list as a `vector<int>` (C++), `ArrayList<Integer>` (Java), or `list[int]` (Python).  Avoid `HashSet` unless you have a compelling reason. |
| **Duplicate Inside a String** | A string like `"abcabc"` must be ignored.  Failing to filter it out leads to masks of 0 and subtle bugs in the DP loop. |

---

## 5.8  The Ugly – What Recruiters Don’t Like (and How to Fix It)

1. **Hard‑to‑Debug Recursion**  
   Recursive DFS is elegant, but the hidden global state (`visited`) makes unit testing tricky.  Use pure functions whenever possible.

2. **Wrong Popcount Version**  
   In Python, using `bin(mask).count('1')` is slower than `bit_count()`.  Interviewers test performance; using the wrong function can cause a **Wrong Answer** verdict on the “largest length” test case.

3. **Signed‑Integer Shifts in C++**  
   Shifting negative ints is undefined behaviour.  Use `unsigned int` or cast to `uint32_t` before shifting.

4. **Over‑Optimizing Prematurely**  
   Adding micro‑optimisations (like `__builtin_popcount` with a custom inline function) can make the code unreadable.  Keep the balance between speed and clarity.

---

## 5.9  How to Turn This Into a Job‑Ready Skill

1. **Write Clean, Testable Code** – Separate helpers (`stringToMask`) from the core algorithm.  
2. **Comment Strategically** – Explain *why* you do a bit‑mask check, not just *how*.  
3. **Cross‑Language Consistency** – Show that the same logic works in Java, Python, and C++.  Recruiters love candidates who can port algorithms.  
4. **Add Unit Tests** – Even in LeetCode, practice with `main()` that runs multiple edge cases.  
5. **Explain Complexity on the Spot** – During interviews, talk through the `O(n·2^n)` time and `O(2^n)` space, and why the limits guarantee a solution will finish in < 1 second.

---

## 5.10  Final Thoughts

LeetCode #1547 is more than a string puzzle; it’s a **badge of efficiency**.  
- **Good** – Bit‑mask DP gives you lightning‑fast runtime, minimal stack usage, and clear, maintainable code.  
- **Bad** – The problem is inherently exponential; no linear‑time solution exists.  
- **Ugly** – Many candidates fall into recursive pitfalls or forget to handle internal duplicates, leading to subtle bugs.

Master the clean DP pattern in Java, Python, and C++, practice the pitfalls listed above, and you’ll be ready to impress any hiring manager who asks you to solve **“Maximum Length of a Concatenated String with Unique Characters.”**  

Happy coding, and good luck on your next interview!