---
title: LeetCode 1233. Remove Sub-Folders from the Filesystem - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1233. Remove Sub‑Folders from the Filesystem  
**Java • Python • C++** | **LeetCode** | **Interview‑Ready**  

> **TL;DR** – Sort the paths alphabetically, keep the first folder of each “block” and drop any folder that starts with the previous kept folder + “/”.  
> Complexity: **O(n log n)** time, **O(n)** extra space.  

---

## Table of Contents  

1. [Problem Recap](#problem-recap)  
2. [Intuition & Edge Cases](#intuition-&-edge-cases)  
3. [Greedy Sorted Scan Solution](#greedy-sorted-scan-solution)  
4. [Implementation in Three Languages](#implementation-in-three-languages)  
   - [Java](#java)  
   - [Python](#python)  
   - [C++](#c++)  
5. [Time / Space Complexity](#time--space-complexity)  
6. [Alternate Approaches](#alternate-approaches)  
7. [Common Pitfalls & FAQ](#common-pitfalls--faq)  
8. [Why This Matters for Your Interview](#why-this-matters-for-your-interview)  

---

## Problem Recap  

You’re given a list of absolute folder paths, e.g.  

```text
folder = ["/a", "/a/b", "/c/d", "/c/d/e", "/c/f"]
```

Return the list after removing *any* folder that is a **sub‑folder** of another folder in the list.  
A path `x` is a sub‑folder of `y` if:

1. `x` starts with `y` **and**  
2. the character immediately after `y` is a `'/'`.

The order of the resulting list does not matter.

---

## Intuition & Edge Cases  

* The longest possible path length is 100 characters – we can treat each string as a plain Java/Python string or `std::string` in C++.
* Sorting the array lexicographically guarantees that a parent folder always appears *before* any of its sub‑folders.
* After sorting, we only need to compare the current path with the **last** folder we decided to keep.  
  If the current path starts with `last + '/'` it is a sub‑folder and can be skipped; otherwise we add it to the result.
* Edge cases to watch out for:  
  * A folder that is a prefix of another but **not** a sub‑folder (e.g. `"/a"` vs `"/ab"`).  
  * Duplicate paths – the problem guarantees uniqueness, but be mindful if you adapt the code.  
  * Very long inputs (`4 × 10⁴` paths) – the solution must be O(n log n) to pass.

---

## Greedy Sorted Scan Solution  

1. **Sort** the array of folder paths.  
2. **Iterate** through the sorted list.  
3. For each folder `curr`:
   * If the result list is empty → add `curr`.  
   * Else let `last` be the last folder in the result list.  
     * If `curr` starts with `last + '/'` → skip `curr` (sub‑folder).  
     * Else add `curr`.  

Because sorting places all children after their parent, the comparison with only the *last* kept folder is sufficient.

---

## Implementation in Three Languages  

Below are clean, production‑ready implementations for Java, Python, and C++.

### Java  

```java
import java.util.*;

class Solution {
    public List<String> removeSubfolders(String[] folder) {
        // 1️⃣ Sort lexicographically
        Arrays.sort(folder);

        List<String> result = new ArrayList<>();

        // 2️⃣ Scan and keep only top‑level folders
        for (String curr : folder) {
            if (result.isEmpty()) {
                result.add(curr);
                continue;
            }

            String last = result.get(result.size() - 1);
            // check if curr is a sub‑folder of last
            if (curr.startsWith(last + "/")) {
                continue;          // skip sub‑folder
            }
            result.add(curr);      // keep new top‑level folder
        }
        return result;
    }
}
```

*Time*: `O(n log n)`  
*Space*: `O(n)` (for the result list)

### Python  

```python
from typing import List

class Solution:
    def removeSubfolders(self, folder: List[str]) -> List[str]:
        # 1️⃣ Sort lexicographically
        folder.sort()

        res = []

        # 2️⃣ Greedy scan
        for cur in folder:
            if not res:
                res.append(cur)
                continue

            last = res[-1]
            # sub‑folder if cur starts with last + '/'
            if cur.startswith(last + "/"):
                continue
            res.append(cur)

        return res
```

### C++  

```cpp
#include <vector>
#include <string>
#include <algorithm>

class Solution {
public:
    std::vector<std::string> removeSubfolders(std::vector<std::string>& folder) {
        // 1️⃣ Sort
        std::sort(folder.begin(), folder.end());

        std::vector<std::string> res;

        for (const std::string& cur : folder) {
            if (res.empty()) {
                res.push_back(cur);
                continue;
            }

            const std::string& last = res.back();
            // check sub‑folder
            if (cur.size() > last.size() &&
                cur.compare(0, last.size(), last) == 0 &&
                cur[last.size()] == '/') {
                continue;               // skip
            }
            res.push_back(cur);           // keep
        }
        return res;
    }
};
```

---

## Time / Space Complexity  

| Operation | Time | Space |
|-----------|------|-------|
| Sorting | **O(n log n)** | **O(1)** (in‑place) |
| Scanning | **O(n)** | **O(n)** for the output list |

Total: **O(n log n)** time, **O(n)** auxiliary space.

---

## Alternate Approaches  

| Approach | Pros | Cons |
|----------|------|------|
| **Trie** (prefix tree) | Handles very large alphabets & dynamic insertions | Extra code, higher constant factor |
| **Hash Set + Parent Check** | Can early‑stop if parent is already removed | Need to check all prefixes → O(n²) worst‑case |
| **Sorting + Stack** | Same as above but uses a stack instead of a list | Essentially identical complexity |

The sorted scan is the cleanest for interview settings.

---

## Common Pitfalls & FAQ  

| Question | Answer |
|----------|--------|
| *Does `"/a"` conflict with `"/ab"`?* | `"/a"` is **not** a sub‑folder of `"/ab"` because the character after `"/a"` is not `'/'`. The code correctly uses `startsWith(last + "/")`. |
| *What if the list contains only one folder?* | The algorithm still works: the result will contain that single folder. |
| *Is the order of the returned list important?* | No – LeetCode accepts any order. The algorithm preserves the sorted order, which is fine. |
| *Can we use a `HashSet` to dedupe?* | The problem guarantees uniqueness, so a set isn’t needed. |
| *What about memory limits?* | `O(n)` extra space is acceptable for `n ≤ 4×10⁴`. |

---

## Why This Matters for Your Interview  

1. **Simplicity** – A two‑step sorted scan is easy to explain and implement, reducing the chance of bugs.  
2. **Big‑O Awareness** – Demonstrating that you considered time/space trade‑offs shows maturity.  
3. **Edge‑Case Thinking** – Handling `"/a"` vs `"/ab"` demonstrates careful string manipulation.  
4. **Language Flexibility** – Knowing the same algorithm in Java, Python, and C++ proves you’re comfortable across stacks, a plus for many hiring managers.  

> *Remember*: Interviewers often value **clarity** over cleverness. A sorted scan solution that runs in `O(n log n)` and is straightforward to read is often the best choice.

---

## SEO‑Optimized Summary  

- **Keywords**: *leetcode 1233*, *remove sub folders*, *sorted scan algorithm*, *Java/Python/C++ solution*, *coding interview*, *software engineering interview tips*, *algorithm interview question*, *job interview coding*.  
- **Meta description**: “Learn how to solve LeetCode 1233 – Remove Sub‑Folders from the Filesystem – with clean Java, Python, and C++ code. Understand the sorted scan algorithm, time complexity, edge cases, and why it’s a top choice for software‑engineering interviews.”

---  

Happy coding, and good luck nailing that next interview!