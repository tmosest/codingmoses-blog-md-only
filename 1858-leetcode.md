---
title: LeetCode 1858. Longest Word With All Prefixes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1858 – Longest Word With All Prefixes  
**Java | Python | C++** – Full solutions + a SEO‑friendly interview‑blog

---

## TL;DR

* Problem: From an array of words, return the longest word that has **every prefix** also present in the array. If several words share the same length, pick the lexicographically smallest one. Return an empty string if no such word exists.  
* Constraints: *words.length* ≤ 10<sup>5</sup>, total characters ≤ 10<sup>5</sup>.  
* Two idiomatic solutions:
  * **HashSet + sorting** – O(N log N) time, O(N) space.  
  * **Trie + DFS/BFS** – O(total characters) time, O(total characters) space.  

The article below explains both, walks through the code, discusses trade‑offs, and gives interview‑ready insights.

---

## 1. Problem Recap (LeetCode 1858)

> Given `String[] words`, find the longest string that can be built one character at a time by other words in the array.  
> If more than one string has the same length, return the lexicographically smallest. Return `""` if none exist.

**Examples**

| Input | Output |
|-------|--------|
| `["k","ki","kir","kira","kiran"]` | `"kiran"` |
| `["a","banana","app","appl","ap","apply","apple"]` | `"apple"` |
| `["abc","bc","ab","qwe"]` | `""` |

---

## 2. Two Main Approaches

| Approach | Core Idea | Complexity | Pros | Cons |
|----------|-----------|------------|------|------|
| **HashSet + Sorting** | Sort words by length; insert into set only if the previous prefix exists. | `O(N log N)` time, `O(N)` space | Very simple, works for all constraints, easy to understand | Sorting dominates; not linear in total characters |
| **Trie + DFS/BFS** | Build a Trie of all words. Run a BFS/DFS that only visits nodes that are marked as complete words. | `O(total chars)` time, `O(total chars)` space | Linear, no sorting, good for huge inputs | Slightly more code, need careful handling of lexicographic order |

Below are full, runnable implementations in **Java, Python, and C++**.

---

## 3. Code

### 3.1 Java – HashSet + Sorting

```java
import java.util.*;

class Solution {
    public String longestWord(String[] words) {
        // Sort by length (shorter first). If lengths equal, natural order ensures
        // lexicographic ordering after we update `longest` appropriately.
        Arrays.sort(words, Comparator.comparingInt(String::length));

        Set<String> seen = new HashSet<>();
        seen.add("");                     // empty prefix is considered present
        String longest = "";

        for (String word : words) {
            String prefix = word.length() == 1 ? "" : word.substring(0, word.length() - 1);
            if (!seen.contains(prefix)) continue;   // prefix missing – skip

            seen.add(word);                       // now this word can serve as prefix

            // Update candidate only if longer or equal but lexicographically smaller
            if (word.length() > longest.length() ||
               (word.length() == longest.length() && word.compareTo(longest) < 0)) {
                longest = word;
            }
        }
        return longest;
    }
}
```

**Why it works**  
Because we process words in ascending order of length, any word we insert into `seen` has all its prefixes already checked. When we later encounter a longer word, we can be sure all its prefixes exist in the set.

### 3.2 Java – Trie (Optional)

```java
class SolutionTrie {
    private static class Node {
        Node[] nxt = new Node[26];
        boolean isWord;
    }

    public String longestWord(String[] words) {
        Arrays.sort(words, Comparator.comparingInt(String::length));
        Node root = new Node();
        String longest = "";

        for (String w : words) {
            Node cur = root;
            boolean ok = true;
            for (int i = 0; i < w.length(); i++) {
                int idx = w.charAt(i) - 'a';
                if (cur.nxt[idx] == null) cur.nxt[idx] = new Node();
                cur = cur.nxt[idx];
                if (!cur.isWord && i < w.length() - 1) { // prefix missing
                    ok = false;
                    break;
                }
            }
            if (ok) {
                cur.isWord = true;
                if (w.length() > longest.length() ||
                   (w.length() == longest.length() && w.compareTo(longest) < 0)) {
                    longest = w;
                }
            }
        }
        return longest;
    }
}
```

The Trie variant is linear in the sum of word lengths, but the HashSet variant is simpler and just as fast for the given constraints.

### 3.3 Python – HashSet

```python
from typing import List

class Solution:
    def longestWord(self, words: List[str]) -> str:
        words.sort(key=len)                 # ascending by length
        seen = {""}
        longest = ""

        for w in words:
            pref = w[:-1] if len(w) > 1 else ""
            if pref not in seen:
                continue
            seen.add(w)
            if len(w) > len(longest) or (len(w) == len(longest) and w < longest):
                longest = w
        return longest
```

