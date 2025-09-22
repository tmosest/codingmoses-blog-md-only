---
title: LeetCode 432. All O`one Data Structure - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 All O`one Data Structure – LeetCode 432  
*O(1) time for every operation*  
*Java | Python | C++ implementation*  

---

### Table of Contents  

| Section | Description |
|---------|-------------|
| 1. Problem Overview | What the problem asks for |
| 2. Data‑Structure Design | Why a doubly‑linked list + hash maps work |
| 3. Core Operations | `inc`, `dec`, `getMaxKey`, `getMinKey` |
| 4. Full Code (Java) | Implementation |
| 5. Full Code (Python) | Implementation |
| 6. Full Code (C++) | Implementation |
| 7. Complexity Analysis | Time & Space |
| 8. Good, Bad & Ugly | Trade‑offs & pitfalls |
| 9. Interview Tips | How to talk about it |
| 10. SEO & Career | Keywords & how this helps your résumé |

> **TL;DR** – The trick is to keep the keys grouped by frequency in a *doubly linked list* (frequency nodes) and map each key to its node. Every operation is just a few pointer changes.

---

## 1. Problem Overview  

> **LeetCode 432 – All O`one Data Structure**  
> *Hard*  

Design a data structure that supports:

| Operation | Effect | Return |
|-----------|--------|--------|
| `inc(key)` | Increment the count of `key` (add `key` with count 1 if it does not exist). | `null` |
| `dec(key)` | Decrement the count of `key`. Remove it if the count reaches 0. | `null` |
| `getMaxKey()` | Return any key with the maximal count. | `""` if empty |
| `getMinKey()` | Return any key with the minimal count. | `""` if empty |

All operations **must run in O(1) average time**.  
Constraints: ≤ 5 × 10⁴ calls, key length ≤ 10.

---

## 2. Data‑Structure Design  

The classic solution uses:

1. **Doubly‑Linked List (DLL)** – each node stores a *frequency* and a set of keys that have exactly that frequency.  
   * `head` → minimal frequency node  
   * `tail` → maximal frequency node  
   * `node.prev`, `node.next` give O(1) navigation.

2. **Hash Map 1 – key → node** – allows us to locate the node that currently holds a key in O(1).

3. **Hash Map 2 – freq → node** (optional) – not strictly required if we keep nodes in order; but we can just walk to `node.next` or `node.prev` because the DLL is always sorted by frequency.

**Why it works in O(1):**

* `inc`:
  * Find the node via `key→node`.  
  * Remove key from current node’s key set.  
  * Move it to the *next* node (`freq+1`).  
    * If that node doesn’t exist or has a higher frequency, create a new node and insert it between `node` and `node.next`.  
  * If the current node becomes empty, unlink it.

* `dec`:
  * Symmetric to `inc`.  
  * If frequency becomes 0, delete from map; otherwise move to previous node (`freq‑1`).  
  * Remove node if empty.

* `getMaxKey` / `getMinKey`:  
  * Return any element from `tail.prev` or `head.next` key sets.  

All operations touch at most a constant number of nodes / pointers.

---

## 3. Core Operations (Pseudo‑Code)

```
inc(key):
    if key not in keyMap:
        // new key → freq 1
        node = head.next
        if node is tail or node.freq != 1:
            node = new Node(1)
            insertAfter(head, node)
        node.keys.add(key)
        keyMap[key] = node
    else:
        node = keyMap[key]
        newFreq = node.freq + 1
        nextNode = node.next
        if nextNode is tail or nextNode.freq != newFreq:
            nextNode = new Node(newFreq)
            insertAfter(node, nextNode)
        nextNode.keys.add(key)
        keyMap[key] = nextNode
        node.keys.remove(key)
        if node.keys empty: removeNode(node)

dec(key):
    node = keyMap[key]
    newFreq = node.freq - 1
    if newFreq == 0:
        delete keyMap[key]
    else:
        prevNode = node.prev
        if prevNode is head or prevNode.freq != newFreq:
            prevNode = new Node(newFreq)
            insertBefore(node, prevNode)
        prevNode.keys.add(key)
        keyMap[key] = prevNode
    node.keys.remove(key)
    if node.keys empty: removeNode(node)

getMaxKey():
    if head.next is tail: return ""
    return any element of tail.prev.keys

getMinKey():
    if tail.prev is head: return ""
    return any element of head.next.keys
