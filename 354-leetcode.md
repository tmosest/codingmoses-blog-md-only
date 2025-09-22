---
title: LeetCode 354. Russian Doll Envelopes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1️⃣ Problem Recap  
**LeetCode 354 – Russian Doll Envelopes (Hard)**  

> **Definition**  
> You’re given a list of envelopes, each represented as `[width, height]`.  
> One envelope can be put inside another *only* if **both** its width and height are strictly smaller.  
> **Goal:** Find the maximum number of envelopes that can be nested one inside another.

> **Constraints**  
> * `1 ≤ envelopes.length ≤ 10⁵`  
> * `1 ≤ wi, hi ≤ 10⁵`  

---

## 2️⃣ Why the Classic DP (O(n²)) Fails  
A straightforward dynamic‑programming approach:

```text
dp[i] = 1 + max{ dp[j] | j < i and wj < wi and hj < hi }
```

This runs in `O(n²)` time, which is *acceptable* for `n` up to a few thousand but blows up for the 100,000‑element constraint.  
**Bottom‑line:** We need an `O(n log n)` solution.

---

## 3️⃣ The Insight – Reduce to Longest Increasing Subsequence (LIS)

1. **Sort envelopes**  
   * Primary key: **ascending width** (`w`)  
   * Secondary key: **descending height** (`h`) when widths are equal  

   Why descending height?  
   If two envelopes have the same width, only the *taller* one can ever be nested after the *shorter* one – the shorter one cannot go into the taller one because the widths are equal.  
   Sorting in descending height ensures that for equal widths we never mistakenly consider a later envelope as “smaller”.

2. **Extract the heights**  
   After sorting, the problem of nesting reduces to selecting an increasing sequence of heights – exactly the **LIS** problem.

3. **Compute LIS in O(n log n)**  
   The classic patience‑sorting (or “tails” array) method gives us the length of the LIS in `O(n log n)`.

---

## 4️⃣ The Code

Below are clean, ready‑to‑copy implementations in **Java**, **Python**, and **C++**.

> 👉 **Tip** – All three solutions use the same strategy:
> * Sort → Pull the heights → LIS with binary search.

---

### 4.1 Java (Java 17+)

```java
import java.util.Arrays;
import java.util.Comparator;

public class Solution {
    // O(n log n) LIS
    private int lengthOfLIS(int[] nums) {
        int[] tails = new int[nums.length];
        int size = 0;
        for (int x : nums) {
            int idx = Arrays.binarySearch(tails, 0, size, x);
            if (idx < 0) idx = -(idx + 1);   // insertion point
            tails[idx] = x;
            if (idx == size) size++;
        }
        return size;
    }

    public int maxEnvelopes(int[][] envelopes) {
        // 1) sort by width asc, height desc
        Arrays.sort(envelopes, new Comparator<int[]>() {
            @Override
            public int compare(int[] a, int[] b) {
                if (a[0] == b[0]) return b[1] - a[1];
                return a[0] - b[0];
            }
        });

        // 2) extract heights
        int[] heights = new int[envelopes.length];
        for (int i = 0; i < envelopes.length; i++) {
            heights[i] = envelopes[i][1];
        }

        // 3) LIS on heights
        return lengthOfLIS(heights);
    }
}
```

---

### 4.2 Python (Python 3.8+)

```python
from bisect import bisect_left
from typing import List

class Solution:
    def length_of_lis(self, nums: List[int]) -> int:
        tails = []
        for x in nums:
            i = bisect_left(tails, x)
            if i == len(tails):
                tails.append(x)
            else:
                tails[i] = x
        return len(tails)

    def maxEnvelopes(self, envelopes: List[List[int]]) -> int:
        # 1) sort by width asc, height desc
        envelopes.sort(key=lambda x: (x[0], -x[1]))
        # 2) heights
        heights = [h for _, h in envelopes]
        # 3) LIS
        return self.length_of_lis(heights)
```

---

### 4.3 C++ (C++17)

