---
title: LeetCode 3331. Find Subtree Sizes After Changes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 3331. Find Subtree Sizes After Changes – Complete Code (Java / Python / C++) + SEO‑Optimized Blog Post

---

### TL;DR  
- **Problem** – Re‑parent each node to the closest ancestor with the same character and return the size of every subtree in the new tree.  
- **Solution** – Two depth‑first traversals.  
  1. **DFS1** – While walking the original tree keep the *last seen* index for every character. If a previous index exists, that node becomes the new parent.  
  2. **DFS2** – Build a new adjacency list with the updated parents and count subtree sizes in one more post‑order DFS.  
- **Complexity** – **O(n)** time, **O(n)** space.  
- **Languages** – Java, Python, C++ – ready to copy‑paste into your IDE or LeetCode.

---

## 1. The Code

> **⚠️  Recursion depth** – The input can contain up to \(10^5\) nodes.  
> In Java / C++ the default stack usually handles this, but in Python you need to bump the recursion limit (or use an iterative DFS).

### 1.1 Java

```java
import java.util.*;

class Solution {
    public int[] findSubtreeSizes(int[] parent, String s) {
        int n = parent.length;
        List<Integer>[] children = new ArrayList[n];
        for (int i = 0; i < n; i++) children[i] = new ArrayList<>();

        // Build original adjacency list
        for (int i = 1; i < n; i++) children[parent[i]].add(i);

        int[] newParent = parent.clone();          // will hold the updated parents
        int[] lastSeen = new int[26];
        Arrays.fill(lastSeen, -1);

        // DFS1 – find new parents
        dfsParent(0, s.toCharArray(), children, lastSeen, newParent);

        // Build new adjacency list from newParent
        List<Integer>[] newChildren = new ArrayList[n];
        for (int i = 0; i < n; i++) newChildren[i] = new ArrayList<>();
        for (int i = 1; i < n; i++) newChildren[newParent[i]].add(i);

        int[] answer = new int[n];
        dfsSize(0, newChildren, answer);
        return answer;
    }

    private void dfsParent(int node, char[] chars, List<Integer>[] children,
                           int[] lastSeen, int[] newParent) {
        int idx = chars[node] - 'a';
        int prev = lastSeen[idx];
        if (prev != -1) newParent[node] = prev;          // new parent found

        lastSeen[idx] = node;                            // push current
        for (int child : children[node]) {
            dfsParent(child, chars, children, lastSeen, newParent);
        }
        lastSeen[idx] = prev;                            // pop / restore
    }

    private int dfsSize(int node, List<Integer>[] children, int[] ans) {
        int sz = 1;
        for (int child : children[node]) {
            sz += dfsSize(child, children, ans);
        }
        ans[node] = sz;
        return sz;
    }
}
```

---

### 1.2 Python

```python
import sys
sys.setrecursionlimit(200000)          # required for deep trees

class Solution:
    def findSubtreeSizes(self, parent: list[int], s: str) -> list[int]:
        n = len(parent)
        children = [[] for _ in range(n)]
        for i in range(1, n):
            children[parent[i]].append(i)

        new_parent = parent[:]              # copy of original parents
        last_seen = [-1] * 26

        def dfs_parent(u: int):
            idx = ord(s[u]) - ord('a')
            prev = last_seen[idx]
            if prev != -1:
                new_parent[u] = prev
            last_seen[idx] = u
            for v in children[u]:
                dfs_parent(v)
            last_seen[idx] = prev

        dfs_parent(0)

        # Build new adjacency
        new_children = [[] for _ in range(n)]
        for i in range(1, n):
            new_children[new_parent[i]].append(i)

        answer = [0] * n

        def dfs_size(u: int) -> int:
            sz = 1
            for v in new_children[u]:
                sz += dfs_size(v)
            answer[u] = sz
            return sz

        dfs_size(0)
        return answer
```

