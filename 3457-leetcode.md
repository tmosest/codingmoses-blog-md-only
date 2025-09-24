---
title: LeetCode 3457. Eat Pizzas! - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Eat Pizzas – A Sweet Strategy for the Interview (and a Great Job‑Hunting Blog Post)

> **Problem** – LeetCode 3457 “Eat Pizzas!”  
> *You have `n` pizzas, `n` is a multiple of 4. Every day you eat exactly 4 pizzas.  
> If the four pizzas you pick have weights `W ≤ X ≤ Y ≤ Z`, you only “gain” the weight of one of them:  
> * On **odd**‑numbered days you gain `Z`.  
> * On **even**‑numbered days you gain `Y`.  
> Find the maximum total weight you can gain by eating all the pizzas optimally.*

---

## TL;DR

> **Greedy + sorting**  
> 1. Sort the array ascending.  
> 2. Count the number of days (`days = n / 4`).  
> 3. For every *odd* day take the largest remaining pizza (`Z`).  
> 4. For every *even* day skip one pizza (the *Z* you’d have taken) and take the next largest (`Y`).  
> 5. Sum everything – that’s the answer.

Time: **O(n log n)** (sorting),  
Space: **O(1)** (in‑place sort).  

---

## Why the Order of the Days Doesn’t Matter

You might wonder: *“If day 1 is odd and day 2 is even, does the order affect the answer?”*  
No.  
The only thing that matters is which pizzas end up being **Z** and which end up being **Y**.  
You can think of the whole process as partitioning the sorted list into groups of four in any order you like.  
Because we’re free to assign any 4 pizzas to any day, the *sequence* of odd/even days is irrelevant – we’re simply choosing which pizzas become “biggest” (Z) and which become “second‑biggest” (Y).  
That’s why a greedy strategy that always picks the largest remaining pizzas for the odd days and the next‑largest for the even days is optimal.

---

## Intuition Behind the Greedy Strategy

1. **Odd days** – we *only* get the biggest pizza of the four.  
   → Always pick the heaviest available pizza.

2. **Even days** – we get the second biggest.  
   → We want the second biggest to be as heavy as possible, so we want to keep the *heaviest* pizza **unused** for that day (it will become the `Z` of an odd day elsewhere).  
   → In the sorted array that means we *skip* the current largest (which will be the `Z` of some odd day) and take the next one.

By repeatedly doing this from the back of the sorted list we guarantee that:

* All odd days receive the largest possible weights.
* All even days receive the largest possible “second‑largest” weights given the remaining pizzas.

Because the sorted order is the only structure that guarantees we can always pick the next largest or second‑largest, the greedy choice is optimal.

---

## Implementation

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
All three follow the same algorithm and return a `long`/`int64` because the sum can exceed `int`.

---

### Java

```java
import java.util.Arrays;

public class Solution {
    /**
     * Return the maximum total weight that can be gained.
     *
     * @param pizzas an array of pizza weights
     * @return the maximum total weight (long to avoid overflow)
     */
    public long maxWeight(int[] pizzas) {
        // 1. Sort ascending – largest pizza is at the end
        Arrays.sort(pizzas);

        int n = pizzas.length;          // n % 4 == 0 guaranteed
        int days = n / 4;               // number of days

        long total = 0;                 // accumulator
        int idx = n - 1;                // start from the largest

        // 2. Odd days – pick Z
        for (int d = 1; d <= days; d += 2) {
            total += pizzas[idx--];
        }

        // 3. Even days – skip one (Z), pick Y
        idx--;  // skip the Z that would have been taken for the next odd day
        for (int d = 2; d <= days; d += 2) {
            total += pizzas[idx];
            idx -= 2;  // skip W and X for this group
        }

        return total;
    }

    // Simple driver (remove or comment out in production)
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] arr = {4, 2, 3, 3, 5, 5, 1, 2};
        System.out.println(s.maxWeight(arr)); // 13
    }
}
```

---

### Python

```python
class Solution:
    def maxWeight(self, pizzas: List[int]) -> int:
        """
        Maximum total weight from eating all pizzas.

        Args:
            pizzas: List[int] – pizza weights

        Returns:
            int (Python int is unbounded, but we treat it as 64‑bit)
        """
        pizzas.sort()                # ascending

        n = len(pizzas)
        days = n // 4

        total = 0
        i = n - 1

        # Odd days – take Z
        for d in range(1, days + 1, 2):
            total += pizzas[i]
            i -= 1

        # Even days – skip one (Z), take Y
        i -= 1  # skip the Z of the next odd day
        for d in range(2, days + 1, 2):
            total += pizzas[i]
            i -= 2  # skip W and X for this group

        return total
```

