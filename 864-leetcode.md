---
title: LeetCode 864. Shortest Path to Get All Keys - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Shortest Path to Get All Keys – 864 (LeetCode)

| Language | File name | Key Idea |
|----------|-----------|----------|
| **Java** | `Solution.java` | BFS + Bit‑masking – state = `(row, col, keys)` |
| **Python** | `solution.py` | `deque` BFS – same state representation |
| **C++** | `solution.cpp` | `queue` + `unordered_set` for visited states |

Below are full, ready‑to‑paste solutions for each language, followed by a **SEO‑optimized blog post** that explains the problem, the algorithm, and interview‑style tips.

---

## 1. Java – `Solution.java`

```java
import java.util.*;

public class Solution {

    private static class State {
        int keys;   // bitmask of collected keys
        int r, c;   // current position

        State(int keys, int r, int c) {
            this.keys = keys;
            this.r = r;
            this.c = c;
        }
    }

    public int shortestPathAllKeys(String[] grid) {
        int m = grid.length, n = grid[0].length();
        int totalKeys = 0;
        int sr = -1, sc = -1;

        // 1️⃣  Find start and count keys
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                char ch = grid[i].charAt(j);
                if (ch == '@') { sr = i; sc = j; }
                if ('a' <= ch && ch <= 'f') totalKeys++;
            }
        }

        int allKeysMask = (1 << totalKeys) - 1;
        Queue<State> q = new ArrayDeque<>();
        Set<String> visited = new HashSet<>();

        q.offer(new State(0, sr, sc));
        visited.add(0 + " " + sr + " " + sc);

        int[][] dirs = {{1,0},{-1,0},{0,1},{0,-1}};
        int steps = 0;

        // 2️⃣ BFS
        while (!q.isEmpty()) {
            int sz = q.size();
            for (int s = 0; s < sz; s++) {
                State cur = q.poll();

                if (cur.keys == allKeysMask) return steps;

                for (int[] d : dirs) {
                    int nr = cur.r + d[0];
                    int nc = cur.c + d[1];
                    if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;

                    char ch = grid[nr].charAt(nc);
                    if (ch == '#') continue;                    // wall

                    int newKeys = cur.keys;
                    if ('a' <= ch && ch <= 'f')
                        newKeys |= 1 << (ch - 'a');              // pick key

                    if ('A' <= ch && ch <= 'F' && ((newKeys >> (ch - 'A')) & 1) == 0)
                        continue;                                // locked door

                    String key = newKeys + " " + nr + " " + nc;
                    if (visited.add(key)) {                     // new state
                        q.offer(new State(newKeys, nr, nc));
                    }
                }
            }
            steps++;
        }
        return -1;   // impossible
    }
}
```

### Complexity

| Time | Space |
|------|-------|
| **O(m · n · 2ⁿ)** where `n` ≤ 6 (keys) | **O(m · n · 2ⁿ)** for the visited set |

---

## 2. Python – `solution.py`

```python
from collections import deque
from typing import List

class Solution:
    def shortestPathAllKeys(self, grid: List[str]) -> int:
        m, n = len(grid), len(grid[0])
        total_keys = 0
        sr = sc = -1

        # 1️⃣ Find start, count keys
        for r in range(m):
            for c in range(n):
                ch = grid[r][c]
                if ch == '@':
                    sr, sc = r, c
                elif 'a' <= ch <= 'f':
                    total_keys += 1

        all_keys_mask = (1 << total_keys) - 1
        q = deque([(0, sr, sc)])                 # (keys, r, c)
        visited = {(0, sr, sc)}
        dirs = [(1,0),(-1,0),(0,1),(0,-1)]
        steps = 0

        # 2️⃣ BFS
        while q:
            for _ in range(len(q)):
                keys, r, c = q.popleft()
                if keys == all_keys_mask:
                    return steps

                for dr, dc in dirs:
                    nr, nc = r + dr, c + dc
                    if not (0 <= nr < m and 0 <= nc < n): continue
                    ch = grid[nr][nc]
                    if ch == '#': continue

                    new_keys = keys
                    if 'a' <= ch <= 'f':
                        new_keys |= 1 << (ord(ch) - ord('a'))
                    if 'A' <= ch <= 'F' and not (new_keys >> (ord(ch) - ord('A')) & 1):
                        continue

                    state = (new_keys, nr, nc)
                    if state not in visited:
                        visited.add(state)
                        q.append(state)
            steps += 1
        return -1
```

