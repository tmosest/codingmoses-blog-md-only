---
title: LeetCode 431. Encode N-ary Tree to Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Encode an N‑ary Tree to a Binary Tree (LeetCode 431) – Code + Interview‑Ready Blog

> **TL;DR**  
> • Convert an N‑ary tree → binary tree using the *left‑child right‑sibling* trick.  
> • Decode back by traversing left→right siblings.  
> • O(N) time, O(H) recursion stack (≤ 1000).  
> • Clean, stateless implementation – perfect for a LeetCode interview.

---

### 1. The Problem (LeetCode 431)

Given an **N‑ary tree** (each node can have any number of children) you must:

| Step | Goal |
|------|------|
| **Encode** | Convert it into a **binary tree** so that the structure can be stored or transmitted. |
| **Decode** | Reconstruct the original N‑ary tree from the binary representation. |

> **Constraints**  
> * `0 <= node.val <= 10⁴`  
> * `0 <= number of nodes <= 10⁴`  
> * Height ≤ 1000  
> * **No global/static state** – the encoder/decoder must be stateless.

The classic solution is the *left‑child right‑sibling* mapping:

* The **left child** of a binary node is the **first child** of the corresponding N‑ary node.  
* The **right child** of a binary node is the **next sibling** of that child.

This representation is lossless: you can always walk back to the N‑ary tree.

---

### 2. Why This Blog?  
Interviewers love problems that test **data‑structure conversions** and your ability to think about **tree traversals** in non‑trivial ways.  
This post gives you:

* **Working code** in **Java, Python, and C++** – copy‑paste ready.  
* A **clear, SEO‑friendly explanation** that you can mention in a cover letter or portfolio.  
* A breakdown of the **good**, **bad**, and **ugly** aspects – so you can talk about trade‑offs during an interview.

---

## 3. The Core Algorithm

```
encode(root):
    if root is null: return null
    binary = new TreeNode(root.val)
    if root.children not empty:
        binary.left = encode(root.children[0])          // first child
        cur = binary.left
        for i from 1 to root.children.size-1:          // siblings
            cur.right = encode(root.children[i])
            cur = cur.right
    return binary

decode(root):
    if root is null: return null
    nary = new Node(root.val, [])
    cur = root.left
    while cur != null:                                // traverse siblings
        nary.children.append(decode(cur))
        cur = cur.right
    return nary
```

* **Time**: Every node is visited once → **O(N)**.  
* **Space**: Recursion stack depth ≤ height of tree (≤ 1000).  
* **Stateless**: No global variables; all state lives in the call stack.

---

## 4. Code (Copy‑Paste Ready)

### 4.1 Java

```java
// Definition for an N-ary tree node.
class Node {
    public int val;
    public List<Node> children;

    public Node() {}
    public Node(int _val) { val = _val; }
    public Node(int _val, List<Node> _children) {
        val = _val;
        children = _children;
    }
}

// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

class Codec {

    // Encode an N-ary tree to a binary tree.
    public TreeNode encode(Node root) {
        if (root == null) return null;

        TreeNode binary = new TreeNode(root.val);

        if (root.children != null && !root.children.isEmpty()) {
            binary.left = encode(root.children.get(0));          // first child
            TreeNode cur = binary.left;
            for (int i = 1; i < root.children.size(); i++) {     // siblings
                cur.right = encode(root.children.get(i));
                cur = cur.right;
            }
        }
        return binary;
    }

    // Decode a binary tree back to an N-ary tree.
    public Node decode(TreeNode root) {
        if (root == null) return null;

        Node nary = new Node(root.val, new ArrayList<>());
        TreeNode cur = root.left;
        while (cur != null) {                                  // traverse siblings
            nary.children.add(decode(cur));
            cur = cur.right;
        }
        return nary;
    }
}
```

### 4.2 Python

```python
# Definition for an N-ary tree node.
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children or []

# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Codec:
    # Encodes an N-ary tree to a binary tree.
    def encode(self, root: 'Node') -> 'TreeNode':
        if not root:
            return None

        node = TreeNode(root.val)
        if root.children:
            node.left = self.encode(root.children[0])        # first child
            cur = node.left
            for child in root.children[1:]:
                cur.right = self.encode(child)              # siblings
                cur = cur.right
        return node

    # Decodes a binary tree back to an N-ary tree.
    def decode(self, root: 'TreeNode') -> 'Node':
        if not root:
            return None

        nary = Node(root.val, [])
        cur = root.left
        while cur:
            nary.children.append(self.decode(cur))
            cur = cur.right
        return nary
```

### 4.3 C++

