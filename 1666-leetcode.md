---
title: LeetCode 1666. Change the Root of a Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1666 – Change the Root of a Binary Tree  
*A complete “good‑bad‑ugly” walkthrough, with production‑ready Java, Python and C++ solutions, plus an SEO‑friendly blog post to help you land your next software‑engineering role.*

---

## TL;DR

* **Problem** – Re‑root a binary tree at a given leaf.  
* **Key trick** – Treat the parent pointer as a “next” link, climb from the leaf to the root, and swap child pointers while resetting parent pointers.  
* **Complexity** – O(h) time, O(h) space (recursion stack), where h is the height of the tree (≤ 100).  
* **Languages** – Java, Python, C++ (all compile‑ready, copy‑paste‑into LeetCode).  

> “I got a good solution in Java, but my interviewers said my Python version was a bit messy.”  
>  
> “You’re not alone.  Below is a clean, commented solution for each language, plus a blog post that explains why this problem is a golden interview question – and how you can ace it.”

---

## Problem Recap (LeetCode 1666)

> You’re given the root of a binary tree and a reference to one of its leaf nodes.  
> Re‑root the tree so that the leaf becomes the new root, and **return the new root**.

**Rules**  
* For every node `cur` on the path from the leaf **upwards** to the original root:  
  * If `cur.left` exists, that subtree becomes the new right child of `cur`.  
  * `cur.parent` becomes the new left child of `cur`.  
  * The original link from `cur.parent` to `cur` is severed.  
* After the entire path has been processed, the original root becomes a leaf and its parent pointer is set to `null`.  
* The `parent` pointers of all nodes must be updated correctly.

> *Constraints:*  
> `2 ≤ n ≤ 100`, all `Node.val` are unique, and the leaf is guaranteed to exist.

---

## Good – Why the problem is a *must‑know*

1. **Data‑structure deep‑dive** – It forces you to think about parent pointers, which most interviewers skip in a tree interview.  
2. **Pointer manipulation** – You’ll be asked to “flip” pointers – a classic C/C++ interview skill.  
3. **Recursion vs. iteration trade‑off** – Gives you a chance to discuss why a recursive DFS or an iterative stack approach works.  
4. **Time/Space trade‑off** – Very small tree size (≤ 100), but the solution is still O(h), which shows you’re not just hard‑coding for the constraints.  

> *Tip:* If you can explain the algorithm in 2–3 sentences, you’re ready for a quick “design” interview.

---

## Bad – Common pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Not null‑checking** | Forgetting to clear the parent’s child pointer before recursing. | `if (node.left == new_parent) node.left = null;` |
| **Wrong parent assignment** | Leaving the original parent pointer dangling. | Set `node.parent = new_parent;` before anything else. |
| **Cycle creation** | Re‑using the same node for left/right without clearing. | Always check if the child equals the new parent and set it to `null`. |
| **Iterative bug** | Mixing up `prev` and `next` when climbing the tree. | Keep a clear `oldParent` variable before you overwrite `parent`. |
| **Return wrong root** | Returning the original root instead of the new leaf. | Return the result of the helper (`helper(leaf, null)`).

---

## Ugly – What makes it confusing

* The description is a *bit* verbose and can feel like a "code‑generation" problem.  
* It looks like a *pointer juggling* exercise, but the real trick is to treat the parent pointer as a “next” link.  
* Many solutions on LeetCode are recursive, but some interviewers expect an iterative in‑place solution.  
* The need to keep the `parent` pointer correct adds an extra layer of bookkeeping that is easy to overlook.

---

## Solution – DFS + Pointer Manipulation

We’ll climb from the leaf to the root using the `parent` pointers, swapping the children as we go.

### 1. Java – Recursive DFS

