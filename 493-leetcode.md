---
title: LeetCode 493. Reverse Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Mastering LeetCode 493 – **Reverse Pairs**  
*The Good, The Bad, and The Ugly – A Complete Guide for Interview‑Ready Developers*  

---

### 1️⃣ Problem Overview  
> **Reverse Pairs**  
> Given an integer array `nums`, return the number of reverse pairs in the array.  
> A reverse pair is a pair `(i, j)` where  
> `0 <= i < j < nums.length` **and** `nums[i] > 2 * nums[j]`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,3,2,3,1]` | `2` | `(1,4)` and `(3,4)` |
| `[2,4,3,5,1]` | `3` | `(1,4)`, `(2,4)`, `(3,4)` |

**Constraints**

- `1 <= nums.length <= 5 * 10^4`
- `-2^31 <= nums[i] <= 2^31 - 1`

---

## 2️⃣ Why This Problem Rocks in Interviews  

- **Time Complexity Target:** `O(n log n)` – forces you to think beyond brute force.  
- **Overflow Awareness:** `nums[i] > 2 * nums[j]` → requires 64‑bit math.  
- **Merge‑Sort Insight:** Classic divide‑and‑conquer pattern that is frequently asked.  
- **Edge‑Case Mastery:** Negative numbers, large positives, and integer overflow.  

---

## 3️⃣ The “Good” – The Standard Merge‑Sort Solution  

### 3.1 Concept  
1. **Divide** the array into halves recursively.  
2. **Conquer**: While merging, count the reverse pairs that cross the two halves.  
3. **Combine**: Merge the two sorted halves.  

Because each level of recursion scans the array only once, the overall complexity is `O(n log n)`.

### 3.2 Pseudocode  

```
function reversePairs(nums):
    return mergeSortAndCount(nums, 0, len(nums)-1)

function mergeSortAndCount(arr, left, right):
    if left >= right: return 0
    mid = (left + right) // 2
    count = mergeSortAndCount(arr, left, mid) +
            mergeSortAndCount(arr, mid+1, right)

    // Count cross pairs
    j = mid + 1
    for i from left to mid:
        while j <= right and arr[i] > 2 * arr[j]:
            j += 1
        count += j - (mid + 1)

    // Merge two sorted halves
    merge(arr, left, mid, right)
    return count
```

### 3.3 Code – Java  

```java
import java.util.*;

public class ReversePairs {
    public int reversePairs(int[] nums) {
        return mergeSortAndCount(nums, 0, nums.length - 1);
    }

    private int mergeSortAndCount(int[] nums, int left, int right) {
        if (left >= right) return 0;

        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(nums, left, mid)
                  + mergeSortAndCount(nums, mid + 1, right);

        // Count reverse pairs across left and right halves
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long) nums[i] > 2L * nums[j]) {
                j++;
            }
            count += j - (mid + 1);
        }

        // Merge
        merge(nums, left, mid, right);
        return count;
    }

    private void merge(int[] nums, int left, int mid, int right) {
        int[] leftArr = Arrays.copyOfRange(nums, left, mid + 1);
        int[] rightArr = Arrays.copyOfRange(nums, mid + 1, right + 1);
        int i = 0, j = 0, k = left;

        while (i < leftArr.length && j < rightArr.length) {
            if (leftArr[i] <= rightArr[j]) nums[k++] = leftArr[i++];
            else nums[k++] = rightArr[j++];
        }
        while (i < leftArr.length) nums[k++] = leftArr[i++];
        while (j < rightArr.length) nums[k++] = rightArr[j++];
    }

    public static void main(String[] args) {
        ReversePairs rp = new ReversePairs();
        int[] nums = {1,3,2,3,1};
        System.out.println(rp.reversePairs(nums)); // 2
    }
}
```

### 3.4 Code – Python  

```python
class Solution:
    def reversePairs(self, nums):
        return self.merge_sort_and_count(nums, 0, len(nums)-1)

    def merge_sort_and_count(self, nums, left, right):
        if left >= right:
            return 0
        mid = (left + right) // 2
        count = (self.merge_sort_and_count(nums, left, mid) +
                 self.merge_sort_and_count(nums, mid+1, right))

        # Count cross pairs
        j = mid + 1
        for i in range(left, mid+1):
            while j <= right and nums[i] > 2 * nums[j]:
                j += 1
            count += j - (mid + 1)

        # Merge
        self.merge(nums, left, mid, right)
        return count

    def merge(self, nums, left, mid, right):
        left_part = nums[left:mid+1]
        right_part = nums[mid+1:right+1]
        i = j = 0
        for k in range(left, right+1):
            if j == len(right_part) or (i < len(left_part) and left_part[i] <= right_part[j]):
                nums[k] = left_part[i]
                i += 1
            else:
                nums[k] = right_part[j]
                j += 1
```

### 3.5 Code – C++  

```cpp
class Solution {
public:
    int reversePairs(vector<int>& nums) {
        return mergeSortAndCount(nums, 0, nums.size() - 1);
    }

private:
    int mergeSortAndCount(vector<int>& nums, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSortAndCount(nums, left, mid)
                  + mergeSortAndCount(nums, mid + 1, right);

        // Count reverse pairs across halves
        int j = mid + 1;
        for (int i = left; i <= mid; ++i) {
            while (j <= right && static_cast<long long>(nums[i]) > 2LL * nums[j]) {
                ++j;
            }
            count += j - (mid + 1);
        }

        // Merge
        merge(nums, left, mid, right);
        return count;
    }

