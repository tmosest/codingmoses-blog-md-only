---
title: LeetCode 3610. Minimum Number of Primes to Sum to Target - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ Problem Recap – Minimum Number of Primes to Sum to Target  
**LeetCode #3610 – Medium**

> You are given two integers `n` and `m`.  
> You must pick a multiset of the *first `m` prime numbers* (each prime may be used arbitrarily many times) such that their sum equals exactly `n`.  
> Return the minimum number of primes needed, or `-1` if it’s impossible.

**Constraints**

| Variable | Range |
|----------|-------|
| `n` | `1 … 1 000` |
| `m` | `1 … 1 000` |

---

## 🧠 Why This Looks Like “Coin Change”

* The primes act like **coins** (unbounded supply).  
* We want the **fewest coins** to reach a target value.  
* Classic dynamic‑programming (DP) solution: `dp[i] = min(dp[i], dp[i‑coin] + 1)`.

The only twist: we must restrict the coin set to the first `m` primes.

---

## 📌 High‑Level Algorithm

1. **Generate primes** up to `n` using the Sieve of Eratosthenes.  
   *Only primes ≤ `n` can contribute to a sum of `n`.*  
2. **Collect the first `m` primes** from the sieve.  
   *If `m` is larger than the number of primes ≤ `n`, we stop early.*  
3. **Unbounded‑knapsack DP** (`dp[0] = 0`).  
   *For each prime `p`*  
   *   for `s = p … n`  
   *        `dp[s] = min(dp[s], dp[s-p] + 1)`  
4. **Answer**: `dp[n]` if reachable, otherwise `-1`.

### Why it Works

* **Optimal Substructure** – the best way to reach `s` uses the best way to reach `s‑p` plus one more `p`.  
* **Overlapping Subproblems** – DP stores already computed minimal counts.  
* **Unboundedness** – the inner loop iterates **increasing** `s`, allowing the same prime to be used multiple times.

---

## ⚙️ Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sieve (up to `n`) | `O(n log log n)` | `O(n)` |
| Collect first `m` primes | `O(n)` | `O(min(m, π(n)))` |
| DP | `O(n · min(m, π(n)))` | `O(n)` |

With `n ≤ 1000`, this is trivial – easily < 1 ms.

---

## 🛠️ Code in Three Languages

Below you’ll find clean, commented implementations in **Java**, **Python**, and **C++**.  
All follow the same algorithmic skeleton.

---

### Java

```java
import java.util.*;

public class Solution {
    public int minNumberOfPrimes(int n, int m) {
        // 1. Sieve up to n
        boolean[] isPrime = new boolean[n + 1];
        Arrays.fill(isPrime, true);
        if (n >= 0) isPrime[0] = false;
        if (n >= 1) isPrime[1] = false;
        for (int i = 2; i * i <= n; i++) {
            if (isPrime[i]) {
                for (int j = i * i; j <= n; j += i) isPrime[j] = false;
            }
        }

        // 2. Collect the first m primes (but only those ≤ n)
        List<Integer> primes = new ArrayList<>();
        for (int i = 2; i <= n && primes.size() < m; i++) {
            if (isPrime[i]) primes.add(i);
        }

        // 3. DP (unbounded coin change)
        int INF = Integer.MAX_VALUE / 2;        // avoid overflow
        int[] dp = new int[n + 1];
        Arrays.fill(dp, INF);
        dp[0] = 0;

        for (int p : primes) {
            for (int s = p; s <= n; s++) {
                dp[s] = Math.min(dp[s], dp[s - p] + 1);
            }
        }

        return dp[n] == INF ? -1 : dp[n];
    }

    public static void main(String[] args) {
        System.out.println(new Solution().minNumberOfPrimes(10, 2)); // 4
        System.out.println(new Solution().minNumberOfPrimes(15, 5)); // 3
        System.out.println(new Solution().minNumberOfPrimes(7, 6));  // 1
    }
}
```

---

### Python

```python
import math
from typing import List

class Solution:
    def minNumberOfPrimes(self, n: int, m: int) -> int:
        # 1. Sieve
        is_prime = [True] * (n + 1)
        if n >= 0: is_prime[0] = False
        if n >= 1: is_prime[1] = False
        for i in range(2, int(math.isqrt(n)) + 1):
            if is_prime[i]:
                step = i
                start = i * i
                is_prime[start:n + 1:step] = [False] * ((n - start) // step + 1)

        # 2. First m primes
        primes = [i for i in range(2, n + 1) if is_prime[i]][:m]

        # 3. DP
        INF = 10 ** 9
        dp = [INF] * (n + 1)
        dp[0] = 0
        for p in primes:
            for s in range(p, n + 1):
                dp[s] = min(dp[s], dp[s - p] + 1)

        return -1 if dp[n] == INF else dp[n]


if __name__ == "__main__":
    sol = Solution()
    print(sol.minNumberOfPrimes(10, 2))  # 4
    print(sol.minNumberOfPrimes(15, 5))  # 3
    print(sol.minNumberOfPrimes(7, 6))   # 1
```

