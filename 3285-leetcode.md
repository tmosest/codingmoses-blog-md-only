---
title: LeetCode 3285. Find Indices of Stable Mountains - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Finding Stable Mountains – LeetCode 3285  
**Problem** | **Java** | **Python** | **C++** | **Job‑Ready Blog**  

> **“A mountain is called stable if the mountain just before it (if it exists) has a height strictly greater than a given threshold.”**  
> Return the indices of all stable mountains.

---

## 1️⃣  Problem Breakdown

| Item | Detail |
|------|--------|
| **Input** | `int[] height` – heights of consecutive mountains (`2 ≤ n ≤ 100`) |
| | `int threshold` – threshold value (`1 ≤ threshold ≤ 100`) |
| **Output** | `List<Integer>` (Java), `List[int]` (Python), `vector<int>` (C++) containing the indices of all stable mountains. Order does not matter. |
| **Special rule** | Mountain `0` is *never* stable (no “previous” mountain). |

The task is essentially a one‑pass scan of the array, checking the previous element against `threshold`.

---

## 2️⃣  Key Observations

1. **Linear scan is enough** – we only need the previous element, no extra data structure.  
2. **Index arithmetic** – when we are at index `i` (`i ≥ 1`), the candidate stable mountain is `i`.  
3. **Strictly greater comparison** – `height[i-1] > threshold`.  

These observations give us a clean O(n) time, O(1) auxiliary space (aside from the result list).

---

## 3️⃣  Algorithm (Pseudocode)

```
stableIndices = empty list
for i from 1 to n-1:
    if height[i-1] > threshold:
        add i to stableIndices
return stableIndices
```

That’s it.

---

## 4️⃣  Complexity Analysis

| Metric | Calculation |
|--------|-------------|
| **Time** | `O(n)` – one pass over the array. |
| **Space** | `O(k)` – where `k` is the number of stable mountains (output). |  

(Extra space is only the result list; no other auxiliary containers.)

---

## 5️⃣  Edge Cases & “Bad” Patterns

| Edge Case | What to watch out for |
|-----------|-----------------------|
| **Minimum array length** (`n = 2`) | Index `0` cannot be stable; ensure loop starts at `1`. |
| **All heights ≤ threshold** | Result should be an empty list. |
| **All heights > threshold** | Result is `[1, 2, ..., n-1]`. |
| **Threshold equals a height** | Use **strictly greater** (`>`), not `>=`. |
| **Negative heights** (not allowed by constraints) | If you expand constraints, ensure comparison works. |

### Common “Bad” Mistakes

- Off‑by‑one errors: adding `i` instead of `i+1` (when using `pairwise` or Python’s `enumerate`).
- Ignoring that mountain `0` is never stable.
- Using `>=` instead of `>` for the comparison.

---

## 6️⃣  “Good” Coding Practices

- **Readability** – clear variable names (`idx`, `prevHeight`, `result`).
- **One‑liner elegance** – possible in Python with `pairwise` from `itertools`.
- **No extra memory** – a simple for‑loop or comprehension.
- **Type safety** – explicit generic types in Java (`List<Integer>`).
- **Comments** – short, explanatory comments that add value.

---

## 7️⃣  “Ugly” – Things You Might Do in a Rush

- Using `ArrayList` in Java but not pre‑allocating capacity.
- Using `stream().filter()` in Java 8, which adds overhead.
- Writing a nested loop or unnecessary function calls.
- Forgetting to handle an empty input (though constraints forbid it).

---

## 8️⃣  Implementation in 3 Languages

### 📚 Java (OOP + Collections)

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    /**
     * Finds the indices of stable mountains.
     *
     * @param height    array of mountain heights
     * @param threshold threshold value
     * @return list of indices of stable mountains
     */
    public List<Integer> stableMountains(int[] height, int threshold) {
        List<Integer> res = new ArrayList<>();
        for (int i = 1; i < height.length; i++) {     // start at 1 because 0 can’t be stable
            if (height[i - 1] > threshold) {
                res.add(i);                           // current index is stable
            }
        }
        return res;
    }
}
```

### 🐍 Python (4‑line concise version)

```python
from itertools import pairwise
from typing import List

class Solution:
    def stableMountains(self, height: List[int], threshold: int) -> List[int]:
        # pairwise gives (prev, curr) for each adjacent pair
        return [i + 1 for i, (prev, _) in enumerate(pairwise(height)) if prev > threshold]
```

*If you prefer a more explicit loop (e.g., for older Python versions):*

```python
class Solution:
    def stableMountains(self, height: List[int], threshold: int) -> List[int]:
        res = []
        for i in range(1, len(height)):
            if height[i - 1] > threshold:
                res.append(i)
        return res
```

### 🧪 C++ (Standard Library)

```cpp
#include <vector>

