---
title: LeetCode 1434. Number of Ways to Wear Different Hats to Each Other - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Overview  
**LeetCode 1434 – Number of Ways to Wear Different Hats to Each Other**  
*Difficulty: Hard*  

> There are **n** people (1 ≤ n ≤ 10) and 40 distinct hats numbered 1‑40.  
> `hats[i]` lists the hats that the *i‑th* person likes.  
> Each person must wear **exactly one** of their liked hats, and **no two people may wear the same hat**.  
> Return the number of valid assignments modulo 1 000 000 007.

The problem is a classic *“assign people to hats”* combinatorics puzzle that can be solved elegantly with **dynamic programming + bitmasking**.  
Below you’ll find a detailed, SEO‑friendly explanation, the “good, the bad, and the ugly” of the approach, and complete implementations in **Java**, **Python**, and **C++**.

---

## 2.  Why DP + Bitmasking?  

| Factor | Why it matters |
|--------|----------------|
| **n ≤ 10** | 2ⁿ ≤ 1,024 → a 10‑bit mask is tiny |
| **40 hats** | 2⁴⁰ is impossible, but we can iterate over hats instead |
| **Hat‑to‑person choice** | We decide *which people* wear a particular hat, not *which hat* a person gets |

The key insight is to **swap the roles**: instead of tracking free hats (40 options), we track *unassigned people* (≤10).  
We iterate through hats from 1 to 40, and for each hat decide whether to give it to one of its interested people or skip it.  
Because a mask encodes all people who still need hats, we can memoize the number of ways from any `(currentHat, mask)` pair.

---

## 3.  State Definition  

```
dp[hat][mask] = number of valid assignments
                 starting from 'hat'
                 when the set of already assigned people is 'mask'
```

* `hat` ranges from 1 to 40 (inclusive).  
* `mask` is a bitmask of length `n`.  
  *Bit i is 1 → person i already has a hat; 0 → still waiting.*

Base cases:

* **All people assigned** (`mask == ALL_MASK`) → 1 way (stop).
* **All hats processed** (`hat > 40`) and people remain → 0 ways.

Transition:

```
1. Skip this hat:
   res = dp[hat+1][mask]

2. Give this hat to any unassigned person who likes it:
   for each person p in peopleWhoLike[hat]:
       if (mask & (1 << p)) == 0:
           res += dp[hat+1][mask | (1 << p)]
```

All arithmetic is performed modulo 1 000 000 007.

---

## 4.  Complexity  

| | Time | Space |
|---|---|---|
| **Recursive DP with memoization** | `O(40 * 2ⁿ)` → at most 40 × 1 024 ≈ 40 000 operations | `O(40 * 2ⁿ)` for the memo table |

With `n = 10`, this runs in **microseconds** in all three languages.

---

## 5.  The Good, The Bad, and The Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | DP state is self‑explanatory once you know the mask trick | The recursion depth of 40 is trivial, but some readers may not understand “mask” | Over‑optimization (bit hacks, iterative DP) can obfuscate logic |
| **Performance** | Exponential in *n* (tiny) and linear in 40 hats → very fast | None – no hidden overhead | None – algorithm is optimal for constraints |
| **Portability** | Works on all mainstream languages | Recursion depth < 50 → safe | For languages lacking big integer support you must use 64‑bit integers carefully |
| **Edge‑case safety** | Handles duplicates in `hats[i]` because we only store unique people per hat | If `hats` contains hats outside 1‑40 → must clamp | None |

The only “ugly” part is the need to pre‑build the `peopleWhoLike` list for each hat, which adds a bit of code but is essential for speed.

---

## 6.  SEO‑Optimized Blog Article  

> **Title:** *Master the “Number of Ways to Wear Different Hats” LeetCode Problem (Hard) – DP Bitmasking Explained in Java, Python & C++*  
> **Meta Description:** *Learn how to solve LeetCode 1434 with dynamic programming and bitmasking. Full solutions in Java, Python, and C++ plus an interview‑ready blog post.*

---

### 6.1  Introduction  

If you’re hunting for a **hard interview problem** that showcases your algorithmic thinking, look no further than LeetCode’s “Number of Ways to Wear Different Hats to Each Other”.  
It’s a favorite among hiring managers at top tech firms because it tests:

* **Dynamic programming** – can you design a state that captures all necessary information?  
* **Bitmasking** – do you know how to encode subsets compactly?  
* **Optimization mindset** – can you squeeze performance out of a seemingly exponential problem?

This article walks you through the intuition, the DP formulation, and gives you **ready‑to‑copy** code snippets in **Java**, **Python**, and **C++**.

---

### 6.2  Problem Recap (in One Sentence)  

