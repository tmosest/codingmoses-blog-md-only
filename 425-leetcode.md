---
title: LeetCode 425. Word Squares - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Solution – 3 Languages in One

Below you’ll find a ready‑to‑paste implementation of **LeetCode 425 – Word Squares** in **Java, Python, and C++**.  
All three solutions use the same idea:  
* **Backtracking** – we build the square row by row.  
* **Fast prefix lookup** – a map (`prefix → list of words`) or a tiny trie gives us all candidates that match the next prefix in O(1) amortised time.

> **Why 3 languages?**  
>  * Java – often the language of choice in big‑tech interviews.  
>  * Python – the quickest way to prototype and discuss logic.  
>  * C++ – the most performance‑critical language in competitive programming.

---

### 1.1  Java (Trie + Backtracking)

```java
import java.util.*;

class TrieNode {
    Map<Character, TrieNode> child = new HashMap<>();
    List<Integer> words = new ArrayList<>();
}

public class Solution {
    private TrieNode root = new TrieNode();
    private int wordLen;
    private String[] words;

    public List<List<String>> wordSquares(String[] words) {
        this.words = words;
        this.wordLen = words[0].length();
        buildTrie(words);

        List<List<String>> res = new ArrayList<>();
        for (String w : words) {
            List<String> cur = new ArrayList<>();
            cur.add(w);
            backtrack(1, cur, res);
        }
        return res;
    }

    private void backtrack(int step, List<String> cur, List<List<String>> res) {
        if (step == wordLen) {
            res.add(new ArrayList<>(cur));
            return;
        }
        StringBuilder sb = new StringBuilder();
        for (String row : cur) sb.append(row.charAt(step));

        for (int idx : getWordsWithPrefix(sb.toString())) {
            cur.add(words[idx]);
            backtrack(step + 1, cur, res);
            cur.remove(cur.size() - 1);
        }
    }

    private void buildTrie(String[] words) {
        for (int i = 0; i < words.length; ++i) {
            TrieNode node = root;
            for (char c : words[i].toCharArray()) {
                node = node.child.computeIfAbsent(c, k -> new TrieNode());
                node.words.add(i);
            }
        }
    }

    private List<Integer> getWordsWithPrefix(String prefix) {
        TrieNode node = root;
        for (char c : prefix.toCharArray()) {
            node = node.child.get(c);
            if (node == null) return Collections.emptyList();
        }
        return node.words;
    }
}
```

> **Complexity** –  
> *Time*: `O(N · 3^L)` in the worst case (every prefix has 3 options).  
> *Space*: `O(N · L)` for the trie and recursion stack.

---

### 1.2  Python (Dictionary of Prefixes + Backtracking)

```python
class Solution:
    def wordSquares(self, words: List[str]) -> List[List[str]]:
        if not words: return []

        L = len(words[0])
        pref_map = {"" : words}
        for w in words:
            for i in range(1, L+1):
                pref_map.setdefault(w[:i], []).append(w)

        res = []
        def backtrack(step, cur):
            if step == L:
                res.append(cur[:])
                return
            prefix = "".join(word[step] for word in cur)
            for candidate in pref_map.get(prefix, []):
                cur.append(candidate)
                backtrack(step + 1, cur)
                cur.pop()

        for w in words:
            backtrack(1, [w])
        return res
```

> The dictionary trick eliminates the need for an explicit trie and keeps the code very concise.

---

### 1.3  C++ (Unordered Map of Prefixes + DFS)

```cpp
class Solution {
public:
    vector<vector<string>> wordSquares(vector<string>& words) {
        int L = words[0].size();
        unordered_map<string, vector<int>> pref;
        for (int i = 0; i < words.size(); ++i) {
            pref[""].push_back(i);
            for (int k = 1; k <= L; ++k)
                pref[words[i].substr(0, k)].push_back(i);
        }

        vector<vector<string>> res;
        vector<int> cur;
        function<void(int)> dfs = [&](int step) {
            if (step == L) {
                vector<string> square;
                for (int idx : cur) square.push_back(words[idx]);
                res.push_back(square);
                return;
            }
            string prefix;
            for (int idx : cur) prefix.push_back(words[idx][step]);

            for (int next : pref[prefix]) {
                cur.push_back(next);
                dfs(step + 1);
                cur.pop_back();
            }
        };

        for (int i = 0; i < words.size(); ++i) {
            cur.push_back(i);
            dfs(1);
            cur.pop_back();
        }
        return res;
    }
};
```

> Notice how the same logic is expressed with C++’s `std::function` and `unordered_map`.

---

## 2.  Blog Article – “Word Squares: The Good, The Bad, and The Ugly”

> **SEO Keywords:** Word Squares, LeetCode 425, Backtracking, Trie, Prefix Map, Coding Interview, Algorithm Design, Java, Python, C++, Job Interview, Technical Interview

---

### 2.1  Introduction

Word Squares is a deceptively simple puzzle that has become a staple in algorithmic interview questions. The goal is to arrange a set of equal‑length words into a square grid such that the `k`‑th row equals the `k`‑th column for all `k`. It’s an excellent test of your ability to combine **backtracking**, **prefix search**, and **data structure design**—skills that recruiters in software engineering roles prize highly.

---

### 2.2  Problem Restatement (What You Need to Know)

- **Input:** An array `words` of unique lowercase strings, all of the same length `L`.  
- **Output:** All possible *word squares* that can be formed from `words`. A word may be reused any number of times.  
- **Constraints:** `1 ≤ words.length ≤ 1000`, `1 ≤ L ≤ 4`.

