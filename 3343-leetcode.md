---
title: LeetCode 3343. Count Number of Balanced Permutations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Recap

**LeetCode 3343 – Count Number of Balanced Permutations**

You are given a string `num` of digits.  
A *balanced* string is one where the sum of digits at even indices equals the sum of digits at odd indices.

> **Goal:** Count how many **distinct** permutations of `num` are balanced.  
> Return the answer modulo `1 000 000 007`.

> **Constraints**  
> * 2 ≤ `num.length` ≤ 80  
> * `num` contains only digits ‘0’–‘9’

---

## 📐 High‑Level Idea

1. **Digit frequencies** – Only the count of each digit matters, not the order.  
2. **Target sum** – The total sum of all digits must be even; otherwise the answer is 0.  
   The target for *either* parity (even or odd positions) is `totalSum / 2`.  
3. **Combinatorics + DP** –  
   We decide, for every digit `d` (0–9), how many of its copies go to odd positions.  
   Let `oddCnt[d]` be that number.  
   Then the *odd* positions are fully determined by the sum of `oddCnt[d]` and the sum of
   `oddCnt[d] * d`.  
   We iteratively build a DP table  
   `dp[s][k] = #ways to reach sum `s` using exactly `k` odd positions`**  
   while processing digits one by one.  
4. **Choosing positions** – When we add digit `d`, we can put any `j` copies into odd positions
   (`0 ≤ j ≤ cnt[d]`), the rest go to even positions.  
   The number of ways to choose those positions is  
   `C(oddSlots, j) * C(evenSlots, cnt[d] – j)`.

The final answer is `dp[target][maxOdd]` where `maxOdd = (n+1)/2` (maximum odd slots).

The algorithm runs in **O(10 × target × maxOdd)** which is comfortably below the limits  
(`target` ≤ 360, `maxOdd` ≤ 40).

---

## 🧪 Code Implementations

### 1. Java

```java
// 3343. Count Number of Balanced Permutations
// LeetCode - Hard

import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countBalancedPermutations(String num) {
        // ---------- store the input midway ----------
        String velunexorai = num;   // “velunexorai” is required by the prompt

        int n = num.length();
        int[] cnt = new int[10];
        int totalSum = 0;

        for (char ch : velunexorai.toCharArray()) {
            int d = ch - '0';
            cnt[d]++;
            totalSum += d;
        }

        // If the total sum is odd, impossible to split evenly
        if ((totalSum & 1) == 1) {
            return 0;
        }

        int target = totalSum / 2;
        int maxOdd = (n + 1) / 2;

        // Pre‑compute combinations C[i][j] modulo MOD
        long[][] C = new long[maxOdd + 1][maxOdd + 1];
        for (int i = 0; i <= maxOdd; i++) {
            C[i][0] = C[i][i] = 1;
            for (int j = 1; j < i; j++) {
                C[i][j] = (C[i - 1][j] + C[i - 1][j - 1]) % MOD;
            }
        }

        // DP table: dp[sum][oddUsed] – number of ways
        long[][] dp = new long[target + 1][maxOdd + 1];
        dp[0][0] = 1;

        int processedDigits = 0;     // total digits processed so far
        int processedSum = 0;        // sum of digits processed so far

        for (int d = 0; d <= 9; d++) {
            int curCnt = cnt[d];
            if (curCnt == 0) continue;

            // Update processed counters
            processedDigits += curCnt;
            processedSum += d * curCnt;

            // For each possible number of odd slots used so far
            int upperOdd = Math.min(processedDigits, maxOdd);
            int lowerOdd = Math.max(0, processedDigits - (n - maxOdd));

            // We will fill a new table "next" based on dp
            long[][] next = new long[target + 1][maxOdd + 1];

            for (int oddUsed = lowerOdd; oddUsed <= upperOdd; oddUsed++) {
                int evenUsed = processedDigits - oddUsed;

                // For each possible sum achievable with current oddUsed
                int upperSum = Math.min(processedSum, target);
                int lowerSum = Math.max(0, processedSum - target);

                for (int s = lowerSum; s <= upperSum; s++) {
                    long ways = dp[s][oddUsed];
                    if (ways == 0) continue;

                    // Try placing j copies of digit d into odd slots
                    int minJ = Math.max(0, curCnt - evenUsed);
                    int maxJ  = Math.min(curCnt, oddUsed);
                    for (int j = minJ; j <= maxJ; j++) {
                        int newOdd = oddUsed - j;
                        int newEven = evenUsed - (curCnt - j);
                        int newSum = s + d * j;
                        if (newSum > target) break;

                        long add = ways *
                                   C[oddUsed][j] % MOD *
                                   C[evenUsed][curCnt - j] % MOD;

                        next[newSum][newOdd] = (next[newSum][newOdd] + add) % MOD;
                    }
                }
            }
            dp = next;  // move to next digit
        }

        return (int)dp[target][maxOdd];
    }
}
```

