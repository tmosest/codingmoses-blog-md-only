---
title: LeetCode 2389. Longest Subsequence With Limited Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2389 – Longest Subsequence With Limited Sum  
### The Good, The Bad, and The Ugly – A Deep Dive & Job‑Ready Solution

> **Keywords** – *Longest Subsequence With Limited Sum*, *LeetCode 2389*, *Binary Search*, *Prefix Sum*, *Java*, *Python*, *C++*, *Interview Problem*, *Algorithm Design*, *Coding Interview*  

---

### 📌 Problem Recap

You’re given two integer arrays:

* `nums` – size **n** (1 ≤ n ≤ 1000)  
* `queries` – size **m** (1 ≤ m ≤ 1000)

For each `queries[i]` you must find the **maximum number of elements** you can pick from `nums` **without changing the original order** such that the sum of the chosen elements is ≤ `queries[i]`.  
Return an array of those maximum sizes.

*Example*  
`nums = [4,5,2,1]`, `queries = [3,10,21]` → answer `[2,3,4]`.

---

### 🏆 Why It Matters for Interviews

* **Greedy + Prefix Sum + Binary Search** – Classic 3‑step pattern that many interviewers love to see.  
* **Time Complexity** – `O((n+m) log n)` – perfect for medium‑size inputs and a great demonstration of asymptotic reasoning.  
* **Language Versatility** – We’ll show clean implementations in **Java, Python, and C++**, covering the most popular interview languages.

---

## 🔍 The Core Idea (The Good)

1. **Sorting Does Not Hurt** –  
   We’re only interested in the *sum*, not the original order. Sorting `nums` gives us the smallest numbers first, allowing us to build the longest possible subsequence greedily.

2. **Prefix Sum Array** –  
   Convert the sorted array into a running‑sum array.  
   `prefix[i] = nums[0] + … + nums[i]`.  
   Now, the sum of the first `k` smallest numbers is just `prefix[k-1]`.

3. **Binary Search on Prefix** –  
   For each query `q`, find the greatest index `k` where `prefix[k] ≤ q`.  
   The answer is `k + 1`.  
   In C++/Java we can use `upper_bound`; in Python we use `bisect_right`.

4. **Edge Cases** –  
   *Empty subsequence* (when `q` is smaller than the smallest number) → answer `0`.  
   *All elements fit* (when `q` ≥ total sum) → answer `n`.

---

## ⚠️ The Pitfalls (The Bad)

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| Using **linear scan** for every query | O(n m) → 1 000 000 ops (acceptable here but not optimal) | Build prefix + binary search |
| Forgetting to sort `nums` | Wrong subsequence length | `Arrays.sort(nums)` / `sort(nums)` |
| Mis‑interpreting binary search result | Off‑by‑one errors (e.g., returning `index` instead of `index+1`) | Use `upper_bound` or `bisect_right` that already returns the count |
| Integer overflow | Sum of up to 1000 elements each ≤ 10⁶ → up to 10⁹, still fits in 32‑bit but keep `long` for safety | Use `long` for prefix sums in Java/C++ |

---

## 📦 Code Solutions

Below are clean, production‑ready implementations for **Java**, **Python**, and **C++**.  
Each snippet follows the exact three‑step strategy described above.

---

### ✅ Java (Java 17)

