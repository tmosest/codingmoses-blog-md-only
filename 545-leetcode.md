---
title: LeetCode 545. Boundary of Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 545 – Boundary of Binary Tree  
**Medium | Binary Tree | DFS / Recursion | Time O(n) | Space O(h)**  

Below you’ll find fully‑working solutions in **Java, Python, and C++**.  
After the code we’ve written a short “blog‑style” article that explains the algorithm, highlights the good, the bad, and the ugly, and is written with SEO‑optimized keywords that recruiters love to see in your portfolio or GitHub README.

---

### 1. Java Solution

```java
import java.util.ArrayList;
import java.util.List;

/** Definition for a binary tree node. */
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class Solution {

    public List<Integer> boundaryOfBinaryTree(TreeNode root) {
        List<Integer> result = new ArrayList<>();
        if (root == null) return result;

        if (!isLeaf(root)) result.add(root.val);

        // 1. Left boundary (excluding leaves)
        TreeNode cur = root.left;
        while (cur != null) {
            if (!isLeaf(cur)) result.add(cur.val);
            cur = (cur.left != null) ? cur.left : cur.right;
        }

        // 2. All leaves (left‑to‑right)
        addLeaves(root, result);

        // 3. Right boundary (excluding leaves), add in reverse
        List<Integer> rightBoundary = new ArrayList<>();
        cur = root.right;
        while (cur != null) {
            if (!isLeaf(cur)) rightBoundary.add(cur.val);
            cur = (cur.right != null) ? cur.right : cur.left;
        }
        for (int i = rightBoundary.size() - 1; i >= 0; --i)
            result.add(rightBoundary.get(i));

        return result;
    }

    private void addLeaves(TreeNode node, List<Integer> out) {
        if (node == null) return;
        if (isLeaf(node)) {
            out.add(node.val);
            return;
        }
        addLeaves(node.left, out);
        addLeaves(node.right, out);
    }

    private boolean isLeaf(TreeNode node) {
        return node.left == null && node.right == null;
    }
}
```

---

### 2. Python Solution

```python
from typing import List, Optional

# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val: int = 0, left: 'TreeNode' = None, right: 'TreeNode' = None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def boundaryOfBinaryTree(self, root: Optional[TreeNode]) -> List[int]:
        if not root: return []

        res = []

        # Root (if not a leaf)
        if not self._is_leaf(root):
            res.append(root.val)

        # Left boundary (excluding leaves)
        node = root.left
        while node:
            if not self._is_leaf(node):
                res.append(node.val)
            node = node.left if node.left else node.right

        # Leaves
        self._add_leaves(root, res)

        # Right boundary (excluding leaves) – collect then reverse
        right_boundary = []
        node = root.right
        while node:
            if not self._is_leaf(node):
                right_boundary.append(node.val)
            node = node.right if node.right else node.left
        res.extend(reversed(right_boundary))

        return res

    def _add_leaves(self, node: Optional[TreeNode], out: List[int]) -> None:
        if not node:
            return
        if self._is_leaf(node):
            out.append(node.val)
            return
        self._add_leaves(node.left, out)
        self._add_leaves(node.right, out)

    @staticmethod
    def _is_leaf(node: TreeNode) -> bool:
        return node.left is None and node.right is None
```

---

### 3. C++ Solution

```cpp
#include <vector>
using namespace std;

/* Definition for a binary tree node. */
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    vector<int> boundaryOfBinaryTree(TreeNode* root) {
        vector<int> ans;
        if (!root) return ans;

        if (!isLeaf(root)) ans.push_back(root->val);

        // Left boundary (excluding leaves)
        TreeNode* cur = root->left;
        while (cur) {
            if (!isLeaf(cur)) ans.push_back(cur->val);
            cur = (cur->left) ? cur->left : cur->right;
        }

        // Leaves
        addLeaves(root, ans);

        // Right boundary (excluding leaves) – collect then reverse
        vector<int> rightB;
        cur = root->right;
        while (cur) {
            if (!isLeaf(cur)) rightB.push_back(cur->val);
            cur = (cur->right) ? cur->right : cur->left;
        }
        for (auto it = rightB.rbegin(); it != rightB.rend(); ++it)
            ans.push_back(*it);

        return ans;
    }

private:
    bool isLeaf(TreeNode* node) {
        return node->left == nullptr && node->right == nullptr;
    }

    void addLeaves(TreeNode* node, vector<int>& out) {
        if (!node) return;
        if (isLeaf(node)) {
            out.push_back(node->val);
            return;
        }
        addLeaves(node->left, out);
        addLeaves(node->right, out);
    }
};
```

> **Tip:** In all three languages the core idea is the same – *DFS with three passes*:
> 1. Left boundary (top‑to‑bottom, skip leaves)  
> 2. All leaves (left‑to‑right)  
> 3. Right boundary (top‑to‑bottom, skip leaves) but added in reverse order  

---

## 📖 Blog‑Style Write‑up: “Boundary of Binary Tree – Good, Bad & Ugly”

> **Keywords**: *LeetCode 545, Boundary of Binary Tree, binary tree traversal, DFS, interview algorithm, software engineer interview, coding interview, time complexity, space complexity, job interview tips.*

---

### 1️⃣ The Good

