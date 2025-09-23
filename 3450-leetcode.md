---
title: LeetCode 3450. Maximum Students on a Single Bench - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Mastering LeetCode 3450 – “Maximum Students on a Single Bench”

> **Want to land your dream software‑engineering job?**  
> Mastering classic LeetCode problems is the fastest way to prove your coding chops to recruiters.  
> This post walks through every angle of the *Maximum Students on a Single Bench* challenge – from the cleanest solution to edge‑cases, pitfalls, and even a quick “SEO‑friendly” guide to help your article rank high on Google and get you noticed by hiring managers.

---

### 📄 Problem Recap (LeetCode 3450)

> **You are given a 2‑D integer array `students`, where each `students[i] = [student_id, bench_id]`**  
> indicating that a student with `student_id` sits on bench `bench_id`.  
> **Return the maximum number of *unique* students sitting on any single bench.**  
> If no students are present, return `0`.

> **Constraints**

| | |
|---|---|
| `0 ≤ students.length ≤ 100` | `1 ≤ student_id, bench_id ≤ 100` |

---

### 🏆 Quick Solution Snapshot

| Language | Code (≈ 4–6 lines) |
|---|---|
| **Python** | `def maxStudentsOnBench(self, students):`<br>`d = defaultdict(set)`<br>`for s, b in students: d[b].add(s)`<br>`return max(map(len, d.values()), default=0)` |
| **Java** | `class Solution { public int maxStudentsOnBench(int[][] students) { ... } }` |
| **C++** | `int maxStudentsOnBench(vector<vector<int>>& students) { ... }` |

> All solutions run in **O(n)** time, **O(n)** space – optimal for the given constraints.

---

## 📌 Detailed Walkthrough

### 1️⃣ Core Idea

- **Group students by bench** → `bench_id → {unique student_id}`  
- **Count unique students** for each bench → size of the set  
- **Return the maximum** among all benches

Using a *set* automatically handles duplicate `student_id`s on the same bench.

---

### 2️⃣ Edge Cases

| Case | Why it matters | Handling |
|---|---|---|
| `students` is empty | No benches → answer `0` | `max(..., default=0)` in Python, check `benchToStudents.isEmpty()` in Java/C++ |
| Duplicate entries | A student may appear multiple times | `Set` removes duplicates |
| Bench IDs not contiguous | Bench IDs can be any value between 1–100 | Use a `Map` / hash‑table rather than a fixed array (though array works too because limits are tiny) |

---

### 3️⃣ Implementation Details

#### Python 3

```python
from collections import defaultdict
from typing import List

class Solution:
    def maxStudentsOnBench(self, students: List[List[int]]) -> int:
        # bench_id → set of unique student_id
        bench_to_students = defaultdict(set)

        for student_id, bench_id in students:
            bench_to_students[bench_id].add(student_id)

        # If bench_to_students is empty, max returns 0
        return max(map(len, bench_to_students.values()), default=0)
```

- **Time**: `O(n)` – one pass over `students`.  
- **Space**: `O(n)` – each unique student occupies a set entry.

---

#### Java 17

```java
import java.util.*;

class Solution {
    public int maxStudentsOnBench(int[][] students) {
        // bench_id -> set of student_id
        Map<Integer, Set<Integer>> benchMap = new HashMap<>();

        for (int[] pair : students) {
            int studentId = pair[0];
            int benchId   = pair[1];
            benchMap.computeIfAbsent(benchId, k -> new HashSet<>()).add(studentId);
        }

        int max = 0;
        for (Set<Integer> set : benchMap.values()) {
            max = Math.max(max, set.size());
        }
        return max;          // automatically 0 if benchMap is empty
    }
}
```

- `computeIfAbsent` lazily creates a `HashSet` when encountering a new bench.  
- Same `O(n)` time and space.

---

#### C++17

```cpp
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>

class Solution {
public:
    int maxStudentsOnBench(std::vector<std::vector<int>>& students) {
        // bench_id -> set of student_id
        std::unordered_map<int, std::unordered_set<int>> benchMap;

        for (const auto& p : students) {
            int studentId = p[0];
            int benchId   = p[1];
            benchMap[benchId].insert(studentId);
        }

        int maxCnt = 0;
        for (const auto& kv : benchMap) {
            maxCnt = std::max(maxCnt, static_cast<int>(kv.second.size()));
        }
        return maxCnt;          // 0 if benchMap empty
    }
};
```

- Uses `unordered_map`/`unordered_set` for average‑case `O(1)` operations.

---

### 4️⃣ “Good, the Bad, and the Ugly”

