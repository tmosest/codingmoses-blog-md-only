---
title: LeetCode 1574. Shortest Subarray to be Removed to Make Array Sorted - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1574 – “Shortest Subarray to be Removed to Make Array Sorted”

> *Problem*: Given an integer array `arr`, remove one contiguous sub‑array (may be empty) so that the remaining elements are in non‑decreasing order. Return the length of the shortest sub‑array that can be removed.

| Difficulty | Medium | Constraints |
|------------|--------|-------------|
| n | 1 ≤ arr.length ≤ 10⁵ | 0 ≤ arr[i] ≤ 10⁹ |
| Time | O(n) | O(1) extra space |

Below are production‑ready solutions in **Java**, **Python** and **C++**.  
After the code we’ll dive into a full‑blown blog article that explains the *good, the bad, and the ugly* of this problem – ideal for polishing your interview notes or writing a job‑seeking article.

---

## 🔧 1. Java Solution

```java
import java.util.*;

class Solution {
    public int findLengthOfShortestSubarray(int[] arr) {
        int n = arr.length;
        // 1️⃣ Longest non‑decreasing prefix
        int left = 0;
        while (left + 1 < n && arr[left] <= arr[left + 1]) left++;

        // The array is already sorted
        if (left == n - 1) return 0;

        // 2️⃣ Longest non‑decreasing suffix
        int right = n - 1;
        while (right > 0 && arr[right - 1] <= arr[right]) right--;

        // 3️⃣ Start with the two obvious candidates
        int ans = Math.min(n - left - 1, right);

        // 4️⃣ Two‑pointer merge: keep prefix part of length i,
        //    keep suffix part starting at j, and check the gap
        int i = 0, j = right;
        while (i <= left && j < n) {
            if (arr[i] <= arr[j]) {               // can keep arr[i] and arr[j]
                ans = Math.min(ans, j - i - 1);   // delete middle
                i++;                              // try a longer prefix
            } else {                              // need to move suffix pointer
                j++;
            }
        }
        return ans;
    }
}
```

> **Complexity**:  
> • Time – **O(n)** (three linear scans)  
> • Space – **O(1)**

---

## 🔧 2. Python Solution

```python
from typing import List

class Solution:
    def findLengthOfShortestSubarray(self, arr: List[int]) -> int:
        n = len(arr)

        # Longest non‑decreasing prefix
        left = 0
        while left + 1 < n and arr[left] <= arr[left + 1]:
            left += 1
        if left == n - 1:                 # already sorted
            return 0

        # Longest non‑decreasing suffix
        right = n - 1
        while right > 0 and arr[right - 1] <= arr[right]:
            right -= 1

        ans = min(n - left - 1, right)    # two trivial deletions

        i, j = 0, right
        while i <= left and j < n:
            if arr[i] <= arr[j]:
                ans = min(ans, j - i - 1)
                i += 1
            else:
                j += 1
        return ans
```

> **Complexity**  
> • Time – **O(n)**  
> • Space – **O(1)**

---

