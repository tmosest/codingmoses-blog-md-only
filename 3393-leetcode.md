---
title: LeetCode 3393. Count Paths With the Given XOR Value - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 3393 – Count Paths With the Given XOR Value  
**Languages**: Java, Python, C++  
**Time complexity**: O(m · n · 16)  
**Space complexity**: O(m · n · 16) ( or O(m · 16) when we use the space‑optimised version)

Below you’ll find three idiomatic solutions:

| Language | Style | Space | Notes |
|----------|-------|-------|-------|
| Java     | Top‑down memoisation | O(m · n · 16) | Recursion + 3‑D array |
| Python   | Bottom‑up DP | O(m · n · 16) | Uses `defaultdict`/list comprehension |
| C++      | Space‑optimised | O(m · 16) | Two rows of 16‑element vectors |

> **Tip for interviews** – Show the interviewer that you understand why the XOR values are only 0–15 (grid values < 16). That’s the key to keeping the DP small.

---

### 1.1  Java – Top‑down memoisation

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;
    private int m, n, k;
    private int[][] grid;
    private int[][][] memo;     // memo[i][j][xor]

    public int countPathsWithXorValue(int[][] grid, int k) {
        this.grid = grid;
        this.k = k;
        this.m = grid.length;
        this.n = grid[0].length;
        memo = new int[m][n][16];
        for (int[][] row : memo) {
            for (int[] arr : row) Arrays.fill(arr, -1);
        }
        return dfs(0, 0, 0);
    }

    private int dfs(int i, int j, int curXor) {
        if (i >= m || j >= n) return 0;
        curXor ^= grid[i][j];
        if (i == m - 1 && j == n - 1) return curXor == k ? 1 : 0;
        int & = memo[i][j][curXor];
        if (& != -1) return &;
        int right = dfs(i, j + 1, curXor) % MOD;
        int down  = dfs(i + 1, j, curXor) % MOD;
        return memo[i][j][curXor] = (right + down) % MOD;
    }
}
```

---

### 1.2  Python – Bottom‑up DP (full table)

```python
class Solution:
    MOD = 1_000_000_007

    def countPathsWithXorValue(self, grid: List[List[int]], k: int) -> int:
        m, n = len(grid), len(grid[0])
        # dp[i][j][xor]  -> ways from (i,j) to end with current xor
        dp = [[[0] * 16 for _ in range(n + 1)] for _ in range(m + 1)]
        dp[m-1][n-1][grid[m-1][n-1] ^ k] = 1

        for i in range(m-1, -1, -1):
            for j in range(n-1, -1, -1):
                for x in range(16):
                    new_x = x ^ grid[i][j]
                    if j + 1 < n:
                        dp[i][j][x] = (dp[i][j][x] + dp[i][j+1][new_x]) % self.MOD
                    if i + 1 < m:
                        dp[i][j][x] = (dp[i][j][x] + dp[i+1][j][new_x]) % self.MOD
        return dp[0][0][0]
```

---

### 1.3  C++ – Space‑optimised DP (two rows)

```cpp
class Solution {
public:
    const int MOD = 1'000'000'007;

    int countPathsWithXorValue(vector<vector<int>>& grid, int k) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> next(n + 1, vector<int>(16, 0));
        next[n-1][k] = 1;                     // base case

