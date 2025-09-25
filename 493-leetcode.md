---
title: LeetCode 493. Reverse Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Reverse Pairs – 3‑Language Implementations

**Problem statement**  
Given an integer array `nums`, return the number of reverse pairs in the array.  
A reverse pair is a pair `(i, j)` such that  

* `0 ≤ i < j < nums.length`  
* `nums[i] > 2 * nums[j]`

---

### 1.1  Java (O(n log n) – Merge‑Sort + Count)

```java
import java.util.*;

public class ReversePairs {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        return mergeSortAndCount(nums, 0, nums.length - 1);
    }

    private int mergeSortAndCount(int[] a, int left, int right) {
        if (left >= right) return 0;

        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(a, left, mid) +
                    mergeSortAndCount(a, mid + 1, right);

        // Count cross pairs
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long)a[i] > 2L * a[j]) j++;
            count += j - (mid + 1);
        }

        // Merge step
        merge(a, left, mid, right);
        return count;
    }

    private void merge(int[] a, int left, int mid, int right) {
        int[] temp = new int[right - left + 1];
        int i = left, j = mid + 1, k = 0;

        while (i <= mid && j <= right) {
            if (a[i] <= a[j]) temp[k++] = a[i++];
            else temp[k++] = a[j++];
        }

        while (i <= mid) temp[k++] = a[i++];
        while (j <= right) temp[k++] = a[j++];

        System.arraycopy(temp, 0, a, left, temp.length);
    }

    // Simple main for quick testing
    public static void main(String[] args) {
        ReversePairs rp = new ReversePairs();
        System.out.println(rp.reversePairs(new int[]{1,3,2,3,1})); // 2
        System.out.println(rp.reversePairs(new int[]{2,4,3,5,1})); // 3
    }
}
```

---

### 1.2  Python (O(n log n) – Merge‑Sort + Count)

```python
from typing import List

class Solution:
    def reversePairs(self, nums: List[int]) -> int:
        if not nums:
            return 0
        return self._merge_sort_and_count(nums, 0, len(nums) - 1)

    def _merge_sort_and_count(self, arr: List[int], left: int, right: int) -> int:
        if left >= right:
            return 0
        mid = left + (right - left) // 2
        count = (self._merge_sort_and_count(arr, left, mid) +
                 self._merge_sort_and_count(arr, mid + 1, right))

        # Count cross pairs
        j = mid + 1
        for i in range(left, mid + 1):
            while j <= right and arr[i] > 2 * arr[j]:
                j += 1
            count += j - (mid + 1)

        # Merge step
        temp = []
        i, j = left, mid + 1
        while i <= mid and j <= right:
            if arr[i] <= arr[j]:
                temp.append(arr[i]); i += 1
            else:
                temp.append(arr[j]); j += 1
        while i <= mid:
            temp.append(arr[i]); i += 1
        while j <= right:
            temp.append(arr[j]); j += 1

        arr[left:right + 1] = temp
        return count

# quick test
if __name__ == "__main__":
    s = Solution()
    print(s.reversePairs([1,3,2,3,1]))   # 2
    print(s.reversePairs([2,4,3,5,1]))   # 3
```

---

### 1.3  C++ (O(n log n) – Merge‑Sort + Count)

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    int reversePairs(std::vector<int>& nums) {
        if (nums.empty()) return 0;
        return mergeSortAndCount(nums, 0, nums.size() - 1);
    }

