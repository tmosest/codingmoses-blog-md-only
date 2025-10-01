---
title: LeetCode 2200. Find All K-Distant Indices in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Find All K‑Distant Indices in an Array – Java / Python / C++ – Plus a Job‑Ready Blog Post

> **Problem** – LeetCode 2200  
> “Given an array `nums`, a value `key`, and an integer `k`, return all indices `i` such that there exists a `j` with `|i – j| ≤ k` and `nums[j] == key`. The result must be sorted in increasing order.”

Below you’ll find:

| Language | Implementation |
|----------|----------------|
| **Java** | ✔️ |
| **Python** | ✔️ |
| **C++** | ✔️ |

After the code, a **SEO‑optimized blog article** explains the solution, highlights the “good, bad, ugly” points, and tells you how to use it in a technical interview.

---

## 1. Code – One‑Pass O(n) Solution

### 1.1 Java

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public List<Integer> findKDistantIndices(int[] nums, int key, int k) {
        List<Integer> res = new ArrayList<>();
        int n = nums.length;
        int nextUnmarked = 0;                     // first index not yet added to res

        for (int j = 0; j < n; j++) {
            if (nums[j] == key) {
                int left  = Math.max(nextUnmarked, j - k);
                int right = Math.min(n - 1, j + k);

                // add the whole segment [left, right]
                for (int i = left; i <= right; i++) {
                    res.add(i);
                }
                // all indices up to right are now marked
                nextUnmarked = right + 1;
            }
        }
        return res;
    }
}
```

### 1.2 Python

```python
from typing import List

class Solution:
    def findKDistantIndices(self, nums: List[int], key: int, k: int) -> List[int]:
        res = []
        n = len(nums)
        next_unmarked = 0                     # first index not yet added

        for j, val in enumerate(nums):
            if val == key:
                left = max(next_unmarked, j - k)
                right = min(n - 1, j + k)

                res.extend(range(left, right + 1))
                next_unmarked = right + 1

        return res
```

### 1.3 C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> findKDistantIndices(vector<int>& nums, int key, int k) {
        vector<int> res;
        int n = nums.size();
        int nextUnmarked = 0;                 // first index not yet added

        for (int j = 0; j < n; ++j) {
            if (nums[j] == key) {
                int left  = max(nextUnmarked, j - k);
                int right = min(n - 1, j + k);

                for (int i = left; i <= right; ++i)
                    res.push_back(i);

                nextUnmarked = right + 1;
            }
        }
        return res;
    }
};
```

> All three solutions run in **O(n)** time and **O(1)** additional space (besides the output list). They avoid duplicate indices by tracking `nextUnmarked`, which is the first position that hasn't yet been added to `res`.

---

## 2. Blog Article – “The Good, The Bad, and The Ugly” of K‑Distant Indices

> **Title (SEO‑friendly):**  
> *“LeetCode 2200 – Find All K‑Distant Indices in an Array: A Clean, Interview‑Ready Solution in Java, Python, & C++”*

> **Meta Description (≈155 chars):**  
> *“Solve LeetCode 2200 with an O(n) algorithm. Get Java, Python, and C++ code, complexity analysis, and interview insights. Impress hiring managers in tech interviews.”*

---

### 2.1 Introduction

When you land a technical interview, the recruiter will want you to:

1. **Understand the problem** (array + distance logic).  
2. **Think of a naive solution** and improve it.  
3. **Explain your optimization** in simple terms.

LeetCode’s “Find All K‑Distant Indices” is a perfect playground for showcasing array traversal, sliding‑window ideas, and time‑space trade‑offs. Below, I break the solution into three parts: *Good*, *Bad*, and *Ugly* – all with the aim of impressing hiring managers at top tech companies.

---

### 2.2 Good – Why This Problem is a Sweet Spot for Interviews

| Why it’s good | What it shows |
|---------------|---------------|
| **Intuitive Intuition** – A key at position `j` “covers” a continuous segment `[j-k, j+k]`. | Understanding of distance and absolute value. |
| **Simple Data Structures** – One pass over an integer array; no hash tables, no recursion. | Comfort with arrays and loops – a core skill for most interviews. |
| **Deterministic Output** – Sorted list, no duplicates. | Shows awareness of edge cases and data integrity. |
| **Scalable** – O(n) runs fast even for the 1e5‑size limits typical in coding contests. | Efficiency mindset: you can always beat O(n²). |

> **Takeaway for your resume:**  
> “Implemented an O(n) solution for LeetCode 2200, reducing runtime from quadratic to linear by tracking the first unmarked index.”

---

### 2.3 Bad – The Pitfalls of the Naïve Approach

A *brute‑force* solution iterates over **every pair of indices**:

