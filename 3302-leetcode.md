---
title: LeetCode 3302. Find the Lexicographically Smallest Valid Sequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3302. Find the Lexicographically Smallest Valid Sequence  
**Java / C++ / Python – One Greedy Solution That Runs in O(n+m)**  

---

## 🚀 TL;DR

| Language | Time | Space |
|----------|------|-------|
| **Java** | O(n+m) | O(m) |
| **C++** | O(n+m) | O(m) |
| **Python** | O(n+m) | O(m) |

*`n = word1.length`, `m = word2.length` (m < n ≤ 3·10⁵)*  

The key idea:  
1. **Pre‑compute the rightmost index in `word1` that can match each suffix of `word2`.**  
2. **Scan `word1` from left to right, greedily picking indices that either match or are safe to skip the current `word2` character** (only allowed once).  
3. The first time we can skip a character, we must do it immediately – that guarantees lexicographic minimality.

---

## 📄 Problem Restatement

You are given two lowercase strings, `word1` and `word2`.

- A string `x` is **almost equal** to `y` if you can change **at most one** character of `x` so that it becomes identical to `y`.  
- A sequence of indices `seq` is **valid** if:
  1. `seq` is sorted ascendingly.
  2. Concatenating `word1[seq[i]]` in order gives a string that is almost equal to `word2`.

Return the *lexicographically smallest* valid sequence (array of indices).  
If no valid sequence exists, return an empty array.

> **Note**: “Lexicographically smallest array” means we compare the sequences element‑by‑element, not the resulting string.

---

## 📈 Constraints

| Parameter | Range |
|-----------|-------|
| `1 ≤ word2.length < word1.length ≤ 3·10⁵` | |
| Characters | 'a' … 'z' |

---

## 🎯 The Greedy Insight

Imagine we are scanning `word1` from left to right and want to build the sequence.

1. **If the current character equals `word2[j]`, we *must* take it** – picking a later match would only shift indices right and never produce a smaller array.
2. **If it doesn’t match** we may *skip* the current `word2[j]` **once** (changing that character).  
   But we can only skip it if we can still finish the rest of `word2` using the suffix of `word1` that follows the current position.

So we need, for every position `i` in `word1`, the *earliest* position in `word2` that can be matched by the suffix `word1[i…]`.  
Pre‑computing this is the trick that lets us decide in O(1) whether skipping is safe.

---

## 📊 How the Algorithm Works

1. **Backward pass – build `last[j]`**  
   `last[j]` stores the *rightmost* index in `word1` that can match `word2[j]`.  
   Scan `word1` from right to left; whenever we see `word1[i] == word2[j]`, set `last[j] = i` and move `j` left.

2. **Forward pass – greedy build**  
   Walk `word1` again (left → right).  
   Keep two variables:  
   * `j` – current position in `word2`.  
   * `skip` – whether we have already used the one allowed mismatch (0 or 1).  

   For each `i`:
   - **Case 1 – Direct match**: `word1[i] == word2[j]` → take `i`, `j++`.
   - **Case 2 – Skip allowed** (`skip == 0`) and it is *safe* to skip `word2[j]`:
     *Safe* means that the next character of `word2` (`word2[j+1]`) can still be matched by a later index.  
     This is equivalent to `i < last[j+1]` (or if `j` is the last index, we are free to skip).  
     If the condition holds, take `i`, set `skip = 1`, `j++`.

3. **Termination**  
   If we managed to match all `m` characters (`j == m`), return the collected indices.  
   Otherwise, return an empty array.

---

## 📐 Correctness Proof Sketch

1. **Feasibility** – The algorithm only adds an index if it either matches the current `word2` character or it is a safe skip.  
   *Safe skip* guarantees that the rest of `word2` can still be matched, so the final string will be almost equal to `word2`.

2. **Lexicographic minimality** –  
   * Any index chosen earlier than a later possible match will be smaller, so we never postpone a match that is possible.  
   * The only time we postpone is to skip a character.  
     We skip *only when it is safe* and *immediately* (the first time a skip is possible).  
     Skipping later would increase the first differing index in the resulting sequence, thus violating lexicographic minimality.

3. **Uniqueness of the sequence** –  
   The greedy choices are forced: once a direct match exists we must take it; if we skip, the skip count becomes 1 and we can never skip again.  
   Therefore the algorithm produces a unique sequence that is lexicographically minimal among all valid sequences.

---

## 📚 Complexity Analysis

