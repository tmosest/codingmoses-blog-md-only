---
title: LeetCode 556. Next Greater Element III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Next Greater Element III – A “Good, Bad & Ugly” Deep‑Dive (Java | Python | C++)  
*LeetCode 556 – Medium – “Find the Next Bigger Integer with the Same Digits”*

---

### Why This Problem Rocks Your Resume

- **LeetCode Top‑10 Interview Questions** – Recruiters on LinkedIn, Indeed & Hired frequently list it.
- **Shows Core Algorithm Skills** – Permutations, greedy & stack reasoning.
- **Language Agnostic** – Demonstrate proficiency in *Java, Python, C++* (and even Go, Rust, etc.).
- **Hands‑On Complexity Analysis** – O(n) time & O(1) extra space.

> **SEO keywords**: *Next Greater Element III, LeetCode 556, interview problem, algorithm, coding interview, job interview, Java coding interview, Python coding interview, C++ coding interview, software engineer interview.*

---

## Problem Recap

> **Input**: Positive integer `n` (1 ≤ n < 2³¹).  
> **Goal**: Find the *smallest* integer that:
> 1. Uses exactly the same decimal digits as `n`.
> 2. Is greater than `n`.  
> 3. Fits in a signed 32‑bit integer; otherwise return `-1`.

**Examples**

| n | Output |
|---|--------|
| 12 | 21 |
| 21 | -1 |
| 123 | 132 |
| 115 | 151 |
| 999999 | -1 |

---

## High‑Level Strategy – “Next Permutation” of Digits

The task is precisely the *next lexicographic permutation* of the digit sequence.

1. **Find the pivot** – the first digit (from right) that is **smaller** than its right neighbour.  
   - If none exists → digits are in descending order → no greater number exists.
2. **Find the successor** – the smallest digit to the right of the pivot that is **greater** than the pivot.  
3. **Swap** pivot & successor.
4. **Reverse** (or sort ascending) the suffix right of the pivot – this yields the smallest larger number.

The whole routine is *O(L)* where `L` is the number of digits (≤ 10 for 32‑bit ints).  
Space is *O(1)* – we work in‑place on a character array.

---

## Edge‑Case Traps (“The Ugly”)

| Case | Why it trips you | Fix |
|------|------------------|-----|
| **Overflow** | Even if the next permutation exists, the result can exceed `INT_MAX`. | Parse into a 64‑bit variable (`long` in Java/C++ or `int64` in Python) and compare against `2³¹‑1`. |
| **All Same Digits** | `1111` → no pivot. | Return `-1` immediately. |
| **Trailing Zeros** | `120` → next is `201` (note the swap order). | Suffix reversal handles zeros automatically. |
| **Negative Numbers** | Problem guarantees positive, but defensive coding helps. | Check `n < 0` and return `-1`. |
| **Large Numbers** | `2147483647` → next exists but overflows. | Use 64‑bit temporary and reject if > `INT_MAX`. |

---

## Code Implementations

### 1. Java (Standard Library, `int`)

```java
public class Solution {
    public int nextGreaterElement(int n) {
        char[] digits = Integer.toString(n).toCharArray();

        // Step 1: Find pivot
        int i = digits.length - 2;
        while (i >= 0 && digits[i] >= digits[i + 1]) i--;
        if (i < 0) return -1; // already maximal permutation

        // Step 2: Find successor
        int j = digits.length - 1;
        while (j >= 0 && digits[j] <= digits[i]) j--;

        // Step 3: Swap
        swap(digits, i, j);

        // Step 4: Reverse suffix
        reverse(digits, i + 1);

        // Overflow check
        long result = Long.parseLong(new String(digits));
        return result > Integer.MAX_VALUE ? -1 : (int) result;
    }

    private void swap(char[] a, int i, int j) {
        char t = a[i]; a[i] = a[j]; a[j] = t;
    }

    private void reverse(char[] a, int start) {
        int l = start, r = a.length - 1;
        while (l < r) swap(a, l++, r--);
    }
}
```

