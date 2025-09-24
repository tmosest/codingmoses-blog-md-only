---
title: LeetCode 3144. Minimum Substring Partition of Equal Character Frequency - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3144 – Minimum Substring Partition of Equal Character Frequency  
> **LeetCode | Dynamic Programming | O(n²·26) | 100‑point solution**  

---

## TL;DR
Given a lowercase string `s`, split it into the fewest possible substrings such that *each substring is “balanced”* – every character in the substring appears the same number of times.  
We solve the problem with a classic DP that runs in **O(n²·26)** time and **O(n)** space.  

| Language | Time | Space |
|----------|------|-------|
| **C++**  |  O(n²·26) |  O(n) |
| **Java** |  O(n²·26) |  O(n) |
| **Python** | O(n²·26) |  O(n) |

---

## 1.  Problem Restatement  

> **Minimum Substring Partition of Equal Character Frequency**  
>  
> **Input:** `s` – a string of length `1 … 1000` containing only lowercase letters.  
> **Output:** the minimum number of substrings in a partition of `s` where each substring is *balanced*.  
> A substring is **balanced** if all characters that appear in it occur the same number of times.  

Example  
```
s = "ababcc"
valid partitions: ("abab","c","c") , ("ab","abc","c") , ("ababcc")
invalid partitions: ("a","bab","cc") , ("aba","bc","c") , ("ab","abcc")
```

---

## 2.  Intuition & Strategy  

* We consider the string from left to right.  
* Let `dp[i]` be the minimal number of balanced substrings needed to cover the prefix `s[0 … i]`.  
* For each `i` we look backwards and try to end a balanced substring at `i`.  
  * While moving the start pointer `j` from `i` down to `0` we maintain the frequency of each character in `s[j … i]`.  
  * If the current window is balanced, we can finish the partition at `i` as `dp[j-1] + 1`.  
* The answer is `dp[n-1]`.  

The sliding window that checks *balancedness* is **O(26)** per step (only 26 letters).  
Thus the overall complexity is **O(n²·26)**.

---

## 3.  Correctness Proof  

We prove that the DP described above yields the optimal partition.

### Lemma 1  
For any index `i`, `dp[i]` equals the minimum number of balanced substrings needed to cover the prefix `s[0…i]`.

*Proof.*  
We show by induction over `i`.  

*Base:* `i = 0`.  
The only substring is `s[0]`.  
If it is balanced (always true because one character occurs once) `dp[0] = 1`.  
Otherwise no partition exists, but the problem guarantees at least one partition exists, so the lemma holds.

*Induction Step:*  
Assume the lemma holds for all indices `< i`.  
When computing `dp[i]` we examine every possible start `j` of the last substring: `j ∈ [0, i]`.  
If `s[j…i]` is balanced, then the rest of the string `s[0…j-1]` can be optimally partitioned into `dp[j-1]` substrings by the induction hypothesis.  
Hence `dp[j-1] + 1` is a feasible partition size for prefix `i`.  
Taking the minimum over all feasible `j` gives the optimal size for `i`.  
Thus `dp[i]` is optimal. ∎



### Lemma 2  
The procedure that maintains the character frequencies while scanning backwards correctly identifies whether `s[j…i]` is balanced.

*Proof.*  
We keep an array `freq[26]` where `freq[c]` counts occurrences of letter `c` in the current window.  
After adding `s[j]`, we recompute `minFreq` and `maxFreq` among all `freq[c] > 0`.  
`minFreq == maxFreq` iff every non‑zero frequency equals the same value, which is precisely the definition of a balanced substring. ∎



### Theorem  
The algorithm returns the minimal possible number of balanced substrings covering the entire string `s`.

*Proof.*  
By Lemma&nbsp;1, `dp[n-1]` is optimal for the whole string.  
The algorithm outputs `dp[n-1]`.  
Therefore the algorithm is correct. ∎



---

## 4.  Complexity Analysis  

* **Time:**  
  * Outer loop over `i` (`n` iterations).  
  * Inner loop over `j` (`≤ n` iterations).  
  * For each `j` we update frequencies in **O(1)** and check balancedness in **O(26)**.  
  → `O(n²·26)` ≈ `O(n²)` for `n ≤ 1000`.

* **Space:**  
  * `dp` array: `O(n)`.  
  * Frequency array: `O(26)`.  
  → `O(n)` total.



---

## 5.  Reference Implementations  

Below are clean, commented, and ready‑to‑paste solutions in **C++**, **Java**, and **Python**.  
All three follow the same DP strategy.

---

