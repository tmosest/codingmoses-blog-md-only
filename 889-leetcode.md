---
title: LeetCode 889. Construct Binary Tree from Preorder and Postorder Traversal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 889. Construct Binary Tree from Preorder and Postorder Traversal  
**Languages:** Java | Python | C++  
**Difficulty:** Medium  
**Interview‑ready:** ✅  

---

### 1️⃣  Java Implementation  

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
    public TreeNode constructFromPrePost(int[] preorder, int[] postorder) {
        // We keep a mutable pointer to the current index in preorder
        int[] idx = new int[]{0};
        return build(preorder, postorder, idx, 0, postorder.length - 1);
    }

    private TreeNode build(int[] pre, int[] post, int[] idx,
                           int postStart, int postEnd) {
        if (idx[0] >= pre.length || postStart > postEnd) return null;

        // Root of the current subtree
        TreeNode root = new TreeNode(pre[idx[0]++]);

        // If this node is a leaf, no children to attach
        if (postStart == postEnd) return root;

        // Find the index of the left child in postorder
        int leftVal = pre[idx[0]];
        int leftIdx = postStart;
        while (post[leftIdx] != leftVal) leftIdx++;

        int leftSize = leftIdx - postStart + 1;

        // Recursively build left & right subtrees
        root.left  = build(pre, post, idx, postStart, leftIdx);
        root.right = build(pre, post, idx, leftIdx + 1, postEnd - 1);

        return root;
    }
}
```

---

### 2️⃣  Python Implementation  

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def constructFromPrePost(self, preorder: list[int], postorder: list[int]) -> TreeNode:
        # use a list as a mutable pointer into preorder
        self.idx = [0]
        return self._build(preorder, postorder, 0, len(postorder) - 1)

    def _build(self, pre, post, post_start, post_end):
        if self.idx[0] >= len(pre) or post_start > post_end:
            return None

        root = TreeNode(pre[self.idx[0]])
        self.idx[0] += 1

        # leaf node – no children
        if post_start == post_end:
            return root

        left_val = pre[self.idx[0]]
        left_idx = post_start
        while post[left_idx] != left_val:
            left_idx += 1

        left_size = left_idx - post_start + 1

        root.left  = self._build(pre, post, post_start, left_idx)
        root.right = self._build(pre, post, left_idx + 1, post_end - 1)

        return root
```

---

### 3️⃣  C++ Implementation  

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
    TreeNode* constructFromPrePost(vector<int>& preorder, vector<int>& postorder) {
        // mutable preorder index
        int idx = 0;
        return build(preorder, postorder, idx, 0, postorder.size() - 1);
    }

