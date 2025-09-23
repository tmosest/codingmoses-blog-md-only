---
title: LeetCode 381. Insert Delete GetRandom O(1) - Duplicates allowed - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 381. Insert Delete GetRandom O(1) – Duplicates Allowed  
### Full‑stack solution in **Java / Python / C++** + a SEO‑friendly blog post

---

### 1️⃣  Problem Recap

We need a data structure that supports the following operations in *average* O(1) time:

| Operation | Description | Return value |
|-----------|-------------|--------------|
| `insert(val)` | Add `val` to the multiset. Duplicates are allowed. | `true` if `val` was not already present, otherwise `false` |
| `remove(val)` | Remove one occurrence of `val` if it exists. | `true` if removal succeeded, otherwise `false` |
| `getRandom()` | Return a random element. The probability of each element is proportional to its frequency in the multiset. | A random integer |

> **Constraints**  
> * `-2³¹ ≤ val ≤ 2³¹ - 1`  
> * ≤ 2 × 10⁵ total operations  
> * `getRandom()` is never called on an empty collection

---

### 2️⃣  Core Idea – “Index‑Based HashMap + Dynamic Array”

1. **Dynamic array** (`ArrayList` / `vector` / `list`) keeps every inserted element.  
   * Random access (`O(1)`) → perfect for `getRandom()`.  
   * The last element can be removed in `O(1)` by swapping with the position we want to delete.

2. **HashMap** (`Map<Integer, Set<Integer>>`) maps a value → *all* indices where that value occurs in the array.  
   * Allows us to quickly locate an arbitrary occurrence of `val`.  
   * The set can be a `HashSet`, `LinkedHashSet`, or `unordered_set` (C++).

3. **Deletion**:  
   * Pick an arbitrary index `i` from the set of `val`.  
   * Replace `array[i]` with the last element `last`.  
   * Update the sets for `val` and `last`.  
   * Remove the last element from the array.

All operations are **amortized O(1)** – set operations and vector operations are O(1) on average.

---

### 3️⃣  Code

> **All three implementations follow the same logic** – only syntax changes.

---

#### 3.1  Java

```java
import java.util.*;

public class RandomizedCollection {
    // Stores all elements
    private final List<Integer> data;
    // Maps value -> set of indices where value occurs
    private final Map<Integer, Set<Integer>> indices;
    private final Random rand;

    public RandomizedCollection() {
        data = new ArrayList<>();
        indices = new HashMap<>();
        rand = new Random();
    }

    /** Inserts a value to the collection. */
    public boolean insert(int val) {
        // First time we see val?
        boolean isNew = !indices.containsKey(val);
        indices.computeIfAbsent(val, k -> new HashSet<>()).add(data.size());
        data.add(val);
        return isNew;
    }

    /** Removes a value from the collection. */
    public boolean remove(int val) {
        Set<Integer> idxSet = indices.get(val);
        if (idxSet == null || idxSet.isEmpty()) return false;

        // Grab an arbitrary index of the value to remove
        int removeIdx = idxSet.iterator().next();
        idxSet.remove(removeIdx);

        int lastIdx = data.size() - 1;
        int lastVal = data.get(lastIdx);

        // Move last element into the spot of the removed element
        data.set(removeIdx, lastVal);
        // Update the index set for lastVal
        Set<Integer> lastIdxSet = indices.get(lastVal);
        lastIdxSet.remove(lastIdx);
        lastIdxSet.add(removeIdx);

        // Remove last element from the list
        data.remove(lastIdx);

        // Clean up map if no more indices left
        if (idxSet.isEmpty()) indices.remove(val);
        return true;
    }

    /** Get a random element from the collection. */
    public int getRandom() {
        return data.get(rand.nextInt(data.size()));
    }
}
```

---

#### 3.2  Python

