---
title: LeetCode 1244. Design A Leaderboard - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1244 – Design A Leaderboard  
**Java / Python / C++** – Full solutions + an SEO‑friendly blog article that explains the “good, the bad, and the ugly” of designing a leaderboard.  

---

### 1️⃣ Problem Recap

| Item | Description |
|------|-------------|
| **Operations** | `addScore(playerId, score)` – add `score` to the player's current score (create if absent).<br>`top(K)` – return the sum of the top `K` scores.<br>`reset(playerId)` – erase the player’s score. |
| **Constraints** | `1 ≤ playerId, K ≤ 10 000`<br>`1 ≤ score ≤ 100`<br>`≤ 1 000` function calls total. |
| **Goal** | Design a data structure that supports the above operations efficiently. |

---

## 2️⃣ The “Good” – O(1) add/reset, O(K) top

The classic solution uses a **hash map** (`playerId → score`) plus a **sorted container** that keeps all scores in descending order.  

| Language | Data structure for sorted scores | Complexity |
|----------|----------------------------------|------------|
| Java | `TreeSet<Integer>` (or a custom doubly linked list) | `addScore`/`reset`: O(log N) <br> `top(K)`: O(K) |
| Python | `bisect.insort` on a list (O(N)) – good enough for ≤ 1000 calls | `addScore`/`reset`: O(N) <br> `top(K)`: O(K) |
| C++ | `multiset<int, greater<int>>` | `addScore`/`reset`: O(log N) <br> `top(K)`: O(K) |

### Java – HashMap + TreeSet

```java
import java.util.*;

public class Leaderboard {

    private final Map<Integer, Integer> scoreMap = new HashMap<>();
    private final TreeSet<Integer> sortedScores = new TreeSet<>(Comparator.reverseOrder());

    public Leaderboard() {}

    public void addScore(int playerId, int score) {
        int oldScore = scoreMap.getOrDefault(playerId, 0);
        if (oldScore > 0) sortedScores.remove(oldScore);
        int newScore = oldScore + score;
        scoreMap.put(playerId, newScore);
        sortedScores.add(newScore);
    }

    public int top(int K) {
        int sum = 0;
        int i = 0;
        for (int s : sortedScores) {
            if (i++ == K) break;
            sum += s;
        }
        return sum;
    }

    public void reset(int playerId) {
        int oldScore = scoreMap.remove(playerId);
        if (oldScore > 0) sortedScores.remove(oldScore);
    }
}
```

### Python – Dictionary + Sorted List (bisect)

```python
import bisect
from collections import defaultdict

class Leaderboard:
    def __init__(self):
        self.scores = defaultdict(int)   # playerId -> score
        self.sorted = []                # descending list of scores

    def addScore(self, playerId: int, score: int) -> None:
        old = self.scores[playerId]
        if old:
            idx = bisect.bisect_left(self.sorted, old, key=lambda x: -x)
            self.sorted.pop(idx)
        new = old + score
        self.scores[playerId] = new
        bisect.insort_left(self.sorted, new, key=lambda x: -x)

    def top(self, K: int) -> int:
        return sum(self.sorted[:K])

    def reset(self, playerId: int) -> None:
        old = self.scores.pop(playerId, 0)
        if old:
            idx = bisect.bisect_left(self.sorted, old, key=lambda x: -x)
            self.sorted.pop(idx)
```

> **Why bisect?**  
> The list is kept sorted in **descending** order, so `bisect_left` with a key of `-x` finds the correct index.

### C++ – Unordered Map + Multiset

```cpp
#include <bits/stdc++.h>
using namespace std;

class Leaderboard {
    unordered_map<int, int> mp;               // playerId -> score
    multiset<int, greater<int>> scores;       // all scores, descending

public:
    Leaderboard() = default;

    void addScore(int playerId, int score) {
        int old = mp.count(playerId) ? mp[playerId] : 0;
        if (old) scores.erase(scores.find(old));   // remove old score
        int nw = old + score;
        mp[playerId] = nw;
        scores.insert(nw);
    }

    int top(int K) {
        int sum = 0, cnt = 0;
        for (int s : scores) {
            if (cnt++ == K) break;
            sum += s;
        }
        return sum;
    }

    void reset(int playerId) {
        int old = mp[playerId];
        scores.erase(scores.find(old));
        mp.erase(playerId);
    }
};
```

---

## 3️⃣ The “Bad” – Re‑sort on every `top`

A naive approach stores all scores in a vector and **sorts it on every `top(K)`** call.

