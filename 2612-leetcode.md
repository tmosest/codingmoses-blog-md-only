---
title: LeetCode 2612. Minimum Reverse Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 📌 2612 – Minimum Reverse Operations  
**Hard | LeetCode | Java | Python | C++ | BFS | TreeSet**

> **Problem**  
> We have an array `arr` of length `n` that contains all zeros except a single `1` at index `p`.  
> We may reverse any sub‑array of fixed length `k` **only if** the sub‑array does **not** contain any banned index from the array `banned`.  
> For every index `i (0 … n-1)` return the minimum number of reversals needed to bring the `1` to `i`; if it’s impossible return `-1`.  

> **Constraints**

| | |
|---|---|
| `1 ≤ n ≤ 10^5` | `0 ≤ p < n` |
| `0 ≤ banned.length < n` | `1 ≤ k ≤ n` |
| All `banned[i]` are distinct and never equal to `p` |

---

## 🚀  Solution Overview

1. **Graph view** – Every index `x` is a node.  
   From node `x` we can jump to any node `y` that can be reached by a *single* reversal that contains `x`.  
2. **Reachable positions** – If we reverse a sub‑array of length `k` that contains `x`, the new position of `1` is at  
   `y = l + (r - x)` where `[l, r]` is the sub‑array.  
   The set of all reachable indices is a continuous range `[min, max]` of the **same parity** as `x`.  
   *Why same parity?*  
   `y - x = (r - l) - 2*(x-l)` → difference is even, therefore `x` and `y` have equal parity.
3. **BFS** – Starting from `p`, explore all indices reachable in 0 moves, then in 1 move, …  
   The first time we reach a node we have found its minimal distance.  
4. **Avoiding banned / visited** –  
   * Maintain two sorted containers (`TreeSet` in Java / `std::set` in C++ / `bisect` + list in Python) – one for every parity.  
   * Initially insert all non‑banned indices into the corresponding set.  
   * When we discover the range `[min, max]` for the current node we iterate through the set and take all indices inside the range, enqueue them and erase them from the set (they are now visited).  
5. **Complexity**  
   *Time* – `O(n log n)` (every node removed once, each removal costs `log n` in a balanced BST).  
   *Space* – `O(n)` for the two sets + queue + answer array.

---

## 🛠️  Code

Below you’ll find a clean, language‑agnostic implementation in **Java**, **Python** and **C++**.  
All three use the same BFS + parity‑based range idea.

---

### Java

```java
import java.util.*;

class Solution {
    public int[] minReverseOperations(int n, int p, int[] banned, int k) {
        // ---- 1. Prepare two parity sets --------------------------------
        TreeSet<Integer> even = new TreeSet<>();
        TreeSet<Integer> odd  = new TreeSet<>();

        boolean[] skip = new boolean[n];
        for (int b : banned) skip[b] = true;

        for (int i = 0; i < n; i++) {
            if (skip[i] || i == p) continue;          // banned or start
            (i % 2 == 0 ? even : odd).add(i);
        }

        // ---- 2. BFS -----------------------------------------------------
        int[] ans = new int[n];
        Arrays.fill(ans, -1);
        Queue<Integer> q = new ArrayDeque<>();
        q.add(p);
        ans[p] = 0;

        int moves = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            while (size-- > 0) {
                int cur = q.poll();

                // --- compute reachable range [min, max] -----------------
                int min, max;
                if (cur < k - 1) {                     // close to left border
                    min = (k - 1) - cur;
                } else {
                    min = cur - k + 1;
                }

                int rev = (n - 1) - cur;               // mirror of cur
                if (rev < k - 1) {                     // close to right border
                    max = (k - 1) - rev;
                } else {
                    max = rev - k + 1;
                }
                max = (n - 1) - max;                   // back to normal

                // --- choose the correct parity set --------------------
                TreeSet<Integer> set = (min % 2 == 0) ? even : odd;
                Integer nxt = set.ceiling(min);
                while (nxt != null && nxt <= max) {
                    q.add(nxt);
                    ans[nxt] = ans[cur] + 1;
                    set.remove(nxt);
                    nxt = set.ceiling(min);
                }
            }
            moves++;
        }
        return ans;
    }
}
```

---

### Python

```python
from collections import deque
from bisect import bisect_left

class Solution:
    def minReverseOperations(self, n: int, p: int, banned: list[int], k: int) -> list[int]:
        # 1. two parity lists (sorted)
        banned_set = set(banned)
        odd, even = [], []

        for i in range(n):
            if i in banned_set or i == p:      # banned or start
                continue
            (odd if i % 2 else even).append(i)

        # 2. BFS
        ans = [-1] * n
        ans[p] = 0
        q = deque([p])
        while q:
            cur = q.popleft()

            # 3. compute reachable [min, max]
            if cur < k - 1:
                mn = (k - 1) - cur
            else:
                mn = cur - k + 1

            rev = (n - 1) - cur
            if rev < k - 1:
                mx = (k - 1) - rev
            else:
                mx = rev - k + 1
            mx = (n - 1) - mx

            # 4. pick the correct parity list
            lst = odd if mn % 2 else even
            idx = bisect_left(lst, mn)
            while idx < len(lst) and lst[idx] <= mx:
                nxt = lst.pop(idx)              # O(n) but each index popped once
                q.append(nxt)
                ans[nxt] = ans[cur] + 1
                # next element keeps same idx because we removed one

        return ans
```

