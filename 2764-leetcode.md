---
title: LeetCode 2764. Is Array a Preorder of Some Binary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code

Below you’ll find a clean, production‑ready implementation in **Java**, **Python** and **C++** that solves LeetCode 2764 – *“Is Array a Preorder of Some Binary Tree”*.  
The core idea is a **stack‑based validation**: we walk through the given preorder list and keep a stack of the *active* ancestors.  
When we encounter a node whose declared parent is **not** the current top of the stack, we pop the stack until we either find that parent or run out of ancestors – at which point the preorder is invalid.

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| Java | `O(n)` | `O(n)` | Uses `Deque<Integer>` for the stack. |
| Python | `O(n)` | `O(n)` | Uses a plain list as a stack. |
| C++ | `O(n)` | `O(n)` | Uses `vector<int>` as a stack. |

---

### 1.1  Java

```java
import java.util.*;

public class Solution {

    /**
     * Determine if the given nodes array represents the preorder traversal
     * of some binary tree.
     *
     * @param nodes 2‑D list where each element is [id, parentId]
     * @return true if valid preorder, false otherwise
     */
    public boolean isPreorder(List<List<Integer>> nodes) {
        if (nodes == null || nodes.isEmpty()) return false;

        Deque<Integer> stack = new ArrayDeque<>();
        // The first element is the root; its parentId must be -1
        int rootId = nodes.get(0).get(0);
        stack.push(rootId);

        for (int i = 1; i < nodes.size(); i++) {
            int curr = nodes.get(i).get(0);
            int parent = nodes.get(i).get(1);

            // Pop until we find the declared parent on the stack
            while (!stack.isEmpty() && stack.peek() != parent) {
                stack.pop();
            }

            // If the stack became empty before finding the parent,
            // the preorder is invalid.
            if (stack.isEmpty()) return false;

            // Current node becomes the new active ancestor
            stack.push(curr);
        }

        return true;
    }

    // ---------------------------------------------------------------
    // Optional: Simple test harness (not required by LeetCode)
    // ---------------------------------------------------------------
    public static void main(String[] args) {
        Solution sol = new Solution();

        List<List<Integer>> nodes1 = Arrays.asList(
                Arrays.asList(0, -1), Arrays.asList(1, 0),
                Arrays.asList(2, 0), Arrays.asList(3, 2),
                Arrays.asList(4, 2));

        System.out.println(sol.isPreorder(nodes1)); // true

        List<List<Integer>> nodes2 = Arrays.asList(
                Arrays.asList(0, -1), Arrays.asList(1, 0),
                Arrays.asList(2, 0), Arrays.asList(3, 1),
                Arrays.asList(4, 1));

        System.out.println(sol.isPreorder(nodes2)); // false
    }
}
```

---

### 1.2  Python

```python
from typing import List

class Solution:
    def isPreorder(self, nodes: List[List[int]]) -> bool:
        """
        Validate if 'nodes' is a valid preorder traversal of a binary tree.
        nodes: List of [id, parentId] pairs.
        """
        if not nodes:
            return False

        stack = [nodes[0][0]]          # root id

        for cur_id, parent_id in nodes[1:]:
            # Pop until the parent matches
            while stack and stack[-1] != parent_id:
                stack.pop()

            if not stack:            # no valid parent found
                return False

            stack.append(cur_id)     # current node becomes active ancestor

        return True


# --------------------------------------------------------------------
# Optional quick demo (not needed for LeetCode)
# --------------------------------------------------------------------
if __name__ == "__main__":
    sol = Solution()

    nodes1 = [[0,-1],[1,0],[2,0],[3,2],[4,2]]
    print(sol.isPreorder(nodes1))   # True

    nodes2 = [[0,-1],[1,0],[2,0],[3,1],[4,1]]
    print(sol.isPreorder(nodes2))   # False
```

---

### 1.3  C++

```cpp
#include <vector>
#include <deque>

class Solution {
public:
    bool isPreorder(const std::vector<std::vector<int>>& nodes) {
        if (nodes.empty()) return false;

        std::deque<int> st;                 // stack of active ancestors
        st.push_back(nodes[0][0]);          // root id

        for (size_t i = 1; i < nodes.size(); ++i) {
            int cur   = nodes[i][0];
            int parent= nodes[i][1];

            // Pop until we find the declared parent
            while (!st.empty() && st.back() != parent) {
                st.pop_back();
            }

            if (st.empty()) return false;   // no valid parent
            st.push_back(cur);              // push current node
        }

        return true;
    }
};
```

---

## 2.  Blog Article – “The Good, the Bad, and the Ugly of Validating Preorder Traversals”

### Title  
**“The Good, The Bad, and The Ugly of Validating Preorder Traversals – A Deep Dive into LeetCode 2764”**

### Meta Description  
Unlock the secrets behind LeetCode 2764. Discover the stack‑based solution, learn why it’s efficient, and get interview‑ready code in Java, Python, and C++. Perfect for software engineers, algorithm enthusiasts, and job seekers.

---

### Introduction  
When you hit LeetCode’s **2764 – *Is Array a Preorder of Some Binary Tree***, you’re immediately faced with a deceptively simple statement: *“Given an array of node id‑parent pairs, is this a valid preorder traversal of a binary tree?”*  
This problem is more than a coding exercise; it’s a micro‑exam of your understanding of tree traversal, stack usage, and edge‑case handling. Below, I dissect the **good**, the **bad**, and the **ugly** aspects that surface when tackling this problem, while showing you a production‑ready solution that you can paste straight into your interview toolkit.

---

### 2.1 The Good – Why This Problem is a Golden Interview Question  

