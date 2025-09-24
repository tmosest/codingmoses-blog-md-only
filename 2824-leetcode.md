---
title: LeetCode 2824. Count Pairs Whose Sum is Less than Target - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2824 – Count Pairs Whose Sum is Less than Target  
**Java | Python | C++ implementations + an SEO‑friendly blog post**  

---

### 1. Code Solutions

Below are three fully‑commented, ready‑to‑copy solutions for **Java 17**, **Python 3.10+**, and **C++17**. All use the optimal **O(n log n)** two‑pointer strategy after sorting the array.

---

#### 1.1 Java

```java
import java.util.Arrays;
import java.util.List;

public class Solution {

    /**
     * Count the number of index pairs (i, j) such that i < j and nums[i] + nums[j] < target.
     *
     * @param nums   List of integers (0‑indexed).
     * @param target Target sum threshold.
     * @return Number of valid pairs.
     */
    public int countPairs(List<Integer> nums, int target) {
        // Convert List to array for in‑place sorting
        int n = nums.size();
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) arr[i] = nums.get(i);

        // 1. Sort ascending
        Arrays.sort(arr);

        // 2. Two‑pointer scan
        int left = 0, right = n - 1, count = 0;
        while (left < right) {
            if (arr[left] + arr[right] < target) {
                // All pairs (left, left+1 … right) are valid
                count += right - left;
                left++;           // move left pointer to try a larger left element
            } else {
                right--;          // shrink the sum from the right side
            }
        }
        return count;
    }
}
```

---

#### 1.2 Python

```python
from typing import List

class Solution:
    def countPairs(self, nums: List[int], target: int) -> int:
        """
        Count pairs (i, j) with i < j and nums[i] + nums[j] < target.
        Uses sorting + two pointers for O(n log n) time.
        """
        nums.sort()                     # O(n log n)
        left, right, count = 0, len(nums) - 1, 0

        while left < right:
            if nums[left] + nums[right] < target:
                count += right - left    # all pairs with current left are valid
                left += 1
            else:
                right -= 1

        return count
```

---

