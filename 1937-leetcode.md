---
title: LeetCode 1937. Maximum Number of Points with Cost - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Way Code – Java / Python / C++

Below are clean, production‑ready solutions for LeetCode 1937 “Maximum Number of Points with Cost”.  
All three codes run in **O(m × n)** time and **O(n)** extra memory, which is optimal for the given constraints.

---

### 1.1 Java (LeetCode compatible)

```java
// 1937. Maximum Number of Points with Cost
// Java 17 / LeetCode compatible

public class Solution {
    public long maxPoints(int[][] points) {
        int rows = points.length;
        int cols = points[0].length;

        long[] dp = new long[cols];
        for (int c = 0; c < cols; ++c) dp[c] = points[0][c];

        for (int r = 1; r < rows; ++r) {
            long[] left = new long[cols];
            long[] right = new long[cols];

            // left‑to‑right sweep
            left[0] = dp[0];
            for (int c = 1; c < cols; ++c) {
                left[c] = Math.max(left[c - 1], dp[c] + c);
            }

            // right‑to‑left sweep
            right[cols - 1] = dp[cols - 1] - (cols - 1);
            for (int c = cols - 2; c >= 0; --c) {
                right[c] = Math.max(right[c + 1], dp[c] - c);
            }

            long[] newDp = new long[cols];
            for (int c = 0; c < cols; ++c) {
                long bestPrev = Math.max(left[c] - c, right[c] + c);
                newDp[c] = bestPrev + points[r][c];
            }
            dp = newDp;
        }

        long ans = Long.MIN_VALUE;
        for (long v : dp) ans = Math.max(ans, v);
        return ans;
    }
}
```

---

### 1.2 Python 3

```python
# 1937. Maximum Number of Points with Cost
# Python 3

class Solution:
    def maxPoints(self, points) -> int:
        rows, cols = len(points), len(points[0])
        dp = [points[0][c] for c in range(cols)]

        for r in range(1, rows):
            left = [0] * cols
            right = [0] * cols

            left[0] = dp[0]
            for c in range(1, cols):
                left[c] = max(left[c-1], dp[c] + c)

            right[cols-1] = dp[cols-1] - (cols-1)
            for c in range(cols-2, -1, -1):
                right[c] = max(right[c+1], dp[c] - c)

            new_dp = [0] * cols
            for c in range(cols):
                best_prev = max(left[c] - c, right[c] + c)
                new_dp[c] = best_prev + points[r][c]
            dp = new_dp

        return max(dp)
```

---

### 1.3 C++ (g++17)

```cpp
// 1937. Maximum Number of Points with Cost
// C++17

class Solution {
public:
    long long maxPoints(vector<vector<int>>& points) {
        int R = points.size(), C = points[0].size();
        vector<long long> dp(C);
        for (int c = 0; c < C; ++c) dp[c] = points[0][c];

        for (int r = 1; r < R; ++r) {
            vector<long long> left(C), right(C);

            left[0] = dp[0];
            for (int c = 1; c < C; ++c)
                left[c] = max(left[c-1], dp[c] + c);

            right[C-1] = dp[C-1] - (C-1);
            for (int c = C-2; c >= 0; --c)
                right[c] = max(right[c+1], dp[c] - c);

            vector<long long> newDp(C);
            for (int c = 0; c < C; ++c) {
                long long bestPrev = max(left[c] - c, right[c] + c);
                newDp[c] = bestPrev + points[r][c];
            }
            dp.swap(newDp);
        }

        return *max_element(dp.begin(), dp.end());
    }
};
```

> **Why `long long`?**  
> Even though each `points[r][c] ≤ 10⁵`, we may add up to 10⁵ cells and subtract up to 10⁵ * 10⁵ in the worst case.  
> The maximum intermediate value fits comfortably in 64‑bit signed integer (`long long` / `long`), preventing overflow.

---

## 2. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 1937”

### Title (SEO‑Optimized)

