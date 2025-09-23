---
title: LeetCode 481. Magical String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 481 – **Magical String**  
> *Medium | 1 ≤ n ≤ 10⁵ | O(n) time | O(n) space*  

**Problem Recap**

A *magical string* `s` consists only of the characters `'1'` and `'2'`.  
If you look at the consecutive runs of identical characters in `s`, the length of each run *itself* reproduces the string:

```
s = 1 22 11 2 1 22 1 22 11 2 11 22 …
len(1)  = 1
len(22) = 2
len(11) = 2
len(2)  = 1
…
```

The first few characters are `"1221121221221121122…"`.  
Given an integer `n`, return the number of `'1'` characters in the first `n` characters of this infinite string.

---

## 2.  Why the Naïve Approach Fails

The most straightforward idea is to *build the string one character at a time*, appending `"1"` or `"2"` according to the run‑length sequence:

```python
s = []
for length in run_lengths:        # run_lengths would itself grow
    s.extend([char] * length)     # O(length) per extension
```

Even if we generate `n` characters, each time we add a block we do a linear copy, leading to **O(n²)** time and a lot of memory churn.  
With `n = 10⁵` this quickly exceeds the 1 s time limit on LeetCode.

---

## 3.  The O(n) Solution – Two‑Pointer Magic

The key insight: we *don't need to store the whole string* while we build it.  
We just need two pieces of information:

1. **The current character to write** (`cur = 1` or `2`).
2. **The next run‑length** – which is *the next element in the same string we are building*.

We can maintain the already‑generated part of the string in a list (or array) and use two indices:

| `i` | next run‑length to consume (s[i]) | `ptr` | current character (`cur`) |
|-----|----------------------------------|-------|---------------------------|
| 0   | 1 (first element of s)           | 0     | 1                         |

Algorithm (pseudo‑code):

```
init s[0] = 1
i = 0          // points to the next run‑length to use
ptr = 0        // next position to fill
cur = 1
count1 = 0

while ptr < n:
    length = s[i]        // read run‑length
    for j in 0 .. length-1:
        s[ptr] = cur
        if cur == 1: count1++
        ptr++
        if ptr == n: break
    cur = 3 - cur        // toggle 1<->2
    i++                  // move to next run‑length
return count1
```

Because each position of `s` is written exactly once, the loop runs **O(n)** times, and the array `s` holds at most `n` integers – perfectly within the constraints.

---

## 4.  Reference Implementations

### 4.1 Java

```java
public class Solution {
    public int magicalString(int n) {
        if (n == 0) return 0;
        // Use an int array – 1 or 2 values only.
        int[] s = new int[Math.max(n, 1000)]; // pre‑allocate a bit more for safety
        s[0] = 1;   // first element of magical string

        int i = 0;          // next run-length index
        int ptr = 0;        // next position to fill
        int cur = 1;        // current character to write
        int count1 = 0;     // answer

        while (ptr < n) {
            int len = s[i];           // run length
            for (int j = 0; j < len && ptr < n; j++) {
                s[ptr] = cur;
                if (cur == 1) count1++;
                ptr++;
            }
            cur = 3 - cur;  // toggle 1 <-> 2
            i++;
        }
        return count1;
    }
}
```

> **Why this is good** – only a single array, no string concatenation, constant‑time operations.  

### 4.2 Python

```python
class Solution:
    def magicalString(self, n: int) -> int:
        if n == 0:
            return 0
        s = [0] * max(n, 1000)
        s[0] = 1

        i = 0      # index of next run-length
        ptr = 0    # next position to write
        cur = 1
        count1 = 0

        while ptr < n:
            length = s[i]
            for _ in range(length):
                if ptr >= n:
                    break
                s[ptr] = cur
                if cur == 1:
                    count1 += 1
                ptr += 1
            cur = 3 - cur
            i += 1
        return count1
```

### 4.3 C++

```cpp
class Solution {
public:
    int magicalString(int n) {
        if (n == 0) return 0;
        vector<int> s(max(n, 1000), 0);
        s[0] = 1;

        int i = 0;      // next run-length index
        int ptr = 0;    // next position to fill
        int cur = 1;    // current char
        int count1 = 0;

        while (ptr < n) {
            int len = s[i];
            for (int j = 0; j < len && ptr < n; ++j) {
                s[ptr] = cur;
                if (cur == 1) ++count1;
                ++ptr;
            }
            cur = 3 - cur;  // toggle 1 <-> 2
            ++i;
        }
        return count1;
    }
};
```

---

## 5.  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 481: Magical String”

### Meta Description (SEO‑optimized)
> Master LeetCode 481 (Magical String) with clean Java, Python, and C++ solutions. Learn the good, bad, and ugly approaches, interview insights, and how this problem can boost your coding interview resume.

---

### Introduction

