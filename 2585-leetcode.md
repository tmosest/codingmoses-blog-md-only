---
title: LeetCode 2585. Number of Ways to Earn Points - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap  

**LeetCode 2585 – Number of Ways to Earn Points**  

> You have `n` types of questions.  
> `types[i] = [countᵢ, markᵢ]` means there are `countᵢ` indistinguishable questions of the *i‑th* type and each question is worth `markᵢ` points.  
> You are given an integer `target`.  
> Return the number of different ways to earn **exactly** `target` points.  
> Because the answer can be huge, return it modulo `10⁹ + 7`.  

`target ≤ 1000`, `n ≤ 50`, `countᵢ, markᵢ ≤ 50`.  

---

## 2.  Intuition – A bounded knapsack

Every type of question behaves like a **bounded item** in a classic 0/1‑knapsack:

| item | weight | value | bound |
|------|--------|-------|-------|
| type *i* | `markᵢ` | `markᵢ` | `countᵢ` |

We do **not** care about the “value” (points earned), only about how many ways we can fill a knapsack of capacity `target` using at most `countᵢ` copies of each item.  
Thus the problem is exactly a **bounded knapsack counting** problem.

The most common way to solve this in interview settings is a **bottom‑up DP** that iterates over the items and updates the DP table in *reverse* order, so that each item is used at most once per DP state.

---

## 3.  Bottom‑Up DP (Iterative) – The “good” solution

### 3.1  DP definition  

`dp[s]` – number of ways to reach a score of `s` using the processed types.

Initialize:  
`dp[0] = 1` – there is one way to score zero points (solve nothing).  
All other entries start at `0`.

### 3.2  Transition  

For each type `(cnt, mark)`:

```
for s from target down to 1:
    for k from 1 to cnt:
        if s - k*mark < 0: break
        dp[s] = (dp[s] + dp[s - k*mark]) mod M
```

Why iterate **backwards**?  
If we updated `dp` forwards we would reuse the same type more than `cnt` times within the same iteration.  
The reverse loop guarantees that every update uses a state that *does not yet* include the current type.

### 3.3  Complexity  

*Time*: `O(target × n × max(countᵢ))` – worst case `≈ 1000 × 50 × 50 = 2.5 M` operations.  
*Space*: `O(target)` – one 1‑D array of length `target+1`.

The solution passes comfortably within the limits and is easy to reason about in an interview.

### 3.4  Code

```java
// Java 17
public class Solution {
    private static final int MOD = 1_000_000_007;

    public int waysToReachTarget(int target, int[][] types) {
        int[] dp = new int[target + 1];
        dp[0] = 1;                      // base case

        for (int[] t : types) {
            int cnt  = t[0];
            int mark = t[1];

            for (int s = target; s >= 1; s--) {
                for (int k = 1; k <= cnt; k++) {
                    int prev = s - k * mark;
                    if (prev < 0) break;
                    dp[s] = (dp[s] + dp[prev]) % MOD;
                }
            }
        }
        return dp[target];
    }
}
```

```python
# Python 3.11
class Solution:
    MOD = 1_000_000_007

    def waysToReachTarget(self, target: int, types: List[List[int]]) -> int:
        dp = [0] * (target + 1)
        dp[0] = 1

        for cnt, mark in types:
            for s in range(target, 0, -1):
                for k in range(1, cnt + 1):
                    prev = s - k * mark
                    if prev < 0:
                        break
                    dp[s] = (dp[s] + dp[prev]) % self.MOD

        return dp[target]
```

```cpp
// C++17
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr int MOD = 1'000'000'007;
    int waysToReachTarget(int target, vector<vector<int>>& types) {
        vector<int> dp(target + 1, 0);
        dp[0] = 1;

        for (auto &t : types) {
            int cnt  = t[0];
            int mark = t[1];
            for (int s = target; s >= 1; --s) {
                for (int k = 1; k <= cnt; ++k) {
                    int prev = s - k * mark;
                    if (prev < 0) break;
                    dp[s] += dp[prev];
                    if (dp[s] >= MOD) dp[s] -= MOD;
                }
            }
        }
        return dp[target];
    }
};
```

---

## 4.  Top‑Down DP + Memoization – The “bad” solution that still works

If you are more comfortable with recursion, the **DFS + memoization** version is very readable:

```python
# Python 3.11
from functools import lru_cache

class Solution:
    MOD = 1_000_000_007

    def waysToReachTarget(self, target: int, types: List[List[int]]) -> int:
        @lru_cache(None)
        def dfs(i: int, remaining: int) -> int:
            if remaining == 0:
                return 1
            if i == len(types) or remaining < 0:
                return 0

            cnt, mark = types[i]
            total = 0
            for k in range(cnt + 1):            # 0 … cnt copies of current type
                total += dfs(i + 1, remaining - k * mark)
            return total % self.MOD

        return dfs(0, target)
```

