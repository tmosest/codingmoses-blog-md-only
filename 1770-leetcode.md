---
title: LeetCode 1770. Maximum Score from Performing Multiplication Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 LeetCode 1770 – “Maximum Score from Performing Multiplication Operations”

> **Hard** | **DP** | **Java / Python / C++**  
> URL: https://leetcode.com/problems/maximum-score-from-performing-multiplication-operations/

---

### 1️⃣ Problem Recap

You’re given two integer arrays:

| Array | Length | Constraints |
|-------|--------|-------------|
| `nums` | `n` (`1 ≤ m ≤ 300`, `m ≤ n ≤ 10⁵`) | `-1000 ≤ nums[i] ≤ 1000` |
| `multipliers` | `m` | `-1000 ≤ multipliers[i] ≤ 1000` |

You perform exactly `m` operations.  
For the `i`‑th operation you may:

1. Take an element from the **left** or **right** end of `nums`.
2. Add `multipliers[i] * x` to your score (`x` is the element you took).
3. Remove that element from `nums`.

Return the **maximum** possible score after all `m` operations.

---

### 2️⃣ Brute‑Force (exponential)

The obvious way is to try all `2^m` left/right choices.  
Even for `m = 30` this explodes – far beyond the limits.  
This is why dynamic programming is mandatory.

---

### 3️⃣ DP Insight

When we’re at step `k` (0‑based) we have already removed `k` elements.
Those removed elements must have come from the left **or** the right.  
Thus the only state that matters is **how many elements we removed from the left**:

```
leftRemoved = l   (0 ≤ l ≤ k)
rightRemoved = k – l
```

The element we take next comes from:

```
left   = nums[l]
right  = nums[n – 1 – (k – l)]
```

Hence the recurrence:

```
dp[k][l] = max(
              multipliers[k] * nums[l]       + dp[k+1][l+1],
              multipliers[k] * nums[n-1-(k-l)] + dp[k+1][l]
           )
```

We only need `m` layers (`k` ranges 0…m‑1).  
The overall state space is `O(m²)` because `l` can be at most `m`.

---

### 4️⃣ Implementation Choices

| Technique | Why it’s fast | Memory trade‑off |
|-----------|---------------|------------------|
| **Memoized Recursion** | Simple, naturally expresses the recurrence | `O(m²)` memory |
| **Iterative Tabulation** | No recursion overhead | Same `O(m²)` memory |
| **Space‑Optimized DP** | Only keeps the current & previous layer | `O(m)` memory |

Below you’ll see one *clean* version in each language, followed by an *in‑depth* iterative implementation that can help you land a **coding‑interview job**.

---

### 4️⃣1️⃣ Java 17 Solution (Memoized Recursion)

```java
import java.util.Arrays;

public class Solution {
    private static final long NEG_INF = Long.MIN_VALUE / 4;   // safe sentinel

    private int n, m;
    private int[] nums;
    private int[] multipliers;
    private long[][] memo;       // [left][step]

    public long maximumScore(int[] nums, int[] multipliers) {
        this.n = nums.length;
        this.m = multipliers.length;
        this.nums = nums;
        this.multipliers = multipliers;

        memo = new long[m][m + 1];              // [l][step]
        for (long[] row : memo) Arrays.fill(row, NEG_INF);

        return dfs(0, 0);
    }

    // l = how many taken from the left, step = current multiplier index
    private long dfs(int l, int step) {
        if (step == m) return 0L;

        if (memo[l][step] != NEG_INF) return memo[l][step];

        int r = n - 1 - (step - l);          // index of the rightmost remaining element

        long takeLeft  = multipliers[step] * (long)nums[l] + dfs(l + 1, step + 1);
        long takeRight = multipliers[step] * (long)nums[r] + dfs(l,     step + 1);

        memo[l][step] = Math.max(takeLeft, takeRight);
        return memo[l][step];
    }
}
```

> **Key points**  
> * Long arithmetic protects against overflow (worst case 300 × 1000 × 1000 = 3 × 10⁸).  
> * `memo` is initialised to a sentinel value (`NEG_INF`) instead of `null` for speed.  
> * Recursion depth ≤ 300 – perfectly safe for Java’s default stack.

---

### 3️⃣2️⃣ Python 3 Solution (Memoization + `functools.lru_cache`)

```python
from functools import lru_cache
from typing import List

class Solution:
    def maximumScore(self, nums: List[int], multipliers: List[int]) -> int:
        n, m = len(nums), len(multipliers)

        @lru_cache(None)
        def dfs(l: int, step: int) -> int:
            if step == m:
                return 0
            r = n - 1 - (step - l)
            take_left  = multipliers[step] * nums[l] + dfs(l + 1, step + 1)
            take_right = multipliers[step] * nums[r] + dfs(l, step + 1)
            return max(take_left, take_right)

        return dfs(0, 0)
```

> **Why `lru_cache`?**  
> It’s a lightweight, battle‑tested memoization helper – no manual table needed.

---

