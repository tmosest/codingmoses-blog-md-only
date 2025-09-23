---
title: LeetCode 616. Add Bold Tag in String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Add Bold Tag in String – LeetCode 616  
**Java, Python & C++ solutions + a “Good‑Bad‑Ugly” interview guide**  

> **Meta‑description** – Looking to nail your next coding interview? Master LeetCode 616 – *Add Bold Tag in String*. Dive into a clean Trie implementation, a boolean‑array trick, and a C++ version. Get job‑ready with the “good, bad & ugly” interview insights.  

---

## 1. 📄 Problem Statement

> Given a string `s` and an array of strings `words`, wrap **every substring** of `s` that appears in `words` with a single pair of bold tags `<b>` … `</b>`.  
> • If two such substrings overlap, merge them into one bold block.  
> • If two bold blocks are consecutive, merge them as well.  

**Example**

```
Input:  s = "aaabbb", words = ["aa","b"]
Output: "<b>aaabbb</b>"
```

**Constraints**

- `1 <= s.length <= 1000`
- `0 <= words.length <= 100`
- `1 <= words[i].length <= 1000`
- `s` and all `words[i]` contain only English letters and digits.

The problem is a classic string‑matching interview question; it’s also the basis for the LeetCode “Bold Words in String” (problem 758).  

---

## 2. 💡 Solution Overview

The main challenge is to **efficiently find all occurrences of the words** in `s` and then merge overlapping/adjacent intervals. Two popular approaches:

| Approach | Core Idea | Time | Space |
|----------|-----------|------|-------|
| **Trie + Scan** | Build a trie of all words; at every index of `s` walk the trie to find the longest match. Mark a boolean array for “bold” positions. | `O(|s| * maxLen(words))` | `O(totalLength(words)) + O(|s|)` |
| **Boolean Array + Naïve Search** | For each word, scan `s` with `indexOf`; mark matched ranges. | `O(|words| * |s|)` | `O(|s|)` |

We’ll focus on the **Trie implementation** – it’s concise, runs fast, and demonstrates a classic data structure. We’ll also show the **boolean array** variant for completeness.

---

## 3. 📌 Good, Bad & Ugly – Interview Talking Points

| # | Topic | What to Highlight | Pitfalls | Quick Fix |
|---|-------|-------------------|----------|-----------|
| 1 | **Clarify the input format** | Is `words` guaranteed non‑empty? | Forgetting to handle empty `words` → `O(0)` | Check `words.length == 0` → return `s` |
| 2 | **Define “overlap” precisely** | Two bold intervals `[l1, r1]` and `[l2, r2]` overlap if `l2 <= r1`. | Mixing inclusive/exclusive indices | Use 0‑based inclusive indices |
| 3 | **Edge cases with consecutive bold** | Merging `[0,2]` + `[3,5]` → `[0,5]` because `3 == 2+1` | Forgetting to merge → dangling tags | After marking, iterate once to insert tags when state changes |
| 4 | **Complexity justification** | Show how trie reduces worst‑case from `O(n*m)` to `O(n * maxLen)` | Over‑optimizing by caching longest match per index | Keep a single `maxEnd` during scan |
| 5 | **Testing** | Include cases with overlapping words, nested words, and identical words | Missing boundary test → `<b>` missing at end | Final check for open tag |

> **“Good”** – A clean explanation of the algorithm, careful handling of indices, and a proven complexity bound.  
> **“Bad”** – An overly verbose implementation that forgets to merge intervals or mishandles the last bold block.  
> **“Ugly”** – Brute‑force nested loops with string concatenation inside; it works but time‑outs on the large test set.

---

## 4. 🔧 Code Snippets

Below are **full, ready‑to‑compile** solutions in **Java**, **Python**, and **C++**. All three use the Trie approach; the Python solution also includes an alternative boolean‑array variant for comparison.

> ⚠️ **All code is tested on the LeetCode platform.**  
> 📌 **Remember to paste the code into the corresponding language section on LeetCode.**  

---

### 4.1 Java – Trie Implementation

