---
title: LeetCode 1666. Change the Root of a Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1666 – *Change the Root of a Binary Tree*  
**Languages** – Java, Python, C++  
**Target** – 100 % correct solution, clear pointer manipulation, interview‑ready

---

### Table of Contents
1. [Problem Statement](#problem-statement)
2. [High‑Level Idea](#high-level-idea)
3. [Step‑by‑Step Algorithm](#step-by-step-algorithm)
4. [Implementations](#implementations)  
   - Java  
   - Python  
   - C++
5. [Complexity Analysis](#complexity-analysis)
6. [Common Pitfalls & Edge Cases](#common-pitfalls-and-edge-cases)
7. [The Good, The Bad, The Ugly](#the-good-the-bad-the-ugly)
8. [How to Talk About It in an Interview](#how-to-talk-about-it-in-an-interview)
9. [SEO & Job‑Search Tips](#seo-and-job-search-tips)
10. [Reference](#reference)

---

## 📝 Problem Statement

> **Change the Root of a Binary Tree**  
> You are given the root of a binary tree and a leaf node `leaf`.  
> Reroot the tree so that the `leaf` becomes the new root.  
> For every node `cur` on the path from `leaf` up to (but excluding) the original root, perform:
> 1. If `cur.left` exists → it becomes `cur.right`.  
> 2. `cur.parent` becomes `cur.left`.  
>  
> After rerooting, all `parent` pointers must be updated correctly.  
> Return the new root.

**Constraints**

- 2 ≤ nodes ≤ 100  
- `Node.val` are unique  
- `leaf` exists in the tree  

---

## ⚡ High‑Level Idea

Treat the tree as a *linked list* where the `parent` pointer is the “next” pointer.  
Starting from the `leaf`, walk up towards the original root, swapping the current node’s children and parent in place.  
Because we walk **once** along the unique path, the algorithm is linear in the length of that path – at most 100 nodes.

Key trick:  
When we re‑wire a node we must **clear** any child that points back to the new parent (otherwise we create a cycle).  
After the walk finishes, the leaf becomes the root and the old root is now a leaf.

---

## 📈 Step‑by‑Step Algorithm

1. **Store original root** (`origRoot`) so we know when we’ve reached the end.
2. Call a recursive helper `flip(cur, newParent)` where:
   * `cur` is the current node on the path  
   * `newParent` is the node that will become `cur.parent`
3. Inside `flip`:
   * `oldParent ← cur.parent`
   * `cur.parent ← newParent`
   * If `cur.left == newParent` → set `cur.left = null`  
     If `cur.right == newParent` → set `cur.right = null`
   * **Base case** – if `cur == origRoot`, return `cur` (new root found)
   * If `cur.left` exists, move it to `cur.right` (`cur.right = cur.left`)
   * Recurse: `cur.left ← flip(oldParent, cur)`
4. Return the node returned by the first call – this is the new root.

The recursion unwinds, building the rerooted tree from the bottom up.

---

## 💻 Implementations

> **Important** – All solutions share the same logic; only syntax changes.

### 1. Java

```java
// Definition for a binary tree node with parent pointer.
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node parent;
    public Node(int val) { this.val = val; }
}

class Solution {
    private Node originalRoot;

    public Node flipBinaryTree(Node root, Node leaf) {
        originalRoot = root;
        return flip(leaf, null);          // leaf becomes new root → new parent = null
    }

    private Node flip(Node cur, Node newParent) {
        Node oldParent = cur.parent;
        cur.parent = newParent;

        // Remove the edge pointing to the new parent to avoid cycles
        if (cur.left == newParent) cur.left = null;
        if (cur.right == newParent) cur.right = null;

        // When we reach the original root, we are done
        if (cur == originalRoot) return cur;

        // If there is a left child, it becomes the new right child
        if (cur.left != null) cur.right = cur.left;

        // Recurse on the old parent; it will become the new left child
        cur.left = flip(oldParent, cur);
        return cur;
    }
}
```

### 2. Python

```python
# Definition for a binary tree node with parent pointer.
class Node:
    def __init__(self, val: int):
        self.val = val
        self.left: 'Node | None' = None
        self.right: 'Node | None' = None
        self.parent: 'Node | None' = None

def flip_binary_tree(root: Node, leaf: Node) -> Node:
    """
    Reroots the binary tree so that `leaf` becomes the new root.
    """
    original_root = root

    def flip(cur: Node, new_parent: Node | None) -> Node:
        old_parent = cur.parent
        cur.parent = new_parent

        # Remove back‑pointer to new parent
        if cur.left is new_parent: cur.left = None
        if cur.right is new_parent: cur.right = None

        if cur is original_root:   # reached the old root
            return cur

        # Left child becomes right child
        if cur.left:
            cur.right = cur.left

        # Recurse: old parent becomes new left child
        cur.left = flip(old_parent, cur)
        return cur

    return flip(leaf, None)
```

### 3. C++

```cpp
/* Definition for a binary tree node with parent pointer. */
struct Node {
    int val;
    Node *left = nullptr;
    Node *right = nullptr;
    Node *parent = nullptr;
    Node(int _val) : val(_val) {}
};

class Solution {
public:
    Node* flipBinaryTree(Node* root, Node* leaf) {
        originalRoot = root;
        return flip(leaf, nullptr);      // leaf becomes new root
    }

private:
    Node* originalRoot;

    Node* flip(Node* cur, Node* newParent) {
        Node* oldParent = cur->parent;
        cur->parent = newParent;

        if (cur->left == newParent) cur->left = nullptr;
        if (cur->right == newParent) cur->right = nullptr;

        if (cur == originalRoot) return cur;

        if (cur->left) cur->right = cur->left;   // left → right

        cur->left = flip(oldParent, cur);        // recurse on old parent
        return cur;
    }
};
```

All three solutions run in **O(h)** time where *h* is the depth of `leaf` (≤ 100) and use **O(h)** extra space for recursion stack.

---

## ⏱ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Rerooting | **O(h)** | **O(h)** (recursive stack) |
| Where *h* ≤ 100 (max tree height) |

Both the time and space budgets are comfortably within the LeetCode limits.

---

## ⚠️ Common Pitfalls & Edge Cases

| Pitfall | How to Avoid |
|---------|--------------|
| Forgetting to clear a child that points back to the new parent | Explicit checks `if (cur.left == newParent)` etc. |
| Mis‑updating the `parent` pointer of the old root | Store `originalRoot` before the first swap and stop recursion when `cur == originalRoot`. |
| Recursive stack overflow (very deep trees) | The problem constraints cap nodes to 100, so recursion is safe. For larger trees, an iterative stack could be used. |
| Returning the wrong node (the old root instead of the leaf) | Return the node returned by the first recursive call – that node is the new root. |

---

## 🔍 The Good, The Bad, The Ugly

| Aspect | What’s Good | What’s Bad | What’s Ugly |
|--------|-------------|------------|-------------|
| **Algorithmic elegance** | One pass, O(h) time, clean pointer juggling. | Recursive depth may feel fragile to interviewers. | Requires a mental model of “parent as next pointer”; easy to miss subtle cycle‑removal. |
| **Readability** | Straightforward helper; each line has a clear purpose. | Too many pointer checks can clutter the code. | Over‑engineering comments or overly generic variable names can hide logic. |
| **Testing** | Small trees, leaf at root, leaf at leaf. | Edge case: leaf already the root (should return root). | Mis‑handling when the original root has a left child that should become its right child after rerooting. |
| **Interview talk‑track** | Discuss parent as “next” pointer, emphasize cycle prevention. | Might be accused of over‑optimization due to recursion. | “Why did you choose recursion over iteration?” – be ready with stack‑based alternative. |

---

## 🗣️ How to Talk About It in an Interview

1. **Restate the problem**: Emphasize that the path from leaf to root is unique – we can process it linearly.
2. **Explain the parent‑as‑next analogy**: This turns a tree into a singly‑linked list when walking upward.
3. **Show the pointer manipulation**:  
   * `oldParent ← node.parent`  
   * `node.parent ← newParent`  
   * Clear back‑edges to avoid cycles.  
   * Rewire children as described.
4. **Discuss complexity**: O(h) time, O(h) space. Mention constraints guarantee safety.
5. **Mention edge‑case handling**: Stop when reaching the original root; ensure new root has no parent.

---

## 🌐 SEO & Job‑Search Tips

- **Use keywords**: *“LeetCode binary tree reroot”, “flip binary tree”, “parent pointer tree interview”, “O(h) reroot algorithm”*.
- **LinkedIn headline**: “Data‑structure engineer with strong tree‑pointer manipulation skills | LeetCode hard problems”.
- **Portfolio**: Add this problem to a “Tree” category on your GitHub repo; include a README explaining the algorithm.
- **Blog post**: Write a short article titled “Rerooting a Binary Tree in Linear Time – A Parent‑as‑Next Pointer Approach”.
- **Interview preparation**: List “Binary Tree – Rerooting” as a key topic in your prep notes.  
- **Resume bullet**: “Implemented an O(h) solution to reroot a binary tree with parent pointers, achieving optimal time and space complexity”.

---

## 📚 Reference

- LeetCode problem “Change the Root of a Binary Tree” (ID: 2101)  
- Official editorial: *Reroot a binary tree by treating parent pointers as next pointers*  

---

### 👋 Wrap‑Up

- One clear, linear pointer‑rewiring pass.  
- Three language‑agnostic implementations shown.  
- Ready to discuss complexity, pitfalls, and interview talk‑track.  

Good luck slashing those interview questions! 🚀  

--- 

*If you’d like a deeper dive into an iterative version or help with a unit‑test suite, feel free to ping me!*