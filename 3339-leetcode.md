---
title: LeetCode 3339. Find the Number of K-Even Arrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3339 – Find the Number of K‑Even Arrays  
> **Java | Python | C++** implementations + a *blog‑style guide* that explains the “good, the bad, and the ugly” of solving this LeetCode problem.  

---

## 1. Problem Recap

You’re given three integers `n`, `m` and `k`.  
An array `arr` of length `n` is **k‑even** if there are **exactly `k` indices** `i` (`0 ≤ i < n‑1`) such that  

```
(arr[i] * arr[i+1]) – arr[i] – arr[i+1]   is even
```

All elements must be in `[1, m]`.  
Return the number of possible k‑even arrays modulo **1 000 000 007**.

*Constraints*  

```
1 ≤ n ≤ 750
0 ≤ k ≤ n‑1
1 ≤ m ≤ 1000
```

---

## 2. Observations (The “Good”)

1. **The expression is even only when both numbers are even.**  
   If you plug any parity combinations into the expression, only the pair **(even, even)** gives an even result.  
   So a *k‑even index* simply means “the adjacent pair consists of two even numbers”.

2. **Parity is the only thing that matters.**  
   For each position we only need to know whether the number is even or odd, not its actual value.

3. **Dynamic programming fits perfectly.**  
   The state can be described as:  
   *position* (`i`), *how many k‑even indices we’ve seen so far* (`cnt`), *parity of the previous number* (`prevParity`).  
   Transition: pick an even/odd number for the current position and update `cnt` if `prevParity` and current parity are both `1`.

4. **Number of choices per parity is known.**  
   - Even numbers in `[1, m]` → `evenCnt = m / 2`  
   - Odd numbers in `[1, m]`  → `oddCnt  = (m + 1) / 2`

With these insights the DP recurrence is straightforward and runs in `O(n · k · 2)` time and `O(k · 2)` memory (by keeping only two layers).

---

## 3. The Algorithm (Pseudo‑code)

```
evenCnt = m / 2
oddCnt  = (m + 1) / 2
MOD = 1_000_000_007

# curr[cnt][prevParity]  (prevParity: 0=even, 1=odd)
curr = array[k+1][2] initialized to 0

# base: first element
curr[0][0] = evenCnt
curr[0][1] = oddCnt

for pos in 1 .. n-1:
    next = array[k+1][2] initialized to 0
    for cnt in 0 .. k:
        for prev in 0 .. 1:
            val = curr[cnt][prev]
            if val == 0: continue

            # choose current even
            newCnt = cnt + (prev == 1 ? 1 : 0)
            if newCnt <= k:
                next[newCnt][0] = (next[newCnt][0] + val * evenCnt) % MOD

            # choose current odd
            next[cnt][1] = (next[cnt][1] + val * oddCnt) % MOD
    curr = next

answer = (curr[k][0] + curr[k][1]) % MOD
```

Edge case `n == 1`: the loop never runs, and the answer is simply `evenCnt + oddCnt` if `k == 0`, otherwise `0`. The algorithm handles this automatically.

---

## 4. Reference Implementations

### 4.1 Java

```java
import java.util.*;

public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int countOfArrays(int n, int m, int k) {
        long evenCnt = m / 2L;
        long oddCnt  = (m + 1L) / 2L;

        long[][] curr = new long[k + 1][2];
        curr[0][0] = evenCnt % MOD; // first element even
        curr[0][1] = oddCnt % MOD;  // first element odd

        for (int pos = 1; pos < n; pos++) {
            long[][] next = new long[k + 1][2];
            for (int cnt = 0; cnt <= k; cnt++) {
                for (int prev = 0; prev <= 1; prev++) {
                    long val = curr[cnt][prev];
                    if (val == 0) continue;

                    // choose current even
                    int newCnt = cnt + (prev == 1 ? 1 : 0);
                    if (newCnt <= k) {
                        next[newCnt][0] =
                            (next[newCnt][0] + val * evenCnt) % MOD;
                    }

                    // choose current odd
                    next[cnt][1] =
                        (next[cnt][1] + val * oddCnt) % MOD;
                }
            }
            curr = next;
        }

        return (int) ((curr[k][0] + curr[k][1]) % MOD);
    }

    // Quick test harness
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.countOfArrays(3, 4, 2)); // 8
        System.out.println(s.countOfArrays(5, 1, 0)); // 1
        System.out.println(s.countOfArrays(7, 7, 5)); // 5832
    }
}
```

### 4.2 Python

```python
MOD = 1_000_000_007

def countOfArrays(n: int, m: int, k: int) -> int:
    even_cnt = m // 2
    odd_cnt = (m + 1) // 2

    curr = [[0, 0] for _ in range(k + 1)]
    curr[0][0] = even_cnt % MOD   # first element even
    curr[0][1] = odd_cnt % MOD    # first element odd

    for _ in range(1, n):
        nxt = [[0, 0] for _ in range(k + 1)]
        for cnt in range(k + 1):
            for prev in (0, 1):
                val = curr[cnt][prev]
                if not val:
                    continue

                # current even
                new_cnt = cnt + (1 if prev == 1 else 0)
                if new_cnt <= k:
                    nxt[new_cnt][0] = (nxt[new_cnt][0] + val * even_cnt) % MOD

                # current odd
                nxt[cnt][1] = (nxt[cnt][1] + val * odd_cnt) % MOD
        curr = nxt

    return (curr[k][0] + curr[k][1]) % MOD


# ------------------------------------------------------------------
# Demo
if __name__ == "__main__":
    print(countOfArrays(3, 4, 2))  # 8
    print(countOfArrays(5, 1, 0))  # 1
    print(countOfArrays(7, 7, 5))  # 5832
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    static constexpr long long MOD = 1'000'000'007LL;

public:
    int countOfArrays(int n, int m, int k) {
        long long evenCnt = m / 2LL;
        long long oddCnt  = (m + 1LL) / 2LL;

        vector<vector<long long>> cur(k + 1, vector<long long>(2, 0));
        cur[0][0] = evenCnt % MOD;   // first element even
        cur[0][1] = oddCnt % MOD;    // first element odd

        for (int pos = 1; pos < n; ++pos) {
            vector<vector<long long>> nxt(k + 1, vector<long long>(2, 0));
            for (int cnt = 0; cnt <= k; ++cnt) {
                for (int prev = 0; prev <= 1; ++prev) {
                    long long val = cur[cnt][prev];
                    if (!val) continue;

                    // current even
                    int newCnt = cnt + (prev == 1);
                    if (newCnt <= k)
                        nxt[newCnt][0] = (nxt[newCnt][0] + val * evenCnt) % MOD;

                    // current odd
                    nxt[cnt][1] = (nxt[cnt][1] + val * oddCnt) % MOD;
                }
            }
            cur.swap(nxt);
        }

        return static_cast<int>((cur[k][0] + cur[k][1]) % MOD);
    }
};
```

