---
title: LeetCode 2673. Make Costs of Paths Equal in a Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Make Costs of Paths Equal in a Binary Tree – 2673 (LeetCode)

> **Title:** Make Costs of Paths Equal in a Binary Tree – Optimal Java/Python/C++ Solutions  
> **Meta‑description:** Solve LeetCode 2673 “Make Costs of Paths Equal in a Binary Tree” in O(n) time. Read our bottom‑up DFS tutorial, code snippets for Java, Python & C++, plus a “good‑bad‑ugly” analysis for interview prep.  

---

## 1. Problem Statement

You are given a perfect binary tree with `n` nodes (1‑based indexing).  
The root is node `1`, and for any node `i` the left child is `2*i` and the right child is `2*i+1`.  

`cost[i]` (0‑based array) holds the cost of node `i+1`.  
You can **increment** the cost of any node by `1` any number of times.  
**Goal:** Make the total cost of every root‑to‑leaf path equal using the minimum number of increments.

Return that minimum number of increments.

> **Examples**  
> 1. `n = 7, cost = [1,5,2,2,3,3,1]` → **6**  
> 2. `n = 3, cost = [5,3,3]` → **0**  

> **Constraints**  
> * `3 ≤ n ≤ 10^5`  
> * `n + 1` is a power of 2 (perfect binary tree)  
> * `1 ≤ cost[i] ≤ 10^4`  

---

## 2. Intuition & Key Insight

A perfect binary tree is *level‑ordered*.  
If we look at any internal node, its two children eventually lead to two leaf paths.  
Let:

```
leftCost  – minimal total cost from the left child to any leaf
rightCost – minimal total cost from the right child to any leaf
```

To make the two paths equal, we must raise the cheaper subtree to match the expensive one.  
The cost of doing this is `abs(leftCost - rightCost)`.

After aligning the children, the current node’s minimal path cost becomes:
```
cost[node] + max(leftCost, rightCost)
```
because we choose the heavier child as the baseline for the next level up.

Thus the problem can be solved *bottom‑up* or *recursively* by DFS.  
Both approaches have **O(n)** time and **O(1)** auxiliary space (ignoring recursion stack).

---

## 3. Bottom‑Up Iterative Solution (C++, Java, Python)

### 3.1 Algorithm

1. Iterate from the last internal node (`n/2 - 1`) down to the root (`0`).  
2. For node `i` (0‑based index):
   * `l = 2*i + 1` (left child index)  
   * `r = 2*i + 2` (right child index)  
   * `increments += |cost[l] - cost[r]|`  
   * `cost[i] += max(cost[l], cost[r])`   // update to minimal path cost from node `i`
3. Return `increments`.

### 3.2 Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Loop over internal nodes | **O(n)** | **O(1)** (in‑place) |
| Recursion stack (DFS variant) | **O(n)** | **O(log n)** (tree height) |

### 3.3 Code Snippets

#### Java

```java
class Solution {
    public int minIncrements(int n, int[] cost) {
        long res = 0;                // use long to avoid overflow for large n
        for (int i = n / 2 - 1; i >= 0; i--) {
            int l = 2 * i + 1;
            int r = 2 * i + 2;
            res += Math.abs(cost[l] - cost[r]);
            cost[i] += Math.max(cost[l], cost[r]);   // propagate minimal cost upwards
        }
        return (int) res;
    }
}
```

#### Python 3

```python
class Solution:
    def minIncrements(self, n: int, cost: List[int]) -> int:
        res = 0
        for i in range(n // 2 - 1, -1, -1):
            l = 2 * i + 1
            r = 2 * i + 2
            res += abs(cost[l] - cost[r])
            cost[i] += max(cost[l], cost[r])          # update to minimal path cost
        return res
```

#### C++

```cpp
class Solution {
public:
    int minIncrements(int n, vector<int>& cost) {
        long long res = 0;                       // long long to avoid overflow
        for (int i = n / 2 - 1; i >= 0; --i) {
            int l = 2 * i + 1;
            int r = 2 * i + 2;
            res += abs(cost[l] - cost[r]);
            cost[i] += max(cost[l], cost[r]);     // propagate upwards
        }
        return static_cast<int>(res);
    }
};
```

---

## 4. Recursive DFS Variant

If you prefer a *clean* recursive implementation, the logic stays the same; the only difference is that we compute the children's costs on the fly.

#### Java

```java
class Solution {
    private long res = 0;

    public int minIncrements(int n, int[] cost) {
        dfs(0, cost);
        return (int) res;
    }

    private int dfs(int i, int[] cost) {
        if (i >= cost.length) return 0;          // leaf
        int left  = dfs(2 * i + 1, cost);
        int right = dfs(2 * i + 2, cost);
        res += Math.abs(left - right);
        return cost[i] + Math.max(left, right);
    }
}
```