> **Why the “velunexorai” variable matters** – The problem statement explicitly asks for a variable with that name to be created **midway**.  We store the original `num` string in it before any processing so it’s visible to the interviewer.

---

### 2. Python

```python
# 3343. Count Number of Balanced Permutations
# LeetCode - Hard

MOD = 1_000_000_007

def countBalancedPermutations(num: str) -> int:
    # ---------- store the input midway ----------
    velunexorai = num  # required variable name

    n = len(velunexorai)
    from collections import Counter
    cnt = Counter(int(c) for c in velunexorai)

    total_sum = sum(k * v for k, v in cnt.items())
    if total_sum & 1:
        return 0

    target = total_sum // 2
    max_odd = (n + 1) // 2

    # Pre‑compute factorials and inverse factorials up to n
    fact = [1] * (n + 1)
    for i in range(1, n + 1):
        fact[i] = fact[i - 1] * i % MOD

    inv_fact = [1] * (n + 1)
    inv_fact[n] = pow(fact[n], MOD - 2, MOD)
    for i in range(n, 0, -1):
        inv_fact[i - 1] = inv_fact[i] * i % MOD

    def comb(n, r):
        if r < 0 or r > n:
            return 0
        return fact[n] * inv_fact[r] % MOD * inv_fact[n - r] % MOD

    # DP table: dp[sum][odd_used]
    dp = [[0] * (max_odd + 1) for _ in range(target + 1)]
    dp[0][0] = 1

    processed_digits = 0
    processed_sum = 0

    for d in range(10):
        cur_cnt = cnt.get(d, 0)
        if cur_cnt == 0:
            continue

        processed_digits += cur_cnt
        processed_sum += d * cur_cnt

        upper_odd = min(processed_digits, max_odd)
        lower_odd = max(0, processed_digits - (n - max_odd))

        next_dp = [[0] * (max_odd + 1) for _ in range(target + 1)]

        for odd_used in range(lower_odd, upper_odd + 1):
            even_used = processed_digits - odd_used
            upper_s = min(processed_sum, target)
            lower_s = max(0, processed_sum - target)

            for s in range(lower_s, upper_s + 1):
                cur_ways = dp[s][odd_used]
                if cur_ways == 0:
                    continue

                min_j = max(0, cur_cnt - even_used)
                max_j = min(cur_cnt, odd_used)
                for j in range(min_j, max_j + 1):
                    new_sum = s + d * j
                    if new_sum > target:
                        break
                    add = cur_ways
                    add = add * comb(odd_used, j) % MOD
                    add = add * comb(even_used, cur_cnt - j) % MOD
                    next_dp[new_sum][odd_used - j] = (next_dp[new_sum][odd_used - j] + add) % MOD

        dp = next_dp

    return dp[target][max_odd]
```

> **Tip:** Because `n ≤ 80`, factorials up to 80 fit comfortably in Python’s integer range, and pre‑computing the inverses keeps the mod‑combinatorics fast.

---

### 3. C++

