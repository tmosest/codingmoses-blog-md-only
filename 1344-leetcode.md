---
title: LeetCode 1344. Angle Between Hands of a Clock - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Angle Between Hands of a Clock  
**The Good, the Bad, and the Ugly – A Deep Dive into LeetCode 1344**

> **Keywords:** LeetCode, 1344, Angle Between Hands, Clock Angle, Interview Question, O(1) solution, Java, Python, C++, Coding Interview, Algorithm, Math, Job Interview  

---

## 1. Problem Recap

> **LeetCode 1344 – Angle Between Hands of a Clock**  
> **Difficulty:** Medium  

> **Signature**  
> ```java
> public double angleClock(int hour, int minutes)
> ```
> or the equivalent in Python/C++.

**Goal:**  
Given an integer `hour` (1 – 12) and `minutes` (0 – 59), return the **smaller angle** (in degrees) between the hour and minute hand of an analog clock.  
Answers within **1 × 10⁻⁵** of the true value are accepted.

**Examples**

| Input | Output |
|-------|--------|
| 12, 30 | 165 |
| 3, 30 | 75 |
| 3, 15 | 7.5 |

---

## 2. Why This Problem Matters in Interviews

| Feature | Why It’s Valuable |
|---------|-------------------|
| **Pure math + O(1)** | Shows you can think geometrically and produce a constant‑time solution. |
| **Edge‑case awareness** | Handling `12` vs `0`, half‑hour offsets, obtuse vs acute angles. |
| **Language agnostic** | Solvable in any language – demonstrates adaptability. |
| **Interview “quick‑win”** | Usually takes < 5 min for a senior developer. |

> *Pro tip:* When answering, explain the formula first, then code. Interviewers love the “why” before the “how”.

---

## 3. The Good – The Clean Math

1. **Hour hand movement**  
   * One full rotation (360°) in 12 h → 30° per hour.  
   * In one minute, the hour hand moves **0.5°** (30° / 60).  

   \[
   \theta_h = (hour \bmod 12) \times 30^\circ + minutes \times 0.5^\circ
   \]

2. **Minute hand movement**  
   * 360° in 60 min → 6° per minute.  

   \[
   \theta_m = minutes \times 6^\circ
   \]

3. **Angle difference**  

   \[
   \Delta = |\theta_m - \theta_h|
   \]

4. **Take the smaller angle**  

   If `Δ > 180°`, then the acute angle is `360° - Δ`.

This reasoning gives a one‑liner O(1) solution in any language.

---

## 4. The Bad – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Using `hour` instead of `hour % 12` | `12` should map to `0` on the clock face. |
| Forgetting the minute contribution to the hour hand | `minutes * 0.5` is essential. |
| Returning the obtuse angle | Take `min(Δ, 360-Δ)`. |
| Using integer division in Python 2 (if you still use it) | Use `minutes / 60.0` or `float(minutes) / 60`. |
| Returning a `float` in Java instead of `double` | Use `double` to meet precision requirement. |

---

## 5. The Ugly – Over‑engineering

Some solutions add unnecessary helper functions, switch statements, or even build a `Clock` class.  
For interview speed, stick to the concise mathematical formula.  
If you need to show object‑oriented design, a simple `Clock` struct is fine, but it adds noise.

---

## 6. Implementation (O(1))

Below are clean, production‑ready solutions in **Java, Python, and C++**.

### 6.1 Java

```java
class Solution {
    public double angleClock(int hour, int minutes) {
        // Hour hand angle: 30° per hour + 0.5° per minute
        double hourAngle = (hour % 12) * 30.0 + minutes * 0.5;
        // Minute hand angle: 6° per minute
        double minuteAngle = minutes * 6.0;
        // Absolute difference
        double diff = Math.abs(minuteAngle - hourAngle);
        // Return the smaller angle
        return diff > 180.0 ? 360.0 - diff : diff;
    }
}
```

### 6.2 Python

```python
class Solution:
    def angleClock(self, hour: int, minutes: int) -> float:
        hour_angle = (hour % 12) * 30.0 + minutes * 0.5
        minute_angle = minutes * 6.0
        diff = abs(minute_angle - hour_angle)
        return min(diff, 360.0 - diff)
```

### 6.3 C++

```cpp
class Solution {
public:
    double angleClock(int hour, int minutes) {
        double hour_angle = (hour % 12) * 30.0 + minutes * 0.5;
        double minute_angle = minutes * 6.0;
        double diff = std::abs(minute_angle - hour_angle);
        return std::min(diff, 360.0 - diff);
    }
};
```

---

## 7. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Angle calculations | **O(1)** | **O(1)** |

Only constant time and memory are used – perfect for interview constraints.

---

## 8. Test Harness (Optional)

```python
if __name__ == "__main__":
    sol = Solution()
    tests = [(12, 30, 165), (3, 30, 75), (3, 15, 7.5), (12, 0, 0)]
    for h, m, expected in tests:
        res = sol.angleClock(h, m)
        assert abs(res - expected) < 1e-5, f"Failed for {h}:{m}"
    print("All tests passed!")
```

Run the same idea in Java or C++ by printing the results and asserting manually.

---

## 9. Alternative Approaches (Why Not Needed)

* **Brute‑force simulation** (increment minute by minute) – O(60) time, unnecessary.  
* **Using complex numbers** – overkill for a simple geometry problem.  
* **Recursion / memoization** – not required.

Stick to the direct formula for clarity and speed.

---

## 10. How This Boosts Your Interview Game

1. **Showcases mathematical intuition** – essential for system‑design & algorithm questions.  
2. **Demonstrates O(1) optimization** – interviewers love constant‑time solutions.  
3. **Highlights attention to edge cases** – handling `12:00` or `0:00`.  
4. **Flexibility across languages** – you can switch between Java, Python, C++ with minimal changes.  

When you answer this problem in an interview, explain the reasoning *before* you write code. Mention the `hour % 12`, the `0.5°` per minute, and why you use `min(diff, 360 - diff)`. This structure shows you’re not just coding but also thinking critically.

---

## 11. Final Take‑away

- **Key formula**:  
  \[
  \text{angle} = \min\!\Bigl(\bigl|6m - (30h + 0.5m)\bigr|,\; 360 - \bigl|6m - (30h + 0.5m)\bigr|\Bigr)
  \]
- **Implement in 1‑3 lines**.  
- **Run in O(1)** time, O(1) space.  
- **Avoid pitfalls**: modulus, minute contribution, obtuse vs acute.

Mastering this problem gives you a solid “math‑plus‑code” trick to impress any hiring manager. Good luck on your next interview! 🚀

--- 

**Want more interview prep?**  
Subscribe for weekly LeetCode walkthroughs, algorithm articles, and career‑boosting posts. Happy coding!