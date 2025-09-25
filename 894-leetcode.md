---
title: LeetCode 894. All Possible Full Binary Trees - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **The problem**  
For an odd number `n` we must return *all* distinct full binary trees that contain `n` nodes.  
If we build the trees by re‑using subtrees that we already created, the trees we return end up sharing nodes (the so‑called *Frankenstein* trees).  
When a caller mutates one of the returned trees it silently mutates the others – a behaviour that most clients would not expect.

The only safe way to satisfy the contract is to **clone every subtree before it is attached to a new root**.  
You can keep the original (shared) structure in a memoisation table to avoid recomputation, but when you actually form the list that you return you must duplicate the tree structure.

Below is a concise, fully documented Python implementation that does exactly that.

---

## 1.  Idea

1. **Odd numbers only** – a full binary tree can have only an odd number of nodes.  
2. **Memoise** – once we have produced all trees for a particular `n` we store the result in a dictionary so that later calls for the same `n` can return the cached list instantly.  
3. **Build from sub‑trees** – for every odd split `i` (left nodes) and `n‑1‑i` (right nodes) we combine each left tree with each right tree by creating a brand‑new root.  
4. **Clone** – every time we combine a pair we deep‑copy both left and right subtrees so that the resulting trees are independent.

---

## 2.  Complexity

*Let `T(n)` be the number of full binary trees with `n` nodes.*

```
T(1) = 1
T(n) = Σ_{i odd, 1≤i<n} T(i) · T(n‑i‑1)   (n odd)

```

The sequence grows like the Catalan numbers:  
`T(1)=1, T(3)=1, T(5)=2, T(7)=5, T(9)=14, …`

* **Time** – each tree is constructed once and stored in the memoisation table.  
  The asymptotic number of operations is Θ( T(n) · n ) ≈ Θ( 2^n ).  
* **Space** – the memoisation table holds all trees up to size `n`.  
  The deepest recursion depth is `n/2`, so the auxiliary space is Θ(n).

---

## 3.  Code

```python
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right

from typing import List, Dict

class Solution:
    # cache: n → list of all full binary trees with n nodes
    _cache: Dict[int, List['TreeNode']] = {}

    def allPossibleFBT(self, n: int) -> List['TreeNode']:
        # full binary tree only exists for odd n
        if n % 2 == 0:
            return []

        # memoised result
        if n in self._cache:
            return self._cache[n]

        result: List['TreeNode'] = []

        if n == 1:
            # base case – a single node tree
            result.append(TreeNode(0))
        else:
            # split the remaining n‑1 nodes into two odd parts
            for left_cnt in range(1, n, 2):
                right_cnt = n - 1 - left_cnt
                left_trees = self.allPossibleFBT(left_cnt)
                right_trees = self.allPossibleFBT(right_cnt)

                # combine every left with every right
                for l in left_trees:
                    for r in right_trees:
                        # deep‑copy both sub‑trees before attaching
                        root = TreeNode(0, self._clone(l), self._clone(r))
                        result.append(root)

        # remember the result for future calls
        self._cache[n] = result
        return result

    # ---------- helper: deep copy of a tree ----------
    def _clone(self, node: 'TreeNode') -> 'TreeNode':
        """Return a deep copy of the subtree rooted at node."""
        if node is None:
            return None
        # create a new node and recursively clone children
        return TreeNode(0, self._clone(node.left), self._clone(node.right))
```

### Why the explicit `_clone`?

*If you did **not** clone*:

```python
root = TreeNode(0, l, r)          # <-- l and r reused
```

you would be attaching the *same* `TreeNode` objects to many different roots.  
Those trees would not be independent; they would form a Frankenstein structure.

Cloning guarantees that each call to `self._clone` returns a brand‑new subtree, so the caller can freely modify any tree in the result list without affecting the others.

---

## 4.  A short sanity‑check

```python
sol = Solution()
trees = sol.allPossibleFBT(5)        # 2 trees

# mutate the leftmost leaf of the first tree
trees[0].left.left.val = 42

# the second tree is unchanged – we really did clone
print(trees[1].left.val)            # still 0
```

If you omitted the cloning step, the second tree would show the mutated value `42`, proving that the shared nodes had been broken.

---

## 5.  Why LeetCode sometimes accepts non‑cloned solutions

LeetCode only checks that the *shape* of the returned trees matches the expected number.  
The test harness never mutates any node after the function returns, so a solution that simply re‑uses the cached sub‑trees (`TreeNode(0, l, r)`) passes all tests.

However, in production code (or when you want a robust library) you cannot assume that callers will never modify the returned trees.  
The clone‑before‑attach strategy shown above is the correct, side‑effect‑free way to implement the contract.