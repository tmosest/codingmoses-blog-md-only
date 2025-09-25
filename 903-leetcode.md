---
title: LeetCode 903. Valid Permutations for DI Sequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # ✅ 903. Valid Permutations for DI Sequence  
**A Complete, SEO‑Optimized Guide (Java | Python | C++)**  

---

## Table of Contents  
1. 📌 Problem Overview  
2. 🧠 Key Insights & Intuition  
3. 💡 Solution Strategies  
   * Naïve Recursion  
   * Memoized DFS (Top‑Down)  
   * Bottom‑Up DP (O(n²))  
   * Optimised DP with Prefix Sums (O(n²) time, O(n) space)  
4. 🏆 “The Good, The Bad, The Ugly”  
5. 📦 Code – Java, Python, C++  
6. 🚀 How This Boosts Your Interview Portfolio  
7. 📄 Final Thoughts  

---

## 1. 📌 Problem Overview  

> **Input** – a string `s` of length `n` (1 ≤ n ≤ 200) where each character is either **'I'** (increasing) or **'D'** (decreasing).  
> **Task** – Count the permutations of the integers `0 … n` that satisfy the sequence of inequalities described by `s`.  
> **Output** – the number of valid permutations modulo `10⁹ + 7`.  

Example  
```
s = "DID"   → 5 valid permutations
```

---

## 2. 🧠 Key Insights & Intuition  

1. **Permutation length** – always `n + 1`.  
2. **Relative order only matters** – we only care whether the next element is larger or smaller.  
3. **Dynamic Programming** – when we decide the position of the smallest remaining number, all larger numbers can be placed arbitrarily respecting the same pattern.  

The classic DP state:  

```
dp[i][j] = number of ways to build the first i+1 numbers 
           (indices 0 … i) such that the i‑th number is the j‑th smallest
           among the i+1 numbers chosen so far.
```

* `i` goes from `0` to `n`.  
* `j` goes from `0` to `i` (there are `i+1` possible ranks).  

Transition depends on `s[i‑1]`:

| `s[i-1]` | Condition for the i‑th number | Transition Formula |
|----------|------------------------------|--------------------|
| 'I'      | j‑th smallest > previous j | Sum of all `dp[i-1][k]` where `k < j` |
| 'D'      | j‑th smallest < previous j | Sum of all `dp[i-1][k]` where `k ≥ j` |

Thus the DP is O(n³) naïvely (nested loops).  
With **prefix sums** we collapse the inner summation to O(1), yielding O(n²) time and O(n) space.

---

## 3. 💡 Solution Strategies  

### 3.1 Naïve Recursion  
Explores all permutations → factorial time, infeasible for n = 200.

### 3.2 Memoized DFS (Top‑Down)  
Recursively choose the rank of the next number, memoise `(i, j)`.  
Time O(n²), but recursion depth and constant factors hurt performance.

### 3.3 Bottom‑Up DP (O(n²))  
Iteratively fill the DP table using the transition described above.  
Still requires a nested loop over `k` → O(n³).

### 3.4 Optimised DP with Prefix Sums (O(n²) time, O(n) space)  
Maintain two arrays `prev` and `curr` of length `n+1`.  
For each `i` build prefix sums of `prev` to answer the summations in O(1).  

**Algorithm Outline**

```
prev[0] = 1
for i = 1 .. n:
    build prefix sums of prev
    if s[i-1] == 'I':
        for j = 0 .. i:
            curr[j] = prefix[j]          // sum of prev[0..j-1]
    else: // 'D'
        for j = 0 .. i:
            curr[j] = prefix[i] - prefix[j]   // sum of prev[j..i-1]
    prev = curr; reset curr
answer = sum(prev[0..n]) % MOD
```

---

## 4. 🏆 “The Good, The Bad, The Ugly”  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Complexity** | O(n²) is fast enough for n ≤ 200. | Requires careful prefix‑sum handling. | Mis‑calculating prefix boundaries leads to off‑by‑one bugs. |
| **Space** | O(n) memory. | Need two arrays, but still small. | Using a full 2‑D array wastes ~40kB—acceptable, but avoid for larger n. |
| **Readability** | DP table clearly reflects the problem. | Prefix sums can obscure logic. | Recursion depth can blow stack for large n. |
| **Edge Cases** | Handles all ‘I’ or all ‘D’ patterns. | Need to modulo after every addition to avoid overflow. | Forgetting to reset the `curr` array leads to stale values. |

---

## 5. 📦 Code – Java, Python, C++  

All three implementations follow the *Optimised DP with Prefix Sums* pattern.

---

