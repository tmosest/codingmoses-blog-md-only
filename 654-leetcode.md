---
title: LeetCode 654. Maximum Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Maximum Binary Tree – The Good, The Bad, and The Ugly  
**LeetCode 654 – A Deep Dive Into Algorithmic Thinking**  

---

## Table of Contents  

| Section | What you’ll learn |
|---------|-------------------|
| 📌 Problem Statement | Understand the challenge and constraints |
| 🔍 Naïve Approach | O(n²) divide‑and‑conquer |
| ⚡️ Divide & Conquer | Classic recursion – clear but slow |
| 🔁 Monotonic Stack | O(n) optimal solution |
| 📐 Time & Space Complexity | Where the “ugly” part hides |
| 🧪 Edge Cases & Testing | Make your code bullet‑proof |
| 💡 Common Pitfalls | Avoid the most frequent mistakes |
| 🚀 Take‑away & Career Impact | Why mastering this matters for interviews |
| 📚 Further Resources | Deepen your knowledge |

> **SEO Keywords**: *Maximum Binary Tree, LeetCode 654, algorithm, monotonic stack, tree construction, interview prep, Java solution, Python solution, C++ solution, divide and conquer, time complexity, space complexity.*

---

## 1️⃣ Problem Statement

> **Maximum Binary Tree**  
> You’re given an array `nums` of distinct integers.  
> Build a binary tree using the following rules:  
> 1. The root is the maximum element of `nums`.  
> 2. The left subtree is the maximum binary tree of the sub‑array left of the max.  
> 3. The right subtree is the maximum binary tree of the sub‑array right of the max.  

Return the root node of the constructed tree.

> **Constraints**  
> * `1 ≤ nums.length ≤ 1000`  
> * `0 ≤ nums[i] ≤ 1000`  
> * All integers in `nums` are unique.

---

## 2️⃣ Naïve Approach – Brute Force

The most straightforward solution mirrors the description:

```text
find index of maximum in nums
create node with that value
recurse on left slice
recurse on right slice
```

### Complexity  
*Finding the maximum* takes `O(k)` where `k` is the slice length.  
At each level we partition the array, so the total work becomes

```
T(n) = T(k1) + T(k2) + O(n)  ->  O(n²)
```

For `n = 1000`, this is still fine for LeetCode, but it quickly becomes un‑scalable and a classic interview “gotcha”.

---

## 3️⃣ Divide & Conquer – Recursive O(n²) Solution

A cleaner implementation uses recursion with a helper that accepts an index range.

```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        return helper(nums, 0, nums.length - 1);
    }
    private TreeNode helper(int[] nums, int l, int r) {
        if (l > r) return null;
        int maxIdx = l;
        for (int i = l + 1; i <= r; i++)
            if (nums[i] > nums[maxIdx]) maxIdx = i;
        TreeNode node = new TreeNode(nums[maxIdx]);
        node.left  = helper(nums, l, maxIdx - 1);
        node.right = helper(nums, maxIdx + 1, r);
        return node;
    }
}
```

> **The Good** – *Clear, concise, and matches the problem’s description perfectly.*  
> **The Bad** – *Time complexity `O(n²)`.*  
> **The Ugly** – *Repeated linear scans, unnecessary memory use, and stack depth can blow up for larger inputs.*

---

## 4️⃣ Monotonic Stack – The Optimal `O(n)` Solution

### The Key Insight

While constructing the tree, **the relative order of elements is what matters**.  
When you process `nums` from left to right, you only need to know the **last larger element** – the element that will become the parent of the current node.

A **monotonic decreasing stack** keeps nodes whose values are strictly decreasing from bottom to top.  
When a new value `x` arrives:

1. All nodes on the stack with value `< x` become the *left child* of `x`.  
2. If the stack isn’t empty after popping, the new top’s *right child* becomes `x`.  
3. Push `x` onto the stack.

At the end, the **bottom of the stack** is the root of the maximum binary tree.

### Complexity  
*Each node is pushed and popped at most once* → **`O(n)` time**.  
Stack space: **`O(n)`**.

### Java Implementation (Monotonic Stack)

```java
class Solution {
    public TreeNode constructMaximumBinaryTree(int[] nums) {
        if (nums == null || nums.length == 0) return null;

        Deque<TreeNode> stack = new ArrayDeque<>();

        for (int num : nums) {
            TreeNode cur = new TreeNode(num);

            // All nodes with smaller value become left child of cur
            while (!stack.isEmpty() && stack.peek().val < num) {
                cur.left = stack.pop();
            }

            // The current top (if exists) becomes cur’s right child
            if (!stack.isEmpty()) {
                stack.peek().right = cur;
            }

            stack.push(cur);
        }

        // Bottom of the stack is the root
        return stack.peekLast();
    }
}
```

