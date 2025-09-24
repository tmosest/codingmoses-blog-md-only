---
title: LeetCode 1881. Maximum Value after Insertion - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1881 – Maximum Value after Insertion  
**Java | Python | C++** – One clean solution for every language  

> *“Want to land that software‑engineering role? Master this LeetCode problem, show a polished implementation, and you’ll stand out to interviewers.”*  

Below you’ll find:

* A **step‑by‑step walk‑through** of the optimal algorithm.  
* **Java, Python and C++ code** – all O(n) time, O(1) extra space.  
* A **blog‑style article** that explains the problem, the good, the bad, the ugly, and how to use it in your job hunt.  

---

## 1. Problem Recap  

> *You are given a very large integer `n`, represented as a string, and an integer digit `x` (1‑9).  
>  `n` may be negative.  
>  Insert `x` into the decimal representation of `n` (never to the left of the `-`) such that the resulting integer is maximized.  
>  Return the new number as a string.*

```
Example
Input : n = "73",  x = 6
Output: "763"

Input : n = "-55", x = 2
Output: "-255"
```

`n.length ≤ 100 000`, so we must avoid quadratic time.

---

## 2. Intuition – Why the Greedy Rule Works  

| Sign of `n` | Goal | Strategy |
|-------------|------|----------|
| Positive | *Make the number larger* | Insert `x` **before the first digit that is smaller than `x`**. Placing it earlier gives more high‑value places. |
| Negative | *Make the number less negative* | Insert `x` **before the first digit that is larger than `x`**. In a negative number, a smaller *absolute* value is “greater”. |
| No such digit | Append `x` at the end (positive) / after all digits (negative) | Because every earlier digit is already the best possible choice. |

The greedy rule is optimal because each position is independent of the rest – once we place `x` at the earliest “good” spot we can’t do better.

---

## 3. Algorithm (O(n) time, O(1) space)

```
if n[0] == '-':            // negative number
    for i = 1 .. len-1:
        if n[i] > x:      // first digit larger than x
            insert x before n[i]
            return
    // no such digit -> append at the end
    return n + x
else:                      // positive number
    for i = 0 .. len-1:
        if n[i] < x:      // first digit smaller than x
            insert x before n[i]
            return
    // no such digit -> append at the end
    return n + x
```

*We work purely with the string representation, so no overflow issues.*

---

## 4. Complexity Analysis  

| Metric | Positive | Negative |
|--------|----------|----------|
| Time | `O(len(n))` | `O(len(n))` |
| Extra Space | `O(1)` (in‑place construction via string builder) | `O(1)` |

`len(n)` ≤ 100 000, so the solution easily fits into the time limits.

---

## 5. Reference Implementations  

### 5.1 Java  

```java
import java.util.*;

public class Solution {
    public String maxValue(String n, int x) {
        StringBuilder sb = new StringBuilder();
        char cx = (char) ('0' + x);

        if (n.charAt(0) == '-') { // negative
            sb.append('-');
            boolean inserted = false;
            for (int i = 1; i < n.length(); i++) {
                if (!inserted && n.charAt(i) > cx) {
                    sb.append(cx);
                    inserted = true;
                }
                sb.append(n.charAt(i));
            }
            if (!inserted) sb.append(cx);   // append at the end
        } else { // positive
            boolean inserted = false;
            for (int i = 0; i < n.length(); i++) {
                if (!inserted && n.charAt(i) < cx) {
                    sb.append(cx);
                    inserted = true;
                }
                sb.append(n.charAt(i));
            }
            if (!inserted) sb.append(cx);   // append at the end
        }
        return sb.toString();
    }
}
```

### 5.2 Python  

```python
class Solution:
    def maxValue(self, n: str, x: int) -> str:
        cx = str(x)
        # Positive number
        if n[0] != '-':
            for i, ch in enumerate(n):
                if ch < cx:
                    return n[:i] + cx + n[i:]
            return n + cx   # no position found

        # Negative number
        for i in range(1, len(n)):
            if n[i] > cx:
                return n[:i] + cx + n[i:]
        return n + cx   # append at the end
```

### 5.3 C++  

```cpp
class Solution {
public:
    string maxValue(string n, int x) {
        char cx = '0' + x;
        // Positive number
        if (n[0] != '-') {
            for (size_t i = 0; i < n.size(); ++i) {
                if (n[i] < cx) {
                    return n.substr(0, i) + cx + n.substr(i);
                }
            }
            return n + cx;
        }

        // Negative number
        for (size_t i = 1; i < n.size(); ++i) {
            if (n[i] > cx) {
                return n.substr(0, i) + cx + n.substr(i);
            }
        }
        return n + cx;
    }
};
```

All three solutions run in linear time and constant auxiliary space, meeting the problem constraints.

---

## 6. Blog Article – “The Good, the Bad, and the Ugly of LeetCode 1881”

> **Title:** Mastering LeetCode 1881: *Maximum Value After Insertion* – A Deep Dive for Job‑Hunters  
> **Meta Description:** Learn the optimal greedy solution to LeetCode 1881, see Java/Python/C++ code, and discover interview tips that help you land a software engineer role.  

---

### 6.1 Introduction  

LeetCode is a staple on the interview prep journey. Among the thousands of problems, **1881 – Maximum Value after Insertion** is a classic “string manipulation + greedy” puzzle that tests both your thinking and your coding style.  

If you’re preparing for roles at Google, Amazon, Microsoft, or any fast‑growth startup, nailing this problem demonstrates:

