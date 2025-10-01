---
title: LeetCode 1095. Find in Mountain Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Code

Below you will find **three complete solutions** for Leet‑Code 1095 – *Find in Mountain Array*.  
Each implementation follows the same O(log n) strategy:

1. Find the peak index (`arr[i] > arr[i+1]`).
2. Binary‑search the ascending part `[0 … peak]`.
3. If not found, binary‑search the descending part `[peak+1 … n‑1]`.

All solutions stay well under the 100 `MountainArray.get()` call limit.

> **Tip:**  
> *Always* prefer the **ascending** search first – it guarantees that if the target appears twice, we return the **minimum** index.

---

### 1.1  Python (3.10+)

```python
# ----------------------------------------------------------
# LeetCode 1095 – Find in Mountain Array
# ----------------------------------------------------------
# The MountainArray interface is provided by LeetCode.
# In a real interview you would not implement it.
class MountainArray:
    def get(self, index: int) -> int: ...
    def length(self) -> int: ...

class Solution:
    def findInMountainArray(self, target: int, mountain_arr: 'MountainArray') -> int:
        n = mountain_arr.length()

        # ---------- helpers ----------------------------------
        def binary_search(left: int, right: int, up: bool) -> int:
            """Standard binary search that takes the
               direction of the mountain into account."""
            while left <= right:
                mid = (left + right) // 2
                mid_val = mountain_arr.get(mid)

                if mid_val == target:
                    return mid

                if up:  # ascending half
                    if target > mid_val:
                        left = mid + 1
                    else:
                        right = mid - 1
                else:   # descending half
                    if target > mid_val:
                        right = mid - 1
                    else:
                        left = mid + 1
            return -1

        def find_peak() -> int:
            """Return the index of the maximum element."""
            lo, hi = 0, n - 1
            while lo < hi:
                mid = (lo + hi) // 2
                # We always compare two neighbouring values.
                if mountain_arr.get(mid) < mountain_arr.get(mid + 1):
                    lo = mid + 1          # go right
                else:
                    hi = mid              # go left
            return lo

        peak = find_peak()

        # 1️⃣  Ascending part
        idx = binary_search(0, peak, True)
        if idx != -1:
            return idx

        # 2️⃣  Descending part
        return binary_search(peak + 1, n - 1, False)
```

> **Runtime:** `O(log n)`  
> **Memory:** `O(1)`

---

### 1.2  Java

```java
// ----------------------------------------------------------
// LeetCode 1095 – Find in Mountain Array (Java)
// ----------------------------------------------------------
public interface MountainArray {
    public int get(int index);
    public int length();
}

public class Solution {
    public int findInMountainArray(int target, MountainArray mountainArr) {
        int n = mountainArr.length();

        // ---------- Find the peak --------------------------------
        int peak = findPeak(mountainArr, n);

        // ---------- Search ascending part -----------------------
        int ascIdx = binarySearch(mountainArr, 0, peak, target, true);
        if (ascIdx != -1) return ascIdx;

        // ---------- Search descending part ----------------------
        return binarySearch(mountainArr, peak + 1, n - 1, target, false);
    }

    private int findPeak(MountainArray arr, int n) {
        int lo = 0, hi = n - 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            // Two get() calls per loop – still < 100 for n <= 10⁴
            if (arr.get(mid) < arr.get(mid + 1)) {
                lo = mid + 1;      // peak is to the right
            } else {
                hi = mid;          // peak is at mid or left
            }
        }
        return lo;
    }

    /**
     * Standard binary search.  If `up` is true we search
     * the ascending half, otherwise we search the descending half.
     */
    private int binarySearch(MountainArray arr, int lo, int hi,
                             int target, boolean up) {
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            int midVal = arr.get(mid);

            if (midVal == target) return mid;

            if (up) {   // ascending
                if (target > midVal) lo = mid + 1;
                else                 hi = mid - 1;
            } else {    // descending
                if (target > midVal) hi = mid - 1;
                else                 lo = mid + 1;
            }
        }
        return -1;
    }
}
```

