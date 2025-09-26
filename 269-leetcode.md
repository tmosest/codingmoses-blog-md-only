---
title: LeetCode 269. Alien Dictionary - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Alien Dictionary – A Complete, Cross‑Language Solution  
*(Java | Python | C++) – plus an SEO‑friendly blog post that can land you that interview call!*

---

## 1. Problem Recap (LeetCode 269)

> **Given** a list of words that are already sorted according to an **unknown** alien alphabet.  
> **Task**: Deduce a possible order of the letters.  
> *Return an empty string if the input ordering is impossible.*

**Examples**

| Input | Output |
|-------|--------|
| `["wrt","wrf","er","ett","rftt"]` | `"wertf"` |
| `["z","x"]` | `"zx"` |
| `["z","x","z"]` | `""`  *(invalid)* |

**Constraints**

* `1 <= words.length <= 100`
* `1 <= words[i].length <= 100`
* All words consist of lowercase English letters only.

---

## 2. The Algorithm – Topological Sort with Kahn (BFS)

1. **Build a graph**  
   For every adjacent pair of words, find the first differing character.  
   *If word1 is longer but a prefix of word2 → impossible.*

2. **Add edges**  
   If `word1[i] != word2[i]`, then `word1[i]` comes **before** `word2[i]`.  
   Edge: `word1[i] → word2[i]`.

3. **Kahn’s algorithm**  
   * In‑degree array for all 26 letters.  
   * Queue of nodes with 0 in‑degree.  
   * Repeatedly pop, append to result, decrement neighbors’ in‑degree.  

4. **Validate**  
   *If result length < number of distinct letters → cycle → return `""`.*

5. **Return** the concatenated string of the order.

This solution runs in **O(V + E)** where  
`V = 26` (letters), `E ≤ words.length * average_word_length`.

---

## 3. Code Implementations

Below are clean, production‑ready implementations in **Java, Python, and C++**.

### 3.1 Java (Kahn’s Algorithm)

```java
import java.util.*;

public class Solution {
    public String alienOrder(String[] words) {
        // 1. Build graph
        List<Set<Integer>> graph = new ArrayList<>(26);
        for (int i = 0; i < 26; i++) graph.add(new HashSet<>());
        int[] indegree = new int[26];
        boolean[] present = new boolean[26];

        for (String w : words) {
            for (char c : w.toCharArray()) present[c - 'a'] = true;
        }

        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i];
            String w2 = words[i + 1];
            int minLen = Math.min(w1.length(), w2.length());
            int diffIdx = -1;
            for (int j = 0; j < minLen; j++) {
                if (w1.charAt(j) != w2.charAt(j)) {
                    diffIdx = j;
                    break;
                }
            }

            if (diffIdx == -1) {
                if (w1.length() > w2.length()) return ""; // prefix rule violation
            } else {
                int u = w1.charAt(diffIdx) - 'a';
                int v = w2.charAt(diffIdx) - 'a';
                if (!graph.get(u).contains(v)) {
                    graph.get(u).add(v);
                    indegree[v]++;
                }
            }
        }

        // 2. Kahn's algorithm
        Queue<Integer> q = new ArrayDeque<>();
        for (int i = 0; i < 26; i++) {
            if (present[i] && indegree[i] == 0) q.offer(i);
        }

        StringBuilder sb = new StringBuilder();
        int processed = 0;
        while (!q.isEmpty()) {
            int u = q.poll();
            sb.append((char) (u + 'a'));
            processed++;
            for (int v : graph.get(u)) {
                if (--indegree[v] == 0) q.offer(v);
            }
        }

        // 3. Detect cycle
        if (processed != countPresent(present)) return "";
        return sb.toString();
    }

    private int countPresent(boolean[] present) {
        int cnt = 0;
        for (boolean b : present) if (b) cnt++;
        return cnt;
    }
}
```

> **Why this code?**  
> * Uses `Set` to avoid duplicate edges.  
> * Handles prefix rule (`w1` longer than `w2`).  
> * Keeps complexity O(26 + edges).  
> * Clean separation of steps for readability.

