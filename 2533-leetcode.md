---
title: LeetCode 2533. Number of Good Binary Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 2533 – Number of Good Binary Strings  
**Medium | 1 ≤ minLength ≤ maxLength ≤ 10⁵ | 1 ≤ oneGroup, zeroGroup ≤ maxLength**

> In a *good* binary string  
> 1. Length ∈ [minLength, maxLength]  
> 2. Every consecutive block of `1`s has a length that is a multiple of `oneGroup`  
> 3. Every consecutive block of `0`s has a length that is a multiple of `zeroGroup`  
> 4. (Zero is considered a multiple of everything)  

Return the number of good strings modulo 1 000 000 007.

---

## 🚀 High‑Level Idea

The constraints (maxLength ≤ 10⁵) forbid enumerating all strings.  
Instead, observe that a good string can be built **incrementally**:

* Starting from an empty string (length = 0, which satisfies the “multiple” rule).  
* Each step appends either  
  * `oneGroup` consecutive `1`s, **or**  
  * `zeroGroup` consecutive `0`s.

So if we know how many good strings of length `i` exist (`dp[i]`), we can get the counts for longer lengths by adding the two possible block sizes.

This is a classic *bottom‑up* dynamic programming recurrence.

```
dp[0] = 1  // empty string
for i = 1 … maxLength
    if i ≥ oneGroup: dp[i] += dp[i - oneGroup]
    if i ≥ zeroGroup: dp[i] += dp[i - zeroGroup]
    dp[i] %= MOD
```

The answer is the sum of `dp[i]` for all `i` in `[minLength, maxLength]`.

The recurrence runs in **O(maxLength)** time and needs **O(maxLength)** memory – easily fast enough for 10⁵.

---

## 💡  Code Walk‑Through (Java)

```java
import java.util.*;

class Solution {
    private static final int MOD = 1_000_000_007;

    public int goodBinaryStrings(int minLength, int maxLength,
                                 int oneGroup, int zeroGroup) {

        // dp[i] = number of good strings of length i
        int[] dp = new int[maxLength + 1];
        dp[0] = 1;                          // empty string

        long result = 0;

        for (int i = 1; i <= maxLength; i++) {
            if (i >= oneGroup) {
                dp[i] = (dp[i] + dp[i - oneGroup]) % MOD;
            }
            if (i >= zeroGroup) {
                dp[i] = (dp[i] + dp[i - zeroGroup]) % MOD;
            }
            if (i >= minLength) {
                result = (result + dp[i]) % MOD;
            }
        }

        return (int) result;
    }
}
```

**Key points**

| ✅ | Explanation |
|---|-------------|
| `dp[0] = 1` | Empty string counts as a valid prefix (zero is a multiple of everything). |
| `if (i >= oneGroup)` | Only extend when we have enough room for a new block. |
| `result += dp[i]` | Only sum when the length meets the minimum requirement. |

---

## 💡  Code Walk‑Through (Python)

```python
class Solution:
    MOD = 1_000_000_007

    def goodBinaryStrings(self, minLength: int, maxLength: int,
                          oneGroup: int, zeroGroup: int) -> int:

        dp = [0] * (maxLength + 1)
        dp[0] = 1                          # empty string
        result = 0

        for i in range(1, maxLength + 1):
            if i >= oneGroup:
                dp[i] = (dp[i] + dp[i - oneGroup]) % self.MOD
            if i >= zeroGroup:
                dp[i] = (dp[i] + dp[i - zeroGroup]) % self.MOD
            if i >= minLength:
                result = (result + dp[i]) % self.MOD

        return result
```

Same logic, just Pythonic syntax.

---

## 💡  Code Walk‑Through (C++)

```cpp
class Solution {
public:
    static const int MOD = 1'000'000'007;
    
    int goodBinaryStrings(int minLength, int maxLength,
                          int oneGroup, int zeroGroup) {
        vector<int> dp(maxLength + 1, 0);
        dp[0] = 1;                         // empty string
        long long res = 0;

        for (int i = 1; i <= maxLength; ++i) {
            if (i >= oneGroup)
                dp[i] = (dp[i] + dp[i - oneGroup]) % MOD;
            if (i >= zeroGroup)
                dp[i] = (dp[i] + dp[i - zeroGroup]) % MOD;
            if (i >= minLength)
                res = (res + dp[i]) % MOD;
        }
        return static_cast<int>(res);
    }
};
```

---

## 📈 Complexity Analysis

| | Java | Python | C++ |
|---|---|---|---|
| **Time** | `O(maxLength)` | `O(maxLength)` | `O(maxLength)` |
| **Space** | `O(maxLength)` | `O(maxLength)` | `O(maxLength)` |

Both time and space are linear in the maximum string length – perfectly fine for 10⁵.

---

## 🎯  “Good, the Bad, and the Ugly”

