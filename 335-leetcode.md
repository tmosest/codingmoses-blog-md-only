---
title: LeetCode 335. Self Crossing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution – One‑pass, O(n) time & O(1) space

The classic “self‑crossing” problem can be solved in a single linear scan by
checking only the last few moves.  
Because the direction always turns counter‑clockwise (North → West → South → East → …),
the path can only intersect itself in a few specific patterns:

| Situation | Crossing condition |
|-----------|--------------------|
| **Standard** – the i‑th segment crosses the segment i‑2. | `dist[i] >= dist[i‑2] && dist[i‑1] <= dist[i‑3]` |
| **Special 4‑step** – the i‑th segment touches the segment i‑3 (when i≥4). | `dist[i‑1] == dist[i‑3] && dist[i] + dist[i‑4] >= dist[i‑2]` |
| **Special 5‑step** – the i‑th segment cuts a loop opened in the previous 5 steps (when i≥5). | `dist[i‑2] > dist[i‑4] && dist[i] + dist[i‑4] >= dist[i‑2] && dist[i‑1] + dist[i‑5] >= dist[i‑3] && dist[i‑1] <= dist[i‑3]` |

If any of these three conditions is true, the path has crossed itself.
Otherwise we continue scanning until the end of the array.

### Why it works

- After every four moves the direction repeats, so the only possible new
  intersection is with a segment that was drawn **2** or **4** steps ago.
- The third case handles the “over‑over” situation where the 5th segment
  overlaps a loop created by the previous segments.
- The conditions are derived from the relative lengths of the involved
  segments; they are both necessary and sufficient for a crossing to occur.

### Complexity

| Metric | Analysis |
|--------|----------|
| **Time** | `O(n)` – one pass through the array |
| **Space** | `O(1)` – only a few integer variables are used |

---

## 2.  Code

Below are clean, one‑pass implementations in **Java**, **Python**, and **C++**.

### 2.1 Java

```java
public class Solution {
    /**
     * Returns true if the path defined by distance[] crosses itself.
     * @param distance an array of positive integers
     * @return true if self‑crossing, false otherwise
     */
    public boolean isSelfCrossing(int[] distance) {
        if (distance == null || distance.length < 4) {
            return false;               // fewer than 4 moves can't cross
        }

        for (int i = 3; i < distance.length; i++) {
            // Case 1: Current line crosses the line 2 steps back
            if (distance[i] >= distance[i - 2] &&
                distance[i - 1] <= distance[i - 3]) {
                return true;
            }

            // Case 2: Current line touches the line 3 steps back
            if (i >= 4 &&
                distance[i - 1] == distance[i - 3] &&
                distance[i] + distance[i - 4] >= distance[i - 2]) {
                return true;
            }

            // Case 3: Current line crosses a loop opened in the last 5 moves
            if (i >= 5 &&
                distance[i - 2] > distance[i - 4] &&
                distance[i] + distance[i - 4] >= distance[i - 2] &&
                distance[i - 1] + distance[i - 5] >= distance[i - 3] &&
                distance[i - 1] <= distance[i - 3]) {
                return true;
            }
        }

        return false;
    }
}
```

### 2.2 Python

```python
class Solution:
    def isSelfCrossing(self, distance: list[int]) -> bool:
        """Return True if the walk crosses itself."""
        if len(distance) < 4:
            return False

        for i in range(3, len(distance)):
            # 1st case
            if (distance[i] >= distance[i - 2] and
                distance[i - 1] <= distance[i - 3]):
                return True

            # 2nd case
            if (i >= 4 and
                distance[i - 1] == distance[i - 3] and
                distance[i] + distance[i - 4] >= distance[i - 2]):
                return True

            # 3rd case
            if (i >= 5 and
                distance[i - 2] > distance[i - 4] and
                distance[i] + distance[i - 4] >= distance[i - 2] and
                distance[i - 1] + distance[i - 5] >= distance[i - 3] and
                distance[i - 1] <= distance[i - 3]):
                return True

        return False
```

### 2.3 C++

```cpp
class Solution {
public:
    bool isSelfCrossing(vector<int>& distance) {
        if (distance.size() < 4) return false;

        for (int i = 3; i < distance.size(); ++i) {
            // Case 1
            if (distance[i] >= distance[i - 2] &&
                distance[i - 1] <= distance[i - 3]) {
                return true;
            }

            // Case 2
            if (i >= 4 &&
                distance[i - 1] == distance[i - 3] &&
                distance[i] + distance[i - 4] >= distance[i - 2]) {
                return true;
            }

            // Case 3
            if (i >= 5 &&
                distance[i - 2] > distance[i - 4] &&
                distance[i] + distance[i - 4] >= distance[i - 2] &&
                distance[i - 1] + distance[i - 5] >= distance[i - 3] &&
                distance[i - 1] <= distance[i - 3]) {
                return true;
            }
        }
        return false;
    }
};
```

