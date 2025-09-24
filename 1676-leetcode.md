---
title: LeetCode 1676. Lowest Common Ancestor of a Binary Tree IV - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – Three Languages  

Below you’ll find a **single‑pass, O(N) time / O(H) space** solution to  
LeetCode 1676 – *Lowest Common Ancestor of a Binary Tree IV*.  
The code is written in **Java, Python, and C++** so you can paste it into the
corresponding online judges or your own projects.

> **Why this version?**  
>  * Uses a `HashSet` (or `unordered_set`) for O(1) membership tests.  
>  * Recursively counts how many of the target nodes appear in each subtree.  
>  * The first node that contains all target nodes in its subtree is the LCA.  
>  * No extra data structures (besides the set) – minimal overhead.

---

### Java (LeetCode style)

```java
// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

class Solution {
    private TreeNode ans = null;
    private Set<TreeNode> targetSet;

    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode[] nodes) {
        if (root == null) return null;

        targetSet = new HashSet<>(Arrays.asList(nodes));
        dfs(root);
        return ans;
    }

    // Returns how many target nodes were found in this subtree
    private int dfs(TreeNode node) {
        if (node == null) return 0;

        int count = 0;
        if (targetSet.contains(node)) count++;

        count += dfs(node.left);
        count += dfs(node.right);

        // If this subtree contains all target nodes and we haven't set the answer yet
        if (count == targetSet.size() && ans == null) {
            ans = node;
        }
        return count;
    }
}
```

---

### Python 3

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, x):
        self.val = x
        self.left = None
        self.right = None

class Solution:
    def lowestCommonAncestor(self, root: 'TreeNode', nodes: list['TreeNode']) -> 'TreeNode':
        if not root:
            return None

        target_set = set(nodes)
        self.ans = None

        def dfs(node: TreeNode) -> int:
            if not node:
                return 0
            count = 1 if node in target_set else 0
            count += dfs(node.left)
            count += dfs(node.right)

            if count == len(target_set) and self.ans is None:
                self.ans = node
            return count

        dfs(root)
        return self.ans
```

---

### C++ (GNU‑C++17)

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
    TreeNode* lowestCommonAncestor(TreeNode* root, vector<TreeNode*>& nodes) {
        if (!root) return nullptr;
        unordered_set<TreeNode*> target(nodes.begin(), nodes.end());
        ans = nullptr;
        dfs(root, target);
        return ans;
    }

private:
    TreeNode* ans = nullptr;

    // returns how many target nodes were found in this subtree
    int dfs(TreeNode* node, unordered_set<TreeNode*>& target) {
        if (!node) return 0;

        int cnt = target.count(node) ? 1 : 0;
        cnt += dfs(node->left, target);
        cnt += dfs(node->right, target);

        if (cnt == target.size() && !ans) ans = node;
        return cnt;
    }
};
```

---

## 2.  Blog Article – *The Good, The Bad, and The Ugly of LCA (Multiple Nodes)*

### Title  
**Lowest Common Ancestor of Multiple Nodes in a Binary Tree – A Deep Dive for Interviews**

### Meta‑Description  
Unlock the LeetCode 1676 solution: learn the best algorithm, compare Java/Python/C++ implementations, and master the interview “good, bad, ugly” pitfalls. Perfect for software engineers prepping for data‑structure questions.

---

### 1.  Introduction  

When you think of *Lowest Common Ancestor* (LCA) in binary trees, the classic interview problem is *two* nodes. LeetCode 1676 throws a twist: **given an array of nodes, find the LCA of all of them**.  
It sounds trivial, but the naive solutions you see on the internet often ignore corner cases, blow up memory, or miss the *O(N)* guarantee.

In this article we’ll walk through:

| **Aspect** | **What you’ll learn** |
|------------|-----------------------|
| Good | The O(N) algorithm that works for any number of nodes |
| Bad | Why the “search every pair” approach fails |
| Ugly | Hidden pitfalls: recursion depth, identity vs. value, and memory leaks |

---

### 2.  Problem Restatement  

> **Input** –  
> * `root` – pointer/reference to the root of a binary tree (values are unique).  
> * `nodes[]` – an array of **TreeNode** objects that all exist in the tree.  
>   
> **Output** – the TreeNode that is the lowest common ancestor of *every* node in `nodes[]`.

> **Constraints**  
> * 1 ≤ N ≤ 10⁴ (number of nodes in the tree).  
> * All node values are unique.  
> * All nodes in `nodes[]` are distinct and present in the tree.

---

### 3.  Naïve Approaches (The Bad)

| Approach | Complexity | Why it’s problematic |
|----------|------------|----------------------|
| **Pairwise LCA** – Compute LCA for every pair, then merge | O(k² · h) | k = |nodes| can be up to 10⁴; quadratic blow‑up. |
| **Mark & Sweep** – DFS each target node, store ancestors, intersect | O(k·h + N) | Requires O(k·h) memory for ancestor lists. |
| **Brute DFS with Set at every node** – For each node, collect all targets in its subtree | O(N²) | Re‑counts the same subtrees many times. |

