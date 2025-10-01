---
title: LeetCode 269. Alien Dictionary - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 269. Alien Dictionary – 3‑Language Code + SEO‑Optimized Blog

> **TL;DR**  
> *The Alien Dictionary* problem is a classic “topological sort” question.  
> We’ll solve it in **Java**, **Python**, and **C++** (Kahn’s BFS, DFS, & recursion).  
> Afterwards, a ready‑to‑publish blog post explains the pitfalls and tricks that interviewers love to hear.

---

## 1. The Problem (LeetCode 269)

> *Given an array of words sorted lexicographically in an alien language, determine the order of the letters.  
> Return an empty string if the order is impossible. If multiple orders are possible, return any of them.*

Constraints are modest (≤ 100 words, ≤ 100 chars each), but the solution must be **O(V+E)**.

---

## 2. High‑Level Strategy

1. **Build a Directed Graph**  
   * Each unique letter is a node.  
   * For every adjacent pair of words, find the first differing character; add an edge `a → b` meaning `a` comes before `b`.

2. **Detect Invalid Prefix Cases**  
   * If `word2` is a prefix of `word1` and `len(word2) < len(word1)`, the ordering is impossible → return `""`.

3. **Topological Sort**  
   * Use **Kahn’s algorithm** (BFS) – easier to reason about and gives deterministic output.  
   * Or use DFS + recursion stack (also works).  
   * Any cycle → return `""`.  
   * If several nodes have indegree 0, we can pop any of them; the problem allows any valid order.

4. **Return the Ordering as a String**  
   * Concatenate nodes in the order they are removed from the queue.

---

## 3. 3‑Language Implementations

### 3.1 Java (Kahn’s Algorithm)

```java
import java.util.*;

public class Solution {
    public String alienOrder(String[] words) {
        // 1. Build graph
        Map<Character, Set<Character>> graph = new HashMap<>();
        int[] indegree = new int[26];
        for (String w : words) {
            for (char c : w.toCharArray()) {
                graph.putIfAbsent(c, new HashSet<>());
            }
        }

        // 2. Add edges
        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i], w2 = words[i + 1];
            int minLen = Math.min(w1.length(), w2.length());
            int j = 0;
            while (j < minLen && w1.charAt(j) == w2.charAt(j)) j++;
            if (j == minLen && w1.length() > w2.length()) return ""; // prefix case
            if (j < minLen) {
                char u = w1.charAt(j), v = w2.charAt(j);
                if (graph.get(u).add(v)) { // new edge
                    indegree[v - 'a']++;
                }
            }
        }

        // 3. Kahn
        Queue<Character> q = new ArrayDeque<>();
        for (char c : graph.keySet()) {
            if (indegree[c - 'a'] == 0) q.offer(c);
        }

        StringBuilder sb = new StringBuilder();
        while (!q.isEmpty()) {
            char c = q.poll();
            sb.append(c);
            for (char nei : graph.get(c)) {
                if (--indegree[nei - 'a'] == 0) q.offer(nei);
            }
        }

        return sb.length() == graph.size() ? sb.toString() : "";
    }
}
```

> **Why Kahn?**  
> • Intuitive queue approach.  
> • Easy to spot cycles: if processed nodes < total nodes → cycle.

---

### 3.2 Python (DFS + Recursion Stack)

```python
class Solution:
    def alienOrder(self, words: List[str]) -> str:
        from collections import defaultdict

        # 1. Build graph
        graph = defaultdict(set)
        nodes = set(c for w in words for c in w)

        for i in range(len(words) - 1):
            w1, w2 = words[i], words[i + 1]
            min_len = min(len(w1), len(w2))
            j = 0
            while j < min_len and w1[j] == w2[j]:
                j += 1
            if j == min_len and len(w1) > len(w2):
                return ""  # prefix conflict
            if j < min_len:
                graph[w1[j]].add(w2[j])

        # 2. DFS topological sort
        visited, on_stack, res = {}, {}, []

        def dfs(node):
            visited[node] = True
            on_stack[node] = True
            for nei in graph[node]:
                if nei not in visited:
                    if not dfs(nei):
                        return False
                elif on_stack[nei]:
                    return False  # cycle
            on_stack[node] = False
            res.append(node)
            return True

        for node in nodes:
            if node not in visited:
                if not dfs(node):
                    return ""

        return "".join(reversed(res))
```

