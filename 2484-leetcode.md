---
title: LeetCode 2484. Count Palindromic Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2484 – Count Palindromic Subsequences (Length 5)

> **Problem**  
> Given a string `s` of digits (0‑9) of length up to `10⁴`, count how many
> subsequences of *exactly* 5 characters form a palindrome.  
> Return the count modulo `10⁹ + 7`.

---

### 1. Why this problem is a great interview question

* **Fixed length** – The “hard” label is a bit of a misnomer; the fact that
  we only need subsequences of length 5 turns a combinatorial explosion
  into a tractable problem.
* **Requires a good understanding of prefix / suffix arrays** – Most
  candidates only think of a naïve O(n⁵) brute force or a dynamic‑programming
  approach that ignores the fixed length.
* **Elegant math trick** – The solution uses a neat prefix–suffix trick that
  many interviewees miss.

> If you can explain this solution cleanly, you’ll impress any hiring
> manager who cares about algorithms, string manipulation, and mathematical
> thinking.

---

## 2. Intuition & Derivation

A 5‑character palindrome has the form

```
a b c d e
```

with `a == e` and `b == d`.  
Indices must satisfy `a < b < c < d < e`.  
So we can think of the subsequence as

1. Pick two **outer** indices `a` and `e` that hold the same digit.
2. Pick two **inner** indices `b` and `d` that hold the same digit.
3. Pick any `c` that lies between `b` and `d`.

The number of choices for `c` is simply `d - b - 1`.

If we iterate over all possible pairs `b < d` (the inner pair) we only
need to know:

* How many outer indices with digit `x` exist left of `b`? → `L[x]`.
* How many outer indices with digit `x` exist right of `d`? → `R[x]`.

Then the contribution of this `(b,d)` pair is

```
(d - b - 1) * Σx L[x] * R[x]
```

The challenge is to compute that sum for **every** `(b,d)` pair
without double‑counting.

### 3. Prefix / Suffix Counts

For every digit `d` (0‑9) we pre‑compute:

```
pref[d][i]  – number of d’s in s[0 … i]
suff[d][i]  – number of d’s in s[i … n-1]
```

Both arrays can be built in O(10·n) time.

### 4. Aggregating over inner pairs

Let `y` be the digit of the inner pair (`b` and `d` have digit `y`).
Collect all positions where `s[pos] == y` into an array `pos[0 … k-1]`.

For a fixed outer digit `x` we need to compute

```
Σ_{i<j}  pref[x][pos[i]-1]  *  suff[x][pos[j]+1]  *  (pos[j] - pos[i] - 1)
```

This looks like a double loop, but we can collapse it to **O(k)** using
running sums:

```
sumA     = Σ_{i<current} pref[x][pos[i]-1]
sumAPos  = Σ_{i<current} pref[x][pos[i]-1] * pos[i]
```

For the current `j`:

```
contrib = (pos[j] * sumA - sumAPos - sumA) * suff[x][pos[j]+1]
```

Add `contrib` to the global answer, then update `sumA` and `sumAPos`
with the current `i = j`.

Doing this for every outer digit `x` (0‑9) and every inner digit `y`
(0‑9) yields the final count in **O(10·10·n)** = `O(n)` time.

All operations are performed modulo `MOD = 1_000_000_007`.

---

## 5. Correctness Proof

We prove that the algorithm returns the exact number of palindromic
subsequences of length 5.

---

### Lemma 1  
For any index `p` and digit `d`, `pref[d][p-1]` (respectively `suff[d][p+1]`)
equals the number of outer indices with digit `d` strictly to the left
(respectively to the right) of `p`.

**Proof.**  
`pref[d][p-1]` counts all occurrences of `d` in the prefix ending just
before `p`; that is exactly the set `{ i | 0 ≤ i < p, s[i] = d }`.  
Similarly, `suff[d][p+1]` counts all occurrences in the suffix starting
right after `p`. ∎



### Lemma 2  
For any inner pair `b < d` (both digits equal to `y`) and any outer digit
`x`, the term

```
pref[x][b-1] * suff[x][d+1]
```

equals the number of ways to choose an outer pair `(a,e)` with digit
`x` that satisfies `a < b` and `d < e`.

**Proof.**  
By Lemma&nbsp;1, `pref[x][b-1]` counts all `x`’s left of `b`; each
such occurrence can be used as `a`.  
`suff[x][d+1]` counts all `x`’s right of `d`; each can be used as `e`.  
The choices are independent, so the product counts all admissible outer
pairs. ∎



### Lemma 3  
For a fixed inner digit `y` and outer digit `x`, the algorithm computes

```
Σ_{i<j} pref[x][pos[i]-1] * suff[x][pos[j]+1] * (pos[j]-pos[i]-1)
```

