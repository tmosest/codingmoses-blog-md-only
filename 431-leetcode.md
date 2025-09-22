---
title: LeetCode 431. Encode N-ary Tree to Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Encode an N‑ary Tree into a Binary Tree – Code Samples

Below are **complete, self‑contained** solutions for Java, Python, and C++.  
All of them use the classic *left‑child / right‑sibling* encoding, which is
stateless (no global/static state), works for any N‑ary tree, and is
straightforward to test.

---

### 1.1 Java

```java
import java.util.ArrayList;
import java.util.List;

// ----- N‑ary node ---------------------------------------------------------
class Node {
    public int val;
    public List<Node> children;

    public Node() {
        this.children = new ArrayList<>();
    }

    public Node(int _val) {
        val = _val;
        children = new ArrayList<>();
    }

    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
}

// ----- Binary node --------------------------------------------------------
class TreeNode {
    int val;
    TreeNode left;   // first child
    TreeNode right;  // next sibling

    TreeNode(int x) { val = x; }
}

// ----- Codec ---------------------------------------------------------------
public class Codec {

    /** Encodes an N‑ary tree to a binary tree. */
    public TreeNode encode(Node root) {
        if (root == null) return null;

        TreeNode bRoot = new TreeNode(root.val);

        // 1. first child → left child
        if (!root.children.isEmpty()) {
            bRoot.left = encode(root.children.get(0));
        }

        // 2. remaining children → right siblings
        TreeNode sibling = bRoot.left;
        for (int i = 1; i < root.children.size(); i++) {
            sibling.right = encode(root.children.get(i));
            sibling = sibling.right;
        }
        return bRoot;
    }

    /** Decodes a binary tree back to an N‑ary tree. */
    public Node decode(TreeNode root) {
        if (root == null) return null;

        Node nRoot = new Node(root.val);
        TreeNode child = root.left;
        while (child != null) {
            nRoot.children.add(decode(child));
            child = child.right;
        }
        return nRoot;
    }

    // Demo
    public static void main(String[] args) {
        // Build a simple 3‑ary tree: 1 → [2,3,4]
        Node root = new Node(1);
        root.children.add(new Node(2));
        root.children.add(new Node(3));
        root.children.add(new Node(4));

        Codec codec = new Codec();
        TreeNode bRoot = codec.encode(root);
        Node recovered = codec.decode(bRoot);

        System.out.println("Original root val: " + root.val);
        System.out.println("Recovered root val: " + recovered.val);
        System.out.println("Children count: " + recovered.children.size());
    }
}
```

---

### 1.2 Python

```python
from typing import List, Optional

# ----- N‑ary node ---------------------------------------------------------
class Node:
    def __init__(self, val: int = 0, children: List['Node'] = None):
        self.val = val
        self.children = children or []

# ----- Binary node --------------------------------------------------------
class TreeNode:
    def __init__(self, val: int = 0, left: 'TreeNode' = None, right: 'TreeNode' = None):
        self.val = val
        self.left = left
        self.right = right

# ----- Codec ---------------------------------------------------------------
class Codec:
    def encode(self, root: Optional[Node]) -> Optional[TreeNode]:
        if not root:
            return None

        b_root = TreeNode(root.val)

        # 1. first child becomes left child
        if root.children:
            b_root.left = self.encode(root.children[0])

        # 2. remaining children become right siblings
        sibling = b_root.left
        for child in root.children[1:]:
            sibling.right = self.encode(child)
            sibling = sibling.right

        return b_root

    def decode(self, root: Optional[TreeNode]) -> Optional[Node]:
        if not root:
            return None

        n_root = Node(root.val)
        child = root.left
        while child:
            n_root.children.append(self.decode(child))
            child = child.right

        return n_root

# Demo -------------------------------------------------------------
if __name__ == "__main__":
    # 1 → [2, 3, 4]
    root = Node(1, [Node(2), Node(3), Node(4)])

    codec = Codec()
    b_root = codec.encode(root)
    recovered = codec.decode(b_root)

    print("Original:", root.val, [c.val for c in root.children])
    print("Recovered:", recovered.val, [c.val for c in recovered.children])
```

