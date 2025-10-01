---
title: LeetCode 2970. Count the Number of Incremovable Subarrays I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Master LeetCode 2970 – Count the Number of Incremovable Subarrays  
**Java | Python | C++** – A full‑stack solution, SEO‑friendly blog article and interview‑ready explanation

> **TL;DR** – The array length ≤ 50, so an \(O(n^3)\) brute force pass is perfect.  
> But we’ll also show an \(O(n^2)\) trick that uses prefix/suffix “is‑strictly‑increasing” checks.  
> The blog below explains the problem, why the brute force works, the optimized idea, and how to talk about it in a job interview.

---

## 📑 Problem Overview (LeetCode 2970)

> **Definition**  
> A subarray of `nums` is *incremovable* if, after deleting that subarray, the remaining array becomes **strictly increasing**.  
> The array may become empty – an empty array is considered strictly increasing.

| Input | Output | Why |
|-------|--------|-----|
| `[1,2,3,4]` | `10` | Every subarray keeps the array increasing. |
| `[6,5,7,8]` | `7` | Subarrays that preserve the strict order are listed in the statement. |
| `[8,7,6,6]` | `3` | `[8,7]` fails because `[6,6]` is not strictly increasing. |

**Constraints**

* `1 ≤ nums.length ≤ 50`
* `1 ≤ nums[i] ≤ 50`

> **Why it matters for interviews**  
> 1️⃣ It tests array manipulation, 2️⃣ It forces you to think about *removal* as a “gap” operation, 3️⃣ It’s a classic *prefix‑suffix* problem.

---

## 🧠 Brute‑Force Solution (O(n³))

The naive approach enumerates every possible subarray \([i, j]\) and then checks if the remaining elements are strictly increasing.

```text
for i in [0..n-1]:
    for j in [i..n-1]:
        last = -∞
        ok   = true
        for k in [0..n-1] if k < i or k > j:
            if last >= nums[k]:
                ok = false; break
            last = nums[k]
        if ok: answer++
```

* **Why it works** – Every possible deletion is tried and verified.  
* **Complexity** –  
  *Time*: \(O(n^3)\) – 125 000 operations for \(n = 50\).  
  *Space*: \(O(1)\).

> **Good** – Easy to implement, no tricky math.  
> **Bad** – Slightly heavy for larger `n` (if constraints changed).  
> **Ugly** – The inner‑loop check feels like a “hand‑rolled” brute force that interviewers sometimes want to see simplified.

---

## 💡 Optimized Approach (O(n²))

Because `n` is tiny, the \(O(n^3)\) is sufficient, but a cleaner \(O(n^2)\) approach demonstrates deep understanding:

1. **Pre‑compute** `pref[i]` – whether `nums[0..i]` is strictly increasing.  
   `pref[i] = pref[i‑1] && nums[i-1] < nums[i]`.
2. **Pre‑compute** `suff[i]` – whether `nums[i..n-1]` is strictly increasing.  
   `suff[i] = suff[i+1] && nums[i] < nums[i+1]`.
3. For each possible removal `[l, r]`, the remaining array is strictly increasing **iff**:
   * `pref[l-1]` is true (prefix before `l` is increasing) **or** `l == 0`.
   * `suff[r+1]` is true (suffix after `r` is increasing) **or** `r == n-1`.
   * The “border” condition:  
     `nums[l-1] < nums[r+1]` (when both sides exist).

```text
for l in [0..n-1]:
    for r in [l..n-1]:
        ok = true
        if l > 0: ok &= pref[l-1]
        if r < n-1: ok &= suff[r+1]
        if l > 0 && r < n-1: ok &= nums[l-1] < nums[r+1]
        if ok: answer++
```

* **Why it’s better** –  
  *You only traverse the array once for prefixes, once for suffixes, and then two nested loops for possible removals.*  
* **Complexity** –  
  *Time*: \(O(n^2)\).  
  *Space*: \(O(n)\) for the two helper arrays.

> **Good** – Shows use of prefix/suffix logic.  
> **Bad** – Requires careful border handling.  
> **Ugly** – Slightly more code than brute force; risk of off‑by‑one errors.