---

### C++ (GNU C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minNumberOfPrimes(int n, int m) {
        // 1. Sieve up to n
        vector<bool> isPrime(n + 1, true);
        if (n >= 0) isPrime[0] = false;
        if (n >= 1) isPrime[1] = false;
        for (int i = 2; i * i <= n; ++i)
            if (isPrime[i])
                for (int j = i * i; j <= n; j += i)
                    isPrime[j] = false;

        // 2. First m primes
        vector<int> primes;
        for (int i = 2; i <= n && (int)primes.size() < m; ++i)
            if (isPrime[i]) primes.push_back(i);

        // 3. DP
        const int INF = 1e9;
        vector<int> dp(n + 1, INF);
        dp[0] = 0;
        for (int p : primes)
            for (int s = p; s <= n; ++s)
                dp[s] = min(dp[s], dp[s - p] + 1);

        return dp[n] == INF ? -1 : dp[n];
    }
};

int main() {
    Solution sol;
    cout << sol.minNumberOfPrimes(10, 2) << '\n'; // 4
    cout << sol.minNumberOfPrimes(15, 5) << '\n'; // 3
    cout << sol.minNumberOfPrimes(7, 6)  << '\n'; // 1
}
```

---

## 📈 “Good”, “Bad”, and “Ugly” Insights

| Category | Why it’s Good | Why it could be Bad | Ugly Patterns to Avoid |
|----------|---------------|--------------------|------------------------|
| **Good** | • O(n log log n) sieve is optimal for small `n`. <br>• DP is *exact* and runs in < 1 ms. <br>• Code is clean and fully type‑safe. | • None – the algorithm is optimal for the constraints. | – |
| **Bad** | • If you ignore the “first `m`” constraint and just use all primes ≤ `n`, you’ll over‑count. <br>• Using a 2‑D DP (`dp[prime][sum]`) wastes O(m·n) extra memory. | • Extra memory may matter on huge inputs (not here). | **Using `dp[i] = min(dp[i], dp[i‑p] + 1)` inside the same prime loop but iterating `s` decreasingly → that gives *unbounded*?** – Actually decreasing order forbids re‑using the same coin in the same iteration, giving *0‑1* behaviour. |
| **Ugly** | • Re‑implementing a primality test via repeated divisions instead of a sieve → O(n √n). <br>• Hard‑coded upper bound of 1000, making the solution fragile for larger test cases. <br>• Using recursion with memoization but forgetting to pass `m`‑limit can blow the stack. | • Performance degrade on bigger `n` (e.g., 1 000 000). | Avoid global state or static variables that carry stale data across test cases. |

---

## 🎯 What Interviewers Are Looking For

1. **Recognizing the pattern** (Coin‑Change DP).  
2. **Handling the “first `m` primes” restriction**.  
3. **Implementing the sieve correctly** (avoid off‑by‑one errors).  
4. **Time‑/Space‑efficiency**: `O(n)` for DP is a clean answer.  

In an interview, you should:

* **Explain the algorithm in words first** – show that you can map the problem to a known template.  
* **Sketch the DP recurrence** on a whiteboard.  
* **Mention edge cases** (`m` larger than number of primes ≤ `n`, impossible sums).  
* **Discuss potential optimizations** (e.g., early exit if `dp[n] == 1`, or using bit‑set DP for extra speed).  

These points are *transferable* to many interview problems: *dynamic programming*, *unbounded knapsack*, *prime number generation*, *LeetCode*.

---

## 📢 SEO‑Ready Blog Post

### Title (H1)  
> “LeetCode 3610: Minimum Number of Primes to Sum to Target – A Clean DP Solution (Java, Python, C++)”

---

### Intro (H2)

> Are you prepping for a **software‑engineering interview**?  
> Today’s spotlight is **LeetCode #3610 – Minimum Number of Primes to Sum to Target**.  
> The problem might seem like an obscure math puzzle, but it’s actually a **classic dynamic‑programming** exercise that many interviewers love to throw at candidates.  
> In this post, you’ll learn the full solution in **Java, Python, and C++**, plus why the algorithm is a perfect fit for interview questions.

Keywords: LeetCode 3610, minimum number of primes to sum to target, dynamic programming, coin change, job interview, software engineering interview, Java Python C++.

---

### Problem Statement (H3)

> Given integers `n` (target sum) and `m` (number of available primes), select a multiset of the first `m` prime numbers (any prime may be reused) so that the sum is exactly `n`.  
> Output the minimal count of primes used, or `-1` if impossible.

Constraints: `1 ≤ n, m ≤ 1 000`.

---

### Why This Is a “Coin‑Change” Problem (H3)

- **Primes = Coins**: Each prime can be used unlimited times.  
- **Goal**: Fewest coins → fewest primes.  
- **Known DP**: `dp[i] = min(dp[i], dp[i‑coin] + 1)`.  
- **Special Twist**: Use only the **first `m` primes**.

---

### Algorithm Walk‑Through (H3)

1. **Sieve of Eratosthenes** – generate all primes up to `n`.  
2. **Take the first `m` primes** from the sieve (stop early if fewer primes exist).  
3. **Unbounded knapsack DP** – fill an array `dp[0…n]` where `dp[s]` is the minimal primes needed for sum `s`.  
4. Return `dp[n]` or `-1` if unreachable.

*(See code snippets below.)*

---

### Java Implementation (H3)

```java
import java.util.*;

