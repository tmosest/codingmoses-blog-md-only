---
title: LeetCode 1534. Count Good Triplets - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 1534 – Count Good Triplets  
*LeetCode “Easy” (O(n³) brute‑force)*  

| Language | Complexity | Key Idea |
|----------|------------|----------|
| **Java** | O(n³) time, O(1) space | 3 nested loops, absolute difference checks |
| **Python** | O(n³) time, O(1) space | Same logic, idiomatic loops |
| **C++** | O(n³) time, O(1) space | Standard for‑loops, `abs` |

> **Why this matters for your resume**  
> Counting valid triplets is a classic “brute‑force + condition filtering” problem. Mastering it shows you can translate problem constraints into loops, maintain O(1) auxiliary space, and think about time‑space trade‑offs—skills interviewers love.

---

## 🛠️ Code

### 1️⃣ Java
```java
public class Solution {
    public int countGoodTriplets(int[] arr, int a, int b, int c) {
        int n = arr.length;
        int count = 0;

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (Math.abs(arr[i] - arr[j]) <= a) {
                    for (int k = j + 1; k < n; k++) {
                        if (Math.abs(arr[j] - arr[k]) <= b &&
                            Math.abs(arr[i] - arr[k]) <= c) {
                            count++;
                        }
                    }
                }
            }
        }
        return count;
    }
}
```

### 2️⃣ Python
```python
from typing import List

class Solution:
    def countGoodTriplets(self, arr: List[int], a: int, b: int, c: int) -> int:
        n = len(arr)
        count = 0

        for i in range(n):
            for j in range(i + 1, n):
                if abs(arr[i] - arr[j]) <= a:
                    for k in range(j + 1, n):
                        if abs(arr[j] - arr[k]) <= b and abs(arr[i] - arr[k]) <= c:
                            count += 1
        return count
```

### 3️⃣ C++
```cpp
class Solution {
public:
    int countGoodTriplets(vector<int>& arr, int a, int b, int c) {
        int n = arr.size(), ans = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (abs(arr[i] - arr[j]) <= a) {
                    for (int k = j + 1; k < n; ++k) {
                        if (abs(arr[j] - arr[k]) <= b &&
                            abs(arr[i] - arr[k]) <= c)
                            ++ans;
                    }
                }
            }
        }
        return ans;
    }
};
```

---

## 📊 Complexity Analysis

| Approach | Time | Space |
|----------|------|-------|
| Brute‑Force (3 loops) | **O(n³)** – 100³ = 1,000,000 iterations worst‑case (acceptable for n ≤ 100) | **O(1)** – only a counter and loop indices |
| Optimized (not required for constraints) | **O(n²)** – pre‑filter `j` & `k` indices using two‑pointer or hash sets | **O(n)** – auxiliary arrays for filtering |

> **Why brute‑force works**  
> LeetCode guarantees `arr.length ≤ 100`, so the worst‑case runtime is ~1e6 checks—well within 1‑second limits.

---

## 🧪 Test Cases

| Input | Expected Output |
|-------|-----------------|
| `arr=[3,0,1,1,9,7] , a=7, b=2, c=3` | `4` |
| `arr=[1,1,2,2,3] , a=0, b=0, c=1` | `0` |
| `arr=[0,0,0,0] , a=0, b=0, c=0` | `4` *(choose any 3 of 4 identical zeros)* |

---

## 🔍 Edge Cases & Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Forgetting the **i < j < k** order | Use nested loops with `j = i+1`, `k = j+1`. |
| Using `>` instead of `<=` in the difference checks | Ensure the condition is `<=` for all three comparisons. |
| Mis‑handling empty or very short arrays | The function naturally returns `0` for `n < 3`. |

---

