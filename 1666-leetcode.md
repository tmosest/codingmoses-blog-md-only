---
title: LeetCode 1666. Change the Root of a Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1666 – **Change the Root of a Binary Tree**  
*LeetCode – Medium – DFS – Pointer manipulation*

> **Goal**  
> Given the root of a binary tree and a leaf node, “reroot” the tree so that the leaf becomes the new root.  
> Every node on the path from the leaf to the old root must swap its children:  
> * if a node has a left child, that child becomes the node’s right child;  
> * the node’s former parent becomes the node’s left child.  
> Finally, all `parent` pointers must be updated (otherwise you’ll get *Wrong Answer*).

Below you’ll find full, ready‑to‑copy solutions in **Java, Python, and C++**.  
After the code I’ve written a short blog‑style article that covers the **good, the bad, and the ugly** of this problem – great for polishing your interview notes and SEO‑optimised for job‑searching.

---

## 1. Java Solution

```java
// Definition for a binary tree node with parent pointer.
class Node {
    int val;
    Node left;
    Node right;
    Node parent;
    Node(int x) { val = x; }
}

public class Solution {
    // original root is needed only for the base case check
    private Node originalRoot;

    public Node flipBinaryTree(Node root, Node leaf) {
        originalRoot = root;
        return helper(leaf, null);          // leaf becomes new root
    }

    private Node helper(Node cur, Node newParent) {
        Node oldParent = cur.parent;        // remember original parent
        cur.parent = newParent;             // update parent pointer

        // if we are “walking back” through the old parent we must
        // disconnect that link to avoid cycles
        if (cur.left == newParent) cur.left = null;
        if (cur.right == newParent) cur.right = null;

        // we’re done once we have reached the old root
        if (cur == originalRoot) return cur;

        // if left child exists it becomes the new right child
        if (cur.left != null) cur.right = cur.left;

        // recurse on the old parent – it becomes the left child
        cur.left = helper(oldParent, cur);

        return cur;
    }
}
```

*Why this works:*  
The recursion walks **up** the tree from the leaf to the original root.  
At each step it:

1. Stores the original parent (`oldParent`).  
2. Makes `newParent` the current node’s parent.  
3. Removes the link back to the old parent to prevent cycles.  
4. Swaps left → right (if present).  
5. Recurses, making the old parent the new left child.

The base case is when the current node is the original root – the recursion stops and the new root is returned.

---

## 2. Python Solution

```python
# Definition for a binary tree node with parent pointer.
class Node:
    def __init__(self, val: int, left=None, right=None, parent=None):
        self.val = val
        self.left = left
        self.right = right
        self.parent = parent

class Solution:
    def flipBinaryTree(self, root: 'Node', leaf: 'Node') -> 'Node':
        self.original_root = root
        return self._dfs(leaf, None)

    def _dfs(self, cur: 'Node', new_parent: 'Node') -> 'Node':
        old_parent = cur.parent
        cur.parent = new_parent

        # disconnect the old link if it still points to new_parent
        if cur.left is new_parent:
            cur.left = None
        if cur.right is new_parent:
            cur.right = None

        if cur is self.original_root:
            return cur

        if cur.left:
            cur.right = cur.left

        cur.left = self._dfs(old_parent, cur)
        return cur
```

*Same logic as Java, just adapted to Python style.*  
All pointers are references, so `is` must be used for identity checks.

---

## 3. C++ Solution

```cpp
/* Definition for a Node. */
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* parent;
    Node(int _val) : val(_val), left(nullptr), right(nullptr), parent(nullptr) {}
};

class Solution {
public:
    Node* flipBinaryTree(Node* root, Node* leaf) {
        originalRoot = root;
        return dfs(leaf, nullptr);           // leaf becomes new root
    }

private:
    Node* originalRoot;

    Node* dfs(Node* cur, Node* newParent) {
        Node* oldParent = cur->parent;
        cur->parent = newParent;

        // disconnect link back to old parent
        if (cur->left == newParent) cur->left = nullptr;
        if (cur->right == newParent) cur->right = nullptr;

        if (cur == originalRoot) return cur;

        if (cur->left) cur->right = cur->left;   // left becomes right

        cur->left = dfs(oldParent, cur);         // old parent becomes left child
        return cur;
    }
};
```

C++ uses raw pointers; the same pointer‑manipulation logic is applied.

---

## 4. Blog Article: *The Good, the Bad, and the Ugly of “Change the Root of a Binary Tree”*

> **SEO keywords:** Change the Root of a Binary Tree, LeetCode 1666, binary tree rerooting, pointer manipulation, DFS, interview tips, job interview, Java, Python, C++

---

### 1. Introduction

Binary trees are the bread‑and‑butter of many interview questions, but the *rerooting* problem (LeetCode 1666) throws a twist: you must **change the root** of a tree **in place**, updating both child links and `parent` pointers. It’s a perfect test of:

- Understanding of tree traversal  
- Ability to reason about pointer manipulation  
- Clean recursive design

Whether you’re preparing for a coding interview or polishing your algorithmic toolbox, this problem is a goldmine.

---

### 2. Problem Recap

