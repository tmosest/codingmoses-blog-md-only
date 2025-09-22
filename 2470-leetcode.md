---
title: LeetCode 2470. Number of Subarrays With LCM Equal to K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2470 – “Number of Subarrays With LCM = K”  
**Java | Python | C++ – Full Working Solutions + Blog Guide**  
*(SEO‑optimized, “the good, the bad, and the ugly” for your next interview)*  

---

## 1.  Problem Overview

> **Given** an integer array `nums` (1 ≤ `nums.length` ≤ 1000) and an integer `k` (1 ≤ `k` ≤ 1000).  
> **Return** the number of **contiguous subarrays** whose **least common multiple (LCM)** equals `k`.

A subarray is a contiguous non‑empty slice of `nums`.  
The LCM of an array is the smallest positive integer divisible by **every** element.

> **Example**  
> `nums = [3, 6, 2, 7, 1]`, `k = 6` → answer: **4** (the four subarrays listed in the problem statement).

---

## 2.  Key Observations

| Observation | Why it matters |
|-------------|----------------|
| `LCM(a, b)` can only **grow** or stay the same when we append more numbers | If it ever exceeds `k`, no later extensions will bring it back down → we can **break** early. |
| `nums[i] ≤ 1000` and `k ≤ 1000` | Limits the maximum useful LCM we need to consider. |
| `LCM` can be computed using `LCM(a, b) = a / gcd(a, b) * b` | `gcd` is cheap (Euclidean algorithm) and keeps intermediate values small. |
| **Time complexity**: `O(n²)` is fine for `n ≤ 1000` | 1 000 000 iterations are trivial for modern CPUs. |

---

## 3.  The “Good” – The Core Algorithm

```text
ans = 0
for i = 0 … n-1:
    cur = 1
    for j = i … n-1:
        cur = lcm(cur, nums[j])
        if cur == k: ans++
        if cur > k:   break   // no need to extend this subarray any further
return ans
```

* `cur` holds the LCM of `nums[i…j]`.  
* As soon as `cur > k` we stop extending that subarray because LCM can’t shrink.  
* `lcm` is computed via `gcd` to avoid overflow and keep it fast.

---

## 4.  “The Bad” – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Using a naive `lcm` that multiplies before dividing → overflow | Use `cur / gcd(cur, x) * x` (or `std::gcd` in C++17). |
| Forgetting to break when `cur > k` → O(n³) in worst case | Add `break` after the comparison. |
| Using `double` or floating point for LCM → precision errors | Stick to integer arithmetic only. |
| Not handling the case `k == 1` properly | The algorithm naturally counts subarrays whose LCM is exactly 1. |

---

## 5.  “The Ugly” – Edge Cases & Testing

| Edge Case | What to test |
|-----------|--------------|
| `k` not present in array | Result must be 0. |
| All elements are 1 | Every subarray has LCM = 1. |
| `k` equals the product of several elements | Ensure the algorithm doesn’t miss multi‑element subarrays. |
| Large `k` close to 1000 | Verify that the `break` logic works correctly. |

Always run the following test harness:

```python
def brute(nums, k):
    import math
    ans = 0
    for i in range(len(nums)):
        for j in range(i, len(nums)):
            cur = 1
            for x in nums[i:j+1]:
                cur = cur * x // math.gcd(cur, x)
            if cur == k:
                ans += 1
    return ans
```

Cross‑check the output of your implementation against this brute force for random inputs.

---

## 6.  Full Code Implementations

### 6.1 Java (LeetCode Style)

```java
class Solution {
    public int subarrayLCM(int[] nums, int k) {
        int count = 0;
        for (int i = 0; i < nums.length; i++) {
            int curLcm = 1;
            for (int j = i; j < nums.length; j++) {
                curLcm = lcm(curLcm, nums[j]);
                if (curLcm == k) count++;
                if (curLcm > k) break;          // important optimization
            }
        }
        return count;
    }

    private int lcm(int a, int b) {
        return a / gcd(a, b) * b;
    }

    private int gcd(int a, int b) {
        while (b != 0) {
            int t = a % b;
            a = b;
            b = t;
        }
        return a;
    }
}
```

### 6.2 Python 3

```python
from math import gcd

class Solution:
    def subarrayLCM(self, nums: list[int], k: int) -> int:
        count = 0
        n = len(nums)
        for i in range(n):
            cur = 1
            for j in range(i, n):
                cur = cur * nums[j] // gcd(cur, nums[j])
                if cur == k:
                    count += 1
                if cur > k:
                    break
        return count
```

### 6.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int subarrayLCM(vector<int>& nums, int k) {
        int count = 0;
        for (int i = 0; i < nums.size(); ++i) {
            long long cur = 1;
            for (int j = i; j < nums.size(); ++j) {
                cur = cur / std::gcd(cur, (long long)nums[j]) * nums[j];
                if (cur == k) ++count;
                if (cur > k) break;          // prune the search
            }
        }
        return count;
    }
};
```

> **Note**: `long long` keeps the intermediate product safe, even though we break when `cur > k`.

---

## 7.  Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Brute force (nested loops + `lcm`) | **O(n²)** (≈ 1 000 000 ops for `n=1000`) | **O(1)** |
| Using `break` on `cur > k` | Same worst‑case, often faster in practice | **O(1)** |

With `n = 1000`, this runs in under a millisecond in all languages.

---

## 8.  Why This Blog Helps Your Career

1. **Interview Mastery** – The LCM subarray problem is a *classic* LeetCode medium that demonstrates your grasp of number theory, two‑pointer/sliding window style reasoning, and optimization tricks.
2. **Multilingual Show‑case** – Having ready‑to‑copy solutions in Java, Python, and C++ proves you’re a versatile engineer comfortable across the stack.
3. **SEO‑Friendly Content** – By sprinkling keywords like “LeetCode 2470”, “Number of Subarrays With LCM Equal to K”, “LCM subarray problem”, and “coding interview”, recruiters crawling for problem‑solving experience will spot your post.
4. **Structured Insight** – Explaining “good, bad, ugly” demonstrates reflective coding skills, something interviewers value when assessing seniority.

---

## 9.  Final Checklist Before You Submit

- [ ] **Run all test cases** (provided, edge, and random).
- [ ] **Comment** the key parts (`lcm`, `break`).
- [ ] **Double‑check overflow**: use `long long`/`int64_t` in C++, `long` in Java if you wish to be extra safe.
- [ ] **Time your solution** on a local machine – should be < 5 ms for `n=1000`.
- [ ] **Push to GitHub** and add a clear README with a short explanation – recruiters love open‑source samples.

---

## 10.  TL;DR

> **Algorithm**: Double loop, compute running LCM, break when it exceeds `k`.  
> **Complexity**: `O(n²)` time, `O(1)` space.  
> **Result**: Count of subarrays whose LCM is exactly `k`.  
> **Languages**: Java, Python, C++ ready to copy.  

Happy coding, and may those interviewers love your LCM‑savvy solutions! 🚀

---