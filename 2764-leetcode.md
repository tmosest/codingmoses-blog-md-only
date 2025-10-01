---
title: LeetCode 2764. Is Array a Preorder of Some Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. LeetCode 2764 – “Is Array a Preorder of Some Binary Tree”

| Language | Time Complexity | Space Complexity |
|----------|-----------------|------------------|
| **Java** |  **O(n)** | **O(n)** |
| **Python** |  **O(n)** | **O(n)** |
| **C++** |  **O(n)** | **O(n)** |

> **Why it matters for your next interview**  
> Solving this problem demonstrates your understanding of tree traversal, stack simulation, and careful edge‑case handling – all skills recruiters love to see.

---

## 2. Problem Summary

You are given a 0‑indexed 2‑D array `nodes`.  
`nodes[i] = [id, parentId]` – the node with id `id` has a parent `parentId` (or `-1` if it is the root).  

The array is *supposed* to be the preorder traversal of **some** binary tree.  
Return **true** if that is indeed the case, otherwise return **false**.

> **Preorder** – visit current node → preorder of left child → preorder of right child.  
> The input guarantees that the ids and parent relationships form a valid binary tree (no cycles, unique parent for each node, etc.).

---

## 3. Intuition

During a preorder walk you always **finish** a subtree before moving to a sibling.  
If we keep a stack of “still open” ancestors, the current node’s parent must be the *top* of that stack.  

If the parent is **not** on the stack, we have jumped out of the current subtree too early – the array cannot be a preorder traversal.

---

## 4. Algorithm (Stack‑Based)

1. **Initialize** a stack with the root id (`nodes[0][0]`).
2. **Iterate** over the rest of `nodes` (from index 1):
   * `curr` = current node id  
   * `parent` = its parent id
   * While the stack is non‑empty *and* the top element is **not** `parent`, pop the stack (we are closing sub‑trees).
   * After the loop, if the stack is empty → `parent` is missing → **return false**.
   * Push `curr` onto the stack (start exploring its subtree).
3. **If we finish the loop** without failing, the array is a valid preorder → **return true**.

Because every node is pushed/popped at most once, the time is linear.

---

## 5. Code

### Java (Stack‑Based)

```java
import java.util.*;

class Solution {
    public boolean isPreorder(List<List<Integer>> nodes) {
        // the stack keeps ids of nodes that are still "open"
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(nodes.get(0).get(0));      // root id

        for (int i = 1; i < nodes.size(); i++) {
            int curr  = nodes.get(i).get(0);
            int parent = nodes.get(i).get(1);

            // close subtrees until the current parent is at the top
            while (!stack.isEmpty() && stack.peek() != parent) {
                stack.pop();
            }

            // if stack empty, parent not found -> invalid preorder
            if (stack.isEmpty()) return false;

            stack.push(curr);
        }
        return true;
    }
}
```

### Python

```python
from typing import List

class Solution:
    def isPreorder(self, nodes: List[List[int]]) -> bool:
        stack = [nodes[0][0]]          # root id

        for i in range(1, len(nodes)):
            curr, parent = nodes[i]
            while stack and stack[-1] != parent:
                stack.pop()

            if not stack:             # parent not found
                return False

            stack.append(curr)
        return True
```

### C++

```cpp
#include <vector>
#include <deque>

class Solution {
public:
    bool isPreorder(const std::vector<std::vector<int>>& nodes) {
        std::deque<int> stack;          // deque works as a stack
        stack.push_back(nodes[0][0]);   // root id

        for (size_t i = 1; i < nodes.size(); ++i) {
            int curr   = nodes[i][0];
            int parent = nodes[i][1];

            while (!stack.empty() && stack.back() != parent) {
                stack.pop_back();        // close subtrees
            }

            if (stack.empty()) return false; // parent missing
            stack.push_back(curr);          // start new subtree
        }
        return true;
    }
};
```

> **Tip** – In all languages the stack can be an `ArrayDeque`, `list`, or `deque` respectively; any LIFO container works.

---

## 6. Complexity Analysis

| Metric | Explanation | Result |
|--------|-------------|--------|
| **Time** | Each node is pushed once and popped at most once | **O(n)** |
| **Space** | Stack holds at most height of tree (≤ n) | **O(n)** |
| **Why O(n) is optimal** | You have to look at every node at least once, so linear time is the best possible. |

---

## 7. Edge‑Case Checklist

| Edge case | Why it matters | How the algorithm handles it |
|-----------|----------------|-----------------------------|
| Single node tree (`nodes.length == 1`) | The loop never runs – always true | Stack starts with root, loop skipped |
| Root id not `0` or non‑sequential ids | Problem statement guarantees uniqueness; algorithm uses ids literally | No assumptions about id ranges |
| Parent id equals `-1` for non‑root | Impossible because tree is guaranteed valid | `-1` will never be on stack → algorithm returns false (but input never contains this case) |
| Node appears before its parent | Stack will pop until parent appears → fail | `stack.empty()` becomes true → return false |

---

## 8. The Good, the Bad, and the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Intuition** | Clear: preorder implies a stack of open ancestors | Some people still think it requires building the tree | Trying to build the whole tree first is unnecessary overhead |
| **Complexity** | O(n) time, O(n) space, no recursion | Using recursion may hit stack overflow on deep trees | Naïve simulation with `List` pops in the middle leads to O(n²) |
| **Readability** | One loop, one stack, few lines | Too many comments can clutter | Over‑engineering with auxiliary maps or parent‑index lookups |
| **Edge‑case safety** | Handles all valid inputs by construction | Edge‑cases like empty array not needed | Forgetting to check stack emptiness → false positives |
| **Implementation** | Works in all major languages with minimal changes | Java requires careful use of `ArrayDeque` | C++ may accidentally use `vector` for stack causing O(n²) pops |

---

## 9. Conclusion & Takeaway

The “Is Array a Preorder of Some Binary Tree” problem boils down to **simulating** a preorder walk with a stack.  
If the current node’s parent is *not* the current stack top, you’ve jumped out of the wrong subtree – the traversal is invalid.

**Why this matters for interviews**

* Shows you understand tree traversals beyond the textbook.
* Demonstrates careful handling of edge‑cases.
* Highlights clean, linear‑time coding – something recruiters love.

### Ready to impress?  
Copy the snippet in your preferred language, practice with variations (e.g., post‑order, in‑order), and add this to your portfolio. Good luck landing that next coding interview! 🚀

--- 

### SEO Highlights (for your blog post)

- LeetCode 2764
- Is Array a Preorder of Some Binary Tree
- Java Python C++ solution
- Binary tree preorder traversal
- Interview coding problem
- Stack-based algorithm
- O(n) time, O(n) space
- Coding interview tips

Feel free to adapt the headings and content to fit your personal blog style or LinkedIn article. Happy coding!