> **Why DFS?**  
> • Works well for small graphs.  
> • Detects cycles with a `on_stack` set.  
> • Returns any valid order (nodes are appended post‑visit).

---

### 3.3 C++ (Kahn’s Algorithm)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string alienOrder(vector<string>& words) {
        unordered_map<char, unordered_set<char>> graph;
        vector<int> indegree(26, 0);

        // Collect all unique letters
        for (auto& w : words)
            for (char c : w)
                graph[c];                // ensures node exists

        // Build edges
        for (size_t i = 0; i + 1 < words.size(); ++i) {
            const string& w1 = words[i];
            const string& w2 = words[i+1];
            size_t minLen = min(w1.size(), w2.size());
            size_t j = 0;
            while (j < minLen && w1[j] == w2[j]) ++j;
            if (j == minLen && w1.size() > w2.size()) return ""; // prefix case
            if (j < minLen) {
                char u = w1[j], v = w2[j];
                if (graph[u].insert(v).second)  // new edge
                    ++indegree[v - 'a'];
            }
        }

        // Kahn
        queue<char> q;
        for (auto &p : graph)
            if (indegree[p.first - 'a'] == 0) q.push(p.first);

        string ans;
        while (!q.empty()) {
            char c = q.front(); q.pop();
            ans += c;
            for (char nei : graph[c]) {
                if (--indegree[nei - 'a'] == 0)
                    q.push(nei);
            }
        }

        return ans.size() == graph.size() ? ans : "";
    }
};
```

> **Why C++?**  
> • Uses `unordered_map` and `unordered_set` for fast look‑ups.  
> • Maintains clarity while being memory efficient.

---

## 4. SEO‑Optimized Blog Article

> **Title:** *Alien Dictionary Problem – Master Topological Sort (Java/Python/C++) | Interview Preparation Guide*  
> **Meta Description:** *Learn how to solve LeetCode 269 “Alien Dictionary” in Java, Python, and C++. Understand the topological sort algorithm, edge cases, and interview‑style pitfalls.*

---

### 4.1 Introduction

When preparing for a software‑engineering interview, it’s rare that you’ll encounter a problem that is *both* tricky and essential. LeetCode’s **Alien Dictionary** (Problem 269) is one of those rare gems. It blends graph theory, string manipulation, and careful edge‑case handling into a single, elegant question. In this article, we’ll:

* Break down the problem statement and constraints.  
* Walk through a clean, production‑ready solution in **Java**, **Python**, and **C++**.  
* Highlight the **good** (concepts you’ll ace), the **bad** (common pitfalls), and the **ugly** (the real interview traps).  
* Provide a final checklist to help you nail this problem on your next interview.

---

### 4.2 Problem Recap

You’re given an array of words sorted in an *alien* lexicographic order. Your task is to infer a possible order of the alphabet used by this alien language. If the given ordering cannot exist (i.e., there’s a cycle or a prefix conflict), return an empty string.

**Examples**

| Words | Result |
|-------|--------|
| `["wrt","wrf","er","ett","rftt"]` | `"wertf"` |
| `["z","x"]` | `"zx"` |
| `["z","x","z"]` | `""` (invalid) |

---

### 4.3 The Core Idea: Topological Sorting

> **Topological Sort** orders nodes of a directed acyclic graph (DAG) such that every directed edge `u → v` appears before `v`.  
> In our case, each node is a letter, and an edge `u → v` indicates `u` comes before `v` in the alien alphabet.

**Why topological sort?**  
* The problem is *exactly* a partial ordering.  
* Detecting cycles (impossible orders) is inherent to topological sorting.

---

### 4.4 Step‑by‑Step Solution

#### 4.4.1 Build the Graph

1. **Collect all unique letters** – they are nodes.  
2. **Compare adjacent words**:  
   * Find the first differing character.  
   * Add a directed edge from the letter in the first word to the one in the second.  
   * If one word is a prefix of another but comes *after* it, the order is invalid (e.g., `"abcd"` then `"abc"`).

#### 4.4.2 Topological Sort

- **Kahn’s Algorithm (BFS)**  
  * Compute indegrees.  
  * Start with all nodes having indegree 0.  
  * Remove them, decrement neighbors’ indegrees, and enqueue new 0‑indegree nodes.  
  * If processed nodes < total nodes → cycle.

- **DFS with Recursion Stack**  
  * Perform a post‑order DFS.  
  * Maintain a “on‑stack” set to detect back‑edges (cycles).  
  * Append nodes to result when backtracking.

Both approaches are O(V + E). Kahn’s algorithm is often preferred for interview clarity, but DFS is equally valid.

#### 4.4.3 Return the Ordering

- Concatenate nodes in the order they’re popped/finished.  
- If a cycle was detected, return `""`.

---

### 4.5 Code Walkthrough

We’ve already provided full implementations in **Java**, **Python**, and **C++** above. Key takeaways:

* **Edge‑case handling**: The prefix conflict check is critical and often missed.  
* **Graph Representation**: `Map<Character, Set<Character>>` (Java), `defaultdict(set)` (Python), `unordered_map<char, unordered_set<char>>` (C++).  
* **Indegree Calculation**: Update only when a *new* edge is added.  
* **Cycle Detection**: In Kahn’s algorithm, compare processed count with total nodes. In DFS, use the `on_stack` set.

---

### 4.6 Good, Bad, and Ugly

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Concept** | Understanding topological sort and DAGs. | Forgetting the *prefix* rule (`abc` before `ab`). | Overcomplicating with unnecessary data structures. |
| **Edge Cases** | Checking `len(word1) > len(word2)` after no diff. | Assuming all words have the same length. | Assuming the alphabet only contains letters from the words (ignore letters that never appear). |
| **Complexity** | O(V+E) – linear. | Quadratic loops over all words & letters. | Memory leaks (e.g., not clearing adjacency lists). |
| **Implementation** | Clean BFS queue or DFS recursion. | Mixing BFS/DFS incorrectly. | Using global state instead of passing parameters. |
| **Interview Tips** | Explain your plan first. | Skip explaining cycle detection. | Deliver code without commentary. |

---

### 4.7 Final Checklist for Interview Success

1. **Clarify Constraints** – words count, length, alphabet size.  
2. **Explain Your Approach** – graph construction, edge‑case check, topological sort.  
3. **Implement Cleanly** – use standard library containers, avoid unnecessary loops.  
4. **Test Edge Cases** – prefix conflict, single word, no edges, full cycle.  
5. **Time & Space Analysis** – O(V+E) time, O(V+E) space.  
6. **Explain Cycle Detection** – why returning `""` is correct.  
7. **Optional** – Show both BFS and DFS to demonstrate depth of understanding.

---

### 4.8 Why This Problem Stands Out

* **Graph Theory + Strings** – shows versatility.  
* **Common Interview Pitfall** – prefix handling.  
* **Open‑Ended** – multiple correct answers (any valid alphabet order).  
* **Scalable** – same logic applies to bigger alphabets or longer words.

If you master this problem, you’ll be ready to tackle other topological‑sort based interview questions like *Course Schedule*, *Word Ladder*, and *Critical Path in a Project*.

---

### 4.9 Conclusion

Alien Dictionary is a *mini‑project* that tests your ability to convert a real‑world problem into a graph, apply the right algorithm, and handle edge cases meticulously. By practicing the Java, Python, and C++ solutions, and keeping the good, bad, and ugly points in mind, you’ll build a strong foundation for graph‑centric interview challenges.

Good luck, and may your alphabet always be topologically sorted! 🌌

---

> **Call to Action:**  
> Download our free interview‑style practice set, and join our community of interviewees. Share your own solutions in the comments and help others master topological sort!

---



---

### 4.10 Keywords & LSI

* Alien Dictionary  
* Topological sort  
* LeetCode 269  
* Java interview  
* Python graph problem  
* C++ interview coding  
* Graph theory interview questions  
* Software engineering interview prep  
* Cycle detection in graphs  
* Prefix conflict handling  

---

By combining comprehensive, multi‑language solutions with a SEO‑friendly article, you’ll attract recruiters looking for *graph‑savvy* candidates. Good luck!