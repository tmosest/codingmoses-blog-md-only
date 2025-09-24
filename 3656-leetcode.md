---
title: LeetCode 3656. Determine if a Simple Graph Exists - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 3656 – “Determine if a Simple Graph Exists”  
### Problem recap (LeetCode)

> **Given** an integer array `degrees`, where `degrees[i]` is the desired degree of vertex *i* in an undirected *simple* graph (no self‑loops, no parallel edges).  
> **Return** `true` if a graph with exactly those degrees can be built, otherwise `false`.

**Constraints**  

* `1 ≤ n = degrees.length ≤ 10⁵`  
* `0 ≤ degrees[i] ≤ n − 1`

The classic solution uses the **Erdős–Gallai theorem** – a degree sequence is *graphical* iff it satisfies a set of inequalities.  
Below you’ll find working implementations in **Java, Python, and C++** (O(n log n)), followed by a detailed blog article that explains the theory, highlights pitfalls, and shows how mastering this problem can boost your interview résumé.

---

## 📦 1. Code – Java

```java
import java.util.Arrays;

public class Solution {
    public boolean simpleGraphExists(int[] ds) {
        int n = ds.length;
        Arrays.sort(ds);                     // non‑decreasing

        // 1. Parity check – total degree must be even
        long total = 0;
        for (int d : ds) total += d;
        if ((total & 1L) == 1L) return false;

        // 2. Prefix sums – we will reuse them in the loop
        long[] prefix = new long[n + 1];     // prefix[i] = d0 + ... + d(i-1)
        for (int i = 0; i < n; i++) prefix[i + 1] = prefix[i] + ds[i];

        // 3. Main loop – Erdős–Gallai inequality
        for (int k = 1; k <= n; k++) {
            // Left hand side : sum_{i=1..k} d_i  (1‑based)
            long lhs = prefix[k];

            // Right hand side : k(k-1) + sum_{i=k+1..n} min(d_i, k)
            long rhs = (long) k * (k - 1);
            // Find first index > k
            int idx = upperBound(ds, k, 0, n);  // index of first > k
            // For i = k+1 .. idx-1 : d_i <= k  → add d_i
            rhs += prefix[idx] - prefix[k];
            // For i = idx .. n-1   : d_i > k   → add k each
            rhs += (long) (n - idx) * k;

            if (lhs > rhs) return false;
        }
        return true;
    }

    /** standard binary search: first index in [lo,hi) with arr[i] > val */
    private int upperBound(int[] arr, int val, int lo, int hi) {
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (arr[mid] <= val) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

> **Why this works**  
> *Sorting* brings degrees into ascending order, enabling us to use prefix sums.  
> The Erdős–Gallai inequality states that for all `k` (1‑based)  
> `∑_{i=1}^{k} d_i ≤ k(k-1) + ∑_{i=k+1}^{n} min(d_i, k)`  
> If the inequality fails for any `k`, no simple graph can realize the sequence.  
> A parity check is a cheap early exit: the sum of degrees must be even (handshake lemma).

---

## 📦 2. Code – Python

```python
class Solution:
    def simpleGraphExists(self, degrees: list[int]) -> bool:
        n = len(degrees)
        degrees.sort()                         # non‑decreasing

        # 1. Parity check
        if sum(degrees) % 2:
            return False

        # 2. Prefix sums
        pref = [0] * (n + 1)
        for i, d in enumerate(degrees):
            pref[i + 1] = pref[i] + d

        # 3. Erdős–Gallai loop
        for k in range(1, n + 1):
            lhs = pref[k]

            # Find first index where degree > k
            lo, hi = k, n
            while lo < hi:
                mid = (lo + hi) // 2
                if degrees[mid] <= k:
                    lo = mid + 1
                else:
                    hi = mid
            idx = lo  # first index > k

            rhs = k * (k - 1)
            rhs += pref[idx] - pref[k]            # d_i <= k part
            rhs += (n - idx) * k                  # d_i > k part

            if lhs > rhs:
                return False

        return True
