---
title: LeetCode 3478. Choose K Elements With Maximum Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

**LeetCode 3478 – Choose K Elements With Maximum Sum**

*Input*  
`nums1`, `nums2` – two integer arrays of length `n`  
`k` – a positive integer (`1 ≤ k ≤ n`)

*Task*  
For every index `i` (0‑based):

1. Find all indices `j` such that `nums1[j] < nums1[i]`.
2. From those indices choose **at most** `k` values of `nums2[j]` that give the maximum possible sum.
3. Store that sum in `answer[i]`.

Return the array `answer`.

*Constraints*  
`1 ≤ n ≤ 10⁵`  
`1 ≤ nums1[i], nums2[i] ≤ 10⁶`

The classic solution uses a **sort + min‑heap** (priority queue) and runs in `O(n log n)` time and `O(k)` extra space.

Below you’ll find clean, well‑commented implementations in **Java, Python, and C++**.

---

## 2.  Core Idea

1. **Sort by `nums1`.**  
   When the array is sorted increasingly, every element we have already processed has a `nums1` value *strictly smaller* than the current one.  
   The only nuance: duplicates must not be considered “smaller”. We solve this by grouping equal values.

2. **Maintain a min‑heap of the best `k` `nums2` seen so far.**  
   - The heap stores the *k largest* `nums2` values encountered.  
   - `sum` keeps the current total of the heap contents.  
   - Whenever a new `nums2` value is inserted, we push it; if the heap grows beyond `k`, we pop the smallest value and subtract it from `sum`.  
   Thus `sum` is always the maximum sum of at most `k` elements that come from indices with a smaller `nums1`.

3. **Answer for a group of equal `nums1`**  
   For each index in the group we assign the current `sum`.  
   Only after all answers for the group are set do we add that group’s `nums2` values to the heap (so they’re not counted for themselves).

---

## 3.  Implementation

### 3.1 Java

```java
import java.util.*;

/**
 * LeetCode 3478 – Choose K Elements With Maximum Sum
 */
class Solution {
    public long[] findMaxSum(int[] nums1, int[] nums2, int k) {
        int n = nums1.length;
        long[] answer = new long[n];

        // pair each element with its original index
        int[][] pairs = new int[n][2];
        for (int i = 0; i < n; i++) {
            pairs[i][0] = nums1[i];   // value for sorting
            pairs[i][1] = i;          // original index
        }

        // sort by nums1 ascending
        Arrays.sort(pairs, Comparator.comparingInt(a -> a[0]));

        // min‑heap for the k largest nums2 values seen so far
        PriorityQueue<Integer> heap = new PriorityQueue<>();
        long currentSum = 0;

        int i = 0;
        while (i < n) {
            int j = i;
            // find the end of the current group (all equal nums1)
            while (j < n && pairs[j][0] == pairs[i][0]) j++;

            // 1️⃣ answer for every index in the group uses the currentSum
            for (int t = i; t < j; t++) {
                answer[pairs[t][1]] = currentSum;
            }

            // 2️⃣ now add this group's nums2 values to the heap
            for (int t = i; t < j; t++) {
                int idx = pairs[t][1];
                int val = nums2[idx];
                heap.offer(val);
                currentSum += val;

                // keep only the k largest values
                if (heap.size() > k) {
                    currentSum -= heap.poll();
                }
            }

            i = j; // move to the next group
        }

        return answer;
    }
}
```

### 3.2 Python

