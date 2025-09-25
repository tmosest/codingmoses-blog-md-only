---
title: LeetCode 3398. Smallest Substring With Identical Characters I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3398 – Smallest Substring With Identical Characters I  
*Hard – Binary Search on the Answer*  

| Language | Complexity |  Reference |
|----------|------------|------------|
| **Java** | `O(n log n)` time, `O(1)` extra space |  [LeetCode](https://leetcode.com/problems/smallest-substring-with-identical-characters-i/) |
| **Python** | `O(n log n)` time, `O(1)` extra space |  |
| **C++** | `O(n log n)` time, `O(1)` extra space |  |

---

### 1. Problem Restatement  

You are given a binary string `s` (`'0'` and `'1'`) and an integer `numOps`.  
You may flip any bit at most `numOps` times.  
After the flips, find the **minimum possible length** of the longest contiguous block of identical characters.

---

### 2. Intuition  

If you are asked “can we make the longest block ≤ L?” the answer only depends on the *runs* of equal characters that are longer than `L`.  
A run of length `R > L` can be split into pieces of length at most `L` by flipping some bits inside that run.  
The cheapest way to do it is to break the run every `L+1` positions:

```
R = q·(L+1) + r   (0 ≤ r ≤ L)
```

You need exactly `q` flips to create the required separators.  
Sum `q` over all runs → that’s the number of operations needed for the candidate `L`.  
If that number ≤ `numOps`, `L` is feasible; otherwise it isn’t.

The only subtlety is when `L == 1`.  
With `L == 1` we want a perfectly alternating string.  
For that we have to check both patterns (`0101…` and `1010…`) and pick the one that needs fewer flips.

Because the feasibility test is monotone (if `L` is feasible then any larger `L` is also feasible), we can binary‑search the minimum feasible `L`.

---

### 3. Algorithm (Pseudocode)

```
runs = lengths of consecutive equal chars in s
maxRun = maximum element of runs

function cost(L):
    if L == 1:
        flipsStartWith0 = number of mismatches with pattern 0101...
        flipsStartWith1 = number of mismatches with pattern 1010...
        return min(flipsStartWith0, flipsStartWith1)

    total = 0
    for each r in runs:
        total += r // (L + 1)
    return total

binary search over L in [1, maxRun]
    mid = (low + high) // 2
    if cost(mid) <= numOps:
        answer = mid
        high = mid - 1
    else:
        low = mid + 1

return answer
```

---

### 4. Correctness Proof  

We prove that the algorithm returns the minimal possible longest block length.

---

#### Lemma 1  
For a run of length `R > L`, the minimum number of flips needed to reduce every block within that run to length ≤ `L` equals `⌊R / (L+1)⌋`.

**Proof.**  
Place a separator (flip a bit) every `L+1` characters.  
After `q = ⌊R/(L+1)⌋` separators the run is split into `q+1` blocks, each of length at most `L` (the last block may be shorter).  
Any solution that uses fewer than `q` flips would leave a block longer than `L`, contradicting feasibility. ∎



#### Lemma 2  
For a fixed `L`, the total number of flips computed by `cost(L)` is the *minimal* number of flips required to obtain a string whose longest block length is ≤ `L`.

**Proof.**  
`cost(L)` is the sum of the values from Lemma&nbsp;1 over all runs longer than `L`.  
All runs shorter than or equal to `L` are already fine and need no flips.  
Flipping bits in distinct runs is independent, so the sum of the optimal costs per run is optimal overall. ∎



#### Lemma 3  
If `cost(L) ≤ numOps` then there exists a sequence of ≤ `numOps` flips that achieves longest block length ≤ `L`.

**Proof.**  
`cost(L)` gives a constructive way to flip bits in each long run as described in Lemma&nbsp;1.  
Applying these flips yields a string with longest block length ≤ `L`.  
Since the total number of flips equals `cost(L)` and `cost(L) ≤ numOps`, the requirement is satisfied. ∎



#### Lemma 4  
If `cost(L) > numOps` then it is impossible to obtain a string with longest block length ≤ `L` using only `numOps` flips.

**Proof.**  
By Lemma&nbsp;2, any feasible solution must perform at least `cost(L)` flips.  
If `cost(L)` exceeds the budget, no feasible solution exists. ∎



#### Theorem  
The binary‑search algorithm outputs the minimum achievable longest block length.

**Proof.**  
Define the predicate `P(L): cost(L) ≤ numOps`.  
By Lemma&nbsp;3, `P(L)` is true iff `L` is achievable.  
By Lemma&nbsp;4, if `P(L)` is false, no larger `L` can be false? Actually monotonic: if `L` is not achievable, any smaller `L` might also not be achievable. The feasibility set is downward‑closed.  
Thus `P(L)` is monotone decreasing with respect to `L`.  
Binary search on a monotone predicate always finds the smallest `L` with `P(L)` true.  
The algorithm returns that `L`. ∎



---

### 5. Complexity Analysis  

| Step | Cost |
|------|------|
| Build runs (`O(n)`) | `O(n)` |
| `cost(L)` for a candidate | `O(m)` where `m ≤ n` is number of runs → `O(n)` (runs are processed once). |
| Binary search | `O(log maxRun)` iterations, `maxRun ≤ n`. |
| **Total** | `O(n log n)` time, `O(1)` additional memory. |

---

### 5. Reference Implementations  

Below are clean, production‑ready solutions in the three requested languages.

---

#### 5.1 Java (LeetCode‑compatible)

```java
import java.util.*;

class Solution {
    public int minLength(String s, int numOps) {
        int n = s.length();
        if (n == 1) return 1;                // only one block can ever exist

        // Build run lengths
        List<Integer> runList = new ArrayList<>();
        int count = 1;
        for (int i = 1; i < n; i++) {
            if (s.charAt(i) == s.charAt(i - 1)) {
                count++;
            } else {
                runList.add(count);
                count = 1;
            }
        }
        runList.add(count);

        int maxRun = 0;
        for (int r : runList) maxRun = Math.max(maxRun, r);

        // Feasibility test for a candidate L
        java.util.function.IntUnaryOperator cost = L -> {
            if (L == 1) {
                int mism0 = 0;          // pattern 0101...
                int mism1 = 0;          // pattern 1010...
                for (int i = 0; i < n; i++) {
                    char expected0 = (i % 2 == 0) ? '0' : '1';
                    char expected1 = (i % 2 == 0) ? '1' : '0';
                    if (s.charAt(i) != expected0) mism0++;
                    if (s.charAt(i) != expected1) mism1++;
                }
                return Math.min(mism0, mism1);
            } else {
                int flips = 0;
                for (int r : runList) {
                    flips += r / (L + 1);
                }
                return flips;
            }
        };

        // Binary search
        int low = 1, high = maxRun, ans = maxRun;
        while (low <= high) {
            int mid = (low + high) >>> 1;
            if (cost.applyAsInt(mid) <= numOps) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
}
```

---

#### 5.2 Python (3.10+)

```python
from typing import List

class Solution:
    def minLength(self, s: str, numOps: int) -> int:
        n = len(s)
        if n == 1:
            return 1

        # Build run lengths
        runs: List[int] = []
        cnt = 1
        for i in range(1, n):
            if s[i] == s[i - 1]:
                cnt += 1
            else:
                runs.append(cnt)
                cnt = 1
        runs.append(cnt)

        max_run = max(runs)

        def cost(L: int) -> int:
            if L == 1:
                # mismatches with two possible alternating patterns
                mism0 = mism1 = 0
                for i, ch in enumerate(s):
                    expected0 = '0' if i % 2 == 0 else '1'
                    expected1 = '1' if i % 2 == 0 else '0'
                    if ch != expected0:
                        mism0 += 1
                    if ch != expected1:
                        mism1 += 1
                return min(mism0, mism1)
            flips = 0
            for r in runs:
                flips += r // (L + 1)
            return flips

        # Binary search
        lo, hi = 1, max_run
        ans = max_run
        while lo <= hi:
            mid = (lo + hi) // 2
            if cost(mid) <= numOps:
                ans = mid
                hi = mid - 1
            else:
                lo = mid + 1
        return ans
```

---

#### 5.3 C++ (GNU C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minLength(string s, int numOps) {
        int n = (int)s.size();
        if (n == 1) return 1;

        // Build run lengths
        vector<int> runs;
        int cnt = 1;
        for (int i = 1; i < n; ++i) {
            if (s[i] == s[i - 1]) cnt++;
            else {
                runs.push_back(cnt);
                cnt = 1;
            }
        }
        runs.push_back(cnt);

        int maxRun = *max_element(runs.begin(), runs.end());

        auto cost = [&](int L)->int{
            if (L == 1) {
                int mism0 = 0, mism1 = 0;
                for (int i = 0; i < n; ++i) {
                    char expect0 = (i % 2 == 0) ? '0' : '1';
                    char expect1 = (i % 2 == 0) ? '1' : '0';
                    if (s[i] != expect0) mism0++;
                    if (s[i] != expect1) mism1++;
                }
                return min(mism0, mism1);
            } else {
                int flips = 0;
                for (int r : runs)
                    flips += r / (L + 1);
                return flips;
            }
        };

        int low = 1, high = maxRun, ans = maxRun;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (cost(mid) <= numOps) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
};
```

---

### 5.4 Edge Cases Handled

| Case | Why it matters | Handling |
|------|----------------|----------|
| `n == 1` | The only block length is 1 | Return 1 immediately |
| `numOps == 0` | No flips allowed → answer is the current longest run | The binary search will still work, but we can skip expensive cost checks |
| `L == 1` | Alternating pattern can’t be handled by the generic run formula | Compute flips for both patterns (`0101…` & `1010…`) |

---

## 6. Blog Article  
*(SEO‑optimized copy‑paste ready for Medium, Dev.to, or a personal blog)*  

> **Title**  
> “From Binary Strings to Job‑Ready Interviews – Mastering LeetCode 3398 with Binary Search”

---

### 6.1 Introduction  

> *“Hard”* is a LeetCode label that means you’re not just practicing; you’re *showing* your algorithmic thinking.  
> Problem 3398 – *Smallest Substring With Identical Characters I* – tests exactly that: you must find a minimal longest block after limited bit flips.  
> In this post I’ll walk through the intuition, the formal solution, why it works, and how you can explain it in an interview.  
> If you’re hunting a software‑engineering role, this is the kind of question you’ll see in coding rounds, and mastering it gives you brag‑worthy material for your resume.

---

### 6.2 What Makes This Problem “Hard”  

| Why Hard | What it means for you |
|----------|-----------------------|
| **Monotone Feasibility + Binary Search** | You’ll learn how to “binary‑search on the answer” – a pattern that appears in many interview questions. |
| **Edge Case L = 1** | Small off‑by‑ones can break your logic. You’ll need to think about both alternating patterns. |
| **Large Constraints** | `n` can be 2 × 10⁵. A naïve O(n²) or O(n × numOps) solution will TLE. |

---

### 6.3 The “Good” – The Core Idea  

*Break every long run into blocks of length ≤ L by flipping a separator every L+1 positions.*  
Why does this work?  
Because flipping the **earliest possible bit** inside a long run always gives you the maximum benefit for the fewest flips.  
The math works out to `⌊R/(L+1)⌋` flips per run – simple, fast, and optimal.

**Why it’s great for your résumé**

* You can explain a *run‑based* solution in 30 seconds.  
* It demonstrates *optimization mindset*: you reduce a complex problem to a handful of arithmetic operations.  
* You’ll show that you can handle **non‑trivial edge cases** (L = 1).  

---

### 6.4 The “Nice” – Binary Search on Feasibility  

You can’t check all possible `L` values one by one (there are up to n).  
Instead, you observe that if a certain `L` is feasible (you can make all blocks ≤ L), then any *larger* `L` is also feasible, but any *smaller* `L` may not be.  
This *monotone* property lets you binary‑search `L` in `O(log n)` steps.  

**Interview‑friendly explanation**

> “I first determine if a given `L` is possible by counting required flips; then I perform a binary search over `L`.  
> Each feasibility test is linear in the number of runs, so the overall complexity is `O(n log n)` – fast enough for the constraints.”

---

### 6.5 The “Bad” – What Could Go Wrong  

1. **Mis‑counting runs** – e.g., forgetting to push the last run.  
2. **Using the generic formula for L = 1** – this fails because an alternating pattern has no “separator” inside the string.  
3. **Incorrect lower/upper bounds in binary search** – e.g., using `maxRun` as upper bound but not handling the case where `maxRun` is 1.  

**How to guard yourself**

* Test with tiny strings (`n = 1`, `n = 2`, `numOps = 0`).  
* Verify the cost function for a known `L` (e.g., L = current max run → flips = 0).  
* Walk through a hand‑drawn example in the interview; show you’re thinking about *mismatch* counts for both patterns when L = 1.

---

### 6.6 The “Bad” – Potential Interview Pitfalls  

| Pitfall | Fix |
|---------|-----|
| “I wrote a solution that passes the sample but not hidden tests” | Add **early exits** for trivial cases (`n = 1`, `numOps = 0`). |
| “I used recursion and got stack overflow” | Prefer iterative loops; in C++ avoid deep recursion because stack size can be limited on the platform. |
| “The interviewer wants to see the time complexity” | Be ready to say *O(n log n)* and explain why each part is linear. |

---

### 6.7 The “Nice” – What to Tell the Interviewer  

1. **State the problem in your own words** – “We’re allowed up to `numOps` bit flips; we want to minimize the length of the longest contiguous block.”  
2. **Explain the run‑based model** – “We can compress the string into run lengths; each run of length `R` will need `⌊R/(L+1)⌋` flips to make all blocks ≤ L.”  
3. **Show the feasibility test** – “Given `L`, we can compute total flips in linear time.”  
4. **Describe the binary search** – “We binary‑search `L` from 1 to the current maximum run.”  
5. **Mention the edge case** – “When L = 1, we need to consider both alternating patterns; we simply count mismatches against 0101… and 1010….”  

Be ready to write code on the whiteboard: the skeleton is short; the tricky part is handling `L==1`.  

---

### 6.8 How This Knowledge Translates to Real‑World Coding  

* **Batch Processing** – Splitting a string into runs is analogous to processing logs in chunks.  
* **Greedy Selections** – Picking the earliest separator is a greedy strategy that often shows up in scheduling problems.  
* **Algorithmic Patterns** – Binary‑search on the answer appears in “minimum/maximum” questions across domains (e.g., “minimum initial energy” for path problems).  

If you can comfortably explain these patterns, you’re signaling to interviewers that you can *recognize* a class of problems and *apply* a proven technique.

---

### 6.9 Final Takeaway  

LeetCode 3398 is not just a question; it’s a micro‑teaching moment.  
*Good* for your résumé: a neat run‑based greedy, a clean `O(n log n)` implementation, and a solid proof of correctness.  
*Nice* for interview preparation: practice explaining *binary‑search on the answer* and handling subtle edge cases.

**Next steps**  
1. Commit this solution to your GitHub repository (include the three language variants).  
2. Add a blog post with the code snippets and a few example runs.  
3. In your resume, note *“Solved LeetCode 3398 – binary string optimization using run decomposition and binary search on the answer.”*  

You’ve just turned an algorithmic puzzle into interview gold. 🚀  

---

### 6.10 Closing  

If you enjoyed this deep dive into LeetCode 3398, let me know!  
Drop a comment or a DM on LinkedIn, and I’ll share more interview‑ready questions that build on the same concepts.  

Happy coding, and may your interview rounds be as tight as the minimal longest block you just optimized!  

---


**End of Blog Post**  

*(Feel free to tweak the headings or add your own personal anecdotes. The structure is ready‑to‑publish and contains all key points an interviewer will appreciate.)**


---


### 7. Closing Remarks  

We’ve covered:

* Formal algorithmic proof that the run‑based greedy is optimal.  
* A thorough reference implementation in Java, Python, and C++.  
* A ready‑to‑publish interview‑style blog article that ties the solution to career growth.  

You’re now fully equipped to ace this question, write clean code, and explain the reasoning in a job interview. Good luck!