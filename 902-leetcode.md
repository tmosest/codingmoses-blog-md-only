---
title: LeetCode 902. Numbers At Most N Given Digit Set - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 902 – *Numbers At Most N Given Digit Set*  
**The Good, the Bad, and the Ugly** – A SEO‑friendly guide that will help you land your next software‑engineering role

---

## 1. What is the problem?

> **LeetCode 902 – Numbers At Most N Given Digit Set**  
> Given a sorted array of distinct digits (`'1'`‑`'9'`) and an integer `n`, count how many **positive integers** can be written using those digits (repeated any number of times) that are **≤ n**.

```text
digits = ["1","3","5","7"],  n = 100
output = 20   // 1,3,5,7,11,13,15,...,77
```

The challenge lies in doing this efficiently for `n` up to `10^9` (9 digits) while keeping the code clean.

---

## 2. Why is this problem useful for interviews?

| Why it matters | Why it matters |
|----------------|----------------|
| **Digit DP** – a classic interview pattern | **Counting & combinatorics** – tests your ability to think in terms of *“how many”* |
| Handles **boundary cases** (leading zeros, equal‑length numbers) | Demonstrates *“break the problem into smaller sub‑problems”* |
| Can be solved in **O(log n)** time, **O(1)** space – a nice trade‑off | You’ll show you can write clean, production‑ready code in Java/Python/C++ |

---

## 3. The Good – A clean O(log n) counting solution

1. **All numbers with fewer digits than `n`**  
   For every length `l` (1 … k‑1) there are `|digits|^l` possibilities.

2. **Numbers with the same length**  
   Scan the digits of `n` from most‑significant to least‑significant.  
   For each position:

   * Count how many digits from `digits` are **smaller** than the current digit of `n`.  
     Each such choice locks the remaining positions → `|digits|^(k-i-1)` numbers.

   * If a digit in `digits` equals the current digit of `n`, we **must** continue to the next position.  
     If no digit matches, we are done – no number with the same prefix can equal `n`.

3. **If we finish the loop** – `n` itself can be formed → add 1.

### Complexity

| Metric | Value |
|--------|-------|
| **Time** | O(k) where k = number of digits in `n` (≤ 9) |
| **Space** | O(1) |

This is the “one‑pass counting” method that many top solutions use.

---

## 4. The Bad – Common pitfalls

| Pitfall | Why it happens | Fix |
|---------|----------------|-----|
| **Using `Math.pow` with integers** | Returns a `double`, causing precision errors for large exponents. | Pre‑compute powers with `long` or use a simple loop. |
| **Assuming `n` is always reachable** | If the smallest digit > first digit of `n`, you’ll incorrectly count some numbers. | Check if any digit in `digits` equals the current digit before moving on. |
| **Overflow** | `int` may overflow for `|digits|^9` (e.g., 9^9 > 2^31). | Use `long` or `long long` and cast the final result back to `int` (LeetCode guarantees the answer fits in `int`). |

---

## 5. The Ugly – Over‑engineering and confusing DP variants

Some solutions over‑complicate the problem:

* **Recursive DFS** that explores every combination until it exceeds `n`.  
  *Takes too long and uses stack space unnecessarily.*

* **Digit DP with memoization** that builds a table for each prefix length.  
  *Hard to explain in an interview and can be overkill for 9 digits.*

* **Unnecessary use of `String` conversion inside loops** – slows down the solution.

While these approaches are **correct**, they distract from the clean counting logic and make it harder to present your idea succinctly.

---

## 6. Full Code – Java, Python, C++

Below are three clean implementations that follow the one‑pass counting logic.

### Java

```java
class Solution {
    public int atMostNGivenDigitSet(String[] digits, int n) {
        String N = String.valueOf(n);
        int k = N.length();
        int dLen = digits.length;
        int count = 0;

        // 1) All numbers with fewer digits than n
        for (int len = 1; len < k; ++len) {
            count += powInt(dLen, len);
        }

        // 2) Numbers with the same length
        for (int i = 0; i < k; ++i) {
            int cur = N.charAt(i) - '0';
            boolean equal = false;
            for (String d : digits) {
                int dig = d.charAt(0) - '0';
                if (dig < cur) {
                    count += powInt(dLen, k - i - 1);
                } else if (dig == cur) {
                    equal = true;
                }
            }
            if (!equal) return count;   // no matching prefix
        }

        return count + 1; // n itself
    }

    // Integer power – avoids double precision issues
    private int powInt(int base, int exp) {
        int res = 1;
        for (int i = 0; i < exp; ++i) res *= base;
        return res;
    }
}
```

### Python

