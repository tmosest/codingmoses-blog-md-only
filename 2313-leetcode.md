---
title: LeetCode 2313. Minimum Flips in Binary Tree to Get Result - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 2313 – Minimum Flips in Binary Tree to Get Result  
**A Deep‑Dive into a LeetCode Hard Problem (Java, Python & C++)**  

---

## Table of Contents  

| Section | Link |
|---------|------|
| Problem Overview | #problem-overview |
| Quick Solution | #quick-solution |
| Detailed Algorithm | #algorithm |
| Complexity Analysis | #complexity |
| Code – Java | #java |
| Code – Python | #python |
| Code – C++ | #cpp |
| The Good, The Bad, & The Ugly | #good-bad-ugly |
| How This Helps Your Job Hunt | #job-hunt |
| Final Thoughts | #final |

---

## Problem Overview <a name="problem-overview"></a>

You’re given a rooted binary tree where:

| Node Value | Meaning |
|------------|---------|
| **0** | Leaf, evaluates to **false** |
| **1** | Leaf, evaluates to **true** |
| **2** | OR node (two children) |
| **3** | AND node (two children) |
| **4** | XOR node (two children) |
| **5** | NOT node (one child) |

You may **flip any leaf** (0 ↔ 1).  
Your goal: find the **minimum number of flips** that make the root evaluate to a given boolean `result`.  
It’s guaranteed that a solution always exists.

Constraints:  
- 1 ≤ #nodes ≤ 10⁵  
- 0 ≤ node.val ≤ 5

---

## Quick Solution <a name="quick-solution"></a>

**Bottom‑up DFS** that returns, for each node, a 2‑tuple:

```
[flipsNeededToBeFalse, flipsNeededToBeTrue]
```

- For a leaf: 0 flips if its value already matches the desired boolean, otherwise 1.
- For internal nodes: combine the two children’s tuples according to the logic operation.

The answer is `result ? root[1] : root[0]`.

The solution runs in **O(N)** time and uses **O(H)** stack space (H = tree height, ≤ N).

---

## Detailed Algorithm <a name="algorithm"></a>

### 1.  What to compute at each node?

For every node we need two numbers:

| Desired Result | Minimum flips |
|----------------|--------------|
| `false` | `costFalse` |
| `true`  | `costTrue`  |

We’ll compute these numbers in a single post‑order traversal.

### 2.  Base Case – Leaf

A leaf has no children.

| Current leaf value | costFalse | costTrue |
|--------------------|-----------|----------|
| 0 (false)          | 0         | 1        |
| 1 (true)           | 1         | 0        |

Implementation:

```python
if node.left is None and node.right is None:   # leaf
    costFalse = 0 if node.val == 0 else 1
    costTrue  = 0 if node.val == 1 else 1
```

### 3.  Recurrence – Internal Nodes

Let `L = [lf, lt]` and `R = [rf, rt]` be the tuples of the left and right child
(`lt`/`rt` may be `None` for a NOT node).

| Node type | Boolean expression | costFalse | costTrue |
|-----------|--------------------|-----------|----------|
| **OR** (2) | `L OR R` | `lf + rf` | `min(lt, rt)` |
| **AND** (3) | `L AND R` | `min(lf, rf)` | `lt + rt` |
| **XOR** (4) | `L XOR R` | `min(lf + rt, lt + rf)` | `min(lf + rt, lt + rf)`? Wait we must be careful. |
| **NOT** (5) | `NOT L` (only one child) | `lt` | `lf` |

#### XOR Correct Derivation

`XOR` is true when the two operands differ, false when they are equal.

```
costTrue  = min( lf + rt, lt + rf )  # left false & right true  OR left true & right false
costFalse = min( lf + rt, lt + rf )? Wait same? No.

costFalse = min( lf + rt, lt + rf )? Actually equal outcomes:

- Both false: lf + rf
- Both true : lt + rt
So:

costFalse = min( lf + rf, lt + rt )
costTrue  = min( lf + rt, lt + rf )
```

That’s the final formula.

### 4.  Implementation Tips

- Use `int` everywhere. The maximum flips needed can’t exceed the number of leaves (≤ 10⁵).
- Recursion depth may reach 10⁵ in a degenerate tree; most interview environments allow it, but you can switch to an explicit stack if necessary.
- For the NOT node, only one child is present – check which one is not `None`.

---

## Complexity Analysis <a name="complexity"></a>

| Metric | Value |
|--------|-------|
| Time | **O(N)** – each node is processed once. |
| Space | **O(H)** – recursion stack depth; worst case O(N). |

---

