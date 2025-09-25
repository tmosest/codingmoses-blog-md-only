---
title: LeetCode 491. Non-decreasing Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 491. Non‑Decreasing Subsequences  
> **Medium** | **Backtracking + HashSet** | **Time O(n · 2ⁿ)** (worst‑case)  

---

### 📌 Problem Statement (LeetCode)

> **Given** an integer array `nums`, return **all** the *different* possible non‑decreasing subsequences of the given array with *at least two* elements.  
> The answer can be in any order.

**Example**

| Input | Output |
|-------|--------|
| `[4,6,7,7]` | `[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]` |
| `[4,4,3,2,1]` | `[[4,4]]` |

**Constraints**

- `1 ≤ nums.length ≤ 15`
- `-100 ≤ nums[i] ≤ 100`

---

## 🔧 Solution Overview

The key challenges:

1. **Non‑decreasing order** – a subsequence may only be extended if the new element is **≥** the last element in the current path.
2. **Duplicates** – the input may contain repeated numbers, so many different paths can produce the *same* subsequence.  
   We must output each subsequence **once**.

The classic approach is **backtracking** (DFS) with a **`HashSet`** to avoid duplicates.

### Backtracking Steps

1. Start with an empty path `cur`.
2. For every index `i` from `start` to `n-1`:
   - If `cur` is empty or `nums[i] ≥ cur.last`, we can add `nums[i]` to `cur`.
   - Recursively continue from `i+1`.
   - Backtrack by removing `nums[i]`.
3. Whenever the current path length ≥ 2, add a *copy* of it to a global `Set<List<Integer>>` (to deduplicate).
4. After exploring all possibilities, return the set converted to a list.

### Avoiding Duplicates Efficiently

- Because the array length is at most 15, the total number of subsequences is manageable (`≤ 2¹⁵ = 32,768`).  
- Still, duplicates can be frequent (e.g., `[4,4,4]`).  
- Using a `Set<List<Integer>>` automatically deduplicates, and the overhead is negligible for the given limits.

---

## 🧩 Code Implementations

Below are clean, well‑commented implementations in **Java**, **Python**, and **C++**.

---

### 1️⃣ Java

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> findSubsequences(int[] nums) {
        Set<List<Integer>> res = new HashSet<>();
        dfs(nums, 0, new ArrayList<>(), res);
        return new ArrayList<>(res);
    }

    private void dfs(int[] nums, int index, List<Integer> cur, Set<List<Integer>> res) {
        if (cur.size() >= 2) {
            // Store a *copy* so later modifications don't affect the set entry
            res.add(new ArrayList<>(cur));
        }

        for (int i = index; i < nums.length; i++) {
            if (cur.isEmpty() || nums[i] >= cur.get(cur.size() - 1)) {
                cur.add(nums[i]);
                dfs(nums, i + 1, cur, res);
                cur.remove(cur.size() - 1);   // backtrack
            }
        }
    }
}
```

> **Why a `HashSet`?**  
> Java’s `HashSet` uses `equals()`/`hashCode()` on lists, so duplicate sequences are automatically discarded.

---

### 2️⃣ Python

```python
from typing import List, Set, Tuple

class Solution:
    def findSubsequences(self, nums: List[int]) -> List[List[int]]:
        result: Set[Tuple[int, ...]] = set()
        self.backtrack(nums, 0, [], result)
        return [list(t) for t in result]

    def backtrack(self, nums: List[int], index: int,
                  path: List[int], result: Set[Tuple[int, ...]]) -> None:
        if len(path) >= 2:
            result.add(tuple(path))          # tuples are hashable

        for i in range(index, len(nums)):
            if not path or nums[i] >= path[-1]:
                path.append(nums[i])
                self.backtrack(nums, i + 1, path, result)
                path.pop()                   # backtrack
```

> **Why tuples?**  
> Python’s `set` only accepts hashable objects. Converting the list to a tuple allows us to store subsequences without duplicates.

---

### 3️⃣ C++

```cpp
#include <vector>
#include <set>
using namespace std;

class Solution {
public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        set<vector<int>> res;
        vector<int> cur;
        dfs(nums, 0, cur, res);
        return vector<vector<int>>(res.begin(), res.end());
    }

