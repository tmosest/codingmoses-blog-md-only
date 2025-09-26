---
title: LeetCode 2032. Two Out of Three - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 2032 – “Two Out of Three”  
**Difficulty:** Easy  
**Tag:** Array, HashMap, Bit Manipulation, Two Pointers  

> **Goal:** Return every integer that appears in *at least two* of the three input arrays.  

> **Constraints**  
> * `1 ≤ nums1.length, nums2.length, nums3.length ≤ 100`  
> * `1 ≤ nums1[i], nums2[i], nums3[i] ≤ 100`

---

## 📌 Why this problem is a gold‑mine for job interviews

| Skill | Why it matters |
|-------|----------------|
| **Set / Map usage** | Demonstrates familiarity with Java’s `HashSet`, Python’s `set`, C++’s `unordered_set`. |
| **Bit‑mask tricks** | Shows algorithmic creativity – especially for languages with low‑level support (C++). |
| **Edge‑case handling** | Illustrates robustness – handling duplicates and empty intersections. |
| **Complexity analysis** | Enables discussion of O(n) vs O(n²) solutions – a classic interview talking point. |

> **SEO hook:** *“LeetCode Two Out of Three solution in Java, Python, C++ – easy interview prep”*

---

## 🔧 Solution Overview

The problem is essentially a “frequency” problem on three small integer ranges (1‑100).  
Four common approaches:

1. **HashSet** – add each array to a set, count occurrences.  
2. **Frequency Array (size 101)** – booleans or ints; no extra containers.  
3. **Bit‑mask (3 bits)** – compact representation of presence in each array.  
4. **Sorting + Two‑pointer** – not needed for the given constraints but educational.

The **Frequency Array** (option 2) is the simplest and fastest in practice for this problem, and works in all three languages without external libraries.  
Below you’ll find clean, production‑ready code in **Java, Python, and C++**.

---

## ✅ Java Implementation (no external collections)

```java
import java.util.ArrayList;
import java.util.List;

class Solution {
    public List<Integer> twoOutOfThree(int[] nums1, int[] nums2, int[] nums3) {
        // Boolean arrays to mark presence of numbers (1‑100).
        boolean[] present1 = new boolean[101];
        boolean[] present2 = new boolean[101];
        boolean[] present3 = new boolean[101];

        for (int n : nums1) present1[n] = true;
        for (int n : nums2) present2[n] = true;
        for (int n : nums3) present3[n] = true;

        List<Integer> result = new ArrayList<>();

        // Numbers are 1‑100 inclusive; iterate over this fixed range.
        for (int i = 1; i <= 100; i++) {
            // Count how many arrays contain this number.
            int count = 0;
            if (present1[i]) count++;
            if (present2[i]) count++;
            if (present3[i]) count++;

            if (count >= 2) result.add(i);
        }

        return result;
    }
}
```

**Complexity**  
- *Time*: `O(n1 + n2 + n3 + 100)` → `O(n)`  
- *Space*: `O(1)` (constant 3 × 101 booleans)

---

## ✅ Python Implementation

```python
from typing import List

class Solution:
    def twoOutOfThree(self, nums1: List[int], nums2: List[int], nums3: List[int]) -> List[int]:
        # Frequency array for numbers 1..100
        freq = [0] * 101

        for n in nums1:
            freq[n] += 1
        for n in nums2:
            freq[n] += 1
        for n in nums3:
            freq[n] += 1

        # Collect numbers that appear at least twice
        return [i for i in range(1, 101) if freq[i] >= 2]
```

**Complexity** – identical to Java.

---

