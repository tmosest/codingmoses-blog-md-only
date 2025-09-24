---
title: LeetCode 3153. Sum of Digit Differences of All Pairs - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sum of Digit Differences of All Pairs – A Complete Guide  
*(LeetCode 3153 – Medium)*  

---

## 📌 Problem Recap

You’re given an array `nums` of **positive integers**.  
All integers contain the **exact same number of digits** (let’s call it `m`).  

The **digit difference** between two integers is the number of positions where the digits differ.  
For example, `13` vs. `23` → `1` (only the first digit differs).

**Task** – return the sum of digit differences over **all unordered pairs** of integers in the array.

> **Input**: `nums = [13, 23, 12]`  
> **Output**: `4`  
> (1 + 1 + 2 from the example above)

Constraints  
- `2 ≤ nums.length ≤ 10⁵`  
- `1 ≤ nums[i] < 10⁹`  
- Every number has the same number of digits.

---

## 🔍 Why is this a LeetCode “Interview Question”?

- **Array & digit manipulation** – tests familiarity with loops, integer math, and array handling.  
- **Optimal complexity** – the optimal solution is `O(n · m)` which is required for the large input limit.  
- **Attention to details** – division by 2, overflow handling, and the “same digit length” condition.

---

## 💡 Solution Overview – Digit‑Column Counting

### Intuition
Instead of comparing every pair (which would be `O(n²)`), we observe that digit differences can be counted **column‑by‑column**.

For a fixed digit position `p` (from the least significant to most significant):
1. Count how many numbers have each digit `0–9`.  
2. Any two numbers that have *different* digits at this position contribute **1** to the pair’s difference.  
3. For a particular digit that appears `c` times, the number of *pairs that contain a number with this digit and a number with a *different* digit* is `c × (n − c)`.

Sum this quantity over all digits `0–9` for the current column.  
Do this for each of the `m` columns, then divide by `2` because every unordered pair is counted twice (once from each number’s perspective).

### Algorithm
```text
n ← length(nums)
m ← number of digits in any nums[i]
answer ← 0

for each column from 0 to m-1 do
    freq[10] ← {0}
    for i from 0 to n-1 do
        digit ← nums[i] % 10
        freq[digit] += 1
        nums[i] //= 10          // remove the processed digit
    for d from 0 to 9 do
        answer += freq[d] * (n - freq[d])

return answer / 2
```

*Time*: `O(n · m)`  
*Space*: `O(1)` (only a 10‑element array per column)

> **Why division by 2?**  
> Each pair `(i, j)` is counted once when `i` processes its digit and once when `j` processes the same digit. So we halve the total.

---

## ✅ Edge Cases & Pitfalls

| # | Edge Case | What to Watch For |
|---|-----------|--------------------|
| 1 | All numbers identical | The answer is `0`. Our algorithm naturally yields `0` because `freq[d] = n` → `n * (n-n) = 0`. |
| 2 | Large numbers (`< 10⁹`) | Use `long`/`int64` for the answer; `freq[d] * (n - freq[d])` can exceed 32‑bit. |
| 3 | Division by 2 | Perform after the loop to keep the result an integer. |
| 4 | Mutating `nums` | If you don’t want to modify the input, copy the array first or extract digits by string manipulation. |
| 5 | Leading zeros? | The problem guarantees the same digit length, so numbers are given without leading zeros. |

---

## 🎯 Code Implementations

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.

> Each solution follows the same algorithmic steps.  
> Feel free to copy‑paste into your IDE or online editor for testing.

---

### Java

```java
import java.util.*;

public class Solution {
    public long sumDigitDifferences(int[] nums) {
        int n = nums.length;
        // Determine number of digits once
        int m = String.valueOf(nums[0]).length();

        long total = 0;
        // Work column by column
        for (int col = 0; col < m; col++) {
            int[] freq = new int[10];
            // Count digits in this column
            for (int i = 0; i < n; i++) {
                int digit = nums[i] % 10;
                freq[digit]++;
                nums[i] /= 10;        // Remove the processed digit
            }
            // Add contributions of this column
            for (int c : freq) {
                total += (long) c * (n - c);
            }
        }
        return total / 2;  // each pair counted twice
    }

    // For quick manual testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.sumDigitDifferences(new int[]{13, 23, 12})); // 4
        System.out.println(s.sumDigitDifferences(new int[]{10, 10, 10, 10})); // 0
    }
}
```

