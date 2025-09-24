---
title: LeetCode 2554. Maximum Number of Integers to Choose From a Range I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 2554 – **Maximum Number of Integers to Choose From a Range I**  
### The Good, The Bad, and The Ugly – A Job‑Ready Guide  

| Section | Description |
|---------|-------------|
| **Problem** | Given a list of *banned* integers, a maximum value `n`, and a budget `maxSum`, pick the largest possible number of distinct integers from the range `[1, n]` that are **not** banned and whose total sum does not exceed `maxSum`. |
| **Target Audience** | Front‑end / back‑end engineers, algorithm interview candidates, and anyone looking to ace a LeetCode‑style coding interview. |
| **Core Concepts** | Greedy, boolean array / hash‑set, time‑space trade‑off. |
| **Languages** | Java, Python, C++ (code snippets provided). |

> **SEO Keywords**: LeetCode 2554, Maximum Number of Integers to Choose From a Range, array solution, Java, Python, C++, greedy algorithm, interview prep, coding interview, algorithmic thinking, job interview tips.

---

### 1. Problem Restatement

You’re given:
- `int[] banned` – a list of numbers you cannot choose.
- `int n` – the upper bound of the range `[1, n]`.
- `int maxSum` – the maximum sum you are allowed to accumulate.

Return the **maximum count** of distinct integers you can pick that satisfy:
- Each chosen number ∈ `[1, n]`.
- Each chosen number is *not* in `banned`.
- The sum of chosen numbers ≤ `maxSum`.

---

### 2. Intuition

The optimal strategy is *greedy*:  
Pick the smallest allowed numbers first.  
Why?  
- The smallest numbers consume the least of the budget, letting you add more numbers before hitting `maxSum`.
- Since every number can be taken at most once, there is no advantage to skipping a small number in favor of a larger one.

Thus, we can:
1. Mark all banned numbers.
2. Iterate from `1` to `n` in increasing order, skipping banned numbers.
3. Keep a running sum; stop as soon as adding the next number would exceed `maxSum`.

---

### 3. Edge Cases

| Situation | Expected Result |
|-----------|-----------------|
| All numbers are banned. | `0` |
| `maxSum` is smaller than the smallest allowed number. | `0` |
| `maxSum` is huge (e.g., `10^9`) and `n` is small. | All allowed numbers can be taken. |
| `banned` contains numbers outside `[1, n]`. | They are irrelevant. |
| `n` is up to `10^4`, `banned.length` up to `10^4`. | Still linear time. |

---

### 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Marking banned numbers | `O(banned.length)` | `O(n)` (boolean array of size `n+1`) |
| Greedy scan | `O(n)` | – |
| **Total** | **`O(n + banned.length)`** | **`O(n)`** (constant relative to constraints, ~10⁴) |

Because `n` ≤ `10⁴`, a simple boolean array is fast and easy to reason about. If `n` were larger (e.g., `10⁹`), we’d switch to a hash‑set of banned numbers and iterate only over allowed numbers, but that is unnecessary here.

---

### 5. Code Implementations

> **Tip:** Use `long` for the running sum to avoid overflow when `maxSum` is close to `10⁹`.

---

#### 5.1 Java

```java
import java.util.*;

class Solution {
    public int maxCount(int[] banned, int n, int maxSum) {
        // Mark banned numbers in a boolean array
        boolean[] bannedArr = new boolean[n + 1];
        for (int num : banned) {
            if (num <= n) bannedArr[num] = true;
        }

        long sum = 0;
        int count = 0;

        // Greedy: pick smallest allowed numbers
        for (int i = 1; i <= n; i++) {
            if (bannedArr[i]) continue;          // skip banned
            if (sum + i > maxSum) break;         // budget exceeded
            sum += i;
            count++;
        }
        return count;
    }
}
```

---

#### 5.2 Python

```python
class Solution:
    def maxCount(self, banned: List[int], n: int, maxSum: int) -> int:
        banned_set = set(banned)  # O(1) look‑ups
        sum_val = 0
        count = 0

        for i in range(1, n + 1):
            if i in banned_set:
                continue
            if sum_val + i > maxSum:
                break
            sum_val += i
            count += 1
        return count
```

> **Why a `set`?**  
> For `n ≤ 10⁴`, both array and set are fine; a set keeps the code simple and works if `n` grows.

---

#### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int maxCount(vector<int>& banned, int n, int maxSum) {
        // Mark banned numbers
        vector<bool> bannedArr(n + 1, false);
        for (int x : banned)
            if (x <= n) bannedArr[x] = true;

        long long sum = 0;
        int count = 0;

        for (int i = 1; i <= n; ++i) {
            if (bannedArr[i]) continue;
            if (sum + i > maxSum) break;
            sum += i;
            ++count;
        }
        return count;
    }
};
```

---

### 6. “The Good, The Bad, The Ugly” of This Problem

| Aspect | What Makes It **Good** | What Makes It **Bad** | What Makes It **Ugly** |
|--------|-----------------------|-----------------------|------------------------|
| **Simplicity** | Easy to understand; greedy proof is straightforward. | The problem’s wording can be a bit verbose; you must parse carefully. | The naive solution might iterate over all numbers even when many are banned, leading to unnecessary work. |
| **Scalability** | Linear time works for the given constraints (`n ≤ 10⁴`). | For much larger `n` you’d need a more clever data structure (e.g., segment tree). | Forgetting to use a 64‑bit sum can cause subtle overflows. |
| **Testing** | A handful of corner cases cover almost all failure modes. | Requires handling both banned numbers outside the range and extreme budgets. | Some interviewers may add hidden constraints (e.g., multiple test cases in a single run). |
| **Interview Value** | Demonstrates greedy reasoning, set/array manipulation, careful edge‑case handling. | It’s not a classic DP or graph problem; might be considered “too easy” for senior roles. | The solution can be “too short” for interviewers who expect deeper discussion (e.g., why greedy is optimal). |

**Takeaway for Interviews:**  
Explain the greedy intuition, sketch the proof that picking the smallest available numbers is optimal, and discuss the time/space trade‑offs. This demonstrates not just coding skill but also algorithmic thinking.

---

### 7. SEO‑Optimized Summary

> **LeetCode 2554** – *Maximum Number of Integers to Choose From a Range I*  
> Learn a clean greedy solution in **Java, Python, and C++**.  
> Master array vs. hash‑set techniques, handle edge cases, and get interview‑ready.  
> Ready for your next coding interview? Practice this problem and add it to your portfolio.  

---

#### Quick Reference

```text
Input: banned = [1,6,5], n = 5, maxSum = 6
Output: 2  // choose 2 and 4

Input: banned = [1,2,3,4,5,6,7], n = 8, maxSum = 1
Output: 0  // nothing fits

Input: banned = [11], n = 7, maxSum = 50
Output: 7  // choose 1..7, sum=28
```

Happy coding, and good luck landing that job!