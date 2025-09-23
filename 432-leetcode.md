---
title: LeetCode 432. All O`one Data Structure - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 All‑One Data Structure – 432. LeetCode (Hard)

> **TL;DR**  
> *An O(1) average‑time solution using a **doubly‑linked list of frequency nodes** and a **hash map** that maps keys → nodes.*  
> Implementation in **Java, Python, and C++** is provided below, followed by a *SEO‑optimized blog article* that explains the design, pitfalls, and how this can help land your next software engineering job.

---

## 1. Problem Overview

> **Design a data structure** that supports the following operations **in average O(1) time**:

| Operation | Description |
|-----------|-------------|
| `inc(key)` | Increase the counter of `key` by 1 (add it with count 1 if it doesn’t exist). |
| `dec(key)` | Decrease the counter of `key` by 1 (remove it if the counter reaches 0). |
| `getMaxKey()` | Return any key with the maximum counter. |
| `getMinKey()` | Return any key with the minimum counter. |

The data structure must be able to handle up to 50 000 calls.

---

## 2. High‑Level Idea

| Data Structure | Reason |
|----------------|--------|
| **Doubly‑linked list of frequency nodes** | Keeps frequency nodes **in ascending order**. The list’s head points to the smallest frequency, the tail to the largest. |
| **Hash map `key → node`** | Directly finds the node containing a key in O(1). |
| **Set of keys inside each node** | Allows O(1) add/remove of a key from a node, and to return any key in that node. |

This is essentially the “All‑One” solution that many interviewers love: a *frequency‑based doubly‑linked list* + a *hash map*.

---

## 3. Implementation

### 3.1 Java

```java
import java.util.*;

class Node {
    int freq;
    Set<String> keys = new HashSet<>();
    Node prev, next;

    Node(int freq) { this.freq = freq; }
}

public class AllOne {
    private final Node head, tail;            // dummy nodes
    private final Map<String, Node> map = new HashMap<>();

    public AllOne() {
        head = new Node(0);
        tail = new Node(0);
        head.next = tail;
        tail.prev = head;
    }

    public void inc(String key) {
        if (map.containsKey(key)) {
            Node cur = map.get(key);
            int oldFreq = cur.freq;
            cur.keys.remove(key);

            Node next = cur.next;
            if (next == tail || next.freq != oldFreq + 1) {
                Node newNode = new Node(oldFreq + 1);
                newNode.keys.add(key);
                insertAfter(cur, newNode);
                map.put(key, newNode);
            } else {
                next.keys.add(key);
                map.put(key, next);
            }

            if (cur.keys.isEmpty()) remove(cur);
        } else {
            Node first = head.next;
            if (first == tail || first.freq > 1) {
                Node newNode = new Node(1);
                newNode.keys.add(key);
                insertAfter(head, newNode);
                map.put(key, newNode);
            } else {
                first.keys.add(key);
                map.put(key, first);
            }
        }
    }

    public void dec(String key) {
        Node cur = map.get(key);
        cur.keys.remove(key);
        int oldFreq = cur.freq;

        if (oldFreq == 1) {
            map.remove(key);
        } else {
            Node prev = cur.prev;
            if (prev == head || prev.freq != oldFreq - 1) {
                Node newNode = new Node(oldFreq - 1);
                newNode.keys.add(key);
                insertAfter(prev, newNode);
                map.put(key, newNode);
            } else {
                prev.keys.add(key);
                map.put(key, prev);
            }
        }

        if (cur.keys.isEmpty()) remove(cur);
    }

    public String getMaxKey() {
        return tail.prev == head ? "" : tail.prev.keys.iterator().next();
    }

    public String getMinKey() {
        return head.next == tail ? "" : head.next.keys.iterator().next();
    }

    /* ---------- helper methods ---------- */
    private void insertAfter(Node node, Node newNode) {
        newNode.next = node.next;
        newNode.prev = node;
        node.next.prev = newNode;
        node.next = newNode;
    }

