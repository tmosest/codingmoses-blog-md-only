---
title: LeetCode 2070. Most Beautiful Item for Each Query - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 2070 – Most Beautiful Item for Each Query  
**Language‑agnostic solution (Java, Python, C++) + a full SEO‑friendly blog post**

---

## 1️⃣ Problem Recap

> **Given**:  
> * `items` – `n` pairs `[price, beauty]`  
> * `queries` – `q` budget values  
> **Return** an array `answer` where `answer[j]` is the maximum beauty of any item whose price ≤ `queries[j]`.  
> If no item fits, the answer is `0`.

> **Constraints**  
> * `1 ≤ n, q ≤ 10⁵`  
> * `1 ≤ price, beauty, queries[j] ≤ 10⁹`

> **Goal**:  Handle up to 100 000 items and queries in *O((n+q) log n)* time.

---

## 2️⃣ The Core Idea

1. **Sort** all items by price (ascending).  
2. Build a **prefix maximum array** `maxBeauty[i]` – the best beauty you can get with any item priced ≤ `price[i]`.  
3. For each query, perform a **binary search** on the sorted prices to find the rightmost price ≤ `budget`.  
4. The answer is the `maxBeauty` value at that index (or `0` if none).

This guarantees *O(n log n + q log n)* time and *O(n)* space.

---

## 3️⃣ Implementation in Three Languages  

Below are production‑ready snippets for **Java**, **Python**, and **C++**.  
Each file is self‑contained and ready to paste into your LeetCode editor.

### 3.1 Java

```java
import java.util.*;

class Solution {
    public int[] maximumBeauty(int[][] items, int[] queries) {
        // 1️⃣ Sort items by price
        Arrays.sort(items, Comparator.comparingInt(a -> a[0]));

        int n = items.length;
        int[] prices = new int[n];
        int[] maxBeauty = new int[n];
        int currentMax = 0;

        for (int i = 0; i < n; i++) {
            prices[i] = items[i][0];
            currentMax = Math.max(currentMax, items[i][1]); // prefix max
            maxBeauty[i] = currentMax;
        }

        // 2️⃣ Answer queries with binary search
        int q = queries.length;
        int[] ans = new int[q];
        for (int i = 0; i < q; i++) {
            int budget = queries[i];
            int idx = upperBound(prices, budget); // last index with price <= budget
            ans[i] = (idx == -1) ? 0 : maxBeauty[idx];
        }
        return ans;
    }

    // Helper: returns last index where arr[idx] <= target; -1 if none
    private int upperBound(int[] arr, int target) {
        int l = 0, r = arr.length - 1, res = -1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if (arr[mid] <= target) {
                res = mid;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return res;
    }
}
```

> **Why it works**  
> *Sorting is `O(n log n)`*  
> *Prefix max is linear*  
> *Each binary search is `O(log n)`*, so overall `O((n+q) log n)`.

---

### 3.2 Python

```python
from bisect import bisect_right
from typing import List

class Solution:
    def maximumBeauty(self, items: List[List[int]], queries: List[int]) -> List[int]:
        # 1️⃣ Sort by price
        items.sort(key=lambda x: x[0])

        prices, max_beauty = [], []
        cur_max = 0
        for price, beauty in items:
            prices.append(price)
            cur_max = max(cur_max, beauty)
            max_beauty.append(cur_max)

        # 2️⃣ Answer queries
        res = []
        for q in queries:
            idx = bisect_right(prices, q) - 1  # last price <= q
            res.append(max_beauty[idx] if idx >= 0 else 0)
        return res
```