```

---

## 4. Full Code – Java  

```java
import java.util.*;

public class AllOne {

    /* --------- Node for doubly linked list --------- */
    private static class Node {
        int freq;                     // the frequency value
        Set<String> keys = new HashSet<>();   // all keys with this freq
        Node prev, next;

        Node(int freq) { this.freq = freq; }
    }

    private final Node head;      // dummy head (freq = 0)
    private final Node tail;      // dummy tail (sentinel)
    private final Map<String, Node> keyToNode = new HashMap<>();

    /** Constructor */
    public AllOne() {
        head = new Node(0);
        tail = new Node(0);
        head.next = tail;
        tail.prev = head;
    }

    /* ---------- inc ---------- */
    public void inc(String key) {
        if (!keyToNode.containsKey(key)) {
            // first occurrence → freq = 1
            Node first = head.next;
            if (first == tail || first.freq != 1) {
                Node newNode = new Node(1);
                insertAfter(head, newNode);
                first = newNode;
            }
            first.keys.add(key);
            keyToNode.put(key, first);
        } else {
            Node cur = keyToNode.get(key);
            int newFreq = cur.freq + 1;
            Node next = cur.next;

            if (next == tail || next.freq != newFreq) {
                Node newNode = new Node(newFreq);
                insertAfter(cur, newNode);
                next = newNode;
            }
            next.keys.add(key);
            keyToNode.put(key, next);

            cur.keys.remove(key);
            if (cur.keys.isEmpty()) removeNode(cur);
        }
    }

    /* ---------- dec ---------- */
    public void dec(String key) {
        Node cur = keyToNode.get(key);
        int newFreq = cur.freq - 1;

        if (newFreq == 0) {
            keyToNode.remove(key);      // key disappears
        } else {
            Node prev = cur.prev;
            if (prev == head || prev.freq != newFreq) {
                Node newNode = new Node(newFreq);
                insertBefore(cur, newNode);
                prev = newNode;
            }
            prev.keys.add(key);
            keyToNode.put(key, prev);
        }

        cur.keys.remove(key);
        if (cur.keys.isEmpty()) removeNode(cur);
    }

    /* ---------- getMaxKey ---------- */
    public String getMaxKey() {
        if (tail.prev == head) return "";
        return tail.prev.keys.iterator().next();
    }

    /* ---------- getMinKey ---------- */
    public String getMinKey() {
        if (head.next == tail) return "";
        return head.next.keys.iterator().next();
    }

    /* ---------- helper methods ---------- */

    /** Insert newNode after node */
    private void insertAfter(Node node, Node newNode) {
        newNode.next = node.next;
        newNode.prev = node;
        node.next.prev = newNode;
        node.next = newNode;
    }

    /** Insert newNode before node */
    private void insertBefore(Node node, Node newNode) {
        newNode.prev = node.prev;
        newNode.next = node;
        node.prev.next = newNode;
        node.prev = newNode;
    }

    /** Remove node from DLL */
    private void removeNode(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
}
```

**Usage (simple test)**

```java
AllOne allOne = new AllOne();
allOne.inc("hello");
allOne.inc("leet");
System.out.println(allOne.getMaxKey()); // "hello" or "leet"
allOne.inc("hello");
System.out.println(allOne.getMaxKey()); // "hello"
allOne.dec("leet");
System.out.println(allOne.getMinKey()); // "leet"
```

---

## 5. Full Code – Python  

```python
from collections import defaultdict, deque
from typing import Dict, Set, Optional


class AllOne:
    """LeetCode 432 – All O`one Data Structure (Python)."""

    class Node:
        __slots__ = ("freq", "keys", "prev", "next")

        def __init__(self, freq: int):
            self.freq = freq
            self.keys: Set[str] = set()
            self.prev: Optional["AllOne.Node"] = None
            self.next: Optional["AllOne.Node"] = None

    def __init__(self) -> None:
        self.head = self.Node(0)      # dummy head
        self.tail = self.Node(0)      # dummy tail
        self.head.next = self.tail
        self.tail.prev = self.head
        self.key_to_node: Dict[str, AllOne.Node] = {}

    def _insert_after(self, node: AllOne.Node, new_node: AllOne.Node) -> None:
        nxt = node.next
        new_node.prev = node
        new_node.next = nxt
        node.next = new_node
        nxt.prev = new_node

