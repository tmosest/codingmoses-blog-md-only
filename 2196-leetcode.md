---
title: LeetCode 2196. Create Binary Tree From Descriptions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 2196 – **Create Binary Tree From Descriptions**

**Goal:**  
Given an array of descriptions `[parent, child, isLeft]`, build the binary tree and return its root.  
- `isLeft == 1` → child is the left child of parent  
- `isLeft == 0` → child is the right child of parent  

The tree is guaranteed to be valid and each node value is unique.

---

### 1.1 Algorithm Overview

| Step | Action | Reason |
|------|--------|--------|
| 1 | **Create/lookup TreeNode objects** for every unique value using a `Map<Integer, TreeNode>`. | Allows us to reference the same node object when linking parents and children. |
| 2 | **Record every child value** in a `Set`. | After all relations are processed, the node that never appears as a child is the root. |
| 3 | **Link parents to children** according to the `isLeft` flag. | Construct the actual binary tree. |
| 4 | **Find the root** – the value not present in the child‑set. | This node has no parent. |

The algorithm runs in *O(n)* time and uses *O(n)* extra space, where *n* is the number of descriptions.

---

### 1.2 Code

#### Java

```java
import java.util.*;

class Solution {
    public TreeNode createBinaryTree(int[][] descriptions) {
        // Step 1: create map from value -> TreeNode
        Map<Integer, TreeNode> nodeMap = new HashMap<>();
        Set<Integer> childSet = new HashSet<>();

        for (int[] desc : descriptions) {
            int parentVal = desc[0];
            int childVal  = desc[1];
            int isLeft    = desc[2];

            TreeNode parent = nodeMap.computeIfAbsent(parentVal, TreeNode::new);
            TreeNode child  = nodeMap.computeIfAbsent(childVal,  TreeNode::new);

            if (isLeft == 1) parent.left = child;
            else            parent.right = child;

            childSet.add(childVal);
        }

        // Step 4: find the root (value not in childSet)
        for (int val : nodeMap.keySet()) {
            if (!childSet.contains(val)) {
                return nodeMap.get(val);
            }
        }
        return null; // shouldn't happen for valid input
    }
}
```

#### Python

```python
from collections import defaultdict
from typing import List, Optional

# Definition for a binary tree node.
class TreeNode:
    def __init__(self, val: int=0, left:'TreeNode'=None, right:'TreeNode'=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def createBinaryTree(self, descriptions: List[List[int]]) -> Optional[TreeNode]:
        nodes = {}
        child_vals = set()

        for parent, child, is_left in descriptions:
            if parent not in nodes:
                nodes[parent] = TreeNode(parent)
            if child not in nodes:
                nodes[child] = TreeNode(child)

            if is_left == 1:
                nodes[parent].left = nodes[child]
            else:
                nodes[parent].right = nodes[child]

            child_vals.add(child)

        # Root is the node that never appears as a child
        for val, node in nodes.items():
            if val not in child_vals:
                return node
        return None  # unreachable for valid input
```

#### C++

```cpp
#include <unordered_map>
#include <unordered_set>
#include <vector>

struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    TreeNode* createBinaryTree(std::vector<std::vector<int>>& descriptions) {
        std::unordered_map<int, TreeNode*> nodeMap;
        std::unordered_set<int> childSet;

        for (auto& desc : descriptions) {
            int parent = desc[0];
            int child  = desc[1];
            int isLeft = desc[2];

            if (!nodeMap.count(parent))
                nodeMap[parent] = new TreeNode(parent);
            if (!nodeMap.count(child))
                nodeMap[child]  = new TreeNode(child);

            if (isLeft == 1)
                nodeMap[parent]->left = nodeMap[child];
            else
                nodeMap[parent]->right = nodeMap[child];

            childSet.insert(child);
        }

        for (auto& [val, node] : nodeMap) {
            if (!childSet.count(val))
                return node;          // root found
        }
        return nullptr;                 // shouldn't happen
    }
};
```

---

## 2.  Blog Article – “The Good, The Bad, and The Ugly of *Create Binary Tree From Descriptions*”

> **Keyword Focus:** `Create Binary Tree From Descriptions`, `LeetCode 2196`, `binary tree construction`, `interview coding challenge`, `software engineer interview`, `job interview algorithm`.

### 2.1 Introduction

When preparing for a **software engineering interview**, one often encounters LeetCode problems that test data‑structure fundamentals. *LeetCode 2196 – Create Binary Tree From Descriptions* is a perfect example: a moderate‑difficulty problem that checks your understanding of **hashmaps**, **tree traversal**, and **root identification**.  

In this article, we’ll dissect the problem, walk through a clean solution in **Java, Python, and C++**, discuss common pitfalls, and explain why mastering this problem makes you a more attractive candidate for senior roles.