```python
from heapq import heappush, heappop
from typing import List

class Solution:
    def findMaxSum(self, nums1: List[int], nums2: List[int], k: int) -> List[int]:
        n = len(nums1)
        answer = [0] * n

        # pair value with original index
        pairs = sorted([(val, idx) for idx, val in enumerate(nums1)], key=lambda x: x[0])

        heap = []          # min‑heap of best k nums2 values
        current_sum = 0
        i = 0
        while i < n:
            j = i
            while j < n and pairs[j][0] == pairs[i][0]:
                j += 1

            # answer for this group
            for t in range(i, j):
                answer[pairs[t][1]] = current_sum

            # add group's nums2 to the heap
            for t in range(i, j):
                idx = pairs[t][1]
                val = nums2[idx]
                heappush(heap, val)
                current_sum += val
                if len(heap) > k:
                    current_sum -= heappop(heap)

            i = j

        return answer
```

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<long long> findMaxSum(vector<int>& nums1, vector<int>& nums2, int k) {
        int n = nums1.size();
        vector<long long> ans(n, 0);

        // pair {nums1 value, original index}
        vector<pair<int,int>> pairs(n);
        for (int i = 0; i < n; ++i) pairs[i] = {nums1[i], i};

        sort(pairs.begin(), pairs.end(), [](const auto& a, const auto& b){
            return a.first < b.first;
        });

        priority_queue<int, vector<int>, greater<int>> minHeap;
        long long curSum = 0;
        int i = 0;
        while (i < n) {
            int j = i;
            while (j < n && pairs[j].first == pairs[i].first) ++j; // group of equal nums1

            // 1️⃣ give answer for the whole group
            for (int t = i; t < j; ++t)
                ans[pairs[t].second] = curSum;

            // 2️⃣ add group's nums2 into the heap
            for (int t = i; t < j; ++t) {
                int idx = pairs[t].second;
                int val = nums2[idx];
                minHeap.push(val);
                curSum += val;
                if ((int)minHeap.size() > k) {
                    curSum -= minHeap.top();
                    minHeap.pop();
                }
            }

            i = j;
        }
        return ans;
    }
};
```

All three solutions run in `O(n log n)` time and use `O(k)` extra space, which comfortably fits the constraints (`n ≤ 10⁵`).

---

## 4.  Blog Post – “Choose K Elements With Maximum Sum: The Good, The Bad & The Ugly”

> **SEO Keywords**: LeetCode 3478, Choose K Elements With Maximum Sum, sort‑based heap, priority queue, algorithm design, time‑space complexity, Java, Python, C++ solutions

---

### 4.1 Introduction

If you’re tackling array‑based interview questions, you’ve probably seen *“pick the best k”* problems all the time.  
LeetCode 3478 asks you to do just that – but with an added twist: the indices you can pick from are **restricted by a comparison on a second array**.  
In this post we walk through the **good** solution, point out the **bad** pitfalls that frequently trip people up, and expose the **ugly** corner‑cases that make this problem subtle.

---

### 4.2 The “Good” – Why This Approach is Elegant

1. **Sorting gives a linear order for “smaller”**  
   Once the array is sorted by `nums1`, all earlier elements satisfy `nums1[j] < nums1[i]` automatically – no expensive scans needed.

2. **Min‑heap keeps only the best k numbers**  
   Because the heap is a *min* heap, we can keep exactly `k` numbers in `O(log k)` per insertion.  
   The running `sum` gives you the answer instantly – no re‑scanning or recomputation.

3. **Group‑by‑equal‑value logic**  
   Duplicates would otherwise give a wrong “smaller” relationship. Grouping them ensures strictness and keeps the algorithm linear after the initial sort.

---

### 4.3 The “Bad” – Common Mistakes

| Mistake | Why It Breaks | Fix |
|---------|---------------|-----|
| **Using a `<=` comparison** for duplicates | Indices with the same `nums1` value are considered “smaller” and end up adding themselves to the heap. | Group equal values and answer **before** inserting the group. |
| **Pushing each element individually before assigning answers** | Elements in the current index get counted in their own sum. | Assign answers for the whole group first, then push the group into the heap. |
| **Using an array instead of a heap** | You’d have to sort each time you need the top‑k, turning the algorithm into `O(nk)` or worse. | Use a `PriorityQueue`/`heapq`/`min‑heap`. |
| **Assuming k equals n** | The heap would hold all values, but the algorithm still works; however, using a heap in that case is overkill. | If you want a super‑fast edge case, you can simply sort `nums2` and take the top‑k sum, but the generic solution remains clean. |

---

### 4.4 The “Ugly” – Edge Cases & Extensions

1. **Very large `k`**  
   When `k` is close to `n`, the heap will grow to that size; still fine, but you can skip heap maintenance entirely by taking the sum of the first `k` sorted `nums2` values.  
   *Complexity*: `O(n log n)` still.

2. **Negative numbers in `nums2`**  
   The problem statement guarantees positive integers, but if you relax that, the “at most k” rule becomes essential: you might choose fewer than `k` items if adding a negative value reduces the total.

3. **Updating `k` on the fly**  
   If the interviewer wants a dynamic version where `k` changes per query, you’d need a balanced BST or a two‑heap trick – a whole different discussion.

4. **Space‑saving variant**  
   Instead of a heap you can keep a sorted multiset of the best `k` values (`std::multiset` in C++ or `SortedList` in Python).  
   The time complexity remains `O(n log k)`, but the constants are a bit larger.

---

### 4.5 Complexity Recap

| Language | Time | Extra Space |
|----------|------|-------------|
| Java | `O(n log n)` | `O(k)` |
| Python | `O(n log n)` | `O(k)` |
| C++ | `O(n log n)` | `O(k)` |

With `n ≤ 100,000`, each solution runs in well under a second on typical hardware.

---

### 4.6 Final Thoughts

LeetCode 3478 is a great illustration of how **sorting + a priority queue** turns a seemingly expensive “pick the best k” problem into a clean, linear‑time solution.  
By carefully handling duplicates, we avoid the most common pitfall and preserve the strict “<” relationship.  

Feel free to copy the code snippets into your local IDE, run the test cases, and practice tweaking the algorithm for variations (e.g., “pick the best k *greater*” instead of “smaller”). Happy coding!