> **Python specifics**  
> *`bisect_right` gives the insertion point; subtract 1 to get the last valid index.*  
> *Both time and space complexities match the Java version.*

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maximumBeauty(vector<vector<int>>& items, vector<int>& queries) {
        // 1️⃣ Sort by price
        sort(items.begin(), items.end(),
             [](const vector<int>& a, const vector<int>& b) { return a[0] < b[0]; });

        int n = items.size();
        vector<int> prices(n), prefMax(n);
        int curMax = 0;
        for (int i = 0; i < n; ++i) {
            prices[i] = items[i][0];
            curMax = max(curMax, items[i][1]);
            prefMax[i] = curMax;
        }

        // 2️⃣ Answer queries
        vector<int> ans;
        ans.reserve(queries.size());
        for (int q : queries) {
            // upper_bound returns iterator to first > q
            auto it = upper_bound(prices.begin(), prices.end(), q);
            if (it == prices.begin())
                ans.push_back(0);                     // all prices > q
            else
                ans.push_back(prefMax[it - prices.begin() - 1]); // last <= q
        }
        return ans;
    }
};
```

> **C++ details**  
> *`upper_bound` is a standard STL binary search.*  
> *We subtract one from the iterator to get the correct index.*

---

## 4️⃣ The Good, The Bad, and The Ugly

| Aspect | What’s Good | What’s Bad | Ugly Truth | How to Fix |
|--------|-------------|------------|------------|------------|
| **Algorithm** | Simple, fast, proven (binary search + prefix max) | None (optimal for constraints) | Might be overkill for small data | None |
| **Complexity** | `O((n+q) log n)` time, `O(n)` space | Still needs `O(n log n)` sort | Sorting may appear heavy in interviews | Acceptable; can’t be improved without a different approach |
| **Edge Cases** | Works for empty items, duplicate prices | None | Duplicate prices with lower beauty can confuse naive “last‑price” logic | Keep prefix max independent of duplicates |
| **Implementation Pitfalls** | Careful binary‑search boundaries | Off‑by‑one errors | Wrong `upperBound` can return `-1` or wrong index | Use helper function or `bisect_right-1` |
| **Readability** | Short, readable code | None | Java `Comparator` syntax can be confusing | Add comments, keep code lean |
| **Testing** | Multiple test harnesses (LeetCode, local) | None | None | None |

### 🚨 Common “Ugly” mistakes in interviews

1. **O(n²) solution** – looping over all items for each query quickly times out.  
   👉 *Never.*  
2. **Using `while (l <= r)` incorrectly** – off‑by‑one errors when searching.  
   👉 *Always test boundary conditions on the smallest and largest budgets.*  
3. **Assuming `int` is safe in C++/Java** – prices and beauties up to `10⁹`.  
   👉 *Use 64‑bit (`long long` in C++, `long` in Java) if you plan to do arithmetic.*

---

## 5️⃣ Testing & Edge‑Case Checklist

| Case | Expected Result | Why it matters |
|------|-----------------|----------------|
| No items (`items = []`) | `0` for every query | Checks empty‑prefix handling |
| All items too expensive | `0` | Checks binary search boundary |
| Multiple items with same price | Best beauty only | Prefix max removes redundancy |
| Very large budgets (≥ max price) | Max overall beauty | Binary search returns last index |
| Very small budgets (≤ min price) | `0` or first item’s beauty | Edge case for `upper_bound` |

> **Tip**: On LeetCode, run `Test Case 1`‑`10` before the final submission; they usually cover these edge cases.

---

## 5️⃣️  Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java | `O((n + q) log n)` | `O(n)` |
| Python | `O((n + q) log n)` | `O(n)` |
| C++ | `O((n + q) log n)` | `O(n)` |

> **Why this is optimal**  
> You must sort the items at least once: `n log n`.  
> For each query you need *log‑time* lookup → `q log n`.  
> No algorithm can beat this asymptotically for the given constraints.

---

## 6️⃣ Final Take‑Away

> **If you’re preparing for a coding interview, LeetCode 2070 is a perfect playground for:**
> - **Sorting + prefix arrays**  
> - **Binary search on arrays**  
> - **Time‑space trade‑offs**  
> - **Clear, maintainable code**  

> **Remember**:  
> *Keep the prefix array separate from the original `items`.*  
> *Always use `upper_bound / bisect_right` + `-1`.*  
> *Reserve space for answer vectors to avoid reallocations.*  

---  

## 📄 SEO‑Optimized Blog Post (Markdown)

```markdown
# LeetCode 2070 – Most Beautiful Item for Each Query  
## Java | Python | C++ Solutions + Interview Guide

**Keywords**: LeetCode 2070, Most Beautiful Item for Each Query, Java solution, Python solution, C++ solution, binary search, prefix maximum, time complexity, interview coding

---

### 1. Introduction  
LeetCode problem **2070 – Most Beautiful Item for Each Query** asks you to find, for every budget value, the most beautiful item that can be bought.  
In a technical interview, this is a classic *sorting + binary search* exercise that tests your ability to optimize both time and space.

---

### 2. Problem Statement  
Given `items = [[price₁, beauty₁], …, [priceₙ, beautyₙ]]` and `queries = [q₁, …, qₘ]`, return an array where each entry is the maximum beauty of an item priced ≤ the corresponding query.  
If no item fits the budget, the answer is `0`.

---

### 3. Intuitive Approach  
1. **Sort** items by price.  
2. Build a **prefix‑maximum beauty** array – at each price we know the best beauty achievable so far.  
3. For each query, **binary search** the sorted price list to find the rightmost price that does not exceed the budget.  
4. The result is the beauty stored at that index.