> **The Good** – *Runs in linear time and is easy to understand once the stack idea surfaces.*  
> **The Bad** – *Initial learning curve; stack manipulation can be error‑prone.*  
> **The Ugly** – *In languages without built‑in stack types (e.g., C) you have to manage pointers manually.*

### Python Implementation (Monotonic Stack)

```python
from typing import List, Optional

class TreeNode:
    def __init__(self, val: int = 0, left: 'TreeNode' = None, right: 'TreeNode' = None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def constructMaximumBinaryTree(self, nums: List[int]) -> Optional[TreeNode]:
        stack = []
        for num in nums:
            node = TreeNode(num)
            while stack and stack[-1].val < num:
                node.left = stack.pop()
            if stack:
                stack[-1].right = node
            stack.append(node)
        return stack[0] if stack else None
```

### C++ Implementation (Monotonic Stack)

```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
        vector<TreeNode*> st;
        for (int num : nums) {
            TreeNode* node = new TreeNode(num);
            while (!st.empty() && st.back()->val < num) {
                node->left = st.back();
                st.pop_back();
            }
            if (!st.empty())
                st.back()->right = node;
            st.push_back(node);
        }
        return st.empty() ? nullptr : st.front();
    }
};
```

---

## 5️⃣ Edge Cases & Testing

| Test | Description | Expected Root |
|------|-------------|---------------|
| `[]` | Empty array | `null` |
| `[7]` | Single element | `TreeNode(7)` |
| `[1,2,3,4,5]` | Strictly increasing | `TreeNode(5)` with all left children |
| `[5,4,3,2,1]` | Strictly decreasing | `TreeNode(5)` with all right children |
| `[3,2,1,6,0,5]` | Mixed | `TreeNode(6)` with subtrees per example |

**Tip**: Use a helper to traverse the tree and serialize it into an array (level order) to compare against LeetCode’s expected output.

---

## 6️⃣ Common Pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Wrong stack condition** (`<=` instead of `<`) | Duplicate values are not allowed, but using `<=` may pop too many nodes. | Use strict `<`. |
| **Not setting `left` after popping** | The popped node becomes the *right* child incorrectly. | Assign `cur.left = poppedNode` inside the loop. |
| **Returning the wrong stack element** | Returning `stack.peek()` gives the most recent node, not the root. | Return `stack.peekLast()` (bottom of stack). |
| **Memory leaks in C++** | Forgetting to `delete` nodes. | Use smart pointers (`std::unique_ptr`) or a destructor. |
| **Recursion depth overflow** | Recursive solution uses stack depth `O(n)`. | Use the iterative stack method. |

---

## 7️⃣ Why Mastering This Matters

* **Algorithmic mindset** – Turning a description into an efficient data‑structure pattern is a core interview skill.  
* **Data‑structure fluency** – Binary trees and monotonic stacks are frequent interview topics.  
* **Time‑space trade‑offs** – Being able to spot the “ugly” quadratic part and improve it demonstrates problem‑solving depth.  
* **Cross‑language versatility** – You’ll impress recruiters if you can solve the same problem in **Java, Python, and C++** (or any other language you love).  

> **Career Take‑away**:  
> *LeetCode 654 is a “show‑case” problem – it looks easy, but the optimal solution hides a subtle pattern.  
> Solving it elegantly will give you a confidence boost for data‑structure interviews and help you land roles that value clean, efficient code.*

---

## 7️⃣ Further Resources

1. **Monotonic Stack Pattern** – *LeetCode discuss thread*  
2. **Recursive Tree Construction** – *CS‑Academy’s “Build Tree from Pre‑order & In‑order”*  
3. **Memory Management in C++** – *“Unique_ptr and Smart Pointers” on GeeksforGeeks*  
4. **Interview Prep Books** – *“Cracking the Coding Interview” – Chapter on Binary Trees & Stack*  
5. **YouTube Walk‑through** – *Ben Awad’s “Monotonic Stack” playlist (Python + JavaScript)*

---

## 🎯 Final Take‑away

> *The Maximum Binary Tree is a deceptively simple problem that rewards a deeper algorithmic insight.*  
> By moving from a brute‑force scan to a **monotonic stack**, you turn a `O(n²)` solution into a clean `O(n)` one that will stand out in interviews.  

Keep practicing the pattern – it appears in other LeetCode problems like **“Largest Rectangle in Histogram”** and **“Maximum Sum Rectangular Subarray”**. Mastering this idea is a small step that can elevate your entire coding‑interview skill set.

Good luck, and happy coding! 🚀