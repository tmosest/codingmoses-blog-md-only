---
title: LeetCode 1166. Design File System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview – 1166. Design File System

> **LeetCode ID**: 1166  
> **Difficulty**: Medium  
> **Tag**: Design, Hash Table, Trie

You are asked to implement a miniature file‑system that supports two operations:

| Method | Signature | Behaviour |
|--------|-----------|-----------|
| `createPath(path, value)` | `boolean` | Adds a new path and attaches an integer value to it. Returns **false** if the path already exists or if its parent path does not exist. |
| `get(path)` | `int` | Returns the value associated with *path*, or **-1** if the path does not exist. |

*Path format* – a non‑empty string that starts with `/` and contains only lowercase English letters. Examples: `/a`, `/leetcode/problems`. The root `/` itself is **not** a valid path.

**Constraints**

- `2 <= path.length <= 100`
- `1 <= value <= 10⁹`
- ≤ 10⁴ calls to `createPath`/`get` in total

The goal is to build an efficient, interview‑ready implementation that can be presented in Java, Python, or C++.

---

## 2.  High‑Level Design Choices

| Approach | Strength | Weakness | Use‑case |
|----------|----------|----------|----------|
| **Hash‑Map based tree** | Simple, fast O(length) per operation | Uses more memory than a flat hash‑table | Common interview choice |
| **Flat Hash‑Table** (path → value) | O(1) average lookup | Needs extra logic to check parent existence | Useful for very large numbers of independent paths |
| **Trie** | Memory‑efficient for common prefixes | Slightly more complex code | Good when many paths share prefixes (e.g., `/a/b/c`, `/a/b/d`) |

The most interview‑friendly solution is a *hash‑map based tree* (sometimes called a **Trie‑like** structure). It keeps a root node and a mapping of child names → child nodes. Each operation only walks down the path components, giving `O(L)` time where `L` is the number of components (≤ 100). Memory usage is `O(N)` where `N` is the total number of nodes created.

---

## 3.  Reference Implementations

Below are fully‑working, ready‑to‑paste solutions in **Java**, **Python**, and **C++**. Each follows the same tree‑based design.

### 3.1 Java – Interview‑Ready

```java
import java.util.HashMap;
import java.util.Map;

public class FileSystem {

    /* ----------  Node definition ---------- */
    private static class Node {
        String name;                     // component name
        int value;                       // value stored at this node
        Map<String, Node> children;      // children by component name

        Node(String name) {
            this.name = name;
            this.value = 0;              // 0 denotes no value yet
            this.children = new HashMap<>();
        }
    }

    /* ----------  FileSystem API ---------- */
    private final Node root;

    public FileSystem() {
        root = new Node("");            // root represents "/"
    }

    public boolean createPath(String path, int value) {
        if (path == null || path.isEmpty() || path.equals("/")) return false;

        String[] parts = path.split("/");   // split on '/', first part empty
        Node cur = root;

        /* walk to the parent of the new path */
        for (int i = 1; i < parts.length - 1; i++) {
            String part = parts[i];
            cur = cur.children.get(part);
            if (cur == null) return false;  // parent does not exist
        }

        String last = parts[parts.length - 1];
        if (cur.children.containsKey(last)) return false; // already exists

        Node newNode = new Node(last);
        newNode.value = value;
        cur.children.put(last, newNode);
        return true;
    }

    public int get(String path) {
        if (path == null || path.isEmpty() || path.equals("/")) return -1;

        String[] parts = path.split("/");
        Node cur = root;

        for (int i = 1; i < parts.length; i++) {
            String part = parts[i];
            cur = cur.children.get(part);
            if (cur == null) return -1;
        }
        return cur.value;
    }
}
```

> **Why this is interview‑friendly?**  
> *Clear separation of concerns*: a lightweight `Node` class, straightforward `createPath` and `get`. The logic is easy to trace in a live coding interview.

---

### 3.2 Python – Concise & Readable

```python
class FileSystem:
    class _Node:
        __slots__ = ("name", "value", "children")

        def __init__(self, name: str):
            self.name = name
            self.value = 0          # 0 means "no value yet"
            self.children = {}

    def __init__(self):
        self.root = self._Node("")  # root represents "/"

    def createPath(self, path: str, value: int) -> bool:
        if not path or path == "/":
            return False

        parts = path.split("/")[1:]        # ignore leading empty part
        cur = self.root

        for part in parts[:-1]:            # walk to parent
            if part not in cur.children:
                return False
            cur = cur.children[part]

        last = parts[-1]
        if last in cur.children:           # already exists
            return False

        cur.children[last] = self._Node(last)
        cur.children[last].value = value
        return True

    def get(self, path: str) -> int:
        if not path or path == "/":
            return -1

        parts = path.split("/")[1:]
        cur = self.root

        for part in parts:
            cur = cur.children.get(part)
            if cur is None:
                return -1
        return cur.value
```

> **Pythonic Touches**  
> *`__slots__`* keeps node objects lean.  
> *Splitting on `/`* gives an easy way to iterate components.

---

### 3.3 C++ – Fast & Modern

