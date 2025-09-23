---
title: LeetCode 2641. Cousins in Binary Tree II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  LeetCode 2641 – *Cousins in Binary Tree II*  
**Languages:** Java | Python | C++  
**Time Complexity:** O(N)  
**Space Complexity:** O(H) (only the queue for the current level)

---

### 📌 Problem Recap  

Given the root of a binary tree, replace the value of each node with the sum of all its cousins’ values.  
Two nodes are *cousins* if they are at the same depth and have different parents.  
The root has no cousins, so its new value is 0.

---

### ✅ ️Solution Idea – Level‑by‑Level BFS

1. **Root → 0** – The root never changes.
2. For each level *except* the root:
   * Compute **`sumChildren`** = sum of all children of the nodes in the current level.
   * For each node in the level  
     `curChildrenSum` = sum of that node’s own children.  
     Then each child’s new value = `sumChildren – curChildrenSum` (all cousins at the next level).
   * Push the children into the queue for the next iteration.
3. Stop when there are no more nodes to process.

The algorithm only keeps the nodes of the current level in memory – O(H) extra space.

---

## 2️⃣ ️ Code Snippets  

> *All three implementations are fully‑compatible with LeetCode’s `TreeNode` definition.*

---

### 🧰 Java

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
    public TreeNode replaceValueInTree(TreeNode root) {
        if (root == null) return null;

        root.val = 0;                       // root has no cousins
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(root);

        while (!queue.isEmpty()) {
            int size = queue.size();
            List<TreeNode> curLevel = new ArrayList<>(size);
            int sumChildren = 0;

            // collect all nodes on current level and compute sum of their children
            for (int i = 0; i < size; ++i) {
                TreeNode node = queue.poll();
                curLevel.add(node);
                if (node.left != null)  sumChildren += node.left.val;
                if (node.right != null) sumChildren += node.right.val;
            }

            // update children values
            for (TreeNode node : curLevel) {
                int ownChildrenSum = 0;
                if (node.left != null)  ownChildrenSum += node.left.val;
                if (node.right != null) ownChildrenSum += node.right.val;

                if (node.left != null) {
                    node.left.val = sumChildren - ownChildrenSum;
                    queue.offer(node.left);
                }
                if (node.right != null) {
                    node.right.val = sumChildren - ownChildrenSum;
                    queue.offer(node.right);
                }
            }
        }
        return root;
    }
}
```

---

### 🧰 Python

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def replaceValueInTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return None

        root.val = 0                    # root has no cousins
        from collections import deque
        q = deque([root])

        while q:
            level_size = len(q)
            level_nodes = []
            sum_children = 0

            # collect nodes of current level and sum of all children
            for _ in range(level_size):
                node = q.popleft()
                level_nodes.append(node)
                if node.left:
                    sum_children += node.left.val
                if node.right:
                    sum_children += node.right.val

            # update children values
            for node in level_nodes:
                own_sum = 0
                if node.left:
                    own_sum += node.left.val
                if node.right:
                    own_sum += node.right.val

                if node.left:
                    node.left.val = sum_children - own_sum
                    q.append(node.left)
                if node.right:
                    node.right.val = sum_children - own_sum
                    q.append(node.right)

        return root
```

---

### 🧰 C++

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *l, TreeNode *r) : val(x), left(l), right(r) {}
 * };
 */
