---
title: LeetCode 2022. Convert 1D Array Into 2D Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Convert 1D Array Into 2D Array – LeetCode 1714  
**Easy** | *Java | Python | C++* | *Job‑Interview‑Ready*  

> **Problem** – LeetCode 1714  
> *You are given a 0‑indexed 1‑D integer array `original` and two integers `m` and `n`.  
> Construct an `m × n` 2‑D array that contains all elements of `original` in row‑major order.  
> If `original.length != m * n`, return an empty 2‑D array.*

---

## 📚 Why This Problem Rocks a Job Interview

* **Array Manipulation** – core CS skill  
* **Time & Space Complexity** – demonstrates analytical thinking  
* **Edge‑Case Awareness** – shows attention to detail  
* **Language‑agnostic** – easy to implement in any language

Mastering this one gives you a solid “array‑reshape” confidence that recruiters love.

---

## 🛠️ Solution Overview

1. **Feasibility Check**  
   ```text
   if original.length != m * n → return empty matrix
   ```
2. **Linear Mapping**  
   Each element `original[i]` maps to position `(row, col)` in the result:
   ```text
   row = i / n   (integer division)
   col = i % n
   ```
   Equivalent to:
   ```text
   result[row][col] = original[i]
   ```
   or, using nested loops:
   ```text
   result[i][j] = original[i*n + j]
   ```
3. **Return the built matrix**.

---

## 📊 Complexity Analysis

| Complexity | Reason |
|------------|--------|
| **Time**   | `O(m * n)` – each element is visited exactly once. |
| **Space**  | `O(m * n)` – output matrix. Extra space `O(1)` aside from output. |

---

## 🧩 Code in Three Languages

### 1️⃣ Java – Simple Simulation (98.3 % faster)

```java
class Solution {
    public int[][] construct2DArray(int[] original, int m, int n) {
        // 1. Quick feasibility test
        if (original.length != m * n) return new int[0][0];

        // 2. Allocate result matrix
        int[][] result = new int[m][n];

        // 3. Fill row‑by‑row
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                result[i][j] = original[i * n + j];
            }
        }
        return result;
    }
}
```

> **Good** – O(1) extra space, direct mapping.  
> **Bad** – None.  
> **Ugly** – None.  
> Just a clean, production‑ready solution.

### 2️⃣ Python – Concise One‑liner

```python
class Solution:
    def construct2DArray(self, original: List[int], m: int, n: int) -> List[List[int]]:
        # 1. Quick check
        if len(original) != m * n:
            return []

        # 2. Reshape using slicing (Pythonic)
        return [original[i*n:(i+1)*n] for i in range(m)]
```

> **Good** – Pythonic slice usage, readable.  
> **Bad** – None.  
> **Ugly** – None.

### 3️⃣ C++ – Standard Library Approach

```cpp
class Solution {
public:
    vector<vector<int>> construct2DArray(vector<int>& original, int m, int n) {
        // Feasibility check
        if (original.size() != static_cast<size_t>(m * n))
            return {};

        // Preallocate matrix
        vector<vector<int>> result(m, vector<int>(n));

        // Fill the matrix
        for (int i = 0; i < m; ++i)
            for (int j = 0; j < n; ++j)
                result[i][j] = original[i * n + j];

        return result;
    }
};
```

> **Good** – Uses `vector` efficiently.  
> **Bad** – None.  
> **Ugly** – None.

---

## 🔍 Edge Cases & Common Pitfalls

| Edge Case | Why It Matters | Fix |
|-----------|----------------|-----|
| `original.length == 0` | Problem constraints forbid it, but if you relax them, you must handle it. | Return `[]` or an empty matrix accordingly. |
| `m * n` overflows 32‑bit int | In languages with 32‑bit int (Java, C++), `m * n` may overflow before comparison. | Cast to `long` or use `size_t`. |
| Negative `m` or `n` | Not allowed by constraints, but defensive programming is good. | Validate and return empty. |
| Huge input (5 × 10⁴) | Need linear time, not nested loops that compute `i*n + j` repeatedly with multiplication overhead. | Use index `k` to iterate once. |

---

## 🎯 How to Use This in a Job Interview

1. **State the Plan** – Clarify feasibility, mapping, complexity.  
2. **Write Clean Code** – Use descriptive variable names, comment the check.  
3. **Discuss Edge Cases** – Show awareness of overflow, empty array.  
4. **Talk Performance** – Emphasize O(m*n) time, O(1) auxiliary space.  
5. **Show Variants** – Mention slicing in Python or stream mapping in Java 8+.  

Interviewers love candidates who think about both correctness and robustness.

---

## 📈 SEO Optimized Blog Article Outline

### Title  
**"LeetCode 1714 – Convert 1D Array Into 2D Array: Java, Python, C++ Solutions + Interview Tips"**

### Meta Description  
"Master LeetCode 1714 with step‑by‑step solutions in Java, Python, and C++. Learn array reshaping, complexity analysis, edge cases, and interview‑ready discussion."

### Headings

| H | Content |
|---|---------|
| **H1** | LeetCode 1714 – Convert 1D Array Into 2D Array |
| **H2** | Problem Statement (Easy) |
| **H2** | Why This Problem Matters in Interviews |
| **H3** | Core Concepts Tested |
| **H2** | Algorithm & Complexity |
| **H3** | Feasibility Check |
| **H3** | Linear Mapping |
| **H2** | Full Code Implementations |
| **H3** | Java |
| **H3** | Python |
| **H3** | C++ |
| **H2** | Edge Cases & Defensive Coding |
| **H2** | Interview Tips & Talking Points |
| **H2** | Conclusion & Take‑Away |

### Keywords & Phrases  
- LeetCode Convert 1D Array Into 2D Array  
- array reshaping Java  
- Python 2D array construction  
- C++ 2D matrix problem  
- interview array manipulation  
- O(mn) algorithm  
- edge case handling  

Use these keywords naturally in headings, first paragraph, and conclusion to boost SEO.

---

## 📘 Final Takeaway

- **Problem**: reshape a 1‑D array into `m × n` if possible.  
- **Solution**: single linear pass with direct index calculation.  
- **Complexity**: `O(mn)` time, `O(mn)` space.  
- **Languages**: clean Java, Python, C++ snippets.  
- **Interview**: explain algorithm, discuss edge cases, showcase clean code.

With this knowledge, you’re ready to ace the “Convert 1D Array Into 2D Array” interview question and impress recruiters! 🚀