```java
// Java 17

import java.util.*;

class Solution {

    /* ---------- Trie Node ---------- */
    private static class TrieNode {
        Map<Character, TrieNode> child = new HashMap<>();
        boolean isWord = false;
    }

    /* ---------- Trie ---------- */
    private static class Trie {
        private final TrieNode root = new TrieNode();

        void add(String word) {
            TrieNode node = root;
            for (char c : word.toCharArray()) {
                node = node.child.computeIfAbsent(c, k -> new TrieNode());
            }
            node.isWord = true;
        }

        /* Return the last index (inclusive) of the longest word
           that starts at position 'start' in 's'.
           If none, return -1. */
        int longestMatch(String s, int start) {
            TrieNode node = root;
            int end = -1;
            for (int i = start; i < s.length(); i++) {
                char c = s.charAt(i);
                node = node.child.get(c);
                if (node == null) break;
                if (node.isWord) end = i;
            }
            return end;
        }
    }

    public String addBoldTag(String s, String[] words) {
        if (words == null || words.length == 0) return s;

        Trie trie = new Trie();
        for (String w : words) trie.add(w);

        int n = s.length();
        boolean[] bold = new boolean[n];
        int maxEnd = -1;                     // farthest matched position so far

        for (int i = 0; i < n; i++) {
            int end = trie.longestMatch(s, i);   // longest word starting at i
            maxEnd = Math.max(maxEnd, end);      // merge with previous interval
            bold[i] = maxEnd >= i;               // current position is bold
        }

        StringBuilder sb = new StringBuilder();
        boolean inBold = false;
        for (int i = 0; i < n; i++) {
            if (bold[i] && !inBold) {          // start bold
                sb.append("<b>");
                inBold = true;
            } else if (!bold[i] && inBold) {    // end bold
                sb.append("</b>");
                inBold = false;
            }
            sb.append(s.charAt(i));
        }
        if (inBold) sb.append("</b>");           // close at the end
        return sb.toString();
    }

    /* ---------- Simple driver for local testing ---------- */
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.addBoldTag("abcxyz123", new String[]{"abc","123"}));
        System.out.println(sol.addBoldTag("aaabbb", new String[]{"aa","b"}));
    }
}
```

**Complexity**  
- Time: `O(|s| * maxLen(words))` – each index scans at most the longest word.  
- Space: `O(totalLength(words) + |s|)` – trie nodes + boolean array.

---

### 4.2 Python – Trie & Boolean‑Array Variant

```python
# Python 3.11

class TrieNode:
    __slots__ = ("child", "is_word")
    def __init__(self):
        self.child = {}
        self.is_word = False

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def add(self, word: str):
        node = self.root
        for ch in word:
            node = node.child.setdefault(ch, TrieNode())
        node.is_word = True

    def longest_match(self, s: str, start: int) -> int:
        node = self.root
        end = -1
        for i in range(start, len(s)):
            node = node.child.get(s[i])
            if node is None:
                break
            if node.is_word:
                end = i
        return end

class Solution:
    def addBoldTag(self, s: str, words: list[str]) -> str:
        if not words:
            return s

        trie = Trie()
        for w in words:
            trie.add(w)

        n = len(s)
        bold = [False] * n
        max_end = -1
        for i in range(n):
            end = trie.longest_match(s, i)
            max_end = max(max_end, end)
            bold[i] = max_end >= i

        res = []
        in_bold = False
        for i, ch in enumerate(s):
            if bold[i] and not in_bold:
                res.append("<b>")
                in_bold = True
            elif not bold[i] and in_bold:
                res.append("</b>")
                in_bold = False
            res.append(ch)
        if in_bold:
            res.append("</b>")
        return "".join(res)

# ---------- Boolean-array brute force (for comparison) ----------
def addBoldTag_bruteforce(s: str, words: list[str]) -> str:
    n = len(s)
    bold = [False] * n
    for w in words:
        start = s.find(w)
        while start != -1:
            for i in range(start, start + len(w)):
                bold[i] = True
            start = s.find(w, start + 1)
    res, in_bold = [], False
    for i, ch in enumerate(s):
        if bold[i] and not in_bold:
            res.append("<b>")
            in_bold = True
        elif not bold[i] and in_bold:
            res.append("</b>")
            in_bold = False
        res.append(ch)
    if in_bold:
        res.append("</b>")
    return "".join(res)

# ---------- Simple driver ----------
if __name__ == "__main__":
    sol = Solution()
    print(sol.addBoldTag("abcxyz123", ["abc","123"]))
    print(sol.addBoldTag("aaabbb", ["aa","b"]))
```

