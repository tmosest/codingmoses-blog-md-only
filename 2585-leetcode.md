---
title: LeetCode 2585. Number of Ways to Earn Points - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2585 – Number of Ways to Earn Points  
> **Hard** – LeetCode  

---

## 1.  Problem Recap  

You’re given

* `target` – the total points you want to score (`1 ≤ target ≤ 1000`).
* `types[i] = [countᵢ, markᵢ]` –  
  * there are `countᵢ` indistinguishable questions of type `i`  
  * each such question is worth `markᵢ` points  
  * `1 ≤ countᵢ, markᵢ ≤ 50`, `1 ≤ n = types.length ≤ 50`

How many different ways can you reach **exactly** `target` points?  
Return the answer modulo `1 000 000 007`.

> **Example**  
> `target = 6, types = [[6,1],[3,2],[2,3]]` → **7** ways.

---

## 2.  High‑Level Idea – Bounded Knapsack DP

The problem is a classic *bounded knapsack* (or “limited coins”) counting problem.  
We need to count, for each possible point value `p` from `0` to `target`, how many ways we can obtain exactly `p` points using the available question types, respecting the per‑type limit (`countᵢ`).

Let  

```
dp[p] = number of ways to reach exactly p points
```

Initialize `dp[0] = 1` (there is one way to reach 0 points – take no questions).

For each question type `(cnt, val)` we update the DP **in reverse**:

```text
for i from target down to 0:
    for k from 1 to cnt:
        if i >= k * val:
            dp[i] += dp[i - k * val]
```

Reversing the outer loop guarantees that when we read `dp[i - k * val]` we’re still using the values from the *previous* type, not the ones we’re currently writing.  
All operations are performed modulo `MOD = 1 000 000 007`.

The time complexity is  
`O(target * n * max(countᵢ)) ≤ 1000 * 50 * 50 = 2.5 × 10⁶`,  
well within the limits.  
The space complexity is `O(target)`.

---

## 3.  Code

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

> *All three solutions use the same DP logic – only the syntax differs.*

---

### 3.1  Java

```java
import java.util.*;

class Solution {
    private static final int MOD = 1_000_000_007;

    public int waysToReachTarget(int target, int[][] types) {
        int[] dp = new int[target + 1];
        dp[0] = 1;

        for (int[] t : types) {
            int count = t[0];
            int mark  = t[1];

            // reverse order: from target down to 0
            for (int i = target; i >= 0; i--) {
                // try taking k questions of this type
                for (int k = 1; k <= count; k++) {
                    int cost = k * mark;
                    if (i < cost) break;            // cannot take k questions
                    dp[i] = (dp[i] + dp[i - cost]) % MOD;
                }
            }
        }
        return dp[target];
    }
}
```

---

### 3.2  Python

```python
class Solution:
    MOD = 1_000_000_007

    def waysToReachTarget(self, target: int, types: List[List[int]]) -> int:
        dp = [0] * (target + 1)
        dp[0] = 1

        for cnt, val in types:
            # reverse order
            for i in range(target, -1, -1):
                for k in range(1, cnt + 1):
                    cost = k * val
                    if i < cost:
                        break
                    dp[i] = (dp[i] + dp[i - cost]) % self.MOD

        return dp[target]
```

---

### 3.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    static constexpr int MOD = 1'000'000'007;