class Solution {
public:
    TreeNode* replaceValueInTree(TreeNode* root) {
        if (!root) return nullptr;

        root->val = 0;                     // root has no cousins
        std::queue<TreeNode*> q;
        q.push(root);

        while (!q.empty()) {
            int sz = q.size();
            std::vector<TreeNode*> cur;
            cur.reserve(sz);
            long long sumChildren = 0;     // use long long to avoid overflow

            // collect current level nodes and sum of all their children
            for (int i = 0; i < sz; ++i) {
                TreeNode* node = q.front(); q.pop();
                cur.push_back(node);
                if (node->left)  sumChildren += node->left->val;
                if (node->right) sumChildren += node->right->val;
            }

            // update children values
            for (TreeNode* node : cur) {
                long long ownSum = 0;
                if (node->left)  ownSum += node->left->val;
                if (node->right) ownSum += node->right->val;

                if (node->left) {
                    node->left->val = static_cast<int>(sumChildren - ownSum);
                    q.push(node->left);
                }
                if (node->right) {
                    node->right->val = static_cast<int>(sumChildren - ownSum);
                    q.push(node->right);
                }
            }
        }
        return root;
    }
};
```

---

## 3️⃣ ️ Blog Article – *“Cousins in Binary Tree II: From Bugs to Brilliance (LeetCode 2641)”*  

> **SEO keywords:**  
> - *Cousins in Binary Tree II*  
> - *LeetCode 2641*  
> - *replace value in tree*  
> - *binary tree cousins*  
> - *job interview binary tree*  
> - *algorithm interview question*  
> - *binary tree BFS*  

---

# Cousins in Binary Tree II – A Job‑Interview‑Ready Guide

### 🚀 Why This Problem Rocks

> LeetCode 2641 is a **“light‑bulb”** question that shows up on many data‑structure interviews.  
> Solving it demonstrates mastery of:
> - level‑order traversal (BFS)  
> - bookkeeping of sums at each depth  
> - handling of edge‑cases (`null` children, single‑child nodes)  

If you nail this, recruiters will say: *“We’re impressed with your tree‑traversal intuition.”*

---

## 📌 Problem Restated (With a Twist)

> Given a binary tree, replace every node’s value with the **sum of its cousins’ values**.  
> The root is always set to 0 because it has no cousins.

> **Example**  
> ```
> Input:   1
>          / \
>         2   3
>        /   / \
>       4   5   6
> Output:  0
>         / \
>        2   3
>       /   / \
>      0   0   0
> ```
> (Here, 4’s cousins are 5 and 6 → 5 + 6 = 11, so 4’s new value is 11.)

---

## 🧭 Step‑by‑Step Walkthrough

1. **Root → 0**  
   The root never participates in cousin calculations, so we set it immediately.

2. **BFS Loop**  
   For every level (except the root), we:
   - **Collect** all nodes on the current level (`currentLevel`).  
   - **Sum all children** of these nodes → `totalChildSum`.
   - **For each node**:  
     * `ownChildSum` = sum of its own children.  
     * Each child’s new value = `totalChildSum – ownChildSum`.

3. **Queue for Next Level**  
   We push the updated children into the queue so that the next iteration works on the next depth.

4. **Termination**  
   When the queue is empty, the entire tree has been processed.

---

## 📈 ️Complexity Analysis  

| Measure | Calculation | Result |
|---------|-------------|--------|
| **Time** | Every node is visited once; constant work per node | **O(N)** |
| **Space** | Queue holds at most one level’s nodes (≤ H nodes) | **O(H)** |

---

## ⚠️ ️Common Pitfalls (“Bad” and “Ugly” parts)

| Issue | Why it matters | Fix |
|-------|----------------|-----|
| **Using `node.left.val` before it’s updated** | In the original JS conversion, child values were overwritten *before* the parent’s `curSum` was computed, corrupting the sum for the next level. | Compute `sumChildren` **first** from the original values, then update children in a second pass. |
| **Overflow on large trees** | Node values can be up to `10^5`; adding many children can exceed 32‑bit range. | Use `long long` (C++) or `int` with careful constraints (Java/Python handle it fine). |
| **Null checks omitted** | Accessing `node.left` when it’s `null` throws an exception. | Guard each child (`if (node.left != null) { … }`). |
| **Queue misuse** | Pushing `null` children can inflate queue size and introduce bugs. | Only enqueue existing children. |

---

## 🎯 ️Take‑away for Interviews

- **State the invariant clearly** – “All nodes in the current level share the same `totalChildSum`”.  
- **Show the math** – New value = `totalChildSum – ownChildSum`.  
- **Explain why root is 0** – it’s a corner case that eliminates a special branch in the loop.  
- **Mention the time/space trade‑off** – BFS uses O(H) extra space versus a recursive DFS that uses O(H) stack plus O(N) recursion depth.  

---

## 📢 ️Blog Post (SEO‑Optimized)

```markdown
# Cousins in Binary Tree II (LeetCode 2641) – Replace Value in Tree

## Table of Contents
1. 📌 Problem Overview
2. ✅ ️Solution Summary
3. 🧰 Java Implementation
4. 🧰 Python Implementation
5. 🧰 C++ Implementation
6. 📈 ️Complexity Analysis
7. ❌ ️Common Mistakes & How to Avoid Them
8. 🚀 ️Why You’ll Nail This in Your Next Coding Interview

## 📌 Problem Overview
Cousins in Binary Tree II is a classic LeetCode question that tests your ability to traverse a binary tree **level by level**, compute aggregate values, and update child nodes without disturbing the parent-child relationships. The goal is to replace each node’s value with the sum of all its cousins’ values.

