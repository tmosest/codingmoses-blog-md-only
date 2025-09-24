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
**Hard – Leetcode**

> *A string can be abbreviated by replacing any number of non‑adjacent substrings with their lengths.  
>  Given a target word and a list of dictionary words of the same length, find the shortest abbreviation of the target that is **not** an abbreviation of any dictionary word.*

---

## TL;DR – Solution Idea

* **Represent** an abbreviation by a **bitmask** (`1` = keep the letter, `0` = abbreviate).  
* For each dictionary word compute a *difference mask* (`diffMask`) – the positions where the target and the dictionary word differ.  
* An abbreviation mask is **safe** if for every word `diffMask` has at least one `1` in common (`mask & diffMask != 0`).  
* Enumerate all masks in order of increasing abbreviation length, keep the first safe one and translate it into the human‑readable abbreviation string.

The bit‑mask approach is the most “Leetcode‑friendly” solution because:

* `m ≤ 21` → at most `2^21 ≈ 2 million` masks – perfectly manageable in memory.  
* Checking a mask against all dictionary words is just a few bitwise ANDs.

The algorithm runs in **O(2^m · n)** time and **O(n)** auxiliary space (where `n` is the size of the dictionary).  
With the constraints (`m ≤ 21`, `n ≤ 1000`) this easily passes the Leetcode limits.

Below you’ll find ready‑to‑paste implementations in **Java**, **Python**, and **C++**.  
After the code we’ll dive into a deeper blog‑style discussion that covers the “good, the bad, and the ugly” of the problem, plus SEO‑friendly sections that help you get noticed by recruiters.

---

## 1. Code

### 1.1 Java (Java 17)

```java
import java.util.*;

public class MinimumUniqueWordAbbreviation {

    public String minAbbreviation(String target, String[] dictionary) {
        int m = target.length();
        // Filter dictionary: keep only words of the same length
        List<String> dict = new ArrayList<>();
        for (String w : dictionary) {
            if (w.length() == m) dict.add(w);
        }

        // Pre‑compute difference masks for every dictionary word
        int[] diffMask = new int[dict.size()];
        for (int i = 0; i < dict.size(); i++) {
            int mask = 0;
            String w = dict.get(i);
            for (int j = 0; j < m; j++) {
                if (w.charAt(j) != target.charAt(j)) {
                    mask |= (1 << j);
                }
            }
            diffMask[i] = mask;
        }

        int bestMask = -1;
        int bestLen = Integer.MAX_VALUE;

        // Enumerate all masks in order of increasing abbreviation length
        // We use a priority queue keyed by abbreviation length.
        PriorityQueue<MaskInfo> pq = new PriorityQueue<>(Comparator.comparingInt(a -> a.len));
        for (int mask = 0; mask < (1 << m); mask++) {
            int len = abbrLength(mask);
            pq.offer(new MaskInfo(mask, len));
        }

        while (!pq.isEmpty()) {
            MaskInfo mi = pq.poll();
            if (mi.len >= bestLen) break; // no better answer possible
            if (isSafe(mi.mask, diffMask)) {
                bestMask = mi.mask;
                bestLen = mi.len;
                break;  // first safe mask is the optimal one
            }
        }

        if (bestMask == -1) return ""; // shouldn't happen
        return buildAbbr(target, bestMask);
    }

    /** true if mask is not abbreviation of any dictionary word */
    private boolean isSafe(int mask, int[] diffMask) {
        for (int d : diffMask) {
            if ((mask & d) == 0) return false; // conflict
        }
        return true;
    }

    /** Abbreviation length = number of kept chars + number of 0‑groups */
    private int abbrLength(int mask) {
        int len = 0;
        boolean prevZero = false;
        for (int i = 0; i < Integer.SIZE; i++) {
            if ((mask & (1 << i)) != 0) { // keep letter
                len++;
                prevZero = false;
            } else {
                if (!prevZero) len++;   // start of a new number
                prevZero = true;
            }
        }
        return len;
    }

    /** Build the human‑readable abbreviation from the mask */
    private String buildAbbr(String target, int mask) {
        StringBuilder sb = new StringBuilder();
        int num = 0;
        for (int i = 0; i < target.length(); i++) {
            if ((mask & (1 << i)) != 0) { // keep
                if (num > 0) { sb.append(num); num = 0; }
                sb.append(target.charAt(i));
            } else {
                num++;
            }
        }
        if (num > 0) sb.append(num);
        return sb.toString();
    }

    private static class MaskInfo {
        int mask, len;
        MaskInfo(int mask, int len) { this.mask = mask; this.len = len; }
    }
}
```

**How to run**

```java
public static void main(String[] args) {
    MinimumUniqueWordAbbreviation solver = new MinimumUniqueWordAbbreviation();
    System.out.println(solver.minAbbreviation("apple", new String[]{"blade"}));          // "a4"
    System.out.println(solver.minAbbreviation("apple", new String[]{"blade","plain"})); // "1p3" (or another valid shortest)
}
```

---

### 1.2 Python 3

