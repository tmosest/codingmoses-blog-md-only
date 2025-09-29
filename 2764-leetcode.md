---
title: LeetCode 2764. Is Array a Preorder of Some Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ 3‑Way Solution for “Is Array a Preorder of Some Binary Tree”

Below you’ll find a **complete, production‑ready implementation** in **Java, Python, and C++**.  
Each version follows the same stack‑based idea, runs in **O(N)** time and **O(N)** space, and is ready to paste into any LeetCode submission or interview white‑board.

---

### 📌 Problem Recap

> **Given** a 0‑indexed `nodes` list where each element is `[id, parentId]`  
> **Determine** if this order could be the **pre‑order traversal** of *some* binary tree.

> *Pre‑order*: visit node → preorder(left) → preorder(right)

> The input guarantees that the pairs form a valid binary tree (one root, no cycles).

---

## ✅ The Core Idea

We simulate the preorder traversal while walking through the given array:

1. **Start** with the root (the first pair – its `parentId` must be `-1`).
2. Maintain a **stack** of *active ancestors* – those nodes that still have unexplored children.
3. For each next node `(id, parent)`:
   * Keep **popping** from the stack until the top equals `parent`.
   * If the stack becomes empty before we find `parent`, the sequence **cannot** be a preorder.
   * Push the current `id` onto the stack – it is now an active ancestor.

Because preorder visits a node before all of its descendants, the current node’s parent must always be the **closest** ancestor that hasn't finished its left subtree yet – exactly the top of the stack.

---

## 📄 Code

### 1️⃣ Java

```java
import java.util.*;

class Solution {
    public boolean isPreorder(List<List<Integer>> nodes) {
        int n = nodes.size();
        if (n == 0) return true;          // trivial case

        // The first node must be the root
        if (nodes.get(0).get(1) != -1) return false;

        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(nodes.get(0).get(0));   // push root id

        for (int i = 1; i < n; i++) {
            int id = nodes.get(i).get(0);
            int parent = nodes.get(i).get(1);

            // Move up the stack until we find the parent
            while (!stack.isEmpty() && stack.peek() != parent) {
                stack.pop();
            }

            if (stack.isEmpty()) return false;  // parent not found
            stack.push(id);
        }
        return true;
    }
}
```

### 2️⃣ Python

```python
from typing import List

class Solution:
    def isPreorder(self, nodes: List[List[int]]) -> bool:
        if not nodes:
            return True

        # Root must have parent -1
        if nodes[0][1] != -1:
            return False

        stack = [nodes[0][0]]            # start with root id

        for id, parent in nodes[1:]:
            # pop until parent is on top
            while stack and stack[-1] != parent:
                stack.pop()

            if not stack:               # parent not found
                return False

            stack.append(id)            # current node becomes active ancestor

        return True
```