## 🧠 Reflections: Good, Bad, Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Readability** | Clear 3‑layer loops, easy to follow | None | Over‑complicated logic would obscure the triple‑index relationship |
| **Performance** | Meets constraints with O(n³) | None | A mis‑typed variable (`abs` vs `fabs`) could crash the program |
| **Scalability** | Works for given constraints | Not scalable to n=1e5 | Trying to optimize prematurely (e.g., complex hashing) may introduce bugs |
| **Testing** | Straightforward unit tests | None | Edge case where `c` is much larger than `a` or `b` can produce misleading expectations |
| **Code Quality** | Uses built‑in `abs`, minimal boilerplate | None | Avoid magic numbers; comment loops for clarity |

---

## 📝 Blog Article (SEO‑Optimized)

---

### Title  
**“Count Good Triplets (LeetCode 1534) – A Complete Guide with Java, Python & C++ Solutions”**

### Meta Description  
Learn how to solve LeetCode 1534 – Count Good Triplets – in Java, Python, and C++. Understand the problem, explore brute‑force and optimized solutions, and get interview‑ready tips for coding interviews.

### Keywords  
LeetCode 1534, Count Good Triplets, interview coding problem, brute force, time complexity, O(n³), Java solution, Python solution, C++ solution, coding interview tips.

---

## Introduction  

The *Count Good Triplets* problem (LeetCode 1534) is a classic “nested‑loop with condition filtering” task. Even though it’s labeled **Easy**, it’s a staple on technical interviews because it tests your ability to:

1. Translate a mathematical definition into code.  
2. Keep track of indices (`i < j < k`).  
3. Apply multiple constraints efficiently.

In this article we’ll walk through the problem statement, discuss the best‑fit solution for the given constraints, present clean Java, Python, and C++ implementations, and reflect on the trade‑offs you should keep in mind when tackling similar problems in a job interview.

---

## Problem Overview  

You’re given an integer array `arr` and three integers `a`, `b`, `c`.  
Count how many triplets `(arr[i], arr[j], arr[k])` satisfy:

* `0 ≤ i < j < k < arr.length`  
* `|arr[i] – arr[j]| ≤ a`  
* `|arr[j] – arr[k]| ≤ b`  
* `|arr[i] – arr[k]| ≤ c`

The array length is at most 100, each element is in `[0, 1000]`, and `a, b, c` are also in `[0, 1000]`.

---

## Solution Outline  

### Brute‑Force is Enough

Because `n ≤ 100`, the naive 3‑nested loop approach runs at most  
`C(100,3) = 161,700` iterations – easily within the time limit.  
The core idea: iterate over all ordered triplets `(i, j, k)` and check the three absolute‑difference constraints.

### Why Not Optimize?  

If we had `n` in the millions, we’d need an O(n²) or O(n log n) strategy (two‑pointer, hashing, etc.).  
Here, a well‑structured O(n³) algorithm is preferable:

* **Less code** → less room for bugs.  
* **Clear logic** → easier to explain to interviewers.  
* **Consistent with constraints** → no wasted effort on unnecessary data structures.

---

## Step‑by‑Step Implementation  

Below we break the algorithm into three steps:

1. **Choose `i`** – first element of the triplet.  
2. **Choose `j`** – ensure `j > i` and filter using `|arr[i]-arr[j]| ≤ a`.  
3. **Choose `k`** – ensure `k > j` and filter using the remaining two constraints.

The innermost condition can be expressed succinctly:  

```text
abs(arr[j] - arr[k]) <= b  &&  abs(arr[i] - arr[k]) <= c
```

We also short‑circuit the innermost loop when the first constraint fails – a tiny performance win.

---

## Full Code in Three Languages  

### Java  

```java
public class Solution {
    public int countGoodTriplets(int[] arr, int a, int b, int c) {
        int n = arr.length;
        int count = 0;

        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                if (Math.abs(arr[i] - arr[j]) <= a) {
                    for (int k = j + 1; k < n; k++) {
                        if (Math.abs(arr[j] - arr[k]) <= b &&
                            Math.abs(arr[i] - arr[k]) <= c) {
                            count++;
                        }
                    }
                }
            }
        }
        return count;
    }
}
```

### Python  

