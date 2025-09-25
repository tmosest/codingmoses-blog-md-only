---
title: LeetCode 460. LFU Cache - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. LFU Cache – O(1) implementation in three languages

The classic LeetCode hard problem 460: *Least Frequently Used (LFU) Cache*.  
The goal is to support `get` and `put` in **average O(1)** time and to evict the
key with the lowest access frequency (and, in case of ties, the least‑recently
used one).

Below you’ll find a complete, production‑ready implementation for:

| Language | File | Key Idea |
|----------|------|----------|
| Java | `LFUCache.java` | Two hash maps + `LinkedHashSet` |
| Python | `lfu_cache.py` | Two dictionaries + `OrderedDict` |
| C++ | `lfu_cache.cpp` | Two `unordered_map` + `list` + `unordered_map` of iterators |

All three solutions run in **O(1)** on average and handle capacities up to `10^4`
and `2·10^5` calls – exactly what the problem statement demands.

> **Why two hash‑maps?**  
> *One* keeps `key → (value, frequency)`.  
> *Two* keeps `frequency → list/LinkedHashSet of keys` (preserving LRU order for each frequency).  
> The `minFreq` variable tells us which frequency bucket contains the eviction candidate.

---

### Java (`LFUCache.java`)

```java
import java.util.*;

/**
 * Design and implement a data structure for a Least Frequently Used (LFU) cache.
 *
 * All operations run in O(1) average time.
 */
public class LFUCache {

    // key -> (frequency, value)
    private final Map<Integer, Node> cache;

    // frequency -> keys that have that frequency (keeps LRU order)
    private final Map<Integer, LinkedHashSet<Integer>> freqMap;

    private final int capacity;
    private int minFreq;   // current minimum frequency

    /* ---------- internal node ---------- */
    private static class Node {
        int key, value, freq;
        Node(int k, int v, int f) { key = k; value = v; freq = f; }
    }

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.cache = new HashMap<>();
        this.freqMap = new HashMap<>();
        this.minFreq = 0;
    }

    /** Returns the value of the key if present, otherwise -1. */
    public int get(int key) {
        Node node = cache.get(key);
        if (node == null) return -1;
        // increase frequency
        updateFreq(node);
        return node.value;
    }

    /** Inserts/updates the key/value pair. */
    public void put(int key, int value) {
        if (capacity <= 0) return;

        Node node = cache.get(key);
        if (node != null) {             // update existing key
            node.value = value;
            updateFreq(node);
            return;
        }

        if (cache.size() == capacity) { // eviction required
            evict();
        }

        // insert new key
        Node newNode = new Node(key, value, 1);
        cache.put(key, newNode);
        freqMap.computeIfAbsent(1, ignored -> new LinkedHashSet<>()).add(key);
        minFreq = 1;                     // new node has freq 1
    }

    /* ---------- helpers ---------- */

    /** Move node to next frequency bucket. */
    private void updateFreq(Node node) {
        int oldFreq = node.freq;
        LinkedHashSet<Integer> set = freqMap.get(oldFreq);
        set.remove(node.key);
        if (set.isEmpty()) {
            freqMap.remove(oldFreq);
            if (minFreq == oldFreq) minFreq++;   // if we removed the min bucket
        }

        node.freq++;
        freqMap.computeIfAbsent(node.freq, ignored -> new LinkedHashSet<>()).add(node.key);
    }

    /** Evicts the least frequently (and least recently) used key. */
    private void evict() {
        LinkedHashSet<Integer> set = freqMap.get(minFreq);
        // LinkedHashSet preserves insertion order → first element is LRU
        int keyToRemove = set.iterator().next();
        set.remove(keyToRemove);
        if (set.isEmpty()) freqMap.remove(minFreq);

        cache.remove(keyToRemove);
    }
}
```

---

### Python (`lfu_cache.py`)

