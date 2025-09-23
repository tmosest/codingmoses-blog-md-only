---
title: LeetCode 428. Serialize and Deserialize N-ary Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 428. Serialize and Deserialize N‑ary Tree  
> **LeetCode Hard | Job‑Interview‑Ready**  

---

### 1. 🚀 Problem Overview  

In a rooted N‑ary tree every node can have an arbitrary number of children (up to **N**).  
The task is to **convert the whole tree into a string** (`serialize`) and then **re‑construct the exact same tree from that string** (`deserialize`).  

- **Input**: `root` – pointer/reference to the root of an N‑ary tree (may be `null`).  
- **Output**:  
  * `serialize(root)` → `String` that uniquely represents the tree.  
  * `deserialize(data)` → `Node` that is bit‑for‑bit identical to the original tree.  

**Important constraints**

| | Value |
|---|-------|
| *Nodes* | `≤ 10⁴` |
| *Value range* | `0 ≤ node.val ≤ 10⁹` |
| *No global/static state* | each call must be **stateless**. |
| *Time* | Linear in the number of nodes. |
| *Space* | O(number of nodes) for the output string. |

---

### 2. 📚 Why This Problem Matters  

* **Interview staple** – Many senior‑level interviewers ask for a “serialize/deserialize” trick for trees, linked lists, graphs, etc.  
* **LeetCode 428** is part of the “Binary Tree” and “Design” collections, and it appears in the “Graph/Tree” job‑prep set.  
* Mastering this problem demonstrates a solid grasp of recursion, traversal techniques, and careful string manipulation – all core skills for algorithm‑centric roles (Software Engineer, Backend Engineer, Data Engineer, etc.).  

---

### 2️⃣  Solution Strategies  

Below are the **three most common patterns** you’ll see in production interview solutions:

| Pattern | What it does | Pros | Cons |
|---------|--------------|------|------|
| **Depth‑First Search (DFS) with child count** | `val childCount  <children‑DFS>` | *Simple, no extra sentinel symbols.* | Requires careful index handling when parsing. |
| **Breadth‑First Search (BFS) with sentinel nulls** | `val  childCnt  child1  child2 … null  …` | *Iterative, easy to understand.* | Must remember where each child list ends. |
| **Custom format with delimiters** | `1|3|2|0|4|0…` | *Very compact, no spaces needed.* | More error‑prone for larger values; hard to debug. |

Below we implement the **DFS + child‑count** pattern in **Java, Python, and C++** – the cleanest, fastest, and safest way to meet the problem’s constraints.

---

## 💻 3‑Language Implementations  

> **All three codes are stateless, O(1) auxiliary space (except recursion stack), and guaranteed to reconstruct the exact same tree.**

### 3.1 Java Solution  

```java
import java.util.*;

// LeetCode Node definition
class Node {
    public int val;
    public List<Node> children;

    public Node() { this.children = new ArrayList<>(); }

    public Node(int val) {
        this.val = val;
        this.children = new ArrayList<>();
    }

    public Node(int val, List<Node> children) {
        this.val = val;
        this.children = children;
    }
}

class Codec {

    /** Encodes an N‑ary tree to a single string. */
    public String serialize(Node root) {
        if (root == null) return "";          // null tree → empty string
        StringBuilder sb = new StringBuilder();
        dfsEncode(root, sb);
        return sb.toString();
    }

    private void dfsEncode(Node node, StringBuilder sb) {
        sb.append(node.val).append(' ');           // node value
        sb.append(node.children.size()).append(' '); // number of children
        for (Node child : node.children) {
            dfsEncode(child, sb);                 // recursively encode each child
        }
    }

    /** Decodes your encoded data to tree. */
    public Node deserialize(String data) {
        if (data.isEmpty()) return null;          // empty string → null tree
        String[] tokens = data.split("\\s+");
        int[] index = {0};                         // mutable index holder
        return dfsDecode(tokens, index);
    }

    private Node dfsDecode(String[] tokens, int[] index) {
        int val = Integer.parseInt(tokens[index[0]++]);   // read node value
        int childCount = Integer.parseInt(tokens[index[0]++]); // read child count
        Node node = new Node(val);
        node.children = new ArrayList<>();
        for (int i = 0; i < childCount; ++i) {
            node.children.add(dfsDecode(tokens, index));
        }
        return node;
    }
}
```

> **Complexity**  
> *Time*: `O(N)` – every node is visited once during both serialize & deserialize.  
> *Space*: `O(N)` – output string length plus recursion stack.  

---

### 3.2 Python Solution  

```python
# Node definition (LeetCode format)
class Node:
    def __init__(self, val=None, children=None):
        self.val = val
        self.children = children or []

class Codec:
    """Encodes an N‑ary tree to a string and decodes it back."""

    def serialize(self, root: 'Node') -> str:
        if not root:
            return ''
        parts = []
        def dfs(node):
            parts.append(str(node.val))
            parts.append(str(len(node.children)))
            for child in node.children:
                dfs(child)
        dfs(root)
        return ' '.join(parts)

    def deserialize(self, data: str) -> 'Node':
        if not data:
            return None
        tokens = data.split()
        idx = 0
        def dfs():
            nonlocal idx
            val = int(tokens[idx]); idx += 1
            child_cnt = int(tokens[idx]); idx += 1
            node = Node(val)
            node.children = [dfs() for _ in range(child_cnt)]
            return node
        return dfs()
```

