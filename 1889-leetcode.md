---
title: LeetCode 1889. Minimum Space Wasted From Packaging - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Problem Overview  
**LeetCode 1889 – Minimum Space Wasted From Packaging (Hard)**  

You’re given

* `packages` – an array of `n` integers, each representing the size of a package.  
* `boxes` – a 2‑D array of size `m`. `boxes[j]` contains the distinct sizes of the boxes produced by supplier `j`. Every supplier has an *infinite* supply of each of its box sizes.

You must choose **one** supplier and use **only its box sizes** to put every package into a separate box.  
If a package of size `p` is placed in a box of size `b`, the wasted space is `b – p`.  
The goal is to minimize the total wasted space across all packages.  
If it is impossible to fit all packages with any supplier, return `-1`.  
Because the answer can be huge, return it modulo `10^9 + 7`.

> **Examples**  
> 1. `packages = [2,3,5]`, `boxes = [[4,8],[2,8]]` → **6**  
> 2. `packages = [2,3,5]`, `boxes = [[1,4],[2,3],[3,4]]` → **-1**  
> 3. `packages = [3,5,8,10,11,12]`, `boxes = [[12],[11,9],[10,5,14]]` → **9**  

---

## 🚀 High‑Level Strategy  
1. **Sort the packages** – makes binary searching easier.  
2. **Build a prefix sum array** of sorted packages – allows us to compute the sum of any contiguous sub‑segment in O(1).  
3. For **each supplier**  
   * Sort its box sizes.  
   * If the largest box is still smaller than the largest package → *skip this supplier*.  
   * Walk through the box sizes in ascending order.  
   * For a box of size `b`, use **binary search** to find the largest package index `idx` such that `packages[idx] ≤ b`.  
   * All packages from the previous index + 1 up to `idx` will be packed into boxes of size `b`.  
   * Compute the wasted space for that segment using the prefix sums.  
   * Accumulate the waste for the current supplier.  
4. Keep the **minimum** waste across all suppliers.  
5. If no supplier could fit all packages → return `-1`.  
6. Else return `minWaste % MOD`.

### Why This Works
* Sorting ensures that when we process box sizes in ascending order, all packages that fit into a smaller box are already handled; packages that need larger boxes are left for later boxes.  
* Binary search (`upper_bound`) gives the last package that fits a given box size in `O(log n)`.  
* Prefix sums let us calculate the sum of package sizes in a range in `O(1)`, which is crucial for efficiency.  

---

## 📊 Complexity Analysis  
*Let* `N = packages.length` (≤ 10⁵) and `M = total number of box sizes across all suppliers` (≤ 10⁵).  

*Sorting* `packages`: `O(N log N)`  
*Sorting* each supplier’s box list: `O(M log M)` overall  
*Processing* each box: one binary search → `O(log N)`  
Total time: **O((N + M) log N)**  
Space: **O(N)** (for packages, prefix sums; plus temporary arrays for each supplier).

---

## 🛠️ Code Implementations  

Below are clean, production‑ready solutions in **Java**, **Python**, and **C++**.  
Each follows the algorithm described above, handles edge cases, and uses the 1 000 000 007 modulus.

---

### Java

```java
import java.util.Arrays;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int minWastedSpace(int[] packages, int[][] boxes) {
        int n = packages.length;
        Arrays.sort(packages);

        // Prefix sum of package sizes
        long[] prefix = new long[n + 1];
        for (int i = 1; i <= n; i++) {
            prefix[i] = prefix[i - 1] + packages[i - 1];
        }

        long best = Long.MAX_VALUE;
        boolean possible = false;
        int largestPackage = packages[n - 1];

        for (int[] supplier : boxes) {
            Arrays.sort(supplier);
            int largestBox = supplier[supplier.length - 1];
            if (largestBox < largestPackage) {
                continue;            // this supplier cannot fit the largest package
            }
            possible = true;

            long waste = 0;
            int prev = -1;           // last index of package already packed

            for (int boxSize : supplier) {
                if (boxSize < packages[0]) {
                    continue;       // this box is too small for any package
                }
                int idx = upperBound(packages, boxSize); // last package that fits
                long sumPackages = prefix[idx + 1] - prefix[prev + 1];
                long numPackages = idx - prev;
                waste += numPackages * boxSize - sumPackages;
                prev = idx;
            }

            best = Math.min(best, waste);
        }

        return possible ? (int) (best % MOD) : -1;
    }

    // last index where packages[i] <= target, or -1 if none
    private static int upperBound(int[] arr, int target) {
        int lo = 0, hi = arr.length - 1, ans = -1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            if (arr[mid] <= target) {
                ans = mid;
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
        return ans;
    }
}
```

---

### Python 3