---

### 1.3  C++17

```cpp
// ----------------------------------------------------------
// LeetCode 1095 – Find in Mountain Array
// ----------------------------------------------------------
class MountainArray {
public:
    virtual int get(int index) = 0;
    virtual int length() = 0;
};

class Solution {
public:
    int findInMountainArray(int target, MountainArray &mountainArr) {
        int n = mountainArr.length();

        auto binarySearch = [&](int lo, int hi, bool up) -> int {
            while (lo <= hi) {
                int mid = lo + (hi - lo) / 2;
                int midVal = mountainArr.get(mid);
                if (midVal == target) return mid;

                if (up) {                      // ascending
                    if (target > midVal) lo = mid + 1;
                    else                 hi = mid - 1;
                } else {                        // descending
                    if (target > midVal) hi = mid - 1;
                    else                 lo = mid + 1;
                }
            }
            return -1;
        };

        // ---------- Find peak ----------
        int lo = 0, hi = n - 1;
        while (lo < hi) {
            int mid = lo + (hi - lo) / 2;
            if (mountainArr.get(mid) < mountainArr.get(mid + 1))
                lo = mid + 1;
            else
                hi = mid;
        }
        int peak = lo;

        // ---------- Search ascending part ----------
        int idx = binarySearch(0, peak, true);
        if (idx != -1) return idx;

        // ---------- Search descending part ----------
        return binarySearch(peak + 1, n - 1, false);
    }
};
```

> **Why the lambda works** – C++17 lets us capture by reference (`[&]`) so the helper can access `mountainArr` directly without needing a separate member function.

---

## 2.  Blog Article – “The Good, The Bad & The Ugly of Finding in Mountain Array”

---

### 2.1  Title & Meta‑Description

**Title**  
> **Find in Mountain Array – LeetCode 1095: A Quick‑Guide to a 100‑Call Binary‑Search Mastery**

**Meta‑Description**  
> Master LeetCode 1095 with an O(log n) solution that respects the 100 `MountainArray.get()` limit. Learn the trade‑offs, pitfalls and interview‑ready best practices. Perfect your coding interview prep for your next tech‑job!

---

### 2.2  Introduction

Finding an element in a *mountain array* is a classic interview problem that tests three core concepts:

| Concept | Why It Matters |
|---------|----------------|
| **Binary Search** | Fast O(log n) time, interview staple |
| **Peak Identification** | The heart of a mountain array – a twist on classic monotonic arrays |
| **API Call Limitation** | Real‑world constraints (LeetCode’s 100‑call limit) mirror “API‑based” interview questions |

If you’re preparing for a software‑engineering interview, solving *Find in Mountain Array* demonstrates:

* Deep understanding of binary search variants.
* Ability to design an algorithm under API constraints.
* Proficiency in three mainstream languages – Python, Java, C++.

---

### 2.3  Problem Statement (LeetCode 1095)

> **Given** a *mountain array* (strictly increasing then strictly decreasing) and a `target` integer, return the *smallest* index `i` such that `mountain_arr.get(i) == target`.  
> **If** the target is not present, return `-1`.  
> **Constraints**  
> * `n ≤ 10⁴` (array length)  
> * `MountainArray.get()` may be called at most **100** times  
> * The array satisfies the mountain property:  
>   `arr[0] < arr[1] < … < arr[peak] > arr[peak+1] > … > arr[n‑1]`  

---

### 2.4  The Good

1. **Optimal Complexity** – O(log n) time, O(1) space.  
2. **Deterministic API‑call Bound** – ≤ (2 × log₂n) + (2 × log₂n) < 100.  
3. **Minimal Code Duplication** – The same binary‑search routine works for both ascending and descending halves.  
4. **Readability** – Clear helper functions (`findPeak`, `binarySearch`) with descriptive comments.  
5. **Robustness** – Handles all edge cases (target only in ascending half, only in descending half, duplicate values, peak at index 1 or n‑2).

---

### 2.5  The Bad

