---
title: LeetCode 3404. Count Special Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3404 – Count Special Subsequences  
*A complete, interview‑ready solution in Java, Python and C++ + a “good‑bad‑ugly” blog post*

---

## TL;DR

| Language | Time | Space | ✔︎ |
|----------|------|-------|---|
| Java | **O(n²)** | **O(n²)** | ✅ |
| Python | **O(n²)** | **O(n²)** | ✅ |
| C++ | **O(n²)** | **O(n²)** | ✅ |

> **Why this matters** – The “Count Special Subsequences” problem is a perfect interview exercise that tests your ability to think about *ratios* and *dynamic programming* over a 4‑index subsequence. Master it and you’ll feel confident tackling a wide range of LeetCode Medium problems.

---

## Problem Recap

Given an array `nums` of positive integers (size 7–1000), count the number of 4‑index subsequences `(p, q, r, s)` such that

* `p < q < r < s`
* `q - p > 1`, `r - q > 1`, `s - r > 1` (there’s at least one element between any two indices)
* `nums[p] * nums[r] == nums[q] * nums[s]`

Return the total count.

---

## High‑Level Insight

The condition `nums[p] * nums[r] == nums[q] * nums[s]` can be rewritten as a **ratio equality**:

```
nums[p] / nums[q] == nums[r] / nums[s]
```

If we fix `q` and `r`, the left side depends only on indices `< q` and the right side depends only on indices `> r`.  
We can pre‑count all possible ratios for the “right side” pairs `(r, s)` and then, for every possible `p`, add up matching ratios from the left side.

That yields an **O(n²)** solution:  
* 2 nested loops for building the right‑side map  
* 2 nested loops for scanning the left side and accumulating the answer

---

## Edge‑Case & Precision Pitfall

Using floating‑point numbers for ratios can introduce tiny rounding errors when the numerator and denominator are large.  
A robust implementation uses a **reduced fraction** (`gcd(numer, denom)`) as the key.  
(For the sake of brevity in this article, we’ll show the double‑based Java version – it passes all LeetCode tests, but if you want bullet‑proof code use the reduced‑fraction approach.)

---

## Code Walk‑through (Java)

```java
import java.util.*;

public class Solution {
    public long numberOfSubsequences(int[] nums) {
        int n = nums.length;
        long answer = 0;
        // Map<ratio, count> for pairs (r, s) where r > q + 1
        Map<Double, Long> rightRatioCount = new HashMap<>();

        // Iterate r from n-3 down to 4 (inclusive)
        for (int r = n - 3; r >= 4; r--) {
            // Build the map for all s > r + 1
            for (int s = r + 2; s < n; s++) {
                double ratio = nums[r] / (double) nums[s];
                rightRatioCount.merge(ratio, 1L, Long::sum);
            }

            int q = r - 2;
            // For every possible p < q - 1, add matching counts
            for (int p = 0; p < q - 1; p++) {
                double ratio = nums[q] / (double) nums[p];
                answer += rightRatioCount.getOrDefault(ratio, 0L);
            }
        }
        return answer;
    }
}
```

### Key Points

| Step | Purpose | Complexity |
|------|---------|------------|
| Build `rightRatioCount` | Count all valid `(r, s)` pairs for the current `q` | O(n) per `r` |
| Scan left side (`p`) | Sum matches with the right side | O(n) per `q` |
| Overall | Two nested loops over `r` and `q` | **O(n²)** time |

---

## Python Implementation

```python
from collections import defaultdict
from math import gcd

class Solution:
    def numberOfSubsequences(self, nums: list[int]) -> int:
        n = len(nums)
        ans = 0
        # Use reduced fraction as key to avoid floating errors
        right = defaultdict(int)

        for r in range(n - 3, 3, -1):
            for s in range(r + 2, n):
                g = gcd(nums[r], nums[s])
                key = (nums[r] // g, nums[s] // g)   # (num, den)
                right[key] += 1

            q = r - 2
            for p in range(q - 1):
                g = gcd(nums[q], nums[p])
                key = (nums[q] // g, nums[p] // g)
                ans += right.get(key, 0)

        return ans
```

> **Why the reduced fraction?**  
> Each ratio is uniquely represented by a pair `(num/g, den/g)` where `g = gcd(num, den)`. No floating precision loss, and look‑ups are O(1).

---

## C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long numberOfSubsequences(vector<int>& nums) {
        int n = nums.size();
        long long ans = 0;
        unordered_map<long long, long long> right;   // key = num << 32 | den

        auto makeKey = [](int num, int den) -> long long {
            long long a = num, b = den;
            return (a << 32) ^ b;                  // 32‑bit packing
        };

        for (int r = n - 3; r >= 4; --r) {
            for (int s = r + 2; s < n; ++s) {
                int g = std::gcd(nums[r], nums[s]);
                long long key = makeKey(nums[r] / g, nums[s] / g);
                ++right[key];
            }

            int q = r - 2;
            for (int p = 0; p < q - 1; ++p) {
                int g = std::gcd(nums[q], nums[p]);
                long long key = makeKey(nums[q] / g, nums[p] / g);
                auto it = right.find(key);
                if (it != right.end()) ans += it->second;
            }
        }
        return ans;
    }
};
```

> **Packing trick** – The key is a 64‑bit integer that concatenates the reduced numerator and denominator. Works because `nums[i] <= 1000` → 10 bits each, far below 32.

---

## Good, Bad, Ugly – What to Learn

| Category | ✅ What’s Great | ❌ What to Watch Out For | 😱 Ugly Traps |
|----------|----------------|--------------------------|---------------|
| **Good** | • Elegant reduction to a ratio equality<br>• O(n²) is optimal for n ≤ 1000<br>• Uses only O(n²) memory |  |  |
| **Bad** | • Floating‑point ratios may fail on edge cases (e.g., 500 / 499) | • `double` equality comparison is fragile – use gcd‑based keys |  |
| **Ugly** |  |  | • Forget the “at least one element between indices” constraint → off‑by‑one bugs<br>• Incorrect loop bounds (off by one) leading to out‑of‑range errors<br>• Using `Map<Double,Long>` in Java without proper `merge` leads to `NullPointerException` |

> **Takeaway** – Always validate the loop ranges carefully. The problem’s extra “gap” requirement means you must start `r` at `n-3`, not `n-2`, and `q` at `r-2`, etc.

---

## SEO‑Optimized Blog Headline & Meta

**Title:**  
“Master LeetCode 3404 – Count Special Subsequences (Java, Python, C++ Solutions)”

**Meta Description:**  
Learn how to solve LeetCode 3404 “Count Special Subsequences” in O(n²) time. Read the full Java, Python, and C++ implementations, understand the ratio trick, and get ready for interviews.  

**Keywords:**  
`Count Special Subsequences`, `LeetCode 3404`, `Java solution`, `Python solution`, `C++ solution`, `DP ratio`, `interview question`, `algorithm analysis`, `O(n^2)`

---

## Final Thoughts

* The ratio‑based DP is the heart of this problem.  
* The `gcd` trick removes floating precision concerns and makes your solution bullet‑proof.  
* The time complexity is **well within the LeetCode constraints**, and the space usage is modest.

**Ready for your next interview?**  
Practice this problem, implement it in your language of choice, and be prepared to explain the rationale and pitfalls during a coding interview. Good luck! 🚀