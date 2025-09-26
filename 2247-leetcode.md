---
title: LeetCode 2247. Maximum Cost of Trip With K Highways - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🏆 Maximum Cost of Trip With K Highways – The Good, the Bad, and the Ugly  
**LeetCode 2247 – Hard**  

> **Keywords**: LeetCode 2247, Maximum Cost of Trip, K Highways, dynamic programming, bitmask DP, DFS+memo, Java, Python, C++, interview problem, graph algorithms  

---  

## 1️⃣ Problem Recap  

You are given an undirected weighted graph with `n` cities (`0 … n‑1`) and a list of `highways`.  
Each highway is `[u, v, w]` – a bidirectional road that costs `w` to drive.  

You want to make a trip that

* **exactly uses `k` highways** (i.e. traverses `k` edges),  
* **never visits a city twice** (the path must be simple),  
* can start from any city.  

Return the *maximum total toll* you can pay on such a trip, or `-1` if it is impossible.

```
n          ≤ 15
|highways| ≤ 50
k          ≤ 50
```

The constraints give you a hint: `n` is tiny, so a **bitmask** is a perfect fit.  

---

## 2️⃣ The Good – Why a Bitmask DP Works

* **State Representation**  
  * `mask` – a 15‑bit integer where bit `i` is `1` if city `i` has already been visited.  
  * `last` – the city where the current path ends.  

  `dp[mask][last]` = maximum cost of a simple path that visits exactly the cities in `mask` and ends in `last`.

* **Initialization**  
  Every city can be a start point.  
  `dp[1 << i][i] = 0` for all `i`.  

* **Transition**  
  From a state `(mask, last)` we may go to any adjacent city `next` that is *not* in `mask`:

  ```
  newMask = mask | (1 << next)
  dp[newMask][next] = max(dp[newMask][next],
                          dp[mask][last] + cost(last, next))
  ```

* **Answer**  
  We need a path with **exactly `k` edges**, i.e. `k+1` visited cities.  
  So among all masks with `popcount(mask) == k+1`, take the maximum value of `dp[mask][*]`.  

* **Complexity**  
  *States* – `n * 2^n`  ≤ 15 × 32 768 ≈ 0.5 M.  
  *Transitions* – each state expands to at most `n-1` neighbors, still tiny.  

  ```
  Time   O(n^2 · 2^n)      (~ few million operations)
  Memory O(n · 2^n)        (~ 2 MB)
  ```

  Both are well within limits for the hard test cases.  

---

## 3️⃣ The Bad – Why Not DFS + Memo or Dijkstra?

1. **DFS + Memo**  
   *Requires a `mask` key, so you end up storing `dp[vertex][mask]` – essentially the same DP but implemented in a recursive style.  
   The recursion depth can be up to `k` (≤ 50) – no stack overflow, but the memo table is still large and the constant factors higher.*

2. **Dijkstra‑like Search**  
   *If you treat each state `(mask, last)` as a node in a huge graph and run a priority queue, the algorithm is correct but overkill.  
   The queue will contain thousands of entries and the overhead of heap operations dominates the run time.*

The bottom line: **plain bitmask DP is both simpler and faster**.

---

## 4️⃣ The Ugly – Edge Cases & Pitfalls

| Pitfall | What goes wrong | How to avoid |
|---------|-----------------|--------------|
| `k >= n` | You can’t visit more than `n-1` edges without revisiting a city. | Immediately return `-1`. |
| Duplicate highways | Not allowed by problem statement, but if present it could break the DP. | Build adjacency lists *without* deduplication or simply ignore duplicates. |
| Large toll values | Using `int` is safe (`k*100 <= 5000`), but if you change constraints use `long`. | Use `int` or `long` consistently. |
| Wrong mask population count | Forget to check `popcount(mask) == k+1`. | Use `Integer.bitCount(mask)` (Java), `bin(mask).count('1')` (Python), `__builtin_popcount` (C++). |

---

## 5️⃣ Reference Implementations  

Below are clean, self‑contained solutions for **Java, Python, and C++**.  
Each follows the exact DP described above.

> *All codes assume the input is already parsed into variables: `int n`, `int[][] highways`, `int k`.*

---

### 5.1 Java 17

```java
import java.util.*;

public class Solution {
    // adjacency list: list of neighbors with weights
    private static class Edge {
        final int to, w;
        Edge(int to, int w) { this.to = to; this.w = w; }
    }

    public int maximumCost(int n, int[][] highways, int k) {
        if (k >= n) return -1;          // impossible to have simple path of length k

        // build graph
        List<List<Edge>> g = new ArrayList<>(n);
        for (int i = 0; i < n; i++) g.add(new ArrayList<>());
        for (int[] e : highways) {
            g.get(e[0]).add(new Edge(e[1], e[2]));
            g.get(e[1]).add(new Edge(e[0], e[2]));
        }

        int states = 1 << n;
        int[][] dp = new int[states][n];
        for (int[] row : dp) Arrays.fill(row, Integer.MIN_VALUE);

        // start from every city
        for (int v = 0; v < n; v++) dp[1 << v][v] = 0;

        // DP over masks
        for (int mask = 1; mask < states; mask++) {
            // number of visited cities = popcount(mask)
            if (Integer.bitCount(mask) > k + 1) continue; // we only need up to k+1
            for (int last = 0; last < n; last++) {
                int curVal = dp[mask][last];
                if (curVal == Integer.MIN_VALUE) continue;
                for (Edge e : g.get(last)) {
                    int nxt = e.to;
                    if ((mask & (1 << nxt)) != 0) continue; // already visited
                    int newMask = mask | (1 << nxt);
                    dp[newMask][nxt] = Math.max(dp[newMask][nxt], curVal + e.w);
                }
            }
        }

        int best = -1;
        int targetMaskBits = k + 1;
        for (int mask = 1; mask < states; mask++) {
            if (Integer.bitCount(mask) != targetMaskBits) continue;
            for (int v = 0; v < n; v++) {
                best = Math.max(best, dp[mask][v]);
            }
        }
        return best;
    }
}
```

