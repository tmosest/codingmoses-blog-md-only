---
title: LeetCode 1671. Minimum Number of Removals to Make Mountain Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔍 1671 – Minimum Number of Removals to Make Mountain Array  
### The Complete “Good, the Bad, and the Ugly” Solution  
*(Java | Python | C++)*  

> **TL;DR** –  
> 1. Find the Longest Increasing Subsequence (LIS) ending at each index.  
> 2. Find the Longest Decreasing Subsequence (LDS) starting at each index.  
> 3. For every index that can be a peak (`LIS[i] > 1 && LDS[i] > 1`) compute  
>    `mountainLen = LIS[i] + LDS[i] - 1`.  
> 4. `answer = n - max(mountainLen)` – the minimum removals.  
>  
>  Complexity: **O(n²)** time, **O(n)** space (n ≤ 1000).

---

## 🏔️ Why This Problem Matters for Interviews

- **Mountain Array** – a classic interview pattern: “strictly increasing then strictly decreasing”.  
- Requires *dynamic programming* (LIS/LDS) and a clear understanding of subsequence problems.  
- Tests your ability to handle edge cases (`n = 3`, all equal numbers, etc.).  
- Perfect for showcasing your coding style and problem‑solving approach in a job interview.

---

## 📚 Detailed Walk‑Through

### 1️⃣ Define a Mountain Array

A mountain array `arr` satisfies:

1. `arr.length >= 3`  
2. ∃ index `i` (`0 < i < n-1`) such that  
   `arr[0] < … < arr[i]` **and** `arr[i] > … > arr[n-1]`  

The peak is unique because both sides are strictly monotonic.

### 2️⃣ Observation

If we know for every index:
- How long a strictly increasing subsequence ends at that index (`LIS[i]`).  
- How long a strictly decreasing subsequence starts at that index (`LDS[i]`).  

Then an index can be a peak **iff** `LIS[i] > 1 && LDS[i] > 1`.  
The total length of the mountain that uses `i` as the peak is `LIS[i] + LDS[i] - 1` (the peak is counted twice).

### 3️⃣ DP for LIS and LDS

Because `n <= 1000`, an `O(n²)` DP is perfectly fine.

- **LIS**  
  ```
  LIS[i] = 1
  for j < i:
      if nums[i] > nums[j]: LIS[i] = max(LIS[i], LIS[j] + 1)
  ```

- **LDS** – iterate backwards  
  ```
  LDS[i] = 1
  for j > i:
      if nums[i] > nums[j]: LDS[i] = max(LDS[i], LDS[j] + 1)
  ```

### 4️⃣ Find the Longest Mountain

```
maxLen = 0
for i in 1 … n-2:
    if LIS[i] > 1 and LDS[i] > 1:
        maxLen = max(maxLen, LIS[i] + LDS[i] - 1)
```

### 5️⃣ Final Answer

```
return n - maxLen
```

---

## 📦 Code Snippets

### Java (LeetCode‑friendly)

```java
class Solution {
    public int minimumMountainRemovals(int[] nums) {
        int n = nums.length;
        int[] lis = new int[n];
        int[] lds = new int[n];

        // LIS (forward)
        for (int i = 0; i < n; i++) {
            lis[i] = 1;
            for (int j = 0; j < i; j++) {
                if (nums[i] > nums[j]) {
                    lis[i] = Math.max(lis[i], lis[j] + 1);
                }
            }
        }

        // LDS (backward)
        for (int i = n - 1; i >= 0; i--) {
            lds[i] = 1;
            for (int j = n - 1; j > i; j--) {
                if (nums[i] > nums[j]) {
                    lds[i] = Math.max(lds[i], lds[j] + 1);
                }
            }
        }

        int best = 0;
        for (int i = 1; i < n - 1; i++) {
            if (lis[i] > 1 && lds[i] > 1) {
                best = Math.max(best, lis[i] + lds[i] - 1);
            }
        }
        return n - best;
    }
}
```

### Python 3

```python
class Solution:
    def minimumMountainRemovals(self, nums: List[int]) -> int:
        n = len(nums)
        lis = [1] * n
        lds = [1] * n

        # LIS
        for i in range(n):
            for j in range(i):
                if nums[i] > nums[j]:
                    lis[i] = max(lis[i], lis[j] + 1)

        # LDS
        for i in range(n - 1, -1, -1):
            for j in range(n - 1, i, -1):
                if nums[i] > nums[j]:
                    lds[i] = max(lds[i], lds[j] + 1)

        best = 0
        for i in range(1, n - 1):
            if lis[i] > 1 and lds[i] > 1:
                best = max(best, lis[i] + lds[i] - 1)

        return n - best
```

### C++ (GNU‑C++17)

