---
title: LeetCode 411. Minimum Unique Word Abbreviation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## ✅  Problem: Minimum Unique Word Abbreviation  
**LeetCode 712** – “Minimum Unique Word Abbreviation”

> *In a technical interview you’ll see this one on LeetCode or the company’s coding platform.  
>  Understanding the **good**, the **bad** and the **ugly** of this problem can give you a real edge when you’re preparing for a software‑engineer interview.*

---

## 📌  Problem Overview

| Detail | Description |
|--------|-------------|
| **Goal** | Find the shortest abbreviation of `target` that does **not** collide with any word in `dictionary`. |
| **Abbreviation** | A string built from the target word where consecutive replaced letters are replaced by a single digit. Example: `word` → `2o2` (first two letters replaced by “2”, keep “o”, last two letters replaced by “2”). |
| **Output** | A single string that is the *shortest* valid abbreviation. If there are multiple with the same length, any one is acceptable. |
| **Examples** | `target = "apple"`, `dictionary = ["blade","plain","amber"]` → output `4e` |

---

## 🚦  Constraints

| Constraint | Reason |
|------------|--------|
| `1 ≤ target.length ≤ 21` | Fits comfortably into a 64‑bit bitmask. |
| `dictionary.length ≤ 1000` | Large enough to rule out a naïve O(2ⁿ) search. |
| All dictionary words are **lowercase** | Simplifies character comparison. |
| Abbreviation length can be as short as 1 | Edge case: “m” (whole word replaced). |

---

## 🤯  Why Is This Hard?

1. **Exponential Search Space** – Every letter can be kept or replaced → 2²¹ possibilities.  
2. **Hidden Conflicts** – Two seemingly unrelated dictionary words can block many candidate abbreviations.  
3. **Compression vs. Conflict** – Adding more kept letters can *decrease* the number of replacement blocks (hence abbreviation length), but it also increases the count of literal letters.  
4. **Subtle Edge Cases** – Mask `0` (replace all letters) or masks that skip some letters entirely may be optimal or invalid depending on the dictionary.

---

## 🛠️  The Optimal Approach – Bitmask + Backtracking + Memoization

### 1.  Convert “conflicts” into **diff masks**

For every dictionary word that has the same length as `target` we build a 64‑bit mask where a `1` means “the letters differ at this position”.

```text
target = a p p l e
blade  = b l a d e
diff   = 0b10010  // positions 1 and 4 differ
```

If a word shares **no** differing positions with the current abbreviation mask, that abbreviation would collide with the word.

### 2.  Recursive Search

We start with an empty mask (`0` – all letters replaced).  
At each step we look at the first *unsatisfied* diff mask and **force** at least one differing bit to be kept.  
This removes that diff mask (and all others that share the chosen bit) from the “unsatisfied” list.

The recursion stops when **no diff masks remain** – every conflict is resolved.

### 3.  Pruning & Memoisation

* **Best‑Known Length** – Keep a global best length. Any path whose current abbreviation length already exceeds this is pruned.  
* **Visited Masks** – Since many different recursion paths can build the same mask, we keep a `boolean[] visited` array of size `2^m`. If a mask has been seen, we skip it.

Because the depth of recursion is at most 21 (the length of `target`) and we cut off early, the search is far faster than the brute‑force 2²¹ scan.

### 4.  Building the Abbreviation

After the optimal mask is found, we walk the target string:

* Keep a letter → add it to the result.  
* For a run of replaced letters → count the run length and add that number.

---

## 💻  Code (Java, Python, C++)

### 1. Java

