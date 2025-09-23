---
title: LeetCode 3672. Sum of Weighted Modes in Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sum of Weighted Modes in Subarrays – A Complete Guide  
*LeetCode 3672 – Medium | Java | Python | C++ | Sliding Window + Balanced BST*  

---

## 1. Problem Recap  

You’re given an integer array `nums` (size ≤ 10⁵) and an integer `k`.  
For every contiguous subarray of length `k` we must:

| Step | Description |
|------|-------------|
| **1** | Count the frequency of every element in the subarray. |
| **2** | The *mode* is the element with the highest frequency; if several elements tie, choose the **smallest** one. |
| **3** | The *weight* of the subarray is `mode * frequency(mode)`. |
| **4** | Sum the weights of all `k`‑length subarrays. |

Return this sum as a `long`.

> **Example**  
> `nums = [1,2,2,3], k = 3` → weights: `4` (for `[1,2,2]`) + `4` (for `[2,2,3]`) = **8**.

---

## 2. Why This Problem Is a Great Interview Question  

| Aspect | Why It Matters |
|--------|----------------|
| **Sliding Window** | Core technique used in almost every array problem. |
| **Frequency Counting** | Demonstrates use of hash tables (`Map`, `unordered_map`). |
| **Order Statistics** | Need to quickly fetch “mode” → use a *balanced BST* or *heap* with custom comparison. |
| **Tie‑Breaking** | Subtle edge‑case that tests careful comparator design. |
| **Time/Space Complexity** | Must handle `n = 10⁵` → `O(n log n)` is fine, but `O(n²)` will TLE. |
| **Language Agnosticism** | Solvable in Java, Python, C++ → shows you can adapt data‑structures to the language. |

---

## 3. High‑Level Strategy  

1. **Maintain a frequency map** `freq[value] = count` for the current window.  
2. **Keep a sorted container** that orders pairs by **frequency descending, value ascending**.  
   * The first element is always the current mode.  
3. **Slide the window** one step at a time:  
   * Remove the outgoing element – update the frequency map and the sorted container.  
   * Add the incoming element – update the same structures.  
   * If the window is full (`size == k`), read the first element of the sorted container and add its weight to the answer.  

Because each element is inserted and removed at most once from the sorted container, the total cost is `O(n log n)`.

---

## 4. Implementation Details by Language  

### 4.1 Java (TreeSet)

Java’s `TreeSet` is a red‑black tree that lets us store pairs and maintain order.  
We store `int[] {freq, value}` and provide a custom comparator:

```java
TreeSet<int[]> set = new TreeSet<>(
    (a, b) -> a[0] != b[0] ? Integer.compare(b[0], a[0]) : Integer.compare(a[1], b[1])
);
```

We also use a `HashMap<Integer, Integer>` for the frequency table.

Full code below (same as the editorial solution but annotated for clarity).

```java
import java.util.*;

public class Solution {
    public long modeWeight(int[] nums, int k) {
        int n = nums.length;
        long ans = 0L;

        // Frequency map: value -> count
        Map<Integer, Integer> freq = new HashMap<>();

        // Sorted set of {count, value} pairs
        TreeSet<int[]> set = new TreeSet<>(
            (a, b) -> a[0] != b[0] ? Integer.compare(b[0], a[0]) : Integer.compare(a[1], b[1])
        );

        int left = 0;
        for (int right = 0; right < n; right++) {

            // Add new element
            int val = nums[right];
            int cnt = freq.getOrDefault(val, 0);
            if (cnt > 0) set.remove(new int[]{cnt, val});
            cnt++;
            freq.put(val, cnt);
            set.add(new int[]{cnt, val});

            // Remove old element when window > k
            if (right - left + 1 > k) {
                int oldVal = nums[left];
                int oldCnt = freq.get(oldVal);
                set.remove(new int[]{oldCnt, oldVal});
                oldCnt--;
                if (oldCnt == 0) {
                    freq.remove(oldVal);
                } else {
                    freq.put(oldVal, oldCnt);
                    set.add(new int[]{oldCnt, oldVal});
                }
                left++;
            }

            // When window is exactly k, add its weight
            if (right - left + 1 == k) {
                int[] modeInfo = set.first();          // highest freq, smallest value
                ans += 1L * modeInfo[0] * modeInfo[1]; // weight = freq * value
            }
        }
        return ans;
    }
}
```

> **Complexities**  
> Time: `O(n log n)` (log factor from `TreeSet` operations)  
> Space: `O(n)` (frequency map + set)

---

### 4.2 Python (SortedContainers + Counter)

Python’s standard library lacks a balanced BST, but the third‑party `sortedcontainers` package gives us a `SortedList` with O(log n) operations.  
If you can’t use external libs, you can emulate the logic with a max‑heap + lazy deletion – the code below uses `SortedList` for brevity.

