---
title: LeetCode 354. Russian Doll Envelopes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 354 – Russian Doll Envelopes  
### Problem & Why It Matters in Interviews

> **LeetCode 354 – Russian Doll Envelopes**  
> **Difficulty:** Hard  
> **Tags:** Array, Sort, Dynamic Programming, Binary Search, Longest Increasing Subsequence (LIS)

You’re given an array of envelopes, each represented as `[width, height]`.  
One envelope can fit into another only if **both** its width *and* height are strictly larger.  
Your task: **find the maximum number of envelopes that can be nested like Russian dolls**.

> **Why interviewers love this problem**
> * It blends 2‑D sorting with 1‑D LIS – a classic “reduce to LIS” trick.  
> * It tests your ability to think about how to eliminate a dimension.  
> * It’s a good showcase of algorithmic elegance (n log n) vs. brute‑force DP (n²).  
> * It’s frequently asked in top‑tier companies (FAANG, Google, Microsoft, etc.).  

If you master this, you’ll have a talking‑point that demonstrates:
* **Data‑structure knowledge** (sorting, binary search).  
* **Algorithmic design** (reduction, LIS).  
* **Code quality** (clean, language‑agnostic, well‑commented).

Below you’ll find a **complete solution in Java, Python, and C++** along with a **SEO‑friendly blog article** you can publish on LinkedIn, Medium, or your personal portfolio to land interviews.

---

## 1. High‑Level Solution Overview

1. **Sort Envelopes**  
   * Sort by **width ascending**.  
   * For equal widths, sort by **height descending**.  
   * Reason: After sorting, if we only consider heights, we’ll never double‑count envelopes with the same width (since they can’t fit each other).  
2. **Apply LIS on Heights**  
   * After sorting, the problem reduces to finding the Longest Increasing Subsequence of the heights.  
   * Use an **O(n log n)** LIS implementation (binary search over a “dp” array).  

Result: The length of the LIS is the maximum number of nested envelopes.

---

## 2. Edge Cases & “The Ugly”

| Case | What goes wrong if you ignore it? |
|------|-----------------------------------|
| **Duplicate envelopes** | Without height descending, two envelopes `[5,4]` and `[5,4]` could be counted twice. |
| **Large input (10⁵)** | O(n²) DP would time‑out; need the binary‑search LIS. |
| **All widths equal** | Sorted heights in descending order ensures LIS size = 1. |
| **All heights equal** | Same reasoning as above. |
| **Non‑positive dimensions** | Constraints say ≥1, but defensive checks are good practice. |

---

## 3. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sorting | **O(n log n)** | **O(1)** (in‑place) |
| LIS (binary search) | **O(n log n)** | **O(n)** (dp array) |
| **Total** | **O(n log n)** | **O(n)** |

For `n = 10⁵`, this comfortably fits in the time limits of most online judges.

---

## 4. Code Implementations

> All three solutions are functionally identical.  
> They follow the same pattern: sort, run LIS, return length.  
> Comments explain the key steps.

### 4.1 Java (LeetCode‑style)

```java
import java.util.*;

class Solution {
    public int maxEnvelopes(int[][] envelopes) {
        // 1. Sort: width asc, height desc for equal widths
        Arrays.sort(envelopes, (a, b) -> {
            if (a[0] != b[0]) return Integer.compare(a[0], b[0]);
            return Integer.compare(b[1], a[1]);  // descending height
        });

        // 2. LIS on heights
        int[] dp = new int[envelopes.length];
        int size = 0;

        for (int[] env : envelopes) {
            int h = env[1];
            int idx = Arrays.binarySearch(dp, 0, size, h);
            if (idx < 0) idx = -(idx + 1); // insertion point
            dp[idx] = h;
            if (idx == size) size++;
        }
        return size;
    }
}
```

### 4.2 Python 3

```python
from bisect import bisect_left
from typing import List

class Solution:
    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        # Sort by width ascending; height descending for equal widths
        envelopes.sort(key=lambda x: (x[0], -x[1]))

        dp = []                     # tails of LIS
        for _, h in envelopes:
            idx = bisect_left(dp, h)
            if idx == len(dp):
                dp.append(h)
            else:
                dp[idx] = h
        return len(dp)
```

