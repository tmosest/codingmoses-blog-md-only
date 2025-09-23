---
title: LeetCode 3258. Count Substrings That Satisfy K-Constraint I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Recap  
**LeetCode 3258 – Count Substrings That Satisfy K‑Constraint I**  

> A binary string satisfies the *k‑constraint* if **either**  
> *the number of 0’s ≤ k* **or** *the number of 1’s ≤ k*.  
>  
> For a given binary string `s` (length ≤ 50) and an integer `k`,  
> return the number of substrings that satisfy the k‑constraint.

> **Examples**  
> ```
> s = "10101", k = 1  → 12  
> s = "1010101", k = 2 → 25  
> s = "11111", k = 1   → 15
> ```

The naive O(n²) solution checks every substring; we can do better with a sliding‑window (two‑pointer) approach in O(n) time and O(1) space.



--------------------------------------------------------------------

## 🔎 Why Sliding Window?  
A substring is defined by a left and right index.  
If we keep a *valid* window `[left … right]` that satisfies the k‑constraint, then any sub‑substring ending at `right` and starting somewhere inside the window is automatically valid.  

When we extend `right`, we may break the constraint – at that point we *shrink* from the left until the window becomes valid again.  
The key insight: the number of new valid substrings added when `right` moves to the next position equals the size of the window (`right – left + 1`). Summing that over all positions gives the answer.



--------------------------------------------------------------------

## 📦 The Three Implementations

### 1. Java

```java
public class Solution {
    public int countKConstraintSubstrings(String s, int k) {
        int n = s.length();
        int left = 0, count0 = 0, count1 = 0, ans = 0;

        for (int right = 0; right < n; right++) {
            // Expand the window
            if (s.charAt(right) == '0') count0++;
            else count1++;

            // Shrink while both counts exceed k
            while (count0 > k && count1 > k) {
                if (s.charAt(left) == '0') count0--;
                else count1--;
                left++;
            }

            // All substrings ending at 'right' are valid
            ans += (right - left + 1);
        }
        return ans;
    }
}
```

### 2. Python

```python
class Solution:
    def countKConstraintSubstrings(self, s: str, k: int) -> int:
        n = len(s)
        left = 0
        cnt0 = cnt1 = 0
        ans = 0

        for right, ch in enumerate(s):
            if ch == '0':
                cnt0 += 1
            else:
                cnt1 += 1

            # shrink window until it is valid again
            while cnt0 > k and cnt1 > k:
                if s[left] == '0':
                    cnt0 -= 1
                else:
                    cnt1 -= 1
                left += 1

            ans += right - left + 1

        return ans
```

### 3. C++

```cpp
class Solution {
public:
    int countKConstraintSubstrings(string s, int k) {
        int n = s.size();
        int left = 0, cnt0 = 0, cnt1 = 0, ans = 0;

        for (int right = 0; right < n; ++right) {
            if (s[right] == '0') ++cnt0;
            else ++cnt1;

            while (cnt0 > k && cnt1 > k) {
                if (s[left] == '0') --cnt0;
                else --cnt1;
                ++left;
            }

            ans += right - left + 1;
        }
        return ans;
    }
};
```

All three codes run in **O(n)** time and use only a handful of integer variables – perfect for the constraints (`n ≤ 50`) and for interview‑style coding.



--------------------------------------------------------------------

## 📚 Blog Post: “The Good, the Bad, and the Ugly of Counting K‑Constraint Substrings”

### Title  
**“K‑Constraint Substrings – A Sliding Window Masterclass: The Good, the Bad, and the Ugly”**  

*SEO keywords*: `leetcode k constraint`, `count substrings`, `sliding window`, `binary string interview`, `coding interview practice`, `Java Python C++ solution`, `leetcode 3258`, `algorithm interview questions`, `data structures interview`.

---

### Introduction  

When you stumble across **LeetCode 3258 – Count Substrings That Satisfy K‑Constraint I**, you’re handed a deceptively simple problem that actually tests your ability to juggle two counters and two pointers at once.  

Below, I’ll walk through the **good** (why the sliding‑window approach shines), the **bad** (what pitfalls people hit when they write a brute‑force solution), and the **ugly** (edge‑case traps that can break even the most elegant code).  
Along the way, I’ll provide working Java, Python, and C++ solutions that you can drop into your code editor in seconds.

> **Pro tip:** Because the input string length is at most 50, you can cheat with brute force if you’re in a hurry. But for a *real* interview or a LeetCode contest, O(n) time is the only path to success.

---

### The Good: Sliding Window – O(n) in a Snap  