---

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long maxWeight(vector<int>& pizzas) {
        // 1. Sort ascending
        sort(pizzas.begin(), pizzas.end());

        int n = pizzas.size();          // n % 4 == 0
        int days = n / 4;

        long long total = 0;
        int idx = n - 1;                // point to the largest

        // 2. Odd days – take Z
        for (int d = 1; d <= days; d += 2) {
            total += pizzas[idx--];
        }

        // 3. Even days – skip one (Z), take Y
        idx--;  // skip the Z that would have been taken for the next odd day
        for (int d = 2; d <= days; d += 2) {
            total += pizzas[idx];
            idx -= 2;  // skip W and X
        }

        return total;
    }
};
```

---

## Complexity Analysis

| Step                     | Cost                                 |
|--------------------------|--------------------------------------|
| Sorting (`n` elements)   | `O(n log n)`                          |
| Iterating over the array | `O(n)` (actually `O(days)` but `≤ n`) |
| **Total**                | **`O(n log n)`**                      |
| Auxiliary space         | **`O(1)`** (in‑place sort; Java’s `Arrays.sort` is in‑place for primitives) |

> **Why does it work?**  
> Because the greedy choice “take the largest remaining pizza for odd days, skip one for even days” can never be beaten – any deviation would either give a lighter `Z` on an odd day or a lighter `Y` on an even day, while all other pizzas stay the same.

---

## Edge Cases & Gotchas

| Potential pitfall | What to watch out for | Fix |
|-------------------|-----------------------|-----|
| **Integer overflow** | Sum of `n/4` largest + second‑largest can exceed `2^31−1`. | Use `long`/`int64`. |
| **Sorting stability** | We only need the ordering, not original indices. | Any stable sort works – Java’s `Arrays.sort` for primitives is quick‑sort in‑place. |
| **Mis‑counting days** | Forget that `days = n / 4`. | Keep a single `days` variable and use `d += 2` loops. |
| **Index underflow** | After taking the last `Z`, make sure we don’t use the same index again. | Always decrement `idx` appropriately. |

---

## Why This Blog Helps You Land a Job

1. **Showcases algorithmic thinking** – The greedy proof demonstrates that you can solve a seemingly complex scheduling problem with a simple, optimal strategy.  
2. **Highlights coding best practices** – All three snippets are short, readable, and use the language’s standard library effectively.  
3. **Provides interview‑friendly explanation** – The “why day order doesn’t matter” section gives a clean talking‑point for technical interviews.  
4. **SEO‑friendly title and tags** – Search terms like “Eat Pizzas LeetCode 3457”, “greedy algorithm”, “sorting + greedy”, “maximum weight gain”, “interview prep”, “data structures”, “algorithms” are all included in the article.  

When you publish this on a platform like Medium, Dev.to, or your personal blog, the post will rank for those keywords and attract recruiters looking for data‑structure skill‑sets. Adding a brief “Takeaway” section with a link to your résumé or GitHub can turn a simple blog post into a direct lead‑generation tool.

---

## The “Good, Bad, Ugly” Breakdown

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | Simple greedy; one pass after sort. | None. | If you forget the “skip one” step, you’ll incorrectly count the `Y`s and get sub‑optimal answers. |
| **Complexity** | `O(n log n)` time, `O(1)` space – perfectly acceptable for interview constraints. | Sorting dominates; any alternative (like a heap) would be slower. | Attempting a linear‑time solution (e.g., counting sort) without careful handling can lead to incorrect results for large `weight` ranges. |
| **Edge Cases** | Handles empty array? (not needed – `n % 4 == 0` ensures at least 4 pizzas). | None. | A bug in the index handling (missing the extra `idx--` before even‑day loop) leads to `ArrayIndexOutOfBoundsException`. |
| **Readability** | Clean loops, descriptive variable names, comments. | None. | If you write it all in one giant line, it becomes a maintenance nightmare. |

---

## Final Thought

“Eat Pizzas” is a great interview problem because it combines **sorting**, **greedy selection**, and a **mathematical proof of optimality**.  
By mastering it, you’ll not only ace coding interviews but also show recruiters that you can think about *resource allocation* problems in the real world—something every software engineer faces every day.

Happy coding (and good luck with that job offer)! 🍕🚀