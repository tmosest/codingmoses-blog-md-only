---
title: LeetCode 2651. Calculate Delayed Arrival Time - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2651 – Calculate Delayed Arrival Time  
**LeetCode ID:** 2651 | **Difficulty:** Easy | **Category:** Math

> **Problem** – Given an `arrivalTime` (0 ≤ arrivalTime < 24) and a `delayedTime` (1 ≤ delayedTime ≤ 24) in hours, return the hour (0 – 23) at which the train will finally arrive.  
> **Time is expressed in 24‑hour format**.

> **Example**  
> ```
> arrivalTime = 13, delayedTime = 11  →  (13 + 11) % 24 = 0
> ```

The solution is a single arithmetic operation and is therefore O(1) in time and space.

---

## 1️⃣  Code – Three Languages

| Language | Code | Complexity | Notes |
|----------|------|------------|-------|
| **Java** | [See below](#java) | O(1) time, O(1) space | Uses `% 24` for wrap‑around. |
| **Python** | [See below](#python) | O(1) time, O(1) space | Modulo works on integers. |
| **C++** | [See below](#cpp) | O(1) time, O(1) space | `int` arithmetic; no overflow risk. |

---

### <a name="java"></a>Java

```java
// 2651. Calculate Delayed Arrival Time – Java
// O(1) time, O(1) space

public class Solution {
    public int findDelayedArrivalTime(int arrivalTime, int delayedTime) {
        // Sum the times and wrap around after 24 hours
        return (arrivalTime + delayedTime) % 24;
    }

    // Optional main method for quick manual testing
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.findDelayedArrivalTime(15, 5));  // 20
        System.out.println(s.findDelayedArrivalTime(13, 11)); // 0
    }
}
```

---

### <a name="python"></a>Python

```python
# 2651. Calculate Delayed Arrival Time – Python
# O(1) time, O(1) space

class Solution:
    def findDelayedArrivalTime(self, arrivalTime: int, delayedTime: int) -> int:
        return (arrivalTime + delayedTime) % 24


# Quick tests
if __name__ == "__main__":
    s = Solution()
    print(s.findDelayedArrivalTime(15, 5))   # 20
    print(s.findDelayedArrivalTime(13, 11))  # 0
```

---

### <a name="cpp"></a>C++

```cpp
// 2651. Calculate Delayed Arrival Time – C++
// O(1) time, O(1) space

class Solution {
public:
    int findDelayedArrivalTime(int arrivalTime, int delayedTime) {
        return (arrivalTime + delayedTime) % 24;
    }
};

int main() {
    Solution s;
    std::cout << s.findDelayedArrivalTime(15, 5) << '\n';   // 20
    std::cout << s.findDelayedArrivalTime(13, 11) << '\n';  // 0
}
```

---

## 2️⃣  The Good, The Bad, and The Ugly

| Aspect | What to Highlight | Common Pitfalls | How to Fix It |
|--------|-------------------|-----------------|---------------|
| **Good** | • O(1) arithmetic → fast in interview<br>• No loops or data structures → minimal code<br>• Edge‑case handling (`24 → 0`) shows understanding of modulo | | |
| **Bad** | • Problem may look trivial but can trip people who forget the wrap‑around<br>• Over‑engineering with loops or string formatting can lead to unnecessary bugs | • Writing `arrivalTime + delayedTime` and forgetting `% 24` | • Always use modulo or check if sum ≥ 24. |
| **Ugly** | • Using `if` statements for every hour (0‑23) is a code smell<br>• Ignoring the constraint `delayedTime <= 24` leads to potential overflow in other languages (not a problem here) | • Code that prints the full time string (e.g., `"20:00"`) when only the hour is needed | • Stick to the problem spec: return an integer. |

---

## 3️⃣  Why This Problem Rocks for Interviews

1. **Mathematical Thinking** – The core idea is to recognize that time “wraps” after 24 hours. This tests a candidate’s ability to map a real‑world scenario to a simple arithmetic formula.

2. **Constant‑Time Brilliance** – Interviewers love O(1) solutions because they demonstrate a clean, optimal thought process.

3. **Edge‑Case Awareness** – The hidden trick is the wrap‑around; many candidates silently assume that 24‑hour addition always stays < 24. A robust answer must explicitly handle the boundary.

4. **Cross‑Language Mastery** – The same logic translates to Java, Python, and C++. Showing the same concise solution in multiple languages is a great resume booster.

---

## 4️⃣  SEO‑Optimized Blog Post

> **Title**: *LeetCode 2651 – Calculate Delayed Arrival Time: Java, Python & C++ Solutions + Interview Insight*  
> **Meta Description**: Master LeetCode 2651 with clear Java, Python, and C++ code, learn the time complexity, and get interview tips to impress recruiters.

---

### 📄 Introduction

If you’re prepping for a software engineering interview, you’ll soon encounter *LeetCode 2651 – Calculate Delayed Arrival Time*. It’s a deceptively simple problem that tests your ability to reason about 24‑hour time wrap‑around and your command of basic arithmetic in code. Below you’ll find **clean, production‑ready solutions** in **Java, Python, and C++**, along with an interview‑style analysis of the *good, the bad, and the ugly*.

---

### 🔍 Problem Recap

You’re given two integers:

| Variable | Range | Meaning |
|----------|-------|---------|
| `arrivalTime` | 0 – 23 | Hour a train arrives |
| `delayedTime` | 1 – 24 | Extra hours of delay |

Return the hour (0 – 23) when the train finally arrives. In 24‑hour format, 24 is represented as `0`.

**Example**

```text
arrivalTime = 13, delayedTime = 11
Result = (13 + 11) % 24 = 0  // 00:00
```

---

### 📌 Core Insight

The **only** operation needed is **addition followed by a modulo 24**. This ensures that any overflow beyond 23 wraps around to the start of the next day.

---

### 🧩 Solutions

#### Java

```java
public class Solution {
    public int findDelayedArrivalTime(int arrivalTime, int delayedTime) {
        return (arrivalTime + delayedTime) % 24;
    }
}
```

#### Python

```python
class Solution:
    def findDelayedArrivalTime(self, arrivalTime: int, delayedTime: int) -> int:
        return (arrivalTime + delayedTime) % 24
```

#### C++

```cpp
class Solution {
public:
    int findDelayedArrivalTime(int arrivalTime, int delayedTime) {
        return (arrivalTime + delayedTime) % 24;
    }
};
```

All three solutions run in **O(1) time** and **O(1) space**.

---

### 🏆 The Good

- **Simplicity** – One line of code plus a method wrapper.
- **Optimality** – Constant time & space.
- **Clear reasoning** – Easy to explain in an interview.

### ⚠️ The Bad

- **Common forget** – Omitting the modulo leads to wrong answers for inputs like `arrivalTime = 20, delayedTime = 5`.
- **Over‑engineering** – Adding loops or string formatting bloats the solution.

### 🔥 The Ugly

- **Hard‑coded checks** – Writing an `if` block for each hour is a maintenance nightmare.
- **Wrong return type** – Returning `"20:00"` as a string when the spec requires an integer.

---

### 🎯 Interview Tips

1. **State the edge case** – “We need to wrap around after 23, so we’ll take the sum modulo 24.”
2. **Mention constraints** – “Because `delayedTime` ≤ 24, integer overflow isn’t an issue in these languages.”
3. **Show the solution** – Write the single line and highlight its elegance.
4. **Ask clarifying questions** – “Do we need to handle minutes? Should 24 be returned as 0?”

---

### 📚 Takeaway

LeetCode 2651 is a classic “math‑in‑code” problem that rewards clean, constant‑time solutions. By mastering this problem in **Java, Python, and C++**, you demonstrate:

- Cross‑language proficiency
- Attention to edge cases
- Ability to produce production‑grade, minimal‑code solutions

Include it on your resume with a brief bullet:  
> *Solved LeetCode 2651 (“Calculate Delayed Arrival Time”) in Java, Python, and C++ – O(1) time and space solution using modulo arithmetic.*

Good luck in your interviews—now you’re ready to talk about time, modulo, and the art of writing elegant code!