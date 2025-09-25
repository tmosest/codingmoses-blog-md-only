---
title: LeetCode 3345. Smallest Divisible Digit Product I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 3345 – “Smallest Divisible Digit Product I”  
### A Deep‑Dive: The Good, the Bad, and the Ugly  
> **Keywords:** LeetCode 3345, coding interview, algorithm design, Java solution, Python solution, C++ solution, job interview, interview prep, algorithmic thinking, code snippets, time‑space complexity

---

### 1️⃣ Problem Overview

> **Given:** two integers `n` and `t`.  
> **Return:** the smallest integer `x ≥ n` whose *product of digits* is divisible by `t`.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `n = 10`, `t = 2` | `10` | `1×0 = 0`, divisible by 2. |
| 2 | `n = 15`, `t = 3` | `16` | `1×6 = 6`, divisible by 3. |

**Constraints**  

```
1 ≤ n ≤ 100
1 ≤ t ≤ 10
```

> The small limits allow a brute‑force scan, but we’ll explore a clever observation that guarantees a solution within `[n, n+10]`.  

---

### 2️⃣ The Insight: Why `[n, n+10]` is Enough

* For any `t ≤ 10`, the worst‑case scenario is when `t` is a prime (e.g., 7).  
* Among any 11 consecutive integers there will be at least one that contains a zero **or** a digit that is a multiple of `t`.  
* A digit product that includes a zero is automatically `0`, which is divisible by every `t`.  
* If a digit product is `≥ t`, we can test divisibility directly.

This guarantees that a solution is guaranteed within 11 numbers. So the algorithm can simply brute‑force that tiny window – a perfect 2‑line solution in Python, and a 10‑line Java/C++ version.

---

### 3️⃣ The “Good”: Clean & Elegant Code

| Language | Implementation |
|----------|----------------|
| **Python** | `2` lines |
| **Java** | `≈10` lines |
| **C++** | `≈10` lines |

We’ll walk through each in detail.

---

#### 3.1 Python (2‑line Brute Force)

```python
class Solution:
    def smallestNumber(self, n: int, t: int) -> int:
        return next(i for i in range(n, n + 11) if (prod := (lambda s: eval('*'.join(s)))(''.join(map(str, i))) ) % t == 0)
```

*Explanation:*  
- `range(n, n+11)` covers `[n, n+10]`.  
- `prod` computes the digit product.  
- The generator stops at the first valid number.

---

#### 3.2 Java (Clear & Readable)

```java
import java.util.function.IntUnaryOperator;

class Solution {
    private static int digitProduct(int x) {
        int prod = 1;
        while (x > 0) {
            prod *= x % 10;
            x /= 10;
        }
        return prod;
    }

    public int smallestNumber(int n, int t) {
        for (int i = n; i <= n + 10; i++) {
            if (digitProduct(i) % t == 0) return i;
        }
        return -1; // never reached
    }
}
```

---

#### 3.3 C++ (Fast & Idiomatic)

```cpp
class Solution {
    int digitProduct(int x) {
        int prod = 1;
        while (x) {
            prod *= x % 10;
            x /= 10;
        }
        return prod;
    }
public:
    int smallestNumber(int n, int t) {
        for (int i = n; i <= n + 10; ++i)
            if (digitProduct(i) % t == 0) return i;
        return -1;   // unreachable with given constraints
    }
};
```

---

### 4️⃣ The “Bad”: Things That Can Go Wrong

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Missing the zero‑product shortcut** | If you forget that a zero digit yields product 0, you may miss a valid answer early. | Handle the zero case in `digitProduct`: `if (x % 10 == 0) return 0;`. |
| **Using floating point or string `eval`** | In Python, `eval('*'.join(...))` is risky and slower. | Use `reduce` or a simple loop to multiply digits. |
| **Wrong loop bounds** | Off‑by‑one errors (`n+9` instead of `n+10`) can miss the solution. | Use `<= n + 10` or `range(n, n + 11)` in Python. |
| **Large `n` mis‑handled** | If the constraints change, brute‑force 11 numbers may fail. | Keep the algorithm generic by iterating until a solution is found, but be aware of the O(1) bound only holds for the given limits. |

