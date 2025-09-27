---
title: LeetCode 510. Inorder Successor in BST II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📝 Problem Recap – LeetCode 510: Inorder Successor in BST II

> **Given a node in a binary search tree, return the in‑order successor of that node in the BST.**  
> A node’s in‑order successor is the node with the smallest key that is greater than the given node’s value.  
> The tree nodes have a `parent` pointer – you’re **not** given the root of the tree.

**Examples**

| Tree | Node | Successor |
|------|------|-----------|
| `[2,1,3]` | `1` | `2` |
| `[5,3,6,2,4,null,null,1]` | `6` | `null` |

**Constraints**

- `1 ≤ n ≤ 10⁴`
- `-10⁵ ≤ Node.val ≤ 10⁵`
- All values are unique

---

## 💡 High‑Level Strategy

1. **Right Subtree Exists?**  
   If the node has a right child, the successor is the *leftmost* node in that right subtree.

2. **No Right Subtree**  
   Move up the tree via the parent pointer until you find an ancestor that is a **left** child of its parent.  
   That ancestor is the successor. If you reach the root without finding such an ancestor, the node has no successor.

Both steps are `O(h)` where `h` is the tree height, and use `O(1)` extra space.

---

## ✅ Implementations

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All three use the same iterative algorithm and handle the `null`‑successor case gracefully.

---

### Java

```java
/**
 * Definition for a binary tree node with parent pointer.
 */
class Node {
    int val;
    Node left, right, parent;
    Node(int x) { val = x; }
}

public class Solution {

    /**
     * Returns the in-order successor of the given node in a BST.
     *
     * @param node the node whose successor is to be found
     * @return the successor node, or null if it does not exist
     */
    public Node inorderSuccessor(Node node) {
        if (node == null) return null;

        /* 1. Node has right subtree – go to its leftmost child. */
        if (node.right != null) {
            Node succ = node.right;
            while (succ.left != null) {
                succ = succ.left;
            }
            return succ;
        }

        /* 2. No right subtree – climb up via parents. */
        Node parent = node.parent;
        Node cur = node;
        while (parent != null && cur == parent.right) {
            cur = parent;
            parent = parent.parent;
        }
        return parent;   // may be null
    }
}
```

**Test harness**

```java
public static void main(String[] args) {
    // Build tree [2,1,3]
    Node root = new Node(2);
    root.left = new Node(1);
    root.right = new Node(3);
    root.left.parent = root;
    root.right.parent = root;

    Solution s = new Solution();
    System.out.println(s.inorderSuccessor(root.left).val); // 2
}
```

---

### Python

```python
class Node:
    def __init__(self, val):
        self.val = val
        self.left = self.right = self.parent = None

class Solution:
    def inorderSuccessor(self, node: Node) -> Node:
        """
        Return the in‑order successor of 'node' in a BST.
        """
        if node.right:
            succ = node.right
            while succ.left:
                succ = succ.left
            return succ

        # No right subtree – climb up via parents
        parent = node.parent
        cur = node
        while parent and cur == parent.right:
            cur = parent
            parent = parent.parent
        return parent   # may be None
```

**Sample usage**

```python
root = Node(2)
root.left = Node(1)
root.right = Node(3)
root.left.parent = root
root.right.parent = root

sol = Solution()
print(sol.inorderSuccessor(root.left).val)   # 2
```

---

### C++

```cpp
#include <iostream>

/* Node definition for a BST with parent pointer */
struct Node {
    int val;
    Node *left, *right, *parent;
    Node(int x) : val(x), left(nullptr), right(nullptr), parent(nullptr) {}
};

class Solution {
public:
    Node* inorderSuccessor(Node* node) {
        if (!node) return nullptr;

        /* Case 1: right subtree exists */
        if (node->right) {
            Node* succ = node->right;
            while (succ->left)
                succ = succ->left;
            return succ;
        }

        /* Case 2: no right subtree – go up until node is a left child */
        Node* parent = node->parent;
        Node* cur = node;
        while (parent && cur == parent->right) {
            cur = parent;
            parent = parent->parent;
        }
        return parent;   // may be nullptr
    }
};
```

**Demo**

```cpp
int main() {
    Node* root = new Node(2);
    root->left = new Node(1);
    root->right = new Node(3);
    root->left->parent = root;
    root->right->parent = root;

    Solution s;
    Node* succ = s.inorderSuccessor(root->left);
    if (succ) std::cout << succ->val << '\n';  // prints 2
    else      std::cout << "No successor\n";
}
```

---

## 🔎 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Right‑subtree traversal | `O(h)` | `O(1)` |
| Climbing via parents | `O(h)` | `O(1)` |
| **Total** | **`O(h)`** | **`O(1)`** |

`h` is the height of the BST (≤ `log n` for balanced, `n` for skewed).

---

## ⚖️ The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm simplicity** | Iterative, no recursion | Requires parent pointers | None – straightforward |
| **Space usage** | Constant (`O(1)`) | Still needs parent links | None |
| **Time usage** | Linear in height | Worst‑case `O(n)` for skewed tree | None |
| **Edge cases** | Handled (no successor → `null`) | None | None |
| **Follow‑up challenge** | Solved via parent traversal | Cannot use values | None |

**Takeaway**: The parent pointer is the secret weapon; it turns an otherwise `O(n)` search into an `O(h)` walk.

---

## 📌 Interview‑Ready Tips

1. **Explain the two cases** – right subtree vs. climb up.  
2. **Mention the parent pointer** – it’s why the “follow‑up” (no value lookup) is trivial.  
3. **Time/Space** – highlight `O(h)` time, `O(1)` space.  
4. **Edge handling** – if the node is the maximum element, return `null`.  
5. **Test your solution** with both balanced and skewed trees.

---

## 📚 Bonus – Follow‑Up: “Without Looking Up Any Node’s Values”

When you’re only allowed to inspect node pointers (not their values), the same algorithm works because:

- You never compare values – you only navigate `left`, `right`, and `parent`.  
- Successor is always the **first ancestor** that is a **left child** of its parent (or the leftmost node in the right subtree).

Thus, the iterative solution above is already optimal for the follow‑up.

---

## 📢 SEO & Job‑Boosting Wrap‑Up

- **Title**: Mastering LeetCode 510: Inorder Successor in BST II – Java, Python, C++ Solutions & Interview Insights  
- **Meta Description**: Get the job‑ready answer for LeetCode 510. Learn the in‑order successor algorithm, see clean Java, Python, and C++ code, and ace your data‑structures interview.  
- **Keywords**: LeetCode 510, Inorder Successor in BST II, BST successor, binary search tree, parent pointer, interview question, Java solution, Python solution, C++ solution, O(h) time, O(1) space, algorithm interview, tech interview prep.

> **Ready to impress recruiters?** Drop this article into your portfolio or LinkedIn posts, and watch interviewers notice your deep understanding of BSTs and pointer logic.

Happy coding and good luck on the next interview! 🚀