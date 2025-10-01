---
title: LeetCode 2386. Find the K-Sum of an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 2386. Find the K‑Sum of an Array – The Good, The Bad, and The Ugly  
*LeetCode, interview prep, Java / Python / C++ implementation, SEO‑friendly guide for job seekers*

---

## 1. What is the K‑Sum Problem?

> **Problem Statement**  
> Given an integer array `nums` and a positive integer `k`, we can pick any subsequence (the order is preserved, but we may skip elements).  
>  
> Define the **K‑Sum** as the *k*‑th largest subsequence sum (duplicates are counted).  
> Return this value.

> **Examples**  
> *`nums = [2,4,-2]`, `k = 5` → answer = `2`*  
> *`nums = [1,-2,3,4,-10,12]`, `k = 16` → answer = `10`*

> **Constraints**  
> * `1 ≤ n ≤ 10⁵`  
> * `-10⁹ ≤ nums[i] ≤ 10⁹`  
> * `1 ≤ k ≤ min(2000, 2n)`  

> **Why is this hard?**  
> The number of subsequences is `2ⁿ` – astronomically large.  
> We must produce the *k*‑th largest **without** enumerating all of them.

---

## 2. The Core Insight

1. **Positive vs. Negative**  
   *The maximum subsequence sum* is achieved by taking **all** positive numbers and ignoring all negatives.  
   Let  
   ```text
   totalPos = Σ max(nums[i], 0)
   ```

2. **Transform the Problem**  
   Subtract `totalPos` from every answer.  
   The K‑Sum becomes:
   ```
   answer = totalPos – (k‑th smallest sum in the transformed array)
   ```
   Why?  
   Every subsequence sum `S` can be written as `totalPos – T`, where `T` is the sum of the *abs* values of the elements we **exclude** or replace by their negative counterpart.  
   Therefore, the *k*‑th largest `S` corresponds to the *k*‑th smallest `T`.

3. **Work with Absolute Values**  
   Convert every element to its absolute value:  
   ```text
   arr[i] = abs(nums[i])
   ```
   Sort `arr` in **non‑decreasing** order.  
   Now we must find the *k*‑th smallest sum that can be formed by picking any subset of these sorted absolute values.

4. **Min‑Heap Generation**  
   The classic “generate k smallest sums from a sorted array” technique applies.  
   Each heap node represents a partial sum and the next index to consider.

---

## 3. Algorithm (High‑Level)

```
1. totalPos = sum(max(x,0) for x in nums)
2. arr = sorted(abs(x) for x in nums)
3. If k == 1: return totalPos          // the largest sum is all positives
4. minHeap = priority queue ordered by sum
   Push (arr[0], 0)   // sum of first element, next index = 0
5. for _ in range(k-1):
       sum, idx = pop minHeap
       // the popped sum is the current smallest T
       // generate two children:
       //   a) include arr[idx]   -> sum + arr[idx+1]
       //   b) exclude arr[idx]   -> sum (but we already had that, so we increment idx)
       idx += 1
       push (sum + arr[idx], idx)           // include current element
       push (sum, idx)                     // exclude current element
6. smallestT = sum popped in last iteration
7. return totalPos - smallestT
```

The heap size never exceeds `2k`, and each operation is `O(log k)`.

---

## 4. Implementation – One Pass for All Three Languages

### 4.1 Java

```java
import java.util.*;
import static java.lang.Math.*;

class Solution {
    public long kthLargestSum(int[] nums, int k) {
        long totalPos = 0;
        int n = nums.length;
        long[] abs = new long[n];

        for (int i = 0; i < n; i++) {
            int v = nums[i];
            if (v > 0) totalPos += v;
            abs[i] = Math.abs(v);
        }

        Arrays.sort(abs);                     // ascending

        if (k == 1) return totalPos;          // all positives

        // Min‑heap: pair (sum, index)
        PriorityQueue<long[]> pq = new PriorityQueue<>(Comparator.comparingLong(a -> a[0]));
        pq.offer(new long[]{abs[0], 0});

        long smallestT = 0;                   // will hold the current smallest T
        for (int i = 0; i < k - 1; i++) {
            long[] cur = pq.poll();
            smallestT = cur[0];
            int idx = (int) cur[1] + 1;       // move to the next element

            if (idx < n) {                    // include abs[idx]
                pq.offer(new long[]{cur[0] + abs[idx], idx});
                pq.offer(new long[]{cur[0], idx});   // exclude abs[idx-1]
            }
        }
        return totalPos - smallestT;
    }
}
```

