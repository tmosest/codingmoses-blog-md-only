---
title: LeetCode 411. Minimum Unique Word Abbreviation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Problem – LeetCode 411  
**Minimum Unique Word Abbreviation**  

> *You’re given a target word and a dictionary of words.  
>  An abbreviation is made by replacing any number of **non‑adjacent** substrings with their lengths.  
>  For example, “substitution” → “s10n”, “sub4u4”, “12”, …  
>  The abbreviation length is the number of unchanged letters plus the number of replaced blocks.  
>  Find the shortest abbreviation of the target that is **not** an abbreviation of any word in the dictionary.*

The constraints are tiny (`target.length ≤ 21`, `dictionary.length ≤ 1000`) but the brute‑force approach is still heavy – we have to check every possible abbreviation against every word in the dictionary.

The key to a fast solution is to treat an abbreviation as a **bitmask**:  
- `1` → keep the letter  
- `0` → abbreviate it  

With this view we can decide in *O(1)* whether a mask conflicts with a dictionary word.



--------------------------------------------------------------------

## 2.  High‑Level Idea

1. **Pre‑compute the “difference mask” for each dictionary word**  
   For every dictionary word that has the same length as the target, build a bitmask `diffMask` where a bit is `1` if the two words differ at that position.

2. **Iterate over all possible masks**  
   There are only `2^m` masks (`m ≤ 21`, i.e. at most 2 097 152).  
   For a mask to be *conflicting* with a dictionary word, it must **never** keep a differing position – in other words  
   ```text
   mask & diffMask == 0      // no kept letter that is different
   ```
   If the mask keeps at least one matching position for every dictionary word, the abbreviation is **unique**.

3. **Pick the mask with the minimal abbreviation length**  
   The length of an abbreviation represented by a mask is:
   - number of `1` bits (kept letters)  
   - plus the number of contiguous blocks of `0` bits (each block becomes a single number)

4. **Convert the chosen mask back to an abbreviation string**  
   Walk through the mask, emit letters when the bit is `1`, otherwise count the length of a consecutive block of `0`s and emit the number.



--------------------------------------------------------------------

## 3.  Why This Works

*If a mask keeps a differing letter, the abbreviation will obviously differ from that dictionary word.*  
So a mask is safe **iff** it keeps at least one letter that matches **every** dictionary word.  
Because we check the mask against every dictionary word, the first mask we find with minimal length is a correct answer.

The total work is  
`O( 2^m * n + 2^m * m )` –  
`2^m` masks, each checked against at most `n` dictionary words and costing `O(m)` to compute its length.  
With `m ≤ 21` this is far below the 1 second limit for Java/Python/C++.



--------------------------------------------------------------------

## 4.  Code Walk‑through

Below you’ll find **complete, ready‑to‑paste solutions** in **Java, Python, and C++**.  
All three share the same logic, only the syntax changes.

### 4.1  Java

```java
import java.util.*;

public class Solution {
    public String minAbbreviation(String target, String[] dictionary) {
        int m = target.length();
        List<Integer> diffMasks = new ArrayList<>();

        // 1. Build difference masks for every word of the same length
        for (String w : dictionary) {
            if (w.length() != m) continue;
            int mask = 0;
            for (int i = 0; i < m; ++i) {
                if (target.charAt(i) != w.charAt(i))
                    mask |= (1 << i);
            }
            diffMasks.add(mask);
        }

        int bestMask = 0;
        int bestLen = Integer.MAX_VALUE;

        // 2. Enumerate all masks
        for (int mask = 0; mask < (1 << m); ++mask) {
            // Skip masks that are too long already
            int curLen = calcAbbrLength(mask, m);
            if (curLen >= bestLen) continue;

            // 3. Check conflict with all dictionary words
            boolean ok = true;
            for (int diff : diffMasks) {
                if ((mask & diff) == 0) { // keeps only differing positions -> conflict
                    ok = false;
                    break;
                }
            }
            if (ok) {
                bestMask = mask;
                bestLen = curLen;
            }
        }

        return buildAbbr(target, bestMask);
    }

    /** Compute abbreviation length for a mask. */
    private int calcAbbrLength(int mask, int m) {
        int len = 0;
        boolean inZero = false;
        for (int i = 0; i < m; ++i) {
            if ((mask & (1 << i)) != 0) {          // keep
                if (inZero) { len++; inZero = false; }
                len++;                             // letter
            } else {                                // abbreviation
                if (!inZero) inZero = true;        // start a block
            }
        }
        if (inZero) len++;                        // trailing block
        return len;
    }

    /** Build abbreviation string from mask. */
    private String buildAbbr(String target, int mask) {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        for (int i = 0; i < target.length(); ++i) {
            if ((mask & (1 << i)) != 0) {
                if (count > 0) { sb.append(count); count = 0; }
                sb.append(target.charAt(i));
            } else {
                count++;
            }
        }
        if (count > 0) sb.append(count);
        return sb.toString();
    }
}
```

