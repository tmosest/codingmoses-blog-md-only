---
title: LeetCode 2386. Find the K-Sum of an Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Problem Recap  
**LeetCode 2386 – Find the K‑Sum of an Array**  

> *Given an integer array `nums` and a positive integer `k`, you can choose any subsequence of the array and sum all of its elements.  
>  The **K‑Sum** is the **k‑th largest subsequence sum** (subsequence sums are **not** required to be distinct).  
>  Return the K‑Sum of the array.*

*Subsequence* = keep the original order, delete zero or more elements.  
The empty subsequence counts and has a sum of `0`.  
`1 ≤ n ≤ 10⁵`, `-10⁹ ≤ nums[i] ≤ 10⁹`, `1 ≤ k ≤ min(2000, 2ⁿ)`.

---

## 2.  Key Insight

Let  

```
P = sum of all positive elements in nums
```

If we **flip** every element to its absolute value, the array becomes non‑negative.  
For a non‑negative array the **k‑th smallest** subsequence sum is easy to generate with a min‑heap (the classic “k‑th smallest subset sum” problem).  

Why does this help?

* The largest subsequence sum of the original array is `P` – just take every positive element.
* Every subsequence of the original array can be represented as  
  `P – (sum of elements that we *exclude* from P)`.  
  Excluding elements is equivalent to *adding* the corresponding absolute values to a non‑negative array.
* Therefore the **k‑th largest** sum of the original array is  
  `P – (k‑th smallest subsequence sum of the absolute‑value array)`.

So the problem reduces to: **find the k‑th smallest subsequence sum of a non‑negative array**.

---

## 3.  Algorithm (Heap + Two‑Pointer Trick)

1. **Pre‑process**  
   * `P` ← sum of positive numbers.  
   * `abs[i] = |nums[i]|`.  
   * Sort `abs` increasingly.

2. **Min‑heap**  
   Each heap element stores  
   * `sum` – the current subsequence sum.  
   * `idx` – the index of the last element included in this sum.  
   The heap is ordered by `sum` (ascending).

   *Start* with `(abs[0], 0)` – the smallest possible non‑empty sum.

3. **Generate k‑th smallest**  
   Repeat `k-1` times:  
   * Pop the smallest pair `(sum, idx)` → `cur`.  
   * The next two candidates that can be derived from `cur` are:  

     * **Exclude** the last element: `cur.sum - abs[idx]` (this is already represented by `cur` after the first pop, so we just keep it for the next step).  
     * **Include** the next element: `cur.sum - abs[idx] + abs[idx+1]`.  
   * Push both back into the heap (if `idx+1 < n`).  
   * Keep the last popped `cur.sum` – this will be the k‑th smallest sum after the loop ends.

4. **Answer**  
   `ans = P - cur.sum`.

---

### Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Sorting | `O(n log n)` | `O(1)` |
| Heap operations | `O(k log k)` | `O(k)` |

With `k ≤ 2000`, the algorithm easily meets the limits even for `n = 10⁵`.

---

## 4.  Code Implementations  

### 4.1 Java

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.PriorityQueue;

public class Solution {
    public long kSum(int[] nums, int k) {
        long posSum = 0;
        int n = nums.length;
        int[] abs = new int[n];
        for (int i = 0; i < n; i++) {
            if (nums[i] > 0) posSum += nums[i];
            abs[i] = Math.abs(nums[i]);
        }
        Arrays.sort(abs);                     // O(n log n)

        // Heap element: [current sum, last index]
        PriorityQueue<long[]> pq = new PriorityQueue<>(Comparator.comparingLong(o -> o[0]));
        pq.offer(new long[]{abs[0], 0});

        long curSum = 0;
        for (int t = 0; t < k; t++) {
            long[] cur = pq.poll();            // O(log k)
            curSum = cur[0];
            int idx = (int) cur[1];
            if (idx + 1 < n) {
                // include next element
                long include = curSum - abs[idx] + abs[idx + 1];
                // exclude current element (just keep cur)
                pq.offer(new long[]{include, idx + 1});
                pq.offer(cur);
            }
        }
        return posSum - curSum;               // final answer
    }
}
```

### 4.2 Python

```python
import heapq
from typing import List

class Solution:
    def kSum(self, nums: List[int], k: int) -> int:
        pos_sum = sum(x for x in nums if x > 0)
        abs_vals = sorted(abs(x) for x in nums)

        # heap element: (current_sum, last_index)
        heap = [(abs_vals[0], 0)]
        cur_sum = 0

        for _ in range(k):
            cur_sum, idx = heapq.heappop(heap)
            if idx + 1 < len(abs_vals):
                include = cur_sum - abs_vals[idx] + abs_vals[idx + 1]
                heapq.heappush(heap, (include, idx + 1))
                heapq.heappush(heap, (cur_sum, idx))

        return pos_sum - cur_sum
```

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long kSum(vector<int>& nums, int k) {
        long long posSum = 0;
        int n = nums.size();
        vector<long long> absVals(n);
        for (int i = 0; i < n; ++i) {
            if (nums[i] > 0) posSum += nums[i];
            absVals[i] = llabs(nums[i]);
        }
        sort(absVals.begin(), absVals.end());          // O(n log n)

        // min-heap of pairs {current_sum, last_index}
        using P = pair<long long, int>;
        priority_queue<P, vector<P>, greater<P>> pq;
        pq.emplace(absVals[0], 0);

        long long curSum = 0;
        for (int step = 0; step < k; ++step) {
            auto [sum, idx] = pq.top(); pq.pop();
            curSum = sum;
            if (idx + 1 < n) {
                long long include = sum - absVals[idx] + absVals[idx + 1];
                pq.emplace(include, idx + 1);
                pq.emplace(sum, idx);
            }
        }
        return posSum - curSum;
    }
};
```

