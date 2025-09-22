---
title: LeetCode 1999. Smallest Greater Multiple Made of Two Digits - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 1999. Smallest Greater Multiple Made of Two Digits  
**LeetCode Problem** – Medium  
**SEO Keywords:** *Smallest Greater Multiple Made of Two Digits, LeetCode 1999, Java BFS solution, Python solution, C++ solution, interview algorithm, job interview tips*

---

## 🚀 TL;DR

* Given `k, digit1, digit2`, find the **smallest integer** that  
  1. Is **strictly larger** than `k`,  
  2. Is a **multiple of `k`**,  
  3. Consists **only** of the two digits `digit1` and `digit2`.  
* Return `-1` if such a number does not exist or exceeds `INT_MAX`.

**Solution** – Breadth‑First Search (BFS) over numbers built from the two digits.  
Complexity: `O(2^len)` where `len` ≤ 10 (because `INT_MAX` has 10 digits).  
Memory: `O(2^len)`.

---

## 📄 Problem Statement (From LeetCode)

```text
Given three integers, k, digit1, and digit2, you want to find the smallest integer that is:
1. Larger than k,
2. A multiple of k,
3. Comprised of only the digits digit1 and/or digit2.
Return the smallest such integer. If no such integer exists or the integer exceeds the limit of a signed 32-bit integer (2^31-1), return -1.
```

**Constraints**

- `1 ≤ k ≤ 1000`
- `0 ≤ digit1, digit2 ≤ 9`

---

## 🛠️ Core Idea

The search space is the set of all numbers that can be written with the two allowed digits.  
Because we want the **smallest** such number, exploring numbers in *increasing length* and *lexicographic order* guarantees the first valid number we encounter is the answer.

**Why BFS?**  
* BFS visits numbers by increasing length, so the first match is guaranteed to be minimal.  
* The maximum length we need to explore is 10 (since `2^31‑1` has 10 digits).  
* The branching factor is only 2 (digit1 or digit2), so the state space is tiny (`≤ 2^10 = 1024` nodes).

---

## 🔧 Implementation (Python, Java, C++)

Below are clean, production‑ready implementations for each language.  
Feel free to copy‑paste into your IDE or a LeetCode sandbox.

---

### Python 3 (BFS)

```python
from collections import deque
from typing import List

class Solution:
    def findInteger(self, k: int, digit1: int, digit2: int) -> int:
        if digit1 == digit2 and digit1 == 0:
            return -1          # only zeroes -> impossible

        # Starting digits cannot be zero (leading zeros are invalid)
        start_digits = []
        if digit1 != 0:
            start_digits.append(digit1)
        if digit2 != 0 and digit2 != digit1:
            start_digits.append(digit2)

        q = deque(start_digits)

        INT_MAX = 2 ** 31 - 1

        while q:
            num = q.popleft()
            if num > k and num % k == 0:
                return num

            # Append next digit only if we don't exceed INT_MAX
            if num <= INT_MAX // 10:
                for d in (digit1, digit2):
                    new_num = num * 10 + d
                    if new_num <= INT_MAX:
                        q.append(new_num)

        return -1
```

---

### Java (Iterative BFS)

```java
import java.util.ArrayDeque;
import java.util.Deque;

class Solution {
    public int findInteger(int k, int digit1, int digit2) {
        // Edge case: both digits are zero
        if (digit1 == 0 && digit2 == 0) return -1;

        Deque<Integer> queue = new ArrayDeque<>();

        // Seed queue with non‑zero starting digits
        if (digit1 != 0) queue.offer(digit1);
        if (digit2 != 0 && digit2 != digit1) queue.offer(digit2);

        final long INT_MAX = (1L << 31) - 1;   // 2^31 - 1

        while (!queue.isEmpty()) {
            int cur = queue.poll();

            if (cur > k && cur % k == 0) return cur;

            if (cur <= INT_MAX / 10) {
                // Append digit1
                long next = cur * 10L + digit1;
                if (next <= INT_MAX) queue.offer((int) next);

                // Append digit2 (avoid duplicate when digits equal)
                if (digit2 != digit1) {
                    next = cur * 10L + digit2;
                    if (next <= INT_MAX) queue.offer((int) next);
                }
            }
        }
        return -1;
    }
}
```

---

### C++ (Iterative BFS)

