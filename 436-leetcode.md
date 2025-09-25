---
title: LeetCode 436. Find Right Interval - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 436 – Find Right Interval  
### A full‑stack (Java | Python | C++) solution + SEO‑friendly interview article

> **TL;DR** – Sort the interval starts, binary‑search each end point, and return the index of the smallest start that is ≥ the end.  
> **Complexity** – **O(n log n)** time, **O(n)** extra space.  
> **Languages** – Java 17, Python 3.11, C++17.  

---

### 1️⃣ Problem Recap (LeetCode 436)

> **Input** – `intervals[i] = [startᵢ, endᵢ]`, all starts are unique.  
> **Task** – For every interval `i` find the interval `j` such that  
> `startⱼ ≥ endᵢ` and `startⱼ` is as small as possible.  
> Return the index `j` for each `i`; use `-1` if none exists.

---

### 2️⃣ The Good – Elegant O(n log n) Solution

| Step | What we do | Why it works |
|------|------------|--------------|
| **1** | Create an array of pairs `(start, originalIndex)` | We need the start values sorted but still know the original position. |
| **2** | Sort that array by `start` | Binary search becomes possible. |
| **3** | For each interval `(s, e)` perform a binary search on the sorted starts for the **first start ≥ e** | This is the classic “lower_bound” operation. |
| **4** | If such a start exists, the answer is its stored original index; otherwise `-1`. | We have the minimal satisfying start. |

**Why this is the “good” part**

* **Scales** – `n ≤ 2·10⁴` → `O(n log n)` is easily within limits.  
* **Clean** – No heavy data structures, only a sorted array and binary search.  
* **Deterministic** – Same answer regardless of the order of intervals in the input.

---

### 3️⃣ The Bad – The Pitfalls That Must Be Avoided

| Bad Practice | Why it fails | Alternative |
|--------------|--------------|-------------|
| **O(n²) nested loops** – check every pair | For 20 k intervals this becomes 400 M operations → TLE. | Use binary search or a tree map. |
| **HashMap + linear scan** – map start→index, then scan all keys for each end | Still O(n²) in the worst case; map lookup is O(1) but the scan is linear. | Sort once and binary‑search. |
| **Assuming starts are sorted** | Input can be arbitrary; ignoring that leads to wrong answers. | Explicitly sort. |

---

### 4️⃣ The Ugly – Edge Cases & Implementation Quirks

| Ugly scenario | What can go wrong | Fix |
|--------------|------------------|-----|
| **Duplicate starts** | Problem guarantees uniqueness, but a wrong implementation that assumes it can crash. | Validate uniqueness if you want to be paranoid. |
| **Very large negative or positive values** | Overflow when using `int` in Java or Python? | Use `int` (32‑bit) – ranges are within ±10⁶ so safe. |
| **No interval found** | Binary search returns an index equal to array length. | Return `-1` explicitly. |
| **Zero‑based vs one‑based indexing** | Mixing up indices during output. | Store the original index, not the sorted position. |

---

## 🏁 Code Walkthrough

Below you’ll find the **full, ready‑to‑compile / run** implementations in **Java, Python, C++**.  
All three follow the same algorithmic idea and use the same time/space complexity.

> **Tip** – Paste the code into your IDE or online compiler, run it with the sample inputs, and you’ll see the expected outputs `[-1]`, `[-1, 0, 1]`, `[-1, 2, -1]`.

---

## 🧪 Java 17 Implementation

```java
import java.util.*;

public class Solution {
    public int[] findRightInterval(int[][] intervals) {
        int n = intervals.length;
        // Pair: (start, originalIndex)
        int[][] starts = new int[n][2];
        for (int i = 0; i < n; i++) {
            starts[i][0] = intervals[i][0];   // start
            starts[i][1] = i;                 // original index
        }

        // Sort by start value
        Arrays.sort(starts, Comparator.comparingInt(a -> a[0]));

        int[] result = new int[n];
        Arrays.fill(result, -1);

        for (int i = 0; i < n; i++) {
            int end = intervals[i][1];
            int idx = lowerBound(starts, end);
            if (idx < n) {
                result[i] = starts[idx][1];  // original index of the right interval
            }
        }
        return result;
    }

    // Classic lower_bound: first index with start >= key
    private int lowerBound(int[][] starts, int key) {
        int lo = 0, hi = starts.length;
        while (lo < hi) {
            int mid = (lo + hi) >>> 1; // avoid overflow
            if (starts[mid][0] < key) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo;
    }

    // ---------- Main method for quick manual testing ----------
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(Arrays.toString(
                s.findRightInterval(new int[][]{{3,4},{2,3},{1,2}})
        )); // [-1,0,1]
    }
}
```

---

## 🐍 Python 3 Implementation

