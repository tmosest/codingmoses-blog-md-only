---
title: LeetCode 2139. Minimum Moves to Reach Target Score - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Overview

> **Problem 2139 – Minimum Moves to Reach Target Score**  
> You start with the integer `1`.  
> In one move you can:  
> * **Increment** (`x = x + 1`) – unlimited times.  
> * **Double** (`x = 2 * x`) – at most `maxDoubles` times.  
>  
> Given `target` and `maxDoubles`, find the minimum number of moves to reach `target`.

The most efficient strategy is to **reverse the process**: start from `target` and work backwards to `1`.  
Why? Because when you go backwards you have a clear choice – either “halve” (the inverse of double) or “decrement” (the inverse of increment).  

### Greedy Reverse Algorithm

```
moves = 0
while target > 1 and maxDoubles > 0:
    if target is odd:
        target -= 1          // one decrement
        moves += 1
    target //= 2              // one halve (double in forward)
    moves += 1
    maxDoubles -= 1

// no doubles left – just decrement to 1
moves += target - 1
```

* Each iteration uses one double (in forward) or one halve (backward).  
* If the current `target` is odd we must first decrement (cost = 1) before halving.  
* The loop runs at most `maxDoubles` times, which is ≤ 100, and the remaining steps are linear in the (small) difference `target‑1`.  
* Time complexity: **O(log target)** because each halve reduces the value at least by half.  
* Space complexity: **O(1)**.

---

## 2.  Code

Below are clean, idiomatic implementations in **Java, Python, and C++** that follow the algorithm described above.

> **Tip:** All three codes compile with the standard 17/3.8/20 compiler and use only basic language features.

### Java (Java 17)

```java
/**
 * 2139. Minimum Moves to Reach Target Score
 *
 * Time:  O(log target)
 * Space: O(1)
 */
public class Solution {
    public int minMoves(int target, int maxDoubles) {
        int moves = 0;
        while (target > 1 && maxDoubles > 0) {
            if ((target & 1) == 1) {          // odd
                target--;                     // decrement
                moves++;                      // one move
            }
            target >>= 1;                     // halve
            moves++;                          // double in forward
            maxDoubles--;                     // used one double
        }
        // no doubles left – just decrement to 1
        moves += target - 1;
        return moves;
    }
}
```

### Python 3 (≥ 3.8)

```python
"""
2139. Minimum Moves to Reach Target Score
"""

class Solution:
    def minMoves(self, target: int, maxDoubles: int) -> int:
        moves = 0
        while target > 1 and maxDoubles:
            if target & 1:          # odd
                target -= 1
                moves += 1
            target >>= 1
            moves += 1
            maxDoubles -= 1
        return moves + target - 1
```

### C++ (C++20)

```cpp
/**
 * 2139. Minimum Moves to Reach Target Score
 * Time: O(log target)
 * Space: O(1)
 */
class Solution {
public:
    int minMoves(int target, int maxDoubles) {
        int moves = 0;
        while (target > 1 && maxDoubles > 0) {
            if (target & 1) {          // odd
                --target;             // decrement
                ++moves;
            }
            target >>= 1;              // halve
            ++moves;                   // double in forward
            --maxDoubles;
        }
        return moves + target - 1;
    }
};
```

All three snippets are ready to paste into LeetCode’s editor or any C++/Java/Python project.

---

## 3.  Blog Article

> **Title (SEO‑Optimized):**  
> *“How to Crack LeetCode 2139 – Minimum Moves to Reach Target Score (Java, Python, C++)”*  

### 3.1  The Good

| ✅ | Why It’s Great |
|---|----------------|
| **Intuitive Reverse Thinking** | Working backwards turns a seemingly complex “double‑then‑increment” sequence into a simple “halve or decrement” loop. |
| **Linear‑Time Greedy** | The algorithm runs in `O(log target)` time and uses only constant extra space – perfect for interview stress‑tests. |
| **Clear Code** | The Java, Python, and C++ implementations are each 10‑15 lines and avoid obscure tricks. |
| **Works for All Edge Cases** | Handles `maxDoubles = 0`, `target = 1`, large targets (`≤ 10^9`), and the maximal `maxDoubles = 100`. |

### 3.2  The Bad

| ⚠️ | Issue |
|---|-------|
| **Harder to Spot the Greedy** | Beginners might try a BFS or DP approach and miss the simple reverse greedy. |
| **Misunderstanding of “Odd” Case** | Forgetting that an odd `target` must be decremented first leads to off‑by‑one errors. |
| **Edge‑Case Bug** | Not adding the remaining `target-1` after the loop results in an undercount when doubles run out early. |

### 3.3  The Ugly

| 🕷️ | Pain Point |
|---|-------------|
| **Infinite Loop Risk** | A subtle bug (`target >>= 1` instead of `target /= 2`) might cause a loop that never ends if the decrement step is omitted. |
| **Non‑intuitive Sign Conventions** | Some languages (e.g., C++) require careful handling of signed integers to avoid overflow when shifting negative numbers – but here `target` is always positive. |
| **Misleading Complexity Claims** | A naive DP solution could claim `O(target)` time, which is huge for `target = 10^9`; always emphasize the `O(log target)` nature of the greedy. |

---

## 4.  Why This Blog Will Get You Hired

1. **SEO‑Friendly Keywords** – The title and headers contain “LeetCode 2139”, “Minimum Moves”, “Java”, “Python”, “C++”, and “interview”, which are search terms recruiters and recruiters use when looking for talent.
2. **Clear Problem Breakdown** – Readers can instantly see the problem statement, constraints, and sample cases.
3. **Step‑by‑Step Solution** – The reverse‑thinking narrative is educational and demonstrates your ability to convert complex problems into clean, efficient algorithms.
4. **Cross‑Language Mastery** – Providing three implementations shows breadth of skill, a key factor for full‑stack or system‑design interviews.
5. **Edge‑Case Awareness** – Discussing pitfalls signals that you’re detail‑oriented, a quality that hiring managers value.

> **Pro Tip:**  
> At the end of the article, add a small “Try It Yourself” section with a simple online compiler link and a call‑to‑action: *“Got a better trick? Drop a comment below – I’m always eager to learn!”*  
> This boosts engagement and improves search rankings.

---

## 5.  Final Checklist

- ✅ Problem statement with constraints  
- ✅ Greedy reverse algorithm explanation  
- ✅ Java, Python, C++ implementations  
- ✅ SEO‑optimized blog outline  
- ✅ Good/Bad/Ugly analysis  
- ✅ Hiring‑relevant takeaways

Happy coding and best of luck landing that dream job!