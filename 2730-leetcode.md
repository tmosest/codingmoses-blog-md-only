---
title: LeetCode 2730. Find the Longest Semi-Repetitive Substring - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

> **2730. Find the Longest Semi‑Repetitive Substring**  
> **Difficulty:** Medium  
> **Input:** a digit string `s` (`1 ≤ |s| ≤ 50`, each char is `'0'…'9'`)  
> **Output:** the length of the longest contiguous substring that contains **at most one** adjacent pair of identical digits.  

A substring is *semi‑repetitive* if there are **zero or one** indices `i` such that  
`substr[i] == substr[i+1]`.  
Examples:  
- `"0010"` – one pair (`00`) → valid  
- `"00101022"` – two pairs (`00`, `22`) → invalid  

We need the **maximum possible length** of such a substring.



--------------------------------------------------------------------

## 2.  Algorithm – Sliding Window (Two‑Pointer)

The classic “at most one violation” problem can be solved with a **single pass** using a sliding window:

| Step | Action |
|------|--------|
| 1. | Keep two indices `left` and `right` – the current window `[left, right]`. |
| 2. | Keep a counter `repeats` – how many adjacent equal pairs are inside the window. |
| 3. | Move `right` from `0` to `n‑1`. After moving, check if `s[right] == s[right‑1]`. If so, increment `repeats`. |
| 4. | While `repeats > 1`, move `left` forward one step. If moving past a pair (`s[left] == s[left‑1]`) decrement `repeats`. |
| 5. | Update answer with `right - left + 1`. |

Because each index is moved at most once, the algorithm runs in **O(n)** time and uses **O(1)** extra space.



--------------------------------------------------------------------

## 3.  Reference Implementations

Below are clean, idiomatic solutions in **Java, Python, and C++**.  
All follow the same sliding‑window logic described above.

> **Tip:**  
> Use `charAt` (Java), direct indexing (Python, C++) and be careful with off‑by‑one errors when checking the pair that ends at the new `right` or starts at the new `left`.

---

### 3.1 Java

```java
/**
 *  LeetCode 2730 – Longest Semi‑Repetitive Substring
 *
 *  Time Complexity : O(n)
 *  Space Complexity: O(1)
 */
class Solution {
    public int longestSemiRepetitiveSubstring(String s) {
        int n = s.length();
        int left = 0;       // left boundary of the window
        int repeats = 0;    // number of adjacent equal pairs in the window

        for (int right = 1; right < n; right++) {
            // Did we create a new adjacent pair by adding s[right]?
            if (s.charAt(right) == s.charAt(right - 1)) {
                repeats++;
            }

            // Shrink window until we have at most one pair
            while (repeats > 1) {
                // Moving left past a pair removes it from the window
                if (s.charAt(left) == s.charAt(left + 1)) {
                    repeats--;
                }
                left++;
            }
        }
        // The longest window length is n - left (right ends at n-1)
        return n - left;
    }
}
```

---

### 3.2 Python

```python
class Solution:
    def longestSemiRepetitiveSubstring(self, s: str) -> int:
        n = len(s)
        left = 0           # left boundary
        repeats = 0        # number of adjacent equal pairs

        for right in range(1, n):
            if s[right] == s[right - 1]:
                repeats += 1

            while repeats > 1:
                if s[left] == s[left + 1]:
                    repeats -= 1
                left += 1

        return n - left
```

---

### 3.3 C++

```cpp
class Solution {
public:
    int longestSemiRepetitiveSubstring(string s) {
        int n = s.size();
        int left = 0;       // left boundary of the window
        int repeats = 0;    // number of adjacent equal pairs

        for (int right = 1; right < n; ++right) {
            if (s[right] == s[right - 1]) repeats++;

            while (repeats > 1) {
                if (s[left] == s[left + 1]) repeats--;
                left++;
            }
        }
        return n - left;   // window length
    }
};
```

All three codes run in linear time, use constant auxiliary memory, and are ready for submission.



--------------------------------------------------------------------

## 4.  Blog Article – “The Good, The Bad, and The Ugly of Semi‑Repetitive Substrings”

> **Keyword‑Rich Title:**  
> *“Semi‑Repetitive Substrings: The Good, The Bad, The Ugly – A LeetCode 2730 Deep‑Dive (Java, Python, C++)”*  
>  
> *Why this matters:* This problem is a textbook sliding‑window challenge that interviewers love because it tests:  
> 1. **Array/string traversal** – can you keep an eye on adjacent pairs?  
> 2. **Two‑pointer technique** – can you shrink/expand a window efficiently?  
> 3. **Edge‑case handling** – what about strings of identical digits or length‑1?  
>  
> Landing this on your résumé demonstrates mastery of *O(n)* algorithms, a must‑have skill for software engineers in production systems.

---

### 4.1 The Good

