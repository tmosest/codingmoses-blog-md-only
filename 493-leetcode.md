---
title: LeetCode 493. Reverse Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 493 – Reverse Pairs  
**Hard** – LeetCode  
**Signature**

| Language | Signature |
|----------|-----------|
| Java | `public int reversePairs(int[] nums)` |
| Python | `def reversePairs(nums: List[int]) -> int:` |
| C++ | `int reversePairs(vector<int>& nums)` |

A *reverse pair* is a pair `(i, j)` such that  
`0 ≤ i < j < nums.length` **and** `nums[i] > 2 * nums[j]`.

---

## Why the problem is a great interview question

| Strength | What it tests |
|----------|---------------|
| **Algorithmic thinking** | Ability to transform a seemingly O(n²) counting problem into an O(n log n) one. |
| **Edge‑case handling** | Proper use of `long`/`long long` to avoid overflow. |
| **Clean code** | Recursion, divide‑and‑conquer, and merge‑sort pattern are classic interview staples. |

---

## Solution Overview – Merge‑Sort + Two‑Pointer Counting

The classic way to solve this problem is to adapt **merge‑sort**:

1. **Divide** the array into halves until we have sub‑arrays of length 1.  
2. **Conquer** while merging the two sorted halves:
   * Before actually merging, count all cross‑pairs `(i, j)` where  
     `i` belongs to the left half and `j` belongs to the right half, using two pointers.
   * Then merge the two halves into one sorted sub‑array.

Because the two halves are already sorted, we can count the cross pairs in linear time during the merge step.  
Overall complexity: **O(n log n)** time and **O(n)** auxiliary space (the temporary array used for merging).

### Counting Cross Pairs

For each element `left[i]` in the left half, we want to know how many elements `right[j]` satisfy  
`left[i] > 2 * right[j]`.  
Because the right half is sorted, we can keep a moving pointer `j` that never goes backwards:

```text
for i in left:
    while j < right.size and left[i] > 2 * right[j]:
        j++
    count += j          // all indices before j satisfy the condition
```

---

## 1. Java Implementation

```java
import java.util.*;

public class Solution {
    public int reversePairs(int[] nums) {
        if (nums == null || nums.length < 2) return 0;
        int[] temp = new int[nums.length];
        return mergeSort(nums, temp, 0, nums.length - 1);
    }

    private int mergeSort(int[] nums, int[] temp, int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSort(nums, temp, left, mid)
                  + mergeSort(nums, temp, mid + 1, right);

        // Count reverse pairs across the halves
        int j = mid + 1;
        for (int i = left; i <= mid; i++) {
            while (j <= right && (long) nums[i] > 2L * nums[j]) j++;
            count += j - (mid + 1);
        }

        // Merge the two halves
        int p = left, q = mid + 1, k = left;
        while (p <= mid && q <= right) {
            if (nums[p] <= nums[q]) temp[k++] = nums[p++];
            else temp[k++] = nums[q++];
        }
        while (p <= mid) temp[k++] = nums[p++];
        while (q <= right) temp[k++] = nums[q++];

        // Copy back to original array
        System.arraycopy(temp, left, nums, left, right - left + 1);
        return count;
    }
}
```

**Key points**

- `long` is used for `nums[i] > 2L * nums[j]` to avoid overflow when `nums[j]` is negative.
- The temporary array `temp` is reused to keep memory allocation minimal.

---

## 2. Python Implementation

```python
from typing import List

class Solution:
    def reversePairs(self, nums: List[int]) -> int:
        if not nums or len(nums) < 2:
            return 0
        temp = [0] * len(nums)
        return self._merge_sort(nums, temp, 0, len(nums) - 1)

    def _merge_sort(self, nums, temp, left, right):
        if left >= right:
            return 0
        mid = (left + right) // 2
        count = (self._merge_sort(nums, temp, left, mid) +
                 self._merge_sort(nums, temp, mid + 1, right))

        # Count cross pairs
        j = mid + 1
        for i in range(left, mid + 1):
            while j <= right and nums[i] > 2 * nums[j]:
                j += 1
            count += j - (mid + 1)

        # Merge step
        p, q, k = left, mid + 1, left
        while p <= mid and q <= right:
            if nums[p] <= nums[q]:
                temp[k] = nums[p]
                p += 1
            else:
                temp[k] = nums[q]
                q += 1
            k += 1
        while p <= mid:
            temp[k] = nums[p]
            p += 1
            k += 1
        while q <= right:
            temp[k] = nums[q]
            q += 1
            k += 1

        nums[left:right+1] = temp[left:right+1]
        return count
```

