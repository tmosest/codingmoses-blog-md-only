---
title: LeetCode 1314. Matrix Block Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code – Three Languages

Below are clean, production‑ready implementations of **LeetCode 1314 – Matrix Block Sum** written in **Java, Python, and C++**.  
All three use a **2‑D prefix sum** (also called an integral image) to compute every block sum in **O(m × n)** time and **O(m × n)** auxiliary memory – the optimal solution for this problem.

> **Tip:**  Prefix sums are a classic interview pattern.  Mastering them gives you an instant boost for a wide variety of “sub‑array sum” questions.

---

### Java – Prefix‑Sum + Clean DP

```java
import java.util.Arrays;

public class Solution {

    public int[][] matrixBlockSum(int[][] mat, int k) {
        int m = mat.length;
        int n = mat[0].length;

        // 1‑based prefix sum array
        int[][] pref = new int[m + 1][n + 1];

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                pref[i][j] = pref[i - 1][j] + pref[i][j - 1]
                           - pref[i - 1][j - 1] + mat[i - 1][j - 1];
            }
        }

        int[][] ans = new int[m][n];

        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int r1 = Math.max(0, i - k) + 1;  // inclusive, 1‑based
                int c1 = Math.max(0, j - k) + 1;
                int r2 = Math.min(m, i + k + 1);  // exclusive
                int c2 = Math.min(n, j + k + 1);

                ans[i][j] = pref[r2][c2] - pref[r1 - 1][c2]
                          - pref[r2][c1 - 1] + pref[r1 - 1][c1 - 1];
            }
        }

        return ans;
    }

    // ---- for quick manual testing ----
    public static void main(String[] args) {
        Solution s = new Solution();
        int[][] mat = {{1,2,3},{4,5,6},{7,8,9}};
        int[][] res = s.matrixBlockSum(mat, 1);
        System.out.println(Arrays.deepToString(res));
    }
}
```

---

### Python – Fast Prefix‑Sum (NumPy‑free)

```python
class Solution:
    def matrixBlockSum(self, mat: List[List[int]], k: int) -> List[List[int]]:
        m, n = len(mat), len(mat[0])

        # 1‑based prefix sum
        pref = [[0] * (n + 1) for _ in range(m + 1)]
        for i in range(1, m + 1):
            row_sum = 0
            for j in range(1, n + 1):
                row_sum += mat[i-1][j-1]
                pref[i][j] = pref[i-1][j] + row_sum

        ans = [[0] * n for _ in range(m)]
        for i in range(m):
            for j in range(n):
                r1 = max(0, i - k) + 1
                c1 = max(0, j - k) + 1
                r2 = min(m, i + k + 1)
                c2 = min(n, j + k + 1)
                ans[i][j] = (
                    pref[r2][c2]
                    - pref[r1-1][c2]
                    - pref[r2][c1-1]
                    + pref[r1-1][c1-1]
                )
        return ans
```

> **Note:**  The inner loop is deliberately written in plain Python for clarity.  If you’re on Python 3.8+ you can use `itertools.accumulate` or `NumPy` for a slight speed boost.

---

### C++ – O(m·n) with 2‑D Prefix Sum

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> matrixBlockSum(vector<vector<int>>& mat, int k) {
        int m = mat.size(), n = mat[0].size();
        vector<vector<int>> pref(m + 1, vector<int>(n + 1, 0));

        // Build 1‑based prefix sum
        for (int i = 1; i <= m; ++i) {
            int rowSum = 0;
            for (int j = 1; j <= n; ++j) {
                rowSum += mat[i-1][j-1];
                pref[i][j] = pref[i-1][j] + rowSum;
            }
        }

        vector<vector<int>> ans(m, vector<int>(n, 0));

        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++j) {
                int r1 = max(0, i - k) + 1;
                int c1 = max(0, j - k) + 1;
                int r2 = min(m, i + k + 1);
                int c2 = min(n, j + k + 1);
                ans[i][j] = pref[r2][c2] - pref[r1-1][c2]
                          - pref[r2][c1-1] + pref[r1-1][c1-1];
            }
        }
        return ans;
    }
};
```

---

## 2. Blog Article – “Matrix Block Sum: The Good, The Bad, and the Ugly”

> **SEO Keywords**: Matrix Block Sum, LeetCode 1314, 2‑D prefix sum, dynamic programming, interview coding, Java, Python, C++ solution, coding interview, job interview, algorithm.

---

### H1 – Matrix Block Sum: Mastering a Classic LeetCode Problem

> *“Given an `m × n` matrix and an integer `k`, compute the sum of every `k`‑radius block around each cell.”*  
> This seemingly simple task is a staple in coding interviews.  It tests your ability to transform a brute‑force loop into a mathematically clever solution.

### H2 – The Problem, in Plain English

- **Input**: `mat` – `m × n` integer matrix; `k` – non‑negative integer.
- **Output**: `ans` – same size matrix where each `ans[i][j]` equals the sum of all elements `mat[r][c]` satisfying  
  `|r - i| ≤ k` and `|c - j| ≤ k`.  
  Borders are truncated – you never read outside the matrix.

> *Why does this matter?*  
> Interviewers often ask for an “O(m × n)” solution, meaning you cannot afford a naive nested loop for every cell.

### H2 – Good: The O(m × n) Prefix‑Sum Approach

| Step | What Happens | Why It Works |
|------|--------------|--------------|
| 1. **Build a 2‑D prefix sum** | `pref[i][j]` = sum of all cells in rectangle `(0,0)` to `(i-1,j-1)`. | Allows constant‑time rectangle sum queries. |
| 2. **Convert every query into a prefix‑sum rectangle** | The block around `(i,j)` is a rectangle `[r1,r2] × [c1,c2]`. | The sum = `pref[r2][c2] - pref[r1-1][c2] - pref[r2][c1-1] + pref[r1-1][c1-1]`. |
| 3. **Compute ans in O(1) per cell** | For all `m × n` cells. | Total time = `O(m × n)`. |
| 4. **Memory** | `pref` is `(m+1)×(n+1)`; `ans` is `m×n`. | Acceptable for constraints up to 100. |

> *Result*: 100% correctness, optimal speed, and clean code.

### H2 – Bad: The Brute‑Force `O(m × n × k²)` Solution

```python
for i in range(m):
    for j in range(n):
        total = 0
        for r in range(max(0, i-k), min(m, i+k+1)):
            for c in range(max(0, j-k), min(n, j+k+1)):
                total += mat[r][c]
        ans[i][j] = total