```python
from collections import Counter
from sortedcontainers import SortedList

class Solution:
    def modeWeight(self, nums: list[int], k: int) -> int:
        n = len(nums)
        freq = Counter()
        # Sort by (-count, value) → largest count first, smallest value first
        order = SortedList(key=lambda x: (-x[0], x[1]))

        ans = 0
        left = 0
        for right, val in enumerate(nums):
            # add new
            c = freq.get(val, 0)
            if c:
                order.remove((c, val))
            c += 1
            freq[val] = c
            order.add((c, val))

            # slide left
            if right - left + 1 > k:
                old = nums[left]
                c_old = freq[old]
                order.remove((c_old, old))
                c_old -= 1
                if c_old:
                    freq[old] = c_old
                    order.add((c_old, old))
                else:
                    del freq[old]
                left += 1

            if right - left + 1 == k:
                mode_count, mode_val = order[0]
                ans += mode_count * mode_val

        return ans
```

> **Complexities**  
> Time: `O(n log n)`  
> Space: `O(n)`

---

### 4.3 C++ (unordered_map + multiset)

C++’s `multiset` is a balanced BST; we’ll store pairs `{count, value}` and use a custom comparator:

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long modeWeight(vector<int>& nums, int k) {
        int n = nums.size();
        long long ans = 0;

        unordered_map<int,int> freq;
        // multiset sorted by freq desc, value asc
        auto cmp = [](const pair<int,int>& a, const pair<int,int>& b){
            if(a.first != b.first) return a.first > b.first; // larger freq first
            return a.second < b.second;                       // smaller value first
        };
        multiset<pair<int,int>, decltype(cmp)> order(cmp);

        int left = 0;
        for(int right=0; right<n; ++right){
            int val = nums[right];
            int c = freq[val];
            if(c) order.erase(order.find({c,val}));
            ++c;
            freq[val] = c;
            order.insert({c,val});

            if(right - left + 1 > k){
                int old = nums[left];
                int c_old = freq[old];
                order.erase(order.find({c_old,old}));
                --c_old;
                if(c_old){
                    freq[old] = c_old;
                    order.insert({c_old,old});
                } else {
                    freq.erase(old);
                }
                ++left;
            }

            if(right - left + 1 == k){
                auto it = order.begin();
                ans += 1LL * it->first * it->second;
            }
        }
        return ans;
    }
};
```

> **Complexities**  
> Time: `O(n log n)`  
> Space: `O(n)`

---

## 5. Good, Bad, and Ugly of the Solution

| Category | What Works Well | Potential Pitfalls | Mitigation |
|----------|-----------------|--------------------|------------|
| **Good** | • Sliding window keeps each element processed twice (add/remove).  <br>• `TreeSet` / `multiset` automatically keeps the mode at the front.  <br>• No need to recompute frequencies from scratch. | | |
| **Bad** | • `TreeSet` or `multiset` operations cost `O(log n)` – acceptable but can be heavy if `k` is small and `n` large. | • In Python, using `sortedcontainers` may be considered an external dependency. | • Use heap + lazy deletion if external libs aren’t allowed. |
| **Ugly** | • Removing an element from a BST requires the exact pair; we must know the previous count to delete it.  <br>• If we forget to remove the old pair, the set will contain stale entries, corrupting the mode. | • Wrong comparator leads to wrong mode (e.g., forgetting to tie‑break by value). | • Always test with edge cases: repeated values, ties, `k == 1`, `k == n`.  <br>• Write helper functions to insert & delete that encapsulate the logic. |

---

## 6. Alternative Approaches (When `O(n log n)` is too slow)

| Approach | Trade‑off |
|----------|-----------|
| **Bucket Sort + Counting Sort** | If `nums[i] ≤ 10⁵`, we can use an array of size `10⁵ + 1` for frequencies and maintain a second array for counts → `O(1)` insertion/removal.  <br>However, getting the mode still requires scanning the counts array (`O(maxValue)`), which is `O(10⁵)` per window → too slow. |
| **Two Heaps + Lazy Deletion** | Use a max‑heap for `(count, value)` and a hash map for lazy deletions.  <br>Insertion O(log n), deletion O(1) (lazy).  <br>Still `O(n log n)`, but constant factor may be lower than `multiset`. |
| **Segment Tree / Binary Indexed Tree** | Store frequencies in a Fenwick tree and maintain a segment tree over values to query the maximum frequency.  <br>Complex to implement; not needed for this constraint. |

---

## 7. Why This Blog Helps You Land a Job

* **Keyword‑rich title** – “Sum of Weighted Modes in Subarrays – LeetCode 3672” shows the problem number and name.  
* **SEO‑friendly** – Frequent use of terms like “LeetCode Medium”, “Java/Python/C++”, “Sliding Window”, “Balanced BST”, “Interview Question”.  
* **Clear, step‑by‑step explanation** – Recruiters value engineers who can articulate the thought process.  
* **Multiple languages** – Demonstrates adaptability.  
* **Annotated code** – Shows not just “here’s the answer”, but “how to build it”.  
* **Edge‑case discussion** – Highlights defensive coding practices.  

Adding this article to your GitHub README or personal website signals that you’re not just solving problems—you’re learning, documenting, and sharing knowledge. Those are exactly the soft skills interviewers look for.

---

## 8. Final Takeaway

The key to this problem is recognizing that the **mode can be kept at the front of a sorted container** while we update frequencies on the fly.  
The resulting `O(n log n)` solution is both clean and efficient across Java, Python, and C++.

Happy coding, and good luck on your next interview! 🚀

---

*Author: Your Name – Software Engineer & Problem‑Solving Enthusiast.*