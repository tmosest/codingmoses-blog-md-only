---
title: LeetCode 1944. Number of Visible People in a Queue - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  Solution Code

Below are three completely independent implementations of the optimal `O(n)` solution for LeetCode 1944 “Number of Visible People in a Queue”.  
All three use the **next‑taller** idea (a monotonic stack under the hood) and have identical logic.

---

## 1.1  Java

```java
import java.util.Arrays;

public class Solution {

    /**
     * Returns the number of people each person can see to the right.
     * @param heights array of distinct heights
     * @return array of counts
     */
    public int[] canSeePersonsCount(int[] heights) {
        if (heights == null || heights.length <= 1) {
            return new int[]{0};
        }

        int n = heights.length;
        int[] nextTaller = new int[n];   // index of first person to the right that is taller (or n)
        int[] result     = new int[n];

        nextTaller[n - 1] = n;   // no one to the right of the last person
        result[n - 1]     = 0;

        for (int i = n - 2; i >= 0; --i) {
            int j = i + 1;           // start with the immediate neighbour
            int cnt = 1;             // i can always see j

            // While j is shorter than i, skip the entire block that j can see
            while (j < n && heights[j] < heights[i]) {
                j = nextTaller[j];   // jump to the next taller person of j
                cnt++;               // j itself is visible
            }

            // If we ended because we reached the end of the array, we have counted one extra
            if (j == n) cnt--;

            nextTaller[i] = j;   // store for future queries
            result[i]     = cnt;
        }

        return result;
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution sol = new Solution();
        int[] h1 = {10,6,8,5,11,9};
        System.out.println(Arrays.toString(sol.canSeePersonsCount(h1)));  // [3, 1, 2, 1, 1, 0]

        int[] h2 = {5,1,2,3,10};
        System.out.println(Arrays.toString(sol.canSeePersonsCount(h2)));  // [4, 1, 1, 1, 0]
    }
}
```

---

## 1.2  Python

```python
from typing import List

class Solution:
    def canSeePersonsCount(self, heights: List[int]) -> List[int]:
        if not heights or len(heights) <= 1:
            return [0]

        n = len(heights)
        next_taller = [n] * n      # index of the first taller person to the right
        result = [0] * n

        # The last person sees nobody
        next_taller[n-1] = n
        result[n-1] = 0

        for i in range(n-2, -1, -1):
            j = i + 1
            cnt = 1            # can always see the immediate neighbour

            while j < n and heights[j] < heights[i]:
                j = next_taller[j]  # skip whole block that j can see
                cnt += 1

            if j == n:
                cnt -= 1

            next_taller[i] = j
            result[i] = cnt

        return result

# ---- quick test ----
if __name__ == "__main__":
    sol = Solution()
    print(sol.canSeePersonsCount([10,6,8,5,11,9]))   # [3, 1, 2, 1, 1, 0]
    print(sol.canSeePersonsCount([5,1,2,3,10]))      # [4, 1, 1, 1, 0]
```

---

## 1.3  C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> canSeePersonsCount(vector<int>& heights) {
        int n = heights.size();
        if (n <= 1) return {0};

        vector<int> nextTaller(n, n);   // index of the first taller person to the right
        vector<int> result(n, 0);

        // last person sees nobody
        nextTaller[n-1] = n;
        result[n-1] = 0;

        for (int i = n-2; i >= 0; --i) {
            int j = i + 1;
            int cnt = 1;              // can always see the neighbour

            while (j < n && heights[j] < heights[i]) {
                j = nextTaller[j];    // skip the block that j can see
                cnt++;
            }

            if (j == n) cnt--;        // we counted one too many

            nextTaller[i] = j;
            result[i] = cnt;
        }
        return result;
    }
};

