---
title: LeetCode 2615. Sum of Distances - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Sum of Distances in an Array – A Complete Interview Guide  
*(LeetCode 2615 – Medium)*  

> **TL;DR** –  
> • **Problem**: For every index `i`, compute the sum of absolute differences to all *other* indices that hold the same value.  
> • **Naïve**: O(n²) time, O(1) space.  
> • **Optimal**: O(n) time, O(1) extra space (besides the result array).  
> • **Languages**: Java, Python, C++.  
> • **Why it matters**: This pattern of *prefix + suffix* sums shows up all over, and mastering it demonstrates strong algorithmic thinking – a must‑have for any software engineering interview.  

---

## Table of Contents  

| Section | Summary |
|---------|---------|
| 1. Problem Statement | Formal description and examples |
| 2. Brute‑Force Approach | O(n²) solution and why it fails |
| 3. Optimal O(n) Strategy | Prefix‑sum trick, two passes |
| 4. Complexity Analysis | Time & space |
| 5. Edge‑Case “Ugly” Pitfalls | Overflow, single element, large input |
| 6. Code Implementations | Java, Python, C++ |
| 7. Good, Bad, Ugly | Quick recap |
| 8. Interview Tips | How to talk about this problem |
| 9. SEO & Career Takeaway | Keywords & why this helps your job hunt |

---

## 1. Problem Statement

> You are given a zero‑based integer array `nums`.  
> For every `i`, let `arr[i]` be the sum of `|i‑j|` over all `j` such that `nums[j] == nums[i]` and `j ≠ i`.  
> If no such `j` exists, `arr[i] = 0`.  
> Return the array `arr`.

**Constraints**

* `1 ≤ nums.length ≤ 10⁵`
* `0 ≤ nums[i] ≤ 10⁹`

---

## 2. Brute‑Force O(n²) Approach

```text
for i in 0..n-1:
    sum = 0
    for j in 0..n-1:
        if i != j and nums[i] == nums[j]:
            sum += abs(i - j)
    arr[i] = sum
```

* **Time**: O(n²) – 10⁵² operations is impossible.  
* **Space**: O(1) – only the result array.

> **Why it fails**: With `n = 10⁵`, you would perform ~10¹⁰ distance calculations, far exceeding time limits.

---

## 3. Optimal O(n) Strategy – Prefix + Suffix

The key observation:  
For indices that share the same value, the sum of distances can be built incrementally.

### 3.1  Prefix Pass (Left → Right)

Maintain a hash map `cnt` (count of seen indices for each value) and `sumIdx` (sum of indices seen so far).

For each `i`:

```
arr[i] += i * cnt[v] - sumIdx[v]
cnt[v]  += 1
sumIdx[v] += i
```

Explanation:  
`cnt[v]` counts how many *previous* indices with value `v` exist.  
`sumIdx[v]` is the total of those indices.  
All distances from current `i` to those previous indices equal `i - prevIndex`, summed as `i * cnt - sumIdx`.

### 3.2  Suffix Pass (Right → Left)

Repeat the same logic from right to left, adding the distances to *future* indices:

```
arr[i] += sumIdx[v] - i * cnt[v]
cnt[v]  += 1
sumIdx[v] += i
```

Now `arr[i]` contains contributions from both sides – exactly what we need.

### 3.3  Complexity

* **Time**: 2 × O(n) ≈ O(n)
* **Space**: Two hash maps → O(k) where k = number of distinct values ≤ n

This is optimal and passes all LeetCode test cases.

---

## 4. Complexity Analysis

| Operation | Time | Extra Space |
|-----------|------|-------------|
| First pass | O(n) | O(k) |
| Second pass | O(n) | O(k) |
| Total | **O(n)** | **O(k)** (hash maps) |

Because `k ≤ n`, worst‑case space is still linear.

---

## 5. Edge‑Case “Ugly” Pitfalls

| Pitfall | Why it matters | Fix |
|---------|----------------|-----|
| **Integer overflow** | Distances can sum to `≈ n²/2` (≈5 × 10⁹ for n=10⁵). | Use 64‑bit integers (`long` in Java/C++, `int64` in Python) for the result and intermediate sums. |
| **Single element array** | `arr[0]` should be 0. Code handles this automatically. |
| **All elements equal** | Maximum sum; ensure your language handles large `long` values. |
| **Large `nums[i]` values** | They are keys in a hash map; no special handling required, but they can be up to 10⁹. |

---

## 6. Code Implementations

Below are concise, production‑ready solutions in Java, Python, and C++. All three follow the two‑pass strategy described above.

### 6.1 Java (LeetCode Signature)

```java
import java.util.HashMap;

class Solution {
    public long[] distance(int[] nums) {
        int n = nums.length;
        long[] arr = new long[n];

        HashMap<Integer, Long> sumIdx = new HashMap<>();
        HashMap<Integer, Integer> count = new HashMap<>();

        // Left → Right
        for (int i = 0; i < n; ++i) {
            int v = nums[i];
            long prevSum = sumIdx.getOrDefault(v, 0L);
            int prevCnt = count.getOrDefault(v, 0);
            arr[i] += (long) i * prevCnt - prevSum;
            sumIdx.put(v, prevSum + i);
            count.put(v, prevCnt + 1);
        }

        // Clear maps for right pass
        sumIdx.clear();
        count.clear();

        // Right → Left
        for (int i = n - 1; i >= 0; --i) {
            int v = nums[i];
            long nextSum = sumIdx.getOrDefault(v, 0L);
            int nextCnt = count.getOrDefault(v, 0);
            arr[i] += nextSum - (long) i * nextCnt;
            sumIdx.put(v, nextSum + i);
            count.put(v, nextCnt + 1);
        }

        return arr;
    }
}
```

