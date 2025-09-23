---
title: LeetCode 527. Word Abbreviation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 527. Word Abbreviation – LeetCode Hard  
### A Complete, Multi‑Language Solution (Java | Python | C++) + SEO‑Optimized Blog Post

---

## 🚀 TL;DR  
- **Goal**: Return the shortest unique abbreviation for every word in a list of distinct words.  
- **Key idea**: Group words by their *initial* abbreviation, then build a **prefix trie** for each group to find the minimal unique prefix length.  
- **Complexities**:  
  - Time – **O(Σ |word|)** (linear in the total number of characters).  
  - Space – **O(Σ |word|)** for the trie nodes.  

The following sections give a step‑by‑step explanation and provide ready‑to‑copy code in **Java**, **Python**, and **C++**. After the code, a short blog‑style article explains the problem, the trade‑offs, and interview‑friendly insights.

---

## 📚 Problem Recap (LeetCode 527)

Given an array of distinct strings `words`, for each word we need to produce its minimal abbreviation following these rules:

1. **Initial abbreviation**:  
   `first_letter + (len - 2) + last_letter`  
   (if the abbreviation is not shorter than the word, keep the word unchanged).

2. **Collision resolution**:  
   If two words share the same abbreviation, **extend the prefix** of each by one character and recompute the abbreviation.  
   Repeat until every abbreviation is unique.

Example  
```text
words = ["like","god","internal","me","internet","interval","intension","face","intrusion"]

Output = ["l2e","god","internal","me","i6t","interval","inte4n","f2e","intr4n"]
```

---

## ⚙️ High‑Level Algorithm

1. **Compute the initial abbreviation** for every word.  
2. **Group words that collide** (i.e., share the same abbreviation).  
3. For each group:  
   1. Build a **prefix trie** from the suffix part of each word (starting from the second character).  
   2. For each word in the group, walk the trie until we reach a node with `count == 1`.  
   3. The depth at that node tells us how many characters we need to keep in the prefix.  
   4. Re‑compute the abbreviation with this new prefix length.  
4. Return the final abbreviations in the original order.

---

## 📦 Code Implementations

Below are three idiomatic solutions for the same problem, one each in **Java**, **Python**, and **C++**.

> **Tip**: All implementations share the same core logic: *group → trie → unique prefix length*.

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    // Trie node used for each group
    private static class TrieNode {
        TrieNode[] children = new TrieNode[26];
        int count = 0;          // how many words pass through this node
    }

    // Helper to build the abbreviation
    private static String abbrev(String word, int prefixLen) {
        int n = word.length();
        if (n - prefixLen <= 3) return word; // abbreviation not shorter
        return word.substring(0, prefixLen + 1) + (n - prefixLen - 2) + word.charAt(n - 1);
    }

    public List<String> wordsAbbreviation(List<String> words) {
        int n = words.size();
        String[] ans = new String[n];

        // Step 1: group by initial abbreviation
        Map<String, List<int[]>> groups = new HashMap<>(); // value: {index, prefixLen}
        for (int i = 0; i < n; i++) {
            String w = words.get(i);
            String abbr = abbrev(w, 0);
            groups.computeIfAbsent(abbr, k -> new ArrayList<>()).add(new int[]{i, 0});
        }

        // Step 2: process each group separately
        for (List<int[]> group : groups.values()) {
            if (group.size() == 1) {           // unique already
                int idx = group.get(0)[0];
                ans[idx] = abbrev(words.get(idx), 0);
                continue;
            }

            // Build trie from the second character onward
            TrieNode root = new TrieNode();
            for (int[] pair : group) {
                String w = words.get(pair[0]);
                TrieNode cur = root;
                for (int pos = 1; pos < w.length() - 1; pos++) { // exclude last char
                    int c = w.charAt(pos) - 'a';
                    if (cur.children[c] == null) cur.children[c] = new TrieNode();
                    cur = cur.children[c];
                    cur.count++;
                }
            }

            // Find minimal unique prefix for each word
            for (int[] pair : group) {
                String w = words.get(pair[0]);
                TrieNode cur = root;
                int prefix = 1; // start with first character
                for (int pos = 1; pos < w.length() - 1; pos++) {
                    if (cur.count == 1) break; // unique
                    int c = w.charAt(pos) - 'a';
                    cur = cur.children[c];
                    prefix++;
                }
                ans[pair[0]] = abbrev(w, prefix - 1);
            }
        }

        return Arrays.asList(ans);
    }
}
```

### 2️⃣ Python

```python
from collections import defaultdict
from typing import List

