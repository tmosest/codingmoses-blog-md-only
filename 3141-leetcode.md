---
title: LeetCode 3141. Maximum Hamming Distances - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3141 – Maximum Hamming Distances  
**Java | Python | C++ – O(m · 2ᵐ) DP solution**  

> **SEO tags** – LeetCode 3141, Maximum Hamming Distances, Java interview solution, Python interview solution, C++ interview solution, dynamic programming bitmask, software engineering interview, coding interview problem

---

## 1. Problem Statement

> **Given** an integer array `nums` (length *n*, 2 ≤ *n* ≤ 2ᵐ) and an integer `m` (1 ≤ *m* ≤ 17)  
> **Return** an array `answer` of the same length where  
> `answer[i] = max_{j≠i} HammingDistance(nums[i], nums[j])`.  

The Hamming distance between two integers is the number of bit positions at which their binary representations differ (leading zeros are allowed to pad the strings to *m* bits).

---

## 2. Naïve Ideas & Their Pitfalls

| Approach | Time | Space | Comments |
|----------|------|-------|----------|
| Brute force pairwise Hamming distance | O(n² · m) | O(1) | n can be 2ᵐ ≈ 131 072 → ~10¹⁰ comparisons → impossible. |
| Pre‑compute all distances in a 2ᵐ × 2ᵐ matrix | O((2ᵐ)²) | O((2ᵐ)²) | 2ᵐ² ≈ 10¹⁰ memory → impossible. |
| BFS from each element over the hyper‑cube | O(n · 2ᵐ) | O(2ᵐ) | Works for very small *m*, but still too slow for *m*=17. |

We need a *linear* algorithm in terms of the hyper‑cube size, i.e. O(m · 2ᵐ).

---

## 3. The Insight – “Propagate” the maximum distance

Every integer *x* (0 ≤ *x* < 2ᵐ) can be thought of as a node in an *m*‑dimensional hyper‑cube.  
Two nodes are neighbours if they differ in exactly one bit.

Let `dp[x]` be the *maximum* Hamming distance from *x* to any element that appears in `nums`.  
If we know `dp[y]` for a neighbour `y = x ^ (1 << k)` (bit *k* flipped), we can update `dp[x]`:

```
dp[x] = max( dp[x] , dp[y] + 1 )
```

The `+1` comes from the extra differing bit between `x` and `y`.  
Doing this once for every bit is enough: after the first pass `dp` knows the distance to the nearest element in the same “half” of the cube; after the second pass it knows the distance to the nearest element in the other half, and so on.  
After processing all `m` bits, `dp[x]` contains the maximum distance to **any** element in `nums`.

The algorithm is essentially a multi‑stage dynamic programming (or “Fast Walsh–Hadamard transform” style) over the hyper‑cube.

---

## 4. Algorithm (pseudo code)

```
size = 1 << m
dp = array of size filled with -INF
for each v in nums:
    dp[v] = 0          // distance to itself is 0

for bit from 0 to m-1:
    prev = copy of dp  // snapshot before this level
    for x from 0 to size-1:
        neighbour = x ^ (1 << bit)
        dp[x] = max( dp[x], prev[neighbour] + 1 )

answer = [ dp[v] for v in nums ]
return answer
```

**Complexities**

* Time: `O(m · 2ᵐ)` – at most `17 · 131072 ≈ 2.2 million` operations.  
* Space: `O(2ᵐ)` – the DP array plus a snapshot.

Both are easily fast enough for the constraints.

---

## 5. Code

### 5.1 Java (Java 17)

