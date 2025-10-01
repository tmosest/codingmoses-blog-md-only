---
title: LeetCode 1228. Missing Number In Arithmetic Progression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap

**LeetCode 1228 – Missing Number In Arithmetic Progression**

> In an array that originally formed an arithmetic progression (AP) one element (never the first or last) was removed.  
> Given the shortened array `arr`, return the missing value.

**Constraints**

| Constraint | Value |
|------------|-------|
| `3 ≤ arr.length ≤ 1000` | ✅ |
| `0 ≤ arr[i] ≤ 10⁵` | ✅ |
| Array is guaranteed to be valid | ✅ |

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[5,7,11,13]` | `9` | Original AP: `[5,7,9,11,13]` |
| `[15,13,12]` | `14` | Original AP: `[15,14,13,12]` |

---

## 2. Solution Overview

*An arithmetic progression of length `N+1` has a constant common difference `d`.  
With one element removed, the remaining length is `N`. The correct difference can be derived as*  

\[
d = \frac{arr[N-1] - arr[0]}{N}
\]

*We then scan once for the first index where the difference deviates from `d`. The missing number is `arr[i] + d`.*

### Key Observations

1. **Only one gap is wrong** – all other adjacent differences equal `d`.  
2. **Missing number is not first/last**, so the wrong gap will always be found in the scan.  
3. `d` can be negative (decreasing AP) – integer division works because `(arr[N-1] - arr[0])` is always divisible by `N` for a valid input.

### Complexity

| Metric | Java / Python / C++ |
|--------|---------------------|
| Time   | **O(N)**            |
| Space  | **O(1)**            |

---

## 3. Code Implementations

Below are clean, self‑contained implementations in **Java**, **Python**, and **C++**.

---

### 3.1 Java

```java
// Java 17
public class Solution {
    public int missingNumberInArithmeticProgression(int[] arr) {
        int n = arr.length;
        int d = (arr[n - 1] - arr[0]) / n;          // common difference of the original AP

        for (int i = 0; i < n - 1; i++) {
            if (arr[i + 1] - arr[i] != d) {         // wrong gap found
                return arr[i] + d;                 // missing number
            }
        }
        // The input guarantees that a missing number exists in the middle,
        // so this line will never be reached.
        throw new IllegalArgumentException("Invalid input");
    }
}
```

---

### 3.2 Python

```python
# Python 3
class Solution:
    def missingNumberInArithmeticProgression(self, arr: list[int]) -> int:
        n = len(arr)
        d = (arr[-1] - arr[0]) // n          # common difference

        for i in range(n - 1):
            if arr[i + 1] - arr[i] != d:
                return arr[i] + d
        raise ValueError("Invalid input")     # should never happen for valid LeetCode cases