```java
import java.util.Arrays;
import java.util.List;
import java.util.ArrayList;
import java.util.stream.Collectors;

public class Solution {
    public int[] answerQueries(int[] nums, int[] queries) {
        // 1️⃣ Sort the numbers
        Arrays.sort(nums);

        // 2️⃣ Build prefix sums (using long to avoid overflow)
        long[] prefix = new long[nums.length];
        long sum = 0;
        for (int i = 0; i < nums.length; i++) {
            sum += nums[i];
            prefix[i] = sum;
        }

        // 3️⃣ Answer each query with binary search (upper_bound)
        int[] result = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int q = queries[i];
            int pos = upperBound(prefix, q);
            result[i] = pos;          // pos is the count of elements ≤ q
        }
        return result;
    }

    // Custom upper_bound: first index where prefix[idx] > target
    private int upperBound(long[] arr, int target) {
        int lo = 0, hi = arr.length;
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (arr[mid] <= target) {
                lo = mid + 1;
            } else {
                hi = mid;
            }
        }
        return lo; // number of elements <= target
    }

    // Optional: main for quick local testing
    public static void main(String[] args) {
        Solution s = new Solution();
        int[] nums = {4,5,2,1};
        int[] queries = {3,10,21};
        System.out.println(Arrays.toString(s.answerQueries(nums, queries)));
    }
}
```

---

### ✅ Python 3

```python
from bisect import bisect_right
from itertools import accumulate
from typing import List

class Solution:
    def answerQueries(self, nums: List[int], queries: List[int]) -> List[int]:
        # 1️⃣ Sort
        nums.sort()

        # 2️⃣ Prefix sums
        prefix = list(accumulate(nums))

        # 3️⃣ Binary search for each query
        return [bisect_right(prefix, q) for q in queries]

# Quick test
if __name__ == "__main__":
    sol = Solution()
    print(sol.answerQueries([4,5,2,1], [3,10,21]))  # -> [2, 3, 4]
```

---

### ✅ C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> answerQueries(vector<int> nums, vector<int> queries) {
        // 1️⃣ Sort
        sort(nums.begin(), nums.end());

        // 2️⃣ Prefix sums (long long for safety)
        vector<long long> prefix(nums.size());
        partial_sum(nums.begin(), nums.end(), prefix.begin());

        // 3️⃣ For each query use upper_bound
        vector<int> ans;
        for (int q : queries) {
            // upper_bound returns iterator to first element > q
            int cnt = upper_bound(prefix.begin(), prefix.end(), q) - prefix.begin();
            ans.push_back(cnt);
        }
        return ans;
    }
};

// Simple driver for manual testing
int main() {
    Solution s;
    vector<int> nums = {4,5,2,1};
    vector<int> queries = {3,10,21};
    vector<int> res = s.answerQueries(nums, queries);
    for (int v : res) cout << v << ' ';
    cout << endl;  // prints: 2 3 4
}
```

---

## 📈 Time & Space Complexity

| Language | Sorting | Prefix | Queries | Total |
|----------|---------|--------|---------|-------|
| Java | `O(n log n)` | `O(n)` | `O(m log n)` | **`O((n+m) log n)`** |
| Python | `O(n log n)` | `O(n)` | `O(m log n)` | **`O((n+m) log n)`** |
| C++ | `O(n log n)` | `O(n)` | `O(m log n)` | **`O((n+m) log n)`** |

Space: `O(n)` for the prefix array (plus input arrays).  
All languages keep the same asymptotic profile.

---

## 🧠 What Interviewers Look For

1. **Clarification** – Did you ask about the need for order? The greedy approach works because order does not matter for sum constraints.
2. **Proof of Correctness** – Sorting yields the shortest elements first; any longer subsequence would necessarily use larger elements and exceed the same sum.
3. **Edge Cases** – Show you handle an empty subsequence and queries larger than the total sum.
4. **Complexity Analysis** – Provide big‑O numbers and explain why binary search is essential.
5. **Clean Code** – Use descriptive variable names, separate helper functions (`upperBound`), and comment key steps.

---

## 🏁 TL;DR (Job‑Ready Takeaway)

*Sort → Prefix Sum → Binary Search.*  
- **Sort** `nums` to access the smallest numbers first.  
- **Prefix Sum** lets you query the sum of any prefix in O(1).  
- **Binary Search** (`upper_bound` / `bisect_right`) gives you the longest prefix that fits the query in `O(log n)`.

All three languages above implement this pattern elegantly, giving you a 5‑minute, interview‑grade solution that will impress recruiters and pass any coding challenge. Happy coding! 🎯