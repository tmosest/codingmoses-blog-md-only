---
title: LeetCode 400. Nth Digit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📘 LeetCode 400 – Nth Digit  
**Java | Python | C++ – O(1) log n Solutions + In‑Depth Blog Post**

> **SEO Keywords** – *LeetCode 400 Nth Digit*, *Nth Digit solution*, *LeetCode interview*, *Java Python C++ algorithm*, *O(log n) time complexity*, *coding interview questions*, *tech‑career resume*

---

### 1️⃣ Problem Overview

> **LeetCode 400 – Nth Digit**  
> *Difficulty:* **Medium**  
> *Public Function:* `int findNthDigit(int n)`

> **Goal**  
> Given a positive integer `n`, return the *n‑th* digit in the infinite sequence  
> `1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, …`  
> Example:  
> `n = 11` → `0` (the 0 in the number 10).

> **Constraints**  
> `1 ≤ n ≤ 2^31 − 1`

---

### 2️⃣ Why This Problem Rocks

| ✅ Good | ❌ Bad | ⚡ Ugly |
|--------|-------|--------|
| **Mathematics meets programming** – a pure arithmetic trick. | **Integer overflow** – many naive loops will exceed `int`. | **Off‑by‑one traps** – mis‑counting the digit groups leads to wrong answers. |
| **O(log n) time** – only a handful of iterations. | **Large `n`** – must use 64‑bit arithmetic. | **Complexity confusion** – some solutions claim `O(1)` but they’re still linear in the number of digits. |
| **Cross‑language learning** – same logic in Java, Python, C++. | **No direct string manipulation** – you can’t just build the sequence. | **Edge case 10‑digit numbers** – 9, 99, 999… require careful handling. |

---

### 3️⃣ Intuition & Mathematical Insight

1. **Group numbers by digit length**  
   *1‑digit numbers:* 1–9 (9 numbers) → 9 digits  
   *2‑digit numbers:* 10–99 (90 numbers) → 180 digits  
   *3‑digit numbers:* 100–999 (900 numbers) → 2700 digits  
   …  

2. **Find the digit group that contains the `n`‑th digit**  
   Subtract the size of each group (`groupSize = count * digitLength`) from `n` until `n` ≤ groupSize.

3. **Locate the exact number**  
   `numberOffset = (n-1) / digitLength` (0‑based)  
   `digitOffset  = (n-1) % digitLength`

4. **Extract the digit**  
   Convert the number to a string or use math (`pow10`) and pick the digit at `digitOffset`.

All arithmetic uses 64‑bit (`long`) to stay safe up to `2^31−1`.

---

### 4️⃣ Pseudo‑Code

```
findNthDigit(n):
    length = 1               # current digit length
    count  = 9               # numbers with this length
    while n > length * count:
        n -= length * count
        length += 1
        count *= 10
    # n is now inside the current group
    number = 10^(length-1) + (n-1) / length
    digitIndex = (n-1) % length
    return digit of number at digitIndex
```

---

### 5️⃣ Complete Implementations

> ⚙️ All three codes below run in **O(log n)** time, **O(1)** space, and handle the full 32‑bit signed integer range.

#### 5.1 Java

```java
public class Solution {
    public int findNthDigit(int n) {
        long N = n;                // promote to long for safety
        int length = 1;            // digit length
        long count = 9;            // numbers with this length

        // Step 1: locate the group
        while (N > length * count) {
            N -= length * count;
            length++;
            count *= 10;
        }

        // Step 2: locate the exact number
        long number = (long) Math.pow(10, length - 1) + (N - 1) / length;
        int digitIndex = (int) ((N - 1) % length);

        // Step 3: extract the digit
        return String.valueOf(number).charAt(digitIndex) - '0';
    }
}
```

#### 5.2 Python

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        N = n
        length = 1          # digit length
        count = 9           # numbers with this length

        # Locate the correct group
        while N > length * count:
            N -= length * count
            length += 1
            count *= 10

        # Locate the exact number
        number = 10 ** (length - 1) + (N - 1) // length
        digit_index = (N - 1) % length

        # Extract digit
        return int(str(number)[digit_index])
```

#### 5.3 C++

```cpp
class Solution {
public:
    int findNthDigit(int n) {
        long long N = n;
        int length = 1;
        long long count = 9;

        // Find the group
        while (N > length * count) {
            N -= length * count;
            length++;
            count *= 10;
        }

        // Find the number
        long long number = (long long)pow(10, length - 1) + (N - 1) / length;
        int digitIndex = (int)((N - 1) % length);

        // Convert to string for digit extraction
        string s = to_string(number);
        return s[digitIndex] - '0';
    }
};
```

> **Tip**: In Java/C++ you could avoid `pow` by iteratively multiplying a `long` variable.  
> In Python, integer arithmetic is arbitrary precision, so no worries.

---

### 6️⃣ Edge‑Case Checklist

| Edge Case | Why it matters | Fix |
|-----------|----------------|-----|
| `n = 1` | First digit | The algorithm naturally returns `1`. |
| `n = 9` | Last 1‑digit number | `N` will stop in the first group. |
| `n = 10` | First digit of `10` | After group subtraction, `N` will be `1`. |
| `n = 190` | Last 2‑digit number | Group subtraction ends after 2‑digit group. |
| `n = 2^31-1` | Max integer | All intermediate values fit into `long`. |

---

### 7️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(log n)` (digit groups ≈ 10) | `O(log n)` | `O(log n)` |
| **Space** | `O(1)` | `O(1)` | `O(1)` |

