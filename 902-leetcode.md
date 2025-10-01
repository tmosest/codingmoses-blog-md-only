---
title: LeetCode 902. Numbers At Most N Given Digit Set - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 902. Numbers At Most N Given Digit Set – A Complete Multilingual Guide  
**Java | Python | C++** | **Dynamic‑Programming** | **LeetCode**  

> *“Your job interview question today: ‘Write a function that returns the number of positive integers you can form from a set of digits that are ≤ n.’”*  
>  
>  Here’s how you can nail that question in **Java, Python and C++**, and a **blog article** that explains *the good, the bad, and the ugly* of the problem.  The article is **SEO‑optimized** for keywords like **Leetcode 902**, **Numbers At Most N Given Digit Set**, **dynamic programming interview**, and **coding interview tips** – perfect for boosting your résumé and landing your next job.

---

## Table of Contents  

| Section | Description |
|---------|-------------|
| Problem | Statement, constraints, examples |
| Naïve Idea | Brute‑force enumeration – why it fails |
| Good | Efficient digit‑DP approach (O(log n) time, O(1) space) |
| Bad | Pitfalls: overflow, off‑by‑one, string‑int conversion |
| Ugly | Handling edge cases (single digit, large `n`) |
| Full Solutions | Java, Python, C++ implementations |
| Complexity | Big‑O analysis |
| Conclusion | Take‑aways for interviews |
| SEO Checklist | Why this article ranks |  

---

## 1. Problem Statement

> **Given** a sorted array of distinct digits (`'1'`–`'9'`) and an integer `n` (1 ≤ n ≤ 10⁹).  
> **You may use each digit any number of times** to build a positive integer.  
> **Return the count of distinct integers that are ≤ n**.

### Constraints

| Parameter | Constraints |
|-----------|-------------|
| `digits.length` | 1 … 9 |
| `digits[i]` | single digit string `'1'`–`'9'` |
| `digits` | sorted ascending, unique |
| `n` | 1 … 1,000,000,000 |

---

## 2. Naïve Idea – Why It Doesn’t Work

A naïve approach would:

1. Enumerate all numbers you can build up to the length of `n`.
2. For each, check if it’s ≤ n and count it.

However, the number of candidates explodes exponentially:

* `digits.length = 9` → 9¹ + 9² + … + 9⁹ ≈ 196 830 000 possibilities.

That’s far beyond what a single test case on LeetCode can handle in time.  You’ll get a **time‑limit exceeded** error.  We need a smarter counting strategy.

---

## 3. The Good – Digit‑DP Counting

Instead of constructing every integer, we **count** how many are possible **digit‑by‑digit**.

### Intuition

1. **Shorter numbers**  
   Any number with fewer digits than `n` is automatically ≤ n.  
   For each length `len` (1 … k‑1 where k = `len(n)`), we can form `|digits|^len` numbers.

2. **Numbers of the same length**  
   Process each position of `n` from most significant to least:
   * For the current digit `d` of `n`, count all possibilities that are **strictly smaller** than `d`.  
     For every smaller digit we can fill the remaining positions with any of `|digits|` choices, giving  
     `|digits|^(remaining positions)`.
   * If the current digit exists in `digits`, we may continue to the next position.  
     If not, we stop – the prefix already makes any extension too large.

3. **Exact match**  
   If we finish all positions with a valid prefix, we must add one more for the number equal to `n` itself.

This is a classic **digit DP** pattern that runs in **O(k)** where k ≤ 10.

### Step‑by‑Step

1. Convert `n` to a string `N` so we can index its digits.
2. `k = N.length()`.
3. `cnt = Σ_{len=1}^{k-1} |digits|^len`  (shorter numbers).
4. For each position `i` (0 … k‑1):
   * `current = int(N[i])`.
   * For each `digit` in `digits`:
     * If `digit` < `current`: `cnt += |digits|^(k-i-1)`.
     * If `digit` == `current`: set a flag to continue.
   * If no flag: **break** – we cannot match `n` anymore.
5. If we finished the loop: `cnt++` for the exact match `n`.

The algorithm is **O(k · |digits|)** but since both are ≤ 10, it is essentially constant time.

---

## 4. The Bad – Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Integer overflow** | `Math.pow` returns `double`; for large powers it loses precision. | Use integer exponentiation or `long` arithmetic. In Java/C++ just use loops or `powi` helper. |
| **Off‑by‑one** | Forget that numbers with fewer digits are counted **before** the main loop. | Add the loop `for (int len = 1; len < k; ++len)` carefully. |
| **String‑int conversion** | `Integer.valueOf(digit)` repeatedly creates objects. | Convert `digits` once to `int[]` at start. |
| **Leading zeros** | Digits are 1‑9, so leading zeros aren’t an issue. | But still confirm the array doesn’t contain `'0'`. |
| **Large n (10⁹)** | 32‑bit int can hold it, but power calculation may exceed. | Use `long` for intermediate results. |

---

## 5. The Ugly – Edge Cases & Gotchas

| Edge Case | Explanation | Code Snippet |
|-----------|-------------|--------------|
| `digits = ["1"]`, `n = 8` | Only one digit, so only 1‑digit numbers (just 1). | `cnt = 1` (from loop) → `return 1`. |
| `digits = ["9"]`, `n = 9` | One 1‑digit number equal to n. | After loop, `cnt += 1`. |
| `n` has same number of digits as the maximum possible number | Example: `digits = ["9"]`, `n = 999`. | The algorithm counts all 1‑digit and 2‑digit numbers, then `+1` for 999. |
| `n` is 1 000 000 000 (10 digits) | `k = 10`, loops run 10 times. | Ensure `pow(|digits|, k-i-1)` works for `i = 0` (9 remaining digits). |
| `digits` unsorted (contradicts constraints) | Might break the logic if we assume sortedness. | Convert to a set or sort first. |

