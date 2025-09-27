---
title: LeetCode 1932. Merge BSTs to Create Single BST - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1932 – Merge BSTs to Create a Single BST  
**Hard | Hash‑Map | DFS | In‑Order Validation**

---

## TL;DR  
- Find the “true” root – the tree whose root value never appears as a child in any other tree.  
- Store all trees in a hash‑map (`rootValue → TreeNode`).  
- Recursively walk from the true root and replace any leaf whose value matches a key in the map with the corresponding whole subtree.  
- After the walk, if more than one tree remains in the map → **impossible**.  
- Validate the merged tree with an in‑order traversal – the values must be strictly increasing.  
- If valid, return the merged root; otherwise return `null`.

The algorithm runs in **O(n)** time (n = number of trees) and uses **O(n)** extra space for the map and recursion stack.

---

## Full Code (Java, Python, C++)

> **Tip:** The `TreeNode` definition is the same across all three languages – only the syntax changes.

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
    public TreeNode canMerge(List<TreeNode> trees) {
        if (trees.size() == 1) return trees.get(0);

        // 1. Build hash‑map: root value -> TreeNode
        Map<Integer, TreeNode> map = new HashMap<>();
        for (TreeNode t : trees) map.put(t.val, t);

        // 2. Find the real root – the one whose value is never a child
        Set<Integer> children = new HashSet<>();
        for (TreeNode t : trees) {
            if (t.left != null)  children.add(t.left.val);
            if (t.right != null) children.add(t.right.val);
        }

        TreeNode root = null;
        for (TreeNode t : trees) {
            if (!children.contains(t.val)) {
                if (root != null) return null;   // multiple candidates → impossible
                root = t;
            }
        }
        if (root == null) return null;           // no root found

        // 3. Merge recursively
        merge(root, map);

        // 4. All trees must be merged
        if (map.size() > 1) return null;

        // 5. Validate BST property
        List<Integer> inorder = new ArrayList<>();
        inorderTraversal(root, inorder);
        for (int i = 1; i < inorder.size(); ++i) {
            if (inorder.get(i) <= inorder.get(i - 1)) return null;
        }

        return root;
    }

    private void merge(TreeNode node, Map<Integer, TreeNode> map) {
        if (node == null) return;

        if (node.left != null && map.containsKey(node.left.val)) {
            node.left = map.remove(node.left.val);
            merge(node.left, map);
        }
        if (node.right != null && map.containsKey(node.right.val)) {
            node.right = map.remove(node.right.val);
            merge(node.right, map);
        }
    }

    private void inorderTraversal(TreeNode node, List<Integer> res) {
        if (node == null) return;
        inorderTraversal(node.left, res);
        res.add(node.val);
        inorderTraversal(node.right, res);
    }
}
```

---

### Python

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def canMerge(self, trees: List[TreeNode]) -> Optional[TreeNode]:
        if len(trees) == 1:
            return trees[0]

        # Map root value -> TreeNode
        tree_map = {t.val: t for t in trees}

        # Collect all child values
        children = set()
        for t in trees:
            if t.left:
                children.add(t.left.val)
            if t.right:
                children.add(t.right.val)

        # Find the unique root
        roots = [t for t in trees if t.val not in children]
        if len(roots) != 1:
            return None
        root = roots[0]

        # Recursive merge
        def merge(node: TreeNode):
            if node is None:
                return
            if node.left and node.left.val in tree_map:
                node.left = tree_map.pop(node.left.val)
                merge(node.left)
            if node.right and node.right.val in tree_map:
                node.right = tree_map.pop(node.right.val)
                merge(node.right)

        merge(root)

        # All trees must be merged
        if tree_map:
            return None

        # Validate BST by inorder
        inorder = []
        def dfs(node: TreeNode):
            if node is None:
                return
            dfs(node.left)
            inorder.append(node.val)
            dfs(node.right)

        dfs(root)
        if any(inorder[i] <= inorder[i-1] for i in range(1, len(inorder))):
            return None

        return root
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
    TreeNode* canMerge(vector<TreeNode*>& trees) {
        if (trees.size() == 1) return trees[0];

        // Map root value -> TreeNode
        unordered_map<int, TreeNode*> mp;
        for (auto t : trees) mp[t->val] = t;

        // Collect child values
        unordered_set<int> child;
        for (auto t : trees) {
            if (t->left)  child.insert(t->left->val);
            if (t->right) child.insert(t->right->val);
        }

        // Find the unique root
        TreeNode* root = nullptr;
        for (auto t : trees) {
            if (child.find(t->val) == child.end()) {
                if (root) return nullptr;   // more than one candidate
                root = t;
            }
        }
        if (!root) return nullptr;

        // Recursive merge
        function<void(TreeNode*)> merge = [&](TreeNode* node) {
            if (!node) return;
            if (node->left && mp.count(node->left->val)) {
                node->left = mp[node->left->val];
                mp.erase(node->left->val);
                merge(node->left);
            }
            if (node->right && mp.count(node->right->val)) {
                node->right = mp[node->right->val];
                mp.erase(node->right->val);
                merge(node->right);
            }
        };
        merge(root);

        // All trees must be merged
        if (!mp.empty()) return nullptr;

        // Validate BST by inorder
        vector<int> inorder;
        function<void(TreeNode*)> dfs = [&](TreeNode* node) {
            if (!node) return;
            dfs(node->left);
            inorder.push_back(node->val);
            dfs(node->right);
        };
        dfs(root);

        for (size_t i = 1; i < inorder.size(); ++i)
            if (inorder[i] <= inorder[i-1]) return nullptr;

        return root;
    }
};
```

