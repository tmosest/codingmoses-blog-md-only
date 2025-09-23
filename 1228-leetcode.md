---
title: LeetCode 1228. Missing Number In Arithmetic Progression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 1228 – “Missing Number In Arithmetic Progression”  
**(Easy)** | ⭐️ 1 ⭐️ ⭐️ ⭐️ ⭐️ | 🎯 Java | 🐍 Python | 🧩 C++  

---

### 1️⃣ Problem Recap  

You’re given an array `arr` that originally contained an arithmetic progression (AP) – every consecutive difference is the same. One element was removed *neither* at the beginning *nor* at the end.  
Return the missing value.

> **Constraints**  
> * `3 ≤ arr.length ≤ 1000`  
> * `0 ≤ arr[i] ≤ 10⁵`  
> * Guaranteed to be valid

**Example**  
```text
Input:  arr = [5, 7, 11, 13]
Output: 9        // original AP: 5,7,9,11,13
```

---

### 2️⃣ High‑Level Strategy

1. **Compute the true common difference** `d` of the original AP.  
   * The original length is `len(arr) + 1`.  
   * `d = (last - first) / originalLength`.

2. **Scan the array** and look for the first adjacent pair whose difference deviates from `d`.  
   * The missing number is `arr[i] + d`.

3. **Return** the missing value.

> **Why this works** –  
> The removed number causes exactly one “gap” that is larger (or smaller) than `d`.  
> All other gaps equal `d`.  
> Because the missing element is never at the ends, the first mismatch we hit during a linear scan is the exact place where the number disappeared.

---

### 3️⃣ Complexity Analysis  

| Approach | Time | Space |
|----------|------|-------|
| O(n) linear scan | **O(n)** (n ≤ 1000) | **O(1)** |
| Binary‑search variant | **O(log n)** | **O(1)** |
| One‑liner with `math` (Python) | **O(n)** | **O(1)** |

For this LeetCode problem, the linear scan is more than fast enough and is the clearest to read.

---

### 4️⃣ Edge‑Case Checklist  

| Case | Why it matters | How we handle it |
|------|----------------|------------------|
| **Negative differences** (decreasing AP) | Example: `[15,13,12]` | `d` will be negative, still works. |
| **Duplicate gaps** (e.g., missing element makes two gaps appear) | Not possible – only one element missing. |
| **Smallest possible array** (`len=3`) | Only one missing number, straightforward. |

---

### 5️⃣ Alternative Approaches (Good, Bad, Ugly)

| Approach | Good | Bad | Ugly |
|----------|------|-----|------|
| **Linear scan** (ours) | Simple, intuitive, constant space | None | None |
| **Binary search** | Faster on very large arrays | Adds complexity | Still constant space |
| **One‑liner** | Very concise (Python) | Hard to read for interviewers | Poor maintainability |

---

### 6️⃣ Reference Implementations  

Below you’ll find clean, production‑ready code for **Java**, **Python**, and **C++**. All three are ready for copy‑and‑paste into your IDE or LeetCode editor.

---

#### 6.1 Java (LeetCode format)

```java
class Solution {
    public int missingNumber(int[] arr) {
        int n = arr.length;
        // original length = n + 1
        int d = (arr[n - 1] - arr[0]) / (n);   // integer division is safe
        
        for (int i = 0; i < n - 1; i++) {
            if (arr[i + 1] - arr[i] != d) {
                return arr[i] + d;
            }
        }
        // Should never reach here because input is guaranteed valid
        throw new IllegalArgumentException("Invalid input");
    }
}
```

---

#### 6.2 Python (LeetCode format)

```python
class Solution:
    def missingNumber(self, arr: List[int]) -> int:
        n = len(arr)
        d = (arr[-1] - arr[0]) // n   # integer division
        for i in range(n - 1):
            if arr[i + 1] - arr[i] != d:
                return arr[i] + d
        raise ValueError("Invalid input")
```

> **One‑liner (Python) – useful for quick hacks**  
> ```python
> missingNumber = lambda arr: next(arr[i] + d for i, d in enumerate([(arr[i+1]-arr[i]) for i in range(len(arr)-1)]) if d != (arr[-1]-arr[0])//len(arr))
> ```

---

#### 6.3 C++ (LeetCode format)

```cpp
class Solution {
public:
    int missingNumber(vector<int>& arr) {
        int n = arr.size();
        int d = (arr.back() - arr.front()) / n;   // integer division
        for (int i = 0; i < n - 1; ++i) {
            if (arr[i + 1] - arr[i] != d) {
                return arr[i] + d;
            }
        }
        throw invalid_argument("Invalid input");
    }
};
```

---

### 7️⃣ How to Use These Solutions in an Interview  

1. **Explain your plan first** – mention the AP property, how you compute `d`, and why a single mismatch reveals the missing number.  
2. **Write the core logic** – keep the code short and clean.  
3. **Discuss complexity** – show you understand O(n) time and O(1) space.  
4. **Cover edge cases** – negative difference, minimal array length, etc.  

---

### 8️⃣ SEO‑Optimized Blog Summary  

> **Title**: “Master LeetCode 1228 – Missing Number in Arithmetic Progression (Java, Python, C++)”  
> **Meta Description**: “Step‑by‑step guide to solve LeetCode 1228 in Java, Python, and C++. Understand the O(n) algorithm, edge cases, and interview tips.”  
> **Keywords**: LeetCode 1228, Missing Number In Arithmetic Progression, LeetCode solution, job interview coding, Java algorithm, Python coding, C++ algorithm, interview preparation, algorithm analysis.  

---

### 9️⃣ Takeaway

- **Simple math** is the secret sauce: `d = (last - first) / (originalLength)`.  
- A **single linear scan** finds the gap, giving **O(n)** time and **O(1)** space.  
- The pattern applies to many “missing element” problems in arithmetic progressions.  

Good luck landing that interview! 🚀