```cpp
#include <unordered_map>
#include <string>
#include <vector>

class FileSystem {
private:
    struct Node {
        int value{0};                       // 0 => no value yet
        std::unordered_map<std::string, Node*> children;
    };

    Node* root = new Node();                // root represents "/"

    // Utility: split a path into components, skipping empty ones
    static std::vector<std::string> split(const std::string& path) {
        std::vector<std::string> parts;
        size_t start = 1; // skip leading '/'
        for (size_t i = 1; i <= path.size(); ++i) {
            if (i == path.size() || path[i] == '/') {
                if (i > start) parts.emplace_back(path.substr(start, i - start));
                start = i + 1;
            }
        }
        return parts;
    }

public:
    FileSystem() = default;
    ~FileSystem() { clear(root); }

    bool createPath(const std::string& path, int value) {
        if (path.empty() || path == "/") return false;

        auto parts = split(path);
        Node* cur = root;

        for (size_t i = 0; i + 1 < parts.size(); ++i) { // go to parent
            const auto& comp = parts[i];
            auto it = cur->children.find(comp);
            if (it == cur->children.end()) return false;
            cur = it->second;
        }

        const std::string& last = parts.back();
        if (cur->children.count(last)) return false;

        Node* child = new Node();
        child->value = value;
        cur->children[last] = child;
        return true;
    }

    int get(const std::string& path) const {
        if (path.empty() || path == "/") return -1;

        auto parts = split(path);
        Node* cur = root;

        for (const auto& comp : parts) {
            auto it = cur->children.find(comp);
            if (it == cur->children.end()) return -1;
            cur = it->second;
        }
        return cur->value;
    }

private:
    void clear(Node* node) {
        for (auto& [_, child] : node->children)
            clear(child);
        delete node;
    }
};
```

> **Why C++?**  
> *Explicit memory management* (with `clear`) shows awareness of ownership.  
> *`unordered_map`* gives expected `O(1)` look‑ups.

---

## 4.  Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| `createPath` | `O(L)` – where `L` is the number of components in the path | Adds one node → `O(1)` additional |
| `get` | `O(L)` | None (only reads existing nodes) |

- `L` ≤ 100 (given constraints).  
- Total memory `O(N)` where `N` = total nodes ever created (≤ number of successful `createPath` calls).

Both Java/Python and C++ implementations satisfy the LeetCode constraints easily.

---

## 5.  Interview‑Specific Trade‑Offs

| Topic | What Interviewers Look For | Pitfalls to Avoid |
|-------|----------------------------|-------------------|
| **Edge Cases** | `/`, empty string, duplicate component | Forgetting to skip the first empty component after split |
| **Parent Existence** | Traversal must reach the *parent* node first | Updating the wrong node or adding a child when the parent is missing |
| **Value 0 vs. No Value** | 0 is a valid stored value? | In the spec values are ≥ 1, so 0 can safely mean “no value yet.” |
| **Memory** | Each node holds a map of children | Over‑allocating maps (e.g., creating a new map for each node) can be wasteful |
| **Garbage Collection** | Java/Python: automatic; C++: manual | Failing to delete nodes leads to leaks |

---

## 5.  Common Pitfalls & How to Fix Them

1. **Splitting `/` incorrectly**  
   *Fix*: ignore empty strings or skip the leading `/`.
2. **Assuming the parent exists automatically**  
   *Fix*: walk down the path *until the parent* and return `false` if any component is missing.
3. **Returning the wrong value for “no value yet”**  
   *Fix*: store values only on nodes that are explicitly created, leave other nodes’ values at a sentinel (e.g., 0 or `None`).
4. **Handling root `/`**  
   *Fix*: the problem statement says root is not a valid path – treat it specially or reject it outright.

---

## 5.  “What If” Variants

- **Flat Hash‑Table**  
  ```cpp
  unordered_map<string,int> val;
  unordered_map<string,string> parent;   // parent[path] = "/a/b"
  ```
  - `createPath` must check `parent[path]` exists.  
  - `get` is `O(1)` average.

- **Segment Tree** (overkill for this problem) – good for batch queries on numeric ranges.

---

## 5.  Summary – Take‑Away Messages for Your Next Interview

1. **Tree‑based hash map (Trie‑like) is the gold‑standard**: clean, fast, and easy to explain.
2. **Always validate the parent** before inserting.  
3. **Keep node storage lightweight** – use a single map per node, a value field, and optionally a sentinel for “no value yet.”
4. **Time complexity** is linear in the number of path components (≤ 100), which is well below the LeetCode limit.  
5. **Space usage** is linear in the number of nodes – fine for ≤ 10⁴ nodes.

> **Interviewer‑style tip**: “Can you describe how you would store the data structure?”  
> Answer: “A root node, each node holds a `children` dictionary keyed by the next path component.”  
> Then write the two methods – the rest follows naturally.

---

## 6.  Final Take‑Away – “Design File System” in 3 Languages

- **Java**: Use `HashMap<String, Node>` inside each node; split paths with `split("/")`.  
- **Python**: Same idea, but use `__slots__` to keep nodes lean.  
- **C++**: `unordered_map` + manual `new`/`delete` gives you both speed and ownership clarity.

All three solutions satisfy the LeetCode constraints and can be demonstrated in any coding interview or technical assessment. Pick the language you’re most comfortable with, or present all three to show breadth of knowledge.

> **Remember** – the real interview value is not just the final code but the ability to discuss *why* you chose this design, what trade‑offs you considered, and how you’d test edge cases. Good luck!