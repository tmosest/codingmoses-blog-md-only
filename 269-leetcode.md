---
title: LeetCode 269. Alien Dictionary - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 👽 Alien Dictionary – 269. LeetCode  
**Difficulty:** Hard  

| Language | Time | Space |
|----------|------|-------|
| **Java** | O(V+E) | O(V+E) |
| **Python** | O(V+E) | O(V+E) |
| **C++** | O(V+E) | O(V+E) |

> **Goal** – Reconstruct the order of the letters in an alien language from a dictionary that is already sorted according to that language.

---

## 1️⃣  Why this problem matters in interviews

1. **Graph theory + Topological Sort** – core CS topic, very interview‑friendly.  
2. **Edge cases** – detecting the “prefix” conflict (`"abc"`, `"ab"`) that makes the dictionary invalid.  
3. **Complexity mindset** – candidates must explain why Kahn’s/BFS or DFS is needed instead of brute‑force.  

If you can explain the idea, write clean code, and talk about pitfalls, you’ll impress hiring managers at Google, Amazon, Microsoft, and the “new‑stack” startups.

---

## 2️⃣  The core idea

1. **Build a directed graph**  
   * Each letter is a node (max 26).  
   * For each adjacent pair of words, the first differing character gives an edge `u → v`.  
   * If one word is a strict prefix of the other (`"abc"` before `"ab"`), the dictionary is impossible → return `""`.

2. **Topological sort**  
   * If the graph has a cycle → impossible.  
   * Otherwise, produce an ordering of all letters that appears in the dictionary.

We’ll show two well‑known algorithms:  
* **Kahn’s BFS (queue + indegree)** – easy to reason about cycles.  
* **DFS + reverse post‑order** – slightly shorter, but need to detect back‑edges.

Both run in **O(V+E)** where  
`V ≤ 26` (letters), `E ≤ 26²` (edges), `words.length ≤ 100`, `word.length ≤ 100`.

---

## 3️⃣  Code – Java (BFS/Kahn)

```java
import java.util.*;

public class Solution {
    public String alienOrder(String[] words) {
        // 1. Build adjacency list and indegree array
        Map<Character, Set<Character>> graph = new HashMap<>();
        int[] indegree = new int[26];
        for (String w : words) {
            for (char c : w.toCharArray()) graph.putIfAbsent(c, new HashSet<>());
        }

        // Compare adjacent words
        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i], w2 = words[i+1];
            int min = Math.min(w1.length(), w2.length());
            int j = 0;
            while (j < min && w1.charAt(j) == w2.charAt(j)) j++;

            if (j == min && w1.length() > w2.length()) return ""; // prefix conflict

            if (j < min) {                 // found the first different char
                char u = w1.charAt(j), v = w2.charAt(j);
                if (graph.get(u).add(v))  // add edge only once
                    indegree[v - 'a']++;
            }
        }

        // 2. Kahn's algorithm
        Queue<Character> q = new ArrayDeque<>();
        for (char c : graph.keySet()) {
            if (indegree[c - 'a'] == 0) q.offer(c);
        }

        StringBuilder sb = new StringBuilder();
        while (!q.isEmpty()) {
            char cur = q.poll();
            sb.append(cur);
            for (char nb : graph.get(cur)) {
                if (--indegree[nb - 'a'] == 0) q.offer(nb);
            }
        }

        // If we couldn't visit all nodes -> cycle
        if (sb.length() != graph.size()) return "";
        return sb.toString();
    }
}
```

---

## 4️⃣  Code – Python (DFS + reverse post‑order)

```python
from collections import defaultdict
import sys
sys.setrecursionlimit(10000)

class Solution:
    def alienOrder(self, words: list[str]) -> str:
        graph = defaultdict(set)
        nodes = set()
        for w in words:
            nodes.update(w)

        # Build edges
        for i in range(len(words)-1):
            w1, w2 = words[i], words[i+1]
            min_len = min(len(w1), len(w2))
            j = 0
            while j < min_len and w1[j] == w2[j]:
                j += 1
            if j == min_len and len(w1) > len(w2):   # prefix case
                return ""
            if j < min_len:
                graph[w1[j]].add(w2[j])

        visited = {}
        order = []

        def dfs(node):
            if node in visited:
                return visited[node]  # False means cycle
            visited[node] = False   # temporary mark
            for nb in graph[node]:
                if not dfs(nb):
                    return False
            visited[node] = True    # permanent mark
            order.append(node)
            return True

        for node in nodes:
            if node not in visited:
                if not dfs(node):
                    return ""

        order.reverse()
        return ''.join(order)
```

---