---

## 📦 Code Implementations

Below are clean, ready‑to‑run implementations for **Java**, **Python**, and **C++** that use the **brute‑force** approach (fits LeetCode constraints perfectly) and an **optimized** variant for extra credit.

### 1. Java

```java
import java.util.*;

public class Solution {
    /* Brute‑force (O(n³)) – passes LeetCode constraints (n <= 50) */
    public int incremovableSubarrayCount(int[] nums) {
        int n = nums.length, ans = 0;
        for (int l = 0; l < n; ++l) {
            for (int r = l; r < n; ++r) {
                if (isIncreasingAfterRemoval(nums, l, r)) ans++;
            }
        }
        return ans;
    }

    private boolean isIncreasingAfterRemoval(int[] a, int l, int r) {
        int last = Integer.MIN_VALUE;          // sentinel
        for (int i = 0; i < a.length; ++i) {
            if (i >= l && i <= r) continue;    // skip removed segment
            if (last >= a[i]) return false;    // not strictly increasing
            last = a[i];
        }
        return true;                           // remains strictly increasing
    }

    /* Optional: Optimized O(n²) version using prefixes/suffixes */
    public int incremovableSubarrayCountOptimized(int[] nums) {
        int n = nums.length, ans = 0;
        boolean[] pref = new boolean[n];
        boolean[] suff = new boolean[n];

        pref[0] = true;
        for (int i = 1; i < n; ++i)
            pref[i] = pref[i-1] && nums[i-1] < nums[i];

        suff[n-1] = true;
        for (int i = n-2; i >= 0; --i)
            suff[i] = suff[i+1] && nums[i] < nums[i+1];

        for (int l = 0; l < n; ++l) {
            for (int r = l; r < n; ++r) {
                boolean ok = true;
                if (l > 0) ok &= pref[l-1];
                if (r < n-1) ok &= suff[r+1];
                if (l > 0 && r < n-1) ok &= nums[l-1] < nums[r+1];
                if (ok) ans++;
            }
        }
        return ans;
    }
}
```

---

### 2. Python

```python
class Solution:
    # Brute force (O(n³)), simple and fast enough for n <= 50
    def incremovableSubarrayCount(self, nums: list[int]) -> int:
        n, ans = len(nums), 0
        for l in range(n):
            for r in range(l, n):
                if self._is_increasing_after_removal(nums, l, r):
                    ans += 1
        return ans

    @staticmethod
    def _is_increasing_after_removal(nums, l, r):
        last = float('-inf')
        for i, val in enumerate(nums):
            if l <= i <= r:
                continue
            if last >= val:
                return False
            last = val
        return True

    # Optimized version (O(n²))
    def incremovableSubarrayCountOptimized(self, nums: list[int]) -> int:
        n = len(nums)
        pref = [True] * n
        for i in range(1, n):
            pref[i] = pref[i-1] and nums[i-1] < nums[i]

        suff = [True] * n
        for i in range(n-2, -1, -1):
            suff[i] = suff[i+1] and nums[i] < nums[i+1]

        ans = 0
        for l in range(n):
            for r in range(l, n):
                ok = True
                if l > 0: ok &= pref[l-1]
                if r < n-1: ok &= suff[r+1]
                if l > 0 and r < n-1: ok &= nums[l-1] < nums[r+1]
                if ok:
                    ans += 1
        return ans
```

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    /* Brute‑force (O(n³)) – works for n <= 50 */
    int incremovableSubarrayCount(vector<int>& nums) {
        int n = nums.size(), ans = 0;
        for (int l = 0; l < n; ++l) {
            for (int r = l; r < n; ++r) {
                if (isIncreasingAfterRemoval(nums, l, r)) ans++;
            }
        }
        return ans;
    }

private:
    bool isIncreasingAfterRemoval(const vector<int>& a, int l, int r) {
        int last = INT_MIN;                     // sentinel
        for (int i = 0; i < (int)a.size(); ++i) {
            if (i >= l && i <= r) continue;     // skip segment
            if (last >= a[i]) return false;     // not strictly increasing
            last = a[i];
        }
        return true;
    }

