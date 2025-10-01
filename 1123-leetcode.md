---
title: LeetCode 1123. Lowest Common Ancestor of Deepest Leaves - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Lowest Common Ancestor of Deepest Leaves (LeetCode 1123)  
*The Good • The Bad • The Ugly*  

---

### 📌 Problem Summary  

Given the **root** of a binary tree, find the *lowest common ancestor (LCA)* of all **deepest leaves**.

> **Leaf**: node with no children.  
> **Depth**: root depth = 0, child depth = parent depth + 1.  
> **LCA**: deepest node that has every target node in its subtree.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[3,5,1,6,2,0,8,null,null,7,4]` | `2` | Node `2` is the LCA of deepest leaves `7` & `4`. |
| `[1]` | `1` | The root is the only deepest leaf. |
| `[0,1,3,null,2]` | `2` | Only leaf `2` – its own LCA. |

---

### 🔍 Why This Problem Matters  

- **Interview staple**: appears in many data‑structure interviews (Google, FAANG, etc.).  
- **Tree‑depth + LCA combo**: tests your understanding of recursion, tree traversal, and pair‑return techniques.  
- **SEO**: “Lowest common ancestor of deepest leaves” is a popular query for Java/Python/C++ solutions.  

---

## 1️⃣ Algorithm Overview – The “Good”  

1. **Depth‑first search (DFS)** that returns a *pair*:  
   - `node` – candidate LCA of the deepest leaves in that subtree.  
   - `depth` – maximum depth found in that subtree.

2. **Combine left & right subtrees**  
   - If `left.depth > right.depth` → deepest leaves lie entirely in the left subtree → propagate `left.node`.  
   - If `right.depth > left.depth` → deepest leaves lie entirely in the right subtree → propagate `right.node`.  
   - If equal → deepest leaves are spread across both subtrees → current `root` is the LCA.

3. The recursion bottoms out at `null` → return `(null, 0)`.

**Time Complexity**: `O(N)` – each node visited once.  
**Space Complexity**: `O(H)` – recursion stack, `H` = tree height (≤ N).

---

## 2️⃣ “The Bad” – Common Pitfalls  

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| Returning only depth (int) | Hard to propagate the actual LCA node | Return a pair/tuple (node, depth) |
| Off‑by‑one depth errors | Incorrect LCA when depths equal | Remember to add `+1` when propagating parent depth |
| Mutating input tree | Unintended side‑effects | Use local variables only |
| Not handling `null` children | Null pointer exceptions | Base case returns `(null,0)` |

---

## 3️⃣ “The Ugly” – Edge Cases & Extensions  

| Edge Case | Typical Mistake | Remedy |
|-----------|-----------------|--------|
| Single‑node tree | Assuming root has children | Base case handles `null` and returns root as LCA |
| Unbalanced tree | Stack overflow on deep recursion | Use tail‑recursion or iterative DFS if needed |
| Multiple deepest leaves at same depth but far apart | Wrong LCA | Algorithm already covers this by equality check |
| Duplicate values (though LeetCode guarantees unique) | Misidentifying LCA | Use node references, not values |

---

## 4️⃣ Full Code Implementations  

Below are clean, ready‑to‑run solutions in **Java**, **Python**, and **C++**.  
All use the same DFS‑pair strategy.

---

### Java

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
    // Helper class to carry node and depth
    private static class Pair {
        TreeNode node;
        int depth;
        Pair(TreeNode node, int depth) { this.node = node; this.depth = depth; }
    }

    public TreeNode lcaDeepestLeaves(TreeNode root) {
        return dfs(root).node;
    }

    private Pair dfs(TreeNode root) {
        if (root == null) return new Pair(null, 0);

        Pair left  = dfs(root.left);
        Pair right = dfs(root.right);

        if (left.depth > right.depth)
            return new Pair(left.node, left.depth + 1);
        if (right.depth > left.depth)
            return new Pair(right.node, right.depth + 1);
        // equal depths
        return new Pair(root, left.depth + 1);
    }
}
```

---

### Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def lcaDeepestLeaves(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        # DFS returns (node, depth)
        def dfs(node: Optional[TreeNode]) -> Tuple[Optional[TreeNode], int]:
            if not node:
                return None, 0

            left_node, left_depth  = dfs(node.left)
            right_node, right_depth = dfs(node.right)

            if left_depth > right_depth:
                return left_node, left_depth + 1
            if right_depth > left_depth:
                return right_node, right_depth + 1

            # equal depths
            return node, left_depth + 1

        return dfs(root)[0]
```

---

### C++

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
    TreeNode* lcaDeepestLeaves(TreeNode* root) {
        return dfs(root).first;
    }

private:
    // pair<node, depth>
    pair<TreeNode*, int> dfs(TreeNode* node) {
        if (!node) return {nullptr, 0};

        auto left  = dfs(node->left);
        auto right = dfs(node->right);

        if (left.second > right.second)
            return {left.first, left.second + 1};
        if (right.second > left.second)
            return {right.first, right.second + 1};

        // equal depths
        return {node, left.second + 1};
    }
};
```

---

## 5️⃣ Interview‑Ready Checklist  

| ✅ | Item |
|----|------|
| ✔ | Understand the definition of *deepest leaf* & *lowest common ancestor*. |
| ✔ | Explain DFS pair technique in 2–3 sentences. |
| ✔ | Mention `O(N)` time, `O(H)` space. |
| ✔ | Cover edge cases: single node, skewed tree, equal depths. |
| ✔ | Discuss potential pitfalls (off‑by‑one, wrong base case). |
| ✔ | Show a quick coding sketch (Java/Python/C++) in the interview. |
| ✔ | Bonus: mention iterative solution using a stack if recursion depth worries. |

---

## 📈 SEO Meta & Keywords  

- **Title**: Lowest Common Ancestor of Deepest Leaves – Java/Python/C++ Solutions (LeetCode 1123)  
- **Meta Description**: Master LeetCode 1123 with clean Java, Python, and C++ implementations. Learn DFS pair strategy, time/space complexity, and interview tips.  
- **Keywords**: lowest common ancestor, deepest leaves, leetcode 1123, binary tree, DFS, interview question, job interview, Java solution, Python solution, C++ solution  

---  

### 🎯 Final Thought  

The beauty of this problem lies in its **simplicity**: a single recursive DFS that simultaneously tracks depth and candidate LCA. By returning a pair, you avoid multiple traversals and handle all edge cases gracefully. Mastering this pattern not only clears LeetCode 1123 but also equips you with a powerful tool for any tree‑based interview question. Good luck, and may the LCA be ever in your favor!