## Code – Java <a name="java"></a>

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
    public int minimumFlips(TreeNode root, boolean result) {
        int[] cost = dfs(root);
        return result ? cost[1] : cost[0];
    }

    private int[] dfs(TreeNode node) {
        // Leaf
        if (node.left == null && node.right == null) {
            int costFalse = node.val == 0 ? 0 : 1;
            int costTrue  = node.val == 1 ? 0 : 1;
            return new int[]{costFalse, costTrue};
        }

        // Internal node
        int[] left  = node.left != null ? dfs(node.left) : null;
        int[] right = node.right != null ? dfs(node.right) : null;

        switch (node.val) {
            case 2:  // OR
                return new int[]{
                    left[0] + right[0],          // false
                    Math.min(left[1], right[1])  // true
                };
            case 3:  // AND
                return new int[]{
                    Math.min(left[0], right[0]), // false
                    left[1] + right[1]           // true
                };
            case 4:  // XOR
                return new int[]{
                    Math.min(left[0] + right[0], left[1] + right[1]), // false
                    Math.min(left[0] + right[1], left[1] + right[0])  // true
                };
            case 5:  // NOT – only one child exists
                int[] child = left != null ? left : right;
                return new int[]{child[1], child[0]}; // costFalse = true of child, etc.
            default:
                throw new IllegalArgumentException("Invalid node value");
        }
    }
}
```

---

## Code – Python <a name="python"></a>

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def minimumFlips(self, root: TreeNode, result: bool) -> int:
        cost = self._dfs(root)
        return cost[1] if result else cost[0]

    def _dfs(self, node: TreeNode) -> list[int]:
        # Leaf
        if node.left is None and node.right is None:
            costFalse = 0 if node.val == 0 else 1
            costTrue  = 0 if node.val == 1 else 1
            return [costFalse, costTrue]

        # Recurse on children
        left  = self._dfs(node.left) if node.left else None
        right = self._dfs(node.right) if node.right else None

        if node.val == 2:          # OR
            return [left[0] + right[0],
                    min(left[1], right[1])]
        if node.val == 3:          # AND
            return [min(left[0], right[0]),
                    left[1] + right[1]]
        if node.val == 4:          # XOR
            return [min(left[0] + right[0], left[1] + right[1]),
                    min(left[0] + right[1], left[1] + right[0])]
        if node.val == 5:          # NOT
            child = left if left is not None else right
            return [child[1], child[0]]   # false cost = child true, true cost = child false
```

---

## Code – C++ <a name="cpp"></a>

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
    int minimumFlips(TreeNode* root, bool result) {
        auto cost = dfs(root);
        return result ? cost.second : cost.first;
    }

private:
    pair<int,int> dfs(TreeNode* node) {   // {costFalse, costTrue}
        if (!node->left && !node->right) {   // leaf
            int costFalse = (node->val == 0) ? 0 : 1;
            int costTrue  = (node->val == 1) ? 0 : 1;
            return {costFalse, costTrue};
        }

        pair<int,int> L = node->left  ? dfs(node->left)  : pair<int,int>{0,0};
        pair<int,int> R = node->right ? dfs(node->right) : pair<int,int>{0,0};

        switch (node->val) {
            case 2:  // OR
                return {L.first + R.first,
                        min(L.second, R.second)};
            case 3:  // AND
                return {min(L.first, R.first),
                        L.second + R.second};
            case 4:  // XOR
                return {min(L.first + R.first, L.second + R.second),
                        min(L.first + R.second, L.second + R.first)};
            case 5:  // NOT
                return {L.second, L.first};  // only one child is non‑null
            default:
                throw invalid_argument("Invalid node value");
        }
    }
};
```

---

## The Good, The Bad, & The Ugly <a name="good-bad-ugly"></a>

| Aspect | What Worked | What Could Go Wrong |
|--------|-------------|---------------------|
| **Good** | 1️⃣ Bottom‑up DP eliminates the need for memo‑tables. <br>2️⃣ One pass gives *both* answers (false/true) so the final `if (result)` is trivial. <br>3️⃣ All formulas are O(1) per node → linear time. |  |
| **Bad** | 1️⃣ Recursion depth may hit 10⁵ – stack overflow on very old judges. <br>2️⃣ The XOR formulas are a *common pitfall*; many candidates write the wrong “same vs. different” logic. |  |
| **Ugly** | 1️⃣ Hard‑coded node values (2–5) hide intent; a map of enum values can make the code cleaner. <br>2️⃣ Forgetting that NOT nodes have *one* child can lead to null‑pointer exceptions. |  |

> **Bottom line:** The algorithm is elegant and fast, but the devil is in the formulas. Test the XOR logic with a few hand‑crafted trees before submitting.

---

## How This Helps Your Job Hunt <a name="job-hunt"></a>

1. **Interview‑Ready Pattern**  
   - *Binary tree + bottom‑up DP* is a classic interview pattern. Mastering it signals you can handle recursive state‑transition problems.

2. **LeetCode Hard Mastery**  
   - Solving this problem earns you a **hard badge** on LeetCode, boosting your résumé and GitHub profile.

3. **Java/Python/C++ Show‑case**  
   - Providing three implementations demonstrates language‑agnostic thinking—a plus for **full‑stack** or **system‑design** roles.

4. **Algorithmic Clarity**  
   - Clear pseudocode + complexity analysis shows you can communicate ideas to hiring managers and teammates.

5. **SEO Keywords**  
   - If recruiters search for *“Java interview binary tree problems”* or *“LeetCode hard solutions”*, this article ranks high thanks to targeted headings and keyword‑rich explanations.

---

## Final Thoughts <a name="final"></a>

*Minimum Flips in Binary Tree to Get Result* is more than a coding challenge—it’s a micro‑ecosystem of recursion, dynamic programming, and logical reasoning.  
The DFS‑DP pattern we used is reusable across many tree‑based interview questions (e.g., *Minimum Depth*, *Maximum Path Sum*, *House Robber III*).  

**Keep practicing**:  

- Try variations (e.g., “flip at most K leaves”, “count distinct flip sets”).
- Optimize for space: implement an iterative post‑order traversal if stack depth worries you.
- Explain your thought process aloud; this mirrors real interview conditions.

Happy coding—and happy hunting! 🚀