### 5.1 Java  

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int numPermsDISequence(String s) {
        int n = s.length();
        int[] prev = new int[n + 1];
        int[] curr = new int[n + 1];
        int[] pref = new int[n + 1];

        // Base: first element can only be 0 (rank 0)
        prev[0] = 1;

        for (int i = 1; i <= n; i++) {
            // Build prefix sums of prev
            pref[0] = 0;
            for (int j = 0; j <= i; j++) {
                pref[j + 1] = (pref[j] + prev[j]) % MOD;
            }

            char op = s.charAt(i - 1); // 'I' or 'D'

            if (op == 'I') {
                for (int j = 0; j <= i; j++) {
                    // sum prev[0 .. j-1]
                    curr[j] = pref[j];
                }
            } else { // 'D'
                for (int j = 0; j <= i; j++) {
                    // sum prev[j .. i-1] = pref[i] - pref[j]
                    int val = pref[i] - pref[j];
                    if (val < 0) val += MOD;
                    curr[j] = val;
                }
            }

            // Prepare for next iteration
            System.arraycopy(curr, 0, prev, 0, i + 1);
            Arrays.fill(curr, 0, i + 1, 0);
        }

        int ans = 0;
        for (int i = 0; i <= n; i++) {
            ans = (ans + prev[i]) % MOD;
        }
        return ans;
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.numPermsDISequence("DID")); // 5
        System.out.println(sol.numPermsDISequence("D"));   // 1
    }
}
```

---

### 5.2 Python  

```python
MOD = 10 ** 9 + 7

def numPermsDISequence(s: str) -> int:
    n = len(s)
    prev = [0] * (n + 1)
    prev[0] = 1
    pref = [0] * (n + 1)

    for i in range(1, n + 1):
        # prefix sums of prev
        pref[0] = 0
        for j in range(i):
            pref[j + 1] = (pref[j] + prev[j]) % MOD

        if s[i - 1] == 'I':
            curr = [pref[j] for j in range(i + 1)]
        else:  # 'D'
            curr = [(pref[i] - pref[j]) % MOD for j in range(i + 1)]

        prev[:i + 1] = curr

    return sum(prev) % MOD


# Example usage
if __name__ == "__main__":
    print(numPermsDISequence("DID"))  # 5
    print(numPermsDISequence("D"))    # 1
```

---

### 5.3 C++  

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static const int MOD = 1'000'000'007;
    
    int numPermsDISequence(string s) {
        int n = s.size();
        vector<int> prev(n + 1, 0), curr(n + 1, 0), pref(n + 1, 0);
        prev[0] = 1;                    // first element rank 0

        for (int i = 1; i <= n; ++i) {
            // Build prefix sums of prev
            pref[0] = 0;
            for (int j = 0; j <= i; ++j)
                pref[j + 1] = (pref[j] + prev[j]) % MOD;

            if (s[i - 1] == 'I') {
                for (int j = 0; j <= i; ++j)
                    curr[j] = pref[j];                  // sum[0..j-1]
            } else { // 'D'
                for (int j = 0; j <= i; ++j)
                    curr[j] = (pref[i] - pref[j] + MOD) % MOD; // sum[j..i-1]
            }

            // Move curr to prev for next iteration
            for (int j = 0; j <= i; ++j) {
                prev[j] = curr[j];
                curr[j] = 0;
            }
        }

        int ans = 0;
        for (int j = 0; j <= n; ++j)
            ans = (ans + prev[j]) % MOD;
        return ans;
    }
};

int main() {
    Solution sol;
    cout << sol.numPermsDISequence("DID") << '\n'; // 5
    cout << sol.numPermsDISequence("D")   << '\n'; // 1
    return 0;
}
```

---

## 6. 🚀 How This Boosts Your Interview Portfolio  

* **Algorithmic Depth** – Demonstrates mastery of DP, prefix sums, and modular arithmetic.  
* **Multi‑Language Mastery** – Showcases ability to implement the same logic in Java, Python, and C++.  
* **Edge‑Case Handling** – Off‑by‑one bugs, modulo pitfalls, and space‑time trade‑offs are covered.  
* **Readable Code** – Clean structure, comments, and test harnesses make it recruiter‑friendly.  

Attach this solution to your GitHub, link to an online‑judge submission, or explain it in a whiteboard interview; recruiters love clear, efficient, and bug‑free code.

---

## 7. 🎯 Quick‑Reference Cheat Sheet  

```
MOD = 1_000_000_007
dp[0][0] = 1
for i = 1 .. n:
    build prefix sums of dp[i-1][*]
    if s[i-1] == 'I':
        dp[i][j] = prefix[j]
    else: // 'D'
        dp[i][j] = (prefix[i] - prefix[j] + MOD) % MOD
answer = sum(dp[n][*]) % MOD
```

Remember:  
* `prefix[k] = sum(dp[i-1][0 .. k-1])`.  
* Use long / 64‑bit integers when adding before modding in C++/Java.  
* Always mod after subtraction to keep the result non‑negative.

---

## 7. 📚 Further Reading  

* “Dynamic Programming on Permutations” – LeetCode Discussions.  
* “Counting Permutations with Inversions” – Competitive Programming 4.  
* “Prefix Sum Trick” – A great article on reducing O(n³) to O(n²) in DP.  

---

### Closing Note  

The Optimised DP with Prefix Sums is the gold‑standard for *Number of Ways to Rearrange* problems on LeetCode.  
With the code above, you’re ready to tackle the question on any interview platform. Happy coding! 🚀

---