```python
from collections import defaultdict, OrderedDict

class LFUCache:
    """
    Design and implement a data structure for a Least Frequently Used (LFU) cache.
    All operations run in O(1) average time.
    """

    def __init__(self, capacity: int):
        self.capacity = capacity
        self.size = 0
        self.min_freq = 0
        self.key_to_val_freq = {}  # key -> (value, freq)
        self.freq_to_keys = defaultdict(OrderedDict)  # freq -> OrderedDict{key: None}

    def get(self, key: int) -> int:
        if key not in self.key_to_val_freq:
            return -1
        val, freq = self.key_to_val_freq[key]
        self._increase_freq(key, val, freq)
        return val

    def put(self, key: int, value: int) -> None:
        if self.capacity <= 0:
            return

        if key in self.key_to_val_freq:
            # Update value, keep frequency
            _, freq = self.key_to_val_freq[key]
            self.key_to_val_freq[key] = (value, freq)
            self._increase_freq(key, value, freq)
            return

        if self.size == self.capacity:
            self._evict()

        # Insert new key
        self.key_to_val_freq[key] = (value, 1)
        self.freq_to_keys[1][key] = None
        self.min_freq = 1
        self.size += 1

    # ---------- internal helpers ----------
    def _increase_freq(self, key: int, value: int, freq: int) -> None:
        """Move key from freq to freq+1."""
        # remove from old freq bucket
        del self.freq_to_keys[freq][key]
        if not self.freq_to_keys[freq]:
            del self.freq_to_keys[freq]
            if self.min_freq == freq:
                self.min_freq += 1

        # add to new freq bucket
        self.freq_to_keys[freq + 1][key] = None
        # update mapping
        self.key_to_val_freq[key] = (value, freq + 1)

    def _evict(self) -> None:
        """Evict the LRU key in the bucket with the lowest frequency."""
        # OrderedDict preserves insertion order -> the first key is LRU
        evict_key, _ = self.freq_to_keys[self.min_freq].popitem(last=False)
        if not self.freq_to_keys[self.min_freq]:
            del self.freq_to_keys[self.min_freq]
        del self.key_to_val_freq[evict_key]
        self.size -= 1
```

> **Why `OrderedDict`?**  
> In Python 3.7+ the regular `dict` preserves insertion order, but `OrderedDict`
> still gives us an explicit `popitem(last=False)` interface that works in older
> CPython versions and guarantees O(1) for delete.

---

### C++ (`lfu_cache.cpp`)

```cpp
#include <bits/stdc++.h>
using namespace std;

/**
 * Design and implement a data structure for a Least Frequently Used (LFU) cache.
 * All operations run in O(1) average time.
 */
class LFUCache {
public:
    struct Node {
        int key, val, freq;
        Node(int k, int v, int f) : key(k), val(v), freq(f) {}
    };

    LFUCache(int capacity) : capacity(capacity), size(0), minFreq(0) {}

    int get(int key) {
        auto it = keyMap.find(key);
        if (it == keyMap.end()) return -1;
        Node &node = *(it->second);
        update(node);
        return node.val;
    }

    void put(int key, int value) {
        if (capacity <= 0) return;

        auto it = keyMap.find(key);
        if (it != keyMap.end()) {          // key exists
            it->second->val = value;
            update(*(it->second));
            return;
        }

        if (size == capacity) evict();

        // insert new key
        Node *node = new Node(key, value, 1);
        keyMap[key] = node;
        freqMap[1].push_back(key);
        freqPos[key] = --freqMap[1].end(); // iterator to its position
        minFreq = 1;
        ++size;
    }

private:
    int capacity, size, minFreq;
    unordered_map<int, Node*> keyMap;               // key -> node*
    unordered_map<int, list<int>> freqMap;          // freq -> list of keys (LRU)
    unordered_map<int, list<int>::iterator> freqPos; // key -> iterator inside its list

    void update(Node &node) {
        int oldF = node.freq;
        // remove key from old bucket
        freqMap[oldF].erase(freqPos[node.key]);
        if (freqMap[oldF].empty()) {
            freqMap.erase(oldF);
            if (minFreq == oldF) ++minFreq;
        }

        // add key to new bucket
        node.freq++;
        freqMap[node.freq].push_back(node.key);
        freqPos[node.key] = --freqMap[node.freq].end();
    }

    void evict() {
        int key = freqMap[minFreq].front();
        freqMap[minFreq].pop_front();
        if (freqMap[minFreq].empty()) freqMap.erase(minFreq);
        delete keyMap[key];
        keyMap.erase(key);
        --size;
    }
};
```

> **Why `list` + iterator map?**  
> `list` gives us constant‑time deletion given an iterator.  
> The `freqPos` map remembers each key’s iterator inside its frequency bucket,
> allowing us to delete it in O(1).

---

## 2. The Good, The Bad & The Ugly – An SEO‑Ready Blog Post

