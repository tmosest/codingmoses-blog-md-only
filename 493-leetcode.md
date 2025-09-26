---
title: LeetCode 493. Reverse Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Reverse Pairs (LeetCode 493) – Code & Blog Guide  

Below you’ll find **fully‑working, production‑ready implementations** in **Java, Python, and C++** for the *Reverse Pairs* problem.  
After the code you’ll read a *blog‑style article* that explains the problem, the “good, bad and ugly” aspects of the typical solutions, and why this problem is a great interview talking‑point for software‑engineering roles.  

> **SEO Keywords**: *LeetCode Reverse Pairs, Reverse Pairs solution, Merge Sort, divide‑and‑conquer, interview question, Java, Python, C++ coding interview, job interview tips, algorithm interview*  

---

## 1.  Problem Recap (LeetCode 493)

> **Reverse Pairs**  
> *Given an integer array `nums`, return the number of reverse pairs in the array.*  
> A reverse pair is a pair `(i, j)` where `0 ≤ i < j < nums.length` and `nums[i] > 2 * nums[j]`.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `[1,3,2,3,1]` | `2` | `(1,4)` and `(3,4)` |
| 2 | `[2,4,3,5,1]` | `3` | `(1,4)`, `(2,4)`, `(3,4)` |

**Constraints**

* `1 ≤ nums.length ≤ 5 × 10⁴`
* `-2³¹ ≤ nums[i] ≤ 2³¹ − 1`

---

## 2.  Why the Naïve O(n²) Approach Fails

The brute‑force double‑loop checks every pair:

```text
for i in 0 .. n-1:
    for j in i+1 .. n-1:
        if nums[i] > 2 * nums[j]:
            count++
```

* **Time**: `O(n²)` – unacceptable for `n = 50,000` (≈ 2.5 billion operations).  
* **Space**: `O(1)` – fine, but speed kills it.

---

## 3.  The “Good” – Divide & Conquer with Merge Sort

The optimal solution uses the same counting trick that underlies *inversion counting*.  
While performing a standard merge‑sort, we **count** how many elements in the right half satisfy the reverse‑pair condition for each element in the left half.

### 3.1  Algorithm Sketch

```
function mergeSortAndCount(nums, left, right):
    if left >= right: return 0

    mid = (left + right) / 2
    count = mergeSortAndCount(nums, left, mid)
          + mergeSortAndCount(nums, mid+1, right)

    // Count reverse pairs crossing the halves
    j = mid + 1
    for i from left to mid:
        while j <= right and nums[i] > 2 * nums[j]:
            j++
        count += j - (mid + 1)

    // Merge the two sorted halves
    merge(nums, left, mid, right)
    return count
```

* **Key Insight**:  
  *While the two halves are sorted, we can walk through them linearly to count all cross‑pairs in O(n).*

### 3.2  Why It Works

* Each recursive call guarantees its segment is sorted after returning.
* The two‑pointer walk (`i` for left, `j` for right) counts all pairs `(i, j)` with `i` in the left half and `j` in the right half.
* Merge step maintains the sorted order for the next level.

### 3.3  Complexity

* **Time**: `O(n log n)` – each level of recursion processes all `n` elements once.
* **Space**: `O(n)` – auxiliary array for merging (can be done in‑place with extra tricks but `O(n)` is simpler).

---

## 4.  The “Bad” – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Integer overflow** when computing `2 * nums[j]` | `nums[j]` can be as large as `2³¹−1`. Multiplying by 2 overflows 32‑bit int. | Use 64‑bit (`long`/`long long`) for the product. |
| **Using `int` for count** | The maximum count can be `n(n-1)/2`, up to ~1.25 billion for `n=50k`. | Use `long` for the counter in Java/C++; `int` in Python is unbounded. |
| **Wrong merge boundaries** | Off‑by‑one errors (`mid` vs `mid-1`) break the sorted invariant. | Test thoroughly with unit tests; use standard merge‑sort skeleton. |
| **Re‑allocating arrays each merge** | Causes high constant overhead. | Reuse a single temporary array (pass it down or create once). |

---

## 5.  The “Ugly” – Alternative Approaches

