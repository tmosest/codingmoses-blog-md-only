---
title: LeetCode 1506. Find Root of N-Ary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Blog Post – “Find the Root of an N‑ary Tree: The Good, The Bad, and The Ugly”  

> **SEO Keywords**: Find Root of N‑ary Tree, LeetCode 1506, N‑ary tree, Interview problem, Java, Python, C++, algorithm, hashset, XOR, O(n) solution, constant space, job interview  

---

### 📝 Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Understanding the Input & Output](#input-output)  
3. [Common Pitfalls (The Ugly)](#pitfalls)  
4. [Approach #1 – HashSet (The Good)](#hashset)  
5. [Approach #2 – XOR (The Ugly, but Elegant)](#xor)  
6. [Time & Space Complexity](#complexity)  
7. [Edge Cases & Validation](#edgecases)  
8. [Code Walkthrough (Java, Python, C++)](#code)  
9. [Interview‑Ready Tips](#tips)  
10. [Wrap‑Up](#wrapup)  

---

## 1. Problem Overview <a name="problem-overview"></a>

> **LeetCode 1506 – Find Root of N‑ary Tree**  
> **Difficulty**: Medium

You are given *all* nodes of an N‑ary tree in an arbitrary order.  
Each node has a unique integer value and a list of children.  
Your task: **Return the root node** (the only node that never appears as a child).

> **Follow‑up**: Can you solve it in **constant auxiliary space** and linear time?

---

## 2. Understanding the Input & Output <a name="input-output"></a>

The driver creates a tree from a *level‑order serialization* (children separated by `null`).  
It then shuffles the `Node` objects and passes the resulting array/list to your `findRoot` function.

**What you receive**:  
`List<Node> tree` (Java) / `List[Node] tree` (Python) / `vector<Node*> tree` (C++)

**What you must return**:  
The *root* `Node` object itself.

*If your function returns the correct root, the driver serializes that tree and compares it with the original input.*

---

## 3. Common Pitfalls (The Ugly) <a name="pitfalls"></a>

| Pitfall | Why it hurts | How to avoid |
|---------|--------------|--------------|
| **Assuming the first element is the root** | The input order is random. | Iterate over all nodes. |
| **Using `Node.equals` incorrectly** | `equals` might be reference‑based; unique values are what matter. | Compare using node references or store node objects in a set. |
| **O(n²) solutions** | Double‑loop over children → TLE on 5 × 10⁴ nodes. | Use a hash set or XOR trick (O(n)). |
| **Using extra data structures unnecessarily** | Increases memory footprint, contradicts the follow‑up. | Prefer the XOR trick for O(1) extra space. |
| **Ignoring null children** | Null separators in serialization only, not in `children` lists. | `children` list never contains `null`. |

---

## 4. Approach #1 – HashSet (The Good) <a name="hashset"></a>

1. **Collect all children** in a `HashSet<Node>`.  
2. **Scan again** – the node that is *not* in the set is the root.

**Why it’s good**  
- Simple to reason about.  
- Works for any tree shape (binary, ternary, etc.).  
- Linear time, O(n) extra space (acceptable for the original problem).

```java
public Node findRoot(List<Node> tree) {
    Set<Node> children = new HashSet<>();
    for (Node n : tree) {
        children.addAll(n.children);
    }
    for (Node n : tree) {
        if (!children.contains(n)) return n;
    }
    return null; // should never happen
}
```

---

## 5. Approach #2 – XOR (The Ugly, but Elegant) <a name="xor"></a>

Observation:  
All node values are unique integers.  
If we XOR **every node value** together and XOR **every child value** together, the pairs cancel out, leaving only the root’s value.

**Implementation**  
- Use `long` to avoid overflow (values up to 5 × 10⁴).  
- Perform XOR while traversing the list once.

```java
public Node findRoot(List<Node> tree) {
    long xor = 0;
    for (Node n : tree) {
        xor ^= n.val;
        for (Node child : n.children) xor ^= child.val;
    }
    // Find node with matching val
    for (Node n : tree) {
        if (n.val == xor) return n;
    }
    return null;
}
```

**Why it’s “ugly”**  
- Relies on value uniqueness; if the problem changes, this breaks.  
- Less intuitive than a set, may raise interviewer's eyebrows.  
- Still O(n) time but O(1) auxiliary space – satisfies the follow‑up.

---

## 6. Time & Space Complexity <a name="complexity"></a>

| Approach | Time | Extra Space |
|----------|------|-------------|
| HashSet | **O(n)** (two passes) | **O(n)** (hash set) |
| XOR | **O(n)** (single pass) | **O(1)** |

Both satisfy the linear‑time requirement. The XOR trick is the only one that meets the constant‑space follow‑up.

---

## 7. Edge Cases & Validation <a name="edgecases"></a>

| Edge Case | Expected Behaviour |
|-----------|--------------------|
| Single node tree | Root is that node. |
| Deep linear tree (each node has one child) | XOR works; set contains n-1 children. |
| Wide shallow tree (root has many children) | No problems. |
| Duplicate values (not allowed by constraints) | Problem invalid; XOR fails. |
| Null children in input serialization | Ignored by the driver; children lists are never null. |

Always check that the root returned by your function serializes back to the original input.

---

## 8. Code Walkthrough (Java, Python, C++) <a name="code"></a>

### 8.1 Java (HashSet + XOR)

```java
import java.util.*;

class Node {
    public int val;
    public List<Node> children;
    public Node() { children = new ArrayList<>(); }
    public Node(int _val) { val = _val; children = new ArrayList<>(); }
    public Node(int _val, List<Node> _children) {
        val = _val; children = _children;
    }
}

class Solution {
    /* ------------- HashSet Approach ------------- */
    public Node findRootHashSet(List<Node> tree) {
        Set<Node> seen = new HashSet<>();
        for (Node n : tree) seen.addAll(n.children);
        for (Node n : tree) if (!seen.contains(n)) return n;
        return null;
    }

    /* ------------- XOR Approach ------------- */
    public Node findRootXOR(List<Node> tree) {
        long xor = 0;
        for (Node n : tree) {
            xor ^= n.val;
            for (Node child : n.children) xor ^= child.val;
        }
        for (Node n : tree) if (n.val == xor) return n;
        return null;
    }
}
```

### 8.2 Python (HashSet + XOR)

```python
from typing import List

class Node:
    def __init__(self, val: int = 0, children: List['Node'] = None):
        self.val = val
        self.children = children or []

class Solution:
    # HashSet
    def findRootHashSet(self, tree: List[Node]) -> Node:
        child_set = set()
        for node in tree:
            child_set.update(node.children)
        for node in tree:
            if node not in child_set:
                return node
        return None

    # XOR
    def findRootXOR(self, tree: List[Node]) -> Node:
        xor_val = 0
        for node in tree:
            xor_val ^= node.val
            for child in node.children:
                xor_val ^= child.val
        for node in tree:
            if node.val == xor_val:
                return node
        return None
```

### 8.3 C++ (HashSet + XOR)

```cpp
#include <vector>
#include <unordered_set>

struct Node {
    int val;
    std::vector<Node*> children;
    Node(int _val = 0) : val(_val) {}
};

struct NodeHash {
    size_t operator()(const Node* n) const noexcept {
        return std::hash<int>()(n->val);
    }
};

class Solution {
public:
    // HashSet
    Node* findRootHashSet(const std::vector<Node*>& tree) {
        std::unordered_set<Node*, NodeHash> seen;
        for (Node* node : tree)
            for (Node* child : node->children)
                seen.insert(child);
        for (Node* node : tree)
            if (seen.find(node) == seen.end())
                return node;
        return nullptr;
    }

    // XOR
    Node* findRootXOR(const std::vector<Node*>& tree) {
        long long xor_val = 0;
        for (Node* node : tree) {
            xor_val ^= node->val;
            for (Node* child : node->children)
                xor_val ^= child->val;
        }
        for (Node* node : tree)
            if (node->val == xor_val)
                return node;
        return nullptr;
    }
};
```

> **Tip** – If you’re writing for LeetCode, the `Solution` class is required; the driver will instantiate it and call `findRoot`.

---

## 9. Interview‑Ready Tips <a name="tips"></a>

1. **Clarify the constraints**  
   *Unique values?* *Maximum number of nodes?*  
   This will help you decide between hash‑set or XOR.

2. **Explain your reasoning**  
   Show that you understand *why* the root is the node that never appears as a child.

3. **State the trade‑offs**  
   - HashSet: *O(n)* space, easier to read.  
   - XOR: *O(1)* space, only works with integer uniqueness.

3. **Mention edge cases**  
   Ask if they expect you to handle single‑node or degenerate trees.

4. **Time‑complexity first**  
   If you can prove O(n) while making one pass, you’ve already satisfied the interviewer’s core requirement.

5. **Prepare for a “why XOR?” question**  
   *“Can you show the cancellation property in action?”*  
   Practice writing the XOR logic on a whiteboard.

6. **If you’re not sure about value uniqueness**, start with the hash‑set solution.  
   You can always add a comment about the follow‑up XOR trick.

---

## 10. Wrap‑Up <a name="wrapup"></a>

*The problem’s core is simple: identify the unique parent node.*  
- The **HashSet** solution is **robust** and easy to explain.  
- The **XOR** solution is **fascinating** and meets the constant‑space follow‑up.  

Both run in **O(n)** time, and the XOR trick gives you **O(1)** auxiliary space.  
Pick the one that matches your interview style, or show both to demonstrate depth.

**Happy coding!** 🚀

--- 

**Want to improve your LeetCode practice?**  
- Explore similar “find the unique element” problems: 213 `House Robber III`, 297 `Serialize and Deserialize Binary Tree`.  
- Study bit‑wise tricks – they often lead to elegant O(1) solutions.  

Good luck – you’ve got this!