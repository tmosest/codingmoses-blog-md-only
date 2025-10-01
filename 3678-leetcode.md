---
title: LeetCode 3678. Smallest Absent Positive Greater Than Average - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. LeetCode 3678 – “Smallest Absent Positive Greater Than Average”

**Problem**  
You’re given an integer array `nums`. Return the smallest *positive* integer that

1. **doesn’t appear** in `nums`, and  
2. is **strictly greater** than the arithmetic mean of all elements in `nums`.

The mean is calculated as  

```
average = (sum of all elements) / (number of elements)
```

> **Constraints**  
> - `1 ≤ nums.length ≤ 100`  
> - `-100 ≤ nums[i] ≤ 100`

The goal is to solve the problem in **O(n)** time and **O(1)** (or O(n) space for the hash‑set) using clean code.

---

## 2. Solutions

Below you’ll find idiomatic, production‑ready implementations in **Java**, **Python**, and **C++**.  
All three follow the same idea:

1. Compute the average.  
2. Build a quick‑lookup structure (hash‑set) for membership testing.  
3. Scan integers starting from 1, stop when we hit a number that is  
   * greater than the average and  
   * not in the set.

Because all values in the input are bounded by `±100`, the answer can never exceed `201` – we could therefore stop at 201.  
But a simple `while (true)` loop is safe and keeps the code short.

---

### 2.1 Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    /**
     * Return the smallest positive integer absent from nums
     * and strictly greater than the average of nums.
     *
     * @param nums the input array
     * @return the desired integer
     */
    public int smallestAbsent(int[] nums) {
        // 1. Compute average
        double avg = 0;
        for (int x : nums) avg += x;
        avg /= nums.length;

        // 2. Build a hash set for O(1) membership
        Set<Integer> present = new HashSet<>();
        for (int x : nums) {
            if (x > 0) present.add(x);   // only positives matter
        }

        // 3. Scan candidates
        int candidate = 1;
        while (true) {
            if (candidate > avg && !present.contains(candidate)) {
                return candidate;
            }
            candidate++;
        }
    }
}
```

*Why this is good:*  
- **Time O(n)** – one pass for the sum and one pass for the set.  
- **Space O(n)** – at most 100 positive numbers stored.  
- Uses primitive `int` for speed.  
- Simple, readable logic that would be praised in an interview.

---

### 2.2 Python

```python
from typing import List

class Solution:
    def smallestAbsent(self, nums: List[int]) -> int:
        # 1. Compute average
        avg = sum(nums) / len(nums)

        # 2. Build a set of positive numbers present
        present = {x for x in nums if x > 0}

        # 3. Scan candidates
        candidate = 1
        while True:
            if candidate > avg and candidate not in present:
                return candidate
            candidate += 1
```

*Why this is good:*  
- Very compact (`while True` loop).  
- Uses Python’s set for constant‑time lookup.  
- Handles negative numbers gracefully.

---

### 2.3 C++

```cpp
#include <vector>
#include <unordered_set>
#include <numeric>   // std::accumulate