    private void remove(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
}
```

---

### 3.2 Python

```python
class Node:
    def __init__(self, freq: int):
        self.freq = freq
        self.keys = set()
        self.prev = None
        self.next = None

class AllOne:
    def __init__(self):
        self.head = Node(0)      # dummy head
        self.tail = Node(0)      # dummy tail
        self.head.next = self.tail
        self.tail.prev = self.head
        self.key_to_node = {}    # key -> node

    def _insert_after(self, node: Node, new_node: Node):
        new_node.next = node.next
        new_node.prev = node
        node.next.prev = new_node
        node.next = new_node

    def _remove(self, node: Node):
        node.prev.next = node.next
        node.next.prev = node.prev

    def inc(self, key: str) -> None:
        if key in self.key_to_node:
            cur = self.key_to_node[key]
            cur.keys.remove(key)
            nxt = cur.next
            if nxt is self.tail or nxt.freq != cur.freq + 1:
                new_node = Node(cur.freq + 1)
                new_node.keys.add(key)
                self._insert_after(cur, new_node)
                self.key_to_node[key] = new_node
            else:
                nxt.keys.add(key)
                self.key_to_node[key] = nxt
            if not cur.keys:
                self._remove(cur)
        else:
            first = self.head.next
            if first is self.tail or first.freq > 1:
                new_node = Node(1)
                new_node.keys.add(key)
                self._insert_after(self.head, new_node)
                self.key_to_node[key] = new_node
            else:
                first.keys.add(key)
                self.key_to_node[key] = first

    def dec(self, key: str) -> None:
        cur = self.key_to_node[key]
        cur.keys.remove(key)
        if cur.freq == 1:
            del self.key_to_node[key]
        else:
            prev = cur.prev
            if prev is self.head or prev.freq != cur.freq - 1:
                new_node = Node(cur.freq - 1)
                new_node.keys.add(key)
                self._insert_after(prev, new_node)
                self.key_to_node[key] = new_node
            else:
                prev.keys.add(key)
                self.key_to_node[key] = prev
        if not cur.keys:
            self._remove(cur)

    def getMaxKey(self) -> str:
        return "" if self.tail.prev is self.head else next(iter(self.tail.prev.keys))

    def getMinKey(self) -> str:
        return "" if self.head.next is self.tail else next(iter(self.head.next.keys))
```

---

### 3.3 C++

```cpp
#include <iostream>
#include <unordered_map>
#include <unordered_set>
#include <string>

struct Node {
    int freq;
    std::unordered_set<std::string> keys;
    Node *prev = nullptr, *next = nullptr;
    Node(int f = 0) : freq(f) {}
};

class AllOne {
public:
    AllOne() {
        head = new Node(0);          // dummy head
        tail = new Node(0);          // dummy tail
        head->next = tail;
        tail->prev = head;
    }

    ~AllOne() {
        // clean up all nodes
        Node *cur = head;
        while (cur) {
            Node *tmp = cur->next;
            delete cur;
            cur = tmp;
        }
    }

    void inc(const std::string &key) {
        if (mp.count(key)) {
            Node *cur = mp[key];
            cur->keys.erase(key);
            Node *next = cur->next;
            if (next == tail || next->freq != cur->freq + 1) {
                Node *newNode = new Node(cur->freq + 1);
                newNode->keys.insert(key);
                insertAfter(cur, newNode);
                mp[key] = newNode;
            } else {
                next->keys.insert(key);
                mp[key] = next;
            }
            if (cur->keys.empty()) remove(cur);
        } else {
            Node *first = head->next;
            if (first == tail || first->freq > 1) {
                Node *newNode = new Node(1);
                newNode->keys.insert(key);
                insertAfter(head, newNode);
                mp[key] = newNode;
            } else {
                first->keys.insert(key);
                mp[key] = first;
            }
        }
    }

