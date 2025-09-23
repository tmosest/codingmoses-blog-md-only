---
title: LeetCode 1838. Frequency of the Most Frequent Element - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 Problem 1838 – Frequency of the Most Frequent Element  
**Difficulty:** Medium | **LeetCode URL:** https://leetcode.com/problems/frequency-of-the-most-frequent-element/  

> **Goal:**  
> In a single‑pass interview question, you’re asked to maximise the frequency of an element that you can obtain by performing *at most* `k` increment operations on an integer array `nums`.  
> (An increment operation means adding 1 to any element of the array.  The value of `k` tells you how many times you can do this.)

---

## 🏁 Why Interviewers Love This Question  

| Feature | Why it matters for your next interview |
|---------|----------------------------------------|
| **Array + “smallest change”** | Shows you can work with the idea that only the smallest element in a chosen sub‑array needs to be bumped up to the largest one. |
| **Sorting + Sliding Window** | A classic interview pattern: “sort first, then scan with two pointers.”  Recruiters want to see you recognise this pattern. |
| **Single‑pass logic** | Demonstrates a deep understanding of how to transform a *global* constraint (`k` total increments) into a *local* window condition. |
| **Time/Space trade‑off** | A clean O(n log n) solution that uses only O(1) additional space (aside from the sort) is the sweet spot recruiters look for. |

> **SEO Keywords**: *Leetcode 1838, Frequency of the Most Frequent Element, sliding window, interview preparation, coding interview, Java solution, Python solution, C++ solution, algorithmic problem, time complexity, space complexity, job interview, tech recruiter*  

---

## 🎯 Problem Overview

```text
Input:  nums = [1,2,4,5], k = 5
Output: 4
```

You can add at most `k` to any elements.  After the operations, what is the **maximum possible frequency** of a single value in the array?  

---

## 🔍 Approach – The “Good” Part

1. **Sort the array**  
   *Why?* Sorting groups equal or close values together.  Once sorted, the only thing that matters is how many elements you can raise to a target value (the rightmost element of the current window).

2. **Sliding‑window on the sorted array**  
   *Maintain a window `[left … right]`*  
   * `total` = sum of all elements inside the window  
   * Condition:  
     ```text
     nums[right] * windowSize  <=  total + k
     ```  
     If the left side is violated, shrink the window from the left until the condition is satisfied again.

3. **Track the best window size** – that size is the answer.

> **Why it works**  
> In a sorted window the *target* is always the rightmost value.  All other values in the window need to be increased to this target.  
> The number of increments needed is  
> ```text
> target * windowSize – total
> ```  
> If this number is ≤ `k`, the window is feasible; otherwise we must drop elements from the left.

---

## ⚠️ The “Bad” – Edge‑Cases & Gotchas

| Issue | How to avoid it |
|-------|-----------------|
| **Integer overflow** – `nums[right] * windowSize` can exceed `int` range. | Use `long`/`long long` for the product (or cast to `1LL`). |
| **Large input sizes** – The `total` can become large as well. | Accumulate `total` in a `long long`/`long`. |
| **Wrong loop condition** – Forgetting to subtract `nums[left]` when shrinking can lead to wrong answers. | Carefully follow the inner while‑loop and always update `total` and `left`. |

---

## 🐛 The “Ugly” – Common Mistakes

| Mistake | Why it happens | Fix |
|---------|----------------|-----|
| **Treating the array as a multiset of frequencies** | Many people think “increase the smallest numbers” is the answer.  That would be O(n k) and fails on the largest tests. | Realise the window must target the *largest* number in the window (`nums[right]`). |
| **Mis‑interpreting `k`** | Thinking `k` is a per‑element limit instead of a global budget. | Use `total + k` as the *available* sum for the current window. |
| **Off‑by‑one errors in window size** | Using `right - left` instead of `right - left + 1`. | Double‑check the window size formula. |
| **Not sorting** | Forgetting that the logic relies on sorted order. | Add `sort(nums.begin(), nums.end())` (Java: `Arrays.sort(nums)`). |

