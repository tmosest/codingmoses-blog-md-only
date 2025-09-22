---
title: LeetCode 333. Largest BST Subtree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Largest BST Subtree – LeetCode 333  
**A Deep‑Dive, O(n) Solution + Code in Java, Python & C++ + SEO‑Ready Blog Post**

---

### 🎯 Problem Summary

Given the root of a binary tree, find the size (number of nodes) of the **largest subtree that is also a Binary Search Tree (BST)**.  
A subtree must contain all of its descendants, i.e. you cannot drop nodes in the middle.

| Key point | Description |
|-----------|-------------|
| **BST rule** | For every node, all values in the left subtree < node.val < all values in the right subtree |
| **Goal** | Return the *maximum* number of nodes among all such BST subtrees |
| **Constraints** | `0 ≤ n ≤ 10⁴`, `-10⁴ ≤ node.val ≤ 10⁴` |
| **Follow‑up** | Achieve O(n) time, O(n) space (recursive stack) |

---

## 📌 Optimal O(n) Approach

Traverse the tree once (post‑order).  
At each node compute **four pieces of information** for the subtree rooted at that node:

| Field | Meaning |
|-------|---------|
| `isBST` | Does this subtree satisfy BST properties? |
| `size` | Number of nodes in this subtree (if it is a BST) |
| `minVal` | Minimum value in this subtree |
| `maxVal` | Maximum value in this subtree |

### Recurrence

1. **Base Case** – An empty child is considered a BST with size 0, min = +∞, max = -∞.
2. For a node `root`:
   - Recursively get `left` and `right`.
   - `root` is a BST **iff**  
     `left.isBST && right.isBST && left.maxVal < root.val && root.val < right.minVal`.
   - If it is a BST:  
     - `size = left.size + right.size + 1`  
     - `minVal = min(root.val, left.minVal)`  
     - `maxVal = max(root.val, right.maxVal)`
   - Otherwise, mark `isBST = false` and keep `size = max(left.size, right.size)` (largest BST found deeper).

The answer is the largest `size` encountered during the traversal.

This runs in **O(n)** time because every node is visited once, and uses O(n) space for recursion (or O(h) if tail‑recursion is eliminated).

---

## 🧑‍💻 Code Implementations

### 1. Java

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int x) { val = x; }
 * }
 */
class Solution {
    private static class Info {
        boolean isBST;
        int size;        // size of the BST if isBST == true
        int min;         // min value in this subtree
        int max;         // max value in this subtree
        Info(boolean isBST, int size, int min, int max) {
            this.isBST = isBST;
            this.size   = size;
            this.min    = min;
            this.max    = max;
        }
    }

    private int ans = 0;

    public int largestBSTSubtree(TreeNode root) {
        dfs(root);
        return ans;
    }

    private Info dfs(TreeNode node) {
        if (node == null) {
            // Empty subtree: valid BST of size 0
            return new Info(true, 0, Integer.MAX_VALUE, Integer.MIN_VALUE);
        }

        Info left  = dfs(node.left);
        Info right = dfs(node.right);

        // Check BST condition for the current node
        if (left.isBST && right.isBST
                && left.max < node.val && node.val < right.min) {
            int sz  = left.size + right.size + 1;
            int mn  = Math.min(node.val, left.min);
            int mx  = Math.max(node.val, right.max);
            ans = Math.max(ans, sz);
            return new Info(true, sz, mn, mx);
        }

        // Not a BST: propagate the best size found so far
        ans = Math.max(ans, Math.max(left.size, right.size));
        return new Info(false, 0, 0, 0);   // size is irrelevant here
    }
}
```

---

### 2. Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def largestBSTSubtree(self, root: TreeNode) -> int:
        self.ans = 0

        def dfs(node):
            if not node:
                # empty subtree: BST of size 0
                return (True, 0, float('inf'), float('-inf'))

            left_is_bst, left_size, left_min, left_max = dfs(node.left)
            right_is_bst, right_size, right_min, right_max = dfs(node.right)

            # current node forms a BST?
            if left_is_bst and right_is_bst and left_max < node.val < right_min:
                sz = left_size + right_size + 1
                mn = min(node.val, left_min)
                mx = max(node.val, right_max)
                self.ans = max(self.ans, sz)
                return (True, sz, mn, mx)
            else:
                self.ans = max(self.ans, left_size, right_size)
                return (False, 0, 0, 0)

        dfs(root)
        return self.ans
```

