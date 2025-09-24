---
title: LeetCode 2519. Count the Number of K-Big Indices - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 2519 – “Count the Number of K‑Big Indices”

> **Why read this?**  
>  • Get a clear, production‑ready solution in **Java, Python & C++**.  
>  • Understand the *good* (intuition), *bad* (common pitfalls) and *ugly* (edge‑case quirks).  
>  • Learn two powerful techniques: *two‑priority‑queues* (O(n log k)) and a *Fenwick tree* (O(n log M)).  
>  • Polish your interview style: you’ll be ready to explain, code and optimize on the spot.  

> **SEO‑keywords** – *LeetCode 2519, k‑big indices, interview coding question, Java priority queue, Python heap, C++ priority_queue, Fenwick tree, algorithmic interview, data structures, big O notation, coding interview solutions.*

---

## 1. Problem Statement (Paraphrased)

> **Input**  
> `nums` – 0‑indexed integer array (`1 ≤ len(nums) ≤ 10⁵`)  
> `k` – positive integer (`1 ≤ k ≤ len(nums)`)  

> **Definition**  
> Index `i` is **k‑big** if  
> * there are at least `k` distinct indices `idx1 < i` with `nums[idx1] < nums[i]`;  
> * there are at least `k` distinct indices `idx2 > i` with `nums[idx2] < nums[i]`.  

> **Task**  
> Return the number of k‑big indices.

> **Example**  
> ```text
> nums = [2,3,6,5,2,3], k = 2 → 2
> ```

---

## 2. Intuition & Key Observations

| Observation | Why it matters |
|-------------|----------------|
| **Left side** – We need to know *how many smaller numbers exist to the left*. If we know the *k‑th smallest* on the left, we can decide immediately. |
| **Right side** – Symmetric: we need the *k‑th smallest* on the right. |
| **Monotonic structure** – For every index we just need *the size of the “smallest k” set*; we don’t care about their exact values. |
| **Two‑pass linear scan** – Compute the left‑side information first, then sweep from right to left to verify the right side and the left side simultaneously. |
| **Time vs Space trade‑off** – `O(n log k)` with priority queues is simpler and fast enough; Fenwick tree gives `O(n log M)` (M = max value) and uses only arrays. |

---

## 3. The “Good” – Two‑Priority‑Queues Approach (O(n log k))

### 3.1 Algorithm Steps

1. **Left Pass**  
   *Maintain a max‑heap of size `k`* (i.e., priority queue in descending order).  
   For each `i` from left to right:  
   * If the heap size is `k` **and** the largest element in the heap (`top`) is `< nums[i]`, then we know there are at least `k` smaller elements to the left → mark `leftGood[i] = true`.  
   * Push `nums[i]` into the heap. If heap size exceeds `k`, pop the maximum (which is the largest among the smallest `k`).  

2. **Right Pass** – Do the same from right to left:  
   * Use a fresh max‑heap of size `k`.  
   * When the heap has `k` elements and its `top` < `nums[i]`, then *right side* is good.  
   * Combine with the previously stored `leftGood[i]`; if both true → increment answer.  
   * Push `nums[i]` and pop if size > `k`.  

3. **Return** the accumulated answer.

### 3.2 Why it works

*The heap always contains the `k` smallest values seen so far.*  
If the heap’s maximum (`top`) is `< nums[i]`, that means **every** element in the heap is `< nums[i]`, so we have at least `k` such elements.  
Otherwise, we don't yet have `k` smaller elements.

### 3.3 Complexity

| Operation | Time | Space |
|-----------|------|-------|
| Each push/pop is `O(log k)` | `O(n log k)` | `O(k)` for heap + `O(n)` for `leftGood` array |
| Total | **`O(n log k)`** | **`O(n + k)`** (dominant `O(n)` due to boolean array) |

Because `k ≤ n ≤ 10⁵`, this easily satisfies the constraints.

---

## 4. The “Bad” – Common Mistakes & Edge Cases

| Mistake | Symptom | Fix |
|---------|---------|-----|
| **Using a min‑heap** | Condition should be `heap.size() == k && heap.peek() < nums[i]` but `peek()` will be the *smallest* → wrong. | Use a **max‑heap** (reverse order). |
| **Pushing after the check** | If you push first, you might include the current element in the count. | Check **before** inserting. |
| **Off‑by‑one when removing from right side** | For index `i`, we must not consider `nums[i]` itself in the right side. | Start right pass with an *empty* heap and *first* push current value after the check. |
| **Ignoring duplicate values** | Condition requires **strictly smaller** numbers. | Use `<` not `<=`. |
| **Not clearing the heap between passes** | Left pass heap may still hold elements in the right pass. | Re‑initialize the heap. |

---

## 5. The “Ugly” – Fenwick Tree (Binary Indexed Tree) Alternative

If you prefer *array‑based* solutions, a Fenwick tree that counts occurrences of values works well.  
Key idea: maintain two frequency Fenwick trees – one for the left side (`leftTree`) and one for the right side (`rightTree`).  
During the scan:

1. Initially, all elements belong to `rightTree`.  
2. For each index `i` (starting from `k` to `n-k-1` to guarantee enough elements on both sides):  
   * Remove `nums[i]` from `rightTree`.  
   * Query `leftTree.prefixSum(nums[i]-1)` and `rightTree.prefixSum(nums[i]-1)` to know how many *strictly smaller* numbers exist on each side.  
   * If both counts ≥ `k`, increment answer.  
   * Add `nums[i]` to `leftTree`.  

