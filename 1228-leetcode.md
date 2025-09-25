---
title: LeetCode 1228. Missing Number In Arithmetic Progression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1228. Missing Number in Arithmetic Progression  
**Difficulty** – Easy  

> **Given** an array `arr` that originally formed an arithmetic progression, one element was removed (but it was *not* the first or the last element).  
> **Return** the missing number.

| Example | Input | Output | Explanation |
|--------|-------|--------|-------------|
| 1 | `[5, 7, 11, 13]` | `9` | The original array was `[5, 7, 9, 11, 13]`. |
| 2 | `[15, 13, 12]` | `14` | The original array was `[15, 14, 13, 12]`. |

> **Constraints**
> - `3 ≤ arr.length ≤ 1000`
> - `0 ≤ arr[i] ≤ 10^5`
> - The input is guaranteed to be a valid array (exactly one element is missing, and it is not the first or the last).

---

## Why This Problem Is Great for Interview Prep

| Good | Bad | Ugly |
|------|-----|------|
| **Simplicity** – only integer arithmetic and a single pass is required. | **Tricky corner‑case** – the array of length 3 needs a special handle. | **Misleading intuition** – many people try to compute the average difference first, which can lead to off‑by‑one bugs. |
| **Time Complexity** – `O(n)` with constant extra space, perfect for coding‑skill demonstration. | **Large input ranges** – values can reach `10^5`; beware of integer overflow when computing differences (not an issue in 32‑bit but keep it in mind). | **Hidden assumption** – “missing element is not first or last” – forgetting it can give wrong answer. |
| **Versatility** – same logic applies to both ascending and descending APs. | | |

---

## Solution Overview

1. **Find the correct common difference `d`**  
   * Look at the first three differences: `d1 = arr[1]-arr[0]`, `d2 = arr[2]-arr[1]`, `d3 = arr[3]-arr[2]` (if it exists).  
   * The correct `d` is the one that appears at least twice.  
   * For `n = 3` we simply compute `d = (arr[2] - arr[0]) / 2`.

2. **Locate the missing element**  
   * Iterate over the array.  
   * When `arr[i+1] - arr[i] != d`, the missing number is `arr[i] + d`.

3. **Return the missing number**  

The algorithm is `O(n)` time, `O(1)` extra space, and works for both increasing and decreasing progressions.

---

## Code Implementations

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++**.

---

### Java (LeetCode‑compatible)

```java
class Solution {
    public int missingNumber(int[] arr) {
        int n = arr.length;

        // Special case: only 3 numbers
        if (n == 3) {
            int d = (arr[2] - arr[0]) / 2;
            return arr[0] + d;
        }

        // Find the correct common difference
        int d1 = arr[1] - arr[0];
        int d2 = arr[2] - arr[1];
        int d3 = arr[3] - arr[2];
        int d = (d1 == d2) ? d1 : (d1 == d3) ? d1 : d2;

        // Locate the missing number
        for (int i = 0; i < n - 1; i++) {
            if (arr[i + 1] - arr[i] != d) {
                return arr[i] + d;
            }
        }

        // Should never reach here for a valid input
        throw new IllegalArgumentException("Invalid input");
    }
}
```

---

### Python 3

```python
class Solution:
    def missingNumber(self, arr: List[int]) -> int:
        n = len(arr)

        # Handle length 3 specially
        if n == 3:
            d = (arr[2] - arr[0]) // 2
            return arr[0] + d

        # Determine the correct common difference
        d1 = arr[1] - arr[0]
        d2 = arr[2] - arr[1]
        d3 = arr[3] - arr[2]
        d = d1 if d1 == d2 else (d1 if d1 == d3 else d2)

        # Find the missing element
        for i in range(n - 1):
            if arr[i + 1] - arr[i] != d:
                return arr[i] + d

        raise ValueError("Invalid input")
```

---

### C++ (C++17)

```cpp
class Solution {
public:
    int missingNumber(vector<int>& arr) {
        int n = arr.size();

        // Special case for 3 elements
        if (n == 3) {
            int d = (arr[2] - arr[0]) / 2;
            return arr[0] + d;
        }

        // Compute the common difference
        int d1 = arr[1] - arr[0];
        int d2 = arr[2] - arr[1];
        int d3 = arr[3] - arr[2];
        int d = (d1 == d2) ? d1 : (d1 == d3) ? d1 : d2;

        // Locate the missing number
        for (int i = 0; i < n - 1; ++i) {
            if (arr[i + 1] - arr[i] != d) {
                return arr[i] + d;
            }
        }

        throw invalid_argument("Invalid input");
    }
};
```

---

## SEO‑Optimized Blog Article

> **Title**: *LeetCode 1228 – Missing Number in Arithmetic Progression: Java, Python, C++ Solutions + Interview Insights*  

> **Meta Description**: Master LeetCode 1228 with clean Java, Python, and C++ code. Understand the problem, solve it in O(n) time, and learn how this question can ace your software‑engineering interview.

---

### What Makes LeetCode 1228 a Must‑Know Problem?

- **Easy Difficulty** – but *highly* valuable for interview prep.
- **Real‑World Scenario** – arithmetic progressions show up in data‑analysis pipelines, signal processing, and financial forecasting.
- **Compact Solution** – only a few lines of code once you know the trick.

---

### The Three Pillars of the Solution

1. **Identifying the Correct Common Difference**  
   *Because one element is missing, one of the differences is split.*  
   The trick is to look at the first three differences; the correct difference will appear at least twice.

2. **Handling the Edge Case of Length 3**  
   *When only three numbers remain, you can recover the missing element by simple averaging.*

3. **Linear Scan to Find the Gap**  
   *Once you know the difference, a single pass tells you exactly where the missing number sits.*

---

### Why This Problem Is a “Job‑Interview Booster”

- **Demonstrates Clean Code** – a short, readable solution signals good coding discipline.
- **Shows Problem‑Solving** – you’re able to spot the hidden “split” in the differences.
- **Versatile** – works for ascending or descending sequences, proving you understand integer math fully.

---

### Common Mistakes to Avoid

| Mistake | Fix |
|---------|-----|
| Using the average of all differences (`sum / (n-1)`) | Use the *majority* difference among the first three. |
| Forgetting the `n == 3` case | Compute `d = (arr[2] - arr[0]) / 2`. |
| Assuming the array is sorted ascending only | Work with signed differences; the logic remains the same. |

---

### Quick Reference – Code Snippets

| Language | Function Signature | Complexity |
|----------|--------------------|------------|
| **Java** | `public int missingNumber(int[] arr)` | `O(n)`, `O(1)` |
| **Python** | `def missingNumber(self, arr: List[int]) -> int` | `O(n)`, `O(1)` |
| **C++** | `int missingNumber(vector<int>& arr)` | `O(n)`, `O(1)` |

---

### Final Takeaway

LeetCode 1228 is a textbook example of turning a seemingly tricky “missing element” problem into a clean, `O(n)` solution. Master it, add the code to your portfolio, and you’ll have a conversation starter for your next software‑engineering interview. Good luck, and keep coding! 🚀

---