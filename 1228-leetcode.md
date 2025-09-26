---
title: LeetCode 1228. Missing Number In Arithmetic Progression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Missing Number in Arithmetic Progression – LeetCode 1228  
**Java, Python & C++ solutions + interview‑ready guide**

---

## 1. Introduction

> **LeetCode 1228 – “Missing Number in Arithmetic Progression”**  
> *A single number was removed from a strictly increasing or decreasing arithmetic progression (AP). Find the missing element.*

The problem is a classic interview question that tests your understanding of arithmetic sequences, edge‑case handling and constant‑time logic. Mastering it gives you a clean solution to show on your résumé, a solid answer for technical interviews, and confidence when tackling harder “gap‑filling” problems.

---

## 2. Problem Statement

```text
Given an integer array arr that contains all elements of a strictly monotonic arithmetic progression of length n+1
except one element that was removed (not the first or last element), find the missing number.

Return the missing number.
```

| Field | Description |
|-------|-------------|
| `arr` | array of size `n` (n ≥ 3) |
| `n`   | length of the input array |
| `n+1` | original size of the AP before removal |

> **Examples**  
> • `arr = [5, 7, 11, 13]` → output `9`  
> • `arr = [15, 13, 12]` → output `14`

---

## 3. Constraints

| Parameter | Minimum | Maximum | Notes |
|-----------|---------|---------|-------|
| `n`       | 3 | 10⁵ | The input size can be large; O(n) is required. |
| `arr[i]`  | -10⁹ | 10⁹ | 32‑bit signed integers. |
| The progression is **strictly monotonic** (increasing or decreasing). |
| The missing element is **never the first or the last** of the original sequence. |

---

## 4. Understanding the Problem

An arithmetic progression (AP) has a constant difference `d`.  
If we delete one element from a progression of length `k+1`, the resulting array has `k` elements, and **exactly one adjacent pair will have a gap of `2·d`** – the place where the deletion happened.

*Why only one such pair?*  
Because every other adjacent pair in the remaining array is still consecutive in the original AP and therefore differs by exactly `d`.

The task reduces to:
1. **Determine the true difference `d`.**  
   In an array of ≥ 4 elements, we can look at the first three differences and choose the one that appears at least twice.
2. **Locate the anomalous pair** where the difference is `2·d` and compute the missing number as `previous + d`.

Edge cases:  
- **`n = 3`** – the “majority” rule cannot be applied because we have only two differences. The smaller magnitude difference is the real `d`.  
- **Negative `d`** – the AP may be decreasing; the algorithm handles signs naturally.

---

## 5. Approach (O(n) time, O(1) space)

1. **Compute the first three differences** (`diff1`, `diff2`, `diff3`).
2. **Identify the correct difference `d`**:
   *If `diff1 == diff2` or `diff1 == diff3` → `d = diff1`; otherwise `d = diff2`.*
3. **Scan once** from the second element to the second‑to‑last:
   *When `arr[i] - arr[i-1] != d`, the missing number is `arr[i-1] + d`.  
   The loop terminates immediately – no need to check the last element because the missing element cannot be at the array’s ends.*
4. **Special‑case `n = 3`**:  
   *The two available differences give `d` as the one with smaller absolute value.  
   If the first difference equals `2·d`, the missing number lies after the first element; otherwise it lies after the second.*

The algorithm never modifies the input array and runs in a single linear pass.

---

## 6. Implementations

Below are clean, interview‑ready implementations in **Java, Python, and C++**.

---

### 6.1 Java

```java
import java.util.*;

class Solution {
    public int missingNumber(int[] arr) {
        int n = arr.length;
        if (n == 3) {
            int diff1 = arr[1] - arr[0];
            int diff2 = arr[2] - arr[1];
            int d = Math.abs(diff1) < Math.abs(diff2) ? diff1 : diff2;
            if (diff1 == 2 * d) {
                return arr[0] + d;          // missing after arr[0]
            } else {
                return arr[1] + d;          // missing after arr[1]
            }
        }

        int diff1 = arr[1] - arr[0];
        int diff2 = arr[2] - arr[1];
        int diff3 = arr[3] - arr[2];

        int d = (diff1 == diff2 || diff1 == diff3) ? diff1 : diff2;

        for (int i = 1; i < n - 1; i++) {
            if (arr[i] - arr[i - 1] != d) {
                return arr[i - 1] + d;
            }
        }
        return -1; // should never reach here
    }
}
```

