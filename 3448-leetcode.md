---
title: LeetCode 3448. Count Substrings Divisible By Last Digit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Cracking LeetCode 3448: Count Substrings Divisible by Last Digit  
*O(n) Solutions in Java, Python, and C++ – a Complete Interview‑Ready Guide*  

---

## TL;DR

- **Problem** – Count all substrings of a digit string that are divisible by their **non‑zero** last digit.  
- **Hardness** – `Hard` on LeetCode.  
- **Optimal Solution** – One pass (`O(n)`), using prefix remainders and a small frequency table for each digit’s divisibility rule.  
- **Languages** – Java, Python, C++.  
- **SEO keywords** – *LeetCode 3448*, *count substrings divisible by last digit*, *O(n) solution*, *Java, Python, C++*, *interview algorithm*, *dynamic programming*, *divisibility rules*.

---

## 1. Problem Overview

> **Input**: A string `s` (`1 ≤ |s| ≤ 10⁵`) containing only decimal digits.  
> **Output**: The number of substrings `s[i…j]` such that:
>  * `s[j] != '0'` (the last digit is non‑zero)
>  * the integer represented by the substring is divisible by the value of its last digit.

> **Example**  
> `s = "12936"` → `11` substrings satisfy the condition.

The key is that *leading zeros are allowed*, so `"01"` is a valid substring, but a substring ending in `'0'` is ignored.

---

## 2. Constraints & Edge Cases

| | | |
|---|---|---|
| `|s|` | up to 100 000 | must be linear |
| digits | `'0'`–`'9'` | digit `0` never forms a valid last digit |
| overflow | result can be up to 5 × 10⁹ | use 64‑bit (`long`/`long long`) |

---

## 3. Naïve Approaches & Their Pitfalls

| Approach | Complexity | Why it fails |
|---|---|---|
| Enumerate all substrings and check divisibility on the fly | `O(n²)` | Too slow for 10⁵ |
| Precompute integer values of all substrings | `O(n²)` memory | Impossible |
| Dynamic programming with state `(endIndex, remainder)` | `O(n·10·10)` | Still too large; we can do better |

Because each last digit has a *specific* divisibility rule (e.g. `4` depends only on the last two digits, `8` on the last three, `3` on the sum of digits), we can take advantage of this and avoid any quadratic behaviour.

---

## 4. Optimized O(n) Strategy

### 4.1  Divisibility Rules by Last Digit

| d | Rule | Implementation |
|---|------|----------------|
| **1, 2, 5** | Every number ending with these digits is divisible by `d` | Add `j+1` (all substrings ending at `j`) |
| **3, 6, 9** | A number is divisible by `3` (or `9`) iff the sum of its digits is.  | Keep prefix remainder modulo `3`/`9` and count matches. |
| **4** | Divisible iff the last **two** digits form a number divisible by 4 | Check `(10*prevDigit + d) % 4` |
| **8** | Divisible iff the last **three** digits (or last two for `j==1`) form a number divisible by 8 | Check `(100*d3 + 10*d2 + d) % 8` |
| **7** | Needs a bit more math:  `n % 7 == 0` ⇔ `n = 10⁰·a + 10¹·b + …` → we use prefix remainder modulo 7 and a small 6‑cycle table. | Pre‑compute inverses of `10^k mod 7` (`k=0..5`) and look up frequencies. |
| **0** | *Ignored* | Skip altogether |

### 4.2  Prefix Remainders

For digits that rely on *the entire substring* (`3,6,9,7`) we maintain the running remainder:

```text
prefix[i] = (value of s[0…i]) % 7
```

Using the remainder at the **end** of a substring is enough because for a substring `s[l…r]`

```
value(l,r) % d == 0   ⇔   prefix[r] % d == prefix[l-1] * (10^(r-l+1) mod d) % d
```

