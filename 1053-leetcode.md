---
title: LeetCode 1053. Previous Permutation With One Swap - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1053. Previous Permutation With One Swap – A Complete Guide  
*(Java | Python | C++)*  

> Want to ace the *Previous Permutation With One Swap* question in a LeetCode interview?  
> Here’s a **step‑by‑step** walk‑through, a clear algorithm, three production‑ready solutions, and a SEO‑optimized blog post that will get your résumé noticed by recruiters.  

---

### 🚀 TL;DR  

* **Problem** – Find the lexicographically largest permutation smaller than the given array using **exactly one swap**.  
* **Core idea** – Scan from the right to locate the first “dip” (`arr[i] < arr[i‑1]`).  
* **Key swap** – Choose the right‑most element that is **smaller** than the dip and **largest** among such candidates (to keep the result as big as possible).  
* **Complexity** – **O(n)** time, **O(1)** extra space.  
* **Edge cases** – Already the smallest permutation, duplicate values, single‑element arrays.  

---

## 📚 Problem Statement (LeetCode 1053)

> Given an array of positive integers `arr` (not necessarily distinct), return the lexicographically largest permutation that is smaller than `arr`, that can be made with exactly one swap.  
> If it cannot be done, then return the same array.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[3,2,1]` | `[3,1,2]` | Swap `2` and `1`. |
| `[1,1,5]` | `[1,1,5]` | Already the smallest permutation. |
| `[1,9,4,6,7]` | `[1,7,4,6,9]` | Swap `9` and `7`. |

---

## 🔎 The “Good, Bad & Ugly” Approach

|  | **Good** | **Bad** | **Ugly** |
|---|----------|---------|----------|
| **Algorithmic Design** | A single pass from right to left to find the *first decreasing index*. | A double‑loop that checks every pair – `O(n²)` – wastes time. | Brute‑force permutation generation and sorting – impossible for `n ≤ 10⁴`. |
| **Handling Duplicates** | Keep track of the *right‑most* candidate to avoid swapping equal numbers that yield the same array. | Ignoring duplicates may produce the wrong index or no swap. | Swapping the first occurrence of a smaller number might produce the *smallest* possible permutation, not the largest. |
| **Complexity** | `O(n)` time, `O(1)` space – optimal for interview. | `O(n²)` time, still passes small tests but not acceptable for large inputs. | `O(n!)` time – astronomically slow. |
| **Code Simplicity** | One helper loop, one swap – easy to read. | Many nested loops, unclear intent. | Over‑engineering with unnecessary helper functions. |

---

## 🧠 Core Algorithm (Pseudocode)

```
1. Find i = last index such that arr[i] < arr[i-1].
   If none exists → array is already the smallest; return arr.

2. In the suffix arr[i … end], find the element
   x that is < arr[i-1] and as large as possible.
   If duplicates of x exist, pick the leftmost (closest to i-1)
   to ensure the final array is the largest.

3. Swap arr[i-1] and x.

4. Return arr.
```

Why does this work?  
The first decreasing position is the rightmost place where the array can be made smaller. To keep the result as large as possible, we swap the *largest* smaller element from the suffix, and we choose the **right‑most** occurrence of that element to avoid unnecessary reductions caused by duplicates.

---

## 📦 Solutions

### Java – `Solution.java`

```java
import java.util.*;

class Solution {
    public int[] prevPermOpt1(int[] arr) {
        int n = arr.length;
        int i = n - 1;

        // 1. Find the first decreasing pair from the right.
        while (i > 0 && arr[i - 1] <= arr[i]) i--;

        if (i == 0) return arr;          // already minimal

        int pivot = i - 1;

        // 2. Find the rightmost element < arr[pivot] and as large as possible.
        int targetIdx = -1;
        int targetVal = Integer.MIN_VALUE;

        for (int j = pivot + 1; j < n; j++) {
            if (arr[j] < arr[pivot] && arr[j] > targetVal) {
                targetVal = arr[j];
                targetIdx = j;
            }
        }

        // 3. Swap
        int tmp = arr[pivot];
        arr[pivot] = arr[targetIdx];
        arr[targetIdx] = tmp;

        return arr;
    }

    // Optional main for quick manual testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(Arrays.toString(s.prevPermOpt1(new int[]{1, 9, 4, 6, 7})));
    }
}
```

**Explanation**  
*The loop `while (i > 0 && arr[i-1] <= arr[i]) i--`*  
gives the rightmost dip.  
*The inner loop* keeps the best candidate (`targetVal`) that is smaller than the pivot.  
Finally we perform the swap.

---

### Python – `solution.py`

```python
from typing import List

class Solution:
    def prevPermOpt1(self, arr: List[int]) -> List[int]:
        n = len(arr)
        i = n - 1

        # 1. Locate the first decreasing pair.
        while i > 0 and arr[i - 1] <= arr[i]:
            i -= 1

        if i == 0:
            return arr        # Already the smallest permutation.

        pivot = i - 1

        # 2. Find the rightmost element < arr[pivot] with maximum value.
        target_idx = -1
        target_val = -1
        for j in range(pivot + 1, n):
            if arr[j] < arr[pivot] and arr[j] > target_val:
                target_val = arr[j]
                target_idx = j

        # 3. Swap
        arr[pivot], arr[target_idx] = arr[target_idx], arr[pivot]
        return arr

# Optional quick test
if __name__ == "__main__":
    s = Solution()
    print(s.prevPermOpt1([1, 9, 4, 6, 7]))
