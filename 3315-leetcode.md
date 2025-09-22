---
title: LeetCode 3315. Construct the Minimum Bitwise Array II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3315 – Construct the Minimum Bitwise Array II  
> **LeetCode Medium | Bit‑Manipulation | Java | Python | C++**

---

## ✅ What the problem asks

Given a list of *prime* numbers `nums`, build an array `ans` of the same length so that

```
ans[i]  OR  (ans[i] + 1)   =   nums[i]
```

and `ans[i]` must be the **smallest** non‑negative integer that satisfies the equation.  
If no such integer exists, put `-1` at that position.

> **Examples**  
> `nums = [2, 3, 5, 7] → ans = [-1, 1, 4, 3]`  
> `nums = [11, 13, 31] → ans = [9, 12, 15]`

---

## 🧩 The key observation

Look at the binary representation of a prime `p` (remember, the only power‑of‑two prime is `2`).

```
p =  5  (101b) → ans =  4  (100b)
p = 13 (1101b) → ans = 12  (1100b)
p =  7  (111b) → ans =  3  (011b)
p = 11  (1011b)→ ans =  9  (1001b)
```

What is common in all these cases?

*The answer is obtained by removing the **rightmost set bit that is part of a consecutive run of 1s**.*

Formally:

1. Count the number of trailing `1`s (`t`) in `p`.  
   (i.e. while the least‑significant bit is `1`, shift right and increment `t`.)
2. If `t == 0` (only `p == 2` has this property), answer is `-1`.  
3. Otherwise, subtract `2^(t‑1)` from `p`:

```
ans = p – 2^(t-1)
```

Why does this work?  
`ans` will have all trailing bits equal to `1` **except** the right‑most one of that run.  
When you add `1`, a carry turns that `1` back to `0` and propagates one step left, so the OR
with `ans` recreates exactly the original prime.

---

## 📦 Implementation

### 1. Java

```java
import java.util.*;

class Solution {
    public int[] minBitwiseArray(List<Integer> nums) {
        int n = nums.size();
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            int p = nums.get(i);
            if (p == 2) {               // the only prime that is a power of two
                ans[i] = -1;
                continue;
            }
            int t = 0;                  // count trailing 1s
            int tmp = p;
            while ((tmp & 1) == 1) {
                t++;
                tmp >>= 1;
            }
            ans[i] = p - (1 << (t - 1));
        }
        return ans;
    }
}
```

### 2. Python 3

```python
class Solution:
    def minBitwiseArray(self, nums: List[int]) -> List[int]:
        res = []
        for p in nums:
            if p == 2:                 # no solution for 2
                res.append(-1)
                continue
            t = 0
            tmp = p
            while tmp & 1:
                t += 1
                tmp >>= 1
            res.append(p - (1 << (t - 1)))
        return res
```

### 3. C++

```cpp
class Solution {
public:
    vector<int> minBitwiseArray(vector<int>& nums) {
        vector<int> ans;
        for (int p : nums) {
            if (p == 2) {               // prime power of two
                ans.push_back(-1);
                continue;
            }
            int t = 0;                  // trailing 1s
            int tmp = p;
            while (tmp & 1) {
                ++t;
                tmp >>= 1;
            }
            ans.push_back(p - (1 << (t - 1)));
        }
        return ans;
    }
};
```

---

## 📈 Complexity Analysis

* **Time** – `O(n · log M)`  
  `n` = length of `nums`, `M` = maximum prime value.  
  The loop that counts trailing 1s touches each bit at most once.
* **Space** – `O(1)` auxiliary (apart from the output array).

---

## 🔍 The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Intuition** | Simple binary pattern – remove a single bit | Requires noticing the pattern in primes | None – the pattern could be missed |
| **Implementation** | One pass per number, no recursion | Special‑case for `2` | Over‑complicated if one tries to use bit‑shifts in a loop |
| **Performance** | Linear in bits, well below limits | O(n log M) is fine for `n ≤ 100` | None – algorithm is optimal |
| **Edge Cases** | Handles all primes (including 2) | Requires explicit `2` check | Failing to treat `2` would break correctness |
| **Readability** | Clear variable names (`t`, `tmp`) | Could be mis‑read if bit‑ops are unfamiliar | Avoid extra variables for clarity |

---

## 📚 Why This Blog Helps Your Job Hunt

* **SEO‑friendly keywords** – *LeetCode solution*, *bitwise array*, *Java*, *Python*, *C++*, *minimum bitwise array*, *coding interview*, *algorithm analysis*.
* **Structure** – Problem statement → Insight → Code → Complexity → Discussion → SEO meta‑description.  
  Recruiters often skim to find the key parts quickly.
* **Technical depth** – Demonstrates mastery of bitwise tricks, prime number properties, and clean algorithm design.
* **Practical code** – Ready‑to‑copy snippets in three major languages; shows versatility.
* **Self‑branding** – Add a small bio and a link to your GitHub/LinkedIn at the end to turn readers into candidates.

---

## 🚀 Take‑away

* The answer always exists **except** for the prime `2`.  
* The solution is as simple as *count trailing ones, subtract `2^(t‑1)`*.  
* Implement it in one pass, and you’ve got an optimal, interview‑ready answer.

Happy coding, and may your interview be as bug‑free as this bitwise trick!