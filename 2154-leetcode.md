---
title: LeetCode 2154. Keep Multiplying Found Values by Two - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Overview – LeetCode 2154: “Keep Multiplying Found Values by Two”

**Goal**  
Given an integer array `nums` and a starting integer `original`, repeatedly double `original` as long as the doubled value is present in `nums`. Return the final value of `original` when it no longer appears in the array.

**Examples**

| `nums`           | `original` | Output |
|------------------|------------|--------|
| `[5, 3, 6, 1, 12]` | `3`        | `24`   |
| `[2, 7, 9]`          | `4`        | `4`    |

**Constraints**

- `1 ≤ nums.length ≤ 1000`
- `1 ≤ nums[i], original ≤ 1000`

The task is straightforward but appears on many interview decks, so a clean, optimal solution can impress interviewers and help you land a role.

---

## 🧩 Algorithmic Solution – Hash‑Based Lookup

### 1. Why a Hash Set?

- **O(1) average‑time lookup** – we can check the existence of a number instantly.
- **Simple & readable** – no need for sorting or binary search.
- **Memory trade‑off** – only 1,000 integers max, so space is negligible.

### 2. Steps

1. **Convert** `nums` into a `Set` (Java `HashSet`, Python `set`, C++ `unordered_set`).
2. **Loop**: While the current `original` value exists in the set, double it (`original *= 2`).
3. **Return** the final value once the loop stops.

### 3. Complexity

- **Time**: `O(n)` to build the set + `O(k)` for the while loop, where `k` is the number of doublings (≤ 10 for the given limits).  
  In practice, `O(n)` dominates, so overall **O(n)**.
- **Space**: `O(n)` for the set.

---

## 📜 Code Implementations

Below are clean, commented implementations in **Java, Python, and C++**. All three follow the same logic and achieve the same time/space guarantees.

### Java

```java
import java.util.HashSet;
import java.util.Set;

public class Solution {
    /**
     * Repeatedly doubles `original` as long as it appears in `nums`.
     * @param nums    Array of integers.
     * @param original Starting value to double.
     * @return Final value after no longer found in the array.
     */
    public int findFinalValue(int[] nums, int original) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) {
            set.add(num);
        }

        while (set.contains(original)) {
            original *= 2;
        }
        return original;
    }

    // Optional: Main method for quick manual testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[] nums = {5, 3, 6, 1, 12};
        System.out.println(sol.findFinalValue(nums, 3)); // 24
    }
}
```

### Python

```python
from typing import List

class Solution:
    def findFinalValue(self, nums: List[int], original: int) -> int:
        """
        Repeatedly double `original` while it exists in `nums`.
        """
        num_set = set(nums)          # O(n) build time
        while original in num_set:   # O(1) average lookup
            original <<= 1           # double
        return original

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.findFinalValue([5, 3, 6, 1, 12], 3))  # 24
```

### C++

```cpp
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    int findFinalValue(vector<int>& nums, int original) {
        unordered_set<int> s(nums.begin(), nums.end()); // O(n)
        while (s.find(original) != s.end()) {
            original <<= 1; // double
        }
        return original;
    }
};

// Optional: quick manual test
/*
int main() {
    Solution sol;
    vector<int> nums = {5, 3, 6, 1, 12};
    cout << sol.findFinalValue(nums, 3) << endl; // 24
}
*/
```

---

## 🏆 The Good, The Bad, The Ugly

| Aspect | The Good | The Bad | The Ugly |
|--------|----------|---------|----------|
| **Algorithm** | Linear time & constant lookup. No over‑engineering. | None – the solution is already optimal for given constraints. | If you naïvely loop through `nums` each time, you’ll hit `O(nk)` which is still fine for 1 000 elements but looks less efficient to interviewers. |
| **Readability** | Simple `while` loop, clear variable names. | None. | Using bit‑shifts (`<<= 1`) can confuse beginners; prefer `* 2` for clarity. |
| **Edge‑Case Handling** | Works for `original` not in array, or when array contains all doubling steps. | None. | If `original` grows beyond `int` range (not in constraints) you’d need a `long`. |
| **Space** | O(n) but negligible (≤ 1000 ints). | None. | Storing the entire array in a set might feel wasteful if you only need a few lookups; but with such small input it's fine. |
| **Interview Takeaway** | Show you understand hash‑based lookups and loop invariants. | None. | Avoid premature optimization (like building a sorted array for binary search); the simplest solution is usually best. |

---

## 📈 SEO‑Optimized Blog Article

> **Title:** LeetCode 2154 – “Keep Multiplying Found Values by Two” – Java, Python & C++ Solutions (The Good, The Bad & The Ugly)

