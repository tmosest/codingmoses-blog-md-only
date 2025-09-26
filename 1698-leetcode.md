---
title: LeetCode 1698. Number of Distinct Substrings in a String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1698. Number of Distinct Substrings in a String  
**Difficulty:** Medium  
**Languages:** Java · Python · C++  
**Goal:** Count how many *different* substrings a given string contains.

> **Why it matters for your job hunt**  
> Interviewers love problems that let you showcase *algorithmic thinking*, *data‑structure mastery* and *clean code*.  
> This problem is a classic “hidden gem” – you can solve it with a simple set, a clever Trie, or even an advanced suffix‑automaton.  
> Writing a clear solution in three languages proves you’re comfortable with cross‑platform coding, a must‑have for senior roles.

---

## 1️⃣ The Brute‑Force / Trie Solution (O(n²))

| Approach | Time | Space |
|----------|------|-------|
| **Set of substrings** | O(n² log n) (hashing) | O(n²) |
| **Trie (prefix‑tree)** | **O(n²)** | O(n²) (worst‑case) |

The Trie is a “space‑saving” version of the set: each new substring is represented by a unique path in the tree.  
We only add a node when a new character sequence appears for the first time, so the total number of nodes equals the number of distinct substrings.

Below are clean, production‑ready implementations in **Java**, **Python** and **C++**.

---

## 2️⃣ Java Implementation (Trie)

```java
import java.util.*;

public class Solution {
    // Trie node containing 26 children (lowercase letters)
    private static class TrieNode {
        TrieNode[] child = new TrieNode[26];
    }

    public int countDistinct(String s) {
        TrieNode root = new TrieNode();
        int distinct = 0;

        for (int i = 0; i < s.length(); i++) {
            TrieNode cur = root;
            for (int j = i; j < s.length(); j++) {
                int idx = s.charAt(j) - 'a';
                if (cur.child[idx] == null) {
                    cur.child[idx] = new TrieNode();
                    distinct++;                    // New distinct substring found
                }
                cur = cur.child[idx];
            }
        }
        return distinct;
    }

    // Optional: main for quick manual testing
    public static void main(String[] args) {
        System.out.println(new Solution().countDistinct("aabbaba")); // 21
        System.out.println(new Solution().countDistinct("abcdefg")); // 28
    }
}
```

### Why this is **good**

- No need for an extra HashSet; memory is bounded by the Trie nodes.
- Easy to read, O(1) char lookup per node.

### What’s **bad** / **ugly**

- Still quadratic time; for very long strings (n > 10⁵) it will be too slow.
- Memory can blow up on pathological inputs (e.g., all unique characters).

---

## 3️⃣ Python Implementation (Trie)

```python
class TrieNode:
    __slots__ = ("child",)
    def __init__(self):
        # 26 lowercase letters
        self.child = [None] * 26


class Solution:
    def countDistinct(self, s: str) -> int:
        root = TrieNode()
        distinct = 0

        for i in range(len(s)):
            node = root
            for ch in s[i:]:
                idx = ord(ch) - 97   # 'a' -> 0
                if node.child[idx] is None:
                    node.child[idx] = TrieNode()
                    distinct += 1
                node = node.child[idx]
        return distinct


# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.countDistinct("aabbaba"))  # 21
    print(sol.countDistinct("abcdefg"))  # 28
```

### Clean‑up points

- `__slots__` keeps each node light‑weight.
- The algorithm is identical to Java’s – just adapted to Python’s syntax.

---

## 4️⃣ C++ Implementation (Trie)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TrieNode {
    TrieNode* child[26];
    TrieNode() {
        memset(child, 0, sizeof(child));
    }
};

class Solution {
public:
    int countDistinct(string s) {
        TrieNode* root = new TrieNode();
        int distinct = 0;

        for (size_t i = 0; i < s.size(); ++i) {
            TrieNode* cur = root;
            for (size_t j = i; j < s.size(); ++j) {
                int idx = s[j] - 'a';
                if (!cur->child[idx]) {
                    cur->child[idx] = new TrieNode();
                    ++distinct;            // New substring discovered
                }
                cur = cur->child[idx];
            }
        }
        // (Optional) Clean up memory if you want to avoid leaks.
        return distinct;
    }
};