```python
for i in range(n):
    for j in range(n):
        if nums[j] == key and abs(i - j) <= k:
            add i to result
            break
```

* **Time Complexity:** O(n²) – acceptable only for tiny arrays.  
* **Risk of TLE (Time Limit Exceeded)** – LeetCode will mark this as “Wrong on Large Input.”  
* **Duplicate Work:** The inner loop keeps checking already‑validated indices.

> **Interview‑wise:** If you present this first, ask the interviewer if they expect a linear solution. If they do, this will quickly turn into a “time‑out” conversation, not a success story.

---

### 2.4 Ugly – Edge‑Case Headaches & Common Mistakes

| Scenario | Mistake | Fix |
|----------|---------|-----|
| `j - k` < 0 | Using `-1` or negative indices in `range`/loops. | Clamp with `max(0, j - k)`. |
| `j + k` ≥ `n` | Out‑of‑bounds indices added. | Clamp with `min(n-1, j + k)`. |
| Overlap of key positions | Duplicated indices in the result list. | Track `nextUnmarked` (or `r` in the editorial) to skip already added segments. |
| Sortedness | Adding indices in arbitrary order when iterating `nums`. | Add ranges in ascending order – our one‑pass algorithm naturally keeps `res` sorted. |

> **Bottom line:** Off‑by‑one bugs or neglecting bounds are the *ugly* part. The pointer trick (`nextUnmarked`) eliminates these and keeps the code clean.

---

### 2.5 Algorithmic Insight – One‑Pass with Interval Merging

1. **Scan left‑to‑right.**  
2. **When `nums[j] == key`:**  
   * Compute `[max(0, j - k), min(n-1, j + k)]`.  
   * Merge it with the last segment (if overlapping).  
3. **Append all new indices** from the merged segment to the result.  
4. **Advance `nextUnmarked`** to `right + 1`.

> This is essentially a *sliding‑window* where the window is defined by the distance `k`. No need for a hash map or a 2‑D DP matrix – a linear pass is enough.

---

### 2.6 Complexity Breakdown

| Approach | Time | Extra Space | Pros | Cons |
|----------|------|-------------|------|------|
| Brute‑Force | O(n²) | O(1) | Easy to understand | Too slow for LeetCode’s constraints |
| One‑Pass Marking | O(n) | O(1) | Linear, no duplicates, sorted | Slightly more bookkeeping (`nextUnmarked`) |

> In an interview, always start by describing the brute‑force idea. Then ask if the interviewer wants a better solution; once they do, pivot to the linear approach.

---

### 2.7 “Good, Bad, Ugly” Summary

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | Linear, clear logic | O(n²) brute‑force | Off‑by‑one & duplicate indices |
| **Coding** | One pass, minimal state | Must track bounds manually | Forgetting to clamp left/right |
| **Interview** | Shows optimization mindset | Demonstrates naïveté if left unoptimized | Could lead to TLE or WA if sloppy |

> **Job Interview Tip:**  
> Frame your answer as:  
> *“I first considered the straightforward O(n²) solution, but realized that each key can affect a contiguous interval. By merging overlapping intervals during a single pass, I reduced the time to O(n) while keeping the output sorted.”*  
> This narrative shows both awareness of pitfalls and the ability to solve them efficiently.

---

## 3. How to Use This in Your Next Technical Interview

1. **Explain the intuition**: “Each `key` position covers a continuous segment of indices.”  
2. **Show the two approaches**: start with brute‑force, then explain why it’s too slow.  
3. **Present the one‑pass algorithm** with pseudocode.  
4. **Highlight time/space complexity** (O(n) time, O(1) extra space).  
5. **Mention edge cases** (bounds, duplicates).  
6. **Show a clean implementation** in the language of your choice (Java, Python, or C++).  

> *Result*: Interviewers will see you can think both naively and optimally, you can write clean code, and you understand common pitfalls.

---

### 4. Next Steps

- **Practice**: Run the solution on LeetCode’s “Easy” and “Medium” examples.  
- **Benchmark**: Compare your code’s runtime against the naive version on random inputs up to 10⁵ elements.  
- **Explain on a whiteboard**: Try to describe the one‑pass interval‑merging technique in 2 minutes—this is a perfect 1‑minute interview warm‑up.

---

## 🎯 Final Thoughts

This problem is a **micro‑case study** of algorithmic optimization that you can pull straight into a coding interview. By mastering the one‑pass O(n) solution and being able to articulate its strengths, weaknesses, and edge cases, you demonstrate:

- **Array manipulation skill**  
- **Complexity analysis**  
- **Clean coding practices** in Java, Python, and C++  
- **Interview‑ready communication**  

Good luck, and may your job search be as fast as the solution above!