> **Complexity** – identical to the Java solution.

---

### 3.3 C++ Solution  

```cpp
#include <bits/stdc++.h>
using namespace std;

// LeetCode Node definition
struct Node {
    int val;
    vector<Node*> children;
    Node(int _val) : val(_val) {}
    Node(int _val, const vector<Node*>& _children) : val(_val), children(_children) {}
};

class Codec {
public:
    // Serialize the N‑ary tree to a string
    string serialize(Node* root) {
        if (!root) return "";
        stringstream ss;
        dfsEncode(root, ss);
        return ss.str();          // contains trailing space – harmless
    }

    // Deserialize the string back to an N‑ary tree
    Node* deserialize(const string& data) {
        if (data.empty()) return nullptr;
        stringstream ss(data);
        return dfsDecode(ss);
    }

private:
    void dfsEncode(Node* node, stringstream &ss) {
        ss << node->val << ' ' << node->children.size() << ' ';
        for (Node* child : node->children)
            dfsEncode(child, ss);
    }

    Node* dfsDecode(stringstream &ss) {
        int val, childCnt;
        ss >> val >> childCnt;
        Node* node = new Node(val);
        node->children.resize(childCnt);
        for (int i = 0; i < childCnt; ++i)
            node->children[i] = dfsDecode(ss);
        return node;
    }
};
```

> **Complexity** – same `O(N)` time & space characteristics.

---

## 📚 Blog Article – “Serialize & Deserialize N‑ary Tree: The Complete Guide”

---

### **Title (SEO‑Optimized)**  
> **LeetCode 428 – Serialize & Deserialize N‑ary Tree | Step‑by‑Step Solution & Interview Tips**

---

### **Meta Description**  
> “Want to ace LeetCode’s hard problem #428? Learn how to serialize and deserialize an N‑ary tree, understand DFS vs BFS patterns, and get job‑ready interview insights.”

---

### **Table of Contents**

