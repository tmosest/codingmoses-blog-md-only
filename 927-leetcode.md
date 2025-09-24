---
title: LeetCode 927. Three Equal Parts - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 3️⃣ 927 – Three Equal Parts  
## LeetCode – Hard | Java / Python / C++ | Interview‑ready

---

### TL;DR  
Given a binary array, split it into **three non‑empty contiguous parts** whose binary values are identical.  
Return the indices `[i , j]` such that

* `arr[0 … i]` – first part  
* `arr[i+1 … j-1]` – second part  
* `arr[j … n-1]` – third part  

If no such split exists return `[-1 , -1]`.  
Leading zeros are **allowed**.

> **All solutions below are O(n) time, O(1) auxiliary space** and are ready to paste into your LeetCode/Interview portal.

---

## 1.  Problem Breakdown

| Key Insight | Reasoning |
|-------------|-----------|
| The **total number of 1’s** must be divisible by 3 | Each part must contain the same number of 1’s |
| If the array contains **only 0’s** we can split anywhere | All three parts represent the value 0 |
| Find the **first index** of each part by skipping the first `k` 1’s | `k = total_ones / 3` |
| The **suffix** of the array defines the binary value we need to match | Once we know the start of each part, the parts must look identical until the end |

---

## 2.  The O(n) Solution

### Steps

1. **Count 1’s**  
   * If `total_ones % 3 != 0` → impossible → `[-1,-1]`.
2. **All‑zero case**  
   * If `total_ones == 0` → return `[0, 2]`.
3. **Find start positions**  
   * Scan from left to right, keeping a counter of 1’s seen.  
   * When counter hits `k`, `2k`, and `3k`, store indices `start1`, `start2`, `start3`.
4. **Align the three parts**  
   * While all three indices are within bounds **and** `arr[start1] == arr[start2] == arr[start3]`, advance all three simultaneously.  
   * If a mismatch occurs → impossible.
5. **Verify that the parts are non‑overlapping**  
   * `start1 + len - 1 < start2` and `start2 + len - 1 < start3`  
   * `len = n - start3` – length of the suffix.
6. **Return answer**  
   * `i = start1 + len - 1`  
   * `j = start2 + len`

### Why It Works

* Every part must have the same binary value → the sequence of 1’s and trailing 0’s must be identical for each part.
* By aligning the **suffix** (`start3` onward) with the two earlier parts we guarantee that the value is the same.
* Because we skip over the exact number of 1’s, the parts are guaranteed to be non‑empty and non‑overlapping.

---

## 3.  Code Implementations

### Java

```java
/**
 * 3.927 Three Equal Parts – Java
 * Time: O(n)  |  Space: O(1)
 */
public class Solution {
    public int[] threeEqualParts(int[] arr) {
        int n = arr.length;
        int totalOnes = 0;
        for (int v : arr) if (v == 1) totalOnes++;

        // 1. total ones must be divisible by 3
        if (totalOnes % 3 != 0) return new int[]{-1, -1};

        // 2. all zeros case
        if (totalOnes == 0) return new int[]{0, 2};

        int onesPerPart = totalOnes / 3;
        int first = -1, second = -1, third = -1;
        int seen = 0;
        for (int i = 0; i < n; i++) {
            if (arr[i] == 1) {
                seen++;
                if (seen == 1) first = i;
                else if (seen == onesPerPart + 1) second = i;
                else if (seen == 2 * onesPerPart + 1) third = i;
            }
        }

        // 3. align suffixes
        int i = first, j = second, k = third;
        while (k < n) {
            if (arr[i] != arr[j] || arr[j] != arr[k]) return new int[]{-1, -1};
            i++; j++; k++;
        }

        int len = n - third;           // length of the pattern
        int endFirst = first + len - 1;
        int startThird = second + len;

        // 4. ensure non‑overlap (usually true if above steps passed)
        if (endFirst + 1 < startThird) return new int[]{endFirst, startThird};
        return new int[]{-1, -1};
    }
}
```

---

### Python

```python
"""
3.927 Three Equal Parts – Python
Time: O(n) | Space: O(1)
"""

class Solution:
    def threeEqualParts(self, arr: list[int]) -> list[int]:
        n = len(arr)
        total = sum(arr)

        # 1. total 1's must be divisible by 3
        if total % 3:
            return [-1, -1]

        # 2. all zeros
        if total == 0:
            return [0, 2]

        k = total // 3          # 1's per part
        first = second = third = -1
        seen = 0
        for i, v in enumerate(arr):
            if v:
                seen += 1
                if seen == 1:
                    first = i
                elif seen == k + 1:
                    second = i
                elif seen == 2 * k + 1:
                    third = i

        # 3. compare the three parts
        while third < n:
            if arr[first] != arr[second] or arr[second] != arr[third]:
                return [-1, -1]
            first += 1
            second += 1
            third += 1

        part_len = n - third   # length of the suffix
        i = first - part_len   # end of first part
        j = second - part_len  # start of third part

        return [i, j]
```

