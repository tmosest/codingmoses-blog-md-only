---
title: LeetCode 1191. K-Concatenation Maximum Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1191 – K‑Concatenation Maximum Sum  
**Published by [Your Name] | 2024-03‑30**

> *“You can repeat an array as many times as you wish – just keep the best slice.”*  

The problem is deceptively simple, yet it’s a favourite on coding‑interview platforms because it forces you to think about **Kadane’s algorithm** *and* a few subtle edge‑cases (negative sums, modulus, the empty slice).  
In this article you’ll find a full solution in **Java, Python, and C++** – all in a single go – followed by a short blog‑style write‑up that explains the problem, the intuition behind the solution, pitfalls and how to debug it.

---

## 1. The problem in a nutshell

> **Input** – an integer array `arr` (1 ≤ |arr| ≤ 10⁴) and an integer `k` (1 ≤ k ≤ 10⁴).  
> **Operation** – create a new array by concatenating `arr` to itself `k` times.  
> **Goal** – find the maximum possible sum of any *contiguous* sub‑array of that huge array.  
> **Output** – that maximum sum modulo 1 000 000 007.  
> If the maximum sum is negative, return `0` instead.

The naive way would be to actually build the length‑`k⋅n` array and run Kadane’s algorithm on it – but that would blow up memory and time for the maximum constraints.  
We need an O(n) solution that works in *constant additional memory*.

---

## 2. Key observations

| Situation | What matters |
|-----------|--------------|
| `k == 1` | Only the original array – standard Kadane’s algorithm (problem 53). |
| **Sum of `arr` is negative** | Repeating the array can only *worsen* the sum, so the best slice is inside the *first two* copies of the array. |
| **Sum of `arr` is non‑negative** | The best slice can start in the first copy and finish in the last copy. The middle copies contribute their *full* sum, i.e. `(k-2) * totalSum`. |

That gives us three distinct cases – exactly what the editorial and many top‑solutions use.

---

## 3. The algorithm

```
1. Compute totalSum = Σ arr[i] (as long).
2. Compute bestOne   = max sub‑array sum of arr (Kadane on arr).
3. If k == 1: answer = bestOne.
4. Else:
     a. Compute bestTwo = max sub‑array sum of the array repeated twice
        (Kadane on an array of length 2·n, but we avoid allocating it).
     b. If totalSum < 0:
            answer = bestTwo.
        Else:
            answer = bestTwo + (k-2) * totalSum.
5. answer = max(answer, 0).
6. return answer mod 1 000 000 007.
```

*Why step 4b works:*  
When `totalSum ≥ 0`, the best slice that uses the first and the last copy contributes the maximum prefix sum (`prefMax`) plus maximum suffix sum (`suffMax`).  
Adding `(k-2) * totalSum` accounts for the middle copies that fully contribute their sum.

---

## 4. Correctness proof sketch

We prove that the algorithm returns the correct maximum sum for every `arr` and `k`.

1. **Kadane’s algorithm** returns the maximum sub‑array sum for a *fixed* array.  
   Applying it to `arr` gives `bestOne`.  
   Applying it to a concatenation of `arr` twice gives `bestTwo`.  
   Both are *exact* because Kadane’s recurrence holds for any array length.

2. **Case `k == 1`**  
   The answer is exactly `bestOne`. No concatenation is possible.

3. **Case `totalSum < 0`**  
   Any slice that contains an *entire* copy of `arr` reduces the total sum because that copy is negative.  
   Therefore the maximum must lie inside the first two copies – exactly what `bestTwo` computes.  
   No longer slice can beat `bestTwo`.

4. **Case `totalSum ≥ 0`**  
   Consider any optimal slice in the `k`‑times concatenated array.  
   If it lies completely inside one copy, its sum ≤ `bestOne`.  
   If it spans more than one copy, we can separate it into:
   * prefix of the first copy (`prefMax`),
   * zero or more *whole* copies in the middle (each contributes `totalSum`),
   * suffix of the last copy (`suffMax`).

   The best prefix is exactly the best suffix of a single copy in the reverse direction, but that detail is captured by `bestTwo`.  
   Adding `(k-2) * totalSum` accounts for the middle copies.  
   Thus the overall maximum is `max(bestTwo + (k-2) * totalSum, bestOne)` which equals the algorithm’s output.

Since all possibilities are covered, the algorithm is correct.

---

## 5. Complexity analysis

| Operation | Time | Extra Space |
|-----------|------|-------------|
| Summation of `arr` | O(n) | O(1) |
| Kadane on `arr` | O(n) | O(1) |
| Kadane on two copies (without allocating) | O(n) | O(1) |
| Total | **O(n)** | **O(1)** |

With `n ≤ 10⁴`, this easily fits in the limits.

---

## 6. Reference implementations

Below you’ll find **ready‑to‑paste** solutions in the three most popular languages.

### 6.1 Java