> Assign each of up to 10 people a unique hat from 1‑40 such that every person gets a hat they like, counting all distinct assignments modulo 1 000 000 007.

---

### 6.3  Intuition & Why the Bitmask Trick Works  

The naive solution would try all 40ⁿ assignments, which is impossible.  
Instead, we realize that the **limited number of people** (≤10) means we can represent the set of “people still needing hats” as a **10‑bit integer**.  
With a mask we can **memoize** subproblems and avoid exploring duplicate work.

---

### 6.3  The DP + Bitmask Transition  

*State:* `(currentHat, mask)`  
*Transition:* either skip the hat or give it to an unassigned interested person.  
The mask flips a bit when a person receives a hat, and we progress hat‑by‑hat.

Pseudo‑code (same for all languages):

```text
res = solve(hat+1, mask)              // skip
for person in peopleWhoLike[hat]:
    if not mask has person:
        res += solve(hat+1, mask | person)
return res % MOD
```

---

### 6.4  Implementation in Java  

```java
import java.util.*;

public class Solution {
    private static final int MAX_HAT = 40;
    private static final int MOD = 1_000_000_007;
    private int n, allMask;
    private List<Integer>[] peopleByHat;
    private int[][] memo;   // -1 → uncomputed

    public int numberWays(int[][] hats) {
        n = hats.length;
        allMask = (1 << n) - 1;
        peopleByHat = new ArrayList[MAX_HAT + 1];
        for (int i = 1; i <= MAX_HAT; i++) peopleByHat[i] = new ArrayList<>();

        for (int person = 0; person < n; person++) {
            for (int hat : hats[person]) {
                peopleByHat[hat].add(person);
            }
        }

        memo = new int[MAX_HAT + 2][1 << n];
        for (int i = 0; i < MAX_HAT + 2; i++) Arrays.fill(memo[i], -1);

        return solve(1, 0);
    }

    private int solve(int hat, int mask) {
        if (mask == allMask) return 1;
        if (hat > MAX_HAT) return 0;
        if (memo[hat][mask] != -1) return memo[hat][mask];

        long res = solve(hat + 1, mask);      // skip the hat

        for (int person : peopleByHat[hat]) {
            if ((mask & (1 << person)) == 0) {
                res += solve(hat + 1, mask | (1 << person));
                res %= MOD;
            }
        }
        return memo[hat][mask] = (int) res;
    }
}
```

*Tip:* Keep the memo table small (`40 × 1024`) – it fits comfortably in memory.

---

### 6.5  Implementation in Python  

```python
from functools import lru_cache

MOD = 10**9 + 7

def numberWays(hats):
    n = len(hats)
    people_by_hat = [[] for _ in range(41)]
    for person, liked in enumerate(hats):
        for hat in liked:
            people_by_hat[hat].append(person)

    all_mask = (1 << n) - 1

    @lru_cache(None)
    def dfs(hat, mask):
        if mask == all_mask:
            return 1
        if hat > 40:
            return 0

        res = dfs(hat + 1, mask)   # skip this hat
        for person in people_by_hat[hat]:
            if not mask & (1 << person):
                res = (res + dfs(hat + 1, mask | (1 << person))) % MOD
        return res

    return dfs(1, 0)
```

Python’s `lru_cache` makes memoization trivial.  
The bit operations are fast because Python integers are arbitrary‑precision.

---

### 6.6  Implementation in C++  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    static const int MAX_HAT = 40;
    static const int MOD = 1'000'000'007;
    int n, allMask;
    vector<vector<int>> peopleByHat;
    vector<vector<int>> memo;          // -1 → uncomputed

public:
    int numberWays(vector<vector<int>>& hats) {
        n = hats.size();
        allMask = (1 << n) - 1;
        peopleByHat.assign(MAX_HAT + 1, {});
        for (int person = 0; person < n; ++person)
            for (int hat : hats[person])
                peopleByHat[hat].push_back(person);

        memo.assign(MAX_HAT + 2, vector<int>(1 << n, -1));
        return dfs(1, 0);
    }

