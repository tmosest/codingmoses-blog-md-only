---
title: LeetCode 1490. Clone N-ary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Clone N‑ary Tree – 1490 (LeetCode)
**Good, Bad & Ugly – A Job‑Ready Deep Dive**  
*Published 2025 – SEO‑optimized for interview prep and hiring managers*

---

## 1. Problem Overview

LeetCode 1490 – **Clone N‑ary Tree**  
- **Difficulty:** Medium  
- **Goal:** Return a deep copy of an N‑ary tree.  
- **Input format:** Level‑order serialization with `null` as a child separator.  
- **Constraints:**  
  - Depth ≤ 1000  
  - Total nodes ≤ 10⁴  

> The task is essentially the same as *Clone Graph* (LC 133) but with a tree structure.  
> You can solve it with DFS (recursive or iterative) or BFS – all O(N) time and O(N) auxiliary space.

---

## 2. Why This Problem Matters

| Aspect | Why Interviewers Care |
|--------|-----------------------|
| **Recursive vs. Iterative** | Shows mastery of stack/queue management and base‑case handling. |
| **HashMap / Dictionary** | Demonstrates handling of node identity (pointer equality). |
| **Graph vs. Tree** | Proves you can reuse a solution across data structures. |
| **Space–Time Tradeoffs** | Highlights understanding of depth‑first vs. breadth‑first traversal. |

> Solving this in *all three major languages* (Java, Python, C++) signals that you’re a “full‑stack” coder ready for any tech stack.

---

## 3. The “Good” – Elegant Recursive DFS

```java
/*  Java – Recursive DFS (clean & idiomatic)  */
class Node {
    public int val;
    public List<Node> children = new ArrayList<>();
    public Node() {}
    public Node(int _val) { val = _val; }
    public Node(int _val, List<Node> _children) { val = _val; children = _children; }
}

class Solution {
    public Node cloneTree(Node root) { return dfs(root); }

    private Node dfs(Node cur) {
        if (cur == null) return null;
        Node clone = new Node(cur.val);
        for (Node child : cur.children) {
            clone.children.add(dfs(child));
        }
        return clone;
    }
}
```

**Pros**
- **Simplicity** – one line per step, easy to reason about.  
- **Space** – O(H) recursion stack (H = tree height).  
- **Readability** – perfect for coding interviews.

**Cons**
- Recursion depth may hit Java’s stack limit if the tree is very deep (≤1000 → safe, but still a risk in languages with stricter limits).  

---

## 4. The “Bad” – Inefficient Map Usage

```java
/*  Java – Using a Map that re‑creates nodes every time (O(N²))  */
class BadSolution {
    public Node cloneTree(Node root) {
        if (root == null) return null;
        Map<Node, Node> map = new HashMap<>();
        clone(root, map);
        return map.get(root);
    }

    private void clone(Node cur, Map<Node, Node> map) {
        if (cur == null) return;
        Node clone = map.computeIfAbsent(cur, n -> new Node(n.val));
        for (Node child : cur.children) {
            clone.children.add(clone(child, map));
        }
    }
}
```

> The `computeIfAbsent` trick is fine, but the code still forces a map lookup for **every** child, causing a hidden O(N²) constant factor in the worst case.  
> **Avoid** in production; use the simple recursive version above.

---

## 5. The “Ugly” – Manual Stack Management in Java

```java
/*  Java – Manual stack but still O(N)  */
class UglySolution {
    public Node cloneTree(Node root) {
        if (root == null) return null;
        Map<Node, Node> map = new HashMap<>();
        Deque<Node> stack = new ArrayDeque<>();
        stack.push(root);
        map.put(root, new Node(root.val));

        while (!stack.isEmpty()) {
            Node cur = stack.pop();
            for (Node child : cur.children) {
                if (!map.containsKey(child)) {
                    map.put(child, new Node(child.val));
                    stack.push(child);
                }
                map.get(cur).children.add(map.get(child));
            }
        }
        return map.get(root);
    }
}
```

> Works fine but clutters the solution. In interviews, a *single* recursive method is usually preferred.  
> Only use iterative versions when the interviewer explicitly asks for it.

---

## 6. BFS – Level‑by‑Level Clone (Python & C++)

### Python