> **Target audience**: software engineers preparing for system‑design interviews, hiring managers looking for data‑structure skills, and anyone curious about how LFU works under the hood.

---

### The Good: Why LFU Matters

* **Real‑world caching** – Databases, CDN edge servers, and OS page‑replacement
  policies all use LFU (or variants).  
  Demonstrating mastery of LFU shows you understand *frequency‑based eviction*,
  not just *time‑based* (LRU) or *size‑based* (FIFO).

* **Interview buzz‑word** – LeetCode problem 460 is a staple in senior‑level
  interviews. Solving it correctly and efficiently is a green flag for hiring
  managers.

* **O(1) complexity** – Proving you can design data structures that meet
  strict time‑space guarantees showcases deep knowledge of hash tables, linked
  lists, and amortized analysis.

---

### The Bad: Common Pitfalls

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| **Not keeping LRU order per frequency** | Evicts the most recently used key → wrong answer | Use `LinkedHashSet` (Java), `OrderedDict` (Python), or `list` with iterators (C++) to preserve order inside each bucket. |
| **Updating frequency on put but not on get** | Wrong eviction candidate | Always bump the frequency for `get` and for an update‑only `put`. |
| **Missing `minFreq` update after bucket deletion** | `evict()` pulls from an empty bucket → `KeyError` / `IndexError` | After removing the last key from a frequency bucket, increase `minFreq` **only if** that bucket was the minimum. |
| **Memory leaks in C++** | `new Node` never deleted | Delete the evicted node’s memory in `evict()` or use smart pointers (`unique_ptr`). |

---

### The Ugly: Edge Cases That Break Your Code

1. **Zero capacity** – Your cache should gracefully do nothing.
2. **Duplicate `put`** – Updating an existing key must *not* reset its frequency.
3. **Large frequencies** – Frequencies can grow unbounded; the bucket map must
   dynamically allocate new buckets and drop old ones.
4. **Simultaneous evictions** – When `capacity` is reached, you must always
   evict *exactly one* key. A buggy implementation might try to evict two
   keys in one call.

---

## 3. Putting It All Together – What Recruiters Look For

* **System‑design fluency** – You can explain *why* the two‑hash‑map pattern
  solves the problem in O(1).
* **Clean code** – No global mutable state, all methods are `public`/`private`
  where appropriate, and memory is cleaned up (C++ example deletes nodes).
* **Testability** – Each implementation can be unit‑tested with the
  `TestCase` from LeetCode, and you can add your own `if __name__ == "__main__"` test harness.
* **Documentation** – Clear comments and a brief “why” section help hiring
  managers and your future self.

> **Pro tip:** When you’re in an interview, walk the interviewer through the
> data‑structure diagram, name each component (`cache`, `freqMap`,
> `minFreq`), and explain the *O(1)* invariants.

---

## 4. How to Use the Blog Post in Your Resume / LinkedIn

1. **Blog Title** – *“LFU Cache – The Good, the Bad & the Ugly”*  
   Search terms: “LFU cache”, “LeetCode 460”, “system design interview”.
2. **Add a Link** – “Read the full article with code: https://your‑blog.com/lfu‑cache‑blog”.
3. **Show‑case** – In the *Projects* section, mention:  
   *Implemented a production‑ready LFU cache in Java, Python, and C++ with O(1) operations; solved LeetCode 460*.
4. **Keywords** – “O(1) time complexity”, “hash map”, “LinkedHashSet”, “OrderedDict”, “frequency‑based eviction”, “system design interview”.
5. **Cover Letter** – Highlight that you tackled a *hard* LeetCode problem and that the solution is reusable in real‑world caching scenarios (CDN, in‑memory DB, etc.).

---

### TL;DR

| Language | Key Complexity | How to Show it in an Interview |
|----------|----------------|--------------------------------|
| Java | `O(1)` | Talk about the two hash‑maps, `minFreq`, and eviction via `LinkedHashSet`. |
| Python | `O(1)` | Mention `defaultdict(OrderedDict)` and how it keeps LRU order. |
| C++ | `O(1)` | Use `unordered_map` + `list` + iterator map for O(1) deletions. |

Implement it, add the code to your GitHub repo, and reference it in your
LeetCode “Discuss” or “Interview Notes” section.  
When recruiters spot that you’ve mastered an O(1) LFU cache in multiple languages, they’ll think: *“This engineer knows hash tables, linked lists, and can optimize for production!”*