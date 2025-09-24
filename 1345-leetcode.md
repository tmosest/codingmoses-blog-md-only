---
title: LeetCode 1345. Jump Game IV - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Jump Game IV – Problem & “Good, Bad, Ugly” Overview  

|  |  |
|---|---|
| **Problem** | You are standing at the first index of an integer array `arr`. In one move you may jump to `i+1`, `i-1`, or any index `j` such that `arr[i] == arr[j]`. Find the minimum number of moves required to reach the last index. |
| **Constraints** | `1 ≤ arr.length ≤ 5·10⁴`, `-10⁸ ≤ arr[i] ≤ 10⁸` |
| **Key Observation** | The problem is a shortest‑path problem on an implicit graph – each array position is a node, and edges exist between consecutive indices and between equal‑value indices. A breadth‑first search (BFS) guarantees the minimal number of steps. |
| **Good** | • BFS visits each node exactly once.  <br>• O(n) time & O(n) extra space – optimal for the given constraints.  <br>• Simple to reason about & easy to implement. |
| **Bad** | • Naïve implementation that keeps visiting the same equal‑value indices over and over leads to O(n²) or worse.  <br>• Some people forget to mark the value’s list as “processed” after the first visit, causing a time‑limit‑exceeded error. |
| **Ugly** | • Using a `HashSet` of `int[]` as keys, or converting the array to a string for hashing – both are unnecessary and slow.  <br>• Relying on recursion or DFS can cause stack overflow for large inputs. |

---

## 2.  Solutions  

Below are clean, fully‑commented implementations in **Java, Python, and C++**.  
All three share the same BFS strategy:  

1. Build a `Map<value, List<indices>>`.  
2. Use a queue for BFS; store the current index and the number of moves taken.  
3. When visiting a node, enqueue its `i+1`, `i‑1`, and all equal‑value indices.  
4. Immediately **clear** the list for that value – this guarantees each list is processed at most once.  

### 2.1 Java –  `Solution.java`  

```java
import java.util.*;

/**
 * 1345. Jump Game IV
 * BFS – O(n) time, O(n) space
 */
class Solution {
    public int minJumps(int[] arr) {
        int n = arr.length;
        if (n == 1) return 0;          // already at the last index

        // 1. Build map from value to all indices having that value
        Map<Integer, List<Integer>> valueIndices = new HashMap<>();
        for (int i = 0; i < n; i++) {
            valueIndices.computeIfAbsent(arr[i], k -> new ArrayList<>()).add(i);
        }

        // 2. BFS structures
        boolean[] visited = new boolean[n];
        visited[0] = true;
        Queue<Integer> q = new ArrayDeque<>();
        q.offer(0);                    // start from index 0

        int moves = 0;
        while (!q.isEmpty()) {
            int levelSize = q.size();   // nodes at the current distance
            for (int s = 0; s < levelSize; s++) {
                int idx = q.poll();

                // Check all three possible destinations
                // a) i+1
                if (idx + 1 == n - 1) return moves + 1;
                if (idx + 1 < n && !visited[idx + 1]) {
                    visited[idx + 1] = true;
                    q.offer(idx + 1);
                }

                // b) i-1
                if (idx - 1 == n - 1) return moves + 1;
                if (idx - 1 >= 0 && !visited[idx - 1]) {
                    visited[idx - 1] = true;
                    q.offer(idx - 1);
                }

                // c) All indices j where arr[idx] == arr[j]
                List<Integer> sameValIdx = valueIndices.get(arr[idx]);
                for (int j : sameValIdx) {
                    if (j == n - 1) return moves + 1;
                    if (!visited[j]) {
                        visited[j] = true;
                        q.offer(j);
                    }
                }
                // Important: clear the list to avoid revisiting these edges
                sameValIdx.clear();
            }
            moves++;                    // finished processing current level
        }

        return -1; // should never happen for a valid input
    }
}
```

---

### 2.2 Python –  `solution.py`  

