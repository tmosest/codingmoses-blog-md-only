---
title: LeetCode 870. Advantage Shuffle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 870. **Advantage Shuffle** – Problem Overview

**Difficulty:** Medium  
**Key Ideas:** Sorting, Greedy, Two‑Pointer Technique, HashMap/TreeMap  
**Typical interview tags:** Array, Greedy, Sorting, Hash Table

**Problem Statement**  
You’re given two integer arrays, `nums1` and `nums2`, of equal length.  
Define the *advantage* of `nums1` with respect to `nums2` as the number of indices `i` where `nums1[i] > nums2[i]`.  
Return any permutation of `nums1` that maximizes this advantage.

**Why it matters**  
- Demonstrates mastery of greedy reasoning.  
- Shows you can transform a global optimization into a local, sortable decision.  
- Commonly appears in coding interviews and technical hiring tests.

---

## ✅ The Solution – Greedy + Two‑Pointer

1. **Sort** `nums1` in ascending order.  
2. **Create** a list of pairs `(nums2[i], i)` and sort it ascending by `nums2[i]`.  
3. **Two pointers**  
   - `lo` starts at the smallest element in `nums1`.  
   - `hi` starts at the largest element.  
   - For each sorted `nums2` element:
     * If `nums1[hi]` > `nums2[i]`, place `nums1[hi]` at position `i` (advantage). Move `hi` left.  
     * Otherwise, place `nums1[lo]` at position `i` (no advantage, sacrifice a small number). Move `lo` right.  
4. Return the arranged array.

**Proof of Optimality**  
- Each `nums2[i]` is processed in ascending order.  
- The algorithm always uses the *smallest* possible `nums1` that still beats the current `nums2[i]`.  
- If no element can beat `nums2[i]`, the smallest remaining element is sacrificed, ensuring no future element is wasted.  
- This greedy choice is locally optimal and, by exchange argument, globally optimal.

**Complexity**  
- Sorting `nums1`: `O(n log n)`  
- Sorting `nums2` indices: `O(n log n)`  
- Two‑pointer pass: `O(n)`  
- Total: `O(n log n)` time, `O(n)` extra space.

---

## 📄 Code Implementations

### Java

```java
import java.util.*;

public class AdvantageShuffle {
    public int[] advantageCount(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int[] result = new int[n];

        // Sort nums1
        Integer[] sortedNums1 = new Integer[n];
        for (int i = 0; i < n; i++) sortedNums1[i] = nums1[i];
        Arrays.sort(sortedNums1);

        // Pair nums2 values with indices and sort
        Integer[] idx = new Integer[n];
        for (int i = 0; i < n; i++) idx[i] = i;
        Arrays.sort(idx, Comparator.comparingInt(i -> nums2[i]));

        int lo = 0, hi = n - 1;
        for (int i = 0; i < n; i++) {
            int pos = idx[i];
            if (sortedNums1[hi] > nums2[pos]) {
                result[pos] = sortedNums1[hi];
                hi--;
            } else {
                result[pos] = sortedNums1[lo];
                lo++;
            }
        }
        return result;
    }

    // For quick manual testing
    public static void main(String[] args) {
        AdvantageShuffle sol = new AdvantageShuffle();
        int[] nums1 = {2,7,11,15};
        int[] nums2 = {1,10,4,11};
        System.out.println(Arrays.toString(sol.advantageCount(nums1, nums2)));
    }
}
```

### Python

```python
from typing import List

class Solution:
    def advantageCount(self, nums1: List[int], nums2: List[int]) -> List[int]:
        n = len(nums1)
        result = [0] * n

        # Sort nums1
        nums1.sort()

        # Pair nums2 values with original indices and sort
        indices = sorted(range(n), key=lambda i: nums2[i])

        lo, hi = 0, n - 1
        for pos in indices:
            if nums1[hi] > nums2[pos]:
                result[pos] = nums1[hi]
                hi -= 1
            else:
                result[pos] = nums1[lo]
                lo += 1
        return result

# Example
if __name__ == "__main__":
    sol = Solution()
    print(sol.advantageCount([2,7,11,15], [1,10,4,11]))
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> advantageCount(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();
        vector<int> result(n);

        // Sort nums1
        sort(nums1.begin(), nums1.end());

        // Indices of nums2 sorted by their values
        vector<int> idx(n);
        iota(idx.begin(), idx.end(), 0);
        sort(idx.begin(), idx.end(),
             [&](int a, int b){ return nums2[a] < nums2[b]; });

        int lo = 0, hi = n - 1;
        for (int pos : idx) {
            if (nums1[hi] > nums2[pos]) {
                result[pos] = nums1[hi--];
            } else {
                result[pos] = nums1[lo++];
            }
        }
        return result;
    }
};

int main() {
    Solution s;
    vector<int> nums1 = {2,7,11,15};
    vector<int> nums2 = {1,10,4,11};
    vector<int> res = s.advantageCount(nums1, nums2);
    for (int x : res) cout << x << ' ';
    cout << endl;
}
```