```java
import java.util.*;

public class Solution {
    public int[] maxHammingDistances(int[] nums, int m) {
        int size = 1 << m;                 // 2^m
        int[] dp = new int[size];
        Arrays.fill(dp, Integer.MIN_VALUE / 2); // avoid overflow

        // base case: distance to itself is 0
        for (int v : nums) dp[v] = 0;

        // DP over bits
        for (int bit = 0; bit < m; bit++) {
            int[] prev = dp.clone();       // snapshot before this level
            int mask = 1 << bit;
            for (int x = 0; x < size; x++) {
                int neighbour = x ^ mask;
                dp[x] = Math.max(dp[x], prev[neighbour] + 1);
            }
        }

        // build answer
        int[] res = new int[nums.length];
        for (int i = 0; i < nums.length; i++) res[i] = dp[nums[i]];
        return res;
    }

    /* -------------------------------------------------------------
       Quick test harness – not part of LeetCode submission
    ------------------------------------------------------------- */
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(Arrays.toString(
            s.maxHammingDistances(new int[]{9, 12, 9, 11}, 4))); // [2,3,2,3]
    }
}
```

### 5.2 Python (Python 3.11)

```python
from typing import List
import math

class Solution:
    def maxHammingDistances(self, nums: List[int], m: int) -> List[int]:
        size = 1 << m
        dp = [-math.inf] * size

        # base case
        for v in nums:
            dp[v] = 0

        # DP over bits
        for bit in range(m):
            prev = dp[:]          # snapshot
            mask = 1 << bit
            for x in range(size):
                neighbour = x ^ mask
                val = prev[neighbour] + 1
                if val > dp[x]:
                    dp[x] = val

        return [dp[v] for v in nums]


# quick demo
if __name__ == "__main__":
    s = Solution()
    print(s.maxHammingDistances([9, 12, 9, 11], 4))   # [2, 3, 2, 3]
```

### 5.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maxHammingDistances(vector<int>& nums, int m) {
        int size = 1 << m;
        vector<int> dp(size, INT_MIN / 2); // avoid overflow

        // base case
        for (int v : nums) dp[v] = 0;

        // DP over bits
        for (int bit = 0; bit < m; ++bit) {
            vector<int> prev = dp;          // snapshot
            int mask = 1 << bit;
            for (int x = 0; x < size; ++x) {
                int neighbour = x ^ mask;
                dp[x] = max(dp[x], prev[neighbour] + 1);
            }
        }

        vector<int> res;
        res.reserve(nums.size());
        for (int v : nums) res.push_back(dp[v]);
        return res;
    }
};
```

---

## 6. “Good, Bad, Ugly” of the Solution

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | Linear in the hyper‑cube size (m · 2ᵐ). | None – the algorithm is optimal for the constraints. | None. |
| **Space Complexity** | O(2ᵐ) – perfectly acceptable for *m* ≤ 17. | None. | None. |
| **Readability** | Clear DP loop, uses bitwise XOR to flip a bit. | Requires understanding of hyper‑cube propagation. | Snapshot copying (`prev = dp.clone()`) can look heavy but is trivial. |
| **Edge Cases** | Handles duplicates, single‑bit arrays, maximum size. | None. | None. |
| **Potential Pitfalls** | Using `Integer.MIN_VALUE/2` to avoid overflow when adding 1. | Forgetting to reset `dp[v] = 0` for every occurrence of a number. | None. |

---

## 7. Why This Shows You’re Ready for a Coding Interview

* **Bit‑wise thinking** – LeetCode problems often hinge on manipulating individual bits; this solution showcases that skill.  
* **Dynamic programming on combinatorial structures** – Propagating information across a hyper‑cube is a non‑trivial DP pattern.  
* **Space‑time trade‑offs** – The solution balances minimal memory usage with clear O(m · 2ᵐ) time.  
* **Language versatility** – We’ve provided idiomatic Java, Python, and C++ implementations, demonstrating adaptability to any stack your employer uses.

If you can explain the algorithm, justify its optimality, and write clean code in any of these languages, you’re a strong candidate for a senior software‑engineering role.

---

## 8. Final Checklist Before Submitting

1. **Remove the demo `main` or `__main__` blocks** – LeetCode will only call the class method.  
2. **Avoid `-inf` in Java** – we used `Integer.MIN_VALUE / 2` for safety.  
3. **Test with all provided examples** – confirm `[2,3,2,3]` and `[1,2,1,2]` work.  
4. **Submit** – copy the relevant class into LeetCode’s editor and run the test suite.

Good luck! 🚀