---
title: LeetCode 3517. Smallest Palindromic Rearrangement I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code – **Java, Python & C++**

Below are clean, production‑ready implementations for LeetCode 3517 – “Smallest Palindromic Rearrangement I”.  
All three solve the problem in **O(n log n)** time (using `Arrays.sort`) and **O(n)** space.  
Feel free to replace the sorting step with a counting sort for an *O(n)* time solution if you want to push the performance boundary.

---

### Java

```java
import java.util.Arrays;

public class Solution {
    public String smallestPalindrome(String s) {
        int n = s.length();
        if (n <= 1) return s;            // trivial cases

        // first half of the string (floor(n/2))
        int halfLen = n / 2;
        char[] firstHalf = new char[halfLen];
        for (int i = 0; i < halfLen; ++i) {
            firstHalf[i] = s.charAt(i);
        }

        // sort ascending → lexicographically smallest half
        Arrays.sort(firstHalf);

        // build the palindrome
        StringBuilder sb = new StringBuilder();
        sb.append(firstHalf);                 // left side

        if (n % 2 == 1) {                     // odd length → keep middle char
            sb.append(s.charAt(halfLen));
        }

        // right side is the reverse of the sorted left side
        sb.append(new StringBuilder(new String(firstHalf)).reverse());

        return sb.toString();
    }
}
```

---

### Python

```python
class Solution:
    def smallestPalindrome(self, s: str) -> str:
        n = len(s)
        if n <= 1:
            return s

        half = n // 2
        first_half = sorted(s[:half])          # list of chars sorted
        left = ''.join(first_half)

        mid = s[half] if n % 2 else ''
        right = left[::-1]                     # reverse of left

        return left + mid + right
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string smallestPalindrome(string s) {
        int n = s.size();
        if (n <= 1) return s;

        int half = n / 2;
        string left = s.substr(0, half);      // first half
        sort(left.begin(), left.end());       // lexicographically smallest

        string mid = (n % 2) ? string(1, s[half]) : "";
        string right = left;
        reverse(right.begin(), right.end());

        return left + mid + right;
    }
};
```

---

## 2. Blog Article – *The Good, The Bad, and The Ugly* of LeetCode 3517

> **SEO Title**  
> *LeetCode 3517 – Smallest Palindromic Rearrangement I | Java, Python, C++ Solutions – Get the Job*

> **Meta Description**  
> Master LeetCode 3517 in seconds. Learn the greedy algorithm, understand edge‑cases, and see Java, Python & C++ code. Impress hiring managers with clean, efficient code.

---

### 2.1  Introduction

When recruiters ask “Can you build the lexicographically smallest palindrome from a palindromic string?”, they’re really looking for three things:

1. **Understanding of palindromes & symmetry**  
2. **Greedy thinking – picking the smallest possible left side**  
3. **Clean, production‑ready code in a language they use**

LeetCode 3517, *Smallest Palindromic Rearrangement I*, is the perfect interview problem to showcase all three. Let’s break it down: the *good*, the *bad*, and the *ugly*.

---

### 2.2  Problem Recap

> **Given** a palindrome string `s` of length `n` (`1 ≤ n ≤ 10⁵`),  
> **Return** the lexicographically smallest palindrome that can be formed by rearranging the characters of `s`.

Examples  
| Input | Output | Explanation |
|-------|--------|-------------|
| `"z"` | `"z"` | Only one character – already minimal. |
| `"babab"` | `"abbba"` | Sorting the first half gives `ab`, reverse is `ba`. |
| `"daccad"` | `"acddca"` | Sorted half `acd` → reverse `dca`. |

**Key Insight:**  
A palindrome is completely defined by its first half (and the middle character, if `n` is odd). If we make the first half as small as possible lexicographically, the whole palindrome becomes the smallest possible.

---

### 2.3  The Good – Simple Greedy + Sorting

**Why this works**

* `s` is already a palindrome → the multiset of the first `⌊n/2⌋` chars equals that of the last `⌊n/2⌋` chars.
* By sorting the first half ascending, we arrange the smallest characters at the front.
* Mirroring this sorted half automatically gives us a valid palindrome.
* The middle character (if odd length) is forced – we can’t change it.

**Algorithm Steps**

