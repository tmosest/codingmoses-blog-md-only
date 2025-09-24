---
title: LeetCode 2259. Remove Digit From Number to Maximize Result - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Mastering LeetCode 2259 – “Remove Digit From Number to Maximize Result”

> **Why this problem matters**  
>  LeetCode 2259 is a classic interview‑style question that tests your **string manipulation**, **greedy thinking**, and **time‑complexity awareness**. A solid grasp of this problem showcases your ability to solve real‑world coding challenges and makes you a stronger candidate for software engineering roles.

---

### 📌 Problem Statement (LeetCode 2259)

> **Input**  
>  * `number` – a string representing a positive integer (2 ≤ length ≤ 100).  
>  * `digit` – a single character digit that is guaranteed to appear at least once in `number`.  
> 
> **Output**  
>  Return the *largest* possible string after removing **exactly one** occurrence of `digit`.

**Example**

| `number` | `digit` | Result |
|----------|---------|--------|
| `"123"`  | `'3'`   | `"12"` |
| `"1231"` | `'1'`   | `"231"` |
| `"551"`  | `'5'`   | `"51"` |

---

## 📐 How to Solve It – Greedy + Edge‑Case Strategy

The optimal solution is **O(n)**, where *n* is the length of `number`.  
The key observation:

> *Remove the first `digit` that is followed by a larger digit.  
> If no such occurrence exists, remove the **last** occurrence of `digit`.*

### Why It Works

* When you delete a digit that is **preceded** by a smaller digit, the left‑hand side remains unchanged, and the right‑hand side is maximized.
* Deleting a digit that has a **larger neighbor to the right** makes the resulting string larger (because you lose a smaller digit while keeping a larger one).
* If every occurrence of `digit` is followed only by **smaller or equal** digits, then removing the **last** occurrence is best: you keep the earliest possible digits and only drop the rightmost one.

### Step‑by‑Step

1. Iterate over the string once.
2. When `number[i] == digit` **and** `i < n-1` **and** `number[i+1] > digit`, delete at `i` and break.
3. If the loop finishes without a break, delete the **last** occurrence of `digit`.

---

## 🛠️ Code Implementations

### Java – Greedy (O(n))

```java
class Solution {
    public String removeDigit(String number, char digit) {
        int n = number.length();
        int deleteIndex = -1;

        for (int i = 0; i < n; i++) {
            if (number.charAt(i) == digit) {
                if (i < n - 1 && number.charAt(i + 1) > digit) {
                    deleteIndex = i;
                    break;               // optimal spot found
                }
                deleteIndex = i;          // fallback: last occurrence
            }
        }
        return number.substring(0, deleteIndex) + number.substring(deleteIndex + 1);
    }
}
```

### Python – Greedy (O(n))

```python
class Solution:
    def removeDigit(self, number: str, digit: str) -> str:
        n = len(number)
        delete_index = -1

        for i, ch in enumerate(number):
            if ch == digit:
                if i < n - 1 and number[i + 1] > digit:
                    delete_index = i
                    break
                delete_index = i

        return number[:delete_index] + number[delete_index + 1:]
```

### C++ – Greedy (O(n))

```cpp
class Solution {
public:
    string removeDigit(string number, char digit) {
        int n = number.size();
        int del = -1;

        for (int i = 0; i < n; ++i) {
            if (number[i] == digit) {
                if (i < n - 1 && number[i + 1] > digit) {
                    del = i;
                    break;
                }
                del = i;   // last occurrence fallback
            }
        }
        number.erase(number.begin() + del);
        return number;
    }
};
```

---

## 🔍 Alternative Brute‑Force (O(n²))

If you’re new to the greedy idea, you can enumerate every possible removal and keep the maximum string.  
Works fine for the constraints (length ≤ 100), but is **not** the optimal interview answer.

```python
class Solution:
    def removeDigit(self, number: str, digit: str) -> str:
        best = ""
        for i, ch in enumerate(number):
            if ch == digit:
                cand = number[:i] + number[i+1:]
                if cand > best:
                    best = cand
        return best
```

---

## ⚖️ The Good, The Bad, The Ugly

| Aspect | What to Highlight | What to Avoid |
|--------|-------------------|---------------|
| **Good** | • O(n) greedy algorithm – fast & scalable. <br>• Clear, single‑pass logic – easy to explain in interviews. <br>• Handles edge cases (no larger right neighbor). | • |
| **Bad** | • Brute‑force is simple but wasteful – may scare interviewers. | • Over‑optimizing for micro‑performance on small inputs; the interviewer cares about logic. |
| **Ugly** | • Subtle off‑by‑one errors when deleting the last digit. <br>• Forgetting to handle the case where all digits are equal. | • Misinterpreting “maximizing” as numeric comparison; keep it lexicographic for strings. |

---

## 🎯 Interview Tips

1. **Clarify**: Ask if the result should be treated as a number or a string (both work because of lexicographic order).  
2. **Walk through**: Pick an example and show how the algorithm decides which index to delete.  
3. **Complexity**: Emphasize O(n) time and O(1) extra space.  
4. **Edge Cases**: Mention when all digits are the same, or when the last digit is the only occurrence.  
5. **Code Clean‑up**: Use meaningful variable names (`deleteIndex`, `del`, `idx`).  
6. **Testing**: Write a few unit tests (e.g., `"111"`, `"987654321"`, `"121212"`).

---

## 🎓 Takeaway

LeetCode 2259 is a perfect showcase of:

* **String manipulation** – removing characters efficiently.  
* **Greedy reasoning** – picking the local optimum that leads to a global optimum.  
* **Time‑space trade‑off** – solving in a single pass with constant extra memory.

A solid implementation in Java, Python, or C++ demonstrates not only language proficiency but also algorithmic insight – exactly what recruiters look for.

---

## 📢 Final SEO Boost

- **Keywords**: LeetCode 2259, Remove Digit, maximize result, greedy algorithm, interview question, coding interview, Java solution, Python solution, C++ solution, job interview tips, software engineering interview, algorithm practice.  
- **Meta Description**: “Learn how to solve LeetCode 2259 – Remove Digit From Number to Maximize Result. See Java, Python, and C++ solutions, a greedy algorithm, and interview‑ready tips. Get hired faster!”  

---

Happy coding! 🚀