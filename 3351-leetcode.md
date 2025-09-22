---
title: LeetCode 3351. Sum of Good Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3351 – *Sum of Good Subsequences*  
### “The Good, The Bad, & The Ugly” – A Practical DP Walk‑through (Java | Python | C++)

> **TL;DR** –  
> *A good subsequence* is a subsequence where the absolute difference between every pair of consecutive elements equals **1**.  
> We want the sum of all elements over **every** good subsequence.  
> The classic solution is a **dynamic programming** that runs in **O(n)** time and **O(max(nums))** memory.

> **Keywords:**  
> *LeetCode 3351* | *Sum of Good Subsequences* | *Dynamic Programming* | *DP with arrays* | *Java* | *Python* | *C++* | *Interview Prep* | *Job Interview*

---

## 1️⃣ Problem Recap

```text
Input  : nums = [1, 2, 1]
Output : 14
```

Good subsequences (size ≥ 1):
- [1]  → sum = 1
- [2]  → sum = 2
- [1]  → sum = 1
- [1,2]→ sum = 3
- [2,1]→ sum = 3
- [1,2,1]→ sum = 4  
Total = 14

Constraints  
- `1 ≤ nums.length ≤ 10^5`  
- `0 ≤ nums[i] ≤ 10^5`  
- Answer modulo `10^9 + 7`

---

## 2️⃣ Why Dynamic Programming?

- Every good subsequence ending with value `x` can be extended only by elements `x‑1` or `x+1`.  
- This “local” property lets us process the array once, building up counts and total sums per value.  
- Without DP, we would need to enumerate all subsequences → `O(2^n)`.

---

## 3️⃣ The DP Idea

Let  
- `cnt[v]`   = **number** of good subsequences that end with value `v`.  
- `sum[v]`   = **total sum of elements** over all good subsequences that end with value `v`.  

When we read a new element `a`:

| Action | Description | Update formula |
|--------|-------------|----------------|
| Start a new subsequence `[a]` | 1 subsequence | `cnt[a] += 1` |
| Extend subsequence ending with `a-1` | Each gives new subsequence `...,(a-1),a` | `cnt[a] += cnt[a-1]` |
| Extend subsequence ending with `a+1` | Same | `cnt[a] += cnt[a+1]` |
| Update sums | For each new subsequence we add the sum of the old subsequence + the new element `a` |  
`sum[a] += sum[a-1] + sum[a+1] + a * (cnt[a-1] + cnt[a+1] + 1)` |

All operations are taken modulo `M = 1 000 000 007`.

The answer is the sum of all `sum[v]` values after the scan.

---

## 4️⃣ Edge Cases & “The Ugly”

| Problem | What can go wrong? | How to guard |
|---------|-------------------|--------------|
| Index out of bounds | `cnt[a-1]` or `cnt[a+1]` when `a` is 0 or `MAX_VAL` | Use array size `MAX_VAL + 3` and always access `cnt[a+1]`, `cnt[a+2]`. |
| Integer overflow | `cnt` and `sum` can exceed `int` | Use `long` (`int64`) everywhere. |
| Modulo after every addition | Failing to mod can overflow | Mod after each assignment. |
| Duplicate numbers | Each occurrence of the same number must be treated independently | DP naturally handles it because we process sequentially. |

---

## 5️⃣ The Code

Below are **three** implementations that follow the same logic:

*All three share the same complexity: `O(n + max(nums))` time, `O(max(nums))` memory.*

> **Tip:** Use `int[]` for the input; all heavy lifting is done in `long` arrays.

### 5.1 Java

```java
import java.util.*;

public class SumOfGoodSubsequences {
    private static final long MOD = 1_000_000_007L;
    private static final int MAX_VAL = 100_000;          // given constraint

    public int sumOfGoodSubsequences(int[] nums) {
        long[] cnt  = new long[MAX_VAL + 3];   // +3 to avoid bounds checks
        long[] sum  = new long[MAX_VAL + 3];

        for (int a : nums) {
            int idx = a + 1;                   // shift by +1 to keep 0-index safe

            long cntPrev = cnt[idx - 1];       // a-1
            long cntNext = cnt[idx + 1];       // a+1

            long sumPrev = sum[idx - 1];
            long sumNext = sum[idx + 1];

            long newCnt = (cntPrev + cntNext + 1) % MOD;
            long newSum = (sumPrev + sumNext + a * newCnt) % MOD;

            cnt[idx] = (cnt[idx] + newCnt) % MOD;
            sum[idx] = (sum[idx] + newSum) % MOD;
        }

        long result = 0;
        for (int i = 0; i < sum.length; i++) {
            result = (result + sum[i]) % MOD;
        }
        return (int) result;
    }

    public static void main(String[] args) {
        SumOfGoodSubsequences solver = new SumOfGoodSubsequences();
        System.out.println(solver.sumOfGoodSubsequences(new int[]{1, 2, 1})); // 14
        System.out.println(solver.sumOfGoodSubsequences(new int[]{3, 4, 5})); // 40
    }
}
```

### 5.2 Python

