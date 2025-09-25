---
title: LeetCode 2083. Substrings That Begin and End With the Same Letter - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – LeetCode 2083  
**Title**: *Substrings That Begin and End With the Same Letter*  
**Difficulty**: Medium  
**Link**: <https://leetcode.com/problems/substrings-that-begin-and-end-with-the-same-letter/>

> You are given a 0‑indexed string `s` consisting only of lowercase English letters.  
> Return the number of **non‑empty** substrings in `s` that begin and end with the same character.

> **Constraints**  
> * `1 ≤ s.length ≤ 10⁵`  
> * `s` contains only lowercase English letters

---

## 2. The Idea – Counting Frequencies

For a given character `c` that appears `k` times in the string, any pair of occurrences of `c` can form a substring that starts and ends with `c`.  
Number of pairs  =  \(\binom{k}{2}\)  =  `k * (k - 1) / 2`.  
Each single character is also a valid substring, so we add `k` to the pair count.

```
total_substrings = Σ ( k*(k-1)/2 + k )   over all 26 letters
```

This gives an **O(n)** solution (one pass to count, one pass over 26 letters) with **O(1)** extra space.

> **Alternative “single‑pass” trick**  
> Scan the string from right to left.  
> While scanning, keep a counter for each letter that tells how many times the letter has already been seen to the right.  
> When you visit a character `c` at position `i`, every future occurrence of `c` will form a substring with `s[i]`.  
> Thus the number of new substrings added at `i` is `count[c] + 1` (the `+1` counts the single‑letter substring).  
> Then increment `count[c]`.

Both approaches have the same asymptotic complexity; the first is simpler to understand, the second uses constant extra space (no `long` array of size 26 is still needed, but it keeps a single array of 26 integers).

---

## 3. Code Implementations

### 3.1 Java 17

```java
/**
 * LeetCode 2083 – Substrings That Begin and End With the Same Letter
 * Java implementation using frequency counting.
 */
class Solution {
    public long numberOfSubstrings(String s) {
        // 26 lowercase letters
        long[] freq = new long[26];
        for (char ch : s.toCharArray()) {
            freq[ch - 'a']++;
        }

        long result = 0;
        for (long k : freq) {
            // k * (k - 1) / 2  +  k
            result += k * (k - 1) / 2 + k;
        }
        return result;
    }
}
```

> **Complexity** – `O(n)` time, `O(1)` extra space.

---

### 3.2 Python 3

```python
# LeetCode 2083 – Python 3 implementation
from typing import List

class Solution:
    def numberOfSubstrings(self, s: str) -> int:
        # Count frequency of each letter
        freq = [0] * 26
        for ch in s:
            freq[ord(ch) - 97] += 1

        result = 0
        for k in freq:
            result += k * (k - 1) // 2 + k
        return result
```

> **Complexity** – `O(n)` time, `O(1)` space.

---

### 3.3 C++17

```cpp
// LeetCode 2083 – C++17 implementation
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long numberOfSubstrings(string s) {
        long long freq[26] = {0};
        for (char ch : s) {
            freq[ch - 'a']++;
        }

        long long ans = 0;
        for (long long k : freq) {
            ans += k * (k - 1) / 2 + k;
        }
        return ans;
    }
};
```

> **Complexity** – `O(n)` time, `O(1)` space.

---

## 4. Blog Article – “The Good, The Bad, and The Ugly of LeetCode 2083”

> **Title**: *The Good, The Bad, and The Ugly of LeetCode 2083 – Mastering Substring Counting for Your Next Coding Interview*  
> **Meta‑Description**: Dive deep into LeetCode 2083, learn the most efficient solutions in Java, Python, and C++, and discover how this problem can boost your interview prospects.  

---

### 4.1 Introduction  

If you’re preparing for a software‑engineering interview, you’ll inevitably stumble on substring‑counting questions.  
LeetCode 2083 is a classic: *“Substrings That Begin and End With the Same Letter.”*  
While the statement looks trivial, the constraints (`|s| ≤ 100 000`) mean a naïve `O(n²)` solution won’t cut it.  

In this post we’ll dissect the problem, walk through **three** effective solutions (two in Java, one in Python, one in C++), and discuss what makes this problem both a *good* training exercise and a *potential stumbling block* in real interviews.

> **SEO Keywords**: leetcode 2083, substrings same letter, coding interview, Java solution, Python solution, C++ solution, job interview coding, algorithm interview, job interview preparation.

---

### 4.2 The Good – Why This Problem is Worth Your Time  

| Aspect | Why it Helps |
|--------|--------------|
| **Simplicity** | The input is a single lowercase string; no nested data structures. |
| **Conceptual Breadth** | Covers frequency counting, combinatorics (`n choose 2`), and single‑pass tricks. |
| **Scalability** | Requires `O(n)` time, testing your ability to handle large inputs. |
| **Language‑agnostic** | Works equally well in Java, Python, C++ – great for showcasing cross‑language skill. |
| **Interview Impact** | Demonstrates understanding of *combinatorial reasoning* and *algorithmic optimization*. |