private:
    int dfs(int hat, int mask) {
        if (mask == allMask) return 1;
        if (hat > MAX_HAT) return 0;
        int &ans = memo[hat][mask];
        if (ans != -1) return ans;

        long long res = dfs(hat + 1, mask);      // skip
        for (int person : peopleByHat[hat]) {
            if (!(mask & (1 << person))) {
                res += dfs(hat + 1, mask | (1 << person));
                if (res >= MOD) res -= MOD;
            }
        }
        return ans = (int)res;
    }
};
```

The C++ solution mirrors the Java one in structure.  
All arithmetic is done with 64‑bit `long long` to avoid overflow before the modulo step.

---

### 6.7  What Hiring Managers Want to Hear  

* **State clarity** – you’ll explain the DP state and why masks are efficient.  
* **Space‑time trade‑offs** – you’ll describe `O(40 * 2ⁿ)` complexity.  
* **Edge‑case awareness** – you’ll mention handling people who like the same hat and hats out of range.

These are the talking points that impress in a coding‑round interview.

---

### 6.8  Final Thoughts  

LeetCode 1434 is a gold‑mine for sharpening your dynamic‑programming skills.  
With the mask‑based DP described above, you can:

* Solve it in a handful of lines of clean code.  
* Provide a fully‑commented solution in any of the top three languages (Java, Python, C++).  
* Walk a recruiter through the “good, the bad, and the ugly” of the solution, showing you understand both algorithm design and practical implementation.

Good luck, and happy coding! 🚀

---

## 7.  Full Code Snippets (Copy‑Paste Ready)

### 7.1  Java (Recursive + Memoization)

```java
import java.util.*;

public class Solution {
    private static final int MAX_HAT = 40;
    private static final int MOD = 1_000_000_007;
    private int n, allMask;
    private List<Integer>[] peopleByHat;
    private int[][] memo;   // memo[hat][mask]  (-1 → uncomputed)

    public int numberWays(int[][] hats) {
        n = hats.length;
        allMask = (1 << n) - 1;
        peopleByHat = new ArrayList[MAX_HAT + 1];
        for (int i = 0; i <= MAX_HAT; i++) peopleByHat[i] = new ArrayList<>();

        for (int person = 0; person < n; person++) {
            for (int hat : hats[person]) {
                peopleByHat[hat].add(person);
            }
        }

        memo = new int[MAX_HAT + 2][1 << n];
        for (int i = 0; i < memo.length; i++) Arrays.fill(memo[i], -1);

        return dfs(1, 0);
    }

    private int dfs(int hat, int mask) {
        if (mask == allMask) return 1;
        if (hat > MAX_HAT) return 0;
        if (memo[hat][mask] != -1) return memo[hat][mask];

        long res = dfs(hat + 1, mask);              // skip this hat

        for (int person : peopleByHat[hat]) {
            if ((mask & (1 << person)) == 0) {
                res += dfs(hat + 1, mask | (1 << person));
                res %= MOD;
            }
        }
        memo[hat][mask] = (int) res;
        return memo[hat][mask];
    }
}
```

---

### 7.2  Python (Recursive + LRU Cache)

```python
from functools import lru_cache
from typing import List

MOD = 10 ** 9 + 7

def numberWays(hats: List[List[int]]) -> int:
    n = len(hats)
    people_by_hat = [[] for _ in range(41)]          # 1‑based hats

    for person, likes in enumerate(hats):
        for h in likes:
            people_by_hat[h].append(person)

    @lru_cache(maxsize=None)
    def dfs(hat: int, mask: int) -> int:
        if mask == (1 << n) - 1:
            return 1
        if hat > 40:
            return 0

        res = dfs(hat + 1, mask)                      # skip this hat
        for person in people_by_hat[hat]:
            if not mask & (1 << person):
                res = (res + dfs(hat + 1, mask | (1 << person))) % MOD
        return res

    return dfs(1, 0)
```

---

### 7.3  C++ (Recursive + Memoization)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    static const int MAX_HAT = 40;
    static const int MOD = 1'000'000'007;
    int n, allMask;
    vector<vector<int>> peopleByHat;
    vector<vector<int>> memo;          // -1 → uncomputed

public:
    int numberWays(vector<vector<int>>& hats) {
        n = hats.size();
        allMask = (1 << n) - 1;
        peopleByHat.assign(MAX_HAT + 1, {});
        for (int person = 0; person < n; ++person)
            for (int h : hats[person])
                peopleByHat[h].push_back(person);

        memo.assign(MAX_HAT + 2, vector<int>(1 << n, -1));
        return dfs(1, 0);
    }

private:
    int dfs(int hat, int mask) {
        if (mask == allMask) return 1;
        if (hat > MAX_HAT) return 0;
        int &ans = memo[hat][mask];
        if (ans != -1) return ans;

        long long res = dfs(hat + 1, mask);           // skip
        for (int person : peopleByHat[hat]) {
            if (!(mask & (1 << person))) {
                res += dfs(hat + 1, mask | (1 << person));
                if (res >= MOD) res -= MOD;
            }
        }
        return ans = (int)res;
    }
};
```

These snippets demonstrate how to solve LeetCode 1434 efficiently in any of the major languages.  
Feel free to integrate them into your test suite or submit them directly to the problem.