| Why this algorithm shines | What recruiters see |
|---------------------------|---------------------|
| **Simplicity** – The solution is just a few loops and a recursive leaf‑collector. No extra state machine or flag logic. | *Clear, readable code* |
| **Linear Time** – Every node is visited at most once → **O(n)**. Perfect for large trees. | *Efficiency claim* |
| **Low Auxiliary Space** – Only recursion depth (`O(h)`) and a few auxiliary lists (`O(h)`). | *Space‑optimized* |
| **Extensible** – You can plug in this routine into any interview or production system that needs a “tree periphery” view. | *Reusable code pattern* |
| **No Double‑Counting** – Leaves are added only once, whether they appear on a boundary or not. | *Bug‑free guarantee* |

> **Recruiter‑friendly takeaway**: *“I can produce clean, O(n) tree‑traversal code that avoids common pitfalls such as double‑counting leaves.”*

---

### 2️⃣ The Bad

| Common pain points | Fix / Avoid |
|--------------------|------------|
| **Null root** – The problem statement guarantees a root, but defensive coding is still wise. | Check for `root == null` early. |
| **Single‑child boundary nodes** – A node with only one child must still be considered part of the boundary. | Use the “if left exists else right” (and vice‑versa for the right boundary). |
| **Root being a leaf** – In that case the boundary is just `[root.val]`. | Skip adding `root` when it is a leaf. |
| **Language quirks** – In Python the recursion limit can hit `sys.setrecursionlimit`, in C++ the pointer‑checks must be careful. | Keep recursion depth in mind, or switch to an iterative stack if `h` could be huge. |

---

### 3️⃣ The Ugly

| When the code looks like a spaghetti monster |
|---------------------------------------------|
| Over‑engineering with “flag” integers (1 = left, 2 = right, 3 = other) and multiple helper methods just to keep track of “where we are” is **unnecessary** for this problem. It makes the code harder to read and more error‑prone. |
| Using a global `vector<int>` and mutating it from deep recursion can be tricky to debug, especially in C++ where manual memory management is involved. |
| Forgetting to skip leaves when adding the left/right boundary often leads to duplicate values in the final list. A “simple‑first” approach that adds the root, left boundary, leaves, right boundary – and then de‑duplicates – is cleaner. |

> **Take‑away**: *Write the simplest DFS that satisfies the boundary rules. Avoid hidden flags or state machines unless the interview explicitly requires them.*

---

### 4️⃣ SEO‑Optimized Summary

If you’re building a GitHub README, a portfolio page, or preparing a **LeetCode solution set** to impress hiring managers, use the following meta‑tags, headings, and keyword‑dense sentences:

- `#LeetCode545`, `#BoundaryOfBinaryTree`, `#BinaryTreeTraversal`, `#DFS`, `#Recursion`, `#InterviewAlgorithm`, `#SoftwareEngineer`, `#CodingInterview`, `#JobInterview`, `#AlgorithmDesign`, `#TimeComplexity`, `#SpaceComplexity`

**Sample README snippet**

```markdown
# Boundary of Binary Tree – LeetCode 545

- **Problem**: Compute the boundary of a binary tree (left boundary, leaves, right boundary) in O(n) time.
- **Languages**: Java, Python, C++.
- **Complexities**: `O(n)` time, `O(h)` auxiliary space (`h` = tree height).
- **Why It Matters**: Demonstrates depth‑first search, edge‑case handling, and clean code – key skills for a software engineer interview.
```

> **Why this matters for your job search**  
> Recruiters search for candidates who can **solve classic tree problems** quickly and with clean code. By adding this problem (LeetCode 545) to your public repo and writing a blog post that explains *good, bad, ugly* insights, you showcase:
> * Problem‑solving skills  
> * Code readability & maintainability  
> * Understanding of time & space trade‑offs  
> * Ability to communicate complex ideas in a digestible format  

---

### 5️⃣ Quick Test Harness (Optional)

```java
public static void main(String[] args) {
    /* Construct the following tree
              1
            /   \
           2     3
          / \     \
         4   5     7
    */
    TreeNode root = new TreeNode(1);
    root.left = new TreeNode(2);
    root.right = new TreeNode(3);
    root.left.left = new TreeNode(4);
    root.left.right = new TreeNode(5);
    root.right.right = new TreeNode(7);

    Solution sol = new Solution();
    System.out.println(sol.boundaryOfBinaryTree(root)); // -> [1, 2, 4, 5, 7, 3]
}
```

---

### 🎯 How to Use These Solutions in Your Interview Portfolio

1. **Upload the three source files** (`Solution.java`, `solution.py`, `solution.cpp`) to a public GitHub repo.  
2. Add the **blog article** as `README.md` or a separate `blog.md` file.  
3. In your LinkedIn or portfolio page, link to the repo and mention *“Solved LeetCode 545 – Boundary of Binary Tree with O(n) DFS”*.  
4. When you apply, add the following snippet to your cover letter or LinkedIn:  

> *“During my recent interview prep, I tackled LeetCode 545 – Boundary of Binary Tree, implementing a clean, linear‑time solution in Java, Python, and C++. The algorithm showcases my ability to perform DFS on binary trees, manage edge‑case logic, and produce production‑ready code.”*

---

**Happy coding & best of luck landing that next software engineering role!** 🚀