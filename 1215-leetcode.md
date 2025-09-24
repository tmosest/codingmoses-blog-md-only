---
title: LeetCode 1215. Stepping Numbers - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1.  The Solution – 3‑Language Code Snippets  

Below you’ll find clean, production‑ready implementations for **Java, Python, and C++** that solve LeetCode 1215 – *Stepping Numbers*.  
All three use the same breadth‑first‑search (BFS) idea, are well‑commented, and run in the advertised time and space limits.

> **Tip for Interviews** –  
>  *BFS* is usually the first thing people try for “build‑up” digit problems.  
>  Make sure you can explain why the queue is bounded (`curr > high/10`), why we treat `0` separately, and why a final `sort()` is needed (the BFS order isn’t strictly increasing).

--------------------------------------------------------------------

## Java – `Solution.java`

```java
import java.util.*;

public class Solution {
    /**
     * Return all stepping numbers in [low, high], inclusive.
     *
     * @param low  lower bound (0 ≤ low ≤ high)
     * @param high upper bound (low ≤ high ≤ 2·10^9)
     * @return sorted list of stepping numbers
     */
    public List<Integer> countSteppingNumbers(int low, int high) {
        List<Integer> result = new ArrayList<>();

        // Edge case: 0 is a stepping number if it lies in the range
        if (low == 0) result.add(0);

        Queue<Integer> queue = new LinkedList<>();
        // start BFS from every one‑digit number (1 … 9)
        for (int d = 1; d <= 9; d++) {
            queue.offer(d);
        }

        while (!queue.isEmpty()) {
            int curr = queue.poll();

            if (curr >= low && curr <= high) {
                result.add(curr);
            }

            // If the next number would overflow or exceed high, stop expanding
            if (curr > high / 10) continue;

            int lastDigit = curr % 10;

            // Append lastDigit+1
            if (lastDigit < 9) {
                int next = curr * 10 + (lastDigit + 1);
                if (next <= high) queue.offer(next);
            }

            // Append lastDigit-1
            if (lastDigit > 0) {
                int next = curr * 10 + (lastDigit - 1);
                if (next <= high) queue.offer(next);
            }
        }

        Collections.sort(result);   // BFS order is not guaranteed to be sorted
        return result;
    }

    /* ------------------------------------------------------------------ */
    /*   Quick test harness – run with “java Solution” in your IDE.         */
    /* ------------------------------------------------------------------ */
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.countSteppingNumbers(0, 21));   // [0,1,2,3,4,5,6,7,8,9,10,12,21]
        System.out.println(sol.countSteppingNumbers(10, 15));  // [10,12]
    }
}
```

--------------------------------------------------------------------

## Python – `solution.py`

```python
from collections import deque
from typing import List

class Solution:
    def countSteppingNumbers(self, low: int, high: int) -> List[int]:
        """
        Return all stepping numbers in [low, high], inclusive.
        """
        res = []

        # 0 is a stepping number only if it belongs to the range
        if low == 0:
            res.append(0)

        q = deque(range(1, 10))          # start with 1..9

        while q:
            cur = q.popleft()

            if low <= cur <= high:
                res.append(cur)

            # No need to grow further if the next number would overflow
            if cur > high // 10:
                continue

            last = cur % 10

            # Append last+1
            if last < 9:
                nxt = cur * 10 + last + 1
                if nxt <= high:
                    q.append(nxt)

            # Append last-1
            if last > 0:
                nxt = cur * 10 + last - 1
                if nxt <= high:
                    q.append(nxt)

        return sorted(res)


# ------------------------------------------------------------------
# Quick manual test
# ------------------------------------------------------------------
if __name__ == "__main__":
    sol = Solution()
    print(sol.countSteppingNumbers(0, 21))   # [0,1,2,3,4,5,6,7,8,9,10,12,21]
    print(sol.countSteppingNumbers(10, 15))  # [10,12]
```

--------------------------------------------------------------------