```python
from collections import defaultdict, deque
from typing import List

class Solution:
    def minJumps(self, arr: List[int]) -> int:
        n = len(arr)
        if n == 1:
            return 0

        # Build value → indices mapping
        val_to_idx = defaultdict(list)
        for i, v in enumerate(arr):
            val_to_idx[v].append(i)

        visited = [False] * n
        visited[0] = True
        q = deque([0])          # BFS queue holds indices

        moves = 0
        while q:
            for _ in range(len(q)):
                idx = q.popleft()

                # Jump to idx+1
                if idx + 1 == n - 1:
                    return moves + 1
                if idx + 1 < n and not visited[idx + 1]:
                    visited[idx + 1] = True
                    q.append(idx + 1)

                # Jump to idx-1
                if idx - 1 == n - 1:
                    return moves + 1
                if idx - 1 >= 0 and not visited[idx - 1]:
                    visited[idx - 1] = True
                    q.append(idx - 1)

                # Jump to all equal-value indices
                for j in val_to_idx[arr[idx]]:
                    if j == n - 1:
                        return moves + 1
                    if not visited[j]:
                        visited[j] = True
                        q.append(j)

                # Important: never revisit this value again
                val_to_idx[arr[idx]].clear()

            moves += 1

        return -1
```

---

### 2.3 C++ –  `Solution.cpp`  

```cpp
#include <bits/stdc++.h>
using namespace std;

/*
 * 1345. Jump Game IV
 * BFS – O(n) time, O(n) space
 */
class Solution {
public:
    int minJumps(vector<int>& arr) {
        int n = arr.size();
        if (n == 1) return 0;

        // 1. Map from value to list of indices
        unordered_map<int, vector<int>> mp;
        for (int i = 0; i < n; ++i)
            mp[arr[i]].push_back(i);

        vector<bool> visited(n, false);
        queue<int> q;
        q.push(0);
        visited[0] = true;

        int moves = 0;
        while (!q.empty()) {
            int sz = q.size();
            for (int _ = 0; _ < sz; ++_) {
                int idx = q.front(); q.pop();

                // idx+1
                if (idx + 1 == n - 1) return moves + 1;
                if (idx + 1 < n && !visited[idx + 1]) {
                    visited[idx + 1] = true;
                    q.push(idx + 1);
                }

                // idx-1
                if (idx - 1 == n - 1) return moves + 1;
                if (idx - 1 >= 0 && !visited[idx - 1]) {
                    visited[idx - 1] = true;
                    q.push(idx - 1);
                }

                // All equal‑value indices
                auto &vec = mp[arr[idx]];
                for (int j : vec) {
                    if (j == n - 1) return moves + 1;
                    if (!visited[j]) {
                        visited[j] = true;
                        q.push(j);
                    }
                }
                vec.clear();          // processed once
            }
            ++moves;
        }
        return -1;  // unreachable – never triggered for a valid input
    }
};
```

---

## 3.  Blog Article – “Jump Game IV: Mastering BFS for LeetCode Interviews”

> **Title**:  
> **Jump Game IV – Leetcode 1345**: Optimal Java / Python / C++ BFS Solution  

> **Meta Description**:  
> Discover the fastest BFS solution to LeetCode’s *Jump Game IV*. Learn Java, Python, and C++ implementations, optimization tricks, and interview‑ready insights.  

---

### 3.1 Introduction  

