---
title: LeetCode 2070. Most Beautiful Item for Each Query - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔥 LeetCode 2070 – “Most Beautiful Item for Each Query”  
*A full‑stack guide (Java | Python | C++) + SEO‑ready blog post to land your next tech job*

---

### 1️⃣ Problem Overview  

> **Problem ID**: 2070  
> **Difficulty**: Medium  
> **Tags**: Sorting | Binary Search | Prefix Max | Interview

You’re given an array of items, each represented as `[price, beauty]`, and an array of queries.  
For each query `q`, you must answer: *What is the maximum beauty of an item whose price ≤ q?*  
If no such item exists, return `0`.

---

### 2️⃣ Constraints  

| Parameter | Range | Notes |
|-----------|-------|-------|
| `items.length`, `queries.length` | 1 … 10⁵ | Big, but still manageable with O((n+q) log n) |
| `price`, `beauty`, `queries[j]` | 1 … 10⁹ | 32‑bit signed int is enough in Java/Python, but use 64‑bit in C++ |
| Items can have duplicate prices or beauties | ✔️ | Must handle ties correctly |

---

### 3️⃣ Intuition & Key Idea  

1. **Sort the items by price** – this puts all affordable items for a budget in a contiguous block.  
2. **Build a “price → best beauty” map** – as we sweep the sorted items, keep the running maximum beauty.  
3. **Answer queries in O(log n)** by binary searching the first price that is ≤ the query value.  

This reduces the naive O(n·q) approach (checking every item for every query) to O((n+q) log n).

---

### 4️⃣ Step‑by‑Step Algorithm  

1. **Sort** `items` by the first element (`price`).  
2. **Pre‑process**:  
   ```text
   maxBeauty = 0
   for each item (price, beauty) in sorted items:
       maxBeauty = max(maxBeauty, beauty)
       if price != lastPriceSeen:
           record (price, maxBeauty)  // keeps the best beauty up to this price
   ```
   This produces two parallel arrays: `prices[]` and `beauties[]`.  
3. **Answer queries**: For each `q` in `queries`, binary search `prices[]` to find the largest index `i` with `prices[i] ≤ q`.  
   If no such index, answer `0`; otherwise answer `beauties[i]`.

---

### 5️⃣ Complexity Analysis  

| Operation | Time | Space |
|-----------|------|-------|
| Sorting items | **O(n log n)** | O(n) |
| Pre‑processing | **O(n)** | O(n) |
| Query loop (binary search) | **O(q log n)** | O(1) |
| **Total** | **O((n+q) log n)** | **O(n)** |

With `n, q ≤ 10⁵`, this comfortably fits within the time limits of LeetCode.

---

### 6️⃣ Edge Cases & Pitfalls  

| Edge Case | Why it matters | Fix |
|-----------|----------------|-----|
| Duplicate prices with different beauties | Binary search may return the wrong beauty if we don’t keep the **maximum** beauty for that price | Keep a running max and update only when beauty increases |
| All queries smaller than the smallest price | Should return `0` | Binary search returns `-1`; treat as `0` |
| Large integer values (up to 1e9) | Risk of overflow in some languages | Use 64‑bit integers (`long` in Java, `long long` in C++, default int in Python) |

---

### 7️⃣ Code Implementations  

> **All implementations follow the same algorithmic logic.**  
> Use the language that matches your job target; feel free to copy‑paste into your editor.

#### 7.1 Java (Java 17)

```java
import java.util.*;

public class Solution {
    public int[] maximumBeauty(int[][] items, int[] queries) {
        // 1️⃣ sort items by price
        Arrays.sort(items, Comparator.comparingInt(a -> a[0]));

        // 2️⃣ build prefix max beauty arrays
        int n = items.length;
        int[] prices = new int[n];
        int[] bestBeauties = new int[n];
        int maxBeauty = 0;
        int idx = 0;
        for (int[] it : items) {
            int price = it[0];
            int beauty = it[1];
            maxBeauty = Math.max(maxBeauty, beauty);
            prices[idx] = price;
            bestBeauties[idx] = maxBeauty;
            idx++;
        }

        // 3️⃣ answer each query by binary search
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int q = queries[i];
            int pos = upperBound(prices, q) - 1; // last index ≤ q
            ans[i] = (pos >= 0) ? bestBeauties[pos] : 0;
        }
        return ans;
    }

    // first index > target
    private int upperBound(int[] arr, int target) {
        int l = 0, r = arr.length;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (arr[m] <= target) l = m + 1;
            else r = m;
        }
        return l;
    }
}
```

