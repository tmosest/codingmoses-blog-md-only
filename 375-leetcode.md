---
title: LeetCode 375. Guess Number Higher or Lower II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap  
**LeetCode 375 – Guess Number Higher or Lower II**  
*Medium | DP*  

> You are playing a guessing game.  
> The opponent picks a secret integer between **1** and **n** (inclusive).  
> Each time you guess a number `x` that is wrong, you pay **$x**.  
> After every wrong guess the opponent tells you whether the secret number is higher or lower than `x`.  
> Your goal: *guarantee a win no matter what number the opponent picked* while spending the *minimum possible* amount of money in the worst case.  

Return that minimum worst‑case cost for a given `n` (1 ≤ n ≤ 200).

---

## 2. High‑Level Idea  

The problem is a classic *dynamic‑programming* variant of the “egg‑dropping” puzzle.  
For any interval `[l, r]` we decide which number `k` to guess first.  
If we guess `k`:

* **Wrong and lower** → the secret lies in `[l, k‑1]`.  
* **Wrong and higher** → the secret lies in `[k+1, r]`.  

The cost incurred in this round is `k`.  
The **worst‑case** cost for the interval is

```
k + max(cost(l, k‑1), cost(k+1, r))
```

We want the minimal such worst‑case over all possible `k`.  
This leads to a simple DP recurrence.

---

## 3. DP Formulation  

Let `dp[l][r]` be the minimal worst‑case cost needed to guarantee a win in the interval `[l, r]`.  
Base cases:

* `l >= r` → only one number → `dp[l][r] = 0`.

Transition (for `l < r`):

```
dp[l][r] = min over k in [l, r]  {  k + max(dp[l][k-1], dp[k+1][r])  }
```

Because the sub‑intervals are always smaller, we can fill the DP table bottom‑up by increasing interval length.

Complexity:  
*Time* – O(n³) in the naïve implementation.  
*Space* – O(n²).

With `n ≤ 200` this is perfectly fine (≈ 8 million operations).  
A small optimization: try only `k` around the middle (the greedy choice), but it is not necessary for this input size.

---

## 4. Reference Implementations  

Below are clean, self‑contained solutions in **Java**, **Python**, and **C++**.  
All three share the same bottom‑up DP approach described above.

---

### 4.1 Java

```java
/**
 *  LeetCode 375 - Guess Number Higher or Lower II
 *
 *  DP solution, O(n^3) time, O(n^2) space.
 */
public class Solution {
    public int getMoneyAmount(int n) {
        if (n <= 1) return 0;

        int[][] dp = new int[n + 2][n + 2];   // 1‑based, dp[l][r] = 0 when l >= r

        // length of interval (len = r - l + 1)
        for (int len = 2; len <= n; len++) {
            for (int l = 1; l + len - 1 <= n; l++) {
                int r = l + len - 1;
                int best = Integer.MAX_VALUE;

                // Try every possible first guess k in [l, r]
                for (int k = l; k <= r; k++) {
                    int cost = k + Math.max(dp[l][k - 1], dp[k + 1][r]);
                    if (cost < best) best = cost;
                }
                dp[l][r] = best;
            }
        }
        return dp[1][n];
    }
}
```

---

### 4.2 Python

```python
# LeetCode 375 - Guess Number Higher or Lower II
# DP solution, O(n^3) time, O(n^2) space

class Solution:
    def getMoneyAmount(self, n: int) -> int:
        if n <= 1:
            return 0

        dp = [[0] * (n + 2) for _ in range(n + 2)]

        # length of interval (len = r - l + 1)
        for length in range(2, n + 1):
            for l in range(1, n - length + 2):
                r = l + length - 1
                best = float('inf')

                for k in range(l, r + 1):
                    cost = k + max(dp[l][k - 1], dp[k + 1][r])
                    if cost < best:
                        best = cost
                dp[l][r] = best

        return dp[1][n]
```

---

### 4.3 C++

```cpp
/**
 * LeetCode 375 - Guess Number Higher or Lower II
 *
 * DP solution, O(n^3) time, O(n^2) space.
 */
class Solution {
public:
    int getMoneyAmount(int n) {
        if (n <= 1) return 0;

        vector<vector<int>> dp(n + 2, vector<int>(n + 2, 0));

        for (int len = 2; len <= n; ++len) {          // interval length
            for (int l = 1; l + len - 1 <= n; ++l) {
                int r = l + len - 1;
                int best = INT_MAX;

                for (int k = l; k <= r; ++k) {       // first guess
                    int cost = k + max(dp[l][k - 1], dp[k + 1][r]);
                    best = min(best, cost);
                }
                dp[l][r] = best;
            }
        }
        return dp[1][n];
    }
};
```

