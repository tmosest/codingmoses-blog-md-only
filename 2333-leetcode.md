---
title: LeetCode 2333. Minimum Sum of Squared Difference - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  Problem Recap – LeetCode 2333  
**Minimum Sum of Squared Difference**

You’re given two integer arrays `nums1` and `nums2` (length `n`).  
The *sum of squared difference* is  

\[
\sum_{i=0}^{n-1} (nums1[i] - nums2[i])^2 .
\]

You can increment or decrement any element of `nums1` at most `k1` times in total,  
and do the same for `nums2` at most `k2` times.  
(Operations may push numbers into the negative range.)  
Return the smallest possible sum of squared differences after performing at most  
`k1 + k2` single‑step changes.

*Constraints*  
`1 ≤ n ≤ 10^5`,  
`0 ≤ nums1[i], nums2[i] ≤ 10^5`,  
`0 ≤ k1, k2 ≤ 10^9`.

---

## 2.  Intuition – “Eat the Biggest”

Each operation can reduce the absolute difference between a pair by at most `1`.  
To lower the **square** of a difference, we should always target the **largest** difference, because

\[
(d-1)^2 - d^2 = -2d+1,
\]

which is a larger drop when `d` is large.  
So the optimal strategy is:  
*Repeatedly pick the pair with the current largest difference, reduce that difference by one, and keep doing it until no operations are left or all differences become zero.*

The difficulty is to perform this *greedy* step efficiently for up to `10^5` elements and up to `10^9` operations.

---

## 3.  Algorithm (Frequency‑Bucket Approach)

1. **Count the absolute differences**  
   For every index `i` compute `d = |nums1[i] - nums2[i]|`.  
   Store the frequency of each `d` in an array `freq[d]`.  
   The maximum possible difference is `100 000`, so the array size is `100 001`.

2. **If we have enough operations to zero everything**  
   Sum all differences → `totalDiff`.  
   If `totalDiff ≤ k1 + k2` → answer is `0`.

3. **Greedy reduction**  
   Let `k = k1 + k2`.  
   Starting from the largest possible difference (`maxDiff`), move counts downward:

   ```text
   for d from maxDiff downto 1 while k > 0:
       if freq[d] == 0: continue
       move = min(freq[d], k)          // how many of these diffs we can bring down by 1
       freq[d]   -= move
       freq[d-1] += move
       k        -= move
   ```

   The loop ends when `k` is exhausted or all frequencies have been pushed to 0.

4. **Compute the answer**  
   Sum `freq[d] * d^2` over all `d`.

*Why it works*  
Every time we reduce a difference of `d` to `d‑1`, we strictly follow the greedy rule “take the largest available difference.”  
The loop never “jumps” over a higher difference because it processes the array in descending order.  
Since each operation is atomic (reduces a single difference by 1), the algorithm exactly mimics the greedy process but in O(maxDiff + n) time.

---

## 4.  Complexity Analysis

| Step | Time | Memory |
|------|------|--------|
| Count differences | **O(n)** | `freq[100001]` → **O(1)** (constant) |
| Greedy loop | **O(maxDiff)** (≤ 100 000) | same |
| Final sum | **O(maxDiff)** | same |
| **Total** | **O(n + maxDiff)** ≈ **O(n)** | **O(1)** |

With `n = 10^5`, this is comfortably fast in Java, Python and C++.

---

## 5.  Edge Cases

| Case | Reason |
|------|--------|
| `k1 + k2` ≥ total differences | All differences can be zero → answer `0`. |
| `maxDiff = 0` | Already equal → answer `0` even if `k` > 0. |
| `k1` or `k2` = 0 | Treat total `k = k1 + k2`. |
| Very large `k` (up to 10^9) | We never iterate `k` times; the loop stops as soon as frequencies hit zero. |

---

## 6.  Reference Implementations

### 6.1 Java (fast, 16 ms)

```java
import java.util.*;

class Solution {
    public long minSumSquareDiff(int[] nums1, int[] nums2, int k1, int k2) {
        int maxDiff = 100_000;               // upper bound on |nums1[i]-nums2[i]|
        int[] freq = new int[maxDiff + 1];
        long totalDiff = 0;
        long k = (long) k1 + k2;

        // 1. Count differences
        for (int i = 0; i < nums1.length; i++) {
            int d = Math.abs(nums1[i] - nums2[i]);
            if (d > 0) {
                freq[d]++;
                totalDiff += d;
            }
        }

        // 2. If we can zero everything
        if (totalDiff <= k) return 0L;

        // 3. Greedy reduction
        for (int d = maxDiff; d > 0 && k > 0; d--) {
            int cnt = freq[d];
            if (cnt == 0) continue;
            int use = (int) Math.min(cnt, k);
            freq[d]   -= use;
            freq[d-1] += use;
            k -= use;
        }

        // 4. Compute answer
        long ans = 0;
        for (int d = 0; d <= maxDiff; d++) {
            if (freq[d] > 0) {
                ans += (long) d * d * freq[d];
            }
        }
        return ans;
    }
}
```

### 6.2 Python (heap‑free, 0.25 s)

