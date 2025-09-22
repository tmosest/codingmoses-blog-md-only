---
title: LeetCode 3334. Find the Maximum Factor Score of Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3334 – “Find the Maximum Factor Score of Array”  
*A LeetCode Medium problem turned into a job‑interview star.*

---

## Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Why it Matters for Interviews](#why-it-matters)  
3. [Intuition & Edge Cases](#intuition)  
4. [Brute‑Force vs. Optimal Approach](#approaches)  
   * 4.1. Brute‑Force (O(n²))  
   * 4.2. Prefix/Suffix GCD & LCM (O(n))  
5. [Time & Space Complexity](#complexity)  
6. [Implementation in Three Languages](#implementations)  
   * Java  
   * Python  
   * C++  
7. [Testing the Solution](#testing)  
8. [Good, Bad, and Ugly of the Problem](#good-bad-ugly)  
9. [Wrap‑Up & Job‑Ready Tips](#wrap-up)  
10. [SEO & Keywords](#seo)

---

## Problem Recap <a name="problem-recap"></a>

> **Given an integer array `nums` (1 ≤ nums.length ≤ 100, 1 ≤ nums[i] ≤ 30),**  
> **remove at most one element to maximize the “factor score” of the remaining array,**  
> where  
> **factor score = GCD(all remaining elements) × LCM(all remaining elements).**  
>  
> Return that maximum factor score.  
> Note: GCD & LCM of a single element are the element itself; an empty array gives score 0.

---

## Why it Matters for Interviews <a name="why-it-matters"></a>

LeetCode 3334 is a **classic interview question** because it tests:

| Skill | Why it matters |
|-------|----------------|
| **Number theory** | Understanding GCD & LCM properties, overflow protection |
| **Dynamic programming / prefix‑suffix** | Building efficient solutions for “remove one” problems |
| **Complexity analysis** | Proving O(n) vs. O(n²) |
| **Coding style** | Clean, reusable GCD/LCM utilities |

If you nail this problem, you’ll show recruiters you can:

- Translate a mathematical definition into code  
- Optimize brute‑force to linear time  
- Handle edge cases gracefully

---

## Intuition & Edge Cases <a name="intuition"></a>

* The factor score of the entire array is our baseline.  
* Removing an element can change both the GCD and the LCM, sometimes drastically.  
* Because `nums[i] ≤ 30`, LCM never exceeds 2³⁰ (~1 B) when combined with GCD (which is at most 30), so a 64‑bit integer (`long`/`long long`) is safe.  
* Edge cases:  
  * Array length = 1 → removing nothing is optimal; score = `x²`.  
  * Array contains all 1’s → GCD = 1, LCM = 1 → score = 1.  
  * Removing any element when all numbers are equal leaves the same GCD/LCM.

---

## Brute‑Force vs. Optimal Approach <a name="approaches"></a>

### 4.1 Brute‑Force (O(n²))

Compute GCD/LCM for every subset that excludes at most one element.

```pseudo
maxScore = 0
for i in [-1 … n-1]:          // -1 means "remove nothing"
    currentGCD = 0
    currentLCM = 1
    for j in [0 … n-1]:
        if j == i: continue
        currentGCD = gcd(currentGCD, nums[j])
        currentLCM = lcm(currentLCM, nums[j])
    maxScore = max(maxScore, currentGCD * currentLCM)
```

**Pros:** Simple, no extra data structures.  
**Cons:** O(n²) time; not ideal if `n` were large.

### 4.2 Prefix / Suffix GCD & LCM (O(n))

The key observation: GCD/LCM of the array without element `i` equals the **prefix up to `i‑1` combined with the suffix after `i`**.

1. Precompute  
   * `prefGCD[k]` = GCD of `nums[0 … k]`  
   * `prefLCM[k]` = LCM of `nums[0 … k]`  
   * `sufGCD[k]`  = GCD of `nums[k … n‑1]`  
   * `sufLCM[k]`  = LCM of `nums[k … n‑1]`

2. For each index `i`:  
   * `gcdWithoutI = gcd(prefGCD[i-1], sufGCD[i+1])` (handle borders)  
   * `lcmWithoutI = lcm(prefLCM[i-1], sufLCM[i+1])`  
   * `score = gcdWithoutI * lcmWithoutI`

3. Also consider the case of “no removal” (score of the whole array).

**Why it works**  
GCD and LCM are associative and commutative:  
`gcd(a,b,c) = gcd(gcd(a,b),c)` and similarly for LCM.  
Thus the array without one element can be built by combining the two halves.

**Complexities**  
* Time: O(n)  
* Space: O(n) (four auxiliary arrays)

---

## Time & Space Complexity <a name="complexity"></a>

| Approach | Time | Space |
|----------|------|-------|
| Brute‑Force | **O(n²)** | **O(1)** |
| Prefix/Suffix | **O(n)** | **O(n)** |

Given the constraints (`n ≤ 100`), both pass comfortably.  
However, interviewers often expect the linear‑time solution.

---

## Implementation in Three Languages <a name="implementations"></a>

Below are clean, self‑contained solutions for Java, Python, and C++.

> **Tip:** The GCD function is built‑in in Java (`java.math.BigInteger.gcd`) and Python (`math.gcd`). In C++ we use `std::gcd` from `<numeric>`.

---

### 1. Java (Java 17)

```java
import java.util.*;

public class Solution {
    private static long gcd(long a, long b) {
        while (b != 0) {
            long t = a % b;
            a = b;
            b = t;
        }
        return a;
    }

    private static long lcm(long a, long b) {
        if (a == 0 || b == 0) return 0;
        return a / gcd(a, b) * b;      // divide first to avoid overflow
    }

    public long maxScore(int[] nums) {
        int n = nums.length;
        if (n == 1) return (long) nums[0] * nums[0];

        long[] prefGcd = new long[n];
        long[] prefLcm = new long[n];
        long[] sufGcd = new long[n];
        long[] sufLcm = new long[n];

        prefGcd[0] = nums[0];
        prefLcm[0] = nums[0];
        for (int i = 1; i < n; i++) {
            prefGcd[i] = gcd(prefGcd[i - 1], nums[i]);
            prefLcm[i] = lcm(prefLcm[i - 1], nums[i]);
        }

        sufGcd[n - 1] = nums[n - 1];
        sufLcm[n - 1] = nums[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            sufGcd[i] = gcd(sufGcd[i + 1], nums[i]);
            sufLcm[i] = lcm(sufLcm[i + 1], nums[i]);
        }

        long best = prefGcd[n - 1] * prefLcm[n - 1]; // no removal

        for (int i = 0; i < n; i++) {
            long g = (i == 0) ? sufGcd[1] : (i == n - 1) ? prefGcd[n - 2] : gcd(prefGcd[i - 1], sufGcd[i + 1]);
            long l = (i == 0) ? sufLcm[1] : (i == n - 1) ? prefLcm[n - 2] : lcm(prefLcm[i - 1], sufLcm[i + 1]);
            best = Math.max(best, g * l);
        }
        return best;
    }

    // ------------------- driver for quick sanity check -------------------
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.maxScore(new int[]{2,4,8,16})); // 64
        System.out.println(s.maxScore(new int[]{1,1,1}));     // 1
        System.out.println(s.maxScore(new int[]{5}));        // 25
    }
}
```

---

### 2. Python (Python 3.10)

```python
import math
from typing import List

class Solution:
    def maxScore(self, nums: List[int]) -> int:
        n = len(nums)
        if n == 1:
            return nums[0] * nums[0]

        pref_gcd = [0] * n
        pref_lcm = [0] * n
        suf_gcd = [0] * n
        suf_lcm = [0] * n

        pref_gcd[0] = pref_lcm[0] = nums[0]
        for i in range(1, n):
            pref_gcd[i] = math.gcd(pref_gcd[i - 1], nums[i])
            pref_lcm[i] = pref_lcm[i - 1] // math.gcd(pref_lcm[i - 1], nums[i]) * nums[i]

        suf_gcd[-1] = suf_lcm[-1] = nums[-1]
        for i in range(n - 2, -1, -1):
            suf_gcd[i] = math.gcd(suf_gcd[i + 1], nums[i])
            suf_lcm[i] = suf_lcm[i + 1] // math.gcd(suf_lcm[i + 1], nums[i]) * nums[i]

        best = pref_gcd[-1] * pref_lcm[-1]  # no removal

        for i in range(n):
            g = suf_gcd[1] if i == 0 else pref_gcd[n - 2] if i == n - 1 else math.gcd(pref_gcd[i - 1], suf_gcd[i + 1])
            l = suf_lcm[1] if i == 0 else pref_lcm[n - 2] if i == n - 1 else (pref_lcm[i - 1] // math.gcd(pref_lcm[i - 1], suf_lcm[i + 1]) * suf_lcm[i + 1])
            best = max(best, g * l)

        return best

# quick manual tests
if __name__ == "__main__":
    s = Solution()
    print(s.maxScore([2,4,8,16]))   # 64
    print(s.maxScore([1,1,1]))      # 1
    print(s.maxScore([5]))          # 25
```

---

### 3. C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long gcdll(long long a, long long b) {
        while (b) { long long t = a % b; a = b; b = t; }
        return a;
    }

    long long lcmll(long long a, long long b) {
        if (a == 0 || b == 0) return 0;
        return a / gcdll(a, b) * b;           // divide first
    }

    long long maxScore(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) return 1LL * nums[0] * nums[0];

        vector<long long> prefGcd(n), prefLcm(n), sufGcd(n), sufLcm(n);

        prefGcd[0] = prefLcm[0] = nums[0];
        for (int i = 1; i < n; ++i) {
            prefGcd[i] = gcdll(prefGcd[i-1], nums[i]);
            prefLcm[i] = lcmll(prefLcm[i-1], nums[i]);
        }

        sufGcd[n-1] = sufLcm[n-1] = nums[n-1];
        for (int i = n-2; i >= 0; --i) {
            sufGcd[i] = gcdll(sufGcd[i+1], nums[i]);
            sufLcm[i] = lcmll(sufLcm[i+1], nums[i]);
        }

        long long best = prefGcd.back() * prefLcm.back();   // no removal

        for (int i = 0; i < n; ++i) {
            long long g = (i == 0) ? sufGcd[1] : (i == n-1) ? prefGcd[n-2] : gcdll(prefGcd[i-1], sufGcd[i+1]);
            long long l = (i == 0) ? sufLcm[1] : (i == n-1) ? prefLcm[n-2] : lcmll(prefLcm[i-1], sufLcm[i+1]);
            best = max(best, g * l);
        }
        return best;
    }
};
```

---

## Testing the Solution <a name="testing"></a>

```bash
# Java
javac Solution.java
java Solution
# Python
python3 solution.py
# C++
g++ -std=c++17 -O2 solution.cpp -o sol
./sol
```

Sample inputs (you can copy‑paste into a test harness):

| Input | Expected Output |
|-------|-----------------|
| `[2,4,8,16]` | `64` |
| `[1,1,1]` | `1` |
| `[5]` | `25` |
| `[4,4,4]` | `16` |
| `[3,6,9]` | `18` (remove 9 → GCD = 3, LCM = 6, score = 18) |

---

## Good, Bad, and Ugly of the Problem <a name="good-bad-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Mathematical clarity** | The definition is *clean* and self‑explanatory. | GCD/LCM can be confusing for non‑math heavy candidates. | Over‑engineering: trying to pre‑compute every possible sub‑array. |
| **Optimization path** | Straight‑forward prefix/suffix trick. | Many interviewers expect you to “see the association” quickly. | Misunderstanding that LCM is *not* linearizable can lead to O(n²) acceptance. |
| **Overflow risk** | 64‑bit integers are safe here. | Some solutions forget to divide before multiplying in LCM. | Forgetting that `a*b` can overflow *even for small a,b* in languages without built‑in 128‑bit support. |
| **Testing** | Simple unit tests suffice. | Edge cases with single element or all ones often get missed. | Missing “no removal” case leads to wrong answer for `[1]` or `[30]`. |

---

## Wrap‑Up & Job‑Ready Tips <a name="wrap-up"></a>

1. **Start with the brute‑force sketch** – it clarifies the problem space.  
2. **Think of “remove one” as “prefix + suffix”** – a pattern that shows up in many array problems (e.g., “Maximum Subarray After Deleting One Element”).  
3. **Write reusable GCD/LCM helpers**; use built‑in functions where possible.  
4. **Test on edge cases** (length = 1, all 1’s, all same, mixed small/large).  
5. **Explain your intuition aloud** during an interview; recruiters care about your thought process as much as the final code.  

> *If you can implement 3334 in under 30 seconds, you’re likely ready to tackle other LeetCode Mediums (e.g., 1646, 1461, 1519) with confidence.*

---

## SEO & Keywords <a name="seo"></a>

- **LeetCode 3334**  
- Find the Maximum Factor Score of Array  
- GCD LCM array removal problem  
- Linear‑time array manipulation  
- Number theory interview problem  
- Java/Python/C++ LeetCode solutions  
- Interview preparation math problems  
- Job interview algorithm questions  

Add the above tags to your blog, GitHub repo, or portfolio to boost visibility among recruiters searching for “LeetCode Medium,” “Number theory,” or “Array manipulation” challenges.  

Happy coding – and good luck on your next interview! 🚀