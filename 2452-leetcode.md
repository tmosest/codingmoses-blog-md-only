---
title: LeetCode 2452. Words Within Two Edits of Dictionary - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 2452 – Words Within Two Edits of Dictionary  
*Brute‑Force → Trie → Your interview‑ready portfolio*

Below you’ll find **full, ready‑to‑paste code** in **Java, Python, and C++** for two approaches:

| Approach | Time | Space | Why it matters in interviews |
|----------|------|-------|------------------------------|
| Brute‑Force (O(Q·D·L)) | `O(m · n · L)` | `O(1)` | Shows you can solve the problem naïvely, good for a “get it working first” interview. |
| Trie (O(L · D + Q · L)) | `O(L · D + Q · L)` | `O(L · D)` | Demonstrates a classic data‑structure optimization – a red‑flag for coding interviews. |

> **TL;DR** – Use the trie if the constraints grow (e.g. `L` up to 10⁵). For the 100‑word LeetCode test set, the brute‑force is more than fast enough.

---

## 🎯 Problem Statement (LeetCode 2452)

> You are given two string arrays, **`queries`** and **`dictionary`**.  
> All words are lowercase and **the same length `L`**.  
> In one edit you may change any single letter to any other letter.  
> Return all words from `queries` that can become *some* word from `dictionary` after at most **two edits**.  
> Preserve the original order of `queries`.

*Constraints*  
```
1 ≤ queries.length, dictionary.length ≤ 100
1 ≤ L ≤ 100
```

---

## 🧠 Brute‑Force Implementation

We simply compare each query with every dictionary word and count the differing positions.  
If the count ≤ 2, the query is accepted.

### Java

```java
import java.util.*;

class Solution {
    // Count mismatches between two equal‑length strings
    private int diff(String a, String b) {
        int cnt = 0;
        for (int i = 0; i < a.length(); i++) {
            if (a.charAt(i) != b.charAt(i)) cnt++;
        }
        return cnt;
    }

    public List<String> twoEditWords(String[] queries, String[] dictionary) {
        List<String> ans = new ArrayList<>();
        for (String q : queries) {
            for (String d : dictionary) {
                if (diff(q, d) <= 2) {
                    ans.add(q);
                    break;          // no need to check further
                }
            }
        }
        return ans;
    }
}
```

### Python

```python
class Solution:
    def twoEditWords(self, queries, dictionary):
        def diff(a, b):
            return sum(1 for i in range(len(a)) if a[i] != b[i])

        ans = []
        for q in queries:
            for d in dictionary:
                if diff(q, d) <= 2:
                    ans.append(q)
                    break
        return ans
```

### C++

```cpp
#include <vector>
#include <string>

class Solution {
public:
    int diff(const std::string& a, const std::string& b) {
        int cnt = 0;
        for (size_t i = 0; i < a.size(); ++i)
            if (a[i] != b[i]) ++cnt;
        return cnt;
    }

    std::vector<std::string> twoEditWords(std::vector<std::string> queries,
                                          std::vector<std::string> dictionary) {
        std::vector<std::string> ans;
        for (const auto& q : queries) {
            for (const auto& d : dictionary) {
                if (diff(q, d) <= 2) {
                    ans.push_back(q);
                    break;
                }
            }
        }
        return ans;
    }
};
```

