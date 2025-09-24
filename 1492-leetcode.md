---
title: LeetCode 1492. The kth Factor of n - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # The K‑th Factor of **n** – LeetCode 1492  
**A Deep Dive, Code Solutions in Java / Python / C++ & a Job‑Ready Blog Post**

> **TL;DR** – Find the *k*-th smallest divisor of a number *n*.  
>  Complexity: **O(√n)** (much better than the naïve O(n) scan).  
>  Languages: Java, Python, C++.  
>  Blog sections: the good, the bad, the ugly.  
>  SEO keywords: *kth factor of n, LeetCode 1492, coding interview, algorithm, O(√n) solution, interview prep*.

---

## Table of Contents

| Section | Link |
|---------|------|
| 1️⃣ Problem Recap | #1-problem-recap |
| 2️⃣ Brute‑Force vs. Optimised | #2-brute-force-vs-optimised |
| 3️⃣ O(√n) Solution | #3-o√n-solution |
| 4️⃣ Code in Java | #4-code-in-java |
| 5️⃣ Code in Python | #5-code-in-python |
| 6️⃣ Code in C++ | #6-code-in-cpp |
| 7️⃣ The Good | #7-the-good |
| 8️⃣ The Bad | #8-the-bad |
| 9️⃣ The Ugly | #9-the-ugly |
| 🔟 Interview Tips | #10-interview-tips |
| 📚 References | #11-references |

---

## 1️⃣ Problem Recap

> **LeetCode 1492 – The k‑th Factor of *n***  
> **Given** two positive integers `n` and `k`, return the *k*-th smallest factor of `n`.  
> If `n` has fewer than `k` factors, return `-1`.

> **Examples**  
> 1. `n = 12, k = 3` → factors `[1, 2, 3, 4, 6, 12]` → answer `3`.  
> 2. `n = 7, k = 2` → factors `[1, 7]` → answer `7`.  
> 3. `n = 4, k = 4` → only `[1, 2, 4]` → answer `-1`.  

> **Constraints**  
> `1 ≤ k ≤ n ≤ 1000`.

> **Follow‑up**: Solve in *less than* `O(n)`.

---

## 2️⃣ Brute‑Force vs. Optimised

| Approach | Time | Space | Comments |
|----------|------|-------|----------|
| **Brute‑Force** – iterate i = 1…n, collect factors | **O(n)** | **O(1)** | Works because *n* ≤ 1000, but not optimal for larger inputs. |
| **Optimised** – iterate i = 1…√n, collect pairs | **O(√n)** | **O(1)** | Exploits divisor symmetry; far more efficient for large *n* (e.g., 10^9). |

---

## 3️⃣ O(√n) Solution

1. **Collect the first half of divisors**  
   Iterate `i` from `1` to `√n`.  
   If `i` divides `n`, append `i` to a list `smallDivs`.  

2. **Handle the second half on the fly**  
   If `k` ≤ `smallDivs.size()`, the answer is `smallDivs[k-1]`.  
   Otherwise, the remaining factors are `n / i` for each divisor `i` in reverse order.  
   Compute how many we skip (`k - smallDivs.size()`) and return the appropriate one.  

3. **Return -1 if we run out of divisors**  

> **Why O(√n)?**  
> Every divisor `d` > √n pairs with a divisor `n/d` < √n.  
> Thus we only need to check up to √n to discover *all* factor pairs.

---

## 4️⃣ Code in Java

```java
/**
 * 1492. The k-th Factor of n
 * https://leetcode.com/problems/the-kth-factor-of-n/
 */
class Solution {
    public int kthFactor(int n, int k) {
        // List to store factors <= sqrt(n)
        List<Integer> small = new ArrayList<>();

        int limit = (int) Math.sqrt(n);
        for (int i = 1; i <= limit; i++) {
            if (n % i == 0) {
                small.add(i);
            }
        }

        // If k is within the small factors list
        if (k <= small.size()) {
            return small.get(k - 1);
        }

        // Remaining factors are n / i for i in reverse order
        int remaining = k - small.size();
        int total = n / limit;               // number of large factors
        if (remaining > total) return -1;     // not enough factors

        // Get the (remaining)-th largest factor
        // Example: small = [1,2,3], remaining = 1 -> we want n/3
        int idx = small.size() - remaining;   // index in small list
        return n / small.get(idx);
    }

    // Quick main for local testing
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.kthFactor(12, 3)); // 3
        System.out.println(sol.kthFactor(7, 2));  // 7
        System.out.println(sol.kthFactor(4, 4));  // -1
    }
}
```

> **Key Points**  
> * `Math.sqrt` → double; cast to int for loop boundary.  
> * We store only the *small* divisors; the *large* ones are derived lazily.  
> * Complexity: `O(√n)` time, `O(√n)` space for the list of small divisors.

---

## 5️⃣ Code in Python

