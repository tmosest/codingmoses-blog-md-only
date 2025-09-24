---
title: LeetCode 2078. Two Furthest Houses With Different Colors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Problem Recap – LeetCode 2078  
**Two Furthest Houses With Different Colors**  
> **Signature (Java)**  
> `public int maxDistance(int[] colors)`

You’re given an array `colors` where `colors[i]` is the paint color of the *i*‑th house (houses are laid out in a single line).  
Return the maximum distance between any two houses that are **painted in different colors**.  
Distance between houses *i* and *j* is `|i – j|`.

Constraints  
* `2 ≤ n = colors.length ≤ 100`  
* `0 ≤ colors[i] ≤ 100`  
* There are at least two houses with different colors.

---

## 2.  The Straight‑Forward Approach (O(n²))

```java
class Solution {
    public int maxDistance(int[] colors) {
        int n = colors.length;
        int best = 0;
        for (int i = 0; i < n-1; i++) {
            for (int j = i+1; j < n; j++) {
                if (colors[i] != colors[j]) {
                    best = Math.max(best, j-i);
                }
            }
        }
        return best;
    }
}
```

* **Time** – `O(n²)`  
* **Space** – `O(1)`  

With `n ≤ 100` this is fast enough for LeetCode, but we can do better.

---

## 3.  The “Good” – Constant‑Space, Linear‑Time Solution

The key observation:  
**The furthest pair will always involve the first or the last house.**  
If you have a pair that does not touch either end, you can always move one end of the pair to an edge and the distance can only increase.

So we only need to examine two positions:

| Position | What it represents | Why it matters |
|----------|--------------------|----------------|
| `i` – leftmost house that is *different* from the last color | farthest from the right end | distance to the last house = `n-1-i` |
| `j` – rightmost house that is *different* from the first color | farthest from the left end | distance to the first house = `j-0` |

The answer is simply `max(j, n-1-i)`.

### Java – Constant Space (O(1) memory)

```java
public class Solution {
    public int maxDistance(int[] colors) {
        int n = colors.length;
        int left  = 0;            // first house index
        int right = n - 1;        // last house index

        // Find leftmost house that differs from the last color
        while (colors[left] == colors[right]) {
            left++;
        }

        // Find rightmost house that differs from the first color
        while (colors[right] == colors[left]) {
            right--;
        }

        // The furthest distance is the max of two candidates
        return Math.max(right, n - 1 - left);
    }
}
```

### Python – Constant Space (O(1) memory)

```python
class Solution:
    def maxDistance(self, colors: List[int]) -> int:
        n = len(colors)
        left, right = 0, n - 1

        # leftmost index with a color different from the last house
        while colors[left] == colors[right]:
            left += 1

        # rightmost index with a color different from the first house
        while colors[right] == colors[left]:
            right -= 1

        return max(right, n - 1 - left)
```

### C++ – Constant Space (O(1) memory)

```cpp
class Solution {
public:
    int maxDistance(vector<int>& colors) {
        int n = colors.size();
        int left = 0, right = n - 1;

        while (colors[left] == colors[right])  --right;
        while (colors[right] == colors[left])  ++left;

        return max(right, n - 1 - left);
    }
};
```

**Complexities**  
* **Time** – `O(n)` (one pass for each side, worst case `O(n)` total)  
* **Space** – `O(1)` (only a few indices)

---

## 4.  The “Bad” – Why Not Iterate Over All Pairs

* O(n²) complexity – overkill for the limits but still easy to code.  
* Works for every scenario but wastes CPU time if `n` grows beyond 100.  

Sometimes interviewers explicitly ask for the best possible solution. Providing a quadratic solution is a *bad* answer because it fails to show understanding of the problem’s structure.

---

## 5.  The “Ugly” – Tricky Edge‑Case Implementation

A buggy version might incorrectly handle arrays where all houses have the same color except one at the very end.  
```java
while (colors[0] == colors[i]) i++;   // stops when i == n-1
// i could become n, leading to ArrayIndexOutOfBoundsException
```
Make sure to **check bounds** or use a safe loop condition (`i < n`) and handle the case where no differing color is found on one side.

---

## 6.  Take‑away for Interview Success

1. **Read the prompt carefully** – note that the answer must involve the first or last house.  
2. **Think about invariants** – the farthest pair will always touch an edge.  
3. **Provide a clean, linear solution** – O(n) time, O(1) space is optimal here.  
4. **Mention the edge‑case handling** – if you show awareness of pitfalls you demonstrate maturity.  
5. **Explain your reasoning** – interviewers love candidates who articulate why a solution works.

---

## 7.  SEO‑Optimized Blog Post

> **Title**  
> “Cracking LeetCode 2078 – Two Furthest Houses With Different Colors (Java, Python, C++)”

> **Meta Description**  
> Learn how to solve LeetCode 2078 in linear time. Read the constant‑space Java, Python, and C++ implementations, plus tips to ace your coding interview.

> **Header Outline**  
> 1. Problem Statement & Constraints  
> 2. Brute‑Force Approach (O(n²))  
> 3. Optimal O(n) Solution – Why It Works  
> 4. Java Implementation  
> 5. Python Implementation  
> 6. C++ Implementation  
> 7. Edge‑Case Handling & Common Pitfalls  
> 8. Take‑away for Coding Interviews  
> 9. Frequently Asked Questions  
> 10. Call‑to‑Action – Practice on LeetCode

> **Keywords**  
> LeetCode 2078, Two Furthest Houses With Different Colors, coding interview, algorithm, Java, Python, C++, O(n) solution, constant space, interview tips, technical interview prep, data structure, time complexity, space complexity.

> **Internal Links**  
> *Link to other LeetCode problem blogs in the series (e.g., 2048, 2099).*

> **External Links**  
> *LeetCode official problem page*, *LeetCode discussion threads*, *HackerRank, CodeSignal resources.*

> **Image Ideas**  
> 1. Flow diagram showing why the pair must touch an edge.  
> 2. Code snippets side‑by‑side.  
> 3. A small animated GIF of two houses moving toward the edge.

> **CTA**  
> “Ready for your next coding interview? Solve more LeetCode problems and build your confidence!”

---  

### Final Code Repository

If you’d like to see all three implementations in a single repo, clone the following GitHub repository:

```
git clone https://github.com/yourusername/leetcode-2078-two-furthest-houses
```

It contains:

* `Solution.java`
* `Solution.py`
* `Solution.cpp`
* A `README.md` with the blog article text.

Happy coding, and best of luck in your next job interview!