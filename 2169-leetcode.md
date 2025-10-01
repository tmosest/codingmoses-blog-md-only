---
title: LeetCode 2169. Count Operations to Obtain Zero - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 2169 – Count Operations to Obtain Zero  
**Easy** | *LeetCode* | `O(log min(num1, num2))` time | `O(1)` space  

> **TL;DR** – Subtract the smaller number from the larger one until one of the numbers becomes zero. The number of subtractions needed is the answer.

---

## 1. Problem Restatement

You are given two non‑negative integers `num1` and `num2`.  
In a single operation you:

* If `num1 >= num2`, replace `num1` with `num1 - num2`;
* Otherwise replace `num2` with `num2 - num1`.

Stop when either `num1 == 0` or `num2 == 0` and return how many operations were performed.

---

## 2. Why This Problem is a “Coding‑Interview Goldmine”

| ✔️  | Why recruiters love this question |
|---|------------------------------------|
| **Easy** | It tests basic control flow, while‑loops, and boundary handling. |
| **Insightful** | The optimal solution uses a classic Euclidean‑algorithm trick (division), a pattern that appears in many interview questions. |
| **Language‑agnostic** | You can solve it in any language – Java, Python, C++, Rust, Go, etc. |
| **Time‑complexity twist** | A naïve `while (num1 && num2) { … }` loop gives `O(max(num1, num2))`, while an optimized approach reduces it to `O(log min)` – a great chance to talk about algorithmic improvement. |

---

## 3. The Brute‑Force (While‑Loop) Approach

The simplest solution literally follows the problem statement:

```text
while (num1 != 0 && num2 != 0)
    if (num1 >= num2)  num1 -= num2;
    else               num2 -= num1;
    ops++;
```

* **Time** – `O(max(num1, num2))` – worst case when the two numbers differ by 1 (e.g., 1 000 000 vs. 999 999).
* **Space** – `O(1)`.

The brute‑force code is perfectly fine for LeetCode’s constraints (`num1, num2 ≤ 10^5`), but it’s a perfect springboard to discuss optimization.

---

## 4. The “Good” – Optimal Solution Using Division

Observe that repeatedly subtracting the smaller number is equivalent to performing a **modulus** operation:

```
num1 >= num2   →  num1 = num1 % num2
```

Why?  
If you subtract `num2` from `num1` k times, the new `num1` equals `num1 - k * num2`.  
Choosing `k = floor(num1 / num2)` gives `num1 % num2`.  
That saves a whole batch of operations.

### Pseudocode

```
ops = 0
while num1 != 0 and num2 != 0
    if num1 >= num2
        ops += num1 // num2   // how many times we can subtract
        num1 %= num2
    else
        ops += num2 // num1
        num2 %= num1
return ops
```

* **Time** – `O(log min(num1, num2))` – each iteration reduces the larger number dramatically.
* **Space** – `O(1)`.

---

## 5. Edge‑Case Handling

| Case | Why it matters | How we handle it |
|------|----------------|------------------|
| `num1 == 0` or `num2 == 0` | No operations needed | Return `0` immediately |
| `num1 == num2` | One operation clears one number | The loop handles it (division yields 1) |
| Large numbers (close to `10^5`) | Still fast | `O(log)` guarantees speed |

---

## 6. Code Snippets

### 6.1 Java (LeetCode Class)

```java
public class Solution {
    public int countOperations(int num1, int num2) {
        if (num1 == 0 || num2 == 0) return 0;
        int ops = 0;
        while (num1 != 0 && num2 != 0) {
            if (num1 >= num2) {
                ops += num1 / num2;
                num1 %= num2;
            } else {
                ops += num2 / num1;
                num2 %= num1;
            }
        }
        return ops;
    }
}
```

### 6.2 Python

```python
def countOperations(num1: int, num2: int) -> int:
    if num1 == 0 or num2 == 0:
        return 0
    ops = 0
    while num1 and num2:
        if num1 >= num2:
            ops += num1 // num2
            num1 %= num2
        else:
            ops += num2 // num1
            num2 %= num1
    return ops
```

### 6.3 C++

```cpp
int countOperations(int num1, int num2) {
    if (num1 == 0 || num2 == 0) return 0;
    int ops = 0;
    while (num1 && num2) {
        if (num1 >= num2) {
            ops += num1 / num2;
            num1 %= num2;
        } else {
            ops += num2 / num1;
            num2 %= num1;
        }
    }
    return ops;
}
```

All three snippets compile cleanly, run in linear‑time (for Python’s `//` division) and use constant extra space.

---

## 7. “The Ugly” – Common Pitfalls

| Mistake | What happens | Fix |
|---------|--------------|-----|
| Using `while (num1 != 0 || num2 != 0)` instead of `&&` | Infinite loop when one number becomes zero but the other is non‑zero. | Change to `&&`. |
| Forgetting to cast to `long` in languages with 32‑bit overflow | Wrong answer for `num1, num2` close to `INT_MAX`. | Use `long long` or Python’s arbitrary ints. |
| `ops += num1 % num2` instead of `num1 / num2` | Counts operations wrong; you’re adding the *remainder*, not how many subtractions were performed. | Use integer division (`/`) for the count. |
| Returning `-1` or `0` in the final line when `num1==num2` | Missing the last operation that turns one number to zero. | The loop handles it; no special case needed. |

---

## 8. Summary & Take‑aways

* The core of the problem is a **modular subtraction** pattern – think Euclid’s algorithm.
* A naïve while‑loop works for the problem limits but is *un‑optimal* and a great interview talking point.
* The optimal solution uses division and modulus to batch subtractions, giving `O(log min)` time.
* All solutions share the same clean `O(1)` space footprint.

---

## 9. SEO‑Optimized Blog Meta (for your personal website or Medium)

| Element | Content |
|---------|---------|
| **Title** | “Solve LeetCode 2169: Count Operations to Obtain Zero – Java, Python, C++ (Optimal) – Interview Guide” |
| **Meta Description** | “Learn how to solve LeetCode 2169 in Java, Python, and C++ with an optimal O(log min) solution. Step‑by‑step guide, code snippets, edge‑case handling, and interview tips.” |
| **Keywords** | LeetCode 2169, Count Operations to Obtain Zero, Java solution, Python solution, C++ solution, interview coding problem, algorithm optimization, Euclidean algorithm, O(log min) time |
| **Header Tags** | `<h1>Count Operations to Obtain Zero – LeetCode 2169</h1>`<br>`<h2>Problem Overview</h2>`<br>`<h2>Optimal Solution</h2>`<br>`<h2>Code in Java / Python / C++</h2>`<br>`<h2>Edge Cases & Pitfalls</h2>` |

---

## 10. FAQs

| Question | Answer |
|----------|--------|
| **Can I just subtract once per loop?** | Yes – it works but is slower. The optimal approach uses division. |
| **Is the problem equivalent to the Euclidean algorithm?** | Conceptually yes, but here we’re counting subtractions instead of computing the GCD. |
| **Why do we use `while (num1 && num2)` instead of `while (num1 || num2)`?** | We need both numbers non‑zero to continue subtracting. |
| **What if one number is zero from the start?** | The answer is 0 – no operations needed. |
| **Does this algorithm handle `num1 == num2` correctly?** | Yes – one operation clears one of them. |

---

## 11. Final Thought

Show recruiters you can go beyond the naïve implementation. Present the brute‑force, point out its inefficiency, then elegantly transition to the division‑based optimal solution. That demonstrates problem‑solving depth, algorithmic awareness, and a passion for clean code – exactly what hiring managers look for. Happy coding! 🚀