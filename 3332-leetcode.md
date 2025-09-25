---
title: LeetCode 3332. Maximum Points Tourist Can Earn - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ LeetCode 3332 – “Maximum Points Tourist Can Earn”  
### 🚀 Java | Python | C++ – All the code you need  
### 📝 Blog post – The Good, The Bad, and The Ugly (SEO‑friendly)

---

### 1️⃣ Problem Recap

> **You are given**  
> * `n` – number of cities (fully connected graph)  
> * `k` – number of days (0‑indexed)  
> * `stayScore[i][c]` – points earned if you **stay** in city `c` on day `i`  
> * `travelScore[c][d]` – points earned if you **travel** from city `c` to city `d` on any day  
>  
> **Goal**: start in any city, perform one action per day (stay or move), and **maximise the total points** after `k` days.

> **Constraints**  
> * `1 ≤ n ≤ 200`  
> * `1 ≤ k ≤ 200`  
> * `0 ≤ travelScore[c][d] ≤ 100`, `1 ≤ stayScore[i][c] ≤ 100`

---

### 2️⃣ Intuition

The problem is a classic *time‑dependent DP*:

* The tourist’s future depends only on **where** he is *and* **which day** it is.
* We can work **backwards** – after the last day the tourist earns nothing, so the DP for “next day” is `0` for all cities.
* For each day we choose **stay** or **move** and pick the best outcome.

The key transition for city `c` on day `d` is

```
stay  = stayScore[d][c] + dp[d+1][c]
move  = max over all d' of ( travelScore[c][d'] + dp[d+1][d'] )
dp[d][c] = max(stay, move)
```

Because we only need the DP for the *next* day, we keep **two 1‑D arrays** (`prev`, `curr`) instead of a full 2‑D table → **O(n)** space.

---

### 3️⃣ Complexity

* **Time**: `O(k · n²)` – for every day we evaluate a `n`‑by‑`n` transition.
* **Space**: `O(n)` – two 1‑D arrays.

With the limits (`n, k ≤ 200`) this is well within the limits of LeetCode (≈8 million inner iterations).

---

### 4️⃣ Code

#### 📌 Java

```java
import java.util.*;

class Solution {
    public int maxScore(int n, int k, int[][] stayScore, int[][] travelScore) {
        int[] prev = new int[n];   // dp for next day (initially 0)
        int[] curr = new int[n];

        for (int day = k - 1; day >= 0; day--) {
            for (int city = 0; city < n; city++) {
                // Stay in the same city
                int stay = stayScore[day][city] + prev[city];

                // Move to any other city (including staying: travelScore[city][city] == 0)
                int move = Integer.MIN_VALUE;
                for (int dest = 0; dest < n; dest++) {
                    int val = travelScore[city][dest] + prev[dest];
                    if (val > move) move = val;
                }
                curr[city] = Math.max(stay, move);
            }
            // Swap prev & curr
            int[] tmp = prev;
            prev = curr;
            curr = tmp;
        }

        // Starting city can be any, so take the best
        int answer = 0;
        for (int val : prev) {
            if (val > answer) answer = val;
        }
        return answer;
    }
}
```

---

#### 📌 Python 3

```python
class Solution:
    def maxScore(self, n: int, k: int, stayScore: List[List[int]],
                 travelScore: List[List[int]]) -> int:
        prev = [0] * n          # dp for next day
        curr = [0] * n

        for day in range(k - 1, -1, -1):
            for city in range(n):
                stay = stayScore[day][city] + prev[city]
                # Max over all destinations
                move = max(travelScore[city][dest] + prev[dest] for dest in range(n))
                curr[city] = max(stay, move)
            prev, curr = curr, prev   # reuse arrays

        return max(prev)
```

---

#### 📌 C++

```cpp
class Solution {
public:
    int maxScore(int n, int k, vector<vector<int>>& stayScore,
                 vector<vector<int>>& travelScore) {
        vector<int> prev(n, 0), curr(n, 0);

        for (int day = k - 1; day >= 0; --day) {
            for (int city = 0; city < n; ++city) {
                int stay = stayScore[day][city] + prev[city];
                int move = INT_MIN;
                for (int dest = 0; dest < n; ++dest) {
                    int val = travelScore[city][dest] + prev[dest];
                    move = max(move, val);
                }
                curr[city] = max(stay, move);
            }
            swap(prev, curr);      // reuse the two arrays
        }
        return *max_element(prev.begin(), prev.end());
    }
};
```

---

### 5️⃣ Blog Article – “The Good, The Bad, and The Ugly”  

---

#### Title
> **Maximum Points Tourist Can Earn – LeetCode 3332**  
> **Java / Python / C++ DP Solution | Interview Prep**

