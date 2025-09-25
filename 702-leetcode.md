---
title: LeetCode 702. Search in a Sorted Array of Unknown Size - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Mastering LeetCode 702: Search in a Sorted Array of Unknown Size  
### The Good, the Bad, and the Ugly – A Complete Interview‑Ready Guide

> **Keywords:** *LeetCode 702*, *search sorted array unknown size*, *ArrayReader*, *exponential search*, *binary search*, *interview question*, *Java solution*, *Python solution*, *C++ solution*, *algorithm interview*, *software engineer interview*, *job interview coding*  

---

### 1️⃣ Problem Recap

> **LeetCode 702 – Search in a Sorted Array of Unknown Size**  
> You are given a sorted array `secret` with unique, strictly increasing elements.  
> The array size is unknown and you cannot access it directly. Instead, you interact with the provided `ArrayReader` interface:

```text
int get(int index)
```

* If `index` is within bounds, it returns `secret[index]`.  
* If `index` is out of bounds, it returns `Integer.MAX_VALUE` (or `2^31-1`).

You must write `search(ArrayReader reader, int target)` that returns the index of `target` or `-1` if it doesn’t exist, using **O(log n)** time and **O(1)** extra space.

---

### 2️⃣ The Good: A Clean, Elegant Two‑Phase Solution

1. **Expand the Search Window (Exponential Search)**
   * Start with `[left = 0, right = 1]`.  
   * While `get(right)` is not out of bounds **and** less than the target, double `right`.  
   * This guarantees we eventually bracket the target between `left` and `right`.

2. **Binary Search Inside the Bracket**
   * Classic binary search with `mid = left + (right‑left)/2`.  
   * Compare `get(mid)` to the target; adjust `left`/`right` accordingly.  
   * Stop when the value matches or the window collapses.

Both phases together run in `O(log T)` where `T` is the smallest index that is either the target or the first out‑of‑bounds index – exactly the required logarithmic bound.

#### Why It’s Great

* **Simplicity:** Only two loops, no recursion.  
* **Deterministic:** No dependency on the size of the array – it scales to 10⁴ or larger.  
* **Memory‑efficient:** Constant additional space.  
* **Industry‑ready:** Works with the real LeetCode `ArrayReader` API (or any wrapper that behaves the same).

---

### 3️⃣ The Bad: Common Pitfalls to Avoid

| Pitfall | Why It Breaks | Fix |
|---------|---------------|-----|
| **Accessing out‑of‑bounds indices without checking** | `reader.get(i)` may return `2^31-1` – treat it as a sentinel, not as a real value. | Always test `value == Integer.MAX_VALUE` before comparing to the target. |
| **Using `while (get(right) < target)` without a bound check** | Could overflow `right` or call `get` on negative indices. | Stop when `get(right) == Integer.MAX_VALUE` or when doubling would overflow `Integer.MAX_VALUE`. |
| **Binary search `mid = (left + right) / 2`** | Can overflow for large indices. | Use `mid = left + (right - left) / 2`. |
| **Returning `right` after loop** | If target isn’t found, you might mistakenly return a valid index. | Return `-1` when the loop finishes without a match. |
| **Ignoring the “unique, strictly increasing” property** | Some solutions assume duplicates or unsorted data. | No action needed; the algorithm relies on monotonicity. |

---

### 4️⃣ The Ugly: Over‑Engineering & Hidden Tricks

* **Recursive Binary Search** – Adds function‑call overhead and risk of stack overflow for very large ranges.  
* **Pre‑fetching the array size** – Impossible under the problem constraints; attempting to do so defeats the purpose.  
* **Binary Search on `Integer.MAX_VALUE`** – Treating the sentinel as a regular value can lead to infinite loops if not handled carefully.  
* **Using `Math.min` for `mid`** – Unnecessary and slower than the safe formula.

> **Bottom line:** Keep it *simple* and *robust*. Any clever trick that complicates the code will only hurt your interview score.

---

### 5️⃣ Full Code – Java, Python, & C++

> **Note:** All implementations use the same two‑phase strategy. The code snippets below are ready to paste into your IDE or LeetCode submission box.

---

#### 5.1 Java (LeetCode Submission)