1. **Linear time** – You traverse the string once, moving the right pointer forward.  
2. **Constant space** – Only a few integers track the counts of 0’s and 1’s.  
3. **Intuitive reasoning** – Any substring that ends at index `right` and starts anywhere inside the current valid window `[left … right]` is guaranteed to satisfy the constraint.  
4. **No hidden loops** – The inner while‑loop shrinks the window *only* when necessary, never iterating more than the total number of characters.

*Why it matters:* In interview panels, demonstrating a sliding‑window solution shows you can think about “state” and “boundary conditions” – the bread‑and‑butter of algorithm design.

---

### The Bad: Brute Force – O(n²) That’s a Drag  

The most naive implementation looks like:

```python
for i in range(n):
    zeros = ones = 0
    for j in range(i, n):
        if s[j] == '0': zeros += 1
        else: ones += 1
        if zeros <= k or ones <= k: ans += 1
```

**Problems:**

- **Quadratic time** – For `n = 50`, you still finish quickly, but for larger inputs it blows up.  
- **Repeated work** – Each inner loop recomputes counts from scratch, ignoring information you already have.  
- **Hard to reason** – The logic becomes cluttered; a recruiter will see a “copy‑paste” style code.

*Takeaway:* Even if the constraints are small, always consider whether you can bring down the complexity with a better algorithm.

---

### The Ugly: Edge Cases That Trip You Up  

| Edge case | Why it matters | Fix |
|-----------|----------------|-----|
| `k` equals string length | Window never shrinks | No special handling needed; algorithm works. |
| All ‘0’s or all ‘1’s | One counter stays 0 | Sliding window still works – the other counter never exceeds `k`. |
| Alternating pattern `010101…` | Both counters may hit `k+1` simultaneously | Shrink until *either* counter is ≤ `k`. The inner while‑loop condition `cnt0 > k && cnt1 > k` guarantees that. |
| `k = 0` | Substring must have *no* 0’s **or** *no* 1’s | Sliding window still valid; you’re essentially counting runs of identical characters. |

> **Mistake to avoid:** Using `cnt0 >= k && cnt1 >= k` inside the shrink loop. That would incorrectly keep shrinking even when one counter is exactly `k`.

---

### Full Working Code (Three Languages)  

> **Copy‑paste the snippets below into your favorite IDE or online editor.**

**Java**

```java
public class Solution {
    public int countKConstraintSubstrings(String s, int k) {
        int n = s.length();
        int left = 0, cnt0 = 0, cnt1 = 0, ans = 0;

        for (int right = 0; right < n; right++) {
            if (s.charAt(right) == '0') cnt0++;
            else cnt1++;

            while (cnt0 > k && cnt1 > k) {
                if (s.charAt(left) == '0') cnt0--;
                else cnt1--;
                left++;
            }

            ans += right - left + 1;
        }
        return ans;
    }
}
```

**Python**

```python
class Solution:
    def countKConstraintSubstrings(self, s: str, k: int) -> int:
        n = len(s)
        left = 0
        cnt0 = cnt1 = 0
        ans = 0

        for right, ch in enumerate(s):
            if ch == '0': cnt0 += 1
            else: cnt1 += 1

            while cnt0 > k and cnt1 > k:
                if s[left] == '0': cnt0 -= 1
                else: cnt1 -= 1
                left += 1

            ans += right - left + 1

        return ans
```

**C++**

```cpp
class Solution {
public:
    int countKConstraintSubstrings(string s, int k) {
        int n = s.size(), left = 0, cnt0 = 0, cnt1 = 0, ans = 0;
        for (int right = 0; right < n; ++right) {
            if (s[right] == '0') ++cnt0;
            else ++cnt1;

            while (cnt0 > k && cnt1 > k) {
                if (s[left] == '0') --cnt0;
                else --cnt1;
                ++left;
            }

            ans += right - left + 1;
        }
        return ans;
    }
};
```

---

### 🎯 Takeaway for the Interview

1. **State the problem clearly** – “Count substrings where either the 0‑count ≤ k or the 1‑count ≤ k.”  
2. **Explain the invariant** – “Our window is always valid; shrinking only when both counters exceed k.”  
3. **Complexity** – “O(n) time, O(1) space.”  
4. **Edge Cases** – “All zeros, all ones, alternating, k = 0 – all handled by the same logic.”  

Being able to articulate this will land you high marks.  

---

### 🎁 Bonus: SEO‑Optimized Meta Description (for your blog)

> “Master LeetCode 3258 with a sliding window solution. Learn the Java, Python, and C++ code, plus how to avoid common pitfalls and explain the algorithm in interviews.”

---

### 🚀 Final Thought

The sliding‑window technique turns a seemingly quadratic problem into an elegant linear one. Once you internalize the idea that *“every substring ending at the current right pointer inside the valid window is valid”*, you can tackle a whole family of “k‑constraint” or “at most k” problems with confidence.

Happy coding, and good luck landing that job!