---
title: LeetCode 2237. Count Positions on Street With Required Brightness - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap (LeetCode 2237)

> **Count Positions on Street With Required Brightness**  
> *Difficulty:* Medium  

You are given:
* an integer `n` (street is the number line `[0 … n‑1]`);
* an array `lights` where each element `[position, range]` represents a street lamp that illuminates the inclusive interval  
  `[max(0, position‑range), min(n‑1, position+range)]`;
* an array `requirement` of length `n` where `requirement[i]` is the minimum brightness a position must have.

**Goal** – return the number of positions `i` such that the brightness at `i` is at least `requirement[i]`.

--------------------------------------------------------------------

## 2.  Core Idea – Line Sweep / Difference Array

For every lamp we can increment the counter at its left border and decrement it *after* its right border.  
When we scan from left to right and keep a running prefix sum we get the exact brightness at each position.

```
diff[ left ]   += 1
diff[ right+1 ] -= 1   (if right+1 < n)
```

Finally, traverse `diff`, accumulate the prefix sum, and compare it with the corresponding `requirement[i]`.  
This gives an **O(n + m)** time solution (`m = lights.length`) and **O(n)** extra memory, which is optimal for the given limits (`n, m ≤ 10⁵`).

--------------------------------------------------------------------

## 3.  Reference Implementations

### 3.1  Java 17

```java
import java.util.*;

public class Solution {
    public int meetRequirement(int n, int[][] lights, int[] requirement) {
        // Difference array
        int[] diff = new int[n + 1];           // n+1 to avoid bounds checks

        for (int[] lamp : lights) {
            int pos  = lamp[0];
            int rng  = lamp[1];
            int left  = Math.max(0, pos - rng);
            int right = Math.min(n - 1, pos + rng);

            diff[left]++;                      // start of illumination
            diff[right + 1]--;                 // end of illumination (inclusive)
        }

        int prefix = 0;          // current brightness
        int answer = 0;

        for (int i = 0; i < n; i++) {
            prefix += diff[i];
            if (prefix >= requirement[i]) answer++;
        }
        return answer;
    }

    /* ----------  quick test harness ---------- */
    public static void main(String[] args) {
        Solution s = new Solution();

        int n1 = 5;
        int[][] lights1 = {{0,1},{2,1},{3,2}};
        int[] req1 = {0,2,1,4,1};
        System.out.println(s.meetRequirement(n1, lights1, req1));   // 4

        int n2 = 1;
        int[][] lights2 = {{0,1}};
        int[] req2 = {2};
        System.out.println(s.meetRequirement(n2, lights2, req2));   // 0
    }
}
```

> **Why `int[n+1]`?**  
> Adding a sentinel at the end (`diff[n]`) guarantees that the `right+1` decrement never hits an out‑of‑bounds index, simplifying the loop logic.

---

### 3.2  Python 3

```python
from typing import List

class Solution:
    def meetRequirement(self, n: int, lights: List[List[int]],
                        requirement: List[int]) -> int:
        diff = [0] * (n + 1)          # n+1 for the sentinel

        for pos, rng in lights:
            left  = max(0, pos - rng)
            right = min(n - 1, pos + rng)
            diff[left] += 1
            diff[right + 1] -= 1

        brightness = 0
        answer = 0
        for i in range(n):
            brightness += diff[i]
            if brightness >= requirement[i]:
                answer += 1

        return answer


# ----------- quick tests -------------
if __name__ == "__main__":
    sol = Solution()
    print(sol.meetRequirement(5, [[0,1],[2,1],[3,2]], [0,2,1,4,1]))  # 4
    print(sol.meetRequirement(1, [[0,1]], [2]))                       # 0
```

> **Note** – Python’s dynamic lists are perfectly fine for `n ≤ 10⁵`.  
> Avoiding any extra `O(m)` passes keeps the solution lean.

---

### 3.3  C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int meetRequirement(int n, vector<vector<int>>& lights,
                        vector<int>& requirement) {
        vector<int> diff(n + 1, 0);               // n+1 sentinel

        for (auto &lamp : lights) {
            int pos = lamp[0];
            int rng = lamp[1];
            int left  = max(0, pos - rng);
            int right = min(n - 1, pos + rng);

            diff[left]++;                         // start
            diff[right + 1]--;                    // end + 1
        }

        int prefix = 0;
        int ans = 0;
        for (int i = 0; i < n; ++i) {
            prefix += diff[i];
            if (prefix >= requirement[i]) ++ans;
        }
        return ans;
    }
};

