---
title: LeetCode 2080. Range Frequency Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Range Frequency Queries (LeetCode 2080) – Java, Python & C++ Solutions + a SEO‑Ready Blog Post

> **TL;DR**  
> • **Problem**: Find how many times a value appears in a sub‑array.  
> • **Core Idea**: Store the positions of every number in a *sorted* list → binary search for the range.  
> • **Complexities**: `O(n)` preprocessing, `O(log n)` per query, `O(n)` memory.  
> • **Why It’s Great for Interviews**: Simple data structure, clear reasoning, and you can talk about trade‑offs (hash‑map + binary search vs. segment tree vs. Mo’s algorithm).  

---

## 1. Problem Statement (LeetCode 2080)

> **Design** a class `RangeFreqQuery` that supports:
> - `RangeFreqQuery(int[] arr)` – constructor.
> - `int query(int left, int right, int value)` – return the frequency of `value` in the sub‑array `arr[left … right]`.

Constraints  
```
1 ≤ arr.length ≤ 1e5
1 ≤ arr[i], value ≤ 1e4
0 ≤ left ≤ right < arr.length
≤ 1e5 queries
```

---

## 2. Core Insight – *Arrays of Indices*

Since `arr[i]` ≤ `10^4`, we can pre‑allocate an array of size `10001`.  
For each possible value `v` we store the **sorted list of indices** where `v` occurs.

```
indices[v] = [i0, i1, i2, ...]   (strictly increasing)
```

*Why sorted?*  
Because we’ll perform binary search on these lists during queries.  
Sorting is inherent because we add indices in ascending order during construction.

**Query algorithm**

1. `l = lower_bound(indices[value], left)` – first index ≥ `left`.  
2. `r = upper_bound(indices[value], right)` – first index > `right`.  
3. Result = `r - l`.

Both steps are `O(log k)` where `k` is the number of occurrences of `value`.  
In the worst case `k = n`, but still `O(log n)`.

---

## 3. Implementation in Three Languages

Below are ready‑to‑compile snippets.  
Feel free to copy‑paste into your IDE or LeetCode playground.

---

### 3.1 Java

```java
import java.util.*;

public class RangeFreqQuery {
    // indices[v] holds all positions where arr[i] == v
    private final List<Integer>[] indices;

    @SuppressWarnings("unchecked")
    public RangeFreqQuery(int[] arr) {
        indices = new ArrayList[10001];           // 0 … 10000
        for (int v = 0; v < indices.length; v++) {
            indices[v] = new ArrayList<>();
        }
        for (int i = 0; i < arr.length; i++) {
            indices[arr[i]].add(i);
        }
    }

    public int query(int left, int right, int value) {
        List<Integer> list = indices[value];
        if (list.isEmpty()) return 0;

        int l = lowerBound(list, left);   // first >= left
        int r = upperBound(list, right);  // first > right
        return r - l;
    }

    // binary search helpers
    private static int lowerBound(List<Integer> list, int target) {
        int lo = 0, hi = list.size();
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (list.get(mid) < target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }

    private static int upperBound(List<Integer> list, int target) {
        int lo = 0, hi = list.size();
        while (lo < hi) {
            int mid = (lo + hi) >>> 1;
            if (list.get(mid) <= target) lo = mid + 1;
            else hi = mid;
        }
        return lo;
    }
}
```

**Key points**

* Uses `ArrayList` – no extra copying during construction.  
* `@SuppressWarnings("unchecked")` is safe because we know the size.  
* `lowerBound` / `upperBound` are the classic “C++ style” helpers.

---

### 3.2 Python

```python
import bisect
from typing import List

class RangeFreqQuery:
    def __init__(self, arr: List[int]):
        # indices[v] = sorted list of positions
        self.indices = [[] for _ in range(10001)]
        for i, v in enumerate(arr):
            self.indices[v].append(i)

    def query(self, left: int, right: int, value: int) -> int:
        lst = self.indices[value]
        if not lst:
            return 0
        l = bisect.bisect_left(lst, left)   # first >= left
        r = bisect.bisect_right(lst, right) # first > right
        return r - l
```

*Python’s `bisect`* is already a fast C implementation, so you don’t need to write your own binary search.

---

### 3.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class RangeFreqQuery {
private:
    vector<vector<int>> indices;   // indices[v] = list of positions