```python
from collections import defaultdict
from typing import List

MOD = 10 ** 9 + 7

class Solution:
    def sumOfGoodSubsequences(self, nums: List[int]) -> int:
        cnt = defaultdict(int)
        total = defaultdict(int)

        for a in nums:
            c_left, c_right = cnt[a - 1], cnt[a + 1]
            t_left, t_right = total[a - 1], total[a + 1]

            new_cnt = (c_left + c_right + 1) % MOD
            new_sum = (t_left + t_right + a * new_cnt) % MOD

            cnt[a] = (cnt[a] + new_cnt) % MOD
            total[a] = (total[a] + new_sum) % MOD

        return sum(total.values()) % MOD

# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.sumOfGoodSubsequences([1, 2, 1]))  # 14
    print(sol.sumOfGoodSubsequences([3, 4, 5]))  # 40
```

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int sumOfGoodSubsequences(vector<int>& nums) {
        const int MOD = 1'000'000'007;
        const int MAX_VAL = 100000;
        vector<long long> cnt(MAX_VAL + 3, 0);
        vector<long long> sum(MAX_VAL + 3, 0);

        for (int a : nums) {
            int idx = a + 1;                    // shift by +1
            long long c_left = cnt[idx - 1];    // a-1
            long long c_right = cnt[idx + 1];   // a+1
            long long t_left = sum[idx - 1];
            long long t_right = sum[idx + 1];

            long long newCnt = (c_left + c_right + 1) % MOD;
            long long newSum = (t_left + t_right + 1LL * a * newCnt) % MOD;

            cnt[idx] = (cnt[idx] + newCnt) % MOD;
            sum[idx] = (sum[idx] + newSum) % MOD;
        }

        long long ans = 0;
        for (auto v : sum) ans = (ans + v) % MOD;
        return (int)ans;
    }
};

// Demo
int main() {
    Solution sol;
    cout << sol.sumOfGoodSubsequences({1, 2, 1}) << '\n'; // 14
    cout << sol.sumOfGoodSubsequences({3, 4, 5}) << '\n'; // 40
}
```

---

## 6️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | `O(n + MAX_VAL)` | `O(n + MAX_VAL)` | `O(n + MAX_VAL)` |
| Memory | `O(MAX_VAL)` (arrays of 100k) | `O(MAX_VAL)` (hash‑maps but effectively array size) | `O(MAX_VAL)` |
| Why `MAX_VAL`? | Values up to `10^5`; we allocate `100003` slots. | Using `defaultdict` keeps memory proportional to distinct values. | Same as Java. |

All run comfortably within 1 s and 512 MB limits.

---

## 7️⃣ SEO‑Ready Blog: “Sum of Good Subsequences – The Good, The Bad & The Ugly”

> *If you’re preparing for a software engineering interview, LeetCode 3351 is a **must‑solve** problem. Below is a deep‑dive that breaks down the DP trick, shows clean Java/Python/C++ code, and explains every pitfall you might face.*

### 7.1 Intro

> “Today, we’re tackling **LeetCode 3351: Sum of Good Subsequences** – a problem that tests your understanding of dynamic programming, modular arithmetic, and edge‑case handling.”

### 7.2 The “Good”

- **Simplicity** – DP with arrays is **straightforward** and scales linearly.  
- **Modularity** – The algorithm naturally handles repeated values and the `0`/`MAX` edge values.  
- **Reusability** – The same pattern (`cnt` + `sum`) applies to many “adjacent” subsequence problems (e.g., Fibonacci‑style subsequences).

### 7.3 The “Bad”

- **Index Off‑by‑One** – Forgetting to shift by `+1` leads to `ArrayIndexOutOfBounds` in Java/C++ or a `KeyError` in Python.  
- **Overflow** – Using `int` instead of `long` in Java or `int` in C++ causes silent overflows.  
- **Modulo Mis‑placement** – Adding huge numbers before taking modulo can exceed `64‑bit` and give wrong answers.

### 7.4 The “Ugly”

- **Boundaries** – When `a` is `0` or `100000`, the naive DP would try to access `cnt[-1]` or `cnt[100001]`.  
- **Hash‑Map Overhead** – Python’s `defaultdict` looks elegant but can waste memory if many distinct values.  
- **Readability vs. Performance** – Too many `% MOD` statements can clutter the code, but they’re essential.

### 7.5 Final Takeaway

> “LeetCode 3351 isn’t just a toy. It’s a micro‑cosm of real‑world interview problems where a single line of DP can turn an exponential nightmare into a linear algorithm. Master this pattern and you’ll ace many subsequence‑based questions.”

---

## 8️⃣ Closing Thoughts

- The DP above is the **canonical** solution; if you present it clearly in an interview, you’ll show deep understanding of *state definition*, *transition*, and *modular arithmetic*.  
- Practice variations: change `cnt` to `int` and `sum` to `long`, or use `unordered_map` for sparse data.  
- Keep the “shift by +1” trick in mind; it’s a lifesaver for boundary safety in all languages.

Good luck with your interview, and may the **good subsequences** be ever in your favor! 🚀

--- 

*Happy coding!*