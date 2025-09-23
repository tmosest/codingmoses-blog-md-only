---
title: LeetCode 1987. Number of Unique Good Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🚀 LeetCode 1987 – Number of Unique Good Subsequences  
**Java | Python | C++ Solutions + SEO‑Optimised Blog Post**

> Want to land your next software‑engineering interview? Mastering **LeetCode 1987** will give you a quick win on the “dynamic programming” track, and this post will walk you through the *best* way to solve it, why it’s elegant, and how to talk about it in a job interview.

---

### Table of Contents  
1. [Problem Statement](#problem-statement)  
2. [Intuition & Key Insight](#intuition)  
3. [Dynamic‑Programming Solution](#dp)  
4. [Complexity Analysis](#complexity)  
5. [Corner Cases & Common Pitfalls](#corner-cases)  
6. [Code Snippets](#code)  
   * Java  
   * Python  
   * C++  
7. [The Good, The Bad, & The Ugly](#good-bad-ugly)  
8. [Interview‑Ready Talking Points](#interview-talk)  
9. [SEO Summary](#seo)  

---

<a name="problem-statement"></a>
## 1. Problem Statement

> **Number of Unique Good Subsequences**  
> You are given a binary string `binary`.  
> A **good subsequence** is a non‑empty subsequence that does **not** start with a leading zero unless it is exactly `"0"`.  
> Find the number of **unique** good subsequences of `binary`.  
> Return the answer modulo \(10^9 + 7\).

> **Examples**  
> * `binary = "001"` → answer = 2 (`"0"`, `"1"`)  
> * `binary = "11"`  → answer = 2 (`"1"`, `"11"`)  
> * `binary = "101"` → answer = 5 (`"0"`, `"1"`, `"10"`, `"11"`, `"101"`)

> **Constraints**  
> * \(1 \leq \text{len(binary)} \leq 10^5\)  
> * `binary` contains only `'0'` and `'1'`.

---

<a name="intuition"></a>
## 2. Intuition & Key Insight

The string is binary, so a good subsequence can only end in `'0'` or `'1'`.  
Think of building subsequences **one character at a time**:

* **`zeroEnd`** – number of unique good subsequences that **end with a '0'**.  
* **`oneEnd`** – number of unique good subsequences that **end with a '1'**.

When we read a new character:

| Char | Effect on `zeroEnd` | Effect on `oneEnd` |
|------|--------------------|---------------------|
| `'0'` | We can append this `'0'` to every subsequence that **ended with a `'1'`** → `zeroEnd += oneEnd`. We cannot start a new non‑empty subsequence with a `'0'` unless it’s the single character `"0"`. | No change. |
| `'1'` | No change. | We can append this `'1'` to any existing subsequence (`zeroEnd + oneEnd`) **or** start a new single‑character subsequence `"1"` → `oneEnd += zeroEnd + oneEnd + 1`. |

Finally, the answer is `zeroEnd + oneEnd`.  
But if the string contained at least one `'0'`, the single `"0"` subsequence is **not** counted above, so we add an extra `1` in that case.

The entire DP runs in **O(n)** time and **O(1)** memory.

---

<a name="dp"></a>
## 3. Dynamic‑Programming Solution

```text
Initialize:
    zeroEnd = 0          // subsequences ending with '0'
    oneEnd  = 0          // subsequences ending with '1'
    hasZero = false      // did we see a '0' at all?

For each character c in binary:
    if c == '0':
        zeroEnd = (zeroEnd + oneEnd) % MOD
        hasZero = true
    else:  // c == '1'
        oneEnd = (zeroEnd + oneEnd + 1) % MOD

answer = (zeroEnd + oneEnd + (hasZero ? 1 : 0)) % MOD
```

`MOD = 1_000_000_007`.

---

<a name="complexity"></a>
## 4. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Scan string once | **O(n)** | **O(1)** |
| Modulo operations | O(1) each | – |

For `n = 10^5`, this is well below the limits.

---

<a name="corner-cases"></a>
## 5. Corner Cases & Common Pitfalls

| Edge case | Why it matters | Fix |
|-----------|----------------|-----|
| String has **no** `'0'` | We must not add the extra `"0"` | Track `hasZero` or count zeros first |
| All zeros (`"0000"`) | Only one unique good subsequence: `"0"` | `zeroEnd` becomes 0, `hasZero` true → answer = 1 |
| Large input (`10^5` ones) | Risk of overflow | Use 64‑bit (`long` / `long long`) during addition before mod |
| Modulo mis‑placement | Wrong answer for large numbers | Always apply `% MOD` after every addition |

---

<a name="code"></a>
## 6. Code Snippets

### Java

```java
public class Solution {
    private static final int MOD = 1_000_000_007;

    public int numberOfUniqueGoodSubsequences(String binary) {
        long zeroEnd = 0, oneEnd = 0;
        boolean hasZero = false;

        for (int i = 0; i < binary.length(); i++) {
            char c = binary.charAt(i);
            if (c == '0') {
                zeroEnd = (zeroEnd + oneEnd) % MOD;
                hasZero = true;
            } else { // '1'
                oneEnd = (zeroEnd + oneEnd + 1) % MOD;
            }
        }
        return (int)((zeroEnd + oneEnd + (hasZero ? 1 : 0)) % MOD);
    }
}
```

### Python

```python
class Solution:
    MOD = 10**9 + 7

    def numberOfUniqueGoodSubsequences(self, binary: str) -> int:
        zero_end = one_end = 0
        has_zero = False

        for ch in binary:
            if ch == '0':
                zero_end = (zero_end + one_end) % self.MOD
                has_zero = True
            else:  # '1'
                one_end = (zero_end + one_end + 1) % self.MOD

        return (zero_end + one_end + (1 if has_zero else 0)) % self.MOD
```

### C++

```cpp
class Solution {
public:
    static const int MOD = 1e9 + 7;
    int numberOfUniqueGoodSubsequences(string binary) {
        long long zeroEnd = 0, oneEnd = 0;
        bool hasZero = false;

        for (char c : binary) {
            if (c == '0') {
                zeroEnd = (zeroEnd + oneEnd) % MOD;
                hasZero = true;
            } else {  // c == '1'
                oneEnd = (zeroEnd + oneEnd + 1) % MOD;
            }
        }
        return static_cast<int>((zeroEnd + oneEnd + (hasZero ? 1 : 0)) % MOD);
    }
};
```

---

<a name="good-bad-ugly"></a>
## 7. The Good, The Bad, & The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic elegance** | O(n) time, O(1) memory – a single pass DP | Naïve enumeration is O(2^n) – impossible for n=10⁵ | Forgetting the extra “0” for strings containing zeros leads to a subtle off‑by‑one bug |
| **Implementation clarity** | Two counters + a flag – easy to read | Complex recursion or hash‑set based approaches can obscure the logic | Mixing data types (int vs long) can cause overflow – a frequent interview slip |
| **Scalability** | Works up to 10⁵ chars with 64‑bit arithmetic | A backtracking solution will hit stack limits | Hard‑coded mod value can be mistakenly placed before the addition, giving wrong results |
| **Interview talking‑point** | “I treat the problem as two state DP.” | “I used a brute‑force set.” | “I forgot to handle the single ‘0’ case.” |

---

<a name="interview-talk"></a>
## 8. Interview‑Ready Talking Points

1. **Explain the definition of a good subsequence** – highlight the “no leading zeros” rule.  
2. **State the DP states** – `zeroEnd` and `oneEnd` – why they are sufficient.  
3. **Walk through the transition** – show how appending a character updates the counters.  
4. **Show the final aggregation** – why we add `1` if any zero existed.  
5. **Complexity** – reassure the interviewer about O(n) time and O(1) memory.  
6. **Edge‑case handling** – mention all-zero string, all‑ones, and the modulo guard.  
7. **Optional Extension** – how you would adapt it if we needed to also count *non‑unique* subsequences.

---

<a name="seo"></a>
## 9. SEO Summary

- **Title Tags**: “LeetCode 1987 – Number of Unique Good Subsequences (Java, Python, C++)”  
- **Meta Description**: “Solve LeetCode 1987 with O(n) DP. Read our Java, Python, and C++ solutions, interview tips, and a deep dive into the algorithm.”  
- **Headings**: `# LeetCode 1987`, `## Java Solution`, `## Python Solution`, `## C++ Solution`  
- **Keywords**: *LeetCode 1987*, *Number of Unique Good Subsequences*, *dynamic programming*, *Java solution*, *Python solution*, *C++ solution*, *job interview*, *software engineer*, *algorithm analysis*, *binary string subsequences*.  
- **Internal Links**: Link to other algorithm blogs (“Dynamic Programming with Two States”) and to the LeetCode problem page.  

With this structure, search engines will index the article under the most relevant terms, attracting developers preparing for coding interviews or tackling LeetCode challenges.

---

**Happy coding!** 🚀  
Feel free to fork this repository or adapt the snippets to your preferred language. Good luck on the next interview—your DP solution is a solid talking‑point that shows both algorithmic thinking and clean coding.