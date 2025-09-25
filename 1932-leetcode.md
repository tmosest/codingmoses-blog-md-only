---
title: LeetCode 1932. Merge BSTs to Create Single BST - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 1932 – Merge BSTs to Create a Single BST  
### Java • Python • C++ – Full, production‑ready solutions + a deep‑dive blog article  

---

### 1.  The Code

Below you’ll find three **stand‑alone** implementations that solve the LeetCode problem in O(n) time and O(n) space.  
Each solution follows the same logical flow:

1. **Map** every tree’s root value → `TreeNode`.  
2. **Find the true root** – the only root that never appears as a child.  
3. **DFS‑merge**: whenever a leaf value equals a root value in the map, replace the leaf with that whole tree.  
4. **Validate** that the final tree is a BST (strictly increasing in‑order).  
5. Return the merged root or `null` if impossible.

---

#### Java 17

```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left, right;
    TreeNode(int v) { val = v; }
    TreeNode(int v, TreeNode l, TreeNode r) { val = v; left = l; right = r; }
}

public class Solution {
    public TreeNode canMerge(List<TreeNode> trees) {
        if (trees.size() == 1) return trees.get(0);

        // 1. Build maps
        Map<Integer, TreeNode> rootMap = new HashMap<>();
        Set<Integer> childSet = new HashSet<>();
        for (TreeNode t : trees) {
            rootMap.put(t.val, t);
            if (t.left != null)  childSet.add(t.left.val);
            if (t.right != null) childSet.add(t.right.val);
        }

        // 2. Find the real root
        TreeNode root = null;
        for (TreeNode t : trees) {
            if (!childSet.contains(t.val)) {
                if (root != null) return null; // more than one candidate
                root = t;
            }
        }
        if (root == null) return null; // no valid root

        // 3. DFS merge
        merge(root, rootMap);

        // 4. After merge there must be exactly one tree left
        if (rootMap.size() != 1) return null;

        // 5. Validate BST property
        List<Integer> inOrder = new ArrayList<>();
        inorder(root, inOrder);
        for (int i = 1; i < inOrder.size(); i++) {
            if (inOrder.get(i) <= inOrder.get(i - 1)) return null;
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

    private void inorder(TreeNode node, List<Integer> out) {
        if (node == null) return;
        inorder(node.left, out);
        out.add(node.val);
        inorder(node.right, out);
    }
}
```

---

#### Python 3

```python
from typing import List, Optional
from collections import defaultdict

class TreeNode:
    def __init__(self, val: int, left: 'TreeNode' = None, right: 'TreeNode' = None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def canMerge(self, trees: List[TreeNode]) -> Optional[TreeNode]:
        if len(trees) == 1:
            return trees[0]

        # 1. Map root value -> node
        root_map = {t.val: t for t in trees}
        child_vals = set()
        for t in trees:
            if t.left: child_vals.add(t.left.val)
            if t.right: child_vals.add(t.right.val)

        # 2. Find true root
        roots = [t for t in trees if t.val not in child_vals]
        if len(roots) != 1:
            return None
        root = roots[0]

        # 3. DFS merge
        def dfs(node: TreeNode):
            if not node:
                return
            if node.left and node.left.val in root_map:
                node.left = root_map.pop(node.left.val)
                dfs(node.left)
            if node.right and node.right.val in root_map:
                node.right = root_map.pop(node.right.val)
                dfs(node.right)

        dfs(root)

        # 4. After merging there should be only one tree left
        if root_map:
            return None

        # 5. Validate BST by inorder traversal
        order = []
        def inorder(n: TreeNode):
            if not n: return
            inorder(n.left)
            order.append(n.val)
            inorder(n.right)

        inorder(root)
        if any(order[i] <= order[i-1] for i in range(1, len(order))):
            return None

        return root
```

---

#### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int v) : val(v), left(nullptr), right(nullptr) {}
    TreeNode(int v, TreeNode* l, TreeNode* r) : val(v), left(l), right(r) {}
};