> **“Master LeetCode 1937 – Maximum Number of Points with Cost: A Deep Dive into DP, Pitfalls, and Interview Success”**

### Meta Description

> Learn the cleanest DP solution to LeetCode 1937, avoid common mistakes, and discover how to ace this question in your next tech interview. Includes Java, Python, and C++ code, plus career‑boosting insights.

---

### Table of Contents

1. [Problem Overview](#problem-overview)  
2. [Brute‑Force – The Bad](#brute-force)  
3. [Dynamic Programming – The Good](#dp-good)  
4. [Two‑Pass Optimization – The Ugly & The Clever](#two-pass)  
5. [Edge Cases & Testing](#edge-cases)  
6. [Time & Space Complexity](#complexity)  
7. [Interview Take‑aways](#interview)  
8. [Conclusion & Career Tips](#conclusion)

---

#### 1. Problem Overview <a name="problem-overview"></a>

> **Maximum Number of Points with Cost (LeetCode 1937)**  
> Given an `m × n` matrix `points`, choose exactly one cell per row.  
> Score = sum of chosen cells – sum of absolute column differences between consecutive rows.  
> Return the maximum achievable score.

> **Constraints**  
> • `1 ≤ m, n ≤ 10⁵`  
> • `m × n ≤ 10⁵`  
> • `0 ≤ points[i][j] ≤ 10⁵`

The problem is a classic DP on a 2‑D grid with a cost that depends on column distance.

---

#### 2. Brute‑Force – The Bad <a name="brute-force"></a>

A naïve approach would try all `n^m` possible paths.  
Even for `m = 10` and `n = 10`, that’s `10⁵` possibilities – still too large.  
More generally, a straightforward DP that iterates over all pairs of columns in every transition would cost **O(m × n²)**, which is impossible when `n` is `10⁵`.

**Takeaway:** Avoid nested column loops; you need a linear‑time per row strategy.

---

#### 3. Dynamic Programming – The Good <a name="dp-good"></a>

**Observation**

For row `r`, if we already know the best scores up to row `r‑1` for every column `c`, we can compute the best score for each column `c'` in row `r`:

```
dp_r[c'] = points[r][c'] + max over all c ( dp_{r-1}[c] - |c' - c| )
```

The only problem is that the inner maximum looks quadratic.

**Reformulation**

Because `|c' - c| = c' - c` if `c ≤ c'` and `c - c'` otherwise, we can split the maximum into two parts:

```
max(  max_{c ≤ c'} (dp_{r-1}[c] + c) - c',
      max_{c ≥ c'} (dp_{r-1}[c] - c) + c' )
```

Notice that the expression inside each max depends only on a *prefix* or *suffix* of `c`.  
Thus, by pre‑computing the best prefix values (`c` increasing) and the best suffix values (`c` decreasing), we can answer every `c'` in O(1) time.

---

#### 4. Two‑Pass Optimization – The Ugly & The Clever <a name="two-pass"></a>

**Prefix sweep (left → right)**

```
left[c] = max( left[c-1], dp_{r-1}[c] + c )
```

After the sweep, `left[c]` equals `max_{k ≤ c} (dp_{r-1}[k] + k)`.

**Suffix sweep (right → left)**

```
right[c] = max( right[c+1], dp_{r-1}[c] - c )
```

After the sweep, `right[c]` equals `max_{k ≥ c} (dp_{r-1}[k] - k)`.

**Combining**

```
bestPrev = max( left[c'] - c',  right[c'] + c' )
dp_r[c'] = points[r][c'] + bestPrev
```

Because each sweep is linear, the whole transition per row is **O(n)**.

**Why is it “the ugly” part?**  
At first glance the two sweeps look like a trick, but they’re in fact a *clever* way to turn a quadratic DP into linear time.  
If you’re comfortable explaining why the sweeps work, you’ll impress interviewers.

---

#### 4. Edge Cases & Testing <a name="edge-cases"></a>

| Test | Matrix | Expected Score | Why it matters |
|------|--------|----------------|----------------|
| 1 | `[[0]]` | 0 | Single row, no cost |
| 2 | `[[1, 2, 3]]` | 3 | Single row, check if the algorithm chooses the max cell |
| 3 | `[[5, 0], [0, 5]]` | 5 | Two rows, max when you stay in same column |
| 4 | `[[1, 100], [100, 1]]` | 199 | Cost is zero when you alternate columns (`|1-0|=1`, `|0-1|=1`) – you’ll learn to handle large `|c'-c|` |
| 5 | `[[10^5]*10^5]` | `10^5 × 10^5` | Max possible values, tests overflow if you use 32‑bit ints |
| 6 | `m × n == 10⁵` with `m=1, n=10⁵` | Sum of all cells | Transition is trivial – the algorithm should still finish in < 1 s |

**Test Strategy**

1. **Small manual matrices** – sanity‑check every line of DP.  
2. **Random large matrices** – generate `m × n ≤ 10⁵` and verify against a brute‑force solver for `m ≤ 5`.  
3. **Edge limits** – `m = 1` and `n = 10⁵`, and the reverse.  

If your implementation passes all these, you’re ready for an interview.

---

#### 5. Time & Space Complexity <a name="complexity"></a>

| Algorithm | Time | Extra Space |
|-----------|------|-------------|
| Brute‑Force | **O(m × n²)** | O(1) |
| DP (quadratic) | **O(m × n²)** | O(n) |
| **Two‑Pass DP (ours)** | **O(m × n)** | **O(n)** |

With `m × n ≤ 10⁵`, this means the solution runs in milliseconds on LeetCode and satisfies all constraints.

---

#### 6. Interview Take‑aways <a name="interview"></a>

| Tip | Why it matters |
|-----|----------------|
| **Explain the cost function analytically** – break `|c'‑c|` into `c'‑c` / `c‑c'`. | Shows you can simplify a problem before coding. |
| **Show the two‑pass sweep** – many candidates skip this step. | Demonstrates algorithmic creativity and time‑savings. |
| **Highlight `long long`/`long` usage** – avoid overflow. | Recruiters value attention to detail and safe coding practices. |
| **Mention “O(m × n²)” is a trap** – talk about why it fails. | Reveals you understand constraints and performance budgets. |
| **Run through an example on the whiteboard** – walk through a 3×4 matrix. | Interviewers love to see reasoning, not just code. |

> **Bonus:** Many hiring managers ask “What would you do if `n` were 10⁶?” – answer: “You can’t run `n²` loops; you need the two‑pass trick or an alternative data structure (segment tree) but it still costs O(n log n).”

---

#### 7. Conclusion & Career Tips <a name="conclusion"></a>

> **You’ve just solved LeetCode 1937 with optimal time, optimal memory, and zero risk of integer overflow.**  
> By mastering this question you’ll:

1. **Showcase DP mastery** – a must‑know for data‑structures and algorithms interviews.  
2. **Avoid the most common pitfalls** – quadratic sweeps, wrong absolute‑value handling, integer overflow.  
3. **Build a “portfolio of languages”** – ready Java, Python, and C++ implementations help you answer “Give me the same solution in 3 languages”.  
4. **Strengthen your coding‑confidence** – practice this exact problem before your next interview.

> **Job‑Interview Tip:**  
> When you’re asked to solve a LeetCode problem, always start with a high‑level idea (DP here) before diving into code.  Walk your interviewer through the “bad”, “good”, and “ugly” steps – it demonstrates *problem‑solving thinking*, which recruiters value far more than a single correct snippet.

---

### Final Thought

LeetCode 1937 is more than a coding puzzle; it’s a micro‑ecosystem of constraints, algorithmic choices, and interview rhetoric.  
By presenting a clean DP solution in **Java**, **Python**, and **C++**, you’re showing that you can:

* Write production‑grade code  
* Optimize for time & space  
* Communicate complex ideas clearly  

All of which are exactly what **top tech recruiters** look for.  Keep practicing, keep polishing, and you’ll land that job offer in no time!