```cpp
class Solution {
public:
    int minimumMountainRemovals(vector<int>& nums) {
        int n = nums.size();
        vector<int> lis(n, 1), lds(n, 1);

        // LIS
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < i; ++j)
                if (nums[i] > nums[j])
                    lis[i] = max(lis[i], lis[j] + 1);
        }

        // LDS
        for (int i = n - 1; i >= 0; --i) {
            for (int j = n - 1; j > i; --j)
                if (nums[i] > nums[j])
                    lds[i] = max(lds[i], lds[j] + 1);
        }

        int best = 0;
        for (int i = 1; i < n - 1; ++i) {
            if (lis[i] > 1 && lds[i] > 1)
                best = max(best, lis[i] + lds[i] - 1);
        }

        return n - best;
    }
};
```

---

## ⚙️ “Good” – What Works Well

| ✅ Feature | Why It’s Good |
|------------|---------------|
| **Simple O(n²) DP** | Easy to understand, no advanced data structures needed. |
| **Clear Separation** | LIS and LDS computed independently, making debugging trivial. |
| **Deterministic Peak Check** | `LIS[i] > 1 && LDS[i] > 1` guarantees a valid mountain. |
| **Scalable** | `n = 1000` → at most 1,000,000 operations → well under 1 ms. |
| **Portable** | Works in Java, Python, and C++ with minimal changes. |

---

## ⚡ “Bad” – Potential Pain Points

| ⚠️ Issue | Mitigation |
|----------|------------|
| **O(n²) may feel heavy** | For `n = 10⁵` it would fail, but constraints are `≤ 1000`. |
| **Edge cases** | All equal numbers? `LIS` and `LDS` will stay 1 → no peak; problem guarantees solvable, but still good to handle explicitly. |
| **Memory** | `O(n)` arrays are tiny, but if you need `O(n log n)` for larger constraints, use binary search LIS. |

---

## 🧙‍♂️ “Ugly” – Gotchas & Advanced Tweaks

1. **Strictly Increasing / Decreasing** – Make sure *strict* comparison (`>`) is used.  
2. **Peak Must Have Both Sides** – Never consider `i = 0` or `i = n-1` as a peak.  
3. **Off‑by‑One in Length Calculation** – Remember to subtract 1 when summing LIS + LDS because the peak is counted twice.  
4. **Alternative Approach** – A faster `O(n log n)` solution can be built with LIS+reverse‑LIS + binary search, but for interview clarity the quadratic DP is preferred.  
5. **Testing** – Include cases like `[1, 2, 3]` (no decreasing side) and `[3, 2, 1]` (no increasing side) to confirm the algorithm only picks valid peaks.

---

## 📈 SEO‑Optimized Blog Draft

> **Title:**  
> *“Master LeetCode 1671 – Minimum Number of Removals to Make Mountain Array (Java/Python/C++)”*  
>  
> **Meta Description:**  
> Learn how to solve LeetCode 1671 in Java, Python, and C++ using dynamic programming. Understand LIS & LDS, peak selection, and complexity analysis to ace your next coding interview.

### Intro Paragraph
> If you’re prepping for a technical interview, you’ll often hit subsequence problems. One of the trickiest yet most enlightening is LeetCode 1671: *Minimum Number of Removals to Make Mountain Array*. This post walks you through the complete solution, breaking it into the “good, bad, and ugly” parts. Grab the code snippets for Java, Python, and C++—so you can practice on LeetCode or prepare for your next job interview.

### Body Sections
1. **Why Mountain Arrays Are Interview Gold**  
2. **Problem Definition & Constraints**  
3. **Dynamic Programming Insight – LIS & LDS**  
4. **Step‑by‑Step Implementation** (Java, Python, C++)  
5. **Complexity Analysis & Edge Cases**  
6. **“Good / Bad / Ugly” Summary**  
7. **Advanced Variants & Further Reading**  
8. **Practice Tips & Sample Test Cases**  

> **Keywords:**  
> LeetCode 1671, Minimum Mountain Removals, LIS, LDS, Dynamic Programming, Java solution, Python solution, C++ solution, interview coding patterns, coding interview tips, coding challenge.

### Call‑to‑Action

> *“Want more LeetCode solutions? Subscribe to our newsletter and get weekly coding challenges delivered straight to your inbox!”*

---

## 🚀 How to Use This Post in Your Job Hunt

1. **Show the Code** – Paste the language‑specific snippet on your GitHub profile or a personal blog.  
2. **Explain the Approach** – In an interview, start with the mountain definition → LIS/LDS → peak check.  
3. **Discuss the “Bad” & “Ugly” parts** – Mention constraints, why strict comparisons matter, and how to handle edge cases.  
4. **Mention the Alternative** – Briefly note the `O(n log n)` option; you can say you chose the quadratic DP for clarity.  
5. **Wrap Up** – Summarize the answer and complexity to reinforce your understanding.

---

## 🎉 Final Takeaway

LeetCode 1671 is a *dynamic‑programming showcase* disguised as a subsequence puzzle. By computing LIS and LDS and validating peaks, you can finish with the minimal number of deletions in a clean, interview‑ready manner. Use the code above, test it thoroughly, and you’ll impress recruiters with both your technical depth and clarity of thought.  

Happy coding! 🚀

--- 

**Note:** All code snippets compile on LeetCode’s standard environment. Feel free to copy‑paste and test. Happy interviewing!