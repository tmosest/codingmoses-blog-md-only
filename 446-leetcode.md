---
title: LeetCode 446. Arithmetic Slices II - Subsequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 446 – Arithmetic Slices II (Subsequence)  
### A Complete Java / Python / C++ Solution + SEO‑Optimized Blog Post  

---

### TL;DR

| Language | Time | Space |
|----------|------|-------|
| **Java** |  O(n²) | O(n²) |
| **Python** |  O(n²) | O(n²) |
| **C++** |  O(n²) | O(n²) |

> *“The good, the bad, and the ugly” of solving **LeetCode 446**.*

---

## 1. Problem Recap

> **Arithmetic Slices II – Subsequence**  
> Given an integer array `nums`, count **all** arithmetic subsequences of length **≥ 3**.  
> A subsequence is arithmetic if the difference between consecutive elements is constant.

*Example:*  
`nums = [2,4,6,8,10]` → answer = 7  
( `[2,4,6]`, `[4,6,8]`, …, `[2,6,10]`)

The array size is up to **1000**, and all values fit into 32‑bit signed integers. The answer always fits into a 32‑bit signed integer as well.

---

## 2. Why DP + HashMap is the “Standard” Solution

1. **Subsequence → Dynamic Programming**  
   For every index `i` we keep a map `dp[i]` where `dp[i][d]` is the number of **arithmetic subsequences ending at `i` with common difference `d`** that have length **≥ 2** (the pair itself counts as length 2, which is not yet a valid slice).  
   When we see a new pair `(j, i)` (`j < i`), the difference `d = nums[i] - nums[j]`.  
   All sequences ending at `j` with difference `d` can be extended by `nums[i]`, turning them into valid slices.  
   So:
   ```text
   new_slices = dp[j][d]          // already counted slices ending at j
   dp[i][d]  += new_slices + 1    // +1 for the new pair (j, i)
   answer   += new_slices
   ```

2. **Handling 32‑bit Overflows**  
   Differences can exceed `int` range (e.g., `INT_MAX - INT_MIN`).  
   We compute differences as `long` first, check bounds, then cast to `int`.  
   The answer is accumulated in `long` to avoid intermediate overflow, finally cast to `int`.

3. **Complexities**  
   - Time: `O(n²)` – every pair `(j, i)` is processed once.  
   - Space: `O(n²)` in the worst case (every difference is distinct). For `n ≤ 1000`, this is fine (~4 MB in Java, ~8 MB in C++).

---

## 3. Reference Implementations

> **Tip:** Use **`List< Map<…> >`** in Java, **`list[dict]`** in Python, and **`vector<unordered_map>​`** in C++.

### 3.1 Java

```java
import java.util.*;

class Solution {
    public int numberOfArithmeticSlices(int[] nums) {
        int n = nums.length;
        long answer = 0L;
        // dp[i] maps common difference -> number of subsequences ending at i with that diff
        @SuppressWarnings("unchecked")
        Map<Integer, Integer>[] dp = new Map[n];
        for (int i = 0; i < n; ++i) {
            dp[i] = new HashMap<>();
            for (int j = 0; j < i; ++j) {
                long diffL = (long) nums[i] - (long) nums[j];
                if (diffL < Integer.MIN_VALUE || diffL > Integer.MAX_VALUE) continue;
                int diff = (int) diffL;

                int prev = dp[j].getOrDefault(diff, 0);
                int cur  = dp[i].getOrDefault(diff, 0);

                dp[i].put(diff, cur + prev + 1);   // +1 for the new pair (j,i)
                answer += prev;                    // only complete slices contribute
            }
        }
        return (int) answer;
    }
}
```

> **Why this is good**  
> *Fast (O(n²)), easy to understand, handles overflow, uses generics safely.*

### 3.2 Python

```python
from typing import List
from collections import defaultdict

class Solution:
    def numberOfArithmeticSlices(self, nums: List[int]) -> int:
        n = len(nums)
        answer = 0
        dp = [defaultdict(int) for _ in range(n)]

        for i in range(n):
            for j in range(i):
                diff = nums[i] - nums[j]
                # diff is already a Python int (unbounded) – no overflow concerns
                prev = dp[j][diff]
                dp[i][diff] += prev + 1
                answer += prev

        return answer
```