- For `3,6,9` the multiplier is always `1` (the remainder is independent of the substring length).
- For `7` the multiplier changes every 6 positions because `10^k mod 7` repeats with period 6.  

### 4.3  Frequency Tables

For each rule we maintain a tiny array of counts:

```text
freq3[0..2]  // counts of prefix remainders modulo 3
freq9[0..8]  // counts of prefix remainders modulo 9
freq7[0..5][0..6]  // for the 6‑cycle of 7
```

During the single scan we:

1. **Count** the current position’s contribution using the appropriate rule.
2. **Update** the corresponding frequency table for later positions.

Because we always use the *already processed* prefix values, the algorithm stays linear.

---

## 5. Implementations

> **Tip** – All three implementations use the same core idea.  
> The only differences are syntax, array handling, and built‑in big‑integer support.

### 5.1 Java (LeetCode‑style)

```java
import java.util.*;

public class Solution {
    // Pre‑computed inverses of 10^k mod 7 for k = 0..5
    private static final int[] INV7 = {1, 5, 2, 3, 6, 4};

    public long countSubstrings(String s) {
        int n = s.length();
        long[] pref3 = new long[n];
        long[] pref9 = new long[n];
        long[] pref7 = new long[n];
        long[] prefAll = new long[n];          // prefix mod 3 (same as pref3)
        long[] freq3 = new long[3];
        long[] freq9 = new long[9];
        long[][] freq7 = new long[6][7];       // 6‑cycle × 7 remainders

        long result = 0;
        for (int j = 0; j < n; ++j) {
            int d = s.charAt(j) - '0';
            if (d == 0) {           // skip substrings ending in 0
                continue;
            }

            // ---------- Rule for digits 1,2,5 ----------
            if (d == 1 || d == 2 || d == 5) {
                result += j + 1;   // all substrings end at j
            }

            // ---------- Rule for digits 3,6,9 ----------
            else if (d == 3 || d == 6) {
                long rem = pref3[j];
                result += freq3[rem] + (rem == 0 ? 1 : 0);
            } else if (d == 9) {
                long rem = pref9[j];
                result += freq9[rem] + (rem == 0 ? 1 : 0);
            }

            // ---------- Rule for digit 4 ----------
            else if (d == 4) {
                if (j == 0) {
                    result += 1;   // only the single digit
                } else {
                    int lastTwo = (s.charAt(j - 1) - '0') * 10 + d;
                    result += (lastTwo % 4 == 0 ? (j + 1) : 1);
                }
            }

            // ---------- Rule for digit 8 ----------
            else if (d == 8) {
                if (j == 0) {
                    result += 1;
                } else if (j == 1) {
                    int lastTwo = (s.charAt(0) - '0') * 10 + 8;
                    result += (lastTwo % 8 == 0 ? 2 : 1);
                } else {
                    int last3 = (s.charAt(j - 2) - '0') * 100
                              + (s.charAt(j - 1) - '0') * 10 + 8;
                    int last2 = (s.charAt(j - 1) - '0') * 10 + 8;
                    if (last3 % 8 == 0) result += j - 1;
                    if (last2 % 8 == 0) result += 1;
                    result += 1;     // the single‑digit substring
                }
            }

            // ---------- Rule for digit 7 ----------
            else if (d == 7) {
                long rem = pref7[j];
                result += (rem == 0 ? 1 : 0);
                // scan all 6 possible exponents k (0…5)
                for (int k = 0; k < 6; ++k) {
                    int idx = (j % 6 - k + 6) % 6;
                    int needed = (int)((rem * INV7[k]) % 7);
                    result += freq7[idx][needed];
                }
            }

            // ---------- Update prefix arrays ----------
            // Compute new prefix values for next iteration
            pref3[j] = (j == 0 ? d : (pref3[j - 1] * 10 + d) % 3);
            pref9[j] = (j == 0 ? d : (pref9[j - 1] * 10 + d) % 9);
            pref7[j] = (j == 0 ? d : (pref7[j - 1] * 10 + d) % 7);

            // Update frequencies for future positions
            freq3[pref3[j]]++;
            freq9[pref9[j]]++;
            freq7[j % 6][pref7[j]]++;
        }
        return result;
    }
}
```