```java
import java.util.*;

class Solution {
    private int bestLen;
    private long bestMask;
    private int targetLen;

    public String minAbbreviation(String target, String[] dictionary) {
        targetLen = target.length();
        List<Long> diffs = new ArrayList<>();

        /* 1. Build diff masks for same‑length words */
        for (String w : dictionary) {
            if (w.length() != targetLen) continue;
            long diff = 0;
            for (int i = 0; i < targetLen; i++) {
                if (w.charAt(i) != target.charAt(i)) diff |= 1L << i;
            }
            diffs.add(diff);
        }

        /* If nothing conflicts, the whole word can be replaced */
        if (diffs.isEmpty()) return String.valueOf(targetLen);

        int maxMask = 1 << targetLen;
        boolean[] visited = new boolean[maxMask];
        bestLen = Integer.MAX_VALUE;
        bestMask = 0L;

        dfs(0L, diffs.toArray(new long[0]), 0, visited);
        return buildAbbr(bestMask, target);
    }

    /* Recursive DFS */
    private void dfs(long mask, long[] remain, int start, boolean[] visited) {
        if (remain.length == 0) {                     // all conflicts resolved
            int cur = abbrLen(mask);
            if (cur < bestLen) {
                bestLen = cur;
                bestMask = mask;
            }
            return;
        }

        int curLen = abbrLen(mask);
        if (curLen >= bestLen) return;                // pruning

        /* Force at least one differing bit from the first remaining mask */
        long diff = remain[0];
        long bits = diff;
        while (bits != 0) {
            int pos = Long.numberOfTrailingZeros(bits);
            bits &= bits - 1;                         // next differing position

            long newMask = mask | (1L << pos);
            if (visited[(int) newMask]) continue;
            visited[(int) newMask] = true;

            /* Build new remaining list */
            int cnt = 0;
            for (long d : remain) if ((d & (1L << pos)) == 0) cnt++;
            long[] next = new long[cnt];
            int idx = 0;
            for (long d : remain) if ((d & (1L << pos)) == 0) next[idx++] = d;

            dfs(newMask, next, pos + 1, visited);
        }
    }

    /* Abbreviation length from a mask */
    private int abbrLen(long mask) {
        int len = 0, i = 0;
        while (i < targetLen) {
            if ((mask & (1L << i)) != 0) { len++; i++; }
            else {
                int j = i;
                while (j < targetLen && (mask & (1L << j)) == 0) j++;
                len++; i = j;
            }
        }
        return len;
    }

    /* Build the final abbreviation string */
    private String buildAbbr(long mask, String target) {
        StringBuilder sb = new StringBuilder();
        int i = 0;
        while (i < targetLen) {
            if ((mask & (1L << i)) != 0) {
                sb.append(target.charAt(i));
                i++;
            } else {
                int j = i;
                while (j < targetLen && (mask & (1L << j)) == 0) j++;
                sb.append(j - i);
                i = j;
            }
        }
        return sb.toString();
    }
}
```

---

### 2. Python

```python
from typing import List

class Solution:
    def minAbbreviation(self, target: str, dictionary: List[str]) -> str:
        n = len(target)
        diff_masks = []

        # 1. Build diff masks
        for w in dictionary:
            if len(w) != n:
                continue
            diff = 0
            for i, (c1, c2) in enumerate(zip(target, w)):
                if c1 != c2:
                    diff |= 1 << i
            diff_masks.append(diff)

        # Whole word can be replaced
        if not diff_masks:
            return str(n)

        best_len = n * 10   # worst possible length
        best_mask = 0
        visited = [False] * (1 << n)

        def abbr_len(mask):
            length = 0
            i = 0
            while i < n:
                if mask & (1 << i):
                    length += 1
                    i += 1
                else:
                    j = i
                    while j < n and not (mask & (1 << j)):
                        j += 1
                    length += 1
                    i = j
            return length

        def build_abbr(mask):
            res = []
            i = 0
            while i < n:
                if mask & (1 << i):
                    res.append(target[i])
                    i += 1
                else:
                    j = i
                    while j < n and not (mask & (1 << j)):
                        j += 1
                    res.append(str(j - i))
                    i = j
            return "".join(res)

        # 2. DFS with pruning
        def dfs(mask, remaining, pos):
            nonlocal best_len, best_mask
            if not remaining:
                l = abbr_len(mask)
                if l < best_len:
                    best_len, best_mask = l, mask
                return
            l = abbr_len(mask)
            if l >= best_len:
                return
            diff = remaining[0]
            bits = diff
            while bits:
                p = (bits & -bits).bit_length() - 1
                bits &= bits - 1
                new_mask = mask | (1 << p)
                if visited[new_mask]:
                    continue
                visited[new_mask] = True
                new_remain = [d for d in remaining if not (d & (1 << p))]
                dfs(new_mask, new_remain, p + 1)

        dfs(0, diff_masks, 0)
        return build_abbr(best_mask)
```

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string minAbbreviation(string target, vector<string> &dictionary) {
        int n = target.size();
        vector<long long> diffs;

        // 1. Build diff masks for same‑length words
        for (auto &w : dictionary) {
            if (w.size() != n) continue;
            long long mask = 0;
            for (int i = 0; i < n; ++i)
                if (w[i] != target[i]) mask |= 1LL << i;
            diffs.push_back(mask);
        }

        // No conflicts → whole word can be replaced
        if (diffs.empty()) return to_string(n);

        int maxMask = 1 << n;
        vector<char> visited(maxMask, 0);
        bestLen = INT_MAX;
        bestMask = 0;

        dfs(0, diffs, 0, visited, target);
        return buildAbbr(bestMask, target);
    }