1. `half = n / 2`
2. `firstHalf = s[0 … half-1]`
3. Sort `firstHalf` ascending.
4. If `n` is odd, `mid = s[half]` else `mid = ""`.
5. `rightHalf = reverse(firstHalf)`
6. Result = `firstHalf + mid + rightHalf`

Complexities  
* **Time:** `O(n log n)` – due to sorting.  
* **Space:** `O(n)` – for the output string and the sorted array.

**Why it’s “good”**

* **Readable** – one loop, one sort, one reversal.  
* **Predictable** – sorting guarantees the minimal left side.  
* **Scalable** – works for the maximum length `10⁵`.

---

### 2.4  The Bad – Naïve Approaches that Fail

| Approach | Problem |
|----------|---------|
| **Brute‑Force Permutations** | `O(n!)` time – impossible for `n > 10`. |
| **Greedy without Sorting** | Taking the leftmost smallest character greedily can lead to an impossible right half. |
| **Rearrange Entire String Randomly** | No guarantee of palindrome property. |
| **Sort Full String, then build** | Sorting the entire string gives a sorted string, but it may not be a palindrome. |

These methods either violate time limits or produce incorrect results. They’re a classic “try every possibility” pitfall.

---

### 2.5  The Ugly – Edge Cases & Pitfalls

| Edge | What Happens? | Fix |
|------|----------------|-----|
| `n = 1` | `half = 0`; empty string sorting. | Return early (`if n <= 1 return s`). |
| `n = 2` | Even length – middle doesn’t exist. | Treat as any even length (half = 1). |
| **All Characters Same** | `s = "aaaa"` → output unchanged. | Still works – sorting does nothing. |
| **Large `n` (10⁵)** | Memory blow? | All operations are linear or `O(n log n)`; well within limits. |
| **Unicode / Uppercase** | Problem guarantees lowercase letters. | No extra handling needed. |

Always remember to handle the odd‑length middle character separately. Forgetting it will produce a string that’s off by one character.

---

### 2.6  Implementation Highlights

Below are the 3–language implementations we provided earlier. They all follow the same algorithm:

```text
1. Extract left half
2. Sort ascending
3. (optional) Append middle char if odd
4. Append reverse of sorted left half
```

**Why these are production‑ready**

* **No external dependencies** – only standard library.
* **Clear variable names** – `halfLen`, `mid`, `left`, `right`.
* **Single responsibility** – the method does exactly one thing.
* **Test‑ready** – easy to unit‑test with the examples above.

---

### 2.7  Complexity Summary

| Implementation | Time | Space |
|----------------|------|-------|
| Java | `O(n log n)` | `O(n)` |
| Python | `O(n log n)` | `O(n)` |
| C++ | `O(n log n)` | `O(n)` |

If you need the ultimate speed, replace the `Arrays.sort` / `sorted()` with a **frequency‑based counting sort** (26 letters). That turns the algorithm into `O(n)` time and still `O(n)` space.

---

### 2.8  Test Cases (Quick Checklist)

```text
s = "z"               → "z"
s = "aa"              → "aa"
s = "babab"           → "abbba"
s = "daccad"          → "acddca"
s = "cbaabc"          → "aabccb"
s = "abccba"          → "abccba"
s = "aaaaabaaaaa"     → "aaaaabaaaaa"
```

Run each in all three languages to confirm identical outputs.

---

### 2.9  Final Thoughts – How This Helps You Land a Job

* **Shows algorithmic clarity** – you reduced the problem to a simple greedy operation.
* **Demonstrates code craftsmanship** – clean, concise, and commented.
* **Illustrates time/space trade‑offs** – you can discuss why you chose sorting vs counting sort.
* **Highlights problem understanding** – you know why sorting the first half is sufficient for a palindrome.

When interviewers see this, they’ll instantly recognize that you can turn a seemingly complex permutation problem into a linear‑time solution. That’s exactly the kind of skill they’re looking for.

Good luck, and happy coding! 🚀

--- 

**Keywords:** Smallest Palindromic Rearrangement, LeetCode 3517, Lexicographically smallest palindrome, Java solution, Python solution, C++ solution, interview coding problem, algorithm interview, job interview coding, greedy algorithm, palindrome rearrangement.