```python
import random
from collections import defaultdict

class RandomizedCollection:
    def __init__(self):
        self.data = []                          # list of all values
        self.indices = defaultdict(set)         # val -> set of positions

    def insert(self, val: int) -> bool:
        is_new = val not in self.indices
        self.indices[val].add(len(self.data))
        self.data.append(val)
        return is_new

    def remove(self, val: int) -> bool:
        if val not in self.indices or not self.indices[val]:
            return False

        remove_idx = self.indices[val].pop()
        last_idx = len(self.data) - 1
        last_val = self.data[last_idx]

        # Replace removed spot with last element
        self.data[remove_idx] = last_val
        self.indices[last_val].add(remove_idx)
        self.indices[last_val].discard(last_idx)

        self.data.pop()

        if not self.indices[val]:
            del self.indices[val]
        return True

    def getRandom(self) -> int:
        return random.choice(self.data)
```

---

#### 3.3  C++ (GNU++17)

```cpp
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <random>

class RandomizedCollection {
public:
    RandomizedCollection() : gen(rd()), dis(0, 0) {}

    bool insert(int val) {
        bool isNew = mp.find(val) == mp.end();
        mp[val].insert(data.size());
        data.push_back(val);
        return isNew;
    }

    bool remove(int val) {
        auto it = mp.find(val);
        if (it == mp.end() || it->second.empty()) return false;

        // index to remove
        int idx = *it->second.begin();
        it->second.erase(idx);

        int lastVal = data.back();
        int lastIdx = data.size() - 1;

        // Move last element into idx
        data[idx] = lastVal;
        mp[lastVal].insert(idx);
        mp[lastVal].erase(lastIdx);

        data.pop_back();

        if (it->second.empty()) mp.erase(it);
        return true;
    }

    int getRandom() {
        dis = std::uniform_int_distribution<int>(0, data.size() - 1);
        return data[dis(gen)];
    }

private:
    std::vector<int> data;
    std::unordered_map<int, std::unordered_set<int>> mp;
    std::random_device rd;
    std::mt19937 gen;
    std::uniform_int_distribution<int> dis;
};
```

---

### 4️⃣  Blog Article – “The Good, The Bad, The Ugly of RandomizedCollection”

> **SEO Keywords**: `LeetCode 381`, `RandomizedCollection`, `O(1) insert delete random`, `multiset data structure`, `hashmap vector trick`, `duplicate elements`, `data structures interview`, `python c++ java`, `job interview coding`

---

#### 4.1  Introduction

When recruiters ask “implement a data structure that supports insert, delete, and getRandom in constant time”, they’re not just testing your knowledge of arrays or hash tables – they’re probing your ability to **combine** data structures creatively.  
LeetCode 381 – *Insert Delete GetRandom O(1) – Duplicates allowed* is a classic interview favourite that showcases this exact skill set.

In this article we’ll dissect the **good**, the **bad**, and the **ugly** of solving this problem. Whether you’re preparing for a coding interview, looking to ace your next role, or simply sharpening your algorithmic toolbox, this guide will give you actionable insights and code snippets in Java, Python, and C++.

---

#### 4.2  The Good: Why the HashMap‑Vector Trick is a Game‑Changer

1. **Simplicity + Power** –  
   *An array for random access, a hashmap for index bookkeeping* – the whole solution fits into less than 100 lines of code.  
   * Every operation is *amortized* O(1) – perfect for high‑volume interview questions.

2. **Robustness with Duplicates** –  
   The hashmap stores *sets* of indices, so you can easily track how many copies of a value exist.  
   This eliminates the need for linked lists or trees that can degrade to O(log n).

3. **Language‑agnostic Pattern** –  
   The same logic translates to Java (`ArrayList + HashMap<Set>`), Python (`list + defaultdict(set)`), and C++ (`vector + unordered_map<unordered_set>`).  
   Demonstrating proficiency across multiple languages can be a strong differentiator in *full‑stack* or *backend* roles.

---

#### 4.3  The Bad: Hidden Pitfalls That Trip Candidates

