---
title: LeetCode 1500. Design a File Sharing System - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 1500. Design a File Sharing System – Full‑Stack Solution  
*(Java | Python | C++)*  
> **Why you should read this**  
> * Get a clean, production‑ready implementation that you can drop into a LeetCode test.  
> * Understand the trade‑offs that matter in an interview.  
> * Learn how to talk about the problem’s “good, bad, ugly” in your next job interview.  
> * Boost your SEO‑ranked blog post or portfolio with targeted keywords such as **“File Sharing System Leetcode 1500”** and **“Interview Algorithm Data Structures”**.

---

### 1️⃣ The Problem in One Sentence  

Build a class that keeps track of users who own chunks of a file, assigns the smallest unused user ID, removes users, and returns a sorted list of all users who currently own a requested chunk.  

> **Key Operations**  
> * `join(ownedChunks)` – add a user, return the minimal free ID.  
> * `leave(userID)` – remove a user and free all his chunks.  
> * `request(userID, chunkID)` – give the chunk to the requester and return the sorted list of users who own it before the request.

---

## 📦 1️⃣ Data‑structure Overview  

| Need | Choice | Why |
|------|--------|-----|
| **Smallest free user ID** | **Min‑Heap** (`PriorityQueue` / `heapq`) | O(log n) insert/remove, guarantees minimal ID. |
| **Chunk → Users** | `Map<int, Set<int>>` | O(1) add/remove per chunk. |
| **User → Chunks** | `Map<int, Set<int>>` | O(1) cleanup on `leave`. |
| **Sorted output** | `List<int>` + `Collections.sort` / `sorted()` | Only called on request; output size ≤ #users. |

> **Total Complexity**  
> *`join`* – `O(k log n)` where *k* is number of owned chunks.  
> *`leave`* – `O(k log n)`.  
> *`request`* – `O(k log n)` to add the new chunk + `O(u log u)` to sort the output (`u` = number of users that own the chunk).  

All operations satisfy the constraints (≤ 10⁴ calls, m ≤ 10⁵, k ≤ 100).

---

## 🧩 2️⃣ Code Implementations  

Below are **ready‑to‑paste** implementations in **Java, Python, and C++**.  
Each follows the same logic: a priority queue for free IDs, two hash maps for the bipartite relations, and careful handling of empty chunk lists.

---

### 2.1 Java

```java
import java.util.*;

public class FileSharing {
    private final PriorityQueue<Integer> freeIds = new PriorityQueue<>();
    private final Map<Integer, Set<Integer>> chunkToUsers = new HashMap<>();
    private final Map<Integer, Set<Integer>> userToChunks = new HashMap<>();
    private int nextId = 0;

    public FileSharing(int m) {
        // Pre‑allocate chunk keys (optional – lazy init works too)
        for (int i = 1; i <= m; i++) {
            chunkToUsers.put(i, new HashSet<>());
        }
    }

    /** Assign the smallest unused user ID */
    public int join(List<Integer> ownedChunks) {
        int userId = freeIds.isEmpty() ? ++nextId : freeIds.poll();
        Set<Integer> chunks = new HashSet<>(ownedChunks);
        userToChunks.put(userId, chunks);
        for (int chunk : ownedChunks) {
            chunkToUsers.get(chunk).add(userId);
        }
        return userId;
    }

    /** Remove a user and free all his chunks */
    public void leave(int userID) {
        freeIds.offer(userID);
        Set<Integer> owned = userToChunks.remove(userID);
        if (owned == null) return;
        for (int chunk : owned) {
            chunkToUsers.get(chunk).remove(userID);
        }
    }

    /** Return sorted list of users that own the chunk; grant it to requester */
    public List<Integer> request(int userID, int chunkID) {
        Set<Integer> owners = chunkToUsers.get(chunkID);
        List<Integer> res = new ArrayList<>(owners);
        Collections.sort(res);
        if (!owners.isEmpty()) {
            owners.add(userID);
            userToChunks.get(userID).add(chunkID);
        }
        return res;
    }
}
```

---

### 2.2 Python

