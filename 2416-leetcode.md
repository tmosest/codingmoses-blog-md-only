---
title: LeetCode 2416. Sum of Prefix Scores of Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 👨‍💻 LeetCode 2416 – Sum of Prefix Scores of Strings  
**Hard** | **Java | Python | C++** | **Interview‑Ready Solution**

---

### 1. Problem Summary

Given an array `words` (1 ≤ n ≤ 1 000, each word length ≤ 1 000) consisting of lowercase English letters, define the *score* of a string `term` as the number of words in `words` that have `term` as a prefix (including itself).

For each `words[i]` we need the sum of the scores of **every non‑empty prefix** of that word.

Return an array `answer` where `answer[i]` is that sum.

---

### 2. Why a Trie?  
The classic data structure for prefix‑related problems is the **Trie (prefix tree)**.  
* Each node represents a prefix.
* A node can store the number of words that pass through it → directly the score of that prefix.
* Insertion and query are both `O(L)` where `L` is the word length.

With the constraints this is optimal (`O(total characters)` ≈ 1 000 × 1 000 = 10⁶ operations).

---

### 3. Core Idea

1. **Build the Trie**  
   For every word, walk the Trie, creating missing child nodes, and increment a `count` field on each node we visit.  
   After insertion, `node.count` equals the number of words having the prefix represented by that node.

2. **Compute the answer**  
   For each word, walk again, adding up the `count` of each visited node.  
   That sum is exactly the required prefix‑score sum.

---

### 4. Implementation Details

| Language | Node structure | How counts are updated | Complexity |
|----------|----------------|------------------------|------------|
| **Java** | `int count; Node[] child = new Node[26];` | After creating / fetching child, `child.count++`. | `O(total characters)` |
| **Python** | `int count; list[26]` (or dict) | Same: `child.count += 1`. | `O(total characters)` |
| **C++** | `int count; Node* child[26];` | `child->count++`. | `O(total characters)` |

All three use the same logic – just syntactic differences.

---

### 5. Code

#### 5.1 Java

```java
import java.util.*;

public class Solution {
    // ---------- Trie Node ----------
    private static class Node {
        int count = 0;
        Node[] child = new Node[26];

        boolean contains(char ch) { return child[ch - 'a'] != null; }
        Node get(char ch)         { return child[ch - 'a']; }
        void put(char ch, Node n) { child[ch - 'a'] = n; }
        void inc()                { count++; }
    }

    // ---------- Core ----------
    private final Node root = new Node();

    // Insert a word into the trie, incrementing counts along the path
    private void insert(String word) {
        Node cur = root;
        for (char ch : word.toCharArray()) {
            if (!cur.contains(ch)) cur.put(ch, new Node());
            cur = cur.get(ch);
            cur.inc();              // count of this prefix increases
        }
    }

    // Sum the counts of all prefixes of word
    private int prefixScoreSum(String word) {
        Node cur = root;
        int sum = 0;
        for (char ch : word.toCharArray()) {
            cur = cur.get(ch);
            sum += cur.count;
        }
        return sum;
    }

    // ---------- LeetCode API ----------
    public int[] sumPrefixScores(String[] words) {
        // 1. Build trie
        for (String w : words) insert(w);

        // 2. Answer queries
        int n = words.length;
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) ans[i] = prefixScoreSum(words[i]);
        return ans;
    }
}
```

#### 5.2 Python

```python
class Solution:
    # ---------- Trie Node ----------
    class Node:
        __slots__ = ('cnt', 'child')
        def __init__(self):
            self.cnt = 0
            self.child = [None] * 26

    def __init__(self):
        self.root = self.Node()

    # ---------- Core ----------
    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            idx = ord(ch) - 97
            if node.child[idx] is None:
                node.child[idx] = self.Node()
            node = node.child[idx]
            node.cnt += 1

    def prefix_score(self, word: str) -> int:
        node = self.root
        s = 0
        for ch in word:
            node = node.child[ord(ch) - 97]
            s += node.cnt
        return s

    # ---------- LeetCode API ----------
    def sumPrefixScores(self, words: list[str]) -> list[int]:
        for w in words:
            self.insert(w)
        return [self.prefix_score(w) for w in words]
```

#### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    struct Node {
        int cnt = 0;
        Node* child[26] = {nullptr};
    };
    Node* root = new Node();

    void insert(const string& word) {
        Node* cur = root;
        for (char ch : word) {
            int id = ch - 'a';
            if (!cur->child[id]) cur->child[id] = new Node();
            cur = cur->child[id];
            ++cur->cnt;              // this prefix now appears one more time
        }
    }

    int score(const string& word) {
        Node* cur = root;
        int s = 0;
        for (char ch : word) {
            cur = cur->child[ch - 'a'];
            s += cur->cnt;
        }
        return s;
    }