```java
import java.util.*;

class Solution {
    private static final long MOD = 1_000_000_007L;

    public int kConcatenationMaxSum(int[] arr, int k) {
        if (k == 1) {
            return (int) Math.max(kadane(arr), 0L);
        }

        long totalSum = 0;
        for (int v : arr) totalSum += v;

        // Kadane on two copies (no allocation)
        long bestTwo = kadaneTwoCopies(arr);

        if (totalSum < 0) {
            return (int) Math.max(bestTwo, 0L);
        }

        long ans = bestTwo + ((k - 2) * totalSum);
        ans %= MOD;
        return (int) Math.max(ans, 0L);
    }

    private long kadane(int[] a) {
        long best = a[0];
        long cur  = a[0];
        for (int i = 1; i < a.length; i++) {
            cur = Math.max(a[i], cur + a[i]);
            best = Math.max(best, cur);
        }
        return best;
    }

    /** Kadane on arr concatenated twice (no new array). */
    private long kadaneTwoCopies(int[] a) {
        long best = a[0];
        long cur  = a[0];
        int n = a.length;
        for (int i = 1; i < 2 * n; i++) {
            int val = a[i % n];
            cur = Math.max(val, cur + val);
            best = Math.max(best, cur);
        }
        return best;
    }
}
```

### 6.2 Python

```python
MOD = 10**9 + 7

def kadane(a):
    best = cur = a[0]
    for x in a[1:]:
        cur = max(x, cur + x)
        best = max(best, cur)
    return best

def kadane_two_copies(a):
    """Kadane on a + a (no list construction)."""
    n = len(a)
    best = cur = a[0]
    for i in range(1, 2 * n):
        x = a[i % n]
        cur = max(x, cur + x)
        best = max(best, cur)
    return best

def k_concatenation_max_sum(arr, k):
    if k == 1:
        return kadane(arr) % MOD

    total = sum(arr)

    best_two = kadane_two_copies(arr)

    if total < 0:
        return max(best_two, 0) % MOD

    ans = (best_two + (k - 2) * total) % MOD
    return max(ans, 0)

# ---- example usage ----
if __name__ == "__main__":
    print(k_concatenation_max_sum([1, 2], 3))   # 9
    print(k_concatenation_max_sum([1, -2, 1], 3)) # 2
```

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
private:
    static constexpr long long MOD = 1'000'000'007LL;

    long long kadane(const vector<int>& a) {
        long long best = a[0];
        long long cur  = a[0];
        for (size_t i = 1; i < a.size(); ++i) {
            cur = max<long long>(a[i], cur + a[i]);
            best = max(best, cur);
        }
        return best;
    }

    /** Kadane on the array repeated twice (no allocation). */
    long long kadaneTwoCopies(const vector<int>& a) {
        long long best = a[0];
        long long cur  = a[0];
        int n = static_cast<int>(a.size());
        for (int i = 1; i < 2 * n; ++i) {
            int val = a[i % n];
            cur = max<long long>(val, cur + val);
            best = max(best, cur);
        }
        return best;
    }

public:
    int kConcatenationMaxSum(vector<int>& arr, int k) {
        if (k == 1) return static_cast<int>(max(kadane(arr), 0LL));

        long long totalSum = 0;
        for (int v : arr) totalSum += v;

        long long bestTwo = kadaneTwoCopies(arr);

        if (totalSum < 0) return static_cast<int>(max(bestTwo, 0LL));

        long long ans = bestTwo + ((long long)(k - 2) * totalSum);
        ans %= MOD;
        return static_cast<int>(max(ans, 0LL));
    }
};
```

---

## 7. Common pitfalls & how to avoid them

| Pitfall | Why it happens | Fix |
|----------|----------------|-----|
| **Using int for intermediate sums** | `arr` can contain up to 10⁴ elements, each up to 10⁴, so the worst‑case sum is ~10⁸. With `k` up to 10⁴, the raw answer can overflow a 32‑bit int. | Use `long`/`long long` everywhere that stores a sum. |
| **Taking `MOD` too early** | If you do `ans %= MOD` *before* adding the middle copies, you lose the ability to compare against `bestOne`. | Compute the full *un‑modded* answer first, then `ans % MOD`. |
| **Ignoring the empty slice** | Kadane returns a negative number if every element is negative. The problem specifies to return `0` in that situation. | Finally `return max(answer, 0) % MOD`. |
| **Allocating the k‑times array** | For `k == 10⁴` and `n == 10⁴` you’d need 10⁸ integers → 400 MB – far beyond the judge’s limits. | Run Kadane over *two* logical copies without actually allocating them. |
| **Modulo sign issue** | In Java/C++/Python, `%` can return a negative number if the dividend is negative. | After taking the modulo, do `if (ans < 0) ans += MOD;`. (All reference solutions already do `ans %= MOD;`). |

---

## 8. Quick sanity check

| Input | Expected output |
|-------|-----------------|
| `arr=[1,2]`, `k=3` | 9 |
| `arr=[1,-2,1]`, `k=3` | 2 |
| `arr=[-1,-2]`, `k=5` | 0 |
| `arr=[-1,3,-4,2]`, `k=1` | 3 |

Feel free to copy the code into LeetCode, CodeSignal or any IDE that supports the language of your choice.

---

## 9. Take‑away

The “repeat‑array‑to‑maximum‑sum” problem is a neat exercise in *optimising a classic algorithm*.  
The crux is to realise that **only the first two copies can contain the optimal slice** if the total sum is negative; otherwise, the middle copies simply add their full sum.  
A clean implementation boils down to *just two* runs of Kadane’s algorithm and a few arithmetic lines.

Happy coding! 🚀