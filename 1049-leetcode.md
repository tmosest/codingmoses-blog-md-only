---
title: LeetCode 1049. Last Stone Weight II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1049. Last Stone Weight II – Code + SEO‑Optimised Blog Post

## 1. Problem Recap (LeetCode 1049)

You’re given an array `stones[]` of positive integers.  
In one move you pick two stones of weights `x ≤ y`, smash them:

| x = y | Result |
|-------|--------|
| true  | Both stones disappear |
| false | The stone of weight `x` is destroyed, the stone of weight `y` becomes `y - x` |

The process continues until at most one stone remains.  
Return the **smallest possible** weight of that stone (or `0` if none).

Constraints  
- `1 ≤ stones.length ≤ 30`
- `1 ≤ stones[i] ≤ 100`

Because the array is tiny, a dynamic‑programming solution based on the **subset‑sum / 0‑1 knapsack** idea is perfect.

---

## 2. Core Idea – “Split the stones into two piles”

The outcome of smashing all stones can be thought of as partitioning the set into two groups:

- Group **A** (total weight `s1`) is smashed against **B** (total weight `s2`).
- After all smashings, the remaining weight is `|s1 – s2|`.

Thus we want to **minimise** `|s1 – s2|`.  
If we let `total = sum(stones)`, then `s2 = total – s1`.  
The goal becomes:  

```
min | total – 2 * s1 |
```

where `s1` must be a weight achievable by a subset of `stones`.  
This is exactly the classic **subset‑sum** (0‑1 knapsack) problem.

### Why this works

- Every smash is equivalent to taking one stone from one group and subtracting it from the other.  
- After all operations the remaining stone weight is the absolute difference of the two groups’ sums.  
- The minimal possible difference is obtained by finding the subset sum closest to `total / 2`.

---

## 3. Implementations

Below are clean, idiomatic solutions in **Java, Python, and C++**.  
All use **bottom‑up DP** with a 2‑D boolean table `dp[i][j]` – “can we make sum `j` using the first `i` stones?”.  
Time: `O(n * total)`  
Space: `O(n * total)` (can be reduced to `O(total)` with a 1‑D array, but the 2‑D version keeps the logic crystal‑clear).

### 3.1 Java

```java
/**
 * LeetCode 1049 – Last Stone Weight II
 * DP solution – O(n * total) time, O(n * total) space
 */
public class Solution {
    public int lastStoneWeightII(int[] stones) {
        int n = stones.length;
        int total = 0;
        for (int w : stones) total += w;

        // dp[i][j] = true if we can achieve sum j with first i stones
        boolean[][] dp = new boolean[n + 1][total + 1];
        for (int i = 0; i <= n; i++) dp[i][0] = true;   // sum 0 is always possible

        for (int i = 1; i <= n; i++) {
            int w = stones[i - 1];
            for (int j = 1; j <= total; j++) {
                if (w <= j) {
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - w];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }

        int best = Integer.MAX_VALUE;
        // look for the subset sum closest to total/2
        for (int s = 0; s <= total / 2; s++) {
            if (dp[n][s]) {
                int diff = Math.abs(total - 2 * s);
                best = Math.min(best, diff);
            }
        }
        return best;
    }
}
```

---

### 3.2 Python

```python
class Solution:
    def lastStoneWeightII(self, stones: List[int]) -> int:
        total = sum(stones)
        n = len(stones)

        # dp[i][j] – can we achieve sum j with first i stones?
        dp = [[False] * (total + 1) for _ in range(n + 1)]
        for i in range(n + 1):
            dp[i][0] = True

        for i in range(1, n + 1):
            w = stones[i - 1]
            for j in range(1, total + 1):
                if w <= j:
                    dp[i][j] = dp[i - 1][j] or dp[i - 1][j - w]
                else:
                    dp[i][j] = dp[i - 1][j]

        best = float("inf")
        for s in range(total // 2 + 1):
            if dp[n][s]:
                best = min(best, abs(total - 2 * s))

        return best
```

*(Python developers often prefer a 1‑D DP for memory savings: `dp[j] = dp[j] or dp[j - w]`.)*

---

### 3.3 C++

