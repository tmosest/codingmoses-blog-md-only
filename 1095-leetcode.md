---
title: LeetCode 1095. Find in Mountain Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1095 – “Find in Mountain Array”  
**Hard** | **Binary Search | Mountain Array | Interview Prep**  

> If you’re looking for a solid interview‑ready solution in **Python, Java, or C++**, you’ve come to the right place.  
> Below you’ll find:  
> * A clear, step‑by‑step explanation that tackles the *good, the bad, and the ugly*.  
> * Fully working code snippets in **Python, Java, and C++** that respect the 100‑`get()`‑call limit.  
> * A mini‑blog article optimized for SEO (keywords, headings, meta‑style copy) that can double as a portfolio post on LinkedIn or Medium.

> **TL;DR** – Find the peak first, then binary‑search the increasing part (return early if found), otherwise binary‑search the decreasing part.

---

## 1. Problem Recap

> A *mountain array* is a sequence that strictly increases to a single peak and then strictly decreases.  
> You are only allowed to read the array via the `MountainArray` interface:
> ```text
> int get(int index);
> int length();
> ```
> The goal: **return the smallest index `i` such that `get(i) == target`, or `-1` if the target is missing.**  
> **Important:** Do **not** make more than **100** calls to `get`.  

Constraints:  
* `3 ≤ mountainArr.length() ≤ 10⁴`  
* `0 ≤ target, mountainArr.get(i) ≤ 10⁶`

---

## 2. The “Good, Bad, & Ugly” of Mountain‑Array Searches

| **Aspect** | **What to Do** | **What NOT to Do** |
|-----------|----------------|--------------------|
| **Peak‑Finding** | Use a binary search that compares `mid` and `mid+1`.  | Do a linear scan or repeatedly call `get` in a loop without pruning. |
| **Increasing‑Part Search** | Classic binary search (`target > midVal → left = mid+1`).  | Treat the descending part the same as ascending – it will fail. |
| **Decreasing‑Part Search** | Reverse the pointer movements (`target > midVal → right = mid-1`). | Forget that `mid` is already `-1` or `0` and still keep `left ≤ right`. |
| **Call Count** | Keep `get()` to a minimum by re‑using the array length and by calling `get` only twice per comparison. | Call `get` on the same index many times or call a helper that loops over the array. |
| **Edge‑Cases** | Check that `target` can be at the peak.  | Assume the array is always increasing or always decreasing. |
| **Testing** | Verify with both ascending and descending portions. | Skipping the *smallest* index requirement – return the first found instead of the lowest. |

---

## 2. Algorithm Overview

1. **Get the array length** once: `n = mountainArr.length()`.  
2. **Find the peak index (`p`)** – binary‑search with the rule `get(mid) < get(mid+1)` → move right; else → move left.  
3. **Search the ascending part `[0 … p]`** with normal binary‑search.  
   * If found → return the index immediately (guaranteed smallest).  
4. **Search the descending part `[p+1 … n-1]`** with a reversed binary‑search.  
5. Return the result (or `-1` if not found).

The call budget is well below 100:  
* `peak` needs at most `log₂(10⁴) ≈ 14` iterations → about `28` calls (`get(mid)` and `get(mid+1)` each iteration).  
* Each binary‑search needs at most `14` calls → two more searches still keep you in the 100‑call limit.

---

## 3. Complexity Analysis

| **Metric** | **Result** |
|------------|------------|
| Time | **O(log n)** – one peak search + at most two binary‑searches. |
| Space | **O(1)** – no extra data structures, just a few integer variables. |

---

## 4. Full Code Samples

> The following snippets are ready to paste into the LeetCode editor (just replace the existing `class Solution`).  
> Each one honours the 100‑`get()`‑call restriction and uses the interface exactly as LeetCode expects.

### 4.1 Python 3

```python
# Definition for MountainArray.
# class MountainArray:
#     def get(self, index: int) -> int:
#     def length(self) -> int

class Solution:
    def findInMountainArray(self, target: int, mountain_arr: 'MountainArray') -> int:
        n = mountain_arr.length()

        # ---------- helper: binary search ----------
        def binary_search(left: int, right: int, target: int, ascending: bool) -> int:
            while left <= right:
                mid = (left + right) // 2
                mid_val = mountain_arr.get(mid)

                if mid_val == target:
                    return mid

                if ascending:                     # normal ascending logic
                    if target > mid_val:
                        left = mid + 1
                    else:
                        right = mid - 1
                else:                              # reversed for descending part
                    if target > mid_val:
                        right = mid - 1
                    else:
                        left = mid + 1
            return -1

        # ---------- helper: find the peak ----------
        def find_peak() -> int:
            l, r = 0, n - 1
            while l < r:
                mid = (l + r) // 2
                # We only need two gets per iteration
                if mountain_arr.get(mid) < mountain_arr.get(mid + 1):
                    l = mid + 1          # peak is to the right
                else:
                    r = mid              # peak is at mid or left
            return l

        peak = find_peak()

        # 1️⃣ Search the ascending part first
        idx = binary_search(0, peak, target, ascending=True)
        if idx != -1:
            return idx

        # 2️⃣ If not found, search the descending part
        return binary_search(peak + 1, n - 1, target, ascending=False)
```