---

## Blog Post (SEO‑Optimized)

> **Title:** *From “Merge” to “Master” – Solving LeetCode 1932 (Merge BSTs) and Boosting Your Data‑Structure Skills*

---

### 1. Introduction

When you’re preparing for a senior software‑engineering interview, it’s rare that you’ll be asked to solve a problem that combines *hash‑maps*, *tree recursion*, and *BST validation* all at once. LeetCode’s **1932 – Merge BSTs to Create a Single BST** is one of those hidden gems. It’s marked “Hard” for a reason: the constraints (5 × 10⁴ trees, each with ≤ 3 nodes) demand a linear solution that is also easy to reason about.

In this post we’ll walk through:

| Keyword | Why it matters |
|--------|----------------|
| `merge bst` | Core problem concept |
| `hash map` | O(1) lookup is critical |
| `dfs` | Depth‑first traversal to glue trees |
| `inorder validation` | Quick way to test BST property |
| `time complexity O(n)` | Interviewers love linear solutions |

---

### 2. Problem Overview

> **Goal**: Combine a list of small binary trees into **one** binary search tree (BST), or determine that it’s impossible.

**Input**  
- `trees`: list of TreeNode pointers.  
- Every tree’s root value is unique.  
- Each tree has at most 3 nodes (root + 0/1/2 children).

**Operations allowed**  
- Pick a tree as the **true root** (the one that never appears as a child).  
- If a leaf’s value matches another tree’s root value, replace that leaf with the *entire* subtree.  
- Each tree can be used at most once.

**Return**  
- The merged root if a single valid BST can be formed, otherwise `null` (or `None` / `nullptr`).

---

### 3. Key Challenges

| Challenge | Why it’s tricky |
|-----------|-----------------|
| Identifying the *real* root | A naive approach could mis‑pick a subtree as the root. |
| Avoiding “double‑merge” | A leaf could match multiple subtrees if the input is malformed. |
| Efficient lookup | Repeatedly searching for a child’s value in an array would be O(n²). |
| Validating the BST property after merging | A tree might look fine structurally but violate the order rule. |

---

### 4. Approach (Step‑by‑Step)

1. **Hash‑Map Construction**  
   Map every root value to its TreeNode (`rootValue → node`).  
   *Why?* Allows constant‑time “is this a subtree?” checks during merging.

2. **Find the True Root**  
   - Gather all child values in a set.  
   - Scan all trees; the one whose root value is *not* in the child set is the real root.  
   - If there’s more than one candidate or none, merging is impossible.