**Python specifics**

- No need for explicit `long`; Python integers have arbitrary precision.
- The slice assignment `nums[left:right+1] = temp[left:right+1]` copies back the sorted segment.

---

## 3. C++ Implementation

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int reversePairs(vector<int>& nums) {
        if (nums.size() < 2) return 0;
        vector<long long> temp(nums.size());
        return mergeSort(nums, temp, 0, nums.size() - 1);
    }

private:
    int mergeSort(vector<int>& nums, vector<long long>& temp,
                  int left, int right) {
        if (left >= right) return 0;
        int mid = left + (right - left) / 2;
        int count = mergeSort(nums, temp, left, mid)
                  + mergeSort(nums, temp, mid + 1, right);

        // Count reverse pairs
        int j = mid + 1;
        for (int i = left; i <= mid; ++i) {
            while (j <= right && (long long)nums[i] > 2LL * nums[j])
                ++j;
            count += j - (mid + 1);
        }

        // Merge two halves
        int p = left, q = mid + 1, k = left;
        while (p <= mid && q <= right) {
            if (nums[p] <= nums[q]) temp[k++] = nums[p++];
            else temp[k++] = nums[q++];
        }
        while (p <= mid) temp[k++] = nums[p++];
        while (q <= right) temp[k++] = nums[q++];

        for (int idx = left; idx <= right; ++idx)
            nums[idx] = (int)temp[idx];

        return count;
    }
};
```

**C++ notes**

- `temp` is a vector of `long long` to avoid overflow when performing `2 * nums[j]`.  
- We cast back to `int` when copying back because the original array is `int`.

---

## 4. Blog Article – “Reverse Pairs (LeetCode 493): The Good, the Bad, and the Ugly”

---

### Title  
**Reverse Pairs – LeetCode 493 – The Good, The Bad & The Ugly (Java, Python & C++)**  

---

### Introduction (SEO‑friendly hook)  

> Looking for a *hard* LeetCode interview problem that will make hiring managers smile?  
> **Reverse Pairs** (LeetCode 493) is the perfect blend of algorithmic elegance and practical difficulty.  
> In this post we’ll dissect the problem, walk through the **divide‑and‑conquer** solution, provide clean Java, Python, and C++ implementations, and discuss pitfalls that could sabotage your interview performance.  
> If you’re targeting a software‑engineering role, mastering this question will boost your technical score and showcase your problem‑solving chops.

*Keywords:* Reverse Pairs, LeetCode 493, interview coding, algorithmic problem, merge sort, two-pointer technique, job interview, software engineering interview.

---

### 1. Problem Recap

> **Given** an integer array `nums`, count the number of reverse pairs:  
> `(i, j)` where `0 ≤ i < j < nums.length` and `nums[i] > 2 * nums[j]`.  

*Typical challenge:* naive `O(n²)` solutions fail on the maximum input size (`10⁵` elements). We need something faster.

---

### 2. The “Good” – Why Merge‑Sort Works

| ✅ Feature | Why it matters |
|------------|----------------|
| **O(n log n) time** | Beats brute‑force by a huge margin. |
| **Sorted sub‑arrays** | Allows linear‑time cross‑pair counting with a moving pointer. |
| **Reusable temporary buffer** | Keeps auxiliary space bounded to `O(n)` – a common interview requirement. |
| **Language‑agnostic pattern** | Same logic applies to Java, Python, C++ – great for portfolio diversity. |

**Takeaway**: The clever insight is that *sorting* the array simultaneously solves two tasks: it gives us the order we need to count cross pairs, and it lets us merge in place.  

---

### 2. The “Bad” – Things That Get in the Way

| ❌ Issue | Why it’s a problem |
|----------|---------------------|
| **Overflow** | `nums[i] > 2 * nums[j]` can overflow `int` when `nums[j]` is negative. Using `long`/`long long` is mandatory. |
| **Index Off‑by‑One** | Forgetting that the right half starts at `mid+1` or using `<=` instead of `<` can silently double‑count or miss pairs. |
| **Recursion Stack Depth** | Python’s default recursion limit (usually 1000) can break on a `10⁵`‑size array. Use an explicit stack or raise the limit. |
| **In‑Place vs. Extra Space** | Some candidates copy back to `nums` after merging; others forget, leading to unsorted halves and wrong counts. |

**Bottom line:** Even a perfectly‑working algorithm can fail if you overlook these small but critical details.

---

### 3. The “Ugly” – Where Bugs Hide

1. **Negative Numbers** – The inequality flips when `nums[j]` is negative. The condition `nums[i] > 2 * nums[j]` still holds, but the two‑pointer loop must handle it correctly; otherwise you might skip valid pairs.
2. **Integer Division Pitfall** – Using `mid = (left + right) / 2` is fine in most languages, but in C++ the expression may overflow if `left + right` is large. Prefer `mid = left + (right - left) / 2`.
3. **Temporary Array Reuse** – Accidentally creating a new temporary array at every recursion level will inflate memory usage and may trigger a *Time Limit Exceeded* verdict on tight judges. Reuse a single buffer as shown in the implementations.
4. **Copy‑Back Logic** – In C++/Java, using `System.arraycopy` or a `for` loop ensures the merged segment is written back correctly. A missing copy‑back turns a correct counting phase into an invalid state for the next merge.

---

### 4. Full Code Snippets

(Refer to the **Java**, **Python**, and **C++** sections above.)

> **Tip:** During an interview, first write the counting logic, then test it on a small example (`[1,3,2,3,1] → 2`), and finally add the merge step. This incremental approach keeps the mental load manageable.

---

### 5. Quick Test Cases

| Input | Expected Output |
|-------|-----------------|
| `[1,3,2,3,1]` | `2` |
| `[2,4,3,5,1]` | `3` |
| `[]` | `0` |
| `[-5,-4,-3,-2,-1]` | `0` |
| `[10,10,10]` | `0` |

Run the code in any language; all three versions will produce the same results.

---

### 6. How to Discuss This in an Interview

1. **Explain the intuition** – “Because we only care about cross pairs, we can use the sorted halves to count them linearly.”
2. **Show the two‑pointer proof** – Walk through a single merge step with example numbers to illustrate the counting logic.
3. **Mention overflow** – “I’ll cast to `long`/`long long` before the multiplication to keep the comparison safe.”
4. **Ask for clarification** – If the interviewer is ambiguous about array bounds, verify whether negative numbers are allowed (they are).
5. **Edge‑case check** – “If the array length is less than two, the answer is obviously zero; this short‑circuit speeds up tiny inputs.”

---

### 7. Closing Thoughts – Turning Reverse Pairs Into a Job‑Winning Skill

> *Reverse Pairs* is more than a “hard” problem; it’s a **showcase** of how you can transform a brute‑force idea into an optimal algorithm.  
> By mastering the merge‑sort + two‑pointer technique, you’ll not only solve LeetCode 493 but also gain a reusable pattern for other counting problems (e.g., *count smaller numbers after self*, *inversion count*, *count sub‑arrays with sum ≤ K*).  

**Action items**

1. **Implement** the three solutions and run them against the test harness below.  
2. **Time yourself** – aim for < 1 second on a typical machine.  
3. **Explain** each step in plain English; interviewers love a clear communicator.  

> Good luck, and remember: the *hard* problems are the ones that let you shine!  

---  

### Code‑Test Harness (All Languages)

```bash
# Example usage (assuming the class is named Solution)

python3 - <<'PY'
from typing import List
def test(nums: List[int]):
    from solution import Solution
    print(Solution().reversePairs(nums))

test([1,3,2,3,1])  # 2
test([2,4,3,5,1])  # 3
PY
```

Replace `from solution import Solution` with the actual filename in your environment.

---

### Final Word

> *The Good* – a clean, optimal algorithm.  
> *The Bad* – a fragile implementation prone to index bugs and overflow.  
> *The Ugly* – hidden pitfalls that can trip up even seasoned coders.  

Tackling Reverse Pairs with confidence demonstrates not only your coding prowess but also your ability to debug, optimize, and communicate.  
Add this to your interview prep list, and you’ll be one step closer to landing that software‑engineering role. Happy coding! 🚀

--- 

*If you found this article helpful, share it on LinkedIn, Tweet it with the tag `#LeetCode493`, or let us know in the comments what other interview problems you’d like a “Good, Bad, Ugly” breakdown for!*