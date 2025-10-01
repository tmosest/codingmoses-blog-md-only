---
title: LeetCode 2214. Minimum Health to Beat Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🛡️ Minimum Health to Beat Game – LeetCode 2214  
**Difficulty**: Medium | **Runtime**: O(n) | **Space**: O(1)

> “You are playing a game that has *n* levels numbered from 0 to *n‑1. You’re given a damage array and an armor value. You can use the armor at most once to reduce damage. Find the minimum starting health that lets you finish all levels with health > 0.”

---

## Table of Contents  
1. [Why This Problem Matters for Interviews](#why)  
2. [Problem Recap & Constraints](#recap)  
3. [The Good, The Bad, & The Ugly](#good-bad-ugly)  
4. [Intuitive Greedy Solution](#solution)  
5. [Code in 3 Languages](#code)  
   - Java  
   - Python  
   - C++  
6. [Complexity Analysis](#complexity)  
7. [Edge Cases & Common Pitfalls](#edge-cases)  
8. [Testing Your Solution](#testing)  
9. [Take‑away & How to Talk About It in an Interview](#takeaway)  
10. [SEO Keywords & Meta‑Tags](#seo)  

---

## 1. Why This Problem Matters for Interviews <a name="why"></a>
- **LeetCode 2214** is a *medium* problem that frequently appears in Java/Python/C++ interview stacks for software‑engineering roles.
- It tests a candidate’s ability to:
  - **Reason with greedy algorithms** – pick the optimal spot to use a limited resource (armor).
  - **Handle large inputs efficiently** – `n ≤ 10⁵`.
  - **Use correct data types** – avoid overflow when summing up to `10¹⁰` damage.

If you nail this problem, you’ll show recruiters that you can:

- Think *in linear time*, *constant space*.  
- Convert a problem description into a clean, production‑grade code snippet.  
- Spot subtle bugs (e.g., forgetting the `+1` to keep health strictly positive).

---

## 2. Problem Recap & Constraints <a name="recap"></a>

| Item | Value |
|------|-------|
| `damage` length `n` | 1 … 10⁵ |
| `damage[i]` | 0 … 10⁵ |
| `armor` | 0 … 10⁵ |

**Goal**: Return the *minimum starting health* (an integer) so that after playing all levels in order, the health is always **strictly positive**.

---

## 3. The Good, The Bad, & The Ugly <a name="good-bad-ugly"></a>

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithm** | Greedy, O(n) time, O(1) space | None – the greedy is optimal. | Some naive solutions iterate twice or use a binary search, increasing complexity unnecessarily. |
| **Data Types** | Use 64‑bit (`long`, `long long`) for cumulative damage | 32‑bit integers can overflow (`10⁵ * 10⁵ = 10¹⁰ > 2³¹`). | Overlooking this leads to WA on large test cases. |
| **Implementation Detail** | `+1` to the total damage ensures health stays > 0 at the end | Forgetting `+1` returns 0, which is invalid. | Misunderstanding the armor’s *upper bound* (use `min(armor, maxDamage)` to avoid subtracting more than available). |
| **Edge Cases** | `damage` can contain zeros, armor can be zero | None | Misreading “at most once” can lead to trying to use armor multiple times. |

---

## 4. Intuitive Greedy Solution <a name="solution"></a>

1. **Total damage you would take without armor**  
   \[
   \text{total} = 1 + \sum_{i=0}^{n-1}\text{damage}[i]
   \]
   We add `1` because after finishing the last level, health must still be **> 0**.

2. **Where to use armor**  
   The armor is most useful on the *single* level that inflicts the **maximum** damage.  
   - Let `maxDamage` = `max(damage)`.  
   - Armor can reduce at most `armor`, so the effective reduction is `min(maxDamage, armor)`.

3. **Final answer**  
   \[
   \text{answer} = \text{total} - \min(\text{maxDamage}, \text{armor})
   \]

That’s all – a single pass through the array.

---

## 5. Code in 3 Languages <a name="code"></a>

Below are clean, commented, production‑ready solutions. All three share the same O(n) time, O(1) space logic.

### Java (Java 17)

```java
import java.util.*;

public class Solution {
    /**
     * Minimum starting health to beat the game.
     *
     * @param damage array of damage per level
     * @param armor  maximum damage the armor can block (used once)
     * @return minimum initial health > 0
     */
    public long minimumHealth(int[] damage, int armor) {
        long total = 1;              // health must stay >0 after all levels
        int maxDamage = 0;           // track the worst level

        for (int d : damage) {
            total += d;
            if (d > maxDamage) maxDamage = d;
        }

        // Armor can block at most armor damage, but never more than the
        // biggest single hit.
        long armorEffect = Math.min(maxDamage, armor);
        return total - armorEffect;
    }

    // Simple driver for quick manual tests
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.minimumHealth(new int[]{2, 7, 4, 3}, 4)); // 13
        System.out.println(s.minimumHealth(new int[]{2, 5, 3, 4}, 7)); // 10
        System.out.println(s.minimumHealth(new int[]{3, 3, 3}, 0));     // 10
    }
}
```

### Python 3

```python
from typing import List

class Solution:
    def minimumHealth(self, damage: List[int], armor: int) -> int:
        """
        Return the minimum starting health needed to survive all levels.
        """
        total = 1          # keep health > 0 after the last level
        max_damage = 0

        for d in damage:
            total += d
            if d > max_damage:
                max_damage = d

        armor_effect = min(max_damage, armor)
        return total - armor_effect

# -----------------------------------------------------------------
# quick manual test harness (you can drop this into a separate file)
if __name__ == "__main__":
    s = Solution()
    print(s.minimumHealth([2, 7, 4, 3], 4))   # 13
    print(s.minimumHealth([2, 5, 3, 4], 7))   # 10
    print(s.minimumHealth([3, 3, 3], 0))       # 10
```

### C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    /**
     * Compute the minimal starting health to survive all levels.
     */
    long long minimumHealth(vector<int>& damage, int armor) {
        long long total = 1;            // health > 0 after final level
        int maxDamage = 0;

        for (int d : damage) {
            total += d;
            if (d > maxDamage) maxDamage = d;
        }

        long long armorEffect = min(static_cast<long long>(maxDamage), static_cast<long long>(armor));
        return total - armorEffect;
    }
};

// Simple driver for manual testing
int main() {
    Solution s;
    cout << s.minimumHealth({2, 7, 4, 3}, 4) << endl; // 13
    cout << s.minimumHealth({2, 5, 3, 4}, 7) << endl; // 10
    cout << s.minimumHealth({3, 3, 3}, 0) << endl;     // 10
    return 0;
}
```

All three solutions:

- Use **64‑bit arithmetic** for `total`.  
- Perform **exactly one loop** over the input.  
- Subtract the optimal armor effect (`min(maxDamage, armor)`).

---

## 6. Complexity Analysis <a name="complexity"></a>

| Measure | Java | Python | C++ |
|---------|------|--------|-----|
| **Time** | `O(n)` – one linear scan | `O(n)` | `O(n)` |
| **Space** | `O(1)` – only two variables | `O(1)` | `O(1)` |
| **Worst‑case operations** | `10⁵` additions & comparisons | `10⁵` | `10⁵` |

The algorithm is *asymptotically optimal*; any solution must at least read the array once.

---

## 7. Edge Cases & Common Pitfalls <a name="edge-cases"></a>

| Situation | What to check | Typical mistake |
|-----------|---------------|-----------------|
| `damage[i] == 0` everywhere | Total damage is still `1`, so answer is `1` | Forgetting to add `1` will return `0`. |
| `armor == 0` | Armor has no effect | Subtracting `0` is fine, but some implementations might accidentally subtract a negative number. |
| `maxDamage == 0` | All levels are harmless | Armor is useless, answer = `1 + sum(damage)`. |
| Large `damage` sum | Use `long` / `long long` | 32‑bit integer overflow on test cases with ~10¹⁰ total damage. |
| Using armor *multiple* times | The prompt says “at most once” | Some solutions incorrectly try to split the armor across several levels. |

---

## 8. Testing Your Solution <a name="testing"></a>

```python
def test_solution():
    s = Solution()
    assert s.minimumHealth([2, 7, 4, 3], 4) == 13
    assert s.minimumHealth([2, 5, 3, 4], 7) == 10
    assert s.minimumHealth([3, 3, 3], 0) == 10
    assert s.minimumHealth([0, 0, 0], 0) == 1  # no damage at all
    assert s.minimumHealth([100000] * 100000, 100000) == 100000 * 100000 + 1 - 100000
    print("All tests passed!")

if __name__ == "__main__":
    test_solution()
```

Run the snippet in your IDE or on the LeetCode sandbox to verify correctness on edge cases.

---

## 9. Take‑away & How to Talk About It in an Interview <a name="takeaway"></a>

> **“I used a single‑pass greedy approach. I added `+1` so health stays > 0 after the last level, and I subtracted the minimum of `armor` and the maximum single‑level damage.”**

*Why this answer sounds great:*

- **Clarity** – The logic is *O(1)* and *O(n)*.  
- **Precision** – You explicitly mention the 64‑bit arithmetic.  
- **Awareness of constraints** – You point out why a 32‑bit type would break on large inputs.

If the interviewer asks for an alternative (e.g., binary search or DP), explain that the greedy solution is optimal and simpler:

> “You could binary‑search the starting health, but you’d still need an O(n) scan inside each check. That gives O(n log V) – overkill when a linear‑time algorithm already exists.”

---

## 10. SEO Keywords & Meta‑Tags <a name="seo"></a>

| Element | Content |
|---------|---------|
| **Title Tag** | Minimum Health to Beat Game – LeetCode 2214 | Medium – Java, Python, C++ O(n) Solution |
| **Meta Description** | Master LeetCode 2214 – “Minimum Health to Beat Game.” Discover a clean Java, Python, and C++ O(n) solution, full explanations, edge‑case handling, and interview talking points. |
| **Primary Keywords** | leetcode 2214, minimum health to beat game, java interview, python interview, c++ interview, O(n) algorithm, greedy algorithm, interview prep |
| **Secondary Keywords** | algorithm design, constant space, large input handling, job interview, software engineer interview, leetcode solutions, data type overflow, interview strategies |
| **Alt Text for Code Images** | “Java solution for LeetCode 2214”, “Python solution for LeetCode 2214”, “C++ solution for LeetCode 2214” |
| **Header Tags** | H1 – Problem Title, H2 – Section Headers, H3 – Sub‑headers (e.g., Java, Python) |
| **Internal Links** | Links to other LeetCode medium‑difficulty solutions (optional) |

*Why SEO matters:* recruiters often scan blogs for “LeetCode 2214 Java solution” or “minimum health interview question.” Using these tags ensures your article surfaces in those searches.

---

## 🎯 Final Verdict

- **Greedy is the king** for this problem: pick the level with maximum damage, block as much as possible, and you’re done.  
- Keep your cumulative damage in a 64‑bit variable.  
- Remember the mandatory `+1` – it’s the difference between a *Wrong Answer* and a *Correct Answer*.

Mastering this problem shows you can turn a concise problem statement into an *efficient* and *bug‑free* solution—exactly what recruiters want in a software‑engineer candidate. Good luck on your next interview! 🚀