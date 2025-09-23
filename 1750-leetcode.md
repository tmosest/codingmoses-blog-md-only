---
title: LeetCode 1750. Minimum Length of String After Deleting Similar Ends - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1750. Minimum Length of String After Deleting Similar Ends  
**Difficulty**: Medium | **Characters**: `a`, `b`, `c` | **Length**: ≤ 10⁵  

---

## TL;DR

```java
class Solution {
    public int minimumLength(String s) {
        int l = 0, r = s.length() - 1;
        while (l < r && s.charAt(l) == s.charAt(r)) {
            char ch = s.charAt(l);
            while (l <= r && s.charAt(l) == ch) l++;
            while (l <= r && s.charAt(r) == ch) r--;
        }
        return r - l + 1;           // may be 0 if l > r
    }
}
```

```python
class Solution:
    def minimumLength(self, s: str) -> int:
        l, r = 0, len(s) - 1
        while l < r and s[l] == s[r]:
            ch = s[l]
            while l <= r and s[l] == ch: l += 1
            while l <= r and s[r] == ch: r -= 1
        return max(r - l + 1, 0)
```

```cpp
class Solution {
public:
    int minimumLength(string s) {
        int l = 0, r = (int)s.size() - 1;
        while (l < r && s[l] == s[r]) {
            char ch = s[l];
            while (l <= r && s[l] == ch) ++l;
            while (l <= r && s[r] == ch) --r;
        }
        return max(r - l + 1, 0);
    }
};
```

The three snippets all use the **two‑pointer greedy** strategy – O(n) time, O(1) extra space.

---

## Why This Problem Is a “Great” Interview Question

| **Good** | **Bad** | **Ugly** |
|----------|---------|----------|
| **Simplicity** – The statement is short and unambiguous. | **Hidden Traps** – Off‑by‑one errors when l > r after deletions. | **Over‑Optimization** – Many candidates over‑think, trying DP or recursion where a linear scan suffices. |
| **Learning Value** – Teaches two‑pointer technique, greedy reasoning, and edge‑case analysis. | **Complex Constraints** – 10⁵ length demands linear time. | **Misreading “Prefix”/“Suffix”** – Some assume you can delete *any* equal ends, ignoring that prefixes/suffixes must be contiguous and non‑overlapping. |
| **Cross‑Language Transfer** – Same logic in Java, Python, C++ (and more). | **Unclear Optimality** – Students may think *any* sequence of deletions works, not proving minimality. | **Memory Misconception** – Creating many substrings is a costly anti‑pattern. |

---

## Algorithm Walk‑through

1. **Pointers**  
   * `l` – index of the leftmost character.  
   * `r` – index of the rightmost character.

2. **While `l < r` and `s[l] == s[r]`**  
   * All characters from `l` to the first different one are the same (`ch`).  
   * Skip *entire* block of `ch` starting at `l` → `l++` until a different char appears.  
   * Skip *entire* block of `ch` ending at `r` → `r--` until a different char appears.  
   * This simulates deleting the maximal equal‑character prefix and suffix in one step.

3. **Termination**  
   * Either the two pointers crossed (`l > r`) → whole string deleted → answer `0`.  
   * Or the characters at `l` and `r` differ → no more deletions possible → answer `r - l + 1`.

**Why Greedy Works**  
If the ends match, deleting *any* non‑empty equal block from each side cannot increase the remaining length. Taking the maximal blocks is safe and never reduces future options. Therefore, the greedy strategy yields the minimal possible remaining length.

---

## Complexity Analysis

| Metric | Value |
|--------|-------|
| Time   | **O(n)** – each character is examined at most twice. |
| Space  | **O(1)** – only two indices and a temporary char. |

---

## Edge Cases & Pitfalls

| Case | What to watch for |
|------|-------------------|
| `"a"` | Only one char → answer `1`. |
| `"aa"` | Whole string is the same char → answer `0`. |
| `"abca"` | Ends differ (`a` ≠ `a`? actually equal but only one `a` on each side, then middle block stays) → final length `2`. |
| Empty string after deletions | `l > r` → `max(r-l+1,0)` protects from negative length. |
| All three characters present | Algorithm still works because it only cares about equality of the current ends. |

---

## SEO‑Optimized Blog Article

> **Title:** Master LeetCode 1750 – Minimum Length of String After Deleting Similar Ends  
> **Meta Description:** Learn the greedy two‑pointer solution for LeetCode 1750, with Java, Python, and C++ code. Boost your interview prep and land your dream job.  
> **Keywords:** LeetCode 1750, minimum string length, two pointer algorithm, interview preparation, coding interview, greedy algorithm, Java solution, Python solution, C++ solution, job interview

---

# Blog Post

> **How to Crush LeetCode 1750 (Minimum Length of String After Deleting Similar Ends)**  
> *By Your Name – Software Engineer & Interview Coach*

## Introduction

If you’re preparing for a coding interview at companies like Google, Amazon, or Facebook, you’ll quickly realize that many “medium” LeetCode problems hide a simple, elegant trick. One such gem is **LeetCode 1750 – Minimum Length of String After Deleting Similar Ends**.  

In this post, I’ll walk you through the problem, explain why the greedy two‑pointer approach is optimal, show you clean Java, Python, and C++ implementations, and cover the pitfalls that trip up even seasoned candidates. By the end, you’ll not only ace this problem but also understand how to present a concise, production‑ready solution in an interview.

---

## Problem Restatement (Short & Sweet)

You’re given a string `s` consisting only of `'a'`, `'b'`, and `'c'`.  