---

## 6. Full Solutions

### 6.1 Java

```java
import java.util.*;

class Solution {
    public int atMostNGivenDigitSet(String[] digits, int n) {
        // Convert digits to int array once
        int m = digits.length;
        int[] d = new int[m];
        for (int i = 0; i < m; ++i) d[i] = digits[i].charAt(0) - '0';

        String N = String.valueOf(n);
        int k = N.length();

        long count = 0;

        // Numbers with fewer digits than n
        long pow = 1;
        for (int len = 1; len < k; ++len) {
            pow *= m;        // m^len
            count += pow;
        }

        // Numbers with same number of digits
        for (int i = 0; i < k; ++i) {
            int cur = N.charAt(i) - '0';
            boolean equalFound = false;
            for (int val : d) {
                if (val < cur) {
                    // Remaining positions can be any digit
                    long remainingPow = 1;
                    for (int r = 0; r < k - i - 1; ++r) remainingPow *= m;
                    count += remainingPow;
                } else if (val == cur) {
                    equalFound = true;
                }
            }
            if (!equalFound) return (int) count;   // cannot match n anymore
        }

        // If we matched all digits, count n itself
        return (int) (count + 1);
    }
}
```

**Time** O(log n) **Space** O(1) (ignoring input size)

---

### 6.2 Python

```python
class Solution:
    def atMostNGivenDigitSet(self, digits: List[str], n: int) -> int:
        # Pre‑convert to integers
        digs = [int(d) for d in digits]
        m = len(digs)

        N = str(n)
        k = len(N)

        count = 0

        # Numbers with fewer digits
        pow_val = 1
        for _ in range(1, k):
            pow_val *= m
            count += pow_val

        # Numbers with same length
        for i, ch in enumerate(N):
            cur = int(ch)
            equal_found = False
            for val in digs:
                if val < cur:
                    # Remaining positions any digit
                    rem = k - i - 1
                    count += m ** rem
                elif val == cur:
                    equal_found = True
            if not equal_found:
                return count

        # Exact match n
        return count + 1
```

**Time** O(log n) **Space** O(1)

---

### 6.3 C++

```cpp
class Solution {
public:
    long long powi(long long base, int exp) {
        long long res = 1;
        for (int i = 0; i < exp; ++i) res *= base;
        return res;
    }

    int atMostNGivenDigitSet(vector<string>& digits, int n) {
        int m = digits.size();
        vector<int> digs(m);
        for (int i = 0; i < m; ++i) digs[i] = digits[i][0] - '0';

        string N = to_string(n);
        int k = N.size();

        long long cnt = 0;

        // Shorter lengths
        long long pow_val = 1;
        for (int len = 1; len < k; ++len) {
            pow_val *= m;
            cnt += pow_val;
        }

        // Same length
        for (int i = 0; i < k; ++i) {
            int cur = N[i] - '0';
            bool equalFound = false;
            for (int val : digs) {
                if (val < cur) cnt += powi(m, k - i - 1);
                else if (val == cur) equalFound = true;
            }
            if (!equalFound) return cnt;
        }

        return cnt + 1;          // include n itself
    }
};
```

---

## 7. Complexity Analysis

| Implementation | Time | Space |
|----------------|------|-------|
| Java | O(log n) | O(1) |
| Python | O(log n) | O(1) |
| C++ | O(log n) | O(1) |

Because `n` ≤ 10⁹, `log n` ≤ 10, the algorithm runs in **well under 1 ms** in practice.

---

## 8. Conclusion – Interview Take‑aways

1. **Recognise digit DP** – it’s a powerful pattern for problems involving “counting numbers up to n”.
2. **Avoid brute force** – exponential blow‑up kills runtime.
3. **Pre‑process inputs** – converting strings to ints once saves both time and memory.
4. **Mind overflow** – use `long` (Java) or `long long` (C++); Python’s int is unbounded.
5. **Explain your logic clearly** – interviewers love a concise explanation of *why* the algorithm works.

---

## 9. SEO Checklist

| Element | Why it matters |
|---------|----------------|
| Title contains “Leetcode 902 – Numbers At Most N Given Digit Set” | Google picks the exact keyword. |
| H1 & H2 tags | Structured content improves readability and ranking. |
| Meta description | “Efficient Java, Python, and C++ solution for Leetcode 902 – digit‑DP counting.” |
| Internal links | Link to “Dynamic Programming Interview” page on your site. |
| External links | Cite the official LeetCode problem page. |
| Backlinks | Invite readers to share the article on LinkedIn / Twitter. |

---

## 10. Final Words

> **Leetcode 902** is a textbook example of a problem that looks simple but hides a huge search space.  
> Master the **digit‑DP counting** trick, avoid the common pitfalls, and you’ll not only solve it in time but also impress interviewers with a clean, optimal solution.  

Happy coding, and good luck on your next interview!

---

### References  

1. LeetCode – Problem 902.  
2. TopCoder “Digit DP” tutorials.  
3. “Programming Interviews Exposed” – B. Leighton.  

--- 

*Feel free to copy the code snippets, publish the article, or use it as a study guide for your own interview preparation.*  

--- 

**Author:** *Your Name – Senior Software Engineer, Coding Interview Coach*  

**Published:** *[Date]*  

**Keywords:** Leetcode 902, Numbers At Most N Given Digit Set, dynamic programming interview, coding interview tips, Java DP solution, Python DP solution, C++ DP solution.  

--- 

*If you liked this guide, share it, comment below with your own edge‑case experiences, and let’s keep learning together!*