> **Note**  
> The Python snippet uses lists + `bisect` which is *log‑linear* on the number of elements that are still present.  
> If you want a fully `O(n)` solution in Python you can replace the two lists with a *union‑find “next” array* that jumps over visited indices – the idea is identical to the Java `TreeSet` approach.

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> minReverseOperations(int n, int p, vector<int> banned, int k) {
        // ---- 1. two parity sets ----------------------------------------
        set<int> even, odd;
        unordered_set<int> skip(banned.begin(), banned.end());

        for (int i = 0; i < n; ++i) {
            if (skip.count(i) || i == p) continue;
            (i % 2 == 0 ? even : odd).insert(i);
        }

        // ---- 2. BFS -----------------------------------------------------
        vector<int> ans(n, -1);
        ans[p] = 0;
        queue<int> q;
        q.push(p);

        while (!q.empty()) {
            int cur = q.front(); q.pop();

            int mn, mx;
            if (cur < k - 1)          // left side
                mn = (k - 1) - cur;
            else
                mn = cur - k + 1;

            int rev = (n - 1) - cur;   // mirror position
            if (rev < k - 1)           // right side
                mx = (k - 1) - rev;
            else
                mx = rev - k + 1;
            mx = (n - 1) - mx;         // back

            set<int> &st = (mn % 2 == 0) ? even : odd;
            auto it = st.lower_bound(mn);
            while (it != st.end() && *it <= mx) {
                int nxt = *it;
                ans[nxt] = ans[cur] + 1;
                q.push(nxt);
                it = st.erase(it);     // erase returns next iterator
            }
        }
        return ans;
    }
};
```

---

## 📚  Blog Post – “How to Nail LeetCode 2612 in an Interview”

> **Target readers** – *Data‑structure enthusiasts, coding‑interview takers, aspiring software engineers.*  
> **SEO focus** – “Minimum Reverse Operations LeetCode 2612”, “Hard LeetCode problems solved”, “Java BFS TreeSet”, “Python LeetCode solutions”, “C++ LeetCode Hard”, “Job interview algorithms”.

---

### 1️⃣ Problem in Plain English

Imagine a 1‑D “snake” that starts at position `p`.  
You’re allowed to **flip a fixed‑length stick** of length `k` along the board – but the stick may *not* touch any “danger” squares (`banned`).  
Every flip changes the snake’s position.  
The question: *how many flips are needed to make the snake arrive at every possible target square?*  
If a target is unreachable we say `-1`.

---

### 2️⃣ Key Insight: Parity & Reachable Ranges

* Every flip is a simple “reverse” operation – the snake moves to a mirror position inside the reversed segment.  
* For a fixed `k`, the set of possible destinations is a *continuous* interval `[L, R]`.  
* Importantly, the snake can only land on indices of **the same parity** (even ↔ even, odd ↔ odd).  
  *Why?* The distance moved is always an even number (`(r - l) - 2·(x-l)`).

This reduces the graph to **two independent subgraphs** (even nodes, odd nodes).  
The BFS will never cross parity boundaries.

---

### 3️⃣ Breadth‑First Search on the Parity Graph

```
Start node = p
Level 0  = all nodes reachable by 0 flips
Level 1  = all nodes reachable by 1 flip
...
```

When we pop a node `x` from the queue we:
1. Compute its reachable interval `[min, max]` of the same parity.
2. Pull *all* nodes inside that interval from the parity‑specific sorted set, enqueue them, and mark them visited.

The first time we touch a node we have found its **minimal** distance – BFS guarantees that.

---

### 4️⃣ Avoiding Banned / Already Visited Nodes

**Java / C++**  
`TreeSet<int>` / `std::set<int>` keep elements sorted and support `lower_bound` / `ceiling`.  
Removing an element is `O(log n)`.

**Python**  
We mimic a balanced BST with a *sorted list* + `bisect`.  
`bisect_left` gives the first element `>= min`.  
While that element is inside `[min, max]` we pop it (O(1) amortised for all pops together) and enqueue it.  
If you need an even faster Python implementation you can replace the lists with a *union‑find “next” array* – the idea is identical.

---

### 5️⃣ Edge Cases & Gotchas

| Case | Why it matters | How we handle it |
|------|----------------|-------------------|
| `k = 1` | Reversing length‑1 subarray does nothing. | The reachable range collapses to the node itself; BFS will never expand – answer remains `0` for `p`, `-1` elsewhere. |
| `k = n` | Only the whole array can be reversed, but if it contains a banned index the move is illegal. | The parity range is the entire array except banned indices – handled naturally by the formula. |
| `banned` empty | Every index is allowed. | All indices except `p` go into the parity sets. |
| `n` large (10^5) | Performance matters. | Each index is removed exactly once → `O(n log n)` total. |

---

### 6️⃣ Complexity Recap

| Language | Time | Space |
|---|---|---|
| Java | `O(n log n)` | `O(n)` |
| C++ | `O(n log n)` | `O(n)` |
| Python | `O(n log n)` (with sorted‑containers) or `O(n)` (with DSU) | `O(n)` |

---

### 🎯  Final Verdict

- **BFS + parity ranges** is the most natural and clean way to solve this hard problem.  
- The trick of “same parity” and “continuous range” removes the need to enumerate every possible sub‑array.  
- Using balanced BSTs (`TreeSet` / `std::set`) gives a fast, guaranteed solution that runs comfortably within the limits.  
- The same logic transfers directly to any language – only the sorted‑set implementation changes.

---

## 📝  Want to impress recruiters?

- **Explain the parity argument** first – it shows deep mathematical insight.  
- **Mention the BFS** – many interviewers love to see the classic traversal approach.  
- **Highlight complexity** – `O(n log n)` is acceptable for a “Hard” LeetCode problem.  
- **Talk about edge cases** – show you’ve considered the boundaries (`k` close to 1 or `n`).  

These talking points will not only get you a correct solution but also give you a talking‑point edge in your next coding interview.

Happy coding! 🚀

---