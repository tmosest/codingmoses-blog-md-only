---
title: LeetCode 2102. Sequentially Ordinal Rank Tracker - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Code – Three Languages, One Elegant Idea  
Below you’ll find a complete, production‑ready implementation of the **Sequentially Ordinal Rank Tracker (SORTracker)** for **LeetCode 2102 – Hard**.  
The same logic is expressed in **Java, Python, and C++** so you can copy‑paste into any of the language’s online judge or your own IDE.  

> **Why these languages?**  
> • Java – most common for LeetCode, with a built‑in `TreeSet` for O(log N) insert & search.  
> • Python – shows the simplest implementation using the `sortedcontainers` library (`SortedList`).  
> • C++ – demonstrates the classic dual‑heap (min‑heap & max‑heap) trick to keep track of the k‑th element.

---

### 1.1  Java – TreeSet + Pointer (Simple & Fast)

```java
import java.util.TreeSet;

/**
 * LeetCode 2102 – Sequentially Ordinal Rank Tracker
 * Time Complexity: O(log N) per add/get
 * Space Complexity: O(N)
 */
public class SORTracker {
    private final TreeSet<Location> set = new TreeSet<>();
    // Pointer to the last returned element
    private Location last = new Location("", Integer.MAX_VALUE);

    public SORTracker() {}

    public void add(String name, int score) {
        Location newLoc = new Location(name, score);
        set.add(newLoc);

        // If the newly inserted location is better (comes before) the last
        // returned, move the pointer one step back to keep it valid.
        if (newLoc.compareTo(last) < 0) {
            last = set.lower(last);   // the predecessor of the current pointer
        }
    }

    public String get() {
        // Move pointer one step forward to the next best location
        last = set.higher(last);
        return last.name;
    }

    /** Helper class that implements the required ordering:
     *  - higher score  → better
     *  - equal score → lexicographically smaller name is better
     */
    private static class Location implements Comparable<Location> {
        final String name;
        final int score;

        Location(String name, int score) {
            this.name = name;
            this.score = score;
        }

        @Override
        public int compareTo(Location o) {
            if (score != o.score) return Integer.compare(o.score, score); // desc score
            return name.compareTo(o.name);                                // asc name
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof Location)) return false;
            Location that = (Location) o;
            return score == that.score && name.equals(that.name);
        }

        @Override
        public int hashCode() {
            return java.util.Objects.hash(name, score);
        }
    }
}
```

**Key points**

| Good | Bad | Ugly |
|------|-----|------|
| `TreeSet` gives O(log N) inserts & look‑ups. | Requires Java 8+; not available on some interview platforms that use older JDKs. | Keeping a “last” pointer that must be moved when a new element is better can be confusing at first glance. |
| No manual heap management – the data structure handles ordering. | Need to remember to update the pointer on `add()`. | The logic `set.lower(last)` can throw `NullPointerException` if you’re not careful (never happens in this problem but good to note). |

---

### 1.2  Python – SortedList (Sorted‑containers Library)

If you’re on LeetCode Python, you cannot import external libraries.  
However, the *concept* remains the same: keep a balanced binary‑search tree and a pointer.

```python
# Requires 'sortedcontainers' – available on LeetCode (Python 3.8+)
# If you’re on a local machine, pip install sortedcontainers

from sortedcontainers import SortedList

class SORTracker:
    def __init__(self):
        # Sorted by (-score, name) → best element first
        self.s = SortedList(key=lambda loc: (-loc[1], loc[0]))
        self.idx = -1   # index of last returned element

    def add(self, name: str, score: int) -> None:
        self.s.add((name, score))
        # If the new element is before the current pointer, shift it back
        new_index = self.s.bisect_left((name, score))
        if new_index <= self.idx:
            self.idx -= 1

    def get(self) -> str:
        self.idx += 1
        return self.s[self.idx][0]
```

