---
title: LeetCode 1120. Maximum Average Subtree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Maximum Average Subtree – LeetCode 1120  
**The Good, the Bad, and the Ugly – A Job‑Interview‑Ready Guide (Java, Python, C++)**

> **Keywords:**  
> *Maximum Average Subtree*, *LeetCode 1120*, *Binary Tree*, *DFS*, *Recursive*, *Java*, *Python*, *C++*, *Algorithm*, *Interview Problem*, *Job Interview*, *Coding*

---

## 1. Problem Statement

Given the root of a binary tree, return the maximum average value of a subtree of that tree.  
A *subtree* is any node plus all of its descendants.  
The average value of a tree is the sum of its node values divided by the number of nodes.  
Answers within 10⁻⁵ of the actual answer will be accepted.

**Constraints**

| | Value |
|---|---|
| Number of nodes | 1 ≤ n ≤ 10⁴ |
| Node.val | 0 ≤ Node.val ≤ 10⁵ |

---

## 2. Why This Problem Matters

- **Tree traversal mastery** – interviewers love to see if you can do a post‑order DFS correctly.
- **Floating‑point precision** – handling averages demands a careful use of `double`.
- **Space vs. time trade‑offs** – the naive approach may hit recursion limits or overflow.

---

## 3. The Good – A Clean Recursive Solution

The core insight: **process each subtree once** and propagate two values upward:

1. **sum**  – total of all node values in the subtree  
2. **count** – number of nodes in the subtree  

With these, the average of that subtree is simply `sum / count`.  
While traversing, we keep a global maximum.

### Complexity

| | Time | Space |
|---|---|---|
| `O(n)` | Each node visited once. | `O(h)` – recursion depth (worst‑case `h = n` for a skewed tree). |

---

## 4. The Bad – Pitfalls You Should Avoid

| Issue | Why it’s a problem | Fix |
|---|---|---|
| **Modifying node values** in‑place | Side effects if the tree is reused; may lead to wrong sums. | Compute sums separately. |
| **Using `int` for sum** | Max sum could be `n * 10⁵ = 10⁹` → still fits, but safer to use `long`. | Use `long` or `long long`. |
| **Recursive depth > recursion limit** | Python can hit a recursion limit (default ~1000). | Use iterative DFS or increase recursion limit (`sys.setrecursionlimit`). |
| **Floating‑point errors** | Dividing by `int` may lose precision. | Cast to `double` before division. |
| **Global state bugs** | Wrong global variable reset between test cases. | Pass `maxAverage` by reference or use a field that resets. |

---

## 5. The Ugly – Handling Edge Cases

| Edge Case | Challenge | How we handle it |
|---|---|---|
| **All nodes have the same value** | Every subtree has identical average; algorithm still works. | No special handling needed. |
| **Tree with a single node** | Average is that node’s value; recursion should return correctly. | Base case returns `sum = val`, `count = 1`. |
| **Very deep tree** | Stack overflow risk. | Use tail‑recursion optimization (not available in Java/Python) or iterative stack. |
| **Large number of nodes** | Time limit exceeded if not linear. | Linear DFS is guaranteed. |

---

## 6. Code Snippets

Below are **three idiomatic implementations** that compile on LeetCode and most interview platforms.

### 6.1 Java

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
    private double maxAverage = Double.NEGATIVE_INFINITY;

    public double maximumAverageSubtree(TreeNode root) {
        dfs(root);
        return maxAverage;
    }

    // Returns {sum, count}
    private long[] dfs(TreeNode node) {
        if (node == null) return new long[]{0, 0};

        long[] left  = dfs(node.left);
        long[] right = dfs(node.right);

        long sum   = left[0] + right[0] + node.val;
        long count = left[1] + right[1] + 1;

        double avg = (double) sum / count;
        maxAverage = Math.max(maxAverage, avg);

        return new long[]{sum, count};
    }
}
```

### 6.2 Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def maximumAverageSubtree(self, root: TreeNode) -> float:
        self.max_avg = float('-inf')
        self.dfs(root)
        return self.max_avg

    def dfs(self, node: TreeNode):
        if not node:
            return 0, 0  # sum, count

        left_sum, left_cnt = self.dfs(node.left)
        right_sum, right_cnt = self.dfs(node.right)

        total_sum = left_sum + right_sum + node.val
        total_cnt = left_cnt + right_cnt + 1

        self.max_avg = max(self.max_avg, total_sum / total_cnt)
        return total_sum, total_cnt
```

### 6.3 C++

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
    double maximumAverageSubtree(TreeNode* root) {
        maxAvg = -std::numeric_limits<double>::infinity();
        dfs(root);
        return maxAvg;
    }

private:
    double maxAvg;

    // returns pair<sum, count>
    std::pair<long long, int> dfs(TreeNode* node) {
        if (!node) return {0, 0};

        auto left  = dfs(node->left);
        auto right = dfs(node->right);

        long long sum   = left.first + right.first + node->val;
        int       count = left.second + right.second + 1;

        maxAvg = std::max(maxAvg, static_cast<double>(sum) / count);
        return {sum, count};
    }
};
```

---

## 7. How Interviewers Might Ask

| Question | What they’re really probing |
|---|---|
| “Can you explain how you avoid recomputing subtree sums?” | Expect DFS with memoization of sum & count. |
| “What if the tree is skewed? Will recursion blow up?” | Know recursion depth = height, discuss iterative alternatives. |
| “Why did you use `double` instead of `float`?” | Precision & LeetCode tolerance. |
| “How would you adapt this to find the subtree with the minimum average?” | Mirror logic with `min`. |

---

## 8. SEO‑Optimized Closing

If you’re preparing for your next **software engineering interview**, mastering the **Maximum Average Subtree** problem shows you can:

1. **Traverse binary trees** efficiently (DFS, post‑order).  
2. **Handle numeric precision** with care.  
3. **Write clean, maintainable code** in Java, Python, and C++.

Feel free to copy the snippets above, run them on LeetCode, and tweak them for speed.  
Remember to explain the intuition, complexity, and edge cases during your interview—those are the *good* parts that impress hiring managers.  

Happy coding, and best of luck landing that dream job! 🚀

---