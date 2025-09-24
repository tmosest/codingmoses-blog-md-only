---
title: LeetCode 2652. Sum Multiples - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 2652 – **Sum Multiples**  
**Your go‑to guide for Java, Python & C++ solutions + a killer SEO‑optimized blog article**

> *“Sum all integers from 1 to n that are divisible by 3, 5, or 7.”*  
> **Difficulty:** Easy

---

## TL;DR

| Language | O(1) Math Formula | O(n) Loop |
|---------|-------------------|-----------|
| **Java** | ✔️ `sumOfMultiples(n)` – uses arithmetic series | ❌  |
| **Python** | ✔️ `sum_of_multiples(n)` – uses arithmetic series | ❌  |
| **C++** | ✔️ `sumOfMultiples(n)` – uses arithmetic series | ❌  |

> **Choose the Math solution** to pass large tests in *< 1 ms* and impress interviewers.

---

## Blog Article – “The Good, The Bad, and The Ugly of Sum Multiples”

> **SEO Keywords**: *LeetCode Sum Multiples, Java sum of multiples, Python sum of multiples, C++ sum of multiples, interview coding challenge, O(1) solution, arithmetic series, algorithm interview prep*

### 1️⃣ Problem Overview

LeetCode 2652 asks you to compute the sum of every integer between 1 and n (inclusive) that is a multiple of 3, 5, or 7.  
Constraints are tiny (`1 ≤ n ≤ 10^3`) – but a *single pass* over the range still shows you an efficient O(n) approach. The real interview‑style question is: can you do it *O(1)* with a mathematical trick?

> **Why it matters**: Interviewers love seeing you use *inclusion‑exclusion* and *arithmetic series* – it shows you’re comfortable with math and complexity analysis.

---

### 2️⃣ The Naïve Approach – O(n) Loop

```java
int sum = 0;
for (int i = 1; i <= n; i++) {
    if (i % 3 == 0 || i % 5 == 0 || i % 7 == 0) {
        sum += i;
    }
}
```

**Pros**  
- Simple to understand  
- Works for any n

**Cons**  
- Linear time → unnecessary for tiny n in real‑world interview scenarios  
- Doesn’t demonstrate deeper algorithmic thinking

> **Bottom line**: good for a quick solution, bad for a *real* coding interview.

---

### 3️⃣ The Elegant O(1) Math Solution

We use the formula for the sum of an arithmetic series:

\[
S_k = k + 2k + 3k + \dots + mk = k \times \frac{m(m+1)}{2}
\]
where `m = ⌊n/k⌋`.

**Inclusion–Exclusion Principle**

- Sum of multiples of 3 + multiples of 5 + multiples of 7  
- Subtract multiples of 15 (3×5), 21 (3×7), 35 (5×7) (since counted twice)  
- Add multiples of 105 (3×5×7) back (since subtracted three times)

**Formula**

```
S = sumMultiples(3) + sumMultiples(5) + sumMultiples(7)
    - sumMultiples(15) - sumMultiples(21) - sumMultiples(35)
    + sumMultiples(105)
```

Where `sumMultiples(k)` is computed in O(1) as:

```
m = n / k
return k * m * (m + 1) / 2
```

**Benefits**

- **Time**: O(1) – no loops  
- **Space**: O(1)  
- **Readability**: Shows mastery of math and algorithm design  
- **Performance**: Passes millions of test cases instantly

---

### 4️⃣ Code Snippets – Ready to Copy

#### 4.1 Java (O(1) Math)

```java
class Solution {
    public int sumOfMultiples(int n) {
        return (int) (sum(n, 3) + sum(n, 5) + sum(n, 7)
                    - sum(n, 15) - sum(n, 21) - sum(n, 35)
                    + sum(n, 105));
    }

    private long sum(int n, int k) {
        long m = n / k;
        return (long) k * m * (m + 1) / 2;
    }
}
```

#### 4.2 Python (O(1) Math)

```python
class Solution:
    def sumOfMultiples(self, n: int) -> int:
        def s(k: int) -> int:
            m = n // k
            return k * m * (m + 1) // 2

        return (
            s(3) + s(5) + s(7)
            - s(15) - s(21) - s(35)
            + s(105)
        )
```

#### 4.3 C++ (O(1) Math)

```cpp
class Solution {
public:
    int sumOfMultiples(int n) {
        auto sum = [&](int k) -> long long {
            long long m = n / k;
            return 1LL * k * m * (m + 1) / 2;
        };
        return static_cast<int>(
            sum(3) + sum(5) + sum(7)
            - sum(15) - sum(21) - sum(35)
            + sum(105)
        );
    }
};
```

> **Tip**: Use `long long` for intermediate products to avoid overflow, even though the final answer fits in `int`.

---

### 5️⃣ Edge Cases & Common Pitfalls

| Issue | Fix |
|-------|-----|
| Integer overflow in Java/C++ | Use `long`/`long long` for intermediate calculations |
| Forgetting inclusion–exclusion | Verify that each multiple set is added/subtracted correctly |
| Off‑by‑one errors (n inclusive) | Use `n / k` to count how many multiples exist up to `n` |
| Testing only small n | Run stress tests up to 10^9 to confirm formula works |

---

### 6️⃣ Complexity Breakdown

| Approach | Time | Space |
|----------|------|-------|
| O(n) Loop | **O(n)** | **O(1)** |
| O(1) Math | **O(1)** | **O(1)** |

> **Takeaway**: In interviews, prefer the O(1) solution – it shows you know how to optimize and use mathematics to reduce runtime.

---

### 7️⃣ Interview Takeaways

1. **Always ask for constraints** – they guide you to the right complexity.  
2. **Explain your thought process** – talk about inclusion–exclusion, arithmetic series, and why you chose O(1).  
3. **Mention edge cases** – overflow, large `n`, and how you guard against them.  
4. **Be ready to implement** – give clean, well‑commented code (see snippets above).  

---

### 8️⃣ Practice, Practice, Practice

- **LeetCode**: 2652 – Sum Multiples  
- **GeeksforGeeks**: “Arithmetic Series Sum” – refresh the formula  
- **HackerRank**: “Inclusion‑Exclusion” practice problems

---

### 9️⃣ Final Words

The Sum Multiples problem is a *classic* for learning how to turn a simple loop into a mathematically elegant solution. By mastering inclusion‑exclusion and arithmetic series, you’ll:

- Reduce time complexity from linear to constant  
- Show interviewers you think beyond brute force  
- Boost confidence for harder problems (e.g., counting coprimes, mobius function)

> **Pro tip**: When you finish, write a quick unit test suite (JUnit/pytest/GoogleTest) to cover edge cases. It shows professionalism and attention to detail.

---

## 📚 References & Further Reading

- LeetCode 2652 – [Sum Multiples](https://leetcode.com/problems/sum-multiples/)  
- GeeksforGeeks – [Arithmetic Progression Sum](https://www.geeksforgeeks.org/arithmetic-progressions/)  
- Brilliant.org – [Inclusion–Exclusion Principle](https://brilliant.org/wiki/inclusion-exclusion-principle/)  

---

## 🚀 Get Your Job

Want to land that software engineering role?  
- **Follow** our channel for more LeetCode walkthroughs  
- **Download** the free interview prep checklist  
- **Reach out** for personalized coaching

*Happy coding!* 🚀