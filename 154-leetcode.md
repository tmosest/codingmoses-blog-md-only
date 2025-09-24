---
title: LeetCode 154. Find Minimum in Rotated Sorted Array II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  The Code – Three Languages

Below are three compact, production‑ready solutions for **LeetCode 154 – Find Minimum in Rotated Sorted Array II**.  
All three use the same binary‑search idea and gracefully handle the presence of duplicate values.

---

## 1.1  Java

```java
/*  Java 17  */
import java.util.*;

public class Solution {
    /**
     * Finds the minimum element in a rotated sorted array that may contain duplicates.
     * @param nums  non‑empty array of integers, rotated 1..n times
     * @return the smallest element
     */
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;

        while (left < right) {
            int mid = left + (right - left) / 2;

            if (nums[mid] > nums[right]) {
                // min is in the right half
                left = mid + 1;
            } else if (nums[mid] < nums[right]) {
                // min is in the left half (including mid)
                right = mid;
            } else {
                // nums[mid] == nums[right] – can't decide, shrink safely
                right--;
            }
        }
        return nums[left];
    }
}
```

---

## 1.2  Python

```python
#  Python 3
class Solution:
    def findMin(self, nums: List[int]) -> int:
        """
        Returns the minimum element in a rotated sorted array with duplicates.
        Uses a binary‑search variant that degrades to linear time only when duplicates
        make the decision ambiguous.
        """
        left, right = 0, len(nums) - 1
        while left < right:
            mid = (left + right) // 2
            if nums[mid] > nums[right]:
                left = mid + 1
            elif nums[mid] < nums[right]:
                right = mid
            else:          # nums[mid] == nums[right]
                right -= 1
        return nums[left]
```

---

## 1.3  C++

```cpp
//  C++17
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0, right = (int)nums.size() - 1;
        while (left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) {
                left = mid + 1;
            } else if (nums[mid] < nums[right]) {
                right = mid;
            } else {               // nums[mid] == nums[right]
                --right;
            }
        }
        return nums[left];
    }
};
```

---

# 2.  Blog Article – “The Good, the Bad, and the Ugly of Finding the Minimum in a Rotated Sorted Array II”

## Title  
**The Good, the Bad, and the Ugly of LeetCode 154 – Find Minimum in Rotated Sorted Array II**

## Meta‑Description  
Master LeetCode 154 in Java, Python, and C++ with a clear binary‑search solution that handles duplicates. Learn the pitfalls, runtime trade‑offs, and interview tips that will impress hiring managers.

---

## 2.1  Introduction  

*If you’ve ever stared at a rotated sorted array that contains duplicates, you’ve probably felt a mix of frustration and curiosity.*  
LeetCode 154, “Find Minimum in Rotated Sorted Array II”, is a classic interview problem that tests:

- **Binary‑search intuition**  
- **Handling edge cases** (especially duplicates)  
- **Understanding runtime guarantees**

Below, we dissect the problem, walk through a clean solution in three languages, and highlight the **good**, **bad**, and **ugly** aspects that interviewers love to probe.

---

## 2.2  Problem Recap  

> **Input** – `nums`, a non‑empty array of integers, originally sorted in ascending order but rotated between 1 and `n` times.  
> **Duplicates allowed** – e.g., `[2,2,2,0,1]`.  
> **Goal** – Return the smallest element.  

### Constraints  
- `1 ≤ n ≤ 5000`  
- `-5000 ≤ nums[i] ≤ 5000`

---

## 2.3  Naïve Approaches – The “Ugly” Ones  

| Approach | Complexity | Why It’s Ugly |
|----------|------------|---------------|
| **Linear scan** – iterate and keep a running minimum | **O(n)** time, **O(1)** space | Works but defeats the purpose of “rotated” (no logarithmic speed‑up). |
| **Full binary search** without handling duplicates | **O(log n)** average but **incorrect** for cases like `[2,2,2,0,1]` | Fails because the pivot can’t be decided when `mid == right`. |
| **Sorting the array** and picking the first element | **O(n log n)** time, **O(n)** space | Overkill; destroys the O(1) space requirement. |

The challenge is to *keep the best* (logarithmic speed) while *tolerating the worst* (duplicates that blur the decision boundary).

---

## 2.4  The Optimal Binary‑Search – The “Good” Part  

### 2.4.1  Intuition  

In a *rotated* sorted array without duplicates, the pivot point (where the array wraps) can be found because the rightmost element is always part of the “higher” half.  
If `nums[mid] > nums[right]`, the minimum lies in the **right** half; otherwise it’s in the **left** half (including `mid`).  

### 2.4.2  Handling Duplicates – The “Ugly” Twist  

When `nums[mid] == nums[right]`, we cannot determine which side holds the minimum.  
The safest move is to shrink the search window by *one* element: `right--`.  
This may degrade performance to **O(n)** in the worst case (e.g., an array full of the same value), but guarantees correctness.

