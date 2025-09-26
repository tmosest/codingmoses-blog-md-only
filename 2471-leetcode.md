---
title: LeetCode 2471. Minimum Number of Operations to Sort a Binary Tree by Level - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem Recap – LeetCode 2471  
**Minimum Number of Operations to Sort a Binary Tree by Level**

> You are given a binary tree with **unique** values.  
> In one operation you may pick **any two nodes that live on the same level** and swap their values.  
> The goal is to make every level of the tree strictly increasing from left to right using the *fewest* swaps possible.  

The task is to return that minimum number of swaps.

---

## 🚀 High‑Level Insight

For each depth‑level independently, we just have to sort an array of numbers by swapping elements.  
The minimal number of swaps required to sort an array where *any* pair may be swapped equals

```
length_of_array – number_of_cycles_in_the_permutation
```

because each cycle of length `k` can be fixed in `k‑1` swaps.  

Thus the whole problem is a *level‑by‑level* application of this simple permutation trick.

---

## 📊 Algorithm

| Step | What we do | Why it works |
|------|------------|--------------|
| 1 | Perform a **BFS** of the tree, gathering nodes level by level. | We need the nodes on each depth in order to sort them. |
| 2 | For a given level `L` of size `m`: <br>• Create a vector of pairs `(value, original_index)`. <br>• Sort this vector by `value`. | The sorted order tells us *where* every original element has to go. |
| 3 | Build a permutation array `pos` such that `pos[original_index] = sorted_index`. <br>Traverse `pos` to count cycles. | A cycle of length `k` needs `k-1` swaps, which is minimal. |
| 4 | Add `cycle_length-1` to the global answer for every cycle. | Summing over all levels gives the final answer. |

---

## 🧮 Complexity Analysis

| Metric | Per Level | Whole Tree |
|--------|-----------|------------|
| **Time** | `O(m log m)` (sorting) + `O(m)` (cycle count) | `O(N log N)` (sum of all `m` is `N`) |
| **Space** | `O(m)` temporary arrays | `O(N)` for BFS queue + temp arrays |

With `N ≤ 10⁵`, this easily fits into time/memory limits.

---

## 🎯 The Code

Below you will find **complete, ready‑to‑paste** solutions in **Java**, **Python 3**, and **C++17**.  
All three use the same BFS + cycle‑counting idea described above.

> **Tip** – The TreeNode definitions are provided only for completeness; on LeetCode the framework already supplies them.

---

### Java

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
    public int minimumOperations(TreeNode root) {
        if (root == null) return 0;

        Queue<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        int answer = 0;

        while (!q.isEmpty()) {
            int size = q.size();
            int[] vals = new int[size];
            int[] idx  = new int[size];

            // Collect nodes of current level
            for (int i = 0; i < size; i++) {
                TreeNode node = q.poll();
                vals[i] = node.val;
                idx[i]  = i;                 // original position

                if (node.left  != null) q.offer(node.left);
                if (node.right != null) q.offer(node.right);
            }

            // Pair value with its original index and sort by value
            Integer[] order = new Integer[size];
            for (int i = 0; i < size; i++) order[i] = i;
            Arrays.sort(order, Comparator.comparingInt(i -> vals[i]));

            // Build permutation: where each original index lands after sorting
            int[] pos = new int[size];
            for (int sortedPos = 0; sortedPos < size; sortedPos++) {
                pos[order[sortedPos]] = sortedPos;
            }

            // Count cycles in permutation
            boolean[] visited = new boolean[size];
            for (int i = 0; i < size; i++) {
                if (visited[i] || pos[i] == i) continue; // already in place
                int cycleSize = 0;
                int j = i;
                while (!visited[j]) {
                    visited[j] = true;
                    j = pos[j];
                    cycleSize++;
                }
                answer += cycleSize - 1;   // minimal swaps for this cycle
            }
        }
        return answer;
    }
}
```

---

### Python 3

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

class Solution:
    def minimumOperations(self, root: TreeNode) -> int:
        if not root:
            return 0

        from collections import deque
        q = deque([root])
        ans = 0

        while q:
            size = len(q)
            vals = [0] * size
            idx  = list(range(size))

            # Grab nodes of current depth
            for i in range(size):
                node = q.popleft()
                vals[i] = node.val
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)

            # Sort by value while keeping original indices
            order = sorted(range(size), key=lambda i: vals[i])
            pos = [0] * size
            for sorted_pos, orig in enumerate(order):
                pos[orig] = sorted_pos

            # Count cycles
            visited = [False] * size
            for i in range(size):
                if visited[i] or pos[i] == i:
                    continue
                cycle_len = 0
                j = i
                while not visited[j]:
                    visited[j] = True
                    j = pos[j]
                    cycle_len += 1
                ans += cycle_len - 1

        return ans
```

