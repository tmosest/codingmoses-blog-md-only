---
title: LeetCode 2491. Divide Players Into Teams of Equal Skill - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 2491 – Divide Players Into Teams of Equal Skill  
**Languages**: Java, Python, C++  
**Complexity**: O(n log n) time, O(1) extra space (in‑place sort)  

---

### 1. Problem Recap  

You’re given an **even‑length** array `skill` (`1 ≤ skill[i] ≤ 1000`).  
You have to pair the players (size 2) so that **every pair has the same total skill**.  
If that is possible, return the **sum of the chemistry** (product of skills in each pair); otherwise return `-1`.

---

### 2. Why Sorting + Two‑Pointers Works  

1. **Sort** the array – then the smallest value is at the front and the largest at the back.  
2. If a valid partition exists, the pair that contains the *smallest* value must also contain the *largest* value, because any other choice would make the sum smaller than the required target.  
3. After fixing the first pair, the next smallest and next largest must again add up to the same target, and so on.  
4. If any pair deviates from that sum, a valid partition is impossible.  
5. While pairing we also accumulate the product to get the total chemistry.

Thus a single linear scan after sorting gives the answer.

---

### 3. Code Implementations  

Below are clean, production‑ready solutions for **Java**, **Python**, and **C++**.

---

#### 3.1 Java  

```java
import java.util.Arrays;

public class Solution {
    public long dividePlayers(int[] skill) {
        Arrays.sort(skill);                // O(n log n)
        int n = skill.length;
        int target = skill[0] + skill[n-1]; // sum that every pair must reach
        long chemistry = 0;

        for (int i = 0; i < n / 2; i++) {
            if (skill[i] + skill[n-1-i] != target) {
                return -1; // impossible
            }
            chemistry += (long) skill[i] * skill[n-1-i];
        }
        return chemistry;
    }
}
```

**Explanation**  
* `Arrays.sort(skill)` sorts the array in‑place.  
* The loop runs `n/2` times – constant extra space.  
* The cast to `long` prevents overflow (max product 1000 × 1000 = 1 000 000, safe, but sum may reach 10^11 for n = 10^5).

---

#### 3.2 Python 3  

```python
from typing import List

class Solution:
    def dividePlayers(self, skill: List[int]) -> int:
        skill.sort()                       # O(n log n)
        n = len(skill)
        target = skill[0] + skill[-1]      # required pair sum
        chemistry = 0

        for i in range(n // 2):
            if skill[i] + skill[n - 1 - i] != target:
                return -1
            chemistry += skill[i] * skill[n - 1 - i]
        return chemistry
```

**Why Python Works**  
* `list.sort()` modifies the list in‑place.  
* Integer arithmetic in Python is unbounded, so no overflow worries.  
* The logic is identical to Java and C++.

---

#### 3.3 C++ (GNU ++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long dividePlayers(vector<int>& skill) {
        sort(skill.begin(), skill.end());          // O(n log n)
        int n = skill.size();
        int target = skill.front() + skill.back(); // pair sum that must match

        long long chemistry = 0;
        for (int i = 0; i < n / 2; ++i) {
            if (skill[i] + skill[n - 1 - i] != target) {
                return -1;                         // impossible
            }
            chemistry += 1LL * skill[i] * skill[n - 1 - i];
        }
        return chemistry;
    }
};
```

**Notes**  
* `1LL * ...` forces 64‑bit multiplication.  
* `sort()` uses introsort – fast and stable for most inputs.  

---

### 4. Complexity Analysis  

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | **O(n log n)** | **O(1)** (in‑place) |
| Pairing & sum | **O(n)** | **O(1)** |
| **Total** | **O(n log n)** | **O(1)** |

`n ≤ 10^5`, so the solution easily passes all LeetCode tests.

---

### 5. The Good, The Bad, & The Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic elegance** | Sorting + two‑pointers is *O(n log n)*, optimal for arbitrary values | Requires sorting – can't be reduced to linear time without extra constraints | If the input is already sorted, we still sort (unnecessary overhead) |
| **Implementation simplicity** | All languages share almost identical code | Very few lines, but forgetting the `long` cast in Java/C++ causes overflow on huge inputs | Debugging overflow bugs in C++ can be subtle |
| **Memory usage** | In‑place sort – negligible overhead | Requires a copy if we want to preserve the original array | Some online judges disallow in‑place mutation – must copy anyway |
| **Edge‑cases** | Handles single‑pair arrays, duplicates, and large values | None – algorithm is deterministic | If `skill.length` is odd (though constraints forbid it) → undefined behaviour |
| **Readability** | Clear variable names (`target`, `chemistry`) | Very terse – a novice might struggle | None – the code is intentionally short, but documentation is still needed |

---

### 6. SEO‑Optimized Blog Article  

> **Title**: *LeetCode 2491 – Divide Players Into Teams of Equal Skill: Java, Python, & C++ Solutions*  
> **Meta Description**: Learn the optimal O(n log n) algorithm for LeetCode 2491. Get step‑by‑step Java, Python, and C++ code, plus a deep dive into the two‑pointer strategy.  

---

#### 6.1 Introduction  

If you’re hunting for that next **LeetCode interview question**, “Divide Players Into Teams of Equal Skill” (Problem 2491) is a classic *medium* challenge that tests sorting, two‑pointer logic, and careful handling of integer overflow.  
In this post we’ll walk through a clean, production‑ready solution, provide implementations in **Java**, **Python**, and **C++**, and highlight the *good*, *bad*, and *ugly* aspects of the approach.

> **Keywords**: LeetCode 2491, divide players into teams, equal skill, Java solution, Python solution, C++ solution, two pointers, sorting algorithm, competitive programming, algorithm design

---

#### 6.2 Problem Statement (Re‑phrased for Clarity)

You’re given an array `skill[]` of even length.  
The task: pair the players such that *every pair has the same total skill*.  
If that partition is possible, return the sum of each pair’s *chemistry* (product of skills).  
Otherwise, return `-1`.

*Constraints*:  
- `2 ≤ skill.length ≤ 10^5` (even)  
- `1 ≤ skill[i] ≤ 1000`

---

#### 6.3 Why Sorting + Two‑Pointers is the Key  

1. After sorting, the smallest (`skill[0]`) and largest (`skill[n-1]`) must form the **target pair sum**.  
2. The next smallest and largest (`skill[1]` & `skill[n-2]`) must also add up to that target, and so on.  
3. A single linear pass validates the partition *and* accumulates the chemistry.

The beauty: the algorithm is **O(n log n)** due to the sort, and **O(1)** extra memory.

---

#### 6.4 Step‑by‑Step Pseudocode  

```
sort(skill)
target = skill[0] + skill[last]
chemistry = 0
for i in 0 .. n/2 - 1
    if skill[i] + skill[n-1-i] != target
        return -1
    chemistry += skill[i] * skill[n-1-i]
