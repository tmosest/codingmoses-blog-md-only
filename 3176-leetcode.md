---
title: LeetCode 3176. Find the Maximum Length of a Good Subsequence I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Finding the Maximum Length of a Good Subsequence (LeetCode 3176)

> **TL;DR** – Use a *two‑dimensional* DP that keeps the best length for every possible *number of allowed “breaks”* (`k`) and *last value* seen.  
> **Complexity** – `O(n·k)` time, `O(n·k)` space (≈ 500 × 25).  
> **Languages** – Java, Python, C++.

---

## 1. Problem Recap

You are given an integer array `nums` (size ≤ 500) and a non‑negative integer `k` (≤ 25).  
A **good** subsequence `seq` satisfies:

```
# of i in [0, |seq|-2] such that seq[i] != seq[i+1]  ≤  k
```

In other words, the subsequence can contain at most `k` “breaks” between consecutive equal elements.  
Return the *maximum possible length* of such a subsequence.

---

## 2. Why a DP on `k` and “last value” works

If we scan `nums` from left to right, at each step we must decide whether to **take** the current element into the subsequence or **skip** it.

* When we take it, two cases arise:
  * **No new break** – the previous element in the subsequence is equal to the current one.  
    → `breaks` stays the same.
  * **New break** – the previous element is different and we are allowed to use one more break (`k > 0`).  
    → `breaks` increases by one.
* When we skip it, nothing changes.

The optimal answer for a prefix depends only on:
1. How many breaks we have used so far (`i`).
2. What the **last value** in the subsequence is (`a`), because only this value decides whether a new break would be needed.

Hence we can maintain

```
dp[i][a] = maximum length of a good subsequence seen so far
           that uses exactly i breaks and ends with value a
```

During the scan we update `dp` in *reverse* order of `i` (to avoid re‑using the current element in the same iteration).

---

## 3. The DP Transition

For every element `a` in `nums`:

```
for i from k down to 0
    // Option 1: continue the current run (no new break)
    cand1 = dp[i][a] + 1

    // Option 2: start a new run with a different number
    // (only if we have a previous value and we still have breaks left)
    cand2 = (i > 0) ? res[i-1] + 1 : 0

    dp[i][a] = max(dp[i][a], cand1, cand2)
    res[i]   = max(res[i], dp[i][a])   // res[i] = best length for any last value
```

`res[i]` keeps the best length over *all* last values for a fixed `i`, which is needed for `cand2`.  
After processing the whole array, the answer is `res[k]`.

---

## 4. Code – Java

```java
import java.util.*;

class Solution {
    public int maximumLength(int[] nums, int k) {
        // dp[i] – map from last value to best length with i breaks
        @SuppressWarnings("unchecked")
        Map<Integer, Integer>[] dp = new HashMap[k + 1];
        for (int i = 0; i <= k; i++) dp[i] = new HashMap<>();

        int[] res = new int[k + 1];   // best length for each i over all last values

        for (int a : nums) {
            for (int i = k; i >= 0; i--) {
                int v = dp[i].getOrDefault(a, 0);
                // Continue run
                v = Math.max(v + 1, (i > 0 ? res[i - 1] + 1 : 0));
                dp[i].put(a, v);
                res[i] = Math.max(res[i], v);
            }
        }
        return res[k];
    }

    // Quick driver for local testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.maximumLength(new int[]{1,2,1,1,3}, 2)); // 4
        System.out.println(s.maximumLength(new int[]{1,2,3,4,5,1}, 0)); // 2
    }
}
```

---

## 5. Code – Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def maximumLength(self, nums: List[int], k: int) -> int:
        # dp[i] – dict: last value -> best length with i breaks
        dp = [defaultdict(int) for _ in range(k + 1)]
        res = [0] * (k + 1)          # best length for each i over all last values

        for a in nums:
            for i in range(k, -1, -1):
                # Option 1: continue run
                val = dp[i][a] + 1
                # Option 2: new break
                if i > 0:
                    val = max(val, res[i - 1] + 1)
                dp[i][a] = val
                res[i] = max(res[i], val)

        return res[k]

# Quick driver for local testing
if __name__ == "__main__":
    s = Solution()
    print(s.maximumLength([1, 2, 1, 1, 3], 2))   # 4
    print(s.maximumLength([1, 2, 3, 4, 5, 1], 0))  # 2
```

---

## 6. Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maximumLength(vector<int>& nums, int k) {
        // dp[i] – unordered_map: last value -> best length with i breaks
        vector<unordered_map<int,int>> dp(k + 1);
        vector<int> res(k + 1, 0);            // best length for each i over all last values

        for (int a : nums) {
            for (int i = k; i >= 0; --i) {
                int val = dp[i][a] + 1;      // continue run
                if (i > 0) val = max(val, res[i - 1] + 1); // new break
                dp[i][a] = val;
                res[i] = max(res[i], val);
            }
        }
        return res[k];
    }
};

// Quick driver for local testing
int main() {
    Solution s;
    cout << s.maximumLength({1, 2, 1, 1, 3}, 2) << endl; // 4
    cout << s.maximumLength({1, 2, 3, 4, 5, 1}, 0) << endl; // 2
    return 0;
}
```

---

## 7. The Good, The Bad, and The Ugly

| Aspect | What Worked | Why It Matters |
|--------|-------------|----------------|
| **Good – Intuitive DP** | The state “`(breaks used, last value)`” captures exactly what we need. | Keeps the solution both simple and efficient (`O(nk)`). |
| **Good – Space‑saving** | Using `unordered_map`/`defaultdict` instead of a full `n × k` table reduces memory to at most `k × distinct(values)` (≤ 25 × 500). | Handles the 10^9 value range without huge hash tables. |
| **Bad – Edge Cases** | When `k = 0`, the transition must skip `res[-1]`. The code explicitly handles this. | A common off‑by‑one bug. |
| **Bad – Reverse Loop** | Updating `dp[i]` in *descending* order of `i` is mandatory. A forward loop breaks correctness. | Avoids using the same element twice in one pass. |
| **Ugly – Large `k`** | If `k` were larger (e.g., 500), the algorithm would degrade to `O(n^2)` or use too much memory. | The problem bounds purposely keep `k ≤ 25`. |
| **Ugly – Implementation Detail** | Some languages (e.g., Java) require type erasure tricks for generic arrays. | Hidden boilerplate can distract from the algorithmic core. |

---

## 8. SEO‑Optimized Summary for Job Interviews

- **Keywords**: LeetCode, Good Subsequence, DP, Dynamic Programming, Interview, Coding Interview, Algorithm, Python, Java, C++  
- **Meta Description**: “Master LeetCode 3176 – Maximum Length of a Good Subsequence. Learn an O(n·k) DP solution in Java, Python, and C++. Perfect for your next software engineering interview.”  
- **H1**: “Dynamic Programming – LeetCode 3176 Good Subsequence”  
- **Call‑to‑Action**: “Want to score higher in coding interviews? Try implementing this efficient DP in your favourite language today!”

---

## 9. Final Thoughts

LeetCode 3176 is a great playground to showcase:

1. **Problem Decomposition** – picking the right DP state.  
2. **Handling Large Ranges** – with hash maps instead of dense arrays.  
3. **Language‑specific Idioms** – efficient map usage in Java, Python, C++.

If you can explain the DP in **under 2 minutes** and hand‑out the code above in a job interview, you’ll demonstrate both *algorithmic insight* and *clean implementation*—exactly what interviewers look for. Happy coding!