---
title: LeetCode 335. Self Crossing - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔍 335 – Self‑Crossing: One‑Pass O(n) Solution (Java / Python / C++)

> **Problem**  
> You start at the origin (0, 0).  
> The array `distance` tells you how far to go each step, turning counter‑clockwise every time:  
> 0 → north, 1 → west, 2 → south, 3 → east, 4 → north …  
> Return `true` if at any point your path crosses itself.

> **Constraints**  
> * `1 ≤ distance.length ≤ 10⁵`  
> * `1 ≤ distance[i] ≤ 10⁵`

> **Key Insight**  
> A crossing can only occur once the path starts to “wind” around itself.  
> For any index `i ≥ 3` there are **three** distinct ways the current segment can intersect the previous ones:

1. **Case 1 (Simple self‑intersection)** – current segment touches the segment 3 steps back  
   `distance[i] ≥ distance[i‑2] && distance[i‑1] ≤ distance[i‑3]`

2. **Case 2 (Edge‑touching while “shifting” inward)** – current segment touches the segment 4 steps back  
   `distance[i‑1] == distance[i‑3] && distance[i] + distance[i‑4] >= distance[i‑2]`

3. **Case 3 (The “spiral” corner where all four surrounding segments touch)** – current segment touches the segment 5 steps back  
   `distance[i‑2] == distance[i‑4] && distance[i] + distance[i‑4] >= distance[i‑2] && distance[i‑1] == distance[i‑3] && distance[i‑1] + distance[i‑5] >= distance[i‑3]`

If any of these conditions holds, the path has crossed itself.

---

## ⚙️ 1. Java Implementation

```java
/**
 * LeetCode 335 – Self Crossing
 * One‑pass, O(n) time, O(1) space
 */
public class SelfCrossing {
    public boolean isSelfCrossing(int[] distance) {
        int n = distance.length;
        if (n < 4) return false;

        for (int i = 3; i < n; i++) {
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
                distance[i - 2] == distance[i - 4] &&
                distance[i] + distance[i - 4] >= distance[i - 2] &&
                distance[i - 1] == distance[i - 3] &&
                distance[i - 1] + distance[i - 5] >= distance[i - 3]) {
                return true;
            }
        }
        return false;
    }
}
```

**Why it works**  
- The loop starts at `i = 3` because crossing requires at least four segments.  
- Each `if` block captures one of the only three ways a new segment can touch an earlier one.  
- The checks are *constant* per iteration → O(n) total.

---

## ⚙️ 2. Python Implementation

```python
class Solution:
    def isSelfCrossing(self, distance: List[int]) -> bool:
        n = len(distance)
        if n < 4:
            return False

        for i in range(3, n):
            # Case 1
            if distance[i] >= distance[i - 2] and distance[i - 1] <= distance[i - 3]:
                return True

            # Case 2
            if i >= 4 and distance[i - 1] == distance[i - 3] \
               and distance[i] + distance[i - 4] >= distance[i - 2]:
                return True

            # Case 3
            if i >= 5 and distance[i - 2] == distance[i - 4] \
               and distance[i] + distance[i - 4] >= distance[i - 2] \
               and distance[i - 1] == distance[i - 3] \
               and distance[i - 1] + distance[i - 5] >= distance[i - 3]:
                return True

        return False
```

The logic mirrors the Java version, but Python’s concise syntax makes the conditions easier to read.

---

## ⚙️ 3. C++ Implementation

```cpp
class Solution {
public:
    bool isSelfCrossing(vector<int>& distance) {
        int n = distance.size();
        if (n < 4) return false;

        for (int i = 3; i < n; ++i) {
            // Case 1
            if (distance[i] >= distance[i-2] &&
                distance[i-1] <= distance[i-3])
                return true;

            // Case 2
            if (i >= 4 &&
                distance[i-1] == distance[i-3] &&
                distance[i] + distance[i-4] >= distance[i-2])
                return true;

            // Case 3
            if (i >= 5 &&
                distance[i-2] == distance[i-4] &&
                distance[i] + distance[i-4] >= distance[i-2] &&
                distance[i-1] == distance[i-3] &&
                distance[i-1] + distance[i-5] >= distance[i-3])
                return true;
        }
        return false;
    }
};
```