```

---

### C++ – `solution.cpp`

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> prevPermOpt1(std::vector<int>& arr) {
        int n = arr.size();
        int i = n - 1;

        // 1. Find the first decreasing pair from the right.
        while (i > 0 && arr[i - 1] <= arr[i]) --i;
        if (i == 0) return arr;  // already minimal

        int pivot = i - 1;

        // 2. Find the best candidate to swap with.
        int targetIdx = -1;
        int targetVal = -1;
        for (int j = pivot + 1; j < n; ++j) {
            if (arr[j] < arr[pivot] && arr[j] > targetVal) {
                targetVal = arr[j];
                targetIdx = j;
            }
        }

        // 3. Swap
        std::swap(arr[pivot], arr[targetIdx]);
        return arr;
    }
};
```

**Tip:** The algorithm runs in *O(n)* time and uses *O(1)* extra space, perfect for interview constraints.

---

## 📝 Blog Post Draft

---

### Title  
**LeetCode 1053: Previous Permutation With One Swap – Master the O(n) Solution (Java, Python, C++)**  

### Meta Description  
Learn how to solve LeetCode 1053, “Previous Permutation With One Swap,” in Java, Python, and C++. Get the full algorithm, edge‑case handling, and a job‑ready blog post with SEO keywords.

### Introduction  
When interviewers hand you LeetCode’s *Previous Permutation With One Swap*, they’re testing your ability to reason about permutations, handle duplicates, and write clean code. This article explains the problem, walks through a greedy algorithm, and gives you three production‑ready implementations. Whether you’re a Java developer, a Pythonista, or a C++ guru, you’ll walk away with a code snippet you can drop into your next interview or portfolio.

### Problem Overview  
*You’re given an array of positive integers. You must produce the lexicographically largest array that is strictly smaller than the original, using exactly one swap. If no such swap exists, return the original array.*  

### Core Insight – “First Decreasing Index”  
The rightmost place where the array decreases (`arr[i] < arr[i‑1]`) is where you can make the array smaller. Anything to its left is already optimal; anything to its right must be altered to get the largest possible result.  

### Step‑by‑Step Algorithm  
1. **Scan from the end** to find the first index `i` where `arr[i-1] > arr[i]`.  
2. If no such `i` exists, the array is already the smallest permutation—return it.  
3. In the suffix `arr[i … end]`, find the element that is  
   * smaller than `arr[i-1]` **and** the largest possible among such elements.  
4. **Swap** `arr[i-1]` with that element.  

### Why This Works  
The first decreasing pair guarantees the array becomes smaller. By picking the *largest* smaller element from the suffix, we keep the prefix (which determines lexicographic order) as large as possible. Choosing the rightmost occurrence handles duplicates safely: if the same number appears multiple times, swapping with the leftmost one would leave a larger number later in the array, which is never desirable.

### Handling Edge Cases  
| Case | What to Do | Why |
|------|------------|-----|
| **Already Minimal** | Return original array | No decreasing pair exists |
| **Duplicates** | Pick rightmost candidate | Avoid unnecessary reductions |
| **Single Element** | Return as‑is | No swap possible |

### Complexity Analysis  
* **Time** – `O(n)` (one pass to find the dip, another pass to find the candidate).  
* **Space** – `O(1)` (in‑place modification).  

### Code Walkthrough – Java  

*(Include the Java snippet from earlier with line‑by‑line comments.)*  

### Code Walkthrough – Python  

*(Include the Python snippet from earlier with concise comments.)*  

### Code Walkthrough – C++  

*(Include the C++ snippet from earlier with comments.)*  

### What Interviewers Are Looking For  
*Recognition of the greedy pattern, correct handling of duplicates, and clean, testable code.*  

### Quick Test Cases  

| Input | Expected Output |
|-------|-----------------|
| `[3,2,1]` | `[3,1,2]` |
| `[1,1,5]` | `[1,1,5]` |
| `[1,9,4,6,7]` | `[1,7,4,6,9]` |
| `[1]` | `[1]` |
| `[2,2,1,2]` | `[2,1,2,2]` |

Run the provided `main`/`__main__` snippets to see them in action.

### SEO & Job‑Search Friendly Tips  
- Use keywords: **“LeetCode 1053,” “Previous Permutation With One Swap,” “Java interview question,” “Python coding challenge,” “C++ array permutation.”**  
- Add a link to your GitHub repo (e.g., `https://github.com/yourname/leetcode-solutions`).  
- Offer a downloadable PDF of the solution for recruiters who prefer quick references.  
- Mention your “O(n) optimal solution” and “handles duplicates”—traits recruiters love.

### Final Words  
You’ve now dissected LeetCode’s 1053 into its simplest form. Pull any of the three snippets into your portfolio, include the algorithm explanation in your résumé, and feel confident tackling this permutation problem in your next coding interview.

--- 

### Conclusion  
*With the algorithm and three clean implementations at hand, you can confidently claim victory over LeetCode 1053. Happy coding, and good luck on your next interview!*  

---

This draft blends a detailed algorithmic explanation with interview‑focused commentary and SEO‑optimized content. Feel free to adjust the tone or add visual diagrams to match your personal blog style.  

---

## 🎯 Final Takeaway

- **Problem**: Largest permutation strictly smaller than the original with one swap.  
- **Solution**: Find rightmost dip, swap with largest smaller suffix element.  
- **Complexity**: Optimal `O(n)` time, `O(1)` space.  
- **Code**: Java, Python, C++ snippets ready for interviews or portfolios.  
- **Blog**: A ready‑to‑publish draft with SEO keywords and edge‑case coverage.

Good luck, and may your interviewers be impressed by both your code and the blog post you’ll proudly showcase! 🚀