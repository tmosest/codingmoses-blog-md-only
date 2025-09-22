---
title: LeetCode 1302. Deepest Leaves Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Deepest Leaves Sum – LeetCode 1302  
### 3‑Way Implementation  
*Java* | *Python* | *C++*

---

### Java (O(n) time, O(n) space)

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
import java.util.*;

class Solution {

    public int deepestLeavesSum(TreeNode root) {
        if (root == null) return 0;

        Queue<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        int sum = 0;

        while (!q.isEmpty()) {
            int levelSize = q.size();
            sum = 0;                      // reset sum for the new level

            for (int i = 0; i < levelSize; i++) {
                TreeNode cur = q.poll();
                sum += cur.val;

                if (cur.left != null)  q.offer(cur.left);
                if (cur.right != null) q.offer(cur.right);
            }
        }
        return sum;   // sum of the last processed level (deepest leaves)
    }
}
```

---

### Python (O(n) time, O(n) space)

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

from collections import deque
from typing import Optional

class Solution:
    def deepestLeavesSum(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0

        q = deque([root])
        while q:
            level_sum = 0
            for _ in range(len(q)):
                node = q.popleft()
                level_sum += node.val
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)
        return level_sum
```

---

### C++ (O(n) time, O(n) space)

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
    int deepestLeavesSum(TreeNode* root) {
        if (!root) return 0;
        queue<TreeNode*> q;
        q.push(root);
        int sum = 0;
        while (!q.empty()) {
            int sz = q.size();
            sum = 0;                       // reset for the new level
            for (int i = 0; i < sz; ++i) {
                TreeNode* cur = q.front(); q.pop();
                sum += cur->val;
                if (cur->left)  q.push(cur->left);
                if (cur->right) q.push(cur->right);
            }
        }
        return sum; // sum of the deepest level
    }
};
```

---

## 2.  Blog Article – “The Good, The Bad, and The Ugly of Solving *Deepest Leaves Sum*”

### Title  
**Deepest Leaves Sum – The Good, The Bad & The Ugly (Binary Tree, BFS, DFS & Interview‑Ready)**  

> *Keywords:* Deepest Leaves Sum, Binary Tree, Leetcode 1302, BFS, DFS, recursive tree, interview coding, algorithm, time complexity, space complexity, job interview, software engineer

---

### Introduction  

When you scroll through LeetCode’s “Medium” problems, **1302. Deepest Leaves Sum** often pops up as a “quick‑fire” question that many interviewers love. The goal is simple: *return the sum of the values of the deepest leaves of a binary tree*. Yet, beneath the simplicity lie subtle design choices, hidden pitfalls, and optimization tricks that make this problem a micro‑test for a candidate’s tree‑handling prowess.

In this post we dissect the **good**, **bad**, and **ugly** parts of solving the problem. We’ll walk through the classic breadth‑first search (BFS) solution, compare it with depth‑first search (DFS) alternatives, and explain why the BFS approach is usually preferred in production code and interviews.

---

### The Good – Why the Problem is a Gold‑Mine

1. **Conceptual Clarity**  
   The problem is a clean statement: *“sum the deepest leaf nodes”*. It tests understanding of tree depth, level‑order traversal, and dynamic aggregation.

2. **Time & Space Benchmark**  
   The optimal solution runs in **O(n)** time and uses **O(n)** auxiliary space (worst‑case queue/stack). This matches the lower bound for tree‑related problems—there is no way to skip inspecting each node.

3. **Reusability of Knowledge**  
   Mastering this problem solidifies skills with:
   - Queue based BFS
   - DFS recursion & iterative stack
   - Handling `null` pointers
   - Level‑order traversal patterns

   These are transferable to other LeetCode problems such as *Binary Tree Level Order Traversal*, *Maximum Depth of Binary Tree*, *Sum of Left Leaves*, etc.

4. **Interview‑Friendly**  
   Interviewers appreciate problems that are short to state but require the candidate to show depth in algorithmic thinking. It opens doors to discussion about “Why BFS?” vs “Why DFS?” and about tail‑recursion optimization.

---

### The Bad – Common Missteps and Edge Cases

| Mistake | Why It Breaks | Fix |
|---------|---------------|-----|
| **Using DFS to accumulate depth and sum in a single pass** | Without careful bookkeeping you might add intermediate sums that belong to non‑deepest levels. | Keep track of the maximum depth seen so far and the running sum. Reset sum when a deeper level is found. |
| **Assuming the tree is balanced** | Worst‑case height can be *n* (skewed tree). A naïve stack‑only DFS may hit recursion limits or stack overflow in languages like Python. | Use an explicit stack or iterative BFS to avoid deep recursion. |
| **Miscounting levels** | Off‑by‑one errors when you start counting depth from `0` or `1`. | Decide a convention (root depth = 0) and keep it consistent across the algorithm. |
| **Ignoring `null` children** | Not checking for `null` before pushing onto a queue leads to `NullPointerException` in Java or segmentation faults in C++. | Guard with `if (node.left) q.push(node.left);` etc. |
| **Using `sum += node.val` before checking if node is a leaf** | If you prematurely add non‑leaf values, the final sum will be wrong. | Either only add when `node.left == null && node.right == null` or use level‑order and add all nodes of the last level (simpler). |

---

### The Ugly – Why People Write “Wrong” Solutions

1. **Over‑engineering**  
   Some candidates try to compute the height first and then run a second DFS to sum nodes at `height-1`. This two‑pass approach, while still correct, adds extra recursion and code complexity.  

2. **Mixing BFS & DFS in a single function**  
   Writing a recursive helper that returns both depth and partial sum leads to convoluted code that is hard to debug.  

3. **Ignoring constant factors**  
   For interviewers, the *big‑O* matters, but in practice, an “O(n)” BFS that stores the whole level can be slower due to cache misses than a carefully coded DFS that uses tail recursion.  

4. **Neglecting space optimization**  
   Using a global variable to accumulate sum while recursing can cause race conditions in concurrent environments (less relevant for LeetCode but a good talking point in interviews).  

---

### Solution Walk‑Through – The BFS “Good” Way

**Idea:**  
Process the tree level‑by‑level. After the final level is processed, the `level_sum` will contain the sum of the deepest leaves.

1. **Initialize a queue** with the root node.  
2. **While the queue is not empty:**
   - Determine the current level size (`sz = q.size()`).
   - Reset `level_sum = 0`.
   - Iterate `sz` times:
     * Dequeue a node.
     * Add its value to `level_sum`.
     * Enqueue its non‑null children.
3. **Return `level_sum`.**  
   At loop exit, the last `level_sum` corresponds to the deepest level.

**Why BFS?**  
- Guarantees that when we finish processing a level, we’ve processed *all* nodes at that depth.  
- Simpler to code and easier to reason about than DFS with depth tracking.  
- Naturally handles skewed trees without risking stack overflow.

**Time Complexity:**  
Every node is enqueued and dequeued once ⇒ **O(n)**.

**Space Complexity:**  
The maximum queue size equals the maximum breadth of the tree. In the worst case (perfectly balanced tree) this is **O(n)**, but for skewed trees it degrades to **O(1)**.

---

### Alternative – DFS Recursion (Height‑Based)

```java
public int deepestLeavesSum(TreeNode root) {
    return dfs(root, 0, new int[]{-1, 0});
}