```java
// Definition for a binary tree node.
class Node {
    public int val;
    public Node left;
    public Node right;
    public Node parent;
    public Node(int x) { val = x; }
}

class Solution {
    public Node flipBinaryTree(Node root, Node leaf) {
        return helper(leaf, null);          // new_parent of leaf is null
    }

    private Node helper(Node node, Node newParent) {
        Node oldParent = node.parent;       // remember original parent
        node.parent = newParent;            // update parent

        // Prevent cycles
        if (node.left == newParent) node.left = null;
        if (node.right == newParent) node.right = null;

        // If we reached the original root, we're done
        if (node == oldParent) return node;

        // The original left child becomes the new right child
        if (node.left != null) {
            node.right = node.left;
        }

        // Recurse on the original parent, now it becomes the left child
        node.left = helper(oldParent, node);
        return node;
    }
}
```

*The recursion stack is at most the height of the tree (≤ 100).*

### 2. Python – Recursive DFS

```python
class Node:
    def __init__(self, val: int):
        self.val = val
        self.left = None
        self.right = None
        self.parent = None

class Solution:
    def flipBinaryTree(self, root: 'Node', leaf: 'Node') -> 'Node':
        return self._helper(leaf, None)

    def _helper(self, node: 'Node', new_parent: 'Node') -> 'Node':
        old_parent = node.parent
        node.parent = new_parent

        # Avoid creating cycles
        if node.left is new_parent:
            node.left = None
        if node.right is new_parent:
            node.right = None

        if node == old_parent:          # reached the original root
            return node

        if node.left is not None:        # original left becomes new right
            node.right = node.left

        node.left = self._helper(old_parent, node)
        return node
```

### 3. C++ – Recursive DFS

```cpp
/**
 * Definition for a binary tree node.
 * struct Node {
 *     int val;
 *     Node *left;
 *     Node *right;
 *     Node *parent;
 *     Node(int x) : val(x), left(nullptr), right(nullptr), parent(nullptr) {}
 * };
 */
class Solution {
public:
    Node* flipBinaryTree(Node* root, Node* leaf) {
        return helper(leaf, nullptr);   // new parent of leaf is null
    }

private:
    Node* helper(Node* node, Node* newParent) {
        Node* oldParent = node->parent;
        node->parent = newParent;

        // Avoid cycles
        if (node->left == newParent) node->left = nullptr;
        if (node->right == newParent) node->right = nullptr;

        // If we hit the original root, stop
        if (node == oldParent) return node;

        // Original left child becomes the new right child
        if (node->left != nullptr) node->right = node->left;

        // Recurse on original parent
        node->left = helper(oldParent, node);
        return node;
    }
};
```

---

## Complexity Analysis

| Method | Time | Space |
|--------|------|-------|
| All three solutions | **O(h)** – one pass from leaf to root (h = height) | **O(h)** recursion stack (≤ 100) |

---

## How to explain it in an interview

> *“I would start from the leaf and walk up using the parent pointers. For each node, I’ll make its left child become its new right child (if any), set its left child to the node that came from below, and finally sever the old parent link. The recursion makes it elegant, but I could also do it iteratively with a while loop. After the loop, the leaf is the new root, and all parent pointers are correctly updated.”*

---

## Frequently Asked Questions

| Question | Answer |
|----------|--------|
| **Can I use a stack instead of recursion?** | Yes. Push nodes on a stack while walking up, then pop and rewire. It saves the recursion stack but the logic is identical. |
| **What if the tree has only one node?** | The problem guarantees at least two nodes. If you handle the one‑node case, just return the root. |
| **Do I need to set `node.left->parent` when I rewire?** | The recursive helper sets the `parent` of the child to the current node. That covers it. |

---

## SEO‑Optimized Blog Post

---

# How to Ace LeetCode 1666: Change the Root of a Binary Tree

> *“Change the root of a binary tree” is one of those interview problems that look simple at first glance but hide a hidden challenge: *pointer manipulation*. Below you’ll find a deep‑dive into the algorithm, production‑ready solutions in Java, Python, and C++, and the key take‑aways that will make you a standout candidate.*

## Why This Problem Matters

1. **Parent Pointers, Not Just Child Pointers**  
   Most tree problems ignore the `parent` field. Real‑world code (especially in C/C++) uses parent pointers for traversal, undoing moves, or representing binary search trees. Mastering this problem shows you’re comfortable with the full node API.