> **Complexity**  
> *Time*: `O(m · n · L)` (m = #queries, n = #dictionary)  
> *Space*: `O(1)` (ignoring the output list)

---

## 🏰 Trie‑Based Implementation

The trie allows us to **explore all dictionary words that differ in at most two positions** without comparing every word.  
We perform a depth‑first search (DFS) over the trie while keeping a counter of how many edits have been made so far.

### Java

```java
import java.util.*;

public class Solution {
    // ---------- Trie node ----------
    private static class Node {
        Node[] next = new Node[26];
        boolean end;
    }

    // ---------- Build trie ----------
    private final Node root = new Node();

    private void insert(String word) {
        Node cur = root;
        for (char c : word.toCharArray()) {
            int i = c - 'a';
            if (cur.next[i] == null) cur.next[i] = new Node();
            cur = cur.next[i];
        }
        cur.end = true;
    }

    // ---------- DFS search ----------
    private boolean search(Node node, String query, int idx, int edits) {
        if (edits > 2) return false;          // prune
        if (idx == query.length()) return edits <= 2 && node.end;

        // Explore all possible children
        for (int i = 0; i < 26; ++i) {
            if (node.next[i] == null) continue;
            int nextEdits = edits + ((char)('a' + i) != query.charAt(idx) ? 1 : 0);
            if (search(node.next[i], query, idx + 1, nextEdits)) return true;
        }
        return false;
    }

    public List<String> twoEditWords(String[] queries, String[] dictionary) {
        // Build dictionary trie
        for (String w : dictionary) insert(w);

        List<String> ans = new ArrayList<>();
        for (String q : queries) {
            if (search(root, q, 0, 0)) ans.add(q);
        }
        return ans;
    }
}
```

### Python

```python
class TrieNode:
    __slots__ = ('children', 'end')
    def __init__(self):
        self.children = [None] * 26
        self.end = False

class Solution:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str):
        node = self.root
        for ch in word:
            i = ord(ch) - 97
            if node.children[i] is None:
                node.children[i] = TrieNode()
            node = node.children[i]
        node.end = True

    def search(self, node: TrieNode, query: str, idx: int, edits: int) -> bool:
        if edits > 2:                      # prune
            return False
        if idx == len(query):
            return edits <= 2 and node.end
        for i in range(26):
            child = node.children[i]
            if child is None: continue
            new_edits = edits + (i != ord(query[idx]) - 97)
            if self.search(child, query, idx + 1, new_edits):
                return True
        return False

    def twoEditWords(self, queries: List[str], dictionary: List[str]) -> List[str]:
        # Build dictionary trie
        for w in dictionary:
            self.insert(w)

        ans = []
        for q in queries:
            if self.search(self.root, q, 0, 0):
                ans.append(q)
        return ans
```

### C++

```cpp
#include <vector>
#include <string>
#include <memory>

struct TrieNode {
    std::array<std::unique_ptr<TrieNode>, 26> next;
    bool end = false;
};

class Solution {
public:
    std::unique_ptr<TrieNode> root = std::make_unique<TrieNode>();

    void insert(const std::string& word) {
        TrieNode* node = root.get();
        for (char c : word) {
            int i = c - 'a';
            if (!node->next[i]) node->next[i] = std::make_unique<TrieNode>();
            node = node->next[i].get();
        }
        node->end = true;
    }

    bool dfs(TrieNode* node, const std::string& query,
             size_t idx, int edits) {
        if (edits > 2) return false;               // pruning
        if (idx == query.size()) return edits <= 2 && node->end;

        for (int i = 0; i < 26; ++i) {
            TrieNode* child = node->next[i].get();
            if (!child) continue;
            int nextEdits = edits + (query[idx] != char('a' + i));
            if (dfs(child, query, idx + 1, nextEdits)) return true;
        }
        return false;
    }

    std::vector<std::string> twoEditWords(std::vector<std::string> queries,
                                          std::vector<std::string> dictionary) {
        for (const auto& w : dictionary) insert(w);

        std::vector<std::string> ans;
        for (const auto& q : queries) {
            if (dfs(root.get(), q, 0, 0)) ans.push_back(q);
        }
        return ans;
    }
};
```

> **Complexity**  
> *Time*: `O(L · |D| + Q · L)` (L = word length) – fast enough even when `L` is up to 10⁵.  
> *Space*: `O(L · |D|)` – memory for all trie nodes.

---

## 📚 The Good, The Bad & The Ugly – A Quick Interview Checklist

| Stage | What you learned | Typical interview slip‑ups | Fix / Optimisation |
|-------|------------------|---------------------------|--------------------|
| ✅ *Good – Brute‑Force* | *You can solve the problem with the simplest logic.* | Forgetting to break after a match → O(Q·D) *everywhere* | `break` after first match. |
| ❌ *Bad – Too many branches* | If you hand‑write a DFS over all dictionary words, you may forget pruning (`edits > 2`) → TLE. | Not pruning → exploring every branch unnecessarily. | Add a guard `if edits > 2 return false`. |
| 🏆 *The Ugly – Real‑world data‑structure* | When constraints are larger, the trie is a must‑know technique. | Forgetting to mark terminal nodes → you’ll match *prefixes* incorrectly. | Ensure `node.end` is checked at the leaf. |

---

## 🎯 Why this matters for a **coding interview**

1. **First impression** – Show you can code a **correct** baseline before moving to optimisations.
2. **Data‑structure knowledge** – Interviewers love to see you use a *trie* (or a hash map with careful pruning) because it’s a classic pattern for “edit distance” style problems.
3. **Time‑complexity** – Highlight the difference between `O(Q·D·L)` and `O(L·D + Q·L)` in your explanation. It demonstrates you understand *why* an optimization is useful, not just that it exists.
4. **Edge‑case handling** – Mention that all words are the same length, so you never have to deal with insertions/deletions – only substitutions.
5. **Testing** – LeetCode’s test cases are tiny, but always write unit tests for yourself.  
   ```java
   assert new Solution().twoEditWords(
         new String[]{"aaa","abc"}, new String[]{"abb","bbc"}) == List.of("aaa","abc");
   ```

---

## 📌 Final Take‑aways for Interview Success

| What you’ll remember | How it helps in a real interview |
|----------------------|---------------------------------|
| **Brute‑Force** – *quick, correct, works for all inputs* | You’ll pass “warm‑up” problems and show you’re not afraid to code something that “just works.” |
| **Trie** – *optimized, classic data‑structure* | Demonstrates that you can see beyond “compare everything” and apply a clean algorithmic pattern. |
| **Complexity discussion** | Interviewers expect you to talk about `O(n log n)`, `O(n²)` etc. – don’t just show code, explain it. |
| **Clean code** | Use `__slots__` in Python, `unique_ptr` in C++, `private` helpers in Java – a polished answer is worth 2‑3 extra points. |

---

## 🎯 How to Prepare

1. **Understand LeetCode 2452** – Practice the two approaches, then add unit tests.  
2. **Implement from scratch** – Don’t copy/paste; write the trie yourself.  
3. **Explain your solution** – On paper or a whiteboard, walk through:
   * The mismatch counter
   * How DFS prunes after 2 edits
   * Why `node.end` matters
4. **Run time‑analysis** – Know the worst‑case numbers: `m, n ≤ 100`, `L ≤ 100`.  
5. **Think bigger** – Mention that for larger `L` (e.g. 10⁵) the trie is essential.

---

## 🎉 Takeaway

- **The brute‑force** is *acceptable* for small constraints and is great to showcase that you can solve the problem end‑to‑end quickly.  
- **The trie** is the *optimal, interview‑friendly* solution that tells recruiters you know classic data‑structures and can push an algorithm to its asymptotic limits.  

Add both snippets to your GitHub portfolio, tag them with **`#LeetCode2452 #Trie #JobInterview #Java #Python #C++`**, and you’ll have a talking‑point that showcases both *correctness* and *efficiency*—the sweet spot every hiring manager looks for. Happy coding!