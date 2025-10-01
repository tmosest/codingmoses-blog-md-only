---
title: LeetCode 1166. Design File System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Design a File System (LeetCode 1166) – Java, Python & C++ – The Good, The Bad & The Ugly

> **Target Keywords** – *Design File System Leetcode 1166*, *Java solution*, *Python solution*, *C++ solution*, *Trie vs HashMap*, *interview problem*, *file system design interview*, *optimal file system implementation*  

---

## 1. Problem Recap

> **LeetCode 1166 – Design File System**  
> You’re asked to implement a simple file‑system that supports two operations:
> 
> 1. **`createPath(path, value)`** – create a new absolute path and associate an integer value. Return `true` if the path is created; return `false` if the path already exists or its parent doesn’t exist.  
> 2. **`get(path)`** – return the value associated with `path` or `-1` if the path is missing.  
>  
> A path looks like `"/a/b/c"`. No root `/` alone, no empty string, all letters are lowercase.

Constraints are generous – up to **10⁴ calls**, path length ≤ 100, value up to 10⁹.  
The classic interview question asks you to design a data structure that works in *O(L)* time per operation, where *L* is the number of segments in the path.

---

## 2. High‑Level Design Ideas

| Approach | Core Idea | Strength | Weakness |
|----------|-----------|----------|----------|
| **HashMap of full path → value** | Store the entire path as a key; validate parent existence by checking the map. | Extremely simple, O(1) lookup. | Requires *O(L)* string processing per call; cannot easily support prefix‑based operations (not needed here). |
| **Trie (prefix tree)** | Each node represents a directory segment; edges are children maps. | Natural hierarchical representation; O(L) per operation, low overhead for deep paths. | Slightly more code, recursion or stack needed. |
| **Hybrid** | Use a HashMap for parent checks, but keep a trie for structural clarity. | Combines clarity of trie with speed of hash lookup. | Over‑engineering for this problem. |

For interview‑style coding tests, a **hash‑based** solution is usually accepted and is the quickest to write. A **trie** solution demonstrates deeper data‑structure knowledge and is often praised in technical interviews.

---

## 3. “Good” – A Minimal HashMap Solution

```java
public class FileSystem {
    private final Map<String, Integer> mp = new HashMap<>();

    public FileSystem() {
        // Root is implicit; no need to store "/".
    }

    public boolean createPath(String path, int value) {
        if (mp.containsKey(path)) return false;   // already exists
        // Parent must exist: everything before the last '/'.
        int lastSlash = path.lastIndexOf('/');
        if (lastSlash == 0) {                     // parent is root "/"
            mp.put(path, value);
            return true;
        }
        String parent = path.substring(0, lastSlash);
        return mp.containsKey(parent) && mp.put(path, value) == null;
    }

    public int get(String path) {
        return mp.getOrDefault(path, -1);
    }
}
```

**Why it’s good**

* **Fast** – constant‑time map lookups; no recursion.  
* **Lean** – only one data structure.  
* **Idiomatic** – uses Java’s built‑in `HashMap`.

**Why it’s not ideal**

* You’re doing string manipulation on every call (`substring`, `lastIndexOf`).  
* It’s not a true hierarchical model; if the problem extended to “list children”, you’d need a different structure.

---

## 4. “Bad” – The Straight‑Forward Trie (Python)

```python
class TrieNode:
    def __init__(self, value=-1):
        self.value = value
        self.children = {}

class FileSystem:
    def __init__(self):
        self.root = TrieNode()

    def createPath(self, path: str, value: int) -> bool:
        parts = path.split('/')[1:]   # skip leading empty string
        node = self.root
        for i, part in enumerate(parts):
            if part not in node.children:
                if i == len(parts) - 1:
                    node.children[part] = TrieNode(value)
                    return True
                else:
                    # parent missing
                    return False
            else:
                node = node.children[part]
                if i == len(parts) - 1:
                    # already exists
                    return False
        return False

    def get(self, path: str) -> int:
        parts = path.split('/')[1:]
        node = self.root
        for part in parts:
            if part not in node.children:
                return -1
            node = node.children[part]
        return node.value
```

**Why it’s bad**

* **Verbose** – more code than the hash‑map solution.  
* **Edge‑case handling** – need to split strings, manage indices.  
* **Recursion / Stack** – Python’s recursion depth may hit limits for deep paths (though depth ≤ 100 in constraints).  

---

## 5. “Ugly” – A Recursive Java Trie (the one you pasted)

> The code you shared is a good starting point but can be tightened:

```java
class FileSystem {
    private final Node root = new Node("");

    public boolean createPath(String path, int val) {
        return create(root, path, val);
    }

    public int get(String path) {
        Node n = get(root, path);
        return n == null ? -1 : n.val;
    }

    private boolean create(Node cur, String path, int val) {
        int slash = path.indexOf('/', 1);
        if (slash == -1) {                     // last component
            if (cur.children.containsKey(path.substring(1))) return false;
            cur.children.put(path.substring(1), new Node(val));
            return true;
        }
        String childName = path.substring(1, slash);
        Node child = cur.children.get(childName);
        if (child == null) return false;
        return create(child, path.substring(slash), val);
    }

    private Node get(Node cur, String path) {
        int slash = path.indexOf('/', 1);
        if (slash == -1) {
            return cur.children.get(path.substring(1));
        }
        Node child = cur.children.get(path.substring(1, slash));
        return child == null ? null : get(child, path.substring(slash));
    }

    private static class Node {
        int val = -1;
        Map<String, Node> children = new HashMap<>();
        Node() {}
        Node(int v) { val = v; }
    }
}
```

