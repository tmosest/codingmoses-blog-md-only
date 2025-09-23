---
title: LeetCode 1644. Lowest Common Ancestor of a Binary Tree II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Summary (LeetCode 1644)

> **Lowest Common Ancestor of a Binary Tree II**  
> Medium | 1 k+ solved | ⭐⭐⭐⭐  

Given the root of a binary tree and two nodes `p` and `q`, return the **lowest common ancestor (LCA)** of `p` and `q`.  
If **either** `p` or `q` is *not* present in the tree, return `null`.  
All node values are unique.

> **Constraints**
> * 1 ≤ #nodes ≤ 10⁴  
> * −10⁹ ≤ node.val ≤ 10⁹  
> * `p != q`

> **Follow‑up**  
> Find the LCA *without* a second pass that checks whether the nodes exist.

---

## 2.  Why is 1644 harder than 236?

| Feature | 236 (“Lowest Common Ancestor of a Binary Tree”) | 1644 (“Lowest Common Ancestor of a Binary Tree II”) |
|---------|-----------------------------------------------|----------------------------------------------------|
| **Guarantee** | Both `p` and `q` are **guaranteed** to be in the tree | Not guaranteed |
| **Result on missing node** | Never `null` | Must return `null` if one/many missing |
| **Early‑exit** | Safe: return as soon as you find either `p` or `q` | Unsafe: you must still search the rest of the tree |

Because of the lack of guarantee, the classic solution from 236 (`if (root==p || root==q) return root;`) cannot be used without modifications. We need to keep track of whether we actually saw both nodes while still traversing the whole tree.

---

## 3.  Solution Overview

We do a single **post‑order DFS** and propagate two pieces of information:

1. **The LCA candidate** returned from the recursive call.
2. **Flags** indicating whether `p` and/or `q` have been seen in the current subtree.

When both flags are `true`, the current node is the real LCA.  
If only one flag is `true`, we return the node that matched (so the parent can decide).  
If none match, we return `null`.

There are two idiomatic ways to store the flags:

| Method | Implementation | Trade‑offs |
|--------|----------------|------------|
| **Two boolean fields** (`pFound`, `qFound`) | Easier to read | Mutable state; harder to test in isolation |
| **Integer counter** (`count`) | Single field | Slightly less expressive but very compact |

Both have **O(N)** time and **O(H)** space (recursive call stack).

---

## 4.  Java Implementation

```java
// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class Solution {
    /* ------------  Method 1 : two boolean flags ------------ */
    private boolean pFound;
    private boolean qFound;

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        // reset flags for each call
        pFound = false;
        qFound = false;
        TreeNode candidate = dfs(root, p, q);
        // return candidate only if both nodes were seen
        return (pFound && qFound) ? candidate : null;
    }

    private TreeNode dfs(TreeNode node, TreeNode p, TreeNode q) {
        if (node == null) return null;

        TreeNode left  = dfs(node.left,  p, q);
        TreeNode right = dfs(node.right, p, q);

        if (node == p) { pFound = true; return node; }
        if (node == q) { qFound = true; return node; }

        if (left != null && right != null) return node; // both found in subtrees
        return (left != null) ? left : right;          // one found or none
    }

    /* ------------  Method 2 : integer counter (optional) ------------ */
    // private int count = 0;
    // public TreeNode lowestCommonAncestorCounter(TreeNode root, TreeNode p, TreeNode q) {
    //     count = 0;
    //     TreeNode candidate = dfsCounter(root, p, q);
    //     return (count == 2) ? candidate : null;
    // }
    //
    // private TreeNode dfsCounter(TreeNode node, TreeNode p, TreeNode q) {
    //     if (node == null) return null;
    //     TreeNode left  = dfsCounter(node.left,  p, q);
    //     TreeNode right = dfsCounter(node.right, p, q);
    //     if (node == p || node == q) { count++; return node; }
    //     if (left != null && right != null) return node;
    //     return (left != null) ? left : right;
    // }
}
```

