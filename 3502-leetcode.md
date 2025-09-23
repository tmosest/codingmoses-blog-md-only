---
title: LeetCode 3502. Minimum Cost to Reach Every Position - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code Solutions

Below are three idiomatic solutions for LeetCode #3502 **“Minimum Cost to Reach Every Position”**.  
The problem boils down to finding the prefix‑minimum of the `cost` array – the cheapest person you can swap with before you reach a given position.

| Language | Time | Space | Notes |
|----------|------|-------|-------|
| **Java** | `O(n)` | `O(n)` | Uses a simple loop. |
| **Python** | `O(n)` | `O(n)` | One‑liner with `itertools.accumulate`. |
| **C++** | `O(n)` | `O(n)` | Simple loop; no extra STL overhead. |

---

### 1.1 Java

```java
import java.util.*;

class Solution {
    /**
     * Returns the minimum total cost to reach each position in the line.
     *
     * @param cost an array where cost[i] is the fee to swap with person i
     * @return an array answer where answer[i] is the minimum cost to reach i
     */
    public int[] minCosts(int[] cost) {
        int n = cost.length;
        int[] answer = new int[n];
        int currentMin = Integer.MAX_VALUE;

        for (int i = 0; i < n; ++i) {
            currentMin = Math.min(currentMin, cost[i]); // prefix minimum
            answer[i] = currentMin;
        }
        return answer;
    }
}
```

---

### 1.2 Python (one‑liner)

```python
from itertools import accumulate
from typing import List

class Solution:
    def minCosts(self, cost: List[int]) -> List[int]:
        # accumulate with `min` as the binary operator gives the prefix‑minimum
        return list(accumulate(cost, min))
```

*Why it works:* `accumulate(cost, min)` keeps the running minimum as it walks through `cost`.  
The result is exactly the array of cheapest costs to reach each index.

---

### 1.3 C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> minCosts(const std::vector<int>& cost) {
        std::vector<int> answer;
        answer.reserve(cost.size());

        int currentMin = INT_MAX;
        for (int c : cost) {
            currentMin = std::min(currentMin, c);   // prefix minimum
            answer.push_back(currentMin);
        }
        return answer;
    }
};
```

---

## 2.  Blog Article – “The Good, the Bad, and the Ugly of LeetCode 3502”

### Title (SEO‑friendly)
**LeetCode 3502 – Minimum Cost to Reach Every Position: Java, Python, C++ Solutions & Interview‑Ready Tips**

---

### 1. Problem Recap

You’re standing at the **end of a line** of `n+1` people (positions `0 … n`).  
Each person `i` charges `cost[i]` to swap places **if they’re ahead of you**.  
People behind you swap for free.  

> **Goal**: For every position `i` (`0 ≤ i < n`), compute the minimum total cost needed to reach `i` from the end.

*Example*

| cost | Output |
|------|--------|
| `[5,3,4,1,3,2]` | `[5,3,3,1,1,1]` |

---

### 2. Intuition – The “Prefix‑Minimum” Insight

Because you can always wait for a cheaper person to appear before you reach a target, **the cost to reach position `i` is simply the cheapest `cost` among all people in front of or at `i`.**  

In other words:

```
answer[i] = min(cost[0], cost[1], …, cost[i])
```

No dynamic programming or graph traversal is needed – just a running minimum.

---

### 3. “The Good”

| Why It’s Great | What It Gives You |
|----------------|-------------------|
| **O(n) time & O(n) space** | Even for `n = 100`, this is instant. |
| **One‑liner in Python** | Showcases mastery of `itertools` and functional style. |
| **Clear logic** | No tricky corner cases – just a prefix min. |
| **Reproducible in any language** | You can write the same algorithm in Java, C++, Go, Rust, etc. |

---

### 4. “The Bad”

| Potential Pitfall | Why It’s Bad |
|-------------------|--------------|
| Mis‑reading “behind you” as “behind you in the array” | Might think you can pay people behind you – the rule is that those swaps are free, so you *only* pay the cheapest in front. |
| Forgetting the “prefix” part | A naive solution might try to compute a DP for every position independently, leading to O(n²). |
| Over‑engineering | Some candidates write a full DP table or a priority‑queue simulation – unnecessary overhead. |

---

### 5. “The Ugly”

| Why It Looks Ugly | How to Clean It Up |
|-------------------|--------------------|
| **Verbose loops** in Java or C++ that allocate temporary arrays | Use a single pass with `Math.min` (Java) or `std::min` (C++). |
| Re‑implementing prefix min from scratch each interview | Memorize the simple pattern: `currentMin = min(currentMin, cost[i])`. |
| Relying on a black‑box library in Python | Show the one‑liner first, then provide a manual loop version for clarity. |

---

### 6. Implementation Walk‑through (Java)

1. **Initialize** `currentMin` to `Integer.MAX_VALUE`.
2. **Loop** over `cost` indices.
3. **Update** `currentMin = Math.min(currentMin, cost[i])`.
4. **Store** `currentMin` into the answer array.
5. **Return** the answer.

```java
int currentMin = Integer.MAX_VALUE;
for (int i = 0; i < n; ++i) {
    currentMin = Math.min(currentMin, cost[i]);
    answer[i] = currentMin;
}
```

*Why this is clean*: One line inside the loop, no nested structures, constant auxiliary variables.

---

### 7. Python One‑liner

```python
return list(accumulate(cost, min))
```

*Explanation*: `accumulate` keeps a running state – here, the minimum. Each step takes the previous minimum and the next `cost`, returning a new minimum.

---

### 8. C++ Simple Loop

```cpp
int currentMin = INT_MAX;
for (int c : cost) {
    currentMin = std::min(currentMin, c);
    answer.push_back(currentMin);
}
```

The `for‑range` syntax keeps it concise while still being readable.

---

### 9. Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Prefix‑Minimum (all languages) | **O(n)** | **O(n)** (output array) |

The constant factor is negligible – a single comparison per element.

---

### 10. Interview Tips

1. **Explain the insight early** – “the cost to reach `i` is the cheapest person ahead of you”.
2. **Show the one‑liner** if the interviewer asks for the simplest code; otherwise present the clear loop.
3. **Mention edge cases**: `n = 1`, all costs equal, strictly decreasing costs.
4. **Be ready to justify** that no other DP or graph traversal is needed.
5. **Highlight time/space** – interviewer loves `O(n)` solutions for `n ≤ 100`.

---

### 11. Take‑away

*LeetCode 3502 is all about the prefix‑minimum trick.*  
- **Good** – O(n) simple.  
- **Bad** – misunderstand the “behind you” rule.  
- **Ugly** – over‑engineering or verbose loops.

Master the one‑liner in Python, the loop in Java, and the range loop in C++; you’ll impress any hiring manager on a front‑end or back‑end technical interview.

---

## 12. Keywords & Meta‑Data (SEO)

| Keyword | Reason |
|---------|--------|
| LeetCode 3502 | Direct reference |
| Minimum Cost to Reach Every Position | Problem title |
| Java solution | Language tag |
| Python one‑liner | Python optimization |
| C++ prefix minimum | C++ pattern |
| Algorithm interview | Job interview |
| Front‑end coding interview | Target audience |
| DP vs Prefix Minimum | Comparative insight |
| Time complexity O(n) | Performance |

**Meta‑description (≈155 chars):**  
“LeetCode 3502 – Minimum Cost to Reach Every Position. Java, Python one‑liner, C++ solutions + interview tips. Understand the prefix‑minimum trick and ace your coding interview.”

---