### 5.1  C++ (GNU C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Helper to test if a window is balanced
    static bool isBalanced(const array<int, 26>& freq) {
        int mn = INT_MAX, mx = 0;
        for (int f : freq) {
            if (f == 0) continue;
            mn = min(mn, f);
            mx = max(mx, f);
        }
        return mn == mx;            // mn==mx iff all non‑zero frequencies equal
    }

    int minimumSubstringsInPartition(string s) {
        int n = s.size();
        vector<int> dp(n, n);               // dp[i] = minimal substrings for prefix [0,i]
        for (int end = 0; end < n; ++end) {
            array<int, 26> freq{};          // zero‑initialized
            for (int start = end; start >= 0; --start) {
                freq[s[start] - 'a']++;      // extend window to the left
                if (isBalanced(freq)) {     // window [start,end] is balanced
                    int candidate = (start == 0) ? 1 : dp[start - 1] + 1;
                    dp[end] = min(dp[end], candidate);
                }
            }
        }
        return dp.back();
    }
};
```

---

### 5.2  Java (Java 17)

```java
import java.util.*;

public class Solution {
    // Check if the current frequency array represents a balanced substring
    private static boolean isBalanced(int[] freq) {
        int min = Integer.MAX_VALUE, max = 0;
        for (int f : freq) {
            if (f == 0) continue;
            min = Math.min(min, f);
            max = Math.max(max, f);
        }
        return min == max;
    }

    public int minimumSubstringsInPartition(String s) {
        int n = s.length();
        int[] dp = new int[n];
        Arrays.fill(dp, n);                   // large initial value

        for (int end = 0; end < n; end++) {
            int[] freq = new int[26];
            for (int start = end; start >= 0; start--) {
                freq[s.charAt(start) - 'a']++;
                if (isBalanced(freq)) {
                    int cand = (start == 0) ? 1 : dp[start - 1] + 1;
                    dp[end] = Math.min(dp[end], cand);
                }
            }
        }
        return dp[n - 1];
    }
}
```

---

### 5.3  Python 3 (3.11)

```python
from typing import List

class Solution:
    @staticmethod
    def is_balanced(freq: List[int]) -> bool:
        """Return True iff all non‑zero frequencies are equal."""
        mn, mx = None, None
        for f in freq:
            if f == 0:
                continue
            if mn is None or f < mn:
                mn = f
            if mx is None or f > mx:
                mx = f
        return mn == mx

    def minimumSubstringsInPartition(self, s: str) -> int:
        n = len(s)
        dp = [n] * n                      # dp[i] = min substrings for prefix [0,i]

        for end in range(n):
            freq = [0] * 26
            for start in range(end, -1, -1):
                freq[ord(s[start]) - 97] += 1
                if self.is_balanced(freq):
                    cand = 1 if start == 0 else dp[start - 1] + 1
                    dp[end] = min(dp[end], cand)

        return dp[-1]
