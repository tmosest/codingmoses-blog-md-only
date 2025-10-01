---
title: LeetCode 2518. Number of Great Partitions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2518 – Number of Great Partitions  
**LeetCode** | **Hard** | `O(nk)` time, `O(k)` space  

> Given an array `nums` of positive integers and an integer `k`, count the number of ways to split the array into two *ordered* groups such that the sum of each group is at least `k`.  
> Return the answer modulo `10⁹ + 7`.

The solution below is written in **Java**, **Python**, and **C++**.  
After the code you’ll find a blog‑style article that explains the algorithm, highlights the “good, the bad, and the ugly”, and is fully **SEO‑optimised** for job‑searchers wanting to land a data‑structures & algorithms role.

---

## 1. 3‑Language Code

### 1.1 Java

```java
import java.util.*;

class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countPartitions(int[] nums, int k) {
        long total = 0;
        for (int v : nums) total += v;
        if (total < 2L * k) return 0;          // impossible to satisfy both sides

        // dp[s] = # subsets of processed elements that sum to s  (s < k)
        long[] dp = new long[k];
        dp[0] = 1;

        for (int v : nums) {
            // update dp in reverse order to avoid double counting
            for (int s = k - 1 - v; s >= 0; --s) {
                dp[s + v] = (dp[s + v] + dp[s]) % MOD;
            }
        }

        long lessThanK = 0;
        for (int s = 0; s < k; ++s) lessThanK = (lessThanK + dp[s]) % MOD;

        // 2^n partitions
        long totalPartitions = powMod(2L, nums.length, MOD);

        // invalid partitions: group1 < k  OR  group2 < k
        long ans = (totalPartitions - 2L * lessThanK) % MOD;
        if (ans < 0) ans += MOD;
        return (int) ans;
    }

    // fast exponentiation: (base^exp) % mod
    private long powMod(long base, int exp, long mod) {
        long res = 1;
        long cur = base % mod;
        int e = exp;
        while (e > 0) {
            if ((e & 1) == 1) res = (res * cur) % mod;
            cur = (cur * cur) % mod;
            e >>= 1;
        }
        return res;
    }
}
```

### 1.2 Python

```python
class Solution:
    MOD = 10 ** 9 + 7

    def countPartitions(self, nums: List[int], k: int) -> int:
        total = sum(nums)
        if total < 2 * k:          # cannot satisfy both groups
            return 0

        # dp[s] = number of subsets with sum s   (s < k)
        dp = [0] * k
        dp[0] = 1

        for v in nums:
            for s in range(k - 1 - v, -1, -1):
                dp[s + v] = (dp[s + v] + dp[s]) % self.MOD

        less = sum(dp) % self.MOD
        total_parts = pow(2, len(nums), self.MOD)
        ans = (total_parts - 2 * less) % self.MOD
        return ans
```

### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static const long long MOD = 1'000'000'007LL;

    int countPartitions(vector<int>& nums, int k) {
        long long total = 0;
        for (int v : nums) total += v;
        if (total < 2LL * k) return 0;        // impossible

        vector<long long> dp(k, 0);
        dp[0] = 1;

        for (int v : nums) {
            for (int s = k - 1 - v; s >= 0; --s) {
                dp[s + v] = (dp[s + v] + dp[s]) % MOD;
            }
        }

        long long less = 0;
        for (int s = 0; s < k; ++s) less = (less + dp[s]) % MOD;

        long long totalParts = modPow(2LL, (int)nums.size(), MOD);
        long long ans = (totalParts - 2LL * less) % MOD;
        if (ans < 0) ans += MOD;
        return static_cast<int>(ans);
    }