You’re given a root node `root` and a leaf node `leaf`. The tree has the usual `val`, `left`, `right` fields **plus** a `parent` pointer.  
Goal: Make `leaf` the new root and adjust all nodes on the path from `leaf` up to the old root so that:

- The left child (if any) becomes the right child.  
- The original parent becomes the left child.  
- All `parent` pointers are updated accordingly.

If you forget to adjust a parent link, the tree will contain a cycle and you’ll get “Wrong Answer”.

---

### 3. The “Good” – Why This Is a Beautiful Problem

| Aspect | Why It’s Great |
|--------|----------------|
| **Minimal Code, Big Impact** | A recursive helper of ~20 lines flips the whole tree. |
| **Clear Base‑Case** | The recursion stops naturally when the original root is reached. |
| **No Extra Space** | All changes are in place; no auxiliary data structures are required. |
| **Language‑agnostic** | The core logic is identical across Java, Python, and C++. |
| **Insight into Parent Pointers** | It demonstrates how “parent” links can be treated as the next pointer of a linked list, giving a fresh perspective on tree traversal. |

---

### 4. The “Bad” – Common Pitfalls

| Pitfall | How to Avoid It |
|---------|-----------------|
| **Cycle Formation** | Always **disconnect** the old parent before assigning it as a new child (`if (cur.left == newParent) cur.left = null;`). |
| **Incorrect Base Case** | If you stop at the leaf instead of the original root, you’ll lose the upper part of the tree. |
| **Missing `parent` Updates** | Forgetting to set `cur.parent = newParent` will leave dangling pointers. |
| **Using Value Comparisons** | In languages with object references, compare by identity (`is` in Python, `==` for pointers in C++). |
| **Stack Overflow on Deep Trees** | With up to 100 nodes this isn’t a real issue, but always be aware of recursion depth for larger inputs. |

---

### 5. The “Ugly” – Less Straightforward Scenarios

| Scenario | Why It Gets Ugly |
|----------|------------------|
| **Tree with Multiple Leaves** | You must ensure you’re passed the *exact* leaf you intend to make the new root. |
| **Pre‑existing Cycles** | If the input tree is malformed (unlikely on LeetCode), the algorithm will still traverse until it hits a loop. |
| **Memory‑Leak in C++** | If nodes are allocated on the heap and you don’t manage ownership, you could leak memory. |
| **Unbalanced Trees** | In highly skewed trees, the recursion depth equals the tree height; still acceptable here but worth noting. |
| **Parent Pointers Not Set Initially** | Some problems may omit `parent`. In that case, you’d need to pre‑compute parent pointers before rerooting. |

---

### 6. Step‑by‑Step Walk‑Through

Let’s trace the algorithm on a small example:

```
      3
     / \
    5   1
   / \ / \
  6  2 0  8
   \ / 
    7 4
```

`leaf = 7`.  
We start at 7 → old parent 2 → new parent `null` (since 7 is new root).  

- 7’s `parent` = `null`.  
- 7’s left child is `null`, right child is `null`.  
- 7’s left stays `null`.  
- 7’s right stays `null`.

Recurse up to 2:

- 2’s old parent = 5, new parent = 7.  
- 2’s left child becomes 5’s left child? No: 2’s left is 4 → becomes right child.  
- 2’s left becomes recursion result (`7`).  
- Update pointers accordingly.

Continue until 3. The final tree matches the LeetCode output.  

---

### 7. Complexity Analysis

| Metric | Time | Space |
|--------|------|-------|
| **Time** | `O(n)` – each node on the path from leaf to root is visited once. | |
| **Auxiliary Space** | `O(1)` – in‑place updates. | |
| **Recursive Stack** | `O(h)` – height of the tree (≤ 100 for this problem). | |

---

### 8. Interview‑Friendly Tips

1. **Explain your intuition first** – treat the path from leaf to root as a linked list where the `parent` pointer is the “next” link.  
2. **Sketch the pointer changes** on a whiteboard or paper; many candidates skip the step that disconnects the old parent, leading to subtle bugs.  
3. **Show the base case** clearly: when you hit the original root, stop recursion.  
4. **Mention edge cases** such as skewed trees or a leaf that is the root itself (though LeetCode guarantees at least two nodes).  
5. **State the complexity** up front; interviewers appreciate a concise big‑O explanation.  

---

### 9. Conclusion

LeetCode 1666 is deceptively simple yet rich with learning opportunities.  
By mastering pointer manipulation in trees, you’re not just solving a single problem—you’re sharpening a skill set that applies to:

- Path‑based algorithms (e.g., Lowest Common Ancestor with parent pointers)  
- Tree restructuring tasks in production code  
- Implementing advanced data structures like Link‑Cut Trees  

Use the clean Java, Python, and C++ solutions above as your go‑to implementation reference, and keep the “Good, Bad, Ugly” checklist handy for future interviews.

Good luck, and happy coding!

--- 

> *Ready to ace your next coding interview?*  
> Practice LeetCode 1666 and add it to your “in‑place tree manipulation” skill set. The problem may be small in size, but the concepts you learn are huge.  

--- 

*End of article.* 

--- 

This completes the full, production‑ready answer: code in three languages, a solid explanation, and an interview‑friendly article. Happy interviewing!