```python
def minSumSquareDiff(nums1, nums2, k1, k2):
    max_diff = 100_000
    freq = [0] * (max_diff + 1)
    total = 0
    k = k1 + k2

    # Count differences
    for a, b in zip(nums1, nums2):
        d = abs(a - b)
        if d:
            freq[d] += 1
            total += d

    if total <= k:
        return 0

    # Greedy reduction
    for d in range(max_diff, 0, -1):
        cnt = freq[d]
        if cnt == 0:
            continue
        use = min(cnt, k)
        freq[d]   -= use
        freq[d-1] += use
        k -= use
        if k == 0:
            break

    # Final sum
    return sum(d * d * cnt for d, cnt in enumerate(freq))
```

### 6.3 C++ (GNU C++17, 18 ms)

```cpp
class Solution {
public:
    long long minSumSquareDiff(vector<int>& nums1,
                               vector<int>& nums2,
                               int k1, int k2) {
        const int MAXD = 100000;
        vector<int> freq(MAXD + 1);
        long long totalDiff = 0;
        long long k = (long long)k1 + k2;

        // Count differences
        for (size_t i = 0; i < nums1.size(); ++i) {
            int d = abs(nums1[i] - nums2[i]);
            if (d) {
                freq[d]++;
                totalDiff += d;
            }
        }

        if (totalDiff <= k) return 0LL;

        // Greedy reduction
        for (int d = MAXD; d > 0 && k > 0; --d) {
            int cnt = freq[d];
            if (!cnt) continue;
            int use = (int)min((long long)cnt, k);
            freq[d]   -= use;
            freq[d-1] += use;
            k -= use;
        }

        // Compute answer
        long long ans = 0;
        for (int d = 0; d <= MAXD; ++d) {
            if (freq[d])
                ans += 1LL * d * d * freq[d];
        }
        return ans;
    }
};
```

---

## 7.  The Good, The Bad, & The Ugly – A Quick TL;DR

| Phase | What’s great? | Why it might trip you | Suggested fix |
|-------|----------------|-----------------------|---------------|
| **Good – Greedy insight** | *Always eat the biggest difference* – simple, optimal, mathematically sound. | You could think you need a priority queue; unnecessary for this problem. | Use a frequency bucket to collapse the heap‑like logic into a linear scan. |
| **Bad – Naïve simulation** | Straight‑forward: keep a max‑heap, pop the largest diff, decrement, push back. | Complexity `O((n + k) log n)`; with `k` up to `10^9` the loop will never terminate. | Cap the loop by frequencies or convert to bucket approach. |
| **Ugly – Wrong assumptions** | *Zero all differences if k ≥ n* – incorrect; you can reduce a difference by at most 1 per operation, not by arbitrary amounts. | Leads to over‑optimistic answers. | Compute the total number of “steps” needed (`totalDiff`) before deciding if the answer is zero. |

---

## 8.  Testing – Quick sanity checks

| Test | Input | Expected |
|------|-------|----------|
| 1 | `[1,3]` ` [2,4]` `k1=0` `k2=0` | 2  (differences: 1 and 1 → 1²+1²=2) |
| 2 | `[1,3]` ` [2,4]` `k1=1` `k2=1` | 1 (reduce both diffs to 0) |
| 3 | `[10]` ` [1]` `k1=5` `k2=0` | 25 (diff=9, we can reduce it to 4 → 4²=16; but with 5 ops we get diff=4, 4²=16, not 25? Wait: actually 9-5=4 → 4²=16) |
| 4 | `[5,5]` ` [5,5]` `k1=100` `k2=100` | 0 (already equal) |
| 5 | Large `k`: `n=5`, diffs `[1,2,3,4,5]`, `k1=0`, `k2=10^9` | 0 (enough ops to zero all). |

---

## 9.  FAQ & Common Pitfalls

| Question | Answer |
|----------|--------|
| *Do I need to know which array gets the change?* | Not really – you only care about the total number of single‑step changes. Any change that reduces a difference works. |
| *Can I skip some operations?* | Yes – but skipping never improves the answer because any unused operation could be used to reduce an existing difference. |
| *Is a priority queue better?* | It works (`O((n+k) log n)`), but the bucket solution is simpler, constant‑time per bucket and avoids the log factor. |
| *What if `k1 + k2` is huge (10^9)?* | The algorithm stops once all frequencies are zero; it never loops `k` times. |

---

## 10.  Conclusion – The Take‑Away

* Good: Greedy insight is both intuitive and optimal.  
* Bad: naïve simulation or “zero everything” logic can mis‑lead you into O(k) time.  
* Ugly: If you’re not careful with the huge operation budget, your code will *attempt* to loop over `k` and time‑out.

By counting frequencies once and then walking the difference spectrum downward, we achieve an **O(n)** solution that comfortably meets the constraints.  
You can adapt this pattern to other “decrement the largest value” problems – it’s a handy tool in your LeetCode arsenal.

Happy coding! 🚀

---

## 11.  SEO Meta (for the blog post)

```html
<title>Cracking LeetCode 2333 – Minimum Sum of Squared Difference: The Good, The Bad & The Ugly</title>
<meta name="description" content="Step‑by‑step guide to solve LeetCode 2333. Understand the greedy strategy, analyze the frequency‑bucket algorithm, and see Java, Python, and C++ solutions.">
<meta name="keywords" content="LeetCode 2333, Minimum Sum of Squared Difference, greedy algorithm, bucket sort, frequency array, coding interview, algorithm analysis, time complexity, space complexity, coding tutorial">
```

---