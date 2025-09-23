---
title: LeetCode 588. Design In-Memory File System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 588 – Design an In‑Memory File System  
**Hard | 588 | Java | Python | C++**

> Want to land that software‑engineering interview?  
> Mastering this problem shows you can design a data‑structure, work with trees, and keep everything **lexicographically sorted** – exactly what hiring managers love.

---

## 1. Problem Recap (SEO‑friendly)

> **Design an in‑memory file system** – *LeetCode 588*.  
> Create a `FileSystem` class that supports:
> * `ls(path)` – list directory contents or a file name.  
> * `mkdir(path)` – create directories.  
> * `addContentToFile(filePath, content)` – create or append file content.  
> * `readContentFromFile(filePath)` – read file content.

All paths are absolute, start with `/`, and do **not** end with `/` (except the root).  
Names consist of lowercase letters, no duplicates in the same folder.

> **Why it matters:**  
> Interviewers ask this to gauge your ability to:
> * Build a tree‑like structure (files & directories).  
> * Handle string manipulation & path traversal.  
> * Keep the directory listing sorted (lexicographic order).  
> * Think about edge cases and performance.

---

## 2. Design Overview

| Layer | What it does | Why it matters |
|-------|--------------|----------------|
| **Node** | Represents a file or a directory. | Keeps the core data together – content for files, children for dirs. |
| **Tree** | Root `Node` + navigation by path. | Enables O(depth) operations. |
| **Sorting** | Use a sorted map (`TreeMap` in Java, `std::map` in C++, `dict + sorted()` in Python). | Guarantees lexicographic order without extra work. |

---

## 3. Reference Implementations

Below are clean, production‑ready implementations for **Java, Python, and C++**.  
All three follow the same design principles and pass the LeetCode tests.

### 3.1 Java

```java
import java.util.*;

public class FileSystem {

    /** Node represents either a directory or a file */
    private static class Node {
        boolean isFile;
        StringBuilder content = new StringBuilder();          // only for files
        TreeMap<String, Node> children = new TreeMap<>();    // only for directories

        Node() { this.isFile = false; }
    }

    private final Node root;

    public FileSystem() {
        root = new Node();          // root is always a directory
    }

    /** List the contents of a directory or the name of a file */
    public List<String> ls(String path) {
        Node node = traverse(path);
        if (node.isFile) {                      // file: return its own name
            String[] parts = path.split("/");
            return Collections.singletonList(parts[parts.length - 1]);
        }
        return new ArrayList<>(node.children.keySet());
    }

    /** Create directories along the given path */
    public void mkdir(String path) {
        traverse(path);     // the traverse method automatically creates missing nodes
    }

    /** Append content to a file; create file if missing */
    public void addContentToFile(String filePath, String content) {
        Node node = traverse(filePath);
        node.isFile = true;
        node.content.append(content);
    }

    /** Return the full content of a file */
    public String readContentFromFile(String filePath) {
        Node node = traverse(filePath);
        return node.content.toString();
    }

    /** Helper: walk the path, creating nodes as needed */
    private Node traverse(String path) {
        Node cur = root;
        if (path.equals("/")) return cur;
        String[] parts = path.split("/");
        for (int i = 1; i < parts.length; i++) { // skip leading empty string
            cur.children.putIfAbsent(parts[i], new Node());
            cur = cur.children.get(parts[i]);
        }
        return cur;
    }
}
```

> **Key Java‑specific choices**  
> * `TreeMap` → built‑in lexicographic ordering.  
> * `StringBuilder` for efficient string appending.  
> * `putIfAbsent` keeps code tidy.

---

### 3.2 Python