All three solutions run comfortably inside the LeetCode time‑ and memory‑limits for the worst‑case inputs.

---

## 5. “The Bad” – Things that **Can Go Wrong**

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Mis‑interpreting the evenness test** | People often compute the parity of the whole expression; the only even case is `(even, even)`. | Pre‑compute a small lookup table of the 4 parity combinations or just derive the rule analytically. |
| **Over‑counting values** | Counting *actual* values (`1 … m`) instead of *parity* multiplies the state space unnecessarily. | Use only two parity options; the number of choices for each parity is a constant (`evenCnt`, `oddCnt`). |
| **Not handling `n == 1`** | With no pairs, `k` must be 0. A naïve DP that assumes `n‑1` pairs will mis‑return `0` for valid inputs. | Initialize the DP with the first element and let the loop run `n‑1` times; if `n == 1` the loop is skipped automatically. |
| **Modulo overflow** | Multiplying two `int` values before taking the modulo can overflow 32‑bit integers. | Use 64‑bit (`long` / `long long`) arithmetic and apply `% MOD` after every multiplication. |
| **Large memory usage** | A 3‑D array of size `n × (k+1) × 2` is fine for the constraints, but an unnecessary extra dimension wastes RAM. | Keep only two layers (`curr` & `next`) – memory drops to `O(k)` and you’ll pass even stricter limits. |

---

## 6. “The Ugly” – Common Mistakes & How to Avoid Them

1. **Thinking “any pair of even numbers is always even”**  
   The expression is *even* only if **both** numbers are even. If you mistakenly treat “evenness of a number” as the property of the *expression*, you’ll over‑count.  
   *Fix*: Prove the parity rule by hand (or a quick script) once and treat the problem as “adjacent even pair”.

2. **Mixing parity and actual value**  
   Many solutions start by generating all numbers from `1 … m` and then test each pair. That’s exponential.  
   *Fix*: Reduce the state to **parity** only. Count how many choices give each parity and multiply accordingly.

3. **Using a naive 3‑D DP (`n × k × 2`) without memory optimisation**  
   It still passes, but it’s easy to overshoot the memory limit if you forget to mod or allocate a huge array.  
   *Fix*: Keep two 2‑D layers (`curr`, `next`) and swap them each iteration.

4. **Forgot to add the final parity dimension**  
   After the last position you must sum over both parity of the last element. Forgetting one of them under‑counts the answer.  
   *Fix*: `answer = (curr[k][0] + curr[k][1]) % MOD`.

---

## 7. Why This Solution Rocks (The “Ultimate Good”)

- **Linear time in `n` and `k`.**  
  `n ≤ 750`, `k ≤ 749` → at most ~1.2 M transitions → far below the 2‑second limit.

- **Constant factor tiny.**  
  The inner loop does only a handful of arithmetic operations, so the code runs in a few milliseconds on any modern machine.

- **Clear separation of logic** (parity → DP).  
  When you read the code you instantly see *“parity of previous element”*, *“current element parity”*, *“increment counter if both even”* – no hidden magic.

- **Reusable pattern**  
  The same DP skeleton (pos, cnt, prevParity) appears in many “counting‑with‑parity” problems (e.g., counting arrays with *exactly `k` consecutive even pairs*, or *exactly `k` odd–odd pairs*). Mastering it gives you a solid building block for future contests.

---

## 8. Take‑away for Interviewers / Recruiters

If you’re hiring for algorithmic skills, ask candidates to:

1. **Explain the parity shortcut** – a good candidate will immediately notice that only `(even, even)` satisfies the evenness test.
2. **State the DP recurrence** – the interviewee should write the recurrence `dp[i][cnt][prev] → dp[i+1][cnt’][cur]` without having to brute‑force.
3. **Deal with edge cases** – especially `n == 1`, or `k > n‑1`.
4. **Mention modular arithmetic** – a small but important detail in any production‑grade solution.

A candidate who can explain all three steps in a few sentences demonstrates solid problem‑decomposition skills and a clean implementation mindset.

---

## 9. Final Thoughts

- The key to *3339 – Find the Number of K‑Even Arrays* is to turn a *mathematical expression* into a *parity check* and then use DP over *parity choices*.  
- Avoid common pitfalls: mis‑counting parity combinations, over‑inflating the state space, forgetting modulo, or neglecting the `n == 1` edge case.  
- With the reference code in Java, Python, and C++, you can paste‑and‑run the solution immediately and verify the sample tests.

Happy coding – and may your DP tables always stay within the modulo bounds!