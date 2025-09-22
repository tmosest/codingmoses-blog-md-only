---
title: LeetCode 2164. Sort Even and Odd Indices Independently - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ LeetCode 2164 – Sort Even and Odd Indices Independently  
### (Java, Python & C++ solutions + SEO‑friendly interview guide)

---

### 📌 Meta Description  
Master LeetCode 2164 with clear, production‑ready solutions in **Java, Python, and C++**.  
Learn the algorithm, edge‑case handling, time/memory analysis, and interview‑style explanations to impress hiring managers.

---

## 1. The Problem in a Nutshell  
> **Sort Even and Odd Indices Independently**

> *Given a 0‑indexed integer array `nums`, reorder it so that:*
> - *Values at **odd** indices (1, 3, 5, …) are sorted **non‑increasingly** (descending).*
> - *Values at **even** indices (0, 2, 4, …) are sorted **non‑decreasingly** (ascending).*

> Return the resulting array.

> **Constraints**  
> - 1 ≤ `nums.length` ≤ 100  
> - 1 ≤ `nums[i]` ≤ 100  

> **Examples**  
> | Input | Output | Explanation |
> |-------|--------|-------------|
> | `[4,1,2,3]` | `[2,3,4,1]` | Odd indices sorted desc → `[4,3,2,1]`; Even indices sorted asc → `[2,3,4,1]` |
> | `[2,1]` | `[2,1]` | Only one odd and one even element – no change needed |

---

## 2. Why This Problem is a “Good, Bad & Ugly” Interview Topic  

| **Good** | **Bad** | **Ugly** |
|----------|---------|----------|
| Easy to understand – clear separation of odd/even indices | Requires two sorts; not truly O(n) – a subtlety for optimization | Index arithmetic can be confusing, especially when reconstructing the array |
| Demonstrates handling of sub‑arrays and re‑assembly | Small input size hides the potential for a counting‑sort O(n) solution | Mis‑reading “odd/even indices” as “odd/even values” can lead to incorrect solutions |
| Provides a playground for testing edge cases (single element, all same values) | Sorting overhead is unnecessary for 1 ≤ n ≤ 100 but still worth mentioning | Mistakes with integer division can corrupt the reconstruction step |

> **Takeaway:** Master the simple two‑pass strategy, but be ready to justify O(n log n) vs O(n) and discuss edge‑case pitfalls.

---

## 3. The Core Idea  

1. **Separate** the elements into two auxiliary arrays:  
   - `even` holds values at indices 0, 2, 4, …  
   - `odd` holds values at indices 1, 3, 5, …

2. **Sort** them independently:  
   - `even` → ascending (`Arrays.sort`)  
   - `odd`  → descending (`Arrays.sort` + reverse comparator)

3. **Re‑merge** into the original array using the same index pattern.

Because `nums.length` ≤ 100, the `O(n log n)` sort cost is trivial and keeps the code readable.

---

## 4. Java Implementation (Fast & Clear)

```java
import java.util.Arrays;
import java.util.Collections;

class Solution {
    public int[] sortEvenOdd(int[] nums) {
        int n = nums.length;

        // Temporary storage for even and odd positions
        Integer[] even = new Integer[(n + 1) / 2];  // ceil(n/2)
        Integer[] odd  = new Integer[n / 2];      // floor(n/2)

        // 1️⃣  Split
        for (int i = 0; i < n; i++) {
            if (i % 2 == 0) {
                even[i / 2] = nums[i];
            } else {
                odd[i / 2] = nums[i];
            }
        }

        // 2️⃣  Sort separately
        Arrays.sort(even);                                     // ascending
        Arrays.sort(odd, Collections.reverseOrder());          // descending

        // 3️⃣  Merge back
        int[] result = new int[n];
        for (int i = 0; i < n; i++) {
            result[i] = (i % 2 == 0) ? even[i / 2] : odd[i / 2];
        }
        return result;
    }
}
```

**Why this is production‑ready**

| Step | Why it matters |
|------|----------------|
| `Integer[]` → allows reverse comparator | Primitive arrays can’t be reversed without copying |
| `Arrays.sort(even)` | Built‑in quicksort/merge‑sort – stable for 100 elements |
| `Collections.reverseOrder()` | One‑liner descending sort – no custom comparator logic |

---

## 5. Python Implementation (Concise & Pythonic)

```python
class Solution:
    def sortEvenOdd(self, nums: list[int]) -> list[int]:
        n = len(nums)
        
        # Split into even & odd lists
        even = [nums[i] for i in range(0, n, 2)]
        odd  = [nums[i] for i in range(1, n, 2)]
        
        # Sort each list
        even.sort()                # ascending
        odd.sort(reverse=True)     # descending
        
        # Re‑merge
        res = [0] * n
        for i in range(n):
            res[i] = even[i // 2] if i % 2 == 0 else odd[i // 2]
        return res
```

**Python highlights**

