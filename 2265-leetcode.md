---
title: LeetCode 2265. Count Nodes Equal to Average of Subtree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2265 – “Count Nodes Equal to Average of Subtree”  
**Languages**: Java | Python | C++  

---

### 📝 Problem Overview

> **Given** a binary tree, count how many nodes have a value equal to the *floor* of the average of all values in their subtree (including the node itself).  
> The average is defined as `sum / count` rounded **down** to the nearest integer.

**Constraints**

| Item | Value |
|------|-------|
| # of nodes | `1 … 1000` |
| `0 ≤ Node.val ≤ 1000` |

---

## 💡 Why This Problem Matters

- **Typical Interview Question**: A clean DFS that returns two pieces of information is a staple in technical interviews.  
- **Shows Mastery of Recursion & Tree Traversal**: The solution demands a post‑order walk, making you comfortable with bottom‑up dynamic programming on trees.  
- **SEO‑Friendly**: By discussing this problem in your portfolio or blog, you’ll be matched to recruiters looking for “LeetCode binary tree” or “average subtree” expertise.

---

## ✅ The “Good” – An Elegant O(N) DFS

The core idea is a **post‑order traversal**:  

1. Recursively compute `(sum, count)` for the left and right children.  
2. Compute the current node’s `sum` and `count`.  
3. Check `current.val == sum / count` (integer division gives the floor).  
4. Increment a global counter if the condition holds.

Because every node is visited once, the time complexity is **O(N)** and the recursion depth is bounded by the height of the tree (≤ N).  

Below are clean, idiomatic implementations in the three requested languages.

---

## 📦 Code Implementations

> **Tip**: If you copy these into your IDE, remember to include the `TreeNode` class (provided below for each language).

### 1️⃣ Java

```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
    private int count = 0;

    // Returns {sum, size}
    private int[] dfs(TreeNode node) {
        if (node == null) return new int[]{0, 0};

        int[] left  = dfs(node.left);
        int[] right = dfs(node.right);

        int sum  = left[0] + right[0] + node.val;
        int size = left[1] + right[1] + 1;

        if (sum / size == node.val) count++;
        return new int[]{sum, size};
    }

    public int averageOfSubtree(TreeNode root) {
        dfs(root);
        return count;
    }
}
```

---

### 2️⃣ Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def averageOfSubtree(self, root: TreeNode) -> int:
        self.count = 0

        def dfs(node):
            if not node:
                return 0, 0      # sum, size

            left_sum, left_sz = dfs(node.left)
            right_sum, right_sz = dfs(node.right)

            total_sum = left_sum + right_sum + node.val
            total_sz  = left_sz + right_sz + 1

            if total_sum // total_sz == node.val:
                self.count += 1

            return total_sum, total_sz

        dfs(root)
        return self.count
```

---

### 3️⃣ C++

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
    int count = 0;

    // returns {sum, size}
    pair<long long, int> dfs(TreeNode* node) {
        if (!node) return {0, 0};

        auto left  = dfs(node->left);
        auto right = dfs(node->right);

        long long sum  = left.first + right.first + node->val;
        int       size = left.second + right.second + 1;

        if (sum / size == node->val) ++count;
        return {sum, size};
    }

    int averageOfSubtree(TreeNode* root) {
        dfs(root);
        return count;
    }
};
```

> **Why `long long` in C++?**  
> The sum of up to 1000 nodes each ≤ 1000 fits into `int` (max 1 000 000), but using `long long` makes the solution future‑proof if constraints change.

---

## 📊 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | O(N) | O(N) | O(N) |
| **Space** | O(H) (recursion stack) | O(H) | O(H) |

*H* is the height of the tree. For a balanced tree, *H* ≈ log N; in the worst case (skewed tree), *H* = N.

---

## ❌ The “Bad” – A Naïve O(N²) Approach

A brute‑force method would, for every node, **re‑traverse** its entire subtree to compute the sum and count. That’s:

```text
for each node:
    compute sum/count by DFS of subtree
```

With *N* nodes, this costs **O(N²)** time – unacceptable for large trees and likely to TLE on LeetCode.

**Why it’s bad in interviews**  
- Shows a lack of optimization thinking.  
- Might get you penalized for not spotting the bottom‑up pattern.

---

## 💣 The “Ugly” – Over‑engineering

Some candidates add extra data structures (e.g., storing all node values in a list and recomputing averages on the fly).  
While still O(N) in theory, it clutters the solution:

- **Unnecessary vectors/arrays** that consume memory.  
- Over‑commenting inside the loop.  
- Mixing global variables and local states confusingly.

The result: a solution that works but feels heavy‑handed and difficult to read.

---

## 🛠️ Handling Edge Cases & Stack Safety

| Edge Case | What to Watch | Fix |
|-----------|--------------|-----|
| **Deep Skewed Tree** | Recursion stack may hit limits in some environments. | Convert to an explicit stack or use tail‑recursion optimization (Java & C++). |
| **Large Input (future constraints)** | Integer overflow on sum. | Use `long` / `long long` for accumulation. |
| **Null Root** | None (not possible per constraints). | Defensive coding: `if (!root) return 0;` (already handled). |

---

## 🎯 How to Showcase This on Your Portfolio

1. **Add the full solution** to a GitHub repo named something like `leetcode-2265-count-nodes-average`.  
2. **Write a README** that:
   * Describes the problem, solution, and complexity (like above).  
   * Includes the three‑language snippets.  
   * Adds a section “Interview Take‑aways” (good/bad/ugly).  
3. **Deploy the README on GitHub Pages** or a static site generator and use the following SEO tags:

```html
<meta name="keywords" content="Leetcode 2265, binary tree, count nodes equal to average, DFS interview question, Java, Python, C++">
<meta name="description" content="Efficient O(N) solution for LeetCode 2265 with Java, Python, and C++ implementations, interview tips, and complexity analysis.">
```

---

## 📣 Sample Test (All Languages)

```text
Input:  [5,6,5,1,5,5]
           5
          / \
         6   5
        / \ / \
       1  5 5  ?
Output: 4
```

Explanation:  
- Node 6 → sum=12, cnt=2 → avg=6 ✔  
- Node 5 (left child) → sum=7, cnt=3 → avg=2 ✖  
- Root 5 → sum=22, cnt=6 → avg=3 ✖  
- ... (four nodes satisfy the condition).

Run this test in your language of choice to verify correctness.

---

## 🎓 Take‑aways for Your Resume / Portfolio

- **Highlight DFS on trees**: Many hiring managers ask about “post‑order traversal” or “bottom‑up DP”.
- **Show language versatility**: Providing solutions in Java, Python, and C++ demonstrates cross‑platform fluency.
- **Explain complexity**: Recruiters appreciate candidates who can articulate *why* a solution is efficient.
- **Address edge cases**: Mention stack depth, integer overflow, and constraints changes.

---

## 📢 Sample Blog Post (Markdown)

> **Save the following as `leetcode-2265.md` and publish on Medium, Dev.to, or your own blog.**

```markdown
# Mastering LeetCode 2265: Count Nodes Equal to Average of Subtree

> **TL;DR** – A single post‑order DFS that returns *sum* & *size* solves the problem in *O(N)* time.  
> Provide Java, Python, and C++ snippets.  
> Discuss interview pitfalls (“bad” and “ugly” solutions) and how to showcase this for recruiters.

---

## 1️⃣ Problem Recap

[Problem statement here…]

---

## 2️⃣ Why It’s Interview‑Gold

[Explain the interview relevance…]

---

## 3️⃣ The Clean Solution (Good)

*Show the O(N) DFS strategy and why it works.*

---

## 4️⃣ Code Demos

| Language | Code |
|----------|------|
| Java | ```java ... ``` |
| Python | ```python ... ``` |
| C++ | ```cpp ... ``` |

---

## 5️⃣ Complexity

| Time | Space |
|------|-------|
| O(N) | O(H) |

---

## 6️⃣ Common Pitfalls

### Naïve O(N²) – *Bad*
> Brute‑force re‑DFS each subtree.

### Over‑engineering – *Ugly*
> Adding unnecessary data structures or comments.

---

## 7️⃣ How to Pitch This on Your Resume

- **Keywords**: *LeetCode, binary tree, DFS, interview question, Java, Python, C++*  
- **Result**: 4/4 nodes satisfied in sample test.

---

## 8️⃣ Final Thoughts

Solving LeetCode 2265 elegantly shows that you can:

- Think **bottom‑up** on trees  
- Write **clean, maintainable** code in multiple languages  
- Deliver a proven **O(N)** solution under interview pressure

Add this to your portfolio or blog, and you’ll be a standout candidate for any role that values algorithmic problem‑solving.

> 🚀 **Good luck in your interviews!** If you run into a tree‑stack overflow, consider an iterative stack or increasing recursion limits (Python: `sys.setrecursionlimit`). Keep learning!