---

### 1.3 C++

```cpp
#include <vector>
using namespace std;

// ----- N‑ary node ---------------------------------------------------------
struct Node {
    int val;
    vector<Node*> children;
    Node(int _val = 0) : val(_val) {}
};

// ----- Binary node --------------------------------------------------------
struct TreeNode {
    int val;
    TreeNode *left;   // first child
    TreeNode *right;  // next sibling
    TreeNode(int _val = 0) : val(_val), left(nullptr), right(nullptr) {}
};

// ----- Codec ---------------------------------------------------------------
class Codec {
public:
    // Encode N‑ary to binary
    TreeNode* encode(Node* root) {
        if (!root) return nullptr;
        TreeNode* bRoot = new TreeNode(root->val);

        // first child
        if (!root->children.empty())
            bRoot->left = encode(root->children[0]);

        // siblings
        TreeNode* sibling = bRoot->left;
        for (size_t i = 1; i < root->children.size(); ++i) {
            sibling->right = encode(root->children[i]);
            sibling = sibling->right;
        }
        return bRoot;
    }

    // Decode binary to N‑ary
    Node* decode(TreeNode* root) {
        if (!root) return nullptr;
        Node* nRoot = new Node(root->val);

        TreeNode* child = root->left;
        while (child) {
            nRoot->children.push_back(decode(child));
            child = child->right;
        }
        return nRoot;
    }
};

// Demo -------------------------------------------------------------
int main() {
    // 1 → [2,3,4]
    Node* root = new Node(1);
    root->children = { new Node(2), new Node(3), new Node(4) };

    Codec codec;
    TreeNode* bRoot = codec.encode(root);
    Node* recovered = codec.decode(bRoot);

    // Simple output
    printf("Original root: %d\n", root->val);
    printf("Recovered root: %d\n", recovered->val);
    return 0;
}
```

---

## 2.  Blog Article – “Encode N‑ary Tree to Binary Tree: The Good, The Bad, & The Ugly”

### 2.1 Introduction (SEO‑Optimized)

> **How to Convert an N‑ary Tree to a Binary Tree** – Learn the classic left‑child/right‑sibling technique, why it’s a game‑changer for interview questions, and how to implement it in *Java, Python, and C++*. Perfect for LeetCode, technical interviews, and expanding your data‑structure repertoire.

If you’re a software engineer prepping for interviews or just love data‑structures, you’ll encounter the **N‑ary tree to binary tree conversion** problem. It tests your ability to think recursively, manage pointers, and keep the solution stateless. This article explains the method in depth, highlights its strengths and pitfalls, and gives ready‑to‑copy code in three languages.

### 2.2 The Good: Why This Encoding Works

| Feature | Explanation |
|---------|-------------|
| **Universality** | Works for any branching factor \(N \ge 0\). |
| **Simplicity** | Only two pointers (`left` and `right`) are needed. |
| **Space‑Efficient** | No extra markers or sentinel nodes – just reuse existing structure. |
| **Preserves Order** | Children are kept in the same order because they become right‑siblings. |
| **Stateless** | No global or static variables; the algorithm is purely recursive. |

The core idea is the *left‑child/right‑sibling representation*. In a binary tree:

* **Left child** stores the first child of an N‑ary node.
* **Right child** stores the next sibling of that child.

Visually:

```
      N-ary node (val)
          |
          v
   Binary left child <-- first child
          |
          v
   Binary right child <-- sibling
          |
          v
   Binary right child <-- next sibling
          ...
```

Thus, any N‑ary tree can be represented as a binary tree with at most one extra edge per child.

### 2.3 The Bad: Potential Drawbacks

| Issue | Mitigation |
|-------|------------|
| **Recursive depth** | N‑ary trees can be very deep (height up to 1000). Use tail‑call optimization or iterative stack if the language runtime limits recursion depth (Python). |
| **Memory overhead** | Two pointers per node may increase memory usage compared to a flattened adjacency list. |
| **Unclear if not familiar** | People new to tree encodings may find the algorithm confusing. Be sure to explain the mapping before coding. |
| **Garbage‑collection** | In languages with manual memory management (C++), you must delete the binary nodes after use to avoid leaks. |

