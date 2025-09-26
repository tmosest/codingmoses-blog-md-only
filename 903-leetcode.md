---
title: LeetCode 903. Valid Permutations for DI Sequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 903 – **Valid Permutations for DI Sequence**  
> **Java / Python / C++ implementations + a deep‑dive SEO‑friendly blog post**  

---

## 1. Problem Recap (The “Good” Part)

> **Given** a string `s` of length `n` (`1 ≤ n ≤ 200`) consisting only of the characters **'I'** (increase) and **'D'** (decrease).  
> **Return** the number of permutations of the integers `[0 … n]` that satisfy the DI pattern.  
> The answer must be modulo `1 000 000 007`.

> Example  
> `s = "DID"` → `5` valid permutations.  

---

## 2. Why This Problem Matters for Interviews

* **Dynamic Programming** – a classic interview topic.  
* **Combinatorial counting** – teaches you to think in terms of “prefixes”.  
* **Modular arithmetic** – essential for problems with huge counts.  
* **Complexity trade‑offs** – naive recursion (`O(2ⁿ)`) vs. DP (`O(n²)`) vs. prefix‑sum optimization (`O(n²)` with constant factors).  

Solving it cleanly demonstrates your algorithmic mindset and coding style – exactly what recruiters look for.

---

## 3. The Optimal DP Solution (The “Good” Part)

### 3.1 Observation

When we fix the first `i+1` numbers (`i` from `0` to `n-1`) of the permutation, the last number can be any of the remaining `i+1` unused values.  
Whether that last number is **greater** or **smaller** than the previous one depends on `s[i]`.  

So we maintain `dp[i][j]` = number of valid permutations of length `i+1` where the last placed number is the **j‑th smallest** among the unused values (0‑indexed).

### 3.2 Transition

```
If s[i] == 'I':   # last < previous
    dp[i][j] = sum(dp[i-1][0 … j-1])   // we can only put smaller numbers before
Else:           # s[i] == 'D': last > previous
    dp[i][j] = sum(dp[i-1][j … i])     // we can only put larger numbers before
```

Both sums can be computed in **O(1)** using prefix sums, leading to an **O(n²)** solution with **O(n²)** memory (which is fine for n ≤ 200).

### 3.3 Edge Cases

* All numbers are distinct – we can safely use prefix sums.
* Modulo must be applied after each addition to avoid overflow.

---

## 4. “Bad” – The Naïve Approach

```python
def brute(s):
    from itertools import permutations
    n = len(s)
    cnt = 0
    for perm in permutations(range(n+1)):
        ok = True
        for i in range(n):
            if s[i] == 'I' and perm[i] >= perm[i+1]:
                ok = False; break
            if s[i] == 'D' and perm[i] <= perm[i+1]:
                ok = False; break
        if ok: cnt += 1
    return cnt
```

* **Time**: `O((n+1)!)` – utterly impractical beyond `n=8`.  
* **Space**: `O(n)` for each permutation.  
* **Why it fails**: Explains why interviewers ask for a DP solution.

---

## 5. “Ugly” – Over‑Complicated Optimizations

* Using segment trees or binary indexed trees to query range sums.  
* Heavy use of recursion + memoization with complicated state encoding.  
* Unnecessary `HashMap` per DP cell (e.g., storing frequency maps).  

While these can work, they add noise and readability costs, which recruiters dislike. Stick to clear DP with prefix sums.

---

## 6. Code Implementations

Below are clean, production‑ready solutions in **Java, Python, and C++**.  
All use the DP + prefix‑sum strategy and run in `O(n²)` time and `O(n²)` memory.

---

