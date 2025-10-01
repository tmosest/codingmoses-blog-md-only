---
title: LeetCode 2927. Distribute Candies Among Children III - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

> **Distribute Candies Among Children III**  
>  You’re given two positive integers `n` and `limit`.  
>  Count the number of ways to give `n` candies to **three** children so that
>  *every* child receives at most `limit` candies.  
>  Order matters – the triplet `(a, b, c)` is different from `(b, a, c)`.

**Examples**

| n | limit | answer | explanation |
|---|-------|--------|-------------|
| 5 | 2 | 3 | (1,2,2), (2,1,2), (2,2,1) |
| 3 | 3 | 10 | All 10 non‑negative triples with sum 3 |

`n, limit ≤ 10⁸`, so a naïve `O(n²)` enumeration would be far too slow.

--------------------------------------------------------------------

## 2.  Intuition – inclusion–exclusion + stars & bars

Without the upper bound the number of non‑negative integer solutions of

```
a + b + c = n
```

is the classic *stars & bars* result  

```
C(n + 2, 2) = (n+2)(n+1)/2
```

We must **remove** all solutions where at least one child gets more than `limit` candies.  
That is a textbook inclusion–exclusion (PIE) problem.

### 2.1  One child too many

Suppose `a > limit`.  Set  

```
a' = a - (limit + 1)   ≥ 0
```

Now  

```
a' + b + c = n - (limit + 1)
```

The number of solutions is  

```
C(n - limit + 1, 2)
```

Exactly the same count for `b > limit` and for `c > limit`.  
So we subtract `3 · C(n - limit + 1, 2)`.

### 2.2  Two children too many

For `a > limit` **and** `b > limit` set

```
a' = a - (limit + 1)
b' = b - (limit + 1)
```

```
a' + b' + c = n - 2(limit + 1)
```

Count: `C(n - 2*limit - 1, 2)`  
There are `C(3,2) = 3` such pairs, so we **add back** `3 · C(n - 2*limit - 1, 2)`.

### 2.3  All three children too many

If all three exceed the limit:

```
a' + b' + c' = n - 3(limit + 1)
```

Count: `C(n - 3*limit - 1, 2)`  
Subtract this once more.

### 2.4  Final formula

Let  

```
C2(x) = 0                    if x < 2
        x*(x-1)/2            otherwise
```

Then the answer is

```
C(n+2, 2)
- 3 * C2(n - limit + 1)
+ 3 * C2(n - 2*limit - 1)
-   C2(n - 3*limit - 1)
```

Every term that would involve a negative binomial is automatically zero by `C2`.  
The whole computation is **O(1)** time and **O(1)** memory.

--------------------------------------------------------------------

## 3.  Reference Implementations

Below are ready‑to‑paste solutions for **Java, Python, and C++**.

> **Tip:** All three use 64‑bit integers (`long` / `long long`) – the result can be as large as ~10¹⁶ for the maximum input, so 32‑bit would overflow.

### 3.1  Python 3

```python
class Solution:
    def distributeCandies(self, n: int, limit: int) -> int:
        """O(1) combinatorial solution"""

        def C2(x: int) -> int:
            """C(x, 2) but zero when x < 2."""
            return (x * (x - 1) // 2) if x >= 2 else 0

        total = C2(n + 2)                # C(n+2, 2)
        t1 = C2(n - limit + 1)           # one child too many
        t2 = C2(n - 2 * limit - 1)       # two children too many
        t3 = C2(n - 3 * limit - 1)       # all three too many

        return total - 3 * t1 + 3 * t2 - t3
```

### 3.2  Java (Java 17+)

```java
class Solution {
    public long distributeCandies(int n, int limit) {
        return solve((long) n, (long) limit);
    }

    private long solve(long n, long limit) {
        long total = comb2(n + 2);           // C(n+2, 2)
        long t1 = comb2(n - limit + 1);      // one child too many
        long t2 = comb2(n - 2 * limit - 1);  // two children too many
        long t3 = comb2(n - 3 * limit - 1);  // all three too many

        return total - 3 * t1 + 3 * t2 - t3;
    }

    /** C(x, 2) but zero when x < 2. */
    private long comb2(long x) {
        return x < 2 ? 0 : (x * (x - 1)) / 2;
    }
}
```

### 3.3  C++17

```cpp
class Solution {
public:
    long long distributeCandies(int n, int limit) {
        return solve(static_cast<long long>(n),
                     static_cast<long long>(limit));
    }

private:
    long long solve(long long n, long long limit) {
        long long total = comb2(n + 2);          // C(n+2, 2)
        long long t1 = comb2(n - limit + 1);    // one child too many
        long long t2 = comb2(n - 2 * limit - 1);// two children too many
        long long t3 = comb2(n - 3 * limit - 1);// all three too many

        return total - 3 * t1 + 3 * t2 - t3;
    }

    /** C(x, 2) but zero when x < 2. */
    long long comb2(long long x) {
        return (x < 2) ? 0 : (x * (x - 1)) / 2;
    }
};
```

