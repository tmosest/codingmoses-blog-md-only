---
title: LeetCode 493. Reverse Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Reverse Pairs (LeetCode 493) – A Deep Dive (Java | Python | C++)
> **Want to impress recruiters with a polished LeetCode solution?**  
> In this post we’ll walk through the *Reverse Pairs* problem, show a **divide‑and‑conquer** solution that runs in *O(n log n)*, and give you clean, production‑ready code in **Java, Python, and C++**.  
> We’ll also cover the good, the bad, and the ugly of this classic interview problem so you can talk it confidently in your next interview.

---

## 1. Problem Recap

Given an integer array `nums`, count the number of *reverse pairs* `(i, j)` such that

```
0 ≤ i < j < nums.length
nums[i] > 2 * nums[j]
```

### Example
```text
Input:  nums = [1,3,2,3,1]
Output: 2   // (1,4) and (3,4)
```

The constraints are:

- `1 ≤ nums.length ≤ 5 × 10⁴`
- `-2³¹ ≤ nums[i] ≤ 2³¹ – 1`

---

## 2. Why the Simple O(n²) Fails

A naïve double loop is `O(n²)` – far too slow for `n = 50,000`.  
We need something like a *binary indexed tree*, *segment tree*, or the classic *merge‑sort counting* trick.

The merge‑sort trick is popular because it:

- Works for **any** comparison‑based pair counting problem (e.g., inversions, reverse pairs).
- Requires only **O(n)** extra memory.
- Gives an elegant, well‑known divide‑and‑conquer pattern.

---

## 3. The Divide & Conquer (Merge‑Sort) Idea

1. **Split** the array into two halves.
2. **Recursively** count reverse pairs inside each half.
3. **Count cross‑half pairs** (`i` in left, `j` in right) while the two halves are *sorted*.
4. **Merge** the two halves into a sorted array.

### Counting cross‑half pairs
When left and right halves are sorted, we can use two pointers:

```
j = mid + 1          // start of right half
for i in left:
    while j <= end and nums[i] > 2 * nums[j]:
        j += 1
    count += j - (mid + 1)
```

The loop moves `j` only forward, so the total work over all levels is `O(n)`.

### Merging
Standard merge‑sort merging keeps the array sorted for the next level.

---

## 4. Complexity

| Step                | Time             | Space            |
|---------------------|------------------|------------------|
| Recursion overhead | `O(log n)`       | `O(log n)`       |
| Counting cross pairs | `O(n)` per level | `O(1)`          |
| Merging            | `O(n)` per level | `O(n)` temporary |
| **Total**           | **O(n log n)** | **O(n)** (auxiliary) |

---

## 5. Implementation

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All three use the same divide‑and‑conquer strategy.

> **Tip:** In Java and C++ we use `long` (or `long long`) to avoid overflow when multiplying by 2.

---

### 5.1 Java

```java
import java.util.*;

public class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length == 0) return 0;
        return mergeSortAndCount(nums, 0, nums.length - 1);
    }

    private int mergeSortAndCount(int[] nums, int left, int right) {
        if (left >= right) return 0;

        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(nums, left, mid)
                  + mergeSortAndCount(nums, mid + 1, right);

        // Count cross pairs
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long) nums[i] > 2L * nums[j]) {
                j++;
            }
            count += j - (mid + 1);
        }

        // Merge sorted halves
        merge(nums, left, mid, right);
        return count;
    }

    private void merge(int[] nums, int left, int mid, int right) {
        int n1 = mid - left + 1;
        int n2 = right - mid;
        int[] leftArr = new int[n1];
        int[] rightArr = new int[n2];

        System.arraycopy(nums, left, leftArr, 0, n1);
        System.arraycopy(nums, mid + 1, rightArr, 0, n2);

        int i = 0, j = 0, k = left;
        while (i < n1 && j < n2) {
            if (leftArr[i] <= rightArr[j]) {
                nums[k++] = leftArr[i++];
            } else {
                nums[k++] = rightArr[j++];
            }
        }
        while (i < n1) nums[k++] = leftArr[i++];
        while (j < n2) nums[k++] = rightArr[j++];
    }
}
```

---

### 5.2 Python

```python
class Solution:
    def reversePairs(self, nums: list[int]) -> int:
        if not nums:
            return 0
        return self._merge_sort_and_count(nums, 0, len(nums) - 1)

    def _merge_sort_and_count(self, nums, left, right):
        if left >= right:
            return 0
        mid = (left + right) // 2
        count = self._merge_sort_and_count(nums, left, mid) + \
                self._merge_sort_and_count(nums, mid + 1, right)

        # Count cross pairs
        j = mid + 1
        for i in range(left, mid + 1):
            while j <= right and nums[i] > 2 * nums[j]:
                j += 1
            count += j - (mid + 1)

        # Merge
        temp = []
        i, j = left, mid + 1
        while i <= mid and j <= right:
            if nums[i] <= nums[j]:
                temp.append(nums[i]); i += 1
            else:
                temp.append(nums[j]); j += 1
        temp.extend(nums[i:mid + 1])
        temp.extend(nums[j:right + 1])
        nums[left:right + 1] = temp
        return count
```