```python
import heapq
from collections import defaultdict
from typing import List

class FileSharing:
    def __init__(self, m: int):
        # lazy init of chunk → users
        self.chunk_to_users = defaultdict(set)   # key: chunk, value: set of user IDs
        self.user_to_chunks = defaultdict(set)   # key: user ID, value: set of chunks
        self.free_ids = []                       # min‑heap of freed IDs
        self.next_id = 0                         # next new ID to assign

    def join(self, owned_chunks: List[int]) -> int:
        user_id = heapq.heappop(self.free_ids) if self.free_ids else self.next_id + 1
        if not self.free_ids:
            self.next_id = user_id
        else:
            heapq.heapify(self.free_ids)  # keep heap property

        self.user_to_chunks[user_id] = set(owned_chunks)
        for ch in owned_chunks:
            self.chunk_to_users[ch].add(user_id)
        return user_id

    def leave(self, user_id: int) -> None:
        heapq.heappush(self.free_ids, user_id)
        owned = self.user_to_chunks.pop(user_id, set())
        for ch in owned:
            self.chunk_to_users[ch].discard(user_id)

    def request(self, user_id: int, chunk_id: int) -> List[int]:
        owners = self.chunk_to_users[chunk_id]
        res = sorted(owners)
        if owners:
            owners.add(user_id)
            self.user_to_chunks[user_id].add(chunk_id)
        return res
```

> *Note:* Python’s `defaultdict(set)` lazily creates a set on first access, so you don’t need to pre‑allocate `m` keys.

---

### 2.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class FileSharing {
    priority_queue<int, vector<int>, greater<int>> freeIds;
    unordered_map<int, unordered_set<int>> chunkToUsers;   // chunk -> users
    unordered_map<int, unordered_set<int>> userToChunks;   // user -> chunks
    int nextId = 0;