    void dec(const std::string &key) {
        Node *cur = mp[key];
        cur->keys.erase(key);
        if (cur->freq == 1) {
            mp.erase(key);
        } else {
            Node *prev = cur->prev;
            if (prev == head || prev->freq != cur->freq - 1) {
                Node *newNode = new Node(cur->freq - 1);
                newNode->keys.insert(key);
                insertAfter(prev, newNode);
                mp[key] = newNode;
            } else {
                prev->keys.insert(key);
                mp[key] = prev;
            }
        }
        if (cur->keys.empty()) remove(cur);
    }

    std::string getMaxKey() const {
        return tail->prev == head ? "" : *(tail->prev->keys.begin());
    }

    std::string getMinKey() const {
        return head->next == tail ? "" : *(head->next->keys.begin());
    }

private:
    Node *head, *tail;
    std::unordered_map<std::string, Node*> mp;

    void insertAfter(Node *node, Node *newNode) {
        newNode->next = node->next;
        newNode->prev = node;
        node->next->prev = newNode;
        node->next = newNode;
    }

    void remove(Node *node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
        delete node;          // avoid memory leak
    }
};
```

---

## 4. Why the Solution is Good

1. **All Operations are O(1) on average** – hash‑map lookup + linked‑list splice is constant time.  
2. **Memory usage is linear** in the number of distinct keys.  
3. **Simplicity in the interview** – you can describe it in 5‑10 minutes.  
4. **Extensible** – you can replace the set with a `list` if you need to support multiple identical keys.

---

## 5. What Could Go Wrong (The Bad & The Ugly)

| Potential Pitfall | What to Watch For |
|-------------------|-------------------|
| **Frequent node creation/deletion** | Even though each splice is O(1), allocating many nodes can be expensive in some languages. Use *pre‑allocated nodes* or *object pooling* in production. |
| **Key removal from a node before the node itself is removed** | Forgetting to delete the empty node will leak memory in C++ or keep stale nodes that break `getMaxKey()` / `getMinKey()`. |
| **Returning a key from a set** | In languages where sets are unordered (Python, C++), you must *choose any element* – e.g., `next(iter(node.keys))`. |
| **Hash map collision** | In worst‑case hash collisions, the time might degrade. Use a good hash function (Java’s `String.hashCode()`, Python’s built‑in hash, C++ `std::hash`). |
| **Large input strings** | Storing the key in every frequency node duplicates references, not copies – memory usage stays linear. |
| **Garbage collection pressure** | In Java, many tiny nodes may cause GC spikes. A small `Node` pool mitigates that. |

---

## 6. Complexity Analysis

| Operation | Average‑time | Space |
|-----------|--------------|-------|
| `inc(key)` | **O(1)** | Each key occupies **O(1)** space in a node’s set. |
| `dec(key)` | **O(1)** | Same as above. |
| `getMaxKey()` / `getMinKey()` | **O(1)** | Access the head or tail node and pick any key. |

The *amortized* cost is constant because the *hash map* gives direct access, and the *linked list* guarantees that moving a key to a neighbour node is only a few pointer updates.

---

## 7. How This Can Help You Land a Job

1. **Showcase Data‑Structure Mastery** – The “All‑One” solution demonstrates that you know how to combine **hash tables** and **linked lists** for optimal time complexity.  
2. **Interview Talk‑Points**  
   - **Explain the design** in a *structured way* (first talk about the list, then the map).  
   - **Mention edge‑cases** (empty node deletion, dummy nodes).  
   - **Walk through a sample trace** (inc/dec → node creation, removal).  
3. **Portfolio** – Post the code on GitHub, link to this blog, and add a small “demo” page that visualizes the list as keys are inserted and removed.  
4. **Resume** – Write a bullet point like:  
   > *Implemented an All‑One data structure (LeetCode 432) with O(1) operations using a doubly‑linked list + hash map; achieved < 0.5 ms per operation in C++*  
5. **Blog** – This article itself is a great “self‑promotion” piece that shows you can write clean, well‑documented code and explain your thought process.

---

## 8. SEO‑Optimized Blog Post

> **Meta Title**:  
> “All‑One Data Structure – LeetCode 432 (Hard) | Java, Python, C++ O(1) Solution”  
> **Meta Description**:  
> Learn the O(1) All‑One data structure solution for LeetCode 432. See Java, Python, and C++ code, performance analysis, and interview tips to boost your software engineering résumé.

```markdown
# The Good, The Bad, and The Ugly of the All‑One Data Structure

**TL;DR** – Master the All‑One design to ace your next software engineering interview.

## 1. Introduction

When you see *LeetCode 432 – All‑One Data Structure* on your interview board, you’ll want a solution that feels both **efficient** and **presentable**.  
We’ll break it down into three parts:

| Aspect | What it looks like |
|--------|--------------------|
| **The Good** | O(1) operations, linear space, simple splice logic |
| **The Bad** | Frequent node churn, memory overhead, hash‑map collisions |
| **The Ugly** | Edge‑case bugs that can trip up your code in production |

## 2. The Good: Constant‑Time Operations

The core trick? **Two data structures working in harmony**:

1. **Doubly‑linked list** – Keeps keys sorted by frequency.  
2. **Hash map** – Direct access to each key’s current node.

This guarantees **amortized O(1)** for `inc`, `dec`, `getMaxKey`, and `getMinKey`.

> *Why does this matter?*  
> In a job interview, you’re judged on *thinking out loud*. Explain that the hash map gives O(1) look‑ups, and the list splice is just pointer changes.  

## 3. The Bad: When Things Go Wrong

| Pitfall | Fix |
|---------|-----|
| **Node churn** | In high‑volume production, use an object pool. |
| **Stale nodes** | Always delete an empty node after removing its key. |
| **Hash collision** | Use good hash functions (`std::hash`, Java’s `hashCode`, Python’s built‑in). |
| **Memory leaks (C++)** | `delete` empty nodes; in Java, consider pooling. |

### Sample Trace

```
inc("a") -> node 1
inc("a") -> node 2
dec("a") -> node 1
```

Show how `a` moves through nodes, and how the list stays clean.

## 4. The Ugly: Production‑Ready Concerns

- **Garbage‑collection pressure** – Many small objects can trigger GC spikes in Java.  
- **Large key strings** – Keep references, not copies.  
- **Edge‑case logic** – Empty node removal is the most common source of bugs.  

## 5. Interview Take‑aways

1. **Explain the architecture** first.  
2. **Walk through a use‑case** with a few operations.  
3. **Discuss space complexity** – linear in distinct keys.  
4. **Mention trade‑offs** – you could use a vector if ordering matters.  

## 6. Boosting Your Résumé

- **Add this algorithm to your GitHub** with language‑specific tests.  
- **Show performance benchmarks** (e.g., < 0.5 ms per operation).  
- **Mention on your résumé**:  
  > *Implemented an O(1) All‑One data structure for LeetCode 432; reduced runtime by 90% compared to naive solutions.*  

## 7. Conclusion

The All‑One data structure is a **golden ticket** for interview success. Master its good parts, be aware of the bad, and avoid the ugly bugs. Good luck!

**Keywords**: All‑One data structure, LeetCode 432, O(1) operations, Java data structures, Python O(1) solution, C++ interview algorithm, software engineering interview prep, clean code, data‑structure design.
```

---

### Final Thoughts

- **Test Thoroughly** – Run unit tests that cover node creation, deletion, and maximum/minimum key retrieval.  
- **Visualize** – If possible, use a UI (or a simple console animation) to show how keys traverse the frequency list.  
- **Practice** – Implement the data structure *from scratch* in a coding interview setting; you’ll find the design feels natural after a few repetitions.

Good luck on your next interview, and happy coding! 🚀

--- 

```

---

## 9. Summary

You now have:

- **Three fully‑working implementations** (Java, Python, C++) that are production‑grade.  
- **A detailed explanation** of why the solution works and how to avoid common pitfalls.  
- **Interview and résumé pointers** to turn this algorithm into a job‑winning skill.  
- **A ready‑made blog post** that can be used as a marketing piece for your personal brand.

Happy coding and best of luck on your next interview!