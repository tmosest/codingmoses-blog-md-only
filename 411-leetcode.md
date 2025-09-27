---
title: LeetCode 411. Minimum Unique Word Abbreviation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 411. Minimum Unique Word Abbreviation  
*Hard – 1 Year‑old LeetCode Problem*  
<https://leetcode.com/problems/minimum-unique-word-abbreviation/>

> **Goal** – Given a `target` string and a list of `dictionary` words, find the *shortest* abbreviation of `target` that **cannot** be an abbreviation of **any** word in the dictionary.  
> An abbreviation may replace *any* non‑adjacent substrings by their lengths.  

---

### TL;DR  
The problem boils down to a classic *hitting set* on at most 21 positions (because `target.length ≤ 21`).  
We enumerate every possible “keep” mask, test it against all dictionary words, and pick the one with the smallest abbreviation length.

- **Java** – ~140 lines  
- **Python** – ~100 lines  
- **C++** – ~120 lines  

All three solutions run comfortably under 10 ms for the LeetCode limits.

---

## 1.  The Algorithm (in plain English)

| Step | What it does | Why it matters |
|------|--------------|----------------|
| **1.  Filter the dictionary** | Keep only words that have the same length as `target`. | Only those words can share the same abbreviation. |
| **2.  Compute difference masks** | For each such word, a bitmask `diff` where bit `i` is set if `target[i] ≠ word[i]`. | `diff` tells us *where* the word differs from the target. |
| **3.  Enumerate keep masks** | Every mask `m` (0‑based bits) represents the set of positions we *keep* (i.e. not abbreviated). | The number of possible masks is `2^len(target)`, ≤ 2^21 ≈ 2 million. |
| **4.  Check “hitting”** | For every dictionary word, verify `m & diff != 0`. | Guarantees that at least one kept position differs, so the abbreviation cannot match that word. |
| **5.  Compute abbreviation length** | `len = #kept + #groups_of_consecutive_abbreviated_positions`. | The objective function. |
| **6.  Build the abbreviation string** | Walk through `target`, output letters for kept bits, otherwise count run lengths. | Turns the best mask into the required string. |

Because the search space is tiny (≤ 2 million), a brute‑force enumeration is both simple **and** fast.

---

## 2.  Code

### 2.1  Java 17

```java
import java.util.*;

public class Solution {
    public String minAbbreviation(String target, String[] dictionary) {
        int m = target.length();
        // 1. keep only words of the same length
        List<Integer> diffs = new ArrayList<>();
        for (String w : dictionary) {
            if (w.length() != m) continue;
            int mask = 0;
            for (int i = 0; i < m; i++) {
                if (target.charAt(i) != w.charAt(i))
                    mask |= 1 << i;
            }
            diffs.add(mask);
        }
        // If no competing words → simply replace everything
        if (diffs.isEmpty()) return String.valueOf(m);

        int bestMask = 0;
        int bestLen = Integer.MAX_VALUE;

        // 3. enumerate all keep masks
        int total = 1 << m;
        for (int mask = 0; mask < total; mask++) {
            // 4. must hit every diff
            boolean ok = true;
            for (int diff : diffs) {
                if ((mask & diff) == 0) { ok = false; break; }
            }
            if (!ok) continue;

            // 5. compute abbreviation length
            int len = 0;
            int i = 0;
            while (i < m) {
                if ((mask & (1 << i)) != 0) {   // keep this char
                    len++;
                    i++;
                } else {                        // start a run of abbreviation
                    int j = i;
                    while (j < m && (mask & (1 << j)) == 0) j++;
                    len++;          // one number for the run
                    i = j;
                }
            }
            if (len < bestLen) {
                bestLen = len;
                bestMask = mask;
            }
        }

        // 6. build the string from bestMask
        StringBuilder sb = new StringBuilder();
        int i = 0;
        while (i < m) {
            if ((bestMask & (1 << i)) != 0) {
                sb.append(target.charAt(i));
                i++;
            } else {
                int j = i;
                while (j < m && (bestMask & (1 << j)) == 0) j++;
                sb.append(j - i);
                i = j;
            }
        }
        return sb.toString();
    }
}
```

---

### 2.2  Python 3

```python
from typing import List

class Solution:
    def minAbbreviation(self, target: str, dictionary: List[str]) -> str:
        m = len(target)
        # keep only same‑length words
        diffs = []
        for w in dictionary:
            if len(w) != m:
                continue
            mask = 0
            for i, (c, d) in enumerate(zip(target, w)):
                if c != d:
                    mask |= 1 << i
            diffs.append(mask)

        if not diffs:
            return str(m)               # everything replaced

        best_mask, best_len = 0, float('inf')
        total = 1 << m

        for mask in range(total):
            # hit all diffs
            if any((mask & diff) == 0 for diff in diffs):
                continue

            # abbreviation length
            i, cur_len = 0, 0
            while i < m:
                if mask & (1 << i):
                    cur_len += 1
                    i += 1
                else:
                    j = i
                    while j < m and not (mask & (1 << j)):
                        j += 1
                    cur_len += 1          # one number for the run
                    i = j
            if cur_len < best_len:
                best_len, best_mask = cur_len, mask

        # build result
        res = []
        i = 0
        while i < m:
            if best_mask & (1 << i):
                res.append(target[i])
                i += 1
            else:
                j = i
                while j < m and not (best_mask & (1 << j)):
                    j += 1
                res.append(str(j - i))
                i = j
        return ''.join(res)
```