| Aspect | Good | Bad | Ugly |
|---|---|---|---|
| **Design** | Clear recurrence; bottom‑up DP | ✅ | |
| **Edge Cases** | Handles zero length as multiple; minLength=1 | ✅ | |
| **Readability** | Simple loops, comments | ✅ | |
| **Scalability** | Linear time/space | ✅ | |
| **Potential Pitfalls** | Forgetting modulo after each addition | ⚠️ | |
| **Memory Usage** | Only `maxLength+1` integers | ⚠️ | |
| **Alternate Approach** | Top‑down memoization (recursive) | ⚠️ | May hit recursion depth on large input |
| **Optimization** | None needed | ⚠️ | Bottom‑up is best |

**Why Bottom‑Up is the winner**  
- Avoids recursion stack limits.  
- Single pass over the length range.  
- No need for extra data structures beyond the `dp` array.

---

## 🎯  SEO‑Optimized Blog Post

> **Title**  
> *Cracking LeetCode 2533 – Number of Good Binary Strings – Java, Python & C++ DP Tutorial*  

> **Meta Description**  
> Master LeetCode 2533 with a clear dynamic programming solution in Java, Python, and C++. Learn the algorithm, complexity, and interview‑ready insights.  

> **Keywords**  
> LeetCode 2533, Number of Good Binary Strings, dynamic programming, coding interview, Java solution, Python solution, C++ solution, software engineer interview, algorithm design

---

### Introduction

When I first saw **LeetCode 2533 – Number of Good Binary Strings**, I thought it was just another “count strings” problem. The twist? The string blocks must be *multiples* of given group sizes.  
In an interview, the interviewer usually wants you to show two things:

1. You understand the constraints and can pick an efficient algorithm.  
2. You can translate that algorithm into clean, idiomatic code in your chosen language.

Below, I walk through the DP solution that runs in **O(n)** time, give you ready‑to‑paste Java, Python, and C++ snippets, and explain why this approach is perfect for a coding interview.

---

### Problem Restatement

A *good* binary string satisfies:

- Length ∈ `[minLength, maxLength]`.
- Every consecutive run of `1`s has a length that is a multiple of `oneGroup`.
- Every consecutive run of `0`s has a length that is a multiple of `zeroGroup`.

Return the number of good strings modulo `10⁹+7`.

Constraints make a naive enumeration impossible (`2^maxLength` strings). We need a *linear* solution.

---

### The Core Insight

A good string can be **grown** from an empty string by repeatedly adding a block of length `oneGroup` or `zeroGroup`.  
If we denote:

```
dp[i] = number of good strings of exact length i
```

then for any `i`:

```
dp[i] = (dp[i - oneGroup]  if i ≥ oneGroup) +
        (dp[i - zeroGroup] if i ≥ zeroGroup)
```

Because the empty string (length 0) satisfies the multiple condition, we start with `dp[0] = 1`.  
The final answer is the sum of `dp[i]` for all `i` in `[minLength, maxLength]`.

---

### Step‑by‑Step Implementation

1. **Initialize** `dp[0] = 1`.  
2. **Iterate** `i` from `1` to `maxLength`.  
3. **Update** `dp[i]` using the recurrence (careful with `i - block` only if it is non‑negative).  
4. **Modulo** the result after each addition to avoid overflow.  
5. **Accumulate** into the answer when `i ≥ minLength`.

This is what all three language solutions look like – just syntax differences.

---

### Java, Python, & C++ Code

*(Full code snippets above)*

> **Tip for the Interviewer**  
> “Did you remember to apply the modulo after each addition?” – The interviewer will test you on integer overflow.

---

### Complexity Discussion

- **Time**: We touch each index once – `O(maxLength)`.  
- **Space**: One array of size `maxLength+1` – `O(maxLength)`.  

With `maxLength ≤ 10⁵`, this runs in milliseconds on modern machines.

---

### Interview‑Ready Variations

| Variation | When to Use | Pros | Cons |
|---|---|---|---|
| **Bottom‑Up DP (the one above)** | Most interviews | Simple, no recursion, fast | None |
| **Top‑Down Memoization** | If you prefer recursion | Easier to write initially | Recursion depth can hit Python’s limit; slightly slower due to function calls |
| **Recursive + Memo** | For small constraints | Elegant functional style | Same depth risk |

---

### Why This Solution Rocks

| ✔️ | Explanation |
|---|-------------|
| *Linear time* | Handles 10⁵ in < 1 s. |
| *Single array* | Minimal memory overhead. |
| *Modular arithmetic* | Avoids 64‑bit overflow. |
| *Clear logic* | One loop, two conditionals – perfect for a clean interview answer. |

---

### Wrap‑Up

- **LeetCode 2533** is a great interview problem for testing *state compression* and *block‑based DP*.  
- The recurrence is **directly derived** from the problem’s “grow the string” description.  
- The provided Java, Python, and C++ code is production‑ready: fully commented, uses constants, and respects the modulo constraint.  

If you’re prepping for a *software engineer* interview, this solution demonstrates:

- Deep understanding of constraints.  
- Ability to design a DP recurrence.  
- Fluency in three of the most popular interview languages.

Good luck, and happy coding! 🚀

---

### Take‑away Code Snippets

| Language | Code |
|---|---|
| **Java** | [Java snippet](#java) |
| **Python** | [Python snippet](#python) |
| **C++** | [C++ snippet](#cpp) |

---

**Feel free to copy‑paste these solutions into your local editor or LeetCode submission panel. Happy solving!**