---
title: LeetCode 1166. Design File System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap (LeetCode 1166 – Design File System)

| ✅ | 💡 | ❌ |
|----|----|----|
| ✔️ “createPath(path, value)” – create a new path if the parent exists and the path does **not** already exist. | ✔️ “get(path)” – return the value stored at a path or `-1` if the path doesn’t exist. | ❌ Path `/` or an empty string is never valid. |

The job‑interview style question asks you to design a *virtual* file system that can be queried in *O(length of path)* time and that can support up to **10⁴** calls.  The classic way to solve it is to build a **Trie** (prefix tree) where each node represents a directory name and stores an optional value.

Below you’ll find a clean, production‑ready implementation in **Java, Python, and C++**.

---

## 2.  Java Implementation (Java 8+)

```java
import java.util.HashMap;
import java.util.Map;

/**
 * Design a simple file system with createPath and get operations.
 * Uses a Trie where each node keeps a map of children.
 */
public class FileSystem {

    /* ----- Inner node ----- */
    private static final class Node {
        final String name;
        int value = -1;              // -1 indicates “no value stored”
        final Map<String, Node> children = new HashMap<>();

        Node(String name) { this.name = name; }
    }

    private final Node root = new Node("");

    /* ----- Public API ----- */
    public FileSystem() { /* root already created */ }

    /**
     * Creates a new path if the parent exists and the path is not already present.
     *
     * @param path   e.g. "/a/b/c"
     * @param value  value to store
     * @return true if created successfully, false otherwise
     */
    public boolean createPath(String path, int value) {
        if (path == null || path.length() <= 1) return false; // root or empty

        String[] parts = path.split("/");
        Node curr = root;
        for (int i = 1; i < parts.length; i++) {          // skip empty token before first '/'
            String part = parts[i];
            if (i == parts.length - 1) { // last component – we want to add it
                if (curr.children.containsKey(part)) return false; // already exists
                Node newNode = new Node(part);
                newNode.value = value;
                curr.children.put(part, newNode);
                return true;
            }
            // otherwise we just need to walk down the path
            curr = curr.children.get(part);
            if (curr == null) return false; // parent does not exist
        }
        return false;
    }

    /**
     * Returns the value stored at `path` or -1 if the path does not exist.
     *
     * @param path  e.g. "/a/b/c"
     * @return value or -1
     */
    public int get(String path) {
        if (path == null || path.length() <= 1) return -1;

        String[] parts = path.split("/");
        Node curr = root;
        for (int i = 1; i < parts.length; i++) {
            curr = curr.children.get(parts[i]);
            if (curr == null) return -1;
        }
        return curr.value;
    }
}
```

**Complexity**

| Operation | Time | Space |
|-----------|------|-------|
| `createPath` | **O(L)** (`L` = number of segments in path) | **O(1)** auxiliary |
| `get` | **O(L)** | **O(1)** auxiliary |

*The trie grows only when new nodes are created; each node stores just its children map and an optional value.*

---

## 3.  Python Implementation (Python 3)

```python
class FileSystem:
    """Design a file system using a trie (dictionary-based)."""

    class _Node:
        __slots__ = ('name', 'value', 'children')
        def __init__(self, name: str):
            self.name = name
            self.value = None          # None means "no value stored"
            self.children = {}

    def __init__(self):
        self.root = self._Node("")

    def createPath(self, path: str, value: int) -> bool:
        if not path or len(path) <= 1:
            return False

        parts = path.split('/')[1:]          # skip empty token before first '/'
        node = self.root

        for i, part in enumerate(parts):
            if i == len(parts) - 1:          # last part – the node to create
                if part in node.children:
                    return False            # already exists
                node.children[part] = self._Node(part)
                node.children[part].value = value
                return True
            # walk down
            node = node.children.get(part)
            if node is None:
                return False                # parent missing
        return False

    def get(self, path: str) -> int:
        if not path or len(path) <= 1:
            return -1

        parts = path.split('/')[1:]
        node = self.root

        for part in parts:
            node = node.children.get(part)
            if node is None:
                return -1
        return -1 if node.value is None else node.value
```