---

### 6.2 Python

```python
from typing import List

class Solution:
    def missingNumber(self, arr: List[int]) -> int:
        n = len(arr)
        if n == 3:
            diff1 = arr[1] - arr[0]
            diff2 = arr[2] - arr[1]
            d = diff1 if abs(diff1) < abs(diff2) else diff2
            return arr[0] + d if diff1 == 2 * d else arr[1] + d

        diff1 = arr[1] - arr[0]
        diff2 = arr[2] - arr[1]
        diff3 = arr[3] - arr[2]
        d = diff1 if diff1 == diff2 or diff1 == diff3 else diff2

        for i in range(1, n - 1):
            if arr[i] - arr[i - 1] != d:
                return arr[i - 1] + d
        return -1   # never reached
```

---

### 6.3 C++

```cpp
#include <vector>
#include <cmath>

class Solution {
public:
    int missingNumber(std::vector<int>& arr) {
        int n = arr.size();
        if (n == 3) {
            int diff1 = arr[1] - arr[0];
            int diff2 = arr[2] - arr[1];
            int d = std::abs(diff1) < std::abs(diff2) ? diff1 : diff2;
            if (diff1 == 2 * d) return arr[0] + d;
            else                 return arr[1] + d;
        }

        int diff1 = arr[1] - arr[0];
        int diff2 = arr[2] - arr[1];
        int diff3 = arr[3] - arr[2];
        int d = (diff1 == diff2 || diff1 == diff3) ? diff1 : diff2;

        for (int i = 1; i < n - 1; ++i) {
            if (arr[i] - arr[i - 1] != d) return arr[i - 1] + d;
        }
        return -1;   // unreachable
    }
};
```

---

## 7. Alternative (Brute‑Force) – why not?

A naive solution tries every possible number between `arr[0]` and `arr[n-1]` and checks if it fits an AP.  
It is **O(n²)** and will TLE on large inputs. It’s useful for practice, but **never** for production or an interview.

---

## 8. Test‑Driven Edge Cases

| Input | Expected | Why it works |
|-------|----------|--------------|
| `[1, 4, 7, 10]` | `13` | d = 3; gap after last element, but the rule guarantees we’ll catch it before the end. |
| `[10, 8, 6, 4]` | `2` | Decreasing AP; d = ‑2, anomalous pair is `10–4 = 6` (2·d). |
| `[5, 15, 25]` | `10` | Increasing AP with d = 10; the middle pair is 10. |
| `[2, 1, 0]` | `-1` | Decreasing AP with d = ‑1; missing after first element. |
| `[3, 3]` | *invalid* | The array length must be ≥ 3 (problem guarantees this). |

---

## 9. Why This Solution Rocks for Interviews

1. **Zero‑memory** – Constant extra space.  
2. **Single scan** – Linear time, which matches the required complexity.  
3. **Handles both increasing & decreasing APs** – The sign of `d` is preserved.  
4. **Clear logic** – You can explain “majority difference” and “anomalous gap” in a few sentences.  
5. **Extensible** – The same pattern solves “find the second missing element” or “find the missing index in a binary search tree” with slight tweaks.

---

## 10. Variations & Extensions

| Variation | How to adapt |
|-----------|--------------|
| **Find two missing numbers** | Two gaps will be `2·d` and `3·d`. Detect both and compute the two missing elements. |
| **Non‑monotonic progression** | Compute the average difference `d = (arr[-1] - arr[0]) / n` (integer division). Then proceed with the scan. |
| **Large integers (64‑bit)** | Use `long` in Java/C++ or `int` in Python (Python’s int is unbounded). |

---

## 11. Final Thoughts

*Missing Number in Arithmetic Progression* is a deceptively simple problem that hides a subtle pattern.  
Mastering this pattern:
- Builds a **solid foundation** for solving “gap” problems (e.g., missing subsequence, missing element in a sorted array).  
- Demonstrates **algorithmic clarity** to hiring managers.  
- Gives you a reusable snippet for any interview that involves arithmetic sequences or linear‑time scans.

Happy coding and good luck on your next interview! 🚀