#### Meta Description (SEO)
> Learn the optimal dynamic‑programming approach for LeetCode 3332 “Maximum Points Tourist Can Earn”. Full solutions in Java, Python, and C++. Perfect interview prep for software engineering jobs.

---

## Introduction

When the recruiter asks, “Can you solve a DP problem involving cities, travel scores, and stay scores?”, the first instinct is to think of a brute‑force search.  
But **LeetCode 3332** is a classic *time‑dependent dynamic programming* challenge that tests both your mathematical insight and coding style.

In this post we’ll:

1. Break down the problem.
2. Walk through the **good** DP trick.
3. Show where the **bad** pitfalls lie.
4. Confront the **ugly** edge‑cases.
5. Share clean, production‑ready code in Java, Python, and C++.

---

## 1️⃣ The Good – O(k · n²) DP with Two Arrays

* **Backward DP**: By starting from the last day and moving backwards, we only ever need the “next‑day” values.
* **Two 1‑D arrays** (`prev`, `curr`): reduces memory from `O(k·n)` to `O(n)`.  
  This is the “clean‑up” trick that keeps the code readable and fast.
* **Explicit stay/move transitions**:  
  ```text
  stay = stayScore[day][city] + prev[city]
  move = max( travelScore[city][dest] + prev[dest] )
  curr[city] = max(stay, move)
  ```
  Simple formulas, no hidden loops.

Because `n, k ≤ 200`, the inner `n²` loop runs at most 40 000 times per day – perfectly fine for LeetCode.

---

## 2️⃣ The Bad – When Things Go Wrong

| Bad Practice | Why It’s Bad | Fix |
|--------------|--------------|-----|
| **Storing the whole `dp[k][n]` table** | Uses `O(k·n)` memory – unnecessary and can hit limits for bigger constraints. | Keep only two rows (`prev`, `curr`). |
| **Re‑computing `move` from scratch every time** | Still `O(n²)` per day, but you can pre‑compute the inner max if you have many queries (not needed here). | Compute `move` inside the inner loop as shown; simple and fast. |
| **Using recursion + memoization** | Risk of stack overflow in Java/C++ and extra overhead. | Iterative DP (bottom‑up) is safer. |

---

## 3️⃣ The Ugly – Edge Cases & Subtle Bugs

| Ugly Situation | Impact | Handling |
|----------------|--------|----------|
| `travelScore[city][city] == 0` | It’s tempting to skip `dest == city`, but skipping can hide a valid transition if staying is the best move. | Keep the destination loop as‑is; the `0` weight is harmless. |
| **Large values near `int` limits** | The maximum total points can reach `100 * 200 + 100 * 200 = 40 000`. Still safe for `int`. | If the constraints changed, use `long`. |
| **All cities tied** | The answer is the max over the first day; forgetting to take `max(prev)` leads to an off‑by‑one error. | After the final swap, use `max_element(prev)` (C++), `max(prev)` (Python), or a simple loop (Java). |

---

## 3️⃣ Full Solutions – Clean Code in Every Language

We already posted the **Java**, **Python**, and **C++** implementations earlier.  
Each version follows the same structure, uses clear variable names (`prev`, `curr`), and includes comments explaining the transition.

---

## 6️⃣ Takeaway for the Interview

* **Explain the DP**: Talk about backward iteration, two‑row optimization, and the stay/move formula.  
  > “I compute `dp[d][c]` by looking at the next day, so I only need two arrays.”  
* **Mention time/space**: Show you can handle `n, k = 200` comfortably.  
* **Show code**: Offer a clean, compilable snippet in the language of choice.  
  – Recruiters love concise code that runs in milliseconds.

> **Result**: You’ll demonstrate mastery over time‑dependent DP, memory optimisation, and clean coding—all golden traits for a software‑engineering interview.

---

## 🔗 Further Reading

* [Dynamic Programming – Interview Algorithm Library](https://www.interviewbit.com/dynamic-programming/)
* [Space‑Optimised DP – LeetCode Tips](https://leetcode.com/articles/dynamic-programming-tutorial/)
* [C++ `swap()` vs `memcpy()` for DP arrays](https://stackoverflow.com/questions/1137481/why-swap-is-faster-than-copys)

---

### 🚀 Final Thought

LeetCode 3332 is not just a DP puzzle; it’s a *coding style test*.  
If you can:

1. **Explain the transition** quickly.
2. **Write iterative bottom‑up code** with two rows.
3. **Know the pitfalls** (big tables, recursion stack, etc.).

you’ll shine in any coding interview and stand out to hiring managers.

Happy coding! 🎉

---