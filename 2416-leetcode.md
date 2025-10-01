---
title: LeetCode 2416. Sum of Prefix Scores of Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2416. Sum of Prefix Scores of Strings – One‑Stop Guide  
*(Java, Python, C++ solutions + a “Good‑Bad‑Ugly” blog post for interview prep)*  

---

## TL;DR

| Language | Runtime | Memory |
|----------|---------|--------|
| **Java** | O(N · L) | O(N · L) |
| **Python** | O(N · L) | O(N · L) |
| **C++** | O(N · L) | O(N · L) |

`N` – number of words (≤ 1000)  
`L` – average length of a word (≤ 1000)  

The optimal solution is a *Trie (prefix tree)*.  
Insert every word once, then query the prefix‑score for each word by walking the Trie once more.

---

## 1️⃣ The Problem in a Nutshell

> **Sum of Prefix Scores of Strings** – LeetCode 2416  
> For every word in `words`, compute the sum of the counts of *all* its non‑empty prefixes.  
> A prefix score of a string `t` is the number of words that start with `t`.

### Example

```text
words = ["abc","ab","bc","b"]

prefix scores:
- "abc": a→2, ab→2, abc→1  → 5
- "ab" : a→2, ab→2          → 4
- "bc" : b→2, bc→1          → 3
- "b"  : b→2                 → 2
```

Output: `[5, 4, 3, 2]`

---

## 2️⃣ Why a Trie Is the Right Tool

| **Pros** | **Cons** |
|----------|----------|
| Fast `O(L)` insert & query | Requires extra memory for 26 children per node |
| Natural representation of prefixes | Slight overhead when many nodes are sparse |
| Simple to extend to other prefix problems | Implementation can be verbose |

The problem is *pure prefix counting*, so a Trie is the canonical solution.

---

## 3️⃣ Algorithm – Step by Step

1. **Build the Trie**  
   *Each node keeps `cnt` – how many words pass through it.*  
   While inserting a word, increment `cnt` for every node traversed.

2. **Compute prefix scores**  
   For each word, walk the Trie again.  
   At each step, add the node’s `cnt` to the running sum.

3. **Return the array of sums**  

Time Complexity: `O(N · L)` – each character is visited twice (insert + query).  
Space Complexity: `O(N · L)` – at most one node per character.

---

## 4️⃣ Reference Implementations

> **Tip:** The Java implementation is slightly more verbose due to Java’s class‑based design.  
> The Python and C++ versions are shorter but follow the same logic.

---

### 4.1 Java

```java
import java.util.*;

class TrieNode {
    int cnt = 0;
    TrieNode[] child = new TrieNode[26];

    boolean contains(char c) { return child[c - 'a'] != null; }
    TrieNode get(char c)      { return child[c - 'a']; }
    void put(char c, TrieNode node) { child[c - 'a'] = node; }
    void inc()                { cnt++; }
}

public class Solution {
    private final TrieNode root = new TrieNode();

    private void insert(String w) {
        TrieNode node = root;
        for (char ch : w.toCharArray()) {
            if (!node.contains(ch)) node.put(ch, new TrieNode());
            node = node.get(ch);
            node.inc();                 // word passes through this node
        }
    }

    private int prefixScore(String w) {
        TrieNode node = root;
        int sum = 0;
        for (char ch : w.toCharArray()) {
            node = node.get(ch);
            sum += node.cnt;            // number of words sharing this prefix
        }
        return sum;
    }

    public int[] sumPrefixScores(String[] words) {
        for (String w : words) insert(w);
        int[] ans = new int[words.length];
        for (int i = 0; i < words.length; ++i) ans[i] = prefixScore(words[i]);
        return ans;
    }
}
```

---

### 4.2 Python

```python
class TrieNode:
    __slots__ = ('cnt', 'child')
    def __init__(self):
        self.cnt = 0
        self.child = [None] * 26

class Solution:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            idx = ord(ch) - 97
            if node.child[idx] is None:
                node.child[idx] = TrieNode()
            node = node.child[idx]
            node.cnt += 1

    def score(self, word: str) -> int:
        node = self.root
        total = 0
        for ch in word:
            node = node.child[ord(ch) - 97]
            total += node.cnt
        return total

    def sumPrefixScores(self, words: List[str]) -> List[int]:
        for w in words:
            self.insert(w)
        return [self.score(w) for w in words]
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TrieNode {
    int cnt = 0;
    array<TrieNode*, 26> child;
    TrieNode() { child.fill(nullptr); }
};

class Solution {
public:
    TrieNode* root = new TrieNode();

    void insert(const string& w) {
        TrieNode* node = root;
        for (char ch : w) {
            int idx = ch - 'a';
            if (!node->child[idx]) node->child[idx] = new TrieNode();
            node = node->child[idx];
            ++node->cnt;
        }
    }

    int score(const string& w) {
        TrieNode* node = root;
        int sum = 0;
        for (char ch : w) {
            node = node->child[ch - 'a'];
            sum += node->cnt;
        }
        return sum;
    }

    vector<int> sumPrefixScores(vector<string>& words) {
        for (auto& w : words) insert(w);
        vector<int> ans;
        ans.reserve(words.size());
        for (auto& w : words) ans.push_back(score(w));
        return ans;
    }
};
```

