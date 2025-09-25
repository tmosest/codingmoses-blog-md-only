---
title: LeetCode 1590. Make Sum Divisible by P - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  LeetCode 1590 – “Make Sum Divisible by P”  
> **Goal** – Remove the *smallest* contiguous sub‑array so that the sum of the remaining numbers is divisible by `p`.  
> **Constraint** – The whole array cannot be removed.

---

## 🔍 Problem Recap (English + Chinese)

**English**  
```
Input:  nums = [3,1,4,2], p = 6
Output: 1   (remove [4])
```

**Chinese**  
> 你需要删除最短的连续子数组（可以为空），使剩余元素之和能够被 `p` 整除。  
> **不允许删除整数组**。

---

## ✅ Solution Overview

1. **Compute the total sum** `S`.  
   *If* `S % p == 0`, we’re already good → return `0`.  
2. **Goal of the sub‑array**  
   The sub‑array we remove must have a sum whose remainder modulo `p` equals  
   `rem = S % p`.  
3. **Prefix sums + HashMap**  
   * Iterate once through the array keeping a running prefix sum modulo `p`.  
   * Store the *latest* index at which each modulo value appears in a hash map.  
   * For each index `i` with current prefix `cur`, we want a previous prefix
     `prev` such that  
     `cur – prev ≡ rem (mod p)` → `prev ≡ (cur – rem) mod p`.  
     If such a `prev` exists, the sub‑array `(prev+1 … i)` is a candidate.  
4. **Track the minimal length**.  
5. **If no candidate** → return `-1`.  
6. **Important** – do **not** allow the candidate length to be the whole array.

This yields **O(n)** time and **O(n)** auxiliary space.  
All calculations use 64‑bit integers to avoid overflow.

---

## 🧑‍💻 Code Implementations

Below are clean, production‑ready implementations in **Java**, **Python** and **C++**.  
Each file is self‑contained and follows the same logic.

### 1. Java 17

```java
import java.util.HashMap;

public class Solution {
    /**
     * Find the length of the smallest subarray to remove
     * so that the sum of the remaining elements is divisible by p.
     *
     * @param nums array of positive integers
     * @param p    divisor
     * @return minimal subarray length or -1 if impossible
     */
    public int minSubarray(int[] nums, int p) {
        long total = 0;
        for (int x : nums) total += x;

        int rem = (int) (total % p);
        if (rem == 0) return 0;               // already divisible

        HashMap<Integer, Integer> pref = new HashMap<>();
        pref.put(0, -1);                      // handle prefix that starts at 0
        long prefix = 0;
        int best = nums.length;               // initialise with worst case

        for (int i = 0; i < nums.length; i++) {
            prefix += nums[i];
            int cur = (int) (prefix % p);
            int need = (cur - rem + p) % p;   // modulo adjustment

            Integer prevIdx = pref.get(need);
            if (prevIdx != null) {
                best = Math.min(best, i - prevIdx);
            }
            pref.put(cur, i);                 // keep the latest index
        }

        return best == nums.length ? -1 : best;
    }
}
```

### 2. Python 3

```python
from typing import List

class Solution:
    def minSubarray(self, nums: List[int], p: int) -> int:
        total = sum(nums)
        rem = total % p
        if rem == 0:
            return 0

        pref = {0: -1}                # prefix_mod -> last_index
        prefix = 0
        best = len(nums)

        for i, v in enumerate(nums):
            prefix += v
            cur = prefix % p
            need = (cur - rem) % p
            if need in pref:
                best = min(best, i - pref[need])
            pref[cur] = i

        return -1 if best == len(nums) else best
```

### 3. C++17

```cpp
#include <vector>
#include <unordered_map>
#include <algorithm>

class Solution {
public:
    int minSubarray(std::vector<int>& nums, int p) {
        long long total = 0;
        for (int x : nums) total += x;

        int rem = static_cast<int>(total % p);
        if (rem == 0) return 0;

        std::unordered_map<int, int> pref; // mod -> latest index
        pref[0] = -1;
        long long prefix = 0;
        int best = static_cast<int>(nums.size());

        for (int i = 0; i < (int)nums.size(); ++i) {
            prefix += nums[i];
            int cur = static_cast<int>(prefix % p);
            int need = (cur - rem + p) % p;

            auto it = pref.find(need);
            if (it != pref.end())
                best = std::min(best, i - it->second);

            pref[cur] = i; // keep the most recent position
        }

        return best == nums.size() ? -1 : best;
    }
};
```