private:
    void dfs(const vector<int>& nums, int idx,
             vector<int>& cur, set<vector<int>>& res) {
        if (cur.size() >= 2) res.insert(cur);

        for (int i = idx; i < nums.size(); ++i) {
            if (cur.empty() || nums[i] >= cur.back()) {
                cur.push_back(nums[i]);
                dfs(nums, i + 1, cur, res);
                cur.pop_back();                 // backtrack
            }
        }
    }
};
```

> **C++ `set`** automatically orders and deduplicates the subsequences.

---

## 📊 Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| DFS exploration | **O(n · 2ⁿ)** (worst‑case) – every subset may be visited | **O(n)** recursion depth |
| Set insertion | **O(k log k)** per insertion, where *k* is the number of unique subsequences (≤ 2ⁿ) | **O(k · n)** total for all stored subsequences |

Given `n ≤ 15`, the algorithm comfortably fits within time limits.

---

## 🎯 Interview‑Ready Tips

| Good | Bad | Ugly |
|------|-----|------|
| ✅ Use backtracking to generate subsequences incrementally. | ❌ Do *not* sort the array – order matters! | ❌ Avoid `String` concatenation for each path; it blows up memory. |
| ✅ Store results in a `Set` (or `unordered_set` / `HashSet`) to dedupe automatically. | ❌ Forget to convert the path to a *copy* before adding to the set; subsequent backtracking corrupts entries. | ❌ Ignoring the *at least two* constraint; adding all subsequences of length 1 leads to wrong answer. |
| ✅ Explain time/space complexity clearly. | ❌ Failing to discuss why duplicates appear (repeated numbers). | ❌ Not handling negative numbers – ensure comparison is `>=`. |

---

## 📝 Blog Article: “The Good, the Bad, and the Ugly of LeetCode’s Non‑Decreasing Subsequences”

> **Meta‑Title:** *Master LeetCode 491: Non‑Decreasing Subsequences – The Good, Bad & Ugly (Java, Python, C++)*  
> **Meta‑Description:** *Discover the step‑by‑step solution to LeetCode 491. Learn the pitfalls, code in Java/Python/C++, and ace your coding interview. Boost your resume today!*

---

### 1️⃣ What Makes This Problem “Medium”  
- Small input size (`n ≤ 15`) but a combinatorial explosion of possible subsequences.  
- Requires careful handling of **duplicates** – a common interview trap.  
- Must respect the **non‑decreasing** constraint while generating all valid subsets.

---

### 2️⃣ The Good: Elegant Backtracking + HashSet

- **Backtracking** gives a natural recursive structure: at each index decide whether to *include* or *skip* the current element.
- **HashSet** (or `set` in C++ / `Set` in Java) removes the burden of manual duplicate checks.
- Clean, readable code that can be adapted to many “subsequence” variants.

---

### 3️⃣ The Bad: Common Pitfalls

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| *Not copying the path before inserting into the set* | The same `List` instance is mutated later. | Create a new `ArrayList<>(path)` (or `tuple(path)` in Python). |
| *Adding subsequences of length 1* | Problem states *at least two* elements. | Check `path.size() >= 2` before adding. |
| *Using a global list instead of a set* | Duplicates survive because list equality isn’t used automatically. | Use a `Set` container. |

---

### 4️⃣ The Ugly: Performance Jitters

- **Time**: In the worst case (e.g., all elements equal) the recursion explores `2ⁿ` paths. For `n=15`, it’s about 32k, which is fine.  
- **Space**: Storing every unique subsequence could blow up if the array contains many distinct values. However, the constraints keep this manageable.  
- **Avoid**: Excessive string concatenation, deep recursion beyond `n` (but here `n ≤ 15`).

---

### 5️⃣ Code in Three Languages – One for Every Stack

> *See the implementations above.*  
> Pick the language you’re most comfortable with, or showcase all three on your GitHub.

---

### 6️⃣ SEO‑Friendly Takeaways for Job Seekers

- **Keywords**: “LeetCode 491 solution”, “Non‑decreasing subsequences”, “Backtracking interview”, “Java Python C++”, “Coding interview tips”.
- **Headers**: Use H1 for the title, H2 for sections like “Good”, “Bad”, “Ugly”, and H3 for sub‑points.
- **Meta Tags**: Include the problem number and key terms in the meta‑description.
- **Link Building**: Reference the official LeetCode problem, similar solutions, and your own GitHub repo.
- **Engagement**: Add a short “What I learned” paragraph to encourage comments.

---

### 7️⃣ Final Checklist Before You Submit

- ✅ Test with the provided examples.  
- ✅ Add edge cases: all negative numbers, single repeated element, strictly increasing array.  
- ✅ Ensure the recursion depth doesn’t exceed stack limits.  
- ✅ Run time and memory tests (LeetCode’s “Runtime” and “Memory” sections).  

---

## 🚀 Takeaway

LeetCode 491 is a *classic* interview puzzle that tests both algorithmic thinking and attention to detail. By mastering the backtracking pattern and the deduplication trick, you’ll not only solve this problem but also gain a reusable toolkit for a wide range of “subsequence” questions.  

Now go ahead, polish your solution, and share it on your portfolio. Good luck landing that dream job!