*Explanation*  
- The DFS is post‑order: we inspect children before deciding on the current node.  
- `dfs` returns the node that is either `p`, `q`, or the LCA of them in the subtree.  
- The flags (`pFound`, `qFound`) are set only when the exact node is matched.  
- After the full traversal, we return the candidate only if both flags are `true`.

---

## 5.  Python Implementation

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def lowestCommonAncestor(self, root: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode | None:
        # two boolean flags
        self.p_found = False
        self.q_found = False

        candidate = self._dfs(root, p, q)
        return candidate if self.p_found and self.q_found else None

    def _dfs(self, node: TreeNode, p: TreeNode, q: TreeNode) -> TreeNode | None:
        if not node:
            return None

        left  = self._dfs(node.left,  p, q)
        right = self._dfs(node.right, p, q)

        if node is p:
            self.p_found = True
            return node
        if node is q:
            self.q_found = True
            return node

        if left and right:            # both nodes found deeper
            return node
        return left or right          # one side found or none
```

*Tips for Python*  
- Use `is` to compare node objects, not `==`, because node values are unique but we need to confirm identity.  
- The solution works in a single DFS; no second “existence” pass is needed.

---

## 6.  C++ Implementation

```cpp
/** Definition for a binary tree node. **/
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
    bool pFound, qFound;   // two boolean flags

    TreeNode* dfs(TreeNode* node, TreeNode* p, TreeNode* q) {
        if (!node) return nullptr;

        TreeNode* left  = dfs(node->left,  p, q);
        TreeNode* right = dfs(node->right, p, q);

        if (node == p) { pFound = true; return node; }
        if (node == q) { qFound = true; return node; }

        if (left && right) return node;               // both found in subtrees
        return left ? left : right;                   // one or none
    }

public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        pFound = qFound = false;
        TreeNode* candidate = dfs(root, p, q);
        return (pFound && qFound) ? candidate : nullptr;
    }
};
```

The C++ code is nearly identical to Java – the only differences are syntax and the use of raw pointers.  

---

## 6.  Complexity Analysis (All Languages)

| Operation | Time | Space |
|-----------|------|-------|
| DFS traversal | **O(N)** (each node visited once) | **O(H)** (call stack, `H` = tree height) |
| Flag updates | constant per node | constant per node |
| Total | **O(N)** | **O(H)** |

---

## 7.  Edge Cases & Common Pitfalls

| Edge case | What to watch out for |
|-----------|-----------------------|
| **Root is `null`** | Immediately return `null`. |
| **`p` or `q` is the same as root** | Still need to confirm the other node exists in the subtree. |
| **Deep skewed tree** | Recursion depth `H` can reach `10⁴`; in languages with limited stack you may need an iterative solution. |
| **Using mutable global state** | Forget to reset flags between test cases → wrong answer. |
| **Comparing by value instead of identity** | In LeetCode the nodes are distinct objects; comparing `node.val == p.val` can give false positives if the tree has duplicate values (not allowed here but good practice). |

---

## 8.  “Good‑Bad‑Ugly” Breakdown

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | • Single pass DFS with clear propagation of flags. <br>• Minimal code, O(N) time. <br>• Works for *any* binary tree shape. | • None yet. | • None. |
| **Bad** | • If you copy‑paste the 236 solution (`if(root==p||root==q)return root;`) you’ll miss the existence check. <br>• Early‑exit in the middle of a DFS can return a wrong LCA if one node is missing. | • Forgetting to reset global flags on every test case. <br>• Using `==` on node values instead of node identity. |
| **Ugly** | • Handling the case where only one of the nodes is present (e.g., `p` is ancestor of `q`, but `q` is absent). The DFS above automatically returns `null` in that scenario, so no extra traversal is needed. | • Some interviewers expect you to implement a *two‑pass* solution: first DFS for LCA, then BFS/DFS again to confirm both nodes exist. <br>• Trying to use an iterative stack while also maintaining two flags can become verbose and error‑prone. |

---

## 9.  How to Use This for a Job Interview

1. **Explain the difference with 236** – shows you understand the nuance.  
2. **Walk through the DFS** – highlight how you propagate the flags.  
3. **Show edge‑case handling** – “if one node is missing, return null”.  
4. **Mention time/space** – interviewers love seeing O‑notation.  
5. **Optional: provide the counter‑based implementation** – demonstrates you know multiple idioms.

---

## 10.  Full Code Package

Below you’ll find the three complete solutions you can paste into the LeetCode editor or your own repository.

| Language | File |
|----------|------|
| **Java** | `Solution.java` |
| **Python** | `solution.py` |
| **C++** | `solution.cpp` |

---

### 10.1 Java (`Solution.java`)

```java
class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int x) { val = x; }
}

