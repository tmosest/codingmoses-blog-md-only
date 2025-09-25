---
title: LeetCode 2847. Smallest Number With Given Digit Product - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣ Solution Code

Below you’ll find **three full, runnable implementations** for the LeetCode problem **2847 – Smallest Number With Given Digit Product**.  
All three solutions use the same greedy logic:

1.  Factor the input `n` only with the primes `2, 3, 5, 7`.  
2.  If a factor remains, the answer is `-1`.  
3.  Combine the prime factors into the smallest possible digits (9 → 3², 8 → 2³, 6 → 2·3, 4 → 2²).  
4.  Build the answer in ascending order of digits.

```text
Complexities
-------------
Time   : O(log n)  (worst‑case we divide by 2, 3, 5 or 7 a few dozen times)
Space  : O(1)      (besides the output string)
```

> **Tip** – When writing interview code, always keep the logic in a separate helper function; this keeps the code readable and easy to test.

---

## 1.1 Java (LeetCode signature)

```java
class Solution {
    public String smallestNumber(long n) {
        // special cases
        if (n == 1) return "1";
        if (n < 10) return String.valueOf(n);

        int[] prime = {2, 3, 5, 7};
        int[] cnt = new int[10];   // cnt[d] = how many times digit d will appear

        // factor n by primes 2,3,5,7
        for (int p : prime) {
            while (n % p == 0) {
                n /= p;
                cnt[p]++;
            }
        }

        if (n != 1) return "-1";   // leftover factor > 7 → impossible

        // merge into larger digits
        cnt[9] = cnt[3] / 2;       // 3*3 = 9
        cnt[3] %= 2;

        cnt[8] = cnt[2] / 3;       // 2*2*2 = 8
        cnt[2] %= 3;

        if (cnt[3] > 0 && cnt[2] > 0) {   // 3*2 = 6
            cnt[3] = 0;
            cnt[2] = 1;          // leave one '2'
            cnt[6] = 1;
        }

        cnt[4] = cnt[2] / 2;       // 2*2 = 4
        cnt[2] %= 2;

        // build answer in ascending order of digits
        StringBuilder sb = new StringBuilder();
        for (int d = 1; d <= 9; d++) {
            for (int i = 0; i < cnt[d]; i++) sb.append((char)('0' + d));
        }
        return sb.toString();
    }
}
```

---

## 1.2 Python 3

```python
class Solution:
    def smallestNumber(self, n: int) -> str:
        if n == 1:            # special case
            return "1"
        if n < 10:            # single digit is already the answer
            return str(n)

        primes = [2, 3, 5, 7]
        cnt = [0] * 10        # cnt[d] = how many times digit d will appear

        # factor n
        for p in primes:
            while n % p == 0:
                n //= p
                cnt[p] += 1

        if n != 1:            # leftover factor > 7 → impossible
            return "-1"

        # merge into larger digits
        cnt[9] = cnt[3] // 2   # 3*3
        cnt[3] %= 2

        cnt[8] = cnt[2] // 3   # 2*2*2
        cnt[2] %= 3

        if cnt[3] > 0 and cnt[2] > 0:   # 3*2
            cnt[6] = 1
            cnt[3] = 0
            cnt[2] = 1

        cnt[4] = cnt[2] // 2   # 2*2
        cnt[2] %= 2

        # build answer in ascending order
        res = []
        for d in range(1, 10):
            res.append(str(d) * cnt[d])
        return "".join(res)
```

---