> **Why this works** –  
> * Every `d` is handled in constant time.  
> * The frequency tables (`freq3`, `freq9`, `freq7`) contain only **few** entries, so memory usage is tiny.  
> * The single pass guarantees `O(n)` time.

---

### 5.2 Python

```python
class Solution:
    INV7 = [1, 5, 2, 3, 6, 4]  # (10^k)^-1 mod 7 for k = 0..5

    def countSubstrings(self, s: str) -> int:
        n = len(s)
        pref3 = [0] * n
        pref9 = [0] * n
        pref7 = [0] * n

        freq3 = [0] * 3
        freq9 = [0] * 9
        freq7 = [[0] * 7 for _ in range(6)]

        ans = 0
        for j in range(n):
            d = int(s[j])

            if d == 0:          # never a valid last digit
                continue

            # 1, 2, 5 – always divisible
            if d in (1, 2, 5):
                ans += j + 1

            # 3, 6, 9 – sum‑of‑digits rule
            elif d in (3, 6, 9):
                rem = pref3[j] if d in (3, 6) else pref9[j]
                if d in (3, 6):
                    ans += freq3[rem] + (1 if rem == 0 else 0)
                else:  # d == 9
                    ans += freq9[rem] + (1 if rem == 0 else 0)

            # 4 – last two digits
            elif d == 4:
                if j == 0:
                    ans += 1
                else:
                    last_two = int(s[j-1:])  # two digits ending at j
                    if last_two % 4 == 0:
                        ans += j + 1
                    else:
                        ans += 1

            # 8 – last three digits (or two for j == 1)
            elif d == 8:
                if j == 0:
                    ans += 1
                elif j == 1:
                    last_two = int(s[0:2])
                    ans += 2 if last_two % 8 == 0 else 1
                else:
                    last_three = int(s[j-2:j+1])
                    last_two = int(s[j-1:j+1])
                    if last_three % 8 == 0:
                        ans += j - 1
                    if last_two % 8 == 0:
                        ans += 1
                    ans += 1

            # 7 – need the 6‑cycle multiplier
            elif d == 7:
                rem = pref7[j]
                ans += 1 if rem == 0 else 0
                for k in range(6):
                    idx = (j % 6 - k + 6) % 6
                    needed = (rem * self.INV7[k]) % 7
                    ans += freq7[idx][needed]

            # compute new prefix remainders
            if j == 0:
                pref3[j] = d % 3
                pref9[j] = d % 9
                pref7[j] = d % 7
            else:
                pref3[j] = (pref3[j-1] * 10 + d) % 3
                pref9[j] = (pref9[j-1] * 10 + d) % 9
                pref7[j] = (pref7[j-1] * 10 + d) % 7

            # update frequencies
            freq3[pref3[j]] += 1
            freq9[pref9[j]] += 1
            freq7[j % 6][pref7[j]] += 1

        return ans
```

> **Python specifics** –  
> * Uses `int(s[a:b])` for quick extraction of last 2 or 3 digits.  
> * `INV7` is a global list, preventing re‑creation inside the loop.

---

### 5.3 C++ (GCC 17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    static constexpr int INV7[6] = {1, 5, 2, 3, 6, 4};

