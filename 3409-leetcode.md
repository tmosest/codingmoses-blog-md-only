---
title: LeetCode 3409. Longest Subsequence With Decreasing Adjacent Difference - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem: Longest Subsequence With Decreasing Adjacent Difference (LeetCode 3409)

> **Given an integer array `nums` (2 ≤ n ≤ 10⁴, 1 ≤ nums[i] ≤ 300)**  
> Find the length of the longest subsequence `seq` such that  
> `|seq[i+1] - seq[i]|` is non‑increasing.  
> Return the maximum possible length.

Examples  
| Input | Output | Explanation |
|-------|--------|-------------|
| `[16,6,3]` | `3` | `[16,6,3]` → diffs `[10,3]` |
| `[6,5,3,4,2,1]` | `4` | `[6,4,2,1]` → diffs `[2,2,1]` |
| `[10,20,10,19,10,20]` | `5` | `[10,20,10,19,10]` → diffs `[10,10,9,9]` |

The constraint `nums[i] ≤ 300` is a key hint: the state space can be bounded by the value domain instead of the array length.

---

## ✅ Solution Overview

1. **Dynamic Programming on value and last difference**  
   `dp[v][d]` = longest subsequence that **starts** with value `v` and whose **first adjacent difference** is `d`.  
   While scanning the array from right to left we update these states.

2. **Transition**  
   For the current element `x = nums[i]`  
   * For every possible *next value* `y` (1 … 300)  
     * `diff = |x - y|`  
     * `dp[x][diff] = max(dp[x][diff], dp[y][diff] + 1)`  
       – we can append `x` before a subsequence that starts with `y` and uses the same `diff`.  
   * After considering all `y`, we must also allow the sequence to **shorten the difference** (since the sequence can only become *more* non‑increasing).  
     * For `diff` from 1 to 299  
       `dp[x][diff] = max(dp[x][diff], dp[x][diff-1])`  
     * Track the global answer `ans = max(ans, dp[x][diff])`.

3. **Complexity**  
   * **Time**: `O(n * V²)` where `V = 300`.  
     `n ≤ 10⁴`, `V² = 90 000` → about **9 × 10⁶** operations – well below the limit.  
   * **Space**: `O(V²)` → a `301 × 301` integer matrix (~0.36 MB).

4. **Why this works**  
   * Scanning from right guarantees that when we process `x`, all possible continuations (`dp[y][*]`) are already computed.  
   * The “non‑increasing” property is enforced by the second pass (`dp[x][diff] = max(dp[x][diff], dp[x][diff-1])`) which essentially says: *“If we can achieve length `k` with difference `diff-1`, we can also achieve length `k` with difference `diff` (since the sequence can stay the same or shrink the diff).”*

---

## 🧑‍💻 Code Implementations

Below are clean, self‑contained solutions for **Java**, **Python**, and **C++**. Each file contains the `Solution` class (LeetCode style) and a `main` method that demonstrates usage.

---

### Java

```java
import java.util.*;

public class Solution {
    public int longestSubsequence(int[] nums) {
        final int MAX_VAL = 300;
        // dp[value][diff] = longest length starting with 'value' and first diff 'diff'
        int[][] dp = new int[MAX_VAL + 1][MAX_VAL + 1];
        int ans = 0;

        for (int i = nums.length - 1; i >= 0; i--) {
            int x = nums[i];

            // Consider all possible next values
            for (int y = 1; y <= MAX_VAL; y++) {
                int diff = Math.abs(x - y);
                dp[x][diff] = Math.max(dp[x][diff], dp[y][diff] + 1);
            }

            // Allow decreasing the diff (non‑increasing constraint)
            for (int diff = 1; diff <= MAX_VAL; diff++) {
                dp[x][diff] = Math.max(dp[x][diff], dp[x][diff - 1]);
                ans = Math.max(ans, dp[x][diff]);
            }
        }
        return ans;
    }

    // --------- Demo -------------
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.longestSubsequence(new int[]{16, 6, 3}));           // 3
        System.out.println(sol.longestSubsequence(new int[]{6, 5, 3, 4, 2, 1}));   // 4
        System.out.println(sol.longestSubsequence(new int[]{10,20,10,19,10,20}));  // 5
    }
}
```

