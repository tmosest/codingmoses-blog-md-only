---
title: LeetCode 745. Prefix and Suffix Search - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 745 – **Prefix and Suffix Search**  
> **Goal:** Find the index of a word that starts with a given prefix **and** ends with a given suffix.  
> **Languages:** Java | Python | C++  
> **Why It Matters:** This problem is a common interview question that tests knowledge of tries, string manipulation, and preprocessing for fast queries. Solving it well can land you a job at Google, Amazon, Microsoft, or any top‑tech firm.  

---

## 📖 Problem Overview

| Feature | Detail |
|---------|--------|
| **Name** | Prefix and Suffix Search |
| **Difficulty** | Hard |
| **Input** | `WordFilter(words)` – array of strings (1 ≤ `words.length` ≤ 10⁴, each 1 ≤ `words[i].length` ≤ 7) <br>`f(pref, suff)` – two strings (1 ≤ `pref.length, suff.length` ≤ 7) |
| **Output** | Index (0‑based) of the **latest** word that starts with `pref` and ends with `suff`. Return **-1** if no such word exists. |
| **Constraints** | Up to 10⁴ calls to `f`. All strings contain only lowercase English letters. |

> **Example**  
> ```text
> WordFilter wordFilter = new WordFilter(["apple"]);
> wordFilter.f("a", "e");   // → 0
> ```

---

## 🧠 Core Idea

The challenge is to answer many queries quickly.  
A naïve linear scan (`O(N)` per query) is too slow.  
**Pre‑processing** the words with a data structure that supports *prefix* and *suffix* lookups in **O(1)** or **O(L)** time (where *L* ≤ 7) is the key.

### Double Trie (Prefix + Suffix)  
Build two tries:

1. **Prefix trie** – stores all prefixes of every word.  
2. **Suffix trie** – stores all reversed suffixes of every word.  

During a query, walk both tries in parallel to gather candidate words, then intersect the two result sets and return the highest index.

**Pros**  
* Simple to understand.  
* Works well when word length is tiny (≤ 7).  

**Cons**  
* Requires two separate tries.  
* Intersecting sets for each query can be costly when many words share the same prefix/suffix.  

### Single Trie with “{” Separator (LeetCode‑Recommended)  
Insert for each word *w* all strings of the form  
`suffix + '{' + w`  (where `{` is a character lexicographically larger than ‘z’).  
During a query, construct the key `suff + '{' + pref` and search the trie.

**Pros**  
* Only **one** trie.  
* Every node stores the **maximum index** (weight) of any word that passes through it → answer is directly the node’s weight.  
* Extremely fast queries (`O(L)`).

**Cons**  
* Slightly more complex insertion logic.  
* Uses 27 pointers per node (26 letters + separator).

The blog below focuses on the **single‑trie** solution because it is the most efficient and widely used in interviews.

---

## 🛠️ Algorithm Walk‑Through

1. **Pre‑process**  
   For each word `w` at index `i`:
   * For every suffix `s` of `w` (including the empty suffix):
     * Insert the string `s + '{' + w` into the trie.  
     * While inserting, update the `weight` of each node traversed to `i`.  
   * Insert `"{" + w` to handle the case of an empty suffix.

2. **Query** `f(pref, suff)`  
   * Build the search key: `key = suff + '{' + pref`.  
   * Walk the trie following `key`.  
   * If at any point the path breaks → return **-1**.  
   * Otherwise → return the `weight` stored at the last node.  

Because we always keep the **latest** (largest index) weight, the returned value is exactly what the problem asks for.

---

## ⏱️ Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| **Constructor** | `O(N · L²)` (N = 10⁴, L ≤ 7) – but in practice ~10⁵–10⁶ operations. | `O(N · L²)` – total number of trie nodes. |
| **Query** (`f`) | `O(L)` (≤ 14 char traversals). | `O(1)` additional. |

> With such short words, the constants are negligible, giving **sub‑millisecond** per query on real machines.

---

## ⚠️ Edge Cases

| Case | How we handle it |
|------|------------------|
| Empty suffix (`suff == ""`) | Insert `"{" + w` (i.e., empty suffix + separator + word). |
| Empty prefix (`pref == ""`) | Query key is `suff + '{'`. |
| No matching word | The traversal will hit a missing node → return **-1**. |

---

## 📦 Implementation