#### 7.2 Python (Python 3)

```python
from bisect import bisect_right
from typing import List

class Solution:
    def maximumBeauty(self, items: List[List[int]], queries: List[int]) -> List[int]:
        # 1️⃣ sort by price
        items.sort(key=lambda x: x[0])

        # 2️⃣ build prefix max arrays
        prices, beauties = [], []
        max_b = 0
        for price, beauty in items:
            max_b = max(max_b, beauty)
            prices.append(price)
            beauties.append(max_b)

        # 3️⃣ answer queries
        res = []
        for q in queries:
            idx = bisect_right(prices, q) - 1
            res.append(beauties[idx] if idx >= 0 else 0)
        return res
```

#### 7.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maximumBeauty(vector<vector<int>>& items, vector<int>& queries) {
        // 1️⃣ sort items by price
        sort(items.begin(), items.end(),
             [](const auto& a, const auto& b){ return a[0] < b[0]; });

        // 2️⃣ prefix max beauty
        int n = items.size();
        vector<int> prices(n), bestBeauty(n);
        int curMax = 0;
        for (int i = 0; i < n; ++i) {
            curMax = max(curMax, items[i][1]);
            prices[i] = items[i][0];
            bestBeauty[i] = curMax;
        }

        // 3️⃣ answer queries
        vector<int> ans;
        ans.reserve(queries.size());
        for (int q : queries) {
            int idx = upper_bound(prices.begin(), prices.end(), q) - prices.begin() - 1;
            ans.push_back(idx >= 0 ? bestBeauty[idx] : 0);
        }
        return ans;
    }
};
```

---

### 8️⃣ “Good, Bad & Ugly” – What the Interviewer Really Wants  

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Good** | *Simplicity*: sort + prefix max + binary search. The logic is clear, easy to test, and works in all major languages. | *Naïve solution*: O(n·q) with two nested loops is far too slow for 10⁵. | *Readability*: Avoid over‑engineering; keep variable names intuitive. |
| **Bad** | LeetCode sometimes forces 1‑second limits – a quadratic solution will TLE. | Over‑complicating binary search implementation can cause bugs (off‑by‑one errors). | Misusing 32‑bit ints in C++ can lead to overflow. |
| **Ugly** | Memory overhead of storing extra arrays – but still O(n). | Unnecessary use of HashMap for price→beauty instead of two parallel arrays increases constant factors. | Re‑computing max beauty for each query (inefficient). |

---

### 9️⃣ Interview‑Ready Tips  

| Skill | How the Problem Helps | How to Showcase It |
|-------|-----------------------|--------------------|
| **Data Structures** | Sorting + binary search is a classic pattern. | Mention “sorted‑array + prefix max” in your explanation. |
| **Complexity Thinking** | Candidates must justify O((n+q) log n) vs. O(n·q). | In your interview, state the time/space bounds and why they’re acceptable. |
| **Testing** | Edge cases (duplicates, small budgets). | Write unit tests in your repo or mention them in your blog post. |
| **Communication** | Clear, concise code + comments. | Show your code on GitHub or a blog; reference it in your resume. |
| **Language Proficiency** | Java, Python, C++ – all accepted. | Pick the language most used at the target company. |

---

### 10️⃣ Blog Post (SEO‑Ready)  

> **Title**: LeetCode 2070 – “Most Beautiful Item for Each Query” – Java, Python & C++ Solutions  
> **Meta Description**: Master LeetCode 2070 with efficient Java, Python, and C++ solutions. Learn the algorithm, complexity, and interview tips to boost your coding interview prep.  

```markdown
# LeetCode 2070 – “Most Beautiful Item for Each Query”

---

## 📌 Problem Statement

You’re given an array `items` where each element is `[price, beauty]`, and an array `queries`.  
For every query `q`, return the maximum beauty of an item with `price <= q`.  
If no item can be afforded, return `0`.

---

## 🚀 Constraints

- `1 ≤ items.length, queries.length ≤ 10⁵`
- `1 ≤ price, beauty, queries[i] ≤ 10⁹`
- Items may share the same price or beauty.

---

## 💡 Intuition

Sorting the items by price lets us sweep them once and keep a running maximum beauty.  
With a prefix‑maximum table we can answer each query with binary search in `O(log n)`.

---

## 📊 Algorithm

1. **Sort** `items` by price.  
2. **Sweep** the sorted array, building two arrays:  
   - `prices[]` – unique, increasing prices.  
   - `bestBeauties[]` – maximum beauty seen up to that price.  
