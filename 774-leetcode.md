---
title: LeetCode 774. Minimize Max Distance to Gas Station - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Summary (LeetCode 774)

> **Minimize Max Distance to Gas Station**  
> *Hard – 10‑point LeetCode*

You are given a sorted array `stations` that contains the positions of existing gas stations on the x‑axis and an integer `k`.  
You can add **exactly** `k` new stations anywhere (not necessarily at integer coordinates).  
After the additions, let  

```
penalty() = maximum distance between two consecutive stations
```

Return the **smallest possible** `penalty()` value.  
Answers within `1e‑6` of the real answer are accepted.

---

## 2.  Core Idea

The solution is a classic *binary‑search‑on‑answer* problem.

1. **Upper bound** – The worst penalty is the largest existing gap.  
   (If we put no new stations the penalty equals that gap.)
2. **Lower bound** – `0` (you can put stations arbitrarily close).
3. **Feasibility test** – For a candidate penalty `mid`, compute how many stations are needed to make every gap ≤ `mid`.  
   If the needed stations `≤ k`, the candidate is feasible; otherwise it is not.

Because the answer is monotonic in `mid` (smaller penalties need more stations), we can binary‑search on `mid` until the precision is within `1e‑6`.

---

## 3.  Detailed Algorithms

### 3.1  Feasibility (`canAchieve`)

For each adjacent pair `stations[i‑1]` and `stations[i]`:

```
gap = stations[i] - stations[i-1]
required = ceil(gap / mid) - 1
```

`required` is the number of *extra* stations needed to split this gap so that each sub‑gap is ≤ `mid`.  
Sum all `required` over the whole array.  
If the sum ≤ `k` → feasible.

### 3.2  Binary Search

```
left  = 0
right = maxGap   // largest original gap

while (right - left > 1e-6):
    mid = (left + right) / 2
    if canAchieve(mid):
        right = mid
    else:
        left = mid

return right   // or left, both are within tolerance
```

---

## 4.  Code Implementations

Below are **Java**, **Python**, and **C++** solutions, each fully commented and ready to copy‑paste into your editor.

---

### 4.1  Java

```java
import java.util.*;

public class Solution {
    /**
     * LeetCode 774 – Minimize Max Distance to Gas Station
     * Time Complexity : O(n log(maxGap))
     * Space Complexity: O(1)
     */
    public double minmaxGasDist(int[] stations, int k) {
        // 1. Find the largest original gap – upper bound of the answer
        double maxGap = 0;
        for (int i = 1; i < stations.length; i++) {
            double gap = stations[i] - stations[i - 1];
            if (gap > maxGap) maxGap = gap;
        }

        double left = 0;      // lower bound (could be 0)
        double right = maxGap;

        // 2. Binary search until precision 1e-6
        while (right - left > 1e-6) {
            double mid = (left + right) / 2.0;
            if (canAchieve(stations, k, mid)) {
                // If we can achieve this penalty, try smaller ones
                right = mid;
            } else {
                // Otherwise we need a larger penalty
                left = mid;
            }
        }

        // left and right are almost equal; return either
        return right;
    }

    /**
     * Check if a given penalty can be achieved with at most k new stations.
     * @param stations sorted positions of existing stations
     * @param k        number of new stations we can add
     * @param penalty  candidate maximum gap
     * @return true if feasible, false otherwise
     */
    private boolean canAchieve(int[] stations, int k, double penalty) {
        int needed = 0;

        for (int i = 1; i < stations.length; i++) {
            double gap = stations[i] - stations[i - 1];
            // Number of extra stations required for this gap:
            // ceil(gap / penalty) - 1
            needed += (int) Math.ceil(gap / penalty) - 1;
            if (needed > k) {          // early exit
                return false;
            }
        }

        return needed <= k;
    }

    // Example usage
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] stations1 = {1,2,3,4,5,6,7,8,9,10};
        System.out.println(s.minmaxGasDist(stations1, 9)); // 0.5

        int[] stations2 = {23,24,36,39,46,56,57,65,84,98};
        System.out.println(s.minmaxGasDist(stations2, 1)); // 14.0
    }
}
```

---

### 4.2  Python 3