public class Solution {
    public int minNumberOfPrimes(int n, int m) {
        // 1. Sieve
        boolean[] isPrime = new boolean[n + 1];
        Arrays.fill(isPrime, true);
        if (n >= 0) isPrime[0] = false;
        if (n >= 1) isPrime[1] = false;
        for (int i = 2; i * i <= n; i++) {
            if (isPrime[i]) {
                for (int j = i * i; j <= n; j += i) isPrime[j] = false;
            }
        }

        // 2. First m primes
        List<Integer> primes = new ArrayList<>();
        for (int i = 2; i <= n && primes.size() < m; i++) {
            if (isPrime[i]) primes.add(i);
        }

        // 3. DP
        int INF = Integer.MAX_VALUE / 2;
        int[] dp = new int[n + 1];
        Arrays.fill(dp, INF);
        dp[0] = 0;

        for (int p : primes) {
            for (int s = p; s <= n; s++) {
                dp[s] = Math.min(dp[s], dp[s - p] + 1);
            }
        }

        return dp[n] == INF ? -1 : dp[n];
    }
}
```

---

### Python Implementation (H3)

```python
import math
from typing import List

class Solution:
    def minNumberOfPrimes(self, n: int, m: int) -> int:
        # Sieve
        is_prime = [True] * (n + 1)
        if n >= 0: is_prime[0] = False
        if n >= 1: is_prime[1] = False
        for i in range(2, int(math.isqrt(n)) + 1):
            if is_prime[i]:
                for j in range(i * i, n + 1, i):
                    is_prime[j] = False

        # First m primes
        primes = [i for i in range(2, n + 1) if is_prime[i]][:m]

        # DP
        INF = 10 ** 9
        dp = [INF] * (n + 1)
        dp[0] = 0
        for p in primes:
            for s in range(p, n + 1):
                dp[s] = min(dp[s], dp[s - p] + 1)

        return -1 if dp[n] == INF else dp[n]
```

---

### C++ Implementation (GNU C++17) (H3)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minNumberOfPrimes(int n, int m) {
        // Sieve
        vector<bool> isPrime(n + 1, true);
        if (n >= 0) isPrime[0] = false;
        if (n >= 1) isPrime[1] = false;
        for (int i = 2; i * i <= n; ++i)
            if (isPrime[i])
                for (int j = i * i; j <= n; j += i)
                    isPrime[j] = false;

        // First m primes
        vector<int> primes;
        for (int i = 2; i <= n && (int)primes.size() < m; ++i)
            if (isPrime[i]) primes.push_back(i);

        // DP
        const int INF = 1e9;
        vector<int> dp(n + 1, INF);
        dp[0] = 0;
        for (int p : primes)
            for (int s = p; s <= n; ++s)
                dp[s] = min(dp[s], dp[s - p] + 1);

        return dp[n] == INF ? -1 : dp[n];
    }
};
```

---

### Complexity Analysis (H3)

- **Sieve**: `O(n log log n)` time, `O(n)` space.  
- **DP**: `O(n)` time, `O(n)` space.  
- **Overall**: `O(n log log n)` time, `O(n)` memory – perfect for `n ≤ 1 000`.

---

### Take‑Away Tips for Interviews (H3)

1. **Pattern recognition** – map to “unbounded knapsack”.  
2. **Sieve correctness** – watch off‑by‑one errors.  
3. **DP recurrence** – whiteboard it.  
4. **Edge cases** – explain `m > #primes ≤ n` and unreachable sums.  
5. **Optimizations** – early stopping if `dp[n] == 1`.

---

### Conclusion (H2)

> The **Minimum Number of Primes to Sum to Target** problem is more than just a LeetCode trick.  
> It demonstrates how a **math‑based problem** can be solved with a **well‑known dynamic‑programming** pattern, a skill every senior software‑engineering candidate should master.  
> Try coding it in your preferred language (Java, Python, C++) and run the test cases – you’ll be ready to impress interviewers with both theory and practice.

---

### Call to Action (H2)

> Want more interview‑style solutions?  
> Subscribe to our newsletter for the latest **LeetCode** walkthroughs and **coding interview tips** in **Java, Python, C++**.

---

## 🎉 Wrap‑Up (H2)

You now have a:

- **Full, efficient solution** in three popular languages.  
- **Insight into algorithmic patterns** that resonate in interviews.  
- A **SEO‑optimized blog post** that’s shareable and discoverable for anyone studying LeetCode or preparing for a job interview.

Happy coding, and good luck on your next interview!