The C++ code follows the same logical pattern, using `vector<int>` and `int` indices.

---

## 📚 4. Blog Article – “Self‑Crossing on LeetCode: The Good, The Bad, and The Ugly”

### Title  
**“Mastering LeetCode 335 – Self‑Crossing: A Clean One‑Pass Solution (Java / Python / C++)”**

### Meta Description  
> Learn the efficient one‑pass solution for LeetCode 335 “Self‑Crossing” in Java, Python, and C++. Understand the three crossing cases, debug pitfalls, and how this problem boosts your interview résumé.

### Headings

| H1 | H2 | H3 | H4 |
|----|----|----|----|
| 🏆 335. Self‑Crossing – The Complete Guide | Why This Problem Rocks | The “Three Cases” Explained | Case 1: Straight‑on Intersection | Case 2: “Edge‑Touch” | Case 3: The Spiral Corner |
| | What Makes It Hard? | O(n) vs O(n²) – The Efficiency Gap | Avoiding Common Pitfalls |  |  |
| | The One‑Pass Implementation | Java | Python | C++ |
| | The Good | Constant Space | Predictable Runtime | Clean Code | 
| | The Bad | Misreading “Counter‑Clockwise” | Off‑by‑One Errors |  |
| | The Ugly | Naïve Brute Force | O(n²) Coordinate Tracking |  |
| | How It Helps Your Job Hunt | Interview Tactics | Showcase in Your Portfolio |  |
| | Final Thoughts | Practice Variants | Resources |  |

---

### 🏆 335. Self‑Crossing – The Complete Guide

**What is Self‑Crossing?**  
You walk on a 2‑D grid, moving north, west, south, east in that order. Given the distances, determine if you ever step on a previously visited point. This is a classic *geometric* interview problem that tests *analysis*, *pattern recognition*, and *O(n)* thinking.

---

#### Why This Problem Rocks

- **It’s a classic “LeetCode Hard”** that many candidates miss in interviews.  
- The solution showcases **constant‑space** thinking and **edge‑case awareness**.  
- It is an excellent talking point for *software engineering* interviews, especially for roles that handle **path planning**, **GPS routing**, or **robotics**.

---

#### The “Three Cases” Explained

1. **Case 1 – Straight‑on Intersection**  
   The current segment crosses the one that was drawn 3 steps earlier.  
   ```text
   0   1   2   3   4
   |   |   |   |   |
   ```

2. **Case 2 – “Edge‑Touch”**  
   The current segment touches the segment 4 steps back. It happens when the two segments are of equal length and the current one extends far enough.  

3. **Case 3 – The Spiral Corner**  
   All four surrounding segments pair up: the current one touches the one 5 steps back. This is the most “spiral‑like” crossing and is the hardest to spot mentally.

---

#### What Makes It Hard?

- The path can become a **self‑overlapping spiral**, and naïve thinking might lead to an O(n²) approach: storing all points and checking each new segment against all previous ones.  
- You must keep **in mind the direction changes** (north → west → south → east → …) and the *counter‑clockwise* pattern.  
- Off‑by‑one errors when indexing `i‑4`, `i‑5` are common.

---

#### The One‑Pass Implementation

The solution uses a simple `for` loop starting at `i = 3`. Each iteration checks the three cases above. Because each check is *O(1)*, the whole algorithm is O(n). No extra data structures are needed → O(1) space.

##### Java (Full Source)

```java
public class SelfCrossing {
    public boolean isSelfCrossing(int[] distance) {
        int n = distance.length;
        if (n < 4) return false;

        for (int i = 3; i < n; i++) {
            // Case 1
            if (distance[i] >= distance[i - 2] &&
                distance[i - 1] <= distance[i - 3]) return true;

            // Case 2
            if (i >= 4 &&
                distance[i - 1] == distance[i - 3] &&
                distance[i] + distance[i - 4] >= distance[i - 2]) return true;

            // Case 3
            if (i >= 5 &&
                distance[i - 2] == distance[i - 4] &&
                distance[i] + distance[i - 4] >= distance[i - 2] &&
                distance[i - 1] == distance[i - 3] &&
                distance[i - 1] + distance[i - 5] >= distance[i - 3]) return true;
        }
        return false;
    }
}
```

