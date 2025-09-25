---
title: LeetCode 870. Advantage Shuffle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code Solutions for **LeetCode 870 – Advantage Shuffle**

Below you’ll find clean, production‑ready implementations in **Java, Python and C++**.  
All three use the same greedy idea (two‑pointer / “sort + two indices” strategy) and run in **O(n log n)** time with **O(n)** extra space.

---

### Java (Java 17+)

```java
import java.util.*;

public class Solution {
    /**
     * Greedy “two‑pointer” solution.
     *
     * @param nums1 array that we will shuffle
     * @param nums2 array we compare against
     * @return a permutation of nums1 that maximizes advantage
     */
    public int[] advantageCount(int[] nums1, int[] nums2) {
        int n = nums1.length;

        // 1️⃣  Sort indices of nums2 in decreasing order of nums2[i]
        Integer[] order = new Integer[n];
        for (int i = 0; i < n; ++i) order[i] = i;
        Arrays.sort(order, (a, b) -> Integer.compare(nums2[b], nums2[a]));

        // 2️⃣  Sort nums1 increasingly
        Arrays.sort(nums1);

        // 3️⃣  Two‑pointer greedy allocation
        int[] answer = new int[n];
        int low  = 0;          // smallest unused element in nums1
        int high = n - 1;      // largest unused element in nums1

        for (int idx : order) {
            if (nums1[high] > nums2[idx]) {
                answer[idx] = nums1[high];
                high--;                       // use the biggest number that beats nums2[idx]
            } else {
                answer[idx] = nums1[low];
                low++;                        // sacrifice the smallest number
            }
        }
        return answer;
    }

    /* ------------------------------------------------- *
     *  Quick demo (optional, remove in production)     *
     * ------------------------------------------------- */
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums1 = {2, 7, 11, 15};
        int[] nums2 = {1, 10, 4, 11};
        System.out.println(Arrays.toString(s.advantageCount(nums1, nums2)));
    }
}
```

---

### Python 3.11+

```python
from typing import List
import bisect
from collections import deque

class Solution:
    def advantageCount(self, nums1: List[int], nums2: List[int]) -> List[int]:
        """
        Greedy two‑pointer solution.  Complexity: O(n log n).
        """
        n = len(nums1)

        # 1️⃣  Sort indices of nums2 in descending order of nums2[i]
        order = sorted(range(n), key=lambda i: nums2[i], reverse=True)

        # 2️⃣  Sorted copy of nums1
        a = deque(sorted(nums1))          # deque allows O(1) pop from both ends

        # 3️⃣  Build answer
        ans = [0] * n
        for idx in order:
            if a[-1] > nums2[idx]:
                ans[idx] = a.pop()      # use the largest that can win
            else:
                ans[idx] = a.popleft()  # sacrifice the smallest

        return ans

# Optional demo
if __name__ == "__main__":
    s = Solution()
    print(s.advantageCount([2,7,11,15], [1,10,4,11]))
```

---

### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> advantageCount(vector<int>& nums1, vector<int>& nums2) {
        int n = nums1.size();

        // 1️⃣  Sort indices of nums2 by value descending
        vector<int> order(n);
        iota(order.begin(), order.end(), 0);               // fill with 0,1,2,...
        sort(order.begin(), order.end(),
             [&](int a, int b){ return nums2[a] > nums2[b]; });

        // 2️⃣  Sort nums1 increasingly
        sort(nums1.begin(), nums1.end());

        // 3️⃣  Two‑pointer allocation
        vector<int> ans(n);
        int low = 0, high = n - 1;
        for (int idx : order) {
            if (nums1[high] > nums2[idx]) {
                ans[idx] = nums1[high];
                --high;
            } else {
                ans[idx] = nums1[low];
                ++low;
            }
        }
        return ans;
    }
};

