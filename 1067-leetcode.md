---
title: LeetCode 1067. Digit Count in Range - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. LeetCode 1067 – **Digit Count in Range**  
**Problem** | `public int digitsCount(int d, int low, int high)`  
**Difficulty** | Hard  

> **Goal** – For a single digit `d (0‑9)` and an inclusive range `[low, high]`, return the total number of times `d` appears in the decimal representations of every integer in that range.  
> **Constraints** – `1 ≤ low ≤ high ≤ 2·10⁸` (≈ 200 million).  

Because the input range can be huge, a naïve loop (`for (int i=low;i<=high;i++) …`) would be far too slow. The standard solution uses **digit‑DP** – a dynamic‑programming technique that processes the decimal digits of a number from most‑significant to least‑significant while keeping track of “tight” and “leading‑zero” states.

Below you’ll find working, annotated implementations in **Java, Python, and C++** that run in `O(log N)` time and `O(log N)` memory.

---

## 2. Java Implementation (Digit‑DP)

```java
/**
 * LeetCode 1067 – Digit Count in Range
 *
 * Java 17, O(log N) time, O(log N) memory
 */
class Solution {
    // Memoization table: pos x count
    private int[][] memo;
    private int target;     // digit we are counting
    private char[] digits;  // string representation of current number

    public int digitsCount(int d, int low, int high) {
        this.target = d;
        return countUpTo(high) - countUpTo(low - 1);
    }

    /** Count occurrences of target in [0, n] */
    private int countUpTo(int n) {
        if (n < 0) return 0;          // guard for low-1
        digits = Integer.toString(n).toCharArray();
        int len = digits.length;
        memo = new int[len][len];     // maximum occurrences <= len
        for (int[] row : memo) Arrays.fill(row, -1);
        return dp(0, 0, true, false);
    }

    /**
     * @param pos      current index in digits[]
     * @param cnt      number of target digits seen so far
     * @param tight    whether previous digits matched the prefix of n
     * @param started  whether we have placed a non‑leading‑zero digit yet
     * @return total target‑digit occurrences from this state onward
     */
    private int dp(int pos, int cnt, boolean tight, boolean started) {
        if (pos == digits.length) return cnt;                // end of number
        if (!tight && started && memo[pos][cnt] != -1)       // cache hit
            return memo[pos][cnt];

        int limit = tight ? digits[pos] - '0' : 9;
        int total = 0;

        // Option 1: skip this position (still leading zeros)
        if (!started) total += dp(pos + 1, cnt, false, false);

        int startDigit = started ? 0 : 1;   // no leading zero allowed after start
        for (int d = startDigit; d <= limit; d++) {
            int newCnt = cnt + (d == target ? 1 : 0);
            boolean newTight = tight && (d == limit);
            total += dp(pos + 1, newCnt, newTight, true);
        }

        if (!tight && started) memo[pos][cnt] = total;
        return total;
    }
}
```

### How it works

| Step | What happens |
|------|--------------|
| `digitsCount` | Calls helper for `high` and `low-1`, then subtracts. |
| `countUpTo` | Converts `n` to char array, prepares memo, calls `dp`. |
| `dp` | Recursively explores all possible digits at position `pos`. |
| State | `(pos, cnt, tight, started)` |
| Memoization | Stores results for *non‑tight* states to avoid recomputation. |

The algorithm visits at most `len * len` states (`len ≤ 10` for 2·10⁸), so it is practically constant time.

---

## 3. Python Implementation (One‑Line DP)

```python
# LeetCode 1067 – Digit Count in Range
# Python 3.11, O(log N) time, O(1) memory (explicitly using recursion limits)
class Solution:
    def digitsCount(self, d: int, low: int, high: int) -> int:
        return self.f(high, d) - self.f(low - 1, d)

    def f(self, n: int, target: int) -> int:
        if n < 0: return 0
        s = str(n)
        m = len(s)
        memo = [[-1] * (m + 1) for _ in range(m)]

        def dp(i: int, cnt: int, tight: bool, started: bool) -> int:
            if i == m:
                return cnt
            if not tight and started and memo[i][cnt] != -1:
                return memo[i][cnt]
            up = int(s[i]) if tight else 9
            res = 0
            if not started:
                res += dp(i + 1, cnt, False, False)  # skip digit
            for dig in range(0 if started else 1, up + 1):
                res += dp(i + 1, cnt + (dig == target), tight and dig == up, True)
            if not tight and started:
                memo[i][cnt] = res
            return res

        return dp(0, 0, True, False)
```

> **Why Python works** – Python’s recursion depth can handle 10 levels (max digits).  
> **Space** – `memo` is at most `10 × 10`.  
> **Time** – Still `O(log N)`.

---

## 4. C++ Implementation (Bottom‑Up Digit DP)

```cpp
// LeetCode 1067 – Digit Count in Range
// C++17, O(log N) time, O(log N) memory
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int digitsCount(int d, int low, int high) {
        target = d;
        return countUpTo(high) - countUpTo(low - 1);
    }

private:
    int target;
    vector<int> digits;
    vector<vector<int>> memo; // memo[pos][cnt]

    int countUpTo(int n) {
        if (n < 0) return 0;
        digits.clear();
        while (n) { digits.push_back(n % 10); n /= 10; }
        reverse(digits.begin(), digits.end()); // most significant first
        int len = digits.size();
        memo.assign(len, vector<int>(len + 1, -1));
        return dfs(0, 0, true, false);
    }

    int dfs(int pos, int cnt, bool tight, bool started) {
        if (pos == digits.size()) return cnt;
        if (!tight && started && memo[pos][cnt] != -1) return memo[pos][cnt];

        int limit = tight ? digits[pos] : 9;
        int res = 0;

        if (!started) res += dfs(pos + 1, cnt, false, false); // skip leading zero

        int startDigit = started ? 0 : 1;
        for (int dig = startDigit; dig <= limit; ++dig) {
            res += dfs(pos + 1, cnt + (dig == target), tight && dig == limit, true);
        }

        if (!tight && started) memo[pos][cnt] = res;
        return res;
    }
};
```

