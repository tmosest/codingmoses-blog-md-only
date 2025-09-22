---
title: LeetCode 805. Split Array With Same Average - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Overview – Split Array With Same Average  
**LeetCode #805** – *Hard*  

> Given an array `nums`, split it into two non‑empty sub‑arrays **A** and **B** (they can be formed by re‑ordering the original elements).  
> If `average(A) == average(B)` return `true`, otherwise return `false`.  
>  
> Constraints:  
> * `1 ≤ nums.length ≤ 30`  
> * `0 ≤ nums[i] ≤ 10⁴`

Because the array length is tiny (≤ 30) we can afford exponential‑time solutions that are clever enough to prune impossible branches.

---

## 2. Intuition & Key Insight  

Let  

```
totalSum   = sum(nums)
n          = nums.length
```

If the average of a chosen subset of size `k` equals the average of the whole array, then the subset’s sum must be

```
targetSum = totalSum * k / n
```

For that to be an integer we need `(totalSum * k) % n == 0`.  
So we only need to test *possible* subset sizes `k` (1 … n/2) that satisfy the divisibility condition and check whether there exists a subset of that size whose sum equals `targetSum`.

Finding a subset with a specific size and sum is a classic *subset‑sum* problem, but with an extra dimension (the size).  
Two approaches that fit perfectly:

1. **Recursive DP + Memoization** – explore the search tree while caching already‑seen states.  
2. **Meet‑in‑the‑Middle** – split the array into two halves, enumerate all subset sums in each half, then combine them.

Both have the same theoretical worst‑case complexity `O(2^(n/2))`, but the recursive DP is often easier to read and implement in interview settings.

---

## 3. Why the Recursive DP is “Good”

* **Readability** – A single recursive function (`dfs`) captures the idea “pick or skip an element”.
* **Early Pruning** – If the remaining elements cannot fill the required size (`k + i > n`) or the target becomes negative, we backtrack immediately.
* **Memoization** – We never recompute the same `(i, k, target)` triple, so the number of distinct states is bounded by `n * (n/2) * target` (still small because `n ≤ 30`).
* **Deterministic** – No randomisation or multi‑pass passes over the data – the algorithm behaves predictably.

---

## 4. Why “Bad” or “Ugly” (and how to avoid it)

| Bad/ Ugly | Why | Fix |
|-----------|-----|-----|
| **Using a string key** like `"target,k,i"` in Java | Inefficient hashing, extra memory, risk of key collisions | Use a `long` that packs the three integers (`(target << 20) | (k << 10) | i`) or a custom object with proper `hashCode`. |
| **Unbounded recursion depth** in languages that don’t optimise tail calls | Stack overflow for larger inputs (though `n ≤ 30` is safe) | Use an iterative DP or limit recursion depth by converting to loops. |
| **Checking all `k` blindly** even when `(totalSum * k) % n != 0` | Extra wasted work | Skip those `k` values early. |
| **Re‑allocating memory** on every DFS call | Garbage‑collection overhead | Use an LRU cache or `Map` that reuses key objects. |
| **Not handling edge cases** like single element arrays | Wrong answer | Return `false` when `n == 1` because we can’t split a single element into two non‑empty sets. |

---

## 5. Solution – Recursive DP (Memoization)

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**. All three solve the problem in *O(2^(n/2))* time and *O(n * target)* memory, and are guaranteed to run fast for the maximum input size (`n = 30`).

> **Tip for interviews**: When asked to solve this problem, first mention the divisibility check, then say you’ll use recursion + memoization. That shows you know the core insight before diving into code.

### 5.1 Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public boolean splitArraySameAverage(int[] nums) {
        int n = nums.length;
        if (n <= 1) return false;                 // cannot split into two non‑empty arrays

        int totalSum = 0;
        for (int x : nums) totalSum += x;

        // try every possible subset size k (1 … n/2)
        for (int k = 1; k <= n / 2; k++) {
            if ((long) totalSum * k % n != 0) continue; // target would not be integer
            int target = (int) ((long) totalSum * k / n);
            if (dfs(nums, 0, k, target, new HashMap<>()))
                return true;
        }
        return false;
    }

    // memo key: (i, k, target) -> Boolean
    private boolean dfs(int[] nums, int i, int k, int target,
                        Map<State, Boolean> memo) {
        if (k == 0) return target == 0;
        if (target < 0 || k + i > nums.length) return false;
        State key = new State(i, k, target);
        if (memo.containsKey(key)) return memo.get(key);

        // pick nums[i] or skip it
        boolean res = dfs(nums, i + 1, k - 1, target - nums[i], memo)
                     || dfs(nums, i + 1, k, target, memo);
        memo.put(key, res);
        return res;
    }

    // Immutable key for memoization
    private static final class State {
        final int idx, size, target;
        State(int idx, int size, int target) {
            this.idx = idx; this.size = size; this.target = target;
        }
        @Override public boolean equals(Object o) {
            if (this == o) return true;
            if (!(o instanceof State)) return false;
            State other = (State) o;
            return idx == other.idx && size == other.size && target == other.target;
        }
        @Override public int hashCode() {
            int h = 17;
            h = 31 * h + idx;
            h = 31 * h + size;
            h = 31 * h + target;
            return h;
        }
    }
}
```

**Why this is good**  
* `State` is immutable and uses a proper `hashCode() / equals()` pair, so the `HashMap` behaves predictably.  
* The recursive function is concise and uses the exact three parameters we need for memoization.

### 5.2 Python

```python
from functools import lru_cache
from typing import List