| Approach | Pros | Cons |
|----------|------|------|
| **Binary Indexed Tree (Fenwick)** | Handles dynamic updates, useful in variations. | Requires coordinate compression, O((n+logV) log n). |
| **Segment Tree** | Similar to BIT but more flexible. | More memory, higher constant factor. |
| **Divide‑and‑Conquer (same as Merge Sort)** | Optimal O(n log n). | Implementation complexity for newbies. |

For this exact problem, *Merge Sort* remains the most straightforward, clean, and interview‑friendly solution.

---

## 6.  Implementation – 3 Languages

### 6.1  Java

```java
import java.util.*;

public class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        int[] temp = new int[nums.length];
        return (int) mergeSortAndCount(nums, temp, 0, nums.length - 1);
    }

    // Returns count as long to avoid overflow
    private long mergeSortAndCount(int[] nums, int[] temp,
                                   int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        long count = mergeSortAndCount(nums, temp, left, mid)
                   + mergeSortAndCount(nums, temp, mid + 1, right);

        // Count cross pairs
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long) nums[i] > 2L * nums[j]) {
                j++;
            }
            count += j - (mid + 1);
        }

        // Merge step
        int i = left, k = left;
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) temp[k++] = nums[i++];
            else temp[k++] = nums[j++];
        }
        while (i <= mid) temp[k++] = nums[i++];
        while (j <= right) temp[k++] = nums[j++];
        System.arraycopy(temp, left, nums, left, right - left + 1);
        return count;
    }
}
```

### 6.2  Python

```python
def reversePairs(nums):
    if not nums:
        return 0

    def merge_sort_and_count(arr, left, right):
        if left >= right:
            return 0
        mid = (left + right) // 2
        count = merge_sort_and_count(arr, left, mid) + \
                merge_sort_and_count(arr, mid + 1, right)

        # Count reverse pairs
        j = mid + 1
        for i in range(left, mid + 1):
            while j <= right and arr[i] > 2 * arr[j]:
                j += 1
            count += j - (mid + 1)

        # Merge sorted halves
        merged = []
        i, j = left, mid + 1
        while i <= mid and j <= right:
            merged.append(arr[i] if arr[i] <= arr[j] else arr[j])
            if arr[i] <= arr[j]:
                i += 1
            else:
                j += 1
        while i <= mid:
            merged.append(arr[i]); i += 1
        while j <= right:
            merged.append(arr[j]); j += 1
        arr[left:right+1] = merged
        return count

    return merge_sort_and_count(nums, 0, len(nums) - 1)
```

### 6.3  C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int reversePairs(std::vector<int>& nums) {
        if (nums.empty()) return 0;
        std::vector<int> temp(nums.size());
        long long res = mergeSortAndCount(nums, temp, 0, nums.size() - 1);
        return static_cast<int>(res);   // count fits in 32‑bit for this problem
    }