---

### C++

```cpp
/**
 * 3.927 Three Equal Parts – C++
 * Time: O(n) | Space: O(1)
 */
class Solution {
public:
    vector<int> threeEqualParts(vector<int>& arr) {
        int n = arr.size();
        int total = accumulate(arr.begin(), arr.end(), 0);
        if (total % 3 != 0) return {-1, -1};
        if (total == 0) return {0, 2};

        int k = total / 3;
        int first = -1, second = -1, third = -1;
        int seen = 0;
        for (int i = 0; i < n; ++i) {
            if (arr[i] == 1) {
                ++seen;
                if (seen == 1) first = i;
                else if (seen == k + 1) second = i;
                else if (seen == 2 * k + 1) third = i;
            }
        }

        // Align the three parts
        int i = first, j = second, kIdx = third;
        while (kIdx < n) {
            if (arr[i] != arr[j] || arr[j] != arr[kIdx]) return {-1, -1};
            ++i; ++j; ++kIdx;
        }

        int partLen = n - third;
        int endFirst = first + partLen - 1;
        int startThird = second + partLen;

        if (endFirst + 1 < startThird) return {endFirst, startThird};
        return {-1, -1};
    }
};
```

---

## 4.  Blog Article – “The Good, The Bad, and the Ugly of LeetCode 927”

> **Title**  
> *Three Equal Parts – The Good, The Bad, and the Ugly (Java, Python, C++)*  

> **Meta‑description**  
> Master LeetCode 927 in minutes. Read the full solution, understand pitfalls, and get a job‑ready interview answer. Includes Java, Python, C++ code.  

> **Keywords**  
> `Three Equal Parts Leetcode`, `Java solution`, `Python solution`, `C++ solution`, `binary array split`, `job interview coding`, `binary number comparison`, `O(n) algorithm`, `array partitioning`

---

### 1️⃣ The Good: Why this Problem Rocks

* **Real‑world vibe** – Think of data packets that must be split into equal segments.  
* **Pure algorithmic challenge** – No heavy math, just clever pointer manipulation.  
* **Perfect interview test** – Shows your understanding of *array traversal*, *edge cases*, and *time‑space trade‑offs*.

### 2️⃣ The Bad: Common Pitfalls

| Mistake | Why it breaks |
|---------|----------------|
| *Ignoring leading zeros* | The binary value of `[0,1,1]` is the same as `[1,1]`. |
| *Assuming any split works when all zeros* | You must still return `[0,2]` – the simplest valid answer. |
| *Using a hash map of prefixes* | O(n) space for a problem that can be done in O(1). |
| *Checking only the number of 1’s* | Two arrays can have the same 1‑count but different pattern. |

### 3️⃣ The Ugly: Hidden Edge Cases

1. **All zeros** – Your algorithm must still return a valid split.  
2. **Trailing zeros after the last part** – They must be counted in the suffix alignment.  
3. **Very short arrays** – E.g., `[1,0,1]` → impossible.  
4. **Large inputs** – `arr.length == 30000` – ensure you do *not* create extra arrays or use recursion.

---

## 5.  SEO‑Optimized Quick‑Start Cheat Sheet

| Language | Quick‑Start Code |
|----------|------------------|
| **Java** | ```java\npublic int[] threeEqualParts(int[] arr) { /* code */ }\n``` |
| **Python** | ```python\nclass Solution:\n    def threeEqualParts(self, arr: List[int]) -> List[int]:\n        # code\n``` |
| **C++** | ```cpp\nclass Solution {\npublic:\n    vector<int> threeEqualParts(vector<int>& arr) {\n        // code\n    }\n};\n``` |

**Tip:** When you paste into LeetCode, just replace the skeleton with the code above and run.  

---

## 6.  Closing Thoughts

* **Practice** – Run the code with random arrays to see edge cases in action.  
* **Explain** – In interviews, walk through *total ones*, *start indices*, *suffix alignment*, *non‑overlap*.  
* **Portfolio** – Add this solution to your GitHub repo, tag it with `#LeetCode927`. Recruiters love seeing clean, well‑commented code.  

> *Ready to land that next coding interview?* 🚀  
> Drop the solution into your IDE, test it, and explain the logic— you’re all set!

---

*Happy coding and good luck!*  

--- 

> **Author** – Coding Interview Ninja  
> **Follow** for more LeetCode insights: LinkedIn, Twitter, Medium  
> **Subscribe** to our newsletter: 30‑day interview prep plan.

---

### End of Article

--- 

With this article, readers not only learn the algorithm but also understand *why* it’s interview‑worthy, which boosts confidence and interview performance—key to landing that dream software engineering role.