class Solution {
public:
    int smallestAbsent(std::vector<int>& nums) {
        // 1. Compute average
        double avg = std::accumulate(nums.begin(), nums.end(), 0.0) / nums.size();

        // 2. Hash set of positives
        std::unordered_set<int> present;
        for (int x : nums)
            if (x > 0) present.insert(x);

        // 3. Scan candidates
        for (int candidate = 1; ; ++candidate) {
            if (candidate > avg && present.find(candidate) == present.end())
                return candidate;
        }
    }
};
```

*Why this is good:*  
- Uses `std::accumulate` for clarity.  
- `unordered_set` gives average‑case O(1) lookups.  
- `for(;;)` is idiomatic for “loop forever until return”.

---

## 3. The “Good, Bad, Ugly” of this Problem

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Problem clarity** | Clear constraints → quick solution | None | None |
| **Optimal solution** | O(n) time, O(n) space | O(n²) if we `count` or `search` linearly for each candidate | Recursion or dynamic programming that over‑complicates |
| **Common pitfalls** | Forget to skip negative numbers when building the set | Using `in`/`contains` on the entire array for each candidate → O(n²) | Infinite loop if the average is computed with integer division (flooring) |
| **Edge cases** | `nums` contains all negatives → answer is 1 | Not handling floating‑point precision → comparing int > double might be tricky | Using `int` average instead of `double`, leading to wrong thresholds |
| **Coding style** | Clear function name, inline comments | Too many temporary variables, verbose code | Hard‑to‑read code, magic numbers without explanation |

### 3.1 What to Highlight in an Interview

- **Explain the algorithm** in a few sentences: “Compute average, build a hash set of positives, then linearly scan for the smallest candidate > avg that isn’t present.”
- **Complexity analysis**: “Time O(n), space O(n) for the set.”
- **Why the set matters**: “Without the set we’d do an `O(n)` search for every candidate, which would degrade to O(n²).”
- **Edge‑case awareness**: “Even if the array is all negative, we still return 1 because it’s the smallest positive integer above any negative average.”
- **Testing**: Show a couple of unit tests or a `main()` that prints sample outputs.

---

## 4. SEO‑Optimized Blog Article

### Title
> **Cracking LeetCode 3678: Smallest Absent Positive Greater Than Average – Java, Python & C++ Solutions for Your Next Coding Interview**

### Meta Description
> Learn how to solve LeetCode 3678 in Java, Python, and C++. Discover efficient O(n) algorithms, edge‑case handling, and interview‑ready explanations. Boost your coding interview performance!

---

### Introduction

If you’re hunting for a **software engineer interview** or simply sharpening your algorithmic thinking, you’ll hit LeetCode problems like **“Smallest Absent Positive Greater Than Average”** (Problem 3678). The prompt looks simple, but it tests a developer’s ability to translate a math requirement into a clean, efficient codebase.

In this post, we’ll:

1. Break down the problem statement.  
2. Walk through **three** production‑ready implementations (Java, Python, C++).  
3. Highlight the **good**, **bad**, and **ugly** coding patterns.  
4. Show how to discuss this problem in an interview and showcase your reasoning skills.

---

### Problem Summary

> **Goal:**  
> Find the smallest positive integer that is **not present** in the array `nums` and is **strictly greater** than the arithmetic mean of `nums`.

> **Key insights:**
> - The average can be fractional, so use floating‑point arithmetic.
> - Since every element is bounded by `±100`, the answer can’t exceed `201`.  
> - Membership queries must be constant‑time → use a hash set.

---

### 5. Code Walkthrough

Below we present clean, commented solutions in **Java**, **Python**, and **C++**. Each one follows the same three‑step pattern: compute mean, hash the positives, scan candidates.

> **Why this matters in a job interview**  
> Recruiters love O(n) solutions. Show them that you can build a `HashSet` (Java), `set` (Python), or `unordered_set` (C++) to keep look‑ups in *average constant* time. It demonstrates that you care about performance and clean design.

#### 3.1 Java – Fast & Readable

```java
public int smallestAbsent(int[] nums) { ... }
```

#### 3.2 Python – Concise & Expressive

```python
def smallestAbsent(self, nums: List[int]) -> int: ...
```

#### 3.3 C++ – System‑Level & Performance

```cpp
int smallestAbsent(vector<int>& nums) { ... }
```

> **Why each is interview‑ready:**
> - They’re **single‑function** solutions with clear complexity annotations.
> - They avoid `O(n²)` pitfalls by using a hash set.
> - They handle all edge cases (negative averages, all negatives, array full of positives).

---

### The “Good, Bad, Ugly” Map

| Coding Pattern | What’s Good | What’s Bad | Avoid This |
|----------------|-------------|------------|------------|
| **Use a hash set for positives** | O(1) look‑ups → O(n) total | Linear search in array → O(n²) | Recursion that copies the array each level |
| **Compute average with double** | Handles fractions → correct threshold | Integer division → truncates → wrong answer | Using `int` division + wrong > comparison |
| **Scan from 1 upward** | Clear logic, minimal code | Not bounding the loop → risk of infinite loop | `while (candidate <= avg)` in C++ (integer comparison bug) |
| **Skip negatives when building the set** | Simplifies membership test | Including negatives wastes space | Populating the set with all numbers (negative + positive) → unnecessary |
| **Commentary & naming** | Descriptive, interview‑friendly | Too many comments → clutter | Magic numbers without explanation (e.g., `for i in range(1000)`) |

---

### Interview‑Ready Discussion

When a recruiter asks you to solve this problem, your answer should include:

1. **High‑level algorithm** – “average, hash‑set, scan”.
2. **Complexity** – “O(n) time, O(n) space”.
3. **Edge‑case considerations** – “array full of negatives → answer 1”.
4. **Testing strategy** – show a couple of quick test cases.
5. **Reflection** – “I avoided O(n²) by hashing. If I had used linear search, it would have been too slow for large inputs.”

> **Pro tip:** After solving, ask the interviewer:  
> *“Would you prefer an in‑place solution that uses no extra data structures, or is a hash set acceptable?”*  
> This demonstrates engagement and willingness to adapt.

---

### How to Showcase This on Your Resume

- **Project name**: *LeetCode 3678 – Smallest Absent Positive Greater Than Average*  
- **Skills**: `Java`, `Python`, `C++`, `HashSet`, `Time Complexity Analysis`, `Coding Interview`  
- **Bullets**:
  - Designed and implemented an **O(n)** algorithm that computes the mean and finds the smallest absent positive integer > mean.  
  - Utilized hash‑set look‑ups to keep member checks at constant time, preventing O(n²) behavior.  
  - Wrote unit tests covering negative‑only, positive‑dense, and mixed‑value edge cases.

Including such a concrete example in your résumé or portfolio signals that you’re not just a “code runner” but a **solutions architect** who can reason mathematically, design cleanly, and analyse complexity on the fly.

---

### Takeaway

LeetCode 3678 is a *micro‑project* that packs a lot of interview value:

- **Mathematical insight** – handling floating‑point averages.  
- **Data‑structure knowledge** – using a set to avoid quadratic time.  
- **Clean code** – easy to read, easy to explain.

Mastering this problem (and showing the three language implementations above) demonstrates to hiring managers that you can **translate spec → algorithm → production code** in a way that scales, is well‑documented, and is ready for a real‑world codebase.

---

### Quick Reference

| Language | Function Signature | Complexity | Notes |
|----------|--------------------|------------|-------|
| Java | `public int smallestAbsent(int[] nums)` | **O(n)** time, **O(n)** space | Uses `HashSet<Integer>` |
| Python | `def smallestAbsent(self, nums: List[int]) -> int` | **O(n)** time, **O(n)** space | Uses `set` for membership |
| C++ | `int smallestAbsent(vector<int>& nums)` | **O(n)** time, **O(n)** space | Uses `unordered_set<int>` |

> **Happy coding, and good luck with your next interview!**

---

### Final Thought

A clean, O(n) solution to LeetCode 3678 showcases **algorithmic maturity**.  
It proves that you can:

- **Translate** a problem’s mathematical requirement into an efficient data‑structure‑centric design.  
- **Avoid** common pitfalls (integer division, O(n²) scans).  
- **Communicate** your reasoning confidently in a coding interview.

Add this problem to your interview toolkit, practice the code, and you’ll walk into your next interview with confidence and a concrete example of your coding prowess. Happy hacking!