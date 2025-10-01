---
title: LeetCode 1485. Clone Binary Tree With Random Pointer - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# Clone Binary Tree With Random Pointer – 1485  
**Java | Python | C++ Solutions + A Deep‑Dive Blog Post**  

> **Goal:**  
> Clone a binary tree where every node also contains a *random* pointer that may point to any node (or `null`).  
> We must return a deep copy – a completely independent tree that preserves both the left/right structure *and* the random links.

> **Why it matters:**  
> - Appears in senior‑level technical interviews (LinkedIn, Google, Facebook).  
> - Shows mastery of DFS/BFS, hashing, and pointer manipulation.  
> - Demonstrates a clean O(n) solution, which recruiters love.  

---

## Table of Contents  

1. [Problem Recap](#problem-recap)  
2. [Approach – The Good, The Bad, and The Ugly](#approach)  
3. [Implementation Details](#implementation-details)  
   - [Java](#java)  
   - [Python](#python)  
   - [C++](#c++)  
4. [Time & Space Complexity](#complexity)  
5. [Edge Cases & Gotchas](#edge-cases)  
6. [Testing & Validation](#testing)  
7. [Conclusion & Interview Tips](#conclusion)  
8. [SEO Metadata](#seo-metadata)  

---

## Problem Recap <a name="problem-recap"></a>

```
class Node {
    int val;
    Node left;
    Node right;
    Node random;
}
```

- `random` may point to **any** node in the tree (including itself) or be `null`.  
- The tree is given as an object graph; you do **not** get a list of indices.  
- Return a *deep copy* – a new tree where every node is freshly allocated and the same connections exist.

**Constraints**

| | | |
|---|---|---|
|Number of nodes|0 – 1000| |
|Node.val|1 – 10⁶| |

---

## Approach – The Good, The Bad, and The Ugly <a name="approach"></a>

| Category | What it means | Why it matters |
|---|---|---|
|**Good** | One‑pass DFS with memoization (`Map<Node, NodeCopy>`) | Linear time, clean recursion, O(n) extra space for the hash map. |
|**Bad** | Two‑pass BFS + HashMap | Still O(n) but requires extra traversal; more code, harder to reason about. |
|**Ugly** | In‑place clone without extra space (e.g., weave nodes) | Very tricky, bug‑prone, not necessary for ≤ 1000 nodes. |

> **Bottom line:**  
> The simplest and most reliable solution for interviewers is **recursive DFS + HashMap**.  
> It guarantees O(n) time, handles cycles introduced by `random`, and is easy to explain.

---

## Implementation Details <a name="implementation-details"></a>

Below are **fully‑commented** implementations in **Java**, **Python**, and **C++**.  
All share the same logic:

1. **Base case:** If the input node is `null`, return `null`.  
2. **Memoization:** If we’ve already cloned a node, return the clone to avoid infinite loops.  
3. **Clone the current node** (`val`, `left`, `right`, `random`).  
4. Recursively clone the children and the random pointer.

---

### Java <a name="java"></a>

```java
/**
 * Definition for Node.
 */
class Node {
    int val;
    Node left;
    Node right;
    Node random;

    Node() {}
    Node(int val) { this.val = val; }
    Node(int val, Node left, Node right, Node random) {
        this.val = val;
        this.left = left;
        this.right = right;
        this.random = random;
    }
}

/**
 * Clone Binary Tree With Random Pointer
 * 1485. LeetCode
 */
public class Solution {
    /**
     * @param root: the root of the original tree
     * @return: the root of the cloned tree
     */
    public Node copyRandomBinaryTree(Node root) {
        Map<Node, Node> memo = new HashMap<>();
        return cloneNode(root, memo);
    }

    private Node cloneNode(Node node, Map<Node, Node> memo) {
        if (node == null) return null;
        if (memo.containsKey(node)) return memo.get(node);

        // Clone current node (val only)
        Node copy = new Node(node.val);
        memo.put(node, copy);

        // Recursively clone children and random pointer
        copy.left   = cloneNode(node.left,   memo);
        copy.right  = cloneNode(node.right,  memo);
        copy.random = cloneNode(node.random, memo);

        return copy;
    }
}
```

---

### Python <a name="python"></a>

```python
# Definition for a binary tree node with a random pointer.
class Node:
    def __init__(self, val: int = 0, left: 'Node' = None,
                 right: 'Node' = None, random: 'Node' = None):
        self.val = val
        self.left = left
        self.right = right
        self.random = random

class Solution:
    def copyRandomBinaryTree(self, root: 'Node') -> 'Node':
        memo = {}
        return self._clone(root, memo)

    def _clone(self, node: 'Node', memo: dict) -> 'Node':
        if not node:
            return None
        if node in memo:
            return memo[node]
        copy = Node(node.val)
        memo[node] = copy
        copy.left = self._clone(node.left, memo)
        copy.right = self._clone(node.right, memo)
        copy.random = self._clone(node.random, memo)
        return copy
```

> **Note**: In Python, we can safely use the node object itself as a dictionary key because objects are hashable by identity.

---

### C++ <a name="c++"></a>

```cpp
/**
 * Definition for a Node.
 */
struct Node {
    int val;
    Node* left;
    Node* right;
    Node* random;
    Node() : val(0), left(nullptr), right(nullptr), random(nullptr) {}
    Node(int _val) : val(_val), left(nullptr), right(nullptr), random(nullptr) {}
    Node(int _val, Node* _left, Node* _right, Node* _random)
        : val(_val), left(_left), right(_right), random(_random) {}
};

class Solution {
public:
    Node* copyRandomBinaryTree(Node* root) {
        unordered_map<Node*, Node*> memo;
        return clone(root, memo);
    }

private:
    Node* clone(Node* node, unordered_map<Node*, Node*>& memo) {
        if (!node) return nullptr;
        if (memo.count(node)) return memo[node];

        Node* copy = new Node(node->val);
        memo[node] = copy;

        copy->left   = clone(node->left,   memo);
        copy->right  = clone(node->right,  memo);
        copy->random = clone(node->random, memo);

        return copy;
    }
};
```

> **Tip**: Use `unordered_map` for O(1) look‑ups.

---

## Time & Space Complexity <a name="complexity"></a>

| Metric | Calculation | Result |
|--------|-------------|--------|
| **Time** | Each node is processed once + hash map look‑ups (O(1)) | **O(n)** |
| **Space** | Recursion stack (O(h) ≤ O(n)) + hash map (O(n)) | **O(n)** |

> For a balanced tree, `h ≈ log n`; for a degenerate (linked list) tree, `h = n`.

---

## Edge Cases & Gotchas <a name="edge-cases"></a>

| Edge Case | Why it matters | How to handle |
|-----------|----------------|---------------|
| `root == null` | Empty tree | Return `null` immediately |
| `random` points to the node itself | Cycle in `random` links | Memoization protects against infinite recursion |
| Deeply unbalanced tree | Stack overflow | Recursion depth equals tree height; for n ≤ 1000 it's safe, but iterative DFS/BFS can be used for very deep trees |
| Duplicate values | `val` is not unique | Hash map uses node identity, not value, so duplicates are fine |

---

## Testing & Validation <a name="testing"></a>

1. **Simple tree** – root only, random = null.  
2. **Balanced tree** – random pointers to siblings.  
3. **Unbalanced tree** – random pointers across different depths.  
3. **Random points to root** – cross‑branch connections.  
4. **Self‑referencing random** – node.random = node.  
5. **Null random** – mix of `null` and valid random links.  
6. **Empty tree** – verify no crashes.  

> Use unit tests or LeetCode’s built‑in checker.  
> In Python, you can traverse both trees simultaneously and assert:

```python
assert original.val == copy.val
assert original.left is not original_copy.left
# ... etc.
```

---

## Conclusion & Interview Tips <a name="conclusion"></a>

- **Explain the algorithm clearly**: “We use a hash map to remember which nodes we’ve already cloned. This way, even if the random pointer creates cycles, we never recurse infinitely.”  
- **Mention complexity**: “Linear time, linear auxiliary space.”  
- **Show alternative approaches**: “We could do a two‑pass BFS if you wanted to avoid recursion, but recursion is simpler for ≤ 1000 nodes.”  
- **Stress test**: “I can write a quick script to generate random trees and validate the clone automatically.”  

**Interview takeaway:**  
Mastering this problem demonstrates an understanding of *object graphs*, *hashing by identity*, and *recursive depth‑first traversal*. Recruiters often use it as a gate‑keeper for senior software engineering roles. Be ready to write the solution in the language you’re most comfortable with (Java, Python, or C++).  

---

## SEO Metadata <a name="seo-metadata"></a>

- **Title**: Clone Binary Tree With Random Pointer – LeetCode 1485 (Java, Python, C++)

- **Meta Description**:  
  "Fast, O(n) solutions for LeetCode 1485 – Clone Binary Tree With Random Pointer. Code in Java, Python & C++. Interview ready explanations, edge‑case handling, and job‑interview tips."

- **Keywords**:  
  `LeetCode 1485`, `Clone Binary Tree With Random Pointer`, `job interview coding`, `Java DFS`, `Python DFS`, `C++ unordered_map`, `deep copy binary tree`, `random pointer`, `O(n) solution`

- **Canonical URL**: `https://yourblog.com/leetcode-1485-clone-binary-tree-with-random-pointer`

---

### Final Thought

When recruiters ask you to **clone a binary tree with random pointers**, they’re testing whether you can:

- Model the graph correctly
- Handle self‑referencing edges
- Write clean, maintainable code

A **recursive DFS + memoization** solution satisfies all of the above with the least friction. Keep the code short, the comments crisp, and be ready to discuss complexity and edge cases. You’ll impress both the *coding* and the *conceptual* parts of the interview. Good luck!