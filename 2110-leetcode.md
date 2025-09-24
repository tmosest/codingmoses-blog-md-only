---
title: LeetCode 2110. Number of Smooth Descent Periods of a Stock - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Problem Overview – LeetCode 2110  
**Number of Smooth Descent Periods of a Stock**  
- **Difficulty:** Medium  
- **Key idea:** Count every contiguous sub‑array whose consecutive elements drop by **exactly 1**.  
- **Input:** `int[] prices` – daily stock prices (1 ≤ n ≤ 10⁵).  
- **Output:** `long` – the total number of smooth descent periods.

> A period of length 1 is always valid; longer periods must satisfy  
> `prices[i‑1] - prices[i] == 1` for all `i` inside the period.

---

## 2️⃣  Why This Problem Matters  
- **Algorithmic Insight:** Recognises a classic “run” or “segment” counting problem.  
- **Interview Context:** Appears in LeetCode, and is a favorite of many hiring teams for data‑structure + greedy thinking.  
- **Real‑world Analogy:** Detecting strictly decreasing sequences in time‑series data (e.g., stock drops, temperature drops).

---

## 3️⃣  The “Good” – Simple Sliding Window / Run Length Encoding  
- **O(n) time, O(1) extra space.**  
- Keep a counter `len` for the current consecutive “drop‑by‑1” run.  
- When the run breaks, add the number of sub‑arrays contributed by that run:  
  \[
  \text{add} = \frac{\text{len} \times (\text{len}+1)}{2}
  \]
- After the loop, add the last run’s contribution.

### Why this works  
Every sub‑array that lies entirely inside a run of length `len` is a valid descent period.  
The formula above counts all **contiguous** sub‑arrays of a sequence of length `len` – exactly what we need.

---

## 4️⃣  The “Bad” – Brute Force O(n²)  
Loop over all start indices, extend until the rule breaks, and count each valid sub‑array.  
- Time: \(O(n^2)\) – unacceptable for \(n = 10^5\).  
- Space: \(O(1)\) – but the time cost kills it.

---

## 5️⃣  The “Ugly” – DP / Segment Trees  
Some solutions try dynamic programming or segment trees, adding unnecessary complexity.  
- **Over‑engineering:** DP would track longest run ending at each position, but the sliding window already does this in constant space.  
- **Hard to read:** More lines of code, higher chance of bugs.  
- **Performance:** Still \(O(n)\) but with heavier constant factors.

---

## 6️⃣  The Clean Solution (Sliding Window)  
Below you’ll find fully‑commented, production‑ready implementations in **Java, Python, and C++**. All use the same idea described above.

| Language | Function | Return Type | Notes |
|----------|----------|-------------|-------|
| Java | `public long getDescentPeriods(int[] prices)` | `long` | Uses `long` to avoid overflow |
| Python | `def get_descent_periods(prices: List[int]) -> int:` | `int` | Python’s int is unbounded |
| C++ | `long long getDescentPeriods(vector<int>& prices)` | `long long` | 64‑bit signed |

---

### 6.1 Java

```java
import java.util.*;

public class Solution {
    /**
     * Count smooth descent periods in an array of stock prices.
     *
     * @param prices array of daily prices
     * @return number of smooth descent periods (long to avoid overflow)
     */
    public long getDescentPeriods(int[] prices) {
        int n = prices.length;
        if (n == 0) return 0;          // defensive (problem guarantees n >= 1)

        long total = 0;
        int runLen = 1;                 // current length of consecutive drop-by-1

        for (int i = 1; i < n; i++) {
            if (prices[i - 1] - prices[i] == 1) {
                runLen++;               // continue the run
            } else {
                total += (long) runLen * (runLen + 1) / 2; // add contribution
                runLen = 1;             // reset for next run
            }
        }

        // add the last run
        total += (long) runLen * (runLen + 1) / 2;
        return total;
    }

    // ----- Optional main for quick manual testing -----
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.getDescentPeriods(new int[]{3, 2, 1, 4})); // 7
        System.out.println(sol.getDescentPeriods(new int[]{8, 6, 7, 7})); // 4
        System.out.println(sol.getDescentPeriods(new int[]{1}));          // 1
    }
}
```