    def _insert_before(self, node: AllOne.Node, new_node: AllOne.Node) -> None:
        prv = node.prev
        new_node.next = node
        new_node.prev = prv
        prv.next = new_node
        node.prev = new_node

    def _remove_node(self, node: AllOne.Node) -> None:
        node.prev.next = node.next
        node.next.prev = node.prev

    # ------------------------------------------------------------------
    def inc(self, key: str) -> None:
        if key not in self.key_to_node:
            first = self.head.next
            if first == self.tail or first.freq != 1:
                new_node = self.Node(1)
                self._insert_after(self.head, new_node)
                first = new_node
            first.keys.add(key)
            self.key_to_node[key] = first
        else:
            cur = self.key_to_node[key]
            new_freq = cur.freq + 1
            nxt = cur.next
            if nxt == self.tail or nxt.freq != new_freq:
                new_node = self.Node(new_freq)
                self._insert_after(cur, new_node)
                nxt = new_node
            nxt.keys.add(key)
            self.key_to_node[key] = nxt
            cur.keys.remove(key)
            if not cur.keys:
                self._remove_node(cur)

    def dec(self, key: str) -> None:
        cur = self.key_to_node[key]
        new_freq = cur.freq - 1
        if new_freq == 0:
            del self.key_to_node[key]
        else:
            prv = cur.prev
            if prv == self.head or prv.freq != new_freq:
                new_node = self.Node(new_freq)
                self._insert_before(cur, new_node)
                prv = new_node
            prv.keys.add(key)
            self.key_to_node[key] = prv
        cur.keys.remove(key)
        if not cur.keys:
            self._remove_node(cur)

    def getMaxKey(self) -> str:
        if self.head.next == self.tail:
            return ""
        return next(iter(self.tail.prev.keys))

    def getMinKey(self) -> str:
        if self.tail.prev == self.head:
            return ""
        return next(iter(self.head.next.keys))
```

**Quick test**

```python
a = AllOne()
a.inc("hello")
a.inc("leet")
print(a.getMaxKey())   # hello or leet
a.inc("hello")
print(a.getMaxKey())   # hello
a.dec("leet")
print(a.getMinKey())   # leet
```

---

## 6. Full Code – C++  

```cpp
#include <unordered_map>
#include <unordered_set>
#include <string>
using namespace std;

class AllOne {
private:
    struct Node {
        int freq;
        unordered_set<string> keys;
        Node *prev = nullptr, *next = nullptr;
        Node(int f) : freq(f) {}
    };

    Node* head;                 // dummy head (freq = 0)
    Node* tail;                 // dummy tail
    unordered_map<string, Node*> keyToNode;

    /* helper to insert after a node */
    void insertAfter(Node* node, Node* newNode) {
        newNode->next = node->next;
        newNode->prev = node;
        node->next->prev = newNode;
        node->next = newNode;
    }

    /* helper to insert before a node */
    void insertBefore(Node* node, Node* newNode) {
        newNode->prev = node->prev;
        newNode->next = node;
        node->prev->next = newNode;
        node->prev = newNode;
    }

    /* unlink an empty node */
    void removeNode(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
        delete node;
    }

public:
    AllOne() {
        head = new Node(0);
        tail = new Node(0);
        head->next = tail;
        tail->prev = head;
    }

    void inc(const string& key) {
        if (keyToNode.find(key) == keyToNode.end()) {
            Node* first = head->next;
            if (first == tail || first->freq != 1) {
                Node* newNode = new Node(1);
                insertAfter(head, newNode);
                first = newNode;
            }
            first->keys.insert(key);
            keyToNode[key] = first;
        } else {
            Node* cur = keyToNode[key];
            int newFreq = cur->freq + 1;
            Node* nxt = cur->next;
            if (nxt == tail || nxt->freq != newFreq) {
                Node* newNode = new Node(newFreq);
                insertAfter(cur, newNode);
                nxt = newNode;
            }
            nxt->keys.insert(key);
            keyToNode[key] = nxt;

            cur->keys.erase(key);
            if (cur->keys.empty())
                removeNode(cur);
        }
    }

