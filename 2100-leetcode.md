---
title: LeetCode 2100. Find Good Days to Rob the Bank - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ 3‑Language Solution – Find Good Days to Rob the Bank (LeetCode 2100)

Below you’ll find a clean, **O(n)** algorithm in **Java**, **Python**, and **C++**.  
All three implementations use the same two‑pass prefix‑suffix technique:

1. **decr[i]** – number of consecutive non‑increasing days ending at *i* (look left → right).  
2. **incr[i]** – number of consecutive non‑decreasing days starting at *i* (look right → left).  

A day *i* is “good” iff both counters are ≥ `time`.

> 💡 **Why this is the best approach?**  
> The brute‑force sliding window would be **O(n·time)**, which can blow up when `time ≈ 10⁵`.  
> The prefix‑suffix method gives a linear scan and is therefore optimal.

---

### Java

```java
import java.util.*;

public class Solution {
    public List<Integer> goodDaysToRobBank(int[] security, int time) {
        int n = security.length;
        int[] decr = new int[n];   // non‑increasing prefix
        int[] incr = new int[n];   // non‑decreasing suffix

        // left → right : count consecutive non‑increasing days
        for (int i = 1; i < n; i++) {
            if (security[i] <= security[i - 1]) {
                decr[i] = decr[i - 1] + 1;
            } else {
                decr[i] = 0;
            }
        }

        // right → left : count consecutive non‑decreasing days
        for (int i = n - 2; i >= 0; i--) {
            if (security[i] <= security[i + 1]) {
                incr[i] = incr[i + 1] + 1;
            } else {
                incr[i] = 0;
            }
        }

        List<Integer> result = new ArrayList<>();
        for (int i = time; i < n - time; i++) {
            if (decr[i] >= time && incr[i] >= time) {
                result.add(i);
            }
        }
        return result;
    }
}
```

---

### Python

```python
from typing import List

class Solution:
    def goodDaysToRobBank(self, security: List[int], time: int) -> List[int]:
        n = len(security)
        decr = [0] * n          # non‑increasing prefix
        incr = [0] * n          # non‑decreasing suffix

        # left → right
        for i in range(1, n):
            decr[i] = decr[i-1] + 1 if security[i] <= security[i-1] else 0

        # right → left
        for i in range(n-2, -1, -1):
            incr[i] = incr[i+1] + 1 if security[i] <= security[i+1] else 0

        return [i for i in range(time, n-time)
                if decr[i] >= time and incr[i] >= time]
```

---

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> goodDaysToRobBank(vector<int>& security, int time) {
        int n = security.size();
        vector<int> decr(n, 0), incr(n, 0);

        // left → right: non‑increasing prefix
        for (int i = 1; i < n; ++i)
            decr[i] = (security[i] <= security[i-1]) ? decr[i-1] + 1 : 0;

        // right → left: non‑decreasing suffix
        for (int i = n-2; i >= 0; --i)
            incr[i] = (security[i] <= security[i+1]) ? incr[i+1] + 1 : 0;

        vector<int> ans;
        for (int i = time; i < n - time; ++i)
            if (decr[i] >= time && incr[i] >= time)
                ans.push_back(i);

        return ans;
    }
};
```

---

## 📚 Blog Article – “The Good, The Bad, and The Ugly: Mastering LeetCode 2100”

### Introduction

> **Problem:** *Find Good Days to Rob the Bank* – a medium‑level challenge that tests your ability to reason about array prefixes, suffixes, and sliding windows.  
> **Goal:** Deliver a concise, **O(n)** solution that passes all edge cases.

In this post we’ll explore:

1. The problem’s core mechanics.  
2. A *good* linear strategy.  
3. Common pitfalls (*the bad*).  
4. The subtle trick that makes the algorithm robust (*the ugly*).  

And we’ll pepper the article with **SEO keywords** such as *LeetCode 2100*, *array prefix*, *dynamic programming*, *interview coding*, *Java Python C++ solutions* so you can get found on Google and land that job!

---

### 1️⃣ Understanding the Good

A *good day* requires **two symmetric patterns**:

- **Non‑increasing** guards for `time` days before `i`.  
- **Non‑decreasing** guards for `time` days after `i`.

The most naïve solution would check every candidate day with two loops, costing **O(n·time)**. When `time` is as large as `10⁵`, this becomes infeasible.

**Key insight:** The two patterns can be pre‑computed with two linear passes:

| Pass | Direction | What we compute | Why it works |
|------|-----------|-----------------|--------------|
| 1 | left → right | `decr[i]`: longest non‑increasing streak ending at `i` | If the streak length ≥ `time`, we already know the “before” part is fine. |
| 2 | right → left | `incr[i]`: longest non‑decreasing streak starting at `i` | Symmetric reasoning for the “after” part. |

Once both arrays exist, a simple scan determines all good days in **O(n)** time.

---

### 2️⃣ The Bad – Common Mistakes

| Mistake | Why it fails | Fix |
|---------|--------------|-----|
| **Using `>=` incorrectly** | Some implementations compare `security[i] < security[i-1]` instead of `<=`. This discards equal guard counts, which are allowed by the problem. | Use `<=` for the left‑to‑right pass and `<=` for the right‑to‑left pass. |
| **Boundary errors** | Forgetting to exclude days that don’t have `time` days before or after (`i < time` or `i > n-1-time`). | Run the final loop from `i = time` to `i < n - time`. |
| **O(n²) approach** | Brute‑force sliding window, recomputing sums each time. | Precompute prefix and suffix arrays instead of recomputing. |
| **Large memory waste** | Using a separate array for each guard trend, but not reusing them. | Only two `int` arrays of length `n` are needed; keep them local. |
| **Ignoring time = 0** | Returning an empty list because `time` is never checked. | If `time == 0`, all indices are valid – the loop naturally handles this. |

---

### 3️⃣ The Ugly – Edge Cases You Must Handle

1. **All equal values**  
   ```
   security = [5,5,5,5], time = 1
   ```
   Both `decr` and `incr` become `0,1,2,…`. Our logic (`>= time`) still works.

2. **Very small arrays**  
   ```
   security = [1], time = 0
   ```
   The algorithm gracefully returns `[0]` because the loop runs from `i=0` to `i < 1-0`.

3. **Large `time` approaching array length**  
   ```
   security = [1,2,3,4,5], time = 4
   ```
   No day satisfies the condition; the loop’s bounds (`i < n - time`) prevent any index from being considered.

4. **Negative guard counts?**  
   The problem guarantees non‑negative integers, but our algorithm would still work because comparisons rely only on order, not absolute value.

---

### 4️⃣ Putting It All Together – A Robust Implementation

Here’s the final, production‑ready Java code (you can copy‑paste it into a LeetCode editor):

```java
import java.util.*;