#### Python

```python
class Solution:
    def minIncrements(self, n: int, cost: List[int]) -> int:
        self.res = 0
        def dfs(i):
            if i >= len(cost): return 0
            l, r = dfs(2 * i + 1), dfs(2 * i + 2)
            self.res += abs(l - r)
            return cost[i] + max(l, r)
        dfs(0)
        return self.res
```

#### C++

```cpp
class Solution {
public:
    long long ans = 0;
    int dfs(int i, vector<int>& cost) {
        if (i >= cost.size()) return 0;
        int left  = dfs(2 * i + 1, cost);
        int right = dfs(2 * i + 2, cost);
        ans += abs(left - right);
        return cost[i] + max(left, right);
    }

    int minIncrements(int n, vector<int>& cost) {
        dfs(0, cost);
        return static_cast<int>(ans);
    }
};
```

---

## 5. “Good – Bad – Ugly” Interview Analysis

| Phase | What You Should Show | What Can Go Wrong | How to Avoid It |
|-------|----------------------|-------------------|-----------------|
| **Good** | • Linear time (O(n)) algorithm.<br>• Minimal increments derived from aligning subtrees.<br>• Bottom‑up or DFS both simple to code. | | |
| **Bad** | • Misinterpreting the tree as *non‑perfect* leads to index errors.<br>• Using `int` for the answer can overflow when `n` ≈ 10⁵. | • Always use 64‑bit integer (`long`/`long long`).<br>• Validate that children indices exist (`l, r < n`). | |
| **Ugly** | • Trying to brute‑force all possible increment patterns (exponential).<br>• Recursively recomputing child costs from scratch at every node (O(n log n) if you don’t cache). | • Never revisit a node’s cost; propagate it once.<br>• Avoid nested loops over leaves. | |

### Why the Bottom‑Up Pattern is a Star in Interviews

1. **Clarity** – The algorithm reads like a simple for‑loop; interviewers can see the logic immediately.  
2. **Proof‑of‑Correctness** – The invariant “`cost[i]` stores minimal path cost from node `i` to any leaf” is maintained at every iteration.  
3. **Memory** – In‑place updates avoid extra arrays; if recursion is used, the stack depth is only `log n` for a perfect binary tree.  
4. **Extensibility** – The same pattern works for the follow‑up variant where you can *decrement* as well, by simply replacing `abs` with `max(0, leftCost - rightCost)`.

---

## 6. Edge‑Case Checklist

| Edge case | Why it matters | Fix |
|-----------|----------------|-----|
| `n` is just the minimum (3) | Only one internal node (root) | Loop handles it correctly (`n/2 - 1 == 0`). |
| All costs already equal | `increments` stays 0 | No special case needed. |
| Very large `n` (≈10⁵) | Potential 32‑bit overflow | Use 64‑bit (`long`/`long long`) for `res`. |

---

## 7. Variant: Increment **or** Decrement

If the problem statement were changed to allow both increments and decrements, the optimal strategy changes slightly:

* We would raise the cheaper subtree **up to** the heavier one, *and* we could optionally lower the heavier one if that leads to a lower total increment count.  
* This reduces to **making both subtrees equal to the maximum of their minimal path costs**, which yields the same `abs(leftCost - rightCost)` cost.  
* The rest of the logic stays identical; only the interpretation of the operation changes.

---

## 8. Takeaway for Your Next Coding Interview

* **LeetCode 2673** is a **classic O(n)** tree‑DP problem that tests:  
  1. Tree traversal (DFS or bottom‑up).  
  2. Working with implicit heaps/array representations of trees.  
  3. Thinking in terms of *sub‑problem invariants* and *propagation*.

* Mastering this pattern demonstrates:
  * **Clean, bug‑free code** (the 3‑line core logic).  
  * **Efficient use of space** (in‑place updates).  
  * **Mathematical reasoning** (alignment of costs, absolute differences).

* **Use it in your interview prep**:
  * Write the iterative version first – it’s easy to explain.  
  * Mention the recursive version – interviewers love seeing both iterative & recursive perspectives.  
  * Highlight the O(n) time & O(1) auxiliary space to impress on efficiency.

---

## 9. Complete Code Repository

All three language implementations are available in this GitHub repo:

```text
https://github.com/your-username/leetcode-2673-cost-path
```

Feel free to copy, adapt, and add unit tests.

---

## 10. Closing

Solving **LeetCode 2673** with a bottom‑up DFS not only earns you a perfect score on the platform but also showcases your ability to:

* Translate a problem into a clean invariant.  
* Optimize for both time and space.  
* Write concise, production‑ready code in Java, Python, or C++.

Add this problem to your interview‑prep playlist, and you’ll be well‑prepared for any data‑structure round that involves **binary trees** and **cost‑adjustment** logic.

Good luck, and happy coding!