---

### 5.2 Python 3

```python
import sys
from collections import defaultdict

def maximumCost(n: int, highways: list[list[int]], k: int) -> int:
    if k >= n:
        return -1

    # adjacency list
    g = [[] for _ in range(n)]
    for u, v, w in highways:
        g[u].append((v, w))
        g[v].append((u, w))

    states = 1 << n
    # dp[mask][v] = max cost to reach v having visited 'mask'
    dp = [[-1] * n for _ in range(states)]
    for v in range(n):
        dp[1 << v][v] = 0

    for mask in range(1, states):
        if bin(mask).count('1') > k + 1:
            continue
        for last in range(n):
            cur = dp[mask][last]
            if cur == -1:
                continue
            for nxt, w in g[last]:
                if mask & (1 << nxt):
                    continue
                new_mask = mask | (1 << nxt)
                dp[new_mask][nxt] = max(dp[new_mask][nxt], cur + w)

    best = -1
    target = k + 1
    for mask in range(states):
        if bin(mask).count('1') != target:
            continue
        for v in range(n):
            best = max(best, dp[mask][v])

    return best
```

---

### 5.3 C++ 17

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Edge {
    int to, w;
    Edge(int to, int w) : to(to), w(w) {}
};

int maximumCost(int n, vector<vector<int>>& highways, int k) {
    if (k >= n) return -1;

    // adjacency list
    vector<vector<Edge>> g(n);
    for (auto& e : highways) {
        g[e[0]].push_back(Edge(e[1], e[2]));
        g[e[1]].push_back(Edge(e[0], e[2]));
    }

    int states = 1 << n;
    vector<vector<int>> dp(states, vector<int>(n, INT_MIN));

    // start from every city
    for (int v = 0; v < n; ++v) dp[1 << v][v] = 0;

    for (int mask = 1; mask < states; ++mask) {
        if (__builtin_popcount(mask) > k + 1) continue;
        for (int last = 0; last < n; ++last) {
            int cur = dp[mask][last];
            if (cur == INT_MIN) continue;
            for (auto &e : g[last]) {
                int nxt = e.to;
                if (mask & (1 << nxt)) continue;
                int newMask = mask | (1 << nxt);
                dp[newMask][nxt] = max(dp[newMask][nxt], cur + e.w);
            }
        }
    }

    int best = -1;
    int target = k + 1;
    for (int mask = 1; mask < states; ++mask) {
        if (__builtin_popcount(mask) != target) continue;
        for (int v = 0; v < n; ++v)
            best = max(best, dp[mask][v]);
    }
    return best;
}
```

---

## 6️⃣ Testing the Code  

```python
# Sample Test
if __name__ == "__main__":
    n = 4
    highways = [[0, 1, 5], [1, 2, 2], [2, 3, 3], [0, 2, 4]]
    k = 2
    print(maximumCost(n, highways, k))   # → 8
```

The test above corresponds to a path `0 → 2 → 3` (cost 4 + 3 = 7) or `1 → 0 → 2` (5 + 4 = 9) – the maximum is `9`.  
You can adjust the example to see the algorithm in action.

---

## 7️⃣ Why This Solution Gets the ⭐️ in Your Interview

* **Graph + DP + Bitmask** – classic interview patterns that recruiters love.  
* **Time & Memory** – meets the “hard” constraints with flying colors.  
* **Clean Code** – no extra libraries, just a single loop over `mask` and `last`.  
* **Language‑agnostic** – you can hand‑write the same idea in Java, Python, or C++ in a minute.

Feel free to paste the snippets into your favorite IDE, tweak the input parsing, or even add a small driver program to read from `stdin`.  

---

## 8️⃣ Take‑away Checklist for Interviews

1. **Read the statement carefully** – notice the *exactly* `k` edges and *no revisits*.  
2. **Spot the tiny `n`** – it hints at bitmask DP.  
3. **Define the DP state** – `mask` + `last`.  
4. **Initialize, transition, and answer** – as shown.  
5. **Handle edge cases** (`k >= n`, empty highways, etc.).  
6. **Explain your complexity** – recruiters love when you talk about `O(n^2·2^n)` and memory usage.  

Good luck, and enjoy crushing LeetCode 2247! 🚀