```

---

### 3.3 C++

```cpp
// C++17
class Solution {
public:
    int missingNumberInArithmeticProgression(vector<int>& arr) {
        int n = arr.size();
        int d = (arr.back() - arr.front()) / n;  // common difference

        for (int i = 0; i < n - 1; ++i) {
            if (arr[i + 1] - arr[i] != d) {
                return arr[i] + d;              // missing number
            }
        }
        throw std::invalid_argument("Invalid input");
    }
};
```

---

## 4. Blog Article – “Missing Number In Arithmetic Progression: The Good, The Bad, and The Ugly”

> **Meta‑Description:**  
> Master LeetCode 1228 with a step‑by‑step guide. Learn the O(N) solution, Java/Python/C++ implementations, edge‑case handling, and interview‑friendly insights. Perfect your coding interview prep and land that dream job.

---

### Introduction

Every coding interviewer loves a problem that forces you to **think mathematically** and **write clean code**. LeetCode 1228 – *Missing Number In Arithmetic Progression* is exactly that. It’s rated “Easy” but packs a twist: one element (not the first or last) is removed from an AP.  

In this article, we’ll:

1. Break down the problem in plain English.  
2. Reveal the key observation that turns a “guess‑and‑check” approach into a **one‑pass O(N) solution**.  
3. Share production‑ready implementations in **Java, Python, and C++**.  
4. Discuss pitfalls (“the ugly”), best‑practice code patterns (“the good”), and common interview traps (“the bad”).  
5. Offer SEO‑friendly keywords so recruiters searching for “LeetCode 1228 solutions” see this post.

---

### 1. Problem Statement (Plain Language)

> *You have an array that used to be a perfect arithmetic progression. Someone removed a middle element. Find that missing number.*

**Key facts**

| Fact | Why it matters |
|------|----------------|
| Length is at least 3 | We’re guaranteed a middle element exists |
| First and last are unchanged | They anchor the original progression |
| Array is valid | We can rely on integer arithmetic, no corrupt data |

---

### 2. The Good: A One‑Line Insight

The missing element creates **exactly one “bad” gap** between consecutive numbers. Every other gap equals the common difference `d`.

> **Observation:**  
> `d = (arr[last] - arr[first]) / originalLength`  
> Since the original length was `N+1` but we have only `N` elements, `originalLength = N`.

Once we know `d`, we scan once to find the first index `i` where `arr[i+1] - arr[i] != d`.  
The missing number is simply `arr[i] + d`.

This yields a **linear‑time, constant‑space** solution – perfect for interviews and production.

---

### 3. The Bad: Common Mistakes

| Mistake | Symptom | Fix |
|---------|---------|-----|
| Using `arr[i+1] - arr[i]` to infer `d` blindly | Wrong `d` if the missing element is near the start | Compute `d` from *first* and *last* elements and divide by `N` |
| Not handling negative differences | Wrong answer for decreasing AP | Use integer division; works for negative numbers as long as the difference is divisible by `N` |
| Failing to check the *last* index | Runtime error on tiny arrays | Loop until `N-2`; the missing element is never first or last |

---

### 4. The Ugly: Edge Cases that Trip You Up

1. **All numbers equal** – AP with `d = 0`. The formula still works; `d` becomes `0`, and the missing number equals any element.
2. **Large values** – `arr[last] - arr[first]` can reach `10^5`, still comfortably within 32‑bit int.
3. **Negative `d`** – Decreasing sequences (e.g., `[15,13,12]`) produce `d = -1`. Integer division in most languages keeps the sign, so no extra handling needed.

---

### 5. Code Walk‑through (Java)

```java
public int missingNumberInArithmeticProgression(int[] arr) {
    int n = arr.length;
    int d = (arr[n - 1] - arr[0]) / n;          // common difference

    for (int i = 0; i < n - 1; i++) {
        if (arr[i + 1] - arr[i] != d) {         // bad gap found
            return arr[i] + d;                 // missing number
        }
    }
    throw new IllegalArgumentException("Invalid input");
}
```

- **Time**: `O(N)` – single pass.  
- **Space**: `O(1)` – only a few integers.

The same logic applies to the Python and C++ snippets shown earlier.

---

### 6. Interview‑Friendly Tips

- **Explain your thought process**: Mention “common difference” and “only one bad gap” before coding.  
- **Ask clarifying questions**: Confirm that the missing element is never first or last.  
- **Write clean code**: Use descriptive variable names (`commonDiff`, `missing`).  
- **Test edge cases**: Include decreasing AP, zero difference, and small arrays.

---

### 7. SEO & Job‑Search Takeaways

| Keyword | Why it matters | How to use |
|---------|----------------|------------|
| “LeetCode 1228” | Exact problem ID searches | Title, heading, meta tags |
| “Missing Number In Arithmetic Progression” | Long‑tail search | Body, FAQs |
| “Java solution” / “Python solution” / “C++ solution” | Language‑specific queries | Sub‑headings |
| “Coding interview” | Recruiter search | Intro, conclusion |
| “O(N) time” | Complexity focus | Complexity analysis section |

**Pro‑Tip:** Add a “Frequently Asked Questions” section at the end with variations of the problem; recruiters love depth.

---

### 8. FAQ

| Question | Answer |
|----------|--------|
| What if the array is already a perfect AP? | By definition, one element is removed, so this cannot happen. |
| Does integer division always give the correct `d`? | Yes, because `(last - first)` is guaranteed divisible by `N` in a valid input. |
| Can the missing number be negative? | No. `arr[i]` are in `[0, 10⁵]`. The missing number will also lie in that range. |
| Is this approach optimal? | For the given constraints, yes. It cannot be better than `O(N)` because you must inspect each element. |

---

### 9. Final Words

LeetCode 1228 is a **great training ground** for mathematical thinking and quick coding. Mastering it gives you a solid O(N) example you can drop into any coding interview. The implementations above are ready for **coding platforms, GitHub gists, or interview prep notes**.

Now you’re not just solving a problem—you’re **speaking the recruiter’s language**. Good luck on your next interview, and may the *missing number* always reveal itself! 🚀

---

**Call to Action:**  
*If you found this guide helpful, share it with your network and drop a comment below with any twists you’ve encountered on similar problems.*  

---  

**Author:** [Your Name], Software Engineer & Interview Coach.  
**LinkedIn:** https://linkedin.com/in/yourprofile  
**GitHub:** https://github.com/yourrepo

--- 

*Happy coding!* 🚀

--- 

*End of article.*  

---  

**© 2024 All Rights Reserved.**  
--- 

> *Feel free to adapt, remix, or redistribute this article under the MIT license.*