--------------------------------------------------------------------

## 4.  Blog Post – “The Good, the Bad, and the Ugly of Distributing Candies”

> **Keywords:** LeetCode 2927, Distribute Candies Among Children, O(1) solution, inclusion–exclusion, stars and bars, interview tips

---

### 4.1  Why This Problem Is Worthy of a Post

1. **Real‑world feel** – “distribute candies” is a story‑based, tangible problem that hides a deeper combinatorics challenge.
2. **Hard rating** – many candidates skip it because the constraints (10⁸) make a brute‑force loop impossible.
3. **A perfect showcase** of *inclusion–exclusion* + *stars & bars*, two techniques every algorithm‑interviewee should master.

---

### 4.2  The “Good” – What Makes It Elegant

- **Closed‑form formula**: Once you set up the PIE, the answer is a single arithmetic expression.  
  **No loops, no recursion** – perfect for an O(1) interview question.
- **Linear algebra**: You can see the problem as counting lattice points inside a bounded tetrahedron.  
  The inclusion–exclusion is just “cutting off” the corners that exceed the limit.
- **Scalable**: Works for any number of children `k` by generalizing to `C(n+k-1, k-1)` and then correcting for the limit.

---

### 4.3  The “Bad” – Common Pitfalls

| Pitfall | Why it happens | How to avoid |
|---------|----------------|--------------|
| Forgetting the **+1** in `limit+1` | You’re subtracting the wrong number of candies when a child exceeds the limit. | Think in terms of “minimum candies the child must take over the limit” – that minimum is `limit+1`. |
| Mis‑calculating `C2(x)` for negative `x` | Many people write `x*(x-1)//2` blindly, which gives a huge negative number for `x<2`. | Guard with an `if (x < 2) return 0;`. |
| Using 32‑bit integers | For `n=10⁸`, `C(n+2,2)` ≈ 5·10¹⁵, which overflows `int`. | Always use 64‑bit (`long`/`long long`/Python’s `int`). |

---

### 4.4  The “Ugly” – Hidden Edge Cases

| Edge Case | Why it’s tricky | What the formula does |
|-----------|-----------------|-----------------------|
| `limit` larger than `n` | All distributions are valid; the answer is just `C(n+2,2)`. | The terms with `n - limit + 1` become negative → `C2=0`. |
| `n` extremely large, `limit` small | The number of “bad” solutions is huge, but the formula subtracts them correctly. | Each `C2` call automatically turns negative arguments to 0. |
| `limit = 0` | Only the triplet `(0,0,0)` is possible. | `n` must also be 0 for any solution; the formula returns 1 if `n==0`, else 0. |

---

### 4.5  Step‑by‑Step Derivation (for the reader)

1. **Stars & Bars** – Without limits:  
   `a+b+c=n` → `C(n+2,2)` lattice points.
2. **One child > limit** – Reduce the problem to `n - (limit+1)` candies.  
   Count: `C(n - limit + 1, 2)`.
3. **Two children > limit** – Subtract `2(limit+1)` candies.  
   Count: `C(n - 2*limit - 1, 2)`.
4. **All three > limit** – Subtract `3(limit+1)` candies.  
   Count: `C(n - 3*limit - 1, 2)`.
5. **Apply PIE** – Sum/​Subtract as shown in the table.

---

### 4.6  Final Takeaway

*If you can write the answer as an algebraic expression, you’ll impress the interviewer with two things at once:*

1. *You understand the math behind combinatorics.*
2. *You can implement it in O(1) time, which is a rare skill.*

---

### 4.7  Interview‑Ready Practice

- **Ask the interviewer**: “Can we extend this to 4 kids?”  
  You’ll explain that the same logic applies, just with `C(n+3,3)` and correcting for the limit.
- **Show the code**: Run your Python solution locally with a few custom cases (especially the edge ones) to prove it’s bug‑free.
- **Explain the guard** on negative `C2` – it’s a small but critical detail.

---

### 4.8  Closing

LeetCode 2927 is a micro‑saga of combinatorial reasoning.  
When you can solve it in a single line, you’re ready for the next *Hard* question.  
Remember: **the best interview answer is a correct, concise, and clearly explained solution** – that’s exactly what the PIE + stars & bars formula gives you.

---

**Happy coding, and good luck on your next interview!**

--------------------------------------------------------------------

## 5.  Final Thoughts

- The O(1) approach is the *official* reference; most solutions in the community use the same PIE formula.
- Mastering this pattern will let you tackle a whole family of *bounded counting* problems.
- Share the reference code with a friend – it’s a great pair‑coding exercise.

Happy interview hunting! 🚀

---