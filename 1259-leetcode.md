---
title: LeetCode 1259. Handshakes That Don't Cross - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1️⃣  Handshakes That Don’t Cross – 1259  
### 🔍 Problem Summary (LeetCode)

> You are given an even number of people `numPeople` standing in a circle.  
> Each person shakes hands with exactly one other person, so there are `numPeople/2` handshakes in total.  
> Count the number of distinct ways to perform the handshakes **without any two handshakes crossing**.  
> Return the answer modulo **1 000 000 007**.

**Constraints**

| numPeople | min | max | parity |
|-----------|-----|-----|--------|
| 2 ≤ numPeople ≤ 1000 | 2 | 1000 | even |

The example  
- `numPeople = 4 → 2`  
- `numPeople = 6 → 5`

---

## 2️⃣  The Math Behind the Answer – Catalan Numbers

If you draw the people around a circle and connect handshake pairs with straight lines, the non‑crossing condition means that the lines form a **non‑crossing perfect matching** on a convex polygon.  

The number of such matchings for a convex polygon with `2k` vertices is exactly the **k‑th Catalan number**:

\[
C_k = \frac{1}{k+1}\binom{2k}{k} = \sum_{i=0}^{k-1} C_i \cdot C_{k-1-i}
\]

So for the problem we only need  
\[
\text{answer} = C_{\,numPeople/2} \pmod{1\,000\,000\,007}
\]

---

## 3️⃣  Dynamic Programming Solution (O(n²))

Instead of computing binomial coefficients and modular inverses (which is also fine), we can build the Catalan numbers with a classic DP recurrence:

```
dp[0] = 1                      // empty polygon
dp[2] = 1                      // one handshake
for i from 4 to n step 2:
    dp[i] = 0
    for j from 2 to i-2 step 2:
        dp[i] += dp[j] * dp[i-j] (mod M)
```

**Why it works**  
- Pair the first person with someone at position `j`.  
- The people between them form a smaller polygon of `j` vertices,  
  the rest form a polygon of `i-j` vertices.  
- Multiply the number of ways for both parts and sum over all valid `j`.

**Complexity**

| Operation | Time | Space |
|-----------|------|-------|
| Outer loop | \(O(n/2)\) | \(O(n)\) |
| Inner loop | \(O(n/2)\) | – |
| Total | \(O(n^2)\) | \(O(n)\) |

With `n ≤ 1000`, `O(n²)` (≈ 500 000 iterations) is trivial.

---

## 4️⃣  Reference Implementations

Below are clean, ready‑to‑copy implementations in **Java**, **Python**, and **C++**.  
All are `O(n²)` and handle the modulo correctly.

> **Tip:**  
> In Java and C++ use `long long`/`int64_t` during multiplication to avoid overflow.  
> In Python `int` is arbitrary precision, so it’s simpler.

---

### 4.1 Java

```java
import java.io.*;
import java.util.*;

class Solution {
    private static final long MOD = 1_000_000_007L;

    public int numberOfWays(int n) {
        // n is guaranteed to be even
        long[] dp = new long[n + 1];
        dp[0] = 1;
        dp[2] = 1;

        for (int i = 4; i <= n; i += 2) {
            long sum = 0;
            for (int j = 2; j <= i - 2; j += 2) {
                sum = (sum + dp[j] * dp[i - j]) % MOD;
            }
            dp[i] = sum;
        }
        return (int) dp[n];
    }
}
```

**How to run (quick test):**

```java
public class Main {
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.numberOfWays(4)); // 2
        System.out.println(s.numberOfWays(6)); // 5
    }
}
```

---

### 4.2 Python

```python
MOD = 1_000_000_007

def number_of_ways(n: int) -> int:
    # n is even
    dp = [0] * (n + 1)
    dp[0] = 1
    dp[2] = 1

    for i in range(4, n + 1, 2):
        total = 0
        for j in range(2, i, 2):
            total = (total + dp[j] * dp[i - j]) % MOD
        dp[i] = total

    return dp[n]


if __name__ == "__main__":
    print(number_of_ways(4))  # 2
    print(number_of_ways(6))  # 5
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1'000'000'007LL;

int numberOfWays(int n) {
    vector<long long> dp(n + 1, 0);
    dp[0] = 1;
    dp[2] = 1;

    for (int i = 4; i <= n; i += 2) {
        long long total = 0;
        for (int j = 2; j <= i - 2; j += 2) {
            total = (total + dp[j] * dp[i - j]) % MOD;
        }
        dp[i] = total;
    }
    return static_cast<int>(dp[n]);
}

int main() {
    cout << numberOfWays(4) << endl; // 2
    cout << numberOfWays(6) << endl; // 5
}
```