```cpp
#include <algorithm>
#include <vector>
using namespace std;

class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        // 1) sort
        sort(envelopes.begin(), envelopes.end(),
            [](const vector<int>& a, const vector<int>& b) {
                if (a[0] == b[0]) return a[1] > b[1]; // height descending
                return a[0] < b[0];                   // width ascending
            });

        // 2) extract heights
        vector<int> heights;
        heights.reserve(envelopes.size());
        for (const auto& e : envelopes)
            heights.push_back(e[1]);

        // 3) LIS (tails method)
        vector<int> tails;
        for (int h : heights) {
            auto it = lower_bound(tails.begin(), tails.end(), h);
            if (it == tails.end())
                tails.push_back(h);
            else
                *it = h;
        }
        return static_cast<int>(tails.size());
    }
};
```

---

## 5️⃣ The Good, the Bad, and the Ugly

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Algorithmic elegance** | Sorting + LIS is a classic “reduction” trick; one line of code later gives you the answer. | None, the optimal solution is clear. | If you forget the *descending* height tie‑break, you’ll get wrong answers on edge cases like `[[5,4],[5,3]]`. |
| **Complexity** | `O(n log n)` time, `O(n)` space – perfect for `n = 10⁵`. | A naïve `O(n²)` DP would kill you on large test cases. | Mis‑counting envelopes when heights are equal (e.g., all envelopes `[1,1]`) – the LIS will still be 1, but you might over‑think it. |
| **Readability** | The code is short, idiomatic, and well‑documented. | None – the implementation is straightforward. | Debugging LIS when you use binary search incorrectly can produce subtle off‑by‑one bugs. |
| **Interview‑friendly** | It shows you know both sorting tricks and LIS. | If you hand‑write an `O(n²)` DP, the interviewer may dismiss you. | Mixing the sort comparator syntax incorrectly (e.g., `a[1] - b[1]` instead of `b[1] - a[1]`) leads to subtle comparator bugs that are hard to spot. |
| **Testing** | Edge cases: duplicate widths, duplicate heights, single envelope, all envelopes identical. | Forget to test the case where widths are sorted but heights are *increasing* vs *decreasing*. | Hard‑to‑replicate bug: using `Arrays.binarySearch` without handling negative indices properly in Java. |

**Takeaway:** Master the *tie‑break* trick. Once you nail that, the rest is just a one‑liner LIS.

---

## 6️⃣ SEO‑Optimized Blog Hook

**Title:**  
> *“LeetCode 354 – Russian Doll Envelopes: A Step‑by‑Step O(n log n) Guide (Java, Python, C++)”*

**Meta Description:**  
> Learn how to solve LeetCode 354 “Russian Doll Envelopes” with an elegant O(n log n) algorithm. Read the full Java, Python, and C++ implementations, plus a deep dive into the algorithmic trade‑offs. Perfect for your next coding interview!

**Keywords (30‑40 words):**  
Russian Doll Envelopes, LeetCode 354, Longest Increasing Subsequence, LIS, O(n log n) algorithm, binary search, dynamic programming, interview coding, Java solution, Python solution, C++ solution, sorting trick, coding interview prep, algorithmic trading, data structures, problem solving.

**Headings:**

1. Introduction
2. Why the naive O(n²) DP Fails
3. Reduce the Problem to LIS
4. The Three Implementation Languages
5. The Good, the Bad, and the Ugly
6. Common Pitfalls & Debugging Tips
7. Final Thoughts & Interview Strategy

---

## 7️⃣ Final Words – Landing the Job

- **Show the reduction**: Explain *why* sorting by width and descending height turns the 2‑D problem into a 1‑D LIS.  
- **Mention complexity**: Interviewers love seeing you think about `O(n log n)` vs. `O(n²)`.  
- **Discuss edge cases**: Prove you understand the subtlety of duplicates.  
- **Present the code**: Offer clean Java/Python/C++ snippets, as above.  
- **Talk about testing**: Outline the test matrix (single envelope, all equal, etc.).  

Remember: the interview is not only about getting the right answer but also about communicating your thought process. Use this problem as a showcase of *algorithmic insight*, *efficient coding*, and *clean implementation*—exactly what hiring managers are looking for.

Good luck! 🚀