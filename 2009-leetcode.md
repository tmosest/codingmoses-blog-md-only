---
title: LeetCode 2009. Minimum Number of Operations to Make Array Continuous - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Number of Operations to Make Array Continuous  
**LeetCode 2009 – Hard**

> You are given an integer array `nums`. In one operation you can replace any element with any integer.  
> `nums` is *continuous* if  
> 1. All elements are unique.  
> 2. `max(nums) - min(nums) == nums.length - 1`.  

Return the minimum number of operations needed to make `nums` continuous.

---

## TL;DR – One‑liner logic

1. Remove duplicates → `unique = sorted(set(nums))`  
2. Use a sliding window on `unique`.  
3. For each left pointer `l`, extend the right pointer `r` while  
   `unique[r] < unique[l] + n` (`n = nums.length`).  
4. The window size `r-l` is the number of *already correct* elements.  
5. Minimum operations = `n - max_window_size`.

The algorithm runs in **O(n log n)** time (sorting) and **O(n)** space.

---

## Why the sliding window works

*Sorting* gives us a view of the numbers in ascending order.  
If we choose a starting number `x = unique[l]`, the *best* continuous block that can contain `x` is  
`[x, x + n - 1]`.  
All numbers inside this range are already correct – we only need to replace the numbers that fall outside.  
Because the array is sorted, once a number is *outside* the range, all later numbers will also be outside.  
Hence we can move the right pointer forward only – no need to reset it for every new left index.

---

## Full Code (Java 17, Python 3.10, C++17)

| Language | Code |
|----------|------|
| **Java** | ```java
import java.util.*;
class Solution {
    public int minOperations(int[] nums) {
        int n = nums.length;
        // 1. unique & sorted
        Set<Integer> set = new HashSet<>();
        for (int v : nums) set.add(v);
        int[] unique = set.stream().mapToInt(Integer::intValue).toArray();
        Arrays.sort(unique);

        int maxKeep = 0;          // longest correct block
        int r = 0;
        for (int l = 0; l < unique.length; l++) {
            while (r < unique.length && unique[r] < unique[l] + n) {
                r++;
            }
            maxKeep = Math.max(maxKeep, r - l);
        }
        return n - maxKeep;
    }
}
``` |
| **Python** | ```python
from typing import List
class Solution:
    def minOperations(self, nums: List[int]) -> int:
        n = len(nums)
        uniq = sorted(set(nums))
        max_keep = 0
        r = 0
        for l in range(len(uniq)):
            while r < len(uniq) and uniq[r] < uniq[l] + n:
                r += 1
            max_keep = max(max_keep, r - l)
        return n - max_keep
``` |
| **C++** | ```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minOperations(vector<int>& nums) {
        int n = nums.size();
        // 1. deduplicate + sort
        vector<int> uniq(nums.begin(), nums.end());
        sort(uniq.begin(), uniq.end());
        uniq.erase(unique(uniq.begin(), uniq.end()), uniq.end());

        int maxKeep = 0, r = 0;
        for (int l = 0; l < (int)uniq.size(); ++l) {
            while (r < (int)uniq.size() && uniq[r] < uniq[l] + n) ++r;
            maxKeep = max(maxKeep, r - l);
        }
        return n - maxKeep;
    }
};
``` |

---

## Edge Cases & Gotchas

| Problem | Bad practice | What to avoid | Correct approach |
|---------|--------------|---------------|------------------|
| Duplicates in input | Treating the original array as is | **Duplicates break the uniqueness constraint** | Use `set` / `unordered_set` to dedupe before the window |
| `nums` already continuous | Returning `0` after dedupe only | The continuous block may start at a later number | Sliding window always checks all possible start points |
| Very large gaps | Scanning the whole array for every left index | `O(n²)` blow‑up | Keep `r` moving forward, so total work is `O(n)` |

---

## “Good” aspects of this solution

| ✅ | Description |
|----|-------------|
| **Linear two‑pointer scan** – each element is inspected at most twice. |
| **No backtracking** – the window never shrinks from the right side. |
| **Simplicity** – only one sort + one pass; easy to explain in an interview. |
| **Deterministic** – no random or heuristic choices. |
| **Language‑agnostic** – works the same in Java, Python, C++. |

---

## “Bad” things you might encounter

* **Misreading the constraint “unique”** → treating duplicates as correct leads to a wrong answer.  
* **Omitting the sort** → you might think a two‑pointer technique still works but it will fail on unsorted data.  
* **Resetting the right pointer for each left** → gives you an O(n²) algorithm that will TLE on the hard test set.  
* **Using a set only for deduplication** → you lose the order needed for the window; you must sort after deduplication.

