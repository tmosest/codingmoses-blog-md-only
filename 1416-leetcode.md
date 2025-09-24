---
title: LeetCode 1416. Restore The Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Restore The Array (LeetCode 1416) – A Complete Guide  
> *Dynamic Programming, Java, Python, C++ – One of the most common “array‑reconstruction” interview puzzles.*

---

## Table of Contents  

| # | Section |
|---|---------|
| 1 | Problem Statement |
| 2 | Why This Problem is a **Must‑Know** Interview Question |
| 3 | Brute‑Force Insight (and why it fails) |
| 4 | Optimal Approach – Dynamic Programming |
| 5 | Implementation Walk‑through |
| 6 | Edge Cases & “Gotchas” |
| 7 | Time & Space Complexity |
| 8 | Code – Java / Python / C++ |
| 9 | FAQ / Common Interview Variants |
| 10 | How to Use This Blog for Job Hunting |
| 11 | TL;DR |

---

## 1. Problem Statement

You are given:

* a string `s` containing only decimal digits, **no leading zeros**.
* an integer `k` (`1 ≤ k ≤ 10⁹`).

The string `s` is the concatenation of an unknown array of positive integers `A = [a₁, a₂, …, a_m]` with each `a_i` in the inclusive range `[1, k]`.  
Your task is to **count the number of different arrays** that could produce the string `s`.  
Return the answer modulo `1 000 000 007`.

> **Examples**  
> * `s = "1000"`, `k = 10000` → `1`  (array `[1000]`)  
> * `s = "1000"`, `k = 10` → `0`  (no valid split)  
> * `s = "1317"`, `k = 2000` → `8` (list of arrays shown in the statement)

---

## 2. Why This Problem is a **Must‑Know** Interview Question

| ✔ | Reason |
|---|--------|
| **Dynamic Programming** | The solution is a classic DP on string positions. |
| **Edge‑Case Handling** | You must avoid leading zeros, handle large `k`, and watch out for integer overflows. |
| **Scalability** | `|s|` can be 10⁵, so O(n²) solutions will time‑out. |
| **Real‑world Scenario** | Think of parsing log files, decoding messages, or reconstructing packet payloads. |
| **Interviewers Love It** | It tests your ability to combine algorithmic insight with clean coding. |

---

## 3. Brute‑Force Insight (and why it fails)

A naïve approach would try every split point:

```
for every position i in s:
    for every split point j after i:
        if s[i:j] is a valid number <= k:
            recurse on s[j:]
```

This is essentially exponential (`O(2^n)`) and will blow up even for small strings.  
We need a polynomial‑time algorithm.

---

## 4. Optimal Approach – Dynamic Programming

### 4.1 Idea

Process the string from **right to left** (or left to right).  
Let `dp[i]` = number of ways to split the suffix `s[i … n-1]` into valid numbers.

- `dp[n] = 1` – empty suffix is a valid split.
- For each position `i` (0 ≤ i < n):
  - If `s[i] == '0'` → `dp[i] = 0` (leading zeros not allowed).
  - Otherwise, extend a number up to at most `10` digits (since `k ≤ 10⁹`).
  - For each `j` from `i` to `min(i+9, n-1)`:
    - Build the number `num = s[i … j]` incrementally.
    - If `num > k` → break (no longer valid).
    - Else `dp[i] = (dp[i] + dp[j+1]) mod M`.

Finally, answer = `dp[0]`.

### 4.2 Why at most 10 digits?

`k ≤ 10⁹` → any number greater than `10⁹` is automatically invalid, so any substring longer than 10 digits cannot be ≤ k.  

### 4.3 Correctness Sketch

*Base Case*: The empty suffix (`dp[n]`) has exactly one way to be split: choose nothing.  
*Inductive Step*: For any position `i`, every valid split of the suffix must begin with a valid number formed from `s[i … j]`.  
All other splits of the remainder are counted in `dp[j+1]`.  
Summing over all valid `j` gives the total count for `dp[i]`.  
Thus, by induction, the DP correctly counts all arrays.

---

## 5. Implementation Walk‑through

Below is a **bottom‑up DP** (iterative) – preferred for Java/Python due to recursion depth limits.  
You can also implement it recursively with memoization; both are O(n × 10).

Key implementation details:

| Issue | Fix |
|-------|-----|
| **Integer Overflow** | Use `long` for intermediate `num` and modulo operations. |
| **Modulo Constant** | `MOD = 1_000_000_007`. |
| **String Length 1e5** | Use `int[]` for DP; `O(n)` memory. |
| **Leading Zero** | Skip any position where `s[i] == '0'`. |
| **Early Break** | When `num > k`, stop extending the substring – further digits only make it larger. |

---

## 6. Edge Cases & “Gotchas”

1. **String starts with `0`** – answer `0`.  
2. **k < 10** – many substrings are invalid, DP still works.  
3. **Maximum length substring** – we only need to look at 10 characters ahead.  
4. **Large `k` (e.g., 10⁹)** – still fine because we compare `num` against `k` using `long`.  
5. **Python Recursion Limit** – avoid recursion; use iterative DP.  