---

### 3. C++

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 * };
 */
class Solution {
public:
    struct Info {
        bool isBST;
        int size;   // size if isBST==true
        int minV;
        int maxV;
        Info(bool b, int s, int mn, int mx) : isBST(b), size(s), minV(mn), maxV(mx) {}
    };

    int largestBSTSubtree(TreeNode* root) {
        int ans = 0;
        dfs(root, ans);
        return ans;
    }

private:
    Info dfs(TreeNode* node, int& ans) {
        if (!node)
            return Info(true, 0, INT_MAX, INT_MIN); // empty tree

        Info left  = dfs(node->left, ans);
        Info right = dfs(node->right, ans);

        if (left.isBST && right.isBST &&
            left.maxV < node->val && node->val < right.minV) {

            int sz = left.size + right.size + 1;
            ans = max(ans, sz);
            int mn = min(node->val, left.minV);
            int mx = max(node->val, right.maxV);
            return Info(true, sz, mn, mx);
        }

        ans = max(ans, max(left.size, right.size));
        return Info(false, 0, 0, 0);   // size is irrelevant when not a BST
    }
};
```

---

## 🧩 What Makes This Solution *Good, Bad, Ugly*?

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time complexity** | O(n) – visits each node once | N/A | None |
| **Space complexity** | O(n) recursion stack (O(h) if tail‑optimised) | N/A | None |
| **Readability** | Uses a clear `Info`/tuple return; inline comments | Slightly verbose in Java due to helper class | None |
| **Edge Cases** | Handles empty tree, negative values, duplicate values, deep trees | N/A | None |
| **Pitfall** | Forgetting to initialise `min`/`max` for null children → wrong comparison | Over‑complicating with extra data structures | None |

### Common Gotchas

1. **Incorrect sentinel values** – Use `+∞` for min on empty child and `-∞` for max. In Java: `Integer.MAX_VALUE / Integer.MIN_VALUE`.  
2. **Wrong min/max updates** – For a BST node, `min` is the smaller of its own value and the left subtree's min; `max` is the larger of its own value and the right subtree's max.  
3. **Neglecting to propagate the best size** – Even when a subtree isn’t a BST, its children might contain the largest BST; keep the best size seen so far.

---

## 📚 Extra Resources & Variations

- **In‑order traversal check** – A BST’s in‑order sequence must be strictly increasing.  
- **Dynamic programming on trees** – Similar pattern used for subtree diameter, largest BST in binary tree, subtree sum problems.  
- **Iterative post‑order** – Use an explicit stack to avoid recursion stack overflow for extremely deep trees.  

---

## 🎯 Interview Take‑Away

> *"Explain how you would find the largest BST subtree in a binary tree, and why you’d use a post‑order traversal."*

Key talking points:
- Post‑order ensures children’s info is available before processing the parent.
- `Info` pattern reduces the number of passes through the tree.
- Discuss sentinel values and why `+∞` / `-∞` are safe regardless of node values.

---

## 📣 Call‑to‑Action

💬 **Want to master more LeetCode interview questions?**  
- **Subscribe** for weekly Java/Python/C++ solutions.  
- **Download** the full “LeetCode 333 – Largest BST Subtree” cheat‑sheet.  
- **Share** this post on LinkedIn or Twitter with #LeetCode333, #BST, #CodingInterview.

---

### 🏁 Summary

- The Largest BST Subtree problem is a classic tree DP problem.  
- A clean post‑order DP that returns `isBST, size, min, max` yields an **O(n)** solution.  
- We provided **production‑ready code** for **Java, Python, and C++** – copy‑paste, run, and impress the hiring manager.  

Good luck with your coding interview, and keep solving!