```python
from typing import List
import heapq

class Solution:
    def minAbbreviation(self, target: str, dictionary: List[str]) -> str:
        m = len(target)

        # Keep only words of the same length as target
        dict_words = [w for w in dictionary if len(w) == m]

        # Pre‑compute difference masks
        diff_masks = []
        for w in dict_words:
            mask = 0
            for i, (tc, wc) in enumerate(zip(target, w)):
                if tc != wc:
                    mask |= 1 << i
            diff_masks.append(mask)

        best_mask, best_len = -1, float('inf')

        # priority queue of (abbr_length, mask)
        pq = []
        for mask in range(1 << m):
            heapq.heappush(pq, (self._abbr_len(mask), mask))

        while pq:
            l, mask = heapq.heappop(pq)
            if l >= best_len:
                break
            if all(mask & d for d in diff_masks):  # safe
                best_mask, best_len = mask, l
                break

        return self._build_abbr(target, best_mask)

    @staticmethod
    def _abbr_len(mask: int) -> int:
        """Number of kept letters + number of zero‑groups."""
        length = 0
        prev_zero = False
        for i in range(32):
            if mask & (1 << i):
                length += 1
                prev_zero = False
            else:
                if not prev_zero:
                    length += 1
                prev_zero = True
        return length

    @staticmethod
    def _build_abbr(target: str, mask: int) -> str:
        res = []
        num = 0
        for i, ch in enumerate(target):
            if mask & (1 << i):
                if num:
                    res.append(str(num))
                    num = 0
                res.append(ch)
            else:
                num += 1
        if num:
            res.append(str(num))
        return ''.join(res)

# Example usage:
if __name__ == "__main__":
    sol = Solution()
    print(sol.minAbbreviation("apple", ["blade"]))                 # "a4"
    print(sol.minAbbreviation("apple", ["blade","plain","amber"])) # "1p3"
```

---

### 1.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class MinimumUniqueWordAbbreviation {
public:
    string minAbbreviation(string target, vector<string> dictionary) {
        int m = target.size();

        // Filter dictionary: only words of length m
        vector<string> dict;
        for (auto &w : dictionary) if (w.size() == m) dict.push_back(w);

        // Pre‑compute difference masks
        vector<int> diffMask;
        for (auto &w : dict) {
            int mask = 0;
            for (int i = 0; i < m; ++i)
                if (w[i] != target[i]) mask |= (1 << i);
            diffMask.push_back(mask);
        }

        int bestMask = -1;
        int bestLen  = INT_MAX;

        // Priority queue: (abbr_len, mask)
        using PI = pair<int,int>;
        priority_queue<PI, vector<PI>, greater<PI>> pq;
        for (int mask = 0; mask < (1 << m); ++mask)
            pq.emplace(abbrLen(mask), mask);

        while (!pq.empty()) {
            auto [len, mask] = pq.top(); pq.pop();
            if (len >= bestLen) break;
            bool safe = true;
            for (int d : diffMask)
                if ((mask & d) == 0) { safe = false; break; }
            if (safe) { bestMask = mask; bestLen = len; break; }
        }

        return buildAbbr(target, bestMask);
    }

private:
    static int abbrLen(int mask) {
        int len = 0;
        bool prevZero = false;
        for (int i = 0; i < 32; ++i) {
            if (mask & (1 << i)) { len++; prevZero = false; }
            else {
                if (!prevZero) len++;
                prevZero = true;
            }
        }
        return len;
    }

    static string buildAbbr(const string& target, int mask) {
        string res;
        int num = 0;
        for (int i = 0; i < target.size(); ++i) {
            if (mask & (1 << i)) {
                if (num) { res += to_string(num); num = 0; }
                res += target[i];
            } else {
                num++;
            }
        }
        if (num) res += to_string(num);
        return res;
    }
};

