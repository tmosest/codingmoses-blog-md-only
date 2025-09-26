---
title: LeetCode 971. Flip Binary Tree To Match Preorder Traversal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 971 – Flip Binary Tree To Match Pre‑Order Traversal  
*Medium | Binary Tree | DFS*  

> **Problem Recap**  
> You’re given a binary tree whose node values are a permutation of `1 … n`.  
> You’re also given an array `voyage` (length `n`) that represents a *desired* pre‑order traversal.  
> In one operation you can “flip” a node, i.e., swap its left and right subtrees.  
> **Goal:** Flip the **fewest** nodes so that the tree’s pre‑order traversal equals `voyage`.  
> Return a list of the values of all flipped nodes. If it’s impossible, return `[-1]`.

---

## 💡 Core Idea (The “Good” part)

The pre‑order traversal visits nodes in the order *root → left → right*.  
If we walk down the tree while simultaneously walking through `voyage`:

1. The current node **must** match the current element of `voyage`.  
   If it doesn’t, we’re doomed → return `[-1]`.

2. After consuming the root, look at the *next* element in `voyage` (`voyage[idx]`).  
   - If the left child exists **and** its value is **not** `voyage[idx]`, the only way to match the order is to flip the current node.  
   - Record the flip, then recurse on *right* first, then *left*.

3. Otherwise (left child matches or left child is `null`), recurse normally *left → right*.

Because we only flip when the next value in `voyage` forces it, we are guaranteed to use the minimal number of flips.

---

## ⚠️ The “Bad” part

- **Global state**: A single `idx` pointer is used across recursive calls.  
  In languages with strict typing (e.g., Java, C++), you must guard against overflow or accidental modification.  
- **Edge‑case handling**: The code must gracefully handle empty subtrees (`null` nodes) and the impossibility case (`[-1]`).  
- **Debugging complexity**: If the tree is large (though `n ≤ 100` here), stack depth may become a concern in recursive languages.

---

## 🔥 The “Ugly” part

- **Code duplication**: Writing almost identical logic in multiple languages can lead to maintenance headaches.  
- **Verbosity**: The Java version often feels verbose due to boilerplate `TreeNode` definitions, imports, and list handling.  
- **Implicit assumptions**: Relying on `voyage[idx]` without bounds checks can produce `ArrayIndexOutOfBoundsException` if the algorithm logic goes wrong.

---

## 📦 Complete Solutions

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three solve the problem in *O(n)* time and *O(h)* space (where `h` is the tree height, worst‑case `O(n)`).

> ⚠️ **Important**: All snippets assume the existence of a `TreeNode` class/struct defined exactly as shown.  
> They also assume that `root` is never `null` in the public API (LeetCode guarantees this).

---

### Java (DFS Recursion)

```java
import java.util.ArrayList;
import java.util.List;

// Definition for a binary tree node.
class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode() {}
    TreeNode(int val) { this.val = val; }
    TreeNode(int val, TreeNode left, TreeNode right) {
        this.val = val; this.left = left; this.right = right;
    }
}

class Solution {
    private int idx = 0;                // current position in voyage
    private List<Integer> flips = new ArrayList<>();
    private int[] voyage;               // reference to avoid passing around

    public List<Integer> flipMatchVoyage(TreeNode root, int[] voyage) {
        this.voyage = voyage;
        dfs(root);
        return flips;
    }

    private void dfs(TreeNode node) {
        if (node == null) return;

        // Mismatch → impossible
        if (node.val != voyage[idx]) {
            flips.clear();
            flips.add(-1);
            return;
        }
        idx++; // consume current value

        // If left child exists and next expected value isn't it, we must flip
        if (node.left != null && node.left.val != voyage[idx]) {
            flips.add(node.val);   // record flip
            dfs(node.right);
            dfs(node.left);
        } else {
            dfs(node.left);
            dfs(node.right);
        }
    }
}
```

---

### Python (DFS Recursion)

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def flipMatchVoyage(self, root: TreeNode, voyage: list[int]) -> list[int]:
        self.idx = 0
        self.flips = []

        def dfs(node: TreeNode | None):
            if not node:
                return
            if node.val != voyage[self.idx]:
                self.flips = [-1]
                return
            self.idx += 1
            # need to flip?
            if node.left and node.left.val != voyage[self.idx]:
                self.flips.append(node.val)
                dfs(node.right)
                dfs(node.left)
            else:
                dfs(node.left)
                dfs(node.right)

        dfs(root)
        return self.flips