The loop runs at most 10 times because the maximum number of digits for a 32‑bit integer is 10.

---

### 8️⃣ SEO‑Optimized Blog Post: “The Good, The Bad, and The Ugly of LeetCode 400 – Nth Digit”

> **Introduction**  
> The *Nth Digit* problem (LeetCode 400) is a staple on coding interview boards. It’s simple to state but subtly tricky: it tests your ability to turn a number‑theory insight into clean, efficient code. In this article, we’ll dissect the problem, reveal the genius behind the O(log n) solution, and expose common pitfalls that trip up many candidates. Along the way, we’ll provide ready‑to‑paste Java, Python, and C++ solutions that you can drop into your coding interview or portfolio.

---

#### Why You Should Master This Problem

- **Interview Frequency**: Recruiters love it because it reveals algorithmic thinking without drowning candidates in data‑structure jargon.
- **Time‑Efficiency Showcase**: A perfect example of transforming a seemingly linear task into logarithmic time.
- **Cross‑Language Proficiency**: Demonstrates that you can port a core idea across Java, Python, and C++ – a must‑have for tech‑companies with polyglot stacks.

---

#### The **Good**: Clean, Elegant Math

The crux of the solution lies in recognizing the *grouped* structure of the sequence:

| Digit Length | Range | Count | Total Digits |
|--------------|-------|-------|--------------|
| 1 | 1‑9 | 9 | 9 |
| 2 | 10‑99 | 90 | 180 |
| 3 | 100‑999 | 900 | 2700 |
| … | … | … | … |

By iteratively subtracting `length * count`, we isolate the group that contains the `n`‑th digit. This reduces a potentially huge search space to a handful of arithmetic operations.

**Takeaway**: *When a problem involves a long sequence, always look for hidden patterns that can be expressed mathematically.*

---

#### The **Bad**: Overflow & Off‑by‑One Nightmares

1. **Integer Overflow**  
   A naïve loop that builds the sequence or iterates `n` times will quickly exceed `int` limits.  
   *Solution:* Use `long` (Java) / `long long` (C++) / default Python ints.

2. **Off‑by‑One Errors**  
   Mis‑counting the digits in a group (e.g., `length * count` vs. `length * (count-1)`) or the offset inside a number leads to wrong answers.  
   *Solution:* Stick to 0‑based indexing (`n-1`) consistently.

3. **Misinterpreting `pow`**  
   In C++/Java, `pow` returns a floating point; converting to `int` can truncate.  
   *Solution:* Build the base (`10^(length-1)`) iteratively or cast the result carefully.

---

#### The **Ugly**: Hidden Edge Cases

- **Large `n` (≈ 2 billion)**: The algorithm must still run within milliseconds.  
- **The 10‑digit threshold**: After 9 digits, the next group (10‑digit numbers) jumps the base from 1 000 000 000 to 10 000 000 000, exceeding 32‑bit.  
- **Negative `n`**: LeetCode guarantees positivity, but defensive programming still matters in real‑world code.

---

#### Step‑by‑Step Walkthrough (Java Example)

```java
int n = 11;         // 11th digit in the sequence
long N = n;         // Promote to long
int length = 1;     // Current digit length
long count = 9;     // Numbers in this group

// Find the correct group
while (N > length * count) {
    N -= length * count;
    length++;
    count *= 10;
}

// Determine the target number
long number = (long) Math.pow(10, length - 1) + (N - 1) / length;

// Extract the specific digit
int digitIndex = (int) ((N - 1) % length);
int result = String.valueOf(number).charAt(digitIndex) - '0';
System.out.println(result); // prints 0
```

---

#### “Why I Write This Blog”

- **Job‑Ready Knowledge**: The more you can explain a problem’s *why* and *how*, the better you’ll communicate during interviews.  
- **Portfolio Asset**: Share the Java/Python/C++ code on GitHub, link it in your résumé. Recruiters love seeing clean, language‑agnostic solutions.  
- **Search Engine Boost**: Keywords like *LeetCode 400 solution*, *Nth digit algorithm*, and *interview coding challenge* help recruiters find you online.

---

#### Final Thoughts

The *Nth Digit* problem is deceptively simple but rich with lessons:

- **Leverage mathematics** to collapse a huge search space.  
- **Guard against overflow** by using 64‑bit integers.  
- **Test thoroughly** with the edge cases listed above.

Master this problem, polish the code snippets, and you’ll have a standout piece in your coding interview toolkit.

> **Ready to impress?** Copy the Java, Python, or C++ solution, practice explaining the algorithm in your own words, and watch recruiters notice the depth of your problem‑solving skills.

---

### 9️⃣ Bonus: Quick Test Cases

| `n` | Expected | Reason |
|-----|----------|--------|
| 3   | 3 | Third digit of the sequence. |
| 11  | 0 | 11th digit is the 0 in `10`. |
| 190 | 9 | Last digit of 2‑digit numbers (`99`). |
| 1000 | 1 | First digit of `100`. |
| 2147483647 | 8 | Edge case – test the limit. |

--- 

Happy coding! 🚀