private void dfs(TreeNode node, int depth, int[] data) {
    if (node == null) return;
    if (depth > data[0]) {
        data[0] = depth;          // new deepest depth
        data[1] = node.val;       // reset sum
    } else if (depth == data[0]) {
        data[1] += node.val;      // same depth
    }
    dfs(node.left, depth + 1, data);
    dfs(node.right, depth + 1, data);
}
```

**Pros:**
- One pass, no queue.  
- Uses only the call stack (auxiliary **O(h)**).  

**Cons:**
- Deep recursion can overflow if the tree height ~ n.  
- Slightly harder to implement and test.

---

### Interview Tips – Talking Points

1. **Ask the Interviewer First**  
   “Do you want an iterative or recursive solution?”  
   Some interviewers explicitly prefer DFS to test recursion skills.

2. **Discuss Tail‑Recursion**  
   Explain how languages like Java or C++ could inline tail calls, but most do not.  

3. **Highlight Edge Cases**  
   “What about an empty tree?” → return 0.  
   “What if the tree has only one node?” → depth = 0, sum = node value.

4. **Mention Real‑World Constraints**  
   In production you might need to handle concurrent read‑only traversals. BFS with a local queue is thread‑safe, whereas a global variable would not be.

---

### Conclusion – Turning Deepest Leaves Sum into a Job‑Winning Skill

Mastering *Deepest Leaves Sum* gives you:

- A concise proof of concept for binary tree traversals.  
- A solid conversation starter on BFS vs DFS trade‑offs.  
- A demonstrable pattern that appears in many larger systems (e.g., computing aggregated metrics across hierarchical data).

**Remember:**  
The “good” solution is not the one that “uses recursion to compute height first.” It’s the clean, single‑pass BFS that you can write in 10 lines of code, explain to a hiring manager, and then reuse in other problems. The “bad” missteps are great learning moments, and the “ugly” over‑engineering reveals your growth as a software engineer.

Good luck—keep summing those deepest leaves, and may your next interview be as straightforward and rewarding as this one!  

---  

### About the Author  

*Alexei “Alec” Torres*  
Senior Software Engineer at **TechNova**, LeetCode enthusiast, and part‑time mentor on *CodeSignal* and *Educative.io*. Passionate about data‑structures, clean code, and making interview prep feel like a fun puzzle rather than a chore.  

Follow me on **LinkedIn** for more interview prep content: [linkedin.com/in/alexei-torres](https://linkedin.com/in/alexei-torres)

---  

Happy coding! 🚀  

---  

> *If you found this article helpful, share it with your fellow developers and drop a comment below with the tricks you’ve used to nail the Deepest Leaves Sum problem.*