return chemistry
```

---

#### 6.5 Full Code – Three Languages  

> *The following snippets are ready to paste into LeetCode’s editor.*

**Java**  
```java
import java.util.Arrays;
public class Solution {
    public long dividePlayers(int[] skill) {
        Arrays.sort(skill);
        int n = skill.length, target = skill[0] + skill[n-1];
        long chemistry = 0;
        for (int i=0;i<n/2;i++) {
            if (skill[i] + skill[n-1-i] != target) return -1;
            chemistry += (long) skill[i] * skill[n-1-i];
        }
        return chemistry;
    }
}
```

**Python**  
```python
class Solution:
    def dividePlayers(self, skill: List[int]) -> int:
        skill.sort()
        n, target = len(skill), skill[0] + skill[-1]
        chem = 0
        for i in range(n//2):
            if skill[i] + skill[n-1-i] != target: return -1
            chem += skill[i] * skill[n-1-i]
        return chem
```

**C++**  
```cpp
class Solution {
public:
    long long dividePlayers(vector<int>& skill) {
        sort(skill.begin(), skill.end());
        int n = skill.size(), target = skill.front() + skill.back();
        long long chem = 0;
        for (int i=0;i<n/2;i++) {
            if (skill[i] + skill[n-1-i] != target) return -1;
            chem += 1LL * skill[i] * skill[n-1-i];
        }
        return chem;
    }
};
```

---

#### 6.6 Complexity & Edge Cases  

* **Time**: `O(n log n)` – dominated by the sort.  
* **Space**: `O(1)` – sort in place, only a few primitive variables.  
* **Overflow**: In Java/C++ use `long`/`long long`; Python’s ints are arbitrary‑precision.

---

#### 6.7 The Good, Bad, and Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| Algorithm | Simple, optimal O(n log n) | Requires sorting even if already sorted | Unnecessary sort if input already sorted |
| Code | Almost identical across languages | Forgetting 64‑bit cast in Java/C++ causes overflow | Debugging subtle C++ overflow bugs |
| Readability | Clear names (`target`, `chemistry`) | Extremely short – novices may miss the `long` cast | None – code is concise but needs documentation |

---

#### 6.8 Takeaway for Interviews  

* Highlight the **two‑pointer** intuition: smallest + largest must match the target.  
* Mention **integer overflow** handling in statically typed languages.  
* Show that the algorithm is *deterministic* and *fast* – perfect talking points for a software engineering interview.

---

#### 6.9 Final Words  

Problem 2491 is a great showcase of how a classic *sorting + two‑pointers* trick can turn a seemingly combinatorial pairing problem into a single linear pass.  
With the Java, Python, and C++ code snippets above you’ll be ready to **solve** it on LeetCode or **crush** it in a live interview.

Happy coding! 🚀  

---  

**Search‑engine friendly tags**  
`#LeetCode2491`, `#DividePlayersIntoTeamsOfEqualSkill`, `#JavaSolution`, `#PythonSolution`, `#CppSolution`, `#TwoPointers`, `#SortingAlgorithm`, `#CompetitiveProgramming`, `#InterviewPrep`, `#AlgorithmDesign`  

---  

Happy interviewing!