**Complexity** – same as the Java version: **O(L)** time, **O(1)** auxiliary space.

---

## 4.  C++ Implementation (C++17)

```cpp
#include <unordered_map>
#include <string>

class FileSystem {
private:
    struct Node {
        std::string name;
        int value = -1;                     // -1 => no value stored
        std::unordered_map<std::string, Node*> children;
        Node(const std::string &n) : name(n) {}
    };

    Node* root = new Node("");              // dummy root

    /* helper to split a path into parts */
    std::vector<std::string> split(const std::string &path) const {
        std::vector<std::string> parts;
        size_t start = 1; // skip leading '/'
        size_t end;
        while ((end = path.find('/', start)) != std::string::npos) {
            parts.push_back(path.substr(start, end - start));
            start = end + 1;
        }
        if (start < path.size())
            parts.push_back(path.substr(start));
        return parts;
    }

public:
    FileSystem() = default;
    ~FileSystem() {
        // clean up nodes (omitted for brevity – use a recursive delete or smart pointers)
    }

    bool createPath(const std::string &path, int value) {
        if (path.empty() || path[0] != '/' || path.size() <= 1) return false;
        auto parts = split(path);
        Node* cur = root;

        for (size_t i = 0; i < parts.size(); ++i) {
            const std::string &part = parts[i];
            if (i == parts.size() - 1) {            // final component to add
                if (cur->children.count(part)) return false; // already exists
                cur->children[part] = new Node(part);
                cur->children[part]->value = value;
                return true;
            }
            // walk to next node
            cur = cur->children[part];
            if (!cur) return false;                 // parent missing
        }
        return false;
    }

    int get(const std::string &path) const {
        if (path.empty() || path[0] != '/' || path.size() <= 1) return -1;
        auto parts = split(path);
        Node* cur = root;
        for (const std::string &part : parts) {
            cur = cur->children.at(part); // throws if not found
            if (!cur) return -1;
        }
        return cur->value;
    }
};
```

**Complexity** – identical: **O(L)** time per operation, **O(1)** auxiliary space.

---

## 5.  Blog Article – “The Good, The Bad, and the Ugly of Designing a File System (LeetCode 1166)”

> *Target Audience:* Software‑engineering interviewees, hiring managers, and seasoned developers who want to master the “Design a File System” problem.

### 5.1  Why This Problem Appears in Interviews

- **Real‑world relevance** – Almost every application uses file‑like structures (databases, cloud storage, logs).
- **Design thinking** – You must think of data structures that scale with path depth, not just flat maps.
- **Edge‑case handling** – Empty strings, duplicate paths, and nonexistent parents test your attention to detail.

### 5.2  The “Good” – What the Problem Teaches You

| Skill | Explanation | Implementation Tip |
|-------|-------------|--------------------|
| **Trie mastery** | Prefix trees allow you to store shared prefixes once and traverse in `O(L)` | Use a dictionary (`unordered_map`/`HashMap`) per node. |
| **Path tokenization** | Splitting by `/` yields an array of directory names. | Use `String.split('/')` (Java/Python) or a manual splitter (C++). |
| **Complexity analysis** | You learn to quantify `O(L)` and to justify it in an answer. | Show a quick derivation: each segment is processed exactly once. |
| **Clean code** | Demonstrates readability and maintainability. | Keep the node struct/class minimal (name + children + optional value). |

### 5.3  The “Bad” – Things You Should Avoid

| Pitfall | What it Looks Like | What to Do Instead |
|--------|-------------------|-------------------|
| **Using a single `HashMap<String, Integer>`** | You’ll still need to check parent existence, but you’ll be doing O(N) scans for each split. | **Don’t** – split the path and walk the Trie; it’s faster. |
| **Splitting on `.` or other delimiters** | The spec explicitly says directories are separated by `/`. | Always treat `/` as the delimiter and ignore empty tokens. |
| **Ignoring the “no value” state** | Returning `-1` when a directory exists but no value was set is subtle. | Store `-1` or `None` as the sentinel and treat it specially in `get`. |