### 3.2 Python (DFS + Kahn Hybrid)

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def alienOrder(self, words: List[str]) -> str:
        # 1. Build graph and in-degrees
        graph = defaultdict(set)
        indeg = [0] * 26
        present = [False] * 26

        for w in words:
            for ch in w:
                present[ord(ch) - 97] = True

        for w1, w2 in zip(words, words[1:]):
            min_len = min(len(w1), len(w2))
            diff = -1
            for i in range(min_len):
                if w1[i] != w2[i]:
                    diff = i
                    break
            if diff == -1:
                if len(w1) > len(w2):
                    return ""
            else:
                u, v = ord(w1[diff]) - 97, ord(w2[diff]) - 97
                if v not in graph[u]:
                    graph[u].add(v)
                    indeg[v] += 1

        # 2. Kahn
        q = deque([i for i in range(26) if present[i] and indeg[i] == 0])
        res = []
        processed = 0

        while q:
            u = q.popleft()
            res.append(chr(u + 97))
            processed += 1
            for v in graph[u]:
                indeg[v] -= 1
                if indeg[v] == 0:
                    q.append(v)

        if processed != sum(present):
            return ""
        return "".join(res)
```

> **Pythonic touch**  
> * `defaultdict(set)` keeps edge uniqueness.  
> * Uses `deque` for efficient FIFO.  
> * One‑liner present‑count check.

### 3.3 C++ (Modern STL, Kahn)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string alienOrder(vector<string>& words) {
        vector<unordered_set<int>> graph(26);
        vector<int> indeg(26, 0);
        vector<bool> present(26, false);

        // mark present letters
        for (const string& w : words)
            for (char c : w) present[c - 'a'] = true;

        // build graph
        for (size_t i = 0; i + 1 < words.size(); ++i) {
            const string& w1 = words[i];
            const string& w2 = words[i + 1];
            int minLen = min(w1.size(), w2.size());
            int diff = -1;
            for (int j = 0; j < minLen; ++j) {
                if (w1[j] != w2[j]) { diff = j; break; }
            }
            if (diff == -1) {
                if (w1.size() > w2.size()) return "";
            } else {
                int u = w1[diff] - 'a';
                int v = w2[diff] - 'a';
                if (!graph[u].count(v)) {
                    graph[u].insert(v);
                    ++indeg[v];
                }
            }
        }

        // Kahn
        queue<int> q;
        for (int i = 0; i < 26; ++i)
            if (present[i] && indeg[i] == 0) q.push(i);

        string order;
        int processed = 0;
        while (!q.empty()) {
            int u = q.front(); q.pop();
            order.push_back(char('a' + u));
            ++processed;
            for (int v : graph[u]) {
                if (--indeg[v] == 0) q.push(v);
            }
        }

        int presentCnt = count(present.begin(), present.end(), true);
        if (processed != presentCnt) return "";
        return order;
    }
};
```

> **C++ Highlights**  
> * Uses `unordered_set` for O(1) edge existence checks.  
> * `queue<int>` for BFS.  
> * Straightforward style that’s easy to audit in an interview.

---

## 4. Blog Article: “The Good, The Bad, and The Ugly of the Alien Dictionary Problem”

> **Target Audience** – Front‑end/Back‑end developers, algorithm enthusiasts, interview candidates.  
> **SEO keywords** – alien dictionary, topological sort, Kahn algorithm, LeetCode 269, interview algorithm, graph traversal, cycle detection.

---

### 4.1 Introduction

> When a recruiter shows you a list of “alien” words and asks for the letter order, they’re really testing your ability to translate a *partial order* into a *total order*.  
> The LeetCode problem **269. Alien Dictionary** is the canonical exercise that packs *graph construction*, *cycle detection*, and *topological sorting* into a single, bite‑sized challenge.

> In this article we’ll break down **the good**, **the bad**, and **the ugly** of the problem, walk through the most efficient solution, and share key interview take‑aways that will impress hiring managers.

---

### 4.2 The Good – Why This Problem Rocks

| Aspect | Why It Helps |
|--------|--------------|
| **Real‑world analogy** | Sorting with incomplete information is common in **dependency resolution**, **build systems**, and **task scheduling**. |
| **Compact yet rich** | It covers *string manipulation*, *graph theory*, *BFS/DFS*, and *cycle detection* in a single prompt. |
| **Multiple valid outputs** | Encourages creative thinking—any valid topological order is accepted, so you can show off a different strategy (DFS vs Kahn). |
| **Scalable** | Easy to extend: add Unicode, handle larger alphabets, or incorporate weights. |

> **Takeaway**: Mastering this problem signals that you’re comfortable with **abstract ordering constraints**—exactly what many tech companies look for.

---

### 4.3 The Bad – Common Pitfalls

| Mistake | Why it hurts |
|---------|--------------|
| **Ignoring the prefix rule** | A longer word that prefixes a shorter one (e.g., `["abc","ab"]`) must return `""`. Failing to check this breaks the logic. |
| **Duplicate edges** | Adding the same edge multiple times inflates the in‑degree count and can cause wrong cycle detection. |
| **Assuming all 26 letters exist** | Some words might omit letters; building the graph for unused nodes can lead to extra nodes with zero in‑degree that shouldn’t appear in the answer. |
| **Using DFS without cycle detection** | DFS can return an order, but you still need to detect back‑edges to handle invalid inputs. |
| **Over‑optimizing early** | Trying to micro‑optimize with bitsets or custom queues before you get the logic right is counter‑productive. |

