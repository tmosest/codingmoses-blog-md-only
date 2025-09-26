---
title: LeetCode 1964. Find the Longest Valid Obstacle Course at Each Position - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1. LeetCode 1964 – *Find the Longest Valid Obstacle Course at Each Position*  
**Hard** |  O(n log n) |  Java |  Python |  C++

> You are given an array `obstacles`.  
> For every index `i` you must choose a subsequence that **includes** `obstacles[i]`,
> is **non‑decreasing**, and has maximum possible length.  
> Return an array `ans` where `ans[i]` is that maximum length.

The classical way to solve this is **Patience Sorting** (the same trick that is used for
Longest Increasing Subsequence).  
We keep an auxiliary array `dp` where `dp[len]` is the *smallest possible ending
value* of a non‑decreasing subsequence of length `len + 1` that has already been seen.  
While scanning the obstacles from left to right we binary‑search for the position
where the current obstacle would fit in `dp`.  
That position + 1 is the answer for this index.  
If the obstacle extends the longest subsequence so far we push it onto `dp`,
otherwise we replace the existing value – the replacement guarantees that the
subsequence ending value stays as small as possible, which keeps the array optimal
for future elements.

Below you’ll find a clean, production‑ready implementation in **Java**, **Python** and **C++**.

> **Why the SEO‑tags?**  
> If you’re aiming for a coding‑heavy role, LeetCode 1964 is a perfect interview
> example. Highlighting “LeetCode 1964 Java solution”, “Longest Obstacle Course
> DP”, “Binary Search O(n log n)” etc. in the article will make the post rank
> for recruiters looking for algorithm‑savvy candidates.

---

### 1.1  Java ( 2024‑Style  )

```java
class Solution {
    public int[] longestObstacleCourseAtEachPosition(int[] obstacles) {
        int n = obstacles.length;
        int[] dp = new int[n];          // dp[len] – smallest ending value of length len+1
        int[] ans = new int[n];
        int len = 0;                    // current longest length

        for (int i = 0; i < n; i++) {
            // upper‑bound style: first index with value > obstacles[i]
            int pos = Arrays.binarySearch(dp, 0, len, obstacles[i] + 1);
            if (pos < 0) pos = -pos - 1;      // standard upperBound

            // update the dp array
            dp[pos] = obstacles[i];

            // if we appended at the end, increase global length
            if (pos == len) len++;

            ans[i] = pos + 1;                // subsequence length that ends at i
        }
        return ans;
    }
}
```

*Why `obstacles[i] + 1`?*  
We use `upper_bound` (first element **greater** than the key).  
Adding `1` to the obstacle turns the “non‑decreasing” requirement into a strictly
increasing search.

---

### 1.2  Python ( 3.10+ )

```python
from bisect import bisect_right
from typing import List

def longestObstacleCourseAtEachPosition(obstacles: List[int]) -> List[int]:
    """
    Patience sorting + binary search (O(n log n)).
    """
    dp = []            # dp[len] – smallest ending value for subsequence of length len+1
    ans = []

    for x in obstacles:
        # bisect_right returns first index with value > x (upper bound)
        pos = bisect_right(dp, x)
        if pos == len(dp):
            dp.append(x)
        else:
            dp[pos] = x
        ans.append(pos + 1)

    return ans
```

*Tip:* `bisect_right` is exactly the `upper_bound` we need.  
If you accidentally use `bisect_left` the answer will be **off by one**.

---

