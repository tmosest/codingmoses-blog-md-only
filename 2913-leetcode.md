---
title: LeetCode 2913. Subarrays Distinct Element Sum of Squares I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2913 – Subarrays Distinct Element Sum of Squares  
*Easy – LeetCode*

> **Problem**  
> Given a 0‑indexed integer array `nums`, a *subarray* is any contiguous non‑empty slice of `nums`.  
> For a subarray `nums[i..j]`, its **distinct count** is the number of unique values it contains.  
> Return the sum of the squares of the distinct counts for **all** subarrays of `nums`.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `[1,2,1]` | `15` | Six subarrays → `1^2+1^2+1^2+2^2+2^2+2^2 = 15` |
| 2 | `[1,1]` | `3` | Three subarrays → `1^2+1^2+1^2 = 3` |

> **Constraints**  
> * `1 ≤ nums.length ≤ 100`  
> * `1 ≤ nums[i] ≤ 100`

Because the array is tiny, a clean O(n²) brute‑force approach is perfectly adequate.  
Below you’ll find **three complete solutions** (Java, Python, C++), followed by a **blog‑style analysis** that will help you land that software‑engineering interview.

---

## 1️⃣ Java – Simple Brute‑Force

```java
import java.util.*;

class Solution {
    public int sumCounts(List<Integer> nums) {
        int n = nums.size();
        int total = 0;

        // For every possible starting index
        for (int i = 0; i < n; i++) {
            Set<Integer> seen = new HashSet<>();

            // Expand the subarray one element at a time
            for (int j = i; j < n; j++) {
                seen.add(nums.get(j));
                int distinct = seen.size();
                total += distinct * distinct;   // square the distinct count
            }
        }
        return total;
    }
}
```

**Why this works**

* The outer loop picks a left boundary `i`.  
* The inner loop extends the right boundary `j`.  
* A `HashSet` keeps track of the unique values seen so far – inserting is O(1).  
* For each subarray we compute `size²` and add it to the answer.

**Complexity**

| Metric | Value |
|--------|-------|
| Time   | `O(n²)` (max 10 000 iterations when `n = 100`) |
| Space  | `O(1)` (the set never holds more than `n` elements) |

---

## 2️⃣ Python – List‑Based Brute‑Force

```python
from typing import List

def sum_counts(nums: List[int]) -> int:
    n = len(nums)
    total = 0

    for i in range(n):
        seen = set()
        for j in range(i, n):
            seen.add(nums[j])
            total += len(seen) ** 2

    return total
```

The logic is identical to the Java version. Python’s `set` gives amortized O(1) insertions.

**Complexity**

* Time: `O(n²)`
* Space: `O(1)`

---

## 3️⃣ C++ – Vector & `unordered_set`

```cpp
#include <vector>
#include <unordered_set>
using namespace std;

int sumCounts(vector<int> nums) {
    int n = nums.size();
    long long total = 0;   // use long long to avoid overflow for larger inputs

    for (int i = 0; i < n; ++i) {
        unordered_set<int> seen;
        for (int j = i; j < n; ++j) {
            seen.insert(nums[j]);
            int distinct = seen.size();
            total += 1LL * distinct * distinct;
        }
    }
    return static_cast<int>(total);
}
```

*Note*: The LeetCode statement guarantees that the result fits in a 32‑bit signed integer, but using `long long` protects against accidental overflow.

**Complexity**

* Time: `O(n²)`
* Space: `O(1)`

---

## 4️⃣ Blog Article – “The Good, The Bad, and The Ugly of LeetCode 2913”

> **Title:**  
> *“Cracking LeetCode 2913: Subarrays Distinct Element Sum of Squares – A Deep Dive for Job‑Seeking Engineers”*

> **Meta‑Description (SEO):**  
> Learn how to solve LeetCode 2913 in Java, Python, and C++. Understand the algorithmic trade‑offs, read a step‑by‑step tutorial, and discover interview‑ready insights to boost your job prospects.

---

### Introduction

In a software‑engineering interview, the recruiter’s goal is not just to test your code – they’re also evaluating your problem‑solving mindset, coding style, and ability to communicate.  
LeetCode 2913 is a classic “array‑and‑hash‑set” problem that is **easy** in terms of difficulty but **powerful** in teaching important concepts:

1. **Brute force vs. optimality** – when is a naïve solution acceptable?
2. **Data structure choice** – why a hash set is perfect for counting distinct elements.
3. **Complexity analysis** – why O(n²) is fine for n ≤ 100 but would break otherwise.
4. **Language‑specific nuances** – Java’s `List<Integer>` vs. Python’s `list`, C++’s `unordered_set`.

Below we break down the *good*, *bad*, and *ugly* aspects of typical solutions and how to turn them into interview‑winning code.

---

#### The Good

| Feature | Why It Matters | What Interviewers Love |
|---------|----------------|------------------------|
| **Simplicity** | A single nested loop and a set keeps the code short and readable. | Reduces cognitive load for the interviewer to follow your logic. |
| **Correctness** | Uses the definition of “distinct count” directly – no hidden tricks. | Demonstrates a clear mapping from problem statement to code. |
| **Performance for Constraints** | O(n²) is ≤ 10 000 operations; runs in < 1 ms. | Shows you understand problem limits and can make the right trade‑off. |
| **Language‑Friendly** | Works in Java, Python, C++ without complex boilerplate. | Gives you flexibility to code in the language you’re most comfortable with. |

