---
title: LeetCode 646. Maximum Length of Pair Chain - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  LeetCode 646 – Maximum Length of Pair Chain  
**Language:** Java | Python | C++  
**Approach:** Greedy (sort by the second value of each pair) – optimal & fastest.  
**Time complexity:** **O(n log n)** (sorting)  
**Space complexity:** **O(1)** (in‑place, apart from the input array)

---

### 1.1  Problem Recap

> Given an array `pairs` of size `n`, where `pairs[i] = [leftᵢ , rightᵢ]` and `leftᵢ < rightᵢ`.  
> A pair **p₂ = [c, d]** follows pair **p₁ = [a, b]** if `b < c`.  
> Find the maximum length of a chain that can be formed using any subset of the pairs in any order.

---

### 1.2  Greedy Insight  

If we always choose the pair that ends **earliest** (smallest `right` value) among all pairs that can start a new chain, we leave the largest possible “free space” for the remaining pairs.  
Thus the optimal strategy is:

1. **Sort** the pairs by their `right` value ascending.  
2. Scan from left to right, picking a pair whenever its `left` is greater than the `right` of the last chosen pair.

The greedy rule is exactly the same as the classic “activity selection” problem.

---

## 2.  Reference Implementations  

Below you’ll find clean, production‑ready code for each of the requested languages.  
All solutions have the same algorithmic complexity but use language‑specific idioms and built‑in library calls.

---

### 2.1  Java

```java
import java.util.Arrays;

public class Solution {
    public int findLongestChain(int[][] pairs) {
        // 1. Sort by the second element (right side)
        Arrays.sort(pairs, (a, b) -> Integer.compare(a[1], b[1]));

        int count = 0;
        int prevRight = Integer.MIN_VALUE;

        // 2. Greedy scan
        for (int[] pair : pairs) {
            if (pair[0] > prevRight) {        // can be appended
                count++;
                prevRight = pair[1];          // update the last chosen pair
            }
        }
        return count;
    }
}
```

**Explanation of key lines**

| Line | Why it matters |
|------|----------------|
| `Arrays.sort(pairs, …)` | Sort by the second value; guarantees that the next selectable pair is the one with the smallest end. |
| `prevRight = Integer.MIN_VALUE` | Allows the first pair to be selected without special‑case logic. |
| `pair[0] > prevRight` | Core greedy condition: start of current pair must be after end of previous pair. |

---

### 2.2  Python

```python
class Solution:
    def findLongestChain(self, pairs: List[List[int]]) -> int:
        # 1. Sort by the second element
        pairs.sort(key=lambda x: x[1])

        count = 0
        prev_end = float('-inf')

        # 2. Greedy scan
        for left, right in pairs:
            if left > prev_end:
                count += 1
                prev_end = right

        return count
```

*`float('-inf')`* serves the same role as `Integer.MIN_VALUE` in Java.

---

### 2.3  C++

```cpp
class Solution {
public:
    int findLongestChain(vector<vector<int>>& pairs) {
        // 1. Sort by second element
        sort(pairs.begin(), pairs.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 return a[1] < b[1];
             });

        int count = 0;
        int prevRight = INT_MIN;

        // 2. Greedy scan
        for (const auto& p : pairs) {
            if (p[0] > prevRight) {
                ++count;
                prevRight = p[1];
            }
        }
        return count;
    }
};
```

---

## 3.  Alternate Dynamic‑Programming (O(n²))  

For completeness, the DP solution is useful when you want to *also* recover the chain or when greedy intuition isn’t obvious.

```python
def findLongestChainDP(pairs):
    n = len(pairs)
    pairs.sort(key=lambda x: x[1])
    dp = [1] * n  # dp[i] = length ending at i
    for i in range(n):
        for j in range(i):
            if pairs[j][1] < pairs[i][0]:
                dp[i] = max(dp[i], dp[j] + 1)
    return max(dp)
```

Complexity: **O(n²)** time, **O(n)** space – far slower for `n <= 1000`, but still perfectly fine for LeetCode.

---

## 4.  Blog Article – “The Good, the Bad, and the Ugly of Solving LeetCode 646”

### 4.1  Why This Problem Matters  
* LeetCode 646 is a classic “maximum chain” problem that blends **greedy algorithms** with the intuition of “activity selection.”  
* It is a favorite on coding‑interview prep lists because it tests:  
  * Understanding of sorting and comparison functions.  
  * Ability to identify optimal sub‑structures.  
  * Clean, O(n log n) code that compiles in all languages.