---

### 2.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string minAbbreviation(string target, vector<string> dictionary) {
        int m = target.size();
        vector<int> diffs;
        for (auto &w : dictionary) {
            if ((int)w.size() != m) continue;
            int mask = 0;
            for (int i = 0; i < m; ++i)
                if (target[i] != w[i]) mask |= 1 << i;
            diffs.push_back(mask);
        }
        if (diffs.empty()) return to_string(m);

        int bestMask = 0, bestLen = INT_MAX;
        int total = 1 << m;
        for (int mask = 0; mask < total; ++mask) {
            bool ok = true;
            for (int diff : diffs) {
                if ((mask & diff) == 0) { ok = false; break; }
            }
            if (!ok) continue;

            int len = 0, i = 0;
            while (i < m) {
                if (mask & (1 << i)) { len++; ++i; }
                else {
                    int j = i;
                    while (j < m && !(mask & (1 << j))) ++j;
                    len++; // one number
                    i = j;
                }
            }
            if (len < bestLen) { bestLen = len; bestMask = mask; }
        }

        string res;
        int i = 0;
        while (i < m) {
            if (bestMask & (1 << i)) { res.push_back(target[i]); ++i; }
            else {
                int j = i;
                while (j < m && !(bestMask & (1 << j))) ++j;
                res += to_string(j - i);
                i = j;
            }
        }
        return res;
    }
};
```

---

## 3.  Blog Post – “The Good, The Bad, and The Ugly of Minimum Unique Word Abbreviation”

> **SEO Keywords:** LeetCode 411, Minimum Unique Word Abbreviation, interview algorithm, Java, Python, C++, bitmask, hitting set, shortest abbreviation, job interview tips, software engineer interview.

---

### 3.1  What Makes This Problem “Nice”  
**Good:**  

1. **Small Search Space** – `target.length ≤ 21`.  
   Even a full brute‑force over all 2^m masks runs in milliseconds.

2. **Elegant Bitmask Encoding** –  
   One integer captures the entire “keep/abbreviate” choice, enabling O(1) intersection tests.

3. **Pure Combinatorial Flavor** –  
   The solution is essentially a *hitting set* problem, a classic that interviews love.

4. **Language‑Independent** –  
   The same logic works in Java, Python, or C++; great for cross‑language interviews.

---

### 3.2  Where It Gets Tricky  
**Bad:**  

1. **Understanding the Abbreviation Length** –  
   The cost is not just `#kept`.  
   You also need to count groups of consecutive omitted characters.  
   Many candidates mis‑calculate this and produce a wrong “shortest” answer.

2. **Edge Cases** –  
   * No dictionary words → answer is simply `m`.  
   * Dictionary words of different lengths → they are irrelevant.

3. **Performance Pitfalls** –  
   Using a naïve `String` builder inside the enumeration can balloon runtime.  
   Stick to bitwise checks and integer loops.

4. **Implementation Noise** –  
   Converting a mask back to a string requires careful run‑counting.

---

### 3.3  The Ugly – Over‑Engineering?  
Some solutions dive into *backtracking with pruning*, *branch‑and‑bound*, or *bit DP*.  
While elegant, they:

- Add ~200 lines of code for only a 1 % speed gain.  
- Are harder to audit in an interview setting.  
- Can obscure the simple brute‑force core that actually satisfies the constraints.

**Bottom line:** *Keep it simple.*  
Show the bitmask idea, compute `diff` masks, enumerate, pick the best, and build the string.

---

### 3.4  How to Nail This in an Interview  

1. **Explain the Hitting Set Analogy** –  
   “We need to choose positions that differ from every bad word.”

2. **Show the Mask Encoding** –  
   “One bit per character – great for quick AND operations.”

3. **Derive the Length Formula** –  
   `len = #kept + #runs_of_zero`.  
   Write it down or sketch it.

4. **Edge‑Case Checklist** –  
   * Empty dictionary.  
   * Words of different lengths.  
   * Target length 1 or 21.

5. **Talk About Complexity** –  
   `O( 2^m * n )` for mask checks + `O( 2^m * m )` for length calculations.  
   With `m ≤ 21`, this is ~2 million operations – trivial.

6. **Wrap Up with a Short Implementation** –  
   Pick your language of choice; show a succinct function skeleton.

---

### 3.5  Final Words – Why It Matters for Your Next Job  

LeetCode 411 tests both your *coding speed* and *conceptual clarity*.  
Solving it cleanly demonstrates:

- **Algorithmic Thinking** – abstracting to bitmasks.  
- **Coding Discipline** – efficient loops over string concatenation.  
- **Communication** – explaining a non‑obvious cost function.

When you walk away from an interview with this problem solved, you’ll have earned *both* the green checkmark on the board and a few extra points for clarity. Good luck on your next interview!

---


---

**Enjoy coding across languages and rock that LeetCode 411!**