### 4.3 C++ (GNU++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        // Sort: width asc, height desc for same width
        sort(envelopes.begin(), envelopes.end(),
            [](const vector<int>& a, const vector<int>& b) {
                if (a[0] != b[0]) return a[0] < b[0];
                return a[1] > b[1];      // descending height
            });

        // LIS on heights using binary search
        vector<int> dp;               // tails
        for (auto &env : envelopes) {
            int h = env[1];
            auto it = lower_bound(dp.begin(), dp.end(), h);
            if (it == dp.end())
                dp.push_back(h);
            else
                *it = h;
        }
        return dp.size();
    }
};
```

> **Note**: All three implementations run in **O(n log n)** time and **O(n)** auxiliary space.

---

## 5. Blog Article – “The Good, the Bad, and the Ugly of Russian Doll Envelopes”

### Meta Description
> Master LeetCode 354 – Russian Doll Envelopes. Learn the elegant O(n log n) solution, pitfalls, and full Java/Python/C++ code. Boost your coding interview prep and land your next software engineering job!

---

### Introduction

If you’re preparing for coding interviews, **LeetCode 354 – Russian Doll Envelopes** is a must‑solve problem. It looks simple but hides a beautiful reduction to the Longest Increasing Subsequence (LIS). In this post we’ll walk through:

- The core intuition behind the **“good”** approach.
- Common mistakes that become the **“bad”** pitfalls.
- Edge cases and subtle bugs – the notorious **“ugly”** traps.

By the end you’ll own the solution, know why it works, and be ready to discuss it confidently in an interview.

---

### Problem Recap (Quick!)

Given `envelopes[i] = [w, h]`, return the maximum number of envelopes you can nest such that each inner envelope is strictly smaller in both dimensions. Rotations are forbidden.

**Constraints**  
- 1 ≤ `envelopes.length` ≤ 10⁵  
- 1 ≤ `w, h` ≤ 10⁵  

---

### The Good – A Clean O(n log n) Solution

#### 1. Reduce 2‑D to 1‑D
Sort envelopes by width ascending. If two envelopes share the same width, sort by height descending. This ensures that when we later apply LIS on heights, envelopes with the same width cannot both be counted (they can’t fit each other).  
> **Why descending?**  
> Suppose we had `[5,4]` and `[5,6]`. After a normal ascending sort they would appear as `[5,4], [5,6]`. The height sequence `4, 6` would incorrectly allow an LIS of length 2. By ordering `[5,6], [5,4]`, the height sequence `6, 4` forces LIS to be `1`.

#### 2. Longest Increasing Subsequence (LIS) on Heights
Now the problem is identical to finding the LIS of the height array. Use the classic patience sorting algorithm:
- Keep a `dp` array where `dp[i]` is the smallest possible tail of an increasing subsequence of length `i+1`.  
- For each height `h`, binary‑search the first `dp[j] >= h` and replace it with `h`.  
- If no such `dp[j]` exists, append `h`.  

The length of `dp` at the end is the answer.

**Pseudo‑code**

```
sort(envelopes, key=(width, -height))
dp = []
for _, h in envelopes:
    i = lower_bound(dp, h)
    if i == len(dp): dp.append(h)
    else: dp[i] = h