```python
from typing import List
import bisect

class Solution:
    def findRightInterval(self, intervals: List[List[int]]) -> List[int]:
        n = len(intervals)
        # (start, original_index)
        starts = sorted((s, i) for i, (s, _) in enumerate(intervals))
        start_vals = [s for s, _ in starts]          # separate list for bisect
        result = [-1] * n

        for idx, (_, end) in enumerate(intervals):
            pos = bisect.bisect_left(start_vals, end)   # first start >= end
            if pos < n:
                result[idx] = starts[pos][1]
        return result

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.findRightInterval([[3,4],[2,3],[1,2]]))   # [-1, 0, 1]
```

---

## 🧱 C++17 Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> findRightInterval(vector<vector<int>>& intervals) {
        int n = intervals.size();
        vector<pair<int,int>> starts;
        starts.reserve(n);

        for (int i = 0; i < n; ++i)
            starts.emplace_back(intervals[i][0], i);   // (start, original index)

        sort(starts.begin(), starts.end());            // sort by start

        vector<int> result(n, -1);
        for (int i = 0; i < n; ++i) {
            int end = intervals[i][1];
            auto it = lower_bound(starts.begin(), starts.end(),
                                  make_pair(end, -1),
                                  [](const pair<int,int>& a, const pair<int,int>& b){
                                      return a.first < b.first;
                                  });
            if (it != starts.end())
                result[i] = it->second;                 // original index
        }
        return result;
    }
};

// ---------- Demo ----------
int main() {
    Solution s;
    vector<vector<int>> arr = {{3,4},{2,3},{1,2}};
    vector<int> res = s.findRightInterval(arr);
    for (int x : res) cout << x << ' ';
    cout << '\n';           // -1 0 1
}
```

---

## 📈 SEO‑Optimized Blog Article

> **Title**: “LeetCode 436 – Find Right Interval: Java, Python, C++ Solutions + Interview Cheat Sheet”

### 1. What is “Find Right Interval” and why does it matter?

LeetCode 436, *Find Right Interval*, is a classic problem that tests a candidate’s ability to combine **sorting** with **binary search**.  
In a real‑world interview, solving it correctly demonstrates:

* Understanding of *sorted‑data structures* (arrays, trees, maps).  
* Ability to reduce complexity from *O(n²)* to *O(n log n)*.  
* Clean coding style (avoiding hidden bugs like index mismatches).

If you want to land a job at a tech giant (Google, Amazon, Meta, etc.), mastering this problem is a **must‑have**.

### 2. Problem Recap – In a nutshell

You’re given `n` intervals `[start, end]` with **unique** starts.  
For each interval, find the “right” interval – the one whose start is **≥** the end of the current interval and **minimizes** that start.  
Return the *original indices* of these right intervals, or `-1` if none exists.

### 3. The Efficient Solution (Good)

1. **Pair starts with indices** → `[(start, idx)]`.  
2. **Sort** the pairs by `start`.  
3. For every interval’s `end`, **binary‑search** the sorted starts to find the first start ≥ `end`.  
4. Translate that back to the original index or `-1`.

*Why it works:* Binary search on a sorted array gives the smallest element that satisfies a predicate in `O(log n)` time. Doing this for each interval yields `O(n log n)` overall.

### 4. What not to do (Bad)

* **Brute force**: Nested loops → `O(n²)`.  
* **HashMap + linear scan**: Still `O(n²)` worst‑case.  
* **Assume input sorted**: Wrong answer on unsorted test cases.

### 5. Edge Cases (Ugly)

* No interval satisfies the condition → return `-1`.  
* All intervals end before the smallest start → `-1` for all.  
* Extremely large or small start/end values → Still safe in 32‑bit `int` for given constraints.

### 6. Why this solution is *job‑ready*

| Skill | Demonstrated |
|-------|--------------|
| Data structures | Arrays, sorting, binary search |
| Time complexity | `O(n log n)` |
| Space complexity | `O(n)` (extra array for pairs) |
| Clean code | Avoids magic numbers, uses helper `lowerBound` |
| Language versatility | Same algorithm in Java, Python, C++ |

If you can explain this in under 5 minutes and code it in any of the languages above, you’re ready for the **algorithmic** part of the interview.

### 7. Bonus: Ready‑to‑paste code

*Java* – see code block above.  
*Python* – see code block above.  
*C++* – see code block above.

Copy, paste, run with sample inputs, and you’ll get the expected outputs instantly.

### 8. Final Checklist Before the Interview

1. **Explain** the approach (why sorting + binary search).  
2. **Write** the code in your preferred language.  
3. **Walk through** edge cases on the whiteboard.  
4. **Mention** time/space complexity.  
5. **Ask** clarifying questions about constraints (e.g., are starts guaranteed unique?).

Good luck, and remember: **practice** the binary search pattern in multiple contexts—intervals, arrays, strings—and you’ll feel confident tackling LeetCode 436 and beyond.

---

> **Keywords**: LeetCode 436, Find Right Interval, Java solution, Python solution, C++ solution, interview algorithm, binary search, sorting, job interview prep, data structures, time complexity, space complexity.  
> **Meta Description**: Master LeetCode 436 – Find Right Interval with Java, Python, and C++ code. Learn the efficient O(n log n) approach, avoid common pitfalls, and ace your coding interview.

---