3. **Recursive Merge**  
   Starting from the true root, traverse the tree.  
   Whenever you encounter a leaf whose value exists in the map, replace that leaf with the mapped subtree and remove the entry from the map.  
   Recurse into the newly inserted subtree.  

4. **Check Completeness**  
   After the traversal, the map should contain only the true root. If any trees remain → impossible.

5. **Validate BST**  
   Perform an in‑order traversal to collect node values.  
   If the list is not strictly increasing → the merged tree violates BST rules → return `null`.

---

### 5. Implementation Highlights

| Language | Special Notes |
|----------|----------------|
| **Java** | Uses `List<TreeNode>` from LeetCode; recursion depth is safe (≤ 3 × 5 000). |
| **Python** | Uses dictionary (`{}`) and set (`set()`) – fast O(1) ops. |
| **C++** | Uses `unordered_map` and `unordered_set`; lambda recursion with `std::function`. |

---

### 6. Time & Space Complexity

| Metric | Formula | Explanation |
|--------|---------|-------------|
| **Time** | **O(n)** | Each tree is processed a constant number of times (construction, root detection, merging, validation). |
| **Space** | **O(n)** | The hash‑map stores one entry per tree; recursion stack depth ≤ 3. |

---

### 7. Edge Cases Covered

- **Multiple real roots** → impossible.  
- **No real root** → impossible.  
- **Unmerged trees left** → impossible.  
- **In‑order not strictly increasing** → invalid BST.  
- **Single tree input** → trivially returned.

---

### 8. “Good, Bad & Ugly” – Common Pitfalls

| Phase | Good | Bad | Ugly |
|-------|------|-----|------|
| **Map building** | O(1) lookup | Forgetting to insert *every* tree → KeyError/NullPointer | Using a list and linear search → O(n²) |
| **Root detection** | Single‑pass set of children | Multiple candidates → silent accept (bug) | Overlooking that root could be *leaf* only (empty tree) |
| **Merging** | Recursively pop from map | Not removing used keys → infinite recursion | Modifying the original input tree structure in place (side‑effects) |
| **Validation** | In‑order traversal | Comparing equal values incorrectly (`>=` instead of `>`) | Forgetting to traverse *all* nodes (skipping a null child) |

---

### 9. Testing Strategy

1. **Basic success cases**  
   - Two trees where one is a direct child.  
   - Three trees forming a perfect BST.

2. **Failure due to multiple roots**  
   - Two trees that are independent (no child link).

3. **Failure due to leftover trees**  
   - A leaf that doesn’t match any other root.

4. **Failure due to BST violation**  
   - Merge produces a tree where left child > parent.

5. **Large input**  
   - 5 × 10⁴ trees, each with 3 nodes – verify runtime < 1 s.

---

### 10. SEO & Career Advice

| Keyword | Placement |
|---------|-----------|
| “Merge BSTs” | Title, 1st paragraph, H2 headings |
| “LeetCode 1932” | Meta description, first line |
| “Hard BST problem” | H3 headings |
| “Interview data‑structure” | Conclusion |
| “Java/Python/C++ solution” | Code section titles |

**Why this matters for your career:**  
- Mastering hash‑maps and tree recursion demonstrates deep data‑structure knowledge – a hallmark of senior‑level roles.  
- LeetCode’s “hard” problems are the bread‑and‑butter of tech‑interviews at Google, Amazon, and FAANG‑level companies.  
- A clean, well‑commented solution shows professionalism and code‑readability—qualities hiring managers value.

---

## Conclusion

Solving **LeetCode 1932 – Merge BSTs to Create a Single BST** is a great way to sharpen your hash‑map intuition, practice DFS, and reinforce BST validation techniques. The linear algorithm above is both elegant and efficient, making it a solid talking point in your next interview.

If you’ve already nailed this problem, consider practicing related problems:  
- **2105. Replace All Digits with the Sum of Digit Subsets** (similar tree merging).  
- **1307. Verbal Arithmetic Puzzle** (hash‑map + backtracking).  

Happy coding and best of luck on your interview journey!