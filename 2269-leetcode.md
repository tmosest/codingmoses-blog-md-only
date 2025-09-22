---
title: LeetCode 2269. Find the K-Beauty of a Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Language Solutions for **LeetCode 2269 – Find the K‑Beauty of a Number**

| Language | File | Complexity | Notes |
|----------|------|------------|-------|
| Java | `Solution.java` | **O(n · k)** time, **O(1)** extra | Uses the built‑in `String.substring` + `Integer.parseInt`. Works comfortably because `n ≤ 10`. |
| Python | `solution.py` | **O(n · k)** time, **O(1)** extra | Uses string slicing and `int()` conversion. |
| C++ | `solution.cpp` | **O(n · k)** time, **O(1)** extra | Uses `substr` and `stoi`. |

> **Tip for interviews** – if the number were much larger you would want a *sliding‑window* integer update instead of repeated parsing.  
> Here we keep the code clean because the constraints are tiny.

---

### Java

```java
// 2269. Find the K-Beauty of a Number
// LeetCode problem #2269
// Author: ChatGPT (adapted from official editorial)

public class Solution {
    public int divisorSubstrings(int num, int k) {
        // Convert the number to a string once
        String s = Integer.toString(num);
        int len = s.length();
        int count = 0;

        for (int i = 0; i <= len - k; i++) {
            // Extract substring of length k
            String sub = s.substring(i, i + k);
            int val = Integer.parseInt(sub);   // Leading zeros are fine

            // 0 is never a divisor
            if (val != 0 && num % val == 0) {
                count++;
            }
        }
        return count;
    }
}
```

---

### Python

```python
# 2269. Find the K-Beauty of a Number
# LeetCode problem #2269
# Author: ChatGPT (adapted from official editorial)

class Solution:
    def divisorSubstrings(self, num: int, k: int) -> int:
        s = str(num)
        n = len(s)
        count = 0

        for i in range(n - k + 1):
            sub = s[i:i + k]
            val = int(sub)          # Leading zeros allowed
            if val != 0 and num % val == 0:
                count += 1
        return count
```

---

### C++

```cpp
// 2269. Find the K-Beauty of a Number
// LeetCode problem #2269
// Author: ChatGPT (adapted from official editorial)

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int divisorSubstrings(int num, int k) {
        string s = to_string(num);
        int n = s.size(), cnt = 0;

        for (int i = 0; i <= n - k; ++i) {
            string sub = s.substr(i, k);
            int val = stoi(sub);           // Leading zeros fine
            if (val != 0 && num % val == 0)
                ++cnt;
        }
        return cnt;
    }
};
```

---

## 2. SEO‑Optimized Blog Post

> **Title**: *Cracking LeetCode 2269: “Find the K‑Beauty of a Number” – Java, Python, and C++ Solutions + Interview Tips*

> **Meta Description**:  
> Learn how to solve LeetCode #2269 in Java, Python, and C++. Understand the algorithm, complexity, pitfalls, and how to ace the question in a coding interview. Boost your coding interview score today!

---

### Introduction

The **“Find the K‑Beauty of a Number”** problem is a classic LeetCode “easy” puzzle that tests your string manipulation, divisor logic, and edge‑case handling.  
In this article we’ll:

1. **Explain the problem** in plain English.
2. Walk through the **core algorithm** and provide solutions in **Java, Python, and C++**.
3. Discuss the **good, bad, and ugly** aspects – from clean code to potential pitfalls.
4. Give **interview‑ready tips** and how to present the solution to hiring managers.

Let’s dive in!

---

### Problem Recap

> **Definition**: The *k‑beauty* of an integer `num` is the number of substrings of `num` (when read as a string) that satisfy:
> 1. Length equals `k`.
> 2. The integer value of the substring divides `num` evenly.
> 3. `0` is **never** a divisor (even if the substring is “00”).

**Input**  
- `num`: positive integer (`1 ≤ num ≤ 10^9`).  
- `k`: positive integer (`1 ≤ k ≤ num.length`).

**Output**  
- Integer count of qualifying substrings.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `num = 240`, `k = 2` | `2` | “24” and “40” are divisors. |
| `num = 430043`, `k = 2` | `2` | “43” occurs twice; “30”, “00”, “04” are invalid. |

---

### Core Idea

