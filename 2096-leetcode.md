---
title: LeetCode 2096. Step-By-Step Directions From a Binary Tree Node to Another - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solving “Step‑By‑Step Directions From a Binary Tree Node to Another”  

Below are three complete, ready‑to‑copy solutions – one each in **Java**, **Python** and **C++**.  
All three use the same DFS‑based approach:

1. Build the path from the root to the *start* node (`sPath`).  
2. Build the path from the root to the *destination* node (`dPath`).  
3. Remove the common suffix (the part that belongs to the lowest common ancestor).  
4. Convert the remaining part of `sPath` to `U`s and append the reversed `dPath`.  

Each implementation is fully commented and follows the LeetCode method signature.

---

### 1.1  Java (LeetCode Signature)

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
    /** DFS helper that appends 'L' or 'R' to sb as it backtracks. */
    private boolean findPath(TreeNode node, int target, StringBuilder sb) {
        if (node.val == target) return true;
        if (node.left != null && findPath(node.left, target, sb)) {
            sb.append('L');
            return true;
        }
        if (node.right != null && findPath(node.right, target, sb)) {
            sb.append('R');
            return true;
        }
        return false;          // target not found in this subtree
    }

    public String getDirections(TreeNode root, int startValue, int destValue) {
        StringBuilder sPath = new StringBuilder();
        StringBuilder dPath = new StringBuilder();

        findPath(root, startValue, sPath);
        findPath(root, destValue, dPath);

        // Remove common suffix (LCA part)
        int i = 0;
        while (i < Math.min(sPath.length(), dPath.length())
                && sPath.charAt(sPath.length() - 1 - i) == dPath.charAt(dPath.length() - 1 - i)) {
            i++;
        }

        // Remaining part of start path → 'U's
        StringBuilder up = new StringBuilder();
        for (int j = 0; j < sPath.length() - i; ++j) up.append('U');

        // Remaining part of dest path → reversed
        dPath.reverse();
        StringBuilder result = new StringBuilder(up);
        result.append(dPath.substring(i));

        return result.toString();
    }
}
```

> **Why this Java implementation is fast?**  
> * The DFS runs in **O(n)** – it visits every node at most twice.  
> * The `StringBuilder` grows linearly with the depth of the tree, never allocating large intermediate strings.

---

### 1.2  Python 3 (LeetCode Signature)

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def _find_path(self, node: TreeNode, target: int, path: list) -> bool:
        """DFS that appends 'L' or 'R' to path on back‑track."""
        if node.val == target:
            return True
        if node.left and self._find_path(node.left, target, path):
            path.append('L')
            return True
        if node.right and self._find_path(node.right, target, path):
            path.append('R')
            return True
        return False

    def getDirections(self, root: TreeNode, startValue: int, destValue: int) -> str:
        s_path, d_path = [], []

        self._find_path(root, startValue, s_path)
        self._find_path(root, destValue, d_path)

        # Remove common suffix (LCA part)
        while s_path and d_path and s_path[-1] == d_path[-1]:
            s_path.pop()
            d_path.pop()

        return 'U' * len(s_path) + ''.join(reversed(d_path))
```

> **Python tip** – Using a list for the path keeps mutation cheap and avoids costly string concatenation inside the recursion.

---

