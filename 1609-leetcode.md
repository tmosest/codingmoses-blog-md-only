---
title: LeetCode 1609. Even Odd Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1609. Even‑Odd Tree  
**Medium | Breadth‑First Search | O(N) time | O(N) space**

> A binary tree is called **Even‑Odd** when:
> 1. Levels are numbered starting at 0 (root = level 0).  
> 2. Even‑indexed levels contain **odd** values that are **strictly increasing** from left to right.  
> 3. Odd‑indexed levels contain **even** values that are **strictly decreasing** from left to right.

Return `true` if the tree satisfies all three rules, otherwise `false`.

---

## 🚀 Why This Problem Matters for Job Interviews

- **Breadth‑first traversal** is a staple for binary‑tree questions.  
- The task tests **stateful traversal** (keeping track of level parity).  
- It requires **early exit** logic—once a violation is found, the whole algorithm stops.  
- Candidates must think about **time/space trade‑offs** and cleanly implement the monotonicity checks.

Being able to discuss this problem (and its variations) in an interview shows you understand tree traversals, edge‑case handling, and algorithmic complexity.

---

## 🔍 Problem Recap (Short Version)

```text
Input  : TreeNode root
Output : boolean

Rule 1 : Level 0, 2, 4… -> odd numbers, strictly increasing
Rule 2 : Level 1, 3, 5… -> even numbers, strictly decreasing
```

---

## 🧠 Core Idea – Level Order with Parity Checks

1. **BFS** (queue) to visit nodes level‑by‑level.  
2. Keep a flag `isEvenLevel` (`true` for even levels).  
3. For each level:
   * **Even level**: value must be odd and `value > prev`.  
   * **Odd level**: value must be even and `value < prev`.  
4. Update `prev` after checking the node.  
5. Enqueue left and right children for the next level.  
6. Flip the level flag after finishing a level.

Because we can return `false` as soon as a rule is broken, the algorithm is linear in the number of nodes.

---

## 🏗️ Implementation

Below are clean, ready‑to‑paste solutions in **Java**, **Python**, and **C++**.

> **Note**: All three solutions use the same BFS logic; the only differences are language‑specific syntax and data‑structures.

---

### Java

```java
import java.util.LinkedList;
import java.util.Queue;

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
    public boolean isEvenOddTree(TreeNode root) {
        if (root == null) return true;

        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        boolean isEvenLevel = true;          // level 0 is even
        int prev = isEvenLevel ? Integer.MIN_VALUE : Integer.MAX_VALUE;

        while (!q.isEmpty()) {
            int size = q.size();
            int levelPrev = prev;            // reset prev for each level

            for (int i = 0; i < size; i++) {
                TreeNode node = q.poll();

                // Check parity rule
                if (isEvenLevel) {
                    if (node.val % 2 == 0 || node.val <= levelPrev) return false;
                } else {
                    if (node.val % 2 == 1 || node.val >= levelPrev) return false;
                }

                levelPrev = node.val;

                // Add children for next level
                if (node.left != null) q.offer(node.left);
                if (node.right != null) q.offer(node.right);
            }

            // Flip level parity for the next layer
            isEvenLevel = !isEvenLevel;
        }
        return true;
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

from collections import deque

class Solution:
    def isEvenOddTree(self, root: TreeNode) -> bool:
        if not root:
            return True

        q = deque([root])
        is_even_level = True          # level 0 is even
        prev = float('-inf') if is_even_level else float('inf')

        while q:
            size = len(q)
            level_prev = prev

            for _ in range(size):
                node = q.popleft()

                # Parity and monotonicity check
                if is_even_level:
                    if node.val % 2 == 0 or node.val <= level_prev:
                        return False
                else:
                    if node.val % 2 == 1 or node.val >= level_prev:
                        return False

                level_prev = node.val

                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)

            # Toggle level parity
            is_even_level = not is_even_level

        return True
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
#include <queue>
using namespace std;

class Solution {
public:
    bool isEvenOddTree(TreeNode* root) {
        if (!root) return true;

        queue<TreeNode*> q;
        q.push(root);
        bool isEvenLevel = true;             // level 0 is even
        int prev = isEvenLevel ? INT_MIN : INT_MAX;

        while (!q.empty()) {
            int sz = q.size();
            int levelPrev = prev;             // reset for this level

            for (int i = 0; i < sz; ++i) {
                TreeNode* cur = q.front(); q.pop();

                if (isEvenLevel) {            // even level: odd values, increasing
                    if (cur->val % 2 == 0 || cur->val <= levelPrev) return false;
                } else {                      // odd level: even values, decreasing
                    if (cur->val % 2 == 1 || cur->val >= levelPrev) return false;
                }

                levelPrev = cur->val;

                if (cur->left)  q.push(cur->left);
                if (cur->right) q.push(cur->right);
            }

            isEvenLevel = !isEvenLevel;       // flip for next level
        }
        return true;
    }
};
```