public:
    RangeFreqQuery(vector<int> &arr) {
        indices.assign(10001, {});
        for (int i = 0; i < arr.size(); ++i) {
            indices[arr[i]].push_back(i);     // indices are appended in order
        }
    }

    int query(int left, int right, int value) {
        const vector<int> &vec = indices[value];
        if (vec.empty()) return 0;

        auto l = lower_bound(vec.begin(), vec.end(), left);
        auto r = upper_bound(vec.begin(), vec.end(), right);
        return r - l;
    }
};
```

The C++ code uses STL’s `lower_bound` / `upper_bound` – identical logic as Java.

---

## 4. Complexity Analysis

| Step | Time | Memory |
|------|------|--------|
| **Preprocessing** | `O(n)` – each element is pushed once into its bucket. | `O(n)` – total length of all buckets equals `n`. |
| **Single query** | `O(log k)` ≤ `O(log n)` | `O(1)` per query (apart from the bucket look‑up). |
| **Worst‑case** | `O(log n)` per query | `O(n)` overall |

*Why `k ≤ n`?*  
If all elements are the same (`value` = 1), the corresponding bucket has size `n`.  
Binary search on `n` elements is still logarithmic.

---

## 5. Alternatives – When to Choose What

| Approach | Pros | Cons | Typical Use‑Case |
|----------|------|------|-----------------|
| **Array of indices + binary search** (our solution) | • O(1) lookup + O(log n) per query. <br>• Very easy to explain. <br>• No heavy data‑structure boilerplate. | • Memory proportional to `n` even if many values never appear. <br>• If `arr[i]` can be large ( > 1e5 ), we cannot pre‑allocate a bucket per value. | Interview questions where constraints allow small domain. |
| **Mo’s algorithm** (offline queries) | • O((n + q) √n) – good when queries are known beforehand. | • Harder to code; needs block decomposition. <br>• Not “online”. | “Batch” interview problems or “offline” interview rounds. |
| **Segment Tree / BIT of HashMaps** | • Log‑time query for *any* value, not just small domain. | • Space: O(n log n) due to map merging. <br>• Implementation complexity high. | Interviewers who explicitly ask for a *segment tree* solution. |
| **Wavelet Tree** | • Handles large `arr[i]` values. | • Non‑trivial to implement. | Advanced interview or “data‑structures” track. |

> **Good**: The indices‑bucket method is perfect for *online* queries and small domain.  
> **Bad**: It’s wasteful if the value range were huge (`1e9`).  
> **Ugly**: If you don’t handle empty buckets correctly, you’ll get wrong answers or `IndexOutOfBoundsException`.

---

## 6. “Good, Bad & Ugly” – A Developer‑Interview Lens

| Stage | Good | Bad | Ugly |
|-------|------|-----|------|
| **Concept** | Intuition of “searching in a list of positions” – very explainable. | None. | If you choose a *segment tree* or *Mo’s algorithm* without first explaining the simpler array of indices, interviewers will think you over‑engineered. |
| **Pre‑processing** | O(n) time; just a single pass. | None. | Forgetting to pre‑allocate the bucket array size can lead to `ArrayIndexOutOfBoundsException`. |
| **Query** | O(log n) per query; uses standard binary search. | None. | Relying on Java’s `Arrays.binarySearch` and mis‑interpreting its return value (negative indices) will crash your code. |
| **Memory** | O(n) total, well within limits. | None. | Storing the whole array in a `HashMap<Integer, Integer>` for every value (size 1e4) will blow memory – **avoid** that. |
| **Testing** | Easy to write unit tests – just brute‑force a random array. | None. | Failing to handle the case where the value does not exist (empty bucket) → `0` vs `r-l` negative bug. |

---

## 7. FAQ – Common Pitfalls & Gotchas

| Question | Answer |
|----------|--------|
| **What if `value` is 0?** | Problem constraints say `arr[i]` ≥ 1, but you can still support 0 by allocating `size 10001`. Just be careful that your bucket array is big enough. |
| **Will `upperBound` ever return `-1`?** | No, `upperBound` returns the *index* of the first element greater than the target. If all elements are ≤ `right`, it returns `list.size()`. |
| **Why not use `Collections.binarySearch`?** | It returns the *index of the exact element* or `-(insertionPoint) - 1`. Converting that into `lowerBound` / `upperBound` is more error‑prone than writing the two small helpers. |
| **How do I adapt if `arr[i]` can be 10^9?** | Use a `HashMap<Integer, List<Integer>>` instead of an array of lists; the approach remains the same. |

---

## 8. Interview‑Ready Tips

1. **Start with the simplest solution** – explain the “arrays of indices” idea; then ask if the interviewer would like a more sophisticated approach (segment tree, Mo’s algorithm, etc.).  
2. **Talk about trade‑offs** – mention that the bucket approach is linear space but fast, whereas a segment tree uses more memory but works for any `arr[i]` value.  
3. **Show your coding style** – use proper naming (`indices`, `lowerBound`, `upperBound`) and comment your code.  
4. **Mention edge cases** – empty bucket, value not present, left > right (should not happen per constraints but still check).  
5. **Provide unit tests** – write a small main function or `@Test` method that asserts known outputs.  
6. **Show familiarity with C++ STL and Python bisect** – many interviewers love to see you know language‑specific idioms.  

---

## 9. Wrap‑Up & Final Thoughts

* The “arrays of indices” solution is **the canonical** answer for LeetCode 2080.  
* It runs in `O(n)` preprocessing time, `O(log n)` per query, and `O(n)` memory – perfect for the given constraints.  
* If you’re preparing for a **coding interview** or a **data‑structures job**, you can confidently talk about this solution, its strengths, and why you’d pick it over a segment tree unless the interview explicitly demands it.

Happy coding, and may your next interview be as smooth as a binary search on a sorted list! 🎉

---  

*Keywords for SEO*: **Range Frequency Queries LeetCode 2080, RangeFreqQuery implementation, Java Python C++ solution, interview data structure, job interview coding, binary search, arrays of indices, segment tree alternative**.