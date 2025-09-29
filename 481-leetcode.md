---
title: LeetCode 481. Magical String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 481. Magical String – Code & Blog

---

### 1. Problem Recap

A **magical string** `s` consists only of the digits `1` and `2`.  
If you look at the *run‑length* of consecutive identical digits in `s`, the
sequence of those run lengths is exactly `s` itself.

```
s = 1 22 11 2 1 22 1 22 11 2 11 22 …
          ↑  ↑  ↑  ↑  ↑  ↑  ↑  ↑  ↑  ↑
lengths = 1  2  2  1  1  2  1  2  2  1  2 …
```

Given an integer `n (1 ≤ n ≤ 10^5)`, return how many `'1'`s appear in the
first `n` characters of `s`.

---

### 2. Brute‑Force vs. Optimal

| Approach | Time | Space | Comments |
|----------|------|-------|----------|
| Build `s` character‑by‑character up to `n` | **O(n)** | **O(n)** | Works for the constraints, but `StringBuilder` in Java or repeated concatenation in Python can be costly. |
| Pre‑compute run‑lengths in an array and generate `s` on the fly | **O(n)** | **O(n)** | Most common optimal solution. |
| Recursive definition with memoization | **O(n)** | **O(n)** | Works but overkill for this size. |

> **Good** – O(n) time, O(n) memory, straightforward to understand.  
> **Bad** – Using `String` concatenation in Java/Python can hit quadratic time.  
> **Ugly** – Some online solutions use `LinkedList` or recursion, which add unnecessary overhead and obscure the logic.

---

### 3. Algorithm (Linear Time, O(n) Space)

1. **Pre‑allocate** an `int[] s` of length `n` to store the magical string as
   numbers 1 or 2.
2. `s[0] = 1`, `s[1] = 2`, `s[2] = 2`.  
   These are the first three digits that satisfy the definition.
3. Keep two pointers:
   * `idx` – the index in `s` that we’re currently reading to know the run‑length.
   * `cur` – the current position we’re writing to in `s`.
4. While `cur < n`:
   * `len = s[idx]` (run‑length: either 1 or 2).
   * `val = 3 - s[cur-1]` – toggle between 1 and 2.
   * Write `len` copies of `val` into `s` starting at `cur`.  
     Stop if we reach `n`.
   * Increment `idx` and `cur` accordingly.
5. Finally, count how many `1`’s are in the first `n` positions.

Because each character is written exactly once, the algorithm is linear.

---

### 4. Implementation

#### Java (LeetCode style)

```java
class Solution {
    public int magicalString(int n) {
        if (n == 0) return 0;

        // Build the string as an int array.
        int[] s = new int[n];
        s[0] = 1; // first three digits are fixed
        if (n > 1) s[1] = 2;
        if (n > 2) s[2] = 2;

        int idx = 2;   // position in s that tells the run length
        int cur = 3;   // next free position to write
        int ones = 1;  // we already placed one '1' at s[0]

        while (cur < n) {
            int len = s[idx];          // 1 or 2
            int val = 3 - s[cur - 1];  // toggle between 1 and 2

            // Write `len` copies of `val`
            for (int i = 0; i < len && cur < n; i++) {
                s[cur++] = val;
                if (val == 1) ones++;
            }
            idx++;
        }

        return ones;
    }
}
```

#### Python

```python
class Solution:
    def magicalString(self, n: int) -> int:
        if n == 0:
            return 0

        s = [0] * n
        s[0] = 1
        if n > 1: s[1] = 2
        if n > 2: s[2] = 2

        idx, cur, ones = 2, 3, 1   # idx: read pos, cur: write pos, ones: count

        while cur < n:
            length = s[idx]         # 1 or 2
            val = 3 - s[cur - 1]    # toggle between 1 and 2

            for _ in range(length):
                if cur >= n:
                    break
                s[cur] = val
                if val == 1:
                    ones += 1
                cur += 1
            idx += 1

        return ones
```

#### C++

```cpp
class Solution {
public:
    int magicalString(int n) {
        if (n == 0) return 0;
        vector<int> s(n, 0);
        s[0] = 1;
        if (n > 1) s[1] = 2;
        if (n > 2) s[2] = 2;

        int idx = 2;          // read index
        int cur = 3;          // write index
        int ones = 1;         // we already placed a 1

        while (cur < n) {
            int len = s[idx];           // 1 or 2
            int val = 3 - s[cur - 1];   // toggle

            for (int i = 0; i < len && cur < n; ++i) {
                s[cur++] = val;
                if (val == 1) ++ones;
            }
            ++idx;
        }
        return ones;
    }
};
```

---

### 5. Blog Article – “LeetCode 481: Magical String – A Deep Dive for Your Next Coding Interview”

> **Meta Description**  
> Master LeetCode 481 “Magical String” in Java, Python, and C++ with an O(n) solution. Understand the problem, pitfalls, and how to ace this medium interview question.

---

#### Introduction

If you’re preparing for software engineering interviews, the LeetCode problem **481. Magical String** is a perfect *medium*‑difficulty exercise. It tests your ability to:

- Translate a formal definition into code.
- Generate a sequence on the fly without storing the entire string in memory.
- Optimize for time and space.

In this post we’ll walk through the problem statement, illustrate a clean O(n) solution, examine common pitfalls (“bad” and “ugly” patterns), and provide ready‑to‑copy implementations in **Java**, **Python**, and **C++**.

