---
title: LeetCode 2855. Minimum Right Shifts to Sort the Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # LeetCode 2855 – “Minimum Right Shifts to Sort the Array”  
### A quick‑guide, full‑solution, and a “good–bad‑ugly” interview‑blog

---

## TL;DR

- **Problem** – Find the smallest number of *right rotations* that turn a given array into ascending order, or return `-1` if impossible.  
- **Core Insight** – A single “break” in the sorted order (a descending pair) tells us the rotation point.  
- **Complexity** – `O(n)` time, `O(1)` space.  
- **Languages** – Java, Python, C++ – all in one code‑block.  
- **Why it matters** – It’s a textbook example of “array rotation” logic that shows up on almost every coding interview.  

---

## 1. Problem Recap (from LeetCode)

> **Given**: `nums` – a 0‑indexed array of distinct positive integers.  
> **Goal**: Return the minimum number of right shifts that will sort `nums` in non‑decreasing order. If no such number of shifts exists, return `-1`.  
> **Right shift**: Move the element at index `i` to index `(i + 1) % n` for all indices.

> **Examples**  
> *Input*: `[3,4,5,1,2]` → *Output*: `2`  
> *Input*: `[1,3,5]` → *Output*: `0`  
> *Input*: `[2,1,4]` → *Output*: `-1`

---

## 2. Insight – One “Drop” Is All You Need

- In a sorted array, every element is smaller than its successor.  
- If the array is a rotated sorted array, it will have **exactly one “drop”** – a position where `nums[i-1] > nums[i]`.  
- If you find more than one drop, the array can’t be sorted by any number of right rotations.  
- If you find zero drops, the array is already sorted → answer `0`.  
- If you find one drop at index `i`, the sorted array would start at `i` (the smallest element).  
  - The number of right shifts required is `n - i` (move the tail to the front).  
  - You must also verify that the last element is **not** smaller than the first element after rotation; otherwise, the array would wrap around in the wrong direction → return `-1`.

The logic is **linear** and uses only a couple of integer variables.

---

## 3. The Code

Below is a single source file that contains **Java, Python, and C++** implementations side‑by‑side. Each implementation follows the same algorithm, so you can copy‑paste the one that matches your interview language.

```java
/* -------------------------------------------------------------
 *  LeetCode 2855: Minimum Right Shifts to Sort the Array
 *  Author: Your Name
 *  -------------------------------------------------------------
 *
 *  Languages: Java, Python, C++
 *  Complexity: O(n) time, O(1) space
 * -------------------------------------------------------------
 */

/* ---------------------------- JAVA ---------------------------- */
public class MinimumRightShiftsSolution {
    public int minimumRightShifts(List<Integer> nums) {
        int n = nums.size();
        int dropIdx = 0;   // index where nums[i-1] > nums[i]
        int dropCnt = 0;   // how many drops we see

        for (int i = 1; i < n; i++) {
            if (nums.get(i - 1) > nums.get(i)) {
                dropIdx = i;
                dropCnt++;
            }
        }

        if (dropCnt > 1) return -1;           // more than one drop → impossible
        if (dropIdx == 0) return 0;           // already sorted

        // Check that the array after rotation will still be sorted
        if (nums.get(n - 1) > nums.get(0)) return -1;

        return n - dropIdx;                   // number of right shifts needed
    }
}

/* ---------------------------- PYTHON -------------------------- */
class PythonSolution:
    def minimumRightShifts(self, nums: list[int]) -> int:
        n = len(nums)
        drop_idx = 0
        drop_cnt = 0
        for i in range(1, n):
            if nums[i - 1] > nums[i]:
                drop_idx = i
                drop_cnt += 1
        if drop_cnt > 1:
            return -1
        if drop_idx == 0:
            return 0
        if nums[-1] > nums[0]:
            return -1
        return n - drop_idx

/* ---------------------------- C++ ---------------------------- */
class CPPSolution {
public:
    int minimumRightShifts(vector<int>& nums) {
        int n = nums.size();
        int dropIdx = 0;
        int dropCnt = 0;
        for (int i = 1; i < n; ++i) {
            if (nums[i - 1] > nums[i]) {
                dropIdx = i;
                ++dropCnt;
            }
        }
        if (dropCnt > 1) return -1;
        if (dropIdx == 0) return 0;
        if (nums[n - 1] > nums[0]) return -1;
        return n - dropIdx;
    }
};
```

