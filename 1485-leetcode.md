---
title: LeetCode 1485. Clone Binary Tree With Random Pointer - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Clone Binary Tree With Random Pointer  
**LeetCode 1485 – Medium**  

| Language | Code |
|---|---|
| **Java** | [View code](#java-code) |
| **Python** | [View code](#python-code) |
| **C++** | [View code](#cpp-code) |

> **SEO Keywords**: Clone Binary Tree With Random Pointer, LeetCode 1485, binary tree deep copy, random pointer clone, Java DFS solution, Python DFS solution, C++ DFS solution, data structure interview, interview coding challenge, job interview coding problems.

---

## 1. Problem Summary

You are given a binary tree where each node contains an extra pointer called `random`.  
`random` may point to **any** node in the tree (including itself) or be `null`.  
Your task is to create a **deep copy** of the entire tree – i.e., every node and every
`random` reference must point to a new node that has the same value.

The input and output format follows the usual LeetCode binary‑tree serialization:
`[value, random_index]` – `random_index` is the position of the node in the array
(or `null` if no random pointer).

> **Constraints**  
> - 0 ≤ number of nodes ≤ 1000  
> - 1 ≤ Node.val ≤ 10⁶

---

## 2. Core Idea – Two‑Pass DFS with a HashMap

1. **First Pass** – Clone every node (ignoring the `random` link) and store a
   mapping from the original node to its clone.  
2. **Second Pass** – Traverse the original tree again; for each node, set the
   clone’s `left`, `right`, and `random` fields by looking up the corresponding
   clone objects in the map.

Because every node is visited twice and we use a hash map to resolve pointers,
the algorithm runs in **O(N)** time and **O(N)** auxiliary space.

The two‑pass approach keeps the code straightforward and avoids complex
pointer gymnastics that a one‑pass in‑place clone would require.

---

## 3. Code Implementations

> **Note**: In all three snippets the *clone* node type is the same as the
> original (`Node`).  LeetCode’s `Solution` interface expects the same class
> for input and output.

### <a name="java-code"></a>Java – DFS with HashMap

```java
/**
 * Definition for a Node.
 * public class Node {
 *     int val;
 *     Node left;
 *     Node right;
 *     Node random;
 *     Node() {}
 *     Node(int val) { this.val = val; }
 *     Node(int val, Node left, Node right, Node random) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *         this.random = random;
 *     }
 * }
 */
class Solution {
    // Map original node -> cloned node
    private final Map<Node, Node> map = new HashMap<>();

    public Node cloneTree(Node root) {
        if (root == null) return null;

        // First pass: clone nodes (no children yet)
        cloneNodes(root);

        // Second pass: set left, right, random
        setPointers(root);

        return map.get(root);
    }

    // Recursive DFS that clones each node once
    private void cloneNodes(Node node) {
        if (node == null || map.containsKey(node)) return;
        map.put(node, new Node(node.val));
        cloneNodes(node.left);
        cloneNodes(node.right);
        cloneNodes(node.random);
    }

    // Recursive DFS that wires up children & random pointers
    private void setPointers(Node node) {
        if (node == null) return;
        Node copy = map.get(node);
        copy.left = map.get(node.left);
        copy.right = map.get(node.right);
        copy.random = map.get(node.random);
        setPointers(node.left);
        setPointers(node.right);
        setPointers(node.random);
    }
}
```

### <a name="python-code"></a>Python – DFS with Dictionary

```python
# Definition for a Node.
class Node:
    def __init__(self, val=0, left=None, right=None, random=None):
        self.val = val
        self.left = left
        self.right = right
        self.random = random

class Solution:
    def cloneTree(self, root: 'Node') -> 'Node':
        if not root:
            return None

        self.mapping = {}
        self._first_pass(root)
        self._second_pass(root)
        return self.mapping[root]

    def _first_pass(self, node):
        if not node or node in self.mapping:
            return
        self.mapping[node] = Node(node.val)
        self._first_pass(node.left)
        self._first_pass(node.right)
        self._first_pass(node.random)

    def _second_pass(self, node):
        if not node:
            return
        clone = self.mapping[node]
        clone.left = self.mapping.get(node.left)
        clone.right = self.mapping.get(node.right)
        clone.random = self.mapping.get(node.random)
        self._second_pass(node.left)
        self._second_pass(node.right)
        self._second_pass(node.random)
```

### <a name="cpp-code"></a>C++ – DFS with `unordered_map`

```cpp
/**
 * Definition for a Node.
 * struct Node {
 *     int val;
 *     Node *left;
 *     Node *right;
 *     Node *random;
 *     Node() : val(0), left(nullptr), right(nullptr), random(nullptr) {}
 *     Node(int _val) : val(_val), left(nullptr), right(nullptr), random(nullptr) {}
 *     Node(int _val, Node* _left, Node* _right, Node* _random)
 *         : val(_val), left(_left), right(_right), random(_random) {}
 * };
 */
class Solution {
public:
    Node* cloneTree(Node* root) {
        if (!root) return nullptr;
        unordered_map<Node*, Node*> mp;
        firstPass(root, mp);
        secondPass(root, mp);
        return mp[root];
    }

private:
    void firstPass(Node* node, unordered_map<Node*, Node*>& mp) {
        if (!node || mp.count(node)) return;
        mp[node] = new Node(node->val);
        firstPass(node->left, mp);
        firstPass(node->right, mp);
        firstPass(node->random, mp);
    }

    void secondPass(Node* node, unordered_map<Node*, Node*>& mp) {
        if (!node) return;
        Node* clone = mp[node];
        clone->left = mp.count(node->left) ? mp[node->left] : nullptr;
        clone->right = mp.count(node->right) ? mp[node->right] : nullptr;
        clone->random = mp.count(node->random) ? mp[node->random] : nullptr;
        secondPass(node->left, mp);
        secondPass(node->right, mp);
        secondPass(node->random, mp);
    }
};
```

---

## 4. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| First DFS | **O(N)** | **O(N)** (hash map) |
| Second DFS | **O(N)** | **O(N)** (hash map) |
| **Total** | **O(N)** | **O(N)** |

`N` is the number of nodes in the tree.  
The recursion depth is bounded by the height of the tree (≤ N).

---

## 5. Discussion – The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | Two‑pass DFS is straightforward to reason about. | Recursion may cause stack overflow on very deep trees. | In‑place one‑pass solutions (linking clones in the original tree) are hard to debug. |
| **Memory** | O(N) map is acceptable for 1 000 nodes. | Requires additional space beyond the new tree. | Using `unordered_map` in C++ may lead to high overhead if many hash collisions occur. |
| **Performance** | Only one extra field to set per node. | Two traversals double the constant factor. | Complex pointer manipulation can introduce subtle bugs when restoring the original tree. |
| **Extensibility** | Works for any tree‑like structure with random links. | Does not handle cycles that aren't covered by the random pointer (not a tree). | The algorithm assumes no cycles other than through `random`; otherwise you’d need cycle detection. |
| **Edge Cases** | Handles `null`, self‑referencing random pointers, and random pointing to any depth. | None. | If the node class contains extra fields (e.g., parent pointers), they must be handled explicitly. |

---

## 6. SEO‑Friendly Blog Article

> **Title**  
> *Master LeetCode 1485: Clone Binary Tree With Random Pointer – Java, Python, & C++ Solutions for Your Interview*

---

### 6.1 Why This Problem Rocks Your Interview

LeetCode’s *Clone Binary Tree With Random Pointer* (1485) is a classic interview
problem because it combines two beloved data‑structure concepts:

1. **Binary tree traversal** (DFS/BFS)  
2. **Random pointer deep copy** – a hidden trap that can make even seasoned
   developers stumble.

It appears in many coding‑interview repositories, and hiring managers love to
use it because it tests:

- Your ability to think recursively
- Your skill with hash tables (`Map`, `dict`, `unordered_map`)
- Your understanding of pointer semantics

---

### 6.2 Why We Use a HashMap (or Dict / Unordered Map)

The random pointer is **not** part of the tree’s natural structure.
When you clone a node, you need to know *which* cloned node corresponds to the
original `random` target.  
A hash map gives you **O(1)** look‑up time and guarantees that each original
node maps to a unique clone.

---

### 6.3 Step‑by‑Step Java Example

```java
public Node cloneTree(Node root) {
    if (root == null) return null;
    Map<Node, Node> map = new HashMap<>();
    cloneNodes(root, map);   // 1st pass
    setPointers(root, map); // 2nd pass
    return map.get(root);
}
```

*First pass* simply does `map.put(node, new Node(node.val))`.  
*Second pass* wires `left`, `right`, and `random` via `map.get(...)`.

---

### 6.4 Alternative Approaches You Might See

| Approach | Pros | Cons |
|----------|------|------|
| **Iterative BFS** | Avoids recursion | Slightly more code; still needs a queue |
| **One‑pass In‑place Clone** | No extra hash map | Very tricky; high risk of bugs |
| **Using Parent Pointers** | Sometimes reduces extra space | Not available in the problem description |

The two‑pass DFS is *usually* the recommended approach for interview coding.

---

### 6.5 How to Prepare for Similar Interview Questions

1. **Practice with Random Pointers**  
   LeetCode’s *Copy List with Random Pointer* (linked list version) is a
   great warm‑up.

2. **Master DFS & BFS Templates**  
   Keep a reusable “clone node with mapping” template in your notebook.

3. **Understand Edge Cases**  
   Empty tree, single node with `random` pointing to itself, very deep trees.

4. **Time & Space Trade‑offs**  
   Be ready to explain why you chose a particular solution and what its
   trade‑offs are.

5. **Ask Clarifying Questions**  
   In a live interview, confirm whether you’re allowed to use extra memory,
   whether the tree can contain cycles via `random`, etc.

---

## 6.6 Wrap‑Up

- **Problem**: Deep copy a binary tree with an arbitrary `random` pointer.  
- **Solution**: Two‑pass DFS + hash map (DFS, Python dict, C++ unordered_map).  
- **Complexity**: O(N) time, O(N) space.  

Whether you’re brushing up for a data‑structures interview, prepping for a
software‑engineering role, or just love LeetCode challenges, mastering this
problem will give you confidence in handling pointers, recursion, and hash
tables – skills that are **highly sought after** by top tech companies.

Happy coding! 🚀