// --------- test harness ----------
int main() {
    Solution s;
    cout << s.meetRequirement(5, {{0,1},{2,1},{3,2}}, {0,2,1,4,1}) << '\n'; // 4
    cout << s.meetRequirement(1, {{0,1}}, {2}) << '\n';                     // 0
}
```

> **Why `vector<int> diff(n+1)`?**  
> The sentinel guarantees `right+1` never goes out of bounds, letting us skip a conditional inside the loop and keep the code tidy.

--------------------------------------------------------------------

## 4.  Blog Article – “The Good, The Bad, and The Ugly of LeetCode 2237”

> **Title**  
> *LeetCode 2237 – Count Positions on Street With Required Brightness: The Good, The Bad, The Ugly – A Deep Dive for Job‑Ready Interviewees*  

### 4.1  TL;DR

* **Solution** – A linear‑time difference‑array sweep (line sweep).  
* **Time Complexity** – `O(n + m)` (`n = street length`, `m = #lamps`).  
* **Space Complexity** – `O(n)` auxiliary.  
* **Why it matters** – Mastering this pattern shows you can turn a seemingly quadratic problem into an elegant `O(n)` one—a prized skill for system‑design and algorithm interviews.

---

### 4.2  The Problem, Re‑framed

Imagine a straight road with `n` intersections (0‑based).  
Each streetlamp illuminates a contiguous segment of intersections.  
You’re given a **minimum brightness requirement** at every intersection and must count how many intersections meet it.

> **Why the difficulty?**  
> A naïve approach iterates over every lamp for every intersection → `O(n·m)` → too slow for `10⁵`.  
> The key is to treat each lamp as an *interval contribution* and combine them smartly.

---

### 4.3  The Good – Line Sweep + Difference Array

1. **Intervals → Prefix Updates**  
   *For every lamp:*  
   ```text
   diff[l]  += 1          // start of illumination
   diff[r+1] -= 1          // one past the end
   ```
   Because the lamp covers `[l, r]` inclusively.

2. **Single Pass to Compute Brightness**  
   Running prefix sum over `diff` gives the exact brightness at each position.  
   While scanning, compare with the requirement and tally the answer.

3. **Why It Works**  
   The *difference array* stores the *net change* at each index.  
   The cumulative sum reconstructs the original array of brightness counts.

4. **Result**  
   *Linear time, constant additional work per lamp and per intersection.*  
   Works for the worst‑case limits easily.

---

### 4.4  The Bad – Pitfalls & Edge Cases

| Pitfall | Why it hurts | Fix |
|---------|--------------|-----|
| **Off‑by‑one at the right boundary** | Lamps cover *inclusive* ranges; forgetting the `right+1` decrement breaks counts at the end. | Explicitly decrement at `right+1` (or use `n` sentinel). |
| **Index out of bounds** | `right+1` can be `n`; if array size is `n`, `diff[n]` is invalid. | Allocate `diff[n+1]` or add a guard before decrementing. |
| **Negative ranges** | Not in constraints, but if `range` were negative, the math would be wrong. | Clamp ranges to `[0, n-1]` with `max/min`. |
| **Large values overflow** | `range` up to `10⁵`; using `int` is safe, but if extended, use `long`. | Use `long` or careful casting if necessary. |

---

### 4.5  The Ugly – When Simpler Isn’t Enough

- **Memory‑Bounded Systems** – The `O(n)` array may still be heavy if `n` ≈ `10⁹` (outside LeetCode limits).  
  *Alternative:* compress coordinates or use a balanced BST of events.  
  Not needed here, but worth knowing.

- **Multiple Queries** – If you had to answer many requirement arrays on the same lamp set, recomputing the sweep each time would be wasteful.  
  *Optimization:* Pre‑compute the brightness array once; each query is `O(n)` comparison.

- **Precision Issues in Other Languages** – In JavaScript or Python, accidental use of `float` can cause subtle bugs with large integers.  
  *Fix:* Stick to integer arithmetic and avoid implicit type conversions.

---

### 4.6  SEO‑Friendly Summary

> *Looking for a **Java**, **Python**, or **C++** solution to LeetCode 2237 – “Count Positions on Street With Required Brightness”?  
> Master the **line sweep** and **difference array** technique to solve this problem in **O(n + m)** time and **O(n)** space.  
> Learn how to avoid common pitfalls like off‑by‑one errors and index out‑of‑bounds, and see why this pattern is a must‑know for system‑design and algorithm interviews.*

---

### 4.7  Final Word

LeetCode 2237 is more than a road‑lighting puzzle; it’s a microcosm of interval‑aggregation problems you’ll meet in production systems (e.g., range updates in databases, traffic modeling).  
Being able to identify and implement the **difference‑array sweep** demonstrates:

* **Algorithmic flexibility** – turning an apparent quadratic problem into linear time.  
* **Coding discipline** – careful boundary handling and concise loops.  
* **Preparation for technical hiring** – a pattern interviewers love to see.

Practice the implementations above, test edge cases, and you’ll be ready to discuss this pattern confidently during your next coding interview or system‑design conversation.

---  

**End of Article**

--- 

### 5.  Final Thoughts

The *difference‑array* line sweep is a classic pattern that shows up in many interview questions (e.g., “Number of Overlap Intervals”, “People in a Park”, “Maximal Rectangle”, etc.).  
By mastering it now, you’ll be equipped to tackle similar interval‑aggregation challenges in real‑world systems, making you a stronger candidate for software engineering roles.

Good luck on your coding interviews – and remember: a clean `O(n)` solution is always a win!