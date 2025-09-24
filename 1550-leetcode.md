---
title: LeetCode 1550. Three Consecutive Odds - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Three Consecutive Odds – LeetCode 1550  
**Java • Python • C++** | **Counting Approach (O(n), O(1))** | **Interview‑Ready**

---

## TL;DR

> *“Given an integer array, return `true` if there exist three consecutive odd numbers; otherwise return `false`.”*

The simplest, most efficient solution is a single linear scan that keeps a counter of consecutive odd numbers.  
Time complexity: **O(n)**  
Space complexity: **O(1)**  

Below you’ll find clean, production‑ready code in **Java, Python, and C++**, followed by a short blog article that explains the problem, the approach, and the pros/cons of various methods.

---

## Code

### 1️⃣ Java

```java
public class Solution {
    public boolean threeConsecutiveOdds(int[] arr) {
        int consecutiveOdds = 0;          // counts how many odds we've seen in a row

        for (int num : arr) {
            if ((num & 1) == 1) {        // fast odd test (num % 2 == 1 is also fine)
                consecutiveOdds++;
                if (consecutiveOdds == 3) {
                    return true;        // found three consecutive odds
                }
            } else {
                consecutiveOdds = 0;     // reset on even number
            }
        }
        return false;                    // never reached a streak of 3 odds
    }
}
```

---

### 2️⃣ Python

```python
class Solution:
    def threeConsecutiveOdds(self, arr: list[int]) -> bool:
        count = 0                     # consecutive odd counter

        for num in arr:
            if num & 1:               # bit‑wise check for odd
                count += 1
                if count == 3:
                    return True
            else:
                count = 0
        return False
```

---

### 3️⃣ C++

```cpp
#include <vector>

class Solution {
public:
    bool threeConsecutiveOdds(std::vector<int>& arr) {
        int count = 0;                // consecutive odd counter

        for (int num : arr) {
            if (num & 1) {            // fast odd test
                ++count;
                if (count == 3) {
                    return true;     // streak of 3 odds found
                }
            } else {
                count = 0;           // reset on even number
            }
        }
        return false;
    }
};
```

> **Why this code?**  
> *Single pass* – only one loop over the array.  
> *Constant memory* – just an integer counter.  
> *Fast odd detection* – `num & 1` is marginally quicker than `% 2` and is idiomatic in many interview settings.

---

## Blog Article – “Three Consecutive Odds: The Good, The Bad, and The Ugly”

> *Meta description:* Learn how to solve LeetCode 1550 – Three Consecutive Odds – in Java, Python, and C++. Explore brute‑force, counting, and one‑liner approaches, and get interview‑ready with a concise O(n) solution.

---

### 1. Problem Recap

- **Input:** `int[] arr` (Java), `list[int] arr` (Python), `vector<int> arr` (C++)  
- **Length:** 1 ≤ `arr.length` ≤ 1000  
- **Value range:** 1 ≤ `arr[i]` ≤ 1000  
- **Goal:** Return `true` if any three **consecutive** elements are all odd; otherwise `false`.

---

### 2. The “Good” – Counting Strategy

The counting strategy is the canonical interview solution:

```text
count = 0
for each num in arr:
    if num is odd:  count += 1
    else:           count = 0
    if count == 3:  return true
return false
```

**Why it’s good**

| Criterion | Why it scores high |
|-----------|--------------------|
| **Time** | O(n) – one pass, linear to array size |
| **Space** | O(1) – just a counter |
| **Readability** | Very small, clear intent |
| **Robustness** | Works for all edge cases (length < 3, alternating parity) |
| **Performance** | Minimal branching, constant‑time operations |

---

### 3. The “Bad” – Naïve Brute Force

```text
for i from 0 to arr.length-3:
    if arr[i], arr[i+1], arr[i+2] are all odd:
        return true
return false
```

**Downsides**

| Issue | Impact |
|-------|--------|
| Re‑checks overlapping windows | O(3n) ≈ O(n) but with a higher constant |
| Less intuitive | Can be mis‑typed in interviews |
| More code | Increases chance of bugs |

It’s still *acceptable* for tiny inputs, but unnecessary when the counting method is available.

---

### 4. The “Ugly” – One‑Liner with `search_n` (C++) / `any` (Python)

> *Example (C++):*  
> `return std::search_n(arr.begin(), arr.end(), 3, 0, [](int a){ return a & 1; }) != arr.end();`

**Pros**

- Extremely short
- Expresses intent in a functional style

**Cons**

- Hard to read for non‑experts
- Relies on language‑specific utilities that many interviewers won’t expect
- Poor performance due to potential extra overhead (iterator comparisons, function objects)

> *When to use:* When you’re working in a production code base that already has such utilities, or when you want to impress with “C++ magic” – but be ready to explain the underlying mechanics in a discussion.

---

### 5. Complexity Summary

| Approach | Time | Space |
|----------|------|-------|
| Counting | **O(n)** | **O(1)** |
| Brute‑Force | **O(n)** (constant factor 3) | **O(1)** |
| One‑Liner | **O(n)** | **O(1)** |

The counting method dominates in *every* metric.

---

### 5. Interview‑Ready Tips

1. **State the problem in your own words.**  
   “We’re looking for a run of three odds—so we just need to keep track of how many odds we’ve seen in a row.”

2. **Explain the counter idea before writing code.**  
   This shows you understand *why* you’re scanning linearly and *how* the algorithm guarantees correctness.

3. **Mention bitwise odd check.**  
   `num & 1` is a classic trick in coding interviews and shows you know low‑level performance tricks.

4. **Ask about constraints.**  
   “Your constraints (≤ 1000) make a brute‑force pass feasible, but the counting solution is still better.”  
   This opens a discussion about *optimization trade‑offs*.

5. **Time‑space trade‑off.**  
   Show you can choose the right algorithm for the situation – *good vs. bad* approaches.

---

### 6. Takeaway – How to Nail This Question

| Step | Action |
|------|--------|
| **1. Understand “consecutive”** | Clarify that windows can overlap; reset counter on even numbers. |
| **2. Use a single counter** | Write the counting loop. |
| **3. Verify edge cases** | Length < 3 → `false`; alternating numbers → `false`; triple odds at any position → `true`. |
| **4. Explain complexity** | Interviewers love seeing O(n) / O(1). |
| **5. Be ready to explain alternatives** | Mention brute‑force and one‑liner for completeness. |

---

### 7. SEO & Career Advice

| SEO Keyword | Why it matters |
|-------------|----------------|
| `Three Consecutive Odds LeetCode` | Core search term used by recruiters hunting for LeetCode experience. |
| `Java Three Consecutive Odds` | Targets Java developers. |
| `Python LeetCode 1550` | Pulls Python‑centric interviews. |
| `C++ Counting Algorithm` | Highlights algorithmic efficiency, a plus for system‑design positions. |
| `Interview algorithm practice` | Broadens reach to anyone preparing for coding interviews. |

> **Job‑search Tip:**  
> Add this problem to your portfolio on GitHub, link it in your LinkedIn headline, and write a blog post like this one. Recruiters often filter candidates by LeetCode performance, so a clear, optimized solution with an accompanying explanation can give you a measurable edge.

---

### 8. Final Thoughts

- **Counting** is the *de‑facto* LeetCode 1550 solution.  
- Brute‑force is quick but redundant.  
- One‑liners are cute, but readability matters most in interviews.  

With the three snippets above and the interview‑ready explanation, you’ve got a polished package that’s ready to impress hiring managers and pass the LeetCode checker.

Happy coding! 🚀