---

### Python

```python
class Solution:
    def longestSubsequence(self, nums: list[int]) -> int:
        MAX_VAL = 300
        # dp[v][d] -> length starting with value v and first diff d
        dp = [[0] * (MAX_VAL + 1) for _ in range(MAX_VAL + 1)]
        ans = 0

        for x in reversed(nums):
            # Transition to all possible next values
            for y in range(1, MAX_VAL + 1):
                diff = abs(x - y)
                dp[x][diff] = max(dp[x][diff], dp[y][diff] + 1)

            # Allow decreasing diff (non‑increasing property)
            for diff in range(1, MAX_VAL + 1):
                dp[x][diff] = max(dp[x][diff], dp[x][diff - 1])
                ans = max(ans, dp[x][diff])

        return ans

# ---------- Demo ----------
if __name__ == "__main__":
    sol = Solution()
    print(sol.longestSubsequence([16, 6, 3]))                     # 3
    print(sol.longestSubsequence([6, 5, 3, 4, 2, 1]))             # 4
    print(sol.longestSubsequence([10,20,10,19,10,20]))            # 5
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int longestSubsequence(vector<int>& nums) {
        const int MAXV = 300;
        vector<vector<int>> dp(MAXV + 1, vector<int>(MAXV + 1, 0));
        int ans = 0;

        for (int i = (int)nums.size() - 1; i >= 0; --i) {
            int x = nums[i];

            // transition to all possible next values
            for (int y = 1; y <= MAXV; ++y) {
                int diff = abs(x - y);
                dp[x][diff] = max(dp[x][diff], dp[y][diff] + 1);
            }

            // allow decreasing diff (non‑increasing constraint)
            for (int diff = 1; diff <= MAXV; ++diff) {
                dp[x][diff] = max(dp[x][diff], dp[x][diff - 1]);
                ans = max(ans, dp[x][diff]);
            }
        }
        return ans;
    }
};

// ---------- Demo ----------
int main() {
    Solution sol;
    cout << sol.longestSubsequence({16, 6, 3}) << endl;           // 3
    cout << sol.longestSubsequence({6, 5, 3, 4, 2, 1}) << endl;   // 4
    cout << sol.longestSubsequence({10,20,10,19,10,20}) << endl;  // 5
    return 0;
}
```

---

## 📚 Blog Article (SEO‑Optimized)

---

# Longest Subsequence With Decreasing Adjacent Difference – A Deep‑Dive into LeetCode 3409

**Keywords**: LeetCode 3409, Longest Subsequence With Decreasing Adjacent Difference, dynamic programming, Java, Python, C++, interview, FAANG, coding interview, algorithm, DP solution

---

### 1️⃣ What’s the Problem About?

You're given a list of integers (`1 ≤ nums[i] ≤ 300`).  
You need to pick a subsequence where the absolute difference between consecutive picks never grows:  
`|a₂ – a₁| ≥ |a₃ – a₂| ≥ …`.  
Your goal: **maximum length**.

Why is this tricky?  
- The subsequence can skip arbitrary elements.  
- The non‑increasing constraint couples every adjacent pair.  
- Brute‑force exponential solutions are impossible for `n = 10⁴`.

---

### 2️⃣ The “Good” – Leveraging the Small Value Range

The most powerful hint: **`nums[i] ≤ 300`**.  
Instead of treating each index as a separate state (which would give us an `O(n²)` DP), we treat **the value** itself as part of the state.  
That cuts the state space down from `10⁴` indices to only `300` distinct values.

---

### 3️⃣ The “Bad” – Naïve DP Fails