```

- **Time**: `O(m × n × k²)` → 10⁶ × k² operations.  
  With `k` up to 100, this can blow up to 10¹⁰ – far too slow.  
- **Space**: Same as optimal, but wasted effort.  

> *Why is it bad?*  
> Interviewers expect you to think beyond the obvious nested loops.  Brute force is a classic red flag.

### H2 – Ugly: Edge‑Case Mishandling & Off‑By‑One Traps

1. **Off‑by‑one in prefix indices**  
   - `pref` is 1‑based, but many newbies forget the +1 offset, producing wrong results.
2. **Boundary clamping**  
   - Forgetting to `max(0, …)` or `min(m, …)` leads to array‑out‑of‑bounds errors.
3. **Integer overflow**  
   - In languages like Java or C++, cumulative sums can exceed `int`.  Use `long` or `long long` if `mat[i][j]` can be large.
4. **Inconsistent variable naming**  
   - Mixing `r1`/`c1` for inclusive vs exclusive bounds confuses readers.

> *Avoiding Ugly*:  
> 1. Write helper functions `int clamp(int val, int low, int high)`;  
> 2. Keep the 1‑based prefix matrix in mind;  
> 3. Test on small matrices first (`[[1]]`, `[[1,2],[3,4]]`).

### H2 – Algorithm Deep Dive: 2‑D Prefix Sum Mechanics

A 2‑D prefix sum `pref[i][j]` is defined as:

```
pref[i][j] = Σ_{r < i, c < j} mat[r][c]
```

The sum of any sub‑matrix `[r1, r2] × [c1, c2]` (inclusive) is:

```
S = pref[r2+1][c2+1]
    - pref[r1][c2+1]
    - pref[r2+1][c1]
    + pref[r1][c1]
```

Because each term either adds or subtracts overlapping regions exactly once.  

> *Why 1‑based?*  
> The +1 shifts allow the “empty” row/column (`pref[0][*] = 0`) to act as the zero‑boundary, simplifying edge cases.

### H2 – Implementation Tips for the Job Interview

| Language | Tip | Rationale |
|----------|-----|-----------|
| **Java** | Use `long` for prefix sums; cast at output to `int` if problem guarantees no overflow. |
| **Python** | Write the prefix loop in a single line, but keep `row_sum` for clarity.  Show you know the `O(1)` query formula. |
| **C++** | Prefer `vector<vector<int>>` over raw arrays; avoid global variables unless specified. |
| **All** | **Explain your idea first**: Outline the prefix‑sum concept verbally before coding.  This demonstrates algorithmic thinking. |

### H2 – Complexity Summary (tabulated)

| Approach | Time | Space | Suitable For |
|----------|------|-------|--------------|
| Brute‑Force | `O(m × n × k²)` | `O(m × n)` | Only if `k` is tiny (`k < 5`). |
| Prefix‑Sum | `O(m × n)` | `O(m × n)` | Best for all constraints. |
| Sliding Window (Alternative) | `O(m × n)` (two passes) | `O(m × n)` | Works if you can maintain a rolling sum per row. |

> *Pro Tip*:  
> Show both the code and a **mental diagram** of the rectangle query.  Visual aids impress interviewers.

### H2 – Final Thoughts: From Problem to Portfolio

*Matrix Block Sum* is more than a coding puzzle—it’s a micro‑ecosystem for **dynamic programming**, **prefix sums**, and **boundary handling**.  
Mastering it gives you:

- Confidence in transforming nested loops into mathematical formulas.
- A reusable pattern for future interview problems involving 2‑D cumulative sums.
- A badge of honor: “I can solve O(m × n) problems in seconds.”

> *Ready to ace your next coding interview?*  
> Practice the prefix‑sum solution in Java, Python, and C++.  Deploy it in a timed mock interview, and watch recruiters take notice.

### H3 – Call to Action

> **Share** your favorite implementation in the comments.  
> **Download** the full code snippets above.  
> **Subscribe** for more “The Good, The Bad, and the Ugly” breakdowns on LeetCode problems.  

--- 

### H3 – About the Author

> A seasoned software engineer who has cleared technical interviews at Google, Amazon, and Microsoft.  
> Passionate about clean algorithms and mentoring the next generation of coders.  
> Follow on LinkedIn: [@YourName](#) | Blog: [yourblog.com](#)

---

*End of article.*