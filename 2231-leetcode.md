---
title: LeetCode 2231. Largest Number After Digit Swaps by Parity - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 2231. Largest Number After Digit Swaps by Parity – Code + Interview‑Ready Blog

**LeetCode ID:** 2231  
**Difficulty:** Easy  
**Tags:** Array, Greedy, Sorting, Priority Queue, String, Interview Question, Java, Python, C++

---

## 📌 Problem Summary

You are given a positive integer `num`.  
You can swap any two digits that have the *same parity* (both even or both odd).  
After any number of swaps, return the **largest possible integer** you can form.

> **Example**  
> `num = 1234 → 3412`  
> `num = 65875 → 87655`

> **Constraints**  
> `1 <= num <= 10^9`  (fits in 32‑bit `int`)

---

## 🧠 Key Insight

The relative order of the *odd* digits and the *even* digits never changes – you can only reorder digits **within each parity class**.  
So the optimal strategy is:

1. **Extract** the odd and even digits separately.
2. **Sort** each list in **descending** order (so the biggest digits are first).
3. **Rebuild** the number by replacing each original digit with the next largest digit of the same parity.

The result is guaranteed to be the maximum possible integer.

---

## ⚙️ Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Extract + sort | `O(d log d)` (d = #digits, ≤ 10) | `O(d)` |
| Rebuild | `O(d)` | `O(1)` |

With `d ≤ 10` the solution is trivial in time and memory.

---

## 🎯 Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

> **Tip** – All three solutions convert the number to a string to work with digits, which keeps the code readable and avoids manual modulo/division loops.

---

### 1️⃣ Java (Java 17)

```java
import java.util.*;

class Solution {
    public int largestInteger(int num) {
        // Convert to char array for easy swapping
        char[] digits = String.valueOf(num).toCharArray();

        // Greedy: for each position find the largest same‑parity digit to the right
        for (int i = 0; i < digits.length; i++) {
            for (int j = i + 1; j < digits.length; j++) {
                if (digits[j] > digits[i] &&
                    (digits[j] - digits[i]) % 2 == 0) {
                    // swap
                    char tmp = digits[i];
                    digits[i] = digits[j];
                    digits[j] = tmp;
                }
            }
        }
        return Integer.parseInt(new String(digits));
    }
}
```

> **Why this works:**  
> The double loop guarantees that every time we look rightward we place the largest possible digit of the same parity at the current position. This is effectively the same as sorting each parity group in descending order.

---

### 2️⃣ Python (Python 3.11)

```python
class Solution:
    def largestInteger(self, num: int) -> int:
        s = list(str(num))
        n = len(s)

        for i in range(n):
            for j in range(i + 1, n):
                if s[j] > s[i] and (int(s[j]) - int(s[i])) % 2 == 0:
                    s[i], s[j] = s[j], s[i]

        return int("".join(s))
```

> **Pythonic note:**  
> The greedy two‑loop approach is very readable. For larger inputs you could use the *sort‑by‑parity* method, but with at most 10 digits the simple solution is perfect.

---

### 3️⃣ C++ (C++17)

```cpp
class Solution {
public:
    int largestInteger(int num) {
        string s = to_string(num);
        int n = s.size();

        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (s[j] > s[i] && ((s[j] - s[i]) % 2 == 0)) {
                    swap(s[i], s[j]);
                }
            }
        }
        return stoi(s);
    }
};
```

> **C++ 17** – Uses `std::to_string`, `std::stoi`, and `std::swap`.  
> The modulo trick `(s[j] - s[i]) % 2` works because characters `'0'`–`'9'` differ by the same amount as their numeric values.

---

## 📚 Blog Article – “Largest Number After Digit Swaps by Parity: The Good, The Bad, and the Ugly”

### Title

> **Largest Number After Digit Swaps by Parity – Master the Interview Problem (Java, Python, C++)**

### Meta Description

> Solve LeetCode 2231 – Largest Number After Digit Swaps by Parity – with clean Java, Python, and C++ solutions. Understand the greedy strategy, pitfalls, and interview tips. Boost your coding interview score!

---

### Introduction

If you’re hunting for a *real‑world* interview problem that tests both your **array manipulation** skills and your **algorithmic intuition**, look no further than LeetCode 2231: **Largest Number After Digit Swaps by Parity**. It’s an “easy” problem on the platform, but it hides subtle nuances that can trip up even seasoned programmers.

In this article, we’ll dive deep into:

- The core greedy strategy that guarantees the maximum number.
- Common mistakes (the “bad” and the “ugly”).
- Three production‑ready solutions (Java, Python, C++).
- How to explain the solution in an interview setting.

Let’s break it down!

---

### The Good – Why It’s Elegant

| Aspect | Why It Works |
|--------|--------------|
| **Greedy** | Each swap is local: we always put the largest possible digit (of the same parity) to the leftmost available spot. |
| **Parity Preservation** | The parity constraint keeps the problem solvable with a simple sort‑by‑group approach. |
| **O(d log d)** | With `d ≤ 10` digits, the algorithm is essentially constant‑time. |
| **No Big Integer Needed** | The final result fits within a 32‑bit signed integer (`≤ 10^9`). |

The elegance lies in recognizing that swapping digits of the same parity is equivalent to independently sorting the odd and even digits in descending order and then re‑assembling them according to the original parity pattern.

---

### The Bad – Pitfalls You Should Avoid

