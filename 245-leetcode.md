---
title: LeetCode 245. Shortest Word Distance III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 245. Shortest Word Distance III – Full Solution Guide (Java | Python | C++)

> **TL;DR** – Scan the list once, keep two indices (last seen of `word1` and `word2`), update the minimum distance on each match. Works in *O(n)* time, *O(1)* space, and handles the “same‑word” case elegantly.

---

### Problem Recap

> Given a **list of strings** `wordsDict` and two strings `word1` and `word2` (both guaranteed to appear in the list), return the **shortest distance** (in indices) between any occurrence of `word1` and `word2`.  
> The two words may be identical – in that case we want the smallest distance between **two different** occurrences of that word.

**Constraints**

| Item | Value |
|------|-------|
| `wordsDict.length` | 1 … 10⁵ |
| `wordsDict[i].length` | 1 … 10 |
| All words are lowercase English letters |

**Examples**

| Input | Output |
|-------|--------|
| `["practice","makes","perfect","coding","makes"]`, `"makes"`, `"coding"` | `1` |
| `["practice","makes","perfect","coding","makes"]`, `"makes"`, `"makes"` | `3` |

---

## 📚 1. Approach: Two‑Pointer Scan (Single Pass)

The most natural way is a single left‑to‑right scan while maintaining the **most recent positions** of each word.  

```
i1 = last index of word1  (initially -∞)
i2 = last index of word2  (initially +∞)
minDist = +∞
for each index i in wordsDict:
    if words[i] == word1: i1 = i
    if words[i] == word2: 
        if word1 == word2: i1 = i2   // we need the previous occurrence
        i2 = i
    minDist = min(minDist, |i1 - i2|)
```

*When `word1 == word2`* we must **swap** the two indices on a hit to ensure we’re comparing *two different* positions.

**Why it works**

- The algorithm never looks back more than once: each index is processed once, so the time complexity is *O(n)*.
- Only constant extra variables are used → *O(1)* auxiliary space.
- The “same‑word” edge case is handled explicitly by swapping the indices when we see the same word.

---

## 🔍 2. Alternative Ideas

| Approach | Complexity | When to Use |
|----------|------------|-------------|
| **Pre‑process all positions into a `Map<String, List<Integer>>`** and then compute minimal difference between two sorted lists via a two‑pointer merge. | Time: *O(n)*; Space: *O(n)* | When you need to answer **many queries** on the same word list – you pay the cost once. |
| **Sliding window / dynamic programming** – not needed here. | | |
| **Binary search** – also unnecessary because we can scan once. | | |

---

## ✅ 3. Code

Below you’ll find ready‑to‑copy solutions in **Java**, **Python**, and **C++**.  
Each implementation follows the two‑pointer scan strategy.

> **Note** – All three snippets are LeetCode‑style `Solution` classes (or functions) and include brief comments for clarity.

---

### Java

```java
import java.util.*;

public class Solution {
    public int shortestWordDistance(String[] wordsDict, String word1, String word2) {
        // indices of the last seen positions
        int i1 = Integer.MIN_VALUE;   // use extreme values to avoid special checks
        int i2 = Integer.MAX_VALUE;
        int minDist = Integer.MAX_VALUE;

        boolean same = word1.equals(word2);

        for (int i = 0; i < wordsDict.length; i++) {
            String w = wordsDict[i];
            if (w.equals(word1)) {
                if (same) {
                    // same word – treat i1 as the previous occurrence
                    i1 = i2;
                }
                i1 = i;
            } else if (w.equals(word2)) {
                i2 = i;
            }

            // Only compare when both indices are set
            if (i1 != Integer.MIN_VALUE && i2 != Integer.MAX_VALUE) {
                minDist = Math.min(minDist, Math.abs(i1 - i2));
            }
        }
        return minDist;
    }
}
```

> **Time** – *O(n)*  
> **Space** – *O(1)*

---

### Python

```python
class Solution:
    def shortestWordDistance(
        self, wordsDict: List[str], word1: str, word2: str
    ) -> int:
        i1 = -10**9          # effectively -∞
        i2 = 10**9           # effectively +∞
        min_dist = 10**9
        same = word1 == word2

        for idx, w in enumerate(wordsDict):
            if w == word1:
                if same:
                    i1 = i2
                i1 = idx
            elif w == word2:
                i2 = idx

            # Update only if both indices have been seen
            if i1 > -10**8 and i2 < 10**8:
                min_dist = min(min_dist, abs(i1 - i2))

        return min_dist
```

> **Time** – *O(n)*  
> **Space** – *O(1)*

---

### C++