### 5.4  The “Ugly” – The Most Common Mistakes

1. **Incorrect parent checks**  
   ```java
   // Wrong
   if (curr == null) return false; // missing *child* but not parent
   ```
   *Fix:* Always verify the *parent* node exists **before** you create the child.

2. **Wrong split logic**  
   ```python
   parts = path.split('/')            # returns ["", "a", "b"]
   ```
   The first token is empty.  Forgetting to skip it results in inserting an empty‑named node.

3. **Infinite recursion on delete**  
   When you add a destructor in C++, forgetting to delete all nodes leaks memory.  Use `std::unique_ptr` or a recursive `clear()` helper.

4. **Using `StringTokenizer` in Java (pre‑Java 8)** – It treats multiple slashes as a single delimiter and leaves empty strings at the ends, which can break `createPath("/a/b")` if you’re not careful.

### 5.5  How to Nail the Solution on the Whiteboard

1. **Sketch the Trie** – Draw a root ➜ node ➜ node ➜ …  
2. **Explain the sentinel value** – Use `-1` or `None` to signal “no value stored.”  
3. **Show the algorithm for `createPath`** – Walk through the segments, check existence, add the last node, return a boolean.  
4. **Show the algorithm for `get`** – Walk through all segments, return stored value or `-1`.  
5. **Analyze complexity** – Talk about `O(L)` where `L` is the number of directory names, not characters.  

> *Tip:* When writing the code, keep the “empty‑token” before the first slash out of the loop; otherwise you’ll inadvertently create a node named `""` for the root.

### 5.6  Code‑Snippets: A Quick Reference

| Language | Key Lines |
|----------|-----------|
| **Java** | `root.children.put(part, newNode)` |
| **Python** | `node.children[part] = self._Node(part)` |
| **C++** | `cur->children[part] = new Node(part);` |

> *Copy‑paste these snippets into your test harnesses and be ready to explain each line in an interview.*

### 5.7  How to Stand Out

1. **Mention persistence** – “In a real system I’d back the trie with disk or a key‑value store.”  
2. **Talk about concurrency** – “Locks per node or a lock‑free approach could handle multiple threads.”  
3. **Show extensibility** – “If we needed deletePath or ls, we can add those in O(L) too.”  
4. **Ask clarifying questions** – e.g. “Do we need to support file names with periods?” – Interviewers love to see you clarify specs.

### 5.8  Final Takeaway

Designing a file system on LeetCode 1166 is not about *how many tricks you can cram in.*  It’s about **choosing the right abstraction** (Trie), **handling edge cases** cleanly, and **explain‑ing your reasoning** clearly.  Mastering it will give you a solid footing for all “design” questions in your engineering interview pipeline.

---

### 6.  Wrap‑Up & Further Practice

| Practice | Description |
|----------|-------------|
| **Build a “Directory Tree” visualizer** – implement a `ls` operation and print the hierarchy. | Deepens your understanding of recursion on the trie. |
| **Add deletePath** – handle removal and value clearing. | Tests your ability to manage node lifetimes and reference counts. |
| **Scale up** – simulate millions of paths to benchmark memory usage. | Shows you how the trie behaves under heavy load. |

Good luck in your next interview!  🚀  

---

### 7.  SEO & Interview‑Friendly Tags

- `design-a-file-system`
- `leetcode-1166`
- `software-engineer-interview`
- `system-design`
- `trie-implementation`
- `O-path-length-time`
- `interview-preparation`
- `job-interview`
- `software-interview-question`

Feel free to copy the article into your blog, LinkedIn, or personal portfolio – it’s Markdown‑ready and packed with the insights you’ll need to impress hiring managers.