---
title: LeetCode 2609. Find the Longest Balanced Substring of a Binary String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2609 – “Find the Longest Balanced Substring of a Binary String”

> **Title**  
> 2609. Find the Longest Balanced Substring – Java / Python / C++ Implementation + SEO‑Optimised Blog Post  

---

## 1️⃣ Problem Recap

You are given a binary string **`s`** (`0`s and `1`s only).  
A **balanced substring** is a contiguous slice of `s` where:

* All the `0`s come *before* any `1` (no `0` after a `1`).  
* The number of `0`s equals the number of `1`s.  

The empty substring is considered balanced (length 0).  
Return the *maximum* length of any balanced substring.

**Examples**

| s        | longest balanced substring | answer |
|----------|----------------------------|--------|
| 01000111 | 000111                    | 6      |
| 00111    | 0011                      | 4      |
| 111      | (empty)                   | 0      |

**Constraints**

* `1 <= s.length <= 50`
* `s[i] ∈ {'0','1'}`

> 👉 You can solve this in **O(n)** time with two simple counters – a great interview‑friendly trick!

---

## 2️⃣ Why a Two‑Pointer / Counter Strategy Works

Because the balanced string must look like `"00...011...11"`, we can scan from left to right:

1. **Count leading `0`s** – let it be `cnt0`.
2. **After the first `1` appears**, keep counting `1`s – let it be `cnt1`.
3. Whenever a new block of `0`s starts (after we already counted some `1`s), we **reset** `cnt0` and `cnt1` and start over.

The length of a balanced substring ending at any index is `2 * min(cnt0, cnt1)` because the smaller of the two counts limits how many pairs we can form.

This single pass gives us the answer in **O(n)** time and **O(1)** extra space.

---

## 3️⃣ Implementations

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**.

### 3.1 Java

```java
class Solution {
    public int findTheLongestBalancedSubstring(String s) {
        int maxLen = 0;
        int zeros = 0, ones = 0;

        for (int i = 0; i < s.length(); i++) {
            char ch = s.charAt(i);

            if (ch == '0') {
                // If we have already seen some '1's, we must reset.
                if (ones > 0) {
                    zeros = 1;
                    ones  = 0;
                } else {
                    zeros++;
                }
            } else { // ch == '1'
                if (zeros > 0) {
                    ones++;
                } else {
                    // A '1' before any '0' can't form a balanced substring
                    zeros = 0;
                    ones  = 0;
                }
            }

            maxLen = Math.max(maxLen, 2 * Math.min(zeros, ones));
        }

        return maxLen;
    }
}
```

> **Good**: One pass, no auxiliary arrays, easy to read.  
> **Bad**: The reset logic is a bit verbose – a small bug can creep in.  
> **Ugly**: If you forget the `ones > 0` guard, you’ll incorrectly allow `0` after a block of `1`s.

---

### 3.2 Python

```python
class Solution:
    def findTheLongestBalancedSubstring(self, s: str) -> int:
        max_len = 0
        zeros = ones = 0

        for ch in s:
            if ch == '0':
                if ones:
                    # new block of zeros after some ones -> reset
                    zeros, ones = 1, 0
                else:
                    zeros += 1
            else:  # ch == '1'
                if zeros:
                    ones += 1
                else:
                    zeros = ones = 0

            max_len = max(max_len, 2 * min(zeros, ones))

        return max_len
```

> **Good**: Pythonic, clear, no line‑count penalty.  
> **Bad**: Still relies on a manual reset, can be less obvious for newcomers.  
> **Ugly**: Forgetting to reset `ones` when starting a new `0` block can lead to incorrect results.

---

### 3.3 C++

```cpp
class Solution {
public:
    int findTheLongestBalancedSubstring(string s) {
        int maxLen = 0, zeros = 0, ones = 0;

        for (char ch : s) {
            if (ch == '0') {
                if (ones) {            // new block of zeros after ones
                    zeros = 1;
                    ones  = 0;
                } else {
                    ++zeros;
                }
            } else {                  // ch == '1'
                if (zeros) {
                    ++ones;
                } else {
                    zeros = ones = 0; // leading 1's can't start a balanced block
                }
            }
            maxLen = max(maxLen, 2 * min(zeros, ones));
        }
        return maxLen;
    }
};
```