These patterns are common in “first‑draft” solutions you’ll see in blogs or discussion forums. They work for small data but fail on the constraints.

---

### 4.  Optimal Strategy (The Good)

#### 4.1  Core Idea  

During a single depth‑first traversal we keep track of **how many of the target nodes** appear in the current subtree.  
* If a subtree contains *all* targets, then the current node is the LCA.  
* We return the count to the parent so the ancestor can know if its own subtree is a candidate.

This gives us:

* **Time** – Each node is visited once → **O(N)**  
* **Space** – Recursion stack depth → **O(H)** where H is the tree height (≤ N)

#### 4.2  Implementation Details  

| Language | Special Notes |
|----------|---------------|
| **Java** | Use a `HashSet<TreeNode>` for O(1) `contains()` checks. Keep a mutable `ans` field that stores the first node that satisfies the count condition. |
| **Python** | Use a `set(nodes)`; the recursion returns an integer count. Use `nonlocal ans` or a class attribute. |
| **C++** | `unordered_set<TreeNode*> target(nodes.begin(), nodes.end());` Recursion returns the count. Store `ans` as a private member. |

#### 4.3  Why It Works  

Let `k = nodes.length`.  
When we visit a node `x`, we count how many of the `k` targets are in its left and right subtrees, then add 1 if `x` itself is a target.  
* If the sum equals `k`, then every target lies in the subtree rooted at `x`.  
* Because we traverse from leaves upward, the **first** such node encountered is the *lowest* one.  
* Once `ans` is set we simply propagate the count upwards; we never overwrite `ans`.

---

### 5.  Code Walk‑Through (Java Example)

```java
private TreeNode ans = null;          // stores the LCA once found
private Set<TreeNode> targetSet;      // fast membership test

public TreeNode lowestCommonAncestor(TreeNode root, TreeNode[] nodes) {
    if (root == null) return null;
    targetSet = new HashSet<>(Arrays.asList(nodes));
    dfs(root);                        // single DFS pass
    return ans;
}

private int dfs(TreeNode node) {
    if (node == null) return 0;
    int count = targetSet.contains(node) ? 1 : 0;
    count += dfs(node.left);
    count += dfs(node.right);

    if (count == targetSet.size() && ans == null) {
        ans = node;                   // first node that covers all targets
    }
    return count;
}
```

* `dfs` returns the number of targets found in the subtree of `node`.  
* As soon as `ans` is set we stop searching for a deeper candidate.

---

### 6.  Edge Cases & Pitfalls (The Ugly)

| Problem | How to Spot It | Fix |
|---------|----------------|-----|
| **Only one target node** | LCA should be that node itself | The algorithm naturally handles this: `count == 1`. |
| **Node identity vs. value** | Two nodes may have the same `val` but are different objects | Always use the *object identity* (`TreeNode` reference) in the set, not the integer value. |
| **Deep recursion** | Tree height close to N (10⁴) → stack overflow on Java/Python | Use iterative DFS or increase recursion limit (`sys.setrecursionlimit`). |
| **Memory leak in C++** | Forgetting to delete local `unordered_set` if you allocate it inside a helper | Allocate it once in the public method; the helper receives it by reference. |
| **Null pointer checks** | LeetCode guarantees `root != null`, but local `dfs` still must guard against `null` children | All three snippets start with `if (node == null) return 0;`. |

---

### 7.  Interview Take‑Aways  

| Question | Answer |
|----------|--------|
| *What is the time complexity of your solution?* | **O(N)** – one DFS visit per node. |
| *Why do we need a HashSet?* | **O(1)** membership tests; without it we’d be doing O(N²). |
| *How do you guarantee you’re returning the lowest ancestor?* | The first node whose subtree count equals `k` is the lowest because we’re moving from leaves to root. |
| *What about space usage?* | Only recursion stack – **O(H)**, not O(N) unless the tree is degenerate. |

---

### 7.  Summary  

| **Good** | **Bad** | **Ugly** |
|----------|---------|----------|
| One pass DFS, O(N) time, O(H) space | Quadratic pairwise LCA, O(k²·h) | Identity vs. value mistakes, recursion depth limits |
| Uses a `HashSet` for constant‑time membership | Requires multiple traversals | Uninitialized `ans` can lead to wrong answers |

With the three ready‑to‑copy implementations above you can confidently hit the *“LeetCode 1676 – Lowest Common Ancestor of a Binary Tree IV”* on any platform and nail that interview question.

---

### 8.  Final Thought  

> **Practice, practice, practice.**  
> Try writing the DFS yourself from scratch in each language.  
> Then, change the tree (skewed, balanced, random) and run the same tests – you’ll see the linear scaling that matters.

Happy coding—and good luck on your next interview!