1. **Linear Complexity**  
   The solution visits each character only once. In interviews, candidates often fall into quadratic traps. Showing a **O(n)** approach instantly signals algorithmic maturity.

2. **Space‑Efficient**  
   Only a few integer variables are used – a constant amount of extra memory. Recruiters appreciate solutions that can scale to very large inputs.

3. **Conceptual Clarity**  
   The sliding window is a *single, clean idea*: “move right, count violations, shrink left while violations>1.” No nested loops, no auxiliary data structures, easy to explain and debug.

4. **Language‑Independent**  
   The same logic works in Java, Python, C++, Go, etc. This portability demonstrates deep understanding of algorithmic patterns beyond syntactic sugar.

---

### 4.2 The Bad

1. **Misunderstanding the “Pair” Count**  
   A common rookie mistake is to count **duplicate characters** instead of *adjacent equal pairs*.  
   - **Duplicate count** would incorrectly allow strings like `"121212"` (no duplicates) but reject `"123444"` (duplicates but only one adjacent pair).  
   - The key is to examine *adjacent* indices: `s[i] == s[i+1]`.

2. **Off‑by‑One Errors**  
   The window boundaries are a frequent source of bugs:  
   - Forgetting to adjust `left` after the pair removal.  
   - Using `s[left] == s[left-1]` instead of `s[left] == s[left+1]` when moving the left pointer.  

3. **Edge Cases**  
   - Single‑character strings (`|s| == 1`) – the answer is 1.  
   - All‑identical strings (`"111111"`) – the longest valid substring is `2`.  
   - Strings with many alternating digits (`"1010101"`) – the algorithm must not shrink unnecessarily.

---

### 4.3 The Ugly

1. **Over‑engineering**  
   Some solutions introduce extra arrays, stacks, or hash maps to keep track of occurrences.  
   - While clever, they add complexity and potential bugs.  
   - The sliding window is **the** minimal solution; extra data structures only increase time/space.

2. **Recursive or Divide‑and‑Conquer Approach**  
   - Recursion for substring partitioning may lead to stack overflow on long inputs.  
   - A divide‑and‑conquer method that merges results from two halves must still handle boundary pairs, often leading to corner‑case headaches.

3. **Blind Brute Force**  
   - Trying every substring (`O(n²)`), counting pairs inside each, is **slow** and will TLE on LeetCode's hidden tests.  
   - Worse, it gives interviewers the impression of not knowing the two‑pointer pattern.

---

### 4.4 Step‑by‑Step Walkthrough (Java)

```java
for (int right = 1; right < n; right++) {
    // 1. Did the new char create a new adjacent pair?
    if (s.charAt(right) == s.charAt(right - 1)) {
        repeats++;          // we now have one more violation
    }

    // 2. Shrink until we have at most one violation
    while (repeats > 1) {
        if (s.charAt(left) == s.charAt(left + 1)) {
            repeats--;      // we removed a pair from the window
        }
        left++;             // move the left boundary forward
    }
}
return n - left;          // longest window length
```

*Why this works:*  
- When `right` moves, the only new violation that can appear is between `right-1` and `right`.  
- When `repeats` exceeds `1`, we must contract the window from the left.  
- Removing the leftmost character may also delete a pair (`left` and `left+1`).  
- Each index is processed a constant number of times → linear time.

---

### 4.5 Testing Strategy

| Test | Input | Expected | Why |
|------|-------|----------|-----|
| 1 | `"52233"` | `4` | `"5223"` |
| 2 | `"5494"` | `4` | whole string |
| 3 | `"1111111"` | `2` | only one pair allowed |
| 4 | `"121212"` | `6` | no adjacent pairs |
| 5 | `"00101022"` | `5` | longest before hitting second pair |
| 6 | `"1"` | `1` | single char |
| 6 | `""` | `0` | empty string (LeetCode will never provide this) |

Use a small unit test harness for each language (JUnit, pytest, or `assert`).  

---

### 4.6 Takeaway for Recruiters

- **Show your reasoning**: Explain that you count *adjacent pairs*, not duplicates.  
- **Highlight the sliding window**: “two pointers, one violation counter.”  
- **Mention edge handling**: `"1111"` → answer `2`.  
- **Add a short proof of linearity**: “Each character is visited at most twice.”  

These points make you memorable in a technical interview and showcase a clean, production‑ready mindset.



--------------------------------------------------------------------

## 5.  Final Thoughts

Semi‑repetitive substring problems, while seemingly niche, are a **great lens** to evaluate your problem‑solving chops. The sliding‑window technique is one of the most frequently asked interview patterns; mastering it with problems like LeetCode 2730 gives you an edge.

Whether you’re coding in Java, Python, or C++, remember:

1. **Adjacent pair, not duplicate**  
2. **Two‑pointer window**  
3. **Constant space**  
4. **Edge‑case coverage**

Drop this into your portfolio, share it on GitHub, and you’ll be well‑positioned to impress hiring managers and peers alike. Happy coding!