**Ugly aspects**

* Repeated string slicing (`substring`) in recursion.  
* Recursive depth can become problematic if path depth were much larger.  
* Slightly confusing parameter names and missing `final` keyword.

---

## 6. Optimal Solution – Hybrid Trie + HashMap (C++)

```cpp
class FileSystem {
public:
    FileSystem() : root(new Node) {}

    bool createPath(string path, int value) {
        if (values.count(path)) return false;      // already exists
        size_t pos = path.find_last_of('/');
        string parent = (pos == 0) ? "/" : path.substr(0, pos);
        if (!values.count(parent)) return false;   // parent missing
        values[path] = value;
        auto parts = split(path);
        Node* cur = root;
        for (auto &p : parts) cur = cur->next[p];
        cur->value = value;
        return true;
    }

    int get(string path) {
        return values.count(path) ? values[path] : -1;
    }

private:
    struct Node {
        int value = -1;
        unordered_map<string, Node*> next;
    };

    Node* root;
    unordered_map<string, int> values;          // quick existence check

    vector<string> split(const string& s) {
        vector<string> parts;
        size_t i = 1;                           // skip leading '/'
        while (i < s.size()) {
            size_t j = s.find('/', i);
            parts.push_back(s.substr(i, j - i));
            i = j + 1;
        }
        return parts;
    }
};
```

**Why this is the “best” for an interview**

* **Speed** – `values` gives O(1) parent check, `unordered_map` in each node gives O(1) child lookup.  
* **Safety** – No deep recursion, all loops.  
* **Clarity** – Separates *path existence* from *hierarchy*.

---

## 7. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| HashMap only | `O(L)` (string ops) | `O(M)` – *M* distinct paths |
| Trie only | `O(L)` | `O(M * avgChildren)` – proportional to number of nodes |
| Hybrid | `O(L)` | `O(M)` + small trie overhead |

`L ≤ 100` and `10⁴` calls mean *worst‑case* 1 million segment visits – far below any time limit.

---

## 8. Corner‑Case Testing (All Languages)

```text
1. createPath("/a", 1)      → true      (root is implicit)
2. get("/a")                → 1
3. createPath("/a/b", 2)    → true      (parent "/a" exists)
4. createPath("/a", 3)      → false     (already exists)
5. createPath("/c/d", 4)    → false     (parent "/c" missing)
6. get("/a/b")              → 2
7. get("/a/c")              → -1
```

Running these tests against any implementation guarantees correctness on the *most common* cases.

---

## 8. Interview Tips

| Topic | What the interviewer expects | How to show mastery |
|-------|-----------------------------|---------------------|
| **Data‑structure choice** | A concise hash‑map solution is accepted, but a trie is a *strong signal* that you can design hierarchical systems. | Explain that a trie is a natural fit for file systems and give a quick sketch before coding. |
| **Edge‑case handling** | No empty paths, root handling, parent‑existence check. | Explicitly handle `path.lastIndexOf('/') == 0` (root parent). |
| **Time‑space trade‑offs** | O(L) time is required; space must grow linearly with the number of directories. | Discuss memory usage: one node per directory segment, value only stored at leaf. |
| **Coding style** | Clean, readable, with meaningful variable names. | Avoid deep recursion unless you’re certain the depth is small; prefer loops. |

> **Pro Tip**: Start with the hash‑map skeleton, add the parent check, then *refactor* into a trie if time permits. Interviewers love “start simple, then iterate”.

---

## 8. What to Say in the Interview

> “Here’s a quick solution using a hash map that meets the problem constraints. If we needed to support more advanced operations, we could switch to a trie for a natural hierarchical representation.”

If the interviewer asks for the trie implementation, explain:

> “The trie stores each directory segment. For `createPath`, we traverse the tree to validate the parent. For `get`, we just walk the tree and return the stored value.”  

Be ready to answer:  

* “What about listing all sub‑folders?” → Show how to traverse the trie or use the `values` map.  
* “What if the number of paths grows to millions?” → Discuss space optimization, compression, or using a prefix hash.

---

## 8. Final Thoughts

* **LeetCode 1166 is a *design* problem, not just a string trick.**  
* A *hash‑map* solution is acceptable in most coding tests; a *trie* showcases deeper understanding.  
* The **hybrid** approach (C++) is a sweet spot for interviews: O(1) checks, O(L) traversal, no recursion.  

When you come across a “Design File System” interview, start with the cleanest code you can write. If you’re comfortable, mention a trie; if you’re tight on time, a hash‑map suffices. Either way, make sure your code:

* Validates the parent path correctly.  
* Handles the special case of the root directory.  
* Works in *O(L)* time per operation.  

---

### Want to practice?

1. **Implement in your preferred language** – write it yourself, then compare with the snippets above.  
2. **Add a `listChildren(path)`** method to see how the trie shines.  
3. **Run a stress test** – 10 k operations, random deep paths, and verify performance.

Good luck, and happy coding!