```

---

## 6.  Blog Post – “The Good, the Bad, and the Ugly of Balanced‑Substring Partitioning”

### 6.1  Introduction  

If you’re a software‑engineering candidate eyeing top‑tech interviews, you’ve probably seen LeetCode **3144 – Minimum Substring Partition of Equal Character Frequency**.  
It’s a *medium‑difficulty* DP puzzle that’s a great showcase of problem‑solving skills.  
Let’s dissect why this problem is a job‑interview gold‑mine, explore the elegant DP that solves it, and talk about pitfalls that can trip you up.

> **Keywords**: LeetCode, interview questions, dynamic programming, balanced substrings, job interview, software engineer, C++, Java, Python, algorithmic thinking.

---

### 6.2  The Good – What Makes It a Great Interview Question

| Aspect | Why It’s Valuable |
|--------|-------------------|
| **Clear Constraints** | Length ≤ 1000, only lowercase letters – allows O(n²) solutions without worrying about time limits. |
| **DP‑Friendly** | The optimal‑partition property leads naturally to a one‑dimensional DP. |
| **Multiple Languages** | Solvable in C++, Java, or Python – demonstrates language versatility. |
| **Logical Decomposition** | You break it into “is this window balanced?” and “can we finish a partition here?” – good for testing communication. |
| **Edge‑Case Rich** | Single‑letter or all‑same‑letter strings are trivial, but mixed patterns (e.g., “ababcc”) push you to test balancedness correctly. |

Interviewers love problems where a **clean, provable DP** exists, because it lets you explain your reasoning step‑by‑step and receive immediate feedback on correctness.

---

### 6.3  The Bad – Common Traps and Misunderstandings

1. **Misreading “balanced”**  
   *You might think a substring is balanced if *some* characters are equal, not *all* that appear.*  
   The correct condition is **all non‑zero frequencies are identical**.

2. **Using Extra Data Structures**  
   *Many candidates build a `Map<Character, Integer>` inside the inner loop.*  
   While functional, this adds overhead; an array of size 26 is faster and easier to reason about.

3. **Forgetting the Prefix DP Transition**  
   The recurrence `dp[end] = min(dp[end], dp[start-1] + 1)` is essential.  
   Skipping the `dp[start-1]` part gives wrong answers for prefixes that already contain several balanced substrings.

4. **Boundary Off‑By‑One**  
   When `start == 0` there is no left prefix.  
   Forgetting to handle this correctly leads to negative indices or wrong results.

5. **Performance Underestimation**  
   You might think `O(n²·26)` is “fast”, but writing it incorrectly (e.g., recomputing frequencies from scratch in the inner loop) will push it toward `O(n³)` and time out.

---

### 6.4  The Ugly – What to Watch For in Your Own Code

* **Unnecessary Re‑Computation**  
  Some naïve solutions recompute the whole frequency array for each `(start, end)` pair.  
  The O(26) check is cheap, but rebuilding the array each time costs O(n) and breaks the time budget.

* **Wrong Balancedness Check**  
  A frequent bug is to compare `freq.min()` and `freq.max()` over *all* 26 letters, including zeros.  
  Since zeros lower `min`, you’ll incorrectly flag windows as unbalanced.  
  The key is to skip zero frequencies.

* **DP Array Initialization**  
  Setting `dp[i]` to `i+1` (worst case) is safe, but initializing it to `n` (string length) is more intuitive.  
  Failing to fill it properly can leave stale high values that never get updated.

* **Memory Leak in C++**  
  Forgetting `array<int, 26> freq{};` or using a raw `int freq[26]` that isn’t reset each `end` will contaminate later windows.

---

### 6.5  Walkthrough – The Clean DP You’ll Tell Your Interviewer  

> *“We’ll maintain a 1‑D DP for prefixes and slide a backward window, updating a 26‑size frequency array. When that window is balanced we update the DP entry.”*

1. **DP Definition**  
   `dp[i] = min number of balanced substrings for prefix up to i`.  
   *Why 1‑D?* Because we only need the optimal partition for the previous prefix – no 2‑D tables required.

2. **Backward Scan**  
   For each `end`, move `start` leftwards, updating `freq[s[start]]`.  
   Check balancedness in O(26).

3. **Balanced Test**  
   Use `min` and `max` of non‑zero frequencies.  
   `min == max` → balanced.

4. **Transition**  
   `candidate = dp[start-1] + 1` (or `1` if start is 0).  
   Keep the minimum over all feasible starts.

5. **Result**  
   `dp[n-1]` is the optimal answer.

---

### 6.6  Practice Tips

| Tip | How to Master |
|-----|---------------|
| **Implement All Three Languages** | Shows you can translate logic to C++, Java, and Python. |
| **Write Unit Tests** | Validate on trivial strings, all‑same strings, alternating patterns, and random strings. |
| **Explain While Coding** | Walk through `dp` updates, frequency maintenance, and balanced check – interviewers love verbal reasoning. |
| **Time Your Runs** | On LeetCode, run 5 random test cases and note the runtime – ensures your solution is efficient. |

---

### 6.7  Conclusion  

LeetCode 3144 is more than just a “medium” DP problem; it’s a **compact showcase of key interview skills**: clean constraint analysis, dynamic programming, careful edge‑case handling, and efficient implementation.  

By mastering the DP shown above and understanding the common pitfalls, you’ll be ready not just to answer the question, but to impress your interviewers with clear logic and robust code.  

**Happy coding, and may your job interviews be as balanced as the substrings you’ll partition!**

---  

### 6.8  Final Words  

If you’re preparing for a software‑engineering interview, include LeetCode **3144** in your practice list.  
The DP solution is straightforward enough to code in 30 minutes, but the reasoning behind it is deep enough to earn praise from senior interviewers.

Good luck, and remember: *balanced solutions are often the best!*

---

### 7.  Take‑away Checklist  

1. **DP on prefixes** – `dp[i] = min(dp[j-1] + 1)` over all balanced windows `[j … i]`.  
2. **O(26) balanced check** – compare min and max of non‑zero frequencies.  
3. **Watch out for zeros** – they must be ignored when determining balancedness.  
4. **Edge case handling** – `start == 0` → candidate = 1.  
5. **Language‑specific best practices** – zero‑initialization, bounds checking, and clear helper functions.  

Use the reference implementations above as your starting point, tweak them to fit your style, and practice explaining the algorithm out loud – that’s what makes you a standout candidate. 🚀

---



> **Author:** *Your Friendly Algorithm Mentor*  
> **Date:** *2024‑10‑15*  
> **Tags:** LeetCode, interview prep, dynamic programming, C++, Java, Python, balanced substrings, algorithmic interview questions.  

---



### 8.  Final Remarks  

Whether you’re coding in C++, Java, or Python, the DP solution above is a *canonical* way to tackle LeetCode 3144.  
Feel free to adapt it, add memoization, or experiment with sliding windows.  
Good luck, and may your balanced substrings always be perfectly balanced! 🚀



---



*Prepared for: Interview‑ready engineers.*  
*Feel free to drop this code into your repository or GitHub Gist and reference it in your portfolio.*



---



#



---



[End of Technical Note]