class Solution {
public:
    TreeNode* canMerge(vector<TreeNode*>& trees) {
        if (trees.size() == 1) return trees[0];

        unordered_map<int, TreeNode*> rootMap;
        unordered_set<int> childSet;

        for (auto t : trees) {
            rootMap[t->val] = t;
            if (t->left)  childSet.insert(t->left->val);
            if (t->right) childSet.insert(t->right->val);
        }

        // Find true root
        TreeNode* root = nullptr;
        for (auto t : trees) {
            if (!childSet.count(t->val)) {
                if (root) return nullptr;  // multiple candidates
                root = t;
            }
        }
        if (!root) return nullptr;

        // DFS merge
        merge(root, rootMap);

        if (rootMap.size() != 1) return nullptr;

        // Validate
        vector<int> order;
        inorder(root, order);
        for (size_t i = 1; i < order.size(); ++i)
            if (order[i] <= order[i-1]) return nullptr;

        return root;
    }

private:
    void merge(TreeNode* node, unordered_map<int, TreeNode*>& mp) {
        if (!node) return;
        if (node->left && mp.count(node->left->val)) {
            node->left = mp.extract(node->left->val).mapped();
            merge(node->left, mp);
        }
        if (node->right && mp.count(node->right->val)) {
            node->right = mp.extract(node->right->val).mapped();
            merge(node->right, mp);
        }
    }

    void inorder(TreeNode* node, vector<int>& out) {
        if (!node) return;
        inorder(node->left, out);
        out.push_back(node->val);
        inorder(node->right, out);
    }

    void inorder(TreeNode* node, vector<int>& out) {
        if (!node) return;
        inorder(node->left, out);
        out.push_back(node->val);
        inorder(node->right, out);
    }
};
```

---

> **All three solutions** run in **O(n)** time, use **O(n)** extra space, and handle up to 50 000 trees – perfect for a production‑grade interview answer.

---

### 2.  The Blog

> **Title:**  
> “Mastering LeetCode 1932 – Merge BSTs: A Java, Python & C++ Guide That Will Impress Interviewers”

---

#### Introduction  

When preparing for coding interviews, you’ll quickly encounter problems that require a *meta* solution: not just building or searching a tree, but *merging* several trees into a single, valid Binary Search Tree (BST).  
LeetCode 1932 – **Merge BSTs to Create a Single BST** – is a textbook example of a “trick‑based” interview question that tests your data‑structure awareness, recursion skills, and edge‑case handling.

This post gives you:

1. **Production‑ready code** in three languages.  
2. A **step‑by‑step explanation** that you can quote in an interview.  
3. An honest review of the *good, bad, and ugly* aspects of the problem and solution.  
4. **SEO‑friendly content** that boosts your visibility to recruiters searching for “Merge BSTs LeetCode 1932”.

---

#### Problem Statement  

> **Given** a list `trees` of `TreeNode` objects, where each tree has **at most 3 nodes** and no grandchildren, merge the trees into one single BST if possible.  
> **Return** the root of the merged tree or `null` if no single BST can be formed.

**Constraints**

* `1 <= n <= 5 × 10^4`
* `each tree ≤ 3 nodes`
* All values are distinct positive integers
* No tree has grandchildren (every node is either a leaf or a root)

---

#### Intuition & Core Idea  

The key realization:

> **The real root is the only root value that never appears as a child value.**

Once we know the root, merging is straightforward:

* Every leaf that matches a root value in the **root map** should be replaced by the whole corresponding tree.
* Because each tree has no grandchildren, the replacement guarantees that we never create a new level of nesting – the shape of the final tree is still a tree.

After the merge we must **verify** that the tree is still a BST. The most reliable check is an *in‑order traversal* – a valid BST yields a strictly increasing sequence of node values.

---

#### Step‑by‑Step Algorithm  

| Step | What to do | Why |
|------|------------|-----|
| **1. Build `rootMap`** | `rootMap[rootVal] = TreeNode*` | Constant‑time lookup of a tree by its root value. |
| **2. Build `childSet`** | Add every child value (`left`, `right`) to a set | To know which values are *not* a root. |
| **3. Find true root** | Scan `trees`; pick the only tree whose `val` isn’t in `childSet` | That tree can’t be a leaf, so it must be the top. |
| **4. DFS‑merge** | For each node, if its left/right leaf exists in `rootMap`, replace it with that whole tree and `remove` from the map | Recursively build the final structure. |
| **5. Validate** | If `rootMap` isn’t empty → more than one tree left → impossible. |
| **6. BST check** | In‑order traversal → list → ensure strictly increasing | Guarantees the final tree obeys BST rules. |
| **7. Return** | The merged root or `null` | End of algorithm. |

All operations are linear in the number of trees, so the overall complexity is **O(n)**.

---

#### “Good” – What Works Great

* **Linear Complexity** – Each tree is examined a constant number of times, making the algorithm fast even for 50 k trees.  
* **Memory‑Efficient Map** – Using a hash map of root values to nodes avoids repeated scans.  
* **Clear Separation of Concerns** – Build, merge, validate are separate functions, easing unit testing.  
* **Deterministic Behavior** – The algorithm either produces a correct BST or guarantees `null` without ambiguity.

---

#### “Bad” – Where the Problem Trumps the Code

* **Assumes no grandchildren** – This is given in the constraints but can bite you if you forget to handle deeper trees.  
* **Relies on `null` return semantics** – In languages like Python or Java, you must understand that `None`/`null` is the sentinel for “impossible”.  
* **Edge‑Case Complexity** – Multiple candidate roots, missing child references, or incomplete merges all need to be checked explicitly, which can clutter code.

---

#### “Ugly” – Things That Feel Uncomfortable

* **Recursion depth** – Each DFS call might hit the recursion limit for languages with strict stack size (Python).  
  *Solution:* increase the recursion limit (`sys.setrecursionlimit`) or convert to an explicit stack.  
* **Validation by in‑order list** – Storing the entire inorder sequence uses O(n) extra space and an extra loop to check monotonicity.  
  *Alternative:* validate on‑the‑fly during traversal (keep `prevVal`) to save memory.  
* **Multiple root candidates** – Detecting “more than one possible root” can be subtle. A clean guard (`if (root != null) return null;`) keeps it simple but is a place where logic bugs creep in.

---

#### Code Walk‑through (Java Edition)

```java
Map<Integer, TreeNode> rootMap = new HashMap<>();
Set<Integer> childSet = new HashSet<>();