---

## 🗺️ Step‑by‑Step Walkthrough (Java Example)

1. **Queue Initialization** – start with the root.  
2. **Level Size** – `size = q.size()` tells how many nodes belong to the current level.  
3. **Prev Value** – `prev` is initialized to `INT_MIN` on even levels (any odd value will be greater) and to `INT_MAX` on odd levels (any even value will be smaller).  
4. **Node Check** –  
   * Even level: `node.val % 2 == 1` AND `node.val > prev`.  
   * Odd level:  `node.val % 2 == 0` AND `node.val < prev`.  
5. **Early Failure** – return `false` immediately if a rule is violated.  
6. **Enqueue Children** – left then right, preserving left‑to‑right order.  
7. **Level Flip** – `isEvenLevel = !isEvenLevel`.  

---

## ⚠️ Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using `prev = Integer.MIN_VALUE` on both levels** | Reset `prev` per level; use `INT_MIN` only on even levels, `INT_MAX` on odd levels. |
| **Mixing left/right order** | Enqueue children in the order `left` then `right`. The BFS guarantees correct level order. |
| **Ignoring `null` children** | Don’t enqueue `null`; otherwise the queue will grow unnecessarily. |
| **Not flipping the flag after each level** | If you forget to toggle `isEvenLevel`, all levels will be treated as even. |

---

## 📊 Complexity Analysis

| Metric | Complexity |
|--------|------------|
| **Time** | `O(N)` – each node is visited exactly once. |
| **Space** | `O(N)` – worst‑case queue holds all nodes at the deepest level (≈ half the tree). |

---

## 🔁 Alternative Approaches

1. **Recursive DFS with Level Tracking** – passes the level number to each recursive call, but you still need to keep track of the previous value per level.  
2. **Iterative DFS with Stack** – similar complexity but more cumbersome to maintain order.  
3. **Divide‑and‑Conquer** – process left and right subtrees, then merge results; unnecessary overhead.

BFS remains the simplest and most readable solution for this problem.

---

## 📈 SEO‑Friendly Highlights

- **Title**: “Even‑Odd Tree LeetCode Solution – Java, Python, C++ Explained”
- **Keywords**: *Even Odd Tree, LeetCode 1646, Binary Tree BFS, Level Order Traversal, Interview Preparation, Data Structures, Time Complexity, Space Complexity, Early Exit Algorithm, Tree Parity Check*  
- **Meta Description**: “Master the Even‑Odd Tree problem on LeetCode with step‑by‑step BFS solutions in Java, Python, and C++. Understand the parity rules, complexity, and interview strategies.”

---

## 🎓 Takeaway

The Even‑Odd Tree problem is a concise showcase of:

1. **Breadth‑first traversal** with a queue.  
2. **Stateful level parity** handling.  
3. **Monotonicity and parity checks** with early exit.  
4. **Linear time** and **optimal space**.

Feel free to tweak the solutions for variations (e.g., strict vs. non‑strict ordering) or to handle large trees in interview settings. Good luck—show those interviewers that you’ve got tree traversals under your belt! 🚀

---