---
title: LeetCode 1095. Find in Mountain Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 “Find in Mountain Array” – A Complete, Interview‑Ready Solution  
*(Java | Python | C++) – with a full‑blown blog article that you can copy‑paste into your portfolio or LinkedIn post.*

---

## 1️⃣ Problem Recap (LeetCode 1095)

You are given a *mountain array* – an array that strictly increases up to a single peak and then strictly decreases.  
Only two methods are available:

```text
int get(int index)   // O(1) – but you can only call it ≤ 100 times
int length()        // O(1)
```

Your task: **return the minimum index i such that get(i) == target**; return -1 if the target is absent.

*Why this is a great interview question?*  
* It tests binary search on a partially sorted array.  
* It forces you to think about the *peak* and the *call budget* (≤ 100 `get()` calls).  
* It’s a perfect “hard” problem that shows you can squeeze 3 binary searches into 100 calls.

---

## 2️⃣ The “Good, the Bad, and the Ugly”

|  | **Good** | **Bad** | **Ugly** |
|---|---|---|---|
| **Data structure** | *Mountain array* – guarantees one peak, strictly monotonic on both sides. | *Flat peaks* (e.g., [1,2,3,3,2,1]) – not allowed, but many candidates forget the strictness. | *Unbounded get() calls* – can’t simply “scan” linearly. |
| **Algorithmic strategy** | 1️⃣ Find the peak with a classic “mountain‑search”. <br>2️⃣ Binary‑search the ascending half. <br>3️⃣ If still not found, binary‑search the descending half. | 1️⃣ Using a single `get()` inside the loop instead of `get(mid)` + `get(mid+1)` saves a call, but loses clarity. | 1️⃣ A *recursive* approach that forgets to use `nonlocal`/`const`/`const_cast` can blow up the stack in Python or waste calls in Java/C++. |

---

## 3️⃣ The Straight‑Forward Plan

1. **Peak‑finding** – binary search on the property `arr[mid] < arr[mid+1]`.  
   *Each iteration uses **2** `get()` calls → ≤ 14 × 2 = 28 calls for a 10 000‑element array.*  
2. **Binary‑search left side** – ordinary ascending binary search.  
3. **Binary‑search right side** – descending binary search (mirror of the ascending one).  
4. **Return the first index found** (because we search the left side first, the index is automatically minimal).  

All three searches together use at most *14 + 14 + 14 = 42* `get()` calls – comfortably below the 100‑call limit.

---

## 3️⃣ Code (All Languages)

Below you’ll find **three self‑contained solutions** that follow the exact algorithm described above.  
All of them respect the 100‑`get()` budget and run in `O(log n)` time with `O(1)` auxiliary space.

> ⚠️ **Important** – If you copy the Java solution into LeetCode, you do **not** need to re‑implement the `MountainArray` interface – LeetCode already supplies it.  
> For the sake of completeness (and a good “copy‑paste” demo for your portfolio), we’ll also include a minimal mock of `MountainArray`.

---

### 📄 Java (JDK 11+)

```java
// This is the interface that LeetCode supplies.  For a local test you can copy it.
interface MountainArray {
    public int get(int index);
    public int length();
}

class Solution {
    // Main entry point LeetCode expects.
    public int findInMountainArray(int target, MountainArray mountainArr) {
        int n = mountainArr.length();

        // 1️⃣  Find peak index
        int peak = findPeak(mountainArr, n);

        // 2️⃣  Search the ascending part
        int idx = binarySearch(mountainArr, target, 0, peak, true);
        if (idx != -1) return idx;

        // 3️⃣  Search the descending part
        return binarySearch(mountainArr, target, peak + 1, n - 1, false);
    }

    // Binary search helper.  `ascending` = true → left < right;  false → left > right.
    private int binarySearch(MountainArray arr, int target,
                             int left, int right, boolean ascending) {
        while (left <= right) {
            int mid = (left + right) >>> 1;          // faster than /2
            int midVal = arr.get(mid);

            if (midVal == target) return mid;

            if (ascending) {
                if (target > midVal) left = mid + 1;
                else                right = mid - 1;
            } else { // descending
                if (target > midVal) right = mid - 1;
                else                left = mid + 1;
            }
        }
        return -1;
    }

    // Classic peak‑finding binary search.
    private int findPeak(MountainArray arr, int n) {
        int lo = 0, hi = n - 1;
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            // `mid < mid+1` ⇒ ascending → move right
            if (arr.get(mid) < arr.get(mid + 1)) lo = mid + 1;
            else                                hi = mid;
        }
        return lo; // peak index
    }
}
```