| Phase | Time | Space |
|-------|------|-------|
| Backward pass | O(n) | O(m) (array `last`) |
| Forward pass | O(n) | O(m) (result array) |
| **Total** | **O(n + m)** | **O(m)** |

`n` can be up to 300 000, which is well within limits for modern CPUs.

---

## ⚠️ Common Pitfalls

| Mistake | Why it fails | How to avoid |
|---------|--------------|--------------|
| **Using the first match during forward scan** | It might be wrong if a later character can be skipped safely earlier. | Pre‑compute `last` and use it to check safety before skipping. |
| **Skipping whenever you see a mismatch** | Might leave no indices to finish `word2`. | Use the `last` array to guarantee that the suffix still contains a match for the remaining suffix of `word2`. |
| **Counting mismatches incorrectly** | Off‑by‑one errors when checking `last[j+1]` for the last character. | Treat the last index specially: you can skip it at any time because there is nothing after it to match. |
| **Using too much memory** | Storing a `last` array of size `n` would double memory. | Store only `last` of size `m` (suffix length), not an array of size `n`. |

---

## 🧩 Code Implementations

### 1️⃣ Java

```java
import java.util.*;

class Solution {
    public int[] validSequence(String word1, String word2) {
        int n = word1.length(), m = word2.length();
        int[] last = new int[m];          // last[j] = rightmost index in word1 that matches word2[j]
        Arrays.fill(last, -1);
        
        // Backward pass
        int j = m - 1;
        for (int i = n - 1; i >= 0 && j >= 0; i--) {
            if (word1.charAt(i) == word2.charAt(j)) {
                last[j] = i;
                j--;
            }
        }
        
        // Forward pass
        j = 0;
        int skip = 0;
        int[] res = new int[m];
        int pos = 0; // position in res
        
        for (int i = 0; i < n && j < m; i++) {
            if (word1.charAt(i) == word2.charAt(j)) {          // direct match
                res[pos++] = i;
                j++;
            } else if (skip == 0) {                           // consider skipping
                boolean safe = (j == m - 1) || (i < last[j + 1]);
                if (safe) {
                    res[pos++] = i;
                    skip = 1;
                    j++;
                }
            }
        }
        
        return (j == m) ? res : new int[0];
    }
}
```

### 2️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> validSequence(string word1, string word2) {
        int n = word1.size(), m = word2.size();
        vector<int> last(m, -1);                     // rightmost positions

        // Backward pass
        int j = m - 1;
        for (int i = n - 1; i >= 0 && j >= 0; --i) {
            if (word1[i] == word2[j]) {
                last[j] = i;
                --j;
            }
        }

        // Forward pass
        vector<int> res;
        res.reserve(m);
        j = 0;
        int skip = 0;
        for (int i = 0; i < n && j < m; ++i) {
            if (word1[i] == word2[j]) {
                res.push_back(i);
                ++j;
            } else if (!skip) {
                bool safe = (j == m - 1) || (i < last[j + 1]);
                if (safe) {
                    res.push_back(i);
                    skip = 1;
                    ++j;
                }
            }
        }

        return (j == m) ? res : vector<int>();
    }
};
```

### 3️⃣ Python

```python
class Solution:
    def validSequence(self, word1: str, word2: str) -> List[int]:
        n, m = len(word1), len(word2)
        last = [-1] * m

        # Backward pass: compute rightmost matching indices
        j = m - 1
        for i in range(n - 1, -1, -1):
            if j >= 0 and word1[i] == word2[j]:
                last[j] = i
                j -= 1

        # Forward pass: greedy construction
        res = []
        j = 0          # current index in word2
        skip = 0       # 0 → no mismatch used yet

        for i, ch in enumerate(word1):
            if j >= m:
                break
            if ch == word2[j]:
                res.append(i)
                j += 1
            elif skip == 0:
                # safe to skip if we can still match remaining suffix
                safe = (j == m - 1) or (i < last[j + 1])
                if safe:
                    res.append(i)
                    skip = 1
                    j += 1

        return res if j == m else []
