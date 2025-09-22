---
title: LeetCode 493. Reverse Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Reverse Pairs (LeetCode 493) – A Deep‑Dive into the Classic Merge‑Sort Solution  
*(Java • Python • C++) – SEO‑Optimized, “Good / Bad / Ugly” Analysis for Your Next Job Interview*  

---

### 🚀 TL;DR  
- **Problem**: Count reverse pairs `(i, j)` where `i < j` and `nums[i] > 2 * nums[j]`.  
- **Optimal Time**: **O(n log n)** using a modified merge‑sort.  
- **Space**: **O(n)** auxiliary array (or O(log n) recursion stack).  
- **Why It Matters**: It’s a frequent hard‑level LeetCode question and a staple in software‑engineering interviews that tests divide‑and‑conquer, overflow handling, and array manipulation.  

---

## 📄 Problem Statement (LeetCode 493)

> Given an integer array `nums`, return the number of reverse pairs in the array.  
> A reverse pair is a pair `(i, j)` where  
> `0 <= i < j < nums.length` **and** `nums[i] > 2 * nums[j]`.  

**Example**  
```text
Input:  nums = [1,3,2,3,1]
Output: 2
Explanation: (1,4) and (3,4) are reverse pairs.
```

**Constraints**  
- `1 <= nums.length <= 5·10⁴`  
- `-2³¹ <= nums[i] <= 2³¹‑1`

---

## 🔍 Why the Classic Merge‑Sort Approach Works

| # | Observation | How Merge‑Sort Helps |
|---|-------------|----------------------|
| 1 | A naive O(n²) double loop is too slow. | Divide the array into halves to reduce comparisons. |
| 2 | While merging two sorted halves, we can *count* cross‑pairs in O(n). | When left[i] > 2 * right[j], every element left[i..mid] will satisfy the condition with `right[j]`. |
| 3 | Numbers can be as large as 2³¹‑1, so `2 * nums[j]` may overflow 32‑bit int. | Use 64‑bit (`long` / `long long`) during comparison. |

---

## 🛠️ The Core Algorithm (Pseudo‑Code)

```
function reversePairs(nums):
    return mergeSortAndCount(nums, 0, len(nums)-1)

function mergeSortAndCount(arr, left, right):
    if left >= right: return 0
    mid = (left + right) // 2
    count = mergeSortAndCount(arr, left, mid)
            + mergeSortAndCount(arr, mid+1, right)
    # Count cross pairs
    j = mid + 1
    for i in range(left, mid+1):
        while j <= right and arr[i] > 2 * arr[j]:
            j += 1
        count += j - (mid + 1)
    # Merge the two halves
    merge(arr, left, mid, right)
    return count
```

---

## 📚 Code Implementations

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

### Java

```java
// 493. Reverse Pairs
// Time:  O(n log n)
// Space: O(n) auxiliary array (plus recursion stack)

class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length <= 1) return 0;
        return mergeSortAndCount(nums, 0, nums.length - 1);
    }

    private int mergeSortAndCount(int[] arr, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(arr, left, mid)
                  + mergeSortAndCount(arr, mid + 1, right);

        // Count cross pairs
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long)arr[i] > 2L * arr[j]) {
                j++;
            }
            count += j - (mid + 1);
        }

        // Merge step
        int[] temp = new int[right - left + 1];
        int i = left, k = 0, l = mid + 1;
        while (i <= mid && l <= right) {
            if (arr[i] <= arr[l]) temp[k++] = arr[i++];
            else temp[k++] = arr[l++];
        }
        while (i <= mid) temp[k++] = arr[i++];
        while (l <= right) temp[k++] = arr[l++];

        System.arraycopy(temp, 0, arr, left, temp.length);
        return count;
    }
}
```

### Python

```python
# 493. Reverse Pairs
# Time:  O(n log n)
# Space: O(n) auxiliary array

class Solution:
    def reversePairs(self, nums: List[int]) -> int:
        if not nums:
            return 0
        return self._merge_sort_and_count(nums, 0, len(nums) - 1)

    def _merge_sort_and_count(self, arr, left, right):
        if left >= right:
            return 0
        mid = (left + right) // 2
        count = self._merge_sort_and_count(arr, left, mid) + \
                self._merge_sort_and_count(arr, mid + 1, right)

        # Count reverse pairs across halves
        j = mid + 1
        for i in range(left, mid + 1):
            while j <= right and arr[i] > 2 * arr[j]:
                j += 1
            count += j - (mid + 1)

        # Merge step
        temp = []
        l, r = left, mid + 1
        while l <= mid and r <= right:
            if arr[l] <= arr[r]:
                temp.append(arr[l]); l += 1
            else:
                temp.append(arr[r]); r += 1
        temp.extend(arr[l:mid+1])
        temp.extend(arr[r:right+1])

        arr[left:right+1] = temp
        return count
```