### 3️⃣ C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool isPreorder(const vector<vector<int>>& nodes) {
        int n = nodes.size();
        if (n == 0) return true;

        // First node must be the root
        if (nodes[0][1] != -1) return false;

        vector<int> stack;
        stack.push_back(nodes[0][0]);   // push root id

        for (int i = 1; i < n; ++i) {
            int id = nodes[i][0];
            int parent = nodes[i][1];

            while (!stack.empty() && stack.back() != parent) {
                stack.pop_back();
            }

            if (stack.empty()) return false; // parent not found

            stack.push_back(id);
        }
        return true;
    }
};
```

> **Why it works** – The stack always represents the current “path” from the root down to the deepest node that still needs its right child processed.  
> When the next node is visited, its parent must be the most recent ancestor still waiting for a child → the stack top.  
> If we cannot find that parent, the order violates preorder properties.

---

## 📚 Blog Post – “The Good, the Bad, and the Ugly of Pre‑Order Validation”

> **Meta description**:  
> Learn the stack‑based solution to LeetCode 2764 “Is Array a Preorder of Some Binary Tree”. Dive into the algorithm, edge‑cases, alternatives, and why this approach is a job‑interview win.

---

### 1️⃣ Introduction

During an interview, the interviewer may ask you to **validate** if a given list of `(node, parent)` pairs could be the pre‑order traversal of *any* binary tree.  
It sounds simple, but the trick lies in handling parent relationships efficiently without building the whole tree.

> **Keywords**: LeetCode 2764, preorder traversal, binary tree, stack algorithm, interview prep.

---

### 2️⃣ Problem Recap

We’re given `nodes = [[id, parentId], …]` – a 0‑indexed list that already represents a *valid* binary tree (unique root, no cycles).  
The task: **Return `true` iff the order is a valid preorder traversal.**

- Pre‑order: node → left subtree → right subtree.
- Root has `parentId = -1`.

---

### 3️⃣ The “Good” – Why the Stack is Perfect

| Benefit | Explanation |
|---------|-------------|
| **O(N) Time** | Each node is processed once; stack pops at most once per node. |
| **O(N) Space** | Stack holds the current ancestor chain; worst‑case depth N (degenerate tree). |
| **No Tree Construction** | We don’t need adjacency lists or node objects – just indices. |
| **Clear Intuition** | In preorder you always “step back” to the nearest ancestor that still needs a child. The stack simulates that back‑tracking. |

---

### 4️⃣ The “Bad” – Edge Cases & Pitfalls

| Issue | Fix |
|-------|-----|
| **Multiple roots** | The first element’s `parentId` must be `-1`. If not, return `false`. |
| **Missing parent in stack** | While popping, if the stack empties before matching the parent → invalid order. |
| **Duplicate IDs** | Problem guarantees uniqueness, so no extra check needed. |
| **Large inputs (1e5)** | Use a fast stack (ArrayDeque in Java, list in Python, vector in C++). Avoid recursion to stay within stack limits. |

---

### 5️⃣ The “Ugly” – Why Simpler DFS Might Mislead

A naïve DFS that rebuilds the tree and then simulates preorder can be:

- **O(N²)** in worst‑case if adjacency lists are built by scanning all nodes for each parent.
- **Memory‑heavy** – storing the whole tree structure when you only need the traversal order.

This “ugly” approach is tempting in an interview but wastes valuable time and resources.

---

### 6️⃣ Alternative Strategies

| Approach | Complexity | Pros | Cons |
|----------|------------|------|------|
| **Recursive Simulation** | O(N) time, O(N) space (recursion depth) | Very intuitive | Risk of stack overflow for deep trees |
| **Hash‑Map Parent Map + Stack** | O(N) time, O(N) space | Avoids list indices | Still needs the stack |
| **Parent‑Index Mapping + Two‑Pointer** | O(N) time, O(1) extra | Slightly less space | Harder to reason, less robust |

> In most interviews, the **stack method** is the sweet spot.

---

### 7️⃣ SEO‑Ready Takeaway

- **Problem ID**: 2764 – “Is Array a Preorder of Some Binary Tree”
- **Key Terms**: `preorder traversal`, `binary tree validation`, `stack algorithm`, `LeetCode solutions`, `interview prep`
- **Title**: “Validate a Pre‑Order Traversal: Stack‑Based Solution for LeetCode 2764”

---

### 8️⃣ Conclusion

The stack‑based solution is **clean, fast, and scalable**. It mirrors the natural preorder traversal mechanics and sidesteps the overhead of tree construction. Whether you’re coding on LeetCode or writing on a whiteboard, this pattern is a staple in your algorithm toolkit.

> **Pro Tip**: In interviews, start by sketching the stack logic, then write the code. Keep the interviewer informed – “I’ll push the root onto the stack, then for each node I’ll pop until I find its parent…”.

---

### 9️⃣ Call to Action

> Try implementing the solution in **Java, Python, or C++** (see code above).  
> Then, create a **GitHub repo** and add a README with the explanation.  
> Share it on LinkedIn with the tags **#Algorithm #LeetCode #InterviewPrep** and watch recruiters notice!

---

## 🎉 Final Words

- **Good**: Fast, memory‑efficient, clear stack logic.
- **Bad**: Must handle the root and missing parent cases explicitly.
- **Ugly**: Rebuilding the tree is overkill and risky.

With this knowledge, you’re not just solving a problem—you’re mastering a pattern that’s useful across many binary‑tree interview questions. Happy coding, and may your next interview be a *pre‑order* of success!