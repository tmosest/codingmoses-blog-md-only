---
title: LeetCode 1540. Can Convert String in K Moves - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Way Code Solution for LeetCode 1540 – *Can Convert String in K Moves*

| Language | File name | Complexity |
|----------|-----------|------------|
| **Java** | `Solution.java` | O(n) time, O(1) extra space |
| **Python** | `solution.py` | O(n) time, O(1) extra space |
| **C++** | `solution.cpp` | O(n) time, O(1) extra space |

> **Important:**  
> `k` can be as large as `10^9`, so we need 64‑bit integers (`long` / `long long`) for safety.

---

### 1.1  Java

```java
// 1540. Can Convert String in K Moves
//  Time:  O(n)
//  Space: O(1)
public class Solution {
    public boolean canConvertString(String s, String t, int k) {
        // Strings must be the same length
        if (s.length() != t.length()) return false;

        // Count how many times each shift value (1..25) is needed
        int[] need = new int[26];          // need[d] = # of indices with shift d

        for (int i = 0; i < s.length(); i++) {
            int diff = (t.charAt(i) - s.charAt(i) + 26) % 26;
            if (diff == 0) continue;      // already equal
            // The i‑th occurrence of shift `diff` requires
            //   (need[diff] * 26 + diff) moves in total so far.
            if ((long)need[diff] * 26 + diff > k) return false;
            need[diff]++;
        }
        return true;
    }
}
```

---

### 1.2  Python 3

```python
# 1540. Can Convert String in K Moves
#  Time:  O(n)
#  Space: O(1)

def canConvertString(s: str, t: str, k: int) -> bool:
    if len(s) != len(t):
        return False

    need = [0] * 26   # need[d] = how many indices need shift d

    for sc, tc in zip(s, t):
        diff = (ord(tc) - ord(sc)) % 26
        if diff == 0:
            continue
        if need[diff] * 26 + diff > k:
            return False
        need[diff] += 1
    return True
```

---

### 1.3  C++

```cpp
// 1540. Can Convert String in K Moves
//  Time:  O(n)
//  Space: O(1)

class Solution {
public:
    bool canConvertString(string s, string t, int k) {
        if (s.size() != t.size()) return false;

        vector<long long> need(26, 0);   // need[d] = # of indices needing shift d

        for (size_t i = 0; i < s.size(); ++i) {
            int diff = (t[i] - s[i] + 26) % 26;
            if (diff == 0) continue;
            if (need[diff] * 26 + diff > k) return false;
            ++need[diff];
        }
        return true;
    }
};
```

---

## 2. Blog Article – “LeetCode 1540: The Good, the Bad, and the Ugly”

> **Title:**  
> *LeetCode 1540 – Can Convert String in K Moves: A Deep Dive (Java, Python, C++)*  
> **Meta‑description:**  
> Understand the algorithm behind LeetCode 1540, see clean Java/Python/C++ solutions, learn the pitfalls, and master the interview trick that could land you your dream tech job.

---

### 2.1  Problem Overview

> **Goal** – Convert string `s` into string `t` in **at most `k` moves**.  
> One move: pick an unused index `j`, shift the character at that index `i` times (wrapping from `z` to `a`), or skip a move.  
> Each index may be used **once**.

**Constraints**  
- `1 ≤ |s| = |t| ≤ 10⁵`  
- `0 ≤ k ≤ 10⁹`  
- Only lowercase letters.

---

### 2.2  The “Good” – The Straight‑Forward Insight

1. **Compute the needed shift per index**  
   ```diff = (t[i] - s[i] + 26) % 26```
   If `diff == 0`, no action is needed.

2. **Each shift value `d` can appear many times**  
   The *first* occurrence of shift `d` costs `d` moves.  
   The *second* occurrence of the same `d` must use an additional full cycle of 26 moves because we cannot shift the same index again.  
   In general, the *j‑th* occurrence costs `26*(j-1) + d`.

3. **Greedy check**  
   For each shift value maintain how many times it has appeared so far (`cnt[d]`).  
   Before processing the next occurrence we verify:
   ```cnt[d] * 26 + d <= k```
   If the inequality fails, the total cost will exceed `k` and we can stop early.

The beauty: a single pass over the strings, constant auxiliary space, and **O(n)** time.

---

### 2.3  The “Bad” – Common Pitfalls

| Pitfall | Why it fails | Fix |
|---------|--------------|-----|
| **Using `int` for `k`** | `k` can be 1 billion; intermediate sums `cnt[d] * 26` may overflow | Use 64‑bit (`long` / `long long`) |
| **Subtracting chars without mod** | `t[i] - s[i]` may be negative | Add `+26` then `%26` |
| **Ignoring indices that already match** | Extra work, wrong counting | Skip when `diff == 0` |
| **Treating each index independently** | We forgot that each shift cycle (26) costs extra moves for repeated `diff` | Use `cnt[d] * 26` to account for cycles |
| **Missing the “at most” condition** | Checking only the sum of all diffs is insufficient | Greedy check ensures we never exceed `k` at any point |

---

### 2.4  The “Ugly” – Over‑Engineering

- **Hash maps** to count shifts (works but adds overhead).  
- **Sorting** the shift values and performing binary search.  
- **DP** over all possible move counts (exponential).  
- **Simulation** of every move.

These approaches are unnecessary. The problem boils down to counting shift frequencies and applying the cycle‑cost formula. Keep it simple, keep it fast.

---

### 2.5  Why This Solution Rocks for Interviews

| Feature | Benefit |
|---------|---------|
| **Linear time** | Handles the maximum `10⁵` length comfortably. |
| **Constant space** | Easy to prove and impress. |
| **Handles large `k`** | 64‑bit arithmetic shows attention to edge cases. |
| **Readable** | Few lines, clear variable names, no obscure tricks. |
| **Extensible** | Can be tweaked for variants (e.g., allow multiple shifts per index). |

---

### 2.6  Complete Code Snippets (Java, Python, C++)

*(See section 1 for the full implementations.)*

---

### 2.7  Takeaway & Interview Tips

1. **Ask clarifying questions** – Did the problem say “at most `k` moves” or “exactly `k` moves”?  
2. **Explain the shift cycle** – Emphasize why each repeated shift costs an extra 26 moves.  
3. **Walk through a counterexample** – Show what happens when you ignore the cycle cost.  
4. **State the complexity upfront** – Interviewers love concise summaries.  
5. **Mention edge cases** – Empty strings, `k = 0`, strings already equal.

> **Bonus:** The same technique solves many “string transformation” interview questions. Master it, and you’ll have a solid tool in your algorithm toolbox.

---

### 2.8  Final Thought

LeetCode 1540 is a classic example where a careful observation (the 26‑move cycle) turns a seemingly complex transformation problem into a linear‑time, constant‑space solution. By understanding the **good**, avoiding the **bad**, and ignoring the **ugly**, you can nail the problem and impress interviewers—potentially landing that coveted tech job. Happy coding!