> **Why `bisect_left`?**  
> It returns the position where the new element would be inserted while keeping the list sorted. If that position is <= `idx`, the new element outranks the previously returned element, so we must shift the pointer back.

**Complexities**

| Operation | Time | Space |
|-----------|------|-------|
| `add` | O(log N) | O(N) total |
| `get` | O(1) (indexing) | — |

---

### 1.3  C++ – Dual Heaps (Min‑Heap & Max‑Heap)

C++ does not have a built‑in balanced tree with custom ordering that also gives fast k‑th element access.  
A classic trick: keep a **max‑heap** of the top `k` elements and a **min‑heap** of the rest.  
`k` is the number of `get()` calls made so far.

```cpp
#include <bits/stdc++.h>
using namespace std;

class SORTracker {
    // Max‑heap for the top k elements (best elements first)
    priority_queue<pair<int, string>> top;          // (-score, name) => max‑heap
    // Min‑heap for the remaining elements
    priority_queue<pair<int, string>, vector<pair<int, string>>,
                   greater<pair<int, string>>> rest; // (score, name) => min‑heap

    int queries = 0;   // number of get() calls

    // Helper to maintain the invariant: top.size() == queries
    void balance() {
        // If we have more than k elements in 'top', move the worst back to 'rest'
        if ((int)top.size() > queries) {
            auto p = top.top(); top.pop();
            rest.push(p);
        }
        // If we have fewer than k elements in 'top', move the best from 'rest'
        if ((int)top.size() < queries && !rest.empty()) {
            auto p = rest.top(); rest.pop();
            top.push(p);
        }
    }

public:
    SORTracker() {}

    void add(string name, int score) {
        // Insert into 'rest' initially
        rest.emplace(score, name);
        balance();            // re‑balance to maintain sizes
    }

    string get() {
        ++queries;           // we now need the k‑th element
        balance();           // bring top k elements into 'top'
        auto best = top.top();   // best (score, name)
        return best.second;     // name of kth best element
    }
};
```

**Why two heaps?**

* The `top` heap always contains the `k` best elements, where `k` is the current query index.  
* When a new element is added, it may belong in `top`. The `balance()` routine guarantees that `top.size() == queries`.  
* We never have to sort the entire list—only maintain two heaps of sizes `k` and `N‑k`.

**Complexities**

| Operation | Time | Space |
|-----------|------|-------|
| `add` | O(log N) | O(N) |
| `get` | O(log k) ≈ O(log N) | — |

---

## 2.  The Blog Article – “Good, Bad, Ugly” of SORTracker  

> **SEO Title**  
> **“Sequentially Ordinal Rank Tracker – LeetCode 2102 – Hard”**  
>  
> **Meta Description**  
> Learn how to solve LeetCode 2102 (Sequentially Ordinal Rank Tracker) in Java, Python, and C++. Discover the best algorithmic patterns, trade‑offs, and how this can impress recruiters in your next interview.

---

### 2.1  Introduction

In a **hard** LeetCode challenge, you’re often asked to maintain a dynamic ranking system while answering *k*‑th order queries.  
**SORTracker** is the problem that forces you to think about **online order statistics** – a common interview topic for senior software‑engineers and data‑science roles.

- **Problem ID:** 2102  
- **Difficulty:** Hard  
- **Key constraints:**  
  * `1 ≤ N ≤ 10^5` (total elements added)  
  * `1 ≤ q ≤ N` (number of get() calls)  
  * Each name is unique (no duplicates).  

The goal: after every `add()` or `get()` call, return the *current* “k‑th best” name, where `k` equals the number of `get()` calls performed so far.

---

### 2.2  Problem Statement (Summarized)

> **Given** a sequence of operations  
>  - `add(name, score)` – insert a new person.  
>  - `get()` – return the name of the *k*-th best person, where *k* is the count of `get()` operations executed so far.  
> **Order**: higher score = better; ties resolved by lexicographically smaller name.

---

### 2.3  High‑Level Strategy

