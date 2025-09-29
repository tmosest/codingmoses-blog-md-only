---
title: LeetCode 902. Numbers At Most N Given Digit Set - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 902 – Numbers At Most N Given Digit Set  
**Java / Python / C++ – Full solutions + a “Good / Bad / Ugly” interview blog**  

---

## 🎯 Problem Summary

> **Given** a sorted array `digits` (each element is a single digit `'1'…'9'`) and an integer `n`,  
> **Return** how many positive integers that can be written using only the digits in `digits` are **≤ n**.

*Example*  
`digits = ["1","3","5","7"]`, `n = 100` → **20** numbers: 1, 3, 5, 7, 11, 13, …, 77.

**Constraints**

| Item | Value |
|------|-------|
| `1 ≤ digits.length ≤ 9` |  |
| each `digits[i]` is a character `'1'…'9'` |  |
| `1 ≤ n ≤ 10⁹` |  |

The problem is a classic **digit‑DP / counting** problem that can be solved in *O(log n)* time and *O(1)* space with a simple combinatorial approach.

---

## 🧠 Solution Overview (the “Good” part)

1. **Count all numbers with fewer digits than `n`**  
   If `n` has `k` digits, any number with `1 … k‑1` digits can be formed from `digits.length` choices for each place.  
   ```
   total = Σ_{i=1}^{k-1} (digits.length)^i
   ```

2. **Handle numbers with the same number of digits**  
   Scan the digits of `n` from most significant to least significant.  
   For the current position `i` (0‑based from left):
   * Count how many digits from `digits` are **strictly less** than `n[i]`.  
     Each such digit can be followed by any combination of the remaining `k-i-1` positions → add  
     `(countLess) * (digits.length)^{k-i-1}` to the answer.
   * If **no digit equals** `n[i]`, we are finished – the current prefix already exceeds `n`.
   * If there **is a digit equal** to `n[i]`, continue to the next position.

3. **Add 1 for `n` itself if it can be formed**  
   If we never broke out early, all prefixes matched `n`; thus `n` itself is a valid number → add `1`.

The algorithm runs in linear time in the number of digits of `n` (≤ 10) and uses constant extra space.

---

## 📚 Code – Java

```java
class Solution {
    public int atMostNGivenDigitSet(String[] digits, int n) {
        String N = String.valueOf(n);
        int k = N.length();          // number of digits in n
        int dlen = digits.length;    // number of allowed digits

        long total = 0;

        // 1) all numbers with fewer digits than n
        for (int len = 1; len < k; len++) {
            total += pow(dlen, len);
        }

        // 2) numbers with the same length
        for (int i = 0; i < k; i++) {
            int cur = N.charAt(i) - '0';
            boolean equal = false;

            for (int j = 0; j < dlen; j++) {
                int d = digits[j].charAt(0) - '0';
                if (d < cur) {
                    total += pow(dlen, k - i - 1);
                } else if (d == cur) {
                    equal = true;
                }
            }

            if (!equal) {          // prefix already exceeds n
                return (int) total;
            }
        }

        // 3) n itself is valid
        return (int) (total + 1);
    }

    // fast integer power for small exponents
    private long pow(int base, int exp) {
        long r = 1;
        while (exp-- > 0) r *= base;
        return r;
    }
}
```

**Complexities**  
- Time: `O(log n)` (≤ 10 iterations)  
- Space: `O(1)`

---

## 📚 Code – Python

```python
class Solution:
    def atMostNGivenDigitSet(self, digits: List[str], n: int) -> int:
        N = str(n)
        k = len(N)
        dlen = len(digits)

        # helper: integer power
        def pow_int(b, e):
            return b ** e

        total = 0

        # 1) all numbers with fewer digits
        for length in range(1, k):
            total += pow_int(dlen, length)

        # 2) same length
        for i, ch in enumerate(N):
            cur = int(ch)
            equal = False
            for d in digits:
                val = int(d)
                if val < cur:
                    total += pow_int(dlen, k - i - 1)
                elif val == cur:
                    equal = True
            if not equal:
                return total

        # 3) n itself
        return total + 1
```

---

## 📚 Code – C++