---

### 5️⃣ The “Ugly”: Inefficient or Obscure Workarounds

1. **Iterating over all numbers up to 10⁶** – unnecessary for the stated constraints and leads to O(n) time.  
2. **Using recursion or DP** where the problem is trivially linear.  
3. **Hard‑coding answers** – not a reusable solution and a red flag in interviews.  

---

### 6️⃣ Complexity Analysis

| Algorithm | Time | Space |
|-----------|------|-------|
| Python 2‑line | **O(1)** (at most 11 iterations, each O(d) where d = number of digits) | **O(1)** |
| Java / C++ | **O(1)** | **O(1)** |

> **d** is tiny (≤ 3 for `n ≤ 100`), so the solution is effectively constant‑time.

---

### 7️⃣ Edge‑Case Checklist

| Case | Reason | Expected Behavior |
|------|--------|-------------------|
| `t = 1` | Any product is divisible by 1 | Return `n` immediately |
| `n` contains a `0` | Product is 0 | Return `n` |
| `t` is a prime (e.g., 7) | Need to find a digit multiple of 7 or zero | The 11‑number window guarantees success |
| `n = 100` | Upper bound of constraints | Should return `100` because `1×0×0 = 0` |

---

### 8️⃣ Testing Strategy

```python
assert Solution().smallestNumber(10, 2) == 10
assert Solution().smallestNumber(15, 3) == 16
assert Solution().smallestNumber(98, 7) == 98  # 9×8=72 divisible by 7? No -> next
assert Solution().smallestNumber(98, 7) == 105  # 1×0×5 = 0 divisible
```

*Always test the boundary `n = 100` and `t = 10`.*

---

### 9️⃣ Takeaways for Your Interview

1. **Leverage Problem Constraints** – small limits let you brute‑force elegantly.  
2. **Identify a Theorem/Observation** – the `[n, n+10]` guarantee turns an O(n) into O(1).  
3. **Write Readable Code** – clear loops, helper functions, and comments impress interviewers.  
4. **Handle Edge Cases Explicitly** – zero products, divisibility by 1, etc.  
5. **Explain Complexity Upfront** – talk about why the solution is efficient.  

---

### 🔑 SEO‑Ready Blog Conclusion

> If you’re preparing for coding interviews, mastering problems like **LeetCode 3345 – Smallest Divisible Digit Product I** shows you can turn constraints into an optimization. The trick of searching only `[n, n+10]` turns an otherwise trivial brute‑force into a near‑constant‑time solution.  
>  
> Practice this pattern with other “small” problems on LeetCode. It builds a habit of spotting hidden guarantees and writing clean, efficient code—exactly the qualities recruiters look for in a junior to mid‑level software engineer.  
>  
> **Next Steps:**  
> - Dive into more “Easy” LeetCode problems and identify similar bounds.  
> - Add these solutions to your GitHub portfolio; link them in your résumé.  
> - Use the code snippets as interview talking points to demonstrate algorithmic thinking.  

> **Ready to land your next role?** Share this post, comment below with your own solutions, or reach out for a deeper discussion on interview strategies!  

---  

### 📌 Quick Reference Code Snippets

```python
# Python
class Solution:
    def smallestNumber(self, n: int, t: int) -> int:
        return next(i for i in range(n, n+11) if (lambda x: (lambda p=1: eval('*'.join(str(x%10) for _ in range(len(str(x))))))(i)) % t == 0)
```

```java
// Java
class Solution {
    private int digitProduct(int x) { ... }
    public int smallestNumber(int n, int t) { ... }
}
```

```cpp
// C++
class Solution {
    int digitProduct(int x) { ... }
public:
    int smallestNumber(int n, int t) { ... }
};
```

Happy coding and best of luck with your job search!