### 2. Python (Elegant, 3 lines inside loop)

```python
class Solution:
    def nextGreaterElement(self, n: int) -> int:
        s = list(str(n))
        i = len(s) - 2
        while i >= 0 and s[i] >= s[i+1]:
            i -= 1
        if i < 0: return -1

        j = len(s) - 1
        while s[j] <= s[i]:
            j -= 1

        s[i], s[j] = s[j], s[i]
        s[i+1:] = reversed(s[i+1:])   # reverse suffix

        val = int(''.join(s))
        return val if val <= 2**31-1 else -1
```

### 3. C++ (Fast, STL)

```cpp
class Solution {
public:
    int nextGreaterElement(int n) {
        string s = to_string(n);
        int i = s.size() - 2;
        while (i >= 0 && s[i] >= s[i+1]) --i;
        if (i < 0) return -1;

        int j = s.size() - 1;
        while (s[j] <= s[i]) --j;

        swap(s[i], s[j]);
        reverse(s.begin() + i + 1, s.end());

        long long val = stoll(s);
        return (val > INT_MAX) ? -1 : static_cast<int>(val);
    }
};
```

---

## Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Find pivot | O(L) | O(1) |
| Find successor | O(L) | O(1) |
| Swap & reverse | O(L) | O(1) |
| Final parse & overflow check | O(L) | O(1) |

Total: **O(L)** time, **O(1)** auxiliary space.  
With `L ≤ 10` the algorithm is effectively *constant* for all inputs.

---

## Interview‑Ready Discussion

1. **Why next permutation?**  
   - The digits are a string of characters; generating all permutations is factorial‑time – impossible for 10 digits.  
   - Greedy “find the first decreasing pair” guarantees the *next* lexicographic order.

2. **Alternate Approaches**  
   - **Stack**: push digits, pop until you find a smaller digit, etc. (essentially same algorithm).  
   - **Sorting the suffix**: instead of reversing, sort the suffix ascending – same result but O(L log L).  
   - **Bitmask enumeration**: only works for very small digit counts.

3. **Common Mistakes**  
   - Forgetting to handle the overflow.  
   - Swapping wrong indices (off‑by‑one).  
   - Reversing the wrong part (pivot inclusive).

4. **Testing Strategy**  
   - All digits descending → -1.  
   - All digits ascending → trivial next.  
   - Single digit → -1.  
   - Edge of 32‑bit boundary (`2147483638` → `2147483863`).  
   - Numbers with duplicate digits (`115` → `151`).

---

## “The Good, The Bad & The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Concept** | LeetCode staple, teaches permutations | Might look too simple | People over‑optimize (e.g., fancy trees) |
| **Implementation** | One pass, in‑place | Requires careful index handling | Overflow mis‑handling leading to WA |
| **Readability** | Clear & concise | Needs comments for pivot logic | No early return → hard to follow |
| **Performance** | O(10) constant | None | Excessive memory allocation (e.g., converting to list of ints) |

---

## Takeaway for the Job Hunt

- **Show the pattern**: *next permutation* is a reusable recipe (used in problems like “Next Permutation” or “Largest Number”).  
- **Talk about edge cases**: Overflow is a classic interview question; handling it shows attention to detail.  
- **Discuss time/space**: Recruiters love when candidates articulate *O(n)* vs *O(n log n)* trade‑offs.  
- **Demonstrate language flexibility**: Provide the same logic in Java, Python, C++ – signals strong cross‑platform skills.

---

## Final Thoughts

Solving LeetCode 556 feels like unlocking a secret level in your interview arsenal. By mastering the next‑permutation logic and anticipating the pitfalls, you’ll impress recruiters who value clean, efficient, and bug‑free code.

Happy coding, and may the interview gods bless you with the next‑great‑job! 🌟

--- 

**Keywords for SEO**: Next Greater Element III, LeetCode 556, interview algorithm, next permutation, job interview coding, Java interview, Python coding interview, C++ interview, software engineer interview, algorithmic problem solving.