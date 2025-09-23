---
title: LeetCode 474. Ones and Zeroes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧩 LeetCode 474 – *Ones and Zeroes*  
### The Good, The Bad, and The Ugly – A Job‑Interview‑Ready Guide

> **Keywords**: LeetCode 474, Ones and Zeroes, dynamic programming, knapsack, job interview, software engineer, algorithm, coding interview, coding challenge, data‑structures, DP 2‑dimensional, interview prep, resume, Java, Python, C++

---

### 1️⃣ Problem Statement

You’re given an array of binary strings `strs` and two integers `m` and `n`.  
Find the size of the largest subset of `strs` such that the subset contains **at most `m` zeros** and **at most `n` ones**.

```
Input:
    strs = ["10","0001","111001","1","0"]
    m = 5
    n = 3

Output:
    4    // {"10","0001","1","0"} is the optimal subset
```

| Constraints | Range |
|-------------|-------|
| 1 ≤ strs.length ≤ 600 |  |
| 1 ≤ strs[i].length ≤ 100 |  |
| strs[i] consists only of '0' and '1' |  |
| 1 ≤ m,n ≤ 100 |  |

---

### 2️⃣ Why This Problem Rocks Your Interview Portfolio

| Feature | Why It Matters |
|---------|----------------|
| **Classic 0/1 Knapsack** | Many companies ask variations of the knapsack problem. Proving you can adapt it to a two‑dimensional capacity shows deep DP understanding. |
| **DP State‑Space Reduction** | From `O(L * m * n)` to `O(m * n)` by iterating backwards. This trade‑off is a favourite pattern in coding interviews. |
| **Edge‑Case Handling** | Requires counting zeros/ones, handling large inputs, and ensuring no overflow – all good “clean‑code” tests. |
| **Multi‑Language Mastery** | Showcasing the same algorithm in Java, Python, and C++ demonstrates language‑agnostic algorithm thinking – a huge plus in multi‑stacked teams. |

> **SEO Tip:** Search terms like “LeetCode 474 solution”, “Ones and Zeroes dynamic programming”, and “0/1 knapsack interview question” are hot keywords that recruiters use when scanning candidate blogs.  

---

### 3️⃣ Intuition & Key Insight

Think of the problem as a **two‑dimensional knapsack**:

- **Weight dimensions:** number of zeros (`0`) and number of ones (`1`).
- **Capacity:** `m` zeros and `n` ones.
- **Item value:** each string contributes `1` to the total subset size.

You can include or exclude each string exactly once – classic 0/1 knapsack logic.

**Key trick**: Iterate **backwards** when updating the DP table to avoid re‑using the same string multiple times.

---

### 4️⃣ Algorithm (Bottom‑Up DP)

```text
dp[z][o] = maximum subset size using at most z zeros and o ones

for each string s in strs:
    z0 = count of '0' in s
    o1 = count of '1' in s
    for z from m down to z0:
        for o from n down to o1:
            dp[z][o] = max(dp[z][o], dp[z - z0][o - o1] + 1)

return dp[m][n]
```

**Complexity**

- **Time:** `O(L * m * n)` where `L = strs.length`.  
- **Space:** `O(m * n)` (2‑D array).  

The backward loops ensure each string is used at most once, turning the 3‑D DP into a 2‑D DP.

---

### 5️⃣ Code (Java, Python, C++)

#### 5.1 Java

```java
// LeetCode 474 – Ones and Zeroes (Java)
public class Solution {
    public int findMaxForm(String[] strs, int m, int n) {
        int[][] dp = new int[m + 1][n + 1];

        for (String s : strs) {
            int[] cnt = countZerosOnes(s);
            int zeros = cnt[0];
            int ones  = cnt[1];

            // Backwards iteration ensures 0/1 behavior
            for (int z = m; z >= zeros; z--) {
                for (int o = n; o >= ones; o--) {
                    dp[z][o] = Math.max(dp[z][o], dp[z - zeros][o - ones] + 1);
                }
            }
        }
        return dp[m][n];
    }

    private int[] countZerosOnes(String s) {
        int[] c = new int[2];
        for (int i = 0; i < s.length(); i++) {
            c[s.charAt(i) - '0']++;   // '0' -> index 0, '1' -> index 1
        }
        return c;
    }
}
```

#### 5.2 Python

