---
title: LeetCode 3216. Lexicographically Smallest String After a Swap - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 3216 – Lexicographically Smallest String After a Swap  
**Easy** | **String**  

> **Problem**  
> Given a string `s` that contains only digits, you may perform **at most one** swap of *adjacent* digits that have the same parity (both odd or both even).  
> Return the lexicographically smallest string that can be obtained.

---

### Why This Problem Matters
- **Interview Signal:** Leetcode “Easy” but requires a clear understanding of *parity* and *lexicographic ordering*.
- **Coding Skill:** Demonstrates greedy reasoning, one‑pass linear traversal, and minimal‑change algorithmic design.
- **Career Boost:** Solving it showcases your ability to read a problem, reason quickly, and deliver an optimal O(n) solution – a hallmark trait that recruiters love.

---

## ✅ Solution Overview (Greedy)

1. **Traverse the string from left to right.**  
2. At position `i`, compare digit `s[i]` and the next digit `s[i+1]`.
3. If:
   * they have **the same parity** (`(s[i]-'0') % 2 == (s[i+1]-'0') % 2`) **and**
   * `s[i] > s[i+1]` (i.e., the left digit is larger than the right one)
   
   then **swap** them and **stop** – we’re allowed only one swap.
4. If the loop finishes without a swap, return the original string.

Because we always perform the *first* possible beneficial swap, the resulting string is guaranteed to be the lexicographically smallest one obtainable with a single adjacent parity‑swap. This greedy choice is optimal: any later swap would either not improve the prefix or would require more than one swap.

---

## 📦 Implementation (All Languages)

### Java

```java
// 3216. Lexicographically Smallest String After a Swap
public class Solution {
    public String getSmallestString(String s) {
        int n = s.length();
        char[] arr = s.toCharArray();

        for (int i = 0; i < n - 1; i++) {
            int a = arr[i] - '0';
            int b = arr[i + 1] - '0';

            if ((a % 2 == b % 2) && (a > b)) {
                // swap once and exit
                char temp = arr[i];
                arr[i] = arr[i + 1];
                arr[i + 1] = temp;
                break;
            }
        }
        return new String(arr);
    }
}
```

---

### Python

```python
# 3216. Lexicographically Smallest String After a Swap
class Solution:
    def getSmallestString(self, s: str) -> str:
        arr = list(s)
        n = len(arr)

        for i in range(n - 1):
            a = int(arr[i])
            b = int(arr[i + 1])

            if a % 2 == b % 2 and a > b:
                arr[i], arr[i + 1] = arr[i + 1], arr[i]
                break

        return ''.join(arr)
```

---

### C++

```cpp
// 3216. Lexicographically Smallest String After a Swap
class Solution {
public:
    string getSmallestString(string s) {
        int n = s.size();

        for (int i = 0; i < n - 1; ++i) {
            int a = s[i] - '0';
            int b = s[i + 1] - '0';

            if ((a % 2 == b % 2) && a > b) {
                swap(s[i], s[i + 1]);
                break;          // only one swap allowed
            }
        }
        return s;
    }
};
```

---

## 🤔 Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Traversal (single loop) | **O(n)** | **O(1)** (in‑place) |
| String conversion (Java) | **O(n)** | **O(n)** (character array) |

`n ≤ 100`, so all solutions run in negligible time.

---

## 🏆 The Good, The Bad, and The Ugly

| Category | What Works | Potential Pitfalls | Best Practice |
|----------|------------|--------------------|---------------|
| **Good** | • One‑pass greedy gives linear time. <br>• No need for extra data structures. <br>• Works for all edge cases (`s` already minimal, no valid swap, swap at the end). | | |
| **Bad** | • Mis‑interpreting “adjacent” can lead to wrong swaps. <br>• Forgetting to break after the first swap violates the “at most one” rule. | • Swapping two *non‑adjacent* same‑parity digits leads to wrong answer. <br>• Over‑optimizing with two pointers may add unnecessary complexity. | |
| **Ugly** | • Using string concatenation inside the loop in Python (`s = s[:i] + s[i+1] + s[i] + s[i+2:]`) can produce O(n²) behavior. <br>• In Java, re‑creating a new string in each iteration would be wasteful. | Avoid repeated string creation; use character arrays or StringBuilder. | |

---

## 🚀 SEO‑Optimized Blog Article: “Master Leetcode 3216 – Lexicographically Smallest String After a Swap”

> **Title:** “Solve Leetcode 3216 in 5 Minutes: Lexicographically Smallest String After a Swap (Java, Python, C++)”

> **Meta Description:** “Learn how to crack Leetcode 3216 in under 5 minutes. Grab the full greedy solution, code snippets in Java, Python, and C++, plus interview tips. Get the job you want!”

> **Target Keywords:** *Leetcode 3216, Lexicographically Smallest String, swap adjacent digits, parity swap, interview coding problem, Java solution, Python solution, C++ solution, algorithm interview tips*

### 1. Hook – Why This Problem Rocks

- **Easy rating, hard in interviews** – People overlook it because it seems trivial, but recruiters love it as a quick sanity check.
- **Real‑world application** – Parity swapping is similar to “bubble‑sort” optimizations in embedded systems.

### 2. Problem Statement (Condensed)

> Given a string of digits, perform *at most one* adjacent swap between two same‑parity digits to produce the smallest possible string lexicographically.

### 3. Key Insight – Greedy First Good Swap

- The smallest string is determined by the earliest position where a swap can reduce the left digit.
- Swapping the *first* eligible pair is always optimal.
- This reduces the problem to a single linear scan.

### 4. Step‑by‑Step Code Walkthrough (Java)

```java
for (int i = 0; i < n - 1; i++) {
    if (sameParity(s[i], s[i+1]) && s[i] > s[i+1]) {
        swap(s, i, i+1);
        break;
    }
}
```

Explain `sameParity` and `swap` helpers.

### 5. Full Code (Java, Python, C++)

Show code blocks (above) with brief notes on language specifics.

### 6. Edge Cases Checklist

| Case | Reason | Result |
|------|--------|--------|
| No valid swap | All adjacent digits differ in parity or already minimal | Original string |
| Swap at last pair | Must still return the smallest string | Swapped string |
| Leading zeros | Lexicographic comparison still applies | Works correctly |

### 7. Time & Space Complexity

- **O(n)** time, **O(1)** space (aside from the output string).

### 8. Interview Tips

- **Explain your greedy reasoning.** Recruiters look for *why* not just *what*.
- **Mention the “at most one swap” constraint** explicitly when discussing your loop.
- **Show off language‑specific optimizations** (e.g., using `StringBuilder` in Java, `char[]` in C++).

### 9. Wrap‑Up

- Summarize the elegant one‑pass solution.
- Encourage readers to practice variations (non‑adjacent swaps, multiple swaps).
- Link to further resources and a call to action: “Try it on Leetcode, upvote, share.”

### 10. Call to Action & SEO

- **Share** on LinkedIn with #Leetcode #CodingInterview.
- **Subscribe** to your YouTube channel for more quick solutions.
- Include a link to the GitHub repository containing all three solutions.

---

## 🎯 Takeaway

- The problem is a **classic greedy** exercise.
- The solution is **O(n)** and trivially implementable in any major language.
- By mastering this, you showcase the ability to **read problem constraints, derive a minimal‑change strategy, and implement cleanly**—exactly what recruiters are hunting for.

Happy coding, and may that interview be a breeze!