### 6.2 Python 3

```python
def distance(nums):
    n = len(nums)
    arr = [0] * n

    sum_idx = {}
    cnt = {}

    # Left -> Right
    for i, v in enumerate(nums):
        prev_sum = sum_idx.get(v, 0)
        prev_cnt = cnt.get(v, 0)
        arr[i] += i * prev_cnt - prev_sum
        sum_idx[v] = prev_sum + i
        cnt[v] = prev_cnt + 1

    # Reset for right pass
    sum_idx.clear()
    cnt.clear()

    # Right -> Left
    for i in range(n - 1, -1, -1):
        v = nums[i]
        next_sum = sum_idx.get(v, 0)
        next_cnt = cnt.get(v, 0)
        arr[i] += next_sum - i * next_cnt
        sum_idx[v] = next_sum + i
        cnt[v] = next_cnt + 1

    return arr
```

### 6.3 C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<long long> distance(vector<int>& nums) {
        int n = nums.size();
        vector<long long> arr(n, 0);
        unordered_map<int, long long> sumIdx;
        unordered_map<int, int> cnt;

        // Left -> Right
        for (int i = 0; i < n; ++i) {
            int v = nums[i];
            long long prevSum = sumIdx[v];
            int prevCnt   = cnt[v];
            arr[i] += 1LL * i * prevCnt - prevSum;
            sumIdx[v] = prevSum + i;
            cnt[v] = prevCnt + 1;
        }

        sumIdx.clear();
        cnt.clear();

        // Right -> Left
        for (int i = n - 1; i >= 0; --i) {
            int v = nums[i];
            long long nextSum = sumIdx[v];
            int nextCnt   = cnt[v];
            arr[i] += nextSum - 1LL * i * nextCnt;
            sumIdx[v] = nextSum + i;
            cnt[v] = nextCnt + 1;
        }
        return arr;
    }
};
```

All three codes run in linear time and handle the maximum possible sums safely.

---

## 7. Good, Bad, Ugly – Quick Recap

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time** | O(n) optimal | O(n²) brute‑force fails | None |
| **Space** | O(k) hash maps (k ≤ n) | O(1) but slower | Overflow if `int` used |
| **Readability** | Two clear passes, inline comments | Single nested loop hard to read | Handling negative indices (not needed here) |
| **Edge Cases** | Works for all inputs | None | Large `nums[i]` values → hash‑map key size |
| **Interview** | Shows clever use of prefix sums | Show how to avoid TLE | Discuss overflow and data types |

---

## 8. Interview Tips – How to Talk About This Problem

1. **Start with the naive idea** – quick O(n²) approach. Show you understand the problem constraints.
2. **Explain the bottleneck** – `n = 10⁵` → ~10¹⁰ operations → impossible in time.
3. **Introduce the prefix‑sum trick** – count & sum of indices per value.  
   *Show a small example on a whiteboard.*  
4. **Two passes** – left → right and right → left, each O(n).  
   *Mention that you add contributions from both sides.*  
5. **Discuss data‑type safety** – use 64‑bit integers to avoid overflow.  
6. **Corner cases** – single element, all equal, distinct values.  
7. **Proof of correctness** – why the sum of distances equals the combination of both passes.  
8. **Complexity analysis** – O(n) time, O(k) space.  
9. **Optional extension** – if asked, discuss how to handle negative indices or a streaming version.

---

## 9. SEO & Career Takeaway

| Keyword | Why it helps |
|---------|--------------|
| `prefix sum` | Shows familiarity with a classic algorithmic trick. |
| `hash map` | Indicates you know to use dictionaries for frequency & cumulative values. |
| `O(n) algorithm` | Highlights ability to design linear‑time solutions. |
| `LeetCode` | Popular platform used by recruiters. |
| `job interview` | Demonstrates problem‑solving skills that hiring managers value. |

**Why this problem helps your job hunt**

* **Technical depth** – You can discuss data‑structures (hash maps), complexity, and data‑type safety.  
* **Clarity of communication** – A two‑pass solution is straightforward to explain, an asset in coding interviews.  
* **Stand‑out topic** – Many candidates skip this problem; mastering it gives you a unique talking point.  

Add this solution to your portfolio on GitHub (with the comment “LeetCode #<problem‑id>”) and share it in your LinkedIn posts or in a “Technical‑Interview‑Practice” article. Recruiters looking for “problem‑solvers” will pick up the relevant keywords and your demonstrated expertise.

---

## 🚀 Final Words

*Mastering “Sum of Absolute Distances for Equal Elements” shows you can turn a seemingly expensive calculation into a linear‑time algorithm with a neat two‑pass hash‑map strategy.*  

> **Next step**: Practice on a whiteboard, explain your solution in plain English, and share the snippet on GitHub. Good luck—your interview will thank you! 🚀

---

*Feel free to copy‑paste any of the code snippets into your coding environment or include them in your portfolio.*