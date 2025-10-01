---
title: LeetCode 510. Inorder Successor in BST II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 510. Inorder Successor in BST II  
## (Java | Python | C++) – A Complete, Interview‑Ready Solution

> **Leetcode 510** – *Inorder Successor in BST II*  
> **Difficulty:** Medium  
> **Tags:** Binary Search Tree, Tree Traversal, Parent Pointer  

---

## Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Intuitive Insight](#intuitive-insight)  
3. [Full Solutions](#full-solutions)  
   * Java  
   * Python  
   * C++  
4. [Complexity Analysis](#complexity-analysis)  
5. [Follow‑Up: No Value Look‑ups](#follow-up-no-value-look‑ups)  
6. [The Good, The Bad, The Ugly](#the-good-the-bad-the-ugly)  
7. [Interview Tips & SEO‑Optimized Keywords](#interview-tips)  
8. [Conclusion](#conclusion)  

---

## Problem Overview <a name="problem-overview"></a>

You are given a **node** of a binary search tree (BST).  
Each node contains a pointer to its parent.  
Return the **in‑order successor** of the given node.  
If the node has no successor, return `null`.

The *in‑order successor* is the node with the smallest key that is **greater** than the given node’s value.  
The tree contains **unique** values and the node pointer is guaranteed to belong to the tree.

**Example 1**

```
      2
     / \
    1   3
```

`node = 1 → successor = 2`

**Example 2**

```
        5
       / \
      3   6
     / \
    2   4
   /
  1
```

`node = 6 → successor = null`

---

## Intuitive Insight <a name="intuitive-insight"></a>

The key to an optimal solution is to look **only** at the structure around the node:

| Case | What to do? |
|------|--------------|
| **Right subtree exists** | The successor is the leftmost node in that right subtree. |
| **No right subtree** | Walk up the tree via the `parent` pointers until you reach a node that is a left child of its parent. The parent of that node is the successor. |

No global tree traversal or value comparisons are required beyond the local pointers.

---

## Full Solutions <a name="full-solutions"></a>

> Each solution below is ready to drop into Leetcode’s editor.

---

### 1️⃣ Java

```java
/**
 * Definition for a binary tree node with parent pointer.
 */
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node parent;

    public Node(int val) {
        this.val = val;
    }
}

public class Solution {
    public Node inorderSuccessor(Node x) {
        // Case 1: Right subtree exists – go leftmost in it.
        if (x.right != null) {
            Node cur = x.right;
            while (cur.left != null) {
                cur = cur.left;
            }
            return cur;
        }

        // Case 2: No right subtree – climb up until we come from a left child.
        Node cur = x;
        while (cur.parent != null && cur == cur.parent.right) {
            cur = cur.parent;
        }
        return cur.parent;          // May be null if x is the largest node.
    }
}
```

> **Why it’s clean** – Two straightforward loops; no recursion or extra data structures.

---

### 2️⃣ Python

```python
class Node:
    def __init__(self, val: int):
        self.val = val
        self.left = None
        self.right = None
        self.parent = None

class Solution:
    def inorderSuccessor(self, x: Node) -> Node:
        # Case 1: Right child exists – find leftmost in that subtree
        if x.right:
            cur = x.right
            while cur.left:
                cur = cur.left
            return cur

        # Case 2: No right child – climb up to find a parent where x is a left child
        cur = x
        while cur.parent and cur == cur.parent.right:
            cur = cur.parent
        return cur.parent   # Might be None if x is the maximum
```

> **Pythonic** – Uses `while` loops; the `return cur.parent` line may return `None`, which is exactly what Leetcode expects for “no successor”.

---

### 3️⃣ C++

```cpp
/* Definition for a binary tree node with parent pointer. */
struct Node {
    int val;
    Node* left;
    Node* right;
    Node* parent;
    Node(int x) : val(x), left(nullptr), right(nullptr), parent(nullptr) {}
};

class Solution {
public:
    Node* inorderSuccessor(Node* x) {
        // Case 1: Right subtree exists
        if (x->right) {
            Node* cur = x->right;
            while (cur->left) cur = cur->left;
            return cur;
        }

        // Case 2: No right subtree – climb up
        Node* cur = x;
        while (cur->parent && cur == cur->parent->right)
            cur = cur->parent;
        return cur->parent;    // nullptr if x is the greatest
    }
};
```

> **C++** – Straightforward pointer logic; no `nullptr` checks inside loops.

---

## Complexity Analysis <a name="complexity-analysis"></a>

| Operation | Time | Space |
|-----------|------|-------|
| Finding successor | **O(h)** – `h` is the height of the tree (≤ `log N` in a balanced tree) | **O(1)** – only a few pointers |
| Extra memory | – | – |

The algorithm never traverses more nodes than the path to the successor, so it’s optimal.

---

## Follow‑Up: No Value Look‑ups <a name="follow-up-no-value-look‑ups"></a>

The original Leetcode follow‑up asks you to solve it *without* inspecting the node’s `val`.  
The solutions above **already** satisfy that: we only use the structure (`left`, `right`, `parent`) and never compare values.  
So, just copy one of the snippets above and you’re good to go!

---

## The Good, The Bad, The Ugly <a name="the-good-the-bad-the-ugly"></a>

### Good  
| Aspect | Why it’s a plus |
|--------|-----------------|
| **O(h) time** | Even for a skewed tree (worst case) you only touch nodes along a single path. |
| **Constant space** | No recursion stack or auxiliary containers – perfect for interview constraints. |
| **Parent pointer usage** | Leverages the given structure to avoid root‑based traversal. |
| **Clear separation of cases** | Two distinct logical blocks: right‑subtree vs. ancestor climb. |

### Bad  
| Aspect | Why it’s a downside |
|--------|---------------------|
| **Assumes `parent` is correctly set** | If the tree’s parent pointers are broken, the algorithm will misbehave. |
| **Not applicable to plain BST without parent pointers** | Requires the extra field; not a general solution for all BST problems. |
| **Potential off‑by‑one** | Forgetting the `==` check for `cur == cur.parent.right` can lead to infinite loops. |

### Ugly  
| Aspect | Why it’s ugly |
|--------|---------------|
| **Edge‑case confusion** | The `while` that climbs up can be misunderstood: you must climb until you *are* a left child. |
| **Return `null` vs. `None` vs. `nullptr`** | In interview code, mixing languages can lead to wrong expectations if you forget the language‑specific null value. |
| **Lack of comments in minimal solutions** | A one‑liner may look slick, but a junior candidate might not explain the logic to the interviewer. |

> **Bottom line:** The algorithm is elegant; just be careful to explain the two cases and why the parent pointer works.

---

## Interview Tips & SEO‑Optimized Keywords <a name="interview-tips"></a>

| Tip | How to phrase it |
|-----|------------------|
| **Start with the two cases** | “If the node has a right child… else…”. |
| **Explain the leftmost search** | “We’re looking for the smallest node > current, which is the leftmost in the right subtree.” |
| **Demonstrate parent climbing** | “While the current node is a right child of its parent, we keep moving up.” |
| **Mention time/space** | “O(h) time, O(1) space – ideal for interview.” |
| **Handle null correctly** | “Return the parent, which may be null if the node is the largest.” |

**SEO Keywords**  
- “Inorder Successor in BST II solution”  
- “Leetcode 510 solution Java Python C++”  
- “BST inorder successor interview question”  
- “BST parent pointer algorithm”  
- “Binary Search Tree traversal without root”  

Include these keywords naturally in headings, meta description, and first paragraph to boost searchability for job seekers tackling Leetcode.

---

## Conclusion <a name="conclusion"></a>

The **Inorder Successor in BST II** problem tests two key skills:

1. **Tree traversal with constraints** – using only node pointers.  
2. **Clear algorithmic thinking** – distinguishing right‑subtree vs. ancestor climbing.

The three solutions above demonstrate how to apply a single, clean idea across **Java, Python, and C++**.  
Remember: always articulate the logic, handle edge cases, and keep the complexity in mind.  

Good luck with your coding interviews—and may those `null` return values become the “null” of your job search!  

---