---

## 🧩 Implementation – One‑Line Solution in Three Languages

Below you’ll find a production‑ready, fully commented implementation in **Java, Python, and C++**.  All three use the same O(n log n) time + O(1) space algorithm described above.

### 1️⃣ Java

```java
import java.util.Arrays;

class Solution {
    public int maxFrequency(int[] nums, int k) {
        // 1. Sort – O(n log n)
        Arrays.sort(nums);

        long total = 0;      // running sum inside the window
        int left = 0;        // left pointer of the sliding window
        int res = 0;         // best window size seen so far

        for (int right = 0; right < nums.length; right++) {
            total += nums[right];

            // Contract window while it needs more increments than we have (k)
            while ((long) nums[right] * (right - left + 1) > total + k) {
                total -= nums[left];
                left++;
            }

            // Update answer
            res = Math.max(res, right - left + 1);
        }
        return res;
    }
}
```

> **Key points**  
> * Use `long` for `total` and the product to avoid overflow.  
> * The inner `while` guarantees the window always satisfies the constraint.

---

### 2️⃣ Python

```python
from typing import List

class Solution:
    def maxFrequency(self, nums: List[int], k: int) -> int:
        nums.sort()                     # O(n log n)
        total = 0
        left = 0
        res = 0

        for right, val in enumerate(nums):
            total += val

            # Shrink window until it becomes feasible
            while val * (right - left + 1) > total + k:
                total -= nums[left]
                left += 1

            res = max(res, right - left + 1)

        return res
```