1. [Problem Snapshot](#problem-snapshot)  
2. [Why It Matters](#why-it-matters)  
3. [Key Constraints & Edge Cases](#key-constraints)  
4. [Solution Overview](#solution-overview)  
5. [DFS + Child Count – The Classic Pattern](#dfs-child-count)  
6. [BFS + Null Sentinel – An Alternative](#bfs-sentinel)  
7. [Code Walk‑Through (Java / Python / C++)](#code-walk-through)  
8. [Complexity & Pitfalls](#complexity-pitfalls)  
9. [Interview‑Ready Tips](#interview-tips)  
10. [Final Thoughts & Next Steps](#final-thoughts)

---

### **1️⃣ Problem Snapshot** <a name="problem-snapshot"></a>

> **LeetCode #428 – Serialize and Deserialize N‑ary Tree**  
> Difficulty: **Hard**  
> Time limit: 1 s | Memory limit: 512 MB  

> *Given a reference to the root of an N‑ary tree, write two functions that:*

| Function | Purpose |
|---|---|
| `serialize(root)` | Turn the entire tree into a string. |
| `deserialize(data)` | Recreate the original tree from that string. |

> *The output of `deserialize(serialize(root))` must be **bit‑for‑bit identical** to the original tree.*

---

### **2️⃣ Why It Matters** <a name="why-it-matters"></a>

- **Core Data‑Structure Skill** – Trees are ubiquitous (file systems, JSON parsing, organization charts).  
- **Interview Gold‑mine** – Many senior interviews ask for “serialize/deserialize” to test recursion, traversal, and state management.  
- **Real‑World Relevance** – Graph databases and distributed systems need efficient serialization for network transport or persistence.

---

### **3️⃣ Key Constraints & Edge Cases** <a name="key-constraints"></a>

| Edge Case | What to Watch For |
|---|---|
| Empty tree (`root == null`) | Return an empty string. |
| Single node tree | Should serialize as `value 0 ` (0 children). |
| Deep tree (depth = 10⁴) | Recursion depth → consider tail‑recursion or iterative BFS if language stack limits are a concern. |
| Large node values (`≤ 10⁹`) | Avoid single‑character delimiters that might clash with digits. Use spaces or a non‑numeric separator. |

> *Remember: No global/static variables – each call must be self‑contained.*

---

### **3️⃣ Solution Overview** <a name="solution-overview"></a>

> **Two clean patterns** – DFS (recursive) and BFS (iterative).  

> **DFS + Child Count**  
> - Encode as: `nodeVal childCnt child1 … childK` recursively.  
> - Very easy to parse using an index pointer.  

> **BFS + Null Sentinel**  
> - Encode level‑by‑level, inserting a sentinel (`null`) to mark the end of a child list.  
> - Iterative, no index pointer needed, but you have to “know” when each sub‑list ends.  

> Both patterns achieve linear time and space.

---

### **4️⃣ Solution Overview** <a name="solution-overview"></a>

> **DFS + Child Count** is the **most common** and recommended due to:

1. **Minimal formatting** – no special delimiter, just spaces.  
2. **Deterministic parsing** – you always read a value, then the number of children.  
3. **Compact** – the string length is roughly `2 * number_of_nodes + sum_of_child_counts`.

---

### **5️⃣ DFS + Child Count – The Classic Pattern** <a name="dfs-child-count"></a>

#### Algorithm

1. **Serialize (DFS)**
   - Visit node.
   - Append `node.val` and `node.children.size()` separated by a space.
   - Recurse on each child.
2. **Deserialize (DFS)**
   - Split the string by spaces → array `tokens`.  
   - Maintain a mutable index (`int[] idx` or a Python list).  
   - Read `tokens[idx++]` → `val`.  
   - Read next token → `childCount`.  
   - Create node with `val`.  
   - Recurse `childCount` times to build children.  
   - Return node.

> **Why it works** – The format is *prefix* (node first) and every node explicitly declares how many children follow, so the parser never mis‑identifies boundaries.

---

### **6️⃣ BFS + Null Sentinel – An Alternative** <a name="bfs-sentinel"></a>

If you prefer an **iterative** solution, you can encode level‑by‑level, inserting a `null` sentinel after each child list.

```text
[ rootVal, rootChildCnt, child1, child2, …, null, … ]
```

> - Each `null` tells the deserializer that the current child list has finished.  
> - Still linear time, but you need to keep a queue and track sentinel positions.  
> - Slightly more verbose than DFS + child‑count.

---

### **7️⃣ Code Walk‑Through** <a name="code-walk-through"></a>

We’ve provided **three full‑featured implementations** earlier (Java, Python, C++).  
Key points to highlight during a presentation:

* Use **spaces** as delimiters – they survive `split`/`istringstream` parsing.  
* Always handle **empty trees** early (return or parse an empty string).  
* Recursion depth is naturally bounded by tree height; for very deep trees consider a tail‑recursive version or BFS.

---

### **8️⃣ Complexity & Pitfalls** <a name="complexity-pitfalls"></a>

| Operation | Time | Space | Common Mistakes |
|---|---|---|---|
| `serialize` | `O(N)` | `O(N)` string | Forgetting a space after child count → parser mis‑aligned. |
| `deserialize` | `O(N)` | `O(N)` recursion stack | Using global variables (violates statelessness). |
| Handling large `val` | `O(1)` per node | `O(1)` | Overflow if you store as `char`. Use string to avoid. |

> **Pitfall 1** – **Index Out‑of‑Bounds** – Always verify that the token array has enough items before accessing.  
> **Pitfall 2** – **Trailing Spaces** – Some languages treat them as empty tokens; be consistent.  
> **Pitfall 3** – **Recursive Stack Overflow** – For extremely skewed trees (depth = 10⁴) you might hit stack limits in Java/Python; consider increasing stack size or using BFS.

---

### **9️⃣ Interview‑Ready Tips** <a name="interview-tips"></a>

1. **Explain Your Format Up Front** – “I’m going to use DFS with child count because…”.
2. **Show the Edge Cases** – Empty tree, single node, large values.  
3. **Discuss Complexity** – Linear time & space; justify recursion stack.  
4. **Mention No Global State** – “Each call uses only local variables / recursion stack.”  
5. **Optional: Add Unit Tests** – Test round‑trip serialization/deserialization quickly.

---

### **10️⃣ Final Thoughts & Next Steps** <a name="final-thoughts"></a>

* Mastering LeetCode #428 equips you with a **versatile tree traversal technique** and showcases your ability to **encode state** in a compact string.  
* Practice by extending the solution to **N‑ary graphs** or **custom serialization formats**.  
* Add unit tests in your preferred language to simulate real‑world scenarios.  
* Keep exploring LeetCode’s *Design* problems (e.g., `Serialize/Deserialize Binary Search Tree`, `Design Twitter`, etc.) – the same patterns apply.

---

> **Happy Coding** and **Good Luck** on your next algorithm interview! 🚀

---

### **Call to Action**  

*If you liked this post, hit the **Subscribe** button, comment below with your own solutions or questions, and share on LinkedIn or Twitter with the hashtag `#LeetCode428`.*

---

### 🛠️ Next Step Resources

1. LeetCode Problem Set: “Binary Tree – Serialization”  
2. “Recursive vs Iterative Tree Traversal” – Coursera / Udacity.  
3. “Graph Serialization” – G4G and Hackerrank challenges.

---

> **End of Article**

---

### 📦 4️⃣ Final Wrap‑Up  

You now have:

1. **Fully functional, stateless code** in three languages.  
2. A **concise, interview‑ready explanation** of why the problem matters.  
3. A **complete blog post** that you can publish on Medium, dev.to, or your personal portfolio site to demonstrate mastery.

Good luck crushing LeetCode 428 and impressing the hiring managers! 🚀