---

## 5. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 375”

### Title  
**“LeetCode 375 – Guess Number Higher or Lower II: The Good, the Bad, and the Ugly of DP”**  

### Meta Description  
Master LeetCode 375 with a clear dynamic‑programming walkthrough. Discover the best strategies, pitfalls, and optimized code in Java, Python, and C++. Perfect for interview prep and boosting your job search.

### Keywords  
LeetCode 375, Guess Number Higher or Lower II, dynamic programming, egg dropping DP, interview algorithms, coding interview, algorithmic strategy, job interview coding, worst‑case cost DP.

---

#### 1. The Good – Why It’s a Gold‑Mine for Interviews

- **Classic DP pattern** – This problem is essentially an “egg‑dropping” variant. Mastering it gives you a ready‑made solution to a whole class of interval DP problems (e.g., “Paint House”, “Burst Balloons”).
- **Clear state definition** – `dp[l][r]` is self‑explanatory: the minimal cost to guarantee a win between two bounds.
- **Deterministic answer** – No randomness; you can prove optimality by the minimax principle, making your solution rock‑solid for a hiring manager.
- **Language‑agnostic** – The same recurrence works in any language; you can swap code snippets as a demonstration of cross‑platform thinking.

#### 2. The Bad – Common Pitfalls

| Pitfall | Symptom | Fix |
|---------|---------|-----|
| Off‑by‑one in indices | Wrong answer for n = 2 | Use 1‑based arrays or carefully offset ranges. |
| Forgetting base cases | Runtime error on n = 1 | Ensure `dp[l][r] = 0` when `l >= r`. |
| Inefficient loop ranges | TLE on n = 200 | Avoid nested loops over `k` beyond `[l, r]`; keep them tight. |
| Integer overflow in other languages | Wrong large n | Use 64‑bit integers (`long long` in C++, `long` in Java). |

#### 3. The Ugly – When the Problem Gets Tricky

- **Choosing the “best” first guess** – Some solutions try all `k` in `[l, r]`, leading to O(n³). A common optimization is to only try `k` around the middle, but it sacrifices correctness for speed if you’re not careful.
- **Recursion depth** – A naïve recursive memoization can hit Java’s recursion limit or Python’s recursion depth. Bottom‑up DP is the safest route.
- **Space overkill** – A full `dp[n+2][n+2]` table consumes ~ 160 kB for `n = 200`. Acceptable, but if you push `n` to 10⁵ (hypothetical) you need a smarter approach (e.g., only store two rows).

#### 4. The Code – Clean, Cross‑Language

(Insert the three code snippets from above, each with a short comment.)

#### 5. How to Use This in Your Job Hunt

1. **Portfolio snippet** – Put the solution into a GitHub repo, tag it `leetcode-375`, and link it in your LinkedIn headline.
2. **Explain the trade‑offs** – During an interview, say: *“I started with a recursive memoization solution but hit recursion depth limits, so I switched to a bottom‑up DP. I also noted the O(n³) time; for larger n I'd use a divide‑and‑conquer optimization.”*
3. **Demo the ‘good, bad, ugly’** – Show you know common pitfalls; you can say *“I avoided the off‑by‑one bug by…”.*
4. **Highlight cross‑language skills** – Present the Java, Python, and C++ versions; it demonstrates language fluency and algorithmic adaptability.

#### 6. Takeaway

LeetCode 375 is a compact, yet powerful, exercise that blends greedy intuition, DP, and worst‑case analysis. Master it, showcase the code in multiple languages, and you’ll have a conversation starter that impresses both hiring managers and interviewers.

---

### 7. Closing Thoughts

- **Practice** – Write the DP from scratch in each language; tweak the loop bounds to see how the runtime changes.
- **Explain it** – Try teaching the problem to a friend or writing a blog post (like this one). Teaching cements knowledge.
- **Link it** – Attach the solutions to your coding portfolio and include the explanation in your resume under “Algorithmic Skills”.

Happy coding, and may your job search be as cost‑optimal as your DP solution!