public class Solution {
    private boolean pFound;
    private boolean qFound;

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        pFound = false; qFound = false;
        TreeNode candidate = dfs(root, p, q);
        return (pFound && qFound) ? candidate : null;
    }

    private TreeNode dfs(TreeNode node, TreeNode p, TreeNode q) {
        if (node == null) return null;
        TreeNode left  = dfs(node.left,  p, q);
        TreeNode right = dfs(node.right, p, q);

        if (node == p) { pFound = true; return node; }
        if (node == q) { qFound = true; return node; }

        if (left != null && right != null) return node;
        return (left != null) ? left : right;
    }
}
```

### 10.2 Python (`solution.py`)

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def lowestCommonAncestor(self, root, p, q):
        self.p_found = False
        self.q_found = False
        candidate = self._dfs(root, p, q)
        return candidate if self.p_found and self.q_found else None

    def _dfs(self, node, p, q):
        if not node: return None
        left  = self._dfs(node.left,  p, q)
        right = self._dfs(node.right, p, q)

        if node is p: self.p_found = True; return node
        if node is q: self.q_found = True; return node

        if left and right: return node
        return left or right
```

### 10.3 C++ (`solution.cpp`)

```cpp
/** Definition for a binary tree node. **/
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
    bool pFound, qFound;

    TreeNode* dfs(TreeNode* node, TreeNode* p, TreeNode* q) {
        if (!node) return nullptr;

        TreeNode* left  = dfs(node->left,  p, q);
        TreeNode* right = dfs(node->right, p, q);

        if (node == p) { pFound = true; return node; }
        if (node == q) { qFound = true; return node; }

        if (left && right) return node;           // LCA found
        return left ? left : right;              // propagate match
    }

public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        pFound = qFound = false;
        TreeNode* cand = dfs(root, p, q);
        return (pFound && qFound) ? cand : nullptr;
    }
};
```

---

## 11.  Blog Post – “Lowest Common Ancestor of a Binary Tree II: Good, Bad, Ugly (and Why It’s a Great Interview Question)”

> **SEO Keywords**: LeetCode 1644, Lowest Common Ancestor, Binary Tree, Job Interview, Coding Interview, Data Structures, Recursion, Counter, Flag, O(N) Solution, Binary Search Tree, DFS, BFS, Algorithmic Thinking

---

### 11.1 Introduction

If you’re preparing for a software engineering interview, you’ll quickly encounter *“Lowest Common Ancestor (LCA)”* problems. The classic variant—*find the LCA in a Binary Search Tree*—is common, but *LeetCode 1644* pushes the envelope by asking for the LCA **in any binary tree**, and **only if both nodes exist**. It’s the perfect blend of data‑structure mastery, recursive thinking, and edge‑case handling.  

This post walks through what makes the problem “Good”, the pitfalls that make it “Bad”, and the nuanced “Ugly” scenario that can trip you up. We’ll finish with a practical checklist for answering this in an interview, so you can walk into the next interview room confident.

---

### 11.2 The “Good” – Why a Single Pass DFS is Elegant

1. **Simplicity**  
   A single DFS that carries two boolean flags (`pFound`, `qFound`) is all you need.  
   No extra loops or complex data structures.

2. **Optimal Time**  
   Every node is visited once → **O(N)**.  
   Interviewers love seeing a linear‑time proof in the *analysis* section.

3. **Uniform for All Trees**  
   Works for balanced, skewed, or degenerate trees.  
   Recursion depth is the tree height (`H`), giving **O(H)** space.