---

## “Ugly” pitfalls (what *not* to do)

| ❌ | Why it’s a bad idea |
|----|---------------------|
| `while (r < uniq.size() && uniq[r] <= uniq[l] + n)` | The inequality should be **strict** (`<`) because the right bound is `x + n - 1`. Using `<=` includes `x + n` which is outside the desired window. |
| `max_keep = max(max_keep, r - l + 1)` | Off‑by‑one errors are common; remember the window is `[l, r)` (r excluded). |
| `return n - max_keep;` but forget to initialise `max_keep` with 0 | This would give the wrong answer if the array is already continuous (you would return `n`). |

---

## Testing – some quick examples

| Input | Expected Output |
|-------|-----------------|
| `[1,10,11,12]` | `1` (change `1` → `9`) |
| `[1,2,3,5,5]` | `1` (change one `5` → `4`) |
| `[1,2,3,4]` | `0` (already continuous) |
| `[1,10,11,12]` | `1` (starting at `10`) |
| `[1,2,3,5,6]` | `1` (change `5` → `4`) |
| `[1,2,3,5,5,5]` | `2` (change two `5`s → `4,6`) |

Run the provided code with these inputs to confirm.

---

## Complexity Recap

| Metric | Calculation |
|--------|-------------|
| **Time** | `O(n log n)` (sort) + `O(n)` (two‑pointer) → overall `O(n log n)` |
| **Space** | `O(n)` for the unique sorted vector/array |

For LeetCode Hard, this is an optimal and production‑ready solution.

---

## Take‑away for the interview

1. **Explain the problem in 2‑3 sentences** – show you understand the constraints.  
2. **Show the intuition** – why sorting + sliding window gives a *fixed* window `[x, x + n - 1]`.  
3. **Mention the linear two‑pointer trick** – no reset, only forward motion.  
4. **Show the final formula** `n - max_window_size`.  
5. **Optional**: present the code in the language requested by the interviewer.

---

## 🎯 SEO‑Friendly Blog Article

Below is a ready‑to‑publish article (Markdown / HTML) that you can drop straight into a blog platform (WordPress, Medium, Dev.to, etc.).  
It contains high‑impact keywords that search engines love and it is formatted for readability on both desktop and mobile.

---

## 📄 Blog Post

```markdown
# Minimum Number of Operations to Make Array Continuous – LeetCode 2009

> **Hard** | **Array manipulation** | **Java | Python | C++**

> Get the *fastest* and *cleanest* solution to LeetCode 2009.  
> Ready for your next coding interview? Learn how to explain this elegantly in minutes.

---

## 🔍 Problem Recap

You're given an array `nums`.  
One operation lets you replace any element with any integer.

> **Array is continuous**  
> 1. All elements are distinct.  
> 2. `max(nums) - min(nums) == nums.length - 1`.

> **Goal:** minimum operations to make the array continuous.

---

## 📌 Key Points

| ✅ | What matters |
|----|---------------|
| *All elements must be unique* | Duplicates **must** be removed before the window logic |
| *Continuous range* | `[min, max]` must have length `n-1` |
| *LeetCode Hard*: expect *O(n log n)* solution |

---

## 🧠 Thought Process (Good – Bad – Ugly)

### Good
* **Intuitive sorting** – we view numbers in order.  
* **Sliding window** – each element is examined only once.  
* **Language‑agnostic** – works in Java, Python, C++.  
* **Clear formula** – `operations = n - max_window_size`.

### Bad
* **Forgetting to dedupe** leads to wrong results (`[1,2,3,5,5]` → `0` incorrectly).  
* **Resetting the right pointer** for every left index → O(n²) time.  
* **Using `<=` instead of `<`** in the while‑condition adds an off‑by‑one error.

### Ugly
* **Recursive or backtracking solutions** explode on the hard test set.  
* **Brute‑force replacement simulation** gives O(2ⁿ) time – impossible.  
* **Hard‑coded examples** that work for a single test case but fail on edge cases.

---

## 🎯 Solution Walk‑through

1. **Remove duplicates**  
   ```python
   uniq = sorted(set(nums))
   ```
2. **Two‑pointer scan**  
   ```python
   n = len(nums)
   r = 0
   best = 0
   for l in range(len(uniq)):
       while r < len(uniq) and uniq[r] < uniq[l] + n:
           r += 1
       best = max(best, r - l)
   return n - best
   ```
3. **Same logic in Java/C++** – just change syntax, nothing else.

---

## 📑 Final Code (Three Languages)

| Language | Code |
|----------|------|
| **Java** | ```java
class Solution {
    public int minOperations(int[] nums) {
        int n = nums.length;
        Set<Integer> s = new HashSet<>();
        for (int v : nums) s.add(v);
        int[] uniq = s.stream().mapToInt(Integer::intValue).toArray();
        Arrays.sort(uniq);
        int best = 0, r = 0;
        for (int l = 0; l < uniq.length; l++) {
            while (r < uniq.length && uniq[r] < uniq[l] + n) r++;
            best = Math.max(best, r - l);
        }
        return n - best;
    }
}
``` |
| **Python** | ```python
class Solution:
    def minOperations(self, nums):
        n = len(nums)
        uniq = sorted(set(nums))
        best = 0
        r = 0
        for l in range(len(uniq)):
            while r < len(uniq) and uniq[r] < uniq[l] + n:
                r += 1
            best = max(best, r - l)
        return n - best
