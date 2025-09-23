---
title: LeetCode 335. Self Crossing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📈 LeetCode 335 – “Self Crossing”  
### A Deep‑Dive into the Algorithm, Code, and Interview‑Style Insight

> **Problem**  
> Given an array of distances, you start at `(0,0)` and move  
> north → west → south → east → … (counter‑clockwise).  
> Return `true` if the path crosses itself at any point, otherwise `false`.

> **Constraints**  
> * `1 ≤ distance.length ≤ 10^5`  
> * `1 ≤ distance[i] ≤ 10^5`

---

### TL;DR – One Pass, O(n) Time, O(1) Space

```text
- Keep only the last 4 segment lengths.
- At step i, check three crossing patterns:
  1) 4‑segment cross (i >= 3)
  2) 5‑segment overlapping cross (i >= 4)
  3) 6‑segment special case (i >= 5)
```

---

## 1. Why This Problem Is a “Hidden Gem” in Interviews

* **Geometry + Pattern Recognition** – Candidates must translate a geometric “spiral” into logical conditions.
* **Constant‑Space Trick** – Many naïve solutions keep the whole path, leading to O(n²) time or O(n) memory. The optimal solution uses only the last 4 moves.
* **Corner Cases** – The “6‑segment special case” is easy to miss; failing it costs the interview.

> *Good*: Clean O(n) algorithm.  
> *Bad*: Too many edge‑case checks.  
> *Ugly*: Quadratic time, excessive memory.

---

## 2. The Algorithm (One‑Pass, Constant Memory)

| i | Segment  | Direction | Condition to Detect Crossing |
|---|----------|-----------|-----------------------------|
| **i ≥ 3** | 4th segment | Check if `dist[i] >= dist[i-2]` **and** `dist[i-1] <= dist[i-3]` |
| **i ≥ 4** | 5th segment | Check if `dist[i-1] == dist[i-3]` **and** `dist[i] + dist[i-4] >= dist[i-2]` |
| **i ≥ 5** | 6th segment | Check if `dist[i-2] >= dist[i-4]` **and** `dist[i-1] <= dist[i-3]` **and** `dist[i] + dist[i-4] >= dist[i-2]` **and** `dist[i-1] + dist[i-5] >= dist[i-3]` |

The intuition:  
- **Case 1** – the current line meets the line two steps back (simple U‑shaped cross).  
- **Case 2** – the current line meets the line three steps back (overlap).  
- **Case 3** – the current line meets the line four steps back (complex spiral).

All checks are constant‑time comparisons of distances.

---

## 3. Code Implementations

### 3.1 Java

```java
public class Solution {
    public boolean isSelfCrossing(int[] distance) {
        for (int i = 3; i < distance.length; i++) {
            // Case 1: Current line crosses the line two steps ahead
            if (distance[i] >= distance[i - 2] &&
                distance[i - 1] <= distance[i - 3]) {
                return true;
            }
            // Case 2: Current line meets the line three steps ahead
            if (i >= 4) {
                if (distance[i - 1] == distance[i - 3] &&
                    distance[i] + distance[i - 4] >= distance[i - 2]) {
                    return true;
                }
            }
            // Case 3: Current line meets the line four steps ahead
            if (i >= 5) {
                if (distance[i - 2] >= distance[i - 4] &&
                    distance[i - 1] <= distance[i - 3] &&
                    distance[i] + distance[i - 4] >= distance[i - 2] &&
                    distance[i - 1] + distance[i - 5] >= distance[i - 3]) {
                    return true;
                }
            }
        }
        return false;
    }
}
```

### 3.2 Python

```python
class Solution:
    def isSelfCrossing(self, distance: List[int]) -> bool:
        for i in range(3, len(distance)):
            # Case 1
            if distance[i] >= distance[i - 2] and distance[i - 1] <= distance[i - 3]:
                return True

            # Case 2
            if i >= 4 and distance[i - 1] == distance[i - 3] \
               and distance[i] + distance[i - 4] >= distance[i - 2]:
                return True

            # Case 3
            if i >= 5 and distance[i - 2] >= distance[i - 4] \
               and distance[i - 1] <= distance[i - 3] \
               and distance[i] + distance[i - 4] >= distance[i - 2] \
               and distance[i - 1] + distance[i - 5] >= distance[i - 3]:
                return True
        return False
```

### 3.3 C++

```cpp
class Solution {
public:
    bool isSelfCrossing(vector<int>& distance) {
        for (int i = 3; i < distance.size(); ++i) {
            // Case 1
            if (distance[i] >= distance[i-2] && distance[i-1] <= distance[i-3])
                return true;

            // Case 2
            if (i >= 4 &&
                distance[i-1] == distance[i-3] &&
                distance[i] + distance[i-4] >= distance[i-2])
                return true;

            // Case 3
            if (i >= 5 &&
                distance[i-2] >= distance[i-4] &&
                distance[i-1] <= distance[i-3] &&
                distance[i] + distance[i-4] >= distance[i-2] &&
                distance[i-1] + distance[i-5] >= distance[i-3])
                return true;
        }
        return false;
    }
};
```

---

## 4. Time & Space Complexity

| Metric | Value |
|--------|-------|
| **Time** | `O(n)` – single pass over the input. |
| **Space** | `O(1)` – only a handful of integer variables. |

---

## 5. Edge Cases & Common Pitfalls

| Pitfall | How to Avoid It |
|---------|-----------------|
| Not checking `i >= 5` before accessing `i-5` | Add guard `if (i >= 5)` before the third case. |
| Mis‑ordering comparisons | Follow the exact inequality order: ≥ and ≤, not the reverse. |
| Using an array of previous distances | It works but wastes memory; still acceptable for interview, but O(n) space is less elegant. |
| Forgetting the “special” 6‑segment case | Test with `distance = [1,1,1,2,1]`; you’ll get false if omitted. |

---

## 6. Why This Solution Rocks for Interviews

1. **Demonstrates Pattern Recognition** – You translate a geometric spiral into simple arithmetic checks.  
2. **Shows Efficiency** – O(n) time and O(1) memory are impressive for a “Hard” LeetCode problem.  
3. **Clear, Readable Code** – Easy to explain during a technical interview.  
4. **Edge‑Case Awareness** – Handling the 6‑segment special case proves thoroughness.

---

## 7. Blog Wrap‑Up & SEO Highlights

> **LeetCode 335 – Self Crossing**  
> *“One Pass, Constant Space, No Geometry Needed.”*  
> 
> In this article, we explored the **Self Crossing** problem, uncovered the three crossing patterns, and delivered clean implementations in **Java, Python, and C++**. We also broke down the *good*, *bad*, and *ugly* parts of common approaches and gave you an interview‑ready, O(n) solution.

### SEO Keywords (inserted naturally)

- Leetcode Self Crossing solution  
- Java self crossing algorithm  
- Python self crossing implementation  
- C++ self crossing code  
- Interview coding challenge  
- One‑pass algorithm  
- Constant memory path crossing  
- Algorithm interview prep  
- Data structures & algorithms  

---

### Final Thought

Mastering “Self Crossing” showcases your ability to transform a geometric puzzle into a logical, efficient solution – exactly the kind of skill hiring managers look for. Keep the patterns in mind, practice the edge cases, and you’ll confidently ace this and other hard LeetCode challenges. Happy coding! 🚀