## 5️⃣  Code – C++ (BFS/Kahn)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string alienOrder(vector<string>& words) {
        vector<unordered_set<char>> graph(26);
        vector<int> indeg(26, 0);
        vector<bool> present(26, false);

        // Record which letters appear
        for (const string& w : words)
            for (char c : w) present[c-'a'] = true;

        // Build graph
        for (int i = 0; i + 1 < words.size(); ++i) {
            string &a = words[i], &b = words[i+1];
            int len = min(a.size(), b.size()), j = 0;
            while (j < len && a[j] == b[j]) ++j;
            if (j == len && a.size() > b.size()) return "";
            if (j < len) {
                char u = a[j], v = b[j];
                if (graph[u-'a'].insert(v).second)
                    ++indeg[v-'a'];
            }
        }

        queue<char> q;
        for (int i = 0; i < 26; ++i)
            if (present[i] && indeg[i] == 0) q.push('a'+i);

        string res;
        while (!q.empty()) {
            char cur = q.front(); q.pop();
            res += cur;
            for (char nb : graph[cur-'a']) {
                if (--indeg[nb-'a'] == 0) q.push(nb);
            }
        }

        // If cycle or missing nodes
        int nodes = accumulate(present.begin(), present.end(), 0);
        return (res.size() == nodes) ? res : "";
    }
};
```

---

## 6️⃣  Blog Article – “The Good, The Bad, and The Ugly of Alien Dictionary”

### 1️⃣ Introduction  
> Ever wondered how a computer can figure out the order of an alien language? The “Alien Dictionary” problem on LeetCode is a classic interview question that tests graph theory, edge‑case handling, and algorithmic efficiency. In this post, we’ll walk through **why it’s good for your resume**, the **tricky pitfalls** that can trip you up, and the **ugly bugs** that you’ll need to guard against. We’ll also sprinkle in some SEO‑friendly keywords like *topological sort*, *Kahn’s algorithm*, *DFS*, and *coding interview* so recruiters can find you.

### 2️⃣ The Good – What You Gain

| Benefit | Why it matters |
|---------|----------------|
| **Graph‑based thinking** | Topological sort is a core CS concept that appears in G, O, and FA interview stacks. |
| **Edge‑case awareness** | Handling the “prefix” rule shows you care about input validity, a trait recruiters value. |
| **Multiple solution styles** | Knowing both BFS (Kahn) and DFS variants shows depth of understanding. |
| **Clean code** | Implementing with adjacency lists, indegree arrays, and early exits demonstrates coding hygiene. |
| **Time complexity mastery** | O(V+E) solution is optimal; explaining why you chose it shows algorithmic maturity. |

> **Keyword focus**: *interview coding challenge*, *algorithm design*, *efficient graph traversal*, *LeetCode hard problem*.

### 3️⃣ The Bad – Common Mistakes

1. **Assuming the input is always valid** – Forgetting the prefix rule leads to wrong answers on test case 3 (`["z","x","z"]`).  
2. **Adding duplicate edges** – If you add the same edge multiple times, indegree counts become wrong.  
3. **Ignoring isolated nodes** – Letters that never appear in any edge must still be part of the output.  
4. **Using recursion without cycle detection** – DFS without a temporary mark can mis‑detect cycles.

> **Tip**: Always keep a set of “seen” letters and an `indegree` array that counts **unique** edges.

### 4️⃣ The Ugly – Hidden Traps

| Trap | Real‑world impact | Fix |
|------|-------------------|-----|
| **Large word lengths** | Even though `V` is ≤26, you may iterate over every character of every word. If `words.length = 100` and `word.length = 100`, a naive double loop can hit 10⁶ ops. | Use a single pass over each pair of adjacent words; stop at first differing char. |
| **Stack overflow in recursive DFS** | In Python/C++, a chain of 100 words can create a recursion depth of 100. | Increase recursion limit in Python, or use iterative DFS. |
| **Queue order bias** | In Kahn’s algorithm, the order in which zero‑indegree nodes are enqueued can produce different but still valid topological orders. | That's acceptable; problem says “any” order is fine. |
| **Mis‑interpreting “letters that appear”** | If you only consider nodes that have outgoing edges, letters that only appear as targets get omitted. | Build a `present` set from all words before constructing the graph. |

### 5️⃣ SEO‑Optimized Takeaway

- **Problem name**: Alien Dictionary – 269 on LeetCode  
- **Key concepts**: *Topological Sort*, *Kahn’s Algorithm*, *DFS*, *Graph Construction*, *Cycle Detection*  
- **Skills showcased**: *Algorithm Design*, *Data Structures*, *Edge‑Case Handling*, *Clean Coding*, *Complexity Analysis*  
- **Job‑search tags**: *Python Developer*, *Java Engineer*, *C++ Programmer*, *Software Engineer*, *Technical Interview Prep*, *Coding Challenge*  

### 6️⃣ Conclusion

The Alien Dictionary problem is a “micro‑portfolio” in one function: it displays graph knowledge, rigorous edge‑case thinking, and clear coding style. Mastering it means you’re ready to tackle any interview question that hides a topological sort under a friendly narrative.  

> **Next steps**:  
> 1. Try implementing both BFS and DFS solutions in your preferred language.  
> 2. Write unit tests for the edge cases (`prefix`, isolated nodes, cycles).  
> 3. Add comments and complexity analysis to your code – recruiters love clean, documented code.  
> 4. Publish the solution on GitHub, tag it with *AlienDictionary* and *TopologicalSort*, and share it on LinkedIn with the above SEO keywords.  

Good luck, and may the (alien) order be with you! 🚀

--- 

> **Want more interview‑ready articles?** Subscribe to our newsletter for weekly coding challenge walkthroughs.