> **Key differences** – Uses an explicit vector for digits, bottom‑up recursion (no lambdas).  
> **Complexity** – Identical to Java/Python: `O(log N)` time, `O(log N)` memory.

---

## 5. Blog Article – “The Good, The Bad, The Ugly of Digit‑DP on LeetCode 1067”

> **Keywords**: LeetCode 1067, Digit Count in Range, digit DP, dynamic programming interview questions, job interview, software engineer, Java solution, Python solution, C++ solution, coding interview, algorithm design

### 5.1 The Problem in a Nutshell

LeetCode 1067 asks you to count how many times a single digit appears inside a huge integer interval. The input can be as large as 200 million, but you must finish in under a millisecond. At first glance, a brute‑force loop seems like the natural answer—simply iterate and convert each integer to a string, but that would require *hundreds of millions of string conversions*, impossible in an interview or a coding challenge.

This is the classic “counting digits in a range” problem. The elegant solution is *digit‑DP* – a dynamic‑programming approach that treats the problem as a traversal of the decimal digits of the boundary numbers.

### 5.2 The Good – Why Digit‑DP is the Gold Standard

| Why it shines | Explanation |
|---------------|-------------|
| **Optimal time** | Runs in `O(log N)` per query (≤ 10 digits for `2·10⁸`). |
| **Simplicity with recursion** | Each recursion step handles a single digit; the state is tiny: position, count so far, tight flag, leading‑zero flag. |
| **Reusable across problems** | Once you master digit‑DP, you can solve a host of problems: “count numbers with a given sum of digits”, “numbers divisible by K”, “count palindromes in range”, etc. |
| **Memory‑friendly** | Memoization table size is `digits × digits` (≤ 100 integers). |

> **Interview Tip** – Present the state diagram first. Show that there are only 4 logical states: `tight` (prefix equal to the boundary) and `started` (whether we have placed a non‑zero digit). This makes the recursion obvious.

### 5.3 The Bad – Common Pitfalls and How to Avoid Them

1. **Leading Zeros** – Forgetting to treat leading zeros specially causes over‑counting of digits in numbers like `0013`.  
   *Solution*: Add a `started` flag; only count `target` if `started` is true, otherwise skip.

2. **Off‑by‑One on Ranges** – Many solutions subtract `count(low-1)` but forget that `low-1` might be `0`.  
   *Solution*: Add guard `if (n < 0) return 0;` in the helper.

3. **Memoization Conditions** – Storing results for *tight* states is wrong because the upper bound changes per call.  
   *Solution*: Memo only for `!tight && started`.  

4. **Recursion Depth** – In languages with shallow recursion limits (e.g., Python 2), `sys.setrecursionlimit` may be needed.  
   *Solution*: In Python, the recursion depth is only 10 for this problem, but set limit anyway if you generalize.

5. **Wrong Digit Order** – Converting a number to a string versus building an array of digits from least‑to‑most can flip the meaning of `pos`.  
   *Solution*: Standardize on **most‑significant first**; it simplifies the `tight` flag logic.

### 5.4 The Ugly – Edge Cases That Trip Up Even Experts

| Edge case | Why it’s nasty | Fix |
|-----------|----------------|-----|
| **d = 0** | Leading zeros never counted, so 0 appears only in numbers like 10, 20… but *not* as the leading digit of 100. | Keep the `started` flag; count `target` only when `started`. |
| **Range includes 1** | `low = 1` gives `high-low+1 = 1` integer, but the helper still works because `countUpTo(0)` is 0. | No change – the `countUpTo(low-1)` guard handles it. |
| **high = 2·10⁸** | 9‑digit numbers but some languages treat 200 000 000 as 9 digits; ensure `digits` length is correct. | Convert to string or use division loop; they both yield the right length. |
| **target = 0 and number = 0** | 0 contains one zero. Some solutions erroneously skip counting because of the `started` flag. | Special case: if `n == 0 && target == 0`, return 1. |

### 5.5 SEO‑Optimized Summary for Job Seekers

> **If you’re preparing for a software engineering interview, mastering digit‑DP not only solves LeetCode 1067 in milliseconds but also demonstrates deep algorithmic thinking.**  
> **Showcasing a clean Java, Python, or C++ implementation of digit‑DP** will impress interviewers who expect you to handle large‑input problems efficiently.  
> **Beyond LeetCode, digit‑DP concepts appear in real‑world scenarios**—pricing algorithms, combinatorial counting in analytics, and even in building efficient search indexes.  

**Key takeaways for the resume:**

- *Dynamic Programming:* Proven expertise in DP patterns.  
- *Time‑Complexity Optimization:* Delivered solutions in `O(log N)`.  
- *Cross‑Language Proficiency:* Java, Python, C++ solutions with the same logic.  
- *Problem Solving:* Tackle hard interview questions like “Digit Count in Range” quickly.

Use the phrase **“Digit‑DP algorithm design”** in the skills section; recruiters often use it as a keyword filter.

---

## 6. Closing

The solutions above are ready to copy‑paste into your IDE or coding platform. When you present them in an interview, walk through the state machine, explain the handling of leading zeros, and emphasize the `tight` flag. That narrative, coupled with the elegant recursive code, will leave interviewers convinced you’re a strong candidate for any software engineering role. 🚀

--- 

*Happy coding!*