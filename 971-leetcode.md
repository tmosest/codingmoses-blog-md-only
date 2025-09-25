---
title: LeetCode 971. Flip Binary Tree To Match Preorder Traversal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ Flip Binary Tree to Match Preorder Traversal – 971 (LeetCode)

> **Problem** –  
> Given a binary tree whose node values are unique 1…n and a desired preorder traversal `voyage` (also 1…n), flip any node (swap its left and right children) so that the tree’s preorder equals `voyage`.  
> Return the list of node values that were flipped. If impossible, return `[-1]`.

| Language | Runtime | Memory | Complexity |
|----------|---------|--------|------------|
| **Java** | O(n)   | O(n)   | O(n)       |
| **Python** | O(n)   | O(n)   | O(n)       |
| **C++** | O(n)   | O(n)   | O(n)       |

> **SEO Keywords**: LeetCode 971, Flip Binary Tree, Preorder Traversal, Binary Tree Flipping, DFS, Interview Algorithm, Java, Python, C++.

---

## 🔧 Code Implementations

### 1. Java (DFS)

```java
import java.util.*;

class Solution {
    private List<Integer> answer = new ArrayList<>();
    private int index = 0;          // pointer to current element in voyage

    public List<Integer> flipMatchVoyage(TreeNode root, int[] voyage) {
        dfs(root, voyage);
        return answer;
    }

    private void dfs(TreeNode node, int[] voyage) {
        if (node == null) return;

        // impossible early exit
        if (!answer.isEmpty() && answer.get(answer.size() - 1) == -1) return;

        // current node must match voyage[index]
        if (node.val != voyage[index++]) {
            answer.clear();
            answer.add(-1);
            return;
        }

        // decide if we need to flip
        if (node.left != null && node.left.val != voyage[index]) {
            // flip required
            answer.add(node.val);
            dfs(node.right, voyage);  // visit right first
            dfs(node.left, voyage);
        } else {
            // no flip, normal preorder
            dfs(node.left, voyage);
            dfs(node.right, voyage);
        }
    }
}
```

### 2. Python (DFS)

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def flipMatchVoyage(self, root: TreeNode, voyage: List[int]) -> List[int]:
        self.idx = 0
        self.res = []

        def dfs(node: TreeNode):
            if not node:
                return
            # impossible early exit
            if self.res and self.res[0] == -1:
                return

            if node.val != voyage[self.idx]:
                self.res = [-1]
                return
            self.idx += 1

            # need to flip if left child doesn't match next voyage
            if node.left and node.left.val != voyage[self.idx]:
                self.res.append(node.val)
                dfs(node.right)
                dfs(node.left)
            else:
                dfs(node.left)
                dfs(node.right)

        dfs(root)
        return self.res
```

### 3. C++ (DFS)

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
    vector<int> flipMatchVoyage(TreeNode* root, vector<int>& voyage) {
        idx = 0;
        ans.clear();
        dfs(root, voyage);
        return ans;
    }

private:
    int idx;                  // index in voyage
    vector<int> ans;          // answer list

    void dfs(TreeNode* node, const vector<int>& voyage) {
        if (!node) return;
        if (!ans.empty() && ans[0] == -1) return;  // impossible early exit

        if (node->val != voyage[idx]) {            // mismatch
            ans.clear();
            ans.push_back(-1);
            return;
        }
        ++idx;

        // Flip if left child doesn't match next element
        if (node->left && node->left->val != voyage[idx]) {
            ans.push_back(node->val);
            dfs(node->right, voyage);
            dfs(node->left, voyage);
        } else {
            dfs(node->left, voyage);
            dfs(node->right, voyage);
        }
    }
};
```

---

## 📚 Blog Article – *“Flip Binary Tree to Match Preorder Traversal: Good, Bad, and Ugly”*

> **Meta Description** – Master LeetCode 971 with a clear DFS solution in Java, Python, and C++. Learn the good, bad, and ugly parts of binary‑tree flipping, plus interview‑ready tips and pitfalls.

### 1. Introduction

If you’re prepping for a software‑engineering interview, binary‑tree problems are a staple. One of the trickier yet highly‑scoring LeetCode questions is **Flip Binary Tree To Match Preorder Traversal** (971). It tests your understanding of preorder traversal, recursion, and state‑tracking. In this article, we’ll dissect the problem, present a clean DFS implementation, and highlight **good**, **bad**, and **ugly** aspects of typical solutions—so you can nail it in the interview room.

### 2. Problem Recap

- **Input**: `root` of a binary tree, array `voyage` of size `n` (values 1…n, unique).
- **Operation**: “Flip” a node means swapping its left and right subtrees.
- **Goal**: Flip as few nodes as possible so that the preorder traversal of the tree equals `voyage`.  
  Return the list of flipped node values, or `[-1]` if impossible.

### 3. Intuition – Walking Preorder in Parallel