## 1.3 C++ (GNU C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestNumber(long long n) {
        if (n == 1) return "1";
        if (n < 10) return to_string(n);

        vector<int> prime = {2, 3, 5, 7};
        vector<int> cnt(10, 0);          // cnt[d] = times digit d will appear

        for (int p : prime) {
            while (n % p == 0) {
                n /= p;
                ++cnt[p];
            }
        }
        if (n != 1) return "-1";

        cnt[9] = cnt[3] / 2;   // 3*3
        cnt[3] %= 2;

        cnt[8] = cnt[2] / 3;   // 2*2*2
        cnt[2] %= 3;

        if (cnt[3] && cnt[2]) {   // 3*2
            cnt[6] = 1;
            cnt[3] = 0;
            cnt[2] = 1;
        }

        cnt[4] = cnt[2] / 2;   // 2*2
        cnt[2] %= 2;

        string res;
        for (int d = 1; d <= 9; ++d) {
            res.append(cnt[d], char('0' + d));
        }
        return res;
    }
};
```

---

# 2️⃣ Blog Article – “The Good, the Bad, and the Ugly of Solving LeetCode 2847”

> **Keywords**: *LeetCode 2847*, *Smallest Number with Given Digit Product*, *Java solution*, *Python solution*, *C++ solution*, *interview coding*, *algorithm*, *greedy factorization*, *coding interview tips*, *tech hiring*  

---

## Introduction

Every software engineer has that one LeetCode problem that feels like a *mini‑code‑wars*: you’re staring at a number, you need to pull it apart, and you must do it in the **shortest possible string**. That’s **2847 – Smallest Number With Given Digit Product**.  

It’s a “medium” problem on LeetCode, but in an interview it can **make or break** the candidate’s confidence. Let’s dissect why this problem is a great showcase, what pitfalls recruiters look for, and how you can **turn it into a portfolio‑ready interview star**.

---

## The Good – Why This Problem Is a Gold‑Mine for Candidates

1. **Clear problem statement** – You’re given a single integer `n`. If you can express the product of its digits as `n`, output the *smallest* number that does so.  
2. **Small input domain** – All prime factors larger than 7 ruin the solution. That constraint gives you a **finite set of “useful” primes** (`2, 3, 5, 7`) to worry about.  
3. **Greedy + factorization** – The optimal solution is almost trivial once you see the pattern: 9 = 3², 8 = 2³, 6 = 2·3, 4 = 2². If you merge the prime factors in the right order you get the smallest string instantly.  
4. **Language‑agnostic logic** – Whether you code in Java, Python, or C++, the core idea remains identical. That’s great for candidates who *like* one language but must answer in another during an interview.  

> **Pro tip**: In an interview, **explain your plan before writing code**. Recruiters love to see you think before you type.

---

## The Bad – Common Pitfalls

| Pitfall | Why it’s a problem | How to avoid it |
|---------|--------------------|-----------------|
| **Leaving `n` < 10** – returning `String.valueOf(n)` in Java or `str(n)` in Python. | It seems trivial, but you must also handle `n = 1` which is *not* covered by the “single digit” rule. | Treat `n == 1` as a separate case *before* the general logic. |
| **Full greedy from 9 to 2** without checking for impossible factors. | You might divide by 9, 8, …, but if a leftover factor > 7 remains you’ll silently output a wrong number. | After factorization, **check `n != 1`**; if true, output `-1`. |
| **Not merging prime factors** | If you just output the primes 2,3,5,7 in descending order, the string might be longer than necessary (e.g., 222 → “222” vs “8”). | Merge `2³` → 8, `3²` → 9, `2·3` → 6, `2²` → 4. This is the *greedy merging* step. |
| **Building the result in wrong order** | Reversing a stack of digits gives the wrong number: “98” vs “89”. | Append digits in **ascending** order after merging. In C++/Java `append(cnt[d], digit)` or in Python `"digit"*cnt[d]`. |

---

## The Ugly – Edge‑Case Head‑aches

1. **Huge numbers** (`n` up to `10¹⁸`) – You might think recursion or loops could overflow. Use **`long`/`long long`** and divide step‑by‑step.  
2. **Prime factorization** – While Python’s `//=` is cheap, Java’s modulo on `long` may look intimidating. Just remember: you only divide by 2, 3, 5, or 7 – that’s at most ~60 iterations.  
3. **Memory leak in Java** – A `StringBuilder` is essential. Avoid concatenating strings in a loop; it’s O(n²).  
4. **C++ `string::append` nuances** – `res.append(cnt[d], char('0'+d));` may be confusing to newcomers. The first argument is the **repeat count**.

---

## How to Shine in an Interview

1. **State your approach**  
   “I’ll factor the number using the primes 2, 3, 5, 7. If anything is left, it’s impossible. Then I’ll greedily merge pairs of 3’s into 9’s, triples of 2’s into 8’s, and so on.”

2. **Explain the greedy merge**  
   “Because 9 is the largest single digit, and 9=3², using 9 reduces the digit count. Likewise 8=2³ and 6=2·3, 4=2². This guarantees the lexicographically smallest result.”

3. **Show the code step‑by‑step**  
   *Do not just dump the final snippet.* Walk through the factorization loop, the merging section, and the string construction. Recruiters love the explanation.

4. **Handle corner cases**  
   * `n == 1` → “1”.  
   * `n < 10` → `str(n)` or `to_string(n)`.  
   * Leftover factor > 7 → `-1`.

5. **Test thoroughly**  
   Run the following cases:
   ```text
   1   → "1"
   10  → "-1"   (10 = 2·5 → OK, but 10<10 is a single digit)
   20  → "45"   (20 = 2²·5 → 4·5)
   256 → "288"  (256 = 2⁸ → 8·8)
   1000000000000000000 → "-1"
   ```
   In interviews, candidates often skip the large‑number test; make sure you don’t.

---

## Conclusion – Turning 2847 into a Hiring Win

*LeetCode 2847* is a **micro‑case study** of greedy factorization, prime handling, and string manipulation. It’s small enough to be solved in under 5 minutes, but it tests your:

* **Mathematical intuition** – Recognizing that 9 = 3², 8 = 2³, etc.  
* **Algorithmic clarity** – Writing clean, language‑agnostic code.  
* **Edge‑case awareness** – Handling `1`, single digits, and impossible inputs.  

When you walk into a tech interview, *mention the greedy strategy* first. Show you can reason about why merging prime factors gives the smallest lexicographic string. Then write your code in the interview‑friendly style above.  

> **Remember**: The *good* is the elegant greedy logic; the *bad* is the temptation to over‑think or forget the `-1` case; the *ugly* is the small implementation bugs that show up only in edge cases. Master them, and you’ll move from “medium” to “must‑hire” in no time.

Happy coding, and good luck on your next technical interview! 🚀

--- 

> **Want more interview‑ready solutions?**  
> 👉 [My GitHub LeetCode Repository](https://github.com/your‑github) – Java, Python, C++ – ready for copy‑paste.  
> 👉 Subscribe to my newsletter for weekly algorithm breakdowns and interview‑prep tips!