---

### 5.3 C++

```cpp
class Solution {
public:
    int reversePairs(vector<int>& nums) {
        if (nums.empty()) return 0;
        return mergeSortAndCount(nums, 0, nums.size() - 1);
    }

private:
    int mergeSortAndCount(vector<int>& nums, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(nums, left, mid)
                  + mergeSortAndCount(nums, mid + 1, right);

        // Cross‑half counting
        int j = mid + 1;
        for (int i = left; i <= mid; ++i) {
            while (j <= right && (long long)nums[i] > 2LL * nums[j]) {
                ++j;
            }
            count += j - (mid + 1);
        }

        // Merge sorted halves
        merge(nums, left, mid, right);
        return count;
    }

    void merge(vector<int>& nums, int left, int mid, int right) {
        int n1 = mid - left + 1;
        int n2 = right - mid;
        vector<int> L(n1), R(n2);
        copy(nums.begin() + left, nums.begin() + mid + 1, L.begin());
        copy(nums.begin() + mid + 1, nums.begin() + right + 1, R.begin());

        int i = 0, j = 0, k = left;
        while (i < n1 && j < n2) {
            nums[k++] = (L[i] <= R[j]) ? L[i++] : R[j++];
        }
        while (i < n1) nums[k++] = L[i++];
        while (j < n2) nums[k++] = R[j++];
    }
};
```

---

## 6. Good, Bad, & Ugly

| **Aspect** | **Why it matters** |
|------------|--------------------|
| **Good**  | • `O(n log n)` performance.<br>• No extra data‑structure complexity (just merge‑sort).<br>• Works for *any* “reverse‑like” pair counting problem. |
| **Bad**   | • Requires careful handling of **integer overflow** (`nums[i] > 2 * nums[j]`).<br>• The two‑pointer logic is easy to get wrong if you forget to reset `j` or use `mid + 1` correctly. |
| **Ugly**  | • The recursion depth can hit stack limits in languages with small recursion limits (Python).<br>• Debugging cross‑half counting is non‑trivial; a subtle off‑by‑one can double‑count or miss pairs.<br>• The merge helper can become a performance bottleneck if you copy slices unnecessarily (C++ with `vector::assign` vs `std::copy`). |

---

## 7. Common Pitfalls & Fixes

| Pitfall | Fix |
|---------|-----|
| **Overflow** (`nums[i] > 2 * nums[j]` may exceed 32‑bit) | Use `long` / `long long` when doing the comparison. |
| **Wrong merge order** (descending vs ascending) | Keep the array sorted *ascending*; this is the only order needed for the counting logic. |
| **Index errors** (`mid + 1` out‑of‑range) | Always compute `mid` with `left + (right - left) / 2`. |
| **Python recursion limit** | Either increase recursion limit (`sys.setrecursionlimit`) or convert to an iterative bottom‑up merge sort. |
| **Timeouts on huge negatives** | The comparison `(long) nums[i] > 2L * nums[j]` still works because multiplying a negative by 2 keeps it negative; the logic stays sound. |

---

## 8. Variants & Extensions

- **Count reverse pairs in a sub‑array** – simply call the same helper on the sub‑range.
- **Generalized condition** (`nums[i] > k * nums[j]`) – replace `2` with `k` in the loop.
- **Streaming version** – use a Binary Indexed Tree or Balanced BST instead of merge sort if you want *O(n log m)* where `m` is the value range.

---

## 9. Interview Tips

1. **Explain the algorithm first** – talk about *divide & conquer*, *two pointers*, and why it’s *O(n log n)*.
2. **Show a proof of correctness** – especially the cross‑half counting logic. Recruiters love a concise proof sketch.
3. **Mention overflow** – it’s a common interview question for this problem.  
4. **Discuss alternative data structures** – e.g., Fenwick Tree, Balanced BST.  
5. **Time & Space trade‑offs** – be ready to answer why merge‑sort is preferable over BIT for large negative numbers.

---

## 10. Final Thought

The *Reverse Pairs* problem is more than a coding challenge; it’s a micro‑ecosystem that teaches you:

- How to turn a quadratic problem into a *divide‑and‑conquer* one.
- How to handle overflow carefully in competitive coding.
- How to structure clean, interview‑ready code in multiple languages.

💡 **Takeaway:** Master the merge‑sort counting technique, and you’ll be ready for *inversion counting*, *reverse pairs*, and many other pair‑counting problems that crop up in data‑structure interviews.

---

### 📌 TL;DR

- **Algorithm:** Divide‑and‑conquer + merge‑sort counting  
- **Complexity:** `O(n log n)` time, `O(n)` auxiliary space  
- **Languages:** Java, Python, C++ (see code blocks above)  
- **Interview Edge:** Watch out for overflow, off‑by‑one errors, and recursion depth.

> **Want to see more LeetCode gold‑mines?** Check out our series on *Inversion Count* (LeetCode ##) and *K‑th Largest Element* (LeetCode #).  

Happy coding, and best of luck on your next interview! 🚀