### C++

```cpp
// 493. Reverse Pairs
// Time:  O(n log n)
// Space: O(n) auxiliary vector (recursion stack is log n)

class Solution {
public:
    int reversePairs(vector<int>& nums) {
        if (nums.empty()) return 0;
        return mergeSortAndCount(nums, 0, nums.size() - 1);
    }

private:
    int mergeSortAndCount(vector<int>& arr, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(arr, left, mid)
                  + mergeSortAndCount(arr, mid + 1, right);

        // Count cross pairs
        int j = mid + 1;
        for (int i = left; i <= mid; ++i) {
            while (j <= right && (long long)arr[i] > 2LL * arr[j]) ++j;
            count += j - (mid + 1);
        }

        // Merge step
        vector<int> temp;
        temp.reserve(right - left + 1);
        int i = left, l = mid + 1;
        while (i <= mid && l <= right) {
            if (arr[i] <= arr[l]) temp.push_back(arr[i++]);
            else temp.push_back(arr[l++]);
        }
        while (i <= mid) temp.push_back(arr[i++]);
        while (l <= right) temp.push_back(arr[l++]);

        copy(temp.begin(), temp.end(), arr.begin() + left);
        return count;
    }
};
```

---

## 📈 Complexity Analysis

| Complexity | Explanation |
|------------|-------------|
| **Time**   | `mergeSortAndCount` splits the array `log n` times. In each merge we perform a linear scan for counting pairs + merging ⇒ `O(n log n)`. |
| **Space**  | `O(n)` temporary array per merge; recursion stack `O(log n)`. |

---

## ⚠️ Pitfalls & Edge Cases (The “Bad” & “Ugly” Bits)

| # | Issue | Fix |
|---|-------|-----|
| 1 | **Integer overflow** (`2 * nums[j]` may exceed 32‑bit). | Cast to `long` (Java) / `long long` (C++) / rely on Python’s arbitrary precision. |
| 2 | **Negative numbers** – the condition still holds; be careful that the comparison logic works for negatives. | The algorithm is unaffected because we use 64‑bit multiplication. |
| 3 | **Recursive depth** may reach `log₂(5·10⁴) ≈ 16` – fine, but always keep an eye on stack limits in very constrained environments. | Iterative mergesort or manual stack simulation can be used if needed. |
| 4 | **Debugging the merge step** – subtle off‑by‑one errors are common. | Keep the merge logic separate and test it on its own (e.g., unit‑test the `merge` helper). |
| 5 | **Large input arrays** – Python’s recursion limit (`sys.setrecursionlimit`) may need adjustment. | Use `sys.setrecursionlimit(1 << 20)` before running the solution in scripts. |

---

## 🎯 What Interviewers Really Want to See

1. **Correctness** – Does your code return the exact count for all test cases?  
2. **Efficiency** – Show you understand why the divide‑and‑conquer approach beats O(n²).  
3. **Overflow Safety** – Mention that you used 64‑bit arithmetic.  
4. **Clean Code** – Use descriptive function names (`mergeSortAndCount`, `merge`).  
5. **Edge‑Case Handling** – Explain how empty or single‑element arrays are handled.  

---

## 📈 Practice Tips

| Tip | Why It Helps |
|-----|--------------|
| **Implement the algorithm from scratch** before copying any snippet. | Builds confidence and reduces “copy‑paste” surprises. |
| **Write a small test harness** to print intermediate sorted halves and pair counts. | Makes debugging far easier. |
| **Try the problem on different data types** (`long`, `BigInteger`). | Reinforces overflow awareness. |
| **Time yourself** – 2‑3 min for a clean solution is realistic in a real interview. | Shows you can solve hard problems under pressure. |

---

## 📢 Call to Action

Want to ace **software‑engineering interviews** that involve hard LeetCode problems?  
1. **Clone** this repo and run the Java, Python, and C++ solutions on the official LeetCode platform.  
2. **Add** your own variations (e.g., counting `nums[i] < nums[j] * 2`) to deepen understanding.  
3. **Show** this code on your GitHub portfolio – it’s a great talking‑point in interviews.  

---

## 🔑 SEO Highlights

- **Primary Keywords**: *Reverse Pairs LeetCode 493*, *493 Reverse Pairs solution*, *Merge Sort algorithm interview*, *software engineer interview questions*.  
- **Secondary Keywords**: *Java reverse pairs*, *Python reverse pairs*, *C++ reverse pairs*, *job interview algorithm*, *algorithm interview questions*, *coding interview tips*.  
- **Meta Description (≈155 chars)**:  
  > Master LeetCode 493 Reverse Pairs with an O(n log n) merge‑sort solution. Read Java, Python, and C++ code, plus a “Good / Bad / Ugly” interview guide.  

---

Happy coding—and may your next interview be a walk through this classic algorithm! 🚀