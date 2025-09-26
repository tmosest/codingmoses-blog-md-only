---
title: LeetCode 2200. Find All K-Distant Indices in an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 📌 LeetCode #2200 – Find All K‑Distant Indices in an Array  
**Languages** – Java | Python | C++  
**Target** – Interview‑ready, Job‑search SEO, “Good‑Bad‑Ugly” breakdown

> **SEO Keywords**: *LeetCode 2200, K‑Distant Indices, interview coding challenge, Java interview problem, Python interview problem, C++ interview problem, job interview algorithm, coding interview prep, algorithmic problem solving*

---

### 1️⃣ Problem Statement

You’re given a 0‑indexed array `nums`, a `key`, and an integer `k`.  
An index `i` is **k‑distant** if there exists at least one index `j` such that  

```
|i – j| ≤ k  and  nums[j] == key
```

Return **all** k‑distant indices sorted in ascending order.

| Constraint | Value |
|------------|-------|
| 1 ≤ nums.length ≤ 1000 | |
| 1 ≤ nums[i] ≤ 1000 | |
| 1 ≤ k ≤ nums.length | |

> **Examples**  
> *Input:* `nums = [3,4,9,1,3,9,5]`, `key = 9`, `k = 1` → *Output:* `[1,2,3,4,5,6]`  
> *Input:* `nums = [2,2,2,2,2]`, `key = 2`, `k = 2` → *Output:* `[0,1,2,3,4]`

---

### 2️⃣ Intuition (the “Why”)

Every occurrence of `key` expands a “coverage window” of length `2k+1` centered at its index.  
All indices that lie inside **any** of these windows are k‑distant.  
Instead of checking each pair `(i, j)` (which would be `O(n²)`), we can:

1. **Record** the start of each window (`j-k`) and the end (`j+k`).
2. **Sweep** through the array once, adding indices that fall inside an *active* window.
3. Use a pointer to avoid re‑adding the same indices, guaranteeing `O(n)` time.

---

### 3️⃣ “Good, Bad, Ugly” Breakdown

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | `O(n)` – passes only once | `O(n²)` brute force is too slow for `n=1000` | None if you ignore optimization |
| **Space Complexity** | `O(1)` additional (list of results) | `O(n²)` if you store all pairs | None |
| **Implementation Simplicity** | Very concise (one loop) | Simple but slow | Over‑engineered dynamic programming (unnecessary) |
| **Readability** | Clear variable names (`r`, `l`) | Verbose but straightforward | Nested recursion & 3D DP can be confusing |
| **Edge Cases** | Handles first/last element (`max(0, j-k)`) | Missing boundary checks | Forgetting to reset pointer → duplicates |
| **Maintainability** | One function, easy to test | No pitfalls | Complex recursion stack, hard to debug |

---

### 4️⃣ Approach – One‑Pass Sweep (Optimal)

1. Initialize an empty result list `res` and a pointer `r = 0` – the smallest index that hasn’t yet been covered.
2. Iterate `j` from `0` to `n-1`:
   * If `nums[j] == key`:
     * `l = max(r, j - k)` – start of the new interval, but not before `r`.
     * `r = min(n-1, j + k) + 1` – next index that should not be re‑added.
     * Append all indices `i` from `l` (inclusive) to `r` (exclusive) to `res`.
3. Return `res`.

Because `r` only moves forward, each index is appended at most once → `O(n)`.

---

### 5️⃣ Complexity

| Metric | Analysis |
|--------|----------|
| **Time** | `O(n)` – single pass, constant work per element |
| **Space** | `O(1)` auxiliary (besides output) – the list of indices grows with output size, which is inevitable |

---

### 6️⃣ Edge‑Case Checklist

| Case | Why it matters | How code handles it |
|------|----------------|---------------------|
| `k` large enough to cover entire array | Every index becomes k‑distant | `l = max(r, j-k)` will become `0`, `r` will become `n` |
| Multiple consecutive `key` values | Overlapping windows must not duplicate indices | `r` updates to `j+k+1`, avoiding repeats |
| `key` appears only at the first/last element | Window starts or ends at array boundaries | `max(0, j-k)` / `min(n-1, j+k)` guard the indices |
| `nums` contains no `key` | Result should be empty | Loop simply never enters the `if` block |