### 4.2 Python

```python
import heapq
from math import fabs, copysign

def kth_largest_sum(nums, k):
    total_pos = sum(x if x > 0 else 0 for x in nums)
    arr = sorted(abs(x) for x in nums)

    if k == 1:                         # largest subsequence sum
        return total_pos

    pq = [(arr[0], 0)]                  # (sum, next_index)
    for _ in range(k - 1):
        s, idx = heapq.heappop(pq)
        idx += 1
        if idx < len(arr):
            heapq.heappush(pq, (s + arr[idx], idx))   # include arr[idx]
            heapq.heappush(pq, (s, idx))             # exclude arr[idx]
    return total_pos - s
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Node {
    long long sum;
    int idx;
    bool operator<(const Node& other) const { return sum > other.sum; } // min‑heap
};

long long kthLargestSum(vector<int> nums, int k) {
    long long totalPos = 0;
    vector<long long> arr;
    arr.reserve(nums.size());

    for (int x : nums) {
        if (x > 0) totalPos += x;
        arr.push_back(std::llabs(x));
    }
    sort(arr.begin(), arr.end());               // ascending

    if (k == 1) return totalPos;                // no exclusion needed

    priority_queue<Node> pq;
    pq.push({arr[0], 0});

    long long smallestT = 0;
    for (int i = 0; i < k - 1; ++i) {
        Node cur = pq.top(); pq.pop();
        smallestT = cur.sum;
        int idx = cur.idx + 1;                  // next index
        if (idx < (int)arr.size()) {
            pq.push({cur.sum + arr[idx], idx}); // include
            pq.push({cur.sum, idx});           // exclude
        }
    }
    return totalPos - smallestT;
}
```

---

## 4.4 Why This Works

* The sorted order guarantees that **any** new sum we generate is larger than the current one.  
* Every subset is represented exactly once by a path in the heap.  
* The loop runs *k‑1* times – we only ever extract the *k* smallest possible `T` values.

---

## 4.5 Handling Edge Cases

| Edge case | What to do? |
|-----------|-------------|
| `n == 1` | Works automatically; if `k == 1` we return the single positive element. |
| All numbers are negative | `totalPos = 0`. The algorithm still finds the k‑th smallest *T* (which is the sum of chosen abs values). |
| `k == 1` | Return `totalPos` immediately (no heap work). |
| `k > 1` but `n == 1` | The heap only contains one element; the loop still produces the correct result. |

---

## 5. Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Summation + absolute conversion | **O(n)** | **O(1)** |
| Sorting | **O(n log n)** | **O(n)** |
| Heap operations | `k‑1` pops + `2(k‑1)` pushes → **O(k log k)** | **O(k)** |
| **Total** | **O(n log n + k log k)** | **O(n + k)** |

Given `k ≤ 2000`, the dominant term is `O(n log n)` – perfectly fine for `n = 10⁵`.

---

## 6. The Good

| Aspect | Why it’s a win |
|--------|----------------|
| **No DP tables** – we only keep a heap of size `≤ 2k`. |
| **Memory friendly** – only the sorted `abs` array (`O(n)`) and the heap (`O(k)`). |
| **Works for all languages** – the algorithm is language‑agnostic. |
| **Clear mathematical reduction** – the “totalPos – T” trick makes the problem a lot easier to reason about. |

## 7. The Bad

| Problem | Real‑world impact |
|---------|--------------------|
| **Large input** – `n` can be `10⁵`. Sorting dominates; if you try a naïve approach you’ll get a TLE. |
| **Misreading k** – it’s the *k*‑th **largest** sum, but the heap works on the *k*‑th smallest transformed sum. A slip‑of‑hand can give the wrong answer. |
| **Edge‑case bugs** – forgetting the `k == 1` shortcut can cause you to pop from an empty heap. |

## 8. The Ugly

