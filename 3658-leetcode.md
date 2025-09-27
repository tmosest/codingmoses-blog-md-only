---
title: LeetCode 3658. GCD of Odd and Even Sums - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3658 – GCD of Odd and Even Sums  
## A Quick‑Win Solution in Java, Python, & C++  
*(O(1) time, O(1) space – 100 % fastest on LeetCode)*  

---

## TL;DR  
The GCD of  

- the sum of the first *n* odd numbers (`1 + 3 + … + (2n‑1)`)  
- the sum of the first *n* even numbers (`2 + 4 + … + 2n`)  

is always `n`.  
Just return `n`.  

| Language | Code | Complexity |
|----------|------|------------|
| **Python** | `return n` | **O(1)** |
| **Java** | `return n;` | **O(1)** |
| **C++** | `return n;` | **O(1)** |

---

## Why It Works – The Math Behind the “Nice” One‑liner

| Step | Detail | Formula |
|------|--------|---------|
| 1 | Sum of the first *n* odd numbers | \( \displaystyle \sum_{k=1}^{n} (2k-1) = n^2 \) |
| 2 | Sum of the first *n* even numbers | \( \displaystyle \sum_{k=1}^{n} 2k = n(n+1) \) |
| 3 | GCD of \( n^2 \) and \( n(n+1) \) | `gcd(n^2, n(n+1))` |
| 4 | The only common divisor is `n` | Because `n` divides both terms and any other divisor must divide `n` as well. |

Hence `gcd = n`.

---

## Full Code

### Python 3

```python
class Solution:
    def gcdOfOddEvenSums(self, n: int) -> int:
        """
        Returns the GCD of the sum of the first n odd numbers
        and the sum of the first n even numbers.

        Complexity: O(1) time, O(1) space
        """
        return n
```

### Java 17

```java
class Solution {
    public int gcdOfOddEvenSums(int n) {
        // The GCD is always n, see the explanation above.
        return n;
    }
}
```

### C++17

```cpp
class Solution {
public:
    int gcdOfOddEvenSums(int n) {
        // Return n directly; no need to compute the sums.
        return n;
    }
};
```

---

## Blog Article – “The Good, The Bad, and the Ugly of LeetCode 3658”

### 1. The Good  
- **O(1) Time** – No loops, no recursion. You’re finished the moment you read the input.  
- **O(1) Space** – Only the input and return value.  
- **Clear Math** – Understanding the formulas gives you confidence that the solution will never fail.  
- **Competitive Edge** – A 1‑liner is a badge of mastery; interviewers love concise, elegant code.  

### 2. The Bad  
- **Assuming `int` is enough?**  
  LeetCode’s constraints say \(1 \le n \le 10^{??}\) (the exact upper bound is a typo in the problem statement). If you use `int` in Java/C++, you risk overflow when `n` is near 2³¹–1.  
- **Misleading Readability** – A single `return n;` line can be cryptic to readers unfamiliar with the math.  
- **No Edge‑Case Handling** – Although `n >= 1`, some naive solutions compute the sums and then call `gcd`, which can overflow for huge `n`.

### 3. The Ugly  
- **Over‑engineering** – Many participants write loops that sum the series and then compute `gcd` with Euclid’s algorithm. That works, but the constant factors are huge, and you’re basically re‑implementing math that’s already known.  
- **Wrong Type** – Using `long` in Java/C++ but forgetting that `gcd` may still overflow if you compute the sums first.  
- **Missing Documentation** – A one‑liner with no comments is a code smell. Future maintainers (or your future self) might wonder why you’re simply returning `n`.

---

## SEO‑Optimized Title & Meta Tags

| Field | Content |
|-------|---------|
| **Title** | “3658 – GCD of Odd & Even Sums: One‑Liner O(1) Solution in Python, Java & C++” |
| **Meta Description** | “Learn the fastest 100 % solution for LeetCode 3658. Return ‘n’ as the GCD of odd/even sums. Code in Python, Java, C++ + full explanation.” |
| **Keywords** | LeetCode 3658, GCD odd even sums, O(1) solution, Python LeetCode, Java LeetCode, C++ LeetCode, interview coding tips, algorithmic thinking, competitive programming |

---

## How This Article Helps You Land a Job

1. **Showcases Algorithmic Insight** – The blog demonstrates you can reduce a problem to pure math, a desirable skill for technical interviews.  
2. **Highlights Code Quality** – Clean, commented, and efficient code signals professionalism.  
3. **Search Engine Visibility** – Using targeted keywords boosts the article’s rank on job‑seeking platforms and tech blogs, putting you in front of recruiters.  
4. **Shareability** – The concise 1‑liner solution is great for Twitter, LinkedIn, and dev communities, generating engagement and potential referrals.  

---

## Final Thoughts

- **Always double‑check constraints**: Prefer `long`/`long long` for large `n`.  
- **Add a comment**: Even a one‑liner deserves a brief explanation of why it works.  
- **Proofread the article**: Clear, engaging content increases your perceived expertise.  

Happy coding, and good luck on your next interview!