All three snippets run in `O(n)` time and use only constant extra space.

---

## 3.  Blog Article

> **Title**  
> Self‑Crossing in LeetCode 335: The Good, The Bad, and The Ugly

> **Meta‑Description**  
> Learn how to solve LeetCode 335 – “Self Crossing” – in Java, Python, and C++ with a one‑pass O(n) algorithm. Understand the pattern‑matching logic, pitfalls, and edge cases. Perfect your interview prep for algorithm questions.

---

### 3.1 Introduction

When it comes to algorithm interviews, LeetCode problems that involve geometry or path simulation often look intimidating. “Self Crossing” (Problem 335) is one such challenge: you walk in a counter‑clockwise spiral and need to determine whether your path ever crosses itself.

At first glance, you might think you have to keep a full set of coordinates or use a line‑segment intersection routine—an O(n²) nightmare. But the real secret lies in the **directional pattern** of the walk. Once you see it, the solution collapses to a handful of simple inequality checks.

This post will walk through that insight, present a concise one‑pass solution, discuss common pitfalls, and give you ready‑to‑copy code in Java, Python, and C++. By the end, you’ll be able to explain the problem, the algorithm, and the edge‑case logic to any interview panel.

---

### 3.2 Problem Recap

You start at the origin `(0,0)` on a 2‑D plane.  
You receive an array `distance[]` of positive integers.  
You move:

1. `distance[0]` meters **North**  
2. `distance[1]` meters **West**  
3. `distance[2]` meters **South**  
4. `distance[3]` meters **East**  
5. … and repeat counter‑clockwise.

Return `true` if the path ever crosses itself; otherwise `false`.

> **Constraints**  
> • `1 ≤ distance.length ≤ 10^5`  
> • `1 ≤ distance[i] ≤ 10^5`

The challenge is to do it in **linear time** and **constant space**.

---

### 3.3 The Good – Why a One‑Pass Works

Because the direction changes predictably (North → West → South → East → North …), each new segment can only intersect the previous segments in a **very limited set of ways**.

After the first four moves the direction repeats, so any new segment can only possibly touch:

- The segment drawn two moves ago (a 90° turn).
- The segment drawn three moves ago (a 180° turn, i.e., a “touch”).
- The segment drawn five moves ago (an “over‑over” situation that closes a loop).

These are the only possibilities that can produce a crossing. The rest of the plane is already “cleared” by the previous pattern.

Hence we only need to keep track of the last few distances—no need for a list of coordinates or a line‑segment intersection routine.

---

### 3.4 The Bad – Common Mistakes

| Mistake | Why it Fails | Fix |
|---------|--------------|-----|
| **Using a full coordinate list** | O(n²) time and O(n) memory, plus floating‑point errors. | Keep only the last 6 distances; compare inequalities. |
| **Checking only two steps back** | Missing the “touch” case when the i‑th segment equals the i‑3rd. | Add the 4‑step “touch” condition. |
| **Using absolute position comparisons** | Requires recomputing positions at each step; unnecessary. | Use relative inequalities between distances. |
| **Assuming symmetry** | The path may have asymmetrical lengths; crossing may happen in irregular patterns. | Use the exact conditions derived from geometry. |

---

### 3.5 The Ugly – Edge Cases That Trip You Up

| Edge case | Typical error | How the algorithm handles it |
|-----------|---------------|-----------------------------|
| **Exactly 4 moves** | Some implementations forget to handle `i == 4` properly. | The second condition (`i >= 4`) checks the touch scenario. |
| **Large numbers causing overflow** | Adding two `int` values can overflow `int` in Java/C++. | Use `long` or cast to `long` during addition (`distance[i] + distance[i-4]`). |
| **Equal lengths creating degenerate loops** | A sequence like `[1,1,1,1,1]` may look non‑crossing, but the 5‑step rule captures it. | Third condition accounts for the “over‑over” loop. |
| **Very long input** | Recursion or excessive allocation may hit limits. | Iterative loop with constant auxiliary storage. |

---

### 3.6 The Algorithm in Detail