    void merge(vector<int>& nums, int left, int mid, int right) {
        vector<int> leftArr(nums.begin() + left, nums.begin() + mid + 1);
        vector<int> rightArr(nums.begin() + mid + 1, nums.begin() + right + 1);
        int i = 0, j = 0, k = left;

        while (i < leftArr.size() && j < rightArr.size()) {
            if (leftArr[i] <= rightArr[j]) nums[k++] = leftArr[i++];
            else nums[k++] = rightArr[j++];
        }
        while (i < leftArr.size()) nums[k++] = leftArr[i++];
        while (j < rightArr.size()) nums[k++] = rightArr[j++];
    }
};
```

---

## 4️⃣ The “Bad” – Common Pitfalls & How to Avoid Them  

| Pitfall | Why It Breaks | Fix |
|---------|--------------|-----|
| **Int overflow** (`nums[i] > 2 * nums[j]`) | `2 * nums[j]` can overflow 32‑bit signed int | Use 64‑bit (`long`/`long long`) when doing the multiplication. |
| **Wrong merge order** | Merging unsorted halves causes pair counting to be wrong | Always merge **after** counting cross pairs; ensure both halves are sorted. |
| **Recursion depth** (Java/C++ recursion limit) | For `n = 5*10^4`, recursion depth ~ 15, safe but some languages may stack‑overflow | Tail‑recursion isn’t required; if needed, convert to iterative bottom‑up merge sort. |
| **Not copying arrays** | Modifying `nums` while iterating can lead to mis‑counts | Create temporary copies (`leftArr`, `rightArr`) before the merge loop. |
| **Negative numbers** | `-1 > 2 * -2` is false, but `-1 > 2 * -3` is true – algorithm still works | No special handling needed; just ensure `2 * nums[j]` is computed in 64‑bit. |

---

## 5️⃣ The “Ugly” – Alternative Approaches (Why You *Shouldn’t* Use Them)  

| Approach | Complexity | Comments |
|----------|------------|----------|
| **Brute Force** (`O(n^2)`) | `O(n^2)` – too slow for `5*10^4`. | Acceptable only for very small inputs. |
| **Binary Indexed Tree (Fenwick)** | `O(n log n)` with coordinate compression | Works, but the cross‑pair counting logic is more complicated than merge‑sort. |
| **Segment Tree** | `O(n log n)` | Similar to BIT; more verbose, harder to explain in an interview. |
| **HashMap + Two‑Pointers** | `O(n log n)` | Needs careful handling of order and duplicates; not a standard pattern. |

If you’re in a time‑crunch interview, stick to the merge‑sort pattern. It’s easy to write, easy to explain, and the interviewer will be impressed.

---

## 6️⃣ Interview Tips & Talking Points  

1. **Explain the intuition first** – say “I’ll use divide & conquer” before jumping into code.  
2. **Mention overflow explicitly** – “I’ll use `long` (Java) / `long long` (C++) to avoid overflow.”  
3. **Show the counting logic** – “While merging, for each `i` in the left half, I’ll move a pointer `j` in the right half until `nums[i] <= 2 * nums[j]`.”  
4. **Demonstrate edge‑case handling** – “The algorithm naturally handles negatives because the inequality is preserved after sorting.”  
5. **Mention time/space** – “Time `O(n log n)`, space `O(n)` due to auxiliary arrays.”  
6. **Optional optimization** – “If you’re concerned about memory, you can merge in place using a single auxiliary array.”  

---

## 7️⃣ Final Takeaway – Why Mastering Reverse Pairs Boosts Your Job Hunt  

- **Showcase algorithmic depth** – `Reverse Pairs` is a classic “reduce the time complexity from O(n²) to O(n log n)” challenge.  
- **Demonstrate robust coding** – You’re using safe arithmetic, handling negative values, and writing clean, testable functions.  
- **Interview readiness** – This problem appears in *Google*, *Facebook*, *Amazon*, and *Microsoft* interviews. Knowing it inside‑out signals strong problem‑solving chops.  
- **Show curiosity** – Explore variations: counting `nums[i] < 2 * nums[j]`, or a generic “count pairs with condition `f(nums[i], nums[j])`”. This shows you’re not just memorizing a solution but truly understanding the pattern.

> **Ready to ace your next coding interview?**  
> Practice the merge‑sort solution above, run it against edge cases, and add it to your portfolio.  
> Add the solution to your GitHub repo, write a short README explaining the algorithm, and share it on LinkedIn with the hashtag #LeetCode493.  
> Recruiters love seeing **clean code + thoughtful explanation**. 🚀  

---  

### 📚 Additional Resources  

- [LeetCode Discuss – 493 Reverse Pairs](https://leetcode.com/problems/reverse-pairs/discuss/)
- [Cracking the Coding Interview – Merge Sort Counting](https://www.crackingthecodinginterview.com)
- [Algorithms Live‑Stream – Divide & Conquer Techniques](https://www.youtube.com/playlist?list=PLs3X0Wl3KJ8J2jQ3v7z3tqH7sS3g6d8xE)

Happy coding, and may your next interview be your dream job! 🎯