```python
class FileSystem:
    class Node:
        __slots__ = ('is_file', 'content', 'children')
        def __init__(self, is_file=False):
            self.is_file = is_file
            self.content = []          # list of strings, efficient concat
            self.children = {}         # key -> Node

    def __init__(self):
        self.root = self.Node()

    def ls(self, path: str) -> list[str]:
        node = self._traverse(path)
        if node.is_file:
            return [path.split('/')[-1]]
        return sorted(node.children)

    def mkdir(self, path: str) -> None:
        self._traverse(path)            # creates nodes on the fly

    def addContentToFile(self, filePath: str, content: str) -> None:
        node = self._traverse(filePath)
        node.is_file = True
        node.content.append(content)

    def readContentFromFile(self, filePath: str) -> str:
        node = self._traverse(filePath)
        return ''.join(node.content)

    def _traverse(self, path: str) -> 'FileSystem.Node':
        cur = self.root
        if path == '/':
            return cur
        for part in path.strip('/').split('/'):
            if part not in cur.children:
                cur.children[part] = self.Node()
            cur = cur.children[part]
        return cur
```

> **Python tricks**  
> * `__slots__` → smaller memory footprint.  
> * `list` for content → avoids quadratic string concatenation.  
> * `sorted()` at `ls` ensures order.

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class FileSystem {
private:
    struct Node {
        bool isFile = false;
        string content;                   // only used if isFile==true
        map<string, Node*> children;      // lexicographic order by default
        Node() = default;
    };
    Node* root;

    // Helper: walks a path, creating nodes as needed
    Node* traverse(const string& path) {
        Node* cur = root;
        if (path == "/") return cur;
        stringstream ss(path);
        string part;
        getline(ss, part, '/');           // skip leading '/'
        while (getline(ss, part, '/')) {
            if (!cur->children.count(part))
                cur->children[part] = new Node();
            cur = cur->children[part];
        }
        return cur;
    }

public:
    FileSystem() : root(new Node()) {}

    vector<string> ls(string path) {
        Node* node = traverse(path);
        if (node->isFile) {
            string name = path.substr(path.rfind('/') + 1);
            return {name};
        }
        vector<string> res;
        for (const auto& kv : node->children) res.push_back(kv.first);
        return res;
    }

    void mkdir(string path) { traverse(path); }

    void addContentToFile(string filePath, string content) {
        Node* node = traverse(filePath);
        node->isFile = true;
        node->content += content;               // string concatenation is fine here
    }

    string readContentFromFile(string filePath) {
        Node* node = traverse(filePath);
        return node->content;
    }
};
```

> **C++ specifics**  
> * `map` guarantees sorted keys.  
> * `stringstream` → clean path splitting.  
> * `vector<string>` for `ls` results.

---

## 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| `ls`      | **O(depth + k log k)** (`k` = number of items in the dir) | O(k) for result list |
| `mkdir`   | **O(depth)** | O(depth) for new nodes |
| `addContentToFile` | **O(depth)** | O(1) for the node, + O(len(content)) for appending |
| `readContentFromFile` | **O(depth)** | O(1) for the node |

> The **depth** is bounded by the maximum path length (≤ 10^4 characters) – in practice very small, so all operations are effectively O(1) for interview purposes.

---

## 5. Good, Bad & Ugly – What Hiring Managers Look For

| Aspect | What you should highlight | What to watch out for |
|--------|--------------------------|-----------------------|
| **Good** | • Clear tree structure<br>• Lexicographic sorting via `TreeMap`/`map`<br>• Single traversal helper | Shows *clean code* & *reusability*. |
| **Bad** | • Splitting path by `/` is **O(len(path))** – fine here but can be expensive for huge trees. | Over‑optimization can hurt readability. |
| **Ugly** | • Edge‑case of `/` root<br>• Empty string after split (leading slash)<br>• File vs directory detection | Small bugs in these places make the code fail tests. |

**Tip:** Always add a `main()` / test harness that mimics the LeetCode example. It forces you to think about edge cases early.

---

## 6. Interview‑Ready Discussion Points

1. **Why a Tree?**  
   * Filesystems are naturally hierarchical – a tree is the most natural abstraction.  
   * Each node’s `children` map lets you jump from one level to the next in *O(1)*.

2. **Keeping Listings Sorted**  
   * `TreeMap` / `std::map` keep keys sorted automatically – no extra `sort()` call.  
   * In Python, `sorted()` on keys works fine because dictionaries are unsorted.

3. **Path Parsing**  
   * `split('/')` works but leaves an empty string at index 0 for absolute paths.  
   * `putIfAbsent` / `children.emplace` create missing directories without a separate existence check.

4. **Memory Footprint**  
   * Every directory node stores a `TreeMap` or `unordered_map`.  
   * Files store only a `StringBuilder` / `list`.  
   * Typical LeetCode input is small; real‑world usage would require persistence or compression.

5. **Error Handling**  
   * The official problem guarantees valid paths – but in real life you’d add validation & throw descriptive exceptions.

---

## 7. How This Helps You Get a Job

| Skill | Example from FileSystem | How recruiters view it |
|-------|-------------------------|------------------------|
| **Data‑structure design** | Tree of `Node` objects | Shows you can model real‑world systems. |
| **Algorithmic thinking** | O(depth) traversal | Highlights efficient problem‑solving. |
| **Language mastery** | Java `TreeMap`, Python `dict`, C++ `map` | Demonstrates language‑specific best practices. |
| **Testing & debugging** | Write unit tests for each method | Proves you value correctness. |

When you list this problem on your resume or discuss it in an interview, use **keywords** that recruiters search for:

* **In‑memory file system**  
* **LeetCode 588**  
* **Design In‑Memory File System**  
* **Data Structures interview**  
* **Job Interview**  
* **Coding Interview**  

Mention the languages you’re comfortable with and highlight that you can adapt the core logic across **Java, Python, C++**—a clear signal of *cross‑platform expertise*.

---

## 8. Sample Blog Post Outline (SEO‑Optimized)

### Title
**“LeetCode 588 – Design an In‑Memory File System (Java, Python, C++) | Interview Success”**

### Meta Description
> Master LeetCode 588 and impress hiring managers. Read the full solution in Java, Python, and C++ plus a deep dive into the design decisions and interview tips.

### Headings (H1–H4)

| Heading | SEO Keywords |
|---------|--------------|
| **H1:** LeetCode 588 – Design an In‑Memory File System | `in-memory file system`, `LeetCode 588` |
| **H2:** Problem Summary & Constraints | `problem statement`, `coding interview` |
| **H2:** Data‑Structure Design | `tree data structure`, `node`, `directory`, `file` |
| **H3:** Java Implementation | `Java`, `TreeMap`, `StringBuilder` |
| **H3:** Python Implementation | `Python`, `dict`, `sorted()` |
| **H3:** C++ Implementation | `C++`, `std::map`, `unordered_map` |
| **H2:** Performance & Trade‑offs | `time complexity`, `space complexity` |
| **H2:** Common Pitfalls & Edge Cases | `edge cases`, `root path`, `file vs directory` |
| **H2:** How This Problem Prepares You for Interviews | `coding interview`, `software engineer`, `job interview` |

### Sample Blog Excerpt

> *“In this post we break down LeetCode’s hardest file‑system problem, show you how to code it in Java, Python, and C++, and explain the design choices that will impress your interviewers. From the tree structure that keeps directories sorted to the subtle quirks of path parsing, we cover everything you need to know to ace the interview and land that dream job.”*

---

## 9. Quick Test Script (Optional)

```python
if __name__ == "__main__":
    fs = FileSystem()
    fs.mkdir("/a/b/c")
    fs.addContentToFile("/a/b/c/d", "hello")
    print(fs.ls("/"))          # ['a']
    print(fs.ls("/a/b/c"))     # ['d']
    print(fs.readContentFromFile("/a/b/c/d"))  # hello
```

Feel free to paste these into your favorite IDE or online compiler to test further.

---

## 10. Final Takeaway

* **Clear abstraction** (Node + Tree) + **sorted container** = a clean, maintainable solution.  
* The same core logic works across **Java, Python, C++** – a great interview proof‑point.  
* Understanding this problem strengthens your *data‑structure* knowledge, a must‑have skill for any **software‑engineering interview**.

Give this a try, add it to your portfolio, and watch those job offers roll in! 🌟