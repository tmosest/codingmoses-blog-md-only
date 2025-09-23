---
title: LeetCode 2261. K Divisible Elements Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

> **2261. K Divisible Elements Subarrays**  
> **Difficulty:** Medium  
> **Signature (Java):**  
> `public int countDistinct(int[] nums, int k, int p)`  

Given an integer array `nums`, an integer `k` and a divisor `p`, count **distinct** contiguous subarrays that contain **at most `k` elements that are divisible by `p`**.

Two subarrays are distinct if their sequences of values are different, not just their positions.

> **Constraints**  
> * `1 ≤ nums.length ≤ 200`  
> * `1 ≤ nums[i] ≤ 200`  
> * `1 ≤ k ≤ nums.length`  
> * `1 ≤ p ≤ 200`

Because `n` is tiny (≤ 200) an `O(n²)` algorithm is plenty fast enough for all LeetCode test‑cases.  
Below we give **three language‑specific solutions** that are easy to understand and ready for a coding‑interview.

---

## 2.  Reference Implementations

| Language | Core Idea | Code |
|----------|-----------|------|
| **Java** | Brute‑force + `HashSet` of tuples | ✅ |
| **Python** | Brute‑force + `set` (tuple + optional Rabin‑Karp) | ✅ |
| **C++** | Brute‑force + `unordered_set` + rolling‑hash for speed | ✅ |

> ⚡️ All three codes use the same “generate‑all‑subarrays → check → add to set” pattern, but the data‑structure choice shows the trade‑offs that the interviewers love to discuss.

---

### 1.1  Java – Hash‑Set Version (Most “Interview‑Friendly”)

```java
import java.util.*;

public class Solution {
    /**
     * Counts distinct subarrays that contain at most k elements divisible by p.
     */
    public int countDistinct(int[] nums, int k, int p) {
        Set<String> seen = new HashSet<>();
        int n = nums.length;

        // O(n^2) – generate every subarray once
        for (int start = 0; start < n; ++start) {
            int divisibleCnt = 0;

            for (int end = start; end < n; ++end) {
                if (nums[end] % p == 0) {
                    divisibleCnt++;
                }
                if (divisibleCnt > k) break;     // no need to extend further

                // Convert the slice to a canonical string
                StringBuilder sb = new StringBuilder();
                for (int i = start; i <= end; ++i) {
                    sb.append(nums[i]).append(',');   // comma keeps boundaries clear
                }
                seen.add(sb.toString());            // set guarantees uniqueness
            }
        }
        return seen.size();
    }
}
```

**Why this is “good”:**  
* Uses only standard library (`HashSet`).  
* Linear in the number of subarrays (`n(n+1)/2`).  
* No tricky hashing – easy to explain in an interview.

**Potential “bad” points:**  
* The string representation is not memory‑optimal; it stores up to 200 × 200 ≈ 40 k characters – fine for LeetCode, but wasteful in tight‑memory scenarios.  
* The inner `for` loop that rebuilds the `StringBuilder` on every subarray can be a bit slow in Java.

---

### 1.2  Python – Set + Optional Rabin‑Karp

```python
def countDistinct(nums: list[int], k: int, p: int) -> int:
    """
    LeetCode 2261 – Python implementation.
    Uses a plain set of tuples (fast for n ≤ 200) and
    an optional Rabin–Karp rolling hash for demo purposes.
    """
    n = len(nums)
    distinct = set()

    # 1️⃣  Simple tuple set (clear, fast enough for n ≤ 200)
    for i in range(n):
        cnt = 0
        for j in range(i, n):
            if nums[j] % p == 0:
                cnt += 1
            if cnt > k:
                break
            distinct.add(tuple(nums[i:j+1]))

    # Uncomment the following block to see a rolling‑hash variant.
    """
    # 2️⃣  Rabin‑Karp style rolling hash (shows how to avoid tuple creation)
    MOD = 10**9 + 7
    BASE = 257
    power = [1] * (n+1)
    for i in range(1, n+1):
        power[i] = (power[i-1] * BASE) % MOD

    for i in range(n):
        h = 0
        cnt = 0
        for j in range(i, n):
            if nums[j] % p == 0:
                cnt += 1
            if cnt > k:
                break
            h = (h * BASE + nums[j]) % MOD
            distinct.add(h)
    """

    return len(distinct)
```