private:
    long long modPow(long long base, int exp, long long mod) {
        long long res = 1;
        long long cur = base % mod;
        int e = exp;
        while (e) {
            if (e & 1) res = (res * cur) % mod;
            cur = (cur * cur) % mod;
            e >>= 1;
        }
        return res;
    }
};
```

---

## 2. Blog‑Style Article

> **Title**: *LeetCode 2518 – Number of Great Partitions (Java / Python / C++) – A DP‑Knapsack Solution*

> **Meta‑Description**:  
> Learn how to solve LeetCode 2518 “Number of Great Partitions” using a dynamic‑programming knapsack approach.  
> Get the complete Java, Python, and C++ implementation, time & space complexity, and tips for acing the hard problem.

---

### 2.1 Introduction

If you’re a data‑science recruiter, a senior software engineer, or an interview‑preparation enthusiast, you’ll recognize the familiar “split the array into two groups with minimum sum” problem.  
It turns out to be an elegant application of **subset‑sum DP** (a classic knapsack problem) combined with **inclusion–exclusion**.

Below we walk through:

* **Good** – Why the DP‑based solution is optimal for `O(nk)` time.
* **Bad** – Common pitfalls (overflow, handling of huge total sums).
* **Ugly** – The “hacky” parts that can trip up even seasoned coders.

We’ll also discuss practical coding tips (mod handling, negative values, fast exponentiation) that will make you a standout candidate on LinkedIn or GitHub.

---

### 2.2 The Problem in a Nutshell

Given `nums` and `k`:
1. **All** partitions = `2^n` (each element chooses group 1 or group 2).
2. **Invalid** partitions are those where *at least one* group has sum `< k`.
3. We need **both** groups to be `≥ k`.

Hence, the answer is:

```
valid = 2^n
        - (# subsets with sum < k)           // group1 bad
        - (# subsets with sum < k)           // group2 bad
        + (# subsets where both sums < k)    // added back because subtracted twice
```

#### Key Observation

If the total sum `S` of all elements is **≥ 2k**, it is **impossible** for both groups to have sum `< k`.  
So the last “+” term is zero and the formula simplifies to:

```
valid = 2^n - 2 * (# subsets with sum < k)
```

If `S < 2k` we immediately return `0` because we cannot satisfy the requirement on both sides.

---

### 2.3 Dynamic‑Programming Knapsack

We only need to count subsets whose sum is **strictly less than `k`**.  
We never need to consider sums larger than `k-1`, so the DP array has size `k`.

```
dp[s] = number of ways to pick a subset from processed elements that sums to s
```

**Transition**  
When we see a new element `v`, any subset that previously summed to `s` can be extended to `s+v`.  
Because `s+v` must still be `< k`, we iterate `s` backwards to avoid using the same element twice.

```
for s = k-1-v down to 0:
    dp[s+v] += dp[s]
```

All operations are performed modulo `M = 1e9+7`.  
After processing all elements we sum all `dp[s]` for `s < k` – this is `lessThanK`, the count of “bad” partitions for a single group.

---

### 2.4 Inclusion–Exclusion in One Pass

The final answer is:

```
answer = 2^n - 2 * lessThanK   (mod M)
```

* `2^n` : All possible partitions (each element independently chooses one of two groups).  
* `- 2 * lessThanK` : Remove partitions where *either* group has sum `< k`.  
  The factor of 2 accounts for the symmetry (group 1 bad or group 2 bad).  
* If `S < 2k` we would have to **add back** partitions where *both* groups are `< k`.  
  But when `S ≥ 2k` such partitions cannot exist – the condition `S - s < k` can never hold simultaneously.  
  Therefore the inclusion–exclusion term collapses to zero in the feasible case.

---

### 2.5 Edge‑Case & Mod Handling

* **Negative modulo** – After subtraction we may get a negative value; add `MOD` once to bring it back into `[0, MOD-1]`.  
* **Overflow** – Use `long long` (Java `long`, Python `int` is unbounded, C++ `long long`) for intermediate sums.  
* **Fast exponentiation** – `powMod(2, n, MOD)` runs in `O(log n)` and avoids repeated multiplications.

---

### 2.6 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Compute total sum | `O(n)` | `O(1)` |
| DP updates | `O(nk)` | `O(k)` |
| Sum of `dp` | `O(k)` | `O(1)` |
| Fast exponentiation | `O(log n)` | `O(1)` |

With `n ≤ 10⁵` and `k ≤ 1000`, the algorithm comfortably fits within typical LeetCode limits.

---

## 2. The Good, the Bad, and the Ugly

| Category | What’s good | What’s risky | Ugly twist |
|----------|-------------|--------------|------------|
| **Good** | *O(nk)* DP, linear in `k`, no need to store huge sums. | | |
| **Bad** | When `k` is large (up to 1000) the DP array is still small, but if `k` were `10⁶` it would explode. | We must early‑exit when `S < 2k`; otherwise we’d subtract subsets that never exist. | |
| **Ugly** | The inclusion–exclusion term looks complicated but simplifies to zero for feasible inputs. | Forgetting to check `S < 2k` leads to negative counts and wrong answers. | Some coders use `int` everywhere, causing overflow before modulo. |

**Take‑away:**  
*Always* guard against overflow and remember that the *double‑counting* term only matters when the total sum is *less* than `2k`.  

---

## 3. SEO‑Optimised Blog Article

> **Title**: *LeetCode 2518 – Number of Great Partitions (Java / Python / C++)*  
> **Keywords**: LeetCode 2518 solution, Number of Great Partitions, dynamic programming knapsack, hard LeetCode problems, Java Leetcode solutions, Python Leetcode solutions, C++ Leetcode solutions.

---

### LeetCode 2518 – Master the “Great Partition” Problem

If you’re eyeing a software‑engineering interview, **LeetCode 2518 “Number of Great Partitions”** is a great candidate for your portfolio.  
Below you’ll find:

1. **Full code in three languages** (Java, Python, C++).
2. A detailed walkthrough of the algorithm.
3. Practical hints that show you how to avoid common pitfalls.
4. A “good, the bad, and the ugly” analysis that will impress hiring managers.

---

#### Why This Problem Matters

* **Subset‑sum** is a classic DP problem, but the twist of splitting into *two ordered groups* tests your ability to reason about *complementary subsets* and *inclusion–exclusion*.
* The constraints (`n` up to 10⁵, `k` up to 1000) push you to design a solution that is **O(n · k)**, not **O(2ⁿ)**.
* Mastering this problem demonstrates fluency in:
  * Knapsack DP
  * Modular arithmetic
  * Fast exponentiation
  * Edge‑case handling

---

#### The Core Idea: Knapsack + Inclusion–Exclusion

1. **Total Sum Check**  
   If the array sum `S` is `< 2k`, you can never give both groups a sum ≥ `k`. Immediate `0`.

2. **Count “Bad” Subsets**  
   Run a 1‑dimensional DP that counts how many subsets have a sum **strictly less than `k`**.  
   We only care about sums `< k`, so the DP array is of length `k` → **O(k)** space.

3. **All Partitions**  
   Every element has two choices → `2ⁿ` partitions.

4. **Subtract Invalid Partitions**  
   A partition is invalid if *group 1* has sum `< k` **or** *group 2* has sum `< k`.  
   Using inclusion–exclusion, the final formula when `S ≥ 2k` simplifies to:

   ```
   answer = 2ⁿ  –  2 * (# subsets with sum < k)
   ```

   The double‑counted case (both groups < k) never exists because `S ≥ 2k`.

5. **Modular Arithmetic**  
   All computations are done modulo `M = 1e9+7`.  
   Pay special attention to negative results after subtraction; add `M` once.

---

#### Step‑by‑Step Pseudocode

```python
if total < 2 * k:
    return 0

dp = [0] * k
dp[0] = 1
for v in nums:
    for s in reversed(range(k - v)):
        dp[s + v] = (dp[s + v] + dp[s]) % M

bad = sum(dp) % M
all_partitions = pow(2, n, M)

answer = (all_partitions - 2 * bad) % M
```

---

#### Practical Coding Tips

* **Fast pow**: Use binary exponentiation (`pow(2, n, M)`) instead of a loop that multiplies `2` many times.
* **Avoid Int Overflow**: In Java and C++ use `long`/`long long` for intermediate sums; Python’s `int` is unlimited.
* **Negative Modulo**: After subtraction, if the result is negative, simply add `M`.
* **Backwards DP Loop**: Prevents double‑counting the same element.

---

#### “The Good, the Bad, and the Ugly”

| **Aspect** | **What You’ll Nail** | **Common Mistake** | **Why It Matters** |
|------------|----------------------|--------------------|--------------------|
| **Good** | Efficient DP that runs in *linear* time relative to `k`. | | Demonstrates algorithmic thinking. |
| **Bad** | Early exit when `S < 2k`. | Forgetting this leads to wrong modulo results. | Shows attention to detail. |
| **Ugly** | The inclusion–exclusion step looks complicated but is trivial after the sum check. | Using `int` everywhere → overflow. | Highlighting edge‑case awareness is prized. |

---

#### Final Verdict

*LeetCode 2518* is a sweet spot between a textbook DP and an interview challenge.  
Implement it in **Java**, **Python**, or **C++**, add the solution to your GitHub readme, and you’ll have a ready‑made talking point for your next interview.

---

### Take the Code Home

- **Java**: [Download](#)  
- **Python**: [Download](#)  
- **C++**: [Download](#)

Happy coding, and good luck on the next interview!

--- 

*End of Article*  

--- 

> **Note**: Replace the `Download` links with your own GitHub Gist or repository links.

---

### 4. Conclusion

With the code snippets and article above, you’re all set to:

* Submit a clean, verified solution to LeetCode 2518.  
* Showcase your understanding of knapsack DP and modular arithmetic on your résumé.  
* Impress interviewers with your ability to identify edge cases and optimize for large constraints.

Good luck – and enjoy conquering this hard LeetCode challenge!