### Complexity

- **Time**: `O(m · n · 2ⁿ)` – at most `O(400 · 64)` for 20×20 grid with 6 keys.  
- **Space**: `O(m · n · 2ⁿ)` – visited states.

---

## 3. C++ – `solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    struct State {
        int keys;  // bitmask
        int r, c;
        State(int k, int rr, int cc) : keys(k), r(rr), c(cc) {}
    };
public:
    int shortestPathAllKeys(vector<string>& grid) {
        int m = grid.size(), n = grid[0].size();
        int totalKeys = 0, sr = -1, sc = -1;

        // 1️⃣ Locate start and count keys
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j) {
                char ch = grid[i][j];
                if (ch == '@') { sr = i; sc = j; }
                if ('a' <= ch && ch <= 'f') ++totalKeys;
            }

        int allMask = (1 << totalKeys) - 1;
        queue<State> q;
        unordered_set<string> vis;                      // visited state hash

        q.emplace(0, sr, sc);
        vis.insert("0 " + to_string(sr) + " " + to_string(sc));

        const int dr[4] = {1,-1,0,0};
        const int dc[4] = {0,0,1,-1};
        int steps = 0;

        // 2️⃣ BFS
        while (!q.empty()) {
            int sz = q.size();
            for (int i = 0; i < sz; ++i) {
                State cur = q.front(); q.pop();

                if (cur.keys == allMask) return steps;

                for (int d = 0; d < 4; ++d) {
                    int nr = cur.r + dr[d];
                    int nc = cur.c + dc[d];
                    if (nr < 0 || nr >= m || nc < 0 || nc >= n) continue;

                    char ch = grid[nr][nc];
                    if (ch == '#') continue;                       // wall

                    int newKeys = cur.keys;
                    if ('a' <= ch && ch <= 'f')
                        newKeys |= 1 << (ch - 'a');                 // collect key

                    if ('A' <= ch && ch <= 'F' && ((newKeys >> (ch - 'A')) & 1) == 0)
                        continue;                                   // locked door

                    string key = to_string(newKeys) + " " + to_string(nr) + " " + to_string(nc);
                    if (vis.insert(key).second) {                    // new state
                        q.emplace(newKeys, nr, nc);
                    }
                }
            }
            ++steps;
        }
        return -1;   // unreachable
    }
};
```

---

## 4. Blog Post – “Mastering LeetCode 864: Shortest Path to Get All Keys”

> **Keywords**: Shortest Path to Get All Keys, LeetCode 864, BFS, bitmask, Java solution, Python solution, C++ solution, coding interview, software engineering interview, job interview prep

---

### Introduction

**LeetCode 864 – *Shortest Path to Get All Keys*** is a classic grid‑search puzzle that tests two core skills every software‑engineer interview demands:

1. **Breadth‑First Search (BFS)** – for shortest‑path exploration.
2. **Bit‑mask DP** – to compress sets of keys into a single integer.

With up to **6 keys** (`a–f`) and a moderate grid size (`≤ 20 × 20`), the algorithm is both fast *and* easy to understand – the perfect interview problem.

---

### The Problem (in one sentence)

> *Given a 2‑D grid where `@` is your start, `#` blocks movement, lowercase letters (`a–f`) are keys and uppercase letters (`A–F`) are locked doors that require the matching key, find the minimal number of moves to collect every key.*

If you can’t get all keys, return `-1`.

---

### Why BFS + Bit‑Masking Works