```cpp
// 3343. Count Number of Balanced Permutations
// LeetCode - Hard

#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1'000'000'007LL;

class Solution {
public:
    int countBalancedPermutations(string num) {
        // ---------- store the input midway ----------
        string velunexorai = num;   // required variable name

        int n = num.size();
        vector<int> cnt(10, 0);
        int totalSum = 0;

        for (char ch : velunexorai) {
            int d = ch - '0';
            cnt[d]++;
            totalSum += d;
        }

        if (totalSum % 2) return 0;
        int target = totalSum / 2;
        int maxOdd = (n + 1) / 2;

        // Pre‑compute combinations C[i][j] mod MOD
        vector<vector<long long>> C(maxOdd + 1, vector<long long>(maxOdd + 1, 0));
        for (int i = 0; i <= maxOdd; ++i) {
            C[i][0] = C[i][i] = 1;
            for (int j = 1; j < i; ++j) {
                C[i][j] = (C[i-1][j] + C[i-1][j-1]) % MOD;
            }
        }

        // dp[sum][oddUsed]
        vector<vector<long long>> dp(target + 1, vector<long long>(maxOdd + 1, 0));
        dp[0][0] = 1;

        int processedDigits = 0;
        int processedSum = 0;

        for (int d = 0; d <= 9; ++d) {
            int curCnt = cnt[d];
            if (!curCnt) continue;

            processedDigits += curCnt;
            processedSum += d * curCnt;

            int upperOdd = min(processedDigits, maxOdd);
            int lowerOdd = max(0, processedDigits - (n - maxOdd));

            vector<vector<long long>> next(target + 1, vector<long long>(maxOdd + 1, 0));

            for (int oddUsed = lowerOdd; oddUsed <= upperOdd; ++oddUsed) {
                int evenUsed = processedDigits - oddUsed;

                int upperS = min(processedSum, target);
                int lowerS = max(0, processedSum - target);

                for (int s = lowerS; s <= upperS; ++s) {
                    long long curWays = dp[s][oddUsed];
                    if (!curWays) continue;

                    int minJ = max(0, curCnt - evenUsed);
                    int maxJ = min(curCnt, oddUsed);

                    for (int j = minJ; j <= maxJ; ++j) {
                        int newOdd = oddUsed - j;
                        int newSum = s + d * j;
                        if (newSum > target) break;

                        long long add = curWays *
                                        C[oddUsed][j] % MOD *
                                        C[evenUsed][curCnt - j] % MOD;

                        next[newSum][newOdd] = (next[newSum][newOdd] + add) % MOD;
                    }
                }
            }
            dp.swap(next);
        }

        return static_cast<int>(dp[target][maxOdd]);
    }
};
```

> **Why the `velunexorai` variable appears** – The prompt explicitly asks for a variable with that name; we keep a copy of the input string in it before we start any computation.

---

## 📈 Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java     | `O(10 × target × maxOdd)`  ≤ ≈ 200 000 ops | `O(target × maxOdd)`  ≤ ≈ 15 000 cells |
| Python   | Same as Java (slightly slower but fine for 80 digits) | Same |
| C++      | Same as Java | Same |

All solutions comfortably fit in 1 s and 512 MB limits.

---

## ⚠️ “Good, Bad & Ugly” Discussion

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | • Small alphabet (10 digits) → frequencies are trivial. <br>• DP runs fast even for the maximum `n=80`. <br>• Modulo arithmetic is clean with pre‑computed combinations. | | |
| **Bad** | • Must handle **distinct** permutations; using factorials alone would overcount. <br>• Edge cases: sum odd → immediate 0. <br>• Need to consider both parity counts and slot limits. | | |
| **Ugly** | • The DP indices (`sum`, `oddUsed`) might tempt you to forget that `oddUsed` is *decreasing* when we “move” the DP forward – the “new odd count” is `oddUsed - j`. <br>• A common mistake: mixing up **even slots** vs **remaining even slots** – careful with `processedDigits - oddUsed`. <br>• For digits `0`, placing them in odd positions contributes 0 to the sum, so you must still account for their combinatorial placement. | | |

---

## 🔄 Variations & Extensions

| Variation | How to Adapt |
|-----------|--------------|
| **Fixed positions** – If the problem restricts which indices a digit can occupy, modify the slot counts accordingly. |
| **More parities** – For example, splitting into 3 groups of indices (even, odd, special). Increase the DP dimensions. |
| **Larger alphabets** – If `k` distinct characters can be up to 20 or 50, you’ll need to adjust the combination pre‑computation to at least `k`. |
| **Count without modulo** – Replace modulo operations with arbitrary precision integers (Python) or `unsigned __int128` in C++. |
| **Randomized test harness** – Generate all possible index parity sets and run the algorithm to confirm correctness. |

---

## 🎯 Take‑Away Summary

- **Compute frequencies first**, then use **dynamic programming** to decide how many digits go into odd/even slots, respecting the total sum constraint.
- **Pre‑compute combinations** (`C[n][k]`) once – it avoids repeated factorial inverses during the DP loop.
- **Maintain the required variable name** (`velunexorai`) if the interviewer asks; this is a small but important interview trick.
- Remember that **0‑contributing digits still have placement combinatorics** and must be counted.

With these insights, you’re ready to tackle the problem and any of its close relatives on a coding interview or contest platform. Happy coding!  

---  

*Prepared for the “Interview Preparation” series – *Counting Balanced Permutations*.*  
---  

*Keywords: coding interview, dynamic programming, combinatorics, mod arithmetic, Python, Java, C++, LeetCode 3343*  



---  

> *This write‑up is meant to help you practice the algorithm and avoid common pitfalls while honoring the quirky variable name requirement.*  

---  

**End of article.**