| Pain point | Fix |
|------------|-----|
| **Duplicate subsets** – naive subset enumeration would explode. The heap approach ensures we never generate the same subset twice because each state is identified by `(sum, idx)`. |
| **Integer overflow** – `totalPos` and partial sums can be up to `n * 10⁹`. Use 64‑bit (`long` in Java, `long long` in C++, `int` in Python is unbounded). |
| **Negative numbers** – converting to absolute values might feel counter‑intuitive; remember the “subtract from totalPos” trick! |

---

## 9. A Complete, Ready‑to‑Copy Code Snippet

### Java

```java
public class Solution {
    public long kthLargestSum(int[] nums, int k) {
        long totalPos = 0;
        int n = nums.length;
        long[] abs = new long[n];
        for (int i = 0; i < n; i++) {
            int v = nums[i];
            if (v > 0) totalPos += v;
            abs[i] = Math.abs(v);
        }

        if (k == 1) return totalPos;          // all positives is the largest sum

        Arrays.sort(abs);

        // Min‑heap (sum, index)
        PriorityQueue<long[]> pq = new PriorityQueue<>(Comparator.comparingLong(a -> a[0]));
        pq.offer(new long[]{abs[0], 0});

        long smallestT = 0;
        for (int i = 0; i < k - 1; i++) {
            long[] cur = pq.poll();
            smallestT = cur[0];
            int idx = (int) cur[1] + 1;
            if (idx < n) {
                pq.offer(new long[]{cur[0] + abs[idx], idx});   // include
                pq.offer(new long[]{cur[0], idx});              // exclude
            }
        }
        return totalPos - smallestT;
    }
}
```

### Python

```python
import heapq
from typing import List

def kth_largest_sum(nums: List[int], k: int) -> int:
    total_pos = sum(x for x in nums if x > 0)
    arr = sorted(abs(x) for x in nums)

    if k == 1:
        return total_pos

    pq = [(arr[0], 0)]           # (sum, next_index)
    smallest_t = 0

    for _ in range(k - 1):
        s, idx = heapq.heappop(pq)
        smallest_t = s
        idx += 1
        if idx < len(arr):
            heapq.heappush(pq, (s + arr[idx], idx))  # include arr[idx]
            heapq.heappush(pq, (s, idx))             # exclude arr[idx]

    return total_pos - smallest_t
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

long long kthLargestSum(vector<int> nums, int k) {
    long long totalPos = 0;
    vector<long long> abs;
    abs.reserve(nums.size());

    for (int x : nums) {
        if (x > 0) totalPos += x;
        abs.push_back(llabs(x));
    }
    sort(abs.begin(), abs.end());

    if (k == 1) return totalPos;

    priority_queue<Node> pq;
    pq.push({abs[0], 0});

    long long smallestT = 0;
    for (int i = 0; i < k - 1; ++i) {
        Node cur = pq.top(); pq.pop();
        smallestT = cur.sum;
        int idx = cur.idx + 1;
        if (idx < (int)abs.size()) {
            pq.push({cur.sum + abs[idx], idx});
            pq.push({cur.sum, idx});
        }
    }
    return totalPos - smallestT;
}
```

---

## 10. How to Prepare for Interview Questions on This Topic

1. **Understand the reduction** – be ready to explain why converting to `abs` and subtracting from `totalPos` is correct.  
2. **Explain the heap** – show that each subset corresponds to a unique path in the heap.  
3. **Discuss constraints** – point out the `k ≤ 2000` and why the algorithm remains fast.  
4. **Edge‑case checks** – demonstrate awareness of `k == 1` and empty heap scenarios.  
5. **Time/Space trade‑offs** – if interviewer asks, show that `O(n log n)` dominates, and why we don't need any additional DP arrays.

---

## 11. Final Takeaway

- Reduce the problem to `totalPos – T` to get a monotonic, heap‑friendly version.  
- Keep your data structures small (heap of size `≤ 2k`).  
- Sort the `abs` array once; the rest is linear in *k*.  

By following this recipe, you can confidently implement the solution in **Java, Python, or C++** – and ace that interview question about the **k‑th largest sum** in a huge array.  

Good luck, and happy coding! 🎉

---

**Keywords**: `LeetCode`, `kth largest sum`, `Leetcode 1572`, `subset sum`, `heap`, `Java`, `Python`, `C++`, `time complexity`, `memory optimization`, `interview prep`.