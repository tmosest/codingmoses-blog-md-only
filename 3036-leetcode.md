---
title: LeetCode 3036. Number of Subarrays That Match a Pattern II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Code – Three Languages

Below you’ll find a **ready‑to‑copy** implementation of the optimal solution for LeetCode 3036  
`Number of Subarrays That Match a Pattern II`.  
The solution runs in **O(n + m)** time and **O(m)** auxiliary space, using the KMP
(“Knuth–Morris–Pratt”) string‑matching idea on a *comparison* array.

> *Why KMP?*  
>  After converting the numeric array into an array of `-1, 0, 1` that
>  represents “<, =, >” between adjacent elements, the problem becomes
>  an exact pattern‑matching problem on an integer string – exactly what
>  KMP is meant for.

---

### 1.1  Java – `Solution.java`

```java
import java.util.*;

public class Solution {

    /* ----------  Build the lps array (KMP preprocessing) ---------- */
    private static void buildLps(int[] pat, int[] lps) {
        int m = pat.length;
        int len = 0;              // length of the previous longest prefix suffix
        lps[0] = 0;               // lps[0] is always 0

        for (int i = 1; i < m; ) {
            if (pat[i] == pat[len]) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
    }

    /* -------------------------  Main API ------------------------- */
    public int countMatchingSubarrays(int[] nums, int[] pattern) {
        int n = nums.length;
        int m = pattern.length;
        if (m == 0) return 0;

        /* Build the “comparison” array for nums (size n‑1) */
        int[] cmp = new int[n - 1];
        for (int i = 0; i < n - 1; i++) {
            if (nums[i] < nums[i + 1]) cmp[i] = 1;   // “>”   → 1
            else if (nums[i] > nums[i + 1]) cmp[i] = -1;  // “<”   → -1
            else cmp[i] = 0;                      // “=”   → 0
        }

        /* KMP requires the pattern itself, so we keep it as‑is. */
        int[] lps = new int[m];
        buildLps(pattern, lps);

        /* ----------  KMP search on cmp  ---------- */
        int i = 0;          // index for cmp[]
        int j = 0;          // index for pattern[]
        int answer = 0;

        while (i < cmp.length) {
            if (cmp[i] == pattern[j]) {
                i++; j++;
            }

            if (j == m) {                     // a full match found
                answer++;
                j = lps[j - 1];               // continue searching for next overlap
            } else if (i < cmp.length && cmp[i] != pattern[j]) {
                if (j != 0) j = lps[j - 1];
                else i++;
            }
        }
        return answer;
    }
}
```

> **Compiling & running**  
> ```bash
> javac Solution.java
> java -jar leetcode.jar   # the LeetCode harness will call countMatchingSubarrays
> ```

---

### 1.2  Python – `solution.py`

```python
class Solution:
    # ---------- KMP preprocessing ----------
    def _build_lps(self, pat):
        m = len(pat)
        lps = [0] * m
        length = 0
        i = 1
        while i < m:
            if pat[i] == pat[length]:
                length += 1
                lps[i] = length
                i += 1
            else:
                if length != 0:
                    length = lps[length - 1]
                else:
                    lps[i] = 0
                    i += 1
        return lps

    # ---------- Main API ----------
    def countMatchingSubarrays(self, nums: List[int], pattern: List[int]) -> int:
        n, m = len(nums), len(pattern)
        if m == 0:
            return 0

        # Build the comparison array for nums (size n-1)
        cmp = [0] * (n - 1)
        for i in range(n - 1):
            if nums[i] < nums[i + 1]:
                cmp[i] = 1          # “>”
            elif nums[i] > nums[i + 1]:
                cmp[i] = -1         # “<”
            else:
                cmp[i] = 0          # “=”

        # Build KMP failure table
        lps = self._build_lps(pattern)

        # KMP search
        i = j = 0
        answer = 0
        while i < len(cmp):
            if cmp[i] == pattern[j]:
                i += 1
                j += 1
            if j == m:                # full match
                answer += 1
                j = lps[j - 1]
            elif i < len(cmp) and cmp[i] != pattern[j]:
                if j != 0:
                    j = lps[j - 1]
                else:
                    i += 1
        return answer
```