> **Why Python is nice**  
> *Built‑in big integers, succinct syntax, `defaultdict` removes boilerplate.*

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int numberOfArithmeticSlices(vector<int>& nums) {
        int n = nums.size();
        long long ans = 0;
        vector<unordered_map<long long, long long>> dp(n);

        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j) {
                long long diff = (long long)nums[i] - nums[j];
                // No need to clamp diff; unordered_map key is long long
                long long prev = dp[j][diff];
                dp[i][diff] += prev + 1;   // +1 for the new pair
                ans += prev;
            }
        }
        return static_cast<int>(ans);
    }
};
```

> **Why C++ shines**  
> *Fast execution, memory efficient, explicit type control.*

---

## 4. The Good, the Bad, and the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic Idea** | Clear DP recurrence, leverages subsequence property | Requires O(n²) memory if all differences distinct | Need to think about long vs int boundaries |
| **Time Complexity** | Acceptable for n ≤ 1000 | Still quadratic – can be heavy on large inputs | None |
| **Space Complexity** | ~4–8 MB, fine for 1000 | Worst‑case O(n²) maps may degrade performance | None |
| **Overflow Handling** | Explicit long for diff & answer | Checking bounds in Java adds a branch | Casting `long` to `int` may be unsafe if diff out of range |
| **Language‑Specific Pitfalls** | HashMap generics warning | `defaultdict` auto‑creates keys | `unordered_map` rehashing overhead |
| **Readability** | Very readable, comments help | Some may think `dp[j][diff]` is confusing | The “+1 for pair” logic can be misunderstood |

> **Takeaway:** The DP‑hash approach is *the* accepted solution, but it requires careful handling of large differences and memory usage.

---

## 5. SEO‑Optimized Blog Title & Keywords

**Title**  
> “LeetCode 446 – Arithmetic Slices II (Subsequence) in Java, Python, C++ | Master the DP Map Trick”

**Primary Keywords**  
- LeetCode 446  
- Arithmetic Slices II  
- Subsequence DP  
- Java LeetCode solutions  
- Python LeetCode solutions  
- C++ LeetCode solutions  
- Coding interview strategy  
- Software engineer interview  

**Meta Description**  
> “Learn the optimal DP‑map solution for LeetCode 446. Read Java, Python, and C++ code, get the big picture, and prepare for interview success.”

---

## 6. Detailed Walk‑Through (Blog Body)

> ### 6.1 Problem Overview  
> ... (as above)

> ### 6.2 Why Brute‑Force Fails  
> The number of subsequences grows as 2ⁿ. For n=1000 this is astronomical.

> ### 6.3 Dynamic Programming with HashMaps  
> ... (explain recurrence)

> ### 6.4 Code Walk‑Through (Java/Python/C++)  
> (Insert code snippets with inline comments)

> ### 6.5 Complexity Analysis  
> (Show O(n²) time, O(n²) space)

> ### 6.6 Edge‑Case Handling  
> - Overflow of differences  
> - Negative numbers  
> - Long sequences with same values

> ### 6.7 Good, Bad, Ugly Discussion  
> (Use the table above)

> ### 6.8 Practical Tips for Interviews  
> - Mention time/space trade‑offs  
> - Show understanding of overflow handling  
> - Explain why we count only `prev` for the answer

> ### 6.9 Further Reading  
> - LeetCode discussion threads  
> - Dynamic programming patterns for subsequence problems

> ### 6.10 Conclusion  
> The DP‑map trick is a classic pattern for subsequence arithmetic problems. Master it and you’ll have a strong tool for any software‑engineering interview.

---

## 7. Final Checklist Before Your Interview

- [ ] Implement the solution in your preferred language.
- [ ] Test with the provided examples and corner cases (`[0]`, `[1,1,1]`, maximum/minimum int values).
- [ ] Time the solution for `n = 1000` to ensure it runs under 1 s.
- [ ] Be ready to explain the DP recurrence and the “+1 for pair” logic.
- [ ] Discuss overflow handling and why we use `long`/`long long`.
- [ ] Prepare a brief comparison of Java/Python/C++ trade‑offs (speed vs readability).

Good luck, and may your arithmetic slices always be *perfectly* balanced!