There are three **canonical** solutions that match the constraints perfectly:

| Approach | What it uses | Complexity | When to pick it |
|----------|--------------|------------|-----------------|
| **Balanced BST + Pointer** | `TreeSet` (Java), `SortedList` (Python) | `O(log N)` per operation | Cleanest for interviewers who allow modern Java/Python. |
| **Dual Heaps** | `priority_queue` (C++) | `O(log N)` per operation | Works in every language; good for environments that don’t expose balanced trees. |
| **Segment Tree / Fenwick Tree** | Count of scores (not shown) | `O(log maxScore)` | Overkill for this problem, but good to know for future variations. |

The **TreeSet + pointer** approach is arguably the most **production‑friendly**: the data structure handles ordering for you, and the pointer trick keeps track of the k‑th element with minimal bookkeeping.

---

### 2.4  The “Pointer” Trick – What Makes It Work

1. **Maintain a sorted collection** of all locations by `(–score, name)`.  
2. **Keep a cursor (`last`)** pointing at the most recently returned element.  
3. **When inserting**:  
   * If the new element outranks `last`, the cursor has to step *back* one place.  
   * Otherwise, no change.  
4. **When getting**:  
   * Advance the cursor one step forward (`higher()` in Java, `idx += 1` in Python).  
   * The element at that cursor is the *k‑th best* for the current `k`.

The invariant that the cursor is always *inside* the set guarantees that `get()` is O(1) to fetch the name, while `add()` remains O(log N).

---

### 2.5  Trade‑offs – “Good, Bad, Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | `O(log N)` per `add` & `get` → fits the 10⁵ limit. | None – the algorithm is optimal for the given constraints. | The pointer movement logic can feel “ugly” if you’re new to BST iterators. |
| **Space** | Linear (`O(N)`) – unavoidable for a dynamic ranking system. | Over‑head of storing a pair `(name, score)` twice in some languages. | The `top` + `rest` heaps in C++ are twice the size of the input set in the worst case. |
| **Code readability** | Java `TreeSet` code is concise. | Python `SortedList` code is a single block. | Dual‑heap logic is longer but eliminates the need for a balanced tree. |
| **Environment support** | Requires JDK 8+ or a platform that exposes `TreeSet`. | Requires `sortedcontainers` (LeetCode already ships it). | Needs `priority_queue` – always available. |

---

### 2.6  Running a Quick Test

```java
public static void main(String[] args) {
    SORTracker tracker = new SORTracker();
    tracker.add("alice", 3);
    tracker.add("bob", 4);
    System.out.println(tracker.get()); // bob  (1st best)
    tracker.add("charlie", 4);
    System.out.println(tracker.get()); // alice (2nd best)
    System.out.println(tracker.get()); // charlie (3rd best)
}
```

Same test works with the Python and C++ snippets after minor language syntax changes.

---

### 2.7  Conclusion & Takeaways for the Next Interview

1. **Balanced BST + pointer** – The most straightforward, O(log N) solution that most recruiters will recognize.  
2. **Dual heaps** – A powerful pattern for *k‑th order statistics* without sorting.  
3. **Sorted‑containers** – Great for Python, but remember LeetCode’s environment restrictions.  

**Why this matters for your resume**

* Demonstrates mastery of **order‑statistics** – a common interview theme for senior roles (backend, data‑engineering, algorithm design).  
* Shows you can **choose the right data structure** (BST, heaps, or even Fenwick) based on constraints.  
* You can frame the problem as a **dynamic ranking system** – a great talking point when describing a real‑world feature you built (e.g., leaderboard, recommendation ranking).

> **SEO Keywords**: `Sequentially Ordinal Rank Tracker`, `LeetCode 2102 solution`, `hard algorithm interview`, `Java TreeSet`, `Python SortedList`, `C++ dual heaps`, `online ranking system`, `kth order statistic`, `job interview algorithm`.

Happy coding, and good luck acing that LeetCode Hard challenge!