If you’re preparing for a technical interview or tackling LeetCode’s “Jump Game IV” (problem #1345), you’ll quickly realize that the naive “jump to every equal value” approach blows up in time. The secret is to treat the array as a graph and use **Breadth‑First Search (BFS)** to find the shortest path. In this article we walk through the algorithm, illustrate why it works, and provide clean code in **Java, Python, and C++**.

> **Keywords**: `Jump Game IV`, `Leetcode 1345`, `BFS`, `minimum jumps`, `array jumps`, `Java solution`, `Python solution`, `C++ solution`, `competitive programming`, `interview coding`.

---

### 3.2 Problem Statement (Re‑written for Clarity)

You are given a one‑dimensional array of integers `arr`.  
You start at index `0` and wish to reach the last index (`arr.length-1`).  
In **one step** you can:

1. Jump to `i + 1` (if it exists).  
2. Jump to `i - 1` (if it exists).  
3. Jump to any index `j` such that `arr[i] == arr[j]`.

Return the **minimum number of steps** required to reach the last index.  
If the array length is `1`, the answer is `0` (you’re already there).

---

### 3.3 Example Walk‑through

| `arr` | `i` | `i-1` | `i+1` | equal indices | Notes |
|-------|-----|-------|-------|---------------|-------|
| `[100, -23, -23, 404, 100, 23, 23, 23]` | 0 | – | 1 | 4 | Jump from 0 → 4 in one step (same value). |
| From 4, you can go to 5 or any index with value 100. | 4 | 3 | 5 | 0 | etc. |

The optimal path for this array is: `0 → 4 → 5 → 6 → 7` → **4 steps**.

---

### 3.4 Algorithm Design

1. **Graph Model**  
   - Nodes: array indices `0 … n-1`.  
   - Edges:  
     * `i ↔ i+1` (if inside bounds).  
     * `i ↔ j` whenever `arr[i] == arr[j]`.  

   This graph is **unweighted**, so BFS gives the shortest path length.

2. **Pre‑processing**  
   Build a hash map: `value → list of all indices having that value`.  
   Complexity: `O(n)`.

3. **BFS Execution**  
   * Keep a `queue<int>` (or `deque` in Python/C++).  
   * Keep a `visited[n]` array to avoid revisiting nodes.  
   * For each popped index `i`, enqueue:
     * `i-1` (if valid and not visited).  
     * `i+1` (if valid and not visited).  
     * Every index `j` in `valueIndices[arr[i]]` (except `i`).  
   * **Clear** `valueIndices[arr[i]]` after the first visit – this guarantees each equal‑value edge list is processed once.

4. **Level Count**  
   Track how many levels we’ve expanded (`moves`).  
   When we enqueue a destination that is the last index, return `moves+1`.

4. **Termination**  
   Since all edges are processed, the algorithm always terminates with an answer.

---

### 3.5 Why “Clear After First Visit” is Critical

Without clearing the list of equal indices, each node would attempt to jump to **all** equal nodes on every visit, leading to an `O(n^2)` blow‑up.  
By clearing after the first time we traverse a particular value, we cut down the number of edges we explore to **at most `n`**.  
This is the main optimization that turns a brute‑force solution into a fast `O(n)` algorithm.

---

### 3.6 Complexity Analysis

| Step | Complexity |
|------|------------|
| Building hash map | `O(n)` |
| BFS traversal | Each node visited once, each edge examined at most once → `O(n)` |
| Total | **`O(n)` time** and **`O(n)` space** |

This meets the requirements for interview‑level constraints.

---

### 3.7 Implementation Highlights

#### Java
- Uses `ArrayDeque` (faster than `LinkedList` for queue).  
- `ArrayList` for mapping; `clear()` is called to avoid re‑processing.

#### Python
- `defaultdict(list)` stores indices.  
- `deque` for O(1) pop/append operations.  
- List clearing with `clear()`.

#### C++
- `unordered_map<int, vector<int>>` gives average‑case O(1) lookups.  
- `vector<bool>` for visited flags.  
- `queue<int>` (or `std::deque` if you prefer) for BFS.

---

### 3.8 Common Pitfalls & Fixes

| Pitfall | Fix |
|---------|-----|
| **Re‑adding same‑value edges repeatedly** | After visiting index `i`, call `mp[arr[i]].clear()` or `val_to_idx[arr[i]].clear()`. |
| **Missing bounds checks** | Always check `i-1 >= 0` and `i+1 < n` before pushing. |
| **Not using visited array** | Use a boolean array to skip already processed nodes; otherwise you’ll get infinite loops. |
| **Returning before level increments** | In BFS, return `moves+1` *after* processing the level, not immediately after popping, unless you’ve found the target. |

---

### 3.9 Takeaway for Interviews

* Treat arrays as graphs.  
* Unweighted shortest‑path problems → BFS.  
* Pre‑process to collapse multiple edges (clear after first visit).  
* Maintain visited flags to avoid re‑exploration.  
* Keep level counter to compute distance.

Mastering this pattern will help you solve other LeetCode problems like “Jump Game” (problems 55, 1302) and graph‑based puzzles.

---

### 3.10 Final Thoughts

The BFS solution to *Jump Game IV* is elegant and runs in linear time, making it both efficient and easy to explain during a technical interview. Whether you’re coding in Java, Python, or C++, the key idea remains: **process equal‑value edges only once**.  
Feel free to copy the code snippets above into your local editor, run tests, and tweak as needed. Good luck slashing your LeetCode scores!

---

### 3.11 Further Reading

- [LeetCode 1345 – Jump Game IV on Discuss](https://leetcode.com/problems/jump-game-iv/)
- [Understanding BFS in Depth – YouTube Series by “The Coding Train”]
- [Graph Theory in Programming – Book by Mark Allen Weiss]

---

### 3.12 End of Article

---

> **End of Blog**  
> (Feel free to share your own solutions, add comments, or ask questions. Happy coding!)

---

**There you have it**: a complete, interview‑ready walkthrough and code for LeetCode’s *Jump Game IV*. The BFS strategy not only ensures optimal performance but also demonstrates a powerful pattern applicable to many other shortest‑path challenges. Happy coding!