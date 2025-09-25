---
title: LeetCode 1434. Number of Ways to Wear Different Hats to Each Other - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Number of Ways to Wear Different Hats – Full‑Stack Solution (Java / Python / C++)  
> **Hard** – 10 people, 40 hats, bitmask DP  

---

### 1. Problem Recap

You are given `n (≤ 10)` people and 40 different hats (numbered `1…40`).  
`hats[i]` contains the list of hats that person *i* likes.  
You must count the **number of ways** to give each person a *different* hat from the ones he likes.  
Return the answer **modulo** `10⁹+7`.

---

### 2. Why a Straight‑Forward DP Fails

If we try to remember *which hats are taken*, we would need a state of size `2¹⁰⁴ ≈ 1.6 × 10³⁸` – impossible.  
The trick: **the number of people is small** (`≤ 10`), so we keep a mask of *un‑hatched* people instead of hats.

---

### 3. Bitmask DP – Core Idea

We iterate **over hats** instead of people.

```
mask  – bit i is 1 if person i still needs a hat
dp[hat][mask] – ways to assign hats hat..40 to the people in 'mask'
```

Transition:

1. Skip current hat: `dp[hat+1][mask]`
2. For each person `p` who likes `hat` and is still in `mask`, give `hat` to `p`  
   → `dp[hat+1][mask ^ (1<<p)]`

Base case: when `mask==0` (everyone got a hat) → 1 way.  
If `hat>40` and people still need hats → 0 ways.

Complexity:  
- States: `40 × 2ⁿ  ≤  40 × 1024 ≈ 41k`  
- Transitions: each state visits all people that like the hat – ≤ 10.  

So O(`40 * 2ⁿ * n`) → trivial for the constraints.

---

### 4. Code – Java

```java
import java.util.*;

class Solution {
    static final int MOD = 1_000_000_007;
    static int[][] memo;           // dp[hat][mask]
    static List<List<Integer>> hats; // original list

    // recursive DP with memoization
    static int dfs(int hat, int mask) {
        if (mask == 0) return 1;              // everyone hatched
        if (hat > 40) return 0;               // ran out of hats
        if (memo[hat][mask] != -1) return memo[hat][mask];

        int res = dfs(hat + 1, mask);          // skip current hat

        // try assigning hat to any eligible person
        for (int person : hats.get(hat)) {
            if ((mask & (1 << person)) != 0) { // still needs a hat
                res = (res + dfs(hat + 1, mask ^ (1 << person))) % MOD;
            }
        }
        return memo[hat][mask] = res;
    }

    public int numberWays(List<List<Integer>> hats) {
        // convert hats[i] to 0‑based indices
        this.hats = new ArrayList<>();
        // hats[0] is dummy because hats are 1‑based
        this.hats.add(new ArrayList<>());
        for (List<Integer> pref : hats) {
            List<Integer> tmp = new ArrayList<>();
            for (int h : pref) tmp.add(h - 1); // 0‑based
            this.hats.add(tmp);
        }

        int n = hats.size();
        int fullMask = (1 << n) - 1;
        memo = new int[41][fullMask + 1];
        for (int[] row : memo) Arrays.fill(row, -1);

        return dfs(1, fullMask);
    }
}
```

> **Why 1‑based to 0‑based?**  
> We use hat indices `0‑39` internally, so we prepend a dummy list at index `0`.

---

### 5. Code – Python

```python
MOD = 1_000_000_007

class Solution:
    def numberWays(self, hats: list[list[int]]) -> int:
        n = len(hats)

        # build reverse mapping: hat -> list of people who like it
        hat_to_people = [[] for _ in range(40)]
        for person, prefs in enumerate(hats):
            for h in prefs:
                hat_to_people[h - 1].append(person)

        from functools import lru_cache

        @lru_cache(None)
        def dfs(hat: int, mask: int) -> int:
            if mask == 0:
                return 1
            if hat == 40:
                return 0

            # skip this hat
            res = dfs(hat + 1, mask)

            # try giving hat to each eligible person
            for p in hat_to_people[hat]:
                if mask & (1 << p):
                    res = (res + dfs(hat + 1, mask ^ (1 << p))) % MOD
            return res

        full_mask = (1 << n) - 1
        return dfs(0, full_mask)
```

---