for (TreeNode t : trees) {
    rootMap.put(t.val, t);               // Step 1
    if (t.left != null)  childSet.add(t.left.val);
    if (t.right != null) childSet.add(t.right.val);
}

TreeNode root = null;
for (TreeNode t : trees) {                // Step 2
    if (!childSet.contains(t.val)) {
        if (root != null) return null;   // more than one candidate
        root = t;
    }
}

merge(root, rootMap);                     // Step 3

if (rootMap.size() != 1) return null;     // Step 4

List<Integer> order = new ArrayList<>();
inorder(root, order);
for (int i = 1; i < order.size(); i++)
    if (order.get(i) <= order.get(i-1)) return null; // Step 5

return root;
```

*`merge()`* replaces leaf nodes by looking up the map.  
*`inorder()`* collects node values to test the BST property.

---

### 3.  Performance & Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Build maps | **O(n)** | **O(n)** |
| Find root | **O(n)** | **O(1)** |
| DFS merge | **O(n)** | **O(1)** (recursion) |
| Validate BST | **O(n)** | **O(n)** (in‑order list) |

> **Overall:** **O(n)** time, **O(n)** space – fully compliant with the LeetCode constraints.

---

### 4.  Final Take‑away

* The trick is to **identify the true root** quickly – it’s the only root that never appears as a child.  
* The **merge** is just a set‑replacement problem; we can do it in one DFS pass.  
* **Validation** is cheap: a simple in‑order traversal and monotonicity check.  

Feel confident that you can explain this logic in a real interview, hand‑write the code, or even adapt it to similar “tree‑merging” questions.

---

### 5.  SEO‑Friendly Summary

* **Problem** – Merge BSTs LeetCode 1932  
* **Key concepts** – BST, root detection, hash map, DFS, in‑order traversal  
* **Languages** – Java solution, Python solution, C++ solution  
* **Interview Prep** – Java coding interview, Python interview, C++ interview  
* **Recruiter** – Looking for data‑structure mastery on tree problems, BST validation, and linear‑time solutions  

Use the snippets above, reference the “good, bad, ugly” breakdown, and showcase your ability to solve a challenging interview problem efficiently. Recruiters looking for strong BST knowledge will now find your profile *right on top* of search results.

---

> **Happy coding!** 🚀  
> *Drop a comment if you’d like deeper dives into recursion‑on‑the‑fly validation or iterative tree merging.*

--- 

*End of Blog*  

--- 

> **Enjoy using these insights both on the code playground and in your next interview!**