---

### Python

```python
from typing import List

class Solution:
    def sumDigitDifferences(self, nums: List[int]) -> int:
        n = len(nums)
        m = len(str(nums[0]))          # all numbers have same length

        total = 0
        for _ in range(m):
            freq = [0] * 10
            for i in range(n):
                digit = nums[i] % 10
                freq[digit] += 1
                nums[i] //= 10        # drop processed digit
            for c in freq:
                total += c * (n - c)

        return total // 2

# Demo
if __name__ == "__main__":
    s = Solution()
    print(s.sumDigitDifferences([13, 23, 12]))          # 4
    print(s.sumDigitDifferences([10, 10, 10, 10]))      # 0
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long sumDigitDifferences(vector<int>& nums) {
        int n = nums.size();
        int m = to_string(nums[0]).size();   // number of digits

        long long total = 0;
        for (int col = 0; col < m; ++col) {
            int freq[10] = {0};
            for (int i = 0; i < n; ++i) {
                int digit = nums[i] % 10;
                ++freq[digit];
                nums[i] /= 10;           // remove processed digit
            }
            for (int c : freq) {
                total += 1LL * c * (n - c);
            }
        }
        return total / 2;    // each pair counted twice
    }
};

int main() {
    Solution s;
    vector<int> a = {13, 23, 12};
    cout << s.sumDigitDifferences(a) << endl;   // 4

    vector<int> b = {10, 10, 10, 10};
    cout << s.sumDigitDifferences(b) << endl;   // 0
}
```

---

## 🔧 “Good, Bad, Ugly” Deep Dive

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic efficiency** | O(n·m) meets the constraints. | None – the optimal approach is already used. | If you inadvertently use the naïve O(n²) approach, the solution will TLE on large inputs. |
| **Space usage** | Constant extra space. | None | Over‑engineering with matrices or hash maps unnecessarily increases memory. |
| **Implementation clarity** | Straightforward loops, clear variable names. | The division by 2 can be overlooked. | Mutating the input array may surprise callers – better to copy or use strings if immutability is required. |
| **Overflow handling** | Uses 64‑bit integers. | Forgetting to cast `int` to `long long` in C++/Python `int` can overflow in Java (`int` too small). | In Java, using `int` for the result will overflow for large inputs (e.g., 100,000 numbers). |
| **Edge‑case robustness** | Handles identical numbers, single‑digit numbers, maximum size. | If the input array isn’t guaranteed to have same length, the algorithm fails silently. | Not validating input length or digit consistency may lead to subtle bugs in production. |

---

## 📈 SEO & Job‑Search Friendly Write‑up

### Meta Title
**Sum of Digit Differences of All Pairs – Java, Python & C++ Solutions | LeetCode 3153**

### Meta Description
Master LeetCode 3153 “Sum of Digit Differences of All Pairs” with clear Java, Python, and C++ implementations. Learn the O(n·m) column‑counting technique, edge‑case handling, and interview‑ready explanations.

### H1
Sum of Digit Differences of All Pairs – LeetCode 3153

### H2
- Problem Statement  
- Intuition & Optimal Approach  
- Edge Cases & Common Pitfalls  
- Java Solution  
- Python Solution  
- C++ Solution  
- Complexity Analysis  
- Good, Bad, Ugly  

### Keywords to Sprinkle
- LeetCode 3153  
- Sum of Digit Differences of All Pairs  
- Java interview coding problem  
- Python algorithm example  
- C++ programming interview question  
- O(n·m) solution  
- Frequency array technique  
- Interview preparation  
- Software engineering interview  

---

## 🎓 Takeaway

The **digit‑column counting** technique is a powerful pattern for problems that ask for pairwise differences on fixed‑width numeric representations. By reducing an `O(n²)` brute force to a single pass over the digits, we achieve both speed and elegance—exactly what interviewers want to see.

Feel free to adapt the code to your own style, add unit tests, or integrate it into a larger codebase. Good luck crushing that interview! 🚀