---

### 2.2 Problem Recap

> *Input:* `descriptions = [[parent, child, isLeft], …]`  
> *Output:* The root of the constructed binary tree.

**Key constraints:**
- All node values are unique.
- The tree is guaranteed to be valid.
- `isLeft` is either `0` (right child) or `1` (left child).

The real challenge lies in **identifying the root** without an explicit pointer. The rest is simply mapping relationships.

---

### 2.3 The Good – Why This Problem is a Power‑Up

| Aspect | Why It Helps |
|--------|--------------|
| **Maps & Sets** | Reinforces fast look‑ups – a staple in interview coding. |
| **Root Discovery** | Teaches a common pattern: “find the element that never appears as a child”. |
| **O(n) Complexity** | Shows you can solve the problem in linear time, a key interview expectation. |
| **Language Agnostic** | The logic translates seamlessly between Java, Python, C++, and many others. |
| **Clear Problem Statement** | Focus on algorithmic thinking, not on parsing messy inputs. |

### 2.4 The Bad – Common Mistakes

1. **Using an array for node storage.**  
   The node values can be as large as 10⁵; an array would waste space.  
   → Use a **hash map** (`Map<Integer, TreeNode>` / `unordered_map`).

2. **Missing the root detection step.**  
   Some candidates simply return any node; the interview panel will instantly spot the bug.  
   → Keep a separate **child set** and pick the node that’s not in it.

3. **Assuming the first description contains the root.**  
   Descriptions are unordered; the root could appear anywhere.  
   → Do **not** make that assumption.

4. **Creating duplicate TreeNode objects.**  
   Failing to reuse existing nodes leads to an incorrect tree structure.  
   → Always `computeIfAbsent` or `setdefault`.

5. **Forgetting to handle the `isLeft` flag correctly.**  
   Mixing left/right can flip the tree.  
   → Double‑check the conditional logic.

---

### 2.5 The Ugly – Edge Cases and Pitfalls

- **Large inputs (n = 10⁴).**  
  The algorithm still runs in O(n) but you must be mindful of recursion depth if you choose a recursive approach for traversal. Our solution only builds the tree; we avoid recursion altogether.

- **Memory constraints in tight environments.**  
  While our solution uses `O(n)` extra memory, some interviewers might ask for a **O(1) extra space** variant (e.g., constructing nodes in-place without an external map). This requires a more advanced strategy like **node interning** or **pointer reuse**.

- **Null children in the final representation.**  
  When printing the tree for verification, remember that a `null` child is part of the output format, e.g., `[1,2,null,null,3,4]`.  

---

### 2.6 Step‑by‑Step Implementation

Below is a concise, language‑agnostic pseudocode, followed by the Java, Python, and C++ implementations already provided in the code section.

```text
1. Create empty map: value → TreeNode
2. Create empty set: childValues
3. For each description [parent, child, isLeft]:
       a. Get or create parent node from map
       b. Get or create child node from map
       c. Link parent.left/right based on isLeft
       d. Add child to childValues
4. For each value in map:
       If value not in childValues:
           Return corresponding TreeNode (root)
```

---

### 2.7 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Building nodes and linking | **O(n)** | **O(n)** (map + set) |
| Finding root | **O(n)** | – |

The algorithm is optimal: you must touch each description at least once.

---

### 2.8 How This Problem Prepares You for Real Interviews

1. **Data‑Structure Proficiency:** Demonstrates mastery of hash maps and trees.  
2. **Problem Decomposition:** Clear separation of node creation, linking, and root discovery.  
3. **Clean Code Practices:** Reuse objects, avoid redundant data structures, use descriptive variable names.  
4. **Cross‑Language Portability:** Shows you can translate logic into any language the hiring team uses.

When interviewing, frame your solution with these points:

> *“I first map every node value to a TreeNode object to avoid duplication. Then I use a set to record child values so that after processing all relationships, the node that never appears as a child is clearly the root.”*

This concise reasoning signals to the interviewer that you understand the problem deeply and can communicate effectively—a critical skill for senior software engineers.

---

### 2.9 Final Take‑away

- **LeetCode 2196** may look simple, but it is a *mini‑test* for mapping, set operations, and root identification.  
- Master it in **Java, Python, and C++**; highlight the algorithmic insight during your interview.  
- Use it as a talking point to showcase your *clean‑code* philosophy and *problem‑solving* mindset.  

Good luck! 🚀

--- 

**SEO Keywords Used:** Create Binary Tree From Descriptions, LeetCode 2196, binary tree construction, software engineer interview, job interview algorithm, build binary tree, hashmap, root detection, interview coding challenge.