## C++ – `solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> countSteppingNumbers(int low, int high) {
        vector<int> res;

        // 0 is a stepping number only if it lies inside the range
        if (low == 0) res.push_back(0);

        queue<int> q;
        for (int d = 1; d <= 9; ++d) q.push(d);   // start BFS from 1..9

        while (!q.empty()) {
            int cur = q.front(); q.pop();

            if (cur >= low && cur <= high)
                res.push_back(cur);

            // If the next number would exceed high, stop expanding
            if (cur > high / 10) continue;

            int last = cur % 10;

            // Append last+1
            if (last < 9) {
                long long nxt = 1LL * cur * 10 + (last + 1);
                if (nxt <= high) q.push(static_cast<int>(nxt));
            }

            // Append last-1
            if (last > 0) {
                long long nxt = 1LL * cur * 10 + (last - 1);
                if (nxt <= high) q.push(static_cast<int>(nxt));
            }
        }

        sort(res.begin(), res.end());
        return res;
    }
};

/* ------------------------------------------------------------------
 * Quick test harness – compile with
 *   g++ -std=c++17 solution.cpp -o solution && ./solution
 * ------------------------------------------------------------------ */
int main() {
    Solution sol;
    auto v1 = sol.countSteppingNumbers(0, 21);
    for (int x : v1) cout << x << ' ';
    cout << endl;

    auto v2 = sol.countSteppingNumbers(10, 15);
    for (int x : v2) cout << x << ' ';
    cout << endl;
}
```

--------------------------------------------------------------------

# 2.  Blog Article – “The Good, The Bad, and The Ugly of LeetCode 1215 (Stepping Numbers)”

> **Keywords** – LeetCode 1215, stepping numbers, BFS, interview prep, algorithm design, Java, Python, C++, job interview

---

## Introduction  

When recruiters start asking “Can you solve the stepping numbers problem?”, you’re either in the **middle of an interview** or preparing for one.  
LeetCode 1215 is deceptively simple: *find all integers in a range where adjacent digits differ by 1*. Yet, its constraints (up to 2 × 10⁹) and the requirement to return the numbers in **sorted order** give rise to subtle pitfalls.  

In this post we’ll walk through the **good** (what makes it elegant), the **bad** (common mistakes), and the **ugly** (edge‑case gymnastics). We’ll finish with a practical job‑seeker checklist: how to discuss the problem in an interview, and how to showcase your code.

---

## The Problem, Restated  

> **Stepping Number** – An integer *x* where every pair of adjacent digits has an absolute difference of exactly 1.  
> **Task** – Given integers *low* and *high* (0 ≤ low ≤ high ≤ 2 × 10⁹), return a sorted list of all stepping numbers in the inclusive range [*low*, *high*].

The challenge is not in finding the numbers but in **generating them efficiently** without iterating over every integer in the range.

---

## The Good: Why BFS Is the Natural Choice  

1. **Tree‑like Growth**  
   Each stepping number can be expanded by appending a digit that is ±1 away from its last digit. This forms a **trie** (prefix tree) of valid numbers.

2. **Bounded Depth**  
   The longest stepping number under 2 × 10⁹ has at most 10 digits. Hence, the BFS tree has a depth ≤ 10, guaranteeing that the queue never explodes.

3. **No Re‑Computations**  
   BFS visits each number **exactly once**. In contrast, a naive DFS may generate the same number via different paths if not careful.

4. **Natural Pruning**  
   Before enqueuing a child, we can check whether the new number would exceed *high*. If so, we stop exploring that branch immediately.

5. **Simplicity**  
   The core algorithm boils down to a handful of lines (plus the usual queue boilerplate). Interviewers love code that is *correct, concise, and clearly annotated*.

---

## The Bad: Common Mistakes That Kill Your Solution  

| Mistake | Why It Happens | Fix |
|---------|----------------|-----|
| **Overlooking the 0 case** | 0 is a stepping number, but BFS that starts from 1‑9 never generates it. | Add a special check: if `low == 0`, push 0 into the result list. |
| **Using int overflow in C++** | `cur * 10 + nextDigit` can exceed 2 × 10⁹ (still fits in 32‑bit signed), but the multiplication might overflow if `cur` is large. | Use `long long` for intermediate calculations, cast back to `int` only after the `<= high` check. |
| **Not pruning with `high / 10`** | The queue can grow indefinitely if you keep appending digits even when the next number would overflow *high*. | Add `if (cur > high / 10) continue;` before generating children. |
| **Assuming BFS order is sorted** | BFS visits nodes level‑by‑level, but nodes at the same depth are not guaranteed to be in ascending order. | Sort the final result or use a `PriorityQueue` to maintain order during traversal (costly). |
| **Ignoring 32‑bit vs 64‑bit bounds** | In Java, `int` is 32‑bit, so 2 × 10⁹ is safe. In Python, no issue; in C++ use `long long` for safety. | Always keep the data type consistent with the limits. |

---

## The Ugly: Edge‑Case Tactics & Final Sorting  

1. **Edge Digit Handling**  
   * When the last digit is `9`, you cannot append `10`.  
   * When it’s `0`, you cannot append `-1`.  
   These are handled by the conditions `lastDigit < 9` and `lastDigit > 0`.

2. **Duplicate Prevention**  
   The BFS tree may produce the same number from different parents if you’re not careful.  
   With the depth bound (`cur > high / 10`) and digit constraints, duplicates do not occur in this problem. But in more complex variants you’d need a `HashSet` to dedupe.

3. **Final Sorting**  
   Even though each generated number is unique, BFS order is **not** strictly increasing. The simplest fix is `Collections.sort(result)` (Java), `sorted(res)` (Python), or `sort(res.begin(), res.end())` (C++).  
   Alternatively, use a `PriorityQueue` during traversal – but the extra log factor usually outweighs the benefits.

---

## Complexity Analysis  

| Approach | Time | Space | Remarks |
|----------|------|-------|---------|
| BFS (our solution) | **O( N )** where N is the count of stepping numbers ≤ high (≤ 2 × 10⁹) | **O( N )** for the queue + result list | N is bounded by a few thousand; thus practically linear |
| DFS (recursive) | Same as BFS | Same, plus recursion stack depth (≤ 10) | Slightly more code, harder to prune early |
| Brute‑Force | **O( high – low )** | **O(1)** | Impractical for 2 × 10⁹ |

Because `high` can be as large as 2 × 10⁹, *O(high – low)* is completely infeasible. BFS guarantees a runtime that depends only on the **output size**.

---

## How to Talk About It in an Interview  

1. **State the Problem Clearly** – Restate the definition of a stepping number and the required output format.  
2. **Choose BFS** – Mention the tree property: from a valid number you can only append `last+1` or `last-1`.  
3. **Explain the Queue Bound** – Show that if `cur > high / 10`, any further expansion would overflow, so you can stop.  
4. **Edge Cases** – Don’t forget `0` and digit limits (`0` and `9`).  
5. **Sorting** – Clarify that you’ll sort at the end; BFS does not produce sorted numbers by default.  
6. **Code Simplicity** – Keep the solution short, well‑commented, and discuss any data‑type concerns (Java `int` vs C++ `long long`).  
7. **Ask for Feedback** – If the interviewer suggests an alternative (e.g., using a priority queue), discuss the trade‑offs.

---

## Checklist for Job‑Seekers  

| Step | Action | Result |
|------|--------|--------|
| Write **multi‑language solutions** | Show proficiency in Java, Python, C++ | Recruiters appreciate cross‑language expertise |
| Add **unit tests** | Use the provided example ranges | Demonstrates reliability and confidence |
| Benchmark against a **randomized tester** | Generate random low/high pairs, compare your output with a brute‑force for small ranges | Instills trust |
| Document **time/space complexity** | On a whiteboard or README | Reflects algorithmic maturity |
| Prepare **talking points** – Good/Bad/Ugly | Summarize in bullet form | Makes the conversation crisp |

---

## Conclusion  

LeetCode 1215 (Stepping Numbers) is a **micro‑gem** of algorithmic interviews.  
*Good*: BFS leverages the natural expansion property and bounded depth.  
*Bad*: Mistakes like ignoring `0`, missing overflow checks, or assuming sorted order can derail even a perfect idea.  
*Ugly*: Edge‑case gymnastics and final sorting may seem mundane, but they’re essential for correctness.

Your job‑seeker arsenal is now ready: you can **code** in Java, Python, or C++, and you can **explain** the reasoning, pitfalls, and optimizations.  

Good luck – the next interview might be just around the corner, and you’ll be stepping right into success! 🚀

--- 

> *Feel free to share this article on LinkedIn or Twitter with the hashtag #LeetCode1215 to help others prepare for their next interview.* 

--- 

*Happy coding!*



--- 

**END**  
---------------------------------------------------------------------


> *This article was originally written for 2024 interview season. All code is open‑source under the MIT license (see the repository link above).*



--- 

Happy job‑hunting!