4. **Handles Edge‑cases Internally**  
   Whether the target nodes are the same as the root, one is missing, or one is an ancestor, the flags automatically decide if we should return `null` or the actual LCA.

---

### 11.3 The “Bad” – Common Missteps (and How to Avoid Them)

| Mistake | Why It Happens | Fix |
|---------|----------------|-----|
| Copying 236 LCA solution | Assuming 1644 is identical to 236 | Add existence flags or second pass |
| Early exit mid‑DFS | Believing you can break once you hit `p` or `q` | Don’t exit early; propagate flags upward |
| Forgetting to reset flags | Running multiple test cases in the same runtime | Reset `pFound` and `qFound` at the start of every call |
| Using value equality (`==`) instead of identity | Confusion between node value and node object | Use `is` in Python, `==` only for primitives, `node == p` in Java/C++ for object identity |

---

### 11.4 The “Ugly” – The Edge‑Case Where One Node Is Missing

> **Scenario**: `p` is the ancestor of `q`, but `q` doesn’t actually exist in the tree.  
>  
> **What’s Ugly?**  
> If you naïvely implement the single‑pass flag‑DFS, you might think it will still return the correct LCA. But it will return `null` because `qFound` stays `false`. That’s *exactly* the expected behavior for this problem. The “ugliness” comes from older solutions that do a *two‑pass* approach: first find the LCA, then a separate BFS/DFS to confirm both nodes exist. This extra traversal can make the solution harder to reason about and prone to bugs.

**Why the Flag‑DFS Wins**  
The flags `pFound` and `qFound` are *propagated* all the way up the recursion stack. If one node never appears, the corresponding flag stays `false`. Hence, the final `if (pFound && qFound)` ensures you return `null` if the second node is missing. No extra loops, no messy bookkeeping—just one clean traversal.

---

### 11.5 Interview Talk-Track

1. **Start with the problem definition** – emphasize that we’re dealing with *objects*, not just integer values.  
2. **Explain the difference with the classic LCA (BST)** – the BST variant exploits ordering, but 1644 requires a pure structural solution.  
3. **Draw the DFS recursion tree** on the whiteboard. Mark where flags are set and how they travel up.  
4. **Show the pseudocode** (similar to the Java code) and point out the two key lines: `if(node == p) { pFound = true; return node; }`.  
5. **Discuss edge‑case**: If `root == p`, we still need to confirm `q` is in the subtree. The flags solve that automatically.  
6. **Mention the optional counter‑based solution** – “instead of booleans, I could have used an integer counter. Both are idiomatic; I’ll choose whichever the interviewer prefers.”  
7. **Time & Space** – quickly write the big‑O formulas.  
8. **Wrap up** – “So 1644 is a great interview question because it tests understanding of recursion, flags, edge‑case handling, and algorithmic complexity in a single problem.”

---

### 11.6 Final Thoughts

- *LeetCode 1644* is more than a coding puzzle; it’s a micro‑exam of how you handle constraints that change a classic problem.  
- Master the single‑pass DFS with flags; you’ll be ready for both online judges and in‑person interviews.  
- Keep your code clean, annotate your thoughts, and be ready to answer follow‑up questions like “What if the tree was a BST?” or “Can you solve this iteratively?”

---

### 11.7 Takeaway

Whether you’re solving this on a whiteboard or a laptop, remember: **understanding the problem statement deeply** (the “good”), **avoiding the pitfalls** (the “bad”), and **handling the trickiest edge cases** (the “ugly”) is what sets top performers apart. LeetCode 1644 is a quintessential example of that path.  

Good luck, and happy coding! 🚀

---

*Feel free to bookmark this post and share it with your coding study group. If you found it helpful, drop a comment or a star on the repository!*  

--- 

> **Author**: *Your Name* – *Data‑Structures Enthusiast, Software Engineer, Interview Coach*  
> **Date**: *2024-07-12*  
> **Comments**: *Open for discussion!*  

--- 

This completes the full explanation, code, and interview‑ready analysis for **LeetCode 1644 – Lowest Common Ancestor of a Binary Tree II**. Happy interviewing!