```python
from typing import List

class Solution:
    def countGoodTriplets(self, arr: List[int], a: int, b: int, c: int) -> int:
        n = len(arr)
        count = 0

        for i in range(n):
            for j in range(i + 1, n):
                if abs(arr[i] - arr[j]) <= a:
                    for k in range(j + 1, n):
                        if abs(arr[j] - arr[k]) <= b and abs(arr[i] - arr[k]) <= c:
                            count += 1
        return count
```

### C++  

```cpp
class Solution {
public:
    int countGoodTriplets(vector<int>& arr, int a, int b, int c) {
        int n = arr.size(), ans = 0;
        for (int i = 0; i < n; ++i) {
            for (int j = i + 1; j < n; ++j) {
                if (abs(arr[i] - arr[j]) <= a) {
                    for (int k = j + 1; k < n; ++k) {
                        if (abs(arr[j] - arr[k]) <= b &&
                            abs(arr[i] - arr[k]) <= c)
                            ++ans;
                    }
                }
            }
        }
        return ans;
    }
};
```

All three implementations are **O(n³)** in time, **O(1)** in space, and are ready for copy‑paste into any LeetCode submission.

---

## Performance & Edge‑Case Discussion  

| Issue | Observation | Fix |
|-------|-------------|-----|
| `i < j < k` ordering | Failing to enforce this yields duplicate triplets. | Use `j = i+1` and `k = j+1`. |
| Overflow on `abs` | With values up to 1000, `int` is safe. | Use `Math.abs`, `abs`, or `std::abs`. |
| Arrays shorter than 3 | Function returns 0 naturally. | Add guard `if (n < 3) return 0;` for clarity. |

The algorithm’s simplicity makes it robust for the problem’s constraints. If you were to push the constraints higher (e.g., `n = 10⁵`), you’d need a smarter filter – but that is **not** required here.

---

## Interview Tips  

* **Explain the O(n³) choice up front** – “Given the small `n`, a straightforward triple loop is optimal.”  
* **Show index progression** – Highlight how `j = i+1` and `k = j+1` enforce the required ordering.  
* **Mention early filtering** – “I skip the innermost loop if `|arr[i]-arr[j]| > a`.”  
* **Talk about testing** – “I verify against the provided examples and edge cases with identical numbers.”  

These steps demonstrate that you can *read a problem*, *choose the correct algorithm for the constraints*, and *write clean, bug‑free code*—exactly what interviewers want.

---

## Conclusion  

*Count Good Triplets* is a deceptively simple problem that showcases a programmer’s ability to combine nested loops with multiple conditions.  
With **Java, Python, and C++** implementations already covered, you’re ready to ace the LeetCode challenge and impress interviewers with clear, efficient code.

Happy coding, and good luck in your next interview! 🚀

---

## 🎯 Keywords & SEO Checklist  

| ✅ Item | Detail |
|---------|--------|
| **Title** | Contains “Count Good Triplets”, “LeetCode 1534”, “Java/Python/C++ Solutions” |
| **Meta Description** | 160‑character summary with target keywords |
| **Header Tags** | H1–H4 structure for readability and search indexing |
| **Alt Text** | “Java code snippet for Count Good Triplets” (in images if used) |
| **Internal Links** | Suggest links to other LeetCode “Easy” problems (e.g., “Two Sum”, “Product of Array Except Self”) |
| **Outbound Links** | Reference LeetCode’s problem page, and a few tutorial blogs |
| **Rich Snippet Markup** | JSON‑LD for “HowTo” or “TechArticle” schema |

--- 

### Sample Test Run (Python)

```python
>>> sol = Solution()
>>> sol.countGoodTriplets([3,0,1,1,9,7], 7, 2, 3)
4
>>> sol.countGoodTriplets([1,1,2,2,3], 0, 0, 1)
0
```

---

### Final Takeaway  

While the brute‑force solution is technically straightforward, its clarity and alignment with constraints make it ideal for a quick, bug‑free interview answer.  
Master this pattern and you’ll have a solid talking point for “why I chose this approach” and “what trade‑offs I considered”.  

Good luck – and may your triplet counts be ever in your favor!