## 🔧 3. C++ Solution

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findLengthOfShortestSubarray(vector<int>& arr) {
        int n = arr.size();
        // Longest non‑decreasing prefix
        int left = 0;
        while (left + 1 < n && arr[left] <= arr[left + 1]) left++;

        if (left == n - 1) return 0; // already sorted

        // Longest non‑decreasing suffix
        int right = n - 1;
        while (right > 0 && arr[right - 1] <= arr[right]) right--;

        int ans = min(n - left - 1, right); // two simple deletions

        // Two‑pointer merge
        int i = 0, j = right;
        while (i <= left && j < n) {
            if (arr[i] <= arr[j]) {
                ans = min(ans, j - i - 1);
                ++i;
            } else {
                ++j;
            }
        }
        return ans;
    }
};
```

> **Complexity**  
> • Time – **O(n)**  
> • Space – **O(1)**

---

## 📚 4. Blog Article – “The Good, the Bad & the Ugly of LeetCode 1574”

> **Title**: *The Good, the Bad & the Ugly of LeetCode 1574 – Shortest Subarray to be Removed to Make Array Sorted*  
> **Meta Description**: Master LeetCode 1574 with a deep dive into its optimal solution, pitfalls, and interview‑ready explanation. Ideal for software engineers looking to impress hiring managers.

---

### 1️⃣ Introduction

When recruiters screen for algorithmic sharpness, they often give you problems that *look* easy but conceal hidden pitfalls. **LeetCode 1574** is one such problem: *Remove a sub‑array to make the remaining array non‑decreasing.* On the surface it feels like a simple “remove the biggest bad segment” question, but the optimal solution hinges on a two‑pointer merge and careful handling of edge cases.

In this article we’ll:

- Explain why a naïve approach fails  
- Show the optimal **O(n)** solution in 3 languages  
- Discuss the *good, the bad, and the ugly* from an interview perspective  
- Provide “take‑away” interview tips that you can mention on the phone

---

### 2️⃣ The Problem in Plain English

You’re given an integer array `arr`. You can delete *one* contiguous sub‑array (the elements you delete don’t have to be contiguous in the final array, they’re simply removed). After the deletion, the **remaining elements must be sorted in non‑decreasing order**.

Your task: return the **minimum number of elements** you have to delete.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1, 2, 3, 10, 4, 2, 3, 5]` | `3` | Delete `[10, 4, 2]` → `[1, 2, 3, 3, 5]`. |
| `[5, 4, 3, 2, 1]` | `4` | The array is strictly decreasing; keep any single element. |
| `[1, 2, 3]` | `0` | Already sorted. |

---

### 3️⃣ Why Naïve Solutions Struggle

A common first thought: *“Find the longest sorted prefix and suffix, then delete everything in between.”*  
This gives you an answer of `right - left - 1`, but it’s not always optimal. For instance:

```
arr = [1, 3, 5, 2, 4, 6]
```

- Longest prefix: `[1, 3, 5]`  
- Longest suffix: `[2, 4, 6]`  
- Delete middle → `3` elements removed.

But actually we can delete `[5, 2]` (2 elements) and still have `[1, 3, 4, 6]` sorted.  
The flaw is that we **must keep at least one element from the suffix** that *fits* after the prefix.

Another naïve idea: try all possible deletions via two nested loops (`O(n²)`), which will time‑out for `n = 10⁵`.

So we need an **O(n)** algorithm that smartly merges prefix & suffix while respecting the ordering constraint.

---

### 4️⃣ The Optimal O(n) Strategy – Two‑Pointer Merge

1. **Identify Longest Non‑Decreasing Prefix**  
   Scan from left until `arr[i] > arr[i+1]`. Let that index be `left`.

2. **Identify Longest Non‑Decreasing Suffix**  
   Scan from right until `arr[j-1] > arr[j]`. Let that index be `right`.

3. **If the whole array is already sorted** (`left == n-1`), answer is `0`.

4. **Initialize Answer** with two obvious candidates:  
   - Delete everything after the prefix → `n - left - 1`  
   - Delete everything before the suffix → `right`

4. **Merge with Two Pointers**  
   - Set `i = 0`, `j = right`.  
   - While `i <= left` and `j < n`:  
     - If `arr[i] <= arr[j]` you can keep both.  
       *Delete the middle segment* → candidate = `j - i - 1`.  
       Increment `i` to test a longer prefix.  
     - Else (`arr[i] > arr[j]`) the current suffix element cannot stay after `arr[i]`.  
       Move `j` forward to find a suffix element that is large enough.

4. **Return Minimum**

This algorithm visits each element at most twice → **O(n)** time and **O(1)** space.

---

### 5️⃣ The Code – “Good, the Bad & the Ugly” View

#### 5.1 Good – The Interview‑Ready Explanation