---

## 📑 Blog Article – *The Good, The Bad, and the Ugly of the Advantage Shuffle*

### H1: Mastering the Advantage Shuffle – A Deep Dive into LeetCode #870

**Meta‑Description:**  
Discover how to solve LeetCode 870 – Advantage Shuffle – with a clean greedy algorithm. Read the full guide, code snippets in Java, Python, and C++, and learn why this problem is a must‑know for your next technical interview.

---

### Introduction

If you’re preparing for software engineering interviews, LeetCode 870 – *Advantage Shuffle* – is a classic. It tests your ability to think greedily, work with sorting, and handle arrays efficiently. In this post, we’ll walk through the problem, dissect the algorithm, and present clear, production‑ready code in three popular languages.

> **Why this matters**  
> • Showcases greedy thinking in interviews  
> • Demonstrates mastery of sorting and two‑pointer tricks  
> • Boosts your portfolio for technical hiring

---

### The Problem in a Nutshell

> **Goal:** Return any permutation of `nums1` that maximizes the count of indices `i` where `nums1[i] > nums2[i]`.  
> **Constraints:** `1 ≤ nums1.length ≤ 10⁵`, all values fit in a 32‑bit integer.

---

### The Good – Elegant, Efficient, and Proven

| Feature | Why It’s Good |
|---------|---------------|
| **O(n log n) time** | Sorting dominates, which is optimal for this problem. |
| **O(n) space** | Uses only a few auxiliary arrays. |
| **Deterministic** | Guarantees maximum advantage every run. |
| **Language‑agnostic** | Easy to translate into Java, Python, C++, Go, etc. |
| **Real‑world relevance** | Greedy logic applies to scheduling, resource allocation, etc. |

---

### The Bad – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using a multiset or TreeMap in Java** | Slows down to `O(n log n)` per operation – unnecessary overhead. |
| **Sorting indices incorrectly** | A typo in the comparator can reverse the order, yielding sub‑optimal results. |
| **Ignoring duplicate values** | The algorithm handles duplicates automatically, but test cases with many ties can trip novices. |
| **Mis‑indexing during two‑pointer sweep** | Off‑by‑one errors break the entire permutation. |

---

### The Ugly – Edge Cases & Performance Hints

1. **All equal numbers**  
   *The algorithm will still run correctly, returning the original array. No advantage possible, but no bug.*

2. **Huge input (10⁵)**  
   *Memory usage matters:* avoid boxing (`Integer[]`) in Java if possible; use primitive arrays.

3. **Large integer values (up to 10⁹)**  
   *No overflow risk in Java’s `int` or Python’s `int`, but be mindful if using languages with 32‑bit signed ints.*

4. **Testing**  
   *Always generate random test cases and compare with a brute‑force solver on small `n` to verify correctness.*

---

### Full Code Snippets

*(See the code section above for Java, Python, and C++ implementations.)*

---

### How to Use This Knowledge in Your Next Interview

1. **Explain the greedy rationale** – emphasize why the smallest beatable element is the best local choice.
2. **Mention time & space complexity** – show you can analyze performance.
3. **Show code** – present a clean, commented snippet in the interview language.
4. **Discuss extensions** – e.g., how you’d handle dynamic updates or additional constraints.

---

### Final Takeaway

Advantage Shuffle is more than a LeetCode exercise; it’s a microcosm of algorithmic thinking that’s useful in production systems. Mastering it demonstrates:

- **Problem‑solving discipline** (identify sorting + greedy).  
- **Clean implementation skills** (two‑pointer sweep).  
- **Analytical confidence** (time/space trade‑offs).  

These are exactly the qualities recruiters look for. Share this solution, discuss it on your portfolio, and you’ll be one step closer to landing that dream job.

--- 

*Happy coding, and good luck on your next interview!*