### 6. Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    const int MOD = 1'000'000'007;
    vector<vector<int>> hatToPeople;          // hat 0..39 -> people list
    vector<vector<int>> memo;                 // dp[hat][mask]

    int dfs(int hat, int mask) {
        if (mask == 0) return 1;              // all hatched
        if (hat == 40) return 0;              // no hats left
        int &res = memo[hat][mask];
        if (res != -1) return res;

        res = dfs(hat + 1, mask);            // skip this hat

        for (int p : hatToPeople[hat]) {     // give hat to p
            if (mask & (1 << p)) {
                res = (res + dfs(hat + 1, mask ^ (1 << p))) % MOD;
            }
        }
        return res;
    }

    int numberWays(vector<vector<int>>& hats) {
        int n = hats.size();
        hatToPeople.assign(40, {});
        for (int person = 0; person < n; ++person) {
            for (int h : hats[person]) {
                hatToPeople[h - 1].push_back(person);
            }
        }
        int fullMask = (1 << n) - 1;
        memo.assign(41, vector<int>(fullMask + 1, -1));
        return dfs(0, fullMask);
    }
};
```

---

### 7. Blog Article (SEO‑Optimized)

> **Title:**  
> *Number of Ways to Wear Different Hats – Hard DP Bitmask Solution (Java/Python/C++)*

---

#### Table of Contents
1. Problem Overview  
2. Why Brute‑Force Fails  
3. Bitmask DP – The Optimal Strategy  
4. Java Implementation  
5. Python Implementation  
6. C++ Implementation  
7. Complexity Analysis  
8. Common Pitfalls (The Ugly)  
9. Take‑away Lessons (The Good)  
10. How to Master Similar Hard LeetCode Problems

---

### 1. Problem Overview

> You have up to **10** people and **40** hats.  
> Each person likes a subset of hats.  
> Count all *valid* assignments where no two people share a hat.  
> Return the count modulo `1 000 000 007`.

---

### 2. Why Brute‑Force Fails

A naïve DFS over people (`O(40ⁿ)`) explodes even for `n = 10`.  
Keeping a state that remembers *taken hats* would need `2³⁴⁰` entries – impossible.  
The only viable approach is to exploit the small **number of people**.

---

### 2. Bitmask DP – The Optimal Strategy

Instead of tracking hats, we track *people still needing a hat*.

```
mask : bit i == 1  →  person i is waiting for a hat
dp[hat][mask] : number of ways to finish assigning hats hat…40
```

**Transition** – for each state:

1. **Skip** current hat: `dp[hat+1][mask]`
2. **Assign** current hat to any person in `mask` who likes it  
   → `dp[hat+1][mask ^ (1<<p)]`

Base case: `mask == 0` → 1.  
If we exhaust hats and people still need hats → 0.

---

### 3. Java Implementation

*(See section 4 in the code block above)*  

- Uses a 2‑D array for memoization (`-1` = uncomputed).  
- Recursively processes hats `0…39`.  
- All arithmetic is taken modulo `MOD`.

---

### 4. Python Implementation

*(See section 5 in the code block above)*  

- Uses a **reverse mapping** (`hat_to_people`) for fast lookup.  
- `@lru_cache` provides memoization.  
- `dfs(0, full_mask)` starts with the first hat (index 0).

---

### 5. C++ Implementation

*(See section 6 in the code block above)*  

- `vector<vector<int>> memo(41, vector<int>(mask+1, -1));`
- Iterates over hats using `hatToPeople` array.

---

### 6. Complexity Analysis

| Metric | Value |
|--------|-------|
| States | `40 × 2ⁿ ≤ 41 000` |
| Transitions per state | `≤ 10` |
| Time | `O(40 × 2ⁿ × n) ≈ 4 × 10⁴` operations |
| Memory | `O(40 × 2ⁿ)` ints ≈ 0.3 MB |

**Result:** Easily runs under 1 ms for the hardest test cases.

---

### 7. Common Pitfalls (The Ugly)

| Symptom | Root Cause | Fix |
|---------|------------|-----|
| `StackOverflowError` in Java | Recursive depth > 10 000 | Use iterative DP or increase recursion limit. |
| Wrong answer for `n=0` | Forget base case | Handle `mask==0` before hat exhaustion. |
| Off‑by‑one errors on hat indices | 1‑based input vs 0‑based arrays | Subtract 1 or add a dummy list at index 0. |
| Time limit exceeded | Double‑counting hats | Use memoization; avoid recomputation. |
| TLE in Python | `dfs` not cached | Decorate with `lru_cache` or build a DP table. |

---

### 8. Take‑away Lessons (The Good)

- **Flip the dimension**: When the brute‑force dimension is huge, look for a smaller one to memoize over.  
- **Iterate over hats** – a powerful pattern in “assign‑to‑unassigned” problems.  
- **Reverse mapping** (`hat → people`) dramatically reduces inner loops.  
- **Memoization** + **bitmask** → almost zero overhead for LeetCode Hard problems.

---

### 9. How to Master Similar Hard LeetCode Problems

1. **Practice DP on subsets** – LeetCode problems `01`–`10` in the *Dynamic Programming* section.  
2. **Learn bit manipulation tricks** – `1<<i`, `mask ^ (1<<i)`, `mask & (1<<i)`.  
3. **Read editorial solutions** – many hard problems use the same pattern.  
4. **Write a generic “DP over hats” helper** – reuse across contests.  
5. **Profile your code** – ensure no hidden quadratic loops.

---

### 10. Final Thought

Mastering the *bitmask DP* pattern unlocks a whole universe of hard combinatorial assignment problems.  
The “Number of Ways to Wear Different Hats” is a textbook example:  
*10 people (small) → mask people → iterate hats (large)*.  
Once you internalise this switch, you can tackle **any** LeetCode Hard problem that at first glance seems intractable.

Happy coding! 🎩💻

---

#### Meta Description  
Learn how to solve the “Number of Ways to Wear Different Hats” LeetCode problem in Java, Python, and C++ using a concise bitmask DP. Get the full source code, complexity analysis, and a SEO‑friendly blog post that explains the good, the bad, and the ugly of this classic hard problem. Perfect for interview preparation and algorithm mastery.