private:
    int mergeSortAndCount(std::vector<int>& a, int left, int right) {
        if (left >= right) return 0;

        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(a, left, mid) +
                    mergeSortAndCount(a, mid + 1, right);

        // Count cross pairs
        int j = mid + 1;
        for (int i = left; i <= mid; ++i) {
            while (j <= right && static_cast<long long>(a[i]) > 2LL * a[j])
                ++j;
            count += j - (mid + 1);
        }

        // Merge step
        std::inplace_merge(a.begin() + left, a.begin() + mid + 1, a.begin() + right + 1);
        return count;
    }
};
```

> **Note**: In C++ we cast to `long long` when doing the comparison to avoid overflow with `2 * a[j]`.

---

## 2.  Blog Article – “Reverse Pairs (LeetCode 493): The Good, The Bad, and The Ugly”

### 2.1  Introduction

If you’re preparing for a software‑engineering interview, one of the most common topics you’ll run into is *counting problems*.  
LeetCode’s **493. Reverse Pairs** is a classic example: given an array `nums`, count how many pairs `(i, j)` satisfy `nums[i] > 2 * nums[j]`.  

On the surface, it looks like a simple comparison. On the surface, it’s easy to fall into a trap.  
In this article we’ll break the problem into three parts:

| Aspect | What it is | Why it matters |
|--------|------------|----------------|
| **The Good** | The O(n log n) solution using merge‑sort. | Shows mastery of divide‑and‑conquer, counting techniques, and careful handling of overflows. |
| **The Bad** | The naive O(n²) brute‑force approach. | Demonstrates what *not* to do when time limits are tight. |
| **The Ugly** | Edge‑cases: negative numbers, overflow, large values, and in‑place vs auxiliary array trade‑offs. | Highlights attention to detail, a must‑have in production code. |

We’ll also give you **SEO‑optimized** copy that will help your résumé get noticed on LinkedIn, Indeed, or any recruiter’s inbox.  

---

### 2.2  The Problem – What is a Reverse Pair?

> **Definition**  
> A *reverse pair* is a pair of indices `(i, j)` such that `0 ≤ i < j < nums.length` and `nums[i] > 2 * nums[j]`.  
> The goal is to count all such pairs.

> **Why It Matters**  
> This problem tests your ability to **count** rather than **find**.  
> It forces you to think about how to avoid quadratic time, a classic interview twist.

---

### 2.3  The Bad – Brute‑Force (O(n²))

The most straightforward code:

```python
def reversePairs(nums):
    cnt = 0
    n = len(nums)
    for i in range(n):
        for j in range(i+1, n):
            if nums[i] > 2 * nums[j]:
                cnt += 1
    return cnt
```

#### Pros

* **Zero thinking** – easy to write, no debugging.
* Works for small inputs.

#### Cons

* **Time complexity**: O(n²). With `n` up to 50 000, the algorithm will try 1.25 billion comparisons—way too slow.
* **Space complexity**: O(1), but that’s irrelevant when you can’t finish in time.
* **Reveals laziness** – recruiters expect you to recognize the O(n²) pitfall.

> **Bottom line**: Avoid the brute‑force solution unless the array size is extremely small.

---

### 2.4  The Good – Divide & Conquer (Merge‑Sort Counting)

#### Why Merge‑Sort?

* Merge‑sort already has a **stable** merge step that merges two sorted halves.
* While merging we can **count** how many elements in the right half satisfy the reverse‑pair condition with an element in the left half.

#### High‑Level Idea

1. **Divide** the array into two halves.
2. **Conquer**: recursively count reverse pairs in each half.
3. **Combine**:  
   * Count cross‑pairs (left element > 2 * right element).  
   * Merge the two halves back into a sorted array.

#### Counting Cross‑Pairs

During the merge, for each element `nums[i]` in the left half, we keep a pointer `j` in the right half.  
Because both halves are sorted, we can **advance `j` only forward**:  

```
while j <= right and nums[i] > 2 * nums[j]:
    j += 1
