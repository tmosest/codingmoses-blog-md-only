---
title: LeetCode 2236. Root Equals Sum of Children - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📌 LeetCode 2236 – Root Equals Sum of Children  
> **Easy, One‑Line, 100 %** – The perfect interview starter

---

## Table of Contents
1. [Why This Problem Is a Must‑Know](#why-this-problem-is-a-must-know)  
2. [Problem Statement (Official)](#problem-statement-official)  
3. [Solution Overview – The “Good, the Bad, the Ugly”](#solution-overview)  
4. [Reference Implementations](#reference-implementations)  
   * Java  
   * Python  
   * C++  
5. [Edge Cases & Common Pitfalls](#edge-cases-common-pitfalls)  
6. [Complexity Analysis](#complexity-analysis)  
7. [How to Talk About It in an Interview](#how-to-talk-about-it-in-an-interview)  
8. [FAQ & Quick Reference](#faq-quick-reference)  
9. [Wrap‑Up & Takeaway](#wrap-up-takeaway)

> **SEO Keywords**: “LeetCode Root Equals Sum of Children”, “Java solution”, “Python solution”, “C++ solution”, “Easy LeetCode problem”, “binary tree interview question”, “coding interview tips”, “job interview algorithm”, “software engineer interview”

---

## Why This Problem Is a Must‑Know

| **Good** | **Bad** | **Ugly** |
|----------|---------|----------|
| ✔️ Extremely simple – perfect for a *warm‑up* in an interview | ❌ People over‑engineer: write a recursive traversal or a helper method that isn’t needed | ⚠️ Forget that the tree always has *exactly* three nodes, leading to `NullPointerException` in Java or `AttributeError` in Python |

Even the most senior engineers sometimes stumble on it. Being able to answer it *in 1–2 lines* demonstrates:

- Quick thinking  
- Understanding of binary tree structure  
- Ability to spot simplifications

---

## Problem Statement (Official)

> **Root Equals Sum of Children**  
> **Difficulty:** Easy  
> You are given the root of a binary tree that contains exactly **three nodes**: the root, its left child, and its right child.  
> Return `true` if the value of the root node is equal to the sum of the values of its two children, otherwise return `false`.

**Constraints**

| Parameter | Value |
|-----------|-------|
| Number of nodes | 3 |
| `-100 <= Node.val <= 100` |
| The tree is *non‑empty* and has *exactly* a left and a right child |

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[10, 4, 6]` | `true` | `10 == 4 + 6` |
| `[5, 3, 1]` | `false` | `5 != 3 + 1` |

---

## Solution Overview – The “Good, the Bad, the Ugly”

### Good  
- **Constant time** (`O(1)`) because we only look at three nodes.  
- **Constant space** (`O(1)`).  
- No recursion or helper functions needed.  

### Bad  
- Many people write a *generic* solution that works for any binary tree, but that’s unnecessary here.  
- If you forget to guard against `null` children (although the constraints guarantee they exist), your code will crash.  

### Ugly  
- The problem’s constraints are *very* specific; a generic “root‑equals‑sum” solution may look over‑engineered and fail to impress interviewers who expect you to *optimize*.  

---

## Reference Implementations

> 👉 **All three solutions are one‑liners.**  
> Add a few comments if you’re sharing them in a live coding session.

### Java

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    public boolean checkTree(TreeNode root) {
        // The tree always has root, left, and right.
        return root.val == root.left.val + root.right.val;
    }
}
```

### Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def checkTree(self, root: TreeNode) -> bool:
        # Simple and fast – just one comparison
        return root.val == root.left.val + root.right.val
```

### C++

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *l, TreeNode *r) : val(x), left(l), right(r) {}
 * };
 */
class Solution {
public:
    bool checkTree(TreeNode* root) {
        // Direct comparison – O(1)
        return root->val == root->left->val + root->right->val;
    }
};
```

---

## Edge Cases & Common Pitfalls

| Issue | Why it happens | Fix |
|-------|----------------|-----|
| `NullPointerException` / `AttributeError` | Forgetting that left/right may be `None` | Add defensive checks: `root.left != null && root.right != null` (not needed per constraints but safe) |
| Wrong operator precedence | Mis‑reading `==` as assignment | Ensure `==` is used, not `=` |
| Returning the wrong type | Returning an integer or string instead of boolean | Return `true/false` or `True/False` explicitly |

> **Pro Tip** – If you’re working in an interview environment where the constraints aren’t guaranteed, you can safely guard:

```java
if (root == null || root.left == null || root.right == null) return false;
```

---

## Complexity Analysis

| Metric | Value |
|--------|-------|
| **Time** | **O(1)** – only three accesses |
| **Space** | **O(1)** – no auxiliary data structures |

---

## How to Talk About It in an Interview

1. **State the constraints first**: “The tree has exactly three nodes – root, left, right.”
2. **Explain the observation**: “Because we always have both children, we don’t need a traversal.”
3. **Show the solution**: “Just compare `root.val` with `root.left.val + root.right.val`.”
4. **Mention complexity**: “O(1) time and space.”
5. **Ask for clarification**: “Should we handle the case where a child is missing?” – this signals you’re thoughtful.

---

## FAQ & Quick Reference

| Question | Answer |
|----------|--------|
| **Can I use recursion?** | Yes, but it’s unnecessary and adds overhead. |
| **What if the tree had more nodes?** | Then you’d need a traversal (DFS/BFS) to compute sums. |
| **Is this problem often asked?** | In coding interviews for junior positions, yes. |
| **How to handle null children in Python?** | Use `if root.left and root.right` guard. |
| **Is this a “warm‑up” or “challenge” problem?** | Warm‑up. |

---

## Wrap‑Up & Takeaway

- **Root Equals Sum of Children** is the *definition of a concise coding interview question*.  
- The optimal solution is a **one‑liner** in any language.  
- Knowing how to *recognize* that you can shortcut a generic approach is a key skill in interviews.  
- Share the code snippet confidently; it shows you can spot patterns and optimize quickly.

> 🚀 **Next step:** Implement the solution in your preferred language, add unit tests, and practice explaining it aloud. A clear, fast answer will make a great first impression on hiring managers looking for *efficient problem‑solvers*.  

Happy coding and good luck on your interview journey!