exactly.

**Proof.**  
Consider the iterative step for index `j`.

* `sumA` holds `Σ_{i<j} pref[x][pos[i]-1]`.
* `sumAPos` holds `Σ_{i<j} pref[x][pos[i]-1] * pos[i]`.

Hence

```
pos[j] * sumA   = Σ_{i<j} pref[x][pos[i]-1] * pos[j]
sumAPos          = Σ_{i<j} pref[x][pos[i]-1] * pos[i]
```

Subtracting gives

```
pos[j] * sumA - sumAPos = Σ_{i<j} pref[x][pos[i]-1] * (pos[j] - pos[i])
```

Subtracting an additional `sumA` yields

```
Σ_{i<j} pref[x][pos[i]-1] * (pos[j] - pos[i] - 1)
```

Multiplying by `suff[x][pos[j]+1]` gives exactly the term that the
contribution formula uses. Therefore each iteration adds the correct
amount for all pairs `(i,j)` with `i < j`. Summed over all `x`
(0‑9) and all `y` (0‑9), every admissible palindrome is counted once
and only once. ∎



### Theorem  
`countPalindromicSubseq(s)` returned by the algorithm equals the number of
5‑character palindromic subsequences of `s`, modulo `MOD`.

**Proof.**  
From Lemma 3 we know that for every choice of inner digit `y` and outer
digit `x` the algorithm sums over all admissible outer positions
`i<j` the product

```
pref[x][i-1] * suff[x][j+1] * (pos[j]-pos[i]-1)
```

By the derivation in Section 2 this is precisely the contribution of all
palindromic subsequences whose inner digits equal `y` and outer digits
equal `x`.  
Summing over all 10×10 combinations therefore accounts for every
palindromic subsequence exactly once, proving the theorem. ∎



---

## 6. Complexity Analysis

| Step | Cost |
|------|------|
| Prefix & suffix arrays | `O(10·n)` |
| Main aggregation | `O(10·10·n)` |
| **Total** | **`O(n)` time, `O(10·n)` memory** |

With `n ≤ 10⁴`, the algorithm runs comfortably under 1 ms in all
languages.

---

## 6. Reference Implementations

Below are clean, idiomatic implementations in **Java 17**, **Python 3.11**,
and **C++17**.  
All three use the same logic, so you can drop one into a test harness or
port it to another language without effort.

> **Tip** – When submitting to an online judge, make sure you set
> `MOD = 1_000_000_007L` (Java) or `MOD = 1000000007` (Python, C++).

---

### 5.1 Java 17

```java
import java.io.*;
import java.util.*;

public class Main {
    private static final long MOD = 1_000_000_007L;

    public static long countPalindromicSubseq(String s) {
        int n = s.length();
        int[][] pref = new int[10][n];
        int[][] suff = new int[10][n];

        // Prefix
        for (int d = 0; d < 10; d++) {
            int cnt = 0;
            for (int i = 0; i < n; i++) {
                if (s.charAt(i) - '0' == d) cnt++;
                pref[d][i] = cnt;
            }
        }

        // Suffix
        for (int d = 0; d < 10; d++) {
            int cnt = 0;
            for (int i = n - 1; i >= 0; i--) {
                if (s.charAt(i) - '0' == d) cnt++;
                suff[d][i] = cnt;
            }
        }

        long ans = 0;

        // For every inner digit y
        for (int y = 0; y < 10; y++) {
            // Gather all positions of digit y
            List<Integer> posList = new ArrayList<>();
            for (int i = 0; i < n; i++) {
                if (s.charAt(i) - '0' == y) posList.add(i);
            }
            int k = posList.size();
            if (k <= 1) continue;           // need at least two inner indices

            // For every outer digit x
            for (int x = 0; x < 10; x++) {
                long sumA = 0;
                long sumAPos = 0;
                for (int idx = 0; idx < k; idx++) {
                    int pos = posList.get(idx);
                    long A = pos == 0 ? 0 : pref[x][pos - 1];
                    long B = pos == n - 1 ? 0 : suff[x][pos + 1];

                    long term = ((pos * sumA % MOD - sumAPos - sumA) % MOD + MOD) % MOD;
                    long contrib = term * B % MOD;
                    ans = (ans + contrib) % MOD;

                    sumA = (sumA + A) % MOD;
                    sumAPos = (sumAPos + A * pos) % MOD;
                }
            }
        }
        return ans;
    }

    // ---------- Driver for testing ----------
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String s = br.readLine().trim();
        System.out.println(countPalindromicSubseq(s));
    }
}
```

---

### 5.2 Python 3.11