// simple test
int main() {
    Solution s;
    vector<int> h1 = {10,6,8,5,11,9};
    auto r1 = s.canSeePersonsCount(h1);
    for (int x : r1) cout << x << ' ';   // 3 1 2 1 1 0
    cout << '\n';

    vector<int> h2 = {5,1,2,3,10};
    auto r2 = s.canSeePersonsCount(h2);
    for (int x : r2) cout << x << ' ';   // 4 1 1 1 0
    cout << '\n';
}
```

---

# 2.  Blog Article

> **Title** – Mastering LeetCode 1944: *Number of Visible People in a Queue* – Monotonic Stack & Next‑Taller Technique  
> **Tags** – #LeetCode #InterviewPrep #AlgorithmDesign #Java #Python #C++ #MonotonicStack #CodingInterview

---

## 2.1  Why This Problem Rocks in Interviews

- **Visibility logic** – Captures a non‑trivial “line‑of‑sight” condition that is not a classic sliding‑window or two‑pointer problem.
- **Scale** – The constraints (`n ≤ 10⁵`) make it a perfect playground for advanced data‑structures: *monotonic stack*.
- **Recruiter signals** – Solving this in O(n) with a clear reasoning showcases your ability to turn a naïve brute force into a production‑ready algorithm – exactly what senior roles demand.

---

## 2.2  Problem Recap

> Given an array `heights` of **distinct** integer heights, each element represents the height of a person standing in a queue from left to right.  
> A person can see another person to the right if *all* people between them are **strictly shorter** than the *shorter* of the two.  
> Return an array `answer` where `answer[i]` equals the number of people the i‑th person can see to the right.

---

## 2.3  Naïve O(n²) Brute‑Force

```text
for i = 0 … n-1:
    for j = i+1 … n-1:
        if everyone between i and j is shorter than min(heights[i], heights[j]):
            answer[i] += 1
```

### Pitfalls

| Problem | Why it hurts |
|---------|--------------|
| **Quadratic time** | For `n = 100 000`, ~10¹⁰ operations → time‑out. |
| **O(1) space** | Acceptable, but the algorithm is opaque for interviewers. |
| **Hard to extend** | Hard to explain why it works; recruiters prefer a clear data‑structure narrative. |

---

## 2.4  Insight: Monotonic Stack

> The key observation:  
> *When walking from right to left, a person can see all the people in a **non‑increasing block** until the first taller person blocks the view.*

This naturally lends itself to a **monotonic decreasing stack**:  
- Each stack entry stores the *index* of a person whose height is **higher than** the next entry in the stack.  
- While processing a new person `i`, we can **jump** over entire blocks that are shorter than `i`, because every person inside such a block is already known to be visible to `i`.

---

## 2.5  Next‑Taller Approach (O(n) amortized)

The idea is to pre‑compute, for every index `i`, the *index of the first taller person to its right* (`nextTaller[i]`).  
If we already know `nextTaller[j]` for a shorter neighbour `j`, we can skip the whole sub‑array `[j+1 … nextTaller[j]-1]` in a single step.

### Algorithm Steps

1. **Initialize**  
   - `nextTaller[n-1] = n` (no one to the right).  
   - `answer[n-1] = 0`.

2. **Traverse from right to left**  
   - Let `j = i + 1` (the immediate neighbour).  
   - `cnt = 1` (person `i` always sees `j`).  
   - While `heights[j] < heights[i]`:  
     - `j = nextTaller[j]` (jump to the next taller person of `j`).  
     - `cnt++`.  
   - If we stopped because `j == n`, we counted one too many → `cnt--`.  
   - Store `nextTaller[i] = j` and `answer[i] = cnt`.

3. **Return** the answer array.

> **Why it’s correct** – Each `cnt` counts every person that is either *directly* visible (`j`) or is part of a **block** of strictly shorter people that `j` can see.  
> Because `nextTaller[j]` is always the first taller person beyond that block, we never double‑count or miss anyone.

---

## 2.6  Walkthrough Example

Let’s run the algorithm on `heights = [10, 6, 8, 5, 11, 9]`.

| i | heights[i] | j start | Visible persons | nextTaller[i] |
|---|------------|---------|-----------------|---------------|
| 5 | 9          | –       | 0               | 6 (n)         |
| 4 | 11         | 5       | 1 (9)           | 6             |
| 3 | 5          | 4       | 1 (11)          | 6             |
| 2 | 8          | 3       | 1 (5) → jump to 4 (11) → +1 = 2 | 4 |
| 1 | 6          | 2       | 1 (8) → jump to 4 (11) → +1 = 2 | 4 |
| 0 | 10         | 1       | 1 (6) → jump to 2 (8) → +1 = 2 → jump to 4 (11) → +1 = 3 | 4 |

Result: `[3, 1, 2, 1, 1, 0]`.

---

## 2.7  Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time   | `O(n)` amortized (each index is pushed/popped at most once) |
| Space  | `O(n)` for `nextTaller` and the answer array (no explicit stack in the final code) |

> **Why amortized?**  
> Even though the inner `while` loop can run many times, each index is “skipped” at most once because it is removed from the stack when a taller person is encountered. Therefore the total number of loop iterations over the entire pass is ≤ 2 n.

---

## 2.8  Code in Three Languages

*(See the code snippets above)*

Each implementation follows the same logical skeleton:

```text
for i from n-2 downto 0:
    j   = i + 1
    cnt = 1                // immediate neighbour
    while j < n and heights[j] < heights[i]:
        j   = nextTaller[j]   // jump over a block of shorter people
        cnt += 1
    if j == n: cnt -= 1       // we counted one too many
    nextTaller[i] = j
    answer[i]     = cnt