---

### 7️⃣ Code Implementations

> **Common Testing Snippet**  
> ```python
> print(Solution().findKDistantIndices([3,4,9,1,3,9,5], 9, 1))
> # → [1, 2, 3, 4, 5, 6]
> ```

---

#### Java

```java
import java.util.*;

class Solution {
    public List<Integer> findKDistantIndices(int[] nums, int key, int k) {
        List<Integer> res = new ArrayList<>();
        int r = 0;                // first not-yet-covered index
        int n = nums.length;

        for (int j = 0; j < n; j++) {
            if (nums[j] == key) {
                int l = Math.max(r, j - k);
                r = Math.min(n - 1, j + k) + 1;   // exclusive upper bound
                for (int i = l; i < r; i++) {
                    res.add(i);
                }
            }
        }
        return res;
    }
}
```

> **Why it’s concise** – Only one `for` loop, a handful of arithmetic operations, no recursion or DP.

---

#### Python

```python
class Solution:
    def findKDistantIndices(self, nums: List[int], key: int, k: int) -> List[int]:
        res = []
        r = 0                      # first index not yet added
        n = len(nums)

        for j, val in enumerate(nums):
            if val == key:
                l = max(r, j - k)
                r = min(n - 1, j + k) + 1
                res.extend(range(l, r))
        return res
```

> `extend(range(l, r))` is the Pythonic way to add a slice of consecutive integers.

---

#### C++

```cpp
class Solution {
public:
    vector<int> findKDistantIndices(vector<int>& nums, int key, int k) {
        vector<int> res;
        int r = 0;                         // first index not yet covered
        int n = nums.size();

        for (int j = 0; j < n; ++j) {
            if (nums[j] == key) {
                int l = max(r, j - k);
                r = min(n - 1, j + k) + 1;  // exclusive upper bound
                for (int i = l; i < r; ++i)
                    res.push_back(i);
            }
        }
        return res;
    }
};
```

---

### 7️⃣ Running the Code

| Language | Command |
|----------|---------|
| Java | `javac Solution.java` & `java Solution` (with a driver) |
| Python | `python3 solution.py` |
| C++ | `g++ -std=c++17 solution.cpp -o solution && ./solution` |

**Driver Skeleton (Python example)**

```python
if __name__ == "__main__":
    sol = Solution()
    print(sol.findKDistantIndices([3, 4, 9, 1, 3, 9, 5], 9, 1))
    print(sol.findKDistantIndices([2, 2, 2, 2, 2], 2, 2))
```

---

### 8️⃣ Interview Tips & Common Interviewer Questions

| Topic | What interviewers might ask |
|-------|----------------------------|
| **Explain the “coverage window”** | Why does `j-k` to `j+k` capture all valid `i`? |
| **Time‑Space trade‑off** | Could you do it in `O(n log n)`? Why not use a set? |
| **Edge Cases** | What if `k` = `n`? What if `key` is absent? |
| **Proof of correctness** | Show that `r` never goes backward; each index is added only once. |
| **Testing** | Write unit tests for: no `key`, all `key`, `k=0`, `k=n`, overlapping windows. |
| **Scalability** | Even though `n` ≤ 1000, interviewers love seeing an `O(n)` solution. |

> **Tip:** Bring the **intuitive explanation** to the table. Saying “every key expands a window” instantly shows you grasp the core logic.

---

### 9️⃣ Conclusion – Your “Winning” Code

- **Fast** (`O(n)`) → passes all LeetCode test cases comfortably.  
- **Simple** → easy to explain to a hiring manager.  
- **Cross‑language** → show you can adapt to the tech stack they use.

> **Final Thought:** When preparing for coding interviews, focus on *clarity* and *optimality*. A concise, well‑documented solution is more valuable than a complicated dynamic‑programming hack that nobody uses in real projects.

---

## 🚀 Want More Interview‑Ready LeetCode Problems?

Subscribe to our weekly newsletter for *Java, Python, and C++ interview challenges*, mock interview practice, and resume‑boosting tips.  

---