---

## 7. Time & Space Complexity

* **Time**: `O(n · L)` where `L = 10` (max digits).  
  For `n = 10⁵`, this is roughly `1 million` operations – comfortably fast.  
* **Space**: `O(n)` for the DP array.  

---

## 8. Code – Java / Python / C++

> **All solutions return the answer modulo `1 000 000 007`.**

### 8.1 Java

```java
import java.util.*;

class Solution {
    private static final int MOD = 1_000_000_007;

    public int numberOfArrays(String s, int k) {
        int n = s.length();
        int[] dp = new int[n + 1];
        dp[n] = 1;                     // empty suffix

        for (int i = n - 1; i >= 0; i--) {
            if (s.charAt(i) == '0') {  // leading zero not allowed
                dp[i] = 0;
                continue;
            }

            long num = 0;
            for (int j = i; j < n && j - i < 10; j++) {  // at most 10 digits
                num = num * 10 + (s.charAt(j) - '0');
                if (num > k) break;                     // cannot be valid
                dp[i] = (int)((dp[i] + dp[j + 1]) % MOD);
            }
        }
        return dp[0];
    }
}
```

### 8.2 Python

```python
MOD = 10**9 + 7

class Solution:
    def numberOfArrays(self, s: str, k: int) -> int:
        n = len(s)
        dp = [0] * (n + 1)
        dp[n] = 1  # empty suffix

        for i in range(n - 1, -1, -1):
            if s[i] == '0':   # leading zeros forbidden
                continue
            num = 0
            for j in range(i, min(n, i + 10)):  # at most 10 digits
                num = num * 10 + int(s[j])
                if num > k:
                    break
                dp[i] = (dp[i] + dp[j + 1]) % MOD

        return dp[0]
```

### 8.3 C++

```cpp
class Solution {
public:
    static const int MOD = 1'000'000'007;
    int numberOfArrays(string s, int k) {
        int n = s.size();
        vector<int> dp(n + 1, 0);
        dp[n] = 1;  // base case

        for (int i = n - 1; i >= 0; --i) {
            if (s[i] == '0') continue;   // no leading zero

            long long num = 0;
            for (int j = i; j < n && j - i < 10; ++j) { // max 10 digits
                num = num * 10 + (s[j] - '0');
                if (num > k) break;
                dp[i] = (dp[i] + dp[j + 1]) % MOD;
            }
        }
        return dp[0];
    }
};
```

---

## 9. FAQ / Common Interview Variants

| Question | Answer |
|----------|--------|
| **Can we use recursion with memoization?** | Yes. Depth will be at most `n`, but Python may hit recursion limits; Java & C++ fine. |
| **What if `k` were larger than `10⁹`?** | The algorithm still works; just increase the digit window (`10` → `digits(k)`), but time remains O(n·digits(k)). |
| **Why not use BigInteger?** | `k` is ≤ 10⁹, so a 64‑bit integer is sufficient. |
| **What if the string may contain leading zeros?** | Adjust the condition: skip positions where `s[i] == '0'` unless the number itself is `0` and allowed. |
| **Could we use a Trie?** | Not necessary; DP is simpler and faster. |

---

## 10. How to Use This Blog for Job Hunting

1. **Show Your Problem‑Solving Process** – Include the full blog with code snippets in multiple languages.  
   Recruiters love to see a clean, commented solution, not just the final answer.  
2. **Highlight Time Complexity** – Interviewers often ask, “What is the worst‑case runtime?”  
   Demonstrating the `O(n·10)` analysis shows deep understanding.  
3. **Add a “Common Pitfall” Section** – Mentions about recursion limits, overflow, etc.  
4. **SEO Boost** – Use keywords like **“Restore The Array LeetCode 1416”**, **“dynamic programming array reconstruction”**, **“Java Python C++ interview problem”**, and **“job interview algorithm”** throughout the article.  
5. **Add a Call‑to‑Action** – Invite recruiters to connect or schedule a call; e.g., “Want to discuss more challenging DP problems? Let’s connect on LinkedIn!”  

> **Pro tip:** Post this blog on **Medium**, your personal site, or a GitHub Gist. Add a short demo video explaining the algorithm; video content ranks higher on search engines and catches recruiters’ eyes.

---

## 11. Conclusion

The **Restore The Array** problem (LeetCode 1416) is a classic DP challenge that blends parsing logic with careful boundary checks.  
With the iterative DP strategy above, you can deliver a solution that is:

- **Fast** (works for 10⁵‑length strings),
- **Correct** (handles zeros and large `k`),
- **Portable** (available in Java, Python, and C++).

Now you’re ready to ace this problem and impress hiring managers. Happy coding! 🚀

--- 

*If you found this guide helpful, share it on LinkedIn or Twitter – let’s help more people crack their next interview!*