### 1.3  C++ (LeetCode Signature)

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
    // DFS helper: returns true if target found, appends direction to path
    bool dfs(TreeNode* node, int target, string &path) {
        if (!node) return false;
        if (node->val == target) return true;

        if (dfs(node->left, target, path)) {
            path.push_back('L');
            return true;
        }
        if (dfs(node->right, target, path)) {
            path.push_back('R');
            return true;
        }
        return false;
    }

    string getDirections(TreeNode* root, int startValue, int destValue) {
        string startPath, destPath;
        dfs(root, startValue, startPath);
        dfs(root, destValue, destPath);

        // Remove common suffix (LCA part)
        int i = 0;
        while (i < min(startPath.size(), destPath.size()) &&
               startPath[startPath.size() - 1 - i] == destPath[destPath.size() - 1 - i]) {
            ++i;
        }

        string up(startPath.size() - i, 'U');
        reverse(destPath.begin(), destPath.end());
        destPath.erase(0, i);          // keep only the non‑common part

        return up + destPath;
    }
};
```

> **C++ note** – Using `reverse` in‑place keeps memory usage minimal and guarantees `O(n)` time.

---

## 2.  Blog Article – “Cracking the LeetCode Binary‑Tree Path Puzzle: Good, Bad & Ugly”

> **Title (SEO‑Optimized):**  
> *Cracking LeetCode’s “Step‑By‑Step Directions From a Binary Tree Node” – DFS, LCA & Interview‑Ready Tips*

> **Meta Description (SEO‑Optimized):**  
> *Learn how to solve the binary‑tree path puzzle from LeetCode in Java, Python & C++. Dive into DFS, LCA, time‑space complexity, common pitfalls and interview tricks to land your next tech job.*

---

### Introduction

Interviews at top‑tier tech companies often start with a classic binary‑tree question: *“Find a path between two nodes.”*  
LeetCode’s “Step‑By‑Step Directions From a Binary Tree Node to Another” (ID 1409) is a perfect example.  
In this article we’ll:

- **Explain** the problem in plain English.  
- Walk through a **DFS‑based solution** that’s fast, memory‑friendly, and easy to code.  
- Point out the **good, bad, ugly** aspects of this approach.  
- Share the **time/space complexity** and why it matters in an interview setting.  
- Offer **tips** on how to talk about this problem in a coding interview (and boost your chances of landing a job).

> **Key SEO Keywords**: *binary tree, LeetCode, coding interview, DFS, LCA, step‑by‑step directions, job interview, data structures, interview questions, algorithm, Java, Python, C++*  

---

### 1.  Problem Recap

Given the **root** of a binary tree, the **value of a start node**, and the **value of a destination node**, return a string that describes the sequence of directions needed to walk from the start node to the destination node.

- `L` – move to the left child.  
- `R` – move to the right child.  
- `U` – move to the parent.

**Examples**

| Root | Start | Destination | Output |
|------|-------|-------------|--------|
| `[5,1,2,0,3,null,4]` | 1 | 4 | `"URR"` |
| `[2,null,3,null,4]` | 3 | 2 | `"U"` |

---

### 2.  Intuition

If we could write down the path from the **root** to each node, the solution would be trivial:

1. Path to *start*: `L R L R …`  
2. Path to *destination*: `L R R …`  

The common suffix of these two strings is exactly the path **to the lowest common ancestor (LCA)** of the two nodes.  
Removing that part leaves:

- The part of the *start* path that we must walk **up** (`U`s).  
- The part of the *destination* path that we must walk **down** (reverse the suffix).

That’s the whole problem.

---

### 3.  Good – Why DFS Works

| Feature | Reason |
|---------|--------|
| **Linear time** – Each DFS touches every node once → `O(n)` | Works for trees up to `10⁴` nodes comfortably. |
| **No extra data structure** – Just a string builder / list | Keeps auxiliary space minimal (`O(depth)`). |
| **Recursion depth ≤ height** – Safe for balanced trees | For pathological skewed trees we can tail‑recursion or iterative stack if needed. |

---

### 4.  Bad – What Could Go Wrong

| Issue | Impact | Mitigation |
|-------|--------|------------|
| **String reversal** (common in some solutions) | Extra linear pass; might allocate a new string | Reverse **in‑place** or use a list for path. |
| **Deep recursion** on skewed trees | Stack overflow on languages with small recursion limits | Use an explicit stack or raise recursion limit in Python. |
| **Mutable global state** (e.g., static string in C++) | Hard to debug | Keep the path variable local to each recursion call. |

---

### 5.  Ugly – The “Slick” but Subtle Bug

> **The “Ugly” pattern** – Building the result string by concatenating characters inside the recursion (e.g., `return path + 'L'`) can lead to **exponential** time in worst case because each concatenation copies the whole string.

**Why it’s ugly**:  
- It defeats the purpose of an `O(n)` algorithm.  
- In interviews, candidates might do exactly that and get penalised for “poor time complexity.”

**Pro tip** – Keep the path in a *mutable* container (list or `StringBuilder`) and only convert to string once the DFS has finished.

---

### 5.  Time & Space Complexity

| Metric | Complexity | Explanation |
|--------|------------|-------------|
| **Time** | `O(n)` | Two DFS traversals, constant extra work on the strings. |
| **Space** | `O(h)` where `h` is tree height | Path string grows to at most the depth of the tree; all other work is constant. |

In a job interview, explicitly stating this complexity shows you understand *why* your solution is efficient.

---

### 6.  Talking About This Problem in an Interview

1. **Clarify the input** – Confirm that all node values are unique (problem guarantees).  
2. **Explain the LCA trick** – Write down the idea of recording two root‑to‑node paths and trimming the common suffix.  
3. **Mention DFS vs. LCA library** – “I’ll implement DFS directly, but I could also call the standard LCA algorithm and then compute the two segments.”  
4. **Show the code** – Highlight the helper that back‑tracks to build the path.  
5. **State complexity** – `O(n)` time, `O(h)` space.  
6. **Edge cases** – “What if one node is the ancestor of the other?” → All `U`s or no moves.  
7. **Testing** – Run on small custom trees, then on the provided examples.

> **Interview Hack** – “If I had an interactive environment, I’d actually print the tree and visually walk through the algorithm. It demonstrates a deep understanding of tree traversal.”

---

### 7.  Final Code Snippets

> (Insert the Java, Python, C++ snippets from section 1.)

> **Why each snippet is interview‑ready** –  
> • Short, clear, and follows the LeetCode interface.  
> • Uses standard library features (StringBuilder, list, reverse).  
> • Contains comments that a hiring manager can skim and understand.

---

### 8.  Take‑Away Checklist

- ✅ **DFS Path** – Build two paths from root to the nodes.  
- ✅ **Trim LCA** – Remove common suffix.  
- ✅ **Upward moves** – Add `U`s equal to the remaining start path length.  
- ✅ **Downward moves** – Append reversed destination suffix.  
- ✅ **Complexity** – Mention `O(n)` time, `O(h)` auxiliary space.  

---

### 9.  Wrap‑Up

The binary‑tree “step‑by‑step directions” problem is a showcase of **clean algorithmic thinking**.  
Mastering it gives you:

- Confidence in handling any *path between nodes* problem.  
- A concrete example of **DFS + LCA** that you can describe to interviewers.  
- Proof of your ability to write **time‑efficient** code in Java, Python or C++.

> **Ready for the next interview?**  
> 1. Review the code snippets above.  
> 2. Practice on similar LeetCode problems: *Binary Tree Inorder Traversal*, *Lowest Common Ancestor*, *Construct Binary Tree from Preorder & Inorder*.  
> 3. Share this article on LinkedIn or a personal blog – it shows you’re actively learning and optimizing.

Happy coding, and may the `U`s be many and the bugs few!

---  

### 10.  Further Reading

- *Introduction to Algorithms* – Chapter on Binary Trees.  
- *Cracking the Coding Interview* – Section on Tree Traversals.  
- LeetCode Discussions for ID 1409 – various solutions, performance hacks.  

---  

**Enjoy the challenge, and good luck on your next interview!**  

---  

*Author: Your Name – Senior Software Engineer, Data Structures Enthusiast.*  

--- 

**End of Article**  

--- 

*Feel free to copy, adapt or publish this article (with attribution). It’s a great way to showcase your algorithmic knowledge while helping the community.*  

--- 

*---*  

--- 

**Follow Up**  

> If you’re preparing for a role at a tech company, we recommend also brushing up on **graph traversal**, **stack‑based DFS**, and **recursive design patterns**—all useful for similar interview problems.  

--- 

**Good luck, and happy coding!**  



--- 

**Note** – All code snippets above are fully functional and can be pasted into the LeetCode online IDE or your local environment for quick testing.