public class Solution {
    public List<Integer> goodDaysToRobBank(int[] security, int time) {
        int n = security.length;
        int[] decr = new int[n];   // longest non‑increasing prefix ending at i
        int[] incr = new int[n];   // longest non‑decreasing suffix starting at i

        for (int i = 1; i < n; i++) {
            decr[i] = security[i] <= security[i-1] ? decr[i-1] + 1 : 0;
        }

        for (int i = n-2; i >= 0; i--) {
            incr[i] = security[i] <= security[i+1] ? incr[i+1] + 1 : 0;
        }

        List<Integer> res = new ArrayList<>();
        for (int i = time; i < n - time; i++) {
            if (decr[i] >= time && incr[i] >= time) {
                res.add(i);
            }
        }
        return res;
    }
}
```

The same pattern translates to Python (list comprehensions) and C++ (`vector<int>`), as shown earlier.

---

### 4️⃣ Why This Matters for Your Interview

| Skill | How it Appears in the Solution |
|-------|--------------------------------|
| **Array manipulation** | Two linear scans, careful index arithmetic. |
| **Time & space complexity** | Mastery of **O(n)** vs. **O(n²)** trade‑offs. |
| **Edge‑case thinking** | Handling `time == 0`, very short arrays, large streaks. |
| **Code readability** | Clear variable names (`decr`, `incr`), comments, and no magic numbers. |
| **Language versatility** | Showcasing identical logic in Java, Python, and C++ demonstrates adaptability—a key interview trait. |

---

### 📈 SEO Boost – How to Get Found

- **Title:** “LeetCode 2100 – Find Good Days to Rob the Bank (Java / Python / C++ O(n) Solution)”  
- **Meta Description:** “Learn a clean, linear solution to LeetCode 2100. Understand prefixes, suffixes, common pitfalls, and edge cases. Boost your coding interview skills.”  
- **Key Phrases:** *LeetCode 2100 solution*, *array prefix dynamic programming*, *rob the bank interview problem*, *Java O(n) algorithm*, *Python suffix array*, *C++ prefix‑suffix trick*, *interview coding strategy*.

Add these phrases naturally to your headings and throughout the post. If you host the article on a blog platform, add them to the **tags** and **categories** as well.

---

### 🎯 Final Takeaway

- **Good:** Linear prefix‑suffix technique.  
- **Bad:** Common indexing and comparison errors.  
- **Ugly:** Rare but critical edge cases (time close to array length, all equal values).  

With the three code snippets, you can drop this solution into any interview or coding test. Add the article to your portfolio, share it on LinkedIn, or even include it in your GitHub README. The combination of **high‑quality code** and **SEO‑friendly content** will help you stand out to recruiters looking for problem‑solving chops.

Happy coding, and may your next interview feel like a *heist with zero casualties*! 🚀