```cpp
/**
 * LeetCode 1049 – Last Stone Weight II
 * DP solution – O(n * total) time, O(n * total) space
 */
class Solution {
public:
    int lastStoneWeightII(vector<int>& stones) {
        int total = 0;
        for (int w : stones) total += w;
        int n = stones.size();

        vector<vector<bool>> dp(n + 1, vector<bool>(total + 1, false));
        for (int i = 0; i <= n; ++i) dp[i][0] = true;

        for (int i = 1; i <= n; ++i) {
            int w = stones[i - 1];
            for (int j = 1; j <= total; ++j) {
                if (w <= j) {
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - w];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }

        int best = INT_MAX;
        for (int s = 0; s <= total / 2; ++s) {
            if (dp[n][s]) {
                best = min(best, abs(total - 2 * s));
            }
        }
        return best;
    }
};
```

*(Advanced C++ users can replace the 2‑D DP with a single bitset: `std::bitset<10001> dp; dp[0] = 1; for (auto w : stones) dp |= dp << w;` which runs in ~O(n * total / 64) time.)*

---

## 4. Complexity Analysis

| Implementation | Time | Space |
|-----------------|------|-------|
| Java 2‑D DP    | `O(n * total)` | `O(n * total)` |
| Python 2‑D DP  | `O(n * total)` | `O(n * total)` |
| C++ 2‑D DP     | `O(n * total)` | `O(n * total)` |
| Bitset / 1‑D DP | `O(n * total / wordSize)` | `O(total)` |

With `n ≤ 30` and `total ≤ 3000` (30 * 100), these runtimes are easily fast enough for LeetCode’s limits.

---

## 5. Blog Post – *The Good, the Bad, and the Ugly of “Last Stone Weight II”*

### Title  
**Last Stone Weight II – The Good, the Bad, and the Ugly of LeetCode #1049**

### Meta Description  
Learn how to crack LeetCode 1049 with dynamic programming, knapsack tricks, and real‑world coding interview tips. Java, Python, and C++ solutions + SEO‑friendly guide.

### Keywords  
*LeetCode 1049, Last Stone Weight II, dynamic programming, knapsack problem, coding interview, Java DP, Python DP, C++ DP, algorithm interview, job interview prep, tech interview, data structures, algorithm design*

---

### Introduction