> **Meta Description:** Master LeetCode 2154 with clean solutions in Java, Python, and C++. Understand the hash‑set trick, complexity, and why this simple algorithm impresses recruiters.

---

### 1️⃣ Introduction

If you’re prepping for a software‑engineering interview, the “Keep Multiplying Found Values by Two” challenge is a staple on LeetCode. The problem looks simple, but it’s a great test of your ability to:

- Translate a textual description into a clear algorithm.
- Choose the right data structure for fast lookups.
- Deliver clean, production‑ready code in multiple languages.

In this article, we’ll dissect the problem, walk through an **O(n)** solution, and discuss why it’s the best approach.

---

### 2️⃣ Problem Recap

You’re given:

- An integer array `nums` (length ≤ 1000).
- An integer `original`.

Repeat:  
- If `original` is in `nums`, double it (`original = 2 * original`).  
- Stop when it’s no longer found.  

Return the final `original`.

---

### 3️⃣ The “Good” – HashSet + While Loop

**Why a HashSet?**  
Because we need **constant‑time membership checks**. Building the set takes `O(n)` time, and each check is `O(1)` on average.

**Algorithm Outline**

```text
1. Convert nums → set
2. While original ∈ set:
       original ← original * 2
3. Return original
```

**Complexity**  
- Time: `O(n)` (set construction) + `O(k)` for doublings (k ≤ 10).  
- Space: `O(n)` for the set.

**Why It Works**  
Each iteration ensures that if a number is present, we immediately double it. Because the array is fixed, no new numbers can appear; we just keep “walking” through possible powers of two.

---

### 4️⃣ The “Bad” – Naïve Linear Search

A less optimal alternative is to scan `nums` on every iteration:

```python
while original in nums:
    original *= 2
```

- Time: `O(nk)` → still fine for n=1000 but looks slower to interviewers.  
- Space: `O(1)`.  

This is an okay solution for very small inputs, but it hides the real efficiency of a hash‑based approach.

---

### 5️⃣ The “Ugly” – Over‑Engineering

Some candidates add unnecessary complexity:

- **Sorting + Binary Search**  
  ```cpp
  sort(nums.begin(), nums.end());
  while(binary_search(nums.begin(), nums.end(), original))
      original *= 2;
  ```
  Sorting is `O(n log n)` and the binary search `O(log n)` per check, so you’re wasting extra work.

- **Recursive Doubling**  
  Recursive code might look elegant but brings stack overhead and can be harder to debug.

Remember: **Clarity beats cleverness** in most interview settings.

---

### 6️⃣ Language‑Specific Tips

| Language | Note |
|----------|------|
| **Java** | Use `HashSet<Integer>`; avoid autoboxing in tight loops if possible. |
| **Python** | `set` is built‑in; use `original <<= 1` for speed but `* 2` is clearer. |
| **C++** | `unordered_set<int>` gives average O(1) lookup; be careful with `<<=` semantics. |

---

### 7️⃣ Edge Cases & Testing

| Edge Case | What to Check |
|-----------|---------------|
| `original` not in `nums` | Should return `original` immediately. |
| `nums` contains a chain 3,6,12,24 | Should double until 48 not found. |
| Large `original` (1000) | Doubling stays within `int` range (max ~ 1024 000 000). |
| Duplicate values in `nums` | HashSet ignores duplicates – still works. |

**Sample Test Suite**

```python
def test():
    sol = Solution()
    assert sol.findFinalValue([5,3,6,1,12], 3) == 24
    assert sol.findFinalValue([2,7,9], 4) == 4
    assert sol.findFinalValue([1,2,4,8,16], 1) == 32
    print("All tests passed.")
```

---

### 8️⃣ Why This Matters for Your Job Hunt

- **Showcase Problem‑Solving**: Demonstrates translating a natural‑language requirement into algorithmic steps.
- **Data Structure Knowledge**: Highlights your understanding of when to use hash sets vs arrays.
- **Clean Code**: The code is concise, self‑documenting, and easily portable between languages.
- **Interview Impact**: A concise `O(n)` solution is often rated higher than a longer, less efficient one.

---

### 9️⃣ Final Thoughts

LeetCode 2154 is deceptively simple, but it’s a perfect teaching tool for hash‑based lookups and algorithmic efficiency. By mastering this pattern, you’ll be ready for a range of interview questions that involve searching, membership testing, and iterative transformations.

Happy coding, and good luck on your next interview! 🚀

---

**Resources**

- [LeetCode 2154 – Keep Multiplying Found Values by Two](https://leetcode.com/problems/keep-multiplying-found-values-by-two/)
- Official Java, Python, and C++ solutions posted above.

> **Tip:** Practice explaining your solution aloud; this trains you to articulate logic clearly—exactly what interviewers want.