public:
    int waysToReachTarget(int target, vector<vector<int>>& types) {
        vector<int> dp(target + 1, 0);
        dp[0] = 1;

        for (const auto &t : types) {
            int count = t[0];
            int mark  = t[1];

            for (int i = target; i >= 0; --i) {          // reverse order
                for (int k = 1; k <= count; ++k) {
                    int cost = k * mark;
                    if (i < cost) break;
                    dp[i] = (dp[i] + dp[i - cost]) % MOD;
                }
            }
        }
        return dp[target];
    }
};
```

---

## 4.  Blog Article – “Knapsack, DP & Why This Matters for Your Next Job”

> **Length:** ~1,200 words  
> **Audience:** Software engineers preparing for LeetCode or system‑design interviews  
> **Tone:** Friendly, insightful, sprinkled with interview‑hints

---

# 5.  “Bounded Knapsack”: The Good, The Bad, and the Ugly

> *What every recruiter looks for in a candidate’s code: clarity, efficiency, and a dash of cleverness.*

---

### 5.1  The Good – Why Bounded Knapsack Wins

| **Aspect** | **Why It’s Good** | **What Recruiters Love** |
|------------|-------------------|---------------------------|
| **Time‑Complexity** | `O(target · n · maxCount)` = 2.5 M operations | Shows you can solve *Hard* LeetCode in < 0.1 s |
| **Space‑Efficiency** | `O(target)` memory (≤ 4 KB in Java/C++/Python) | Demonstrates low‑footprint mindset |
| **Scalability** | Works for 1000 points, 50 types, 50 questions each | Handles worst‑case constraints |
| **Determinism** | DP guarantees correct counting | Avoids pitfalls of back‑tracking explosion |

---

### 5.2  The Bad – Common Pitfalls

1. **Updating DP in the wrong order**  
   *If you loop `i` from `0` to `target`, you’ll double‑count* (each type can be reused unlimited times).  
   **Fix:** reverse the loop.

2. **Using `int` without modulus**  
   The raw count can grow astronomically (`C(50, 0) * …`).  
   **Fix:** always `dp[i] %= MOD`.

3. **Ignoring per‑type limits**  
   A naïve “unbounded” coin DP would over‑count.  
   **Fix:** nested loop over `k = 1 … cnt`.

4. **Recursion + Memoization overhead**  
   Recursion depth may hit 51 (safe), but Python’s recursion limit is 1000 – still fine, but DP is more cache‑friendly and faster.

---

### 5.3  The Ugly – A Less‑Efficient Brute‑Force

> *A straightforward DFS that tries every combination is O(`cntᵢ^n`)*.  
> Not feasible for `target = 1000, n = 50`.

```python
def bad_solution(target, types):
    from functools import lru_cache

    @lru_cache(None)
    def dfs(idx, remaining):
        if remaining == 0:
            return 1
        if idx == len(types) or remaining < 0:
            return 0
        cnt, val = types[idx]
        return sum(dfs(idx + 1, remaining - k * val) for k in range(cnt + 1)) % MOD

    return dfs(0, target)
```

While *nice to read*, this approach would time‑out on large inputs – a classic “ugly” trade‑off.

---

## 6.  Why This Problem (and Solution) is a Great Interview Show‑stopper

1. **Demonstrates Mastery of DP**  
   Bounded knapsack is a staple. Solving it correctly shows you can handle *state* and *transition* design.

2. **Shows Attention to Constraints**  
   Using reverse DP to respect limits rather than a naïve approach speaks to thoughtful optimization.

3. **Highlights Clean Coding Practices**  
   Modular, constant‑time modular arithmetic, and clear loops make your code readable to reviewers.

4. **Cross‑Language Versatility**  
   Being able to translate the same logic to Java, Python, or C++ proves language agnosticism—valuable in polyglot teams.

---

## 7.  SEO‑Friendly Wrap‑Up (Target Keywords: “bounded knapsack”, “LeetCode 2585”, “DP interview”, “earn points DP”, “bounded coin change”)

> **Title**: *Master LeetCode 2585 – Bounded Knapsack DP for “Number of Ways to Earn Points”*  
> **Meta Description**: *Solve LeetCode 2585 (Number of Ways to Earn Points) in Java, Python, and C++ using an efficient bounded knapsack DP. Learn the algorithm, complexity, and interview‑friendly code.*  
> **Tags**: #DynamicProgramming #Knapsack #LeetCode #Interview #CodingInterview #Algorithm

---

### 8.  TL;DR (One‑Line Summary)

Use a reverse‑order bounded‑knapsack DP:

```text
dp[0] = 1
for each type (cnt, val):
    for i = target … 0:
        for k = 1 … cnt:
            if i >= k*val:
                dp[i] = (dp[i] + dp[i-k*val]) % MOD
```

This runs in ~2.5 M ops and uses `O(target)` memory – perfect for LeetCode 2585.

---

Happy coding! 🚀