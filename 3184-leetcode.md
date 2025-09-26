---
title: LeetCode 3184. Count Pairs That Form a Complete Day I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 3184 – Count Pairs That Form a Complete Day (Easy)

| Language | Complexity | Link to Code |
|----------|------------|--------------|
| **Java** | O(n) | `Solution.java` |
| **Python** | O(n) | `solution.py` |
| **C++** | O(n) | `solution.cpp` |

> *“A complete day is a time duration that is an exact multiple of 24 hours.”*

The problem is a classic “two‑sum‑mod‑k” puzzle.  Below you’ll find a clean, production‑ready implementation in **Java**, **Python**, and **C++**, followed by a blog‑style walkthrough that highlights the *good*, *bad*, and *ugly* aspects of solving this interview problem.

---

## 📚 Problem Recap

Given an array `hours[]` (1 ≤ n ≤ 100, 1 ≤ hours[i] ≤ 10⁹), count how many unordered pairs `(i, j)` with `i < j` satisfy

```
(hours[i] + hours[j]) % 24 == 0
```

In other words, the sum is a multiple of 24.

---

## 🧠 Solution Idea – HashMap + Remainder Counting

Instead of checking every pair (`O(n²)`), we can solve it in linear time:

1. **Store counts of remainders**  
   For each hour, compute `rem = hour % 24`.  
   The complement remainder needed to reach a multiple of 24 is  
   `comp = (24 - rem) % 24`.

2. **Count valid pairs on the fly**  
   While iterating, the number of pairs involving the current element equals the current count of `comp` already seen.

3. **Update frequency map**  
   Increment the counter for `rem`.

This technique is the same as the classic two‑sum trick, but we’re working modulo 24.

---

## ✅ Reference Implementation

### Java

```java
// Solution.java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int countCompleteDayPairs(int[] hours) {
        Map<Integer, Integer> freq = new HashMap<>();
        int count = 0;

        for (int h : hours) {
            int rem = h % 24;
            int complement = (24 - rem) % 24; // 0 maps to 0
            count += freq.getOrDefault(complement, 0);

            freq.merge(rem, 1, Integer::sum);
        }
        return count;
    }
}
```

### Python

```python
# solution.py
from collections import defaultdict
from typing import List

class Solution:
    def countCompleteDayPairs(self, hours: List[int]) -> int:
        freq = defaultdict(int)
        ans = 0
        for h in hours:
            rem = h % 24
            comp = (24 - rem) % 24
            ans += freq[comp]
            freq[rem] += 1
        return ans
```

### C++

```cpp
// solution.cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countCompleteDayPairs(vector<int>& hours) {
        unordered_map<int, int> freq;
        long long ans = 0;
        for (int h : hours) {
            int rem = h % 24;
            int comp = (24 - rem) % 24;
            ans += freq[comp];
            freq[rem]++;
        }
        return static_cast<int>(ans);
    }
};
```

---

## 📖 Blog Article – “The Good, The Bad, and The Ugly of Solving LeetCode 3184”

### 1. Introduction

If you’re eyeing a software engineering role, you’ll likely encounter LeetCode 3184 in your interview prep. It’s labeled *Easy*, but the mental gymnastics required to avoid the quadratic pitfall are non‑trivial. This post breaks the problem into its core elements, explains why a linear approach shines, and even touches on how interviewers may evaluate your solution.

### 2. The Problem in Plain English

You’re given a list of hours. Any two of them that sum to an exact multiple of 24 form a “complete day.” Count how many such pairs exist. For small arrays, a double loop works; for arrays with 10⁵ elements, it would blow up.

### 3. The *Good* – A Linear Time Solution

The elegant solution uses a **frequency map of remainders**. Think of each hour as a bucket in a 24‑hour cycle. If a number’s remainder is `r`, you need another number with remainder `24 – r` (or `0` if `r` is `0`) to reach a multiple of 24. By keeping a counter for each remainder as you iterate, you can instantly know how many valid partners the current number has already seen. The algorithm runs in O(n) time and O(1) additional space (constant 24 buckets).

#### Why It’s Good

- **Speed** – Linear time is the only way to meet the constraints if the array grows.
- **Simplicity** – One pass, one map, no nested loops.
- **Scalability** – Works for `hours.length` up to 10⁵ or more, without hitting time limits.

### 4. The *Bad* – The Brute‑Force Approach

A naive double loop:

```java
int count = 0;
for (int i = 0; i < n; i++)
  for (int j = i + 1; j < n; j++)
    if ((hours[i] + hours[j]) % 24 == 0) count++;
```

This O(n²) algorithm is fine for the problem’s original constraints (n ≤ 100), but it’s a *bad* choice if you’re talking interview prep because:

- **Performance** – It’s easy to say “I'll just do this in the interview”; interviewers expect you to optimize.
- **Clarity** – Shows lack of insight into the problem’s modular nature.
- **Scalability** – Not a future‑proof solution for larger datasets.

### 5. The *Ugly* – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Using `% 24` on huge numbers** | Forgetting that the sum may overflow `int` | Use `long` for sum if language permits; however, for Java you can keep `% 24` safe because `int` stays within 32 bits, but use `long` if you plan to sum explicitly. |
| **Mis‑handling remainder 0** | Thinking `comp = 24 - rem` gives 24 instead of 0 | Use `(24 - rem) % 24` so that `rem = 0` yields `comp = 0`. |
| **Mutable map during iteration** | Accidentally resetting counts inside the loop | Keep a separate `freq` map and update it after counting. |
| **Overflow in counter** | Counting pairs for huge inputs | Use `long` for the answer. |

### 6. Interview‑Ready Tips

1. **Explain the O(n) idea before coding** – Interviewers love to see you think about time complexity.
2. **Mention edge cases** – Zero remainder, large numbers, duplicates.
3. **Test on small samples** – Show you understand the expected output.
4. **Talk about space** – Clarify that you only use 24 counters, regardless of input size.
5. **Optional** – Show a one‑pass version that avoids a second map look‑up by incrementing a counter first.  

```java
// one-pass trick
int count = 0;
for (int h : hours) {
    int rem = h % 24;
    count += freq.getOrDefault( (24 - rem) % 24, 0 );
    freq.put(rem, freq.getOrDefault(rem, 0) + 1);
}
```

### 7. Summary

- **Good**: One‑pass remainder counting, O(n) time, O(1) space.
- **Bad**: Brute‑force nested loops; easy but not interview‑grade.
- **Ugly**: Common mistakes around modulus and integer overflow.

If you can code the solution in **Java**, **Python**, and **C++**—as shown above—and explain the reasoning clearly, you’ll be well‑positioned to ace this question during a coding interview.

### 8. SEO‑Optimized Takeaway

- **Keywords**: LeetCode 3184, Count Pairs That Form a Complete Day, interview coding, algorithm, hashmap, two‑sum modulo 24, Java solution, Python solution, C++ solution, job interview, software engineering interview.
- **Meta Description**: “Master LeetCode 3184 with a linear‑time hashmap solution. Get Java, Python, and C++ code plus an SEO‑friendly interview guide that covers the good, bad, and ugly of solving Count Pairs That Form a Complete Day.”

Happy coding, and good luck on your next interview! 🚀