---

## 📘 Blog Article: “The Good, The Bad, and The Ugly of LeetCode 1590”

> *“Make Sum Divisible by P” – a deceptively simple interview question that hides subtle pitfalls. Let’s break it down, explore edge cases, and learn how to nail it in your next coding interview.*

### 1️⃣ Good – What Makes This Problem Friendly

| Aspect | Why It Helps |
|--------|--------------|
| **Single Pass** | One linear scan with a hashmap gives O(n) time. |
| **Deterministic Remainder** | The target remainder is simply `total % p`. |
| **Clear Mathematical Insight** | Removing a sub‑array with remainder `rem` guarantees divisibility. |
| **Wide Test Coverage** | LeetCode’s hidden tests check edge cases: empty removal, impossible cases, large numbers. |

### 2️⃣ Bad – The Tricky Corners

| Pitfall | Why It Breaks |
|---------|---------------|
| **Integer Overflow** | `sum(nums)` can be > 2^31‑1. Use 64‑bit (`long`/`long long`). |
| **Modulo with Negative Numbers** | In Java/C++ `-1 % p` is negative. Use `(cur - rem + p) % p`. |
| **Removing the Entire Array** | The statement forbids it. A naïve “minimum of n” will incorrectly return `n` instead of `-1`. |
| **HashMap Initialization** | Forgetting to insert `0 → -1` misses candidates that start at index `0`. |
| **Using “first occurrence” only** | Storing only the first index can miss a shorter sub‑array later. Keep the *latest* occurrence for each modulo. |
| **O(n²) Brute‑Force** | Passes small tests but TLE on larger inputs. |

### 3️⃣ Ugly – When the Interviewer Tricks You

> The interviewer might ask you to adapt the solution for **dynamic updates** or to **handle multiple queries**. The underlying principle still holds, but you’ll need to:

* **Pre‑compute prefix sums** and store them in an array.  
* Use **prefix sum difference** to answer queries in O(1).  
* For updates, rebuild the hashmap or use a segment tree if many updates are required.

### 4️⃣ Interview‑Ready Checklist

- **Read the statement carefully** – note the “cannot delete the whole array” clause.  
- **Use 64‑bit arithmetic** everywhere.  
- **Normalize modulo results** to be non‑negative before looking up the hashmap.  
- **Initialize the hashmap with `{0: -1}`** – this handles sub‑arrays that start at index 0.  
- **Return `-1` if the best length equals the array size**.  
- **Explain your algorithm in plain English** before diving into code.

> **Tip:** Practice the “prefix‑sum + hashmap” pattern on other problems (e.g., *Longest Subarray with Sum Zero*, *Maximum Size Subarray Sum Equals k*, *Subarray Division Problems*). Once you master it, you’ll see that many “hard” problems boil down to a similar pattern.

### 5️⃣ SEO‑Friendly Keywords

* LeetCode 1590  
* Make Sum Divisible by P  
* Interview coding problem  
* Java solution  
* Python solution  
* C++ solution  
* Prefix sum  
* HashMap technique  
* Algorithm complexity  
* Data Structures interview  

### 6️⃣ Final Take‑Away

> **The core of this problem is a beautiful mix of arithmetic and data‑structure savvy.**  
> - *Good*: Linear time, clear target remainder.  
> - *Bad*: Watch out for overflow, negative modulo, and the “full array” restriction.  
> - *Ugly*: When you forget the hashmap or try a quadratic brute force, the test harness will immediately flag you out.

If you can articulate this reasoning during an interview and hand over one of the three clean, production‑ready code snippets above, you’ll be a strong candidate for any algorithm‑centric role. Happy coding! 🚀

--- 

**Feel free to copy the code snippets into your IDE and run the provided test cases. Good luck with your interview!**