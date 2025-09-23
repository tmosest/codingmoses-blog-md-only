---
title: LeetCode 2196. Create Binary Tree From Descriptions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Solution Code

Below are clean, production‑ready implementations for **LeetCode 2196 – “Create Binary Tree From Descriptions”** in **Java, Python and C++**.  
All three solutions share the same algorithmic idea:

1. **Build a node map** – every unique value → its `TreeNode`.
2. **Link children to parents** – using the `isLeft` flag.
3. **Find the root** – the only value that never appears as a child.

The approach is **O(n)** in time and space where *n* is the number of descriptions.

---

### Java

```java
import java.util.*;

public class Solution {
    // Definition for a binary tree node.
    public static class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int x) { val = x; }
    }

    public TreeNode createBinaryTree(int[][] descriptions) {
        // 1. Build map from value to node
        Map<Integer, TreeNode> nodeMap = new HashMap<>();

        // 2. Keep track of all children to find the root later
        Set<Integer> children = new HashSet<>();

        for (int[] d : descriptions) {
            int parent = d[0], child = d[1], isLeft = d[2];

            // Get or create parent node
            TreeNode parentNode = nodeMap.computeIfAbsent(parent,
                    k -> new TreeNode(k));

            // Get or create child node
            TreeNode childNode = nodeMap.computeIfAbsent(child,
                    k -> new TreeNode(k));

            // Link according to isLeft flag
            if (isLeft == 1) {
                parentNode.left = childNode;
            } else {
                parentNode.right = childNode;
            }

            children.add(child);
        }

        // 3. The root is the node that never appears as a child
        int rootVal = -1;
        for (int val : nodeMap.keySet()) {
            if (!children.contains(val)) {
                rootVal = val;
                break;
            }
        }
        return nodeMap.get(rootVal);
    }
}
```

---

### Python

```python
from typing import List, Optional

# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val:int):
        self.val = val
        self.left: Optional[TreeNode] = None
        self.right: Optional[TreeNode] = None

class Solution:
    def createBinaryTree(self, descriptions: List[List[int]]) -> Optional[TreeNode]:
        node_map = {}
        children = set()

        for parent, child, is_left in descriptions:
            # Create nodes lazily
            if parent not in node_map:
                node_map[parent] = TreeNode(parent)
            if child not in node_map:
                node_map[child] = TreeNode(child)

            # Attach child
            if is_left == 1:
                node_map[parent].left = node_map[child]
            else:
                node_map[parent].right = node_map[child]

            children.add(child)

        # Find root (not a child)
        root_val = next(val for val in node_map if val not in children)
        return node_map[root_val]
```

---

### C++

```cpp
#include <unordered_map>
#include <unordered_set>
#include <vector>

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    TreeNode* createBinaryTree(const std::vector<std::vector<int>>& descriptions) {
        std::unordered_map<int, TreeNode*> nodes;
        std::unordered_set<int> children;

        for (const auto &d : descriptions) {
            int parent = d[0], child = d[1], isLeft = d[2];

            // Create nodes on demand
            if (!nodes.count(parent)) nodes[parent] = new TreeNode(parent);
            if (!nodes.count(child))   nodes[child]   = new TreeNode(child);

            // Attach
            if (isLeft)
                nodes[parent]->left = nodes[child];
            else
                nodes[parent]->right = nodes[child];

            children.insert(child);
        }

        // Root is the node that never appears as a child
        int rootVal = -1;
        for (const auto &kv : nodes)
            if (!children.count(kv.first)) {
                rootVal = kv.first;
                break;
            }

        return nodes[rootVal];
    }
};
```

> **Tip** – Always remember to delete the nodes when you’re done (or use smart pointers in C++17+).  
> LeetCode’s test harness handles it, but real projects should manage memory properly.

---

## 2️⃣  Blog Article – “The Good, The Bad, & The Ugly of Building a Binary Tree from Descriptions”

> **SEO Keywords**: LeetCode 2196, create binary tree from descriptions, interview prep, Java binary tree, Python binary tree, C++ binary tree, data structures, algorithm interview, data structure problems

---

### 📚 Introduction

Binary trees are a staple of computer science interviews.  
LeetCode problem **2196 – *Create Binary Tree From Descriptions*** asks you to reconstruct a tree when you’re only given a set of parent‑child relationships.  

It’s a deceptively simple problem that tests:

- **Mapping between values and objects** (`HashMap / unordered_map`).
- **Set operations** to determine the root.
- **Graph traversal logic** (even if implicit).
- **Clean O(n) solutions** vs. naive O(n²) approaches.

