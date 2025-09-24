---
title: LeetCode 3533. Concatenated Divisibility - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 “Concatenated Divisibility” – A Full‑Stack Deep Dive (Java | Python | C++)  
> *Master this LeetCode Hard in 30 min → Land your dream software‑engineering role.*

---

## 1.  Problem Statement

> You’re given an array `nums` of **positive** integers (size ≤ 13) and a positive integer `k` (≤ 100).  
> A *permutation* of `nums` is said to form a **divisible concatenation** if the number obtained by concatenating the decimal representation of the integers in that order is divisible by `k`.  
> **Return the lexicographically smallest permutation** that satisfies the condition.  
> If no such permutation exists return an empty list.

**Examples**

| `nums` | `k` | Result |
|--------|-----|--------|
| `[3,12,45]` | 5 | `[3,12,45]` |
| `[10,5]` | 10 | `[5,10]` |
| `[1,2,3]` | 5 | `[]` |

---

## 2.  Why Is This Hard?

* The naïve approach enumerates all `n!` permutations → impossible even for `n = 13`.  
* Concatenating numbers and checking divisibility would overflow `int64` for many cases.  
* We must keep track of **remainder modulo k** as we build the concatenation.  
* We also need the **lexicographically smallest** valid order – that’s an extra optimization.

The standard solution is a **DP + Bitmask** with recursion + memoization – exactly the pattern used for “Traveling Salesman” type problems but with a remainder state.

---

## 3.  High‑Level Idea

1. **State**  
   * `mask` – bitmask of the numbers that have already been placed.  
   * `rem` – remainder of the number formed so far, modulo `k`.  

2. **Transition**  
   For each unused number `x`:
   * Compute `len(x)` – number of decimal digits of `x`.  
   * Compute `pow10[len(x)] % k` once at the beginning.  
   * New remainder:  
     ```text
     newRem = (rem * pow10[len(x)] + x) % k
     ```
   * Recurse on `mask | (1<<idx)` and `newRem`.

3. **Base**  
   When all bits are set (`mask == all`):  
   * If `rem == 0` → a valid permutation, return empty list.  
   * Otherwise → impossible, return `null`.

4. **Lexicographic Minimisation**  
   In the recursion, iterate numbers **sorted in ascending order**.  
   As soon as we find a *valid* child, prepend the current number to its solution and stop – because the first success will be the lexicographically smallest (since we tried numbers in ascending order).

5. **Memoisation**  
   Store results in a `Map<Long, List<Integer>>` or a 2‑D array `dp[mask][rem]` to avoid recomputation.  
   Key = `(mask << 7) | rem` (since `k ≤ 100`, 7 bits are enough).

---

## 4.  Code Implementations

Below are clean, self‑contained solutions in **Java**, **Python**, and **C++**.  
All three use the same DP‑mask logic, so you can pick your favourite language for the interview.

> **Tip** – Show this code on your GitHub or in a `leetcode_solution` folder. Recruiters love clean, testable snippets.

### 4.1 Java