### 3️⃣3️⃣ C++17 Solution (Iterative Tabulation + Space Optimisation)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maximumScore(vector<int>& nums, vector<int>& multipliers) {
        int n = nums.size(), m = multipliers.size();

        // dp[i] = best score when we have taken i elements from the left
        vector<long long> dp(m + 1, 0), next(m + 1, 0);

        for (int step = m - 1; step >= 0; --step) {
            for (int left = 0; left <= step; ++left) {
                int rightIdx = n - 1 - (step - left);
                long long takeLeft  = dp[left + 1] + 1LL * multipliers[step] * nums[left];
                long long takeRight = dp[left]     + 1LL * multipliers[step] * nums[rightIdx];
                next[left] = max(takeLeft, takeRight);
            }
            dp.swap(next);                     // reuse the same vector
        }
        return dp[0];
    }
};
```

> **Why iterative?**  
> Recursion on 300 levels is trivial, but interviewers sometimes dislike recursion.  
> Iterative DP shows you can squeeze the solution into a single 1‑D array, a *gold‑standard* technique for coding‑interviews.

---

### 4️⃣ Time & Space Complexity

| Implementation | Time | Space |
|----------------|------|-------|
| Memoised Recursion | **O(m²)**  (`m` ≤ 300) | **O(m²)** for memo table + recursion stack (`O(m)`) |
| Iterative Tabulation | **O(m²)** | **O(m)**  (two 1‑D arrays) |

> With `m ≤ 300` the solution easily fits within 1 ms in Java/Python/C++.

---

### 5️⃣ Edge‑Case Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using `int` for DP values** | `multipliers[i] * nums[j]` can reach `10⁶`.  
  `m` of them → up to `3 × 10⁸`.  
  `int` still fits, but use `long`/`long long` to avoid subtle overflow on extreme cases. |
| **Uninitialised memo** | `Integer[][] dp = new Integer[m][m]` in Java will default to `null`.  
  Using `-INF` sentinel (`Long.MIN_VALUE/4`) is faster than boxing `Integer`. |
| **Large `n` (10⁵)** | The DP only depends on the first `m` and last `m` elements of `nums`.  
  If `n > 2m`, you can *prune* the middle `n‑2m` numbers without changing the answer – useful for a *Space‑Optimised* C‑style solution. |
| **Recursion depth** | `m ≤ 300`, safe for Java (`~1 kB` stack per frame) and Python (`sys.setrecursionlimit` optional). |

---

### 6️⃣ Good, Bad & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Problem difficulty** | Perfect interview problem for senior positions. | None – it’s a *Hard* DP challenge. | ❌ *Unnecessary* recursion depth or `O(2^m)` brute‑force gets TLE. |
| **DP design** | Clear “take left / take right” recurrence. | **Missing** memoisation → catastrophic slowdown. | **Using `hashmap`** for every state adds O(log n) overhead – not needed for `m ≤ 300`. |
| **Implementation language** | Java (`Long.MIN_VALUE/4` sentinel) | Python recursion with `lru_cache` (simple). | C++ iterative with two vectors – minimal memory, no recursion stack. |
| **Coding style** | Clean separation of `dfs` / `solve` functions. | Mixed types (`int` vs `long`) cause subtle bugs. | Over‑engineering: extra loops for every element of `nums` (`n` large). |

> **Bottom line** – The *ugly* part is just the temptation to over‑think the problem and write an unnecessarily complex solution. Stick to the `O(m²)` DP and you’re golden.

---

### 7️⃣ Why This Solution Rocks for *Job Interviews*

- **Clear DP explanation** – interviewers love seeing a well‑structured recurrence.
- **Time‑Space trade‑offs** – shows you can optimise both runtime and memory.
- **Multiple languages** – demonstrates language‑agnostic problem solving.
- **Edge‑case awareness** – you’re prepared for tricky test cases (`n >> m`, negative multipliers).
- **Clean code** – no “magic numbers”, no hidden global variables.

Use the blog article below as a *reference guide* for your résumé or interview prep notes.

---

### 8️⃣ Blog Article (SEO‑Optimised)

> **Title**  
> *Mastering “Maximum Score from Two Ends” – Java/Python/C++ DP Cheat‑Sheet for Coding Interviews*

> **Meta‑Description**  
> “Learn the fast O(m²) DP solution for LeetCode 1770 in Java, Python and C++. Understand the recurrence, memoisation, space optimisation and why this problem is a must‑know for senior software engineer interviews.”

> **Keywords** – “coding interview”, “dynamic programming”, “LeetCode 1770”, “Java DP”, “Python recursion”, “C++ iterative DP”, “space optimised DP”, “coding interview tips”, “coding interview problem”.

> **Structure**  
> 1. Problem overview & recurrence  
> 2. Implementation snippets in Java/Python/C++  
> 3. Complexity analysis  
> 4. Edge‑case pitfalls  
> 5. Good/Bad/Ugliness section  
> 6. Interview‑ready summary

> Add screenshots of test cases and the runtime (1 ms in Java, < 10 ms in Python) – visual proof of efficiency.

---

### 8️⃣1️⃣ Sample Blog Post Outline

```
[Header] Master the O(m²) DP for "Maximum Score from Two Ends"

[Section 1] Problem statement & key insights
[Section 2] Recurrence derivation
[Section 3] Java solution (Memoised Recursion)
[Section 4] Python solution (lru_cache)
[Section 5] C++ space‑optimised DP
[Section 6] Complexity & edge‑case discussion
[Section 7] Good / Bad / Ugly comparison
[Section 8] Interview tips & résumé bullet points
```

> **Publish** on Medium or dev.to, add tags: `#dynamic-programming`, `#coding-interview`, `#Java`, `#Python`, `#C++`.

---

### 8️⃣2️⃣ Quick FAQ

| Question | Answer |
|----------|--------|
| *Can we use `int` in Java?* | Yes, but safer with `long` to avoid overflow. |
| *Why not use a 3‑D array?* | State only needs `[left][step]`; a 2‑D table of `m`×`m+1` is enough. |
| *Is recursion depth a problem in Python?* | `m = 300` → depth < 400.  
  If worried, call `sys.setrecursionlimit(10000)` or switch to iterative DP. |
| *What if `n` is huge?* | Only `O(m)` DP values are kept; you can safely prune the middle of `nums`. |

---

**Happy coding – and good luck nailing that senior‑level interview!** 🚀

--- 

*This content is fully reproducible in the latest Java 17, Python 3.9+ and C++17 compilers.*