---

### 📦 Python 3 (Python 3.9+)

```python
# LeetCode’s MountainArray type hint
from typing import Any

class MountainArray:
    def get(self, index: int) -> int: ...
    def length(self) -> int: ...

class Solution:
    def findInMountainArray(self, target: int, mountain_arr: 'MountainArray') -> int:
        n = mountain_arr.length()

        def search(left: int, right: int, asc: bool) -> int:
            """Binary search for `target` in [left, right] (inclusive)."""
            while left <= right:
                mid = (left + right) // 2
                val = mountain_arr.get(mid)
                if val == target:
                    return mid
                if asc:
                    if target > val:
                        left = mid + 1
                    else:
                        right = mid - 1
                else:  # descending part
                    if target > val:
                        right = mid - 1
                    else:
                        left = mid + 1
            return -1

        def find_peak() -> int:
            lo, hi = 0, n - 1
            while lo < hi:
                mid = (lo + hi) // 2
                if mountain_arr.get(mid) < mountain_arr.get(mid + 1):
                    lo = mid + 1
                else:
                    hi = mid
            return lo

        peak = find_peak()
        idx = search(0, peak, True)
        if idx != -1:
            return idx
        return search(peak + 1, n - 1, False)
```

---

### 📘 C++ (GNU‑C++17)

```cpp
// LeetCode’s MountainArray interface
class MountainArray {
public:
    virtual int get(int index) const = 0;
    virtual int length() const = 0;
};

class Solution {
public:
    int findInMountainArray(int target, MountainArray &mountainArr) {
        int n = mountainArr.length();

        auto binarySearch = [&](int left, int right, bool ascending) {
            while (left <= right) {
                int mid = left + ((right - left) >> 1);
                int val = mountainArr.get(mid);
                if (val == target) return mid;
                if (ascending) {
                    if (target > val) left = mid + 1;
                    else             right = mid - 1;
                } else { // descending
                    if (target > val) right = mid - 1;
                    else             left = mid + 1;
                }
            }
            return -1;
        };

        // Find peak
        int lo = 0, hi = n - 1;
        while (lo < hi) {
            int mid = lo + ((hi - lo) >> 1);
            if (mountainArr.get(mid) < mountainArr.get(mid + 1))
                lo = mid + 1;
            else
                hi = mid;
        }
        int peak = lo;

        // Search ascending part first
        int idx = binarySearch(0, peak, true);
        if (idx != -1) return idx;

        // Then search descending part
        return binarySearch(peak + 1, n - 1, false);
    }
};
```

> All three implementations call `get()` at most  
> `log₂(n) * 2` (peak search) + `log₂(n)` (asc) + `log₂(n)` (desc) ≤ **42** < **100**.

---

## 3️⃣ Step‑by‑Step Walk‑Through (The Blog Version)

> **SEO‑ready keywords**: *mountain array*, *binary search*, *LeetCode 1095*, *coding interview*, *job interview*, *data structure*, *algorithm*, *time complexity*, *space complexity*, *Java*, *Python*, *C++*, *software engineer*, *coding challenge*.

---

### 📝 3.1  Introduction

> **“I can’t finish this LeetCode problem, my calls keep exploding.”**  
> If you’ve read this, you’re probably prepping for a *software‑engineering interview* and need to show that you can *think on your feet*, *manage resources*, and *apply binary search in non‑obvious ways*.  

We’ll walk through **“Find in Mountain Array”** – a hard LeetCode problem that is **exactly the kind of puzzle recruiters love**.  

---

### 🗂️ 3.2  Problem Statement (Simplified)