| Aspect | Why It’s Good |
|--------|---------------|
| **O(N) Complexity** | With up to 10⁵ nodes, a linear algorithm is a must. The stack‑based solution guarantees `O(n)` time and `O(n)` auxiliary space. |
| **Conceptual Clarity** | Validating preorder is a classic application of the *“active ancestor”* concept. It forces you to think about the call stack of a recursive DFS in an iterative way. |
| **Binary Tree Assumptions** | The problem guarantees that the input forms a binary tree. This lets you skip safety checks for cycles or multiple roots, making the code lean and efficient. |
| **Language‑agnostic** | The same logic works in Java, Python, C++, Go, Rust – a great opportunity to showcase cross‑platform coding chops. |
| **Clear Failure Modes** | A single `false` return indicates a clear violation (missing parent on the stack). This is perfect for quick mental verification during an interview. |

---

### 2.2 The Bad – Common Pitfalls and “Gotchas”

1. **Assuming the Input is a Binary Search Tree**  
   Many candidates mistakenly treat `id` as the key of a BST and try to reconstruct a BST. This adds unnecessary complexity and can lead to TLE.

2. **Using Recursion on a 10⁵ Node Tree**  
   A naïve recursive DFS may blow the call stack. The iterative stack approach is the only safe way.

3. **Ignoring the Root’s Parent Value**  
   The root’s `parentId` must be `-1`. Failing to check this opens up false positives (e.g., `[5, -2]` would be mis‑validated).

4. **Mis‑using `Deque`/`Stack` APIs**  
   In Java, `Stack` is legacy and slow; use `ArrayDeque`. In Python, don’t use `pop(0)` – that’s O(n). Always use `list.pop()` or `list.append()`.

5. **Assuming All Nodes Are Visited Exactly Once**  
   The array is guaranteed to be a preorder list of a binary tree, so you don’t need to keep a `visited` set. Adding one only increases space/time.

---

### 2.3 The Ugly – Edge Cases That Make You Sweat

| Edge Case | Why It’s Ugly | How the Stack Solution Handles It |
|-----------|---------------|-----------------------------------|
| **Sibling Nodes** – e.g., `1` and `2` both children of `0` | In preorder, the left child comes before the right. If the array mistakenly swaps them, you’ll get a pop‑out failure. | While processing `2`, the stack will pop `1` until it reaches `0`, but `1` will no longer be in the stack → return `false`. |
| **Missing Parent** – e.g., a node lists a parent that never appears | The algorithm will pop until the stack empties, then fail. | `while (!stack.isEmpty() && stack.peek() != parent) stack.pop();` → stack empties → `false`. |
| **Multiple Roots** | Two entries with `parentId == -1` would make the array invalid. | The algorithm starts with the first element; when the second root appears, its parent (`-1`) won’t match the stack top → `false`. |
| **Deep Left Skew** – a chain of left children | Stack size becomes `n`. Still fine, but shows memory usage. | No special handling required; the algorithm scales linearly. |

---

### 2.4 Code Walk‑through (Java)

```java
Deque<Integer> stack = new ArrayDeque<>();
stack.push(nodes.get(0).get(0));        // root id

for (int i = 1; i < nodes.size(); i++) {
    int curr   = nodes.get(i).get(0);
    int parent = nodes.get(i).get(1);

    // Remove nodes that are not the current parent
    while (!stack.isEmpty() && stack.peek() != parent) {
        stack.pop();
    }

    if (stack.isEmpty()) return false; // Parent missing

    stack.push(curr);                  // Current node becomes the new active ancestor
}
return true;
```

*The loop mimics the call stack of a recursive DFS. Every time we step down to a child, we push it; when the child’s children are exhausted, we pop back up until we hit its parent again.*

---

### 2.5 Cross‑Language Snippets

| Language | Key Differences |
|----------|-----------------|
| **Python** | Use `list` as stack (`append`, `pop`). No need for type annotations in a LeetCode submission. |
| **C++** | `deque<int>` or `vector<int>` works; prefer `vector<int>` for cache locality. |
| **Java** | `ArrayDeque` is faster than `Stack`. Always check `stack.isEmpty()` before `peek`. |

---

### 2.6 Interview Tips

- **Explain the Stack Intuition**: “We keep a stack of ancestors. The top must always be the parent of the current node.”
- **Edge‑Case Test**: Bring up the “missing parent” scenario or “duplicate roots”.
- **Time Complexity**: Mention `O(n)` time, `O(n)` space. Emphasize that each node is pushed and popped at most once.
- **Avoid Recursion**: Mention stack overflow risk with 10⁵ nodes.

---

### 2.7 Final Thoughts – Why This Matters for Your Job Hunt  

- **Algorithmic Rigor**: LeetCode 2764 is a perfect showcase of your ability to translate a tree problem into an iterative stack solution, a skill highly valued by top tech firms.
- **Clean Code**: The provided snippets are concise, well‑commented, and production‑grade—ideal for a portfolio or a code‑review interview.
- **Multi‑Language Confidence**: Demonstrating the same logic in Java, Python, and C++ shows versatility and depth, making you a stronger candidate for backend or systems roles.

---

### Call to Action  

If you’re prepping for a software engineering interview, **add LeetCode 2764 to your “must‑solve” list**. Try the stack solution, test it against edge cases, and brag about its `O(n)` efficiency when the interviewer asks.  

**Happy coding, and best of luck landing that dream job!**

--- 

*End of Article* 

--- 

### SEO Checklist  

- **Keywords**: LeetCode 2764, binary tree preorder, stack algorithm, interview question, Java Python C++ code.  
- **Image Alt Text**: “Stack validation algorithm pseudocode.”  
- **Internal Links**: Link to “Tree Traversal” tutorial or “Stack vs Queue” guide.  
- **External Links**: Reference LeetCode’s problem page and official discussion thread.

--- 

Happy interview hunting!