**Notes**

- `__slots__` reduces memory overhead for the trie.  
- The brute‑force variant is `O(len(words) * len(s))` and should be avoided in production.

---

### 4.3 C++ – Trie Implementation

```cpp
// C++17 (GNU++17)

#include <bits/stdc++.h>
using namespace std;

struct TrieNode {
    array<TrieNode*, 62> child; // 0-9, A-Z, a-z
    bool isWord;
    TrieNode() : child{nullptr}, isWord(false) {}
};

class Trie {
    TrieNode* root = new TrieNode();
public:
    void add(const string& word) {
        TrieNode* node = root;
        for (char ch : word) {
            int idx = charIndex(ch);
            if (!node->child[idx]) node->child[idx] = new TrieNode();
            node = node->child[idx];
        }
        node->isWord = true;
    }

    /* Return last index of longest match starting at 'start'.
       Return -1 if no match. */
    int longestMatch(const string& s, int start) const {
        TrieNode* node = root;
        int end = -1;
        for (int i = start; i < (int)s.size(); ++i) {
            int idx = charIndex(s[i]);
            node = node->child[idx];
            if (!node) break;
            if (node->isWord) end = i;
        }
        return end;
    }

private:
    // Map: '0'-'9' -> 0-9, 'A'-'Z' -> 10-35, 'a'-'z' -> 36-61
    static int charIndex(char c) {
        if ('0' <= c && c <= '9') return c - '0';
        if ('A' <= c && c <= 'Z') return 10 + (c - 'A');
        return 36 + (c - 'a');
    }
};

class Solution {
public:
    string addBoldTag(string s, vector<string> words) {
        if (words.empty()) return s;

        Trie trie;
        for (const auto& w : words) trie.add(w);

        int n = s.size();
        vector<bool> bold(n, false);
        int maxEnd = -1;
        for (int i = 0; i < n; ++i) {
            int end = trie.longestMatch(s, i);
            maxEnd = max(maxEnd, end);
            bold[i] = (maxEnd >= i);
        }

        string res;
        bool inBold = false;
        for (int i = 0; i < n; ++i) {
            if (bold[i] && !inBold) { res += "<b>"; inBold = true; }
            if (!bold[i] && inBold) { res += "</b>"; inBold = false; }
            res += s[i];
        }
        if (inBold) res += "</b>";
        return res;
    }
};
```

**Complexity** – identical to the Java version.  

> 👉 **Tip:** In C++ you can optimize the trie with an array of 62 children (10+26+26).  

---

## 5. 🎯 Takeaway for the Interview

- **Explain the algorithm step‑by‑step** (build, scan, mark, merge).  
- **Validate edge cases** (empty `words`, single‑character words, overlapping intervals).  
- **Highlight the O(∑|words|)** space/time trade‑off of the trie.  
- **Show a minimal driver** or `main` function if you’re coding outside LeetCode; it demonstrates confidence.

Remember, LeetCode’s **runtime** and **memory** counters are strict. A solution that merges intervals lazily (on‑the‑fly) and **inserts tags in a single pass** will pass all tests.  

---

## 6. 🚀 Final Thought

> **Bold Words in String** is the “string‑matching” interview equivalent of a coding interview’s *best practice* problem. Mastering the trie scan + interval merge pattern not only gets you through LeetCode, but also proves your ability to handle string preprocessing, efficient lookup, and careful boundary logic—skills every senior software engineer must own.

Happy coding and good luck at your next interview! 🚀  

--- 

**💬 Got questions?**  
- Want a Rust solution?  
- Curious about Aho‑Corasick for multi‑pattern matching?  

Let me know in the comments – I’ll update the repo with more languages!