---
title: LeetCode 1123. Lowest Common Ancestor of Deepest Leaves - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1123. Lowest Common Ancestor of Deepest Leaves  
**Difficulty:** Medium  

### TL;DR  
- **Goal:** Return the lowest common ancestor (LCA) of all deepest leaf nodes in a binary tree.  
- **Core idea:** Do a single DFS that returns **(sub‑LCA, depth)** for every subtree.  
- **Complexity:** O(N) time, O(H) space (recursion stack).  

Below you’ll find ready‑to‑paste, production‑ready solutions in **Java**, **Python**, and **C++**, followed by a fully‑SEO‑optimized blog article that explains the good, the bad, and the ugly of this problem—perfect for impressing recruiters during your next interview.

---

## Code – 3 Languages

> **Tip:** All three implementations assume the standard LeetCode `TreeNode` definition.  
> **Feel free** to copy the code into your local IDE or LeetCode sandbox.

### Java

```java
// Definition for a binary tree node.
public class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}

public class Solution {
    // Public entry point
    public TreeNode lcaDeepestLeaves(TreeNode root) {
        return dfs(root).node;
    }

    // Helper returns a pair of (LCA, depth)
    private Result dfs(TreeNode node) {
        if (node == null) {
            return new Result(null, 0);          // depth of null is 0
        }

        Result left  = dfs(node.left);
        Result right = dfs(node.right);

        // If one side is deeper, propagate that side's LCA upward
        if (left.depth > right.depth) {
            return new Result(left.node, left.depth + 1);
        }
        if (left.depth < right.depth) {
            return new Result(right.node, right.depth + 1);
        }

        // Same depth – current node is the LCA
        return new Result(node, left.depth + 1);
    }

    // Simple container to keep the pair together
    private static class Result {
        TreeNode node;
        int depth;
        Result(TreeNode node, int depth) {
            this.node = node;
            this.depth = depth;
        }
    }
}
```

### Python

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val: int = 0, left: 'TreeNode' = None, right: 'TreeNode' = None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def lcaDeepestLeaves(self, root: TreeNode) -> TreeNode:
        return self.dfs(root)[0]          # 0 → node, 1 → depth

    def dfs(self, node: TreeNode):
        if not node:
            return (None, 0)              # (LCA, depth)

        left_lca, left_depth   = self.dfs(node.left)
        right_lca, right_depth = self.dfs(node.right)

        if left_depth > right_depth:
            return (left_lca, left_depth + 1)
        if left_depth < right_depth:
            return (right_lca, right_depth + 1)

        # equal depths => current node is the LCA
        return (node, left_depth + 1)
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
 * };
 */