This solution runs in `O((n+m) log n)` time and `O(n)` space, comfortably handling 100 k items/queries.

---

### 4. Code Walkthrough  

#### 4.1 Java  
```java
import java.util.*;

class Solution {
    public int[] maximumBeauty(int[][] items, int[] queries) {
        Arrays.sort(items, Comparator.comparingInt(a -> a[0]));
        int n = items.length;
        int[] prices = new int[n];
        int[] pref = new int[n];
        int cur = 0;
        for (int i = 0; i < n; i++) {
            prices[i] = items[i][0];
            cur = Math.max(cur, items[i][1]);
            pref[i] = cur;
        }
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int budget = queries[i];
            int idx = upperBound(prices, budget); // last ≤ budget
            ans[i] = (idx == -1) ? 0 : pref[idx];
        }
        return ans;
    }
    private int upperBound(int[] arr, int target) {
        int l=0, r=arr.length-1, res=-1;
        while(l<=r){
            int mid=l+(r-l)/2;
            if(arr[mid]<=target){ res=mid; l=mid+1; }
            else r=mid-1;
        }
        return res;
    }
}
```

#### 4.2 Python  
```python
from bisect import bisect_right
from typing import List

class Solution:
    def maximumBeauty(self, items: List[List[int]], queries: List[int]) -> List[int]:
        items.sort(key=lambda x: x[0])
        prices, pref = [], []
        cur = 0
        for p,b in items:
            prices.append(p)
            cur = max(cur,b)
            pref.append(cur)
        return [pref[bisect_right(prices, q)-1] if bisect_right(prices, q)>0 else 0
                for q in queries]
```

#### 4.3 C++  
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maximumBeauty(vector<vector<int>>& items, vector<int>& queries) {
        sort(items.begin(), items.end(),
             [](const auto &a, const auto &b){ return a[0] < b[0]; });
        int n = items.size();
        vector<int> price(n), pref(n);
        int cur = 0;
        for (int i=0;i<n;++i){
            price[i] = items[i][0];
            cur = max(cur, items[i][1]);
            pref[i] = cur;
        }
        vector<int> ans;
        ans.reserve(queries.size());
        for (int q : queries){
            auto it = upper_bound(price.begin(), price.end(), q);
            if(it==price.begin()) ans.push_back(0);
            else ans.push_back(pref[it-price.begin()-1]);
        }
        return ans;
    }
};
```

---

### 5. Complexity  

| Operation | Java | Python | C++ |
|-----------|------|--------|-----|
| Sort items | `O(n log n)` | `O(n log n)` | `O(n log n)` |
| Build prefix | `O(n)` | `O(n)` | `O(n)` |
| Binary search per query | `O(log n)` | `O(log n)` | `O(log n)` |
| Total time | `O((n+m) log n)` | `O((n+m) log n)` | `O((n+m) log n)` |
| Extra space | `O(n)` | `O(n)` | `O(n)` |

---

### 6. Common Pitfalls & Fixes  

| Mistake | Fix |
|---------|-----|
| Off‑by‑one in binary search (using `upper_bound` then subtracting `1`) | Always test with minimal & maximal budgets. |
| Forgetting to store **prefix max** after sorting | Build a running max while iterating the sorted array. |
| Using a linear scan for each query | Replace with binary search (`bisect_right` / `upper_bound`). |
| Not reserving vector space (C++) | `ans.reserve(queries.size());` |
| Using `int` for sums that may overflow | Not required here because we only store individual beauties, but keep `long`/`long long` if you plan to sum values. |

---

### 7. Take‑Home Tips for Interviews  

* **Explain the approach first** – sorting + prefix array + binary search.  
* **Show the time‑space trade‑off** – why sorting is inevitable.  
* **Demonstrate edge case handling** – show that your binary search handles budgets smaller than any price and larger than all prices.  
* **Show confidence with boundaries** – be ready to answer “what happens if budgets equal min or max price?”.  

---

### 8. Final Words  

LeetCode 2070 may look simple, but it’s a great test of your algorithmic toolkit.  
By mastering **sorting + prefix arrays + binary search**, you’ll be prepared for a wide range of interview questions that revolve around *efficient query answering on sorted data*.  

Happy coding and good luck on your interview prep! 🚀

---
```

---

**Deploy** this Markdown on your personal blog or on a platform like GitHub Pages, and you’ll have a ready‑to‑share solution set with all the key interview concepts and the optimal algorithmic approach for LeetCode 2070.