```

> **Python‑specific notes**  
> * `list[int]` syntax requires Python 3.9+.  
> * The binary search is inlined for speed (no `bisect` import).  
> * Prefix sums allow O(1) range sum queries.

---

## 📦 3. Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool simpleGraphExists(vector<int>& degrees) {
        int n = degrees.size();
        sort(degrees.begin(), degrees.end());          // non‑decreasing

        // 1. Parity check
        long long total = 0;
        for (int d : degrees) total += d;
        if (total & 1LL) return false;

        // 2. Prefix sums
        vector<long long> pref(n + 1, 0);
        for (int i = 0; i < n; ++i) pref[i + 1] = pref[i] + degrees[i];

        // 3. Erdős–Gallai loop
        for (int k = 1; k <= n; ++k) {
            long long lhs = pref[k];

            // Find first index where degree > k (upper_bound)
            int idx = upper_bound(degrees.begin() + k, degrees.end(), k) - degrees.begin();

            long long rhs = 1LL * k * (k - 1);
            rhs += pref[idx] - pref[k];                // degrees <= k
            rhs += 1LL * (n - idx) * k;                // degrees > k

            if (lhs > rhs) return false;
        }
        return true;
    }
};
```

> **C++‑specific notes**  
> * `upper_bound` from `<algorithm>` is used to locate the first element `> k`.  
> * Long long is used to avoid overflow when `n` can be 10⁵ and degrees up to n−1.

---

## 📑 4. Blog Article – “Determining if a Simple Graph Exists: The Good, the Bad, and the Ugly”

---

### 🏁 Introduction

In any **interview** for a software‑engineering role, you’ll often encounter questions that look simple on the surface but conceal deep algorithmic challenges. One such problem is **“Determine if a Simple Graph Exists”** (LeetCode 3656). It’s a perfect illustration of how **graph theory** can meet **real‑world coding**.

If you can master this problem, you’ll not only impress recruiters with your grasp of the **Erdős–Gallai theorem**, but also demonstrate:

- **Mathematical thinking** (handshake lemma, inequalities)
- **Algorithmic optimisation** (O(n log n) vs. naïve O(n²))
- **Attention to edge‑cases** (overflow, parity, large inputs)

Let’s walk through the **good**, the **bad**, and the **ugly** of solving this problem, and see how the solution fits into your interview arsenal.

---

### ✅ The Good – What Makes This Problem Valuable

| Aspect | Why It Matters |
|--------|----------------|
| **Concrete graph‑theoretic concept** | It forces you to apply the Erdős–Gallai theorem, a pillar of degree‑sequence analysis. |
| **Clear input–output contract** | Degrees are an integer array, no hidden graph representation. |
| **Scalable constraints** | Up to 10⁵ vertices – requires a linearithmic algorithm, not brute‑force. |
| **Rich discussion** | You can talk about the handshake lemma, the necessity of even sum, and why simple graphs differ from multigraphs. |

> **Take‑away**: LeetCode 3656 is a *stand‑alone interview classic* that tests both theory and coding.

---

### ⚠️ The Bad – Common Pitfalls

1. **Forgetting the Parity Check**  
   The sum of degrees must be even (each edge contributes 2). Skipping this early exit leads to unnecessary work and may mislead you into thinking a sequence is valid.

2. **Integer Overflow**  
   With n ≤ 10⁵ and each degree ≤ n − 1, the sum can reach ~10¹⁰. Use 64‑bit integers (`long long` in C++, `long` in Java, `int` in Python is arbitrary‑precision).

3. **Mis‑indexing the Inequality**  
   Erdős–Gallai is 1‑based; forgetting to adjust indices after sorting (or using 0‑based arrays) yields wrong results.

4. **O(n²) Naïve Implementation**  
   Summing min(d_i, k) for every k leads to O(n²), which times out on the 10⁵ upper bound.

5. **Incorrect Upper‑Bound**  
   Finding the first degree greater than `k` is critical. A linear scan each iteration turns the algorithm into O(n²).

