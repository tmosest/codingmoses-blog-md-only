---
title: LeetCode 1259. Handshakes That Don't Cross - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1259. Handshakes That Don’t Cross – A Complete, Job‑Ready Guide

> **Problem statement (LeetCode 1259)**  
> You are given an even number of people `numPeople` that stand around a circle and each person shakes hands with someone else so that there are `numPeople / 2` handshakes in total.  
> Count the number of ways these handshakes can occur **without crossing**.  
> Return the answer modulo \(10^9 + 7\).

The constraints are modest (`2 ≤ numPeople ≤ 1000`, `numPeople` is even), but the number of matchings grows explosively – it is a *Catalan number* in disguise.  
Below you will find a clean, production‑ready implementation in **Java, Python, and C++**, plus a short **blog post** that explains why this solution works, the pros and cons, and why it is interview‑friendly.

---

## 📖 Blog Article – “The Good, the Bad, and the Ugly of Handshakes That Don’t Cross”

### 1.  The Problem in Plain English

Imagine a round table with an even number of guests.  
Everyone must shake hands with exactly one other guest, and all the handshake lines must stay **inside the circle** without crossing each other.  
How many distinct handshake patterns can you form?

### 2.  Why It’s a Catalan Number

If you draw the circle with people numbered `1 … n`, every valid handshake configuration can be mapped to a **non‑crossing perfect matching** of points on a circle.  
A classic combinatorial bijection shows that the number of such matchings is the *Catalan number* \(C_{n/2}\):

\[
C_k = \frac{1}{k+1}\binom{2k}{k}
\]

So the task boils down to computing a Catalan number modulo \(10^9+7\).

### 3.  Two Standard Ways to Compute \(C_k\)

| Method | Time | Space | Good for |
|--------|------|-------|----------|
| **DP recurrence**: \(C_0 = 1\); \(C_i = \sum_{j=0}^{i-1} C_j \cdot C_{i-1-j}\) | \(O(k^2)\) | \(O(k)\) | Small \(k\) (≤ 500) – easy to implement |
| **Closed‑form**: factorials + modular inverse | \(O(k)\) (after pre‑computing factorials) | \(O(k)\) | When you want the fastest possible solution |

Because `numPeople` ≤ 1000 → `k = numPeople/2` ≤ 500, the DP approach is perfectly fine and easier to understand for interviewers.

### 4.  The DP Solution (Why It’s Interview‑Friendly)

```text
C[0] = 1
for i = 1 .. k:
    C[i] = 0
    for j = 0 .. i-1:
        C[i] = (C[i] + C[j] * C[i-1-j]) % MOD
```

* The outer loop runs `k` times (≤ 500).  
* The inner loop averages `k/2` iterations → ~125 000 operations – trivial.  
* Uses only integer arithmetic, no floating point.

#### Good
- **Readable** – easy to explain in an interview.
- **No need for modular inverses** – fewer pitfalls.
- **Runs fast** for all allowed inputs.

#### Bad
- Still \(O(k^2)\); not great if `k` could be in the millions.  
- Requires a two‑dimensional conceptual view (Catalan recurrence), which some candidates may forget.

#### Ugly
- If the interview question changes to *odd* number of people, the DP still works, but the problem statement explicitly guarantees even `numPeople`, so we can rely on it.

### 5.  Edge Cases & Modulo Arithmetic

| Edge | What to check |
|------|--------------|
| `numPeople = 2` | Should return 1 (`C_1 = 1`). |
| `numPeople = 1000` | `k = 500`; still fast. |
| Modulo 1 000 000 007 | Use 64‑bit intermediate to avoid overflow. |

### 6.  Why This Matters for Your Job Hunt

- **Catalan numbers** appear in many algorithmic contexts (parenthesization, binary trees, stack permutations). Knowing they arise here demonstrates depth of combinatorial knowledge.
- **Dynamic programming** is a core interview topic; this problem is a classic “small DP” that tests correctness and modulo handling.
- **Time complexity awareness**: You’ll discuss the trade‑off between DP and closed‑form approaches, showing you can choose the best algorithm for constraints.

### 7.  Final Thoughts

The “handshake” problem is a beautiful blend of combinatorics and dynamic programming.  
By turning a seemingly geometric puzzle into a Catalan recurrence, we get a clean, optimal, and interview‑ready solution.  

Below you’ll find full, ready‑to‑paste implementations in **Java, Python, and C++**. Feel free to copy, run, and use them in your coding interviews or on LeetCode.

---

## 📦 Code Snippets – Three Languages

> **All implementations assume `MOD = 1_000_000_007`**.  
> They all read an integer `numPeople` and print the number of non‑crossing handshake patterns.

---

### Java

```java
import java.io.*;
import java.util.*;

public class Handshakes {
    private static final int MOD = 1_000_000_007;

    public int numberOfWays(int numPeople) {
        int n = numPeople / 2;          // k
        long[] dp = new long[n + 1];    // dp[i] = Catalan number C_i
        dp[0] = 1;

        for (int i = 1; i <= n; i++) {
            long sum = 0;
            for (int j = 0; j < i; j++) {
                sum += dp[j] * dp[i - 1 - j];
                if (sum >= (1L << 62)) sum %= MOD; // prevent overflow
            }
            dp[i] = sum % MOD;
        }
        return (int) dp[n];
    }

    // Simple driver to test
    public static void main(String[] args) throws IOException {
        Handshakes hs = new Handshakes();
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int numPeople = Integer.parseInt(br.readLine().trim());
        System.out.println(hs.numberOfWays(numPeople));
    }
}
```

---

### Python

```python
import sys

MOD = 10**9 + 7

def number_of_ways(num_people: int) -> int:
    k = num_people // 2
    dp = [0] * (k + 1)
    dp[0] = 1
    for i in range(1, k + 1):
        s = 0
        for j in range(i):
            s += dp[j] * dp[i - 1 - j]
            if s >= 1 << 61:  # avoid overflow on some interpreters
                s %= MOD
        dp[i] = s % MOD
    return dp[k]

if __name__ == "__main__":
    num_people = int(sys.stdin.readline().strip())
    print(number_of_ways(num_people))
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

const int MOD = 1'000'000'007;

int numberOfWays(int numPeople) {
    int k = numPeople / 2;
    vector<long long> dp(k + 1, 0);
    dp[0] = 1;
    for (int i = 1; i <= k; ++i) {
        long long sum = 0;
        for (int j = 0; j < i; ++j) {
            sum += dp[j] * dp[i - 1 - j];
            if (sum >= (1LL << 62)) sum %= MOD; // avoid overflow
        }
        dp[i] = sum % MOD;
    }
    return static_cast<int>(dp[k]);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int numPeople;
    if (cin >> numPeople) {
        cout << numberOfWays(numPeople) << '\n';
    }
    return 0;
}
```

---

## 🎯 Final Takeaway

- **Problem** → *Catalan numbers* (non‑crossing handshake matchings).  
- **Solution** → DP recurrence \(C_i = \sum C_j C_{i-1-j}\).  
- **Complexity** → \(O((numPeople/2)^2)\) time, \(O(numPeople/2)\) space – perfectly fine for the given constraints.  
- **Interview‑friendly** → clean code, clear math, easy to explain, no tricky modular inverses.

Good luck landing that job! 🚀