Below are clean, production‑ready implementations for **Java**, **Python**, and **C++**.

> **Tip:** When posting your code in an interview or on your GitHub, add comments that clearly state the purpose of each part (especially the separator logic).

---

### 📌 Java Implementation (Single Trie + “{”)

```java
// Java 17
class WordFilter {
    private static final int ALPHABET_SIZE = 27; // 26 letters + '{'

    private static class TrieNode {
        TrieNode[] next = new TrieNode[ALPHABET_SIZE];
        int weight = -1;            // max index passing through this node
    }

    private final TrieNode root = new TrieNode();

    private int charIndex(char c) {
        return c == '{' ? 26 : c - 'a';
    }

    private void insert(String key, int weight) {
        TrieNode node = root;
        for (char ch : key.toCharArray()) {
            int idx = charIndex(ch);
            if (node.next[idx] == null) {
                node.next[idx] = new TrieNode();
            }
            node = node.next[idx];
            node.weight = weight; // keep the latest index
        }
    }

    public WordFilter(String[] words) {
        for (int i = 0; i < words.length; i++) {
            String w = words[i];
            // All suffixes of w
            for (int start = 0; start < w.length(); start++) {
                insert(w.substring(start) + "{" + w, i);
            }
            // Empty suffix (edge case)
            insert("{" + w, i);
        }
    }

    public int f(String pref, String suff) {
        String key = suff + "{" + pref;
        TrieNode node = root;
        for (char ch : key.toCharArray()) {
            int idx = charIndex(ch);
            if (node.next[idx] == null) return -1;
            node = node.next[idx];
        }
        return node.weight;
    }
}
```

---

### 📌 Python Implementation (Single Trie + “{”)

```python
# Python 3.9+

class TrieNode:
    __slots__ = ('next', 'weight')
    def __init__(self):
        self.next = [None] * 27      # 26 letters + '{'
        self.weight = -1


class WordFilter:
    def __init__(self, words):
        self.root = TrieNode()
        for weight, word in enumerate(words):
            # All suffixes of word
            for i in range(len(word)):
                key = word[i:] + "{" + word
                self._insert(key, weight)
            # Empty suffix
            self._insert("{" + word, weight)

    def _insert(self, key, weight):
        node = self.root
        for ch in key:
            idx = 26 if ch == '{' else ord(ch) - 97
            if node.next[idx] is None:
                node.next[idx] = TrieNode()
            node = node.next[idx]
            node.weight = weight

    def f(self, pref, suff):
        key = suff + "{" + pref
        node = self.root
        for ch in key:
            idx = 26 if ch == '{' else ord(ch) - 97
            if node.next[idx] is None:
                return -1
            node = node.next[idx]
        return node.weight
```

---

### 📌 C++ Implementation (Single Trie + “{”)

```cpp
// C++17
class WordFilter {
public:
    struct Node {
        int weight = -1;
        std::array<Node*, 27> child = {nullptr};
    };
    Node* root = new Node();

    void insert(const std::string& key, int weight) {
        Node* cur = root;
        for(char c : key) {
            int idx = (c == '{') ? 26 : c - 'a';
            if(!cur->child[idx]) cur->child[idx] = new Node();
            cur = cur->child[idx];
            cur->weight = weight;      // keep the largest index
        }
    }

    WordFilter(std::vector<std::string> words) {
        for(int i = 0; i < (int)words.size(); ++i) {
            const string& w = words[i];
            for(int j = 0; j < (int)w.size(); ++j) {
                insert(w.substr(j) + "{" + w, i);
            }
            insert("{" + w, i);  // empty suffix case
        }
    }

    int f(string pref, string suff) {
        string key = suff + "{" + pref;
        Node* cur = root;
        for(char c : key) {
            int idx = (c == '{') ? 26 : c - 'a';
            if(!cur->child[idx]) return -1;
            cur = cur->child[idx];
        }
        return cur->weight;
    }
};
```

---

## ✅ Test Cases

```text
words   = ["apple", "apply", "applesauce", "banana"]
queries =
    f("app", "le") → 2  (applesauce)
    f("ap",  "ly") → 1  (apply)
    f("ba",  "na") → 3  (banana)
    f("c",   "d")  → -1
```

Run your own unit tests with a few thousand random words to be absolutely confident.

---

## 🎨 Good, Bad, Ugly