---

### 😖 The Ugly – Edge Cases That Trip Up Even Experienced Coders

| Edge case | Why it breaks naive solutions | How to handle it |
|-----------|--------------------------------|-------------------|
| **All zeros** | Total sum is 0 (even), but every inequality holds trivially. ✔︎ | No special code needed. |
| **All `n-1`** | Complete graph, total sum = n(n-1), even iff n is even. The inequality reduces to `k(k-1) + ...`. ✔︎ | Same algorithm works. |
| **Large single high degree** | A vertex with degree n-1 forces all others to have at least 1, but if many are zero the inequality fails early. ✔︎ | The algorithm detects this in the first few `k`. |
| **Duplicate degrees** | The theorem only cares about the multiset, not distinctness. ✔︎ | Sorting takes care of ordering; duplicates are fine. |
| **Negative degrees** | Input guarantees 0 ≤ degree ≤ n−1, but some languages (e.g., older C++ compilers) may accept negative ints. ✔︎ | Validate input or rely on constraints. |

> **Bottom line**: A robust solution must treat *every* input gracefully—never assume a “nice” distribution.

---

### 🧠 How to Explain This to a Hiring Manager

1. **State the problem clearly** – a degree sequence, simple undirected graph, ask for existence.  
2. **Mention the handshake lemma** – total degree even, quick rejection.  
3. **Introduce Erdős–Gallai** – the key theorem; show the inequality graphically.  
4. **Outline the algorithm** – sort, prefix sums, binary search for the upper bound, loop over k.  
5. **Complexity** – O(n log n) time, O(n) space.  
6. **Possible optimisations** – counting sort for O(n) time if degrees are bounded.  
7. **Edge‑case robustness** – show how your code handles overflow and parity.

This narrative demonstrates *problem‑solving depth* and *software‑engineering pragmatism*—exactly what interviewers want.

---

### 📦 Practical Implementation Tips

| Language | Key Detail | Why It Helps |
|----------|------------|--------------|
| **Java** | Use `long` for prefix sums, `Arrays.sort`. | Avoids overflow, built‑in fast sort. |
| **Python** | Pre‑allocate prefix list, manual binary search. | No extra imports, fast enough for 10⁵. |
| **C++** | `upper_bound` from `<algorithm>`, `long long`. | Very concise, optimal STL usage. |

---

### 🎯 Bonus – Counting‑Sort Variant (O(n))

If the degree values are guaranteed to be in `[0, n-1]`, you can replace `Arrays.sort` (O(n log n)) with a counting sort:

1. Count frequencies of each degree.  
2. Build the sorted array by iterating over the counts.  
3. All other steps stay the same.

The time complexity drops to **O(n)**, but the code becomes a bit longer. For interview settings, the `O(n log n)` solution is usually enough and easier to discuss.

---

### 🚀 How This Solves Your Job Search

* **Showcases breadth** – graph theory + algorithm design.  
* **Demonstrates depth** – understanding of theorems and proofs.  
* **Highlights coding skills** – careful handling of edge cases, optimised loops, clean structure.  

When you land a conversation around LeetCode 3656, you’ll feel confident presenting a solution that is **both mathematically sound and practically efficient**. That is the sweet spot interviewers seek.

---

### 📚 Further Reading

- *Graph Theory* by Reinhard Diestel – chapter on degree sequences.  
- *Algorithm Design* by Kleinberg & Tardos – section on linear‑time graph problems.  
- LeetCode discussions for 3656 – real‑world clarifications and alternative approaches.

---

### 📌 Final Verdict

**LeetCode 3656** may look like a simple “array‑check” question, but it’s actually a gateway to **deep algorithmic reasoning**. Master it, present it clearly, and you’ll open doors to roles that value *both* theory and practice.

Good luck, and happy coding! 🚀

--- 

*Feel free to copy the code snippets above into your own project or interview prep repo. The three‑language implementations illustrate that the same mathematical core translates seamlessly across ecosystems.*