---

## 5️⃣ Blog Article – “The Good, the Bad, and the Ugly of Sum of Prefix Scores”

> **SEO Keywords:**  
> LeetCode 2416, Sum of Prefix Scores, Prefix Trie, Java Python C++ solutions, Interview Prep, Data Structures, Competitive Programming

---

### 5.1 Title  
**“Sum of Prefix Scores of Strings – The Complete Guide (Java / Python / C++)”**

### 5.2 Meta Description  
Master LeetCode 2416 with step‑by‑step Trie solutions in Java, Python, and C++. Understand the problem, algorithmic trade‑offs, and pitfalls for your next coding interview.

### 5.3 Introduction  
When you scroll through the LeetCode “Hard” section, the “Sum of Prefix Scores of Strings” (2416) stands out: a classic prefix‑counting problem that tests both your data‑structure intuition and coding polish. In this article we’ll:

- Break down the problem into digestible parts.  
- Show you the optimal *Trie* solution (the “good”).  
- Reveal hidden traps that cause TLE or MLE (“the bad”).  
- Discuss code‑style mistakes that crash interviews (“the ugly”).  

By the end you’ll be able to write clean, efficient solutions in Java, Python, or C++ and feel confident walking into your next interview.

### 5.4 The Good – Why Tries Win  
- **O(N · L) Time:** Each character is processed twice (insert + query).  
- **O(N · L) Memory:** One node per distinct prefix – far better than naive counting.  
- **Cache‑Friendly:** Array of 26 children keeps data contiguous.  

#### Implementation Highlights  
- `cnt` at every node tells “how many words pass here.”  
- `insert` updates counts; `query` aggregates them.  
- No string copying – we just traverse indices.

### 5.5 The Bad – Common Pitfalls  
| Mistake | Why it hurts | Fix |
|---------|--------------|-----|
| Using a `Map` instead of a fixed array | O(log 26) lookup becomes O(1) *average* but costs memory & overhead | Use `TrieNode[26]` |
| Forgetting to count the word itself | Prefix score of a word includes the word | Increment `cnt` after moving to the child |
| Re‑building the Trie for each query | Extra O(N · L) per word → O(N² · L) | Build once, then query all words |
| Not handling large inputs with recursion | Stack overflow (C++/Java recursion) | Use iterative loops or manual stack |

### 5.6 The Ugly – Code‑style “Fatal” Errors  
- **Java Omitted Null Checks** – NullPointerException on the first word.  
- **Python `child = [None]*26` inside `__init__`** – All nodes share the same list → corrupt tree.  
- **C++ `delete` of unused nodes** – Forgetting to free memory in an interview can lead to leaks.  

Tip: Always write a tiny `insert`‑/`query` pair that you can unit‑test locally before submitting.

### 5.7 Edge Cases You Must Test  
1. **All words are the same:**  
   ```text
   ["a","a","a"] → [3,3,3]
   ```  
2. **No common prefixes:**  
   ```text
   ["abc","def"] → [1,1]
   ```  
3. **Longest word length 1000:** Stress‑test memory usage.  
4. **Large `N` (1000) with random strings:** Ensure no MLE.

### 5.8 Interview‑Ready Tips  
- **Explain the Trie before coding.** Interviewers love seeing the thought process.  
- **Use `__slots__` in Python** (or `array` in C++) to keep memory tight.  
- **Comment the purpose of each field (`cnt`)** – makes the code readable in 30 sec.  
- **Show complexity after the code** – they’re always asking *“Why did you choose this approach?”*

### 5.9 Wrap‑Up & Final Thoughts  
LeetCode 2416 is a *prefix‑counting* problem that’s perfectly solved by a Trie. The algorithm is elegant, the implementation is short, and the time & memory limits are easily met.  

**Key takeaways for your interview:**

1. *Always ask yourself*: “Is this a prefix problem?”  
2. *Start with a Trie*: it’s fast, memory‑efficient, and extensible.  
3. *Avoid over‑engineering*: a 26‑element array beats a `HashMap` for small alphabets.  
4. *Test edge cases*: identical words, no common prefixes, maximum lengths.  

With the reference solutions in Java, Python, and C++, you can now craft a polished answer, ace the “Good”, dodge the “Bad”, and avoid the “Ugly” pitfalls. Good luck – and happy coding! 🚀

--- 

## 6️⃣ Final Checklist Before You Submit

1. **Build the Trie once** – don’t rebuild it for each word.  
2. **Increment counts on the child node** – includes the word itself.  
3. **Walk the Trie twice** – once for insertion, once for scoring.  
4. **Check for memory limits** – avoid `HashMap` for children.  
5. **Run a small local test** – cover duplicate words, empty strings (not allowed by constraints), and the maximum length case.

Happy solving, and may your prefix tree stand the test of time!