```java
/**
 * This is ArrayReader's API interface.
 * You should not implement it, or speculate about its implementation.
 */
interface ArrayReader {
    public int get(int index);
}

class Solution {
    public int search(ArrayReader reader, int target) {
        // 1️⃣ Exponential search to find a bound that definitely contains target
        int left = 0, right = 1;
        while (true) {
            int val = reader.get(right);
            if (val == Integer.MAX_VALUE || val >= target) break;
            left = right;
            right <<= 1;                     // double the window
            if (right < 0) {                 // overflow guard
                right = Integer.MAX_VALUE;
                break;
            }
        }

        // 2️⃣ Classic binary search within [left, right]
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int midVal = reader.get(mid);

            if (midVal == target) return mid;
            if (midVal == Integer.MAX_VALUE || midVal > target) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return -1; // not found
    }
}
```

---

#### 5.2 Python

```python
# The ArrayReader interface is provided by LeetCode
# class ArrayReader:
#     def get(self, index: int) -> int: ...

class Solution:
    def search(self, reader: 'ArrayReader', target: int) -> int:
        left, right = 0, 1

        # Exponential expansion of the right bound
        while True:
            val = reader.get(right)
            if val == 2**31 - 1 or val >= target:
                break
            left = right
            right <<= 1   # double the window
            if right >= 2**31 - 1:  # avoid overflow
                right = 2**31 - 1
                break

        # Binary search in the bounded range
        while left <= right:
            mid = left + (right - left) // 2
            mid_val = reader.get(mid)

            if mid_val == target:
                return mid
            if mid_val == 2**31 - 1 or mid_val > target:
                right = mid - 1
            else:
                left = mid + 1

        return -1
```

---

#### 5.3 C++

```cpp
// Assume ArrayReader is defined as follows:
// class ArrayReader {
// public:
//     int get(int index);
// };

class Solution {
public:
    int search(ArrayReader &reader, int target) {
        int left = 0, right = 1;

        // Exponential search to find an upper bound
        while (true) {
            int val = reader.get(right);
            if (val == INT_MAX || val >= target) break;
            left = right;
            right <<= 1;                 // double the window
            if (right < 0) {             // overflow guard
                right = INT_MAX;
                break;
            }
        }

        // Binary search within [left, right]
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int midVal = reader.get(mid);

            if (midVal == target) return mid;
            if (midVal == INT_MAX || midVal > target) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }

        return -1; // target not found
    }
};
```

---

### 6️⃣ Complexity Analysis

| Step | Time Complexity | Space Complexity |
|------|-----------------|-------------------|
| Exponential search | **O(log k)** (k = index of target or first out‑of‑bounds) | **O(1)** |
| Binary search | **O(log k)** | **O(1)** |
| **Total** | **O(log k)** | **O(1)** |

*The algorithm meets the required `O(log n)` runtime and constant extra space.*

---

### 7️⃣ Interview‑Ready Tips

| Tip | Explanation |
|-----|-------------|
| **Explain the two phases upfront** | Shows you understand why you can’t just binary‑search from the start. |
| **Mention the sentinel (`Integer.MAX_VALUE`)** | Clarifies handling of out‑of‑bounds. |
| **Discuss edge cases** | Empty array (not allowed by constraints), target less than first element, target greater than last element. |
| **Talk about overflow** | Why `mid = left + (right - left)/2` is safer than `(left+right)/2`. |
| **Show the code in your favorite language** | If the interviewers prefer Java, Python, or C++, be ready to write it live. |

---

### 8️⃣ How This Solves Your Job Hunt

* **LeetCode Mastery:** Problem 702 is a classic “interview favorite” that showcases your understanding of binary search, exponential search, and sentinel handling.  
* **Algorithmic Confidence:** The two‑phase approach demonstrates a systematic way to handle unknown sizes – a pattern that appears in many real‑world systems.  
* **Multi‑Language Proficiency:** Providing Java, Python, and C++ solutions proves you’re versatile and ready for diverse codebases.  
* **SEO‑Optimized Blog:** By publishing a detailed article, you increase your visibility on search engines – recruiters often search for “LeetCode 702 solutions” or “search unknown size array”.  
* **Portfolio Boost:** Add the code and the blog post to your GitHub and LinkedIn. Tag the repository with `leetcode`, `array-search`, `binary-search`, `algorithm-interview`.

---

### 9️⃣ Final Takeaway

Problem 702 is solvable with a **short, robust, and provably logarithmic solution**. Avoid over‑engineering, keep overflow in mind, and always treat `Integer.MAX_VALUE` as a sentinel. With the code above and the interview strategy outlined, you’re fully equipped to ace this question—and to get noticed by hiring managers.

Happy coding—and may your interviews be as clean and efficient as this solution! 🚀

---