The Python and C++ snippets follow the same structure and logic, ensuring that your *interview portfolio* contains code in the language you most often use.

---

### 📈 The Good

| ✅ Feature | Why It Matters |
|------------|----------------|
| **O(n) Time** | Handles the maximum 10⁵ input size easily. |
| **O(1) Space** | No extra arrays, only a few integer variables. |
| **Readable Code** | Three clear `if` blocks map directly to the three crossing cases. |
| **Language‑agnostic** | Implementations available in Java, Python, and C++. |

---

### ⚠️ The Bad

| ❌ Common Pitfall | Fix |
|-------------------|-----|
| Misinterpreting “counter‑clockwise” direction | Visualize the path or draw a quick diagram for the first few steps. |
| Off‑by‑one errors on `i‑4`, `i‑5` | Use `if (i >= 4)` / `if (i >= 5)` guards before accessing. |
| Assuming crossing can only happen after step 3 | The algorithm starts at 3, but you must still check all indices ≥ 3. |

---

### 👹 The Ugly

The most dreaded approach is the **naïve O(n²) sweep**:

```text
for each new segment
    for each previous segment
        check if they intersect
```

- **Why it’s ugly**:  
  - Time complexity explodes for `n = 10⁵`.  
  - Memory waste: you’d store all coordinates or segments.  
  - Hard to debug: intersect checks in 2‑D geometry can be subtle.

> *Lesson:* A clear mathematical insight (the three cases) turns a hopeless O(n²) problem into a clean O(n) solution.

---

### 🚀 How This Helps Your Job Hunt

1. **Interview Gold** – Recruiters love candidates who solve LeetCode Hard problems efficiently.  
2. **Portfolio Showcase** – Put the three implementations in a public GitHub repo; add unit tests.  
3. **Resume Bullet** – “Optimized Self‑Crossing algorithm from O(n²) to O(n) with O(1) space”.  
4. **Discussion Topic** – During behavioral interviews, talk about *algorithmic thinking*, *edge‑case handling*, and *performance trade‑offs*.

---

### 🎯 Final Thoughts

- The **Self‑Crossing** problem is a perfect blend of geometry and algorithmic reasoning.  
- Master the three crossing cases, implement the one‑pass solution in your preferred language, and test thoroughly.  
- Show the code in your interview or portfolio and explain the **why** behind each condition.  
- Keep practicing variations: “self‑overlap”, “self‑touch”, or even “self‑crossing on a circle” to sharpen your pattern recognition.

> **Pro Tip:** Write the solution on a whiteboard first. Draw the path for a few distances (e.g., `[1, 2, 3, 4]`) and annotate where each case could occur. This visual aid impresses interviewers and clarifies your own understanding.

---

### 🔗 Resources

| Resource | Link |
|----------|------|
| LeetCode 335 – Self‑Crossing | https://leetcode.com/problems/self-crossing |
| GeeksforGeeks Discussion | https://www.geeksforgeeks.org/leetcode-335-self-crossing/ |
| “Geometric Path Crossing” blog series | https://towardsdatascience.com/ | 

---

### 📢 Share Your Experience

Did you crack LeetCode 335? Drop a comment or tweet us with your own implementation and test cases! Let’s keep the discussion flowing.  

> *Follow us on Twitter @CodingChampion* for weekly coding challenges, interview tips, and more LeetCode Deep‑Dive guides.

---

## Closing

You now have:

- **Three crystal‑clear solutions** (Java, Python, C++).  
- **A complete blog article** optimized for SEO with the right keywords: *LeetCode 335*, *Self‑Crossing*, *O(n) solution*, *Interview tips*.  
- **Insights** into the problem’s difficulty levels, debugging pitfalls, and interview relevance.

Happy coding—and good luck with those interviews! 🎯

--- 

*End of Blog Post* 

--- 

**SEO Checklist**  
- Primary keyword: “LeetCode 335 Self‑Crossing”  
- Secondary keywords: “one‑pass solution”, “Java implementation”, “Python solution”, “C++ code”.  
- Title, meta description, headings, and keyword density comply with SEO best practices.  

--- 

That completes the full, interview‑ready solution set and a comprehensive blog article to help you ace LeetCode 335 and boost your career prospects. Happy interviewing! 🚀