> **Why `L ≤ 4` matters**  
> A small word length dramatically reduces the search space, but the algorithm must still scale to 1000 words.

---

### 2.3  The “Good” – Clean, Fast, and Scalable

| Feature | Why It’s Good |
|---------|---------------|
| **Trie + Backtracking** | Fast O(1) prefix lookup. Trie nodes hold indices of words, so memory stays linear. |
| **Prefix Map (Python/C++)** | Simpler code, same asymptotic behaviour. |
| **Iterative Building** | Each recursion level builds the next row, eliminating the need for complicated state. |
| **Reusable Words** | Because we store indices, a word can be inserted multiple times without duplication. |

**Key Takeaway:** A trie (or hash‑map of prefixes) keeps the backtracking tree pruned to only viable branches. This is the core optimisation that turns an exponential brute‑force into a practical solution.

---

### 2.4  The “Bad” – Over‑Engineered or Under‑Optimised

| Pitfall | What It Looks Like | Why It’s Bad |
|---------|--------------------|--------------|
| **Brute‑Force Generation** | Generate all permutations of `words` of length `L` and then filter. | O((N^L) · L^2) – impossible for N=1000. |
| **Naïve String Search** | For each prefix, scan all words. | O(N · L) per recursion → O(N^L) worst case. |
| **Copying Whole Squares** | Duplicate entire square at every recursion step. | O(L²) memory churn. |
| **Too Deep Recursion** | Use recursion depth > 1000 (e.g., for longer words). | Stack overflow risk. |

> **Lesson:** Even a “simple” backtracking solution can become a *bad* if you ignore fast prefix lookup. The first step to avoid this is to build a *prefix index*.

---

### 2.5  The “Ugly” – Over‑Complicated and Hard to Maintain

| Ugly Pattern | Why It Hurts |
|--------------|--------------|
| **Multiple Tries & Maps** | A separate trie for each character position + a global map. | Code duplication; harder to read. |
| **Mixing Languages** | Java uses a full class, Python uses a function, C++ uses a lambda with a global state. | Makes cross‑language comparison painful. |
| **Hard‑coded Constants** | `for (int i = 0; i < words.size(); ++i)` inside loops with magic numbers. | Violates DRY principle. |
| **Unnecessary Global Variables** | Store everything as static members. | Makes unit testing difficult. |

> **Fix:** Stick to a single data‑structure per algorithm (either *Trie* or *Prefix Map*) and keep the recursion logic in one place. A clear separation of concerns is vital for readability.

---

### 2.6  Complexity Analysis – What Recruiters Care About

1. **Time**  
   * Building the prefix map / trie: `O(N · L)`  
   * DFS / DFS with pruning: `O(N · 3^L)` (worst‑case) – the factor 3 comes from each prefix having at most 3 matching words for `L = 4`.  
   * Final runtime is dominated by the number of *valid* squares.

2. **Space**  
   * Trie or map: `O(N · L)`  
   * Recursion stack: `O(L)`  
   * Result storage: `O(K · L²)` where `K` is the number of valid squares.

---

### 2.7  Practical Tips for the Interview

1. **Explain Your Thought Process** – Start by describing the recursion tree, then show how you prune it with a trie or prefix map.  
2. **Show the Code, Then Discuss Edge Cases** – E.g., “What if the prefix is not found?” – return an empty list.  
3. **Talk About Reusability** – Highlight that we’re storing indices, not copies.  
4. **Mention Complexity Early** – “Because L ≤ 4, a trie keeps this efficient even with 1000 words.”  
5. **Offer Alternatives** – “If you prefer Python, a simple dict of prefixes is cleaner; if speed is paramount, a trie gives you a tighter constant factor.”

---

### 2.8  How This Prepares You for a Job Interview

- **Algorithm Design:** You’ll demonstrate how to reduce an NP‑complete problem to something executable in seconds.  
- **Data Structures:** Recruiters love to see you choose the right tool (hash‑map vs. trie).  
- **Coding Style:** Clean code, proper variable names, and minimal copying show professional maturity.  
- **Communication:** Walking through the Good/Bad/Ugly patterns in an interview shows you can critique your own code, a valuable soft skill.

---

### 2.9  Final Words – From Code to Career

Word Squares is more than a puzzle; it’s a microcosm of what hiring managers want: **clever use of data structures, efficient recursion, and clear communication**.  
- **Practice** with the Java, Python, and C++ snippets above.  
- **Run your own tests** – e.g., `words = ["area","lead","wall","lady","ball"]`.  
- **Explain the algorithm** to a friend or in a mock interview; teaching is the best way to cement knowledge.  

When you land that interview, bring this question (and the code) to the table. The interviewer will appreciate that you’re ready to write clean, efficient code on the spot—a skill that directly translates into delivering production‑grade software.

> *Good luck on your next interview!* 🚀

---

## 3.  Quick Checklist Before You Submit

| Language | Checklist |
|----------|-----------|
| Java | ✅ `root.child.computeIfAbsent()` keeps trie tight.  ✅ `new ArrayList<>(cur)` copies only the final result. |
| Python | ✅ Use `defaultdict(list)` for prefixes.  ✅ `backtrack` pops after recursion. |
| C++ | ✅ `unordered_map<string, vector<int>>` gives O(1) lookup.  ✅ `std::function<void(int)>` keeps recursion tidy. |

Copy‑paste the corresponding snippet into your LeetCode/CodeSignal/InterviewBuddy editor and hit **Run**. All three should produce the same list of word squares for any valid input.

Happy coding—and good luck on your next technical interview!