// Demo
int main() {
    Solution sol;
    cout << sol.countDistinct("aabbaba") << '\n'; // 21
    cout << sol.countDistinct("abcdefg") << '\n'; // 28
    return 0;
}
```

> **Tip:** In competitive coding environments you usually don’t delete the nodes; but for a real project you’d implement a recursive delete or use smart pointers.

---

## 5️⃣ The “Follow‑Up” – O(n) with a Suffix Automaton

If you’re interviewing for a **Senior/Lead** role, showing an O(n) solution demonstrates deeper knowledge:

```java
public int countDistinct(String s) {
    int n = s.length();
    int[] link = new int[2 * n];
    int[] len = new int[2 * n];
    int[][] next = new int[2 * n][26];
    Arrays.fill(link, -1);
    for (int i = 0; i < 2 * n; ++i) Arrays.fill(next[i], -1);

    int last = 0, size = 1;
    for (char ch : s.toCharArray()) {
        int cur = size++;
        len[cur] = len[last] + 1;
        int p = last;
        int c = ch - 'a';
        while (p != -1 && next[p][c] == -1) {
            next[p][c] = cur;
            p = link[p];
        }
        if (p == -1) {
            link[cur] = 0;
        } else {
            int q = next[p][c];
            if (len[p] + 1 == len[q]) {
                link[cur] = q;
            } else {
                int clone = size++;
                len[clone] = len[p] + 1;
                System.arraycopy(next[q], 0, next[clone], 0, 26);
                link[clone] = link[q];
                while (p != -1 && next[p][c] == q) {
                    next[p][c] = clone;
                    p = link[p];
                }
                link[q] = link[cur] = clone;
            }
        }
        last = cur;
    }

    long ans = 0;
    for (int i = 1; i < size; ++i) ans += len[i] - len[link[i]];
    return (int) ans;
}
```

> **Why use it?**  
> - Handles strings up to 10⁶+ comfortably.  
> - Showcases knowledge of automata theory – a plus in algorithm‑centric roles.

---

## 6️⃣ Blog Article: “The Good, The Bad, and The Ugly of Distinct Substrings”

> **Title:** *From Tries to Suffix Automata: Mastering the “Number of Distinct Substrings” Problem*

### Abstract
Counting distinct substrings is deceptively simple yet a powerhouse interview question. It tests your grasp of data structures (sets, tries, suffix trees/automata), hashing, and algorithmic complexity. This article walks through the most common solutions, their trade‑offs, and how to explain them to interviewers like a pro.

### Keywords
`#LeetCode #CodingInterview #DistinctSubstrings #Trie #SuffixAutomaton #AlgorithmDesign #Java #Python #C++`

---

### 1️⃣ The “Good” – A Set or Trie
- **What**: Enumerate every substring, insert into a hash set, or build a Trie to avoid duplicates.
- **Why**: Simple to implement, passes LeetCode’s constraints (n ≤ 500).
- **Result**: `O(n²)` time, `O(n²)` memory.  
  *Ideal for quick wins or when constraints are modest.*

### 2️⃣ The “Bad” – Quadratic Time with Rolling Hash
- **What**: Generate all substrings, compute a rolling hash (e.g., Rabin‑Karp), insert into a hash set.
- **Why**: Slightly faster constants than a set of strings, but still `O(n²)`.
- **Result**: Still quadratic, risk of hash collisions, more complex code.  
  *Not worth the extra code unless you need to avoid string allocation.*

### 3️⃣ The “Ugly” – Suffix Tree (Too Heavy)
- **What**: Construct a suffix tree, then count leaf edges.
- **Why**: Historically the textbook solution (`O(n)`).
- **Result**: Implementation is long (~200 lines), hard to debug, rarely required on LeetCode.  
  *Best avoided unless you’re truly prepared to prove mastery of advanced data structures.*

### 4️⃣ The “Heroic” – Suffix Automaton (O(n))
- **What**: Build a minimal DFA that recognises all suffixes; the number of distinct substrings equals the sum over states of `len[state] - len[link[state]]`.
- **Why**: Linear time, linear memory. Handles strings up to millions of characters.
- **Result**: A concise, clever code that impresses senior interviewers.  
  *If you can explain it in plain English, you’re a cut above the rest.*

---

## 7️⃣ How to Talk About It in an Interview

1. **Start Simple**  
   *“I’ll first implement a Trie because it’s intuitive and works for the given constraints.”*  
2. **Mention the Follow‑Up**  
   *“If we had to process very long strings, we would switch to a suffix automaton for linear time.”*  
3. **Explain Complexity**  
   *“The Trie gives O(n²) time; the automaton reduces it to O(n) by reusing already seen suffixes.”*  
4. **Show Code Readability**  
   *Use comments, `__slots__`, and clear variable names.*  
5. **Relate to Real‑World**  
   *“In search engines or genome sequencing, we often need to count distinct patterns efficiently, so suffix automata are practical.”*

---

## 8️⃣ SEO‑Optimized Takeaway

- **Keywords**: “LeetCode 1698”, “distinct substrings solution”, “Trie vs suffix automaton”, “O(n) distinct substrings”, “Java/Python/C++ implementation”.
- **Meta Description**: “Solve LeetCode 1698 (Number of Distinct Substrings) with clean Java, Python, and C++ code. Compare Trie, Rolling Hash, and the linear‑time Suffix Automaton approach.”
- **Content Hierarchy**: Clear headings (`H1`, `H2`, `H3`) to boost search engine readability.

---

### Call to Action
> *Ready to ace your next coding interview? Grab the code snippets, practice explaining them, and let the “Number of Distinct Substrings” problem become your secret weapon.*

--- 

**End of Article**  

Feel free to copy, adapt, and publish on your blog or LinkedIn to attract recruiters looking for algorithm experts. Good luck! 🚀

--- 

**#Answer** – The problem can be solved cleanly in all three languages with a Trie. For bigger constraints, show the O(n) suffix automaton. The blog above explains the trade‑offs, ready for interview storytelling and SEO visibility.