```text
for i from 3 to n-1
    if distance[i] >= distance[i-2]   // longer or equal to the segment 2 steps back
       and distance[i-1] <= distance[i-3]  // the middle segment is not longer
          return true

    if i >= 4 and
       distance[i-1] == distance[i-3]          // touching the 3‑step‑back segment
       and distance[i] + distance[i-4] >= distance[i-2]
          return true

    if i >= 5 and
       distance[i-2] > distance[i-4]           // opening a loop
       and distance[i] + distance[i-4] >= distance[i-2]
       and distance[i-1] + distance[i-5] >= distance[i-3]
       and distance[i-1] <= distance[i-3]
          return true

return false
```

The three blocks correspond exactly to the three crossing scenarios explained earlier.

---

### 3.7 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` – one scan | `O(n)` – one scan | `O(n)` – one scan |
| **Space** | `O(1)` – 5–6 integer variables | `O(1)` | `O(1)` |

The algorithm works for `n = 10^5` comfortably on all major platforms.

---

### 3.8 Code Snippets

#### Java

```java
public class Solution {
    public boolean isSelfCrossing(int[] distance) {
        if (distance == null || distance.length < 4) return false;
        for (int i = 3; i < distance.length; i++) {
            if (distance[i] >= distance[i-2] &&
                distance[i-1] <= distance[i-3]) return true;
            if (i >= 4 &&
                distance[i-1] == distance[i-3] &&
                distance[i] + distance[i-4] >= distance[i-2]) return true;
            if (i >= 5 &&
                distance[i-2] > distance[i-4] &&
                distance[i] + distance[i-4] >= distance[i-2] &&
                distance[i-1] + distance[i-5] >= distance[i-3] &&
                distance[i-1] <= distance[i-3]) return true;
        }
        return false;
    }
}
```

#### Python

```python
class Solution:
    def isSelfCrossing(self, distance: list[int]) -> bool:
        if len(distance) < 4:
            return False
        for i in range(3, len(distance)):
            if distance[i] >= distance[i-2] and distance[i-1] <= distance[i-3]:
                return True
            if i >= 4 and distance[i-1] == distance[i-3] and \
               distance[i] + distance[i-4] >= distance[i-2]:
                return True
            if i >= 5 and distance[i-2] > distance[i-4] and \
               distance[i] + distance[i-4] >= distance[i-2] and \
               distance[i-1] + distance[i-5] >= distance[i-3] and \
               distance[i-1] <= distance[i-3]:
                return True
        return False
```

#### C++

```cpp
class Solution {
public:
    bool isSelfCrossing(vector<int>& distance) {
        if (distance.size() < 4) return false;
        for (int i = 3; i < distance.size(); ++i) {
            if (distance[i] >= distance[i-2] &&
                distance[i-1] <= distance[i-3]) return true;
            if (i >= 4 &&
                distance[i-1] == distance[i-3] &&
                distance[i] + distance[i-4] >= distance[i-2]) return true;
            if (i >= 5 &&
                distance[i-2] > distance[i-4] &&
                distance[i] + distance[i-4] >= distance[i-2] &&
                distance[i-1] + distance[i-5] >= distance[i-3] &&
                distance[i-1] <= distance[i-3]) return true;
        }
        return false;
    }
};
```

Feel free to copy, paste, and run. Remember to test the tricky cases:

```text
[1,2,3,4]          -> false
[1,1,1,1]          -> true (touch case)
[1,1,2,1,1,1]      -> true (5‑step loop)
```

---

### 3.9 How to Discuss This in an Interview

1. **Explain the movement pattern**: “We walk North → West → South → East → …, so after the first 4 steps the direction repeats.”

2. **State the limited intersection possibilities**:  
   - Two steps back (90° turn).  
   - Three steps back (180° touch).  
   - Five steps back (loop closure).

3. **Present the inequalities**:  
   Show the three conditions.  
   Use a simple diagram if you’re on paper.

4. **Mention the time/space complexity**:  
   `O(n)` time, `O(1)` space.

5. **Talk about edge‑case safety**:  
   Overflow handling, array length checks, large inputs.

This concise yet rigorous explanation demonstrates depth of understanding—a hallmark of a top‑tier software engineer.

---

### 4.  Conclusion

LeetCode 335 “Self Crossing” is deceptively simple once you spot the *directional pattern*. The key is to treat the problem as a **sequence of relative inequalities** instead of absolute geometry. With that perspective, a one‑pass `O(n)` algorithm emerges naturally.

We covered:

- The **why** behind the one‑pass trick.  
- The **what** of common pitfalls.  
- The **how** of edge‑case handling.  
- Ready‑to‑use code in **Java, Python, C++**.

Now you can confidently tackle this question, explain your solution in any language, and impress interviewers with both elegance and efficiency. Happy coding—and good luck on your next interview!