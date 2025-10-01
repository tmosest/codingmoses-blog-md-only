---
title: LeetCode 2313. Minimum Flips in Binary Tree to Get Result - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2313. Minimum Flips in Binary Tree to Get Result  
### Solution (DFS + DP) – Java | Python | C++

---

### Problem Recap
You’re given a binary tree where

| node value | meaning |
|-----------|---------|
| `0` | leaf = `false` |
| `1` | leaf = `true` |
| `2` | `OR` |
| `3` | `AND` |
| `4` | `XOR` |
| `5` | `NOT` (only one child) |

You can **flip** a leaf (0 ↔ 1).  
Return the minimum number of flips that make the root evaluate to the given `result`.

The tree can contain up to `10⁵` nodes – a linear‑time algorithm is required.



---

## Core Idea – Bottom‑Up DP

For every node we keep **two values**:

```
dp[node][0] – minimum flips to make this subtree evaluate to false
dp[node][1] – minimum flips to make this subtree evaluate to true
```

The answer is simply `dp[root][result]`.

We compute these values by traversing the tree **post‑order** (DFS).  
Leaf nodes are trivial:  

```
dp[leaf][leaf.val] = 0          // no flip needed
dp[leaf][1-leaf.val] = 1        // flip once
```

For internal nodes we combine the children's DP according to the operator:

| operator | formula for dp[*][false] | formula for dp[*][true] |
|----------|--------------------------|--------------------------|
| `OR` (2) | `dpL[0] + dpR[0]` | `min(dpL[1], dpR[1])` |
| `AND` (3)| `min(dpL[0], dpR[0])` | `dpL[1] + dpR[1]` |
| `XOR` (4)| `min(dpL[0] + dpR[1], dpL[1] + dpR[0])` | `min(dpL[0] + dpR[0], dpL[1] + dpR[1])` |
| `NOT` (5)| `dpChild[1]` | `dpChild[0]` |

The formulas come from the truth tables of the boolean operators.

The algorithm visits each node once → **O(N)** time and **O(H)** space (recursion stack).

---

## 1. Java Implementation

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
    public int minimumFlips(TreeNode root, boolean result) {
        int[] res = dfs(root);
        return result ? res[1] : res[0];
    }

    /** returns [dpFalse, dpTrue] for the subtree rooted at node */
    private int[] dfs(TreeNode node) {
        if (node.left == null && node.right == null) {          // leaf
            return new int[]{node.val, 1 - node.val};
        }

        int[] left  = node.left  == null ? null : dfs(node.left);
        int[] right = node.right == null ? null : dfs(node.right);

        switch (node.val) {
            case 2:  // OR
                return new int[]{
                    left[0] + right[0],                     // false only if both false
                    Math.min(left[1], right[1])             // true if any true
                };
            case 3:  // AND
                return new int[]{
                    Math.min(left[0], right[0]),            // false if any false
                    left[1] + right[1]                      // true only if both true
                };
            case 4:  // XOR
                return new int[]{
                    Math.min(left[0] + right[1], left[1] + right[0]),  // different
                    Math.min(left[0] + right[0], left[1] + right[1])   // same
                };
            case 5:  // NOT – only one child
                int[] child = left != null ? left : right;
                return new int[]{
                    child[1],   // make false → child must be true
                    child[0]    // make true → child must be false
                };
            default:
                throw new IllegalArgumentException("Invalid node value");
        }
    }
}
```

---

## 2. Python Implementation (Python 3)

```python
# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val, self.left, self.right = val, left, right

class Solution:
    def minimumFlips(self, root: TreeNode, result: bool) -> int:
        dp_false, dp_true = self.dfs(root)
        return dp_true if result else dp_false

    def dfs(self, node: TreeNode):
        # leaf
        if not node.left and not node.right:
            return node.val, 1 - node.val

        left  = self.dfs(node.left)  if node.left  else None
        right = self.dfs(node.right) if node.right else None

        if node.val == 2:          # OR
            return (left[0] + right[0],
                    min(left[1], right[1]))
        if node.val == 3:          # AND
            return (min(left[0], right[0]),
                    left[1] + right[1])
        if node.val == 4:          # XOR
            return (min(left[0] + right[1], left[1] + right[0]),
                    min(left[0] + right[0], left[1] + right[1]))
        # NOT
        child = left if left is not None else right
        return (child[1], child[0])   # flip child value
```

---

## 3. C++ Implementation

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
    int minimumFlips(TreeNode* root, bool result) {
        auto res = dfs(root);          // pair<falseCost, trueCost>
        return result ? res.second : res.first;
    }

private:
    // returns pair<dpFalse, dpTrue>
    std::pair<int,int> dfs(TreeNode* node) {
        if (!node->left && !node->right) {          // leaf
            return {node->val, 1 - node->val};
        }

        auto left  = node->left  ? dfs(node->left)  : std::pair<int,int>{0,0};
        auto right = node->right ? dfs(node->right) : std::pair<int,int>{0,0};

        switch (node->val) {
            case 2:   // OR
                return {left.first + right.first,
                        std::min(left.second, right.second)};
            case 3:   // AND
                return {std::min(left.first, right.first),
                        left.second + right.second};
            case 4:   // XOR
                return {std::min(left.first + right.second,
                                 left.second + right.first),
                        std::min(left.first + right.first,
                                 left.second + right.second)};
            case 5:   // NOT
            {
                auto child = node->left ? left : right;
                return {child.second, child.first};
            }
            default:
                throw std::invalid_argument("Invalid TreeNode value");
        }
    }
};
```

> **Tip**: If you’re submitting to LeetCode, use the provided `TreeNode` struct.  
> The code above compiles with `g++ -std=c++17`.