class Solution {
public:
    std::vector<int> stableMountains(const std::vector<int>& height, int threshold) {
        std::vector<int> res;
        for (size_t i = 1; i < height.size(); ++i) {          // start from 1
            if (height[i - 1] > threshold) {
                res.push_back(static_cast<int>(i));
            }
        }
        return res;
    }
};
```

---

## 9️⃣  Testing (Sample Usage)

```java
// Java
Solution s = new Solution();
System.out.println(s.stableMountains(new int[]{1,2,3,4,5}, 2)); // [3,4]
```

```python
# Python
sol = Solution()
print(sol.stableMountains([10,1,10,1,10], 3))  # [1, 3]
```

```cpp
// C++
Solution s;
auto ans = s.stableMountains({10,1,10,1,10}, 3);
for (int idx : ans) std::cout << idx << ' ';  // 1 3
```

---

## 🔟  SEO‑Optimized Blog Article

---

### Title  
**“LeetCode 3285: Find Indices of Stable Mountains – Java, Python, C++ Solutions & Interview Tips”**

### Meta Description  
Solve LeetCode 3285 with clear Java, Python, and C++ code. Understand the stable mountains problem, analyze time/space complexity, learn best practices, and get interview‑ready.

### Keywords  
LeetCode 3285, find indices of stable mountains, coding interview, algorithm, data structures, Java solution, Python solution, C++ solution, interview coding question, time complexity, space complexity, job interview prep.

---

#### 🚀 Introduction

When landing a tech interview, solving *LeetCode 3285 – Find Indices of Stable Mountains* showcases your ability to translate a real‑world problem into clean, efficient code. This post walks you through the problem, dives into the “good, the bad, and the ugly” of typical solutions, and presents working Java, Python, and C++ implementations that you can copy‑paste into your test harness.

> **Why this problem matters:**  
> - It tests **array traversal** and **index arithmetic** – fundamentals of any coding interview.  
> - The solution is **O(n)** time, **O(1)** auxiliary space, perfect for a high‑scoring “easy” question.  
> - You’ll learn how to avoid common pitfalls and write code that recruiters love.

---

#### 📌 Problem Recap

Given an integer array `height` (size `n`) and an integer `threshold`, a mountain at index `i` (where `i > 0`) is **stable** if `height[i-1] > threshold`. Mountain `0` is never stable. Return all stable mountain indices.

---

#### 🔍 The “Good” – What to Aim For

| Aspect | Why it’s good |
|--------|---------------|
| **Linear Scan** | One pass – minimal time. |
| **Simple Condition** | `height[i-1] > threshold` – no complex logic. |
| **Explicit Indexing** | Avoid off‑by‑one bugs by starting loop at `1`. |
| **No Extra Containers** | Only result list/vector – memory‑efficient. |
| **Clean Code** | Descriptive variable names, single responsibility. |

---

#### ⚠️ The “Bad” – Common Mistakes

1. **Off‑by‑One** – adding `i` instead of `i+1` when using Python’s `enumerate` or Java’s `for` loop.  
2. **Wrong Comparison** – using `>=` instead of `>`.  
3. **Ignoring Mountain 0** – forgetting that index 0 is never stable.  
4. **Unnecessary Complexity** – using streams or recursive calls where a simple loop suffices.  

---

#### 🐛 The “Ugly” – Things to Avoid

- **Over‑engineering**: e.g., building a full adjacency list when only the previous element matters.  
- **Large memory footprint**: pre‑allocating huge arrays or lists when not needed.  
- **Platform‑specific hacks**: using `pairwise` in Python only when the interpreter supports it (Python 3.10+).  

---

#### 🧩 Code Walkthrough

(See sections above for the complete Java, Python, and C++ snippets.)

- **Java**: Uses `ArrayList<Integer>` for dynamic sizing.  
- **Python**: The 4‑line solution leverages `itertools.pairwise` for elegance.  
- **C++**: A straightforward `for` loop with `size_t` indices.

---

#### 📊 Complexity Breakdown

- **Time**: `O(n)` – one traversal.  
- **Space**: `O(k)` – output list/vector; auxiliary space is constant.

---

#### 🎯 Interview Tips

- **Explain the logic first**: “We look at the previous mountain… if it exceeds the threshold, the current mountain is stable.”  
- **Mention edge cases**: “When `n = 2` or when all heights are below threshold.”  
- **Show a test harness**: Use the provided examples and a few edge cases.  
- **Highlight efficiency**: “Linear time, constant auxiliary space – optimal for this problem size.”

---

#### 📚 Further Reading

- LeetCode Problem 3285: https://leetcode.com/problems/find-indices-of-stable-mountains/  
- Array traversal patterns: https://leetcode.com/articles/array-traversal/  
- Python `itertools` documentation: https://docs.python.org/3/library/itertools.html

---

#### 🎉 Conclusion

LeetCode 3285 is a textbook example of turning a simple observation into a clean, optimal solution. Mastering it demonstrates mastery of array handling, boundary conditions, and concise coding—all skills recruiters look for. Drop the sample code into your IDE, run a few tests, and you’ll be ready to ace this question in any interview.

Happy coding, and may the stable mountains favor you!

--- 

**(End of article)**

---

## 🎉 Final Takeaway

You now have:

1. A deep understanding of the problem and its constraints.  
2. Insight into what makes a solution “good” versus “bad” or “ugly.”  
3. Ready‑to‑use Java, Python, and C++ code that runs efficiently.  
4. A fully SEO‑optimized blog article to help you practice or share with peers.

Use this knowledge to impress recruiters, solve the problem on LeetCode, and step confidently into your next technical interview. 🚀

---