> **Tip**: In interviews, always ask whether the array can contain duplicates. The LeetCode statement guarantees distinct integers, so we don't need to handle equality cases. If duplicates were allowed, you would change the comparisons to `>=` accordingly.

---

## 4. The “Good, the Bad, and the Ugly”

| Category | What’s Good | What’s Bad | What’s Ugly |
|----------|-------------|------------|-------------|
| **Algorithmic Simplicity** | One linear scan, constant space. | Requires a keen eye for the “one drop” property – not obvious at first glance. | A naive solution that rotates the array `n` times and checks for sortedness (`O(n²)`), leading to TLE on large inputs. |
| **Robustness** | Handles all edge cases (empty array, single element, already sorted). | Assumes all numbers are distinct; must modify if duplicates are allowed. | Fails on arrays that wrap incorrectly because the check `last > first` is omitted. |
| **Readability** | Clear variable names (`dropIdx`, `dropCnt`). | Some may prefer `breakPoint` or `rotationIndex` for semantic clarity. | Obfuscated code with magic numbers or inline comments missing. |
| **Time/Space** | `O(n)` time, `O(1)` space – optimal. | Still `O(n)` time; if constraints were `10⁷`, we might need a different approach. | O(n²) or O(n log n) due to repeated sorting or rotating. |
| **Interview Impact** | Demonstrates pattern recognition (rotated sorted arrays). | Needs to explain the invariant of “at most one drop.” | Over‑engineering or writing a brute force simulation shows lack of algorithmic thinking. |

---

## 5. Why Mastering This Problem Helps You Get a Job

1. **Pattern Recognition** – Many interview problems revolve around recognizing sortedness, rotations, or cyclic patterns. Once you spot the “one drop” trick, you can apply it to similar LeetCode questions (e.g., *Circular Array Rotations*, *Find Minimum in Rotated Sorted Array*).
2. **Clean Code** – Your solution is concise, readable, and free of unnecessary complexity. Interviewers love candidates who can deliver a clear, production‑ready solution in a short time.
3. **Space‑Time Trade‑Off** – Showing that you can solve it in `O(n)` time and `O(1)` space demonstrates an awareness of algorithmic efficiency—critical in real‑world systems where performance matters.
4. **Cross‑Language Confidence** – Providing Java, Python, and C++ solutions shows versatility. Many hiring managers ask candidates to code in a language of their choice; you’ll be ready for any stack.
5. **Storytelling** – In the “good–bad–ugly” section, you’re explaining why a particular approach is superior. Interviewers appreciate the ability to narrate trade‑offs, a skill that translates to code reviews and architectural decisions.

> *Pro tip*: In your resume or portfolio, add a short “Algorithmic Patterns” section. Mention “Rotated Sorted Array Recognition” and link to this solution on GitHub. Recruiters love concrete evidence of problem‑solving prowess.

---

## 6. SEO‑Optimized Takeaway

- **Primary keywords**: `leetcode minimum right shifts`, `array rotation sort`, `minimum right shifts to sort array solution`, `Java Python C++ solution`, `coding interview array rotation`.  
- **Meta description** (≈155 characters):  
  “Solve LeetCode 2855 in Java, Python, and C++ with an O(n) algorithm. Understand the one‑drop trick, see a good–bad–ugly breakdown, and boost your interview prep.”
- **Alt‑text for images** (if you add screenshots): “Java code for LeetCode 2855 minimum right shifts”.  

---

## 7. Final Checklist

- [x] Understand the “single drop” invariant.  
- [x] Implement linear scan, constant space.  
- [x] Validate edge cases (`n == 1`, already sorted).  
- [x] Include a `last > first` safety check.  
- [x] Write clean, commented code in all three languages.  
- [x] Prepare a concise blog post with SEO elements.  
- [x] Upload to GitHub, link from your résumé, and practice explaining the approach in a mock interview.

Good luck! 🚀