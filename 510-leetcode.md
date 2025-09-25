---
title: LeetCode 510. Inorder Successor in BST II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 In‑order Successor in BST II – 510. LeetCode  
### The Good, The Bad, and The Ugly (with 3‑language solutions)

> **Goal:**  
> Given a node **x** in a Binary Search Tree (BST) that has a parent pointer, find its in‑order successor – the node with the smallest key greater than **x.val**.  
> If no successor exists, return `null`.

> **Why it matters:**  
> This is a classic interview problem that tests:
> * BST properties  
> * Pointer manipulation  
> * Edge‑case handling (no right child, root, leaf, etc.)  
> * Ability to write clean, readable code in Java, Python, or C++ – the languages most recruiters ask about.

> **SEO Keywords**: *LeetCode 510*, *Inorder Successor in BST*, *Java interview problem*, *Python interview solution*, *C++ interview coding*, *Binary Search Tree*, *Parent pointer*, *Job interview preparation*, *Coding interview tips*

---

## The Good

| Aspect | Why it’s good |
|--------|--------------|
| **No root needed** | We only have the node itself; the solution works with parent links, which is a realistic interview scenario. |
| **O(h) time & O(1) space** | `h` is tree height; we traverse only ancestors or the right subtree. No recursion stack or extra data structures. |
| **Simple logic** | Two mutually exclusive cases: right child exists → go leftmost; else climb ancestors until we’re a left child. |
| **Reusable** | The algorithm works for *any* BST node – perfect for interview re‑use. |

---

## The Bad

| Problem | Common pitfalls |
|--------|----------------|
| **Null pointer bugs** | Forgetting to check `node.right != null` before accessing `node.right.left`. |
| **Parent null** | Root node has no parent – must return `null` if it has no right subtree. |
| **Mis‑identifying the “left” vs “right” side** | Using `== node.parent.left` instead of `== node.parent.right` when climbing. |
| **Complexity creep** | Recursively traversing the whole tree (e.g., DFS) – unnecessary overhead. |

---

## The Ugly

| Scenario | Ugly outcome |
|----------|--------------|
| **Large skewed tree** | Linear time (O(n)) if height = n, but unavoidable for a tree of that shape. |
| **Wrong recursion depth** | Stack overflow in languages that use recursion for the “ancestor climb” (Python, Java). |
| **Mixed‑type node values** | If node values are not unique or not integers, the definition of “successor” can break. |
| **Wrong return type** | Returning a value instead of the node object – loses parent reference for further queries. |

---

## 👨‍💻 Solution Walk‑through

1. **If the node has a right child**  
   *The successor is the leftmost node in that right subtree.*  
   ```text
   x = x.right
   while (x.left != null) x = x.left
   return x
   ```

2. **Otherwise**  
   *Climb up using the parent pointers until we come from a left child.*  
   ```text
   while (x.parent != null && x == x.parent.right)
       x = x.parent
   return x.parent
   ```

*Time Complexity:* `O(h)` – `h` is the height of the tree.  
*Space Complexity:* `O(1)` – no recursion, only a few pointers.

---

## 📦 Code Implementations

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

class Solution {
    public Node inorderSuccessor(Node x) {
        // Case 1: right subtree exists – find its leftmost node
        if (x.right != null) {
            x = x.right;
            while (x.left != null) {
                x = x.left;
            }
            return x;
        }

        // Case 2: climb ancestors until we are a left child
        while (x.parent != null && x == x.parent.right) {
            x = x.parent;
        }
        return x.parent;   // may be null if no successor
    }
}
```

> **Why it’s clean** – no helper methods, explicit pointer checks, and the logic is literally the pseudocode.

---

### 2️⃣ Python

```python
class Node:
    def __init__(self, val):
        self.val = val
        self.left = None
        self.right = None
        self.parent = None

class Solution:
    def inorderSuccessor(self, x: Node) -> Node:
        # Right subtree: go to the leftmost node
        if x.right:
            x = x.right
            while x.left:
                x = x.left
            return x

        # No right subtree: ascend until x is a left child
        while x.parent and x == x.parent.right:
            x = x.parent
        return x.parent  # may be None
```

> **Pythonic notes** – `None` is the natural null; the `while` loops keep the code readable and concise.

---

### 3️⃣ C++

```cpp
/**
 * Definition for a binary tree node with parent pointer.
 */
struct Node {
    int val;
    Node *left = nullptr;
    Node *right = nullptr;
    Node *parent = nullptr;
    Node(int _val) : val(_val) {}
};

class Solution {
public:
    Node* inorderSuccessor(Node* x) {
        // Case 1: right child exists
        if (x->right) {
            x = x->right;
            while (x->left) x = x->left;
            return x;
        }

        // Case 2: climb up to find a node that is a left child
        while (x->parent && x == x->parent->right) {
            x = x->parent;
        }
        return x->parent;   // may be nullptr
    }
};
```

> **Why C++?** – Using raw pointers mirrors the interview environment where memory is manual. The code is short, fast, and O(1) space.

---

## 📈 SEO‑Friendly Tips for Your Job Search

1. **Title & Meta**  
   - Title: *"Master LeetCode 510 – Inorder Successor in BST – Java, Python, C++ Solutions"*  
   - Meta description: *"Learn the optimal in‑order successor algorithm for BSTs, with clean Java, Python, and C++ code. Perfect for coding interview prep and landing a software engineer role."*

2. **Headers**  
   Use H1 for the title, H2 for *The Good, The Bad, The Ugly*, *Solution Walk‑through*, *Code Implementations*, and H3 for each language.

3. **Keyword Placement**  
   - Sprinkle “LeetCode 510” in the first paragraph, solution sections, and code comments.  
   - Use “Binary Search Tree” and “in‑order successor” naturally throughout the article.

4. **Rich Snippets**  
   - Include the code snippets in `<pre><code>` blocks.  
   - Add JSON‑LD “codeRepository” schema if you host the repo on GitHub.

5. **Internal/External Links**  
   - Link to LeetCode’s problem page.  
   - Link to your GitHub repo (or blog) for the full source.  
   - Reference other interview questions: *“Binary Search Tree Traversal”, “Lowest Common Ancestor”*.

6. **Social Sharing**  
   - Add “Share” buttons and a concise tweetable quote:  
     *“Solved LeetCode 510 in O(h) time – Java, Python, C++ solutions. Check out the clean, parent‑pointer‑aware code! #coding #interview”*

7. **Call to Action**  
   End with: *“Want more interview tips? Subscribe to our newsletter or download the free e‑book on algorithmic thinking.”*

---

## 🚀 Takeaway

- **Fast, clean, O(h) solution** that respects the parent pointer constraint.  
- **Three solid implementations** that you can paste into your interview sandbox.  
- **SEO‑optimized blog post** that showcases your coding chops and boosts your online visibility—exactly what recruiters look for.

Happy coding, and good luck landing that dream software engineer role! 🎯