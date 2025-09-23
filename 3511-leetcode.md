---
title: LeetCode 3511. Make a Positive Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📈  Make a Positive Array – 3511  
**LeetCode – Medium – O(n) Greedy + Prefix Sum**  
*You’re allowed to replace any element with any integer between \(-10^{18}\) and \(10^{18}\).  
Find the minimum number of replacements needed to make the array “positive”.*  

---

### 1️⃣ Problem Recap

> An array is **positive** if *every* sub‑array of length **≥ 3** has a **positive** sum.  
>  
> You can perform the following operation any number of times:  
> Replace one element in `nums` with any integer in the range \([-10^{18},10^{18}]\).  
>  
> **Goal:** Return the minimum number of operations needed.

| Example | Input | Output | Why it works |
|---------|-------|--------|--------------|
| 1 | `[-10, 15, -12]` | `1` | Replacing `-10` by `0` makes the whole array sum `3`. |
| 2 | `[-1,-2,3,-1,2,6]` | `1` | Replacing `-2` by `1` fixes all 3‑length sub‑arrays that were non‑positive. |
| 3 | `[1,2,3]` | `0` | Already positive. |

> **Constraints**  
> \(3 \le n \le 10^5\)  
> \(-10^9 \le nums[i] \le 10^9\)

---

### 2️⃣ Key Insight

Every sub‑array of length **≥ 3** contains a sub‑array of length **exactly 3**.  
If *every* length‑3 sub‑array has a positive sum, then **every** longer sub‑array will automatically be positive as well (since it’s a sum of overlapping positive 3‑length blocks).

Thus we only need to enforce positivity on **all 3‑length windows**.

---

### 3️⃣ Greedy + Prefix Sum Approach

1. **Prefix sum array** `pref[i] = sum(nums[0 … i])`  
   *Allows us to compute the sum of any window in O(1).*

2. **Scan the array from left to right**  
   Maintain `maxPref` = maximum prefix sum seen so far (initially `0`).  
   For each position `i` (the last index of a 3‑window):

   - The sum of the 3‑window ending at `i` is  
     `windowSum = pref[i] - maxPref`.
   - If `windowSum <= 0`, we **must** change one element inside this window.  
     Count one operation, set `maxPref = pref[i]` (treat the whole window as “fixed” by making it zero), and **skip the next two indices** (`i += 2`).  
     Skipping is safe because the replacement we imagine would fix any sub‑array that includes these positions.

3. Return the number of operations.

> **Why skip?**  
> After replacing an element in the window `[i-2, i]`, any sub‑array that still contains any of the next two indices would already contain the replaced (and thus positive) element, so it cannot become non‑positive again.  
> Skipping guarantees we never double‑count a needed replacement.

---

### 4️⃣ Complexity

- **Time**: \(O(n)\) – one linear pass.  
- **Space**: \(O(n)\) for the prefix sum array (can be reduced to \(O(1)\) if we keep a rolling sum).  
  In practice the extra space is fine for \(n \le 10^5\).

---

### 5️⃣ Implementation

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

> **Note**: The code comments explain the logic and help you adapt it for interviews or job‑coding challenges.

---

#### 5.1 Java (LeetCode)

```java
import java.util.*;

class Solution {
    public int makeArrayPositive(int[] nums) {
        int n = nums.length;
        long[] pref = new long[n];
        pref[0] = nums[0];
        for (int i = 1; i < n; i++) {
            pref[i] = pref[i - 1] + nums[i];
        }

        int ops = 0;
        long maxPref = 0;            // best prefix sum so far
        for (int i = 0; i <= n - 3; i++) {
            // sum of window nums[i] + nums[i+1] + nums[i+2]
            long windowSum = pref[i + 2] - maxPref;
            if (windowSum <= 0) {
                ops++;                     // replace one element in this window
                maxPref = pref[i + 2];     // pretend this window became 0
                i += 2;                    // skip the next two indices
            } else {
                maxPref = Math.max(maxPref, pref[i]); // update best prefix
            }
        }
        return ops;
    }
}
```

---

#### 5.2 Python 3

```python
class Solution:
    def makeArrayPositive(self, nums: List[int]) -> int:
        n = len(nums)
        pref = [0] * n
        pref[0] = nums[0]
        for i in range(1, n):
            pref[i] = pref[i - 1] + nums[i]

        ops = 0
        max_pref = 0  # best prefix seen so far
        i = 0
        while i <= n - 3:
            window_sum = pref[i + 2] - max_pref
            if window_sum <= 0:
                ops += 1
                max_pref = pref[i + 2]
                i += 3  # skip the next two indices as well
            else:
                max_pref = max(max_pref, pref[i])
                i += 1
        return ops
```

---

#### 5.3 C++ (g++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int makeArrayPositive(vector<int>& nums) {
        int n = nums.size();
        vector<long long> pref(n);
        pref[0] = nums[0];
        for (int i = 1; i < n; ++i) pref[i] = pref[i-1] + nums[i];

        int ops = 0;
        long long maxPref = 0;              // best prefix so far
        for (int i = 0; i <= n - 3; ++i) {
            long long windowSum = pref[i+2] - maxPref;
            if (windowSum <= 0) {
                ++ops;                     // change one element
                maxPref = pref[i+2];       // window considered fixed
                i += 2;                    // skip next two indices
            } else {
                maxPref = max(maxPref, pref[i]); // keep best prefix
            }
        }
        return ops;
    }
};
```

---

### 6️⃣ Good, Bad & Ugly – What You Should Keep in Mind

| ✅ Good | ❌ Bad | ⚠️ Ugly |
|---------|-------|--------|
| ✅ Greedy is **optimal** – no backtracking needed. | ❌ Skipping logic can be confusing – always explain why you skip 2 indices. | ⚠️ Forgetting the prefix sum range (`long long` / `long`) leads to overflow. |
| ✅ Single linear scan – fits interview constraints. | ❌ Some solutions use extra arrays unnecessarily; prefer rolling sums. | ⚠️ Misreading the “sub‑array length > 2” condition (some think “≥ 3” – correct is “> 2”). |
| ✅ Easy to prove correctness: every long sub‑array contains a 3‑window. | ❌ People might try DP and waste time – greedy is simpler. | ⚠️ Not handling the case where `n < 3` (though constraints forbid it). |
| ✅ Works for negative and positive large values because we use `long`/`long long`. | ❌ Over‑optimizing: you could skip the prefix array and use a sliding sum, but clarity matters. | ⚠️ If you replace the element with a very large negative number in the example, you might still need another operation – ensure you pick a value that neutralizes the window (0 or positive). |

---

### 7️⃣ Why This Blog Helps You Land a Job

- **Clear algorithmic explanation** – interviewers love concise reasoning.  
- **Multiple language implementations** – show you can code in Java, Python, and C++.  
- **SEO‑friendly content** – keywords like “Make a Positive Array”, “Leetcode 3511”, “greedy algorithm”, “O(n) solution” will rank high.  
- **Practical tips** – the “Good/Bad/Ugly” table gives interviewers a cheat‑sheet of pitfalls.

Use this article as a reference when preparing for coding interviews, and feel free to adapt the code snippets for your own projects or portfolio.

---

### 8️⃣ TL;DR

1. *Only 3‑length windows matter.*  
2. Scan left‑to‑right with a prefix sum.  
3. When a 3‑window sum ≤ 0, count an operation, treat that window as fixed, and skip the next two indices.  
4. Return the total operations – that’s the minimum.

Happy coding, and may the interview gods be with you! 🚀