```python
# LeetCode 474 – Ones and Zeroes (Python 3)
class Solution:
    def findMaxForm(self, strs: List[str], m: int, n: int) -> int:
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for s in strs:
            zeros, ones = s.count('0'), s.count('1')
            for z in range(m, zeros - 1, -1):
                for o in range(n, ones - 1, -1):
                    dp[z][o] = max(dp[z][o], dp[z - zeros][o - ones] + 1)

        return dp[m][n]
```

#### 5.3 C++

```cpp
// LeetCode 474 – Ones and Zeroes (C++)
class Solution {
public:
    int findMaxForm(vector<string>& strs, int m, int n) {
        vector<vector<int>> dp(m + 1, vector<int>(n + 1, 0));

        for (const string &s : strs) {
            int zeros = 0, ones = 0;
            for (char c : s) {
                if (c == '0') zeros++; else ones++;
            }

            for (int z = m; z >= zeros; --z) {
                for (int o = n; o >= ones; --o) {
                    dp[z][o] = max(dp[z][o], dp[z - zeros][o - ones] + 1);
                }
            }
        }
        return dp[m][n];
    }
};
```

---

### 6️⃣ Edge Cases & Common Pitfalls

| Issue | What Went Wrong | Fix |
|-------|-----------------|-----|
| **Off‑by‑one** in DP indices | Using `z >= zeros` but accessing `dp[z - zeros]` may go negative | Loop from `m` down to `zeros`, inclusive |
| **Large input strings** | Counting zeros/ones via `s.count('0')` in Python is O(length) | Still necessary; total time bounded by constraints |
| **Integer overflow** | Not a problem in Java/Python; C++ uses `int` (fits 32‑bit) | Ensure capacities `m, n <= 100` → safe |
| **Empty string** | `count('0')` and `count('1')` are 0 → still valid | Code handles it naturally |
| **Time limit** | Nested loops `L*m*n` may hit 6 e7 operations at worst | Acceptable for 1 s limit in most judges; consider pruning if `m` or `n` small |

---

### 7️⃣ Interview‑Ready Tips

1. **Explain the 2‑D DP analogy**: “We’re packing a bag that can hold X zeros and Y ones.”
2. **Show back‑wards iteration**: “This ensures each string is used once.”
3. **Discuss complexity**: “O(Lmn) time, O(mn) space; with `m,n≤100` it’s fine.”
4. **Mention optimizations**: “If we pre‑compute zeros/ones counts, we avoid re‑scanning each string.”
5. **Highlight readability**: “Clear variable names (`zeros`, `ones`) and helper methods aid maintainability.”

---

### 8️⃣ Bonus – Top‑Down Memoization (Optional)

```java
// Java memoized version
public int findMaxFormMemo(String[] strs, int m, int n) {
    int[][] memo = new int[m+1][n+1];
    for (int[] row : memo) Arrays.fill(row, -1);
    return dfs(strs, 0, m, n, memo);
}

private int dfs(String[] strs, int idx, int zeros, int ones, int[][] memo) {
    if (idx == strs.length) return 0;
    if (memo[zeros][ones] != -1) return memo[zeros][ones];

    int z = strs[idx].chars().filter(c -> c == '0').count();
    int o = strs[idx].chars().filter(c -> c == '1').count();

    int exclude = dfs(strs, idx+1, zeros, ones, memo);
    int include = 0;
    if (zeros >= z && ones >= o)
        include = 1 + dfs(strs, idx+1, zeros - z, ones - o, memo);

    return memo[zeros][ones] = Math.max(exclude, include);
}
```

---

### 9️⃣ Final Take‑Away

- **Problem**: 2‑D 0/1 knapsack (zeros & ones) → classic DP.
- **Solution**: Bottom‑up DP with backward loops → `O(L*m*n)` time, `O(m*n)` space.
- **Why It Matters**: Demonstrates dynamic programming, space/time trade‑offs, and clean coding in Java/Python/C++.
- **Job‑Interview Impact**: Talk about knapsack intuition, complexity, edge cases, and language‑agnostic thinking to impress hiring managers.

> **SEO CTA:** *If you want to master LeetCode 474 and land your dream software engineering role, dive into this post, practice the code snippets, and share your own variations on GitHub. Follow for more DP insights, interview prep, and career‑growth tips!*

Happy coding and best of luck in your next interview! 🚀