A first‑attempt might think:  
`dp[i]` = longest subsequence starting at index `i`.  
But when you try to add the non‑increasing constraint, you end up needing to remember the *last difference* used, leading to a state like `dp[i][lastDiff]`.  
Even then, `lastDiff` can be up to 300, so you’d still be stuck at `O(n²)` for the inner loop.

This approach is *bad* because it quickly explodes to a few hundred million operations, far beyond typical interview limits.

---

### 4️⃣ The “Ugly” – An Even Worse Attempt

Some people try a greedy or two‑pointer approach, assuming that always picking the next smallest difference works.  
That fails on test cases like `[10,20,10,19,10,20]`, where greedy would miss the optimal `[10,20,10,19,10]`.  
The “ugly” pitfall is not accounting for **skipping elements** and the *full* non‑increasing property.

---

### 5️⃣ The “Best” – Value‑Based DP with Difference Shrinking

#### 5.1 State Definition

`dp[value][diff]` – longest subsequence **starting with `value`** where the first difference is `diff`.  
We fill this table while iterating the input array from **right to left**.

#### 5.2 Transition

For current `x = nums[i]`:

| next value `y` | `diff = |x-y|` | Update | Explanation |
|----------------|---------------|--------|------------|
| all `y` (1…300) | | `dp[x][diff] = max(dp[x][diff], dp[y][diff] + 1)` | we can prepend `x` before a subsequence that already starts with `y` and uses the same first difference. |

After all `y`, we must allow the sequence to “tighten” the difference (since the sequence may use a *larger* first difference but still be non‑increasing):

| `diff` | Update | Reason |
|--------|--------|--------|
| `diff` 1…300 | `dp[x][diff] = max(dp[x][diff], dp[x][diff-1])` | If we can achieve length `k` with a smaller difference, we can also achieve length `k` with a larger (or equal) difference because the subsequence can stay the same or shrink the gap. |

During this second pass we update the global maximum `ans`.

#### 5.3 Why Right‑to‑Left Works

Processing from the end guarantees that all future subsequences are already known, so the transition is valid. This is a classic *offline DP* trick used in many LeetCode problems (e.g., Longest Increasing Subsequence with a value‑based DP).

---

### 6️⃣ Final Thoughts & Interview Tips

| Point | Takeaway |
|-------|----------|
| **Use constraints** | `nums[i] ≤ 300` → bounded DP. |
| **State compression** | Value + last diff → `O(V²)` instead of `O(n²)`. |
| **Two‑phase update** | First extend, then allow diff to shrink. |
| **Time complexity** | `O(n * V²)` → 9 × 10⁶ ops for worst case. |
| **Space complexity** | `O(V²)` → tiny memory. |
| **Edge cases** | All numbers equal → answer `n`. |
| **Interview strategy** | 1. Explain intuition early. 2. Show the DP table concept. 3. Discuss complexity. 4. Mention the value‑domain trick. |

> **Pro tip**: When discussing this problem in an interview, highlight the *value‑based* DP as a classic way to exploit small integer ranges. Mention that you’re iterating from the back to ensure future states are ready.

---

### 7️⃣ SEO‑Optimized Summary

- **Title**: “LeetCode 3409 – Longest Subsequence With Decreasing Adjacent Difference | Java / Python / C++ DP Solution”
- **Meta Description**: “Discover a clean dynamic‑programming solution for LeetCode 3409. Learn Java, Python, and C++ code, time/space complexity, and interview tips for FAANG.”  
- **Key Phrases**: LeetCode 3409, Longest Subsequence With Decreasing Adjacent Difference, DP solution, Java, Python, C++, interview problem, coding interview, FAANG, algorithm analysis.

---

### 🚀 Bonus: Running the Code

All three snippets above contain a `main` / `demo` section that prints the expected results for the sample inputs. Copy the code into a file (`Solution.java`, `solution.py`, `solution.cpp`) and run it with your favourite compiler/interpreter to see the magic in action.

---

Happy coding, and may your subsequence always stay non‑increasing! 🎉