```python
import math
from typing import List

class Solution:
    """
    LeetCode 774 – Minimize Max Distance to Gas Station
    Time Complexity : O(n log(max_gap))
    Space Complexity: O(1)
    """
    def minmaxGasDist(self, stations: List[int], k: int) -> float:
        # Upper bound: largest original gap
        max_gap = max(stations[i] - stations[i-1] for i in range(1, len(stations)))

        left, right = 0.0, max_gap

        # Binary search until 1e-6 precision
        while right - left > 1e-6:
            mid = (left + right) / 2.0
            if self._can_achieve(stations, k, mid):
                right = mid
            else:
                left = mid

        return right

    def _can_achieve(self, stations: List[int], k: int, penalty: float) -> bool:
        needed = 0
        for i in range(1, len(stations)):
            gap = stations[i] - stations[i-1]
            # ceil(gap / penalty) - 1
            needed += math.ceil(gap / penalty) - 1
            if needed > k:                # early exit
                return False
        return needed <= k

# Example usage
if __name__ == "__main__":
    s = Solution()
    print(s.minmaxGasDist([1,2,3,4,5,6,7,8,9,10], 9))   # 0.5
    print(s.minmaxGasDist([23,24,36,39,46,56,57,65,84,98], 1))  # 14.0
```

---

### 4.3  C++ (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    /**
     * LeetCode 774 – Minimize Max Distance to Gas Station
     * Time Complexity : O(n log(max_gap))
     * Space Complexity: O(1)
     */
    double minmaxGasDist(vector<int>& stations, int k) {
        // Upper bound: maximum original gap
        double maxGap = 0.0;
        for (size_t i = 1; i < stations.size(); ++i)
            maxGap = max(maxGap, double(stations[i] - stations[i-1]));

        double left = 0.0, right = maxGap;

        // Binary search until tolerance 1e-6
        while (right - left > 1e-6) {
            double mid = (left + right) / 2.0;
            if (canAchieve(stations, k, mid))
                right = mid;
            else
                left = mid;
        }
        return right;   // left and right are practically the same
    }

private:
    bool canAchieve(const vector<int>& stations, int k, double penalty) {
        long long needed = 0;
        for (size_t i = 1; i < stations.size(); ++i) {
            double gap = stations[i] - stations[i-1];
            needed += static_cast<long long>(ceil(gap / penalty)) - 1;
            if (needed > k) return false;   // early exit
        }
        return needed <= k;
    }
};

// Example usage
int main() {
    Solution s;
    vector<int> a1 = {1,2,3,4,5,6,7,8,9,10};
    cout << fixed << setprecision(5) << s.minmaxGasDist(a1, 9) << endl; // 0.50000

    vector<int> a2 = {23,24,36,39,46,56,57,65,84,98};
    cout << fixed << setprecision(5) << s.minmaxGasDist(a2, 1) << endl; // 14.00000
}
```

---

## 5.  Blog Article – “Minimize Max Distance to Gas Station: The Good, the Bad, and the Ugly”

> **Target Audience:** *Software engineers preparing for coding interviews, especially those targeting top tech companies.*  
> **Primary Keywords:** *LeetCode 774, Minimize Max Distance to Gas Station, binary search, algorithm interview, Java, Python, C++ solutions, hard interview questions, coding interview preparation.*

---

### 5.1  Introduction

When you’re scrolling through the “Hard” section of LeetCode, **774 – Minimize Max Distance to Gas Station** usually catches your eye. It’s the kind of problem that looks simple but hides a subtle twist. If you can solve it with a clean, efficient solution, you’ll not only earn those coveted points but also impress interviewers with your algorithmic maturity.

In this post, we’ll dissect the problem, reveal the elegant binary‑search‑on‑answer strategy, discuss pitfalls (the “bad”), and show how to write clean code in **Java**, **Python**, and **C++** (the “ugly” that still matters). By the end, you’ll be ready to answer the question in any interview room.

---

### 5.2  Problem Recap

- **Input:** Sorted array `stations[]` (positions of existing gas stations), integer `k` (number of new stations you can add).
- **Task:** Add `k` stations anywhere on the x‑axis to **minimize** the maximum distance between any two consecutive stations.
- **Output:** Smallest possible penalty (within `1e‑6` precision).

Why does this feel hard?  
Because you’re not just asked to compute a simple average or greedy split; you need to decide *where* to place each of `k` stations optimally.

---

### 5.3  The “Good”: Binary Search on Answer

The key observation:

> If you know the target penalty `P`, you can **count** how many extra stations are needed to make every gap ≤ `P`.  
> The number of stations needed is monotonic decreasing in `P`.

Thus, the problem becomes a **feasibility test** for a given `P`. With monotonicity, you can binary‑search `P`.

#### 5.3.1  Feasibility Formula

For any two adjacent existing stations at `x` and `y`:

```
gap = y - x
required = ceil(gap / P) - 1
```

Why?  
`ceil(gap / P)` gives the smallest number of sub‑gaps each ≤ `P`.  
Subtract 1 to get the number of *new* stations needed.

Sum over all gaps; if the sum ≤ `k`, the penalty `P` is achievable.

#### 5.3.2  Binary‑Search Loop

```
left = 0          // impossible (unless all stations coincide)
right = max_gap   // worst possible penalty
while right - left > 1e-6:
    mid = (left + right) / 2
    if canAchieve(mid): right = mid
    else: left = mid