---

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findSubtreeSizes(vector<int>& parent, string s) {
        int n = parent.size();
        vector<vector<int>> children(n);
        for (int i = 1; i < n; ++i) children[parent[i]].push_back(i);

        vector<int> newParent = parent;          // copy
        vector<int> lastSeen(26, -1);

        function<void(int)> dfsParent = [&](int u) {
            int idx = s[u] - 'a';
            int prev = lastSeen[idx];
            if (prev != -1) newParent[u] = prev;
            lastSeen[idx] = u;
            for (int v : children[u]) dfsParent(v);
            lastSeen[idx] = prev;                // restore
        };
        dfsParent(0);

        // Build new adjacency list
        vector<vector<int>> newChildren(n);
        for (int i = 1; i < n; ++i) newChildren[newParent[i]].push_back(i);

        vector<int> answer(n);

        function<int(int)> dfsSize = [&](int u) -> int {
            int sz = 1;
            for (int v : newChildren[u]) sz += dfsSize(v);
            answer[u] = sz;
            return sz;
        };
        dfsSize(0);
        return answer;
    }
};
```

---

## 2. The Blog Post – *Make Your Interview Score Explode!*

> *This post is written for candidates who want to master LeetCode’s tree problems, nail their next interview, and land a job in a top tech company.*  
> Use the heading “**Find Subtree Sizes After Changes**” and the keyword “LeetCode 3331” in your search engine for instant visibility.

---

### 2.1 Title  
> **“LeetCode 3331 – Find Subtree Sizes After Changes: Master the DFS Tree Problem”**

### 2.2 Meta‑Description (SEO)  
> **“Learn how to solve LeetCode 3331 – Find Subtree Sizes After Changes – with Java, Python, and C++ solutions, plus an in‑depth explanation of DFS, tree re‑parenting, and size calculation.”**

### 2.3 Full Article

> **Keywords**: LeetCode 3331, find subtree sizes after changes, tree problems, DFS, parent array, Java, Python, C++, coding interview, algorithm design, data structures, job interview, software engineering interview, interview questions, coding challenge.

---

### 📄 3331 – What Does the Problem Actually Ask?

You’re given a rooted tree as a **parent array** `parent[i]` (root has `-1`) and a string `s` where `s[i]` is the character of node `i`.  
For every node `x` (except the root) you must **re‑parent** it to the **closest ancestor** that holds the same character.  
After all nodes have moved, you return an array `ans` where `ans[i]` equals the number of nodes in the subtree rooted at node `i` **in the new tree**.

---

### 🔍  Key Observations

1. **The tree structure is known by a parent array.**  
   Converting that into an adjacency list (`children`) is trivial: for every `i > 0`, add `i` to `children[parent[i]]`.

2. **Finding the “closest” ancestor with the same character is a classic DFS trick.**  
   While exploring the tree you keep, for each character, the *last* node you’ve visited (`lastSeen`).  
   When you arrive at a node `u`, if `lastSeen[char] != -1` it means there is a *previous* ancestor with the same char, and *because we’re doing a depth‑first walk*, that previous index is automatically the closest one.

3. **After updating all parents you can treat the new tree just like any other tree.**  
   Build a new adjacency list (`newChildren`) from the updated `newParent` array and perform a post‑order DFS to count subtree sizes.

---

### 📐  The Two‑DFS Algorithm

| Step | What it does | Why it works |
|------|--------------|--------------|
| **DFS1** | `dfsParent(node)` <br> *Maintain* `lastSeen[26]` (index of most recent node for each character). <br> *If* a previous index exists, set `newParent[node] = lastSeen[char]`. | The stack nature of DFS guarantees that `lastSeen[char]` is the nearest ancestor with that character. |
| **Re‑build tree** | For every child `i > 0`, insert `i` into `children[newParent[i]]`. | Gives us a proper adjacency list for the new parent relations. |
| **DFS2** | `dfsSize(node)` – post‑order DFS that returns subtree size. | Classic post‑order trick: size of node = 1 + sum of sizes of its children. |

---

### 🚀  Why This is Efficient

| Metric | Result |
|--------|--------|
| **Time** | Each node is visited twice – once in DFS1 and once in DFS2. \(O(n)\). |
| **Space** | Adjacency lists, parent arrays, `lastSeen`, and `ans` – all \(O(n)\). |
| **Constants** | The algorithm only needs a single integer per character (`lastSeen[26]`). No heavy data structures. |

---

## 3. How to Use This in an Interview

| Question | How the answer helps |
|----------|----------------------|
| “Explain your solution in 2‑min.” | **DFS + stack of last seen** – two passes, constant‑size auxiliary data. |
| “What’s the worst‑case time?” | **Linear** – even for a degenerate chain of \(10^5\) nodes. |
| “Could you write this in Python?” | Provide the Python code above and mention recursion limit. |
| “What about iterative DFS?” | You can replace the recursion with an explicit stack if you prefer. |

---

## 4. Quick‑Start Checklist

1. **Copy** the language block that matches your environment.  
2. **Paste** into LeetCode’s editor or your local IDE.  
3. **Run** on the sample tests.  
4. **Add** your own custom cases (deep chains, balanced trees, all‑same characters, all‑different characters).  
5. **Submit** – you’ll get accepted in the first go!  

---

## 5. SEO‑Optimized Blog Wrap‑Up

> **Title:** *LeetCode 3331 – Find Subtree Sizes After Changes: DFS Tree Re‑Parenting in Java, Python & C++*  
> **Meta‑Description:** *Master LeetCode 3331 (Find Subtree Sizes After Changes) with full Java, Python, and C++ solutions, a step‑by‑step DFS explanation, and interview‑ready insights.*

### 📌 Why Reading This Helps You Land a Job

- **Deep DFS Mastery** – Most interviews test tree traversal. This problem demonstrates how to maintain state (`lastSeen`) while walking a tree.  
- **Parent‑Array to Adjacency** – Converting between representations is a common interview trick.  
- **Clear Complexity Analysis** – Demonstrating \(O(n)\) space/time shows you can reason about asymptotics on the fly.  
- **Language Flexibility** – Knowing the same algorithm in three languages signals to interviewers that you understand *concept* over *syntax*.  

---

### 🎯 Takeaway

If you’ve ever stared at the parent array, felt unsure how to traverse the tree, or worried about recursion limits, the above code and explanation give you a **bullet‑proof blueprint**.  

Use it, test it, and explain it in your next interview. You’ll demonstrate:

- **Algorithmic thinking** – two passes, minimal extra memory.  
- **Coding discipline** – clean separation of concerns (parent update vs. size calculation).  
- **Language fluency** – Java, Python, and C++ versions show adaptability.  

Happy coding, and good luck landing that dream job! 🚀