class TrieNode:
    __slots__ = ("children", "count")
    def __init__(self):
        self.children = [None] * 26
        self.count = 0

class Solution:
    def _abbrev(self, word: str, prefix_len: int) -> str:
        n = len(word)
        if n - prefix_len <= 3:
            return word
        return word[:prefix_len + 1] + str(n - prefix_len - 2) + word[-1]

    def wordsAbbreviation(self, words: List[str]) -> List[str]:
        n = len(words)
        ans = [None] * n

        # Group indices by initial abbreviation
        groups = defaultdict(list)
        for idx, w in enumerate(words):
            groups[self._abbrev(w, 0)].append(idx)

        for group in groups.values():
            if len(group) == 1:
                idx = group[0]
                ans[idx] = self._abbrev(words[idx], 0)
                continue

            # Build trie
            root = TrieNode()
            for idx in group:
                w = words[idx]
                node = root
                for ch in w[1:-1]:      # skip first and last char
                    c = ord(ch) - 97
                    if node.children[c] is None:
                        node.children[c] = TrieNode()
                    node = node.children[c]
                    node.count += 1

            # Find unique prefix length
            for idx in group:
                w = words[idx]
                node = root
                prefix = 1
                for ch in w[1:-1]:
                    if node.count == 1:
                        break
                    node = node.children[ord(ch) - 97]
                    prefix += 1
                ans[idx] = self._abbrev(w, prefix - 1)

        return ans
```

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TrieNode {
    array<TrieNode*, 26> child{};
    int cnt = 0;
    TrieNode() { child.fill(nullptr); }
};

class Solution {
public:
    string abbrev(const string& w, int prefixLen) {
        int n = w.size();
        if (n - prefixLen <= 3) return w;
        return w.substr(0, prefixLen + 1) + to_string(n - prefixLen - 2) + w.back();
    }

    vector<string> wordsAbbreviation(vector<string>& words) {
        int n = words.size();
        vector<string> ans(n);

        // Group indices by initial abbreviation
        unordered_map<string, vector<int>> groups;
        for (int i = 0; i < n; ++i) {
            groups[abbrev(words[i], 0)].push_back(i);
        }

        for (auto &kv : groups) {
            auto &grp = kv.second;
            if (grp.size() == 1) {
                ans[grp[0]] = abbrev(words[grp[0]], 0);
                continue;
            }

            // Build trie for this group
            TrieNode *root = new TrieNode();
            for (int idx : grp) {
                TrieNode *node = root;
                const string &w = words[idx];
                for (size_t pos = 1; pos + 1 < w.size(); ++pos) { // exclude last char
                    int c = w[pos] - 'a';
                    if (!node->child[c]) node->child[c] = new TrieNode();
                    node = node->child[c];
                    ++node->cnt;
                }
            }

            // Find minimal unique prefix for each word
            for (int idx : grp) {
                TrieNode *node = root;
                const string &w = words[idx];
                int prefix = 1; // first character
                for (size_t pos = 1; pos + 1 < w.size(); ++pos) {
                    if (node->cnt == 1) break; // unique
                    node = node->child[w[pos] - 'a'];
                    ++prefix;
                }
                ans[idx] = abbrev(w, prefix - 1);
            }
            // (In production code we would delete all allocated nodes.)
        }

        return ans;
    }
};
```

> **Note**: In the C++ version you may want to reuse a global trie pool or use smart pointers to avoid memory leaks in a real‑world interview.

---

## 📊 Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java | **O(Σ |word|)** | **O(Σ |word|)** |
| Python | **O(Σ |word|)** | **O(Σ |word|)** |
| C++ | **O(Σ |word|)** | **O(Σ |word|)** |

`Σ |word|` = total number of characters in the input list.  
Because every character is visited a constant number of times, the algorithm is effectively linear.

---

## ❗ Edge Cases & Pitfalls

| Scenario | What to watch for |
|----------|-------------------|
| Single‑letter words | The suffix loop (`for ch in w[1:-1]`) runs zero times – trie stays empty. The algorithm still works because the group size will be `1`. |
| Very short words (`len <= 3`) | The abbreviation is never shorter – keep the word itself. |
| All words share the same abbreviation | Trie depth will be the word length – each word ends up unchanged. |
| Words with repeated middle characters | The trie counts handle this automatically. |