### 3.4 C++ – HashSet

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string longestWord(vector<string>& words) {
        sort(words.begin(), words.end(),
             [](const string &a, const string &b){ return a.size() < b.size(); });

        unordered_set<string> seen{""};
        string longest = "";

        for (const string &w : words) {
            string pref = w.size() > 1 ? w.substr(0, w.size()-1) : "";
            if (seen.find(pref) == seen.end()) continue;

            seen.insert(w);
            if (w.size() > longest.size() ||
                (w.size() == longest.size() && w < longest)) {
                longest = w;
            }
        }
        return longest;
    }
};
```

All solutions run comfortably within LeetCode limits (`O(10^5)` words, total characters ≤ `10^5`).

---

## 4. Blog Article – “The Good, the Bad, and the Ugly of Longest Word With All Prefixes”

> **Title:** *Longest Word With All Prefixes: Interview Prep, Edge Cases & Code Walkthrough*  
> **Meta‑Description:** Master LeetCode #1858 – find the longest word with all prefixes. Read the complete Java, Python, C++ solutions, see pitfalls, and learn how to ace the interview.

### 4.1 Introduction

When interviewers throw a string‑based puzzle your first instinct is usually “sort and scan”. LeetCode 1858 *Longest Word With All Prefixes* is exactly that: you have to build words incrementally, but the twist is the requirement of **every** prefix being present. If you skip a prefix you lose the candidate. This seemingly simple rule hides subtle pitfalls – empty strings, duplicate words, and lexicographic ordering all bite at the edges.

### 4.2 The “Good” – Why It’s a Great Interview Problem

* **Conceptual clarity** – Tests knowledge of data structures (HashSet, Trie) and sorting.  
* **Scales** – Constraints allow O(N log N) or linear time, so candidates can choose an optimal strategy.  
* **Edge‑case rich** – Empty string, single‑character words, duplicate prefixes, and lexicographic tiebreakers make for a thorough discussion.  

### 4.3 The “Bad” – Common Pitfalls

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| Skipping sorting | Without ordering you might insert a word before its prefix, causing false positives | Always sort by length ascending |
| Not handling duplicates | Two identical words can create a false prefix chain | Insert words into a `Set` first, or handle duplicates gracefully |
| Wrong prefix extraction | Using `substring(0, len-1)` on a 1‑char string throws an exception | Use a conditional or check `len == 1` |
| Ignoring lexicographic tie | Returning the first longest word may not be the smallest | Compare strings when lengths equal |

### 4.4 The “Ugly” – Performance Traps

* **Quadratic time** – naïve approach of checking all prefixes of every word (`O(N * L^2)`) blows up.  
* **Trie memory bloat** – allocating a full 26‑child array for each node is wasteful when alphabet is sparse.  
* **HashSet overkill** – for extremely large input sets, the constant factor of hashing may outweigh the simplicity.

### 4.5 The Cleanest Solution – HashSet + Sorting

1. **Sort by length** – ensures that when we encounter a word, all shorter candidates were already processed.  
2. **Maintain a `Set` of valid prefixes** – start with `""`.  
3. **For each word**:  
   * Check if its prefix is in the set.  
   * If yes, add the word to the set and possibly update the answer.  
4. **Return the best candidate**.  

The code is about 30 lines and runs in `O(N log N)` time, `O(N)` space – more than enough for LeetCode’s limits.

### 4.6 The Alternative – Trie (Linear)

A Trie gives **O(total characters)** time and can be more memory‑efficient if implemented with hash maps for child nodes. It also lends itself nicely to a BFS that stops as soon as a non‑word node appears, guaranteeing the lexicographically smallest candidate without extra sorting.

### 4.7 Take‑away Tips for Interviewers

* Ask the candidate to explain why sorting is necessary.  
* Probe the handling of duplicates and lexicographic ties.  
* Request the candidate to discuss time/space trade‑offs of Trie vs HashSet.  

### 4.8 Final Thoughts

Longest Word With All Prefixes is a sweet spot for demonstrating data‑structure fluency while keeping the code readable. By mastering the two core strategies above and being mindful of the edge cases, you’ll be ready to tackle this problem and any of its variations (like “build a dictionary word chain” or “auto‑complete”).

---

## 5. Conclusion

- **Problem**: Find the longest word that can be built one character at a time using other words in the list.  
- **Solutions**: Java, Python, and C++ code provided.  
- **Strategies**: HashSet + Sorting is simple and fast; Trie is linear and elegant.  
- **Blog**: Detailed discussion of pitfalls, performance traps, and how to interview this problem.

Good luck on your next coding interview – may your answer always be *longest* and *lexicographically smallest*! 🎉

--- 

Happy coding, and remember: every prefix matters!