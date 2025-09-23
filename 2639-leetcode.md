---
title: LeetCode 2639. Find the Width of Columns of a Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 📐 Find the Width of Columns of a Grid – A Deep‑Dive Blog  
> **LeetCode 2639 – Easy**  
> **SEO Keywords:** `LeetCode 2639`, `find column width grid`, `Java solution`, `Python solution`, `C++ solution`, `algorithm interview`, `job interview prep`

---

## 1️⃣ Introduction  

When preparing for data‑structure & algorithm interviews, you’ll often encounter problems that sound simple but are a *great* playground for clean coding, edge‑case handling, and optimal performance.  
LeetCode 2639 – *Find the Width of Columns of a Grid* is one such problem. It’s classified as **Easy**, but it tests your understanding of:

- 2‑D array traversal  
- Handling negative integers  
- String conversion vs. mathematical digit counting  
- Time/Space complexity mindset  

In this article we’ll walk through the **good**, **bad**, and **ugly** parts of solving this problem, provide ready‑to‑copy solutions in **Java**, **Python**, and **C++**, and give you interview‑ready tips that will boost your résumé.

> ⚡ **TL;DR:**  
> For each column, compute the maximum string‑length of its values (negative numbers get an extra `-`).  
> Complexity: **O(m·n)** time, **O(n)** space.  

---

## 2️⃣ Problem Statement (Re‑Worded)

You are given an `m x n` integer matrix `grid`.  
For each column `i` (0‑indexed), define the *width* of the column as the longest **string representation** of any integer in that column.  
- If the integer is non‑negative, its width is the number of digits.  
- If the integer is negative, its width is the number of digits **plus one** (for the minus sign).

Return an array `ans` of length `n` where `ans[i]` is the width of the `i`‑th column.

**Constraints**

| Parameter | Value |
|-----------|-------|
| `m == grid.length` | `1 ≤ m ≤ 100` |
| `n == grid[0].length` | `1 ≤ n ≤ 100` |
| `-10⁹ ≤ grid[r][c] ≤ 10⁹` | |

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[[1], [22], [333]]` | `[3]` | The widest value in the only column is `333` (3 digits). |
| `[[−15, 1, 3], [15, 7, 12], [5, 6, −2]]` | `[3, 1, 2]` | Column 0: `-15` → 3, Column 1: all 1‑digit, Column 2: `12` and `-2` → 2. |

---

## 3️⃣ The “Good” – Clean, Readable Solution

### 3.1 Concept

1. **Initialize** an array `ans` of size `n` with zeros.  
2. **Iterate** over each column `j`.  
3. For each element `grid[i][j]` in that column, compute its **width**:  
   - Convert to string → `len = s.length()` → Handles negative sign automatically.  
   - Update `max` if this `len` is larger.  
4. Store `max` into `ans[j]`.  
5. Return `ans`.

This approach is *O(m·n)* in time and *O(n)* in extra space.

### 3.2 Code (Java)

```java
public class Solution {
    public int[] findColumnWidth(int[][] grid) {
        int m = grid.length;
        int n = grid[0].length;
        int[] ans = new int[n];

        for (int col = 0; col < n; col++) {
            int maxWidth = 0;
            for (int row = 0; row < m; row++) {
                int len = Integer.toString(grid[row][col]).length();
                if (len > maxWidth) {
                    maxWidth = len;
                }
            }
            ans[col] = maxWidth;
        }
        return ans;
    }
}
```

### 3.3 Code (Python)

```python
class Solution:
    def findColumnWidth(self, grid: List[List[int]]) -> List[int]:
        if not grid: return []
        m, n = len(grid), len(grid[0])
        ans = [0] * n
        for col in range(n):
            max_w = 0
            for row in range(m):
                w = len(str(grid[row][col]))
                if w > max_w:
                    max_w = w
            ans[col] = max_w
        return ans