### 1.3  C++ (Modern, 2024‑style)

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> longestObstacleCourseAtEachPosition(std::vector<int>& obstacles) {
        std::vector<int> dp;            // dp[len] – minimal end value of length len+1
        std::vector<int> ans;
        for (int x : obstacles) {
            // upper_bound: first element > x
            auto it = std::upper_bound(dp.begin(), dp.end(), x);
            int pos = it - dp.begin();
            if (it == dp.end())
                dp.push_back(x);        // extend longest length
            else
                *it = x;                // replace to keep minimal ending value
            ans.push_back(pos + 1);     // subsequence length that ends at this index
        }
        return ans;
    }
};
```

> **Why `upper_bound` instead of `lower_bound`?**  
> We want a non‑decreasing subsequence.  
> `upper_bound` guarantees we insert *after* all elements equal to `x`, ensuring the
> subsequence stays non‑decreasing and the dp array remains optimal.

---

## 2. The Good, The Bad, and The Ugly – A 2024 Interview Guide  

> *A comprehensive, SEO‑friendly, job‑search‑friendly article for anyone preparing for a
> technical interview.*

---

### 2.1  Introduction – Why LeetCode 1964 Matters

- **Keyword‑rich**: “LeetCode 1964”, “Longest Valid Obstacle Course”, “DP interview
  problem”, “coding interview”, “job interview algorithm”, “Java/Python/C++”.
- Recruiters search for candidates who can solve *Hard* LeetCode questions with
  clean, efficient code.  
- LeetCode 1964 is a **real‑world dynamic‑programming challenge** that appears in
  many interview question banks.

---

### 2.2  The Good – What Makes This Problem a Great Interview Topic

| Feature | Why It Helps |
|---------|--------------|
| **Real‑world analogy** | Obstacle courses mirror real‑life planning problems (e.g., scheduling). |
| **Scales to O(n log n)** | Demonstrates the candidate’s knowledge of advanced data structures. |
| **Language‑agnostic** | Solvable in Java, Python, C++. Perfect for multi‑language portfolios. |
| **Non‑trivial DP** | Shows ability to transform a naive O(n²) solution into an optimal one. |

> *Result*: You’ll show recruiters you can go from a brute‑force idea to an
> optimal algorithm in under a minute.

---

### 2.3  The Bad – Common Pitfalls

| Mistake | Fix |
|---------|-----|
| Using **`lower_bound`** (first `>=`) instead of **`upper_bound`** (first `>`) | `obstacles[i]` must be **included**; we need a *strictly increasing* index for the new value. |
| Forgetting to add **1** to the binary‑search result | `pos` is a zero‑based index; `ans[i] = pos + 1`. |
| Modifying the original `obstacles` array | Keep `dp` separate to avoid corrupting input data. |
| Not initializing `dp` size properly | `dp` can be a vector of length `n` (max possible subsequence length). |
| Using recursion for binary search in languages with small stack | Use iterative binary search to stay within stack limits. |

---

### 2.4  The Ugly – Edge Cases That Sneak in

1. **All elements equal**  
   Example: `[5,5,5,5]` → answer `[1,2,3,4]`.  
   *Why it breaks naive solutions?* Because every element can be appended; `dp`
   keeps getting larger.

2. **Strictly decreasing array**  
   Example: `[4,3,2,1]` → answer `[1,1,1,1]`.  
   Binary‑search always returns `0`, so we never extend `dp`.

3. **Large input values (up to 10⁹)**  
   Ensure you use `long`/`long long` if you’re tempted to add 1 to the value
   before searching. The algorithm itself only needs comparison, not arithmetic.

---

### 2.5  Quick‑Start Code (Java / Python / C++)

> **Copy‑paste ready** – just drop into your IDE or a LeetCode “submit” window.

```java
// Java 17+  (LeetCode format)
class Solution {
    public int[] longestObstacleCourseAtEachPosition(int[] obstacles) {
        int n = obstacles.length;
        int[] dp = new int[n];
        int[] ans = new int[n];
        int len = 0;                     // longest length seen so far

        for (int i = 0; i < n; ++i) {
            int pos = binarySearch(dp, 0, len, obstacles[i]); // first > obstacles[i]
            dp[pos] = obstacles[i];
            if (pos == len) len++;
            ans[i] = pos + 1;
        }
        return ans;
    }

    private int binarySearch(int[] dp, int left, int right, int target) {
        int l = left, r = right;
        while (l <= r) {
            int m = (l + r) >>> 1;
            if (dp[m] <= target) l = m + 1;
            else r = m - 1;
        }
        return l;                    // upper‑bound index
    }
}
```

```python
# Python 3.10+  (LeetCode format)
from bisect import bisect_right
from typing import List

def longestObstacleCourseAtEachPosition(obstacles: List[int]) -> List[int]:
    dp = []          # smallest ending value for each length
    ans = []

    for x in obstacles:
        pos = bisect_right(dp, x)    # first index with value > x
        if pos == len(dp):
            dp.append(x)
        else:
            dp[pos] = x
        ans.append(pos + 1)

    return ans
```

```cpp
// C++17 (LeetCode format)
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> longestObstacleCourseAtEachPosition(std::vector<int>& obstacles) {
        std::vector<int> dp;          // minimal end value for each length
        std::vector<int> ans;

        for (int x : obstacles) {
            auto it = std::upper_bound(dp.begin(), dp.end(), x);
            int pos = it - dp.begin();
            if (it == dp.end())
                dp.push_back(x);
            else
                *it = x;
            ans.push_back(pos + 1);
        }
        return ans;
    }
};
```

---

## 3. Wrap‑Up – Take the Next Step

- **Showcase**: Add LeetCode 1964 solutions to your GitHub portfolio.  
- **Explain**: Use the article’s bullet points in your resume: “Solved LeetCode 1964
  in Java with O(n log n) DP” etc.
- **Practice**: Repeat the binary‑search logic on similar “DP + binary search”
  problems (e.g., “Maximum Score from Removing Stones” or “Longest Increasing
  Subsequence”).
- **Interview**: When asked for a *Hard* problem, mention the obstacle‑course
  analogy, then describe the Patience‑Sorting DP in 30 seconds.

> **Your Next Recruiter‑Ready Move** – submit these solutions, publish this article,
> and watch interview offers roll in.

--- 

> **Final thought**: Mastering LeetCode 1964 demonstrates that you’re not just a
> coder; you’re a **strategic problem solver** who can navigate “the good,
> bad, and ugly” of algorithm design – exactly what any senior software role needs.