private:
    int bestLen, targetLen;
    long long bestMask;

    void dfs(long long mask, vector<long long> remain, int pos,
             vector<char> &visited, string &target) {
        if (remain.empty()) {                       // all conflicts resolved
            int cur = abbrLen(mask);
            if (cur < bestLen) {
                bestLen = cur;
                bestMask = mask;
            }
            return;
        }

        int curLen = abbrLen(mask);
        if (curLen >= bestLen) return;              // pruning

        long long diff = remain[0];
        for (int i = 0; i < targetLen; ++i)
            if (diff & (1LL << i)) {
                long long newMask = mask | (1LL << i);
                if (visited[newMask]) continue;
                visited[newMask] = 1;

                vector<long long> newRemain;
                for (auto d : remain)
                    if (!(d & (1LL << i))) newRemain.push_back(d);
                dfs(newMask, newRemain, pos + 1, visited, target);
            }
    }

    int abbrLen(long long mask) {
        int len = 0, i = 0;
        while (i < targetLen) {
            if (mask & (1LL << i)) { ++len; ++i; }
            else {
                int j = i;
                while (j < targetLen && !(mask & (1LL << j))) ++j;
                ++len; i = j;
            }
        }
        return len;
    }

    string buildAbbr(long long mask, string target) {
        string res;
        for (int i = 0; i < targetLen; ) {
            if (mask & (1LL << i)) { res += target[i]; ++i; }
            else {
                int j = i;
                while (j < targetLen && !(mask & (1LL << j))) ++j;
                res += to_string(j - i);
                i = j;
            }
        }
        return res;
    }
};
```

---

## 🎯  What You’ll Remember

| Lesson | Why It Helps in Interviews |
|--------|----------------------------|
| **Bitmask to encode conflicts** | Turns the *combinatorial* problem into a *binary* one – far easier to reason about and optimize. |
| **Force a differing bit** | Guarantees that each recursion step makes progress – you never get stuck exploring useless masks. |
| **Memoise visited masks** | Cuts the search from exponential to practically linear in the number of useful masks. |
| **Prune with the best length** | Keeps the algorithm fast even when the dictionary is huge. |

---

## 📈  Practice Tips

1. **Run the LeetCode tests yourself** – copy the three implementations and hit “Submit”.  
2. **Add your own edge cases**  
   * `target = "a"` – only two possibilities (`0` → “1”, `1` → “a”).  
   * A dictionary that contains *every* word that could collide with a short abbreviation.  
3. **Walk through the recursion on paper** – choose a target of length 5 and manually list the first few recursion steps.  
4. **Explain your algorithm to a friend** – being able to articulate the bitmask strategy is a perfect “talk‑through” for a live coding interview.

---

> **Want more LeetCode‑style prep?**  
>  Check out my *free* “30‑Day LeetCode Challenge” on GitHub – it’s a curated list of the most common interview problems (including LeetCode 712) with step‑by‑step solutions and interview‑style explanations.  

Happy coding! 🚀

---