---

### 6.2 Python

```python
from typing import List

def get_descent_periods(prices: List[int]) -> int:
    """
    Count smooth descent periods in a list of prices.
    
    Parameters
    ----------
    prices : List[int]
        Daily stock prices, 1 <= len(prices) <= 1e5
    
    Returns
    -------
    int
        Total number of smooth descent periods.
    """
    n = len(prices)
    if n == 0:
        return 0

    total = 0
    run_len = 1          # current run length

    for i in range(1, n):
        if prices[i - 1] - prices[i] == 1:
            run_len += 1
        else:
            total += run_len * (run_len + 1) // 2
            run_len = 1

    total += run_len * (run_len + 1) // 2
    return total

# ---- Optional quick test ----
if __name__ == "__main__":
    print(get_descent_periods([3, 2, 1, 4]))  # 7
    print(get_descent_periods([8, 6, 7, 7]))  # 4
    print(get_descent_periods([1]))          # 1
```

---

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    /**
     * @param prices vector of daily stock prices
     * @return number of smooth descent periods as long long
     */
    long long getDescentPeriods(vector<int>& prices) {
        int n = prices.size();
        if (n == 0) return 0;          // safety guard

        long long total = 0;
        int runLen = 1;                // current run length

        for (int i = 1; i < n; ++i) {
            if (prices[i-1] - prices[i] == 1) {
                ++runLen;              // extend run
            } else {
                total += 1LL * runLen * (runLen + 1) / 2; // add contribution
                runLen = 1;              // start new run
            }
        }

        total += 1LL * runLen * (runLen + 1) / 2; // last run
        return total;
    }
};

