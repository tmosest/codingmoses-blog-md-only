---
title: LeetCode 1363. Largest Multiple of Three - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Largest Multiple of Three – LeetCode 1363  
**Java | Python | C++ | Algorithm | Interview Prep | Job‑Ready Coding**

---

### Table of Contents  
1. [Problem Overview](#problem-overview)  
2. [Key Observations](#key-observations)  
3. [Greedy + Modulo‑3 Counting](#greedy-modulo-3-counting)  
4. [Implementation Details](#implementation-details)  
   * 4.1 Java  
   * 4.2 Python  
   * 4.3 C++  
5. [Time & Space Complexity](#time-space-complexity)  
6. [Common Pitfalls & Edge Cases](#pitfalls)  
7. [“Good, Bad, Ugly” of the Solution](#good-bad-ugly)  
8. [Why This Matters for Your Next Job Interview](#why-matters)  
9. [Conclusion & Further Reading](#conclusion)  

---

## Problem Overview <a name="problem-overview"></a>
> **LeetCode 1363 – Largest Multiple of Three**  
> Given an array of digits (`0‑9`), build the **largest possible number** that is a multiple of `3` by concatenating any subset of the digits in any order.  
> Return the result as a string (no unnecessary leading zeros). Return `""` if no such number exists.  

Examples  
| Input | Output | Explanation |
|-------|--------|-------------|
| `[8,1,9]` | `"981"` | `981` is divisible by `3` and is the biggest arrangement. |
| `[8,6,7,1,0]` | `"8760"` | Removing the `1` gives a sum divisible by `3`. |
| `[1]` | `""` | No multiple of `3` can be formed. |

---

## Key Observations <a name="key-observations"></a>
1. **Divisibility by 3**  
   A number is divisible by 3 iff the sum of its digits is divisible by 3.  
   → We only need the **total sum** mod `3`.

2. **Only 10 distinct digits**  
   We can store how many times each digit appears in an array of size 10.  
   → O(1) memory, no sorting required.

3. **Greedy Removal**  
   If the sum ≡ 1 (mod 3):  
   * Remove **one** smallest digit ≡ 1 (mod 3) **or**  
   * Remove **two** smallest digits ≡ 2 (mod 3).  

   If the sum ≡ 2 (mod 3):  
   * Remove **one** smallest digit ≡ 2 (mod 3) **or**  
   * Remove **two** smallest digits ≡ 1 (mod 3).  

   Removing the *smallest* possible digits keeps the resulting number as large as possible.

4. **Leading Zeros**  
   After removals the string might start with `0`.  
   * If all remaining digits are `0`, return `"0"`.  
   * If no digits left, return `""`.

---

## Greedy + Modulo‑3 Counting <a name="greedy-modulo-3-counting"></a>
1. Count each digit (`0`‑`9`).  
2. Compute `totalSum % 3`.  
3. Decide how many digits of each remainder class (`%3 == 1` or `2`) to delete.  
4. Build the answer by iterating **from 9 down to 0** and appending the remaining digits.  
5. Handle edge cases (empty string / only zeros).

This approach runs in **O(n)** time (one pass to count, constant‑time cleanup) and uses **O(1)** extra space.

---

## Implementation Details <a name="implementation-details"></a>
Below are clean, well‑commented implementations in **Java, Python, and C++**.  
All three share the same logical flow but are idiomatic to each language.

### 4.1 Java <a name="java"></a>
```java
import java.util.*;

public class Solution {
    public String largestMultipleOfThree(int[] digits) {
        // Count occurrences of each digit.
        int[] cnt = new int[10];
        int sum = 0;
        for (int d : digits) {
            cnt[d]++;
            sum += d;
        }

        // Determine how many digits to delete.
        int r1 = cnt[1] + cnt[4] + cnt[7]; // digits with remainder 1
        int r2 = cnt[2] + cnt[5] + cnt[8]; // digits with remainder 2
        int delete1 = 0, delete2 = 0; // deletions of remainder 1 / 2 digits

        int rem = sum % 3;
        if (rem == 1) {
            if (r1 >= 1) { delete1 = 1; }
            else if (r2 >= 2) { delete2 = 2; }
            else return "";   // impossible
        } else if (rem == 2) {
            if (r2 >= 1) { delete2 = 1; }
            else if (r1 >= 2) { delete1 = 2; }
            else return "";
        }

        // Remove the smallest needed digits.
        for (int d = 1; d <= 9 && delete1 > 0; d += 3) { // 1,4,7
            int take = Math.min(cnt[d], delete1);
            cnt[d] -= take;
            delete1 -= take;
        }
        for (int d = 2; d <= 9 && delete2 > 0; d += 3) { // 2,5,8
            int take = Math.min(cnt[d], delete2);
            cnt[d] -= take;
            delete2 -= take;
        }

        // Build the number from largest to smallest digit.
        StringBuilder sb = new StringBuilder();
        for (int d = 9; d >= 0; d--) {
            for (int i = 0; i < cnt[d]; i++) sb.append(d);
        }

        if (sb.length() == 0) return "";
        if (sb.charAt(0) == '0') return "0";
        return sb.toString();
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.largestMultipleOfThree(new int[]{8,1,9})); // 981
        System.out.println(s.largestMultipleOfThree(new int[]{8,6,7,1,0})); // 8760
        System.out.println(s.largestMultipleOfThree(new int[]{1})); // ""
    }
}
```

> **Why this Java version is efficient?**  
> • No sorting → `O(n)` time.  
> • Uses constant‑size arrays → minimal overhead.  
> • Straightforward logic with clear comments – great for interview whiteboard.

---

### 4.2 Python <a name="python"></a>
```python
from typing import List
from collections import Counter

class Solution:
    def largestMultipleOfThree(self, digits: List[int]) -> str:
        cnt = [0] * 10
        total = 0
        for d in digits:
            cnt[d] += 1
            total += d

        r1 = cnt[1] + cnt[4] + cnt[7]
        r2 = cnt[2] + cnt[5] + cnt[8]
        delete1 = delete2 = 0
        rem = total % 3

        if rem == 1:
            if r1 >= 1: delete1 = 1
            elif r2 >= 2: delete2 = 2
            else: return ""
        elif rem == 2:
            if r2 >= 1: delete2 = 1
            elif r1 >= 2: delete1 = 2
            else: return ""

        # Remove smallest digits of each remainder
        for d in (1, 4, 7):
            while delete1 and cnt[d]:
                cnt[d] -= 1
                delete1 -= 1

        for d in (2, 5, 8):
            while delete2 and cnt[d]:
                cnt[d] -= 1
                delete2 -= 1

        # Build result from 9 down to 0
        res = []
        for d in range(9, -1, -1):
            res.extend([str(d)] * cnt[d])

        if not res:          # nothing left
            return ""
        if res[0] == '0':    # all zeros
            return "0"
        return "".join(res)

# ---- Example usage ----
if __name__ == "__main__":
    s = Solution()
    print(s.largestMultipleOfThree([8, 1, 9]))        # 981
    print(s.largestMultipleOfThree([8, 6, 7, 1, 0])) # 8760
    print(s.largestMultipleOfThree([1]))              # ""
```

> **Python niceties**  
> • `cnt` is a fixed‑size list → O(1) memory.  
> • `Counter` is overkill; a list keeps the algorithm tight.  
> • The `while` loops make the removal step explicit and easy to read on a whiteboard.

---

### 4.3 C++ <a name="cpp"></a>
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string largestMultipleOfThree(vector<int>& digits) {
        array<int, 10> cnt{};            // digit counts
        long long sum = 0;
        for (int d : digits) {
            ++cnt[d];
            sum += d;
        }

        int r1 = cnt[1] + cnt[4] + cnt[7];
        int r2 = cnt[2] + cnt[5] + cnt[8];
        int del1 = 0, del2 = 0;
        int rem = sum % 3;

        if (rem == 1) {
            if (r1 >= 1) del1 = 1;
            else if (r2 >= 2) del2 = 2;
            else return "";
        } else if (rem == 2) {
            if (r2 >= 1) del2 = 1;
            else if (r1 >= 2) del1 = 2;
            else return "";
        }

        // delete smallest remainder‑1 digits (1,4,7)
        for (int d = 1; d <= 9 && del1 > 0; d += 3) {
            int take = min(cnt[d], del1);
            cnt[d] -= take;
            del1 -= take;
        }
        // delete smallest remainder‑2 digits (2,5,8)
        for (int d = 2; d <= 9 && del2 > 0; d += 3) {
            int take = min(cnt[d], del2);
            cnt[d] -= take;
            del2 -= take;
        }

        string res;
        for (int d = 9; d >= 0; --d) {
            res.append(cnt[d], char('0' + d));
        }

        if (res.empty()) return "";
        if (res[0] == '0') return "0";
        return res;
    }
};

// quick test
int main() {
    Solution s;
    cout << s.largestMultipleOfThree({8,1,9}) << endl;       // 981
    cout << s.largestMultipleOfThree({8,6,7,1,0}) << endl;  // 8760
    cout << s.largestMultipleOfThree({1}) << endl;          // ""
}
```

> **C++ perks**  
> • Uses `array<int,10>` for O(1) memory.  
> • `string::append(count, char)` is a fast way to build the result.  
> • Compact yet fully typed – ideal for a coding‑platform interview.

---

## Time & Space Complexity <a name="time-space-complexity"></a>
| Language | Time | Extra Space |
|----------|------|-------------|
| Java | **O(n)** | **O(1)** (10‑size array) |
| Python | **O(n)** | **O(1)** |
| C++ | **O(n)** | **O(1)** |

The algorithm is linear in the input length and uses only a few dozen bytes of memory.

---

## Common Pitfalls & Edge Cases <a name="pitfalls"></a>
| Pitfall | What happens? | Fix |
|---------|---------------|-----|
| **Removing too many digits** – e.g., deleting all `%1` digits when only one existed | Leaves a non‑divisible sum | Check `r1` and `r2` before deletion. |
| **Leaving only zeros** – e.g., input `[0,0,0]` | Resulting string `"000"` → should be `"0"` | After building the string, if `sb[0] == '0'` return `"0"`. |
| **Empty output** – after deletions no digits left | Should return `""` | If `sb.length()==0` → `""`. |
| **Sorting** – using `sort()` will be `O(n log n)` | Slower, not required | Count digits instead of sorting. |
| **Off‑by‑one in loops** – mis‑handling remainder classes | Wrong deletions | Use clear tuples `(1,4,7)` and `(2,5,8)` or step by 3. |

---

## “Good, Bad, Ugly” of the Solution <a name="good-bad-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | Uses a mathematical property (sum % 3) that turns the problem into a *removal* question. | The problem’s wording might mislead you into thinking you need to sort or generate permutations. | None – the solution is clean; the “ugly” part is just handling the `0` edge case, which is unavoidable. |
| **Code readability** | Comments explain *why* we delete particular digits. | Very long loops could obscure intent if you cram everything into one line. | Over‑engineering (e.g., using heaps or recursion) is unnecessary and will make your code look clunky. |
| **Interview style** | Works on a whiteboard – just an array of 10 counters, a few integer calculations, and a loop from 9→0. | In Python or JavaScript you might be tempted to use `sort()` for simplicity; that would be a red flag for interviewers who expect O(n). | Not using the modulo‑3 property and instead trying to brute‑force all subsets will doom your solution to exponential time. |

---

## Why This Matters for Your Next Job Interview <a name="why-matters"></a>
* **Algorithmic thinking** – Demonstrates that you can derive a *mathematical invariant* (sum of digits) and turn it into a greedy algorithm.  
* **Space‑time trade‑offs** – Shows you know when sorting is wasteful and you can replace it with counting.  
* **Edge‑case coverage** – Interviewers love candidates who think of “empty answer” and “only zeros” scenarios.  
* **Clean code** – Well‑commented, language‑idiomatic solutions signal professionalism and communication skills.  
* **Versatility** – Being able to implement the same logic in Java, Python, or C++ proves you can adapt quickly to tech stacks.  

> **Pro tip**: When walking through this problem on a whiteboard, start with a quick sketch of “sum % 3” → “delete one or two digits” → “build from 9 to 0”. Highlight the 10‑size count array. The interviewer will immediately see you’re on the right track.

---

## Conclusion & Further Reading <a name="conclusion"></a>
The **Largest Multiple of Three** problem is a classic example of how a seemingly combinatorial question collapses into a simple modulo arithmetic problem. By leveraging the fact that only ten distinct digits exist, we can build a linear‑time, constant‑space greedy algorithm that is both **efficient** and **easy to understand**.

If you’re preparing for coding interviews, this problem is a must‑solve because it tests:
1. Knowledge of number theory (divisibility by 3).  
2. Ability to design greedy strategies.  
3. Skill in handling edge cases (leading zeros, impossible deletions).  
4. Clean implementation in multiple languages.

---

### 🚀 Ready to Level Up?  
Try solving the problem on LeetCode yourself (link below) and then compare your solutions to the three implementations above.  
🔗 **LeetCode 1363 – Largest Multiple of Three**: <https://leetcode.com/problems/largest-multiple-of-three/>

Good luck, and may your next interview be a perfect multiple of three!