### 4.2  Python

```python
class Solution:
    def minAbbreviation(self, target: str, dictionary: List[str]) -> str:
        m = len(target)
        diff_masks = []

        # build difference masks
        for w in dictionary:
            if len(w) != m:
                continue
            mask = 0
            for i, (c1, c2) in enumerate(zip(target, w)):
                if c1 != c2:
                    mask |= 1 << i
            diff_masks.append(mask)

        best_mask = 0
        best_len = float('inf')

        for mask in range(1 << m):
            cur_len = self._abbr_len(mask, m)
            if cur_len >= best_len:
                continue

            # check conflict
            ok = True
            for diff in diff_masks:
                if (mask & diff) == 0:
                    ok = False
                    break
            if ok:
                best_mask, best_len = mask, cur_len

        return self._build_abbr(target, best_mask)

    def _abbr_len(self, mask: int, m: int) -> int:
        length = 0
        in_zero = False
        for i in range(m):
            if mask & (1 << i):
                if in_zero:
                    length += 1
                    in_zero = False
                length += 1          # letter
            else:
                if not in_zero:
                    in_zero = True
        if in_zero:
            length += 1
        return length

    def _build_abbr(self, target: str, mask: int) -> str:
        sb = []
        count = 0
        for i, ch in enumerate(target):
            if mask & (1 << i):
                if count:
                    sb.append(str(count))
                    count = 0
                sb.append(ch)
            else:
                count += 1
        if count:
            sb.append(str(count))
        return ''.join(sb)
```

### 4.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string minAbbreviation(string target, vector<string> dictionary) {
        int m = target.size();
        vector<int> diffMasks;

        // build difference masks
        for (const string& w : dictionary) {
            if ((int)w.size() != m) continue;
            int mask = 0;
            for (int i = 0; i < m; ++i)
                if (target[i] != w[i]) mask |= 1 << i;
            diffMasks.push_back(mask);
        }

        int bestMask = 0;
        int bestLen = INT_MAX;

        for (int mask = 0; mask < (1 << m); ++mask) {
            int curLen = abbrLen(mask, m);
            if (curLen >= bestLen) continue;

            bool ok = true;
            for (int diff : diffMasks) {
                if ((mask & diff) == 0) {   // conflict
                    ok = false;
                    break;
                }
            }
            if (ok) {
                bestMask = mask;
                bestLen = curLen;
            }
        }

        return buildAbbr(target, bestMask);
    }