return len(dp)
```

#### 3. Why It’s O(n log n)
- Sorting: O(n log n).  
- LIS: Each element triggers one binary search → O(n log n).  
Total: O(n log n). Perfect for n = 10⁵.

---

### The Bad – Common Pitfalls

| Mistake | Symptom | Fix |
|---------|---------|-----|
| **Sorting only by width** | Duplicate widths create false LIS. | Sort by width asc, height desc. |
| **Using DP O(n²)** | TLE on 10⁵ envelopes. | Switch to LIS with binary search. |
| **Off‑by‑one in binary search** | Wrong tail updates, answer too small. | Use `lower_bound` (first `>=`). |
| **Assuming heights are unique** | Still works, but be careful with duplicates. | LIS handles duplicates fine (strictly increasing). |
| **Ignoring negative indices** | Crash when binary search returns negative. | Translate negative insertion point: `idx = -(idx + 1)` in Java/C++. |

---

### The Ugly – Edge Cases & Hidden Traps

1. **All envelopes identical** – `[1,1] × n`.  
   *Sorted heights*: `1,1,1,…`. LIS → 1. Good.

2. **All widths equal, heights descending** – e.g., `[(2,10), (2,9), …]`.  
   *Sorted heights*: `10,9,…`. LIS → 1. Good.

3. **Mixed large numbers** – Up to 10⁵.  
   No overflow risk in 32‑bit int; use long if you suspect larger constraints.

4. **Empty input** – Though constraints say ≥1, defensive coding is always good: return 0.

5. **Duplicate widths but different heights** – The height‑descending trick is essential.

---

### Full Code (Pick Your Language)

> **Java**
> ```java
> class Solution {
>     public int maxEnvelopes(int[][] envelopes) {
>         Arrays.sort(envelopes, (a, b) -> a[0] == b[0] ? b[1] - a[1] : a[0] - b[0]);
>         int[] dp = new int[envelopes.length];
>         int size = 0;
>         for (int[] env : envelopes) {
>             int h = env[1];
>             int idx = Arrays.binarySearch(dp, 0, size, h);
>             if (idx < 0) idx = -(idx + 1);
>             dp[idx] = h;
>             if (idx == size) size++;
>         }
>         return size;
>     }
> }
> ```

> **Python**
> ```python
> class Solution:
>     def maxEnvelopes(self, envelopes):
>         envelopes.sort(key=lambda x: (x[0], -x[1]))
>         dp = []
>         for _, h in envelopes:
>             i = bisect_left(dp, h)
>             if i == len(dp): dp.append(h)
>             else: dp[i] = h
>         return len(dp)
> ```

> **C++**
> ```cpp
> class Solution {
> public:
>     int maxEnvelopes(vector<vector<int>>& envelopes) {
>         sort(envelopes.begin(), envelopes.end(),
>              [](auto& a, auto& b){ return a[0] == b[0] ? a[1] > b[1] : a[0] < b[0]; });
>         vector<int> dp;
>         for (auto& e : envelopes) {
>             int h = e[1];
>             auto it = lower_bound(dp.begin(), dp.end(), h);
>             if (it == dp.end()) dp.push_back(h);
>             else *it = h;
>         }
>         return dp.size();
>     }
> };
> ```

---

### Why You Should Master This

- **Algorithmic depth** – Demonstrates understanding of sorting tricks and LIS.  
- **Complexity analysis** – Shows you can handle large inputs.  
- **Multi‑language readiness** – Interviewers may ask you to write in any language.  

Be ready to explain every line:  
- Why we sort widths ascending?  
- Why we reverse heights for equal widths?  
- How the patience sorting algorithm guarantees the minimal tail?  
- What guarantees the strictness of the subsequence?  

---

### Final Thoughts

LeetCode 354 is a classic problem that tests two essential skills: **problem reduction** and **efficient implementation**. By mastering the O(n log n) LIS solution, you not only get a correct answer but also gain a conversation starter that showcases elegant algorithm design.

Good luck, and may your next interview be filled with fewer “bad” surprises and more “good” confidence!

---

### Call to Action

- **Practice**: Run the provided code on the LeetCode platform.  
- **Discuss**: Bring up this problem in your next interview; it’s a great way to demonstrate algorithmic thinking.  
- **Build**: Add the solution to your GitHub repo and tag it `#leetcode354`.

---

### References

- [LeetCode 354 – Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/)
- [Longest Increasing Subsequence – Wikipedia](https://en.wikipedia.org/wiki/Longest_increasing_subsequence)
- [Patience Sorting Algorithm](https://cp-algorithms.com/sequences/longest_increasing_subsequence.html)

---

> **Author**: [Your Name], Senior Software Engineer & Interview Coach  
> **Follow me on LinkedIn** for more interview prep articles.

--- 

### End of Blog

---

## 6. How to Use This Post for Interview Prep

1. **Read the article** – Understand the intuition and the pitfalls.  
2. **Implement the code** – Practice typing it from scratch in your preferred language.  
3. **Explain aloud** – Pretend you’re talking to an interviewer; explain sorting, descending trick, LIS, and complexity.  
4. **Tackle Edge Cases** – Show you can think ahead: identical envelopes, duplicate widths, etc.  
5. **Mock Interview** – Pair with a friend or use a platform like Pramp or Interviewing.io.

Good luck, and may you land that dream **software engineering** position! 🚀

---

### Conclusion

- **Good**: Clean O(n log n) algorithm via sorting + LIS.  
- **Bad**: Common mistakes – wrong sorting, O(n²) DP, binary search errors.  
- **Ugly**: Edge cases with duplicate widths, large numbers, defensive coding.

The full code snippets (Java, Python, C++) and the in‑depth article above give you every tool you need to ace **LeetCode 354** and impress interviewers. Happy coding!

--- 

### End of Blog

---

**Happy Interviewing!**