---

#### Problem Recap (What the Interviewer Wants)

> A *magical string* `s` consists only of the digits `1` and `2`.  
> If you group consecutive identical digits in `s`, the *run‑length* of each group equals the corresponding digit in `s` itself.

> **Goal:**  
> Return the number of `'1'`s in the first `n` characters of `s` (`1 ≤ n ≤ 10^5`).

> **Examples**
> ```
> n = 6  →  s = 122112  →  3 ones
> n = 1  →  s = 1       →  1 one
> ```

---

#### Why Is This Problem Interesting?

- **Self‑referential Sequence** – The string defines itself, a classic pattern seen in Fibonacci‑type problems.
- **Run‑Length Encoding** – You’ll be building a string from its run‑length representation, a common interview trick.
- **Large Input Size** – You must avoid O(n^2) approaches such as repeated string concatenation.

---

#### Naïve Approaches (The Bad)

1. **Repeated Concatenation** –  
   ```java
   String s = "";
   while (s.length() < n) {
       s += (s.length() == 0 ? "1" : (s.length() % 2 == 0 ? "22" : "1"));
   }
   ```
   *Why it’s bad:* Each `+=` on a `String` creates a new object → O(n²) time.

2. **LinkedList of Characters** –  
   ```java
   LinkedList<Integer> list = new LinkedList<>();
   list.add(1); list.add(2); list.add(2);
   ```
   *Why it’s bad:* LinkedList overhead is high, and you still iterate over the list many times.

These patterns are easy to write but make the solution fail for `n = 10^5`.

---

#### Optimal Strategy (The Good)

1. **Pre‑allocate an array** of size `n` (or an `int[]` in Java, `vector<int>` in C++, `list[int]` in Python).  
2. **Use two pointers**:
   - `idx` – reads the run‑length from the array.
   - `cur` – writes the next group into the array.
3. **Toggle between 1 and 2** by `val = 3 - prev`, avoiding conditional statements.
4. **Count `1`s on the fly** – no need for a second pass.

**Time Complexity:** `O(n)` – each element is written exactly once.  
**Space Complexity:** `O(n)` – we keep the array of length `n` (the minimal necessary storage).

---

#### Walkthrough (Illustration)

```
Start:  s[0] = 1, s[1] = 2, s[2] = 2
idx = 2  (pointing at third element which tells run length 2)
cur = 3  (next free position)

Loop:
len = s[idx] = 2          // we need a group of 2 digits
val = 3 - s[cur-1] = 3-2 = 1   // toggle to 1
Write 2 copies of 1 at positions 3 and 4
ones += 2
idx++  → 3
cur++  → 5
```

Continue until `cur == n`. The array now contains the first `n` digits of the magical string, and we already know how many ones we counted.

---

#### The Code

> **Java**  
> ```java
> class Solution {
>     public int magicalString(int n) { … }
> }
> ```

> **Python**  
> ```python
> class Solution:
>     def magicalString(self, n: int) -> int: …
> ```

> **C++**  
> ```cpp
> class Solution {
> public:
>     int magicalString(int n) { … }
> };
> ```

*(See the code blocks above for the full implementations.)*

---

#### Edge Cases & Testing

| Input | Expected Output | Reason |
|-------|-----------------|--------|
| 1 | 1 | Only the first digit `1`. |
| 2 | 1 | `12` – still one `1`. |
| 3 | 1 | `122` – only the first digit is `1`. |
| 4 | 2 | `1221` – two `1`s. |
| 100000 | *some number* | Ensure algorithm runs within time limit. |

Always test the lower bounds, the exact value `n = 10^5`, and random mid‑range values.

---

#### Common Interview Traps

| Trap | What to Watch For |
|------|-------------------|
| Off‑by‑one errors | Remember that `idx` starts at `2` (third element). |
| Integer overflow | `n ≤ 10^5`, so `int` is safe. |
| Wrong toggle | Use `val = 3 - prev` instead of `prev == 1 ? 2 : 1`. |
| Unnecessary loops | Avoid nested loops that iterate over already generated parts. |

---

#### Final Thoughts (The Ugly)

Some solutions on the internet use:

- **Recursive generation** with memoization – works but hides the linear nature and increases stack depth risk.
- **LinkedList or Queue** for the run‑length sequence – adds constant factors that matter for tight time limits.
- **StringBuilder with many `append` calls** – still O(n) but the constant factor is higher than array manipulation.

Stick to the array‑based linear approach for a clean, fast solution.

---

#### SEO Summary

- **Keywords**: *LeetCode 481*, *Magical String*, *Java solution*, *Python solution*, *C++ solution*, *coding interview*, *algorithm*, *job interview*, *software engineering*, *programming interview questions*.  
- **Meta**: “Solve LeetCode 481 – Magical String in Java, Python & C++ with an O(n) algorithm. Learn how to ace this interview question.”

---

### 6. Take‑Away Checklist

- [ ] Understand the definition of a magical string.
- [ ] Use an array to store the sequence – no string concatenation.
- [ ] Maintain two pointers (`idx`, `cur`) and toggle values with `3 - prev`.
- [ ] Count ones as you generate the array.
- [ ] Verify with edge cases (1, 2, 3, 4, 10^5).
- [ ] Keep the solution time O(n) and memory O(n).

Good luck, and happy coding!