### 2.4.3  Step‑by‑Step (Python)  

```python
left, right = 0, len(nums) - 1
while left < right:
    mid = (left + right) // 2
    if nums[mid] > nums[right]:
        left = mid + 1          # min in right half
    elif nums[mid] < nums[right]:
        right = mid             # min in left half (mid included)
    else:                       # nums[mid] == nums[right]
        right -= 1              # shrink window safely
return nums[left]
```

The same logic applies to Java and C++ (see the code section above).

### 2.4.4  Complexity Analysis  

| Scenario | Time | Space |
|----------|------|-------|
| **Best / average** (few duplicates) | **O(log n)** | **O(1)** |
| **Worst** (all elements equal) | **O(n)** | **O(1)** |

The worst‑case scenario occurs only when the array is heavily duplicated; in real interview data this is rare, so the algorithm is still “fast enough”.

---

## 2.5  Common Pitfalls & Interview‑Ready Tips  

| Pitfall | Fix | Interview Tip |
|---------|-----|---------------|
| Using `mid = (left + right) / 2` without overflow check | `mid = left + (right - left) / 2` | Show awareness of integer overflow (important in Java/C++). |
| Forgetting to move `right` when `nums[mid] == nums[right]` | `right -= 1` | Emphasize that this is *necessary* for correctness with duplicates. |
| Returning `nums[right]` instead of `nums[left]` after loop | Return `nums[left]` | After `left == right`, either is fine, but using `left` keeps logic consistent. |
| Assuming the array is rotated at least once | Check `nums[0] < nums[-1]` to skip binary search if not rotated | Demonstrates edge‑case awareness. |

---

## 2.6  The “Bad” – When It Becomes Inefficient  

If an interviewer feeds a dataset like `[1,1,1,1,1,0]`, the algorithm will perform `n-1` iterations because `right--` never eliminates the ambiguity.  
While still correct, this reveals a **time‑complexity limitation** that you should discuss:  

> *“Duplicates can cause the binary search to degrade to linear time. In practice, however, LeetCode’s test cases are balanced, so the algorithm remains efficient.”*

This is an excellent moment to ask clarifying questions: *“Will the test data contain many duplicates?”* and to explain your trade‑offs.

---

## 2.7  Extensions & Variations  

| Variation | What to consider | Suggested Approach |
|-----------|------------------|--------------------|
| **LeetCode 33 – Search in Rotated Sorted Array** | No duplicates, target search | Classic binary search with pivot detection |
| **LeetCode 148 – Sort List** | Linked list, O(n log n) time | Bottom‑up merge sort (no extra memory) |
| **Arrays with Negative Numbers** | Same logic applies | No change; `int` handles negative values |

These are good follow‑up topics for a deeper interview discussion.

---

## 2.8  Takeaway for the Job‑Seeker  

1. **Know the core binary‑search trick** – pivot detection via `nums[mid] > nums[right]`.  
2. **Handle duplicates gracefully** – `right--` is the simplest, correct, and interview‑friendly technique.  
3. **Explain complexity trade‑offs** – show awareness that the worst‑case is linear but the average is logarithmic.  
4. **Provide clean, commented code** – as we did in Java, Python, and C++.  
5. **Speak about edge cases** – such as already sorted arrays or arrays with all equal elements.

If you can articulate these points during an interview, you’ll impress any hiring manager with both your algorithmic thinking and your communication skills.

---

## 2.9  Final Code Snippets (All Three Languages)

```java
// Java
public int findMin(int[] nums) {
    int left = 0, right = nums.length - 1;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] > nums[right]) left = mid + 1;
        else if (nums[mid] < nums[right]) right = mid;
        else right--;
    }
    return nums[left];
}
```

```python
# Python
def findMin(self, nums: List[int]) -> int:
    left, right = 0, len(nums) - 1
    while left < right:
        mid = (left + right) // 2
        if nums[mid] > nums[right]:
            left = mid + 1
        elif nums[mid] < nums[right]:
            right = mid
        else:
            right -= 1
    return nums[left]
```

```cpp
// C++
int findMin(vector<int>& nums) {
    int left = 0, right = nums.size() - 1;
    while (left < right) {
        int mid = left + (right - left) / 2;
        if (nums[mid] > nums[right]) left = mid + 1;
        else if (nums[mid] < nums[right]) right = mid;
        else --right;
    }
    return nums[left];
}
```

---

## 2.10  Closing Thoughts  

LeetCode 154 is more than a puzzle; it’s a **communication test**.  
Master the binary‑search logic, keep your code readable, and be ready to discuss how duplicates affect runtime.  

That’s the perfect blend of the **good, the bad, and the ugly** that any senior software engineering role expects.

---  

*Happy coding, and good luck with your interviews!*