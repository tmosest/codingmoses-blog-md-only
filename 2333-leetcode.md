---
title: LeetCode 2333. Minimum Sum of Squared Difference - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Solving LeetCode 2333 – Minimum Sum of Squared Difference  
**Java | Python | C++** – All three solutions use the same optimal idea.  
Also read the **blog post** that dives into the *good, the bad, and the ugly* of this problem, plus interview‑ready SEO‑friendly copy.

---

## 📌 Problem Recap

| Parameter | Type | Constraints |
|-----------|------|-------------|
| `nums1, nums2` | `int[]` | `1 ≤ n ≤ 10⁵`, `0 ≤ nums[i] ≤ 10⁵` |
| `k1, k2` | `int` | `0 ≤ k1, k2 ≤ 10⁹` |

You may add or subtract `1` to any element of `nums1` **at most** `k1` times and to any element of `nums2` **at most** `k2` times.  
After all modifications, return the **minimum possible**  

\[
\sum_{i=0}^{n-1}\bigl(\text{nums1}[i]-\text{nums2}[i]\bigr)^2
\]

Note: elements can become negative.

---

## ⚙️ Core Idea

1. **Differences matter, not the values** –  
   Only the absolute difference `d = |nums1[i] - nums2[i]|` influences the squared difference.  
   Every operation reduces a difference by `1` (by moving one side towards the other).

2. **Greedy on the largest differences** –  
   Each decrement on a difference `d` reduces the squared sum by  

   \[
   d^2 - (d-1)^2 = 2d-1
   \]

   So we should always spend an operation on the **current largest** difference.

3. **Counting instead of a heap** –  
   The difference can never exceed `100 000`.  
   Store the frequency of each difference in an array of size `100 001`.  
   This gives **O(n + maxDiff)** time and **O(maxDiff)** space, far better than a priority queue.

4. **Apply all operations** –  
   Iterate from the maximum difference downwards, “move” counts to the next lower difference until we run out of operations or reach `0`.

5. **Compute the answer** –  
   Sum `freq[d] * d²` for all remaining differences.

---

## 🧩 Implementation

Below you’ll find ready‑to‑copy code in **Java**, **Python**, and **C++**.

---

### 1️⃣ Java (Java 17)

```java
import java.util.*;

public class Solution {
    public long minSumSquareDiff(int[] nums1, int[] nums2, int k1, int k2) {
        final int MAX = 100_000;
        int[] freq = new int[MAX + 1];
        long ops = (long) k1 + k2;          // total allowed operations
        long sumDiff = 0;                  // total sum of differences

        // Count differences
        for (int i = 0; i < nums1.length; i++) {
            int d = Math.abs(nums1[i] - nums2[i]);
            if (d > 0) {
                freq[d]++;
                sumDiff += d;
            }
        }

        // If we have enough ops to reduce all diffs to zero
        if (sumDiff <= ops) return 0L;

        // Greedy reduction from largest to smallest diff
        for (int d = MAX; d > 0 && ops > 0; d--) {
            int cnt = freq[d];
            if (cnt == 0) continue;
            long canMove = Math.min(cnt, ops);
            freq[d] -= (int) canMove;
            freq[d - 1] += (int) canMove;
            ops -= canMove;
        }

        // Compute final sum of squares
        long result = 0;
        for (int d = 0; d <= MAX; d++) {
            if (freq[d] > 0) {
                result += (long) d * d * freq[d];
            }
        }
        return result;
    }
}
```

**Complexities**

- Time: `O(n + 100000)` → ~`O(n)`
- Space: `O(100000)` → `O(1)` (constant w.r.t. input size)

---

### 2️⃣ Python 3

```python
from typing import List

class Solution:
    def minSumSquareDiff(self, nums1: List[int], nums2: List[int],
                         k1: int, k2: int) -> int:
        MAX = 100_000
        freq = [0] * (MAX + 1)
        ops = k1 + k2
        total_diff = 0

        # Count differences
        for a, b in zip(nums1, nums2):
            d = abs(a - b)
            if d:
                freq[d] += 1
                total_diff += d

        # If all diffs can be eliminated
        if total_diff <= ops:
            return 0

        # Greedy reduction
        for d in range(MAX, 0, -1):
            if ops == 0:
                break
            cnt = freq[d]
            if cnt == 0:
                continue
            take = min(cnt, ops)
            freq[d] -= take
            freq[d - 1] += take
            ops -= take

        # Sum remaining squares
        ans = 0
        for d, cnt in enumerate(freq):
            if cnt:
                ans += d * d * cnt
        return ans
```

**Complexities**

- Time: `O(n + 100000)` → `O(n)`
- Space: `O(100000)` → `O(1)`

---

