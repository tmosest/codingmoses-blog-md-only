---
title: LeetCode 1937. Maximum Number of Points with Cost - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1937. Maximum Number of Points with Cost  
**Problem** – Medium (LeetCode)  
**Languages** – Java | Python | C++  

---

## 1️⃣  What the problem asks for  

You’re given an `m × n` matrix `points`.  
* You start at row 0, score 0.  
* For each row you must pick **exactly one** column.  
* If you pick `(r , c)` you add `points[r][c]` to your score.  
* If the previous pick was in column `c₁` and the current pick is in column `c₂` you **lose**  
  `abs(c₁‑c₂)` points.  
* Find the maximum possible score after the last row.  

`m , n ≤ 10⁵` but `m·n ≤ 10⁵`, so a single row can still have up to 10⁵ columns.  

---

## 2️⃣  The Core Idea – Two‑Pass DP  

If we knew the best score that ends in column `j` on row `r‑1`, the score for column `i` on row `r` would be  

```
max over all j:   dpPrev[j] - abs(j-i)  +  points[r][i]
```

A naïve implementation would be `O(m·n²)`.  
The trick: split the absolute value into two cases.

* `j ≤ i` → `dpPrev[j] - (i-j) = (dpPrev[j] + j) - i`  
  The part inside the parentheses is a prefix maximum that can be pre‑computed left‑to‑right.

* `j ≥ i` → `dpPrev[j] - (j-i) = (dpPrev[j] - j) + i`  
  This is a suffix maximum that can be pre‑computed right‑to‑left.

So for every row we do **two linear scans** to compute the left and right helpers and then a final linear scan to update the DP.  
Complexity: `O(m·n)` time, `O(n)` additional space.

---

## 3️⃣  Implementation – 3 Languages

### 3.1  Java

```java
import java.util.*;

class Solution {
    public long maxPoints(int[][] points) {
        int m = points.length, n = points[0].length;
        long[] dp = new long[n];
        // row 0
        for (int i = 0; i < n; ++i) dp[i] = points[0][i];

        for (int r = 1; r < m; ++r) {
            long[] left = new long[n];
            long[] right = new long[n];
            long[] newDp = new long[n];

            // left-to-right
            left[0] = dp[0];
            for (int i = 1; i < n; ++i)
                left[i] = Math.max(left[i-1], dp[i] + i);

            // right-to-left
            right[n-1] = dp[n-1] - (n-1);
            for (int i = n-2; i >= 0; --i)
                right[i] = Math.max(right[i+1], dp[i] - i);

            // combine
            for (int i = 0; i < n; ++i) {
                long best = Math.max(left[i] - i, right[i] + i);
                newDp[i] = best + points[r][i];
            }
            dp = newDp;
        }

        long ans = Long.MIN_VALUE;
        for (long v : dp) ans = Math.max(ans, v);
        return ans;
    }
}
```

### 3.2  Python

```python
from typing import List

class Solution:
    def maxPoints(self, points: List[List[int]]) -> int:
        m, n = len(points), len(points[0])
        dp = points[0][:]                # row 0

        for r in range(1, m):
            left = [0] * n
            right = [0] * n
            new_dp = [0] * n

            left[0] = dp[0]
            for i in range(1, n):
                left[i] = max(left[i-1], dp[i] + i)

            right[n-1] = dp[n-1] - (n-1)
            for i in range(n-2, -1, -1):
                right[i] = max(right[i+1], dp[i] - i)

            for i in range(n):
                best = max(left[i] - i, right[i] + i)
                new_dp[i] = best + points[r][i]

            dp = new_dp

        return max(dp)
```

### 3.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxPoints(vector<vector<int>>& points) {
        int m = points.size(), n = points[0].size();
        vector<long long> dp(n);
        for (int i = 0; i < n; ++i) dp[i] = points[0][i];

        for (int r = 1; r < m; ++r) {
            vector<long long> left(n), right(n), newDp(n);

            left[0] = dp[0];
            for (int i = 1; i < n; ++i)
                left[i] = max(left[i-1], dp[i] + i);

            right[n-1] = dp[n-1] - (n-1);
            for (int i = n-2; i >= 0; --i)
                right[i] = max(right[i+1], dp[i] - i);

            for (int i = 0; i < n; ++i) {
                long long best = max(left[i] - i, right[i] + i);
                newDp[i] = best + points[r][i];
            }
            dp.swap(newDp);
        }

        return *max_element(dp.begin(), dp.end());
    }
};
```

All three solutions run in `O(m·n)` time and `O(n)` space, and they correctly handle the large values (`long`/`long long` in Java/C++).

---

## 4️⃣  Good, Bad & Ugly – A Quick Takeaway

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Approach** | Linear DP with two passes – elegant, optimal | Needs careful handling of `long` to avoid overflow | Forgetting the `-i` / `+i` adjustments leads to wrong results |
| **Complexity** | `O(m·n)` time, `O(n)` memory – passes constraints | None | Naïve `O(m·n²)` recursion / DP leads to TLE |
| **Implementation** | Clear separation of left/right passes | Off‑by‑one errors common in indices | Mixing `int` and `long` leads to subtle bugs |
| **Edge Cases** | Works for single‑row / single‑column | Must handle negative differences correctly | Ignoring the penalty when `c1 == c2` (zero cost) |

---

## 5️⃣  Why This Blog Helps You Land a Job

1. **Demonstrates Clean Coding** – All three solutions follow the same clear pattern, showing you can write production‑ready code in any major language.  
2. **Shows Algorithmic Thinking** – The two‑pass DP shows you can reduce a quadratic problem to linear time, a skill interviewers love.  
3. **Highlights Attention to Detail** – Discussing pitfalls (overflow, off‑by‑ones) shows you’re careful with edge cases.  
4. **SEO‑Friendly Content** – Keywords like *LeetCode 1937*, *Maximum Number of Points with Cost*, *Dynamic Programming*, *Java Python C++ solutions* rank high for job seekers looking for interview practice.  

---

## 6️⃣  Final Words

LeetCode 1937 is a classic DP problem where the “cost” depends on column distance.  
The trick is to separate the cost into two linear passes that give you prefix/suffix maximums.  
Once you master this pattern, you can apply it to many other “row‑to‑row with distance penalty” problems, such as the *Minimum Falling Path Sum* (LeetCode 931) or similar path‑finding challenges.

Good luck on your interview journey—happy coding! 🚀