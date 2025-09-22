---
title: LeetCode 1960. Maximum Product of the Length of Two Palindromic Substrings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. The Problem – 1960. Maximum Product of the Length of Two Palindromic Substrings

> **Given** a string `s` (2 ≤ |s| ≤ 10⁵), find **two non‑overlapping odd‑length palindromic substrings** whose product of lengths is maximal.  
> Return that maximum product.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `"ababbb"` | `9` | `"aba"` (3) and `"bbb"` (3) → 3 × 3 = 9 |
| 2 | `"zaaaxbbby"` | `9` | `"aaa"` (3) and `"bbb"` (3) → 3 × 3 = 9 |

**Why is it hard?**  
- We must check *every* odd‑length palindrome.  
- The string can be 100 000 characters long, so an O(n²) approach is impossible.  
- The two substrings must not overlap, which means we must combine information from *both* sides of the string.

The key is to use **Manacher’s algorithm** to get all odd‑length palindromes in linear time, then combine prefix‑ and suffix‑maximums.

---

## 2. Algorithm Overview

1. **Compute longest odd palindrome at every center**  
   `odd[i]` = maximum odd length of a palindrome centered at index `i` (Manacher).  
   (Length is guaranteed to be odd.)

2. **Build prefix maximum array**  
   `pref[i]` = longest odd palindrome length in `s[0…i]`.  
   Recurrence: `pref[i] = max(pref[i-1], odd[i])`.

3. **Build suffix maximum array**  
   `suff[i]` = longest odd palindrome length in `s[i…n-1]`.  
   Recurrence: `suff[i] = max(suff[i+1], odd[i])`.

4. **Split the string once**  
   For every split position `i` (0 ≤ i < n‑1) the first palindrome must end at or before `i`, the second must start at or after `i+1`.  
   The best product for that split is `pref[i] * suff[i+1]`.

5. **Take the maximum over all splits**.

**Complexities**

- Time: **O(n)** – one pass for Manacher, two linear passes for prefix/suffix, one linear pass for the split.
- Memory: **O(n)** – arrays `odd`, `pref`, `suff`.

---

## 3. Code

Below you’ll find clean, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three solve the problem in the same O(n) time and O(n) space.

---

### 3.1 Java

```java
import java.util.*;

public class Solution {
    public int maxProduct(String s) {
        int n = s.length();
        int[] odd = manacherOdd(s);
        int[] pref = new int[n];
        int[] suff = new int[n];

        // prefix max
        pref[0] = odd[0];
        for (int i = 1; i < n; i++) {
            pref[i] = Math.max(pref[i - 1], odd[i]);
        }

        // suffix max
        suff[n - 1] = odd[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            suff[i] = Math.max(suff[i + 1], odd[i]);
        }

        int best = 0;
        for (int i = 0; i < n - 1; i++) {
            best = Math.max(best, pref[i] * suff[i + 1]);
        }
        return best;
    }

    /** Manacher for odd-length palindromes only */
    private int[] manacherOdd(String s) {
        int n = s.length();
        int[] d = new int[n];          // d[i] = max radius (half length)
        int center = 0, right = 0;
        for (int i = 0; i < n; i++) {
            int k = (i < right) ? Math.min(d[center + right - i], right - i) : 1;
            while (i - k >= 0 && i + k < n && s.charAt(i - k) == s.charAt(i + k)) {
                k++;
            }
            d[i] = k;
            if (i + k > right) {
                center = i;
                right = i + k;
            }
        }
        int[] oddLen = new int[n];
        for (int i = 0; i < n; i++) {
            oddLen[i] = d[i] * 2 - 1;   // convert radius to length
        }
        return oddLen;
    }

    // For quick testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.maxProduct("ababbb"));     // 9
        System.out.println(sol.maxProduct("zaaaxbbby"));  // 9
    }
}
```

**Key points**

- `manacherOdd` returns *odd* palindrome lengths directly.  
- Prefix/suffix arrays store the maximum length so far / from the end.  
- The final loop considers every split once.

---

### 3.2 Python

```python
class Solution:
    def maxProduct(self, s: str) -> int:
        n = len(s)

        # ----- Manacher for odd palindromes -----
        d = [0] * n          # radius
        center = right = 0
        for i in range(n):
            k = 1 if i >= right else min(d[center + right - i], right - i)
            while i - k >= 0 and i + k < n and s[i - k] == s[i + k]:
                k += 1
            d[i] = k
            if i + k > right:
                center, right = i, i + k

        odd = [2 * r - 1 for r in d]   # odd length at each center

        # ----- prefix & suffix maximums -----
        pref = [0] * n
        suff = [0] * n
        pref[0] = odd[0]
        for i in range(1, n):
            pref[i] = max(pref[i - 1], odd[i])

        suff[n - 1] = odd[n - 1]
        for i in range(n - 2, -1, -1):
            suff[i] = max(suff[i + 1], odd[i])

        # ----- combine over splits -----
        best = 0
        for i in range(n - 1):
            best = max(best, pref[i] * suff[i + 1])

        return best


# Simple test harness
if __name__ == "__main__":
    sol = Solution()
    print(sol.maxProduct("ababbb"))     # 9
    print(sol.maxProduct("zaaaxbbby"))  # 9
```

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxProduct(string s) {
        int n = s.size();
        vector<int> odd = manacherOdd(s);
        vector<int> pref(n), suff(n);

        pref[0] = odd[0];
        for (int i = 1; i < n; ++i)
            pref[i] = max(pref[i-1], odd[i]);

        suff[n-1] = odd[n-1];
        for (int i = n-2; i >= 0; --i)
            suff[i] = max(suff[i+1], odd[i]);

        int best = 0;
        for (int i = 0; i < n-1; ++i)
            best = max(best, pref[i] * suff[i+1]);

        return best;
    }