| Bad | Reason | Fix |
|-----|--------|-----|
| Using **recursion** for binary search | Each recursive call could inflate the stack; hard to reason about `MountainArray.get()` call count | Use **iterative** loops |
| Ignoring the 100‑call limit | Naïve linear scans or multiple redundant `get()` calls can easily exceed 100 | Two `get()` calls per binary‑search level → < 100 for `n ≤ 10⁴` |
| Not returning the **minimum** index when the target appears twice | Interviewers usually expect the lowest index | Search ascending part first; only search descending part if needed |

---

### 2.6  The Ugly

Some candidates over‑optimize by:

* Implementing a **binary search tree** or **hash map** to cache `get()` results – unnecessary for this problem.  
* Using fancy bit‑manipulation or inline assembly to speed up `get()` – not required and may confuse reviewers.  
* Attempting to find the *peak by checking every element* – defeats the purpose of the problem.

The *ugly* part is simply: **Don’t make the algorithm more complicated than it needs to be**.

---

### 2.7  Step‑by‑Step Walkthrough

1. **Locate the Peak**  
   *Binary search the slope* – compare `mid` and `mid+1`.  
   *If* `arr[mid] < arr[mid+1]`, move **right**; else move **left**.  
   *Result*: Peak index `p`.  

2. **Binary Search on Ascending Half**  
   *Standard binary search* but with `lo`/`hi` boundaries `[0, p]`.  
   *Direction*: If `target > midVal` → `lo = mid + 1`.  
   *If* found, return the index immediately.  

3. **Binary Search on Descending Half**  
   Same routine but reverse the comparison logic.  

4. **Return** `-1` if both halves fail.

---

### 2.7  Code Highlight – Python Version

```python
def binary_search(left, right, up):
    while left <= right:
        mid = (left + right) // 2
        mid_val = mountain_arr.get(mid)
        if mid_val == target:
            return mid
        if up:  # ascending
            left = mid + 1 if target > mid_val else right = mid - 1
        else:   # descending
            right = mid - 1 if target > mid_val else left = mid + 1
    return -1
```

*Notice the concise ternary‑style update:*  
`left = mid + 1 if target > mid_val else right = mid - 1` – this keeps the helper DRY across both halves.

---

### 2.8  Takeaway & Interview Tips

| Tip | Implementation |
|-----|----------------|
| **Test with extremes** | `n = 2`, `target` at start/end | Verify boundary conditions in your helper |
| **Check API call counter** | Print `get()` count while debugging | Ensure recursion depth ≈ log₂n |
| **Use same binary search routine** | Write a flag (`up`) to indicate direction | Reduces maintenance burden |
| **Explain your reasoning** | Show your thought process (peak, ascending search, descending search) | Interviewers appreciate clear explanations |

---

### 2.9  Conclusion

*Find in Mountain Array* is a micro‑ecosystem of algorithmic reasoning:

1. *Find the peak* – a binary search on a *comparison* rather than *value*.  
2. *Search both halves* – a single, direction‑aware routine suffices.  
3. *Respect API limits* – two `get()` per iteration keeps the call count < 100.

The presented solution, implemented in **Python, Java, and C++**, demonstrates a clean, optimal, interview‑ready strategy. Practice this today, and you’ll walk into your next tech‑company interview with confidence!

---

### 2.10  Call‑to‑Action

*Download* the three‑language solution from the repository above and run your own unit tests.  
*Share* this article on LinkedIn or Twitter with the hashtag `#LeetCode1095` and tag your peers for a friendly coding challenge.

---

## 3.  Final Thoughts

By mastering the *Find in Mountain Array* problem and understanding the good, bad, and ugly aspects of its solution, you’ll:

* Be prepared for API‑driven interview questions.  
* Showcase your ability to stay within strict call limits.  
* Display clear, maintainable code across Python, Java, and C++.

Good luck on your next tech interview – you’ve got this! 🚀
--- 

--- 

*Prepared by: [Your Name], software‑engineering interview coach.* 
--- 
**End of article**