```cpp
#include <vector>
using namespace std;

// Definition for an N-ary tree node.
struct Node {
    int val;
    vector<Node*> children;
    Node() : val(0) {}
    Node(int _val) : val(_val) {}
    Node(int _val, vector<Node*> _children)
        : val(_val), children(_children) {}
};

// Definition for a binary tree node.
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int _val) : val(_val), left(nullptr), right(nullptr) {}
};

class Codec {
public:
    // Encode an N-ary tree to a binary tree.
    TreeNode* encode(Node* root) {
        if (!root) return nullptr;

        TreeNode* node = new TreeNode(root->val);
        if (!root->children.empty()) {
            node->left = encode(root->children[0]);      // first child
            TreeNode* cur = node->left;
            for (size_t i = 1; i < root->children.size(); ++i) {
                cur->right = encode(root->children[i]);  // siblings
                cur = cur->right;
            }
        }
        return node;
    }

    // Decode a binary tree back to an N-ary tree.
    Node* decode(TreeNode* root) {
        if (!root) return nullptr;

        Node* nary = new Node(root->val);
        TreeNode* cur = root->left;
        while (cur) {
            nary->children.push_back(decode(cur));
            cur = cur->right;
        }
        return nary;
    }
};
```

> **Tip** – In all languages, never forget the `if (!root)` guard; otherwise you’ll hit a `NullPointerException`/`NoneType` error.

---

## 5. The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Space** | No extra data structures; only recursion stack. | Recursion depth could reach 1000 – safe in Java/Python but watch for stack overflow in deeper trees. | None; algorithm is naturally tail‑recursive on the sibling list, but you still allocate new `TreeNode`/`Node` objects. |
| **Time** | Linear, O(N). | None; algorithm is optimal. | None. |
| **Simplicity** | Clear left‑child/right‑sibling mapping – easy to explain. | The *child list* is `null` or empty → extra `null` checks. | In C++ you must `delete` nodes afterward; forgetting it can cause memory leaks. |
| **Lossless** | Perfect bijection. | None. | If you mistakenly switch `left`/`right` during encoding, decoding will fail silently. |
| **Statelessness** | Meets interview requirement. | Must avoid globals – can be a pitfall for beginners. | None. |
| **Testing** | Easy to write unit tests by round‑tripping. | Edge‑case `root = null`. | Over‑complicated test harnesses may hide bugs. |

### 5.1 Common Gotchas

1. **Null Children List**  
   *Java* – `root.children` can be `null`. Check `root.children != null` before accessing.  
   *Python* – `children or []` ensures a list is always present.

2. **Memory Leaks in C++**  
   Every `new` must eventually be paired with `delete`. In real interviews you’re usually asked to *ignore* this, but if you’re building a library, write a destructor or use smart pointers.

3. **Recursion Stack Limits**  
   *Python* default recursion limit is 1000. If you’re unlucky and the tree’s height equals that limit, you’ll get a `RecursionError`.  
   Java & C++ have larger stacks, but it’s still a good idea to test with a deep chain of children.

4. **Left‑Child/Right‑Sibling Inversion**  
   Swapping `left` and `right` in the encoder will break the decoder. Always double‑check the mapping diagram before coding.

---

## 6. How to Nail This Interview

1. **Sketch the Mapping**  
   Draw a tiny N‑ary node with 3 children → binary tree. Show the left‑child right‑sibling diagram.  
   Visual aids win points.

2. **Explain the Recursion**  
   *Encode* – “First child → left, siblings → chain of rights.”  
   *Decode* – “Walk left, then right until `nullptr`.”  

3. **Time & Space** – Mention O(N) and O(H) stack, and reassure that H ≤ 1000.

4. **Edge Cases**  
   * Empty tree.  
   * Node with no children.  
   * Wide trees vs. deep trees.

5. **Ask Questions**  
   * “Would you prefer an iterative implementation to avoid recursion?”  
   * “How would you handle a tree with 10⁵ nodes?”  

Showing that you can anticipate trade‑offs demonstrates deep structural understanding—exactly what interviewers look for.

---

## 6. Bonus – Quick Test Harness (Python)

```python
def tree_to_list(root):
    if not root: return None
    return [root.val] + [tree_to_list(child) for child in root.children]

codec = Codec()

# Example N-ary tree:
#        1
#      / | \
#     2  3  4
#        |
#        5
root = Node(1, [Node(2), Node(3, [Node(5)]), Node(4)])
binary = codec.encode(root)
decoded = codec.decode(binary)

assert tree_to_list(root) == tree_to_list(decoded)
print("Round‑trip succeeded!")
```

Run the snippet above in a fresh Python interpreter to see the algorithm in action.

---

## 7. Final Takeaway

> **"The left‑child right‑sibling representation turns an arbitrary‑degree tree into a binary one without losing any structure, and it’s as simple as a single recursive pass."**

You now have:

* **Verified, multi‑language solutions** that you can paste into any coding platform.  
* A **structured interview answer** explaining the *good*, *bad*, and *ugly* trade‑offs.  
* **Keywords** ready for your résumé: *Encode N‑ary Tree to Binary Tree*, *LeetCode 431*, *tree conversion*, *data‑structure interview*, *left‑child right‑sibling*, *O(N) algorithm*.

Feel confident that you’ll impress your next hiring manager with both code and strategy. Happy coding! 💻✨