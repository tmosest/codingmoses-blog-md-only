---
title: LeetCode 1973. Count Nodes Equal to Sum of Descendants - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 1973 – Count Nodes Equal to Sum of Descendants  
**Goal:**  Return the number of nodes in a binary tree whose value equals the sum of all values in its descendant subtree.  

> *A descendant is any node that lies on a path from the current node to a leaf.  
> The sum of descendants for a leaf is defined as 0.*

The optimal solution runs in **O(n)** time and **O(h)** space, where *n* is the number of nodes and *h* the tree height (recursion stack).

Below are clean, production‑ready implementations in **Java**, **Python** and **C++**.

---

### 1.1 Java – Post‑order DFS (Recursive)

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
    private int count = 0;

    public int equalToDescendants(TreeNode root) {
        dfs(root);
        return count;
    }

    /**
     * Returns the sum of the entire subtree rooted at `node`.
     * While unwinding the recursion we compare the node's value
     * with the sum of its descendants (left + right).
     */
    private int dfs(TreeNode node) {
        if (node == null) return 0;

        int leftSum  = dfs(node.left);
        int rightSum = dfs(node.right);
        int childSum = leftSum + rightSum;

        if (node.val == childSum) count++;

        // Return sum including this node for its parent.
        return node.val + childSum;
    }
}
```

---

### 1.2 Python – Post‑order DFS (Recursive)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def equalToDescendants(self, root: TreeNode) -> int:
        self.count = 0

        def dfs(node: TreeNode) -> int:
            if not node:
                return 0
            left  = dfs(node.left)
            right = dfs(node.right)
            child_sum = left + right
            if node.val == child_sum:
                self.count += 1
            return node.val + child_sum

        dfs(root)
        return self.count
```

---

### 1.3 C++ – Post‑order DFS (Recursive)

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

    int equalToDescendants(TreeNode* root) {
        dfs(root);
        return count;
    }

