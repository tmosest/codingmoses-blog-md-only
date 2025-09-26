---
title: LeetCode 2154. Keep Multiplying Found Values by Two - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2154 – “Keep Multiplying Found Values by Two”

*“The good, the bad, and the ugly of a simple yet interview‑friendly problem.”*

---

### 1. Problem Summary (Easy)

You’re given an integer array `nums` and an integer `original`.  
While `original` is present in `nums`, you double it (`original *= 2`).  
Return the first value that is **not** found in `nums`.

> **Input** – `nums = [5,3,6,1,12]`, `original = 3`  
> **Output** – `24`  
> **Explanation**  
> 3 → 6 → 12 → 24 (24 isn’t in the array, so we stop)

**Constraints**

- `1 ≤ nums.length ≤ 1000`
- `1 ≤ nums[i], original ≤ 1000`

---

### 2. Why This Problem Is a Good Interview Question

| Good | Bad | Ugly |
|------|-----|------|
| **Simplicity** – It tests basic algorithmic thinking without drowning you in boilerplate. | **Hidden pitfalls** – A naïve linear scan for each double can be costly for larger inputs. | **Edge‑case traps** – Forgetting to stop when the value is absent, or mis‑handling integer overflow if the constraints were larger. |
| **Multiple language support** – You can demonstrate the same logic in Java, Python, C++ – great for a portfolio. | **Misreading the “while” loop** – Some candidates treat it as “do‑while” and double before the check. | **Set vs. list** – Using a `List` instead of a `HashSet` leads to O(n²) time, making the solution slower than the editorial. |
| **Time‑space trade‑off** – You learn why a hash‑based structure is the natural fit. | | |

---

### 3. Algorithm – Fast Lookup with a HashSet

1. **Convert `nums` into a `HashSet`** – gives average O(1) membership checks.  
2. **Loop** while `original` is contained in the set:  
   `original *= 2`  
3. Return `original` when the loop exits.

**Why it works**

- Every iteration guarantees the current value is present, so we’re allowed to double it.  
- As soon as the value is missing, no further doubling is possible – we return the first “missing” number.  
- Because we only scan the set once per iteration and each iteration increases `original` exponentially, the loop terminates quickly (at most ~10 steps for the given constraints).

---

### 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Building HashSet (`O(n)`) | `O(n)` | `O(n)` |
| Each loop iteration (`O(1)` lookup + `O(1)` multiply) | `O(log answer)` (≈ number of doublings) | `O(1)` |
| **Total** | **O(n + log answer)** | **O(n)** |

With `nums.length ≤ 1000`, this is far below any practical time limit.

---

### 5. Code – 3 Languages

Below are clean, idiomatic solutions for **Java, Python, and C++**. Feel free to copy, paste, and run them in your favourite editor.

#### 5.1 Java (LeetCode‑style)

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int findFinalValue(int[] nums, int original) {
        Set<Integer> set = new HashSet<>();
        for (int num : nums) set.add(num);

        while (set.contains(original)) {
            original <<= 1;          // original *= 2
        }
        return original;
    }
}
```

#### 5.2 Python

```python
def find_final_value(nums: list[int], original: int) -> int:
    nums_set = set(nums)            # O(n) time, O(n) space

    while original in nums_set:     # O(1) average lookup
        original <<= 1              # multiply by 2

    return original
```

#### 5.3 C++

```cpp
#include <unordered_set>
#include <vector>

class Solution {
public:
    int findFinalValue(std::vector<int>& nums, int original) {
        std::unordered_set<int> st(nums.begin(), nums.end());
        while (st.count(original)) {
            original <<= 1; // multiply by 2
        }
        return original;
    }
};
```

---

### 6. Common Mistakes & How to Avoid Them

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Using a `List` and `list.contains()` inside the loop | O(n²) time, may TLE on larger constraints | Switch to `HashSet`/`unordered_set` |
| Forgetting the **loop condition** (`while (set.contains(original))`) | Infinite loop or wrong output | Always guard the loop with the existence check |
| Using `original * 2` instead of `original <<= 1` in Java/C++ | Less readable, but functionally fine. | `<<= 1` is a bit‑shifting trick that’s a bit faster in tight loops |
| Not handling overflow | In this problem constraints prevent it, but for larger inputs it’s a risk | Use `long long` in C++/Python’s unlimited int, or check before multiplying |

---

### 7. Testing Strategy

```text
1. Basic cases:
   - nums = [1,2,4,8], original = 1 -> 16
   - nums = [2,7,9], original = 4 -> 4
2. Edge cases:
   - single element array, original matches / doesn't match
   - maximum constraints (1000 elements, all 1000)
3. Randomized testing:
   - Generate random arrays and originals, compare brute‑force vs hash‑set implementation
```

---

### 8. Why This Blog Helps Your Job Hunt

- **SEO‑friendly keywords**: “LeetCode 2154”, “Keep Multiplying Found Values by Two”, “Java solution”, “Python solution”, “C++ solution”, “hash set”, “algorithm interview”.
- **Showcases**: Multi‑language proficiency, clear understanding of data structures, time/space analysis, handling edge cases – all vital for a software engineering interview.
- **Portfolio**: Add the code snippets to your GitHub README or personal blog; recruiters love seeing clean, commented solutions.

---

### 9. Takeaway

This problem is a classic “look‑and‑double” puzzle that tests whether you can pick the right data structure and write concise, correct code. By mastering it, you demonstrate:

- Quick problem analysis  
- Efficient data‑structure selection  
- Clean, idiomatic implementation in your preferred language  
- Awareness of common pitfalls

Good luck crushing your next coding interview! 🚀