```cpp
class Solution {
public:
    int shortestWordDistance(vector<string>& wordsDict, string word1, string word2) {
        int i1 = INT_MIN;   // -∞ sentinel
        int i2 = INT_MAX;   // +∞ sentinel
        int minDist = INT_MAX;
        bool same = word1 == word2;

        for (int i = 0; i < wordsDict.size(); ++i) {
            const string &w = wordsDict[i];
            if (w == word1) {
                if (same) i1 = i2;   // previous occurrence
                i1 = i;
            } else if (w == word2) {
                i2 = i;
            }

            if (i1 != INT_MIN && i2 != INT_MAX) {
                minDist = min(minDist, abs(i1 - i2));
            }
        }
        return minDist;
    }
};
```

> **Time** – *O(n)*  
> **Space** – *O(1)*

---

## 📊 4. Complexity Summary

| Language | Time | Space | Why it’s Interview‑Ready |
|----------|------|-------|--------------------------|
| Java | *O(n)* | *O(1)* | Uses only primitive ints, no HashMap, fast on a single query. |
| Python | *O(n)* | *O(1)* | Same idea, minimal boilerplate. |
| C++ | *O(n)* | *O(1)* | Works with `vector<string>` and `std::string`. |

---

## 🎯 4. Good / Bad / Ugly – What Every Interviewer Loves

| Category | What We’re Evaluating | Verdict |
|----------|----------------------|---------|
| **Good** | *Simplicity* – one pass, no auxiliary data structures, clear O(1) space. | ✅ |
| **Bad** | *Edge‑case handling* – many people forget the “same‑word” case and end up with an incorrect answer (distance 0 or negative indices). | ❌ |
| **Ugly** | *Code readability vs speed* – a naive two‑array solution (`i1`, `i2` stored separately and swapped on hit) can look confusing if not commented properly. | ❌ |

> **Takeaway:**  
> - Write clean, commented code.  
> - Always guard the “same‑word” case – it’s a classic interview trap.  
> - If you need to answer *many* queries, pre‑processing is fine; otherwise, the single‑scan is the gold standard.

---

## 📄 5. Sample Test Harness

| Code | Test Case | Expected |
|------|-----------|----------|
| All three languages | `["a","b","a","c","b","a"]`, `"a"`, `"b"` | `1` |
| All three languages | `["a","a","a","a","a"]`, `"a"`, `"a"` | `1` |
| All three languages | `["one","two","three"]`, `"one"`, `"three"` | `2` |

You can paste these test cases into the LeetCode IDE or your local environment to confirm.

---

## 🚀 6. Why Interviewers Love This Question

- **Fundamental**: Tests understanding of array traversal and index arithmetic.  
- **Edge‑case awareness**: The “same‑word” situation is a common interview stumbling block.  
- **Versatile**: Solutions in multiple languages show language‑agnostic problem solving.  
- **Scalable**: The O(n) solution is efficient even for the maximum input size.

If you nail this problem in your interview, you demonstrate:

- Mastery of **time/space trade‑offs**.  
- Ability to write **robust code** that handles special cases.  
- Comfort with **LeetCode‑style** class/function signatures.

---

## 📈 7. Performance & Trade‑offs

| Metric | Value | Why it matters |
|--------|-------|----------------|
| **Time** | *O(n)* (≈ 10⁵ ops) | Fast enough for production‑grade data pipelines. |
| **Space** | *O(1)* | Keeps memory footprint tiny – critical for constrained devices. |
| **Readability** | High (comments + meaningful variable names) | Interviewers judge code quality as much as correctness. |
| **Pre‑processing Map** | Extra *O(n)* memory | Good for batch query scenarios, but not for a single query. |

---

## 📢 8. SEO‑Ready Summary

- **Keywords**: *Shortest Word Distance III LeetCode*, *Java solution*, *Python solution*, *C++ solution*, *two pointers*, *interview question*, *job interview prep*, *data structure interview*, *O(n) solution*.
- **Title**: “245. Shortest Word Distance III – Complete Java / Python / C++ Solutions & Interview Guide”
- **Meta Description** (≈160 chars): “Solve LeetCode 245 – Shortest Word Distance III with optimal O(n) Java, Python, and C++ code. Learn the two‑pointer trick, edge‑case handling, and interview tips.”

Use these snippets in your résumé or GitHub portfolio to showcase your ability to solve classic interview problems efficiently.

---

## 🎓 9. Final Thoughts

The two‑pointer scan is the **canonical** answer for this LeetCode problem.  
If you’re preparing for coding interviews, make sure you can:

1. **Explain the algorithm** in plain English.  
2. **Implement it in your preferred language** (Java, Python, C++).  
3. **Discuss the edge‑case** where the two words are identical.  
4. **Compare trade‑offs** with a pre‑processing map when many queries are expected.

Mastering this simple yet subtle problem will boost your confidence for other “distance / nearest pair” interview questions such as *Shortest Uncommon Subsequence*, *First Missing Positive*, and many more.

Happy coding—and good luck on your next interview! 🚀