---

## 🧠 Good, Bad, & Ugly from an Interview Perspective

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Data structure choice** | Trie gives *exact* minimal prefix length in O(1) per word. | Using a naive pairwise comparison would be O(k² |word|) – unacceptable for 100 + words. | Forgetting to delete trie nodes (C++ memory leak) or using too much recursion depth can crash the program. |
| **Time‑space trade‑off** | Linear time and space – well within LeetCode limits. | If you were to build a trie for *all* words globally, you’d waste space; grouping first keeps the trie small. | If you store the entire suffix string instead of building a trie, you’ll waste O(|word|²) time. |
| **Readability** | Java’s static inner classes and `computeIfAbsent` make the code compact. | Python’s `__slots__` reduces memory overhead but is optional. | C++ pointer gymnastics can be hard to read; consider using `unique_ptr` or a vector of nodes. |
| **Interview take‑away** | Show that you can *group* first, then *localise* the collision‑resolution logic to a trie. | Avoid a monolithic trie for all words – it will be hard to debug and might blow up the stack. | Remember to handle the “not shorter” rule early; otherwise you’ll generate longer strings than needed. |

---

## 📖 Blog‑Style Write‑up

> **Title**: *LeetCode 527 – Word Abbreviation: A Trie‑Based Approach in Java, Python & C++*  
> **Keywords**: LeetCode Word Abbreviation, Leetcode 527 solution, Java Python C++ algorithm, interview, trie, string abbreviation

---

### LeetCode 527 – Word Abbreviation: A Blog‑style Deep Dive

#### 1. Problem Context  
The **Word Abbreviation** challenge asks you to compress a list of distinct words into their shortest unique abbreviations. It blends **string manipulation** with **collision resolution**, a common interview theme. Mastering this problem shows you can:

- Think about *minimal unique identifiers*.
- Use a **prefix trie** to discover the smallest distinguishing prefix.
- Implement an algorithm that is both **time‑efficient** and **memory‑aware**.

#### 2. Core Insight  
Start with the *naïve* abbreviation (`first + len‑2 + last`). If two words clash, the only way to differentiate them is to **extend the prefix** until the prefixes diverge. A trie built on the suffix (characters 2…n‑1) is perfect for that: each node keeps a counter of how many words go through it, so the first node with `count == 1` tells us the minimal prefix length required.

#### 3. Why a Trie?  
| Why | Alternatives | Verdict |
|-----|--------------|---------|
| **Exact prefix length** in *O(depth)*. | HashMap of all prefixes → O(k² n). | Faster and deterministic. |
| **Space‑efficient** when group sizes are small. | Flat array of words → O(k² n). | Works for large groups too. |
| **Natural for “extend prefix” logic** – each step corresponds to moving down a trie level. | Recursive string comparison → O(k² n). | More elegant. |

#### 4. Implementation Tips for Interviews

| Tip | Reason |
|-----|--------|
| **Group by initial abbreviation first** | Keeps the trie small – you never need to build a trie for 100 unique abbreviations. |
| **Use a `TrieNode` with an array of 26 pointers** | Fast access, minimal overhead. |
| **Handle the “not shorter” rule early** | Avoids generating an abbreviation that is the same or longer than the original word. |
| **Clean up the trie after each group** (C++) | Prevents memory leaks; in Java/Python GC takes care of it. |
| **Return results in original order** | Preserve input order – typical interview requirement. |

#### 5. Potential Interview Extensions

- **Space optimisation**: Instead of a full trie, keep a counter array per group and binary search the minimal prefix length.  
- **Large alphabet**: Replace `char‑to‑index` with a `unordered_map` or `unordered_set` if characters can be any Unicode code point.  
- **Parallel groups**: Process each group in parallel (Java Streams, Python `concurrent.futures`, C++ `std::async`) to reduce runtime on multicore machines.

---

## 🎯 Final Takeaway

The *group → trie → unique prefix* strategy gives a clean, linear‑time solution to LeetCode 527. It showcases:

- **Strong understanding of strings and suffixes**.
- **Effective use of the Trie data structure** for prefix discrimination.
- **Clean separation of concerns** (abbreviation helper, grouping, trie building, prefix search).

Copy‑paste the code snippets above into your IDE, run the sample tests, and you’ll be ready to ace this problem in any coding interview. Happy coding! 🏁