```java
List<Integer> all = new ArrayList<>();
// addScore: update map, rebuild list
// top: Collections.sort(all, Collections.reverseOrder()); // O(N log N)
```

This is *acceptable* for the problem’s limits (≤ 1000 calls), but it **does not scale** – each `top` becomes expensive when the leaderboard grows to thousands or millions of players.  
**Avoid** this in real interviews unless the constraints explicitly allow it.

---

## 4️⃣ The “Ugly” – Using a priority queue without update support

A min‑heap of size `K` seems attractive, but when a player’s score changes, the heap no longer reflects the correct ordering. One might attempt to *lazy‑remove* outdated entries, but that quickly becomes messy and error‑prone.

> **Bottom line:** A heap that cannot remove or update arbitrary elements is a recipe for bugs and O(N) cleanup overhead.

---

## 5️⃣ Going Beyond the Good – Advanced options

| Option | What it buys | When to consider |
|--------|--------------|------------------|
| **Fenwick / Binary Indexed Tree** (score → frequency) | Allows `top(K)` in O(log M) where `M` is the max possible score (here ≤ 10⁶). | If you need **prefix sums** of frequencies. |
| **Segment Tree** | Similar to Fenwick but gives more flexibility. | When `score` can be large and you need range queries. |
| **Binary Indexed Tree of frequencies + dictionary** | Keeps `addScore`/`reset` O(log M), `top(K)` O(log M). | For tight time budgets with many operations. |

> **Tip:** If you’re asked to implement a *Fenwick tree* version, first think about the mapping `score → count` and how you’d obtain the k‑th largest value by binary searching on cumulative frequencies.

---

## 5️⃣ Interviewer‑friendly Talking Points

| Topic | What to say | Why it matters |
|-------|-------------|----------------|
| **Why a hash map?** | Keeps **O(1)** access to a player's current score. | Essential for fast updates. |
| **Why a sorted set?** | Enables `top(K)` in **O(K)** by iterating from the greatest. | Avoids expensive scanning of unsorted data. |
| **Handling duplicates** | Use a `multiset` / `TreeSet` that stores *scores*, not `(playerId,score)` pairs. | Easier removal when a player’s score changes. |
| **Edge case: reset to 0** | Remove the player from both map and multiset; do *not* insert `0` back. | Guarantees that `top(K)` only sums active players. |
| **Space complexity** | `O(N)` where `N` = number of active players. | Keep this in mind when discussing trade‑offs. |

---

## 5️⃣ SEO‑Optimized Blog Article

Below is a complete, copy‑paste‑ready blog post that you can publish on Medium, Dev.to, or your own personal blog.  The article is sprinkled with high‑value keywords that recruiters love: *Design a Leaderboard*, *Java Leaderboard solution*, *Python Leaderboard*, *C++ Leaderboard*, *LeetCode 1244*, *Data structures interview*.  

```markdown
# Design A Leaderboard (LeetCode 1244) – Java, Python & C++ Solutions

**Keywords:** Design a Leaderboard, LeetCode 1244, Java Leaderboard solution, Python leaderboard, C++ leaderboard, interview problem, data structures, hashmap, sorted set, TreeSet, multiset, Fenwick tree

---

## 📌 Problem Overview

LeetCode 1244, *Design A Leaderboard*, asks you to build a data structure that supports three operations:  
`addScore`, `top(K)`, and `reset`.  Constraints allow up to **1 000** total calls, but the interviewer’s goal is to see your thinking about efficient data‑structure design, not just a brute‑force implementation.

---

## 🚀 The “Good” – Optimal Trade‑Off

### Why it’s Good
- **Fast updates** (`addScore`, `reset`): O(log N) for insertion/deletion in a balanced tree or multiset.
- **Fast top‑K query**: O(K) to sum the first K elements.
- **Simple to implement** with only standard library containers.

### Java Implementation
```java
import java.util.*;

public class Leaderboard {
    private final Map<Integer, Integer> scoreMap = new HashMap<>();
    private final TreeSet<Integer> sortedScores = new TreeSet<>(Comparator.reverseOrder());

    public void addScore(int playerId, int score) {
        int old = scoreMap.getOrDefault(playerId, 0);
        if (old > 0) sortedScores.remove(old);
        int newScore = old + score;
        scoreMap.put(playerId, newScore);
        sortedScores.add(newScore);
    }

    public int top(int K) {
        int sum = 0, i = 0;
        for (int s : sortedScores) {
            if (i++ == K) break;
            sum += s;
        }
        return sum;
    }