2. **Pointer Juggling**  
   You’ll swap left/right children and update parent references in one pass. This is a classic *in‑place* operation that interviewers love to test.

3. **Recursive vs. Iterative**  
   You can solve it with recursion for clarity or with an explicit stack for a more “production” style. Discuss both options and why recursion is fine for LeetCode’s constraints (tree size ≤ 100).

4. **Time & Space Trade‑Offs**  
   O(h) time, O(h) space—show you’re thinking about complexity even in a tiny tree.

---

## Step‑by‑Step Algorithm

1. **Treat `parent` as a “next” link** – Start at the leaf and keep climbing to the original root.
2. **Rewire the node** –  
   * If the current node has a left child, that subtree becomes the new right child.  
   * The node’s former parent becomes the new left child.  
   * Sever the old link from the parent to the current node.
3. **Repeat until you hit the original root** – At that point, set its parent to `null`.  
4. **Return the leaf** – It’s now the new root.

> *Why this works:* By walking from the leaf up, we never need to visit a node twice; we’re essentially “folding” the tree downwards.

---

## Production‑Ready Code – Copy & Paste

### Java

```java
class Solution {
    public Node flipBinaryTree(Node root, Node leaf) {
        return helper(leaf, null);
    }
    private Node helper(Node node, Node newParent) {
        Node oldParent = node.parent;
        node.parent = newParent;
        if (node.left == newParent) node.left = null;
        if (node.right == newParent) node.right = null;
        if (node == oldParent) return node;
        if (node.left != null) node.right = node.left;
        node.left = helper(oldParent, node);
        return node;
    }
}
```

### Python

```python
class Solution:
    def flipBinaryTree(self, root: 'Node', leaf: 'Node') -> 'Node':
        return self._helper(leaf, None)
    def _helper(self, node, new_parent):
        old_parent = node.parent
        node.parent = new_parent
        if node.left is new_parent: node.left = None
        if node.right is new_parent: node.right = None
        if node == old_parent: return node
        if node.left is not None: node.right = node.left
        node.left = self._helper(old_parent, node)
        return node
```

### C++

```cpp
class Solution {
public:
    Node* flipBinaryTree(Node* root, Node* leaf) {
        return helper(leaf, nullptr);
    }
private:
    Node* helper(Node* node, Node* newParent) {
        Node* oldParent = node->parent;
        node->parent = newParent;
        if (node->left == newParent) node->left = nullptr;
        if (node->right == newParent) node->right = nullptr;
        if (node == oldParent) return node;
        if (node->left != nullptr) node->right = node->left;
        node->left = helper(oldParent, node);
        return node;
    }
};
```

---

## Interview Tips

| What to mention | Why it matters |
|----------------|----------------|
| Explain parent as “next” pointer | Shows you understand non‑trivial tree attributes. |
| Discuss both recursive and iterative solutions | Demonstrates flexibility and readiness for production code. |
| Highlight cycle prevention (`if (node.left == newParent) node.left = null`) | Shows you’re careful about pointer bugs. |

---

## Final Takeaway

*LeetCode 1666 is a showcase problem: a tree + parent pointer + in‑place flip. A clean, concise solution (like the ones above) will impress interviewers and demonstrate you can manipulate pointers without crashing. Copy the code into LeetCode, run the tests, and when the interview panel asks “What’s the idea behind your solution?”, you’ll answer in a sentence that covers the whole algorithm.*

> **Good luck on your next interview!**  
> And if you liked this deep‑dive, subscribe to our newsletter for more “golden interview questions” and production‑grade code.

---

## Call to Action

*If you’re prepping for a **software‑engineering** interview, start by mastering LeetCode 1666. It’s short, it tests pointer skills, and it’s a perfect “quick‑design” question.*  

> *Comment below:*  
> *“Which language did you struggle with most?  Let’s discuss it!”*

--- 

Happy coding – and good luck on your next interview! 🚀

--- 

**End of Post**  

--- 

**Happy interviewing!**