> **Check‑list for interviewers**  
> 1. Prefix validation.  
> 2. Edge uniqueness.  
> 3. Correct in‑degree initialization.  
> 4. Cycle detection.  
> 5. Return string only for present letters.

---

### 4.4 The Ugly – Edge‑Cases & Hidden Complexity

| Edge‑Case | Why it’s tricky |
|-----------|-----------------|
| **Repeated characters in words** | Example `["a","a"]` is valid but should still produce `"a"`. |
| **Disconnected sub‑graphs** | Letters that never appear in an edge can be placed anywhere; any order that respects known constraints is fine. |
| **Long chain of constraints** | Input like `["a","b","c", …]` tests whether you handle deep recursion (DFS) or long queue operations (Kahn) gracefully. |
| **Circular dependency** | `["z","x","z"]` demonstrates a cycle that must be caught. |
| **Sparse vs dense graph** | Real data may have 26 nodes but only a handful of edges. Make sure your algorithm runs in `O(V+E)` regardless. |

> **Pro Tip**: Use a *boolean matrix* or *unordered_set* for edges to guard against duplicates, and run a quick `indegree == 0` queue to catch cycles early.

---

### 4.5 The Efficient Solution (Kahn’s Algorithm)

1. **Graph Construction**  
   *Scan all adjacent pairs, find the first differing character, and add an edge.*

2. **In‑Degree Calculation**  
   *Track how many prerequisites each letter has.*

3. **Topological Sort (BFS)**  
   *Start with all zero‑in‑degree nodes, pop, append to result, decrement neighbors.*

4. **Cycle Detection**  
   *If the number of processed nodes is less than the number of distinct letters, a cycle exists.*

> **Complexity**:  
> *Time*: `O(V + E)` → `O(26 + edges)` → practically linear.  
> *Space*: `O(V + E)` → small constant overhead.

---

### 4.6 Code Highlights (Java, Python, C++)

| Language | Key Feature | Why it matters |
|----------|-------------|----------------|
| **Java** | `Set<Integer>` for edges, `Queue<Integer>` for BFS | Avoids duplicate edges, guarantees FIFO |
| **Python** | `defaultdict(set)`, `deque` | Concise syntax, fast built‑ins |
| **C++** | `unordered_set`, `queue<int>` | Low‑level control, compile‑time guarantees |

> *All implementations are commented and ready to paste into an interview console.*

---

### 4.7 Interview‑Ready Talking Points

1. **Explain the Graph** – Show how string differences translate to directed edges.  
2. **State the Prefix Rule** – Mention the edge case and why it matters.  
3. **Choose Kahn vs DFS** – Outline pros/cons of each topological method.  
4. **Cycle Detection** – Demonstrate understanding of back‑edges or processed‑count check.  
5. **Edge Uniqueness** – Talk about using a hash‑set to avoid over‑counting in‑degrees.

> **Result**: You’ll demonstrate *clean, maintainable code* and *solid algorithmic reasoning*—a recipe for a 5‑star interview answer.

---

### 4.7 Conclusion

> The **Alien Dictionary** problem is more than just a graph puzzle.  
> It’s a micro‑cosm of **dependency management**, a **graph theory playground**, and a **test of edge‑case hygiene**.  
> By mastering the *good* (real‑world analogies), avoiding the *bad* (common bugs), and handling the *ugly* (edge‑cases), you’ll turn a seemingly whimsical prompt into a showcase of your algorithmic prowess.

> Whether you’re a seasoned engineer preparing for a *software‑engineering interview* or a junior coder aiming for that next role, this problem is a must‑have in your algorithm toolkit.

---

### 4.8 Final Thought

> “If you can solve the Alien Dictionary, you can solve *any* scheduling problem where order matters, even if you’re missing some pieces of the puzzle.”

> **Happy coding, and may your topological sort always be valid!**

---

## 5. Closing

> 1. **Run** the provided solutions locally.  
> 2. **Practice** the problem with variations (e.g., custom alphabets).  
> 3. **Read** the article, highlight key interview take‑aways, and use them to answer “What does it mean to have a partial order?” on your next call.  
> 4. **Share** your code on GitHub or a coding‑platform for the community to see.  

> Good luck—and remember: *a well‑sorted alphabet can open the door to your next job!* 

--- 

Feel free to tailor the article to your own voice or add personal anecdotes. Happy interviewing!