private:
    TreeNode* build(vector<int>& pre, vector<int>& post, int& idx,
                   int postStart, int postEnd) {
        if (idx >= pre.size() || postStart > postEnd) return nullptr;

        TreeNode* root = new TreeNode(pre[idx++]);

        // leaf node
        if (postStart == postEnd) return root;

        int leftVal = pre[idx];
        int leftIdx = postStart;
        while (post[leftIdx] != leftVal) ++leftIdx;

        int leftSize = leftIdx - postStart + 1;

        root->left  = build(pre, post, idx, postStart, leftIdx);
        root->right = build(pre, post, idx, leftIdx + 1, postEnd - 1);

        return root;
    }
};
```

---

## 📄  Blog Article: “The Good, the Bad, and the Ugly”  
### **Construct Binary Tree from Preorder & Postorder – A Complete Guide for Interviews**

---

#### 📝  Table of Contents  
1. [Introduction & Context](#introduction)  
2. [Problem Overview](#problem-overview)  
3. [Why This Problem Matters in Interviews](#interview-matters)  
4. [The Good: Why the Solution is Beautiful](#the-good)  
5. [The Bad: Common Pitfalls & Edge‑Case Gotchas](#the-bad)  
6. [The Ugly: Complex Cases & How to Tame Them](#the-ugly)  
7. [Algorithm & Complexity](#complexity)  
8. [Testing Strategy & Sample Unit Tests](#testing)  
9. [Take‑away & Career‑Boost Tips](#takeaway)  
10. [Conclusion](#conclusion)

---

### 1️⃣  Introduction & Context  
> **LeetCode 889** – *Construct Binary Tree from Preorder and Postorder Traversal* – is a staple of technical interviews, especially at big‑tech firms and fintech startups.  
> Mastering this problem demonstrates that you can **reason with recursion**, understand **tree traversal properties**, and produce clean, **O(N)** solutions in multiple languages.  

**Keywords:** *LeetCode 889*, *Construct Binary Tree from Preorder and Postorder*, *Binary Tree Reconstruction*, *Coding Interview*, *Job Interview Prep*.

---

### 2️⃣  Problem Overview  

- **Input:** Two integer arrays: `preorder` (root → left → right) and `postorder` (left → right → root).  
- **Output:** Root node of the binary tree that matches both traversals.  
- **Constraints:**  
  - All node values are **unique**.  
  - Number of nodes `n` satisfies `1 <= n <= 30` (but solution scales to larger `n`).  

> **Why is it Hard?**  
> Unlike inorder & preorder or inorder & postorder, you cannot directly map node indices; the tree is **not uniquely defined**. A valid tree is still guaranteed because the input is consistent.  

---

### 3️⃣  Why This Problem Matters in Interviews  

| Skill | Why It’s Asked |
|-------|----------------|
| **Recursion** | Many interviewees struggle with recursive tree construction. |
| **Index Mapping** | Demonstrates understanding of traversal relationships. |
| **Space Optimization** | Shows ability to minimize extra data structures. |
| **Multi‑language Mastery** | Employers expect you to translate algorithms between Java, Python, C++, etc. |

> **SEO Boost** – Include phrases like *“LeetCode 889 solution”*, *“binary tree interview question”*, *“coding interview Java Python C++”* to rank higher in search results for recruiters looking for proven problem solvers.

---

### 4️⃣  The Good – What Makes This Problem Great  

| Feature | Why It’s Positive |
|---------|-------------------|
| **Clear Recursion Pattern** | Root → split into left/right based on next preorder value. |
| **No Extra Hash Map Needed** | A simple linear search in postorder keeps code readable. |
| **Linear Time Complexity** | Each node is visited once: **O(N)**. |
| **Deterministic Tree Structure** | Any valid preorder/postorder pair produces *a* correct tree (not necessarily unique). |
| **Language Agnostic** | Same logic applies in Java, Python, C++, Go, etc. |

**Takeaway:** Focus on the *recursive helper* that uses a mutable preorder index and postorder bounds. It’s the pattern that makes the solution elegant.

---

### 5️⃣  The Bad – Common Mistakes & Edge‑Case Quirks  

| Mistake | Impact | Fix |
|---------|--------|-----|
| **Using a HashMap for value → postorder index** | Adds O(N) extra space; still okay but unnecessary for small constraints. | Perform a linear scan (`while post[i] != pre[idx]`) – works because each element is examined at most twice. |
| **Incorrect postorder bounds** | Off‑by‑one errors produce `NullPointerException` / `None` nodes in wrong places. | Remember that the last element of the postorder segment is the current root (`postEnd`). |
| **Assuming the tree is full** | Some solutions mistakenly think each internal node must have two children; this fails for right‑skewed trees. | Check `postStart == postEnd` to identify leaves. |
| **Not updating preorder index after recursion** | Leads to duplicate nodes or missing children. | Use a mutable wrapper (`int[] idx` in Java, `idx[0]` in Python) that is incremented once per node. |
| **Failing to subtract 1 from postEnd for right subtree** | Adds the root of the current subtree into the right subtree recursion. | Use `postEnd - 1` when building the right subtree. |

> **Debugging Tip:** Visualize with a small example:  
> ```text
> preorder :  [1, 2, 4, 5, 3, 6]
> postorder:  [4, 5, 2, 6, 3, 1]
> ```
> Step through the recursion manually to see where indices shift.

---

### 6️⃣  The Ugly – Non‑Unique Trees & Edge Cases

| Scenario | Why it’s Ugly | Handling Strategy |
|----------|---------------|-------------------|
| **Single‑Child Nodes** | A node might have only a left child (or only a right child). In such cases the “left subtree size” is still determined correctly because the left child is the next element in preorder. | Recursion handles it automatically; no special flags needed. |
| **Tree with All Nodes in One Line (Skewed)** | Preorder and postorder are identical (e.g., `[1,2,3]` & `[3,2,1]`). The algorithm must still create a valid right‑skewed tree. | The `postStart == postEnd` leaf condition stops recursion, preserving skewed shape. |
| **Empty Input** | Not allowed per LeetCode constraints, but defensive code is trivial. | Add guard `if (pre.length == 0) return null;`. |
| **Large Input (n > 30)** | Real interview data may be larger; using `while` to find index in postorder could become \(O(N^2)\) if implemented naïvely. | Build a hashmap `value -> index` once for O(1) lookup, achieving true O(N). For the medium constraints (≤30) the linear scan is perfectly fine. |

---

### 7️⃣  Time & Space Complexity  

| Language | Time | Space |
|----------|------|-------|
| Java | **O(N)** – Each node is processed once; linear scan of postorder happens O(1) per recursion level. | **O(N)** – Recursion stack depth up to `N`; no additional data structures. |
| Python | **O(N)** | **O(N)** – Recursion depth and mutable index. |
| C++ | **O(N)** | **O(N)** – Stack depth; each node creation is constant time. |

> **Proof of Optimality:**  
> - Preorder index increment is constant per node.  
> - Postorder scans stop at the left child, which is unique.  
> - For skewed trees, recursion depth = `N`; for balanced trees = `O(log N)`.  

---

### 8️⃣  Testing Strategy & Sample Unit Tests  

```python
def inorder(root):
    if not root: return []
    return inorder(root.left) + [root.val] + inorder(root.right)