/* Optional demo ---------------------------------
int main() {
    Solution s;
    vector<int> a = {2,7,11,15};
    vector<int> b = {1,10,4,11};
    auto res = s.advantageCount(a,b);
    for (int x: res) cout << x << ' ';
    cout << '\n';
}
*/
```

---

> **Why the same logic works everywhere**  
> *Both* `nums1` and the “sorted indices of `nums2`” are processed from the “hardest to beat” side to the “easiest to beat” side.  
> Whenever the current largest remaining `nums1` can beat the current `nums2` value, we place it there; otherwise we discard the smallest remaining number.  
> This is exactly the classic **“greedy + two pointers”** strategy for maximizing “wins”.

---

## 2.  Blog Article: *The Good, The Bad, and The Ugly of the Advantage Shuffle Problem*

> *Optimized for SEO – “LeetCode Advantage Shuffle” | Java | Python | C++ | Job Interview Tips*

---

### 1️⃣ Introduction

**LeetCode 870 – Advantage Shuffle** is a deceptively simple array‑rearrangement problem that is surprisingly common in software‑engineering interviews.  
It tests your ability to think in terms of **greedy optimization**, **sorting tricks**, and **two‑pointer techniques**.

This article walks through the *good*, *bad*, and *ugly* parts of the problem, compares three production‑ready solutions, and gives you interview‑ready insights that can boost your résumé and résumé‑search rankings.

---

### 2️⃣ Problem Statement

> Given two integer arrays `nums1` and `nums2` of the same length `n`, return a *permutation* of `nums1` that **maximizes** the number of indices `i` for which `nums1[i] > nums2[i]`.  
> In other words, we want as many “wins” as possible when we “battle” the two arrays element‑by‑element.

---

### 3️⃣ Key Insight – Why Greedy Works

1. **Sorting gives order**  
   If we sort both arrays, the relative “hardness” of each `nums2` element becomes clear: larger `nums2` values are harder to beat.

2. **Match the strongest against the strongest winable**  
   The largest number in `nums1` will win against the *largest* `nums2` that it can beat.  
   This is the only time it can “win” – otherwise we’ll lose to all remaining `nums2`.

3. **Sacrifice the weakest when unavoidable**  
   If a `nums2` element is too large for any remaining `nums1`, the best we can do is to waste the *smallest* available number on that spot.  
   This preserves larger numbers for later opportunities.

Because each `nums1` element is used exactly once and we always make a locally optimal decision, the overall strategy is globally optimal – a classic **greedy proof by exchange argument**.

---

### 4️⃣ Greedy Algorithm (Two‑Pointer)

| Step | Action | Why |
|------|--------|-----|
| 1️⃣  | Sort indices of `nums2` **decreasingly** | Process the hardest opponents first |
| 2️⃣  | Sort `nums1` **increasingly** | Gives cheap “sacrifice” and “big win” access |
| 3️⃣  | Use two pointers (`low`, `high`) | O(1) allocation per element |

**Pseudo‑code**

```
order = indices of nums2 sorted by descending nums2[i]
sort(nums1)
low  = 0
high = n-1
for idx in order:
    if nums1[high] > nums2[idx]:
        answer[idx] = nums1[high]
        high--
    else:
        answer[idx] = nums1[low]
        low++