```cpp
class Solution {
public:
    int atMostNGivenDigitSet(vector<string>& digits, int n) {
        string N = to_string(n);
        int k = N.size();          // digits in n
        int dlen = digits.size();  // allowed digits

        long long total = 0;

        // 1) fewer digits
        for (int len = 1; len < k; ++len)
            total += pow_int(dlen, len);

        // 2) same length
        for (int i = 0; i < k; ++i) {
            int cur = N[i] - '0';
            bool equal = false;
            for (const string &d : digits) {
                int val = d[0] - '0';
                if (val < cur)
                    total += pow_int(dlen, k - i - 1);
                else if (val == cur)
                    equal = true;
            }
            if (!equal) return static_cast<int>(total);
        }

        // 3) n itself
        return static_cast<int>(total + 1);
    }

private:
    long long pow_int(int base, int exp) {
        long long r = 1;
        while (exp--) r *= base;
        return r;
    }
};
```

---

## 🤕 The “Bad” & “Ugly” – Common Pitfalls

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| Using `Math.pow` (Java) / `pow` (C++) which returns **double** | Precision loss for large exponents; rounding errors | Use integer multiplication (fast exponentiation) |
| Forgetting to handle the case where `n` itself is **not** constructible | Wrong final answer (missing `+1`) | After the loop, add `1` **only** if the loop never broke early |
| Assuming `n` has the same number of digits as the longest possible number | Incorrect count of shorter numbers | Explicitly pre‑add all numbers with fewer digits |
| Recursion with memoization but forgetting to use `long long` | Overflow when `digits.length` = 9 and length = 10 | Use `long long` (64‑bit) for intermediate sums |
| Off‑by‑one errors in the exponent `(k-i-1)` | Wrong power value | Verify indices carefully; unit‑test on samples |

---

## 🔄 Alternative: Digit‑DP (the “Uglier” but more general)

If you need to support *different* digit sets per position or more constraints (e.g., sum of digits, no leading zeros), the classic digit‑DP with memoization becomes handy.

```java
// Java skeleton
class Solution {
    int[] dp;            // memo table
    boolean[][] vis;
    String[] digits;
    int nDigits;

    public int atMostNGivenDigitSet(String[] digits, int n) {
        this.digits = digits;
        String s = String.valueOf(n);
        nDigits = s.length();
        dp = new int[nDigits + 1];
        vis = new boolean[nDigits + 1][1];
        Arrays.fill(dp, -1);
        return dfs(0, true);
    }

    int dfs(int pos, boolean tight) {
        if (pos == nDigits) return 1;
        if (!tight && vis[pos][0]) return dp[pos];
        int limit = tight ? (String.valueOf(n)).charAt(pos) - '0' : 9;
        int ans = 0;
        for (String d : digits) {
            int val = d.charAt(0) - '0';
            if (val > limit) break;
            ans += dfs(pos + 1, tight && val == limit);
        }
        if (!tight) { vis[pos][0] = true; dp[pos] = ans; }
        return ans;
    }
}
```

This version is more verbose but shows how the same logic generalises.

---

## 📈 SEO & Interview Take‑aways

- **Keywords**: *LeetCode 902*, *Numbers at most N given digit set*, *Java solution*, *Python solution*, *C++ solution*, *coding interview*, *digit DP*, *job interview help*.
- **Meta Description** (for a blog post):  
  “Master LeetCode 902 – Numbers At Most N Given Digit Set. See Java, Python, and C++ solutions, detailed explanations, and common pitfalls to avoid. Boost your coding interview performance!”
- **Call‑to‑Action**: “Like, share, and subscribe for more interview‑ready tutorials.”

**Interview Tip**:  
- Start with the high‑level idea (counting with fewer digits).  
- Clarify assumptions about the input (`n`’s length).  
- Show the combinatorial counting before coding.  
- Discuss edge cases (“does `n` itself count?”).  
- Mention the *O(log n)* solution as a clean, production‑ready answer.

---

## 🎉 Final Checklist

- [ ] Implement combinatorial counting with integer powers.  
- [ ] Pre‑add all shorter‑length numbers.  
- [ ] Handle prefix mismatch correctly.  
- [ ] Add `+1` only when `n` itself is constructible.  
- [ ] Use 64‑bit integers to avoid overflow.  
- [ ] Run unit tests on sample inputs (`[1,3,5,7]`, `100`) and edge cases (`["1","2","3"]`, `1234`).

Happy coding and good luck in your next interview! 🚀

--- 

**Reference**:  
- LeetCode problem statement: https://leetcode.com/problems/numbers-at-most-n-given-digit-set/  
- Classic digit‑DP resource: https://cp-algorithms.com/algebra/digit-dp.html

--- 

*Prepared by: ChatGPT – Your AI coding interview coach.*