Fenwick tree queries/updates are `O(log M)` where `M` is the maximum value in `nums` (≤ 10⁵ after compression).  
Space is `O(M)` and no heap is required.

> **When to use?**  
> *You need to handle very large `k` close to `n`* or *you want a deterministic log‑factor independent of `k`*.  
> *You’re comfortable with 1‑based indexing and prefix sums.*

---

## 6. Sample Code – All Three Languages

> Each snippet compiles on its own LeetCode submission.  
> The class names and method signatures match the official problem template.

---

### 6.1 Java

```java
import java.util.PriorityQueue;
import java.util.Collections;

class Solution {
    public int kBigIndices(int[] nums, int k) {
        int n = nums.length;
        boolean[] leftGood = new boolean[n];

        /* ---------- Left pass ---------- */
        PriorityQueue<Integer> leftHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int i = 0; i < n; i++) {
            if (leftHeap.size() == k && leftHeap.peek() < nums[i]) {
                leftGood[i] = true;
            }
            leftHeap.offer(nums[i]);
            if (leftHeap.size() > k) leftHeap.poll();
        }

        /* ---------- Right pass ---------- */
        int answer = 0;
        PriorityQueue<Integer> rightHeap = new PriorityQueue<>(Collections.reverseOrder());
        for (int i = n - 1; i >= 0; i--) {
            if (rightHeap.size() == k && rightHeap.peek() < nums[i]) {
                if (leftGood[i]) answer++;
            }
            rightHeap.offer(nums[i]);
            if (rightHeap.size() > k) rightHeap.poll();
        }

        return answer;
    }
}
```

---

### 6.2 Python

```python
import heapq
from typing import List

class Solution:
    def kBigIndices(self, nums: List[int], k: int) -> int:
        n = len(nums)
        left_good = [False] * n

        # ----- Left to right -----
        left_heap = []          # Python's heapq is a min‑heap → store negatives to get a max‑heap
        for i, val in enumerate(nums):
            if len(left_heap) == k and -left_heap[0] < val:
                left_good[i] = True
            heapq.heappush(left_heap, -val)
            if len(left_heap) > k:
                heapq.heappop(left_heap)

        # ----- Right to left -----
        right_heap = []
        ans = 0
        for i in range(n - 1, -1, -1):
            if len(right_heap) == k and -right_heap[0] < nums[i]:
                if left_good[i]:
                    ans += 1
            heapq.heappush(right_heap, -nums[i])
            if len(right_heap) > k:
                heapq.heappop(right_heap)

        return ans
```

---

### 6.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int kBigIndices(vector<int>& nums, int k) {
        int n = nums.size();
        vector<char> leftGood(n, 0);

        // ----- Left pass -----
        priority_queue<int> leftHeap;   // max‑heap by default
        for (int i = 0; i < n; ++i) {
            if (leftHeap.size() == k && leftHeap.top() < nums[i]) {
                leftGood[i] = 1;
            }
            leftHeap.push(nums[i]);
            if (leftHeap.size() > k) leftHeap.pop();
        }

        // ----- Right pass -----
        priority_queue<int> rightHeap;
        int ans = 0;
        for (int i = n - 1; i >= 0; --i) {
            if (rightHeap.size() == k && rightHeap.top() < nums[i]) {
                if (leftGood[i]) ++ans;
            }
            rightHeap.push(nums[i]);
            if (rightHeap.size() > k) rightHeap.pop();
        }

        return ans;
    }
};
```

> **Note**: All three snippets run in **O(n log k)** time and use only a boolean array + two heaps.

---

## 6. 6‑Step “Interview‑Ready” Checklist

| Step | What to say / do |
|------|------------------|
| 1. Clarify “strictly smaller” vs “≤” | “Make sure we’re counting strictly smaller numbers.” |
| 2. Choose data‑structure | “I’ll use two max‑heaps because we only need the k smallest on each side.” |
| 3. Write left pass | “First check, then insert; that way the current element isn’t counted.” |
| 4. Right pass | “Same logic, but we need to avoid counting the current element on the right side.” |
| 5. Verify combined condition | “Both left and right must be true → we’ve found a k‑big index.” |
| 6. Complexity | “O(n log k) time, O(n) space – easily passes 10⁵ constraints.” |
| 7. Edge cases | “Duplicate values, off‑by‑one, clearing the heap, using max‑heap.” |

---

## 7. Testing Tips

| Test | Expected Result | Why |
|------|-----------------|-----|
| `nums = [1,2,3,4,5], k = 1` | 4 | Every index except the first has at least one smaller on the left & right. |
| `nums = [5,4,3,2,1], k = 2` | 0 | No index has *smaller* values on both sides. |
| `nums = [1,1,1,1], k = 1` | 0 | No strictly smaller values. |
| `nums = [10⁵, 1, 1, …, 1], k = 1` | 1 | The first element is k‑big because there are many smaller ones to the right. |
| Random large array + random `k` | Passes 2‑second limit | Use `random.randint(1, 10⁵)` and verify with brute‑force for small `n`. |

---

## 8. Final Thoughts

* **Two‑priority‑queues**: fastest to code, elegant, and fully satisfies the problem constraints.  
* **Fenwick tree**: great if you need to avoid heap overhead or if you’re already familiar with BITs.  
* Always keep *strictly smaller* and *off‑by‑one* nuances in mind – they’re the usual interview gremlins.  

Feel confident: you can now tackle LeetCode 2519 in **Java, Python, and C++** while explaining the *why* and *how* to a hiring manager.

---  

**Happy coding & good luck on your next interview!**