**Pros**  
* Intuitive recursion + memoization (state = `(i, remaining)`).

**Cons**  
* Requires `O(target × n)` memory for the cache (about 50 k integers in the worst case) – still fine, but more overhead than the iterative 1‑D array.  
* May be slower in tight runtime environments because of function call overhead.

---

## 5.  “The ugly” – A naive 3‑nested‑loop that breaks the constraints

```java
// Wrong: O(target² × n) – far too slow for the limits
for (int i = 0; i < n; i++) {
    for (int s = 0; s <= target; s++) {
        for (int k = 0; k <= types[i][0]; k++) {   // reuse the same type many times
            if (s + k*mark <= target) {
                dp[s + k*mark] += dp[s];
            }
        }
    }
}
```

- This forward update **over‑counts** because the same type is reused in the same iteration.  
- It also has a cubic factor (`target × target × n`), which is too big for `target = 1000`.

**Never** show this in an interview unless you have time to correct it on the spot.

---

## 6.  Which solution should you bring to the interview?

| Situation | Recommended approach |
|-----------|----------------------|
| *Time is tight, interviewer wants a quick idea* | Bottom‑up iterative DP (reverse loops). |
| *You want to show elegance and use recursion* | Top‑down DP + `@lru_cache` (Python) or memoized recursion (Java/C++). |
| *The interviewer specifically asks for “bounded knapsack”* | Discuss the analogy, then present the reverse‑loop transition. |

**Tip** – Always mention the **reverse‑order trick** for bounded items; interviewers love to see that you know how to prevent double‑counting.

---

## 7.  Interview‑Ready Checklist

1. **Clarify the “indistinguishable” assumption** – we are counting *combinations*, not permutations.  
2. **Explain the DP state** (`dp[s]` – ways to reach score `s`).  
3. **Show the reverse iteration** – “Why do we iterate backwards? It guarantees that each type is used at most its bound.”  
4. **Mention modulo handling** – `dp[s] = (dp[s] + dp[prev]) % MOD`.  
5. **Give complexity** – `O(target * n * maxCount)` time, `O(target)` space.  
6. **Optional** – If asked, present the top‑down version as a cleaner, albeit slightly slower, alternative.

---

## 8.  SEO‑Optimized Blog Title & Structure

### Title  
**“Number of Ways to Earn Points – The Good, The Bad, and The Ugly of Solving LeetCode 2585”**

### Headings & Keywords  

| Heading | SEO Keywords |
|---------|--------------|
| `# The Good: Bottom‑Up DP (Bounded Knapsack)` | Number of Ways to Earn Points, bounded knapsack, LeetCode 2585, interview algorithm |
| `# The Bad: Naïve Forward DP` | bad solution, naive DP, time complexity, interview pitfalls |
| `# The Ugly: Recursion Overkill` | ugly solution, recursion, memoization, recursion depth, stack overflow |
| `# Real‑World Application` | job interview, software engineer interview, LeetCode practice, coding interview tips |
| `# Summary` | algorithm, complexity, time, space, LeetCode 2585 |

Using Markdown headings with `#` and `##` keeps the article easy to scan.  
Including the problem title, “bounded knapsack,” and “LeetCode” in the first paragraph pulls in readers searching for those terms.

---

## 9.  The Blog Post (Full Text)

> **NOTE** – The code snippets below are ready‑to‑paste for Java, Python, and C++.

```markdown
# Number of Ways to Earn Points – The Good, The Bad, and The Ugly

**LeetCode 2585** is a classic interview puzzle that tests your ability to model a bounded knapsack problem and count the number of ways to reach a target score.  
Below we dissect the problem, show the best‑practice solution, highlight common pitfalls, and finish with a quick‑reference cheat sheet that you can flash before a coding interview.

---

## 📌 Problem Statement

> Given an array `types`, where `types[i] = [countᵢ, markᵢ]` (there are `countᵢ` indistinguishable questions each worth `markᵢ` points) and an integer `target`, return the number of ways to earn **exactly** `target` points.  
> Modulo `10⁹ + 7`.

---

## ✅ The Good: Bottom‑Up DP (Bounded Knapsack)

### 1️⃣ DP State  
`dp[s]` – ways to score exactly `s` points after processing some question types.

### 2️⃣ Transition  
For each `(cnt, mark)` iterate scores **backwards** and add all feasible copies:

```pseudo
for s = target .. 1:
    for k = 1 .. cnt:
        if s - k*mark < 0: break
        dp[s] = (dp[s] + dp[s - k*mark]) % MOD