In this post, I’ll walk you through the **good** (what works), the **bad** (common pitfalls), and the **ugly** (edge‑cases you might miss). The goal? Turn this LeetCode challenge into a showcase of your problem‑solving chops on your résumé.

---

### ✅ Problem Recap

You’re given `descriptions`, a list of `[parent, child, isLeft]` triples.  
`isLeft = 1` → child is the left child, otherwise it’s the right child.  
All node values are unique, and the descriptions describe a *valid* binary tree.

Return the **root** of the reconstructed tree.

---

### 📈 The Good – Clean O(n) Algorithm

1. **One Pass Construction**  
   Iterate once over `descriptions`:
   - For each triple, fetch or create the `TreeNode` for `parent` and `child`.  
   - Wire the `child` to `parent`'s `left` or `right`.  
   - Record every child value in a `Set`.

2. **Root Identification**  
   The root is the only value never seen as a child.  
   After the loop, iterate over the keys of the node map and pick the one absent from the child set.

3. **Complexity**  
   - **Time**: `O(n)` – each description processed once.  
   - **Space**: `O(n)` – for node map and child set.

This solution is the gold‑standard interview answer. It shows familiarity with hash tables and set theory.

---

### ⚠️ The Bad – Common Pitfalls

| Pitfall | Why it’s a problem | Fix |
|---------|-------------------|-----|
| **Re‑creating nodes** | Accidentally creating duplicate `TreeNode` objects for the same value. | Use `computeIfAbsent` (Java), `dict.setdefault` (Python), or `unordered_map::operator[]` in C++ to lazily instantiate. |
| **O(n²) naive linking** | For each description searching the whole node list for the parent. | Use a map to directly locate parents in constant time. |
| **Assuming first element is root** | The input isn’t guaranteed to be sorted. | Identify the root via the child set. |
| **Not handling missing roots** | If you forget to search for the root, you may return `null`. | Explicitly find and return the root after construction. |
| **Memory leaks (C++)** | Dynamically allocating nodes without freeing them. | Use smart pointers (`std::unique_ptr`) or manual cleanup. |

---

### 🦟 The Ugly – Edge Cases & Tricky Scenarios

| Scenario | Why it matters | Mitigation |
|----------|----------------|------------|
| **Large value ranges** (`1 ≤ value ≤ 10⁵`) | Using a dense array would waste space. | Stick to hash maps/sets. |
| **Minimal input** (`n = 1`) | Only one node; root is also the child. | Works naturally with the algorithm: root set will contain the parent but not the child. |
| **All nodes on one side** (e.g., a degenerate right‑heavy tree) | No left children at all. | `isLeft` flag still applied; no special case needed. |
| **Duplicated descriptions** | The problem guarantees validity, but a faulty test might contain duplicates. | HashMap + set will dedupe nodes automatically. |
| **Large depth recursion** | If you later add a DFS to serialize the tree, recursion depth could hit the stack limit. | Use iterative traversal or increase recursion stack. |

---

### 🧠 Why This Matters for Your Job Hunt

- **Data‑structure fluency** – Demonstrates mastery of hash maps, sets, and tree manipulation.
- **Algorithmic efficiency** – Shows you can deliver O(n) solutions where O(n²) would be disastrous.
- **Attention to detail** – Handling edge cases, memory management, and clean code are prized by hiring managers.
- **Interview readiness** – Problems like this appear in many technical interviews; you’ll be comfortable presenting your thought process.

---

### 📌 Quick Reference – One‑Line Pseudocode

```text
for (p, c, l) in descriptions:
    nodeMap[p] = nodeMap.getOrCreate(p)
    nodeMap[c] = nodeMap.getOrCreate(c)
    if l: nodeMap[p].left  = nodeMap[c]
    else: nodeMap[p].right = nodeMap[c]
    childSet.add(c)

rootVal = (nodeMap.keys - childSet).single
return nodeMap[rootVal]
```

---

### 🚀 Takeaway

> *“A good solution is a solution that’s efficient, readable, and accounts for all edge cases. The bad solution is one that over‑complicates or misuses data structures. The ugly one is what slips through when you forget to think about memory, recursion limits, or the simplest root‑finding trick.”*

Master this problem, polish your code (Java, Python, C++), and you’ll have a ready‑to‑show example of clean algorithmic thinking that can land you that next interview call.

Happy coding, and good luck on the job hunt! 🚀

---