```

### 3.4 Code (C++)

```cpp
class Solution {
public:
    vector<int> findColumnWidth(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        vector<int> ans(n, 0);

        for (int col = 0; col < n; ++col) {
            int maxW = 0;
            for (int row = 0; row < m; ++row) {
                string s = to_string(grid[row][col]); // handles '-'
                int w = s.size();
                if (w > maxW) maxW = w;
            }
            ans[col] = maxW;
        }
        return ans;
    }
};
```

> ✅ *Why it’s good:*  
> - **Readable**: Clear variable names, one‑liner for width calculation.  
> - **Idiomatic**: Uses language‑specific string conversion functions.  
> - **Performance**: No unnecessary operations; only `O(m·n)` traversals.

---

## 4️⃣ The “Bad” – What You Should Avoid

| Bad Practice | Why it’s Bad | Fix |
|--------------|--------------|-----|
| Using `StringBuilder` or `String` repeatedly in tight loops (Java) | Memory churn, GC pressure | Use `Integer.toString()` which is optimized internally |
| Casting to `long` then to `String` unnecessarily | Adds overhead | Direct `toString` or `std::to_string` |
| Relying on `Math.log10()` or division to count digits | Edge cases (`0`) + floating point inaccuracies | String conversion handles all integers cleanly |
| Not handling empty input (rare, but defensive) | Runtime error on LeetCode test cases | Add guard `if (!grid) return {};` |

---

## 5️⃣ The “Ugly” – Over‑Optimized but Hard to Read

Some interviewers love a **single‑liner** or a **functional style**:

```java
public int[] findColumnWidth(int[][] grid) {
    return IntStream.range(0, grid[0].length)
                    .map(j -> IntStream.of(grid).map(row -> Integer.toString(row[j]).length()).max().getAsInt())
                    .toArray();
}
```

While concise, it sacrifices:

- **Clarity** – Someone reading the code may not immediately understand the intent.  
- **Debuggability** – Harder to step through.  
- **Performance** – The stream API may have hidden overhead.  

> ⚠️ *Avoid in real interviews* unless the interviewer explicitly asks for a functional solution.

---

## 6️⃣ Edge‑Case Checklist

| Case | Why it matters | How our solution handles it |
|------|----------------|-----------------------------|
| **All negative numbers** | Need the `-` sign counted | `Integer.toString()` includes `-`. |
| **Zero** | `0` has length 1 | String conversion returns `"0"`. |
| **Maximum/minimum integer** (`±10⁹`) | No overflow in string conversion | `toString` handles 32‑bit ints safely. |
| **Single row / single column** | Ensure loop bounds are correct | Loops over `grid.length` and `grid[0].length`. |
| **Large grid (100x100)** | Performance test | O(10⁴) operations → trivial time. |

---

## 7️⃣ Alternative Approaches

### 7.1 Math‑Based Digit Counting (Avoiding Strings)

If you want to avoid string conversion:

```java
int digits(int x) {
    if (x == 0) return 1;
    int len = 0;
    if (x < 0) {
        len++;  // for '-'
        x = -x;
    }
    while (x > 0) {
        x /= 10;
        len++;
    }
    return len;
}
```

This works, but the string conversion approach is *cleaner* and less error‑prone for most interview contexts.

### 7.2 Using Pre‑Calculated Widths

You could pre‑compute a 2‑D array `widths` of the same shape as `grid` and then take column maxima. This adds unnecessary memory (`O(m·n)` extra), so it’s not worth it for an Easy problem.

---

## 8️⃣ Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Clean string‑based | **O(m·n)** | **O(n)** (output array) |
| Math‑based | **O(m·n)** | **O(n)** |
| One‑liner stream | **O(m·n)** | **O(n)** |

Both approaches satisfy the constraints easily.

---

## 9️⃣ Interview Tips & Resume Highlights

1. **Explain the core idea first** – “We iterate over columns and track the maximum width using string conversion.”
2. **Mention edge cases** – negative numbers, zero, min/max ints.
3. **Talk about complexity** – “This runs in linear time with respect to the number of elements.”
4. **Show the code** – Provide a clean snippet (like above) and ask the interviewer if they prefer a different language.
5. **Optional** – “If we had to avoid string conversion, we could use a digit‑counting routine.” This demonstrates depth.

> 📌 *Resume bullet:*  
> • Designed and implemented an efficient O(m·n) algorithm for computing column widths in 2‑D integer grids, handling negative values and edge cases, resulting in clear, maintainable code across Java, Python, and C++.

---

## 🔟 Conclusion

LeetCode 2639 is a deceptively simple problem that offers a solid platform to demonstrate clean coding, thorough edge‑case coverage, and interview communication skills. By focusing on readability and correct handling of negatives and zero, you can ace this question in a coding interview and showcase a polished skill set on your résumé.

Happy coding, and best of luck landing that job! 🚀

---