```java
import java.util.*;

public class Solution {

    // -------------------------------------------------------
    // Public API required by LeetCode
    // -------------------------------------------------------
    public int[] concatenatedDivisibility(int[] nums, int k) {
        int n = nums.length;
        int totalMask = (1 << n) - 1;

        // Pre‑compute 10^len(x) % k for every number
        int[] pow10Mod = new int[n];
        for (int i = 0; i < n; i++) {
            int len = numDigits(nums[i]);
            pow10Mod[i] = modPow10(len, k);
        }

        // Sort once – ensures we try candidates in ascending order
        int[][] indexed = new int[n][2];          // {originalIndex, value}
        for (int i = 0; i < n; i++) indexed[i] = new int[]{i, nums[i]};
        Arrays.sort(indexed, Comparator.comparingInt(a -> a[1]));

        // Memo table: mask → rem → solution
        List<Integer>[][] memo = new List[1 << n][k];
        List<Integer> best = dfs(0, 0, totalMask, k, nums, pow10Mod, indexed, memo);

        if (best == null) return new int[0];
        int[] ans = new int[best.size()];
        for (int i = 0; i < best.size(); i++) ans[i] = best.get(i);
        return ans;
    }

    // -------------------------------------------------------
    // Recursive DP with memoisation
    // -------------------------------------------------------
    private List<Integer> dfs(int mask, int rem,
                              int totalMask, int k,
                              int[] nums,
                              int[] pow10Mod,
                              int[][] indexed,
                              List<Integer>[][] memo) {

        if (mask == totalMask) {                 // all numbers placed
            return rem == 0 ? new ArrayList<>() : null;
        }
        if (memo[mask][rem] != null) return memo[mask][rem];

        List<Integer> answer = null;

        // Try every unused number in ascending order
        for (int[] pair : indexed) {
            int idx = pair[0], val = pair[1];
            if ((mask & (1 << idx)) != 0) continue;   // already used

            int newRem = (int)((rem * (long)pow10Mod[idx] + val) % k);
            List<Integer> child = dfs(mask | (1 << idx), newRem,
                                      totalMask, k, nums, pow10Mod,
                                      indexed, memo);
            if (child != null) {                      // first hit is lexicographically smallest
                child.add(0, val);                    // prepend current number
                answer = child;
                break;
            }
        }

        memo[mask][rem] = answer;
        return answer;
    }

    // Utility: number of decimal digits
    private int numDigits(int x) {
        int cnt = 0;
        while (x > 0) { cnt++; x /= 10; }
        return cnt;
    }

    // 10^len % k – computed by repeated multiplication
    private int modPow10(int len, int k) {
        long p = 1 % k;
        for (int i = 0; i < len; i++) p = (p * 10) % k;
        return (int)p;
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def concatenatedDivisibility(self, nums: List[int], k: int) -> List[int]:
        n = len(nums)
        total = (1 << n) - 1

        # Pre‑compute (10^len(x)) % k
        pow10 = [0] * n
        for i, val in enumerate(nums):
            digits = len(str(val))
            pow10[i] = pow(10, digits, k)

        # Sort indices by value to get lexicographic order
        idxs = sorted(range(n), key=lambda i: nums[i])

        memo: Dict[Tuple[int, int], Optional[List[int]]] = {}

        def dfs(mask: int, rem: int) -> Optional[List[int]]:
            if mask == total:
                return [] if rem == 0 else None
            key = (mask, rem)
            if key in memo:
                return memo[key]

            for i in idxs:
                if mask & (1 << i):      # already used
                    continue
                new_rem = (rem * pow10[i] + nums[i]) % k
                child = dfs(mask | (1 << i), new_rem)
                if child is not None:
                    memo[key] = [nums[i]] + child
                    return memo[key]

            memo[key] = None
            return None

        res = dfs(0, 0)
        return res if res is not None else []
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> concatenatedDivisibility(vector<int>& nums, int k) {
        int n = nums.size();
        int allMask = (1 << n) - 1;

        // Pre‑compute 10^len(x) % k
        vector<int> pow10mod(n);
        for (int i = 0; i < n; ++i) {
            int len = 0, tmp = nums[i];
            while (tmp) { len++; tmp /= 10; }
            long long p = 1;
            for (int j = 0; j < len; ++j) p = (p * 10) % k;
            pow10mod[i] = (int)p;
        }

        // Sort indices for lexicographic order
        vector<int> idx(n);
        iota(idx.begin(), idx.end(), 0);
        sort(idx.begin(), idx.end(),
             [&](int a, int b){ return nums[a] < nums[b]; });

        // DP table: memo[mask][rem] = optional permutation
        vector<vector<vector<int>>> memo(1<<n, vector<vector<int>>(k));
        vector<vector<bool>> vis(1<<n, vector<bool>(k,false));

        function<vector<int>(int,int)> dfs = [&](int mask, int rem) -> vector<int> {
            if (mask == allMask) {
                return rem == 0 ? vector<int>() : vector<int>{-1};
            }
            if (vis[mask][rem]) return memo[mask][rem];
            vis[mask][rem] = true;

            for (int id : idx) {
                if (mask & (1<<id)) continue;
                int newRem = (int)((rem * 1LL * pow10mod[id] + nums[id]) % k);
                vector<int> child = dfs(mask | (1<<id), newRem);
                if (!child.empty() && child[0] != -1) {   // found
                    child.insert(child.begin(), nums[id]);
                    memo[mask][rem] = child;
                    return child;
                }
            }
            memo[mask][rem] = vector<int>{-1};
            return memo[mask][rem];
        };

        vector<int> ans = dfs(0, 0);
        if (!ans.empty() && ans[0] == -1) ans.clear();   // no solution
        return ans;
    }
};
```