> **Running**  
> ```bash
> python -m unittest solution.py   # optional test harness
> ```

---

### 1.3  C++ – `solution.cpp`

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    /* ----------  KMP preprocessing: build lps array ---------- */
    void buildLps(const vector<int>& pat, vector<int>& lps) {
        int m = pat.size();
        lps.assign(m, 0);
        int len = 0;                       // previous longest prefix suffix
        for (int i = 1; i < m; ) {
            if (pat[i] == pat[len]) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0)
                    len = lps[len - 1];
                else
                    lps[i] = 0, i++;
            }
        }
    }

    /* ----------  Main API ---------- */
    int countMatchingSubarrays(vector<int>& nums, vector<int>& pattern) {
        int n = nums.size(), m = pattern.size();
        if (m == 0) return 0;

        /* Build the comparison array for nums (size n-1) */
        vector<int> cmp(n - 1);
        for (int i = 0; i < n - 1; ++i) {
            if (nums[i] < nums[i + 1]) cmp[i] = 1;      // “>”
            else if (nums[i] > nums[i + 1]) cmp[i] = -1; // “<”
            else cmp[i] = 0;                            // “=”
        }

        /* KMP preprocessing on the pattern */
        vector<int> lps;
        buildLps(pattern, lps);

        /* KMP search */
        int i = 0, j = 0, answer = 0;
        while (i < cmp.size()) {
            if (cmp[i] == pattern[j]) {
                ++i; ++j;
            }

            if (j == m) {          // full match
                ++answer;
                j = lps[j - 1];
            } else if (i < cmp.size() && cmp[i] != pattern[j]) {
                if (j != 0)
                    j = lps[j - 1];
                else
                    ++i;
            }
        }
        return answer;
    }
};
```

> **Compilation**  
> ```bash
> g++ -std=c++17 -O2 -pipe -static -s solution.cpp -o solution
> ```

--- 

## 2.  A Quick‑Look at the Optimal Approach

| Approach | Time | Space | Remarks |
|----------|------|-------|---------|
| **Brute‑Force** | **O(n · m)** | O(1) | Simple to write but TLE on the 10⁵‑size tests. |
| **Sliding‑Window + Hash** | **O(n)** | O(n) | Uses a rolling hash; collision risk (rare but possible). |
| **KMP on Comparison Array** | **O(n + m)** | **O(m)** | Collision‑free, deterministic, easy to reason about. |

### 2.1  What the “Comparison Array” Looks Like

For `nums = [1, 3, 2, 4]`:

```
1 < 3 →  1
3 > 2 → -1
2 < 4 →  1
```

So the comparison array is `[1, -1, 1]`.  
The pattern stays `[1, -1, 1]` (exactly the same values).  
Now the task is: *“how many times does the integer string `pattern` appear in
this comparison string?”* – a classic KMP question.

---

## 3.  Blog Article – “Cracking LeetCode 3036 with KMP (Java, Python, C++)”

> **Keywords**: *Leetcode 3036*, *Number of Subarrays That Match a Pattern II*, *KMP*, *Java*, *Python*, *C++*, *coding interview*, *algorithm design*, *job interview prep*.

---

### 3.1  Introduction

> **LeetCode 3036 – Number of Subarrays That Match a Pattern II**  
>  is a deceptively simple yet highly‑rated problem that tests a candidate’s
>  ability to recognize patterns, transform data, and apply a classic
>  string‑matching algorithm (KMP).  
>  For anyone preparing for coding interviews at top tech companies, mastering
>  this problem is a “must‑know” because it demonstrates:

- Deep understanding of **array manipulation** and **dynamic programming**.
- Ability to identify a problem as a hidden **string‑matching** task.
- Proficiency in **Java, Python, and C++** – the three languages most often
  requested in interviews.

---

### 3.2  Problem Statement (Short & Sweet)

> Given two integer arrays `nums` (size *n*) and `pattern` (size *m*),
> count how many contiguous sub‑arrays of `nums` have the **exact same
> directional pattern** as `pattern`.  
> The directional pattern is defined by comparisons:
> * `pattern[i] = 1`  → the sub‑array element at position *i* is **greater**  
> * `pattern[i] = -1` → the sub‑array element at position *i* is **less**  
> * `pattern[i] = 0`  → the sub‑array element at position *i* is **equal**  

> *Return the total count.*

---

### 3.3  Why Brute‑Force Fails (Good Reasons)

The straightforward O(*n* · *m*) algorithm checks every possible sub‑array
and compares it against the pattern.  
With constraints up to 100 000 elements, this becomes impractical:

```python
for start in range(n - m + 1):
    match = True
    for k in range(m):
        if compare(nums[start + k], nums[start + k + 1]) != pattern[k]:
            match = False; break
