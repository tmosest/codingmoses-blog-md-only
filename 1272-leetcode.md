---
title: LeetCode 1272. Remove Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 1272 – Remove Interval  
> **Java / Python / C++ Solutions + Blog Post**  
> *The good, the bad, and the ugly – and why it’ll land you a job.*

---

## TL;DR  

| Language | Time | Space |
|----------|------|-------|
| **Java** | **O(N)** | **O(1)** (output not counted) |
| **Python** | **O(N)** | **O(1)** (output not counted) |
| **C++** | **O(N)** | **O(1)** |

> 👉 All three implementations run in linear time, need no extra storage, and are trivial to read – perfect for an interview.

---

## 1. Problem Recap

> **Remove Interval**  
> A sorted list of *disjoint* half‑open intervals `intervals` describes a set of real numbers.  
> You are given another interval `toBeRemoved`.  
> **Goal:** Return the set after removing `toBeRemoved` from every interval in `intervals`.  
> The result must also be a sorted list of disjoint intervals.

**Examples**

| Input | Output |
|-------|--------|
| `intervals = [[0,2],[3,4],[5,7]]`, `toBeRemoved = [1,6]` | `[[0,1],[6,7]]` |
| `intervals = [[0,5]]`, `toBeRemoved = [2,3]` | `[[0,2],[3,5]]` |
| `intervals = [[-5,-4],[-3,-2],[1,2],[3,5],[8,9]]`, `toBeRemoved = [-1,4]` | `[[-5,-4],[-3,-2],[4,5],[8,9]]` |

---

## 2. Why This Problem Rocks for Interviews

| ✅ Feature | Why it matters |
|------------|----------------|
| **Linear scanning** | Shows you can process sorted data in one pass. |
| **Edge‑case handling** | Must think about open/closed ends and overlapping. |
| **Space‑efficiency** | O(1) extra space (besides output) is a nice interview brag. |
| **Multiple language support** | Demonstrates you can translate logic across Java, Python, and C++. |

---

## 3. The Core Idea

1. **Split each interval if it overlaps with `toBeRemoved`.**  
   Because the intervals are *disjoint and sorted*, we only need to check two possibilities for each:
   * **Left part** – the part *before* `toBeRemoved` (if any).
   * **Right part** – the part *after* `toBeRemoved` (if any).

2. **Discard the middle overlapped segment.**

3. **Collect the valid parts in the same order.**

Since the input is sorted, we can simply iterate once – **O(N)** time.

---

## 4. The Code (Java, Python, C++)

### 4.1 Java – Linear‑time, Concise

```java
import java.util.*;

class Solution {
    public List<List<Integer>> removeInterval(int[][] intervals, int[] toBeRemoved) {
        int start = toBeRemoved[0];
        int end   = toBeRemoved[1];
        List<List<Integer>> result = new ArrayList<>();

        for (int[] interval : intervals) {
            // Left side: [interval[0], min(interval[1], start))
            if (interval[0] < start) {
                result.add(Arrays.asList(interval[0], Math.min(interval[1], start)));
            }

            // Right side: [max(interval[0], end), interval[1])
            if (interval[1] > end) {
                result.add(Arrays.asList(Math.max(interval[0], end), interval[1]));
            }
        }
        return result;
    }
}
```

> **Why it’s great**  
> *Uses only the standard library.*  
> *No explicit `List<List<Integer>>` construction overhead.*  
> *All logic stays inside one loop.*

---

### 4.2 Python – One‑liner‑ish, Readable

```python
from typing import List

class Solution:
    def removeInterval(self, intervals: List[List[int]], toBeRemoved: List[int]) -> List[List[int]]:
        s, e = toBeRemoved
        res = []

        for a, b in intervals:
            if a < s:                      # left part
                res.append([a, min(b, s)])
            if b > e:                      # right part
                res.append([max(a, e), b])

        return res
```

> **Why it’s great**  
> *Pythonic, minimal boilerplate.*  
> *Directly expresses the “left / right” logic.*

---