> **Key Terms**  
> - *Cousins*: Same depth, different parents  
> - *Replace Value in Tree*: The operation you’ll implement

## ✅ ️Solution Summary
- Set root value to **0** (no cousins).  
- For each level, calculate the sum of **all children**.  
- For each node on that level, subtract the sum of that node’s **own** children from the total.  
- Update each child’s value to that difference and push it to the queue for the next level.

This **BFS** strategy runs in linear time **O(N)** and uses only **O(H)** auxiliary space.

## 🧰 Java Implementation
```java
class Solution {
    public TreeNode replaceValueInTree(TreeNode root) {
        if (root == null) return null;
        root.val = 0;
        Queue<TreeNode> q = new LinkedList<>();
        q.offer(root);
        while (!q.isEmpty()) {
            int size = q.size();
            List<TreeNode> level = new ArrayList<>();
            int sumChildren = 0;
            for (int i = 0; i < size; i++) {
                TreeNode node = q.poll();
                level.add(node);
                if (node.left != null) sumChildren += node.left.val;
                if (node.right != null) sumChildren += node.right.val;
            }
            for (TreeNode node : level) {
                int ownSum = 0;
                if (node.left != null) ownSum += node.left.val;
                if (node.right != null) ownSum += node.right.val;
                if (node.left != null) {
                    node.left.val = sumChildren - ownSum;
                    q.offer(node.left);
                }
                if (node.right != null) {
                    node.right.val = sumChildren - ownSum;
                    q.offer(node.right);
                }
            }
        }
        return root;
    }
}
```

## 🧰 Python Implementation
```python
class Solution:
    def replaceValueInTree(self, root: TreeNode) -> TreeNode:
        if not root:
            return None
        root.val = 0
        from collections import deque
        q = deque([root])
        while q:
            sz = len(q)
            level = []
            sum_children = 0
            for _ in range(sz):
                node = q.popleft()
                level.append(node)
                if node.left: sum_children += node.left.val
                if node.right: sum_children += node.right.val
            for node in level:
                own = 0
                if node.left: own += node.left.val
                if node.right: own += node.right.val
                if node.left:
                    node.left.val = sum_children - own
                    q.append(node.left)
                if node.right:
                    node.right.val = sum_children - own
                    q.append(node.right)
        return root
```

## 🧰 C++ Implementation
```cpp
class Solution {
public:
    TreeNode* replaceValueInTree(TreeNode* root) {
        if (!root) return nullptr;
        root->val = 0;
        queue<TreeNode*> q;
        q.push(root);
        while (!q.empty()) {
            int sz = q.size();
            vector<TreeNode*> level;
            long long sumChildren = 0;
            for (int i = 0; i < sz; ++i) {
                TreeNode* node = q.front(); q.pop();
                level.push_back(node);
                if (node->left)  sumChildren += node->left->val;
                if (node->right) sumChildren += node->right->val;
            }
            for (TreeNode* node : level) {
                long long ownSum = 0;
                if (node->left)  ownSum += node->left->val;
                if (node->right) ownSum += node->right->val;
                if (node->left) {
                    node->left->val = static_cast<int>(sumChildren - ownSum);
                    q.push(node->left);
                }
                if (node->right) {
                    node->right->val = static_cast<int>(sumChildren - ownSum);
                    q.push(node->right);
                }
            }
        }
        return root;
    }
};
```

## 📈 ️Complexity Analysis
| **Complexity** | **What It Means** |
|----------------|-------------------|
| Time **O(N)** | Every node is visited once. |
| Space **O(H)** | Only one level of nodes lives in the queue at a time. |

## ❌ ️Common Mistakes & How to Avoid Them
- **Updating children before computing `sumChildren`** – leads to incorrect totals.  
- **Null‑pointer dereference** – always check `if (node.left)` before accessing `.val`.  
- **Overflow on huge trees** – use 64‑bit integers in languages that support it.

## 🚀 ️Why You’ll Nail This in Your Next Coding Interview
- The problem blends **breadth‑first search** with **numeric reasoning** – a sweet spot for data‑structure aficionados.  
- Demonstrates you can handle **edge‑cases** like single‑child nodes and null pointers gracefully.  
- Shows you’re comfortable writing clean, **O(H)** BFS code that is easily translatable between Java, Python, and C++.

> **Take home**: Master this, and recruiters will see you as a *tree‑traversal guru* ready for the toughest algorithmic challenges.

```

---

**Feel free to adapt, publish, and share your mastery of Cousins in Binary Tree II!**