1. **Assuming You Can Swap Any Two Digits**  
   ❌ Swapping a `1` (odd) with a `4` (even) is illegal – many beginner solutions mistakenly do this.

2. **Using Integer Arithmetic Instead of String**  
   ❌ Working directly with integer digits (`num % 10`) can lead to bugs when you try to swap digits in place.

3. **Ignoring Leading Zeroes**  
   While the problem guarantees `num >= 1`, you might think about numbers that start with `0`. A greedy algorithm that just reorders digits may inadvertently create a leading zero if you’re not careful.

4. **Over‑Optimizing for Larger Numbers**  
   Because `num` is capped at `10^9`, there’s no need for a heap or counting sort. Over‑engineering can make your solution harder to understand.

---

### The Ugly – Complexity Traps

- **Recursive/Backtracking**  
  Some naive approaches attempt to explore every permutation of odd/even swaps, leading to exponential time (`O(d!)`). This is *terrible* for even `d = 10`.

- **Using BigInteger**  
  A common mistake is to parse the final string back into `BigInteger` to avoid overflow, but the constraints make it unnecessary—and `BigInteger` is slower.

- **Wrong Parity Check**  
  Using `(digit1 + digit2) % 2 == 0` is *incorrect* for parity comparison. The correct test is `(digit1 - digit2) % 2 == 0` or `digit1 % 2 == digit2 % 2`.

---

### Interview‑Ready Explanation

> **“I first separate the digits into two lists—odds and evens. Then I sort each list descending so the biggest digits are at the front. Finally, I walk through the original number, replacing each digit with the next biggest digit of the same parity from the corresponding list. The result is the largest possible integer.”**

Explain that the algorithm is *greedy* because at each step we pick the locally optimal choice (the largest same‑parity digit to the left), and due to the independent parity groups this local optimum is also global.

---

### Code Walkthrough

#### Java

```java
char[] digits = String.valueOf(num).toCharArray();
for (int i = 0; i < digits.length; i++)
    for (int j = i + 1; j < digits.length; j++)
        if (digits[j] > digits[i] &&
            (digits[j] - digits[i]) % 2 == 0) {
            char t = digits[i];
            digits[i] = digits[j];
            digits[j] = t;
        }
return Integer.parseInt(new String(digits));
```

*Why it’s clean:* Two nested loops, a simple parity check, and an in‑place swap.

#### Python

```python
s = list(str(num))
for i in range(len(s)):
    for j in range(i+1, len(s)):
        if s[j] > s[i] and (int(s[j]) - int(s[i])) % 2 == 0:
            s[i], s[j] = s[j], s[i]
return int("".join(s))
```

*Pythonic twist:* Using `swap` syntax `s[i], s[j] = s[j], s[i]`.

#### C++

```cpp
string s = to_string(num);
for (int i = 0; i < s.size(); ++i)
    for (int j = i+1; j < s.size(); ++j)
        if (s[j] > s[i] && ((s[j] - s[i]) % 2 == 0))
            swap(s[i], s[j]);
return stoi(s);
```

*Highlights:* `std::swap` keeps the code concise.

---

### Bonus: Sort‑by‑Parity Approach (Alternative)

If you want to emphasize *sorting* rather than the two‑loop greedy, you can do:

```java
List<Character> odds = new ArrayList<>();
List<Character> evens = new ArrayList<>();

for (char c : digits) {
    if ((c - '0') % 2 == 0) evens.add(c);
    else odds.add(c);
}
odds.sort(Collections.reverseOrder());
evens.sort(Collections.reverseOrder());

int o = 0, e = 0;
for (int i = 0; i < digits.length; i++) {
    if ((digits[i] - '0') % 2 == 0) digits[i] = evens.get(e++);
    else digits[i] = odds.get(o++);
}
```

> **Why this is still “good”** – It’s slightly longer but makes the *independence of parity groups* crystal clear. In an interview, it might actually impress the interviewer by showing you’ve considered multiple solution paths.

---

### 🚀 Final Takeaway

LeetCode 2231 is a *golden* interview problem. Mastering it gives you:

- Confidence handling constraints and corner cases.
- Experience explaining greedy logic clearly.
- A reusable pattern (group → sort → rebuild) that appears in many array problems.

Happy coding, and good luck on your next interview! 🚀

---

### Resources & Further Reading

- [LeetCode 2231 – Problem Statement](https://leetcode.com/problems/largest-number-after-digit-swaps-by-parity/)
- [Greedy Algorithms – Coursera](https://www.coursera.org/learn/greedy-algorithms)
- [Parity in Programming – Stack Overflow Q&A](https://stackoverflow.com/questions/what-is-parity)

---

### 📈 SEO Boost

- **Keywords**: “LeetCode 2231”, “Largest Number After Digit Swaps by Parity”, “Java solution”, “Python solution”, “C++ solution”, “greedy algorithm”, “coding interview tips”, “array manipulation”, “parity constraint”, “algorithmic intuition”.
- **Headers**: Use `<h1>`, `<h2>`, `<h3>`, and `<h4>` to structure content for readability and search engines.
- **Images**: Include a small diagram of “odd / even groups” and a flowchart of the algorithm.
- **Backlinks**: Reference official LeetCode, Medium, and GitHub repositories for further exploration.

With the code and the interview guide above, you’re ready to ace LeetCode 2231 and showcase your problem‑solving chops in any technical interview. Happy coding!