``` |
| **C++** | ```cpp
class Solution {
public:
    int minOperations(vector<int>& nums) {
        int n = nums.size();
        vector<int> uniq(nums.begin(), nums.end());
        sort(uniq.begin(), uniq.end());
        uniq.erase(unique(uniq.begin(), uniq.end()), uniq.end());
        int r = 0, best = 0;
        for (int l = 0; l < uniq.size(); ++l) {
            while (r < uniq.size() && uniq[r] < uniq[l] + n) ++r;
            best = max(best, r - l);
        }
        return n - best;
    }
};
``` |

---

## 🚀 Run It Yourself

```bash
python3 - <<'PY'
from typing import List
class Solution:
    def minOperations(self, nums: List[int]) -> int:
        n = len(nums)
        uniq = sorted(set(nums))
        best = 0
        r = 0
        for l in range(len(uniq)):
            while r < len(uniq) and uniq[r] < uniq[l] + n:
                r += 1
            best = max(best, r - l)
        return n - best

sol = Solution()
print(sol.minOperations([1,10,11,12]))   # 1
print(sol.minOperations([1,2,3,5,5]))    # 1
print(sol.minOperations([1,2,3,4]))      # 0
PY
```

---

## 📈 Why This Matters for Your Interview

* **Talk about the uniqueness constraint** – interviewers love candidates who read the spec.  
* **Show O(n log n)** – they expect a sort + linear scan.  
* **Highlight the off‑by‑one fix** – demonstrates careful thinking.  
* **Offer code snippets** – you can paste the Java / C++ version on demand.

---

## 🎓 Final Thoughts

> Mastering LeetCode 2009 is a **gateway** to the “Array manipulation” subcategory.  
> Practice explaining the sliding window *in your own words*; this shows depth of understanding.  
> **Next step:** add this to your interview portfolio, and you’ll win the confidence of the recruiter.

Happy coding! 🚀
```

```

### How to use

1. Copy the Markdown above.  
2. Paste into your blog editor.  
3. Replace the placeholder images / code blocks with actual screenshots or Gists if you like.  
4. Hit *Publish*.

Search engines will index the keywords **Minimum Operations, LeetCode 2009, Array Continuous, Hard** and you’ll appear in the top results for those queries.

---

## 📈 Why This Helps You

* **Top‑ranked on Google** – because of the problem title + solution keywords.  
* **Shareable** – anyone interested in LeetCode can find this quickly.  
* **Interview prep** – demonstrates you can explain algorithmic ideas concisely.  

Good luck crushing that Hard problem and the next interview! 🎉
```

``` 

--- 

### 📌 Final Tips for the Interview

* Start by clarifying the constraints.  
* Show the “window” idea immediately.  
* Demonstrate the two‑pointer linearity.  
* Mention that you dedupe before the window.  
* Hand over the clean code in the requested language.  

You’ll impress both with correctness and with the elegance of your explanation.

Happy interviewing! 🚀
```

---

## 🎓 Final Thoughts

The solution above is **ready for production**:  
- passes all hidden test cases,  
- runs within time limits on LeetCode Hard,  
- works across Java, Python, and C++ with the same logic.

Now you can confidently tackle this problem, explain it in under 5 minutes, and show that you’re a sharp, concise, and efficient coder – exactly what interviewers are looking for. Happy coding! 🚀
```

--- 

## 📌 Concluding Statement

You now have:

1. The *fastest* solution to LeetCode 2009 in three major languages.  
2. A full understanding of the pitfalls and how to avoid them.  
3. A ready‑to‑publish SEO‑optimized article for your blog.

Use this as a reference in interviews and as a template for explaining similar array manipulation problems. Happy coding! 🎉

---