    void dec(const string& key) {
        Node* cur = keyToNode[key];
        int newFreq = cur->freq - 1;
        if (newFreq == 0) {
            keyToNode.erase(key);
        } else {
            Node* prv = cur->prev;
            if (prv == head || prv->freq != newFreq) {
                Node* newNode = new Node(newFreq);
                insertBefore(cur, newNode);
                prv = newNode;
            }
            prv->keys.insert(key);
            keyToNode[key] = prv;
        }
        cur->keys.erase(key);
        if (cur->keys.empty())
            removeNode(cur);
    }

    string getMaxKey() {
        if (head->next == tail) return "";
        return *(tail->prev->keys.begin());
    }

    string getMinKey() {
        if (tail->prev == head) return "";
        return *(head->next->keys.begin());
    }
};
```

---

## 7. Complexity Analysis  

| Operation | **Time** | **Space** |
|-----------|----------|-----------|
| `inc` / `dec` | O(1) amortized (constant‑time DLL manipulations) | O(1) additional per key |
| `getMaxKey` / `getMinKey` | O(1) | — |

*Total auxiliary space*: `O(n)` where `n` is the number of unique keys stored.

---

## 8. The “Good/Bad” Debate  

**Good**

- **O(1) operations**: All core actions execute in constant time – an ideal interview requirement.
- **Straightforward data structure**: The doubly linked list of frequency nodes is intuitive and often appears in interviews as a classic design pattern.
- **Clear separation of concerns**: Keys and frequencies are decoupled – frequency nodes hold a set of keys; the mapping `key_to_node` gives O(1) access to the node for each key.

**Bad**

- **Memory overhead**: Each frequency node allocates a `set`/`unordered_set`, even if empty for a brief moment. Though usually negligible, it’s non‑trivial when many keys and frequency changes happen.
- **Edge‑case handling**: Need to carefully maintain `head` and `tail` pointers, and delete nodes properly (especially in C++), which can be error‑prone for beginners.
- **Testing complexity**: Verifying that all edge cases (max/min after deletions, duplicate keys, etc.) work requires a non‑trivial suite of unit tests.

---

## 8. “What If” – Alternative Approaches  

1. **Hash map + Balanced BST**  
   Use a hash map for key → frequency, and a balanced BST (or `std::map`/`TreeMap`) to keep keys sorted by frequency.  
   *Pros*: Simpler to code in languages with built‑in sorted maps.  
   *Cons*: Each `inc/dec` would require log‑time BST updates → not O(1). Not suitable for the interview requirement.

2. **Two hash maps + two heaps**  
   Maintain two heaps (max‑heap and min‑heap) keyed by frequency.  
   *Pros*: Can easily retrieve max/min.  
   *Cons*: Heaps need lazy deletion or extra bookkeeping to stay in sync with frequency changes → extra complexity and not strictly O(1).

3. **Skip‑list** (randomized balanced structure)  
   Similar to the linked‑list idea but with probabilistic balancing for faster operations.  
   *Pros*: Still O(1) average time.  
   *Cons*: More code, less deterministic, overkill for interview.

---

## 9. “Good/Bad” Commentary  

**Good:**

- The problem tests knowledge of data structures beyond naive hash tables: you need to combine a hash map for O(1) look‑ups with a doubly linked list for ordered traversal.
- It’s an excellent example of the *design a data structure* pattern that interviewers love, as it requires thoughtful balancing of time and space constraints.

**Bad:**

- The implementation is verbose. A new candidate may be overwhelmed by the many pointer manipulations (especially in C++) and lose focus on the core idea.
- There are subtle bugs that can easily creep in: forgetting to delete a node, mis‑linking pointers, or mishandling the edge cases of empty lists.
- The design is somewhat fragile: a single mistake in pointer updates can corrupt the entire structure, leading to crashes or incorrect results.

---

## 10. Take‑away for Interviews

- **Explain the design first**: Mention “hash map + doubly linked list of frequency nodes” and why each operation is O(1).
- **Walk through an example**: Show how `inc("a")`, `inc("b")`, `inc("a")` change the list.
- **Highlight edge cases**: Empty list, deletion to zero, duplicate increments.
- **Time‑complexity proof**: Every step is constant time; no loops over all keys.
- **Space analysis**: At most one node per distinct frequency and one entry per key.

**Final Thought**  
The “All O`one Data Structure” is a perfect showcase of algorithmic thinking and efficient coding. Mastering it will impress interviewers and earn you that coveted job offer!

--- 

*Happy coding, and may your keys always stay in order!*