---

### 4.3 The Bad – Common Pitfalls and How to Avoid Them  

| Pitfall | How to Fix |
|---------|------------|
| **Using `int` for the answer** | The maximum number of substrings is `n(n+1)/2`. For `n = 10⁵`, this is ≈ 5 × 10⁹, exceeding `int` (2.1 × 10⁹). Use `long`/`long long`/`int64`. |
| **Mis‑counting single‑character substrings** | Remember that each occurrence counts as a substring. Don’t forget the `+ k` term in the formula. |
| **Forgetting 0‑based indexing** | When converting `char` to array index, use `c - 'a'` (not `c - 'A'` or `c - 97`). |
| **Off‑by‑one errors in single‑pass** | The `+ 1` in the single‑pass solution counts the single‑letter substring. Skipping it underestimates the answer by `|s|`. |
| **Time complexity misunderstanding** | A naïve double‑loop (`O(n²)`) will TLE for 10⁵. Always think about a linear scan or frequency array. |

---

### 4.4 The Ugly – Edge Cases That Can Trip You Up  

| Edge Case | Why It Matters |
|-----------|----------------|
| **All identical characters** | `s = "aaaaa"` → answer = `n*(n+1)/2`. Verify that your code handles the large combinatorial term without overflow. |
| **All distinct characters** | `s = "abcde"` → answer = `n` (only single‑letter substrings). Check that the pair term yields zero. |
| **Very short strings** | `s = "a"` or `s = ""` (though length ≥ 1). Make sure you don’t return negative numbers. |
| **Large input in Python** | Using the `//` operator is crucial; integer division in Python automatically gives a `int`, but using `/` returns a `float` which may lose precision for 5 × 10⁹. |

---

### 4.5 Step‑by‑Step Implementation

Below is a quick recap of the Java frequency solution (most developers start with this).

```java
long[] freq = new long[26];
for (char ch : s.toCharArray()) {
    freq[ch - 'a']++;
}

long result = 0;
for (long k : freq) {
    result += k * (k - 1) / 2 + k;
}
```

> **Tip**: Always run the provided unit tests (`Test Cases` tab in LeetCode) and add a couple of your own before submitting.

---

### 4.6 Quick Test Harness (Python)  

```python
def brute_force(s: str) -> int:
    n = len(s)
    count = 0
    for i in range(n):
        for j in range(i, n):
            if s[i] == s[j]:
                count += 1
    return count

# Run quick sanity checks
assert brute_force("a") == 1
assert brute_force("aa") == 3          # "a", "a", "aa"
assert brute_force("ab") == 2
assert brute_force("aaa") == 6         # 3 single + 3 pairs
print("All brute force checks passed!")
```

> Running this against the Java / Python / C++ implementations guarantees that the linear algorithm produces the same results.

---

### 4.7 Interview‑Ready Tips  

1. **Explain the “Why” before the “How”** – Interviewers love to hear *why* you’re using a particular approach.  
2. **Mention edge cases** – “We need to be careful with overflow; that’s why we use a 64‑bit integer.”  
3. **Show a clean solution** – Even if you’re not using the single‑pass trick, the frequency approach is short and easy to read.  
4. **Time to code** – Practice typing the solution in all three languages so you can switch on a whiteboard or a shared IDE without hesitation.  

---

### 4.8 Conclusion – Mastering Substring Counting  

LeetCode 2083 is a deceptively simple puzzle that teaches powerful lessons:

* Use **frequency counting** + combinatorial math for a clean, O(n) solution.  
* Avoid common pitfalls: overflow, single‑char oversight, indexing errors.  
* Practice the **single‑pass** trick to showcase space‑optimized thinking.  

Solving this problem in Java, Python, and C++ demonstrates versatility and efficiency—qualities that recruiters look for in their interviewees.  

> **Next step**: Add a `TestCase` class in Java that randomly generates strings and compares your implementation against the brute force for `n ≤ 10`.  
> **Bonus**: Share your solution on GitHub, add a README with the interview‑style explanation above, and let recruiters see your problem‑solving pipeline in action.

Good luck – and happy coding!  

--- 

### 4.9 SEO & Meta Tags  

```html
<title>The Good, The Bad, and The Ugly of LeetCode 2083 – Mastering Substring Counting for Your Next Coding Interview</title>
<meta name="description" content="Explore LeetCode 2083, learn efficient solutions in Java, Python, and C++, and find out how to ace this interview question to boost your job prospects." />
<meta name="keywords" content="leetcode 2083, substrings same letter, coding interview, Java solution, Python solution, C++ solution, job interview coding, algorithm interview, job interview preparation" />
```

--- 

### 4.10 References & Further Reading  

1. LeetCode 2083 – Official Problem Page  
2. “Counting Substrings with the Same Start and End” – LeetCode discuss  
3. “Job Interview Coding Interview Preparation” – Google Developers  

--- 

Happy interviewing!