// ----- Optional main for quick testing -----
int main() {
    Solution sol;
    cout << sol.getDescentPeriods({3,2,1,4}) << endl; // 7
    cout << sol.getDescentPeriods({8,6,7,7}) << endl; // 4
    cout << sol.getDescentPeriods({1}) << endl;       // 1
    return 0;
}
```

---

## 7️⃣  Blog Article – “The Good, The Bad, and The Ugly of LeetCode 2110”

### Title
**LeetCode 2110 – Number of Smooth Descent Periods of a Stock: The Good, The Bad & The Ugly**

> *SEO Tags:*  
> `Number of Smooth Descent Periods of a Stock`, `LeetCode 2110`, `smooth descent periods`, `stock price algorithm`, `DP vs Sliding Window`, `Java Python C++ solutions`, `data‑structure interview questions`.

---

### 7.1 Introduction  

If you’ve been practicing on LeetCode, you’ll bump into **Problem 2110 – “Number of Smooth Descent Periods of a Stock”**. At first glance it looks like a simple “count sub‑arrays” problem, but it actually teaches a neat trick that appears in many interview questions: *run‑length counting*. In this post, we’ll dissect the problem, discuss common pitfalls, and present a clean, production‑ready solution that works in Java, Python, and C++.

---

### 7.2 Problem Statement (Restated)

> You are given an array `prices` of length *n* (1 ≤ *n* ≤ 10⁵).  
> A **smooth descent period** is a contiguous sub‑array where each day’s price is **exactly 1 less** than the previous day’s price.  
> Sub‑arrays of length 1 always qualify.  
> Return the total number of smooth descent periods.

---

### 7.3 Example Walk‑through  

| Input | Output | Explanation |
|-------|--------|-------------|
| `[3, 2, 1, 4]` | `7` | 1‑day periods (4 of them) + 2‑day periods `[3,2]`, `[2,1]` + 3‑day period `[3,2,1]` |
| `[8, 6, 7, 7]` | `4` | Only single‑day periods because `[8,6]` fails the “difference = 1” rule |
| `[1]` | `1` | Trivial single element |

---

### 7.4 The Good – Sliding Window / Run‑Length Encoding

The key observation: **If you have a contiguous run of length `L` where each pair of consecutive elements differs by 1, then every sub‑array inside this run is valid**.  
- Number of sub‑arrays in a run of length `L` is \(\frac{L(L+1)}{2}\).  
- We just need to find every maximal run and add the formula result.

**Algorithm Steps**

1. Initialise `runLen = 1` and `total = 0`.  
2. Iterate from the second element to the end.  
3. If `prices[i-1] - prices[i] == 1`, increment `runLen`.  
4. Else (run broken):
   - Add `runLen * (runLen + 1) / 2` to `total`.  
   - Reset `runLen = 1`.  
5. After the loop, add the last run’s contribution.

**Why is this O(n)?**  
We only scan the array once; each operation inside the loop is O(1).

**Space?**  
Only a few integer variables – O(1).

---

### 7.5 The Bad – Brute Force O(n²)

A naive approach checks every possible sub‑array and verifies the descent rule. While it works for tiny inputs, it quickly becomes infeasible for *n* = 10⁵:

- **Time:** ~5 × 10⁹ operations → minutes or hours.  
- **Space:** Still O(1), but the sheer time cost kills it.

---

### 7.6 The Ugly – Over‑engineering (DP / Segment Trees)

Some solutions implement dynamic programming or a segment tree to keep track of the longest descent run ending at each index.  
- **Pros:** Still O(n).  
- **Cons:** Adds ~20 extra lines of code, more debugging headaches, and a larger constant factor.  
- **Result:** Harder to maintain, harder to read, and offers no advantage over the sliding window.

---

### 7.7 Final Clean Code (Java / Python / C++)

(Show the implementations listed in section 6 above.)

*Tip:* In Java and C++ use 64‑bit integers (`long` / `long long`) to guard against overflow; Python’s integers are arbitrary‑precision, so you’re safe.

---

### 7.7 What to Practice Next

Once you master Problem 2110, try these variations:

- **LeetCode 1576 – Find Min Arrow Speed to Hit Targets** (also run‑length counting).  
- **Longest Arithmetic Subsequence** (generalizes the “difference = 1” rule).  
- **Count “good sub‑arrays” where the product is divisible by a number** (DP or two‑pointer).

---

### 7.8 Take‑away Checklist

| ✅ | ✔️ | ❌ |
|----|----|----|
| Use a single scan to detect runs. | Keep `runLen` and add \(\frac{L(L+1)}{2}\) for each run. | Avoid O(n²) brute force. |
| Return `long` (Java/C++) or `int` (Python). | 64‑bit arithmetic to prevent overflow. | Over‑engineer with DP or segment trees. |

---

### 7.9 Closing

LeetCode 2110 is a micro‑lesson in *efficient counting*. The sliding window approach is elegant, fast, and easy to implement in any major language. If you’re preparing for technical interviews, mastering this pattern will save you time and impress interviewers.

> **Want more interviews tips?** Subscribe to my newsletter for weekly coding challenge walkthroughs in Java, Python, and C++.

---

### 7.10 Call to Action

> **Try the code yourself!**  
> Copy the implementation for your preferred language, run the samples, and then test on random arrays to see the performance difference.  
> Happy coding!

---

## 8️⃣  Final Words

- **LeetCode 2110** is a classic “run‑length” counting problem.  
- The **sliding window** solution is the cleanest, fastest, and most maintainable.  
- Over‑engineering with DP or trees is unnecessary; the simple math formula already gives you the answer.

If you find this article helpful, share it on LinkedIn or Twitter and tag your fellow coders. Good luck on your next interview! 🚀

--- 

*Author:* [Your Name] – Senior Software Engineer & Code Mentor.  
*Contact:* `you@example.com` | `@yourhandle` on Twitter

---

*Feel free to tweak the article’s tone or structure to suit your personal brand. The most important part: the algorithmic insights stay the same.*