> **What the optional block does:**  
> * Keeps a single integer hash per subarray, so memory is `O(n²)` integers instead of tuples.  
> * Works the same for the interview – you can mention that this is a “classic” Rabin–Karp trick.

---

### 1.3  C++ – Unordered‑Set + Rolling Hash

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int countDistinct(vector<int>& nums, int k, int p) {
        int n = nums.size();
        unordered_set<long long> seen;          // stores hash of subarray
        const long long MOD = 1'000'000'007LL;  // a big prime
        const long long BASE = 257;             // > max(nums[i])

        // Pre‑compute powers of BASE
        vector<long long> pow(BASE * 1LL, 1);
        for (int i = 1; i < pow.size(); ++i) {
            pow[i] = (pow[i-1] * BASE) % MOD;
        }

        for (int i = 0; i < n; ++i) {
            long long h = 0;
            int cnt = 0;
            for (int j = i; j < n; ++j) {
                if (nums[j] % p == 0) cnt++;
                if (cnt > k) break;
                h = (h * BASE + nums[j]) % MOD;
                seen.insert(h);
            }
        }
        return seen.size();
    }
};
```

> **Why this works:**  
> * Every subarray is mapped to a unique hash value (collision probability ≈ 1/`MOD`).  
> * Because `n ≤ 200`, a simple set of hashes is enough – no need for a full suffix‑array.

---

## 2.  The Trade‑Offs – “Good – Bad – Ugly”

| Aspect | Good (Interview‑Friendly) | Bad (Harder to Explain) | Ugly (Over‑engineering) |
|--------|---------------------------|------------------------|-------------------------|
| **Complexity** | `O(n²)` time, `O(n²)` space – perfectly acceptable for `n≤200`. | `O(n²)` time, `O(n²)` space – same but with a heavy data‑structure. | `O(n)` time, `O(n)` space with suffix arrays – overkill for this problem. |
| **Readability** | Simple loops + `HashSet` – very readable. | Same, plus a small rolling‑hash snippet. | Trie implementation – needs a lot of code and many arrays. |
| **Memory** | `HashSet<tuple>` – uses about a few KB. | Same – but Python tuples add overhead. | `unordered_set<long long>` – a bit more efficient than Python. |
| **Collision Risk** | None – set of tuples guarantees uniqueness. | None – tuples unique. | Small risk with rolling hash, mitigated by a large prime. |
| **Interview Value** | Shows you can reason about *subarray* generation + *set* usage. | Adds a bit of “hash‑engineering” flair. | Shows you can implement a “fast” hash‑based solution and understand pitfalls. |
| **Pitfalls** | Re‑creating strings every time (Java) – slightly slower. | Python’s tuple creation is heavy but fine. | In C++, forgetting to use `reserve` can cause rehash overhead. |

> **Bottom line:**  
> The plain `HashSet` / `tuple` solution is what you want to write in an interview – it’s fast, clean, and easy to explain.  
> If the interviewer asks you to *optimize* the solution, talk about how you could replace the tuple with a single 64‑bit rolling hash and why that would still be correct.  
> Finally, you can mention the *suffix‑array* approach as a “wow‑factor” – you can say, “I’ve studied the linear‑time algorithm for counting distinct substrings; I could plug it here if the constraints were larger.”

---

## 3.  SEO‑Friendly Blog Article

> **Title (H1):**  
> **K Divisible Elements Subarrays – A LeetCode 2261 Masterclass (Java, Python, C++)**

> **Meta‑description (≈150 characters):**  
> “Learn how to solve LeetCode 2261 – K Divisible Elements Subarrays – with clean Java, Python and C++ code. Understand hash‑set, trie, Rabin‑Karp, suffix‑array tricks.”

> **Keywords:**  
> LeetCode 2261, K Divisible Elements Subarrays, distinct subarrays, algorithm interview, hash set, rolling hash, Rabin‑Karp, suffix array, O(n²) solution, C++ unordered_set, Java HashSet, Python tuple set.

---

### 3.1  Introduction

> **(H2) Why LeetCode 2261 is a Classic Interview Question**  
> *“Counting subarrays with constraints is a staple in data‑structures interviews. LeetCode 2261 is a perfect blend of array traversal + set usage.”*

---

### 3.2  Problem Breakdown

> **(H2) Formal Problem Statement**  
> *Explain the definition of “divisible by p” and “at most k” in plain words.*  
> *Illustrate with a 4‑element array example to show the concept of “at most k” vs. “exactly k”.*

---

### 3.3  Solution Strategy – The Brute‑Force + Set Approach

> **(H2) Step‑by‑Step Guide**  
> 1. **Generate all subarrays** – nested loops (`O(n²)`).  
> 2. **Count divisible elements** while extending.  
> 3. **Add to a Set** – ensures uniqueness.  
> 4. Return the set’s size.  

> *Show pseudocode on the page and link each language snippet.*

---

### 3.4  Language‑Specific Walk‑throughs

| Language | Highlight | Code Block |
|----------|-----------|------------|
| **Java** | `HashSet<String>` + `StringBuilder`. | (Insert Java code) |
| **Python** | Tuple set + optional Rabin‑Karp. | (Insert Python code) |
| **C++** | `unordered_set<long long>` + rolling hash. | (Insert C++ code) |

> **(H3) Explanation of Rolling Hash**  
> “Instead of storing the whole slice, we build a 64‑bit integer using a base > 200. Collisions are astronomically unlikely because we mod with 1 000 000 007.”

---

### 3.5  Advanced Techniques – “If Constraints Were Bigger”

> **(H2) Rolling Hash vs. Tuple**  
> “Discuss why a 64‑bit hash is sufficient and mention the probability of collision. Emphasize that this is a classic Rabin‑Karp trick.”

> **(H2) Suffix‑Array Solution**  
> “Introduce the linear‑time algorithm for counting distinct substrings (Karkkainen‑Sanders).  
> Briefly outline the steps: build SA → LCP → formula → adapt to “at most k” condition.”  

> *End with a teaser:* “I’ve also implemented a full trie for this problem, but it’s unnecessary unless you’re tackling 10⁵ elements.”

---

### 3.6  Summary & Take‑Away

> **(H2) Take‑away Checklist**  
> * Write the simple `HashSet` solution first.  
> * Be ready to discuss collision‑free hash replacement.  
> * Mention suffix array as a theoretical optimization.  
> * Practice the code in all three languages; LeetCode accepts any.  

> **Call‑to‑Action (H3):**  
> “Try the code on LeetCode yourself. Drop a comment if you want the suffix‑array walkthrough or help optimizing for larger data‑sets!”

---

## 4.  Final Tips for the Interview

1. **Start with the simplest solution.**  
2. **Explain the core logic clearly:** “We’re enumerating every subarray and using a `set` to automatically dedupe.”  
3. **If asked to optimize, discuss the rolling hash approach and why it still gives a collision‑free answer for LeetCode limits.**  
4. **Mention the suffix‑array approach only if the interviewer is looking for “wow” factor** – not to actually implement it.  
5. **Always test your code on a small custom input** (e.g., `nums = [2, 3, 4, 6]`, `k=1`, `p=2`) – it shows you’re careful.

---

### 5.  Closing

You now have **ready‑to‑submit** Java, Python and C++ code for LeetCode 2261, plus a clear understanding of the “good – bad – ugly” trade‑offs that make a candidate stand out.  

Happy coding! 🚀

--- 

> **Happy hacking, and may your interviewers love the hash‑set solution as much as we love it.**