---

## 5️⃣  Blog Post: “Handshakes That Don’t Cross – The Good, The Bad, and the Ugly”

---

### 5.1  Headline & Meta‑Description (SEO‑Ready)

> **Title:** Handshakes That Don’t Cross – A Simple DP to Ace LeetCode 1259  
> **Meta‑Description:** Learn the math behind the non‑crossing handshake problem, see O(n²) Java/Python/C++ solutions, and get interview‑ready with clear code snippets. Perfect for your next technical interview!

---

### 5.2  Introduction

> Picture a group of friends standing in a circle, each one shaking hands with exactly one other. How many ways can they do this without any of the handshakes crossing? This deceptively simple question is a classic LeetCode hard problem (#1259) and a great interview pitfall.  
> In this article, we’ll dissect the problem, explain why the answer is a Catalan number, show a clean DP implementation, and give you three ready‑to‑copy solutions in **Java**, **Python**, and **C++**. Along the way, we’ll talk about the good, the bad, and the ugly of different approaches so you can pick the right one for your interview or coding challenge.

---

### 5.3  The Good – Why DP Is a Natural Fit

- **Linear Recurrence** – Each handshake pair splits the circle into two independent sub‑circles.  
- **Avoids Combinatorics Pitfall** – You can’t just count all handshakes and subtract crossings; the combinatorial structure is non‑trivial.  
- **Time‑Efficient** – `O(n²)` is easily fast for `n ≤ 1000`.  
- **Modular Arithmetic Friendly** – Works with a single modulus (`1 000 000 007`).

---

### 5.4  The Bad – Common Missteps

| Mistake | Why It Fails |
|---------|--------------|
| **Using factorials without modular inverses** | You’ll overflow and need to compute modular inverses, which adds unnecessary complexity. |
| **Assuming all pairings are valid** | Ignoring the “non‑crossing” constraint leads to astronomically many invalid configurations. |
| **O(n³) brute force** | Even with pruning, 1000 people gives ~10⁹ iterations—impossible in time. |
| **Using `int` for multiplication in Java/C++** | `dp[j] * dp[i-j]` can exceed 32‑bit range; use `long long` / `long`. |

---

### 5.5  The Ugly – Why “Catalan Numbers” Might Be Overkill

- **Binomial Coefficient Complexity** – Computing \(C_k\) via \(\frac{1}{k+1}\binom{2k}{k}\) requires modular inverses and factorial pre‑computation.  
- **Potential Off‑By‑One Errors** – Handling `k` vs. `k-1` indices can trip you up.  
- **Less Intuitive for Interviewers** – Some interviewers may prefer seeing the DP logic unfold rather than a one‑liner formula.

---

### 5.6  Final Takeaway

A concise DP that mirrors the combinatorial structure of the problem is the safest, fastest, and most interview‑friendly solution. It avoids pitfalls, stays within time limits, and clearly demonstrates your understanding of recursion and modular arithmetic.

---

### 5.7  Bonus – Performance Tips

- **Pre‑allocate arrays** once; no repeated memory allocation in loops.  
- **Use `long long`** (C++), `long` (Java), or built‑in `int` (Python) to avoid overflow.  
- **Mod after each addition/multiplication** to keep numbers small.  
- **If you’re up for a challenge**: compute Catalan numbers in `O(k)` using a single loop with the formula \(C_{k+1} = \frac{2(2k+1)}{k+2}C_k\) and modular inverses; this is a great interview “extra credit” move.

---

## 6️⃣  Closing Thoughts

> The “Handshakes That Don’t Cross” problem is a perfect blend of combinatorics, recursion, and modular arithmetic. Mastering it not only gives you a clean O(n²) solution but also demonstrates a deep understanding of how a seemingly simple puzzle hides a classic Catalan structure.  
> Keep the code snippets handy, practice explaining the DP reasoning, and you’ll be ready to impress your next recruiter or ace the coding interview. Happy coding!

---