public:
    /* Optional: Optimized O(n²) using prefix / suffix arrays */
    int incremovableSubarrayCountOptimized(vector<int>& nums) {
        int n = nums.size(), ans = 0;
        vector<bool> pref(n), suff(n);

        pref[0] = true;
        for (int i = 1; i < n; ++i)
            pref[i] = pref[i-1] && nums[i-1] < nums[i];

        suff[n-1] = true;
        for (int i = n-2; i >= 0; --i)
            suff[i] = suff[i+1] && nums[i] < nums[i+1];

        for (int l = 0; l < n; ++l) {
            for (int r = l; r < n; ++r) {
                bool ok = true;
                if (l > 0) ok &= pref[l-1];
                if (r < n-1) ok &= suff[r+1];
                if (l > 0 && r < n-1) ok &= nums[l-1] < nums[r+1];
                if (ok) ans++;
            }
        }
        return ans;
    }
};
```

---

## 📈 Complexity Analysis

| Variant | Time | Space |
|---------|------|-------|
| Brute‑force | \(O(n^3)\)  (≤ 125 000 ops for `n=50`) | \(O(1)\) |
| Optimized | \(O(n^2)\)  | \(O(n)\) (prefix + suffix arrays) |

*Both variants produce the same answer; choose the one you feel most comfortable explaining.*

---

## ⚠️ Edge Cases & Testing Tips

| Test | Reason |
|------|--------|
| `[5]` | Removing `[5]` leaves empty → valid. |
| `[5,5]` | Only `[5,5]` (removing all) works. |
| `[10,1,2,3]` | Removing `[10]` fails because the first element is > `1`. |
| `[1,2,2,3,4]` | `[2,2]` cannot be kept; check border logic. |

*Always test the border condition (`nums[l-1] < nums[r+1]`) when **both** sides exist.*

---

## 📢 Talking About This Problem in an Interview

1. **Start with the intuition** – “We delete a slice; we’re left with a prefix and a suffix that must be compatible.”
2. **Explain the brute‑force** – “We enumerate all deletions and test the rest for strictness.”
3. **Show the prefix‑suffix optimization** – “We pre‑compute whether the left side is already increasing and whether the right side is. Then we just check the boundary condition.”
4. **Mention constraints** – “Because `n` ≤ 50, the cubic solution is fast enough, but the quadratic trick demonstrates deeper array‑technique mastery.”
5. **Finish with a quick demo** – “Here’s a clean Java implementation I’d use on LeetCode.”

---

## 🎯 Interview Takeaways

| Skill | How the Problem Tests It |
|-------|--------------------------|
| **Array traversal** | Deleting a subarray creates a “gap” – think of it as two independent runs. |
| **Prefix / Suffix** | Compute monotonicity in linear passes; typical interview pattern. |
| **Boundary logic** | Off‑by‑one errors happen often – practice `l‑1`, `r+1` conditions. |
| **Time‑space trade‑offs** | Show you can pick \(O(n^3)\) for small constraints but know how to improve. |

**Interview‑style answer snippet**

> *“I first solved it with a straightforward \(O(n^3)\) approach, which is actually ideal for the problem’s constraints. Then I realized we can pre‑compute the increasing prefixes and suffixes and collapse the check into \(O(n^2)\). This is a textbook prefix‑suffix problem that showcases both brute‑force understanding and clever optimization.”*

---

## 📌 SEO & Keywords for the Blog

* LeetCode 2970  
* Count the Number of Incremovable Subarrays  
* Prefix‑Suffix Array Problem  
* Brute‑Force Array Manipulation  
* Optimized O(n²) Array Problem  
* Java, Python, C++ LeetCode solutions  
* Interview Array Manipulation Questions  
* Strictly Increasing Array  
* Coding Interview Array Deletion  

---

## 🎉 Ready to Rock Your Interview?

*Copy the code, run the tests, and practice explaining the two solution strategies.*  
When you see a problem about *removal*, remember the **gap trick** – it’s a pattern that shows up in many interview puzzles (subarray removal, sliding window, segment deletion, etc.).

Good luck – you’ve got this! 🌟