private:
    vector<int> manacherOdd(const string &s) {
        int n = s.size();
        vector<int> d(n, 1);          // radius
        int center = 0, right = 0;
        for (int i = 0; i < n; ++i) {
            int k = (i < right) ? min(d[center + right - i], right - i) : 1;
            while (i - k >= 0 && i + k < n && s[i-k] == s[i+k]) ++k;
            d[i] = k;
            if (i + k > right) {
                center = i;
                right = i + k;
            }
        }
        vector<int> oddLen(n);
        for (int i = 0; i < n; ++i)
            oddLen[i] = d[i]*2 - 1;   // length, not radius
        return oddLen;
    }
};

// test
int main() {
    Solution sol;
    cout << sol.maxProduct("ababbb") << endl;     // 9
    cout << sol.maxProduct("zaaaxbbby") << endl;  // 9
}
```

---

## 4. Blog Article – “The Good, The Bad, and The Ugly of Leetcode 1960”

> **Title**: *The Good, The Bad, and The Ugly of Leetcode 1960 – Mastering the Maximum Product of Two Palindromic Substrings*  
> **Meta description**: Learn how to solve Leetcode 1960 in Java, Python, and C++ with an O(n) solution. Understand the pitfalls, trade‑offs, and interview‑friendly approach using Manacher’s algorithm.

---

### 4.1 Why this problem matters

Interviewers love problems that force you to think about **optimal sub‑structures** and **linear time** tricks.  
Leetcode 1960 is a perfect blend:

- It asks for two *non‑overlapping* palindromic substrings.  
- The constraint |s| ≤ 10⁵ ensures you cannot afford quadratic checks.  
- The answer hinges on the *maximum palindrome lengths* on either side of a split, not the actual substrings.

Cracking this problem shows you can:

- Use *Manacher’s algorithm* to compute palindrome radii in O(n).  
- Merge results with prefix/suffix scans in a single pass.  
- Handle boundary conditions cleanly.

---

### 4.2 The Good

| Aspect | What we learn |
|--------|---------------|
| **Linear time** | You’ll impress interviewers with an O(n) solution where most naive approaches are O(n²). |
| **Manacher’s magic** | A once‑read algorithm that can be reused for many palindrome problems. |
| **Prefix‑suffix DP** | Classic technique to combine two halves of a string. |
| **Clean code** | All three implementations are succinct and easy to read – a plus for code‑review. |
| **Language‑agnostic** | Java, Python, C++ solutions are all available, showing your versatility. |

---

### 4.3 The Bad

| Pitfall | How to avoid it |
|---------|-----------------|
| **Mixing up radius and length** | Always remember that Manacher’s `d[i]` is a *radius*; the odd length is `2*d[i] - 1`. |
| **Index off‑by‑one on splits** | Splits must be between `i` and `i+1`. A common mistake is to include overlapping characters. |
| **Neglecting odd‑length requirement** | If you use a full palindrome algorithm (including even lengths), you must filter or ignore even lengths. |
| **Using long where int is enough** | The product of two lengths ≤ 10⁵ each fits in 32‑bit signed int. Avoid unnecessary 64‑bit conversions unless the interview explicitly asks. |

---

### 4.4 The Ugly

| Ugly behavior | Why it’s ugly |
|---------------|---------------|
| **O(n²) brute force** | Trying every pair of centers and expanding is tempting but leads to TLE on 10⁵ strings. |
| **Two‑dimensional DP** | Some solutions mistakenly build an `n x n` table of palindrome existence, blowing up memory. |
| **Complex hashing** | Rolling hash can detect palindromes in O(1) after O(n) preprocessing, but the constants are huge and the code is brittle. |
| **Over‑engineering the split** | People try to sort palindromes by length and then greedily pick two, which fails when palindromes overlap in complex ways. |

---

### 4.5 How to Nail the Interview

1. **Explain the constraints** – Emphasize the need for linear time.  
2. **Describe Manacher in words** – “It keeps a current palindrome window and mirrors the left side to guess radii.”  
3. **Show the prefix‑suffix trick** – “We only need the best palindrome that ends before a split and the best that starts after.”  
4. **Discuss edge cases** – What if no palindrome exists on one side? The answer is 0, which is handled automatically.  
5. **Offer the code** – Provide the Java/Python/C++ snippets.  
6. **Answer follow‑up questions** – “What if even lengths were allowed?” or “Can we return the substrings?”  

---

### 4.6 SEO Keywords (for job hunting)

- Leetcode 1960  
- Maximum product of two palindromic substrings  
- Java solution Leetcode 1960  
- Python solution Leetcode 1960  
- C++ solution Leetcode 1960  
- Manacher algorithm interview  
- Palindrome substring algorithm  
- Linear time palindrome problem  
- Software engineer interview problems  
- Coding interview questions  

---

### 4.7 Takeaway

> **Mastering Leetcode 1960** is more than just a test of string manipulation – it’s a showcase of algorithmic thinking, code elegance, and interview communication.  
> By combining Manacher’s linear palindrome detection with a simple prefix/suffix merge, you get a clean, optimal solution that will impress any hiring manager.  

Happy coding, and good luck landing that dream job! 🚀