1. **Stringify** the integer so we can take contiguous slices.
2. **Slide** a window of size `k` across the string.
3. Convert each window to an integer (leading zeros don’t matter).
4. Check two conditions: `val != 0` and `num % val == 0`.
5. Increment the counter accordingly.

Because the maximum length of `num` is 10, the naive `O(n · k)` algorithm is more than fast enough.

---

### Solution Details & Code

Below is the **canonical implementation** that you’ll see in the LeetCode editor for all three languages.

#### Java

```java
public class Solution {
    public int divisorSubstrings(int num, int k) {
        String s = Integer.toString(num);
        int count = 0;
        for (int i = 0; i <= s.length() - k; i++) {
            int val = Integer.parseInt(s.substring(i, i + k));
            if (val != 0 && num % val == 0) {
                count++;
            }
        }
        return count;
    }
}
```

#### Python

```python
class Solution:
    def divisorSubstrings(self, num: int, k: int) -> int:
        s = str(num)
        count = 0
        for i in range(len(s) - k + 1):
            val = int(s[i:i + k])
            if val != 0 and num % val == 0:
                count += 1
        return count
```

#### C++

```cpp
class Solution {
public:
    int divisorSubstrings(int num, int k) {
        string s = to_string(num);
        int count = 0;
        for (int i = 0; i <= s.size() - k; ++i) {
            int val = stoi(s.substr(i, k));
            if (val != 0 && num % val == 0)
                ++count;
        }
        return count;
    }
};
```

**Complexity**

- *Time*: `O(n · k)` (worst case ~ 100 operations, trivial for modern CPUs).
- *Space*: `O(1)` (ignoring input string, which is part of the problem).

---

### Good, Bad, and Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Clear variable names (`s`, `val`, `count`). | None. | Some people may over‑optimize with manual integer updates. |
| **Edge Cases** | Handles leading zeros (`int("04")` → 4). | Must guard against `val == 0` to avoid division by zero. | Forgetting the guard makes the code crash or give wrong answers. |
| **Performance** | With `n ≤ 10` the naïve solution is fine. | Using `Integer.parseInt` inside a loop may feel heavy but is negligible. | For larger inputs, you’d want a *rolling* integer update to avoid repeated parsing. |
| **Scalability** | Works for 32‑bit ints. | For `num` > 2^31‑1 you’d need `long` or `BigInteger`. | Using `long` in Java and `long long` in C++ is safe for up to 10^9. |
| **Language Idioms** | Java uses `substring`; Python slicing; C++ `substr`. | None. | Mixing 0‑based and 1‑based indices incorrectly leads to off‑by‑one errors. |

---

### Interview Tips

1. **Explain the Constraints First**  
   - `num` ≤ 10^9 → at most 10 digits.  
   - `k` ≤ number of digits → window size never exceeds string length.

2. **Talk Through Edge Cases**  
   - Leading zeros → `int("00") = 0`.  
   - Zero divisor rule.  
   - Duplicate substrings (e.g., “43” appears twice in `430043`).

3. **Show a Clean O(n · k) Implementation**  
   - Mention that a sliding‑window integer update could reduce constant factors but is unnecessary here.

4. **Time & Space Analysis**  
   - O(n·k) time, O(1) space.  
   - Why it meets the problem’s constraints.

5. **If Asked for a More Efficient Version**  
   - Show the rolling window approach:  
     ```
     val = val % pow10(k-1) * 10 + new_digit
     ```
     but explain why it’s overkill for this problem.

---

### Closing Thoughts

The “Find the K‑Beauty of a Number” challenge is deceptively simple but showcases the importance of **careful edge‑case handling** (leading zeros, division by zero) and **clear code structure**. Solving it in Java, Python, or C++ demonstrates language‑agnostic algorithmic thinking – a key interview skill.

Feel free to copy the snippets above into your local IDE or LeetCode editor. Happy coding, and may this problem boost your interview confidence! 🚀

---

### Resources & Further Reading

- [LeetCode Problem 2269](https://leetcode.com/problems/find-the-k-beauty-of-a-number/)
- [Sliding Window Technique](https://leetcode.com/discuss/general-discussion/1172340/sliding-window-solution)
- [Avoiding Division by Zero](https://stackoverflow.com/q/1157228/)

--- 

**SEO Keywords:**  
LeetCode 2269, Find the K‑Beauty of a Number, Java solution, Python solution, C++ solution, coding interview, algorithm, string manipulation, divisor, interview tips, job interview prep.