3. For each query `q`, binary search `prices[]` for the greatest `price ≤ q` and output the corresponding beauty.  
   If none, output `0`.

---

## 📝 Complexity

- **Time**: `O((n + q) log n)`  
- **Space**: `O(n)`  

---

## 🧪 Edge Cases

| Case | Why | Fix |
|------|-----|-----|
| Duplicate prices | Must keep the *maximum* beauty | Update only when beauty > current max |
| Query < smallest price | Should return `0` | Binary search yields `-1` → treat as `0` |
| Large integers | Risk of overflow | Use 64‑bit (`long` / `long long`) |

---

## 🛠️ Code (Java / Python / C++)

### Java

```java
// paste into your LeetCode editor
public class Solution {
    public int[] maximumBeauty(int[][] items, int[] queries) {
        Arrays.sort(items, Comparator.comparingInt(a -> a[0]));
        int n = items.length;
        int[] prices = new int[n];
        int[] bestBeauties = new int[n];
        int maxBeauty = 0;
        for (int i = 0; i < n; i++) {
            maxBeauty = Math.max(maxBeauty, items[i][1]);
            prices[i] = items[i][0];
            bestBeauties[i] = maxBeauty;
        }
        int[] ans = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int q = queries[i];
            int idx = upperBound(prices, q) - 1;
            ans[i] = (idx >= 0) ? bestBeauties[idx] : 0;
        }
        return ans;
    }
    private int upperBound(int[] arr, int target) {
        int l = 0, r = arr.length;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (arr[m] <= target) l = m + 1;
            else r = m;
        }
        return l;
    }
}
```

### Python

```python
from bisect import bisect_right
from typing import List

class Solution:
    def maximumBeauty(self, items: List[List[int]], queries: List[int]) -> List[int]:
        items.sort(key=lambda x: x[0])
        prices, best = [], []
        cur_max = 0
        for p, b in items:
            cur_max = max(cur_max, b)
            prices.append(p)
            best.append(cur_max)
        return [best[bisect_right(prices, q)-1] if bisect_right(prices, q) else 0 for q in queries]
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> maximumBeauty(vector<vector<int>>& items, vector<int>& queries) {
        sort(items.begin(), items.end());
        vector<int> prices, best;
        int curMax = 0;
        for (auto& it : items) {
            curMax = max(curMax, it[1]);
            prices.push_back(it[0]);
            best.push_back(curMax);
        }
        vector<int> ans;
        ans.reserve(queries.size());
        for (int q : queries) {
            int pos = upper_bound(prices.begin(), prices.end(), q) - prices.begin() - 1;
            ans.push_back(pos >= 0 ? best[pos] : 0);
        }
        return ans;
    }
};
```

---

## 📚 Interview‑Ready Checklist

| ✔️ | Action |
|----|--------|
| ✅ Master *sorting + prefix max* pattern | Works for many “max value ≤ X” problems. |
| ✅ Practice binary search on arrays | Avoid off‑by‑one bugs. |
| ✅ Validate edge cases on LeetCode’s “Test Cases” tab | Build confidence. |
| ✅ Document your solution on a blog | Recruiters love seeing clean, readable code + explanations. |
| ✅ Share on LinkedIn/GitHub | Increase visibility. |

---

## 🎯 Next Steps

- Clone this repo: <https://github.com/your-username/leetcode-2070>
- Follow the “Interview‑Ready Checklist”.
- Add more test harnesses in your unit tests.

Good luck crushing **LeetCode 2070** and landing your dream software role!

```

---

### 11️⃣ Closing  

You now have:

1. **Three polished solutions** (Java, Python, C++) ready for any interview.
2. A **blog post** that showcases your understanding of the algorithm, complexity, and edge‑case handling.
3. **Interview tips** to frame this problem as a showcase of your data‑structure, algorithmic, and communication skills.

Happy coding and best of luck with your next interview – you’ve got this! 🎉

--- 

*Feel free to adapt the code snippets and explanations to your personal style or the specific company's coding standards.* 

--- 

*All content above is for educational purposes and can be freely used on GitHub, personal blogs, or interview prep materials.* 

--- 

**End of Guide**  
--- 
``` 

---

Feel free to copy the above Markdown into your blog editor (e.g., Jekyll, Hugo, Medium) and adjust the meta tags to suit your SEO strategy. Your post should include images of the algorithm flow or test case diagrams to further boost engagement. Good luck!