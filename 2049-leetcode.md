---
title: LeetCode 2049. Count Nodes With the Highest Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Three‑Language Solutions for LeetCode 2049  
**“Count Nodes With the Highest Score”** – O(n) DFS

| Language | Code |
|----------|------|
| **Java** | <details><summary>Click to expand</summary>

```java
import java.util.*;

class Solution {
    /**  O(n) DFS solution for LeetCode 2049  */
    public int countHighestScoreNodes(int[] parents) {
        int n = parents.length;
        // Build adjacency list
        List<Integer>[] children = new ArrayList[n];
        for (int i = 0; i < n; i++) children[i] = new ArrayList<>();
        for (int i = 1; i < n; i++) children[parents[i]].add(i);

        long maxScore = 0;
        int bestCnt   = 0;
        dfs(0, children, n, maxScore, bestCnt);
        return bestCnt;
    }

    // DFS that returns the size of the subtree rooted at `node`
    private int dfs(int node, List<Integer>[] children, int n,
                    long maxScore, int bestCnt) {
        long score = 1;
        int sum = 0;          // number of nodes in all child subtrees

        for (int child : children[node]) {
            int sz = dfs(child, children, n, maxScore, bestCnt);
            score *= sz;     // multiply by size of each child subtree
            sum += sz;
        }

        int rest = n - sum - 1;   // nodes that stay when the current node is removed
        if (rest > 0) score *= rest;

        if (score > maxScore) {
            maxScore = score;
            bestCnt = 1;
        } else if (score == maxScore) {
            bestCnt++;
        }

        return sum + 1;   // size of current subtree
    }
}
```
</details> |
| **Python** | <details><summary>Click to expand</summary>

```python
class Solution:
    def countHighestScoreNodes(self, parents: List[int]) -> int:
        n = len(parents)
        # adjacency list
        children = [[] for _ in range(n)]
        for i in range(1, n):
            children[parents[i]].append(i)

        self.max_score = 0
        self.result = 0

        def dfs(node: int) -> int:
            score = 1
            total = 0  # nodes in child subtrees
            for child in children[node]:
                sz = dfs(child)
                score *= sz
                total += sz

            rest = n - total - 1
            if rest > 0:
                score *= rest

            if score > self.max_score:
                self.max_score = score
                self.result = 1
            elif score == self.max_score:
                self.result += 1

            return total + 1  # size of this subtree

        dfs(0)
        return self.result
```
</details> |
| **C++** | <details><summary>Click to expand</summary>

```cpp
class Solution {
public:
    int countHighestScoreNodes(vector<int>& parents) {
        int n = parents.size();
        vector<vector<int>> child(n);
        for (int i = 1; i < n; ++i) child[parents[i]].push_back(i);

        long long maxScore = 0;
        int answer = 0;

        function<int(int)> dfs = [&](int node) -> int {
            long long score = 1;
            int sum = 0;                 // nodes in child subtrees
            for (int ch : child[node]) {
                int sz = dfs(ch);
                score *= sz;
                sum += sz;
            }
            int rest = n - sum - 1;      // remaining nodes when this node is removed
            if (rest > 0) score *= rest;

            if (score > maxScore) {
                maxScore = score;
                answer = 1;
            } else if (score == maxScore) {
                ++answer;
            }
            return sum + 1;              // size of current subtree
        };

        dfs(0);
        return answer;
    }
};
```
</details> |

> **Why O(n) Works**  
> Each node is visited exactly once.  
> - Building the adjacency list is **O(n)**.  
> - The DFS computes subtree sizes in a single post‑order pass – again **O(n)**.  
> The only heavy operation is multiplication of at most 2 × 10⁵ numbers, which fits easily in 64‑bit (`long`/`long long`).

> **Handling the Root**  
> The root has no “parent side” subtree.  
> In the formula `rest = n - total - 1`, `rest` is zero for the root (there is no node above it).  
> We multiply by `rest` only when it’s > 0; otherwise we simply ignore that factor – this keeps the score correct for the root.

> **Edge‑Case Checklist**  
> * `n = 2` – only the root and one leaf; score = 1, answer = 1.  
> * All nodes are leaves – every score = 1, answer = n.  
> * Very unbalanced tree (a chain) – `rest` may be zero many times, but the logic still works.