/* ---- Example main -------------------------------------------------- */
int main() {
    MinimumUniqueWordAbbreviation solver;
    cout << solver.minAbbreviation("apple", {"blade"}) << endl;            // a4
    cout << solver.minAbbreviation("apple", {"blade","plain","amber"})   // 1p3
         << endl;
}
```

---

## 2. Blog‑Style Discussion  
*(The “good, the bad, and the ugly” of Leetcode 411 – plus interview‑ready nuggets for recruiters)*

### 2.1 Good – What’s Beautiful About the Problem

| ✔️ | Why it’s a great interview challenge |
|---|---------------------------------------|
| **Bit‑mask elegance** | With `m ≤ 21`, a 32‑bit integer can encode every possible abbreviation. The solution feels “pure” – only a handful of bitwise operations. |
| **Combinatorial breadth** | You’re forced to think about *all* possibilities – a good test of exhaustive search skills. |
| **Deterministic output** | The answer is always unique up to “any shortest” – you never have to guess. |
| **Real‑world flavor** | Word abbreviations are a tiny sub‑problem in many auto‑complete and compression engines. It feels like a slice of “product‑level” engineering. |

### 2.2 Bad – The Pain Points

| ❌ | What could trip you up |
|---|------------------------|
| **Dictionary filtering** | Some test cases intentionally mix words of different lengths. A naive solution will mistakenly treat them as “conflicting” because the masks have zero bits in the extra positions. |
| **Abbreviation length calculation** | Off‑by‑one bugs are common when counting the number of 0‑groups. A missing `num` flush at the end will produce wrong output. |
| **Search order** | Enumerating masks *randomly* (e.g., a simple loop) may hit the optimal answer early, but a *bad* ordering can lead to exploring many masks unnecessarily. A priority queue or BFS by length guarantees the first safe mask you find is optimal. |
| **Large dictionaries** | The worst‑case `O(2^m · n)` may feel heavy, but with `m = 21` it is still fast in practice. If you see a TLE, you’re probably generating masks incorrectly or using an un‑optimized abbr‑len routine. |

### 2.3 Ugly – Corner Cases & “Tricks” You Should Know

1. **All letters same as dictionary**  
   *If every dictionary word is exactly equal to the target, no abbreviation can be safe except the full word itself.*  
   The algorithm naturally returns the full word because the mask `111…1` has `mask & diffMask != 0` for every `diffMask = 0`.  
   In code we simply return an empty string if `bestMask == -1` (shouldn’t happen).

2. **Empty dictionary**  
   The shortest abbreviation is simply “0” (i.e., the entire word abbreviated).  
   The current implementation will still produce the minimal mask `0` and output the full number (`m`).  
   If you want the literal empty string for no dictionary, just add a guard: `if (dict.isEmpty()) return Integer.toString(m);`.

3. **Large bit‑shift overflow**  
   In Java/C++ we always shift *inside* the loop that only goes up to `m`.  
   In Python we use a 32‑bit mask, but we only iterate `m` times; the extra bits are irrelevant.  

4. **Priority queue overhead**  
   The priority queue stores all `2^m` masks – at most 2 million entries.  
   It’s memory‑friendly but not strictly necessary; a simple DFS with pruning also works and uses no queue at all.  
   For interviewers, you can explain both ways – the queue gives a clean *“sorted by length”* view, DFS gives a tighter runtime in practice.

---

## 3. “Job‑Interview‑Ready” FAQ

| ❓ | Answer |
|---|--------|
| **What are the core concepts I need to mention?** | *Bitmask representation, difference mask, safe mask test, enumeration by abbreviation length.* |
| **How can I explain time complexity succinctly?** | `O(2^m · n)` time (exponential in word length, linear in dictionary size) and `O(n)` extra space. |
| **Why is `m ≤ 21` important?** | It guarantees that we can iterate over all possible masks without blowing memory or CPU. |
| **What’s the “gotcha” if the dictionary contains words of different length?** | Those words can never be abbreviated into the target, but they can’t conflict with a mask either – just filter them out. |
| **Can the solution return any of several equally short abbreviations?** | Yes – the problem states *“any shortest abbreviation”*; all the above codes return the first one found. |
| **Is this a good example for a technical interview?** | Absolutely. It tests combinatorial search, bit‑operations, and careful string manipulation—all “nice‑to‑have” skills for a backend or full‑stack role. |

---

## 4. SEO‑Friendly Sections (for your résumé or portfolio)

> **Keywords to capture recruiter attention**  
> `Leetcode 411`, `Minimum Unique Word Abbreviation`, `bitmask algorithm`, `Java solution`, `Python solution`, `C++ solution`, `job interview`, `software engineering interview`, `algorithm interview questions`, `word abbreviation`, `string manipulation`.

### 4.1 Title & Meta Description (for a blog post)

```
Title: “Leetcode 411 – Minimum Unique Word Abbreviation – Java, Python & C++”
Meta: “Solve Leetcode 411 (Minimum Unique Word Abbreviation) with clean Java, Python, and C++ code. Understand bitmask tricks, time complexity, and why this is a great interview question. Ready for your next coding interview.”
```

### 4.2 Suggested Headings for an Article

1. **Leetcode 411 – What Is the Minimum Unique Word Abbreviation?**  
2. **Why Bitmask? – The Power of 32‑Bit Numbers**  
3. **Step‑by‑Step Walk‑through (Java, Python, C++)**  
4. **Complexity Analysis & Edge‑Case Handling**  
5. **Good, Bad, & Ugly – From Interview to Real‑World**  
6. **How to Talk About This Question in a Coding Interview**  
7. **What Recruiters Really Want to Hear**  

Each heading naturally contains the target keywords, so a search‑engine crawler will surface your article for people looking for “Leetcode 411 solutions” or “word abbreviation interview questions.”

---

## 5. Final Thoughts

Leetcode 411 is a *nice* problem that forces you to think in two dimensions: **combinatorial explosion** (all masks) and **set intersection** (dictionary conflicts).  
By mastering the bit‑mask trick you not only nail this specific question but also gain a reusable technique that appears in many areas:

- *Subset DP*,  
- *Bit‑set hashing*,  
- *Compression & packing*,  
- *Permission flag checks* in security contexts.

Show it on your portfolio, explain it confidently in interviews, and you’ll stand out as someone who can *encode* logic efficiently and *decode* complex constraints into clean code.

Good luck, and happy coding!