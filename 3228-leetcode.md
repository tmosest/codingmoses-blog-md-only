---
title: LeetCode 3228. Maximum Number of Operations to Move Ones to the End - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🔥 LeetCode 3228 – **Maximum Number of Operations to Move Ones to the End**  
**Tag** – Medium | Greedy | One‑Pass | O(n) | Binary String  

> *“Want to ace the next interview? Master this LeetCode problem, get the code in Java, Python and C++, and learn how to explain it like a pro.”*  

---

### Table of Contents
1. [Problem Statement](#1)
2. [Intuition & “Good, the Bad, and the Ugly”](#2)
3. [Greedy One‑Pass Solution](#3)
4. [Complexity Analysis](#4)
5. [Reference Implementations](#5)
   - Java  
   - Python  
   - C++
6. [Edge Cases & Common Pitfalls](#6)
7. [Testing & Sample Runs](#7)
8. [Why This Makes a Great Interview Question](#8)
9. [Conclusion & SEO Take‑aways](#9)

---

<a name="1"></a>
## 1. Problem Statement

Given a binary string `s`, you can perform the following operation **any number of times**:

1. Pick an index `i` such that `i + 1 < s.length`, `s[i] == '1'` and `s[i+1] == '0'`.
2. Move that `'1'` to the right until it reaches the end of the string or encounters another `'1'`.

**Goal:** Return the *maximum* number of operations you can perform.

> **Example**  
> `s = "1001101"`  
> Operations:  
> 1. `i=0` → `"0011101"`  
> 2. `i=4` → `"0011011"`  
> 3. `i=3` → `"0010111"`  
> 4. `i=2` → `"0001111"`  
> **Answer:** `4`

---

<a name="2"></a>
## 2. Intuition – The Good, the Bad, and the Ugly

| Aspect | Explanation |
|--------|-------------|
| **Good** | • The operation is *local* – you only care about a `'1'` that is immediately followed by a `'0'`. <br>• Every zero will be “cleared” by each preceding one exactly once. <br>• A single left‑to‑right scan with a counter of seen `'1'`’s suffices. |
| **Bad** | • It’s easy to misinterpret the operation: some think moving a `'1'` consumes the `'1'` (it does not). <br>• Off‑by‑one bugs arise if you forget to skip consecutive zeros; otherwise you double‑count operations. |
| **Ugly** | • A naive simulation (moving one step at a time) would be **O(n²)** and impossible for `n = 10⁵`. <br>• If you try to use a stack or queue, you’re over‑engineering; the greedy trick is all you need. |

---

<a name="3"></a>
## 3. Greedy One‑Pass Solution

### Core Idea
- **`ones`** = number of `'1'` characters seen so far.
- When we hit a `'0'` that is *after* at least one `'1'`, that zero can be moved over by **every** preceding `'1'`.  
  Thus we add `ones` to the answer.
- Once we see a run of zeros, we skip the whole run – the contribution has already been counted.

This greedy choice is optimal because:
- Moving a `'1'` over a zero never creates new opportunities earlier in the string.
- Each zero can be “handled” by each preceding one exactly once; any strategy that delays moving a `'1'` cannot increase the total operations.

---

<a name="4"></a>
## 4. Complexity Analysis

| Metric | Value |
|--------|-------|
| Time   | **O(n)** – single pass through the string. |
| Space  | **O(1)** – only a few integer counters. |
| `n`   | Length of `s` (≤ 10⁵). |

---

<a name="5"></a>
## 5. Reference Implementations

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**. Each follows the same greedy logic.

### Java

```java
public class Solution {
    /**
     * Returns the maximum number of operations to move all '1's to the end.
     *
     * @param s Binary string
     * @return Number of operations
     */
    public static int maxOperations(String s) {
        int ones = 0;   // count of '1's seen so far
        int ops  = 0;   // answer
        int i = 0, n = s.length();

        while (i < n) {
            if (s.charAt(i) == '1') {
                ones++;
                i++;                  // move past this '1'
            } else { // s.charAt(i) == '0'
                if (ones > 0) {
                    ops += ones;      // this zero will be moved by each previous '1'
                }
                // skip consecutive zeros to avoid double counting
                while (i < n && s.charAt(i) == '0') {
                    i++;
                }
            }
        }
        return ops;
    }

    // Example usage
    public static void main(String[] args) {
        System.out.println(maxOperations("1001101")); // 4
        System.out.println(maxOperations("00111"));   // 0
    }
}
```

> **Why this version?**  
> - Uses a `while` loop to skip runs of zeros efficiently.  
> - Handles edge cases where the string starts with zeros or ends with zeros.  
> - Compiles in Java 17+ and runs in microseconds.

---

### Python

```python
def max_operations(s: str) -> int:
    """
    Return the maximum number of operations to move all '1's to the end.

    :param s: Binary string
    :return: Number of operations
    """
    ones = 0   # number of '1's seen so far
    ops = 0
    i = 0
    n = len(s)

    while i < n:
        if s[i] == '1':
            ones += 1
            i += 1
        else:  # s[i] == '0'
            if ones:
                ops += ones
            # skip consecutive zeros
            while i < n and s[i] == '0':
                i += 1
    return ops

# Demo
if __name__ == "__main__":
    print(max_operations("1001101"))  # 4
    print(max_operations("00111"))    # 0
```

> Python’s `while` loop and slicing are clean and fast enough for `n = 10⁵`.  

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

int maxOperations(const string &s) {
    long long ones = 0;          // number of '1's seen
    long long ops  = 0;          // answer
    int i = 0, n = s.size();

    while (i < n) {
        if (s[i] == '1') {
            ++ones;
            ++i;
        } else { // s[i] == '0'
            if (ones) ops += ones;
            // skip consecutive zeros
            while (i < n && s[i] == '0') ++i;
        }
    }
    return static_cast<int>(ops);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cout << maxOperations("1001101") << '\n'; // 4
    cout << maxOperations("00111")   << '\n'; // 0
}
```

> Uses `long long` for safety (though the answer fits in `int` for `n ≤ 10⁵`).  
> Fast I/O and single pass guarantee `O(n)`.

---

<a name="6"></a>
## 6. Edge Cases & Common Pitfalls

| Edge Case | What to watch for | Fix |
|-----------|-------------------|-----|
| String starts with zeros | `ones == 0` → no operations counted. | Keep `ones` at 0; skip zeros normally. |
| Consecutive zeros after many ones | You could double‑count if you don’t skip them. | After adding `ones` to `ops`, **skip all consecutive zeros**. |
| All ones (`"1111"`) | No zeros → 0 operations. | Loop still runs; `ops` stays 0. |
| All zeros (`"0000"`) | No ones → 0 operations. | Same as above. |
| Large input (`n = 10⁵`) | Risk of `O(n²)` if you simulate moves. | Use greedy counter; no simulation. |

---

<a name="7"></a>
## 7. Testing & Sample Runs

```text
Input:  1001101
Output: 4   (as described in the statement)

Input:  00111
Output: 0   (no 1 followed by 0)

Input:  010010
Output: 3
Explanation: zeros at positions 1,3,4 are each moved by preceding ones.
```

You can paste the Java, Python, or C++ snippets into your IDE or online compiler (LeetCode uses a similar environment) and run the sample tests.

---

<a name="8"></a>
## 8. Why This Is a Great Interview Question

- **Greedy reasoning**: Show you can spot a simple pattern in seemingly complex operations.  
- **One‑pass linear time**: Demonstrates efficiency‑first mindset.  
- **Edge‑case handling**: You must think about strings of all zeros, all ones, and varying lengths.  
- **Multi‑language implementation**: Many interviews ask you to write code in a language of your choice; being fluent in Java, Python, and C++ shows versatility.

> *Tip:* When explaining, start with a small example, show the greedy step, then argue why moving a `'1'` earlier can never hurt, and finish with the O(n) proof.

---

<a name="9"></a>
## 9. Conclusion & SEO Take‑aways

- **Problem:** Leverage a simple greedy counter to compute the maximum number of `"1→0"` moves.  
- **Solution:** `O(n)` single pass with `ones` counter; skip zero runs.  
- **Codes:** Clean, production‑ready Java, Python, and C++ implementations.  

**If you’re preparing for coding interviews or coding contests, mastering this problem will strengthen your algorithmic toolkit.**  

### SEO Pointers for Your Blog Post

| Goal | Keyword | Why it matters |
|------|---------|----------------|
| Targeted audience | “LeetCode 1001101”, “max operations move 1’s to end” | Specificity attracts readers looking for this exact problem. |
| Multi‑language content | “Java Python C++ solution” | Improves rankings for language‑specific searches. |
| Explain & optimize | “greedy algorithm LeetCode”, “O(n) string operations” | Signals deep understanding and algorithmic efficiency. |
| Real‑world context | “interview preparation”, “coding interview questions” | Appeals to recruiters and fellow developers. |

Now you’re fully equipped to conquer **LeetCode** or any interview that throws this problem your way. Happy coding! 🚀

--- 

**Prepared by:** *[Your Name]* – Algorithm Enthusiast & Full‑Stack Developer  
**Tags:** #LeetCode #AlgorithmDesign #Greedy #Java #Python #C++ #InterviewPrep #O(n) #CodingChallenge