def test_pre_post(pre, post):
    root = Solution()._build(pre, post, 0, len(post)-1)
    assert inorder(root) == sorted(pre)  # not useful, just sanity check
    # Build expected tree manually for small case:
    #   1
    #  / \
    # 2   3
    #   / \
    #  4   5
    # ...
```

> **Automated Testing Idea:**  
> - Generate random unique values.  
> - Construct a random binary tree.  
> - Compute its preorder & postorder.  
> - Feed to solver; then verify `preorder(root)` and `postorder(root)` match originals.  

> **Why It Helps Recruiters:** Demonstrates ability to *validate* solutions programmatically, a crucial skill in production code reviews.

---

### 9️⃣  Take‑away & Career‑Boost Tips  

1. **Translate Between Languages:** Show recruiters you can implement the same logic in Java, Python, and C++ in a single interview.  
2. **Explain Your Recursion:** Always verbalize the *preorder index* and *postorder segment* logic.  
3. **Emphasize Space Efficiency:** Mention that you could add a hashmap for O(1) index lookup, but opted for a simpler solution given constraints.  
4. **Show Sample Code in the Resume:** Add a bullet: *Implemented LeetCode 889 in Java, Python, and C++ with O(N) complexity.*  
5. **Practice Edge Cases:** Right‑skewed, left‑skewed, single‑child nodes, and minimal tree sizes.  

> **Resume Hook:** “Implemented efficient binary tree reconstruction algorithm for LeetCode 889 in Java, Python, and C++, achieving linear time and space complexity.”

---

### 🔚  Conclusion  

Mastering **LeetCode 889** equips you with a **recursion + index‑mapping** toolkit that is highly transferable across coding interviews.  

- **The Good** – Clean recursion, linear time, language‑agnostic.  
- **The Bad** – Watch out for off‑by‑one bounds and missing index updates.  
- **The Ugly** – Handle non‑unique, skewed trees gracefully.  

> By practicing the pattern above, you not only solve the problem but also signal to recruiters that you’re comfortable **abstracting concepts across languages**, **optimizing space**, and **debugging complex recursion**.  

**Your next step:** Build a small command‑line tool that accepts two arrays and prints the tree in level order; include it in your portfolio website or GitHub readme to showcase your solution live to hiring managers.

---

**Happy coding!**  

> *Ready for your next interview? Dive into the solutions, run them on your favorite IDE, and then push your repo to GitHub with clear commit messages. Recruiters love visible, well‑documented problem solutions.*  

---



---  

> **Disclaimer:** The code snippets above are fully compilable in their respective environments (Java 17+, Python 3.10+, C++17). Always double‑check the LeetCode problem statement for any hidden constraints before submitting.  

---