```

### 3️⃣ Complexity  
*Time* `O(target × n × max(count))` – ≈ 2.5 M ops.  
*Space* `O(target)`.

### 4️⃣ Java 17 Code

```java
public class Solution {
    private static final int MOD = 1_000_000_007;
    public int waysToReachTarget(int target, int[][] types) {
        int[] dp = new int[target + 1];
        dp[0] = 1;
        for (int[] t : types) {
            int cnt = t[0], mark = t[1];
            for (int s = target; s >= 1; s--) {
                for (int k = 1; k <= cnt; k++) {
                    int prev = s - k * mark;
                    if (prev < 0) break;
                    dp[s] = (dp[s] + dp[prev]) % MOD;
                }
            }
        }
        return dp[target];
    }
}
```

### 5️⃣ Python 3.11 Code

```python
from typing import List

class Solution:
    MOD = 1_000_000_007
    def waysToReachTarget(self, target: int, types: List[List[int]]) -> int:
        dp = [0] * (target + 1)
        dp[0] = 1
        for cnt, mark in types:
            for s in range(target, 0, -1):
                for k in range(1, cnt + 1):
                    prev = s - k * mark
                    if prev < 0:
                        break
                    dp[s] = (dp[s] + dp[prev]) % self.MOD
        return dp[target]
```

### 6️⃣ C++17 Code

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr int MOD = 1'000'000'007;
    int waysToReachTarget(int target, vector<vector<int>>& types) {
        vector<int> dp(target + 1, 0);
        dp[0] = 1;
        for (auto &t : types) {
            int cnt = t[0], mark = t[1];
            for (int s = target; s >= 1; --s) {
                for (int k = 1; k <= cnt; ++k) {
                    int prev = s - k * mark;
                    if (prev < 0) break;
                    dp[s] += dp[prev];
                    if (dp[s] >= MOD) dp[s] -= MOD;
                }
            }
        }
        return dp[target];
    }
};
```

---

## ⚠️ The Bad: Naïve Forward DP

If you mistakenly iterate scores **forwards** you’ll double‑count combinations and explode the runtime:

```java
for (int s = 0; s <= target; s++) {
    for (int k = 0; k <= cnt; k++) {
        if (s + k*mark <= target) dp[s + k*mark] += dp[s];
    }
}
```

*Why it fails* – forward updates let the same question type be reused in the same iteration.  
Time: `O(target² × n)` – infeasible for `target = 1000`.

---

## 👿 The Ugly: Deep Recursion

Recursion is clean but may hit stack limits.  Use memoization to keep it efficient:

```python
@lru_cache(None)
def dfs(i, remaining):
    ...
```

The overhead of function calls and a larger memo table (≈ 50 k ints) makes it a less attractive choice when speed matters.

---

## 📋 Cheat Sheet

| Step | What to explain | Code snippet (Java) |
|------|----------------|--------------------|
| 1 | Clarify “combinations, not permutations.” | `dp[0] = 1;` |
| 2 | DP state definition | `int[] dp = new int[target + 1];` |
| 3 | Reverse iteration rationale | `for (int s = target; s >= 1; s--)` |
| 4 | Add all valid copies | `for (int k = 1; k <= cnt; k++)` |
| 5 | Modulo handling | `dp[s] = (dp[s] + dp[prev]) % MOD;` |
| 6 | Complexity | `O(target × n × max(count))` |

---

## 🔗 Related Resources

- [Top‑Down DP with Memoization in Python](https://docs.python.org/3/library/functools.html#functools.lru_cache)
- [Bounded Knapsack Problem](https://en.wikipedia.org/wiki/Knapsack_problem)
- [LeetCode 2585 Solutions (Java/Python/C++)](https://leetcode.com/problems/number-of-ways-to-earn-100-points)

---

## 🎯 Summary

* The **reverse‑loop DP** is your go‑to solution for LeetCode 2585.  
* The **naïve forward DP** is a common mistake to avoid.  
* **Recursion + memoization** is acceptable but slower.  
* Always mod the answer by `10⁹ + 7`.

Good luck crushing your next coding interview – and remember: think bounded knapsack, reverse loops, and you’re golden! 🚀
```

---

## 10.  Final Takeaway

- **Bottom‑up iterative DP** is the most efficient, readable, and interview‑friendly solution.  
- Recursion + memoization is a good “clean‑code” alternative.  
- Avoid forward‑looping naive DP unless you can spot and fix the double‑counting immediately.

Use the cheat sheet to stay sharp, and the blog post to reinforce the core concepts in your mind before tackling **LeetCode 2585** or any similar bounded knapsack problem. Happy coding! 🚀

```

---

### End of blog post

---

## 10.  Ready to ship

You now have:

1. The **best‑practice solution** for all three languages.  
2. A quick‑reference cheat sheet that covers the good, bad, and ugly.  
3. A fully‑SEO‑friendly Markdown article ready to be published on a blog or shared on GitHub.

Drop these snippets in your interview, follow the checklist, and you’ll ace **LeetCode 2585** in no time. Good luck!
```

---

That completes the **solution and the SEO‑optimized blog post**. Happy interviewing!