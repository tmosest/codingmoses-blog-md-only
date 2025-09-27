---
title: LeetCode 1344. Angle Between Hands of a Clock - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – 3 Languages

Below are clean, ready‑to‑paste solutions for LeetCode **1344 – Angle Between Hands of a Clock**.  
Each implementation follows the same **unitary‑method** math and is written in a style that’s easy to read, test, and discuss in an interview.

---

### 1.1  Java

```java
/**
 * 1344. Angle Between Hands of a Clock
 *
 * The problem can be solved with a single line of math:
 *   hour angle = (hour % 12) * 30 + minutes * 0.5
 *   minute angle = minutes * 6
 *   difference = abs(hour angle - minute angle)
 *   answer = min(difference, 360 - difference)
 *
 * Time Complexity : O(1)
 * Space Complexity: O(1)
 */
class Solution {
    public double angleClock(int hour, int minutes) {
        // 1 hour == 30 degrees, 1 minute == 0.5 degrees for the hour hand
        double hourAngle = (hour % 12) * 30.0 + minutes * 0.5;

        // 1 minute == 6 degrees for the minute hand
        double minuteAngle = minutes * 6.0;

        // Take the smaller angle
        double diff = Math.abs(hourAngle - minuteAngle);
        return Math.min(diff, 360.0 - diff);
    }
}
```

---

### 1.2  Python

```python
# 1344. Angle Between Hands of a Clock
# Time: O(1), Space: O(1)

class Solution:
    def angleClock(self, hour: int, minutes: int) -> float:
        hour_angle = (hour % 12) * 30 + minutes * 0.5
        minute_angle = minutes * 6
        diff = abs(hour_angle - minute_angle)
        return min(diff, 360 - diff)
```

---

### 1.3  C++

```cpp
/* 1344. Angle Between Hands of a Clock
 * O(1) time, O(1) space
 */
class Solution {
public:
    double angleClock(int hour, int minutes) {
        double hourAngle = (hour % 12) * 30.0 + minutes * 0.5;
        double minuteAngle = minutes * 6.0;
        double diff = std::abs(hourAngle - minuteAngle);
        return std::min(diff, 360.0 - diff);
    }
};
```

---

## 2.  Blog Article – “The Good, The Bad & The Ugly of LeetCode 1344”

> *“Mastering Clock Angles: A Complete Guide to LeetCode 1344 – From Basics to Interview‑Ready Code”*

### 2.1  Why this Problem Matters

- **Classic Interview Question** – it shows you can blend geometry with arithmetic.
- **Lightweight but Insightful** – only 1‑line of math; a perfect showcase of clean coding.
- **Testing Edge Cases** – forces you to handle the “12‑hour wrap‑around” and “obvious 360‑degree flip”.

### 2.2  Problem Recap

> **Input**: `hour (1…12)`, `minutes (0…59)`  
> **Output**: The smaller angle (in degrees) between the hour and minute hands, accurate to `10⁻⁵`.

### 2.3  The Good – Simple Math

| Concept | Value |
|---------|-------|
| Hour hand moves 30° per hour | `30° = 360° / 12` |
| Hour hand moves 0.5° per minute | `0.5° = 30° / 60` |
| Minute hand moves 6° per minute | `6° = 360° / 60` |

**Formula**  

```
hourAngle   = (hour % 12) * 30 + minutes * 0.5
minuteAngle = minutes * 6
difference  = |hourAngle - minuteAngle|
answer      = min(difference, 360 - difference)
```

- `hour % 12` handles the “12” edge case (12 becomes 0 degrees).
- `min(difference, 360 - difference)` automatically chooses the smaller angle.

### 2.4  The Bad – Common Pitfalls

| Mistake | Why it breaks | Fix |
|---------|---------------|-----|
| Forgetting `% 12` | 12:00 → 360° instead of 0° | `hour % 12` |
| Integer division | `minutes / 60` becomes 0 in Java/C++ | Use double or `minutes * 0.5` |
| Not taking the absolute difference | Negative angles | `abs()` |
| Returning raw difference > 180 | Wrong “smaller angle” | `min(diff, 360 - diff)` |
| Floating‑point rounding errors | Wrong precision | Return `double` and rely on language’s default rounding |

### 2.5  The Ugly – Over‑engineering

It’s tempting to write a “step‑by‑step” simulation:

```text
for each minute:
    move hour hand by 0.5°
    move minute hand by 6°
```

But that adds loops, state, and unnecessary complexity. The “ugly” solution often looks like:

```java
// Too many variables, magic numbers, and redundant checks
```

**Rule of thumb** – keep the solution **stateless** and **formula‑based**. Simplicity is a signal of good problem‑solving.

### 2.6  Optimizing for Interviews

1. **Explain the math upfront** – show you understand the underlying geometry.
2. **Show edge‑case handling** – talk about the `12%12` trick.
3. **Mention complexity** – “O(1) time, O(1) space”.
4. **Write clean code** – no unused variables, consistent naming.
5. **Add a test case** – e.g. `angleClock(3, 30) → 75`.

### 2.7  SEO & Job‑Search Strategy

- **Keywords**: “LeetCode 1344 solution”, “clock angle problem”, “Java Python C++ interview”, “O(1) geometry problem”, “best clock angle code”.
- **Meta‑description**: “Learn the optimal O(1) solution for LeetCode 1344 – Angle Between Hands of a Clock. Full Java, Python, C++ code + interview tips.”
- **Header tags**: H1 – problem title; H2 – sections (Good, Bad, Ugly, Code).
- **Internal links**: link to related problems (e.g., “Clock Angle variations”, “Math problems in LeetCode”).
- **Rich snippets**: provide a “code snippet” block for each language.
- **Engagement**: ask readers to comment with their own tricks or share the article.

### 2.8  Final Thoughts

- The *good* is a single, elegant formula that solves a geometry problem in O(1).
- The *bad* is the many pitfalls that derail even experienced coders; watch out for modulo, division, and sign errors.
- The *ugly* is over‑engineering – loops, state, or extra variables. Keep it lean.

Armed with this solution and a polished explanation, you’re ready to ace the “clock angle” question on LeetCode and in your next coding interview. Good luck, and happy coding!