---
title: LeetCode 1048. Longest String Chain - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Mastering LeetCode 1048 – Longest String Chain  
**A Deep‑Dive into DP, Sorting & “Predecessor” Logic**  

> *Want to land a software‑engineering interview?  
>  Solve the “Longest String Chain” problem in **Java, Python, and C++** – the official solution + a polished blog‑style write‑up.*

---

### Table of Contents  

| # | Section | Time to Read |
|---|---------|--------------|
| 1 | Problem Overview | 3 min |
| 2 | Intuition & Key Insight | 5 min |
| 3 | Brute‑Force & Why It Fails | 3 min |
| 4 | Optimal DP + Sorting Strategy | 8 min |
| 5 | Code Walkthrough (Java) | 6 min |
| 6 | Code Walkthrough (Python) | 6 min |
| 7 | Code Walkthrough (C++) | 6 min |
| 8 | Edge Cases & Common Pitfalls | 4 min |
| 9 | Complexity Analysis | 2 min |
|10 | SEO & Career Take‑away | 4 min |
|11 | References & Further Reading | 1 min |

> *Total: ~45 minutes of reading, plus ~20 minutes to code and run tests.*

---

## 1. Problem Overview (LeetCode #1048)

> **Longest String Chain**  
> *Medium*  

Given an array `words` of lowercase English words, we can call `wordA` a **predecessor** of `wordB` if we can insert **exactly one letter** anywhere in `wordA` (without re‑ordering the other letters) to obtain `wordB`.  
A *word chain* is a sequence `[word1, word2, … , wordk]` where each word is a predecessor of the next one.  
Return the **maximum length** of any possible chain.

**Constraints**

| # | Property | Value |
|---|----------|-------|
| 1 | `1 ≤ words.length ≤ 1000` | |
| 2 | `1 ≤ words[i].length ≤ 16` | |
| 3 | `words[i]` only lowercase English letters | |

---

## 2. Intuition & Key Insight

1. **Chain Length is Determined by Word Length**  
   Each step adds exactly one letter, so a chain of length `L` must involve words of lengths `len`, `len+1`, …, `len+L-1`.  

2. **Sorting by Length**  
   If we process words from *shortest to longest*, all possible predecessors of a word are already processed.  
   This eliminates the need for back‑tracking or heavy recursion.

3. **Dynamic Programming on Words**  
   Let `dp[w]` = longest chain ending at word `w`.  
   When we consider a word `w`, we generate all *possible predecessors* (remove one character at each position) and update `dp[w]` using their `dp` values.

4. **Hash Map for O(1) Lookup**  
   We store the `dp` values in a `HashMap` keyed by the word string.  
   Thus, computing the predecessor’s chain length is `O(1)`.

---

## 3. Brute‑Force & Why It Fails

A naive solution would:

1. Check every pair `(a, b)` to see if `a` is a predecessor of `b`.  
2. Build a graph and perform DFS/BFS to find the longest path.

With up to 1000 words, this leads to `O(n²)` pair checks, and each predecessor check costs `O(len)` → ~16 million operations – still fine, but the graph‑based longest path is *NP‑hard* for arbitrary graphs.  

The DP + sorting approach reduces it to `O(n · len²)` (~1 million operations) and guarantees correctness.

---

## 4. Optimal DP + Sorting Strategy

```text
sort words by length ascending
for each word w in sorted order
    dp[w] = 1                       // base case: chain of length 1
    for each index i in w
        predecessor = w with char at i removed
        if predecessor exists in dp
            dp[w] = max(dp[w], dp[predecessor] + 1)
answer = max(dp.values)
```

*Why it works*  
Because all predecessors of `w` are shorter than `w`, they have already been processed and their `dp` values are final. The recurrence relation is exactly the classic **Longest Increasing Subsequence** on a DAG.

---

## 5. Code Walkthrough – Java 17

```java
import java.util.*;

public class Solution {
    public int longestStrChain(String[] words) {
        // 1️⃣ Sort by length ascending
        Arrays.sort(words, Comparator.comparingInt(String::length));

        // 2️⃣ DP map: word -> longest chain ending at this word
        Map<String, Integer> dp = new HashMap<>();

        int best = 1;                       // at least one word

        for (String w : words) {
            int cur = 1;                    // chain length for w

            // Generate all predecessors by removing one char
            for (int i = 0; i < w.length(); i++) {
                StringBuilder sb = new StringBuilder(w);
                sb.deleteCharAt(i);
                String pred = sb.toString();

                if (dp.containsKey(pred)) {
                    cur = Math.max(cur, dp.get(pred) + 1);
                }
            }

            dp.put(w, cur);
            best = Math.max(best, cur);
        }
        return best;
    }
}
```