        for (int i = m-1; i >= 0; --i) {
            vector<vector<int>> cur(n + 1, vector<int>(16, 0));
            for (int j = n-1; j >= 0; --j) {
                for (int x = 0; x < 16; ++x) {
                    int newX = x ^ grid[i][j];
                    cur[j][x] = ( (j+1 < n ? cur[j+1][newX] : 0) +
                                  (i+1 < m ? next[j][newX] : 0) ) % MOD;
                }
            }
            next.swap(cur);
        }
        return next[0][0];
    }
};
```

---

## 2.  Blog Article – “The Good, The Bad, and The Ugly of LeetCode 3393”

> **SEO Keywords**:  
> *Leetcode 3393*, *Count Paths With the Given XOR Value*, *dynamic programming grid XOR*, *software engineer interview*, *grid path counting*, *XOR path problems*, *job interview algorithms*, *DP tricks*, *space‑optimised DP*, *job‑search guide*  

### 2.1  Problem Snapshot

> **Name** – Count Paths With the Given XOR Value  
> **Difficulty** – Medium  
> **Key Constraints**  
> - Grid size up to 300 × 300  
> - Each cell value < 16 (4 bits)  
> - Target XOR `k` < 16  
> - Return answer modulo `10^9 + 7`

The problem asks: *How many ways can you walk from the top‑left to the bottom‑right cell, only moving right or down, such that the XOR of all visited cell values equals `k`?*  

> **Why the XOR values stay tiny** – Because every cell is 0–15, the cumulative XOR is always 0–15. This allows us to keep a DP dimension of size 16 rather than `2^16` or `1e9`.

---

### 2.2  The Good

| What is great? | Why it matters? |
|----------------|-----------------|
| **Small state space** – 4‑bit XOR → 16 DP states per cell. | Makes O(m · n · 16) feasible. |
| **Classic DP on a grid** – The same recurrence appears in “Unique Paths II” (with obstacles) or “Minimum Path Sum”. | Candidates can reuse familiar patterns. |
| **Clear base‑case intuition** – At the destination, we check if the last XOR matches `k`. | Gives a natural termination condition for recursion. |
| **Potential for space optimisation** – Only two rows are needed, because movement is monotonic. | Reduces memory from ~108 MB to ~6 MB in C++. |

> **Interview Insight** – Ask the interviewer: *Do we really need 300 × 300 × 16 memory?* The answer: “No – we can compress it to `m·16`”. This showcases algorithmic thinking.

---

### 2.3  The Bad

| Common pitfall | Fix |
|----------------|-----|
| **Using a 2‑D DP without XOR dimension** – Failing to remember that XOR can change the state. | Add a third dimension: `dp[i][j][xor]`. |
| **Ignoring the modulo at each step** – Overflow on `int`. | Use `long` in Java/Python or `int64_t` in C++, apply `% MOD` frequently. |
| **Recursion depth > 1000** – Causes stack overflow on 300 × 300 grids. | Either increase recursion limit (Python) or use iterative DP. |
| **Treating XOR as additive** – Mistaking XOR for addition. | Remember: `newX = oldX ^ cellVal`. |

> **Why these are “bad”** – They turn a clean DP into a hard‑to‑understand mess, and they’ll be the first things the interviewer catches.

---

### 2.4  The Ugly

1. **Brute‑Force DFS**  
   ```python
   def dfs(i, j, cur_xor):
       if i==m-1 and j==n-1: return cur_xor==k
       # explore both branches
   ```
   *Time:* O(2^(m+n)) – impossible.

2. **Bitmasking 2^16 States**  
   ```python
   dp[i][j][mask]  # mask in 0..65535
   ```
   *Space:* ≈ 10 GB for 300 × 300.  
   *Result:* Memory crash.

3. **Recursive memoisation without pruning**  
   Without the 4‑bit observation, one might pre‑allocate `dp[m][n][65536]`, which again blows up.

> **Why it’s ugly** – The naive approach shows a lack of insight. Interviews penalise candidates who start with a huge state space.

---

### 2.5  Implementation Walk‑through

1. **State definition**  
   ```text
   dp[i][j][xor] = number of ways to reach (m-1, n-1)
                   from (i, j) while having current cumulative xor = xor
   ```
2. **Transition**  
   - Move right: `dp[i][j][xor] += dp[i][j+1][xor ^ grid[i][j]]`  
   - Move down: `dp[i][j][xor] += dp[i+1][j][xor ^ grid[i][j]]`  
   All operations are taken modulo `MOD`.

3. **Base case**  
   At `(m-1, n-1)`:  
   ```text
   dp[m-1][n-1][grid[m-1][n-1] ^ k] = 1
   ```
   Because we want the final xor to be `k`, the *current* xor at the destination must be `grid[m-1][n-1] ^ k`.

4. **Order of filling**  
   - Bottom‑up from the bottom‑right corner to the top‑left corner.  
   - Because we only reference cells to the right or below, the DP respects the monotonic path rule.

5. **Space optimisation**  
   Only the current and next rows are needed:  
   ```text
   nextRow[j][xor]  <-- ways starting at (i+1, j)
   curRow[j][xor]   <-- ways starting at (i, j)
   ```
   The inner loop over the 16 xor values stays.

---

### 2.6  Why the XOR Trick Matters

| What would happen if cell values were up to 10⁹? | What if `k` were up to 10⁹? |
|----------------------------------------------|----------------------------|
| The XOR could become 32 bit → 2³² states. | Same – 2³² states. |
| Memory would explode (≈ 300 · 300 · 4 · 10⁹ bytes). | Not feasible. |
| We’d need *bit‑DP* or *meet‑in‑the‑middle*. | We’d switch to a different algorithm (e.g., BFS + bitset). |

Because of the **4‑bit constraint**, the DP stays tiny, and the problem is solvable with an “ordinary” DP.

---

### 2.7  Interview‑Ready Tips

| Situation | What to say |
|-----------|-------------|
| **Asked for an O(m · n) solution** | “We can keep just two rows of 16‑element arrays – that’s the standard space‑optimised DP.” |
| **Wants a recursive solution** | “We can memoise the state `dp[i][j][xor]`. Recursion keeps the code clean, but watch the stack depth.” |
| **Wants to know why modulo is used** | “Because the answer can grow exponentially (≈ 2^(m+n)). We keep it in the 64‑bit range and then mod 10⁹ + 7 at every addition.” |
| **Asks about time limits** | “The DP is 16 × m × n ≈ 1.44 × 10⁶ operations for the max grid, which is comfortably under LeetCode’s 2 s limit.” |
| **Requests an optimization** | “If memory is a concern, we can drop the third dimension from a full 3‑D table to just a 2‑D table of `m` × 16 or even `n` × 16.” |

---

### 2.8  Test‑Your‑Solution Checklist

| Scenario | Expected Output |
|----------|-----------------|
| 1 × 1 grid `[k]` | 1 (the single path) |
| All zeros, `k` = 0 | `C(m+n-2, m-1)` (normal “unique paths”) |
| Grid all 1, `k` = 1 | 0 (odd length → XOR = 1, but path length is m+n‑1, check parity) |
| Mixed 0–15, random k | Run a brute force for n≤5 to verify. |

---

### 2.9  Conclusion – How to Use This in Your Job Hunt

* **Showcase DP + Bit‑wise insight** – Many interviewers look for the ability to spot “state compression” opportunities.  
* **Explain the space‑optimisation** – It demonstrates that you can read the problem’s constraints and squeeze memory.  
* **Discuss the “XOR trick”** – It’s a common interview theme; many variations (AND/OR path problems) appear.  
* **Practice on LeetCode** – Add the problem to your “Solved” list and keep the code in your GitHub for quick reference.  

> **Final Thought** – Mastering LeetCode 3393 teaches you how to turn a seemingly exponential state into a linear‑time DP by leveraging the problem’s hidden 4‑bit property. This skill is a perfect example of the algorithmic elegance you’ll want to display on your next software‑engineering interview.  

---

Happy coding—and happy interviewing! 🚀