- **Linear scan** – shows you understand the importance of *pre‑computing* boundary indices.  
- **Two‑pointer merging** – demonstrates advanced pointer tricks (common in “Longest Increasing Subsequence” or “Minimum deletions to keep order”).  
- **Edge‑case handling** – the `if (left == n-1)` guard proves you think of already sorted inputs.  

> *Interview Tip*: “In a linear pass, I can quickly capture the sorted boundaries, then merge them with a two‑pointer technique to respect the ordering constraints.”

#### 5.2 Bad – The Common Pitfalls

| Pitfall | Reason | Fix |
|---------|--------|-----|
| **Ignoring the “fits after” condition** | Deletes too many elements or misses a shorter deletion. | Compare prefix’s last kept element with suffix’s first kept element. |
| **Using nested loops** | O(n²) → TLE for `n=10⁵`. | Use two linear scans and a single two‑pointer pass. |
| **Over‑looking edge cases** (`arr=[1]`, all equal elements, or strictly decreasing). | May produce `-1` or wrong index. | Add the early “already sorted” check and keep `i <= left` / `j < n` bounds. |

#### 5.3 Ugly – The Hidden “Trick” That Breaks the Naïve Idea

> **The Ugly** is the realization that *you cannot just glue the prefix and suffix arbitrarily*.  
> It is tempting to think you can “skip” the middle and keep the suffix. But if `arr[left] > arr[right]`, the suffix element cannot follow the prefix, so you must move the suffix pointer until you find a “compatible” element.

In practice, many candidates get stuck in this exact trap, leading to incorrect answers.  
The key is to treat the suffix pointer as a **search** rather than a fixed segment.

---

### 5️⃣ Interview Take‑Aways

1. **State Your Plan First**  
   “I’ll find the longest increasing prefix and suffix, then try to merge them while keeping order.”  
   This shows you’re structuring the solution.

2. **Explain the Edge‑Case Check**  
   “If the array is already sorted, we can delete nothing.”  
   This small line often trips candidates out.

3. **Discuss the Two‑Pointer Idea**  
   “I’ll walk through the array from left and right, then move the pointers until I find a pair that keeps order.”  
   Highlight why it is **linear**.

4. **Mention Complexity Early**  
   “My algorithm runs in O(n) time and O(1) space, satisfying the constraints.”  

5. **Optional Python/Java/C++ Code Snippet**  
   Share one of the solutions above on the phone; recruiters appreciate seeing you can write correct code on the spot.

---

### 5️⃣ Conclusion

LeetCode 1574 is a deceptively subtle problem that rewards those who:

- Recognize the *boundary* of sorted prefix/suffix  
- Understand the need for a *merge* that preserves order  
- Apply a **two‑pointer** technique to achieve linear time

With the three clean, language‑agnostic solutions above, you’re ready to hit any technical interview that throws this question at you. Remember to emphasize **why the naïve solutions fail** – it demonstrates deeper algorithmic insight.

Happy coding, and may you crush that next hiring interview! 🎯

--- 

### 🎤 Interview Tip Cheat‑Sheet

| Situation | What to Say |
|-----------|-------------|
| **Phone screen** | “I’ll first find the longest sorted prefix and suffix, then merge them using a two‑pointer scan.” |
| **On‑site** | “My solution is linear and uses constant space, so it scales to the maximum constraint of 10⁵.” |
| **Code review** | “The key trick is to ensure the suffix element you keep is larger or equal to the last element of the chosen prefix.” |

---

### 🔗 Useful Links

- LeetCode 1574 Discussion: https://leetcode.com/problems/shortest-subarray-to-be-removed-to-make-array-sorted/discuss/  
- Two‑Pointer Technique Guide: https://leetcode.com/articles/two-pointer-solution/  
- Java Interview Prep: https://www.javaworld.com/article/3201234/learn-30-java-interview-questions.html  

---

Happy interviewing! If you found this article helpful, share it on LinkedIn or Twitter with #LeetCode #CodingInterview. 🚀