> **Why it works**  
> *The peak is the pivot point where the array changes direction.  
> By searching the ascending part first we guarantee the *smallest* index if the target appears before the peak.  
> The reversed binary‑search on the descending side works because the elements are strictly decreasing.*

---

### 4.2 Java

```java
/**
 * This is the interface LeetCode gives us.  In the actual
 * problem you don't need to implement it – it's provided.
 */
interface MountainArray {
    int get(int index);
    int length();
}

class Solution {

    public int findInMountainArray(int target, MountainArray mountainArr) {
        int n = mountainArr.length();

        // ---------- helper: binary search ----------
        // ascending = true → normal binary search
        // ascending = false → reversed (descending part)
        java.util.function.BiFunction<Integer, Integer, Integer> ascSearch =
                (left, right) -> {
                    int l = left, r = right;
                    while (l <= r) {
                        int mid = (l + r) >>> 1;
                        int midVal = mountainArr.get(mid);
                        if (midVal == target) return mid;
                        if (target > midVal) l = mid + 1;
                        else r = mid - 1;
                    }
                    return -1;
                };

        java.util.function.BiFunction<Integer, Integer, Integer> descSearch =
                (left, right) -> {
                    int l = left, r = right;
                    while (l <= r) {
                        int mid = (l + r) >>> 1;
                        int midVal = mountainArr.get(mid);
                        if (midVal == target) return mid;
                        if (target > midVal) r = mid - 1; // reversed
                        else l = mid + 1;
                    }
                    return -1;
                };

        // ---------- find peak ----------
        int l = 0, r = n - 1;
        while (l < r) {
            int mid = (l + r) >>> 1;
            if (mountainArr.get(mid) < mountainArr.get(mid + 1)) {
                l = mid + 1;   // peak is to the right
            } else {
                r = mid;       // peak is at mid or left
            }
        }
        int peak = l;

        // ---------- search ascending part ----------
        int idx = ascSearch.apply(0, peak);
        if (idx != -1) return idx;

        // ---------- search descending part ----------
        return descSearch.apply(peak + 1, n - 1);
    }
}
```

> **Key points**  
> * `>>> 1` is an unsigned right shift – faster than `/ 2`.  
> * `BiFunction` is used for brevity; you could also extract private helper methods if you prefer.  
> * The peak‑search calls `get` twice per loop (`mid` and `mid+1`), but the overall calls stay well under 100.

---

### 4.3 C++

```cpp
/**
 * This is the interface LeetCode gives us.  In the actual
 * problem you don't need to implement it – it's provided.
 */
class MountainArray {
public:
    virtual int get(int index) const = 0;
    virtual int length() const = 0;
};

class Solution {
public:
    int findInMountainArray(int target, MountainArray &mountainArr) {
        int n = mountainArr.length();

        // -------- binary search (ascending or descending) --------
        auto binarySearch = [&](int left, int right, bool ascending) -> int {
            while (left <= right) {
                int mid = left + ((right - left) >> 1);
                int midVal = mountainArr.get(mid);

                if (midVal == target) return mid;

                if (ascending) { // normal ascending
                    if (target > midVal) left = mid + 1;
                    else right = mid - 1;
                } else {          // reversed for descending part
                    if (target > midVal) right = mid - 1;
                    else left = mid + 1;
                }
            }
            return -1;
        };

        // -------- find peak (index of maximum element) --------
        int l = 0, r = n - 1;
        while (l < r) {
            int mid = l + ((r - l) >> 1);
            if (mountainArr.get(mid) < mountainArr.get(mid + 1))
                l = mid + 1;     // peak is to the right
            else
                r = mid;         // peak at mid or left
        }
        int peak = l;

        // -------- search the ascending part first --------
        int idx = binarySearch(0, peak, true);
        if (idx != -1) return idx;

        // -------- if not found, search the descending part --------
        return binarySearch(peak + 1, n - 1, false);
    }
};
```

> **Notes**  
> * The lambda captures `mountainArr` by reference and works with `int` variables only.  
> * C++ uses `>> 1` (shift) to avoid division.  
> * The peak search uses `get(mid)` and `get(mid+1)` – just like the Java version.

---

## 5. Checklist Before Submitting

- [ ] Replace existing `class Solution` with the language‑specific snippet above.  
- [ ] No extra helper functions that scan the whole array.  
- [ ] Run the test cases LeetCode provides.  
- [ ] Verify that the answer is **the smallest** index (not just any occurrence).  
- [ ] Ensure your code compiles in the given environment (use `#include <bits/stdc++.h>` if needed in C++).  

---

## 6. Conclusion

> LeetCode’s **Find in Mountain Array** is an excellent exercise in applying binary search correctly to a non‑trivial data layout.  
> The key is to recognise the *direction change* (peak) and to reverse pointer movements for the descending part.  
> By carefully counting `get()` calls you stay comfortably under the 100‑call limit.

Good luck, and enjoy cracking the mountain! 🏔️

--- 

*If you find this guide helpful, please give the LeetCode problem a 👍 rating – it helps others learn the right pattern.*