```

All three solutions use the *same* logic; the only differences are language‑specific syntactic details.

---

## 🧩 “Good, Bad, Ugly” – What This Problem Teaches

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Difficulty** | 2‑hour interview question | Requires careful O(n) reasoning | 300 000‑char inputs trip naive DP or recursion |
| **Key Insight** | A single skip is only possible if the rest of `word2` still fits | Forgetting to pre‑compute `last` leads to O(n²) or TLE | Using a full DP array (`dp[i][j]`) blows memory (3·10⁵²) |
| **Common Mistakes** | Taking the *first* matching index is wrong; you must postpone only for *safe* skips | Skipping arbitrarily gives wrong lexicographic result | Not handling the last character of `word2` (skip allowed only once) |
| **What Interviewers Love** | Linear time, O(m) memory, clear greedy reasoning | Explanation of safety check via `last[j+1]` | Demonstrating you can handle huge inputs without recursion |

---

## 🎯 How This Helps Your Job Hunt

*Why is mastering this kind of greedy problem a “career‑boost” tactic?*

1. **Interview‑Friendly** – Many tech interviews ask for *O(n)* solutions on linear‑time problems.  
   Demonstrating that you can think in terms of *two passes* and *pre‑computed safety* shows you’re comfortable with optimal‑time algorithms.

2. **Scalability Mindset** –  
   Companies like Google, Facebook, and Amazon emphasize handling data sizes in the millions.  
   Showing you can process 3·10⁵ characters in under a second (≈ 10 ms) signals that you’ll write production‑ready code.

3. **Explain‑and‑Demo** –  
   When you walk through the solution during an interview, narrate the *greedy insight*, *safety check*, and *lexicographic guarantee*.  
   That demonstrates clarity of thought—a prized soft‑skill in hiring panels.

---

## 📌 Quick Reference – Paste‑Ready Code Snippets

### Java

```java
public int[] validSequence(String word1, String word2) {
    int n = word1.length(), m = word2.length();
    int[] last = new int[m];
    Arrays.fill(last, -1);

    // Backward pass
    int j = m - 1;
    for (int i = n - 1; i >= 0 && j >= 0; i--) {
        if (word1.charAt(i) == word2.charAt(j)) {
            last[j] = i;
            j--;
        }
    }

    // Forward pass
    int[] res = new int[m];
    int idx = 0, skip = 0, j2 = 0;
    for (int i = 0; i < n && j2 < m; i++) {
        if (word1.charAt(i) == word2.charAt(j2)) {
            res[idx++] = i;
            j2++;
        } else if (skip == 0) {
            boolean safe = (j2 == m - 1) || (i < last[j2 + 1]);
            if (safe) {
                res[idx++] = i;
                skip = 1;
                j2++;
            }
        }
    }
    return (j2 == m) ? res : new int[0];
}
```

### C++

```cpp
vector<int> validSequence(string word1, string word2) {
    int n = word1.size(), m = word2.size();
    vector<int> last(m, -1);

    // Backward pass
    int j = m - 1;
    for (int i = n - 1; i >= 0 && j >= 0; --i) {
        if (word1[i] == word2[j]) {
            last[j] = i;
            --j;
        }
    }

    // Forward pass
    vector<int> res;
    res.reserve(m);
    j = 0;
    int skip = 0;
    for (int i = 0; i < n && j < m; ++i) {
        if (word1[i] == word2[j]) {
            res.push_back(i);
            ++j;
        } else if (!skip) {
            bool safe = (j == m - 1) || (i < last[j + 1]);
            if (safe) {
                res.push_back(i);
                skip = 1;
                ++j;
            }
        }
    }

    return (j == m) ? res : vector<int>();
}
```

### Python

```python
def validSequence(self, word1: str, word2: str) -> List[int]:
    n, m = len(word1), len(word2)
    last = [-1] * m

    # Backward pass
    j = m - 1
    for i in range(n - 1, -1, -1):
        if j >= 0 and word1[i] == word2[j]:
            last[j] = i
            j -= 1

    # Forward pass
    res, j, skip = [], 0, 0
    for i, ch in enumerate(word1):
        if j >= m:
            break
        if ch == word2[j]:
            res.append(i)
            j += 1
        elif skip == 0:
            safe = (j == m - 1) or (i < last[j + 1])
            if safe:
                res.append(i)
                skip = 1
                j += 1

    return res if j == m else []
```

---

## 🌐 TL;DR

- **Problem:** Find smallest‑lexicographic index array, allowing at most one mismatch.
- **Solution:** Two linear passes, pre‑compute rightmost safe positions (`last`), and enforce safety check before a single skip.
- **Complexity:** `O(n)` time, `O(m)` memory.
- **Takeaway:** Mastering such greedy patterns boosts interview performance and demonstrates scalability thinking—essential for any top‑tier tech role.

---

### 🔖 SEO Tags & Keywords (for blog readers)

`#Algorithms #Greedy #Interview #LinearTime #TwoPassAlgorithm #ScalableCode #Java #C++ #Python #TechInterviewTips #BigData #InterviewPreparation`

Happy coding and good luck on your next interview! 🚀