While these concerns exist, they rarely appear in typical interview test cases. The major takeaway is that you must keep the recursion depth in mind and ensure the environment (e.g., Python’s 1000‑recursion‑limit) can handle it.

### 2.4 The Ugly: Things that Can Go Wrong

1. **Incorrect sibling linkage**  
   *If you accidentally connect a sibling to the wrong parent, the entire tree structure breaks.*

2. **Child order violation**  
   *Mixing up the order of children while constructing right‑sibling links scrambles the output.*

3. **Null pointer mishandling**  
   *In Java or C++, forgetting to initialise `children` (or `children` being `null`) causes crashes.*

4. **Memory leaks in C++**  
   *Failing to `delete` binary nodes after use can quickly exhaust memory.*

### 2.4 Debugging Checklist

| Check | What to look for |
|-------|-----------------|
| `root == null` (or `None`) | Return `null` / `None` immediately – handles empty trees. |
| First child assignment | `left` should only be set if there is at least one child. |
| Sibling loop | Use a `while` or `for` that starts from the second child; ensure you move the `sibling` pointer each time. |
| Order preservation | Compare the list of children before and after encoding/decoding. |
| Stack depth | Use `sys.setrecursionlimit` in Python or iterative DFS if you hit recursion limits. |

### 2.5 Putting It All Together – The Classic Implementation

The three code snippets in the previous section are the *canonical* solutions for this problem. Notice the uniform pattern:

1. **Encode**  
   * Recurse on the first child → `left`.  
   * Recurse on remaining children → chain of `right` siblings.

2. **Decode**  
   * Traverse `left` children while moving through `right` siblings.  

Both processes are *mirror images* of each other, guaranteeing that an N‑ary tree can be recovered exactly as it was.

### 2.6 Test‑Ready Example

```text
1 -> [2, 3, 4]
        |
        v
       Binary left child (2)
             |
             v
       Binary right child (3)
             |
             v
       Binary right child (4)
```

When you run the demo code in any language, you should see:

```
Original root: 1
Recovered root: 1
Children count: 3
```

That confirms the encoding and decoding are correct.

### 2.7 Summary & Takeaway

* **What you learn** – Master a classic tree transformation, improve your recursive thinking, and become comfortable writing clean, stateless code.
* **When to use it** – Interview questions on LeetCode, coding bootcamps, or when you need to store a hierarchical structure as a binary tree (e.g., certain serialization libraries).
* **Potential pitfalls** – Watch recursion depth, manage memory, and test thoroughly.

> **Ready for the next challenge?**  
> Dive into LeetCode’s “Encode N‑ary Tree to Binary Tree” problem, apply the left‑child/right‑sibling encoding, and watch your interview confidence soar.

---

### 2.8 Keywords & Meta Tags (For SEO)

| Tag | Value |
|-----|-------|
| **Title** | Encode N‑ary Tree to Binary Tree – Java, Python, C++ |
| **Description** | Convert an N‑ary tree into a binary tree using left‑child/right‑sibling encoding. Learn the algorithm, get ready‑to‑copy Java, Python, C++ code, and improve interview skills. |
| **Keywords** | n‑ary tree, binary tree, left child right sibling, tree encoding, leetcode, interview question, recursion, data structure, Java coding, Python coding, C++ coding |
| **Author** | [Your Name / Portfolio] |
| **Date** | 2024‑09‑28 |
| **Canonical URL** | https://yourwebsite.com/blog/encode-nary-tree-to-binary |

--- 

### 2.9 Closing Thoughts

Converting an N‑ary tree to a binary tree is a staple in technical interviews and an excellent exercise for sharpening recursion and pointer handling.  
By mastering this technique—and understanding its strengths and weaknesses—you’ll add a powerful tool to your interview arsenal.

Happy coding, and may your trees always stay balanced!