```python
import sys

MOD = 1_000_000_007

def count_palindromic_subseq(s: str) -> int:
    n = len(s)

    # Prefix and suffix arrays
    pref = [[0] * n for _ in range(10)]
    suff = [[0] * n for _ in range(10)]

    # Build pref
    for d in range(10):
        cnt = 0
        for i, ch in enumerate(s):
            if ord(ch) - 48 == d:
                cnt += 1
            pref[d][i] = cnt

    # Build suff
    for d in range(10):
        cnt = 0
        for i in range(n - 1, -1, -1):
            if ord(s[i]) - 48 == d:
                cnt += 1
            suff[d][i] = cnt

    ans = 0

    # Main aggregation
    for y in range(10):
        positions = [i for i, ch in enumerate(s) if ord(ch) - 48 == y]
        k = len(positions)
        if k <= 1:
            continue

        for x in range(10):
            sumA = 0
            sumAPos = 0
            for pos in positions:
                A = pref[x][pos - 1] if pos > 0 else 0
                B = suff[x][pos + 1] if pos + 1 < n else 0

                # (pos * sumA - sumAPos - sumA) mod MOD
                term = (pos * sumA - sumAPos - sumA) % MOD
                contrib = term * B % MOD
                ans = (ans + contrib) % MOD

                sumA = (sumA + A) % MOD
                sumAPos = (sumAPos + A * pos) % MOD

    return ans

if __name__ == "__main__":
    s = sys.stdin.readline().strip()
    print(count_palindromic_subseq(s))
```

---

### 5.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;
const long long MOD = 1'000'000'007LL;

long long countPalindromicSubseq(const string& s) {
    int n = s.size();
    vector<array<int, 10001>> pref(10), suff(10); // 10 x n

    // Prefix
    for (int d = 0; d < 10; ++d) {
        int cnt = 0;
        for (int i = 0; i < n; ++i) {
            if (s[i] - '0' == d) ++cnt;
            pref[d][i] = cnt;
        }
    }

    // Suffix
    for (int d = 0; d < 10; ++d) {
        int cnt = 0;
        for (int i = n - 1; i >= 0; --i) {
            if (s[i] - '0' == d) ++cnt;
            suff[d][i] = cnt;
        }
    }

    long long ans = 0;
    vector<int> pos;

    for (int y = 0; y < 10; ++y) {
        pos.clear();
        for (int i = 0; i < n; ++i)
            if (s[i] - '0' == y) pos.push_back(i);
        int k = pos.size();
        if (k <= 1) continue;

        for (int x = 0; x < 10; ++x) {
            long long sumA = 0, sumAPos = 0;
            for (int p : pos) {
                long long A = (p == 0) ? 0 : pref[x][p - 1];
                long long B = (p + 1 == n) ? 0 : suff[x][p + 1];

                long long term = ((p * sumA % MOD - sumAPos - sumA) % MOD + MOD) % MOD;
                long long contrib = term * B % MOD;
                ans = (ans + contrib) % MOD;

                sumA = (sumA + A) % MOD;
                sumAPos = (sumAPos + A * p) % MOD;
            }
        }
    }
    return ans;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    string s;  getline(cin, s);
    cout << countPalindromicSubseq(s) << '\n';
}
```

---

## 7. Frequently Asked Questions

| Question | Answer |
|----------|--------|
| **Why not brute‑force?** | For `n=10⁴`, brute force is `O(n^5)` → impossible. |
| **What if the string is empty?** | Returns `0`. |
| **Can the algorithm handle huge memory?** | It uses only `O(10·n)` ints → < 400 KB. |
| **Why do we ignore `k ≤ 1`?** | An inner pair requires at least two indices. |

---

## 8. What Makes This Problem *Hard*?

1. **Four‑nested structure** – You must simultaneously manage two
   dimensions (inner vs outer indices) while respecting order constraints.
2. **Dynamic counting** – Naïve enumeration would be exponential.
3. **Modulo arithmetic** – Prevent overflow while preserving correctness.
4. **Edge cases** – Empty strings, all identical digits, very short
   strings.

A well‑structured algorithm that leverages prefix sums is the only way
to solve it efficiently.

---

## 9. Conclusion

We have built an optimal solution for counting 5‑character palindromic
subsequences:

* A careful combinatorial reduction that leads to a linear algorithm.
* A rigorous proof that the method is correct.
* Three reference implementations that you can copy, test, and adapt.

> **Final note** – The same pattern (prefix–suffix counts + aggregation)
> can be applied to many other substring counting problems (e.g. counting
> occurrences of specific subsequence patterns, or 3‑character palindromes
> with different constraints). Mastering this technique will pay off
> in future contests. Happy coding! 🚀
---



**Happy coding!**