> **Common Pitfalls**  
> 1. Using `int` for the score can overflow (max score can be ~2 × 10¹⁰).  
> 2. Forgetting to subtract the current node (`-1`) when computing `rest`.  
> 3. Mis‑ordering the DFS (pre‑order vs post‑order) – you must first know child sizes before computing the node’s score.

> **Time & Space**  
> *Time:* **O(n)**  
> *Space:* **O(n)** for the adjacency list + recursion stack (≤ n).

---

## 2️⃣  SEO‑Optimized Blog Post – “LeetCode 2049 Explained: Good, Bad, Ugly”

> **Keywords**: *LeetCode 2049, Count Nodes With the Highest Score, binary tree DFS, O(N) algorithm, interview coding problem, software engineer interview, technical interview tips, algorithm design, job interview preparation.*

---

### 📌 Introduction  
If you’re chasing your first software‑engineering role or brushing up for a coding interview, **LeetCode 2049 – “Count Nodes With the Highest Score”** is a classic problem that tests your understanding of trees, recursion, and careful arithmetic. Below we dissect the problem, walk through a clean O(n) solution, and highlight what makes it a *great interview question* (the “good”), what pitfalls keep candidates stuck (the “bad”), and the common messy patterns that can lead to bugs (the “ugly”).  

---

### 📜 Problem Statement  
> You are given a binary tree of `n` nodes (0‑based indexing) described by the array `parents`, where `parents[i]` is the parent of node `i` (the root has `-1`).  
> For each node, you remove it and compute the product of the sizes of all resulting connected components.  
> The *score* of a node is that product.  
> **Task**: Return how many nodes achieve the highest score.

---

### 🔍 Key Insight – DFS + Subtree Size  
The problem boils down to two facts:  

1. **When you delete a node, every child becomes a separate component whose size equals its subtree size.**  
2. **The remaining nodes (all that are not in any child subtree) form one more component** – the “rest” of the tree.

Therefore, if you know the size of every subtree, you can compute the score for each node in a single bottom‑up pass.  

---

## 🧑‍💻 Solution Overview  
We build an adjacency list (`children`) and perform a post‑order DFS that returns the size of the subtree rooted at the current node.

During the DFS:

1. For each child `c`, recursively compute `size_c`.  
2. Multiply the running product (`score`) by `size_c`.  
3. Accumulate `total += size_c`.  
4. After exploring all children, the number of remaining nodes is  
   `rest = n - total - 1`.  
5. If `rest > 0`, multiply `score` by `rest`.  
6. Track the maximum score seen so far and count how many nodes hit it.

All arithmetic uses 64‑bit integers (`long`/`long long`) to avoid overflow.

---

## 📈 Complexity Analysis  

| Step | Cost |
|------|------|
| Building adjacency list | **O(n)** |
| DFS traversal | **O(n)** (each node visited once) |
| Memory | **O(n)** (adjacency list + recursion stack) |

Thus the overall solution runs in **O(n)** time and **O(n)** space – the optimal for this problem.

---

## 📚 Language‑Specific Implementations  

### 1. Java  
```java
import java.util.*;

class Solution {
    public int countHighestScoreNodes(int[] parents) {
        int n = parents.length;
        List<Integer>[] children = new ArrayList[n];
        for (int i = 0; i < n; i++) children[i] = new ArrayList<>();
        for (int i = 1; i < n; i++) children[parents[i]].add(i);

        long[] maxScore = new long[1];   // use array to allow modification in nested lambda
        int[]   answer  = new int[1];

        // Post‑order DFS implemented via a private method
        int dfs(int node) {
            long score = 1;
            int sum = 0;
            for (int child : children[node]) {
                int sz = dfs(child);
                score *= sz;
                sum += sz;
            }
            int rest = n - sum - 1;
            if (rest > 0) score *= rest;

            if (score > maxScore[0]) { maxScore[0] = score; answer[0] = 1; }
            else if (score == maxScore[0]) { answer[0]++; }

            return sum + 1;
        }

        dfs(0);
        return answer[0];
    }
}
```

### 2. Python  
```python
from typing import List

class Solution:
    def countHighestScoreNodes(self, parents: List[int]) -> int:
        n = len(parents)
        children = [[] for _ in range(n)]
        for i in range(1, n):
            children[parents[i]].append(i)

        self.max_score = 0
        self.answer = 0

        def dfs(node: int) -> int:
            score = 1
            total = 0
            for child in children[node]:
                sz = dfs(child)
                score *= sz
                total += sz
            rest = n - total - 1
            if rest > 0:
                score *= rest
            if score > self.max_score:
                self.max_score = score
                self.answer = 1
            elif score == self.max_score:
                self.answer += 1
            return total + 1

        dfs(0)
        return self.answer
```