```

The Java, Python, and C++ code differ only in syntax, not in algorithmic substance.

---

## 2.9  Edge‑Case Checklist

| Edge Case | What Happens? | Why It’s Safe |
|-----------|----------------|-------------------|
| `n == 0`  | Returns `[0]`  | No people, no visibility. |
| `n == 1`  | Returns `[0]`  | Last person sees nobody. |
| All heights increasing (e.g., `[1,2,3,4]`) | Each person sees only the neighbour | The `while` loop never runs because `j` is never shorter. |
| All heights decreasing (e.g., `[4,3,2,1]`) | First person sees all others | The `while` loop runs until `j == n`; we subtract one at the end. |

---

## 2.10  Good, Bad, Ugly – What Interviewers Care About

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | O(n) passes all tests quickly. | O(n²) is *unacceptable* on the given limits. | Forgetting to handle the “last person” case can silently give `0` instead of `1`. |
| **Space** | `O(n)` auxiliary arrays (simple to reason). | Too many auxiliary structures can blow up memory on extremely large inputs. | Using a naïve recursion (stack overflow) is an ugly pitfall. |
| **Readability** | Monotonic stack is a textbook pattern, easy to explain. | Obscure bit‑twiddling tricks may hide the underlying logic. | Over‑commenting inside loops can become unreadable; lean but explanatory code is best. |
| **Correctness** | Jumping over *entire* visible blocks guarantees no double‑counting. | Manual `while` logic may accidentally over‑count if the end‑of‑array case is forgotten. | Not handling the “exactly equal height” scenario (although problem guarantees distinct heights) can break other similar problems. |
| **Interview Narrative** | “I realized that once I know the next taller person, all the people in between are visible; hence I can skip them in one jump.” | “I tried a nested loop first but it was too slow.” | “I got lost in a complicated stack maintenance and forgot to update the counter properly.” |

---

## 2.11  Interview Take‑aways

1. **Understand the geometry** – visualize the queue, picture a line of sight, and think in terms of “blocks” of people.
2. **Translate the problem into a monotonic stack** – this is a common pattern for “visibility” or “next greater element” problems.
3. **Explain the *why* of each step** – recruiters love a clear justification for why a block can be skipped.
4. **Mention edge cases** – this demonstrates defensive programming. Even if the input guarantees distinct heights, it’s good to talk about equal‑height variants.
5. **Write clean, minimal code** – you can solve in Python, Java, or C++; the core idea stays the same.

---

## 2.12  Conclusion

> The “Visibility” queue problem is a micro‑cosm of modern algorithm design: it sits at the intersection of combinatorial reasoning and efficient data‑structure usage.  
> Solving it cleanly in O(n) showcases mastery over monotonic stacks and algorithmic elegance – a winning combo for senior software roles.

---

## 2.13  Further Reading & Practice

- **Next Greater Element** – classic LeetCode problem.  
- **Stone Wall (Codility)** – uses a stack to compute wall height changes.  
- **Largest Rectangle in Histogram** – a dynamic programming twist on stack logic.  
- **Sliding Window Maximum** – also relies on a monotonic queue.

---

> **TL;DR:**  
> 1. Recognize “visible blocks” → monotonic decreasing stack.  
> 2. Pre‑compute `nextTaller[i]` while traversing from right to left.  
> 3. Jump over blocks, adjust the counter, and handle the end‑of‑array case.  
> 4. Implement in any language; the algorithmic core stays the same.

--- 

*Happy coding, and may your line‑of‑sight always be clear in every interview!*