### 4.2  The Good  
* **Simplicity** – Once you spot the “pick the smallest end” rule, the code boils down to a single loop.  
* **Performance** – Sorting dominates, giving *O(n log n)* time – fast even for the upper bound of 1000 pairs.  
* **Portability** – The same logic applies in Java, Python, C++, and even in functional languages.  
* **Scalability** – The greedy solution is still optimal for larger constraints (e.g., `n = 10⁶`), while DP would be infeasible.

### 4.3  The Bad  
* **Hidden Assumptions** – The greedy rule only works because pairs are *non‑overlapping* (`left < right`). If this invariant breaks, the algorithm fails.  
* **Readability for Beginners** – Sorting with a custom comparator or lambda may be confusing for newcomers. Adding comments is essential.  
* **Edge Cases** – Empty input or negative values are handled implicitly but can trip people up during interviews if not mentioned.

### 4.4  The Ugly  
* **Testing Misconceptions** – Some candidates mistakenly compare `right` of the current pair with the `right` of the *previous selected* pair *without* ensuring the pair is actually selected.  
* **Off‑by‑One Errors** – In Python, forgetting `prev_end = float('-inf')` and initializing with `-1` could incorrectly reject the first pair if its `left` is `0`.  
* **Sorting Stability** – In C++, `sort` is not stable. If two pairs share the same `right`, the order of their `left` values does not affect correctness, but an unstable sort can make debugging harder.

### 4.5  SEO‑Optimized Keyphrases (Use Them in Your Resume, Blog, or LinkedIn)  

| Keyword | Why It Matters |
|---------|----------------|
| **Maximum Length of Pair Chain** | Core problem title – appears in interviews and search results. |
| **LeetCode 646** | People often search by problem number. |
| **Greedy algorithm for pair chain** | Highlights algorithmic skill. |
| **Java solution for pair chain** | Attracts recruiters looking for Java proficiency. |
| **Python greedy algorithm** | Same for Python. |
| **C++ interview problem** | Appeals to systems‑level roles. |
| **Activity selection problem** | Shows knowledge of classic algorithmic patterns. |
| **Job interview preparation** | Broadens reach. |
| **Algorithmic problem solving** | Generic skill indicator. |

> *Tip:* When posting the article on Medium, Dev.to, or a personal blog, use tags like `#LeetCode`, `#Algorithm`, `#Java`, `#Python`, `#C++`, `#InterviewPrep`, `#JobSearch`.  
> Search engines will index the article, and recruiters using job boards (LinkedIn, Indeed, etc.) often filter by these tags.

### 4.6  Final Thoughts  

*If you can explain this problem in under 30 seconds, you’ve shown mastery of greedy reasoning and clean coding.*  
Keep the code snippets ready in your personal repo – recruiters love seeing well‑structured solutions across languages.  
And, as always, practice the DP version. Even if you’re not going to use it in production, knowing *two* distinct solutions demonstrates depth—something every hiring manager looks for.

---

## 5.  Take‑away Checklist for Your Interview

1. **State the greedy rule**: “Choose the pair with the smallest right endpoint that doesn’t overlap with the previously chosen pair.”
2. **Show the sort**: In Java use `Arrays.sort(pairs, (a,b) -> a[1] - b[1])`; in Python `pairs.sort(key=lambda x: x[1])`; in C++ `sort(..., [](auto& a, auto& b){ return a[1] < b[1]; })`.
3. **Explain the scan**: Keep `prevEnd`, increment `count` when `left > prevEnd`, update `prevEnd`.
4. **Complexity**: `O(n log n)` time, `O(1)` auxiliary space.
5. **Edge cases**: Empty array, all overlapping pairs, negative values – all handled naturally.
6. **Optional DP**: Mention `O(n²)` DP if interviewer asks about alternative approaches.

---

### 6.  Wrap‑up  

You now have:

* **Three production‑ready solutions** – Java, Python, C++.  
* **A clear, interview‑friendly explanation** – Good, Bad, Ugly.  
* **SEO‑friendly copy** that will land your blog post in search results for “Maximum Length of Pair Chain” and related queries.  

Drop this into your portfolio, post the article, and you’ll not only showcase your coding skills but also your ability to communicate them—exactly what recruiters are hunting for. Happy coding, and good luck on your next interview!