```

---

### C++ (DFS Recursion)

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
        flips.clear();
        dfs(root, voyage);
        return flips;
    }

private:
    int idx = 0;
    vector<int> flips;

    void dfs(TreeNode* node, vector<int>& voyage) {
        if (!node) return;
        if (node->val != voyage[idx]) {
            flips = {-1};
            return;
        }
        ++idx;
        if (node->left && node->left->val != voyage[idx]) {
            flips.push_back(node->val);
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

## 🔍 Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| All 3 implementations | **O(n)** | **O(h)** (call stack + flips list) |

- `n` = number of nodes (≤ 100).  
- `h` = height of the tree (worst‑case `n`).

---

## 🎓 Why This Code Rocks (And How It Helps Your Interview)

| Feature | Benefit |
|---------|---------|
| **Linear traversal** | Guarantees the solution is fast enough for the constraints. |
| **Minimal flips** | The logic is deterministic; no back‑tracking or brute‑force needed. |
| **Clear recursion** | Makes the pre‑order logic obvious, which is what interviewers look for. |
| **Fail‑fast** | Immediately detects impossibility, saving time. |
| **Reusable pattern** | The same DFS-with-index pattern can solve many “voyage” style LeetCode problems (e.g., 1043, 1129). |

During an interview, you can:

1. **Explain the intuition** first—shows that you understand the problem.  
2. **Write the skeleton** of `dfs(node)` and the global index.  
3. **Handle the flip condition** in one `if`.  
4. **Return `[-1]`** as a special case.  

You’ll impress the interviewer with a solution that is **both elegant and correct**.

---

## 📈 SEO‑Ready Blog Post

> **Title**  
> LeetCode 971 – Flip Binary Tree To Match Pre‑Order Traversal (Java / Python / C++ Solutions)  

> **Meta Description**  
> Master LeetCode 971 by learning the optimal DFS solution to flip a binary tree for a desired pre‑order traversal. Get Java, Python, and C++ code snippets that solve the problem in linear time – perfect for software engineering interviews.  

---

### 📝 Blog Post

### Flip Binary Tree To Match Pre‑Order Traversal – 971 – A Full Interview Guide

---

#### Introduction

Interviewers often ask *binary tree* questions to gauge your recursion and greedy‑strategy skills.  
LeetCode 971 – **Flip Binary Tree To Match Pre‑Order Traversal** is a classic “voyage” problem that tests exactly those skills.

In this article you’ll discover:

- A **complete, linear‑time solution** in **Java**, **Python**, and **C++**.  
- The **why** behind each line of code (minimal flips, fail‑fast, DFS‑with‑index).  
- A **job‑interview cheat sheet** for explaining your approach in a coding interview.  

Let’s dive in!

---

#### 1️⃣ Problem Breakdown

| Constraint | Value |
|------------|-------|
| Node values | Permutation of `1 … n` |
| `voyage` length | `n` |
| `n` | ≤ 100 |
| Allowed operation | Flip (swap left/right children) |
| Goal | Minimal flips → desired pre‑order traversal |

---

#### 2️⃣ Intuitive Walkthrough (Pre‑Order + Index)

Imagine you’re standing in the tree.  
The *root* must match the first number in `voyage`.  
After you pick that number, the *next* number tells you which child you’ll visit next.

If the next number belongs to the right subtree, we must flip the current node.  
If it belongs to the left subtree (or left child is `null`), we stay as is.

This greedy rule yields the **fewest** flips.

---

#### 3️⃣ Code Highlights

- **DFS recursion** keeps the algorithm simple.  
- **Index pointer** (`idx`) moves linearly through `voyage`.  
- **Flips list** stores only flipped node values.  
- **Early exit** for impossible cases saves time.

---

#### 4️⃣ Code Snippets

> **Java**

```java
public List<Integer> flipMatchVoyage(TreeNode root, int[] voyage) { ... }
```

> **Python**

```python
class Solution:
    def flipMatchVoyage(self, root: TreeNode, voyage: List[int]) -> List[int]: ...
```

> **C++**

```cpp
class Solution {
public:
    vector<int> flipMatchVoyage(TreeNode* root, vector<int>& voyage) { ... }
};
```

(See the full code above for the exact implementation.)

---

#### 5️⃣ Complexity

- **Time:** O(n)  
- **Space:** O(h) – call stack + result array

---

#### 6️⃣ Interview‑Ready Tips

| Situation | How to Explain |
|-----------|----------------|
| **DFS pattern** | “I traverse the tree exactly once, using the global index to sync with `voyage`.” |
| **Minimal flips** | “The only flip occurs when the next element forces it, so we’re guaranteed minimal flips.” |
| **Impossible case** | “If at any point the current node value does not match the expected value, I immediately return `[-1]`.” |
| **Edge‑case** | “I guard against `null` children and ensure the index stays within bounds.” |

---

#### 7️⃣ TL;DR

- The **good** part is a linear, greedy DFS that flips only when necessary.  
- The **bad** part is managing global state safely in recursive code.  
- The **ugly** part is code duplication across languages and boilerplate.

All three language versions below solve the problem in `O(n)` time and `O(h)` space while being easy to understand and ready for a technical interview.

---

### 🚀 Takeaway

Mastering LeetCode 971 showcases:

- Proficiency in **binary tree traversal**.  
- Ability to write **clean, fail‑fast recursive code**.  
- Understanding of **greedy strategies** for minimal‑change problems.

If you can explain this solution clearly in an interview, you’ll demonstrate:

1. **Algorithmic insight** – knowing the right pattern to use.  
2. **Coding style** – concise yet readable.  
3. **Problem‑solving mindset** – handling edge cases and impossibility early.

Add this solution to your interview prep stack and you’ll be a step closer to landing that software engineering role!  

Good luck 🚀, and happy coding!