You can repeatedly **delete a non‑empty prefix and a non‑empty suffix that contain the same character and do not overlap**.  
Your goal: **minimize the length of the string** after performing the operation any number of times (including zero).

---

## Intuition: Why a Two‑Pointer Sweep?

Think of the string as a line of characters. The operation always removes equal characters from *both* ends.  
If the leftmost and rightmost characters are different, you can never delete anything – the answer is simply the current length.

When they are the same, you have two choices:

1. Delete the *smallest* possible block (just the first character on each side).
2. Delete the *largest* possible block (all consecutive equal characters from each side).

Why not choose the larger block? Because taking more of the same character from both ends can’t hurt. It reduces the string faster and leaves you with the same middle segment. Hence, **the optimal strategy is to always delete the maximal equal prefix and suffix**. That’s exactly what a two‑pointer scan does.

---

## The Greedy Two‑Pointer Algorithm

1. **Initialize** two indices, `l = 0` and `r = s.length() - 1`.
2. **While** `l < r` **and** `s[l] == s[r]`:
   * Let `ch = s[l]`.
   * Advance `l` while `s[l] == ch`.
   * Retreat `r` while `s[r] == ch`.
3. **Result** is `max(r - l + 1, 0)`.  
   The `max` handles the case where `l` surpasses `r` (string fully deleted).

> **Why is this linear?**  
> Each iteration of the outer loop moves at least one pointer past a block of identical characters. Since the string has at most `n` characters, the total work is `O(n)`.

---

## Code in Three Languages

Below are clean, production‑ready solutions in Java, Python, and C++. Each follows the same logic and has the same time/space guarantees.

### Java

```java
class Solution {
    public int minimumLength(String s) {
        int l = 0, r = s.length() - 1;
        while (l < r && s.charAt(l) == s.charAt(r)) {
            char ch = s.charAt(l);
            while (l <= r && s.charAt(l) == ch) l++;
            while (l <= r && s.charAt(r) == ch) r--;
        }
        return r - l + 1;  // will be 0 if l > r
    }
}
```

### Python

```python
class Solution:
    def minimumLength(self, s: str) -> int:
        l, r = 0, len(s) - 1
        while l < r and s[l] == s[r]:
            ch = s[l]
            while l <= r and s[l] == ch:
                l += 1
            while l <= r and s[r] == ch:
                r -= 1
        return max(r - l + 1, 0)
```

### C++

```cpp
class Solution {
public:
    int minimumLength(string s) {
        int l = 0, r = (int)s.size() - 1;
        while (l < r && s[l] == s[r]) {
            char ch = s[l];
            while (l <= r && s[l] == ch) ++l;
            while (l <= r && s[r] == ch) --r;
        }
        return max(r - l + 1, 0);
    }
};
```

---

## Common Mistakes & How to Avoid Them

| Mistake | Why It Happens | Fix |
|---------|----------------|-----|
| **Using recursion or DP** | Candidates think each operation is a subproblem. | Greedy is enough; DP adds unnecessary complexity. |
| **Off‑by‑one error** | Forgetting that `l` and `r` can cross. | Return `max(r - l + 1, 0)` or check `if (l > r) return 0;`. |
| **Deleting only one character** | Thinking “remove any equal ends” means one char each. | Always delete the *entire* block of equal chars. |
| **Creating substrings in a loop** | Memory blowup for 10⁵ length. | Work with indices only. |

---

## Why This Problem Is a “Must‑Know” for Interviews

1. **Two‑Pointer Mastery** – A staple for many interview questions (reverse string, palindrome checks, sliding windows, etc.).  
2. **Greedy Insight** – Demonstrates ability to prove that a locally optimal choice leads to a globally optimal solution.  
3. **Edge‑Case Sensitivity** – Showcases attention to detail (empty string, single character, full deletion).  
4. **Language Flexibility** – You can present the same logic in Java, Python, C++ or even JavaScript, impressing interviewers who value clean, cross‑platform code.  

If you can articulate this solution, you’ll get one interview question out of the way *while you’re still warm.* It also sets you up nicely for other medium‑level problems that hinge on similar patterns.

---

## Final Thoughts

LeetCode 1750 is deceptively simple: a string, a few characters, and a two‑pointer sweep.  
The key is to recognize that equal ends should be trimmed in bulk, not one at a time. That insight turns the problem into a single linear pass, giving you an elegant, efficient solution.

Use the code snippets above in your practice and in interviews. Highlight the greedy proof, walk through a quick example, and be ready to answer “why this works.” With this problem in your toolbox, you’re one step closer to landing that dream software engineering role.

---

**Happy Coding!**

*If you found this post helpful, share it on LinkedIn or Twitter, and let me know in the comments which LeetCode problem you’re tackling next.*

---

## Conclusion

LeetCode 1750 is a perfect example of how a well‑understood pattern—two pointers and greedy deletion—can solve a seemingly intricate problem in linear time. Master this, and you’ll not only earn the “problem‑solving” badge on your résumé but also demonstrate the analytical mindset recruiters look for.  

Good luck, and feel free to reach out if you’d like one‑on‑one interview coaching or a deeper dive into other LeetCode challenges!

--- 

> **Follow me** for more interview prep guides, algorithm tutorials, and career‑building advice.

--- 

*End of Post*

---

## Conclusion

LeetCode 1750 isn’t just another string manipulation problem; it’s a micro‑course in greedy two‑pointer technique. By avoiding over‑engineering and focusing on maximal deletions, you can solve it in linear time with minimal code.  

Feel free to drop my contact details if you want a personalized mock interview or a deeper analysis of similar problems. Good luck—and happy coding!