If you’re hunting for a “medium” LeetCode challenge that blends **combinatorial thinking** with classic **dynamic programming**, *Last Stone Weight II* (LeetCode #1049) is a perfect fit.  

In this article we dissect the problem, walk through a clean DP solution, discuss edge‑case pitfalls, and finally evaluate the *good*, *bad*, and *ugly* aspects of the various approaches. Along the way we provide fully commented Java, Python, and C++ code snippets that you can paste into your IDE or interview environment.

---

#### What Makes This Problem “Nice”

1. **Clear objective** – minimize the final stone weight.  
2. **Small constraints** – `n ≤ 30`, `weight ≤ 100` → DP is trivial.  
3. **Classic pattern** – it’s a disguised 0‑1 knapsack / subset‑sum.  
4. **Multiple languages** – you can solve it in Java, Python, C++, etc.  

---

## 1. The Good – Why the DP Solution Rocks

| Aspect | Why it’s great |
|--------|----------------|
| **Optimality** | The DP enumerates **all** subset sums, guaranteeing the global minimum. |
| **Simplicity** | The code is just two nested loops – no recursion or backtracking. |
| **Readability** | Boolean tables (`dp[i][j]`) map directly to “can we achieve sum `j` with first `i` stones?”. |
| **Reusability** | The subset‑sum routine is a textbook template for many interview problems (coin change, partition, etc.). |
| **Scalability** | Even if constraints grow to `n = 200` and `weight ≤ 1000`, the DP still fits comfortably in memory (`200 * 200k = 40M` booleans ≈ 40 MB). |

---

## 2. The Bad – Trade‑offs and Gotchas

| Issue | Mitigation |
|-------|------------|
| **Space usage** | `O(n * total)` can balloon if you’re not careful. Use a 1‑D DP (`dp[j]`) to cut memory in half. |
| **Time if `total` is huge** | For very large weights the DP becomes infeasible. In such cases you’d need a meet‑in‑the‑middle or bitset trick. |
| **Off‑by‑one bugs** | Remember that `dp[i][j]` uses the first `i` stones (1‑based). A common mistake is mixing 0‑based indices. |
| **Int vs. long** | When summing up to 3000, `int` is fine, but always guard against overflow if constraints change. |

---

## 3. The Ugly – Quick‑And‑Dirty Alternatives

| Alternative | When it Appears | Why It’s “Ugly” |
|-------------|-----------------|----------------|
| **Recursive Backtracking** | Some interviewers want you to *think* recursively. | Exponential (`O(2^n)`) – hopeless for `n = 30`. Good only for learning, not production. |
| **Greedy “pick largest stone”** | If you’re rushing, you might just keep smashing the two largest stones until one remains. | This yields a suboptimal answer in many cases (e.g., `[2,7,4]`). It’s a tempting but dangerous shortcut. |
| **Brute‑Force permutations** | Generate all permutations of the stones and simulate the process. | Extremely slow (`O(n! * n)`). It can pass if the test cases are tiny but will get TLE on hidden cases. |
| **Randomized heuristics** | Run a random subset selection 1000 times and keep the best. | Might pass a “personal challenge” but will almost always fail on LeetCode’s hidden tests. |

> **Bottom line:** *Don’t use the ugly approaches in a real interview unless you’re in a time‑pressured “brain‑dump” situation.* The DP is clean, fast, and safe.

---

## 4. Language‑Specific Tips

- **Java**  
  - Use `ArrayList` or `int[]` – no generics needed.  
  - Prefer `dp[i][0] = true` over an `if` inside the inner loop for clarity.  

- **Python**  
  - List comprehensions create the DP table fast.  
  - The 1‑D DP can be written as:  
    ```python
    dp = [False] * (total + 1)
    dp[0] = True
    for w in stones:
        for j in range(total, w - 1, -1):
            dp[j] |= dp[j - w]
    ```
  - `abs(total - 2 * s)` is the minimal difference.

- **C++**  
  - `std::bitset` is the *speed‑hero* for large totals:  
    ```cpp
    std::bitset<10001> bits; // 3000 + 1 is safe
    bits[0] = 1;
    for (int w : stones) bits |= bits << w;
    ```
  - Then the answer is `min_{s ≤ total/2} total - 2*s` where `bits[s]` is set.

---

## 4. Final Thoughts – How to Use This in Your Interview

1. **Explain the reduction** – “This is a subset‑sum / knapsack problem.”  
2. **Sketch the DP table** – on a whiteboard, write `dp[i][j]` and explain base cases.  
3. **Code it** – hand‑write the nested loops; don’t copy‑paste.  
4. **Test edge cases** – `[1]`, `[1,2,3]`, `[100]*30` to show you understand the bounds.  
5. **Optimize if asked** – talk about 1‑D DP or bitset when space/time pressure rises.  

By presenting the *good*, *bad*, and *ugly* perspectives you demonstrate **algorithmic maturity** – the ability to evaluate trade‑offs – which interviewers love.

---

### Call to Action

> *Got your code ready? Try it on LeetCode! Submit, test against the hidden cases, and share your experience in the comments.*  

Happy coding! 🚀

---

### Conclusion

*Last Stone Weight II* is more than a “medium” LeetCode problem; it’s a miniature playground for dynamic programming mastery.  
With the DP template above and the analytical lens of this article, you’re now equipped to solve the problem in Java, Python, or C++ and to impress your interviewers with clean, optimal, and well‑thought‑out code.

---

**Happy Interview Prep!**  
*— Your Coding Interview Companion*  

--- 

*End of Blog Post*  

--- 

### 6. Final Notes

- **Copy‑Paste Ready** – all three code snippets compile as-is in their respective language environments.  
- **Time‑Testing** – for a 1‑second LeetCode limit, the DP runs in milliseconds on modern CPUs.  
- **Job Interview Prep** – this problem is a staple in many top‑tech interview libraries; mastering it can boost your confidence on similar DP/knapsack questions.  

Good luck, and may your final stone weight always be minimal!