In the world of coding interviews, *magical strings* are a classic “curveball” that tests your ability to think in terms of **generative sequences** rather than simple greedy or DP tricks.  
LeetCode 481 (“Magical String”) is notorious for its deceptively simple statement and its potential to trip up even seasoned developers.

In this article we’ll dissect the problem, compare the **good**, **bad**, and **ugly** implementation patterns, and provide a concise, production‑ready solution in **Java**, **Python**, and **C++** – all optimized for speed and clarity. We’ll also highlight how you can frame this problem in an interview to show off algorithmic thinking and code quality.

---

### 1. Problem Overview

> A magical string `s` contains only `'1'` and `'2'`.  
> If you look at the lengths of consecutive identical characters, that length sequence is *exactly* the string `s`.  
> For a given `n`, count how many `'1'`s appear in the first `n` characters of `s`.

> *Constraints:* `1 ≤ n ≤ 10⁵`

---

### 2. The Good: A Linear‑Time, Linear‑Space Solver

| Feature | Why It Matters |
|---------|----------------|
| **Single Pass** | Each position of the string is written once – `O(n)` time. |
| **No String Concatenation** | Avoids costly `O(n²)` copying. |
| **Intuitive Two‑Pointer** | Keeps track of run‑length index (`i`) and fill index (`ptr`). |
| **Minimal Memory** | An array of size `n` stores only integers 1/2 – `O(n)` space. |
| **Language‑agnostic** | The same logic applies in Java, Python, C++. |

**Pseudo‑code** (see section 3). The Java, Python, and C++ snippets in the reference implementation are all faithful to this pattern.

---

### 3. The Bad: Quadratic Pitfalls

1. **Repeated String Concatenation**  
   ```java
   String s = "";
   for (int len : runLengths) {
       s += String.valueOf(cur).repeat(len); // O(len) each time
   }
   ```
   *Why bad:* Each `+=` triggers a new array allocation and copy. For `n = 10⁵`, this ends up being `O(n²)`.

2. **Unbounded Recursion**  
   ```python
   def build(n):
       if n == 0: return ""
       return str(1) + build(n-1)   # recursion depth = n
   ```
   *Why bad:* Stack overflow and `O(n)` space due to call frames.

3. **Over‑generating**  
   Building the entire infinite string up to a very large limit (e.g., `10⁶`) and then slicing it wastes time and memory.

---

### 4. The Ugly: Over‑Optimization and Hard‑to‑Read Code

Some developers go to extremes:

- **Using LinkedLists** or **Deque** to emulate the queue behavior. While it works, Java’s `LinkedList` is notoriously slow for index access – *not worth it for `n = 10⁵`*.
- **Bit‑packing** the string into bits to save memory. That introduces complex bit‑twiddling that’s unnecessary and hampers readability.
- **Premature micro‑optimizations** (e.g., pre‑allocating a huge array `10⁶` even though `n` might be just 50). The extra memory may be wasteful in interviews where clarity is prized.

---

### 5. Interview‑Ready Talk

> *“The magical string problem is a great example of how to generate data on the fly using a self‑referential sequence. The trick is to realize that the run‑lengths we need are actually the string itself, so we can iterate over the string as we build it.”*

> *Key Points to Mention:*  
> 1. **Two indices** – one for the next run‑length (`i`), one for the next character to write (`ptr`).  
> 2. **Toggle** between `'1'` and `'2'` using `cur = 3 - cur`.  
> 3. **Early exit** – stop filling once we hit `n`.  
> 4. **Complexity** – `O(n)` time, `O(n)` space, which is optimal for the given constraints.

> By framing the solution as a *single linear pass* with *constant‑time updates*, you demonstrate both algorithmic insight and coding discipline—exactly what interviewers are after.

---

### 6. Take‑Away Checklist

- ✅ Use a single array/list; avoid string concatenation.  
- ✅ Keep two pointers: run‑length index and write index.  
- ✅ Toggle the current character with `cur = 3 - cur`.  
- ✅ Return the count of `'1'` after the loop.  
- ✅ Verify against edge cases (`n = 1`, `n = 2`, etc.).  

---

### 7. Final Thoughts

LeetCode 481 is a micro‑cosm of algorithmic elegance: a self‑referential definition that forces you to think about **generation** instead of **search** or **DP**. Mastering the linear‑time solution gives you a solid example of:

- Efficient time/space trade‑offs  
- Clean two‑pointer patterns  
- Practical interview communication

Use the provided Java, Python, and C++ snippets as a reference in your coding interviews or as part of a portfolio to demonstrate problem‑solving chops. Good luck, and may your code stay magical!  

---  

**Keywords:** LeetCode 481, Magical String, interview prep, coding interview, Java solution, Python solution, C++ solution, algorithm, two‑pointer, O(n) solution, job interview coding, SEO optimized blog.