* **Algorithmic intuition** – spotting the greedy rule.  
* **Edge‑case handling** – negative numbers, no suitable position.  
* **Clean implementation** – code that’s easy to read and run in O(n).  

Below we dissect the problem, walk through the optimal solution, provide ready‑to‑copy code in Java, Python, and C++, and give you interview‑friendly insights.

---

### 6.2 Problem Statement (Restated)

> You’re given a large integer `n` as a string (digits 1‑9, possibly negative) and an integer digit `x` (1‑9).  
> Insert `x` somewhere in the decimal representation of `n` (never to the left of a `-`) to make the resulting number the largest possible.  
> Return the new number as a string.

---

### 6.3 Why the Greedy Rule Is Intuitive

1. **Positive Numbers**  
   Placing a larger digit earlier yields a higher place value.  
   → *Insert `x` before the first digit that is smaller than `x`.*

2. **Negative Numbers**  
   In a negative number, a smaller absolute value is “greater.”  
   → *Insert `x` before the first digit that is larger than `x`.*

3. **If No Such Digit Exists**  
   All digits are already optimal relative to `x`.  
   → *Append `x` at the end.*

This simple rule is the heart of the solution – it removes the need for any brute‑force enumeration.

---

### 6.4 Step‑by‑Step Algorithm

```text
If n starts with '-':                     // negative number
    Loop from index 1 to len(n)-1
        If n[i] > x:                      // first digit bigger than x
            Insert x before n[i] and return
    Append x at the end and return

Else:                                     // positive number
    Loop from index 0 to len(n)-1
        If n[i] < x:                      // first digit smaller than x
            Insert x before n[i] and return
    Append x at the end and return
```

*All operations are O(1) per character → overall O(n) time.*

---

### 6.5 Ready‑to‑Run Code

> **Java**  
> ```java
> public class Solution {
>     public String maxValue(String n, int x) { ... }
> }
> ```

> **Python**  
> ```python
> class Solution:
>     def maxValue(self, n: str, x: int) -> str: ...
> ```

> **C++**  
> ```cpp
> class Solution {
> public:
>     string maxValue(string n, int x) { ... }
> };
> ```

*(See the full implementations in the previous section.)*

---

### 6.6 Common Pitfalls (The Ugly)

| Pitfall | Why it breaks | Fix |
|---------|----------------|-----|
| **Using `int` for the number** | 100 000 digits overflow | Treat the input as a *string*; never convert to numeric type. |
| **Ignoring the negative sign** | Inserting before `-` changes the sign | Skip the first character (`'-'`) when scanning. |
| **Appending at the wrong position** | For positive numbers, appending before the last digit can reduce the value | Use the first *smaller* digit rule; otherwise append at the end. |
| **Assuming `x` is a char** | Mistaking `int` `x` for `'0' + x` | Always convert `x` to `char` (`char cx = (char)('0'+x);`). |
| **Off‑by‑one errors** | Wrong loop boundaries (e.g., start at `0` for negative) | For negatives, start at `i=1` to skip `'-'`. |

---

### 6.7 What Interviewers Look For

1. **Problem understanding** – can you restate the task in your own words?  
2. **Algorithm choice** – do you explain why greedy works instead of brute force?  
3. **Edge cases** – negative numbers, all digits already optimal, single‑digit numbers.  
4. **Complexity** – O(n) time, O(1) space; discuss why it’s efficient.  
5. **Code cleanliness** – readable variable names, proper comments, modular design.  
6. **Testing** – show a few example runs, including edge cases.

---

### 6.8 Quick Self‑Test Checklist

- [ ] Does the code handle `n = "1"` and `x = 9`? → `"91"`  
- [ ] Does the code handle `n = "-9"` and `x = 1`? → `"-19"`  
- [ ] Does the code handle `n = "987654321"` and `x = 5`? → `"9876543215"` (no insertion point)  
- [ ] Does the code handle `n = "-123456"` and `x = 7`? → `"-1237456"` (insert before `7`)

If all boxes are checked, you’re ready to give this problem a perfect score!

---

### 6.9 Takeaway – Turning a Problem into a Career Asset

LeetCode 1881 is more than a string puzzle; it’s a *mini‑case study* in algorithmic design. Mastering it gives you:

* Confidence in greedy techniques.  
* A clean multi‑language solution portfolio (Java, Python, C++).  
* A talking point that can lead to deeper questions about string manipulation in interviews.

**Next step:** Practice the problem on your own, run the reference code on random inputs, and be ready to explain your solution in a pair‑programming interview. Good luck – your next job offer could be just an insertion away!  

---

### 6.9 Closing Thoughts  

String manipulation problems like LeetCode 1881 surface in many real‑world coding interviews. By internalizing the greedy rule, avoiding the common pitfalls, and presenting a clean, efficient solution, you’ll not only solve the problem but also impress hiring managers who value clarity and performance.

Happy coding, and may your insertion always land you the maximum value – and the maximum offer!

--- 

> **Author:** Jane Doe – Senior Software Engineer, AI & Cloud Platforms  
> **Contact:** jane.doe@example.com | **LinkedIn:** /in/janedoe  

--- 

### 7. Conclusion  

You now possess:

* A **solid understanding** of LeetCode 1881’s greedy solution.  
* **Reference code** in the three major languages.  
* **Interview insight** that turns a string manipulation problem into a showcase of your algorithmic acumen.  

Use this knowledge to practice, polish, and shine in your next technical interview. Good luck!