| Pitfall | What Happens | How to Avoid It |
|---------|--------------|-----------------|
| **O(n) in worst‑case** | Using a list and a set that rehashes frequently may cause a linear‑time spike. | Rely on `HashSet`/`unordered_set` – they’re O(1) amortized, not worst‑case. |
| **Off‑by‑One errors** | Wrong index update during swap (forgetting to update the set for the moved element). | Write a helper method `swapAndUpdate(int i, int lastIdx)` or comment meticulously. |
| **Memory leak in Java** | Not removing the key from the map when its set becomes empty → O(n) space. | Clean up with `if (set.isEmpty()) indices.remove(val);`. |
| **Random generator initialization** | In C++ creating the distribution on every call can be expensive. | Keep a `std::mt19937` generator alive and only rebuild the distribution when the size changes. |
| **Python `defaultdict` misuse** | Accidentally keeping empty sets that keep the key alive. | Delete the key when the set becomes empty (`del self.indices[val]`). |

---

#### 4.4  The Ugly: Edge Cases & Debugging Tricks

1. **Swap‑and‑Remove Bug**  
   *Common error*: forgetting to remove the last index from the moved value’s set.  
   **Debug tip**: after every `remove()`, print the hashmap for both the removed value and the last value to ensure the sets are consistent.

2. **Empty Collection on `getRandom()`**  
   The problem guarantees this won’t happen, but defensive coding pays off in real‑world APIs.  
   ```java
   if (data.isEmpty()) throw new NoSuchElementException();
   ```

3. **Large Negative Numbers**  
   When using arrays, negative values don’t matter – they’re just keys in the hashmap.  
   But in C++ `unordered_map<int, ...>` is fine; just be careful with `size_t` vs `int` conversions.

4. **Randomness Bias**  
   Some interviewers test for true uniformity. Use a *system RNG* (`Random` in Java, `random.choice` in Python, `mt19937` in C++) and **re‑seed** if you’re writing a long‑running service.

---

#### 4.5  How This Wins Interviewers

* **Showcases Data‑Structure Fusion** – Combining array and hashmap is a hallmark of clever engineering.  
* **Highlights Edge‑Case Handling** – Duplicates, deletions of missing values, and clean‑up logic demonstrate attention to detail.  
* **Demonstrates Language Proficiency** – Providing Java, Python, and C++ solutions shows you’re ready for any stack.  
* **Speaks to Real‑World Use** – `RandomizedCollection` is essentially a *multiset* with fast random sampling – a pattern used in caches, load balancers, and gaming engines.

---

#### 4.6  Quick Checklist Before the Interview

| ✅ | Item |
|----|------|
| **Understand the problem** – duplicates are allowed. |
| **Know the data structures** – dynamic array + hashmap of index sets. |
| **Plan the delete** – swap with last element, update indices. |
| **Write helper functions** – to keep code readable. |
| **Test thoroughly** – insert, delete, getRandom, edge cases. |
| **Explain your design** – big‑O, why we use a set, why it’s constant time. |
| **Mention trade‑offs** – memory overhead, potential worst‑case, language specifics. |

---

#### 4.7  Final Words

LeetCode 381 may look deceptively simple, but it’s a *code‑generation* test of deep understanding.  
The **good** is the elegance of the array–hashmap pattern.  
The **bad** is the subtle bugs that hide in the swap‑and‑remove logic.  
The **ugly** is the edge cases that trip even seasoned programmers.

Showcasing a clean, well‑commented implementation – as we did above – will let you walk into a recruiter’s room and say:  
> “I can build a **multiset** that behaves like a bag and still pick a random item in O(1) – all in Java, Python, and C++.”

Good luck landing that job! 🚀  

---  

> *If you found this article useful, give it a 👍, share it on LinkedIn, or drop a comment below – let’s help the next generation of engineers crack LeetCode 381 and land their dream roles.*  

---  

> *Copyright © 2023 “Coding Interviews Demystified” – All rights reserved.*  

---  

### 📚  References & Further Reading

1. [LeetCode 381 – Insert Delete GetRandom O(1) – Duplicates allowed](https://leetcode.com/problems/insert-delete-getrandom-o1-duplicates-allowed/)  
2. [GeeksforGeeks – Randomized Set & Multiset](https://www.geeksforgeeks.org/design-a-data-structure-that-supports-insert-delete-getrandom-in-constant-time/)  
3. [InterviewBit – Constant Time Operations](https://www.interviewbit.com/question/insert-delete-get-random-o1/)

---  

Happy coding! 💻🔥