**Highlights**

- `Arrays.sort` uses a stable sort, complexity `O(n log n)`.  
- Building each predecessor costs `O(len)` → at most 16 operations.  
- HashMap ensures `O(1)` look‑ups.  

---

## 6. Code Walkthrough – Python 3.11

```python
from typing import List
from collections import defaultdict

class Solution:
    def longestStrChain(self, words: List[str]) -> int:
        # 1️⃣ Sort by length
        words.sort(key=len)

        dp = defaultdict(int)          # word -> longest chain ending at word
        best = 1

        for w in words:
            cur = 1
            # generate all predecessors
            for i in range(len(w)):
                pred = w[:i] + w[i+1:]
                if dp[pred]:
                    cur = max(cur, dp[pred] + 1)
            dp[w] = cur
            best = max(best, cur)

        return best
```

- Python’s `defaultdict(int)` behaves like a hash map with default 0.  
- String slicing is efficient for the small length (`≤ 16`).

---

## 7. Code Walkthrough – C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestStrChain(vector<string>& words) {
        sort(words.begin(), words.end(),
             [](const string &a, const string &b){ return a.size() < b.size(); });

        unordered_map<string, int> dp;
        int best = 1;

        for (const string &w : words) {
            int cur = 1;
            string pred;
            pred.reserve(w.size());

            // remove each character once
            for (int i = 0; i < (int)w.size(); ++i) {
                pred = w.substr(0, i) + w.substr(i + 1);
                auto it = dp.find(pred);
                if (it != dp.end())
                    cur = max(cur, it->second + 1);
            }
            dp[w] = cur;
            best = max(best, cur);
        }
        return best;
    }
};
```

- `unordered_map` gives amortized `O(1)` look‑ups.  
- `substr` is safe because word length is tiny.

---

## 8. Edge Cases & Common Pitfalls

| Issue | What Happens | Fix |
|-------|--------------|-----|
| Duplicate words | `dp` will overwrite same key | Either remove duplicates first or use `max` when inserting. |
| Word length 1 | Predecessor is empty string | `dp` will ignore as empty key is absent. |
| All words unrelated | Answer should be 1 | Base case `cur = 1` handles this. |
| Large memory usage | 1000 words × 16 chars → trivial | No issue; map size ≤ 1000. |
| Sorting stability | Not required | Any stable or unstable sort works. |

---

## 9. Complexity Analysis

| Step | Complexity |
|------|------------|
| Sorting | `O(n log n)` |
| DP Loop | Each word `w` of length `L` → `O(L)` predecessor checks → `O(n · L)` (L ≤ 16) |
| Hash Map Ops | Amortized `O(1)` per lookup/insert |

**Total**: `O(n log n + n · L)` ≈ `O(n log n)` for `n ≤ 1000`.  
**Memory**: `O(n)` for the map + `O(n)` for the words array.

---

## 10. SEO & Career Take‑away

### Keywords  
- “Longest String Chain Leetcode”  
- “Dynamic Programming LeetCode 1048”  
- “Java Leetcode solution”  
- “Python Leetcode 1048”  
- “C++ Leetcode 1048”  
- “DP interview question”  
- “Software engineer interview prep”

### Why Recruiters Love This Problem

| Skill | How the Problem Showcases It |
|-------|------------------------------|
| **Dynamic Programming** | Classic DP on sequences; you show understanding of state definition & recurrence. |
| **Greedy Sorting** | Sorting by length is a clean, greedy step that simplifies the DP. |
| **Hashing** | Efficient O(1) look‑ups for predecessor checks. |
| **Time‑Space Trade‑offs** | You discuss why the chosen solution is optimal versus brute‑force. |
| **Clean Code** | Demonstrates ability to write concise, readable code across languages. |

#### How to Frame It on LinkedIn or Resume

> *Solved “Longest String Chain” (LeetCode #1048) – O(n log n) DP solution using greedy sorting and hash maps. Reduced complexity from naive O(n²) to O(n log n). Implemented in Java, Python, and C++.*

---

## 11. References & Further Reading

1. LeetCode Problem 1048 – https://leetcode.com/problems/longest-string-chain/  
2. “Video How We Think About a Solution” – 1‑minute video insight.  
3. DP & Top‑Down Memoization blog posts – for deeper theory.  
4. TopCoder “Longest Chain” – similar DP pattern.  

---

### Final Thought

The **Longest String Chain** problem is a classic example of turning a seemingly graph‑heavy question into a clean DP with a clever observation: *word length drives the chain*. Master this pattern and you’ll be ready for a wide range of interview questions that test DP, hashing, and algorithmic reasoning. Good luck on your next coding interview! 🚀