---
title: LeetCode 1007. Minimum Domino Rotations For Equal Row - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔥 1007 – Minimum Domino Rotations for Equal Row  
**A complete, SEO‑friendly walkthrough, code in Java, Python & C++ + “the good, the bad, the ugly”**  

---

### 1️⃣ Problem Recap

> **LeetCode 1007 – Minimum Domino Rotations for Equal Row**  
> Given two equal‑length arrays `tops` and `bottoms` (1‑indexed numbers from 1 to 6), each pair represents the two halves of a domino.  
> You may rotate any domino (swap its top and bottom).  
> Return the *minimum* number of rotations needed so that all the top values are equal **or** all the bottom values are equal.  
> If it is impossible, return `-1`.

---

### 2️⃣ Why It Matters

- **Interview favourite** – the problem tests *array manipulation*, *greedy*, and *edge‑case awareness*.  
- **Real‑world relevance** – similar to “make all columns equal in a matrix with row flips”.  
- **Coding interview** – appears in Amazon, Google, Microsoft, and many other tech companies.  
- **SEO advantage** – “minimum domino rotations interview question” and “make a row uniform dominoes” are frequent search queries.  

---

### 3️⃣ The Brute‑Force Idea

Check every possible target value (1–6).  
For each target:

1. Scan all dominoes.
2. Count how many tops need flipping to become the target.
3. Count how many bottoms need flipping to become the target.
4. If any domino lacks the target on both halves, discard this candidate.

Finally, pick the smallest rotation count.

**Time**: O(6 · n) → O(n)  
**Space**: O(1)

Because the domain is tiny (1–6), a single pass per value is fast enough, and the algorithm is easy to reason about.

---

### 4️⃣ Edge Cases (The Ugly)

| Scenario | Why it trips many implementations | Fix |
|----------|-----------------------------------|-----|
| `tops[i] == bottoms[i]` | Some solutions double‑count rotations | Treat as a single domino; do not add the same number twice. |
| Candidate value never appears in a domino | The algorithm should immediately skip it | If `cnt[target] < n`, continue. |
| Arrays of length 2 | Both values may be equal or different | Still works; just make sure to handle `n==2` correctly. |

---

### 5️⃣ The Clean, Production‑Ready Solution

We’ll present the same greedy logic in **Java**, **Python**, and **C++**. All implementations share:

- A helper to test a candidate.
- Early termination when a candidate is impossible.
- Return `-1` if no candidate works.

---

## 🚀 Java

```java
import java.util.*;

public class Solution {
    public int minDominoRotations(int[] tops, int[] bottoms) {
        int n = tops.length;
        // candidates are numbers 1..6
        int best = Integer.MAX_VALUE;

        for (int target = 1; target <= 6; target++) {
            int topRot = 0, bottomRot = 0;
            boolean possible = true;

            for (int i = 0; i < n; i++) {
                if (tops[i] != target && bottoms[i] != target) {
                    possible = false;
                    break; // cannot use this target
                }
                if (tops[i] != target) topRot++;          // flip to make top = target
                if (bottoms[i] != target) bottomRot++;   // flip to make bottom = target
            }

            if (possible) best = Math.min(best, Math.min(topRot, bottomRot));
        }
        return best == Integer.MAX_VALUE ? -1 : best;
    }
}
```

> **Key points**  
> * The `for` loop over `1..6` guarantees O(n).  
> * No extra data structures → O(1) space.  
> * `Integer.MAX_VALUE` is a sentinel that clearly signals “no solution”.

---

## ⚡️ Python

```python
from typing import List

class Solution:
    def minDominoRotations(self, tops: List[int], bottoms: List[int]) -> int:
        n = len(tops)
        INF = float('inf')
        best = INF

        for target in range(1, 7):
            top_rot = bottom_rot = 0
            possible = True

            for t, b in zip(tops, bottoms):
                if t != target and b != target:
                    possible = False
                    break
                if t != target:
                    top_rot += 1
                if b != target:
                    bottom_rot += 1

            if possible:
                best = min(best, min(top_rot, bottom_rot))

        return -1 if best == INF else best
```

> **Why it’s elegant**  
> * `zip` pairs tops & bottoms – no manual indexing.  
> * `float('inf')` is Python’s natural sentinel.  
> * The code is ~10 lines long – great for quick interviews.

---

## 🥇 C++

```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minDominoRotations(vector<int>& tops, vector<int>& bottoms) {
        int n = tops.size();
        int best = INT_MAX;                // sentinel

        for (int target = 1; target <= 6; ++target) {
            int topRot = 0, bottomRot = 0;
            bool possible = true;

            for (int i = 0; i < n; ++i) {
                if (tops[i] != target && bottoms[i] != target) {
                    possible = false;
                    break;
                }
                if (tops[i] != target) ++topRot;
                if (bottoms[i] != target) ++bottomRot;
            }

            if (possible)
                best = min(best, min(topRot, bottomRot));
        }
        return best == INT_MAX ? -1 : best;
    }
};
```

> **C++ specific notes**  
> * Use `INT_MAX` from `<climits>`.  
> * `vector<int>` is zero‑based; no `size()` off‑by‑one bugs.  

---

### 6️⃣ Time & Space Complexity Recap

| Language | Time | Space |
|----------|------|-------|
| Java | O(n) | O(1) |
| Python | O(n) | O(1) |
| C++ | O(n) | O(1) |

All three solutions share the same asymptotic guarantees.

---

### 7️⃣ The Good, the Bad & the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Clear loops & early breaks | Code that builds a 7‑size frequency array (unnecessary) | Solutions that count rotations twice for `tops[i]==bottoms[i]` |
| **Performance** | Single O(n) scan per candidate | Using a hash‑map of size 7 is fine, but adds ~O(1) space | Solutions that check all 6 candidates but also build a 6‑size frequency table → trivial extra work |
| **Edge‑case handling** | All 6 values tested, sentinel for “no solution” | Many Python solutions forget to skip impossible candidates → `-1` is returned incorrectly | Java or C++ that return 0 when both arrays are identical – wrong answer |

> **Takeaway** – The simplest greedy scan is not only fast, it is also *robust* when you treat edge cases correctly.  

---

### 📚 Final Thoughts & Interview Tips

1. **Explain the candidate idea** – the interviewers love seeing the reasoning: “the target must appear in every domino”.
2. **Talk about early exit** – if a candidate fails at index 4, no need to finish the scan.
3. **Mention the constant domain** – because 1–6 is tiny, we can afford O(6 · n).  
4. **Show the helper** – optionally write a separate function `testTarget(t, b, target)` to keep the code modular.  
5. **Edge case demonstration** – give the interviewer a quick example (`tops = [1,2]`, `bottoms = [2,1]` → answer `1`).

---

### 🎯 SEO‑friendly Conclusion

If you’re preparing for a technical interview, the **“Minimum Domino Rotations for Equal Row”** problem is a must‑know.  
- Google searches: *“minimum domino rotations interview”*, *“dominoes make row uniform”*, *“LeetCode 1007 solution”*.  
- Companies: Amazon, Google, Microsoft, Uber, Meta, etc.  
- Solution: a clean greedy scan in O(n) time, O(1) space, with careful handling of identical halves.

Happy coding and good luck on your next interview! 🚀