```python
#  Python 3 – BFS (queue) solution
from collections import deque
from typing import List

class Node:
    def __init__(self, val=0, children=None):
        self.val = val
        self.children = children or []

class Solution:
    def cloneTree(self, root: 'Node') -> 'Node':
        if not root:
            return None

        mapping = {root: Node(root.val)}
        q = deque([root])

        while q:
            cur = q.popleft()
            for child in cur.children:
                if child not in mapping:
                    mapping[child] = Node(child.val)
                    q.append(child)
                mapping[cur].children.append(mapping[child])

        return mapping[root]
```

### C++

```cpp
//  C++17 – BFS (queue) solution
#include <vector>
#include <queue>
#include <unordered_map>

using namespace std;

struct Node {
    int val;
    vector<Node*> children;
    Node(int _val = 0) : val(_val) {}
};

class Solution {
public:
    Node* cloneTree(Node* root) {
        if (!root) return nullptr;
        unordered_map<Node*, Node*> mp;
        mp[root] = new Node(root->val);
        queue<Node*> q;
        q.push(root);

        while (!q.empty()) {
            Node* cur = q.front(); q.pop();
            for (Node* child : cur->children) {
                if (!mp.count(child)) {
                    mp[child] = new Node(child->val);
                    q.push(child);
                }
                mp[cur]->children.push_back(mp[child]);
            }
        }
        return mp[root];
    }
};
```

**Key Takeaways**

| Language | Preferred Traversal | Complexity |
|----------|---------------------|------------|
| Java | Recursive DFS (default) | O(N) time, O(H) stack |
| Python | BFS (queue) is clearer | O(N) time, O(N) queue |
| C++ | BFS or DFS, both OK | O(N) time, O(N) memory |

---

## 7. Follow‑Up: From Trees to Graphs

If the input were a **directed graph** instead of a tree, the same pattern works:

- **Maintain a `map<original, clone>`** to avoid revisiting nodes.  
- Traverse with DFS/BFS as usual.  
- The only difference is that we **must** check the map before recursing/copying to handle cycles.

> **Interview Trick:**  
> *“I’ll just copy the tree logic but add a visited set. That works for graphs too.”*  
> Show the interviewer you understand *identity vs. value*.

---

## 8. Time & Space Complexity Recap

| Approach | Time | Space |
|----------|------|-------|
| Recursive DFS | **O(N)** | O(H) recursion stack |
| Iterative DFS | **O(N)** | O(H) stack |
| BFS | **O(N)** | O(N) queue (worst case width) |

> For a balanced tree, H ≈ log N → almost linear stack usage.  
> For a degenerate (linked‑list) tree, H ≈ N → still acceptable for the given constraints.

---

## 9. SEO‑Friendly Closing: “Why This Blog Helps Your Job Hunt”

1. **Keyword Rich** – “Clone N‑ary Tree”, “LeetCode 1490”, “DFS”, “BFS”, “Java Python C++”.  
2. **Interview‑Ready** – Covers *all* traversal styles, map usage, and the graph extension.  
3. **Job‑Specific** – Demonstrates that you can solve a canonical interview problem *across languages*.  
4. **Problem‑Solving Insight** – Explains why certain approaches are “good” or “bad”, a skill recruiters love.  

> **Takeaway:** Mastering this problem proves you can *deep‑clone*, *traverse*, and *reuse* logic across data structures—a hallmark of a senior backend engineer.

---

## 10. Full Code Snippets

> Use the following snippets as a quick reference or to copy‑paste into your IDE.

### Java (Recursive DFS)

```java
class Node { ... }          // same as in section 3
class Solution { ... }      // recursive DFS code
```

### Java (Iterative DFS)

```java
class Solution { ... }      // iterative DFS code (section 4)
```

### Java (BFS)

```java
class Solution { ... }      // BFS code (section 4)
```

### Python (BFS)

```python
class Node: ...
class Solution: ...
```

### C++ (BFS)

```cpp
struct Node { ... };
class Solution { ... };
```

---

## 11. Quick‑Start Checklist

| ✅ | Item |
|----|------|
| ✔️ | Write the `Node` definition in your language.  
| ✔️ | Choose *DFS* for quick interviews (Java/Python) or *BFS* for clarity (Python/C++).  
| ✔️ | Use a single `map/Dictionary` to store original → clone.  
| ✔️ | Demonstrate the graph extension if asked.  
| ✔️ | Test with degenerate trees (depth = 1000) to confirm stack safety.  

---

**Happy coding – and good luck on your next interview!**  
*(Feel free to bookmark this page or share it with your fellow candidates.)*