class Solution:
    def splitArraySameAverage(self, nums: List[int]) -> bool:
        n = len(nums)
        if n <= 1:
            return False

        total_sum = sum(nums)

        # check all valid subset sizes
        for k in range(1, n // 2 + 1):
            if (total_sum * k) % n != 0:
                continue
            target = (total_sum * k) // n

            @lru_cache(maxsize=None)
            def dfs(i: int, remaining_k: int, remaining_target: int) -> bool:
                if remaining_k == 0:
                    return remaining_target == 0
                if remaining_target < 0 or remaining_k + i > n:
                    return False
                # choose or skip current element
                return (dfs(i + 1, remaining_k - 1, remaining_target - nums[i]) or
                        dfs(i + 1, remaining_k, remaining_target))

            if dfs(0, k, target):
                return True
        return False
```

**Why this is good**  
* The `@lru_cache` decorator handles memoization automatically.  
* The recursion depth is safe (`n ≤ 30`).  
* The code is only a few lines, making it ideal for interview quick‑write sessions.

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool splitArraySameAverage(vector<int>& nums) {
        int n = nums.size();
        if (n <= 1) return false;

        int totalSum = 0;
        for (int x : nums) totalSum += x;

        // memoization key: (idx, k, target)
        unordered_map<long long, bool> memo;

        auto key = [](int idx, int k, int target) -> long long {
            return (static_cast<long long>(idx) << 40) |
                   (static_cast<long long>(k)   << 20) |
                   static_cast<long long>(target);
        };

        function<bool(int,int,int)> dfs = [&](int idx, int k, int target) -> bool {
            if (k == 0) return target == 0;
            if (target < 0 || k + idx > n) return false;
            long long id = key(idx, k, target);
            auto it = memo.find(id);
            if (it != memo.end()) return it->second;

            bool res = dfs(idx + 1, k - 1, target - nums[idx]) ||
                       dfs(idx + 1, k, target);
            memo[id] = res;
            return res;
        };

        for (int k = 1; k <= n / 2; ++k) {
            if ((1LL * totalSum * k) % n != 0) continue;
            int target = (int)((1LL * totalSum * k) / n);
            if (dfs(0, k, target)) return true;
        }
        return false;
    }
};
```

**Why this is good**  
* The `key` function packs the three integers into a single 64‑bit integer, giving O(1) look‑ups in the unordered map.  
* The recursive lambda keeps the logic compact.

---

## 6. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Recursive DP (Memoization) | `O(2^(n/2))` (worst case) | `O(n * target)` ~ `O(2^(n/2))` (memo entries) |
| Meet‑in‑the‑Middle | `O(2^(n/2))` | `O(2^(n/2))` for storing all subset sums |

With `n = 30`, `2^(n/2) = 2^15 ≈ 32,768`, which comfortably fits in memory and runs in a few milliseconds on modern CPUs.

---

## 7. Take‑away for Interviewers

1. **Mention the divisibility rule** first.  
2. **Explain the search problem**: “subset of size `k` with sum `target`”.  
3. **Choose recursion + memoization** – show you can write clean, memoised DFS.  
4. **Optional**: If asked for an even faster constant‑factor solution, suggest Meet‑in‑the‑Middle.

This problem demonstrates mastery over *dynamic programming*, *subset‑sum*, and *bit‑mask tricks*, making it a staple in technical interview toolkits.

---

## 7. Final Thought

Recursive DP + Memoization isn’t just a quick hack; it’s a proven, interview‑friendly strategy that balances **conceptual elegance** and **practical performance**.  
Feel confident writing it in any language, and be ready to explain the early pruning and memoization choices—you’ve just turned a seemingly complex problem into a concise, efficient solution!