class Solution {
public:
    TreeNode* lcaDeepestLeaves(TreeNode* root) {
        return dfs(root).first;  // first → LCA, second → depth
    }

private:
    pair<TreeNode*, int> dfs(TreeNode* node) {
        if (!node) return {nullptr, 0};

        auto left  = dfs(node->left);
        auto right = dfs(node->right);

        if (left.second > right.second)
            return {left.first, left.second + 1};
        if (left.second < right.second)
            return {right.first, right.second + 1};

        return {node, left.second + 1};
    }
};
```

---

## Blog Article  
### Title  
**“Mastering LeetCode 1123: Lowest Common Ancestor of Deepest Leaves – The Good, the Bad, and the Ugly”**

---

### Introduction (SEO‑optimized meta description)

> *Looking to ace your next software‑engineering interview? This deep‑dive into LeetCode 1123 (Lowest Common Ancestor of Deepest Leaves) explains the most efficient algorithm, walks you through Java/Python/C++ code, and breaks down the problem’s nuances. Whether you’re a junior developer or senior architect, master this “good, bad, ugly” guide and boost your interview success.*

---

### Table of Contents  

1. [Problem Overview](#problem-overview)  
2. [The Good – Why It’s a Worth‑while Challenge](#the-good)  
3. [The Bad – Common Pitfalls and Misconceptions](#the-bad)  
4. [The Ugly – Edge Cases and Gotchas](#the-ugly)  
5. [Algorithmic Insight – Depth‑First Search + Pair](#algorithmic-insight)  
6. [Complexity Analysis](#complexity)  
7. [Code Walkthrough – Java, Python, C++](#code)  
8. [Alternative Approaches & Why They’re Less Efficient](#alternatives)  
9. [Interview Tips & Talking Points](#interview-tips)  
10. [Final Take‑away & Further Reading](#final)

---

#### Problem Overview <a name="problem-overview"></a>

LeetCode 1123 asks you to **return the lowest common ancestor (LCA) of all deepest leaves** in a binary tree.  
- **Leaf**: Node with no children.  
- **Depth of root**: 0.  
- **LCA**: Node deepest in the tree that contains all deepest leaves in its subtree.

The task is a twist on the classic LCA problem because the set of target nodes isn’t known upfront – it’s the *deepest* leaves.

---

#### The Good <a name="the-good"></a>

| What’s Good | Why It Matters |
|-------------|----------------|
| **Single Pass Solution** | One DFS gives you both the deepest depth and the LCA in O(N). |
| **Elegant Use of Recursion** | Return a tuple/pair → clear code, no global state. |
| **Scales with Height** | Works for skewed trees (worst‑case O(N) depth). |
| **Reusable Skeleton** | The same pattern solves LeetCode 865 (Smallest Subtree with All Deepest Nodes). |

> *In interviews, recruiters love solutions that are concise yet powerful. This problem is a perfect showcase.*

---

#### The Bad <a name="the-bad"></a>

| Common Mistakes | Fix |
|-----------------|-----|
| **Counting leaves instead of depths** | Depth is the key, not the leaf count. |
| **Using two separate DFS passes** | You can get depth and LCA in one traversal. |
| **Assuming a balanced tree** | Recursion depth can be up to N (1000), still fine but beware stack overflow in languages like Python with low recursion limits. |
| **Returning wrong value for empty subtree** | Return depth `0` and node `null`. |

---

#### The Ugly <a name="the-ugly"></a>

| Edge Cases | How to Handle |
|------------|---------------|
| **Single node tree** | LCA is the root itself. |
| **All leaves at same depth** | Current node becomes LCA if left/right depths equal. |
| **Unbalanced tree** | Recursion stack may hit Python’s default limit (~1000). Use `sys.setrecursionlimit()` if needed. |
| **Large depth but small width** | Algorithm remains linear; only stack size grows. |

---

#### Algorithmic Insight <a name="algorithmic-insight"></a>

1. **DFS(node)** → returns **(sub‑LCA, depth)**.  
2. For each node, compute left and right results.  
3. **Three cases**:  
   * Left deeper → propagate left LCA upward.  
   * Right deeper → propagate right LCA upward.  
   * Equal depth → current node is the LCA.  
4. **Base case**: `null` → `(null, 0)`.

Why it works?  
- The *depth* tells you *how far* you are from the deepest leaves.  
- When two subtrees are equally deep, the lowest common ancestor must be the current node because both deepest leaves are in separate subtrees.

---

#### Complexity Analysis <a name="complexity"></a>

| Metric | Calculation |
|--------|-------------|
| **Time** | Every node visited once → **O(N)** |
| **Space** | Recursion stack up to tree height **H** → **O(H)** (≤ N for worst‑case skewed tree). |

---

#### Code Walkthrough – Java, Python, C++ <a name="code"></a>

> *All three code samples above are annotated. Pick the language you’re most comfortable with, run the tests, and you’ll pass in 15 minutes.*

- **Java** uses a static inner class `Result` to keep `(node, depth)` together.  
- **Python** returns a tuple; the recursion depth is automatically handled by Python’s interpreter.  
- **C++** returns a `std::pair<TreeNode*, int>` for minimal overhead.

---

#### Alternative Approaches & Why They’re Less Efficient <a name="alternatives"></a>

| Approach | Complexity | Drawback |
|----------|------------|----------|
| **Two‑pass BFS** | First pass to find max depth, second to find LCA via ancestor set. | O(N) time *but* extra O(N) memory for storing ancestor paths. |
| **Level‑by‑level traversal** | Collect all deepest leaves, then run a generic LCA on that list. | Requires extra data structures, multiple passes, O(N) extra space. |
| **Iterative Post‑order** | Avoid recursion but still needs a stack that holds the pair for each node. | More code verbosity; no real benefit over recursion for N ≤ 1000. |

> *A single DFS that returns a pair is the “Gold Standard.”*  

---

#### Interview Tips & Talking Points <a name="interview-tips"></a>

1. **Explain the pair idea first** – recruiters appreciate a clear problem‑solution map.  
2. **State the base case explicitly** – depth of `null` is `0`.  
3. **Show you understand recursion limits** – in Python, mention `sys.setrecursionlimit(2000)` for safety.  
4. **Highlight O(N) time** – contrast with naive solutions.  
5. **Connect to Problem 865** – if asked, “Do you know a similar problem?” you can mention the reuse of the algorithm.  

---

#### Final Take‑away & Further Reading <a name="final"></a>

- **One pass DFS** that returns a pair gives you both the deepest depth *and* the LCA in linear time.  
- This pattern is a reusable building block for many “deepest‑node” problems.  
- Master the nuance between *leaf count* and *depth* – that’s where most interviewers spot weak solutions.  
- **Further reading**:  
  - LeetCode 865 – Smallest Subtree with All Deepest Nodes  
  - “Cracking the Coding Interview – Chapter 4 (Trees & Graphs)”  
  - “Algorithms + Data Structures” by Goodrich & Tamassia (DFS patterns)

> *Now you’re ready to walk into that interview, explain the “good, bad, ugly” points, and walk away with the LCA of all deepest leaves under your belt.*  

--- 

**Happy coding, and may your next interview be a perfect match for your skill set!**