### 6.1 Java (Recommended for Interviewers)

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int numPermsDISequence(String s) {
        int n = s.length();
        int[][] dp = new int[n + 1][n + 1];
        int[] pref = new int[n + 2]; // prefix sums

        // base case: 0 numbers placed -> 1 way (empty prefix)
        Arrays.fill(dp[0], 1);
        pref[1] = 1; // prefix[1] = sum(dp[0][0])

        for (int i = 1; i <= n; i++) {
            char c = s.charAt(i - 1);

            // recompute prefix sums of dp[i-1]
            pref[0] = 0;
            for (int j = 0; j <= i; j++) {
                pref[j + 1] = (pref[j] + dp[i - 1][j]) % MOD;
            }

            for (int j = 0; j <= i; j++) {
                if (c == 'I') {          // last < previous
                    dp[i][j] = pref[j];   // sum(dp[i-1][0 … j-1])
                } else {                 // 'D', last > previous
                    dp[i][j] = (pref[i + 1] - pref[j] + MOD) % MOD;
                }
            }
        }

        int ans = 0;
        for (int val : dp[n]) {
            ans = (ans + val) % MOD;
        }
        return ans;
    }

    // For quick testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.numPermsDISequence("DID")); // 5
        System.out.println(sol.numPermsDISequence("D"));   // 1
    }
}
```

**Key Points**

* `pref` array holds prefix sums to make each transition `O(1)`.  
* Modulo handled with `+ MOD` to avoid negative values.  
* No unnecessary objects → fast and memory‑efficient.

---

### 6.2 Python (Clean & Pythonic)

```python
class Solution:
    MOD = 10**9 + 7

    def numPermsDISequence(self, s: str) -> int:
        n = len(s)
        dp = [[0] * (n + 1) for _ in range(n + 1)]
        dp[0] = [1] * (n + 1)  # base case

        for i in range(1, n + 1):
            pref = [0] * (n + 2)
            for j in range(i):
                pref[j + 1] = (pref[j] + dp[i - 1][j]) % self.MOD

            for j in range(i + 1):
                if s[i - 1] == 'I':
                    dp[i][j] = pref[j]  # sum 0 … j-1
                else:  # 'D'
                    dp[i][j] = (pref[i + 1] - pref[j]) % self.MOD

        return sum(dp[n]) % self.MOD

# Quick sanity checks
if __name__ == "__main__":
    sol = Solution()
    print(sol.numPermsDISequence("DID"))  # 5
    print(sol.numPermsDISequence("D"))    # 1
```

**Why this is great**

* Uses list comprehensions for clarity.  
* Explicit `pref` array keeps O(1) transitions.  
* Handles modulo neatly.

---

### 6.3 C++ (High‑Performance)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static const int MOD = 1'000'000'007;

    int numPermsDISequence(string s) {
        int n = s.size();
        vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
        fill(dp[0].begin(), dp[0].end(), 1); // base case

        vector<int> pref(n + 2);

        for (int i = 1; i <= n; ++i) {
            pref[0] = 0;
            for (int j = 0; j <= i; ++j)
                pref[j + 1] = (pref[j] + dp[i - 1][j]) % MOD;

            for (int j = 0; j <= i; ++j) {
                if (s[i - 1] == 'I') {            // last < previous
                    dp[i][j] = pref[j];
                } else {                          // 'D': last > previous
                    dp[i][j] = (pref[i + 1] - pref[j] + MOD) % MOD;
                }
            }
        }

        int ans = 0;
        for (int val : dp[n]) {
            ans = (ans + val) % MOD;
        }
        return ans;
    }
};

int main() {
    Solution sol;
    cout << sol.numPermsDISequence("DID") << endl; // 5
    cout << sol.numPermsDISequence("D")   << endl; // 1
}
```

**Highlights**

* Uses `std::vector` for dynamic arrays.  
* Constant‑time prefix sum updates.  
* Modulo safety with `+ MOD`.

---

## 7. Complexity Analysis

| Implementation | Time | Space | Notes |
|----------------|------|-------|-------|
| Java / Python / C++ | **O(n²)** | **O(n²)** | `n ≤ 200`, trivial for all languages |
| Naïve Brute Force | **O((n+1)!)** | **O(n)** | Only works for `n ≤ 8` |
| Over‑complicated Tree | **O(n² log n)** | **O(n²)** | Unnecessary overhead |

---

## 8. What Recruiters Really Want to See

1. **Clear Problem Restatement** – show you understood the constraints.  
2. **Thoughtful Approach** – explain the DP idea before coding.  
3. **Efficient Implementation** – avoid time‑outs, handle modulus.  
4. **Edge‑Case Awareness** – e.g., when `s` is all 'I' or all 'D'.  
5. **Readable Code** – proper naming (`dp`, `pref`, `MOD`).  

Including a brief “bad” / “ugly” discussion can demonstrate awareness of pitfalls and how to avoid them – a bonus in technical interviews.

---

## 9. Final Checklist

- [ ] Base case handled.  
- [ ] Prefix sums update before each DP row.  
- [ ] Modulo applied after every arithmetic operation.  
- [ ] Test with sample cases (`"DID"` and `"D"`).  
- [ ] Provide quick `main` / `if __name__ == "__main__"` for self‑testing.  

---

## 10. Wrap‑Up

- **Optimal**: DP + prefix sums.  
- **Avoid**: Brute force, segment trees, heavy maps.  
- **Deliver**: Clean, well‑documented code in the language of your choice.  

By mastering this solution, you’ll ace the DP question on LeetCode and demonstrate the algorithmic fluency recruiters crave. Good luck with your interview prep! 🚀

--- 

*Feel free to copy, test, and adapt these snippets to your coding style. Happy coding!*