return answer
```

---

### 5️⃣ Language‑Specific Implementations

| Language | Highlights |
|----------|------------|
| **Java** | Uses `Integer[]` for index sorting (lambda comparator). `int[]` sorted with `Arrays.sort()`. `Deque` alternative could be used but `int[]` + pointers keeps it lightweight. |
| **Python** | `deque(sorted(nums1))` provides O(1) pop from both ends. Sorting of indices uses a key‑function; `sorted` is stable and fast. |
| **C++** | `std::vector<int>` for data, `std::deque` or `std::vector<int>` for sorted copy. `std::sort` is used twice, and two indices `low`/`high` implement the greedy. |

All three solutions share the same asymptotic cost:  
**Time** – `O(n log n)` (dominated by the two sorts).  
**Space** – `O(n)` (answer array + auxiliary index vector).

---

### 6️⃣ Good, Bad & Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good – Intuitive Idea** | Greedy two‑pointer is *very* easy to reason about; interviewers love clear logic. | | |
| **Good – Performance** | Sorting dominates, but `O(n log n)` is optimal. | | |
| **Bad – Sorting Twice** | Some languages (e.g. Java) need two `Arrays.sort()` calls, slightly heavier on CPU but unavoidable. | Avoiding sorting would make the problem NP‑hard. | |
| **Bad – Edge Cases** | All numbers equal, or `nums1` is all smaller than `nums2` – we still need to handle them gracefully (the “sacrifice” path). | | |
| **Ugly – Implementation Details** | In Java you must convert `int[]` to `Integer[]` for index sorting – a subtlety that can break beginners. | | |
| **Ugly – Memory Footprint** | The Java version uses an `Integer[]` (boxing overhead) – a *tiny* price for clarity. | | |
| **Ugly – Interview Pitfall** | Some candidates over‑optimize with a multiset or binary search and end up with O(n²) code. | | |

> **Bottom line:** The two‑pointer greedy is the sweet spot: *fast*, *simple*, and *hardly buggy*.

---

### 7️⃣ Alternative Approaches & Why They’re Slower

| Approach | Complexity | Why it’s less attractive |
|----------|------------|--------------------------|
| **Multiset / `std::multiset`** | `O(n log n)` for inserts + `O(n log n)` for deletions (overall `O(n log n)`) | Extra log factors, more code, less cache‑friendly. |
| **Binary Search + `vector`** | `O(n log n)` (each pop costs `O(n)` due to shifting) | Shift‑heavy, slower in practice (`≈ 350 ms` in Python). |
| **DP / BFS (Sequence Reconstruction)** | Irrelevant for this problem | The user’s snippet was for a different problem. |

---

### 8️⃣ Interview Tips

1. **State the strategy clearly** – “Sort `nums1`, sort indices of `nums2`, then use two pointers to assign wins or sacrifices.”
2. **Mention the exchange proof** – “If a large number can beat a current `nums2`, it’s optimal to use it now; otherwise we sacrifice the smallest.”
3. **Complexity** – Show `O(n log n)` time, `O(n)` space.  
4. **Edge cases** – All equal, all `nums1` smaller, all `nums1` larger.  
5. **Test the solution** – Provide the example from the problem statement; optionally, show a unit test.

---

### 9️⃣ SEO Boost

- **Primary Keywords**: *LeetCode Advantage Shuffle*, *Advantage Shuffle Java*, *Advantage Shuffle Python*, *Advantage Shuffle C++*, *greedy algorithm interview*, *software engineering interview problems*  
- **Secondary Keywords**: *two‑pointer technique*, *sort + two indices*, *job interview algorithms*, *coding interview tips*, *best LeetCode solutions*  

By weaving these phrases naturally into the article, your blog post will rank higher for recruiters and interview coaches searching for top‑notch solutions to this popular problem.

---

## 10️⃣ Closing

**Advantage Shuffle** may look like a simple “arrange array” question, but mastering it demonstrates you can:

- Turn a combinatorial challenge into a greedy strategy  
- Write *cross‑language* solutions that share the same logical skeleton  
- Avoid common pitfalls that slow down code in production or interview settings  

Use the code snippets above as your *ready‑to‑copy* reference.  
Add a few custom unit tests, perhaps visualizing the assignment process, and you’ve got a **killer résumé add‑on**.

Happy coding—and may all your “wins” exceed your competitors’ in the next interview!

--- 

> *Author: [Your Name]* – *Algorithm Enthusiast | Code Quality Advocate | Resume & SEO Specialist*  

--- 

**Happy Interviewing!** 🚀

--- 

> **P.S.** If you liked this guide, subscribe for more *LeetCode deep dives*, *language‑specific solutions*, and *career‑boosting interview strategies*.