## ✅ C++ Implementation (no STL containers, only vector)

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> twoOutOfThree(std::vector<int>& nums1,
                                   std::vector<int>& nums2,
                                   std::vector<int>& nums3) {
        // Boolean flags for each possible value
        bool p1[101] = {false};
        bool p2[101] = {false};
        bool p3[101] = {false};

        for (int n : nums1) p1[n] = true;
        for (int n : nums2) p2[n] = true;
        for (int n : nums3) p3[n] = true;

        std::vector<int> res;
        for (int i = 1; i <= 100; ++i) {
            int cnt = (p1[i] ? 1 : 0) + (p2[i] ? 1 : 0) + (p3[i] ? 1 : 0);
            if (cnt >= 2) res.push_back(i);
        }
        return res;
    }
};
```

**Complexity** – `O(n)` time, `O(1)` extra space.

---

## 🧐 The Good, the Bad, and the Ugly

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Readability** | The frequency‑array approach uses a single loop and fixed size, making it *very* easy to understand. | It relies on the hidden assumption that values lie in `1‑100`. | Hard‑coded bounds make the solution fragile for other constraints. |
| **Performance** | O(n) time, constant memory. | No performance drawback for given limits. | Using a set would add `O(1)` overhead per insert but still O(n). |
| **Scalability** | Works for any bounded integer range; simply adjust array size. | If the input range expands to `10⁹`, a hash map becomes mandatory. | Over‑engineering: bit‑mask tricks for such a small input add unnecessary complexity. |
| **Language‑agnostic** | Works unchanged in Java, Python, C++. | Requires language‑specific syntax but the core logic stays the same. | None. |

### Take‑away for job interviews

- **Explain your trade‑offs**: “I used a frequency array because the value range is small (1‑100). If it were larger, I’d use a hash map or bit‑mask.”
- **Mention edge cases**: duplicates inside a single array don’t matter because we use boolean flags.
- **Show your code style**: Keep functions small, use descriptive names, and comment where the logic is non‑obvious.

---

## 📈 SEO‑Optimized Blog Post

> **Title**  
> *LeetCode Two Out of Three – Java, Python, C++ Solutions | Interview Prep for Software Engineers*

> **Meta Description**  
> Master LeetCode’s “Two Out of Three” problem with clean Java, Python, and C++ solutions. Understand the best algorithmic trade‑offs, edge cases, and interview talking points.

> **Keywords**  
> LeetCode Two Out of Three, LeetCode solution, interview prep, Java solution, Python solution, C++ solution, hash set, bit mask, array problems, job interview coding

---

### Introduction

If you’re hunting for a front‑end, back‑end, or full‑stack role, the LeetCode “Two Out of Three” question appears in many interview stacks. It’s a classic *frequency‑count* problem that lets you show off your knowledge of data structures, time‑space analysis, and clean coding practices.

In this post we’ll:

1. Walk through the problem and constraints.  
2. Compare three popular solution strategies.  
3. Present production‑ready Java, Python, and C++ code.  
4. Highlight the good, the bad, and the ugly.  
5. Wrap up with interview‑ready talking points.

---

### Problem Recap

You’re given three integer arrays `nums1`, `nums2`, `nums3`.  
Return a list of all distinct integers that appear in **at least two** of the arrays. The order of the output doesn’t matter.

Constraints:

- Each array length is 1‑100.  
- Elements are between 1 and 100.

---

### Solution Strategies

| Approach | Pros | Cons |
|----------|------|------|
| **HashSet** (3 sets + count map) | Simple to understand; general‑purpose | Extra memory; slower for small, bounded ranges |
| **Frequency Array** (size 101 booleans) | Constant space; O(n) time; language‑agnostic | Requires knowledge of value bounds |
| **Bit‑mask** (3 bits per number) | Extremely compact; great for interview show‑off | Harder to read; only useful for tight constraints |

For LeetCode’s constraints, the **frequency array** is the sweet spot. It runs in linear time and uses negligible memory. That’s exactly why it’s the first solution in the editorial.

---

### Code Snapshots

#### Java

```java
class Solution {
    public List<Integer> twoOutOfThree(int[] nums1, int[] nums2, int[] nums3) {
        boolean[] p1 = new boolean[101];
        boolean[] p2 = new boolean[101];
        boolean[] p3 = new boolean[101];

        for (int n : nums1) p1[n] = true;
        for (int n : nums2) p2[n] = true;
        for (int n : nums3) p3[n] = true;

        List<Integer> res = new ArrayList<>();
        for (int i = 1; i <= 100; i++) {
            int cnt = (p1[i] ? 1 : 0) + (p2[i] ? 1 : 0) + (p3[i] ? 1 : 0);
            if (cnt >= 2) res.add(i);
        }
        return res;
    }
}
```

#### Python

```python
class Solution:
    def twoOutOfThree(self, nums1, nums2, nums3):
        freq = [0] * 101
        for n in nums1: freq[n] += 1
        for n in nums2: freq[n] += 1
        for n in nums3: freq[n] += 1
        return [i for i in range(1, 101) if freq[i] >= 2]
```

#### C++

```cpp
class Solution {
public:
    vector<int> twoOutOfThree(vector<int>& nums1,
                              vector<int>& nums2,
                              vector<int>& nums3) {
        bool p1[101] = {false}, p2[101] = {false}, p3[101] = {false};

        for (int n : nums1) p1[n] = true;
        for (int n : nums2) p2[n] = true;
        for (int n : nums3) p3[n] = true;

        vector<int> res;
        for (int i = 1; i <= 100; ++i) {
            int cnt = (p1[i] ? 1 : 0) + (p2[i] ? 1 : 0) + (p3[i] ? 1 : 0);
            if (cnt >= 2) res.push_back(i);
        }
        return res;
    }
};
```

---

### The Good, the Bad, the Ugly

- **Good**: All three snippets share the same core idea—count each number once per array—and run in `O(n)` time.  
- **Bad**: The value bound of 100 is hidden in the code. If the constraints change, the solution breaks.  
- **Ugly**: Over‑complicating with bit‑masking for a 1‑100 range is unnecessary. Keep it simple—unless the interview explicitly asks for the most compact solution.

---

### Interview‑Ready Talking Points

1. **Why a frequency array?**  
   *“The problem guarantees that all values are ≤ 100, so a simple boolean array suffices.”*

2. **Handling duplicates**  
   *“Using boolean flags ensures each array is counted at most once per value, making duplicates inside a single array irrelevant.”*

3. **Time/Space trade‑offs**  
   *“O(n) time, O(1) space. If the value range were larger, I’d switch to a `HashMap`.”*

4. **Edge Cases**  
   - Empty array? Not possible due to constraints.  
   - All three arrays contain the same single number? Output that number.  

5. **Testing**  
   - Show a couple of unit tests (e.g., `assert solution.twoOutOfThree([1,2,3],[5,6,7],[1,2,3]) == [1,2,3]`).  
   - This demonstrates defensive coding.

---

### Closing

“Two Out of Three” is deceptively simple but opens a window into your problem‑solving mindset. By mastering the frequency‑array strategy across **Java, Python, and C++**, you’re ready to tackle similar *count‑in‑two‑out‑of‑three* questions, whether they appear on LeetCode, in technical interviews, or on your daily coding tasks.

Happy coding, and best of luck landing that job interview! 🚀

--- 

**References**  
- LeetCode Editorial – Two Out of Three  
- [Cracking the Coding Interview](https://www.oreilly.com/library/view/cracking-the-coding/9780984782857/)  

---

> **Call to Action**  
> Want more interview‑ready code snippets? Subscribe to our newsletter and get the latest LeetCode walkthroughs delivered straight to your inbox.

---

### Done!

You now have a polished, interview‑friendly solution and a blog post that ranks well for the most searched keywords around LeetCode and interview prep. Share it on LinkedIn, GitHub, or your personal blog to showcase your algorithmic mastery and boost your job‑search profile. Good luck!