private:
    int dfs(TreeNode* node) {
        if (!node) return 0;
        int left  = dfs(node->left);
        int right = dfs(node->right);
        int child_sum = left + right;
        if (node->val == child_sum) ++count;
        return node->val + child_sum;
    }
};
```

---

## 2.  Blog Article – “The Good, The Bad, and The Ugly of Counting Nodes Equal to the Sum of Descendants”

### Title  
**Mastering LeetCode 1973: Count Nodes Equal to Sum of Descendants – A Deep Dive into Good, Bad & Ugly Patterns**

### Meta Description  
Learn how to solve LeetCode 1973 in Java, Python, and C++ with a clean post‑order DFS approach. Discover the best practices, common pitfalls, and interview‑ready tips that will impress recruiters.

### Keywords  
LeetCode 1973, Count Nodes Equal to Sum of Descendants, Binary Tree DFS, Post‑order Traversal, Java LeetCode, Python LeetCode, C++ LeetCode, Tree Traversal, Interview Question, Technical Interview, Backend Engineer

---

### 1️⃣ Introduction  

Binary tree problems are the bread‑and‑butter of coding interviews.  
LeetCode **1973 – Count Nodes Equal to Sum of Descendants** is a great example: it forces you to combine a classic tree traversal with a subtle arithmetic check.

In this post, we’ll:
1. Break the problem into three parts – *The Good*, *The Bad*, and *The Ugly*.
2. Provide a production‑ready solution in **Java**, **Python**, and **C++**.
3. Discuss edge cases, recursion depth, and why this pattern is a must‑know for any backend engineer.

---

### 2️⃣ The Good – An Elegant Post‑order DFS  

| Aspect | Why It’s Good |
|--------|---------------|
| **Post‑order traversal** | Guarantees that we know the descendant sums *before* evaluating the current node. |
| **Single pass** | O(n) time – each node visited once. |
| **Linear stack space** | O(h) space – recursion depth equals tree height, far better than BFS’s O(n). |
| **Readable & maintainable** | Clear separation of “compute sum” and “check equality”. |
| **No global side‑effects** | Using a class/instance field (`count`) keeps the helper pure. |

> **Key Insight**  
> If `sum(left) + sum(right) == node.val`, increment the counter.  
> Return `node.val + sum(left) + sum(right)` to parent nodes.

This pattern is reusable for many “subtree aggregate” problems (sum, average, height, etc.).

---

### 3️⃣ The Bad – Common Pitfalls  

| Pitfall | Consequence | Fix |
|---------|-------------|-----|
| **Using global variables** | Hard to unit‑test, risk of stale state across calls. | Store state in a class field or return a tuple. |
| **Brute‑force BFS/DFS** | Re‑computing sums from scratch for each node → O(n²). | Compute once in a post‑order pass. |
| **Iterative stack with manual sum tracking** | Bug‑prone, verbose. | Prefer recursion unless tree height ≈ 10⁵ (risk of stack overflow). |
| **Ignoring leaf node definition** | Mis‑count when node has no children. | Treat leaf’s descendant sum as 0. |
| **Integer overflow** | Values up to 10⁵, but the sum of many nodes could exceed `int`. | Use `long` in Java/C++ or Python’s unlimited integers. |

---

### 4️⃣ The Ugly – Inefficient or Incorrect Solutions  

1. **Naïve Recursion (O(n²))**  
   ```java
   int count = 0;
   int sumDescendants(TreeNode node) { … } // O(n)
   void traverse(TreeNode node) {
       if (node == null) return;
       int sum = sumDescendants(node);
       if (node.val == sum) count++;
       traverse(node.left);
       traverse(node.right);
   }
   ```
   *Why Ugly?* Each node triggers an entire subtree sum, leading to quadratic time.

2. **Using a `HashMap` to store subtree sums**  
   ```java
   Map<TreeNode, Integer> memo = new HashMap<>();
   int getSum(TreeNode node) {
       if (node == null) return 0;
       return memo.computeIfAbsent(node, n -> n.val + getSum(n.left) + getSum(n.right));
   }
   ```
   *Why Ugly?* Adds extra space and complexity; not needed for this problem.

3. **Iterative DFS with a stack and two passes**  
   - First pass to push nodes.  
   - Second pass to pop and compute sums.  
   *Why Ugly?* Double work and fragile code.

Avoid these patterns unless the interview specifically asks for an alternative (e.g., bottom‑up DP, segment trees).

---

### 5️⃣ Edge Cases & Robustness  

| Scenario | What to watch out for |
|----------|-----------------------|
| **Empty tree (`root == null`)** | Return 0 – no nodes to count. |
| **Highly unbalanced tree** | Height ≈ n → recursion may overflow. | Use an explicit stack or `tail recursion` (rarely allowed). |
| **Large descendant sums** | Potential overflow in 32‑bit ints. | Use `long` (Java/C++) or Python’s int. |
| **All nodes equal to 0** | Every non‑leaf matches because descendant sum is 0. | Counter will be `n - number_of_leaves`. |

Testing with these edge cases guarantees a bullet‑proof submission.

---

### 6️⃣ Interview‑Ready Talking Points  

1. **State the Approach First** – “I’ll do a post‑order DFS because I need the descendant sums before I can check equality.”  
2. **Explain Complexity** – “One pass, O(n) time, O(h) stack.”  
3. **Mention Overflow** – “All input values are ≤ 10⁵, but the sum may exceed 32‑bit; I’ll use `long`.”  
4. **Show the Code** – Provide a clean snippet in the language of your choice.  
5. **Optional – Discuss a Brute‑Force Baseline** – “If we were to brute‑force, we’d end up with O(n²), which is impractical.”  
6. **Ask for Variations** – “What if we wanted to return the actual nodes instead of a count?” – leads to a small refactor.

---

### 6️⃣ Conclusion  

LeetCode 1973 is not just a test of recursion; it’s a micro‑course on **efficient tree aggregation**.  
The “post‑order DFS” solution is the gold standard:  
*single traversal, linear space, clear logic.*  

Avoid the bad and ugly patterns, because recruiters are looking for clean, *interview‑friendly* code that scales.

---

### 7️⃣ Takeaway Checklist  

- [ ] Use **post‑order DFS** for subtree aggregates.  
- [ ] Keep counters in a class/instance field; avoid global statics.  
- [ ] Treat leaves as having 0 descendant sum.  
- [ ] Guard against overflow (`long` / `BigInteger`).  
- [ ] Handle empty tree as a base case.  
- [ ] Be ready to explain recursion depth and stack usage.  

Mastering these principles will give you a solid foothold for **backend engineering interviews** where tree logic is common.

---

## 3️⃣ Final Thoughts  

Binary trees are everywhere – from parsing expression trees in compilers to organizing a directory structure in file systems.  
The pattern we demonstrated today will surface in many other problems: “Sum of left subtree”, “Maximum path sum”, “Largest BST in a binary tree”, etc.

So next time you see a LeetCode tree problem, think:

> *Compute once → store once → propagate upwards.*  

Happy coding—and good luck impressing your next recruiter!