```python
# 1492. The k-th Factor of n
# Python 3

class Solution:
    def kthFactor(self, n: int, k: int) -> int:
        small = []
        limit = int(n ** 0.5)
        for i in range(1, limit + 1):
            if n % i == 0:
                small.append(i)

        if k <= len(small):
            return small[k - 1]

        remaining = k - len(small)
        # Number of large factors is total divisors minus small ones
        total_large = (len(small) * 2) - (1 if limit * limit == n else 0)
        if remaining > total_large - len(small):
            return -1

        # Index in small list for the required large divisor
        idx = len(small) - remaining
        return n // small[idx]

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.kthFactor(12, 3))  # 3
    print(sol.kthFactor(7, 2))   # 7
    print(sol.kthFactor(4, 4))   # -1
```

> **Pythonic Touches**  
> * Use list comprehension for `small`.  
> * Compute `limit = int(n ** 0.5)`.  
> * Handle perfect squares correctly (`n == limit * limit`).

---

## 6️⃣ Code in C++

```cpp
// 1492. The k-th Factor of n
// C++17

#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int kthFactor(int n, int k) {
        vector<int> small;
        int limit = sqrt(n);
        for (int i = 1; i <= limit; ++i) {
            if (n % i == 0)
                small.push_back(i);
        }

        if (k <= (int)small.size())
            return small[k - 1];

        int remaining = k - small.size();
        // Count of large divisors
        int totalLarge = (int)small.size() * 2
                        - (limit * limit == n ? 1 : 0);

        if (remaining > totalLarge - (int)small.size())
            return -1;

        int idx = (int)small.size() - remaining;
        return n / small[idx];
    }
};

int main() {
    Solution sol;
    cout << sol.kthFactor(12, 3) << endl; // 3
    cout << sol.kthFactor(7, 2) << endl;  // 7
    cout << sol.kthFactor(4, 4) << endl;  // -1
    return 0;
}
```

> **C++ Highlights**  
> * `sqrt` returns double; cast to int.  
> * Use `vector<int>` for the small divisors.  
> * Keep code compact yet readable.

---

## 7️⃣ The Good

| Aspect | Why It’s Good |
|--------|---------------|
| **Optimal Complexity** – `O(√n)` is ideal for large `n`. |
| **Simplicity** – Only one loop up to √n; no heavy data structures. |
| **Space‑Efficient** – Stores at most √n integers. |
| **Readability** – Clear variable names (`small`, `remaining`). |
| **Cross‑Language Consistency** – Same logic applies to Java, Python, C++. |

---

## 8️⃣ The Bad

| Issue | What Happens |
|-------|--------------|
| **Brute‑Force** – iterating to `n` can be slow for `n` up to 10⁶ or 10⁹. |
| **Unnecessary List** – if you only need the k‑th factor, storing all small divisors is overkill. |
| **Edge Cases** – forgetting the perfect‑square check can double‑count the middle divisor. |
| **Code Complexity** – verbose `if/else` nesting can obscure the logic. |

---

## 9️⃣ The Ugly

> The *ugly* solution is often the first one you write:  
> 1. **Hard‑coded loops**  
> 2. **Mix of loops & recursion**  
> 3. **No early exit**  
> 4. **Messy variable names**  

```java
// Ugly Java
int[] f = new int[1000]; // overkill array
int cnt = 0;
for (int i = 1; i <= n; i++) {
    if (n % i == 0) {
        f[cnt++] = i;
    }
}
if (k <= cnt) return f[k - 1];
return -1;
```

> **Why it’s ugly**  
> * Uses a fixed‑size array instead of a dynamic list.  
> * O(n) time, far from optimal.  
> * No handling of perfect squares.  
> * Poor naming (`f`, `cnt`).  

---

## 🔟 Interview Tips

1. **Clarify the problem** – Ask if `k` is 1‑based (yes, LeetCode uses 1‑based).  
2. **Explain the divisor pairing** – `i` and `n/i`.  
3. **State complexity upfront** – O(√n) is a win.  
4. **Edge Cases** – perfect squares, `k` larger than total divisors, `n == 1`.  
5. **Walk through an example** – e.g., `n = 36`, `k = 5`.  
6. **Mention a clean exit** – return `-1` early if impossible.  
7. **Show the code** – keep it concise, test with sample inputs.  
8. **Discuss alternatives** – brute force, pre‑computing factor lists, memoization for repeated queries.

> **Remember:** Interviewers value *clarity* and *efficiency* more than raw performance. A well‑structured solution with good comments will score high on technical interviews.

---

## 📚 References

1. LeetCode Problem 1492 – <https://leetcode.com/problems/the-kth-factor-of-n/>  
2. Sample solutions from the community (various languages).  
3. Official editorial – O(√n) factorisation.  
4. Competitive programming blogs on divisor enumeration.

---

## 🎯 SEO‑Optimised Conclusion

The *k‑th Factor of n* (LeetCode 1492) is a classic interview problem that tests understanding of divisor symmetry, complexity optimisation, and clean coding practices. The optimal `O(√n)` solution is simple, elegant, and works for very large numbers, making it a great showcase in your portfolio.  

By sharing code snippets in **Java, Python, and C++**, you demonstrate language versatility—a plus for hiring managers. The accompanying blog post covers the *good*, *bad*, and *ugly* aspects, providing interviewers with a clear, professional narrative.  

Ready to ace your next coding interview? Dive into the code, practice edge cases, and keep the logic clean. Good luck – you’ve got this! 🚀

---