#### 1.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countPairs(vector<int>& nums, int target) {
        // 1. Sort the array
        sort(nums.begin(), nums.end());          // O(n log n)

        int left = 0;
        int right = nums.size() - 1;
        long long count = 0;                      // use long long for safety

        // 2. Two‑pointer traversal
        while (left < right) {
            if (nums[left] + nums[right] < target) {
                count += right - left;           // all pairs with current left
                ++left;
            } else {
                --right;
            }
        }
        return static_cast<int>(count);
    }
};
```

---

## 2. Blog Post – “The Good, The Bad, and The Ugly of LeetCode 2824”

> **Keywords**: Count Pairs Whose Sum is Less than Target, LeetCode 2824, two pointers, algorithm interview, Java solution, Python solution, C++ solution, coding interview prep, optimal algorithm, sorting, brute force, binary search, interview coding challenge.

---

### Introduction

If you’ve been hunting for that *“just the right”* LeetCode problem to showcase your algorithmic chops on your résumé, **LeetCode 2824 – Count Pairs Whose Sum is Less than Target** is a sweet spot. It’s **easy** yet demonstrates mastery of classic techniques: sorting, two‑pointer traversal, and complexity analysis. In this article, we’ll walk through the problem, discuss the optimal approach, highlight pitfalls (“bad” bits), and show how to turn the solution into a conversation star during interviews (“ugly” is usually the missing link).

---

### Problem Statement

> *Given an integer array `nums` and an integer `target`, count how many pairs `(i, j)` with `0 <= i < j < n` satisfy `nums[i] + nums[j] < target`.*

Constraints are tiny (`n ≤ 50`) but that hides the fact that a naïve `O(n²)` brute force is still acceptable. However, interviewers love seeing you solve it in **`O(n log n)`**.

---

### Good – Why the Two‑Pointer Trick Rocks

1. **Intuitive After Sorting**  
   When the array is sorted, if `nums[left] + nums[right]` is already < `target`, *every* element between `left` and `right` will pair with `nums[right]` to stay below `target`. This allows us to add `right - left` pairs in one step.

2. **Linear Scan Post‑Sort**  
   After sorting (`O(n log n)`), we only traverse the array once (`O(n)`), making the algorithm fast and memory‑friendly (`O(1)` auxiliary space if in‑place sort is used).

3. **Extensibility**  
   The same template solves variants:  
   - Count pairs with sum ≤ `target`.  
   - Count pairs whose sum is within a range (`[low, high]`).  
   - Find the k‑th smallest pair sum.

4. **Clear Code**  
   A concise implementation is easy to understand, debug, and prove correct with a small proof by induction or loop invariant.

---

### Bad – Common Mistakes & How to Avoid Them

| Mistake | Why It Happens | Fix |
|---------|----------------|-----|
| **O(n²) Brute Force** | Misinterpreting “easy” as “just loop over all pairs.” | Use the two‑pointer logic once the array is sorted. |
| **Sorting Each Test Case Separately** | Forgetting that the function can be called multiple times in a test harness. | Sort inside the function – the cost is negligible for `n ≤ 50`. |
| **Using `int` for Count on Large Inputs** | `n` can be up to `50`, but if constraints change (e.g., `n = 10⁵`) overflow occurs. | Use `long long` / `long` for count. |
| **Mis‑handling Negative Numbers** | Thinking that negative numbers break the two‑pointer logic. | The logic holds because sorting orders negatives correctly. |
| **Off‑By‑One Errors in Pointers** | Incrementing `left` before counting. | Count first (`count += right - left`) *then* increment `left`. |

---

### Ugly – Things Interviewers Like to Probe

1. **Proof of Correctness**  
   Interviewers often ask: *Why does adding `right - left` guarantee all those pairs are valid?*  
   **Answer:** In a sorted array, any element between `left` and `right` is ≥ `nums[left]` and ≤ `nums[right]`. If `nums[left] + nums[right] < target`, then for any `k` with `left < k < right`, `nums[k] + nums[right]` ≤ `nums[right] + nums[right]` but still < `target` because `nums[k]` ≥ `nums[left]`. Thus all those pairs are valid.

2. **Complexity Tweaks**  
   Show that the sorting dominates the runtime (`O(n log n)`), while the two‑pointer part is linear (`O(n)`).

3. **Edge Cases**  
   *Empty array?* *All elements equal?* *All sums already below target?* Demonstrate that the algorithm handles them gracefully.

4. **Space‑Optimized Alternatives**  
   If `nums` cannot be sorted in place, discuss using `std::nth_element` in C++ or copying into a new list in Python to keep the original intact.

5. **Variations**  
   Ask “What if we wanted to find the *k* smallest pair sums?” – pivot into a min‑heap approach or a binary‑search over possible sums.

---

### Step‑by‑Step Implementation (Java)

```java
public int countPairs(List<Integer> nums, int target) {
    int n = nums.size();
    int[] a = new int[n];
    for (int i = 0; i < n; i++) a[i] = nums.get(i);
    Arrays.sort(a);

    int left = 0, right = n - 1, count = 0;
    while (left < right) {
        if (a[left] + a[right] < target) {
            count += right - left;
            left++;
        } else {
            right--;
        }
    }
    return count;
}
```

*You can copy the Java code, run it on LeetCode’s test harness, and the `O(n log n)` solution will win the “optimal” badge.*

---

### Why This Problem Is a “Show‑Stopper” in Your Interview

- **Demonstrates Classic Skill**: Sorting + two pointers → a staple in the “Top 3 interview tricks” list.  
- **Scales to Larger Constraints**: If the interviewer changes `n` to `10⁵`, your logic still works and you can discuss a `O(n log n)` solution.  
- **Versatile for Discussion**: You can pivot to related problems such as “Sum of Pair Sums” or “Find Median Pair Sum.”  
- **Talk About Test‑Driven Development**: Write a few unit tests before coding – a habit that impresses senior engineers.

---

### Closing Thoughts

LeetCode 2824 may look trivial at first glance, but it’s a **gold‑mine** for interview prep. Master the two‑pointer method, know how to justify its correctness, anticipate the “bad” pitfalls, and bring the “ugly” discussion points to the table. Once you nail this problem, add a line to your résumé:

> **Solved LeetCode 2824 – Count Pairs Whose Sum is Less than Target** in *Java, Python, and C++* with an optimal `O(n log n)` algorithm.

That small line says you can *sort, think linearly, and write clean code* – exactly what interviewers want. Good luck! 🚀

---

### Meta‑Information (for SEO)

```html
<title>LeetCode 2824 – Count Pairs Whose Sum is Less than Target | Java, Python, C++ Solutions</title>
<meta name="description" content="Solve LeetCode 2824 – Count Pairs Whose Sum is Less than Target using the optimal two‑pointer algorithm. Java, Python, and C++ code snippets, interview tips, and complexity analysis.">
<meta name="keywords" content="LeetCode 2824, Count Pairs Whose Sum is Less than Target, two pointers, sorting algorithm, coding interview, Java solution, Python solution, C++ solution, algorithm interview prep, optimal time complexity, interview coding challenge">
```

---

### Quick Reference: Full Code Snippets (copy‑paste ready)

| Language | File | Function |
|----------|------|----------|
| **Java** | `Solution.java` | `public int countPairs(List<Integer> nums, int target)` |
| **Python** | `solution.py` | `def countPairs(self, nums: List[int], target: int) -> int:` |
| **C++** | `solution.cpp` | `int countPairs(vector<int>& nums, int target)` |

Feel free to tweak the code for your preferred coding environment. Happy coding and good luck in your next interview!