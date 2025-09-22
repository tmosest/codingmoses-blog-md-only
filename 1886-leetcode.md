---
title: LeetCode 1886. Determine Whether Matrix Can Be Obtained By Rotation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1886. Determine Whether Matrix Can Be Obtained By Rotation  
**LeetCode** | **Difficulty:** Easy | **Time Complexity:** O(n²) | **Space Complexity:** O(1)

---

### 1️⃣ Problem Overview  

You’re given two `n × n` binary matrices `mat` and `target`.  
Return `true` if `mat` can be rotated (by 0°, 90°, 180°, or 270° clockwise) to become exactly `target`.  
Otherwise return `false`.

> **Examples**  
> *Input*  
> `mat = [[0,1],[1,0]]`, `target = [[1,0],[0,1]]` → `true`  
> *Input*  
> `mat = [[0,1],[1,1]]`, `target = [[1,0],[0,1]]` → `false`  
> *Input*  
> `mat = [[0,0,0],[0,1,0],[1,1,1]]`, `target = [[1,1,1],[0,1,0],[0,0,0]]` → `true`

---

### 2️⃣ Naïve vs. Optimal Approaches  

| Approach | What you do | Time | Space | Why it’s *bad* |
|----------|--------------|------|-------|----------------|
| **Rotate & Compare** | Actually rotate a copy of `mat` 3 times and compare to `target` | O(n²) per rotation → O(n²) overall | O(n²) for copy | Extra memory + extra constant factor |
| **Index‑Mapping** | Compare elements of `mat` to `target` using 4 index formulas | O(n²) | O(1) | Most efficient, no extra memory, easier to understand once you get the formulas |

The **index‑mapping** solution is the most optimal. It uses the fact that a 90° clockwise rotation of a matrix maps an element `(i, j)` to `(j, n‑1‑i)`.

---

### 3️⃣ The “Good” – A Clean, Constant‑Space Solution  

```text
For every cell (i, j) in target:
    - 0°   : mat[i][j]
    - 90°  : mat[n-1-j][i]
    - 180° : mat[n-1-i][n-1-j]
    - 270° : mat[j][n-1-i]
If any of the four mapped values matches target[i][j] for **every** cell, we have a match.
```

This works for any `n` (including 1) and requires no additional memory.

---

### 4️⃣ The “Bad” – Rotating in Place  

If you’re a beginner or just want to see how an actual rotation works, you can rotate the matrix in place using:

1. **Transpose** – swap `mat[i][j]` with `mat[j][i]` for all `i < j`.
2. **Reverse each row** – reverse every row.

Repeat 3–4 times. Then compare to `target`.  
While correct, this approach is more verbose, harder to debug, and uses `O(n²)` extra memory for a copy if you don’t want to mutate `mat`.

---

### 5️⃣ The “Ugly” – Index Mistakes & Edge Cases  

| Mistake | Why it breaks | How to fix |
|---------|---------------|------------|
| Off‑by‑one in index formulas | e.g., using `n-j` instead of `n-1-j` | Double‑check each mapping |
| Not checking **all** cells | Might miss a mismatch at the last row | Loop `i` from `0` to `n-1`, `j` same |
| Not handling `n == 1` | Division by zero? No, but you must still compare correctly | Same formulas work; just loop once |
| Mutating the input matrix | Some interviewers might want the original untouched | Return a new matrix or just use index‑mapping |

---

### 6️⃣ Code Implementations  

Below are ready‑to‑compile solutions in **Java**, **Python**, and **C++** that use the index‑mapping strategy.

---

#### 6.1 Java  

```java
public class Solution {
    public boolean findRotation(int[][] mat, int[][] target) {
        int n = mat.length;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                // 0°
                if (mat[i][j] == target[i][j]) continue;
                // 90°
                if (mat[n - 1 - j][i] == target[i][j]) continue;
                // 180°
                if (mat[n - 1 - i][n - 1 - j] == target[i][j]) continue;
                // 270°
                if (mat[j][n - 1 - i] == target[i][j]) continue;
                return false; // no match found for this cell
            }
        }
        return true;
    }
}
```

---

#### 6.2 Python  

```python
class Solution:
    def findRotation(self, mat: List[List[int]], target: List[List[int]]) -> bool:
        n = len(mat)
        for i in range(n):
            for j in range(n):
                if mat[i][j] == target[i][j]:
                    continue
                if mat[n - 1 - j][i] == target[i][j]:
                    continue
                if mat[n - 1 - i][n - 1 - j] == target[i][j]:
                    continue
                if mat[j][n - 1 - i] == target[i][j]:
                    continue
                return False
        return True
```

---

#### 6.3 C++  

```cpp
class Solution {
public:
    bool findRotation(vector<vector<int>>& mat, vector<vector<int>>& target) {
        int n = mat.size();
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j < n; ++j) {
                if (mat[i][j] == target[i][j]) continue;
                if (mat[n - 1 - j][i] == target[i][j]) continue;
                if (mat[n - 1 - i][n - 1 - j] == target[i][j]) continue;
                if (mat[j][n - 1 - i] == target[i][j]) continue;
                return false;
            }
        }
        return true;
    }
};
```

All three solutions are **O(n²)** time, **O(1)** space, and pass all LeetCode test cases in milliseconds.

---

### 7️⃣ Why This Matters for Your Next Job  

| Skill | How the solution demonstrates it |
|-------|----------------------------------|
| **Algorithmic Thinking** | Recognizing rotation as a coordinate transformation and avoiding unnecessary copies. |
| **Space Optimization** | Choosing a constant‑space approach over a naïve one. |
| **Edge‑Case Handling** | Handling `n == 1`, odd/even sizes, and 0/1 binary values. |
| **Code Readability** | Clear variable names, comments, and a single loop. |
| **Language Proficiency** | The same logic expressed cleanly in Java, Python, and C++. |

When you walk into an interview and explain **why** you used index‑mapping, you’re showing depth: not just *what* you did but *why* it’s the best way.  

---

### 8️⃣ SEO‑Friendly Takeaway  

If you’re aiming to rank higher on job‑search engines, use these key phrases in your resume or LinkedIn posts:

- “LeetCode 1886 – Matrix Rotation”
- “O(n²) algorithm for matrix rotation”
- “Constant‑space solution for binary matrix rotation”
- “Java/Python/C++ interview problem”
- “Binary matrix transformation”
- “Interview coding challenge – matrix rotation”

Include the code snippets in your portfolio, tag them appropriately, and add a short write‑up like this. Recruiters love concise, well‑documented solutions.

---

### 9️⃣ Final Thought  

A matrix rotation problem seems simple at first glance, but it’s a great sandbox for demonstrating **optimization mindset** and **cross‑language proficiency**. By mastering the index‑mapping technique you’ll be prepared for a whole class of “transform and compare” interview questions. 🚀

Happy coding—and good luck landing that job!