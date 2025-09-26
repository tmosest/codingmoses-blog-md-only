---
title: LeetCode 1952. Three Divisors - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## Three Divisors – 1952 (LeetCode)  
**Goal**: Return `true` if an integer `n` has exactly three positive divisors, otherwise `false`.  
**Constraints**: `1 ≤ n ≤ 10⁴`

---

## 1️⃣ Three Ways to Solve It  
Below you’ll find clean, idiomatic solutions in **Java**, **Python**, and **C++**.  
The implementation uses the mathematical fact:

> A number has exactly three divisors **iff** it is a perfect square of a prime number.

---

### 📌 Java (Efficient & Readable)

```java
public class Solution {
    // O(√n) time, O(1) space
    public boolean isThree(int n) {
        if (n < 4) return false;          // 1,2,3 cannot have 3 divisors

        int root = (int) Math.sqrt(n);
        if (root * root != n) return false; // not a perfect square

        return isPrime(root);              // check if sqrt(n) is prime
    }

    private boolean isPrime(int x) {
        if (x < 2) return false;
        if (x % 2 == 0) return x == 2;
        for (int i = 3; i * i <= x; i += 2) {
            if (x % i == 0) return false;
        }
        return true;
    }
}
```

> **Why it works**  
> * Perfect squares are the only candidates that can have an odd number of divisors.  
> * If the square root is a prime, its divisors are `1`, `p`, `p²` → exactly three.

---

### 📌 Python (Elegant One‑liner)

```python
class Solution:
    def isThree(self, n: int) -> bool:
        # Perfect square check
        root = int(n ** 0.5)
        if root * root != n:
            return False

        # Prime check for root
        if root < 2:
            return False
        if root % 2 == 0:
            return root == 2
        for i in range(3, int(root ** 0.5) + 1, 2):
            if root % i == 0:
                return False
        return True
```

> **Pythonic touches**  
> * `int(n ** 0.5)` gives the floor of the square root.  
> * The loop iterates only over odd numbers for speed.

---

### 📌 C++ (Fast & STL‑free)

```cpp
class Solution {
public:
    bool isThree(int n) {
        if (n < 4) return false;

        int root = static_cast<int>(sqrt(n));
        if (root * root != n) return false;

        return isPrime(root);
    }

private:
    bool isPrime(int x) {
        if (x < 2) return false;
        if (x % 2 == 0) return x == 2;
        for (int i = 3; i * i <= x; i += 2)
            if (x % i == 0) return false;
        return true;
    }
};
```

> **Why it’s optimal**  
> * `sqrt()` from `<cmath>` is O(1).  
> * Prime test loops only up to `√root`, which is ≤ 100 for the given constraints.

---

## 2️⃣ Blog Article – “The Good, the Bad, and the Ugly of Solving Three Divisors”

### Title  
**“Mastering LeetCode 1952 – Three Divisors: A Job‑Ready Guide (Java, Python, C++)”**

### Meta Description  
Unlock your next interview with our in‑depth walkthrough of LeetCode 1952 (Three Divisors). Learn the math, code in Java/Python/C++, and understand the good, bad, and ugly pitfalls. Ideal for software engineers targeting top tech jobs.

---

### 📚 Introduction  
When you hit **LeetCode 1952 – Three Divisors**, you’re looking at an “Easy” problem that can be a *golden opportunity* in a technical interview. It tests your ability to:

1. Spot mathematical shortcuts.  
2. Write clean, efficient code.  
3. Translate a simple algorithm into multiple languages.

Below, we dissect the problem, show you the *good* efficient solution, expose the *bad* naive pitfalls, and warn you about the *ugly* anti‑patterns. All while keeping the code snippets ready for Java, Python, and C++.

---

### 🔍 Problem Recap  
> **Given** an integer `n` (1 ≤ n ≤ 10⁴).  
> **Return** `true` if `n` has exactly three positive divisors, otherwise `false`.

---

### 💡 The Math Behind the Solution  

1. **Divisors in Pairs** – For a number `n`, every divisor `d` has a partner `n/d`.  
2. **Perfect Squares** – Only perfect squares break this pairing, leaving one unpaired divisor (`√n`).  
3. **Three Divisors** – For a perfect square `p²`, its divisors are `1`, `p`, `p²`.  
   *Hence, `p` must be prime to keep the count at three.*

**Key Insight**: *Check if `n` is a perfect square; if so, test if its square root is a prime.*

---

### ⚡️ The Good – Efficient & Elegant Code  

*Why it’s great*:  
- **Time Complexity**: O(√n) – far below the constraint limit.  
- **Space Complexity**: O(1).  
- **Readability**: Small helper function (`isPrime`).  
- **Language Agnostic**: Works in Java, Python, C++ with minor syntax changes.

See the code snippets above.

---

### ❌ The Bad – Naïve Brute Force  

```java
int count = 0;
for (int i = 1; i <= n; i++) {
    if (n % i == 0) count++;
}
return count == 3;
```

*Problems*  
- **O(n) Time** – Even with n = 10⁴, this is wasteful in an interview setting.  
- **Inefficient for Larger Limits** – If constraints were relaxed, this would time out.  
- **Poor Use of Math** – Ignores the divisor pairing property.

**When it’s Acceptable**: For toy problems or very small input ranges. But for real interviews, this is a red flag.

---

### ⚠️ The Ugly – Common Anti‑Patterns  

| Pattern | Why It’s Ugly | Fix |
|---------|---------------|-----|
| **Hard‑coded limits** | `if (n < 4)` | Use generic checks (`root*root != n`). |
| **Repeated sqrt** | Calling `sqrt(n)` inside a loop | Compute once and reuse. |
| **Full trial division** | Checking all numbers up to `n` | Check only up to `√root`. |
| **Not handling edge cases** | Forgetting `n = 1` | Return `false` immediately. |
| **Verbose variable names** | `isThree` vs `hasThreeDivisors` | Use clear, descriptive names. |

---

### 🚀 Takeaway for Job Interviews  

1. **Talk through the math** before coding.  
2. **Mention the O(√n) strategy** – interviewers love seeing algorithmic awareness.  
3. **Show clean code** – small helper functions, clear names, and comments.  
4. **Be ready to translate** to any language – you’ll likely get asked to rewrite in another language.

---

### 🎯 Keywords & SEO Boost  

- Three Divisors problem  
- LeetCode 1952 solution  
- Java three divisors code  
- Python prime check  
- C++ perfect square algorithm  
- Interview algorithm practice  
- Job interview algorithm questions  
- Software engineer interview prep  
- Math-based coding challenge  

---

### 📌 Final Thoughts  

- **Good**: Elegant prime‑square logic, O(√n) time.  
- **Bad**: Brute‑force divisor counting.  
- **Ugly**: Inefficient loops, unhandled edge cases, poor naming.

By mastering this problem, you’ll demonstrate your ability to combine mathematical insight with clean code—a key skill employers look for. Happy coding, and best of luck on your next interview!