public:
    int countSubstrings(string s) {
        int n = s.size();
        vector<int> pref3(n), pref9(n), pref7(n);
        vector<long long> freq3(3), freq9(9);
        vector<array<long long,7>> freq7(6); // 6‑cycle × 7

        long long ans = 0;
        for (int j = 0; j < n; ++j) {
            int d = s[j]-'0';
            if (d == 0) continue;

            // 1, 2, 5
            if (d == 1 || d == 2 || d == 5) {
                ans += j + 1;
            }
            // 3, 6, 9
            else if (d == 3 || d == 6) {
                int rem = pref3[j];
                ans += freq3[rem] + (rem==0);
            } else if (d == 9) {
                int rem = pref9[j];
                ans += freq9[rem] + (rem==0);
            }
            // 4
            else if (d == 4) {
                if (j == 0) ans += 1;
                else {
                    int lastTwo = (s[j-1]-'0')*10 + d;
                    ans += (lastTwo % 4 == 0 ? j+1 : 1);
                }
            }
            // 8
            else if (d == 8) {
                if (j == 0) ans += 1;
                else if (j == 1) {
                    int lastTwo = (s[0]-'0')*10 + 8;
                    ans += (lastTwo % 8 == 0 ? 2 : 1);
                } else {
                    int last3 = (s[j-2]-'0')*100 + (s[j-1]-'0')*10 + 8;
                    int last2 = (s[j-1]-'0')*10 + 8;
                    if (last3 % 8 == 0) ans += j-1;
                    if (last2 % 8 == 0) ans += 1;
                    ans += 1;
                }
            }
            // 7
            else if (d == 7) {
                int rem = pref7[j];
                ans += (rem==0);
                for (int k=0;k<6;k++) {
                    int idx = (j%6 - k + 6)%6;
                    int need = (rem * INV7[k]) % 7;
                    ans += freq7[idx][need];
                }
            }

            // update prefix remainders for next round
            pref3[j] = (j==0? d : (pref3[j-1]*10 + d)%3);
            pref9[j] = (j==0? d : (pref9[j-1]*10 + d)%9);
            pref7[j] = (j==0? d : (pref7[j-1]*10 + d)%7);

            // freq updates
            freq3[pref3[j]]++;
            freq9[pref9[j]]++;
            freq7[j%6][pref7[j]]++;
        }
        return ans;
    }
};
```

> **C++ notes** –  
> * Uses `vector` for dynamic arrays.  
> * `array<long long,7>` for fixed inner table, which is slightly faster than `vector`.  

---

## 6. Complexity Summary

| Language | Time  | Extra Space |
|----------|-------|-------------|
| Java     | O(n)  | ~50 bytes  |
| Python   | O(n)  | ~70 bytes  |
| C++      | O(n)  | ~50 bytes  |

> **Why `O(1)` per position** –  
> Every `d` triggers at most a handful of array accesses and arithmetic operations.

---

## 7. Testing & Validation

- **Unit tests** – Check small strings manually (`"12345"`, `"9876543210"`, `"1111111111"`).
- **Stress tests** – Random strings up to `10^5` digits; compare against a brute force algorithm on a reduced dataset (`n ≤ 2000`) to validate correctness.
- **Edge cases** –  
  - All digits same (`"1111"`, `"2222"`, `"4444"`, `"7777"`).  
  - Strings ending with `0` (ensures skip).  
  - Maximal length random string (`1e5` digits).

---

## 8. Conclusion

> **Takeaway** – A seemingly “heavy” counting problem over all substrings of a decimal number can be solved in linear time by:
> 
> 1. Grouping digits by *mathematical divisibility properties*.  
> 2. Using **prefix remainders** and short frequency tables.  
> 3. Scanning once, computing each contribution in constant time.  
> 
> This approach applies equally well to other divisibility checks (e.g., `n % 11`, `n % 13`) – only the cycle period and inverse table change.

> **Why you should remember this** –  
> The same pattern shows up in many interview problems:  
> *Reduce the problem to a small state space (prefix remainder + position) and use a frequency map to accumulate counts.*  

---

> **Happy coding!** 🎉  
> If you liked this solution, give it a 👍 on GitHub or drop a ⭐ on the repository.  
> *Happy coding – and may your numbers always be divisible!*