> **Why the `-1` sentinel?**  
> It’s a lightweight “impossible” flag that avoids `nullptr` checks in C++.

---

## 5.  Complexity Analysis

| DP Version | Time | Space |
|------------|------|-------|
| **Recursive + Memo** (all 3 languages) | **O( 2ⁿ · k · n )** – each state checks every unused number.  
| | For `n ≤ 13`, `k ≤ 100`, that’s at most `8192 · 100 · 13 ≈ 10⁷` operations – easily < 0.1 s on modern CPUs. |
| | **Space**: `dp[mask][rem]` → `2ⁿ · k` states, each storing a list of up to `n` ints:  
  `O( 2ⁿ · k · n )` ≈ a few megabytes. |

*The sorted‑first‑success trick reduces the actual branching: as soon as we hit a valid child we stop looping, so in practice the algorithm is *linear in the number of states*.*

---

## 6.  Edge‑Case Checklist

| Test | Why it matters |
|------|----------------|
| **All numbers of one digit** – e.g. `[11,22,33]`, `k = 3` | `pow10Mod` becomes `10 % 3 = 1` → transition simplifies. |
| **Large numbers (≥ 10⁹)** – e.g. `[999999999, 123456]` | We never construct the full concatenated integer – only remainders are kept. |
| **k = 1** | Every permutation is valid – the answer is just the sorted array. |
| **No solution** | Ensure you return `[]` instead of `[null]` or `-1`. |
| **Duplicate values** | Problem guarantees *distinct* integers, so no special handling required. |

---

## 7.  How to Test

```bash
# Java
javac Solution.java
# Python
python - <<'PY'
from solution import Solution
print(Solution().concatenatedDivisibility([3,12,45],5))
PY
# C++
g++ -std=c++17 solution.cpp && ./a.out
```

Make sure you add unit tests for the three examples above and a few random cases (`random.seed(0)` for reproducibility).

---

## 8.  Frequently Asked Interview Questions

| Question | Short Answer |
|----------|--------------|
| *Can we do this without sorting?* | Yes, but you’d need to generate all permutations in lexicographic order, which defeats the purpose. Sorting is O(n log n) and trivial. |
| *What if `k` is not prime?* | It doesn’t matter – the modular arithmetic works for any integer `k`. |
| *Why not use DP over the number of digits instead of numbers?* | Because the state space would blow up – the number of possible digit lengths is far larger than the number of numbers (`n`). |
| *What if duplicates were allowed?* | You would need to store counts of each number in the mask; still doable but not in this problem. |
| *Is there a greedy solution?* | No – the problem is NP‑hard in general; DP is the standard approach. |

---

## 9.  How This Solves the Problem Statement

> *You are given an array of distinct integers `nums` and an integer `k`. Find the smallest (in lexicographic order) array that can be obtained by permuting `nums` such that the concatenated integer is divisible by `k`.*  

The provided DP finds the **lexicographically smallest** permutation that satisfies the divisibility constraint, or returns an empty array if impossible – exactly what the problem demands.

---

## 9.  Why This Matters for You

*You’ve seen a problem that at first glance seems like a brute‑force search over all `n!` permutations. The key insight is to work with **remainders** and use **bitmask DP** to prune the search space dramatically.*  

Mastering this pattern will let you tackle a whole class of “concatenation‑and‑divisibility” problems, and the code above is a ready‑to‑use template you can adapt for interview contests.

Good luck – now go nail that interview! 🚀

--- 

*Prepared by: [Your Name]*  
*Date: 2023‑08‑08*