Think of a **preorder traversal** as a strict sequence: **root → left → right**.  
The `voyage` array tells us *what* we should see next.  
If we visit the tree in preorder and keep an index `i` into `voyage`, we can decide on‑the‑fly whether a flip is required.

> **Key Insight**  
> At a node, if the next expected value in `voyage` is **not** the value of its left child, the only way to make the sequence match is to **flip** that node and visit the right child first.

This simple rule drives all correct solutions.

### 4. Algorithm – DFS with a Global Index

1. **Initialize** `i = 0`, `answer = []`.
2. **Recursive DFS(node)**  
   * If `node == null` → return.  
   * If `node.val != voyage[i]` → impossible → `answer = [-1]`.  
   * Increment `i`.  
   * **Flip Decision**  
     *If left child exists and its value ≠ `voyage[i]` →**  
     Add `node.val` to `answer` and recurse as `right → left`.  
     *Else* → recurse `left → right` normally.

3. **Return** `answer` (might be `[-1]`).

### 4. Code Review – Good, Bad & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n) – each node visited once. | | |
| **Space Complexity** | O(h) recursion stack (≤ n). | | |
| **Clarity** | Global index + short conditional. | Hard‑to‑read if nested loops or too many early returns. | Over‑using global variables or mutating `voyage` (e.g., `pop()` in Python) makes the logic brittle. |
| **Early Exit** | Clear check for impossible case (`ans[0] == -1`). | Forgetting early exit leads to wrong answers on large trees. | Throwing exceptions inside recursion for failure (not interview‑friendly). |
| **Edge‑Case Handling** | Handles `null` children, empty tree, `n = 1`. | Failing when `index` goes out of bounds. | Assuming left/right always non‑null – leads to segmentation faults in C++. |
| **Language Idioms** | Java `List<Integer>`, Python `List[int]`, C++ `vector<int>`. | Use of static/global variables may look un‑idiomatic in some languages. | Mixing iterative and recursive logic in one function can confuse interviewers. |

### 5. Complexity Analysis

- **Time**: Each node is processed once → **O(n)**.  
- **Space**: Recursion depth ≤ tree height `h` (worst case `O(n)`).  
- **Worst‑case flips**: Up to `n` (every node might need a flip).

### 6. Common Pitfalls & How to Avoid Them

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| Forgetting to increment the voyage index | Mis‑aligned sequence | `i++` immediately after a match. |
| Not checking for `null` before accessing child value | RuntimeError / SegFault | `if (node.left && node.left.val != voyage[i])` |
| Returning `answer` before the entire tree is processed | Early termination after a single flip | Use an “impossible flag” (`answer[0] == -1`) to exit early, but only after setting it. |
| Using iterative stack but not preserving the preorder order after a flip | Mis‑ordered visit | Simpler to stick with recursion for readability in interviews. |

### 7. Alternative Approaches

- **Iterative DFS with a stack** – simulate preorder manually; still needs the same index logic.  
- **Dynamic Programming** – overkill for this size; unnecessary extra space.  
- **BFS + Heuristic** – wrong order; preorder is depth‑first.

> **Recommendation**  
> Stick to the recursive DFS; it’s concise, expressive, and most interviewers expect a recursive solution for tree problems.

### 8. Interview‑Ready Tips

1. **State Tracking** – Explain that the `index` variable keeps the traversal state.  
2. **Flip Condition** – Emphasize the decision rule: “if left child doesn’t match the next voyage element, flip”.
3. **Early Exit** – Mention the impossible flag to avoid needless work.
4. **Edge Cases** – Discuss what happens with a single node, an empty tree, or when `voyage` is already a valid preorder.
5. **Testing** – Write unit tests for the provided examples, plus random trees + random voyages to catch edge cases.

### 9. Final Thoughts

**Flip Binary Tree To Match Preorder Traversal** is a perfect blend of tree traversal and state management. The recursive DFS solution is elegant, runs in linear time, and is straightforward to explain in an interview. By highlighting the good (clear state, early exit), bad (mutating global state without protection), and ugly (unnecessary complexity), you’ll be ready to demonstrate both code mastery and problem‑solving mindset.

> **Takeaway** – Master the DFS “walk‑in‑parallel” pattern; it will help you solve this LeetCode 971 and many other tree‑flipping or rearrangement problems that show up in coding interviews.

---

### 10. Conclusion

With the code snippets above and the interview‑ready commentary, you’re all set to crush LeetCode 971. Practice explaining the intuition, complexity, and edge‑case handling, then showcase the clean implementation in **Java**, **Python**, or **C++**. Good luck, and may those flips be minimal and your answers correct! 🚀

---

> **Author**: *Your Name* – Software Engineer & Interview Coach  
> **Contact**: <your.email@example.com> | **LinkedIn**: <https://linkedin.com/in/yourprofile>  
> **Follow**: #LeetCode #BinaryTree #InterviewPrep #AlgorithmDesign

--- 

> *Disclaimer*: The above article is SEO‑optimized for interview‑preparation blogs and may be used for personal study or shared with your peers. Happy coding!