```cpp
#include <queue>
#include <climits>

class Solution {
public:
    int findInteger(int k, int digit1, int digit2) {
        if (digit1 == 0 && digit2 == 0) return -1;

        std::queue<int> q;
        if (digit1 != 0) q.push(digit1);
        if (digit2 != 0 && digit2 != digit1) q.push(digit2);

        const long long INT_MAX_LL = static_cast<long long>(INT_MAX);

        while (!q.empty()) {
            int cur = q.front(); q.pop();

            if (cur > k && cur % k == 0) return cur;

            if (cur <= INT_MAX_LL / 10) {
                long long next = 1LL * cur * 10 + digit1;
                if (next <= INT_MAX_LL) q.push(static_cast<int>(next));

                if (digit2 != digit1) {
                    next = 1LL * cur * 10 + digit2;
                    if (next <= INT_MAX_LL) q.push(static_cast<int>(next));
                }
            }
        }
        return -1;
    }
};
```

---

## 📚 Why This Works – Step‑by‑Step

1. **Start with the smallest non‑zero digits.**  
   Any valid number cannot begin with `0`.  
   Therefore we seed the BFS queue with the two allowed digits if they are non‑zero.

2. **Breadth‑First Exploration.**  
   *Queue* → pop → check → enqueue children (`cur*10 + digit`).  
   Because we always pop the shortest (fewest digits) numbers first, the first number that satisfies the two conditions is guaranteed to be the minimal one.

3. **Prune Overflow.**  
   We never generate numbers larger than `INT_MAX`.  
   Before appending a new digit, we check `cur <= INT_MAX / 10`.  
   This guarantees the resulting number fits in a 32‑bit signed integer.

4. **Early Exit.**  
   As soon as we find a number that is `> k` and divisible by `k`, we return it.  
   No need to explore deeper levels.

5. **Return -1 if Queue Exhausts.**  
   If the BFS completes without finding a valid number, the answer is `-1`.

---

## ⚙️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Building all candidate numbers | **O(2^L)**, where `L ≤ 10` (digits of INT_MAX) | **O(2^L)** for the queue and visited state |

Since `L` is at most 10, the algorithm runs in microseconds even for the worst case.  
It is far below the LeetCode time limit of 1 second.

---

## 🐛 Edge Cases & “Ugly” Scenarios

| Edge Case | Why It Matters | How Our Code Handles It |
|-----------|----------------|-------------------------|
| Both digits are 0 | The only possible number would be 0, which is not > `k` | Immediate `-1` return |
| `digit1 == digit2` | We would enqueue duplicate numbers, potentially causing infinite loops | We check `digit2 != digit1` before enqueuing the second digit |
| Leading zeros allowed? | Numbers like `012` are invalid | Starting digits are never zero |
| Overflow after multiplication | `cur * 10 + d` may exceed `int` | We use `long long` (or `long`) and compare with `INT_MAX` |
| Very large `k` near `INT_MAX` | No numbers > `k` can be constructed | BFS will exhaust the queue and return `-1` |

---

## 🎯 Good, Bad, and Ugly of the Problem

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Problem Clarity** | Clear constraints and objective | N/A | None |
| **Search Space** | Small (≤ 1024 nodes) | N/A | None |
| **Multiple Solutions** | DFS, BFS, DP, BFS with remainder pruning | Some naive DFS implementations can explode | Recursive DFS without memoisation can hit recursion depth limits |
| **Testing Difficulty** | Edge cases are well‑defined | Requires careful handling of `INT_MAX` | The case `k = 1, digits = {0,1}` tests that we never return 0 |
| **Interview Value** | Shows understanding of BFS & number theory | Might be perceived as “trivial” | Requires thoughtful handling of leading zeros and overflow |

**Takeaway:**  
This problem is a great interview toy because it blends simple BFS with number‑theory checks and careful overflow handling. It tests a candidate’s ability to reason about constraints and edge cases without drowning in complex data structures.

---

## 📈 SEO‑Optimized Blog Summary

- **Title:** *“LeetCode 1999 – Smallest Greater Multiple Made of Two Digits (Java, Python, C++)”*  
- **Meta Description:** *“Learn how to solve LeetCode 1999 with BFS in Java, Python, and C++. Discover edge cases, complexity, and interview tips.”*  
- **Keywords:** LeetCode 1999, Smallest Greater Multiple, BFS, Java solution, Python solution, C++ solution, interview algorithm, job interview tips, programming challenge.  
- **Structure:** Intro → Problem Statement → Constraints → BFS Approach → Code (Python, Java, C++) → Edge Cases → Complexity → Good/Bad/Ugly → Conclusion.

Feel free to embed the code blocks and screenshots of LeetCode results to boost engagement and share on LinkedIn or Twitter.

---

## 🎓 Closing Thoughts

* The **BFS** solution is the cleanest and most efficient approach.  
* It guarantees minimality because we explore numbers in order of increasing length.  
* Handling the overflow and the duplicate‑digit edge case makes the code robust.  
* This problem is perfect for interview prep: it’s short, yet tests BFS, overflow handling, and attention to detail.

Good luck with your coding interviews! 🚀