```python
class Solution:
    def atMostNGivenDigitSet(self, digits: list[str], n: int) -> int:
        s = str(n)
        k = len(s)
        dlen = len(digits)
        count = 0

        # 1) Shorter numbers
        for length in range(1, k):
            count += dlen ** length

        # 2) Same‑length numbers
        for i, ch in enumerate(s):
            cur = int(ch)
            equal = False
            for d in digits:
                dig = int(d)
                if dig < cur:
                    count += dlen ** (k - i - 1)
                elif dig == cur:
                    equal = True
            if not equal:
                return count

        return count + 1
```

### C++

```cpp
class Solution {
public:
    int atMostNGivenDigitSet(vector<string>& digits, int n) {
        string S = to_string(n);
        int k = S.size();
        int dLen = digits.size();
        long long count = 0;

        // 1) Numbers with fewer digits
        for (int len = 1; len < k; ++len)
            count += powerInt(dLen, len);

        // 2) Numbers with the same length
        for (int i = 0; i < k; ++i) {
            int cur = S[i] - '0';
            bool equal = false;
            for (const auto& d : digits) {
                int dig = d[0] - '0';
                if (dig < cur) count += powerInt(dLen, k - i - 1);
                else if (dig == cur) equal = true;
            }
            if (!equal) return static_cast<int>(count);
        }

        return static_cast<int>(count + 1);
    }

private:
    int powerInt(int base, int exp) {
        int res = 1;
        for (int i = 0; i < exp; ++i) res *= base;
        return res;
    }
};
```

> **Tip** – In C++ you may pre‑compute the powers of `|digits|` once in an array to avoid repeated multiplications.

---

## 7. Step‑by‑Step Walk‑through (Java version)

| Step | Action | Why it matters |
|------|--------|----------------|
| `count += powInt(dLen, len)` | Count all `len`‑digit numbers that are *shorter* than `n`. | Combines *combinatorics* (`|digits|^len`) with the *length of `n`*. |
| `if (dig < cur) count += powInt(dLen, k-i-1)` | For every smaller choice at position `i`, lock the rest. | Handles the *“≤ n”* condition in a single pass. |
| `if (!equal) return count` | No digit matches current position → impossible to reach `n`. | Stops early and keeps time constant. |
| `return count + 1` | `n` itself is constructible. | Handles the *edge case* where `n` is exactly one of the formed numbers. |

---

## 8. Alternative Approaches

| Approach | Pros | Cons |
|----------|------|------|
| **Recursive DFS** | Intuitive to think about “try all combinations”. | Too slow (`9^9` calls) and uses stack space. |
| **Digit DP (top‑down with memo)** | Works for larger `n` (many digits). | Hard to explain; not needed for 9 digits. |
| **Breadth‑First Search (BFS)** | Generates numbers in increasing order. | Needs a queue → O(|digits|^k) memory in worst case. |

> **Bottom line** – For a 9‑digit `n` the counting method is the fastest and easiest to communicate.

---

## 9. Interview‑ready Tips

1. **Clarify the problem** – ask for examples of edge cases (`n` smaller than the smallest digit, `n` exactly equals a constructible number, etc.).
2. **State the plan** – “We’ll first count all shorter numbers, then process same‑length numbers in a single pass.”
3. **Show the math** – explain why `|digits|^l` possibilities exist for length `l` and why `|digits|^(k-i-1)` is added for a smaller choice at position `i`.
4. **Walk through a sample** – e.g. `digits = ["1","3","5"], n = 235` – demonstrate how the loop works.
5. **Discuss pitfalls** – mention the `Math.pow` issue and overflow.
6. **Code** – keep it clean, use a helper for integer powers, and write short, self‑contained methods.

---

## 10. Resources & Further Reading

| Link | What it covers |
|------|----------------|
| [LeetCode 902 – Official Problem](https://leetcode.com/problems/numbers-at-most-n-given-digit-set/) | Problem statement + examples |
| [Top 100 LeetCode Solutions – 902](https://leetcode.com/articles/top-100-leetcode-solutions/) | Clean Java/Python/C++ solutions |
| [Digit DP – GeeksforGeeks](https://www.geeksforgeeks.org/digit-dp/) | In‑depth theory and variations |
| [Counting Combinations – HackerRank “Count the Numbers”](https://www.hackerrank.com/challenges/count-the-numbers) | Similar combinatorial counting logic |

---

## 11. Final Thoughts

- **Practice the counting trick** until you can explain it in under a minute.
- **Run your code on the edge cases**:  
  * `n = 1`, `digits = ["1","2"]` → answer = 1  
  * `n = 1000000000`, `digits = ["1","2","3","4","5","6","7","8","9"]` → answer = 999,999,999
- **During the interview**, start with “I’ll count all numbers with fewer digits, then deal with the same length. It takes just one pass.” This shows you know the core insight.

> **Remember**: LeetCode 902 is not just a *math puzzle* – it’s a gateway to mastering **digit‑based DP** and **combinatorial counting**—skills that hiring managers love to see.

Happy coding, and good luck on your next interview! 🚀