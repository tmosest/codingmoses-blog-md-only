---
title: LeetCode 2243. Calculate Digit Sum of a String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2243. Calculate Digit Sum of a String  
**LeetCode – Easy** | **Time Limit:** 1 s | **Memory Limit:** 256 MB  

---

### Table of Contents
1. [Problem Overview](#problem-overview)  
2. [Brute‑Force Idea](#brute-force-idea)  
3. [Optimal Algorithm](#optimal-algorithm)  
4. [Implementation](#implementation)  
   - Java  
   - Python  
   - C++  
5. [Complexity Analysis](#complexity-analysis)  
6. [Edge‑Case Checklist](#edge-case-checklist)  
7. **The Good, the Bad, and the Ugly**  
8. [Interview‑Ready Tips](#interview-ready-tips)  
9. [Conclusion](#conclusion)  

---

## Problem Overview <a name="problem-overview"></a>

Given a string `s` composed of digits (`0`‑`9`) and an integer `k`, repeatedly **group** the string into blocks of length `k` (the final block may be shorter).  
For each block, compute the sum of its digits and replace the block with that sum (converted to a string).  
Concatenate the sums of all blocks to form a new string.  
Repeat this process until the string’s length becomes **≤ k**; return the final string.

**Example**

```
s = "11111222223", k = 3
Round 1 → "3465"
Round 2 → "135"   (length ≤ k, stop)
```

Constraints  
- `1 ≤ s.length ≤ 100`  
- `2 ≤ k ≤ 100`  
- `s` contains only digits  

---

## Brute‑Force Idea <a name="brute-force-idea"></a>

A naïve solution would:

1. Parse every character into an integer array.  
2. In each round, walk the array, summing `k` consecutive numbers, pushing the sum into a new list.  
3. Convert the list back to a string and repeat.

While correct, this approach uses unnecessary temporary arrays and a lot of string conversions.  
It also makes the code harder to read and maintain.

---

## Optimal Algorithm <a name="optimal-algorithm"></a>

The key observation: **the process is purely sequential** – each round only depends on the current string, not on any history.  
Therefore we can simulate it in place using a `StringBuilder` (Java), a mutable list (Python), or a `stringstream` (C++).

**Steps**

1. While `s.length() > k`:
   - Create an empty builder.
   - For every index `i` from `0` to `s.length()` stepping by `k`:
     - Compute the sum of digits from `i` to `min(i + k, s.length())`.
     - Append the numeric sum (converted to string) to the builder.
   - Replace `s` with `builder.toString()`.
2. Return `s`.

The inner loop processes each character exactly once per round, and each round shortens the string dramatically, so the total cost is linear in the initial length.

---

## Implementation <a name="implementation"></a>

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

| Language | Code |
|----------|------|
| **Java** | <details><summary>Click to expand</summary>

```java
/**
 * LeetCode 2243 – Calculate Digit Sum of a String
 * Author: Your Name (LinkedIn | GitHub)
 */
public class Solution {
    public String digitSum(String s, int k) {
        // Continue until the string length is ≤ k
        while (s.length() > k) {
            StringBuilder next = new StringBuilder();

            // Walk through the string in steps of k
            for (int i = 0; i < s.length(); i += k) {
                int sum = 0;
                // Sum digits of the current block
                for (int j = i; j < Math.min(i + k, s.length()); j++) {
                    sum += s.charAt(j) - '0';
                }
                // Append the sum as a string
                next.append(sum);
            }

            s = next.toString(); // Prepare for next round
        }
        return s;
    }
}
```

</details> |
| **Python** | <details><summary>Click to expand</summary>

```python
"""
LeetCode 2243 – Calculate Digit Sum of a String
Author: Your Name (LinkedIn | GitHub)
"""

class Solution:
    def digitSum(self, s: str, k: int) -> str:
        # Keep reducing while the length exceeds k
        while len(s) > k:
            next_str = []
            for i in range(0, len(s), k):
                block = s[i:i + k]
                digit_sum = sum(int(ch) for ch in block)
                next_str.append(str(digit_sum))
            s = "".join(next_str)
        return s
```

</details> |
| **C++** | <details><summary>Click to expand</summary>

```cpp
/**
 * LeetCode 2243 – Calculate Digit Sum of a String
 * Author: Your Name (LinkedIn | GitHub)
 */
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string digitSum(string s, int k) {
        while ((int)s.size() > k) {
            string next;
            for (int i = 0; i < (int)s.size(); i += k) {
                int sum = 0;
                for (int j = i; j < min(i + k, (int)s.size()); ++j) {
                    sum += s[j] - '0';
                }
                next += to_string(sum);
            }
            s.swap(next);          // avoid copy
        }
        return s;
    }
};
```

</details> |

---

## Complexity Analysis <a name="complexity-analysis"></a>

| Operation | Time | Space |
|-----------|------|-------|
| Each round | `O(current_length)` | `O(current_length)` (builder) |
| Total rounds | ≤ `log_k (initial_length)` | — |
| Overall | **O(n)** where `n` ≤ 100 | **O(n)** |

With `n` ≤ 100, the solution runs comfortably within the limits.

---

## Edge‑Case Checklist <a name="edge-case-checklist"></a>

| Edge Case | What to test | Why it matters |
|-----------|--------------|----------------|
| `k` equals string length | `s = "123", k = 3` | One round only |
| `k` larger than string | `s = "12", k = 5` | No rounds, return `s` |
| All zeros | `s = "0000", k = 2` | Result keeps zeros (`"00"`), test leading zeros |
| Single‑digit string | `s = "7", k = 3` | Immediate return |
| Max length (100) | Random 100‑digit string, `k = 10` | Stress test |

---

## The Good, the Bad, and the Ugly <a name="the-good-the-bad-and-the-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic elegance** | Straight‑forward simulation; no hidden tricks | Requires careful loop bounds to avoid off‑by‑one | Some naïve solutions convert every character to int repeatedly, hurting performance |
| **Readability** | Clear `for` loops, descriptive variable names | Over‑nested loops can confuse | Using regex or complex list comprehensions can obscure intent |
| **Edge‑Case Handling** | Explicit `min(i + k, len)` handles the last block | Forgetting to handle the last block leads to errors | Using integer division without checking remainder → silent bugs |
| **Space Utilization** | Reuse builder or stringstream | Allocating a new string each round may be wasteful | Storing every intermediate string in a vector → O(n²) memory |
| **Testing** | Simple unit tests cover all edge cases | Lack of negative tests can hide bugs | Failing to test with leading zeros causes mistaken outputs |

> **Pro tip:** When explaining your solution in an interview, start with the intuition (“group, sum, replace”) before diving into code. This demonstrates a clear problem‑solving mindset.

---

## Interview‑Ready Tips <a name="interview-ready-tips"></a>

1. **Explain the invariant**: “After each round, the string’s length is at most `previous_length / k + 2`.”  
2. **Discuss why `while (len > k)` is correct**: Because the process stops exactly when the length ≤ k.  
3. **Show how you handle the last block**: Use `min(i + k, len)` or `substring(i, min(...))`.  
4. **Mention complexity**: Linear time, linear auxiliary space.  
5. **Ask clarifying questions**: “Is the input guaranteed to be non‑empty?” (Yes per constraints.)  
6. **Share a quick unit test**: `assert solution.digitSum("00000000", 3) == "000"`.  

These points give interviewers confidence that you understand both the algorithm and its edge‑cases.

---

## Conclusion <a name="conclusion"></a>

LeetCode 2243 is a textbook example of **string manipulation + simulation**.  
The trick lies in grouping correctly and summing digits without unnecessary overhead.  
The provided Java, Python, and C++ implementations are concise, efficient, and production‑ready.

If you can explain this solution in an interview—covering the intuition, edge cases, and complexity—you’ll impress recruiters and get closer to that job offer. Good luck! 🚀

---

**Keywords:**  
`LeetCode 2243`, `digit sum`, `string grouping`, `simulation algorithm`, `Java solution`, `Python solution`, `C++ solution`, `interview prep`, `time complexity O(n)`, `edge case handling`, `coding interview tips`.  
--- 

*Feel free to fork this repo and add your own tests or optimizations! Happy coding!*