- `range(0, n, 2)` / `range(1, n, 2)` cleanly handles index extraction.
- `list.sort()` is in‑place and already uses Timsort (`O(n log n)`).
- The `//` operator guarantees integer division.

---

## 6. C++ Implementation (Idiomatic STL)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> sortEvenOdd(vector<int>& nums) {
        int n = nums.size();
        vector<int> even((n + 1) / 2);
        vector<int> odd(n / 2);

        // Split
        for (int i = 0; i < n; ++i) {
            if (i % 2 == 0) even[i / 2] = nums[i];
            else            odd[i / 2]  = nums[i];
        }

        // Sort
        sort(even.begin(), even.end());                 // ascending
        sort(odd.begin(), odd.end(), greater<int>());    // descending

        // Merge
        vector<int> res(n);
        for (int i = 0; i < n; ++i) {
            res[i] = (i % 2 == 0) ? even[i / 2] : odd[i / 2];
        }
        return res;
    }
};
```

**Why STL?**

- `sort` uses introsort (O(n log n)) – perfectly fine for `n ≤ 100`.  
- `greater<int>()` is a lightweight comparator for descending order.

---

## 5. Complexity Analysis  

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n log n)` – two independent sorts | `O(n log n)` – same reason | `O(n log n)` – same reason |
| **Space** | `O(n)` auxiliary (`even` + `odd`) | `O(n)` auxiliary | `O(n)` auxiliary |
| **In‑place?** | No (uses helper arrays) | No | No |

> **Can we do better?**  
> Since `nums[i]` ≤ 100 and `n` ≤ 100, a counting‑sort (frequency array of size 101) would give `O(n)` time and `O(1)` extra space.  
> However, the sorted‑array approach is simpler, easier to reason about, and perfectly acceptable in interview settings.

---

## 6. Common Mistakes & Edge‑Case Checklist  

| Mistake | How to avoid |
|---------|--------------|
| **Swapping odd/even values instead of indices** | Carefully read “odd/even *indices*” and use `i % 2` checks. |
| **Using integer division incorrectly** | In C++/Java, `i / 2` (integer division) is safe. In Python, use `i // 2`. |
| **Neglecting to reverse the odd list** | Remember `Collections.reverseOrder()` in Java, `reverse=True` in Python, and `greater<int>()` in C++. |
| **Not handling odd length arrays** | Compute sizes as `(n + 1) / 2` for even and `n / 2` for odd. This covers both even and odd `n`. |
| **Assuming values are distinct** | Sorting works with duplicates; just ensure the order remains stable where required. |

---

## 7. Variations You Might Encounter  

| Variation | How it Changes the Solution |
|-----------|-----------------------------|
| **All indices sorted ascending** | Drop the reverse comparator; use a single array or two sorts. |
| **Sort indices in reverse order (even descending, odd ascending)** | Swap the sort orders for `even` and `odd`. |
| **Large constraints (n ≈ 10⁵, values ≈ 10⁹)** | Replace sorting with counting‑sort or bucket sort if value range is small, or use a stable priority queue. |
| **Need to output indices instead of values** | After merging, output the original positions stored during the split phase. |

---

## 8. Interview‑Ready Tips  

1. **Explain your plan before coding** – show you’re thinking about time/space trade‑offs.  
2. **Mention the counting‑sort alternative** – shows awareness of O(n) possibilities.  
3. **Test edge cases** in your explanation:  
   - Single element (`[42]`)  
   - All values equal (`[5,5,5,5]`)  
   - Maximum length (`n=100`)  
4. **Discuss readability vs performance** – trade‑offs matter to hiring managers.

---

## 9. Why This Problem is a Must‑Know for Job Interviews  

- **Array manipulation fundamentals** – separates concerns (split → sort → merge).  
- **Index arithmetic** – crucial for many data‑structure problems.  
- **Sorting nuances** – demonstrates understanding of ascending vs descending ordering.  
- **Complexity conversation** – can lead to deeper discussions on algorithmic optimizations.

Add this problem to your interview practice list and you’ll be ready to discuss it confidently with recruiters and hiring managers.

---

## 🔑 SEO Keywords (used throughout the article)  
- LeetCode 2164  
- Sort Even and Odd Indices Independently  
- Java solution LeetCode 2164  
- Python solution LeetCode 2164  
- C++ solution LeetCode 2164  
- interview algorithm  
- software engineering interview  
- data structure interview  
- array manipulation  
- counting sort vs quicksort  
- job interview prep  

---

### 📈 Final Word  
Master the **split‑sort‑merge** approach, know the edge cases, and be ready to discuss why an `O(n log n)` solution is acceptable here and how you’d optimize it for larger inputs.  
With the code snippets and interview insights above, you’re fully equipped to ace LeetCode 2164—and impress the hiring team that’s on the lookout for clean, efficient problem‑solvers. 🚀

Happy coding and good luck with your next interview!