private:
    long long mergeSortAndCount(std::vector<int>& nums,
                                std::vector<int>& temp,
                                int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        long long count = mergeSortAndCount(nums, temp, left, mid) +
                          mergeSortAndCount(nums, temp, mid + 1, right);

        // Count cross pairs
        int j = mid + 1;
        for (int i = left; i <= mid; ++i) {
            while (j <= right && static_cast<long long>(nums[i]) > 2LL * nums[j]) {
                ++j;
            }
            count += j - (mid + 1);
        }

        // Merge
        int i = left, k = left;
        while (i <= mid && j <= right) {
            if (nums[i] <= nums[j]) temp[k++] = nums[i++];
            else temp[k++] = nums[j++];
        }
        while (i <= mid) temp[k++] = nums[i++];
        while (j <= right) temp[k++] = nums[j++];
        std::copy(temp.begin() + left, temp.begin() + right + 1,
                  nums.begin() + left);
        return count;
    }
};
```

> **Note on C++**  
> *The `long long` type is mandatory for `2 * nums[j]`.  
> The final cast to `int` is safe because the answer is always ≤ 1 250 000 000 for `n ≤ 50 000`.*

---

## 7.  Blog Article – “Reverse Pairs” (Interview‑Ready)

> **Title**: Reverse Pairs (LeetCode 493) – The Ultimate Interview Coding Challenge  
> **Author**: *Your Friendly Algorithm Enthusiast*  

---

### 7.1  Introduction

Reverse pairs look deceptively simple: *“count how many times an element is more than twice another that appears later in the array.”*  
In reality it’s a textbook example of a **divide‑and‑conquer** algorithm that tests a candidate’s ability to

* Reason about sorted sub‑arrays  
* Use two‑pointer traversal  
* Prevent integer overflows  
* Write clean, reusable code

> 👉 **Why recruiters love it** – It covers **time‑complexity thinking**, **array manipulation**, and **edge‑case handling** – all the things they look for in a software‑engineer.

---

### 7.2  Problem Statement (Re‑written)

> **Given** a list of 32‑bit signed integers,  
> **Find** the number of index pairs `(i, j)` such that `i < j` and `nums[i] > 2 × nums[j]`.

> **Constraints**  
> *Array length ≤ 50 000*  
> *Numbers are in the full 32‑bit signed range.*

---

### 7.3  “The Good”

**Merge‑sort counting** is the canonical solution:

1. **Sort** the array by recursion (divide the range in halves).  
2. **While merging**, walk once through the left and right halves and count how many cross‑pairs satisfy the condition.  
3. **Return** the sum of counts from all recursive levels.

> **Why it’s *good*** –  
> *Runs in O(n log n)*, which is the best you can do for a static array.  
> *Implementation is short (≈ 70 lines in most languages) and easy to reason about.*  

---

### 7.4  “The Bad”

Common mistakes that turn the elegant solution into a buggy implementation:

* **Overflow**: `2 * nums[j]` must be evaluated as 64‑bit.  
* **Wrong data type for the counter**: The answer can be ~1.25 billion.  
* **Boundary errors in the merge step**: Off‑by‑one bugs destroy sortedness.  
* **Repeated allocation of temporary buffers**: Wastes time.

---

### 7.5  “The Ugly” – When You Have to Think Outside the Box

1. **Binary Indexed Tree (Fenwick)** – Coordinate‑compress the values, then iterate from right to left inserting into the BIT and querying how many earlier numbers are > 2×current.  
2. **Segment Tree** – Similar to BIT but with more flexibility for range queries.  
3. **Recursive Divide & Conquer (same as Merge‑Sort)** – Slightly cleaner but more verbose.

All of them reach *O(n log n)* or *O(n log n + n log V)* time, but **merge‑sort** is still the most *interview‑ready* because you only need to remember the classic *inversion‑count* pattern.

---

### 7.6  Why This Problem is a Great Interview Topic

| Interview Focus | What It Shows |
|-----------------|---------------|
| **Algorithmic Thinking** | Candidate can spot the cross‑pair counting trick. |
| **Coding Skill** | Ability to write a correct recursive routine and an in‑place merge. |
| **Edge‑Case Awareness** | Detects overflow, large count, negative numbers. |
| **Language Mastery** | Different languages (Java/Python/C++) demand different handling of long integers. |
| **Time/Space Trade‑offs** | Discussion about `O(n)` temporary array vs. truly in‑place merges. |

---

### 7.7  Quick‑Check Unit Tests (for all three languages)

```text
[1, 3, 2, 3, 1]          → 2
[2, 4, 3, 5, 1]          → 3
[0, 0, 0, 0]             → 0
[2147483647, -2147483648] → 1  (checks overflow handling)
```

> **Tip for interviewers**: Ask the candidate to walk through the *cross‑pair counting* part with a small hand‑drawn array; it exposes their mental model.

---

### 7.8  Conclusion & Job‑Interview Takeaway

* **Reverse Pairs** is a classic *O(n log n)* divide‑and‑conquer problem that blends array sorting with a counting twist.  
* Implementing it correctly demonstrates mastery of algorithmic thinking, careful handling of data types, and clean code style—exactly what hiring managers are looking for.  
* Master this pattern, write the three‑language snippets above, and you’ll be ready to ace the coding interview stage of your next software‑engineering job hunt.

---

> **Pro‑Recruiter Note** – “I noticed you solved *Reverse Pairs* efficiently.” →  
> *Answer*: “I used a merge‑sort based counting approach, carefully avoiding overflow by using 64‑bit integers. It runs in `O(n log n)` time, which is essential for large inputs.”  
> This tells the recruiter you understand both algorithmic optimality and practical edge‑cases.  

Happy coding, and good luck on your next interview!