> *Tip:* In your interview, start with the brute‑force approach, then ask the interviewer if you’re allowed to optimize – this shows humility and willingness to iterate.

---

#### The Bad

| Issue | Consequence | Fix |
|-------|-------------|-----|
| **Excessive Memory** – Using an array of size `n²` to store all subarray results. | Wastes RAM, unnecessary. | Avoid storing results; accumulate directly. |
| **Integer Overflow** – Squaring distinct counts could overflow 32‑bit in languages with stricter limits. | Bugs on edge cases (`n=100`, all values distinct). | Use 64‑bit integer (`long long` in C++, `long` in Java, `int` in Python is unbounded). |
| **Inefficient Set Reset** – Re‑allocating the set in each outer loop can be costly in micro‑benchmarks. | Slight slowdown. | Reuse the same set if you clear it, but with n ≤ 100 the benefit is negligible. |
| **Ignoring Type Constraints** – Passing a raw `int[]` when the API expects `List<Integer>` leads to compilation errors. | Code fails to compile. | Match the signature exactly (`List<Integer>`). |

> *Key takeaway:* Even “easy” problems hide small pitfalls; catching them shows attention to detail.

---

#### The Ugly

| Problem | Why It’s Ugly | What’s a Better Practice |
|---------|--------------|--------------------------|
| **Hard‑Coded Magic Numbers** – Using `n < 100` as a hidden assumption. | Makes code brittle if the constraints change. | Derive `n` from `nums.size()`; avoid magic numbers. |
| **Lack of Documentation** – No comments or explanations. | Hard to read for humans; interviewers may doubt your thought process. | Add brief comments or a method description. |
| **Non‑Idiomatic Code** – Using `for (int i = 0; i < nums.size(); i++)` instead of a foreach. | Misses language features, can be slower. | Use enhanced for loops where appropriate, but for indexes you’ll still need them. |
| **Ignoring Edge Cases** – No test for `nums` being empty (though constraints say ≥ 1). | Shows careless testing. | Still assert the pre‑condition or handle gracefully. |

> *Lesson:* Clean, well‑commented, and edge‑case‑aware code separates a good candidate from a great one.

---

### How to Talk About This Problem in an Interview

1. **Restate the problem in your own words.**  
   *“We need to consider every contiguous slice of the array, count the unique numbers inside, square that count, and add them all together.”*

2. **Explain your chosen algorithm.**  
   *“We’ll walk through each starting index `i`, keep a running set of seen numbers as we expand the end index `j`. Adding `set.size()²` to the answer for every expansion.”*

3. **Analyze complexity.**  
   *“With `n ≤ 100`, the double loop runs ≤ 10 000 times – perfectly fine. Each set insert is O(1), so overall O(n²). Memory usage is O(1) besides the set.”*

4. **Discuss potential optimizations (optional).**  
   *“If `n` were larger, we might use a sliding window or prefix frequency array to avoid recomputing the set from scratch.”*

5. **Mention edge cases & overflow.**  
   *“We use a 64‑bit integer to be safe, and the set handles duplicate values automatically.”*

---

### Final Thoughts

- **Practice the pattern.**  
  Counting distinct elements in a sliding window is a recurring interview trope – apply it to problems like “Longest Substring with At Most K Distinct Characters” or “Subarray Distinct Count”.

- **Know your language limits.**  
  Java’s `HashSet` is a wrapper around a hash table, Python’s `set` is a hash table, and C++’s `unordered_set` is the same idea. All give amortized O(1) insert/lookup.

- **Keep it readable.**  
  Interviewers read code faster than they debug it. Use meaningful variable names (`i`, `j`, `seen`, `distinct`, `total`) and avoid unnecessary abstraction.

- **Prepare a few test cases** (edge, typical, large).  
  *`[1]`, `[1,2,3,4]`, `[1,1,1,1]`, `[100,99,100]`* – demonstrate that you understand how distinct counts change.

- **Show curiosity.**  
  Ask if the interviewer wants a more efficient solution or wants you to discuss how you’d handle an `n` of 10⁵. Even if it’s impossible with the naive approach, explaining the bottleneck shows you’re thinking about scalability.

---

### Ready to Land That Job?

1. **Copy the code** into your favourite IDE and run the LeetCode tests.  
2. **Add a short comment block** explaining the approach – this will look great in your GitHub README.  
3. **Blog it up** (like the section above) – use keywords: *“LeetCode 2913 solution”, “array distinct element”, “hash set algorithm”, “job interview tips”*.  
4. **Share** the post in tech communities, LinkedIn, or your portfolio. Recruiters often look at your writing samples.  

With a clean implementation in **Java, Python, and C++**, a solid interview walkthrough, and a polished blog post, you’ll stand out as a candidate who not only solves the problem but also communicates it clearly. Good luck! 🚀

---


*© 2024 Engineering Interview Insights* – All rights reserved.  

--- 

*Feel free to adapt the article for your own voice or expand on the optimizations if you want to showcase deeper algorithmic skills.*