| Category | ✅ Good | ⚠️ Bad | 👿 Ugly |
|---|---|---|---|
| **Clarity** | `set` + `map` express intent clearly | None | Some interviewers prefer a “raw array” solution for micro‑optimizations |
| **Performance** | `O(n)` time, `O(n)` space – optimal | Constant factor overhead of hash‑tables | Using a 2‑D array (size 101×101) is still fine, but memory waste if many benches unused |
| **Robustness** | Handles duplicates automatically | None | None |
| **Readability** | Minimal boilerplate | Using `default=0` in Python may confuse beginners | None |

---

## 📈 Complexity Analysis

| | **Time** | **Space** |
|---|---|---|
| **Python** | `O(n)` | `O(n)` |
| **Java** | `O(n)` | `O(n)` |
| **C++** | `O(n)` | `O(n)` |

- `n = students.length` (max 100).  
- All solutions are well within the LeetCode limits.

---

## 🔍 Alternatives & Trade‑offs

1. **Using a 2‑D boolean array**  
   - `bool occupied[101][101]` → `bench_id`, `student_id` indexing.  
   - Fast and memory‑friendly for the problem’s bounds but less flexible if bench/student IDs grew.

2. **Sorting + Linear Scan**  
   - Sort by `bench_id` then count unique `student_id`s per bench.  
   - `O(n log n)` time; unnecessary extra work for this simple problem.

3. **Bitmasking**  
   - Since IDs ≤ 100, we could use a 128‑bit mask per bench.  
   - Overkill and less readable.

---

## 🚀 SEO‑Optimized Blog Post Structure

1. **Title (SEO)** – `Maximum Students on a Single Bench – LeetCode 3450: Java, Python, C++ Solutions & Interview Tips`
2. **Meta Description** – `Learn the optimal solution to LeetCode 3450, with clean Java, Python, and C++ code. Understand time/space complexity, edge cases, and interview insights to boost your job search.`
3. **Header Tags** –  
   - `# Maximum Students on a Single Bench (LeetCode 3450)`  
   - `## Problem Statement`  
   - `## Solution Overview`  
   - `## Python Implementation`  
   - `## Java Implementation`  
   - `## C++ Implementation`  
   - `## Complexity Analysis`  
   - `## Good, Bad, Ugly`  
   - `## Interview Tips & Job‑Hiring Advice`
4. **Keywords** – `LeetCode, interview coding, maximum students, Java, Python, C++, hash map, hash set, data structures, time complexity, space complexity, job interview, software engineer interview, coding interview questions`
5. **Internal/External Links** – Link to LeetCode problem, related tutorials, and your portfolio or LinkedIn.
6. **Call‑to‑Action** – `Like, comment, and share if you found this helpful. Want more interview prep? Subscribe to my newsletter!`

---

## 🎯 Final Takeaway

- The **set‑in‑a‑map** pattern is the cleanest, fastest, and most idiomatic way to solve “Maximum Students on a Single Bench.”  
- It gracefully handles duplicates and empty inputs with minimal code.  
- Understanding this pattern demonstrates mastery of **hash tables**, **set operations**, and **edge‑case reasoning**—key skills interviewers look for.

> **Want to impress recruiters?**  
> Drop this article on your blog, add it to your portfolio, and share it on LinkedIn. The combination of a clear solution + SEO‑friendly write‑up is a proven way to get noticed and land your next software‑engineering role. 🚀

--- 

### 📚 Bonus: Full Code Snippets

```python
# Python 3
from collections import defaultdict
from typing import List

class Solution:
    def maxStudentsOnBench(self, students: List[List[int]]) -> int:
        d = defaultdict(set)
        for s, b in students:
            d[b].add(s)
        return max(map(len, d.values()), default=0)
```

```java
// Java 17
import java.util.*;

class Solution {
    public int maxStudentsOnBench(int[][] students) {
        Map<Integer, Set<Integer>> bench = new HashMap<>();
        for (int[] p : students) {
            bench.computeIfAbsent(p[1], k -> new HashSet<>()).add(p[0]);
        }
        int max = 0;
        for (Set<Integer> set : bench.values()) {
            max = Math.max(max, set.size());
        }
        return max;
    }
}
```

```cpp
// C++17
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>

class Solution {
public:
    int maxStudentsOnBench(std::vector<std::vector<int>>& students) {
        std::unordered_map<int, std::unordered_set<int>> bench;
        for (auto& p : students) bench[p[1]].insert(p[0]);

        int mx = 0;
        for (auto& kv : bench) mx = std::max(mx, static_cast<int>(kv.second.size()));
        return mx;
    }
};
```

Happy coding and good luck on your interview journey!