### 4.3 C++ – Modern, OOP‑free

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> removeInterval(vector<vector<int>>& intervals, vector<int>& toBeRemoved) {
        int s = toBeRemoved[0], e = toBeRemoved[1];
        vector<vector<int>> ans;

        for (const auto& iv : intervals) {
            int a = iv[0], b = iv[1];

            if (a < s)                           // left side
                ans.push_back({a, min(b, s)});

            if (b > e)                           // right side
                ans.push_back({max(a, e), b});
        }
        return ans;
    }
};
```

> **Why it’s great**  
> *Uses `vector` and range‑based `for`.*  
> *No dynamic memory beyond the output vector.*

---

## 5. Edge Cases & Pitfalls (The Ugly)

| Scenario | Why it can fail | Fix |
|----------|-----------------|-----|
| `toBeRemoved` fully contains an interval | Both left and right checks are true but produce empty ranges | The logic automatically discards the interval because `min(b,s)` ≤ `max(a,e)`. No code change needed. |
| `toBeRemoved` starts or ends exactly on an interval boundary | Off‑by‑one errors if you use closed intervals | We’re using half‑open `[a,b)` representation; the `min` / `max` checks keep boundaries correct. |
| Empty result set | All intervals removed | Our code simply returns an empty vector/list – correct. |
| Large numbers (int overflow) | `int` might overflow if `ai`, `bi` near 10⁹ | Use 64‑bit (`long long`/`long`) if language permits; LeetCode’s constraints fit in 32‑bit, so we’re safe. |

---

## 6. Complexity Analysis

| Measure | Java | Python | C++ |
|---------|------|--------|-----|
| **Time** | `O(N)` – one pass over `intervals` | `O(N)` | `O(N)` |
| **Space** | `O(1)` extra – output not counted | `O(1)` | `O(1)` |

*`N = intervals.length`*.

---

## 7. Test Harness (Python Example)

```python
def test():
    sol = Solution()
    assert sol.removeInterval([[0,2],[3,4],[5,7]], [1,6]) == [[0,1],[6,7]]
    assert sol.removeInterval([[0,5]], [2,3]) == [[0,2],[3,5]]
    assert sol.removeInterval([[-5,-4],[-3,-2],[1,2],[3,5],[8,9]], [-1,4]) == [[-5,-4],[-3,-2],[4,5],[8,9]]
    assert sol.removeInterval([[0,1]], [0,1]) == []
    assert sol.removeInterval([[0,5],[6,10]], [2,7]) == [[0,2],[7,10]]
    print("All tests passed!")

test()
```

---

## 8. The Good, The Bad, The Ugly

| Category | What you get | Where you can shine |
|----------|--------------|--------------------|
| **Good** | Linear, O(1) space; easy to reason; works for all edge cases | Emphasize “one‑pass, clean logic” in interviews. |
| **Bad** | None really – the algorithm is straightforward | Be careful not to over‑complicate (e.g., unnecessary `while` loops). |
| **Ugly** | Some languages use verbose syntax (`Arrays.asList` in Java) | Use helper functions or language idioms to keep code tidy. |

---

## 9. SEO‑Friendly Takeaway

- **Keywords**: `Remove Interval`, `LeetCode 1272`, `Java solution`, `Python solution`, `C++ solution`, `interval removal problem`, `coding interview question`, `algorithm interview`, `job interview interview coding`, `disjoint intervals`.
- **Meta Description**: “Solve LeetCode 1272 – Remove Interval in Java, Python, and C++ with linear‑time code. Read our full blog post covering the algorithm, edge cases, and interview tips.”

---

## 10. Final Thoughts

You’ve just mastered a classic interview question that tests:

1. **Array traversal**  
2. **Boundary conditions**  
3. **Space‑efficiency**

With these three implementations, you can confidently answer “Can you explain how you’d solve Remove Interval?” in any technical interview. Remember to talk through the logic, walk through edge cases, and showcase the clean, linear‑time solution. Good luck landing that dream job! 🚀

---