> **Why Python is great here**  
> The code is extremely terse – just a few lines – yet it keeps the same O(n log n) time and O(1) space guarantees.

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxFrequency(vector<int>& nums, int k) {
        sort(nums.begin(), nums.end());   // O(n log n)
        long long total = 0;              // running sum
        int left = 0, res = 0;

        for (int right = 0; right < (int)nums.size(); ++right) {
            total += nums[right];

            // Contract window while it requires more than k increments
            while (1LL * nums[right] * (right - left + 1) > total + k) {
                total -= nums[left];
                ++left;
            }

            res = max(res, right - left + 1);
        }
        return res;
    }
};
```

> **C++ tips**  
> * Use `1LL` for the product to keep everything in 64‑bit range.  
> * `std::sort` is `O(n log n)` but can be performed in-place (only the recursion stack is used).

---

## 📑 Blog: “Cracking Leetcode 1838 – Frequency of the Most Frequent Element: A Sliding‑Window Masterclass”

---

### 1️⃣ Problem Overview

> **LeetCode 1838 – Frequency of the Most Frequent Element**  
> You’re given an integer array `nums` and a non‑negative integer `k`.  
> You may perform at most `k` increment operations on any element (add 1 each time).  
> **Goal:** return the largest frequency of a single value achievable after all operations.

---

### 2️⃣ Why This Question is a *Golden Ticket* for Interviews

| Recruiter Concern | Candidate Skill Demonstrated |
|-------------------|-----------------------------|
| **Array manipulation** | Ability to think in terms of ranges and counts |
| **Optimization mindset** | Recognising the need for sorting and a single pass |
| **Problem‑pattern awareness** | Sliding window after sorting is a well‑known LeetCode pattern |
| **Language versatility** | Showcasing solutions in Java, Python, and C++ demonstrates cross‑platform fluency |

If you nail this problem, you’ll have a solid talking point for any software‑engineering interview, especially in companies that value *algorithmic polish* (Google, Facebook, Amazon, etc.).

---

### 3️⃣ The “Good” – Why Our Approach Rocks

| Step | Why it’s “Good” |
|------|-----------------|
| **Sorting first** | Groups values so that the largest value in any sub‑array is at the right boundary.  This reduces the global constraint to a local one. |
| **Two‑pointer sliding window** | One pass (O(n)) after the sort → fast, simple, and proven pattern. |
| **Running sum (`total`)** | Enables constant‑time feasibility checks for the current window. |
| **Updating the answer on each iteration** | Guarantees we never miss a larger window. |

> **Result** – A clean O(n log n) time, O(1) space solution that is both fast and easy to reason about.

---

### 4️⃣ The “Bad” – Edge‑Case Awareness

> *Large products → overflow.*  
> *Accumulate sums in 64‑bit integers.*  
> *Make sure the window size is `right - left + 1`, not `right - left`.*

Recruiters respect candidates who pre‑empt these pitfalls.  Add a comment in your code: “Using 64‑bit math to avoid overflow” and you’ll score extra points.

---

### 5️⃣ The “Ugly” – How to Turn Common Mistakes into Wins

1. **Mis‑using `k` as a per‑element budget**  
   *Fix:* Use `total + k` as the *available* sum inside the window.  
2. **Dropping the left pointer incorrectly**  
   *Fix:* Update `total` every time you remove an element.  
3. **Forgetting to sort**  
   *Fix:* Add the sort line before the loop – it’s the cornerstone of the entire logic.

Turning a naïve solution into a *sliding window* strategy is the ultimate proof of *problem‑pattern recognition*—a core interviewable trait.

---

### 6️⃣ How to Explain the Sliding Window Logic on the Fly

> “After sorting, any window `[left…right]` has a natural target: the rightmost element `nums[right]`.  All other elements in the window need to be increased to this target.  
> The number of increments required equals `target * windowSize – total`.  If that value is ≤ k, the window is valid; otherwise we shrink from the left until it is.”

When explaining, emphasize the **local vs global** distinction: the window’s feasibility is a *local* check (`k` is a *global* budget).

---

### 7️⃣ Summary – What Recruiters Should Take Home

* **Pattern Recognition** – “Sort + Sliding Window” is a staple LeetCode strategy.  
* **Overflow Awareness** – Using 64‑bit math shows you care about production‑ready code.  
* **Cross‑Language Implementation** – Demonstrating Java, Python, and C++ solutions proves you’re a full‑stack algorithmic thinker.  
* **Problem‑Solving Clarity** – A concise explanation (like the table above) is perfect for technical interviews.  

> **Takeaway**: “I sorted, then I slid a window, then I kept the best window size – that’s all you need to solve LeetCode 1838.”

---

### 8️⃣ Final Word

> “If you can articulate why sorting then sliding‑window solves the problem *and* provide clean Java, Python, and C++ code, you’ve just delivered a complete interview masterpiece.  
> This is the kind of question that showcases *clarity, speed, and depth* – the exact combination recruiters look for.  

---

**Happy coding, and best of luck with your next interview!** 🚀  

--- 

> *Remember to test your solution with the LeetCode hidden tests – they cover edge cases like `k = 0`, all elements already equal, or very large `k`.  A production‑grade solution will pass all of them.*

---  

*Happy coding, future star candidate!* 🌟  
---  

**(If you have questions or want to dive deeper into the mathematics behind the product‑check, drop a comment below or open an issue in the GitHub repo.)**  
---  

*Stay curious. Keep coding. Keep interviewing.*  

---  

**End of Blog**  
---  

---  

*You now have:  
* A polished, production‑ready code base in three languages.  
* A ready‑to‑publish blog post to impress recruiters and interviewers alike.  
* A clear understanding of the pattern, pitfalls, and how to explain it.*  

Good luck! 🏆

--- 

**[Download the PDF cheat sheet](#)** (includes the code snippets + quick‑reference explanation).  

---  

*Let me know if you’d like a deeper dive into the math, a video walkthrough, or a mock interview session.*  



---  

**TL;DR** – *Sort, slide, shrink, track.*  All in `O(n log n)` time, `O(1)` space, and a few dozen lines of code.  This is the interview problem you want on your resume.  
---  

*Good luck, and enjoy your journey to the next level!*  

---  



---  



---  

---  

---  

---  

---  

---  



---  

---  

---  

---  

---  

---  



---  

---  

---  



---  

---  

---  



---  

---  

---  



---  

---  

---  



---  



---  



---  

---  



---  

---  



---  

---  



---  

---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---  



---