```python
import bisect
from typing import List

MOD = 10**9 + 7

class Solution:
    def minWastedSpace(self, packages: List[int], boxes: List[List[int]]) -> int:
        n = len(packages)
        packages.sort()

        # Prefix sums
        pref = [0]
        for p in packages:
            pref.append(pref[-1] + p)

        largest_pkg = packages[-1]
        best = float('inf')
        possible = False

        for supplier in boxes:
            supplier.sort()
            if supplier[-1] < largest_pkg:
                continue      # cannot fit the largest package
            possible = True

            waste = 0
            prev = -1

            for box in supplier:
                if box < packages[0]:
                    continue
                idx = bisect.bisect_right(packages, box) - 1
                if idx <= prev:
                    continue
                sum_pkg = pref[idx + 1] - pref[prev + 1]
                num_pkg = idx - prev
                waste += num_pkg * box - sum_pkg
                prev = idx

            best = min(best, waste)

        return int(best % MOD) if possible else -1
```

---

### C++ (17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr long long MOD = 1'000'000'007LL;

    int minWastedSpace(vector<int>& packages, vector<vector<int>>& boxes) {
        int n = packages.size();
        sort(packages.begin(), packages.end());

        // prefix sums
        vector<long long> pref(n + 1, 0);
        for (int i = 1; i <= n; ++i)
            pref[i] = pref[i - 1] + packages[i - 1];

        long long best = LLONG_MAX;
        bool possible = false;
        int largestPkg = packages.back();

        for (auto& supplier : boxes) {
            sort(supplier.begin(), supplier.end());
            if (supplier.back() < largestPkg) continue;   // cannot fit
            possible = true;

            long long waste = 0;
            int prev = -1;

            for (int box : supplier) {
                if (box < packages.front()) continue;     // too small for any package
                int idx = upperBound(packages, box);       // last fitting package
                long long sumPkg = pref[idx + 1] - pref[prev + 1];
                long long cnt = idx - prev;
                waste += cnt * box - sumPkg;
                prev = idx;
            }

            best = min(best, waste);
        }

        return possible ? static_cast<int>(best % MOD) : -1;
    }

private:
    // last index where packages[i] <= target, or -1 if none
    int upperBound(const vector<int>& arr, int target) {
        int lo = 0, hi = (int)arr.size() - 1, ans = -1;
        while (lo <= hi) {
            int mid = lo + (hi - lo) / 2;
            if (arr[mid] <= target) {
                ans = mid;
                lo = mid + 1;
            } else {
                hi = mid - 1;
            }
        }
        return ans;
    }
};
```

---

## 📚 Blog Article – “Minimum Space Wasted From Packaging: Good, Bad & Ugly”  

> **Target Audience**: software‑engineers, interview‑preparation enthusiasts, recruiters.  
> **Keywords**: Minimum Space Wasted From Packaging, LeetCode Hard, Binary Search, Prefix Sum, Java, Python, C++, coding interview, algorithms, job interview tips, data structures, interview problem solving.

---

### 🎬 The Story (Good, Bad & Ugly)

| Phase | What Happened | Why It Matters | Take‑away |
|-------|---------------|----------------|-----------|
| **Good** | **Sorted packages + prefix sums** give us a *simple* yet powerful foundation. | Allows O(1) range queries, which keeps the algorithm fast. | *Always* sort and pre‑process data before iterating. |
| **Bad** | **Large inputs** (10⁵) force us to consider the *worst‑case* runtime. A naïve O(n²) approach would time‑out. | Many candidates underestimate the need for logarithmic operations. | Never assume O(n²) is acceptable on 10⁵ elements. |
| **Ugly** | **Skipping unusable suppliers** can be error‑prone if you forget the “largest package” check. | A single oversight can lead to an incorrect `-1` or a larger waste value. | Test your code on edge cases: only one package, all boxes the same size, etc. |

---

### 💡 Why Interviewers Love This Problem  
* **It forces you to think about** how *different data structures* (arrays, prefix sums, binary search trees) interact.  
* It shows you can **balance pre‑processing and per‑supplier work** – a common interview theme.  
* It tests **attention to detail** (modulo arithmetic, 64‑bit safety, edge‑case handling).  

### 🌱 How to Nail It in a Technical Interview  
1. **Explain the intuition first** (why sorting & prefix sums).  
2. **Walk through a simple example** on paper or a whiteboard.  
3. **State your complexity** and why it meets the constraints.  
4. **Implement cleanly** – modular functions, proper variable names, and comments.  
5. **Ask clarifying questions**: “Do we have to use exactly the boxes the supplier offers?” “Is it allowed to mix different box sizes from the same supplier?” (They’re explicitly allowed, but only from the chosen supplier).  

---

### 🚀 SEO‑Friendly Summary  

> *Minimum Space Wasted From Packaging* – a **LeetCode Hard** problem that tests binary search and prefix sum mastery.  
> This blog walks through the algorithmic strategy, shows production‑ready Java, Python, and C++ code, and explains why this approach is efficient enough for 10⁵ data points.  
> Whether you’re preparing for a **software engineering interview**, a **coding challenge** or just want to deepen your understanding of *data‑structure‑based optimization*, the solutions here will give you a clear path to success.  

**Keywords**: Minimum Space Wasted From Packaging, LeetCode 1889, Binary Search, Prefix Sum, Java, Python, C++, Hard Interview Problem, Coding Interview Prep, Algorithms, Job Interview Tips, Software Engineering.  

Happy coding – and may the minimal waste (and the interview call‑back) come your way! 🚀