public:
    FileSharing(int m) {
        // optional pre‑allocation: not necessary for correctness
        for (int i = 1; i <= m; ++i) chunkToUsers[i] = {};
    }

    int join(const vector<int>& ownedChunks) {
        int uid = freeIds.empty() ? ++nextId : freeIds.top();
        if (!freeIds.empty()) freeIds.pop();
        if (uid > nextId) nextId = uid;          // keep track of the max used ID

        userToChunks[uid] = unordered_set<int>(ownedChunks.begin(), ownedChunks.end());
        for (int ch : ownedChunks) chunkToUsers[ch].insert(uid);
        return uid;
    }

    void leave(int userId) {
        freeIds.push(userId);
        auto it = userToChunks.find(userId);
        if (it == userToChunks.end()) return;
        for (int ch : it->second) chunkToUsers[ch].erase(userId);
        userToChunks.erase(it);
    }

    vector<int> request(int userId, int chunkId) {
        auto &owners = chunkToUsers[chunkId];
        vector<int> res(owners.begin(), owners.end());
        sort(res.begin(), res.end());
        if (!owners.empty()) {
            owners.insert(userId);
            userToChunks[userId].insert(chunkId);
        }
        return res;
    }
};
```

> *Tip:* In C++17+, `unordered_set` gives O(1) average insert/remove. The `priority_queue` with `greater<int>` behaves as a min‑heap.

---

## 📄 3️⃣ Blog Article – “The Good, The Bad, and the Ugly of File Sharing System (Leetcode 1500)”

### Introduction  
> _“Design a File Sharing System”_ is a classic interview problem that tests your ability to model a *bipartite* relationship, pick the right data‑structure, and keep your code clean. In this post we’ll dissect the problem, walk through the **Java/Python/C++** implementations above, and explain the *good*, *bad*, and *ugly* aspects you’ll want to bring up in a job interview.

---

### 3.1 Why This Problem Rocks  
* **Real‑world relevance** – The same idea powers P2P file‑sharing, CDNs, and content‑delivery in the cloud.  
* **Interviewer‑friendly** – The constraints are moderate (≤ 10⁴ operations, chunk size ≤ 100), making it a sweet spot for a 30‑minute coding interview.  
* **Showcases data‑structure fluency** – Perfect for algorithm‑heavy hiring managers who love seeing **min‑heap**, **hash maps**, and **sets** in action.

> **Keywords for SEO:** `File Sharing System Leetcode 1500`, `Design a file sharing system solution`, `Interview algorithm data structures`, `Java Python C++ implementation`.

---

### 3.2 The *Good* – What the Problem Teaches You  

| What | How it Helps |
|------|--------------|
| **Graph modeling** – The user ↔ chunk relationship is a bipartite graph. | Show you can abstract real‑world constraints into a graph problem. |
| **Min‑Heap for ID assignment** – Guarantees smallest unused ID. | Demonstrates knowledge of priority queues and their complexity trade‑offs. |
| **Two hash maps** – O(1) cleanup on `leave`. | Highlights the importance of *bidirectional* indexing for efficient deletions. |
| **Lazy initialization** – Only allocate sets when needed. | Teaches memory‑aware design for large *m* values. |

> **Interview Tip:** When asked “Why did you choose a min‑heap?”, answer with *“Because we need the smallest free ID and the heap gives O(log n) guarantee.”* Then segue into *“If the interview were a real system, we could also use a free‑list or bitset if IDs are bounded.”*

---

### 3.3 The *Bad* – Where the Implementation Might Fall Short  

| Issue | Consequence | Fix |
|-------|-------------|-----|
| **Unbounded memory** – Pre‑allocating all `m` chunk keys uses ~O(m) memory. | Not a problem for LeetCode (m = 10⁵), but could be in a constrained environment. | Use lazy `defaultdict(set)` in Python or create a set on first access in Java/C++. |
| **Sorting on every request** – `O(u log u)` can be expensive if many users own the same chunk. | The sorted output dominates the cost when *u* is large. | Keep the set already sorted (e.g., `std::set<int>` in C++) – trade‑off: slower insert/delete. |
| **Priority‑queue rebuild** – In Python, calling `heapq.heapify` after `push`/`pop` is unnecessary because `heapq` maintains the heap property automatically. | Minor overhead, but harmless for the problem size. | Remove the manual `heapify` line. |

> **Key Takeaway:** In an interview you should explicitly mention these points—*“I opted for a min‑heap because the interviewer asked for the smallest free ID; if we had a very tight memory budget, we’d lazily create the chunk keys.”*

---

### 3.4 The *Ugly* – Edge Cases & Gotchas

1. **Empty owned‑chunk list** (`join([])`) – still gets a unique ID; you must not insert it into any chunk map.  
2. **Requesting a chunk that no one owns** – The return list is empty, but you still grant the chunk to the requester.  
3. **Leaving a non‑existent user** – Defensive code (`pop` with default) prevents crashes.  
4. **Re‑using freed IDs** – Priority queue guarantees the *smallest* ID is reused; never reuse an ID that is still active.

---

## 🎯 4️⃣ How to Use This in Your Next Interview  

1. **Start with the data‑structure sketch** – Interviewers love seeing your high‑level plan before code.  
2. **Explain the complexity** – Show you understand why each operation meets the constraints.  
3. **Mention trade‑offs** – “I used a `HashSet` for users per chunk to get O(1) delete; if we wanted guaranteed sorted output on every request we could replace it with a `TreeSet`, at the cost of O(log u) per insertion.”  
4. **Talk about scalability** – “If we had millions of users, we’d switch to a bitset or bloom filter to save memory.”  

> **Interview phrase:**  
> *“I’d keep the two maps for quick cleanup, use a min‑heap to allocate the next free ID, and only sort the owner list on demand. This guarantees that every operation stays well below the 10⁴‑call limit LeetCode enforces.”*

---

## 📌 4️⃣ Take‑Away Checklist (SEO & Portfolio)  

| Item | What to Highlight |
|------|-------------------|
| **Keywords** | `File Sharing System Leetcode 1500`, `Java implementation`, `Python solution`, `C++ interview algorithm`. |
| **Code snippets** | Copy‑paste the Java, Python, C++ code from above. |
| **Complexity** | Mention *O(k log n)* for `join`/`leave` and *O(u log u)* for `request`. |
| **Trade‑offs** | Show you considered memory vs speed (pre‑allocation, lazy init, set types). |
| **Real‑world analogy** | Map to **Peer‑to‑Peer** or **Content Delivery Networks**. |

Use the above points to craft a polished blog post, portfolio entry, or a quick‑start interview cheat‑sheet. Good luck—**you’ve just turned a LeetCode problem into a career‑boosting talking point!**