---

## Good, Bad & Ugly Aspects of This Problem

| Aspect | Why It’s Interesting | What to Watch Out For |
|--------|---------------------|------------------------|
| **Good** | • Linear DP → perfect for interview‑style “must‑run‑in‑O(N)”. <br>• Truth‑table based formulas are neat, reusable, and can be generalized to other operators. | – |
| **Bad** | • `XOR` needs a **four‑term** expression instead of a simple `dpL[0] + dpR[0]` or `dpL[1] + dpR[1]`. <br>• The `NOT` node only has one child; careful handling of `nullptr` is required. | • Hard to get the formulas wrong – double‑check truth tables. |
| **Ugly** | • The `XOR` formulas look asymmetric; a single typo can break the entire solution. <br>• Many solutions on LeetCode “solve” the problem but forget the `dp[0] + dp[1]` variant for XOR. | • Avoid hard‑coding values; use constants (`OR = 2`, etc.) for readability. |

---

## 4. Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java, Python, C++ | **O(N)** | **O(H)** recursion depth (≤ N, usually ~log N for balanced trees) |

---

### Summary

1. Bottom‑up DP storing **two costs per node** (`false` and `true`).  
2. Post‑order DFS to merge children’s costs according to the boolean operator truth tables.  
3. Return `dp[root][result]`.  

This pattern—**cost for both outcomes**—is a classic interview technique that applies to many “flip” or “toggle” problems.

---

# SEO‑Optimized Blog Post

> **Title**  
> **Minimum Flips Binary Tree – LeetCode 2313 Explained (Job‑Interview Friendly)**

---

## 1️⃣ What is “Minimum Flips in a Binary Tree”?

When interviewers ask, they want to see **algorithmic thinking**, **DP** mastery, and clean code.  
In this blog we’ll walk through LeetCode 2313 step‑by‑step, present a bullet‑proof solution in Java, Python and C++, and discuss the **good, the bad, and the ugly** of tackling this problem.

---

## 2️⃣ Why LeetCode 2313 Matters for Your Interview Portfolio

- **High‑frequency boolean‑operator tree problems** show you can think in **bottom‑up DP**.  
- Demonstrates **O(N)** time complexity – a non‑trivial requirement for large trees (`10⁵` nodes).  
- Highlights **truth‑table driven formulas** – a skill recruiters love.

---

## 3️⃣ Problem Breakdown (Recap)

| Leaf | True / False | Operator | 1‑Child? |
|------|--------------|----------|----------|
| `0` | false | – | – |
| `1` | true | – | – |
| `2` | OR | yes | – |
| `3` | AND | yes | – |
| `4` | XOR | yes | – |
| `5` | NOT | yes | **one** child |

Goal: *minimum flips* to force root to evaluate to given `result`.

---

## 4️⃣ The DP Solution – A “Good” Approach

1. **Post‑order traversal** – ensures children are solved before parents.  
2. **Two‑dimensional DP** – `dp[node][0]` & `dp[node][1]`.  
3. **Truth‑table formulas** – concise, mathematically sound.  

```text
OR:   false = L0+R0        true = min(L1,R1)
AND:  false = min(L0,R0)   true = L1+R1
XOR:  false = min(L0+R1, L1+R0)
      true  = min(L0+R0, L1+R1)
NOT:  false = child.true   true = child.false
```

*Result*: `dp[root][result]` in **O(N)** time.

---

## 5️⃣ Common Pitfalls – “Bad” Moments

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| Forgetting to flip a leaf for the opposite value | Mis‑reading the base case | `dp[leaf][1-leaf.val] = 1` |
| Mixing up OR/AND formulas | Swapping “true” & “false” conditions | Double‑check truth tables |
| Not handling the NOT node’s single child | Assuming both left & right exist | Use `node.left ? dfs(node.left) : dfs(node.right)` |

---

## 6️⃣ The Ugly – Code Tricky Parts

- **XOR** formulas look the most complex; a small typo can cascade errors.  
- **NULL children** for OR/AND must be handled with care to avoid `nullptr` dereference.  
- **Recursive stack** might blow up for highly unbalanced trees – use iterative DFS or increase stack size if needed.

---

## 7️⃣ Full Code Snippets (Java / Python / C++)

*(Insert the code sections from the previous answer here; add a brief comment block for each language.)*

---

## 8️⃣ Take‑Away for Your Next Interview

1. **Explain the DP clearly** – recruiters want to see you can formalize the “min flips” problem.  
2. **Show the truth‑table derivation** – it demonstrates analytical skills.  
3. **Highlight complexity** – LeetCode loves O(N) solutions; don’t over‑optimize.  
4. **Mention edge cases** – balanced, skewed, and NOT‑only trees.  

> *“The interviewer will ask: why do you store two values per node?”*  
> *Answer:* “Because the cost to make a subtree true may depend on flipping *any* of its leaves, not just the ones already true/false.”

---

## 9️⃣ Final Thought

LeetCode 2313 is a **gold‑mine** for algorithm interviews.  
With a concise DP solution, clean code in the three major languages, and a clear explanation, you’ll be ready to impress hiring managers and land that coveted software‑engineering role.

Happy coding! 🚀



--- 

**Keywords:**  
`minimum flips binary tree`, `LeetCode 2313`, `binary tree DP`, `bool operator tree`, `software engineering interview`, `job interview algorithm`, `bottom‑up DP`, `O(N) algorithm`, `Python DFS`, `Java DFS`, `C++ DFS`, `job interview preparation`  

Feel free to copy the code blocks into your IDE, run your own tests, and showcase this solution on your portfolio or GitHub. Good luck!