count += j - (mid + 1)
```

The pointer `j` never moves backward, giving an overall O(n) counting phase per merge.

#### Complexity

| Metric | Calculation |
|--------|--------------|
| Time | O(n log n) – `n` log n merges, each merge scans all elements once. |
| Space | O(n) auxiliary array (or `inplace_merge` in C++ for an in‑place merge). |

#### Implementation Snippets

| Language | Key line |
|----------|----------|
| **Java** | `while (j <= right && (long)a[i] > 2L * a[j]) j++;` |
| **Python** | `while j <= right and arr[i] > 2 * arr[j]: j += 1` |
| **C++** | `while (j <= right && static_cast<long long>(a[i]) > 2LL * a[j]) ++j;` |

> **Why `long long`?**  
> `nums[i]` can be up to 2 ³¹ ‑ 1. Multiplying by 2 may overflow a 32‑bit signed integer.  
> In Java and C++ we cast to 64‑bit before comparison. In Python the integer type is arbitrary‑precision, so no cast is needed.

#### Final Thought

The O(n log n) merge‑sort counting solution is the *canonical* interview answer.  
It shows you can:

* Recognise a counting problem as a “merge‑count” variant.  
* Apply divide‑and‑conquer correctly.  
* Guard against overflow.  
* Write clean, testable code.

---

### 2.5  The Ugly – Edge‑Cases & Practical Concerns

| Edge‑Case | Why it’s nasty | What to do |
|-----------|----------------|------------|
| **Negative Numbers** | `nums[i] > 2 * nums[j]` still holds, but `2 * nums[j]` may become **more negative** than `INT_MIN`. | Use `long long` (C++/Java) or cast to `float64` (Python) before comparison. |
| **Overflow** | `2 * 2 ³¹ ‑ 1` overflows 32‑bit. | Multiply first, then compare (`nums[i] > 2LL * nums[j]`). |
| **Large Input (`n = 50 000`)** | Requires a fast algorithm. | Stick to merge‑sort counting; test with random data of this size to verify performance. |
| **In‑place vs Extra Space** | `inplace_merge` in C++ saves a temporary array but can be slower; Java/C++ often allocate a temporary `int[]` for safety. | Choose the one that gives you the cleanest code. In interview coding platforms, a temporary array is usually fine. |
| **Stable vs Unstable** | Merge‑sort is stable; if you mutate the array you must preserve ordering if later steps rely on it. | Use the standard merge algorithm; no need to re‑implement `inplace_merge`. |

> **Recruiter tip**: Mention in your explanation that you *considered* both in‑place and auxiliary‑array approaches and chose the one that gives the simplest, most maintainable code.

---

### 2.6  Putting It All Together – The Interview Script

A quick “talk‑through” you can give during an interview:

> “I’ll solve this with a divide‑and‑conquer approach.  
> I split the array into halves, recursively count reverse pairs inside each half, then count cross‑pairs while merging.  
> While merging, I maintain a pointer in the right half that never moves back, giving us an O(n) counting step.  
> Finally, I merge the halves to keep the array sorted for the next level.  
> This runs in O(n log n) time and O(n) auxiliary space.”

> **If the interviewer asks for *in‑place***:  
> “We can use `inplace_merge` (C++), or simply copy back the merged buffer (Java/Python). The difference is only in constant factors, not asymptotic complexity.”

---

### 2.7  SEO‑Optimized Takeaway Paragraph

> **“Reverse Pairs (LeetCode 493) – O(n log n) merge‑sort counting solution. Demonstrated expertise in divide‑and‑conquer, two‑pointer counting, and overflow‑safe integer handling. Ideal for any software‑engineering interview, especially for positions requiring strong algorithmic skills and production‑grade code robustness.”**

Copy‑paste this paragraph into your LinkedIn headline, job descriptions, or a `README` that recruiters may glance over.

---

### 2.8  Summary – How to Nail the Problem

| Step | What to Do | Why it matters |
|------|------------|----------------|
| 1 | Recognise the O(n²) brute‑force trap. | Shows analytical skill. |
| 2 | Implement the merge‑sort counting solution. | Demonstrates algorithmic thinking. |
| 3 | Handle overflows (`long long` / `2L`). | Shows production‑readiness. |
| 4 | Test on large random inputs (n = 50 000). | Confirms performance. |
| 5 | Write a clear, commented, 3‑language code snippet. | Provides tangible deliverables for your portfolio. |

> **Goal**: Get interviewers to say *“We’ll keep this candidate on our radar.”*  

---

### 2.9  Final Words

Reverse Pairs is more than a number‑counting challenge; it’s a **micro‑test** of your software‑engineering mindset:

* **The Good** shows you can build efficient, elegant code.
* **The Bad** reminds you to avoid quadratic pitfalls.
* **The Ugly** checks that you think about every possible edge‑case.

Use the three‑language snippets above to **practice** in Java, Python, and C++.  
Once you’re comfortable, add the problem to your personal repo and include the SEO‑optimized description in your résumé.  

Good luck – you’ll be “the candidate” who turns a *reverse pair* into a *reverse engineer of interviews*!