    public void reset(int playerId) {
        int old = scoreMap.remove(playerId);
        if (old > 0) sortedScores.remove(old);
    }
}
```

### Python Implementation (bisect on a sorted list)

```python
import bisect
from collections import defaultdict

class Leaderboard:
    def __init__(self):
        self.scores = defaultdict(int)
        self.sorted = []

    def addScore(self, playerId: int, score: int) -> None:
        old = self.scores[playerId]
        if old:
            idx = bisect.bisect_left(self.sorted, old, key=lambda x: -x)
            self.sorted.pop(idx)
        new = old + score
        self.scores[playerId] = new
        bisect.insort_left(self.sorted, new, key=lambda x: -x)

    def top(self, K: int) -> int:
        return sum(self.sorted[:K])

    def reset(self, playerId: int) -> None:
        old = self.scores.pop(playerId, 0)
        if old:
            idx = bisect.bisect_left(self.sorted, old, key=lambda x: -x)
            self.sorted.pop(idx)
```

### C++ Implementation (unordered_map + multiset)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Leaderboard {
    unordered_map<int, int> mp;
    multiset<int, greater<int>> scores;

public:
    void addScore(int playerId, int score) {
        int old = mp.count(playerId) ? mp[playerId] : 0;
        if (old) scores.erase(scores.find(old));
        int nw = old + score;
        mp[playerId] = nw;
        scores.insert(nw);
    }

    int top(int K) {
        int sum = 0, cnt = 0;
        for (int s : scores) {
            if (cnt++ == K) break;
            sum += s;
        }
        return sum;
    }

    void reset(int playerId) {
        int old = mp[playerId];
        scores.erase(scores.find(old));
        mp.erase(playerId);
    }
};
```

---

## 📉 The “Bad” – Sorting on every `top`

```java
// addScore: rebuild vector
// top: Collections.sort(vector, Collections.reverseOrder()); // O(N log N)
```

Works for the problem’s small limits but becomes a **bottleneck** once the leaderboard contains thousands of players.  
> **Interview Tip:** Ask the interviewer about constraints before choosing this.

---

## 🧨 The “Ugly” – Heap that can’t update

Using a priority queue to keep the largest `K` scores seems clever, but when a player’s score changes, the heap is no longer valid.  You would have to *lazy‑remove* stale entries, which adds complexity and bug risk.

---

## 6️⃣ Bottom Line for Interviews

| Operation | Best‑case (our good solutions) | Worst‑case (bad/ugly) |
|-----------|--------------------------------|----------------------|
| `addScore` | O(log N) (TreeSet / multiset) | O(N) (list) |
| `reset` | O(log N) | O(N) |
| `top(K)` | O(K) | O(N log N) (sorting) |

**Key takeaway:**  
Maintain a **hash map for fast look‑ups** and a **sorted container** that reflects the current state of scores.  This balances update speed and query speed, a pattern that appears in many interview problems (leaderboards, top‑k queries, median maintenance, etc.).

---

## 7️⃣ 📈 SEO Checklist (for recruiters)

- **Title**: “Design A Leaderboard – Java, Python & C++ Solutions (LeetCode 1244)”
- **Meta description**: “Learn the optimal way to implement the Design A Leaderboard problem on LeetCode 1244. Full Java, Python and C++ code, plus a deep dive into the good, bad, and ugly solutions.”
- **Headings**: `#`, `##`, `###` with keywords.
- **Keywords**:  
  - Design A Leaderboard  
  - LeetCode 1244  
  - Leaderboard data structure  
  - Java Leaderboard solution  
  - Python Leaderboard solution  
  - C++ Leaderboard solution  
  - HashMap + TreeSet  
  - multiset + unordered_map  
  - bisect.insort  
  - interview problem
- **Internal links** (if on your blog): link to related posts like “How to use TreeSet for descending order” or “Python bisect tricks”.
- **External links**: Reference the original LeetCode problem and any official editorial or discussion threads.

---

## 🎯 Final Words

- **Show the trade‑offs**: Explain why you chose a map + sorted set over a heap or a naive sort.  
- **Mention constraints**: The interview is often about understanding limits; mention that with 1 000 calls, an O(N) list is fine, but for production you’d need a more scalable structure.  
- **Talk about edge cases**: Resetting to zero, handling duplicate scores, and ensuring `top(K)` only counts active players.

With the code above and the interview talking points, you’ll be ready to ace the *Design A Leaderboard* question and impress any hiring manager who reads your blog! 🚀
```

---

Enjoy your interview prep and happy coding!