return right
```

The loop stops when the interval is smaller than the required precision. The algorithm runs in `O(n log(max_gap))` time, `O(1)` space.

---

### 5.4  The “Bad”: Common Pitfalls

| Pitfall | What went wrong | How to fix |
|---------|-----------------|------------|
| **Precision bugs** | Using `int` for gaps or division by zero. | Work in `double`/`float`; guard against `P = 0`. |
| **Early‑exit missed** | Summing `needed` over all gaps can overflow if `k` is large. | Break early when `needed > k`. |
| **Wrong bounds** | Setting `left` to the minimum gap instead of `0`. | Start with `0`; `right` must be the maximum original gap. |
| **Not sorting** | Problem guarantees sorted input, but some solutions forget. | Assert or sort defensively if you’re not sure. |
| **Integer division** | `ceil(gap / P)` when `gap` and `P` are integers → truncation. | Convert to `double` or use `float` division. |

Avoiding these mistakes is crucial for “clean” solutions that pass all LeetCode test cases and look professional in an interview.

---

### 5.5  The “Ugly”: Real‑World Code (Java, Python, C++)

Below are ready‑to‑paste solutions. They’re intentionally **short** but demonstrate best practices:

1. **Early exit** in feasibility to avoid unnecessary computation.
2. **Math.ceil** (or `std::ceil`) to handle floating‑point division correctly.
3. **Typed language** (Java, C++) vs dynamic (Python) – all maintain the same logic.

#### 5.5.1  Java – The “OOP” Way

```java
private boolean canAchieve(int[] stations, int k, double penalty) {
    int needed = 0;
    for (int i = 1; i < stations.length; i++) {
        needed += (int) Math.ceil((stations[i] - stations[i-1]) / penalty) - 1;
        if (needed > k) return false;   // early exit
    }
    return needed <= k;
}
```

#### 5.5.2  Python – The “Syntactic Sugar”

```python
needed += math.ceil((stations[i] - stations[i-1]) / penalty) - 1
```

#### 5.5.3  C++ – The “Low‑Level” Nuance

```cpp
needed += static_cast<long long>(ceil(gap / penalty)) - 1;
```

Notice the cast to `long long` to avoid overflow when `k` can be up to `1e9`.

---

### 5.6  Why “Coding Interview Preparation” Matters

Top tech companies like **Google**, **Amazon**, **Facebook**, **Microsoft**, and **Apple** frequently ask hard interview questions. Problems like LeetCode 774 test:

- **Problem‑solving mindset** – Recognizing that you can binary‑search a continuous value.
- **Mathematical precision** – Understanding ceil and gap counting.
- **Clean coding** – Writing concise, testable functions in Java, Python, or C++.

**Interviewers** love solutions that:

1. Are **correct** for all edge cases.  
2. Run in **O(n log(max_gap))** (well within time limits).  
3. Show **algorithmic insight**: a single line explains why binary search works.

---

### 5.7  Take‑away Checklist

- Understand the monotonic relationship between penalty and stations needed.
- Implement the feasibility function correctly with `ceil(gap / P) - 1`.
- Use binary search with `1e‑6` tolerance.
- Test with both provided examples and edge cases (e.g., all stations far apart, `k = 0`, `k` huge).
- Write clean, commented code in your preferred language.

---

### 5.8  Final Thought

**774 – Minimize Max Distance to Gas Station** is more than a LeetCode problem; it’s a mini‑case study in algorithm design. By mastering the binary‑search‑on‑answer pattern, you’ll feel confident tackling similar “range‑minimization” problems in interviews.

If you’ve already solved this problem, consider posting your own version on Stack Overflow or GitHub—your code could become the next go‑to reference for future interviewees.

Happy coding, and may your interview rooms be full of efficient solutions!

--- 

*Author: Alex T. – Senior Software Engineer & Interview Coach.*  
*Subscribe for more algorithm deep‑dives and interview prep guides.*  

--- 

**End of Blog Article**  

--- 

### 5.9  Closing

You now have the *why*, *how*, and *exact code* to nail LeetCode 774 in any language. Remember, interviews value clarity as much as correctness. Keep your code concise, add comments that explain intent, and you’ll walk out of that room feeling both accomplished and confident. Happy coding! 🚀

--- 

**Tags:** LeetCode 774, binary search, algorithm interview, Java, Python, C++, hard interview questions, coding interview preparation.  

--- 

*Feel free to comment with your own solutions or ask follow‑up questions. Let’s code smarter, not harder!*  

--- 

**Word Count:** ~1,300 words (well‑within typical blog post length)  

--- 

*End of article.*  

--- 

With the article, the Java/Python/C++ solutions, and the in‑depth explanation, you have a full resource that covers **the problem, algorithmic insight, pitfalls, and clean code**—exactly what a job‑seeking engineer needs.