private:
    // length of abbreviation represented by mask
    int abbrLen(int mask, int m) {
        int len = 0;
        bool inZero = false;
        for (int i = 0; i < m; ++i) {
            if (mask & (1 << i)) {          // keep letter
                if (inZero) { ++len; inZero = false; }
                ++len;                      // letter
            } else {                        // abbreviation block
                if (!inZero) inZero = true;
            }
        }
        if (inZero) ++len;                  // trailing block
        return len;
    }

    // build abbreviation string from mask
    string buildAbbr(const string& target, int mask) {
        string res;
        int cnt = 0;
        for (int i = 0; i < (int)target.size(); ++i) {
            if (mask & (1 << i)) {
                if (cnt) { res += to_string(cnt); cnt = 0; }
                res += target[i];
            } else {
                ++cnt;
            }
        }
        if (cnt) res += to_string(cnt);
        return res;
    }
};
```

All three solutions are **O(2^m · n)** and run comfortably inside the limits.



--------------------------------------------------------------------

## 5.  Edge Cases & Tips

| # | Situation | Why it matters | How we handled it |
|---|-----------|----------------|-------------------|
| 1 | Dictionary contains the target itself | That word obviously conflicts with any mask that keeps all letters. | We skip it automatically because `mask & diffMask == 0` for the mask that keeps everything. |
| 2 | No dictionary word has the same length | The abbreviation “0” (i.e. the number `m`) is always unique. | We just never add any `diffMask`, so every mask is acceptable; the algorithm picks the shortest one (usually “0”). |
| 3 | The target length is 1 | There are only two masks: keep or abbreviate. | Bit‑mask enumeration still works – the shortest unique mask will be found quickly. |
| 4 | The dictionary is huge but all words are longer/shorter | We ignore them entirely. | Filtering out the different‑length words keeps the inner loop tiny. |

> **Tip:** When you write the code in an interview, start by explaining the “mask = 1 / 0” intuition.  
>  Interviewers love when you can reduce a seemingly complicated problem to a single bitwise expression.



--------------------------------------------------------------------

## 6.  Complexity Analysis

| Step | Work |
|------|------|
| Build difference masks | `O(n · m)` |
| Enumerate masks | `2^m` masks |
| Conflict check per mask | `O(n)` |
| Compute length per mask | `O(m)` |
| **Total** | `O( 2^m · n + 2^m · m )` |
| **Space** | `O(n)` for the list of difference masks |

With `m ≤ 21` we have at most **2 097 152** masks, which is far below a 1 second time limit in any language.



--------------------------------------------------------------------

## 7.  Interview‑Ready Checklist

| ✔️ | Item | Why it matters |
|----|------|----------------|
| 1 | **Explain the mask → bitwise logic** | Shows you understood how abbreviations work. |
| 2 | **Show the pre‑computed diff masks** | Proves you can reduce repeated work. |
| 3 | **Highlight the conflict condition** `mask & diffMask == 0` | It’s the heart of the algorithm. |
| 4 | **Walk through the conversion back to a string** | Gives a chance to discuss edge cases (trailing zeros, consecutive blocks). |
| 5 | **Mention time & space** | Interpreters appreciate a clear complexity claim. |

> **Question you might ask yourself:**  
> *“Could we prune the search earlier?”*  
> Yes – if you find a mask whose length is already greater than the current best, skip the rest of the checks.  
> This small optimisation keeps the solution even faster.



--------------------------------------------------------------------

## 8.  Frequently Asked Questions (FAQ)

| Question | Answer |
|----------|--------|
| **Can we use recursion / backtracking instead of bitmask enumeration?** | Yes, but the bitmask trick makes conflict checking `O(1)` and is simpler to code. |
| **What if the dictionary contains duplicate words?** | It doesn’t matter – the `diffMask` list may contain duplicates, but the algorithm still works. |
| **What about 1‑based vs 0‑based indexing?** | We always use 0‑based positions for the masks; the conversion functions handle the mapping automatically. |
| **Is there a greedy solution that always works?** | No – the shortest unique abbreviation is not always the one that keeps the leftmost letter, etc. Brute force over masks guarantees correctness. |
| **Will this solution pass on larger inputs?** | The algorithm is specifically tuned for `m ≤ 21`. For larger `m` you would need a different strategy (e.g. DP over intervals). |

--------------------------------------------------------------------

## 9.  Resources & Further Reading

| Topic | Link |
|-------|------|
| Bitmask DP basics | <https://cp-algorithms.com/combinatorics/bitmask.html> |
| Substring abbreviation rules | <https://en.wikipedia.org/wiki/Abbreviation> |
| LeetCode discussion for 411 | <https://leetcode.com/problems/minimum-unique-word-abbreviation/discuss/> |
| Competitive programming templates | <https://github.com/atcoder/atcoder-library> |

--------------------------------------------------------------------

## 10.  Bottom Line

- Treat every abbreviation as a **bitmask**.  
- Keep **one** matching letter for every dictionary word → unique abbreviation.  
- Enumerate all `2^m` masks, check them against all dictionary words, pick the one with the minimal abbreviation length.  
- All three solutions above run in milliseconds and are ready to submit to LeetCode or bring to an interview.

Happy coding, and good luck on your next interview!