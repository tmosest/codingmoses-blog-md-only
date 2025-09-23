---
title: LeetCode 1583. Count Unhappy Friends - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 How to Crack Leet Code #1583 – “Count Unhappy Friends”  
*Java | Python | C++ – The complete solution + an SEO‑friendly blog post that helps you land a job*  

---

### 1️⃣ Problem Overview  

> **Count Unhappy Friends**  
> *Medium – Leet Code 1583*  

You’re given:

| Item | Description |
|------|-------------|
| `n` | Even number of friends (`2 ≤ n ≤ 500`) |
| `preferences[i]` | A permutation of all friends **except** `i` – sorted from most to least preferred |
| `pairs[i] = [x, y]` | The only legal pairing (`x` ↔ `y`) |

A friend `x` is **unhappy** if there exists a friend `u` such that:  

1. `x` prefers `u` over its own partner `y`; **and**  
2. `u` prefers `x` over its own partner `v`.

Return the number of unhappy friends.

> **Examples**  
> *Example 1* – 2 unhappy friends  
> *Example 2* – 0 unhappy friends  
> *Example 3* – all 4 are unhappy  

---

### 2️⃣ Key Insight  

The condition is *not* “does someone have a better match”.  
It is a **mutual preference**: both people would rather be together.  
So for every person we must:

1. Walk through their preference list *until* we hit their partner.  
2. For each better‑than‑partner candidate `u`, check if `u` also prefers us over `u`’s partner.

If any such `u` exists, the person is unhappy.

To make step 2 fast we pre‑compute a **rank matrix**:

```
rank[i][j] = position of friend j in preferences[i]   (0 = top choice)
```

Then “`u` prefers x over v`” is just `rank[u][x] < rank[u][v]`.

---

### 3️⃣ Algorithm (O(n²) time, O(n²) space)

| Step | What we do |
|------|------------|
| 1 | Build `rank[n][n]` by scanning each `preferences[i]`. |
| 2 | Build `partner[n]` from `pairs`. |
| 3 | For every friend `x`:<br>   *partner = partner[x]*<br>   For each `u` in `preferences[x]` until we hit `partner`:<br>   *if* `rank[u][x] < rank[u][partner[u]]` → `x` unhappy, `break`. |
| 4 | Return the counter. |

---

### 4️⃣ Code

#### Java

```java
import java.util.*;

class Solution {
    public int unhappyFriends(int n, int[][] preferences, int[][] pairs) {
        // 1. rank matrix
        int[][] rank = new int[n][n];
        for (int i = 0; i < n; i++) {
            for (int pos = 0; pos < preferences[i].length; pos++) {
                rank[i][preferences[i][pos]] = pos;
            }
        }

        // 2. partner array
        int[] partner = new int[n];
        for (int[] p : pairs) {
            partner[p[0]] = p[1];
            partner[p[1]] = p[0];
        }

        int unhappy = 0;
        // 3. scan everyone
        for (int x = 0; x < n; x++) {
            int y = partner[x];
            for (int u : preferences[x]) {
                if (u == y) break;               // reached own partner → happy
                int v = partner[u];              // u's partner
                if (rank[u][x] < rank[u][v]) {   // mutual preference
                    unhappy++;
                    break;
                }
            }
        }
        return unhappy;
    }
}
```

#### Python

```python
class Solution:
    def unhappyFriends(self, n: int, preferences: List[List[int]],
                       pairs: List[List[int]]) -> int:
        # rank[i][j] = position of j in preferences[i]
        rank = [[0] * n for _ in range(n)]
        for i, pref in enumerate(preferences):
            for pos, friend in enumerate(pref):
                rank[i][friend] = pos

        partner = [0] * n
        for a, b in pairs:
            partner[a] = b
            partner[b] = a

        unhappy = 0
        for x in range(n):
            y = partner[x]
            for u in preferences[x]:
                if u == y: break
                v = partner[u]
                if rank[u][x] < rank[u][v]:
                    unhappy += 1
                    break
        return unhappy
```

#### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int unhappyFriends(int n, vector<vector<int>>& preferences,
                       vector<vector<int>>& pairs) {
        // rank[i][j]
        vector<vector<int>> rank(n, vector<int>(n));
        for (int i = 0; i < n; ++i) {
            for (int pos = 0; pos < preferences[i].size(); ++pos) {
                rank[i][preferences[i][pos]] = pos;
            }
        }

        vector<int> partner(n);
        for (auto &p : pairs) {
            partner[p[0]] = p[1];
            partner[p[1]] = p[0];
        }

        int unhappy = 0;
        for (int x = 0; x < n; ++x) {
            int y = partner[x];
            for (int u : preferences[x]) {
                if (u == y) break;
                int v = partner[u];
                if (rank[u][x] < rank[u][v]) {
                    ++unhappy;
                    break;
                }
            }
        }
        return unhappy;
    }
};
```

---

### 5️⃣ Complexity Analysis

| Metric | Java / Python / C++ |
|--------|---------------------|
| Time   | **O(n²)** – we visit each friend’s preference list once. |
| Space  | **O(n²)** – the `rank` matrix; `partner` and auxiliary arrays are O(n). |

With `n ≤ 500`, this easily passes Leet Code’s limits.

---

### 6️⃣ Common Pitfalls (“The Bad & The Ugly”)

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| Using a *hash map* for the rank instead of a 2‑D array | Too slow (`O(n²)` lookups become `O(1)` but with a high constant) | Use a plain int matrix – fast cache friendly |
| Breaking only after finding *any* better friend | We must also ensure that *that friend* prefers us | Check the second condition (`rank[u][x] < rank[u][partner[u]]`) |
| Skipping the partner in the inner loop by `if(u==y) continue` | Would incorrectly count some unhappy people | `break` when partner is reached – we’re guaranteed to be happy beyond that point |
| Counting both `x` and `y` as unhappy for the same pair | The algorithm counts each person independently, but the condition is symmetric; no double counting issue | No change needed – each person is evaluated once |

---

### 7️⃣ Why This Blog Helps You Get a Job

1. **SEO‑Optimized Keywords** – “Count Unhappy Friends”, “Leet Code 1583”, “Java interview question”, “Python algorithm”, “C++ solution”, “O(n²) time”, “space complexity”, “mutual preference algorithm”, “unhappy friends logic”.
2. **Clear Code** – Ready‑to‑copy solutions for Java, Python, C++ – the three most common languages interviewers expect.
3. **Deep Dive** – Understanding *why* the algorithm works gives you the confidence to explain it in an interview.
4. **Pitfalls** – Common mistakes you can mention as “things I learned” to show you’ve thought about edge cases.
5. **Time & Space Analysis** – Shows your ability to evaluate complexity – a must‑have interview skill.

---

### 8️⃣ Quick Takeaway

- Build a **rank matrix** for O(1) preference queries.  
- Map every friend’s partner in an array.  
- For each friend, iterate preferences until you hit the partner; if any better‑than‑partner candidate also prefers you, you’re unhappy.  
- Complexity: **O(n²)** time, **O(n²)** space – perfectly fine for `n ≤ 500`.

Drop the code into your editor, run the Leet Code tests, and you’ll be ready to ace this problem and impress hiring managers! 🚀

--- 

Happy coding, and best of luck landing that dream job!