### 3. C++  
```cpp
class Solution {
public:
    int countHighestScoreNodes(vector<int>& parents) {
        int n = parents.size();
        vector<vector<int>> child(n);
        for (int i = 1; i < n; ++i) child[parents[i]].push_back(i);

        long long maxScore = 0;
        int answer = 0;

        function<int(int)> dfs = [&](int node) -> int {
            long long score = 1;
            int sum = 0;
            for (int ch : child[node]) {
                int sz = dfs(ch);
                score *= sz;
                sum += sz;
            }
            int rest = n - sum - 1;
            if (rest > 0) score *= rest;

            if (score > maxScore) { maxScore = score; answer = 1; }
            else if (score == maxScore) { ++answer; }

            return sum + 1;
        };

        dfs(0);
        return answer;
    }
};
```

---

## 🚀 The “Good” – Why This Solution Rocks  

| ✔️ Feature | Why it matters |
|------------|----------------|
| **Linear time** | Interviewers love O(n) – it shows you understand tree traversal. |
| **Clear separation of concerns** | Building adjacency → DFS → score calculation. |
| **Safe arithmetic** | Using `long / long long` prevents overflow on 2 × 10⁵ nodes. |
| **Post‑order traversal** | Guarantees child subtree sizes are known before computing a parent’s score. |
| **In‑place state tracking** | No need for an auxiliary array to store all scores – we update `maxScore` and `result` on the fly. |

---

## ⚠️ The “Bad” – Common Mistakes That Make You Lose Points  

| ❌ Mistake | Impact | Fix |
|------------|--------|-----|
| Using `int` for score | Overflow → wrong answers | Switch to `long` (`long long` in C++/Java) |
| Forgetting `-1` when computing `rest` | Adds an extra node to the rest component | `rest = n - total - 1` |
| Not handling `rest = 0` (root) | Multiplying by 0 kills the product | Multiply only if `rest > 0` |
| Pre‑order DFS without memo | Children not processed yet → missing subtree size | Use post‑order (return size) |
| Ignoring recursion stack limit | Stack overflow on deep trees | Tail‑recursive or iterative DFS if language permits |

---

## 🎭 The “Ugly” – Messy Patterns That Lead to Bugs  

| 🙃 Pattern | Why it’s ugly | Cleaner alternative |
|------------|---------------|----------------------|
| Building `rest` as `n - total` (forgetting `-1`) | Adds an invisible component of size 0 | `rest = n - total - 1` |
| Recursion returning a pair `(size, score)` | Over‑engineering; two‑layer loops | Keep score updated during traversal |
| Storing all component sizes in a separate vector | Unnecessary memory | Compute and discard as soon as you finish a node |
| Using global variables without encapsulation | Hard to reason / test | Pass them as parameters or store in a small wrapper struct |

---

## 📣 Final Takeaway  
LeetCode 2049 is more than a trick question – it forces you to think **about the consequences of cutting a node**. Master the O(n) DFS trick, avoid the overflow traps, and you’ll impress both the hiring manager and the algorithmic blackboard.  

> **Pro‑Tip**: When practicing, try writing the solution in **two** languages (e.g., Java & Python). It trains you to translate algorithmic ideas across ecosystems—a vital skill when interviewing for roles that use heterogeneous tech stacks.

Good luck, and happy coding! 🚀  

--- 

*For deeper dives into tree problems or other LeetCode interview challenges, subscribe to our newsletter and keep coding!*  

--- 

> **Author**: *[Your Name]* – coding enthusiast, algorithm advocate, and first‑year software engineer preparing for a portfolio of interviews.  

--- 

> **Contact**: `youremail@example.com` | `LinkedIn: /in/yourprofile` | `GitHub: /yourusername`

--- 

### 🎉 TL;DR  
- O(n) DFS + subtree size = optimal solution for LeetCode 2049.  
- Use 64‑bit arithmetic, handle the root carefully, and update the maximum score on the fly.  
- The problem is a stellar interview question: clean, scalable, and full of subtle arithmetic nuance.  

Happy interviewing!  

--- 

*Feel free to adapt the blog snippet into your own blog platform or LinkedIn post to showcase your deep dive into **LeetCode 2049**.*  

--- 

**End of post**.