> You’re given an array that **strictly increases to one peak** and then **strictly decreases**.  
> Only two API calls are permitted:  
> * `int get(int index)` – returns the element at `index`.  
> * `int length()` – returns the array size.  
> **Call budget**: at most **100** `get()` calls.  
> **Goal**: Return the *smallest* index where the element equals `target`, or -1 if it’s absent.

---

### 🔍 3.3  The Intuition

1. **There is a single peak**.  
   → A *peak‑finding binary search* will locate it in `O(log n)` calls.  
2. **Both halves are monotonic**.  
   → Classic binary search works on each side.  
3. **Ascending search first guarantees minimal index**.  
   → If `target` appears on both sides, the left one will be found first.  

---

### ⚙️ 3.4  Detailed Algorithm

#### 3.4.1  Peak‑Finding

| Step | Action | Calls |
|------|--------|-------|
| `mid` = `(lo+hi)/2` | Compare `arr[mid]` with `arr[mid+1]` |
| `arr[mid] < arr[mid+1]` → peak is to the right | `lo = mid+1` |
| else → peak is to the left (or at `mid`) | `hi = mid` |

*Each loop needs two `get()` calls*: one at `mid`, one at `mid+1`.

#### 3.4.2  Binary Search on Ascending Half

> Standard ascending binary search.

#### 3.4.3  Binary Search on Descending Half

> Mirror of the ascending search.  
> If `arr[mid] < target` → move left; else move right.

---

### 📊 3.5  Complexity Analysis

* **Time**: `3 * log₂(n)` → `O(log n)`  
* **Space**: Constant auxiliary variables → `O(1)`  

For `n ≤ 10,000`, the number of `get()` calls is at most 42 (much less than 100).  

---

### 🛠️ 3.6  Implementations

> Each language has a tiny `binarySearch()` helper that accepts a boolean flag `ascending`.  
> The *peak‑finding* routine is a small loop that keeps narrowing the search space.  

> (Insert the code blocks from section 3 above here, each with explanatory comments.)

---

### 🎯 3.7  Edge Cases & Common Pitfalls

| Case | Why It’s a Pitfall | Fix |
|------|--------------------|-----|
| `n == 1` | No `mid+1` exists. | Handle separately or trust LeetCode’s guarantee that `n ≥ 3`. |
| `target` equals the peak | If you search left first, you’ll still return the peak, which is correct. | No special handling needed. |
| **Recursive vs. iterative** | Python recursion may hit stack depth limits if you forget to `nonlocal`. | Use `while` loops or `nonlocal` for the local variable (`peak`). |
| **Off‑by‑one** in descending binary search | Confusing the `left`/`right` update directions can cause infinite loops. | Verify with small test arrays. |

---

### 🎓 3.8  Takeaway for Your Interview

> 1. **Show resource awareness**: The 100‑call limit is the *resource constraint* your interviewer implicitly gives you.  
> 2. **Demonstrate modularity**: Keep your helpers pure and well‑named (`binarySearch`, `findPeak`).  
> 3. **Use language‑specific idioms**: Java’s bit‑shift (`>>> 1`), Python’s closures, C++’s lambda captures.  
> 4. **Explain your reasoning aloud**: Recruiters will listen to *how* you approach a problem as much as *whether* you solve it.

---

## 4️⃣ Final Thoughts

* **The core idea**: *Use binary search twice, once for peak, once for each half.*  
* **The call budget**: 42 `get()` calls – well under the limit.  
* **All solutions**: `O(log n)` time, `O(1)` space.  

> **Pro tip** – For any interview, it’s best to start with a **plain iterative approach**.  
> Once you have that working, you can sprinkle in *language‑specific optimizations* (bit‑shifts, `nonlocal`, `const`) to impress the hiring manager.

Happy coding, and may your next interview call‑count stay below the 100‑call budget! 🚀

--- 

> **Got a challenge you can’t solve?**  
> Post it on Stack Overflow or discuss it in your *study group*.  Collaboration is the best way to master these puzzles.  

--- 

> **Enjoyed this post?**  
> 👉 **Subscribe** for more interview‑prep walkthroughs.  
> 👉 **Share** it with friends who’re prepping for software‑engineering roles.  

--- 

**Good luck!** 🌟  