| Concept | Explanation |
|---------|-------------|
| **State** | `(row, col, keys)` – you must remember *where* you are *and* *what keys* you have. |
| **Bit‑mask** | Each key is a bit (`1 << index`). For ≤6 keys we need at most 64 distinct bitmasks (`2⁶`). |
| **Transition** | Move to an adjacent cell if it’s not a wall. If it contains a key, set the bit; if it’s a door, check the bit before moving. |
| **Visited** | Hash `(row, col, keys)` to avoid revisiting states. Without the keys dimension we would loop forever. |

Because BFS explores *level by level*, the first time we see `allKeysMask` we’ve found the shortest path.

---

### “Good” – What Makes This Solution Win

- **Time‑efficient**: `O(m·n·2^k)` where `k` ≤ 6, perfectly fine for LeetCode limits.  
- **Space‑efficient**: `O(m·n·2^k)` visited states – less than 400 × 64 ≈ 25k elements.  
- **Straightforward**: One clear BFS loop, a bitmask operation per move.  
- **Language‑agnostic**: The same logic works in Java, Python, C++, and even Go or Rust.

---

### “Bad” – Things to Watch Out For

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| **Wrong key mask** | Using `1 << (ch - 'a')` with the wrong base character leads to wrong bits for `d`–`f`. | Verify `ch - 'a'` (or `ord(ch) - ord('a')`). |
| **Over‑visited states** | Storing just `(row, col)` ignores key differences, causing cycles. | Include `keys` in the visited key. |
| **Large grid & many keys** | Worst case can blow up; if keys > 10 the solution slows dramatically. | Problem guarantees ≤6 keys, so it’s safe. |

---

### “Ugly” – The Subtle Complexity

- **Hash‑based visited**: Building strings `"mask r c"` or tuples can be *memory hungry* if the grid is large.  
  - *Solution*: In C++ you can use a 3‑D vector `vector<vector<vector<bool>>> visited;` or a custom `struct` hash.  
- **Handling doors before keys**: If you pick a key *after* passing a door, you must backtrack – the BFS automatically handles this, but debugging can be confusing.  
- **Multiple starting points**: LeetCode guarantees one start, but a generic solver would need to queue all `@` cells.

---

### Interview‑style Tips

| Situation | What to Say |
|-----------|-------------|
| **When asked to explain BFS** | “I’ll enqueue the start, then for each level I’ll expand all neighbours. Because the grid is unweighted, the first time I reach the goal is guaranteed to be the shortest.” |
| **When the interviewer stresses memory** | “Since we have at most 6 keys, we can encode key sets in a 6‑bit integer, drastically reducing the state space.” |
| **If the interviewer wants a solution in C++** | “I’ll use a custom hash for the struct `(r, c, mask)` and a `queue`. That avoids building strings.” |
| **If they ask about edge cases** | “I’ll treat a door as a wall if I don’t have the key, otherwise I’ll pass through. The mask ensures I never revisit a state with the same key set.” |
| **When time complexity is queried** | “It’s O(m·n·2^k) where k ≤ 6, so at most 400 × 64 operations, which is trivial for 20×20 grids.” |

---

### Final Verdict

**LeetCode 864** is *the* grid puzzle that showcases mastery of BFS + bitmask. Your ability to implement it in any of the languages you’ve practiced (Java, Python, C++) proves that you understand *search*, *state compression*, and *time‑space trade‑offs*.  

When you present the solution in an interview:

1. **Show the pseudocode** (state, transition, visited).  
2. **Explain why BFS gives shortest path**.  
3. **Highlight the bitmask trick** – “Each key is a bit, so we need only 6 bits.”  
4. **Mention constraints** – “At most 6 keys, so the state space stays tiny.”

You’ll not only solve the problem but also convince the interviewer you’re ready for real‑world graph traversal challenges.

---

### Wrap‑Up

Mastering LeetCode 864 means you’ve practiced a *core algorithmic pattern* that will appear across many coding interviews: **search + state compression**.  

Try the **Java**, **Python**, and **C++** implementations above, test edge cases, and you’ll be confident walking into any interview room.

Good luck! 🚀

---


> *Feel free to adapt the blog text to your own voice or to add code snippets for your favourite language. Happy coding!*