```

Time complexity: `O(10⁵ · 10⁵)` → **exceeds the 1‑second limit** on LeetCode.

---

### 3.4  The Key Insight – Convert to a Comparison Array

1. **Build a “direction” array** for `nums` of length `n-1`:
   - `>`  → `1`
   - `<`  → `-1`
   - `=`  → `0`

2. **The pattern itself is already a sequence of `1, -1, 0`**.  
   Therefore, we simply search for `pattern` inside the comparison array.

3. **No extra memory for hashing**; just a linear transformation of the
   original array.

---

### 3.5  Why KMP Is the Right Tool

- **Deterministic**: Unlike hash‑based methods, KMP never fails due to collisions.
- **Efficient**: Preprocessing takes `O(m)` time; searching takes `O(n)`.
- **Simple**: The failure table (`lps`) guarantees linear progression even
  after a match.

---

### 3.6  Implementation Highlights

| Language | Core Technique | Code Snippet |
|----------|----------------|--------------|
| **Java** | `int[] cmp = new int[n-1]` | `if (nums[i] < nums[i+1]) cmp[i] = 1;` |
| **Python** | List comprehension + KMP helper | `cmp[i] = 1 if nums[i] < nums[i+1] else -1` |
| **C++** | `vector<int> cmp(n-1);` | `cmp[i] = (nums[i] < nums[i+1]) ? 1 : -1;` |

> **Tip**: In LeetCode’s Java harness, ensure that the method signature matches
> `public int countMatchingSubarrays(int[] nums, int[] pattern)`.

---

### 3.7  Complexity Analysis (What Interviewers Love)

| Metric | Brute‑Force | KMP (Optimal) |
|--------|-------------|---------------|
| Time   | `O(n·m)`    | `O(n + m)`    |
| Space  | `O(1)`      | `O(m)`        |

> The linear time complexity guarantees that even the worst‑case tests with
> *n* = 10⁵ and *m* = 10⁵ run in under a few milliseconds.

---

### 3.8  Common Pitfalls & “Good Bad” Practices

| Pitfall | Fix |
|---------|-----|
| Forgetting that the comparison array has length *n‑1* | Use `if (i < cmp.size())` guard. |
| Over‑complicating by using `int[][]` for 2‑D slices | Stick to a 1‑D comparison array. |
| Using a rolling hash without handling collisions | Prefer KMP for guaranteed correctness. |
| Mixing “>`” and “`<`” semantics in the comparison array | Double‑check the sign mapping. |

---

### 3.9  Real‑World Takeaway

> **LeetCode 3036** is an excellent example of **“data‑structure transformation”**.  
> Many interview problems hide a pattern‑matching core; turning the problem
> into a familiar algorithmic form (here, KMP) is a powerful skill.  
> Implement it in **Java, Python, C++**, and you’ll have a versatile, proven
> solution that impresses interviewers at Google, Amazon, Microsoft, and more.

---

### 3.10  Call to Action

> Practice the following variations:

1. **Add a new direction** (`2` meaning “difference > threshold”) and
   adapt the comparison array accordingly.
2. **Change the requirement** to “at least *k* overlapping matches”.
3. **Translate the solution into Go** (for your next interview).

> Remember: **Good code is elegant, correct, and fast**.  
> The KMP solution above ticks all those boxes.

--- 

## 4.  Conclusion

We have:

- Delivered production‑ready implementations in Java, Python, and C++.
- Shown the transformation from an array‑pattern problem to a KMP search.
- Provided a ready‑to‑publish blog post that highlights why mastering LeetCode 3036
  is essential for coding interview success.

Happy coding, and good luck on your next interview!