| # | Aspect | Good | Bad | Ugly |
|---|--------|------|-----|------|
| 1 | **Time** | Pre‑processing `O(N·L²)` is trivial for *L* = 7. | None. | None. |
| 2 | **Space** | `O(N·L²)` nodes is acceptable (~10⁶). | None. | None. |
| 3 | **Readability** | Double‑trie code is intuitive but slower. | None. | Complexity of the separator trick can confuse interviewers if you skip comments. |
| 4 | **Maintainability** | Easier to extend with other string queries. | Double‑trie duplication of logic. | None. |
| 5 | **Interview Impact** | Demonstrates solid data‑structures knowledge. | None. | Over‑engineering (e.g., using `unordered_map` per node) will hurt. |

> **Takeaway:** Use the single‑trie “{” trick – it's the fastest and the one that interviewers actually expect from you.

---

## 📌 Take‑Away Checklist (Job‑Ready)

- [ ] ✅ Understand *trie* basics (children array, node weight).  
- [ ] ✅ Know why `{` is safe as a separator.  
- [ ] ✅ Implement *insert* that updates the node’s weight.  
- [ ] ✅ Build key `suffix + '{' + prefix` in queries.  
- [ ] ✅ Handle empty suffix by inserting `"{" + word`.  
- [ ] ✅ Discuss complexity: `O(N·L²)` build, `O(L)` query.  
- [ ] ✅ Show unit tests.  
- [ ] ✅ Be ready to explain *good* (speed), *bad* (complexity), *ugly* (coding trick) parts.

---

## 📚 SEO‑Friendly Blog Sections

Below is a full blog post you can paste into Medium, Dev.to, or your personal blog. It contains all the keywords that recruiters are searching for.

---

# LeetCode 745 – Master Prefix & Suffix Search in Java, Python & C++

> **Keywords**: LeetCode 745, Prefix and Suffix Search, WordFilter, trie, Java, Python, C++, interview, data structures, OOP, coding challenge, software engineer interview

## 1. What Is LeetCode 745?

LeetCode 745 challenges you to build a **WordFilter** that supports queries of the form:

```text
f(prefix, suffix) → largest index of a word that starts with prefix and ends with suffix
```

This is a classic string problem that every *software engineer interview* must master.

## 2. The Separator Trick: Why `{` Works

When you see `word + "{" + suffix` in a solution, think of:

- **No collisions**: `{` isn't a letter, so it can't appear in the input words.
- **Consistent traversal**: Each `TrieNode` stores at most 27 children (26 letters + `{`).

## 3. Building the Trie – O(N·L²) Time, Fast in Practice

Even though the theoretical build time is quadratic in word length, with *L* ≤ 7 the real‑world time is a handful of milliseconds.

### Java Code

```java
class WordFilter { /* ... */ }
```

### Python Code

```python
class WordFilter: /* ... */
```

### C++ Code

```cpp
class WordFilter { /* ... */ };
```

## 4. Querying in O(L) Time – Sub‑Millisecond Answers

All queries involve traversing at most 14 nodes (prefix + suffix ≤ 7 each). That’s why solutions pass 10⁴+ queries in < 0.01 s on a laptop.

## 5. Edge Cases & Practical Tips

- **Empty suffix** → insert `"{" + word`.  
- **Empty prefix** → key is just `suffix + "{"`.  
- **No match** → return `-1`.  
- **Unit tests**: Verify with thousands of random words.

## 6. Interview Secrets: Explain the Good, Bad & Ugly

- **Good**: Lightning‑fast queries, clean node weight updates.  
- **Bad**: Over‑engineering with hash maps per node.  
- **Ugly**: Forgetting to comment the separator logic.

## 7. Checklist for Recruiters

- Data‑structure knowledge (tries).  
- Algorithmic efficiency (O(N·L²) build, O(L) query).  
- Clean code in Java, Python, C++.  
- Comprehensive unit tests.  
- Ability to explain trade‑offs.

---

## 🎯 Final Words

If you ace LeetCode 745 with the **single trie + “{”** implementation, you're showing recruiters that you:

- Can implement sophisticated data structures.  
- Understand why certain tricks (like the separator) are necessary.  
- Deliver fast, maintainable code.

Go ahead, post the code on GitHub, write a blog, and keep practicing! 🚀

--- 

Happy coding and good luck with your interviews!