> **Good**: Tight loops, minimal variable overhead.  
> **Bad**: C++’s `for (char ch : s)` syntax is modern but can trip up people used to index‑based loops.  
> **Ugly**: Mixing `++zeros` and `zeros = 1` can be confusing – a comment helps.

---

## 4️⃣ Complexity Analysis

| Implementation | Time | Space |
|----------------|------|-------|
| Java/Python/C++| **O(n)** | **O(1)** |
| Brute‑force (nested loops) | **O(n²)** | **O(1)** |

With `n ≤ 50`, all solutions run in microseconds, but the linear approach is a best‑practice pattern for interviewers.

---

## 5️⃣ Edge‑Case Checklist

| Edge Case | What to Watch For |
|-----------|-------------------|
| String starts with `1` | Should not start counting `0`s until a `1` appears. |
| Multiple blocks of `0`s after a block of `1`s | Must reset counters. |
| All `0`s or all `1`s | Result is `0`. |
| Empty substring | Handled automatically (no pair → 0). |

---

## 6️⃣ The “Good, Bad, Ugly” of this Problem

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Conceptual clarity** | Balanced substring is a simple “all zeros then all ones” pattern. | The requirement “equal counts” may confuse some. | Misinterpreting “empty substring” leads to wrong base cases. |
| **Algorithmic beauty** | One linear scan, no extra data structures. | Reset logic can look clunky. | Off‑by‑one errors during counter resets. |
| **Coding difficulty** | Minimal, fits in 30 minutes. | Requires careful handling of edge cases. | Writing the reset in a different language can break the logic. |
| **Interview appeal** | Shows understanding of two‑pointer technique. | Interviewers may ask “why not two nested loops?” | Failing to explain the “min(zeros, ones)” trick. |

---

## 7️⃣ SEO‑Optimized Blog Post

> **Title**  
> 2609. Find the Longest Balanced Substring – Java, Python, C++ Solution + Interview Tips  

> **Meta Description**  
> Master LeetCode #2609 in minutes. Learn a linear‑time solution, compare Java/Python/C++ implementations, and get interview‑ready tips.  

### 📄 Blog Outline

1. **Intro** – What is a balanced binary substring?  
2. **Problem statement** – Quick recap and examples.  
3. **Brute force vs. linear** – Why O(n) is preferable.  
4. **The O(n) algorithm** – Two counters, reset logic.  
5. **Full code in Java, Python, C++** – Annotated snippets.  
6. **Complexity analysis** – Why it matters in interviews.  
7. **Common pitfalls** – Edge cases and reset bugs.  
8. **Good, Bad, Ugly** – Quick cheat‑sheet for interviewers.  
9. **Takeaway** – Why this problem showcases clean algorithm design.  
10. **Call to action** – “Try it out, comment your thoughts, share on LinkedIn.”

### 📚 Sample SEO‑Friendly Content

> **Finding the Longest Balanced Substring in O(n)**  
> In a world where interviewers love linear‑time solutions, LeetCode’s 2609 challenges you to find the longest substring where all zeros precede ones, and the counts match. A single pass using two counters solves this in **O(n)** time—perfect for a 30‑minute interview.  
>  
> **Java, Python, C++**  
> The algorithm is identical across languages; only syntax changes. See our three clean implementations below.  
>  
> **Why It’s a Great Interview Question**  
> 1. **Conceptual clarity** – The “0‑block → 1‑block” pattern is intuitive.  
> 2. **Algorithmic elegance** – No auxiliary arrays, just two ints.  
> 3. **Edge‑case handling** – Reset logic tests your attention to detail.  
>  
> **Ready to ace the next coding interview?**  
> Implement the solution, test edge cases, and explain the min‑count logic to your interviewer. It’s a small problem that demonstrates mastery of linear scans—a must‑know for any software engineering interview.  

---

## 8️⃣ Final Thoughts

* **Time‑efficient** – O(n) is optimal for this problem.  
* **Space‑efficient** – Only two integer counters.  
* **Portability** – The same logic works across languages.  
* **Interview Value** – Showcases your ability to reduce a seemingly “balanced‑substring” problem to a simple scan.

> 🚀 Practice the code, add unit tests for edge cases (`"1"`, `"0"`, `"111000"`, etc.), and you’ll be ready to impress recruiters with a clean, efficient solution!  

---

### 🎉 Happy coding!  
Feel free to drop a comment or share your own twist on this problem.