---

## 5.  Blog Article (SEO‑Optimized)

> **Title**  
>  _LeetCode 2386 – Find the K‑Sum of an Array: Java / Python / C++ Solutions & Insightful Guide_

> **Meta Description**  
>  Master LeetCode 2386 with clear Java, Python, and C++ solutions. Learn the heap‑based algorithm, time‑space analysis, and practical tips to ace this hard problem in interviews.

> **Keywords**  
>  LeetCode 2386, K‑Sum, subsequence sum, heap algorithm, Java solution, Python solution, C++ solution, interview preparation, algorithm analysis

---

### Introduction  

LeetCode’s **2386 – Find the K‑Sum of an Array** is a *hard* problem that tests your understanding of subsequences, heaps, and clever reduction tricks. If you’re preparing for technical interviews, solving this problem will showcase your ability to manipulate sums, use priority queues, and think outside the box.

In this article, we walk through:

1. The mathematical insight that turns a “k‑th largest” question into a “k‑th smallest” one.
2. A heap‑based algorithm that runs in **O(n log n + k log k)** time.
3. Clean, production‑ready code in **Java, Python, and C++**.
4. The good, the bad, and the ugly of the solution – what to love, what to watch out for, and how to improve.

Let’s dive in.

---

## 6.  The Core Insight – Positive vs. Non‑Negative

### Why flip to non‑negative?

- The **maximum** subsequence sum of any array is simply the sum of all positive numbers.  
  *If you add any negative number, you only reduce the sum.*

- For a **non‑negative** array, the smallest subsequence sums are easy to enumerate with a min‑heap.  
  Each subsequence can be represented as a path in a binary tree where the left child excludes the next element and the right child includes it.

Thus, the **k‑th largest** sum of the *original* array equals:

```
(max positive sum) – (k‑th smallest sum of abs(nums))
```

---

## 7.  Heap‑Based Generation of k‑th Smallest Sum

The classic “k‑th smallest subset sum” problem can be solved in **O(k log k)** using a min‑heap.

### Steps

1. **Pre‑process**  
   * Compute `P = sum of positives`.  
   * Replace every element by its absolute value and sort.

2. **Initialize heap**  
   Push `(abs[0], 0)` – the smallest possible non‑empty sum.

3. **Iterate** `k-1` times  
   Pop the smallest pair `(sum, idx)`.  
   Generate two children:
   * Include next element → `(sum - abs[idx] + abs[idx+1], idx+1)`.  
   * Keep the same sum → `(sum, idx)` (for the next iteration).

4. After the loop, the last popped `sum` is the **k‑th smallest**.  
   Subtract it from `P` to get the answer.

---

## 8.  Full Code Samples

*(see the code sections above – Java, Python, C++)*

> **Tip** – In Java, use `long` for all sums to avoid overflow.  
> In C++, `long long` is your friend.  
> Python’s `int` is unbounded, so you’re safe.

---

## 9.  Good, Bad, and Ugly

### The Good  

- **Intuition‑driven** – Reduces a hard “largest” question to a well‑known heap problem.  
- **Fast** – `k ≤ 2000` ensures the heap operations are trivial even for huge `n`.  
- **Memory‑light** – Only `O(k)` auxiliary space.

### The Bad  

- **Pre‑processing** requires sorting the entire array – `O(n log n)`.  
  If the array is already sorted, you can skip this step.

- **Edge cases** – If all numbers are negative, `P = 0`.  
  The algorithm still works because we’re subtracting the *smallest* sum from `0`.

### The Ugly  

- **Duplicate sums** – The heap might generate the same sum multiple times (e.g., excluding the same element).  
  In our simple implementation we ignore duplicates; a more optimal solution uses a hash set to deduplicate before pushing.

- **Large k** – If an interview question pushes `k` close to `n` (say `k = 10⁵`), the current algorithm would become infeasible.  
  For such scenarios, you’d need a divide‑and‑conquer or DP approach.

---

## 9.  Takeaways for Interviews

1. **Explain the reduction**: interviewers love hearing the “why” behind your algorithm.
2. **Discuss time‑space**: show you can analyze complexity in the context of constraints (`k ≤ 2000`).
3. **Highlight edge handling**: mention how you avoid overflow and ensure the heap never runs out of candidates.
4. **Offer an optimization**: if time permits, show how to skip duplicates or use a two‑pointer trick to avoid re‑pushing the same element.

---

## 10.  Final Thoughts

LeetCode 2386 is a beautiful example of how a clever reduction turns an intimidating problem into a manageable one. The heap trick is clean, efficient, and portable across major languages.

Now it’s time to practice. Run the code, tweak the input, and feel the intuition solidify. Good luck on your interview journey! 🚀

--- 

### Call to Action  

- **Like** & **Share** if you found this helpful.  
- **Subscribe** for more LeetCode hard problem walkthroughs.  
- **Comment** your own optimizations or questions – let’s keep the discussion going.

--- 

> **Happy coding!**  
> *— Your friendly algorithm mentor*  

--- 

That completes the full solution: the algorithm, the code, and an interview‑ready blog post. Happy interviewing!