---

### C++17

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
    int minimumOperations(TreeNode* root) {
        if (!root) return 0;
        queue<TreeNode*> q;
        q.push(root);
        int ans = 0;

        while (!q.empty()) {
            int sz = q.size();
            vector<int> vals(sz);
            // Gather current level
            for (int i = 0; i < sz; ++i) {
                TreeNode* node = q.front(); q.pop();
                vals[i] = node->val;
                if (node->left)  q.push(node->left);
                if (node->right) q.push(node->right);
            }

            // Indices of nodes before sorting
            vector<int> order(sz);
            iota(order.begin(), order.end(), 0);

            // Sort indices by the value they point to
            sort(order.begin(), order.end(),
                 [&](int a, int b){ return vals[a] < vals[b]; });

            // Build permutation: original index -> sorted index
            vector<int> pos(sz);
            for (int sortedIdx = 0; sortedIdx < sz; ++sortedIdx) {
                pos[order[sortedIdx]] = sortedIdx;
            }

            // Cycle detection
            vector<bool> vis(sz, false);
            for (int i = 0; i < sz; ++i) {
                if (vis[i] || pos[i] == i) continue;
                int cycle = 0;
                int j = i;
                while (!vis[j]) {
                    vis[j] = true;
                    j = pos[j];
                    ++cycle;
                }
                ans += cycle - 1;
            }
        }
        return ans;
    }
};
```

---

## 🎭 “Good – Bad – Ugly” in this LeetCode Interview

| Aspect | What’s *Good* | What’s *Bad* | What’s *Ugly* |
|--------|--------------|--------------|--------------|
| **Problem Structure** | *Unique values* guarantee a bijection between current order and sorted order. | None. | None. |
| **Data Collection** | BFS guarantees you always know the left‑to‑right order of a level. | Requires a queue that can hold up to `N` nodes → memory pressure on gigantic trees. | The same queue in C++/Java/Python; the memory cost is unavoidable. |
| **Sorting** | Sorting per level gives a clean, deterministic result. | Sorting every level can add up to `N log N` work – still fast enough but not linear. | In the very early solutions people used bit‑packing tricks (`value<<20 | index`) to sort the whole tree at once. It works but hurts readability. |
| **Cycle Counting** | Simple math: `cycle_size-1` swaps per cycle. | Requires a separate `visited` array per level → small overhead. | Some naive people count “misplaced elements” instead of cycles, giving a non‑minimal answer. |

---

## 📚 Take‑Away for Interviews

1. **Understand the “any two swap” model** – it reduces sorting to a permutation problem.  
2. **Break it into independent sub‑problems** – a tree level behaves like a simple array.  
3. **Count cycles, not inversions** – cycles give you the exact minimal swaps.  
4. **Always code a clean BFS** – it keeps the logic easy to reason about.

---

## 🏁 TL;DR

- **Collect each level** with BFS.  
- **Sort** the values while keeping original positions.  
- **Build a permutation** that tells where each original value should go.  
- **Count cycles**; the number of swaps needed for a cycle of length `k` is `k‑1`.  
- **Sum** over all levels → final answer.

The three language snippets above are essentially the same algorithm; pick the one that matches your interview prep stack.

---

## 🔍 SEO Keywords & Headings

- **LeetCode 2471**  
- **Minimum Number of Operations to Sort a Binary Tree by Level**  
- **Binary tree level sorting**  
- **Tree sorting by swapping**  
- **LeetCode solution**  
- **software engineering interview**  
- **binary tree interview problem**  
- **tree level sorting algorithm**  
- **O(N log N) LeetCode solution**  

Including these terms in your README or blog post will help recruiters and fellow candidates find the content when searching for interview prep resources. Happy coding! 🚀