public:
    vector<int> sumPrefixScores(vector<string>& words) {
        for (const string& w : words) insert(w);
        vector<int> ans;
        ans.reserve(words.size());
        for (const string& w : words) ans.push_back(score(w));
        return ans;
    }
};
```

---

### 6. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Build trie | `O(total_chars)` | `O(total_chars)` |
| Query each word | `O(total_chars)` | — |
| **Total** | `O(total_chars)` | `O(total_chars)` |

With `n ≤ 1000` and each word ≤ 1000, the worst‑case total characters is `10⁶`, which easily fits into time limits.

---

### 7. Edge Cases & Common Pitfalls

| Issue | Fix |
|-------|-----|
| **Counting the root node** | The root represents an empty prefix – we do **not** count it. Increment counts only after moving to a child. |
| **Multiple identical words** | The trie automatically accumulates counts; identical words are handled without special code. |
| **Memory usage** | For worst case (all distinct characters), we create at most `total_chars` nodes – fine for 1 000 × 1 000. |
| **Recursion depth** | Avoid recursion; use iterative loops (shown above) to keep stack usage low. |
| **Input validation** | LeetCode guarantees lowercase letters – no need for checks. |

---

### 8. “Good, Bad, Ugly” in Interviews

| Interview Point | What the interviewer expects | What you should say |
|------------------|-----------------------------|---------------------|
| **Good** | A clean, iterative Trie that updates counts on the fly. | “I used a single pass to build the trie; the `count` field stores the score for each prefix.” |
| **Bad** | Forgetting to add counts in the query or incrementing at the wrong node. | “I made sure to ignore the empty prefix and only accumulate after visiting a child.” |
| **Ugly** | Using a hash map for every node (excessive overhead) or DFS recursion that blows the stack. | “I used a fixed array of 26 for speed and iterative traversal for safety.” |

---

### 8. Variations You Might See in Interviews

1. **Return the highest prefix‑score sum** – you can just keep a global maximum while querying.
2. **Prefix‑score for a target string only** – build the trie once, then query just that target.
3. **Online queries** – keep the trie persistent and handle insert/query interleaved.

---

### 8.1 Discussing It in the Interview

1. **State your approach**: “I’ll build a Trie where each node stores the number of words that share that prefix.”
2. **Explain the two passes**: insertion → count increment; query → sum of counts.
3. **Mention complexity**: `O(total characters)` time & space.
4. **Talk about edge cases**: identical words, empty prefixes.
5. **Optional**: Mention you could also solve it with a sorted array + binary search, but it would be `O(n log n)` per query – not as elegant as a Trie.

---

### 8.2 Why Interviewers Love the Trie

* **Data‑structure knowledge** – Shows you can pick the right structure for the problem.
* **Space‑time trade‑off** – You balance memory with performance.
* **Scalability** – Your solution works even if the constraints double.
* **Implementation detail** – Attention to node counts, iterative loops, and memory hygiene demonstrates coding discipline.

---

### 8.3 How to Nail the Interview

| Step | Recommendation |
|------|----------------|
| **Start with the intuition** | “We’re dealing with prefixes, so a Trie is natural.” |
| **Walk through your code** | Highlight the two passes and the `cnt` field. |
| **Time/Space** | State `O(total_chars)` explicitly. |
| **Mention pitfalls** | Show awareness of root vs. child counts. |
| **Talk about extensions** | “If we needed only the maximum score, we’d track it during insertion.” |
| **Practice** | Solve 2416 on LeetCode and also write a brief explanation – interviewers appreciate a well‑articulated solution. |

---

## 📚 Blog Article – “Sum of Prefix Scores of Strings: A Trie‑Driven Masterclass”

### Title  
**Sum of Prefix Scores of Strings – Mastering the Trie for LeetCode Hard**

### Meta Description  
“Learn how to solve LeetCode 2416 with a clean Trie implementation in Java, Python, and C++. Discover interview tips, edge‑case handling, and why this problem is a must‑know for any software‑engineering interview.”

---

### 1. The Problem in a Nutshell  
(Insert problem statement – same as above)

> *Why this problem matters*: It tests your ability to turn a theoretical definition (“prefix score”) into a concrete data‑structure solution. It’s a **hard** LeetCode question that appears often in senior‑level interview pipelines.

### 2. Good – Why the Trie is Perfect  
* Handles arbitrary prefix lengths.
* Count per node = prefix score → *O(1)* access.
* Scales linearly with input size.

### 3. Bad – Why a Brute‑Force or Hash‑Map Approach Fails  
* Naïve double loop → `O(n²·L)` (worst case 10¹⁰ operations).
* Hash‑maps per word would still need a linear scan per query → unnecessary overhead.
* Sorting + binary search is `O(n log n)` per query – not optimal for `L = 1 000`.

### 4. Ugly – Hidden Pitfalls  
* Forgetting that the *root* is the empty prefix – leads to off‑by‑one errors.
* Incrementing counts **before** moving to the child instead of after.
* Using recursion on a deep trie (depth can be 1 000) → stack overflow on some judges.

### 5. Implementation Walk‑through (Java, Python, C++)  
(Insert code snippets from section 5 with brief inline comments.)

### 6. Complexity Breakdown  
(Insert complexity table.)

### 7. “What Interviewers Look For”  
* Recognition of the Trie as the canonical solution.
* Correct handling of identical words and the empty prefix.
* Discussion of time/space trade‑offs.
* Ability to explain the two‑pass logic in plain English.

### 8. Variations You Should Know  
* “What if we only need the maximum prefix‑score sum?”
* “What if words come in an online stream?”
* “Can we solve it with a sorted array + binary search?” – discuss pros/cons.

### 9. SEO‑Friendly Keywords  
* **LeetCode 2416**  
* **Sum of Prefix Scores**  
* **Prefix Trie**  
* **Hard LeetCode Problems**  
* **Software Engineer Interview**  
* **Data‑Structure Interview Questions**  
* **Job Interview Prep**  

Incorporate these keywords naturally throughout headings, paragraphs, and the meta description.

### 10. Final Thoughts  
Mastering this problem showcases your *data‑structure intuition*, *clean coding*, and *algorithmic efficiency*—qualities every hiring manager values. By being able to articulate the approach, the implementation details, and the subtle pitfalls, you’ll stand out in a technical interview.

> **Next step** – practice the problem on LeetCode, then add a short write‑up (like this blog post) to your portfolio or GitHub README. Recruiters love candidates who can explain their solutions, not just code them.

Happy coding and good luck landing that dream software‑engineering role! 🚀

---