### 3️⃣ C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long minSumSquareDiff(vector<int>& nums1, vector<int>& nums2,
                               long long k1, long long k2) {
        const int MAX = 100000;
        vector<int> freq(MAX + 1, 0);
        long long ops = k1 + k2;
        long long totalDiff = 0;

        // Count differences
        for (size_t i = 0; i < nums1.size(); ++i) {
            int d = abs(nums1[i] - nums2[i]);
            if (d) {
                ++freq[d];
                totalDiff += d;
            }
        }

        if (totalDiff <= ops) return 0LL;

        // Greedy reduction from largest diff downwards
        for (int d = MAX; d > 0 && ops > 0; --d) {
            int cnt = freq[d];
            if (!cnt) continue;
            long long move = min<long long>(cnt, ops);
            freq[d] -= move;
            freq[d - 1] += move;
            ops -= move;
        }

        // Final sum of squares
        long long ans = 0;
        for (int d = 0; d <= MAX; ++d) {
            if (freq[d])
                ans += 1LL * d * d * freq[d];
        }
        return ans;
    }
};
```

**Complexities**

- Time: `O(n + 100000)` → `O(n)`
- Space: `O(100000)` → `O(1)`

---

## 📚 Why These Solutions Pass All Tests

- **Big `k` values** are handled with `long long` / `long`.
- **Maximum difference** is bounded by `10⁵`, so the counting array never overflows.
- **No heap** → no `O(n log n)` overhead.
- The greedy loop guarantees that we spend each operation on the most valuable difference.

---

## ✍️ Blog Post – *The Good, The Bad, and The Ugly*

> **Title**:  
> **“LeetCode 2333: Mastering Minimum Sum of Squared Difference – A Job‑Interview Cheat Sheet”**

> **Meta description** (SEO):  
> “Learn how to crack LeetCode 2333 in Java, Python, and C++. Understand greedy counting, edge cases, and interview‑tactics for software engineering roles.”

---

### 1️⃣ Introduction

LeetCode’s **Minimum Sum of Squared Difference** (problem #2333) is a classic *greedy + counting* puzzle.  
Interviewers love this problem because it tests:

- **Algorithmic insight** (recognizing that only differences matter).
- **Space‑time trade‑offs** (counting array vs priority queue).
- **Attention to detail** (large `k` values, long integer overflow, negative numbers).

### 2️⃣ The Good

| What’s great | Why it matters |
|--------------|----------------|
| **Linear time** – `O(n)` – scalable to `10⁵` elements. | Demonstrates ability to optimize beyond naive O(n log n). |
| **Simple data structure** – frequency array – no heap, no extra libraries. | Shows clever use of problem constraints. |
| **Deterministic greedy** – always optimal. | Avoids messy backtracking or DP. |
| **Handles huge `k`** via `long long`. | Highlights careful type handling. |

### 3️⃣ The Bad

| Common pitfalls | Fixes |
|-----------------|-------|
| Forgetting that `d` can be `0` – leads to division by zero in `pow`. | Skip `d==0` when counting. |
| Using `int` for `k1 + k2` when `k` can be `10⁹`. | Use `long long`/`long`. |
| Relying on a priority queue → `O(n log n)` and higher constant. | Use counting array (size 100 001). |
| Not checking if total differences ≤ operations → wasted loop. | Early return `0`. |

### 4️⃣ The Ugly

| Edge cases | Handling |
|-----------|----------|
| **All differences zero** – answer is `0`. | Skip counting and return early. |
| **Maximum difference is 100 000** – array index safety. | Use `MAX + 1` array. |
| **Operations exceed total difference sum** – must set all diffs to `0`. | Early exit. |
| **Negative numbers after operations** – irrelevant because only differences matter. | No special handling needed. |

### 5️⃣ Interview‑Ready Tips

| Tip | Why it impresses |
|-----|------------------|
| **Explain the `2d-1` savings** – quantify why the greedy choice is optimal. | Shows deep mathematical insight. |
| **Mention constraints** – using a frequency array exploits `d ≤ 100 000`. | Demonstrates efficient problem‑specific design. |
| **Talk about complexity** – `O(n)` time, `O(1)` auxiliary space. | Interviewers love tight complexity analysis. |
| **Show edge‑case coverage** – early return for `totalDiff <= ops`, handling `0` diffs. | Illustrates robustness. |
| **Offer a fallback heap solution** – in languages where array counting is impractical. | Shows versatility. |

### 6️⃣ SEO Keywords

- LeetCode 2333 solution
- Minimum Sum of Squared Difference
- greedy algorithm interview
- counting sort vs priority queue
- C++ O(n) LeetCode solution
- Java 17 LeetCode 2333
- Python LeetCode 2333
- software engineering interview tips
- algorithmic problem solving

> By weaving these keywords naturally into the post, recruiters who are Googling “LeetCode 2333 solution” or “minimum sum of squared difference” will see your content high in search results.

---

## 🎉 Wrap‑up

*All three implementations achieve the same optimal linear‑time algorithm.*  
Pick the one that matches your language of choice and impress your interviewers with a clean, well‑commented solution.

Happy coding! 🚀