---
title: LeetCode 373. Find K Pairs with Smallest Sums - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# Find K Pairs with Smallest Sums – 373 – LeetCode  
> **Your one‑stop guide to mastering this interview problem in Java, Python, and C++**  

> **Meta‑Description** – Learn how to solve LeetCode 373 “Find K Pairs with Smallest Sums” using a priority‑queue (min‑heap) approach. Get ready for your next coding interview with clear, multi‑language solutions, edge‑case coverage, and a blog‑ready “good‑bad‑ugly” analysis.  

---

## 1. Why This Problem Matters

- **High‑frequency interview topic** – almost every senior backend interview asks for the *k smallest pairs* or a similar *k‑closest* variation.  
- **Demonstrates data‑structure mastery** – priority queues, hash sets, and two‑pointer tricks.  
- **Showcases time‑space trade‑offs** – you’ll need to explain why a min‑heap beats a naïve `O(m*n)` solution.  

If you can nail this problem in **Java**, **Python**, and **C++**, you’ll have a solid talking point for technical interviews and coding bootcamps.

---

## 2. Problem Statement (LeetCode 373)

You are given two **sorted** integer arrays `nums1` and `nums2`, and an integer `k`.

A *pair* is defined as `(u, v)` where `u` comes from `nums1` and `v` comes from `nums2`.

Return the **k pairs** with the smallest sums.

> **Examples**  
> ```text
> nums1 = [1, 7, 11]
> nums2 = [2, 4, 6]
> k = 3
> Output: [[1, 2], [1, 4], [1, 6]]
> ```
> ```text
> nums1 = [1, 1, 2]
> nums2 = [1, 2, 3]
> k = 2
> Output: [[1, 1], [1, 1]]
> ```

> **Constraints**  
> - 1 ≤ nums1.length, nums2.length ≤ 10⁵  
> - –10⁹ ≤ nums1[i], nums2[i] ≤ 10⁹  
> - 1 ≤ k ≤ 10⁴  
> - `k` ≤ `nums1.length` × `nums2.length`

---

## 3. The “Good – Bad – Ugly” Breakdown

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Data Structure** | Min‑heap gives `O(log k)` extraction. | Naïve nested loops = `O(m*n)` time, impossible for 10⁵ size arrays. | Forgetting to dedupe pairs – duplicates can lead to incorrect results. |
| **Space** | `O(k)` heap + `O(k)` visited set. | Storing the entire Cartesian product is wasteful. | Using a hash set of `Pair` objects in Java can cause large overhead if not implemented correctly. |
| **Edge Cases** | Handles empty arrays, `k` > product size, duplicate values. | Hard‑coded limits may fail on negative numbers or extremely large k. | Off‑by‑one errors when pushing new indices onto the heap. |
| **Readability** | Clear helper structs (`IndexPair` in C++/Python). | Overly verbose Java code with anonymous inner classes. | Using 0‑based indexing vs. 1‑based indexing in comments causes confusion. |
| **Performance** | `O(k log k)` is optimal given the need to sort k results. | `O(m + n)` scanning then sorting would still be `O(m*n)` worst‑case. | If you push both `(i+1, j)` and `(i, j+1)` without checking bounds, you’ll get `ArrayIndexOutOfBounds`. |

---

## 4. Optimal Approach – Min‑Heap + Visited Set

1. **Start** with the pair `(0, 0)` – the smallest possible sum.  
2. **Heap** stores tuples: `(sum, i, j)`.  
3. **Visited set** remembers which indices have been pushed to avoid duplicates.  
4. **While** we still need pairs (`k` times) and the heap isn’t empty:  
   * Pop the smallest sum.  
   * Append `(nums1[i], nums2[j])` to the result.  
   * Push `(i+1, j)` **if** it hasn’t been visited and `i+1 < nums1.length`.  
   * Push `(i, j+1)` **if** it hasn’t been visited and `j+1 < nums2.length`.  
5. **Return** the list.

**Why it works**  
Because both arrays are sorted, the next smallest sums are always adjacent to the current smallest pair. By pushing only the two “next” candidates we never miss a candidate while keeping the heap size ≤ k.

**Complexity**  
- Time: `O(k log k)` (each pop/push is `log k`)  
- Space: `O(k)` (heap + visited set)

---

## 5. Code Implementations

Below are **clean, production‑ready** implementations in **Java**, **Python**, and **C++**.  
All three use the same algorithmic idea and handle all edge cases.

### 5.1 Java

```java
import java.util.*;

public class Solution {
    public List<List<Integer>> kSmallestPairs(int[] nums1, int[] nums2, int k) {
        List<List<Integer>> result = new ArrayList<>();
        if (nums1.length == 0 || nums2.length == 0 || k == 0) return result;

        // Min‑heap ordering by sum
        PriorityQueue<int[]> minHeap = new PriorityQueue<>(Comparator.comparingInt(a -> a[0]));
        Set<Long> visited = new HashSet<>();   // store (i << 32) | j

        minHeap.offer(new int[]{nums1[0] + nums2[0], 0, 0});
        visited.add(encode(0, 0));

        while (k-- > 0 && !minHeap.isEmpty()) {
            int[] cur = minHeap.poll();
            int sum = cur[0];
            int i = cur[1];
            int j = cur[2];

            result.add(Arrays.asList(nums1[i], nums2[j]));

            if (i + 1 < nums1.length) {
                long key = encode(i + 1, j);
                if (visited.add(key)) {
                    minHeap.offer(new int[]{nums1[i + 1] + nums2[j], i + 1, j});
                }
            }
            if (j + 1 < nums2.length) {
                long key = encode(i, j + 1);
                if (visited.add(key)) {
                    minHeap.offer(new int[]{nums1[i] + nums2[j + 1], i, j + 1});
                }
            }
        }
        return result;
    }

    // Encode two indices into a single long to avoid Pair objects
    private long encode(int i, int j) {
        return (((long) i) << 32) | (j & 0xffffffffL);
    }
}
```

**Key points**

- Using a `long` key instead of a custom `Pair` class keeps memory low.  
- The comparator is simple: sort by the pre‑computed sum.  
- Edge cases are handled upfront (`k == 0`, empty arrays).

---

### 5.2 Python 3

```python
import heapq
from typing import List, Tuple

class Solution:
    def kSmallestPairs(self, nums1: List[int], nums2: List[int], k: int) -> List[List[int]]:
        if not nums1 or not nums2 or k == 0:
            return []

        result: List[List[int]] = []
        min_heap: List[Tuple[int, int, int]] = []          # (sum, i, j)
        visited = set()                                     # store (i, j) tuples

        heapq.heappush(min_heap, (nums1[0] + nums2[0], 0, 0))
        visited.add((0, 0))

        while k and min_heap:
            s, i, j = heapq.heappop(min_heap)
            result.append([nums1[i], nums2[j]])
            k -= 1

            if i + 1 < len(nums1) and (i + 1, j) not in visited:
                heapq.heappush(min_heap, (nums1[i + 1] + nums2[j], i + 1, j))
                visited.add((i + 1, j))

            if j + 1 < len(nums2) and (i, j + 1) not in visited:
                heapq.heappush(min_heap, (nums1[i] + nums2[j + 1], i, j + 1))
                visited.add((i, j + 1))

        return result
```

**Highlights**

- `heapq` gives an efficient binary heap out of the box.  
- The `visited` set prevents pushing duplicate index pairs.  
- Type hints make the function self‑documenting.

---

### 5.3 C++

```cpp
#include <vector>
#include <queue>
#include <unordered_set>
#include <functional>

class Solution {
public:
    std::vector<std::vector<int>> kSmallestPairs(
        const std::vector<int>& nums1,
        const std::vector<int>& nums2,
        int k) {

        std::vector<std::vector<int>> res;
        if (nums1.empty() || nums2.empty() || k == 0) return res;

        // Min‑heap: {sum, i, j}
        using HeapNode = std::tuple<int, int, int>;
        auto cmp = [](const HeapNode& a, const HeapNode& b) {
            return std::get<0>(a) > std::get<0>(b);  // reverse for min‑heap
        };
        std::priority_queue<HeapNode, std::vector<HeapNode>, decltype(cmp)> pq(cmp);
        std::unordered_set<long long> visited;          // encode (i,j) -> long

        auto encode = [](int i, int j) -> long long {
            return (static_cast<long long>(i) << 32) | (j & 0xffffffffLL);
        };

        pq.emplace(nums1[0] + nums2[0], 0, 0);
        visited.insert(encode(0, 0));

        while (k > 0 && !pq.empty()) {
            auto [s, i, j] = pq.top(); pq.pop();
            res.push_back({nums1[i], nums2[j]});
            --k;

            if (i + 1 < nums1.size() && visited.insert(encode(i + 1, j)).second) {
                pq.emplace(nums1[i + 1] + nums2[j], i + 1, j);
            }
            if (j + 1 < nums2.size() && visited.insert(encode(i, j + 1)).second) {
                pq.emplace(nums1[i] + nums2[j + 1], i, j + 1);
            }
        }
        return res;
    }
};
```

**Why this is fast in C++**

- `std::tuple<int, int, int>` is lightweight.  
- Encoding indices into a single `long long` keeps the visited set lean.  
- The lambda comparator keeps the code concise.

---

## 6. Testing & Edge‑Case Coverage

| **Scenario** | **What to test** |
|--------------|------------------|
| **Empty arrays** | `nums1 = []`, `nums2 = [1, 2]` → `[]` |
| **Negative numbers** | `nums1 = [-5, -1]`, `nums2 = [0, 3]` → correct pairs |
| **Duplicate values** | `nums1 = [1, 1]`, `nums2 = [1, 1]` → duplicates are allowed & counted |
| **k larger than product** | `k = 10`, arrays of length 2 → return all 4 pairs |
| **k = 0** | Should return `[]` immediately |
| **Both arrays length 1** | Simple 1‑pair test |

Use unit tests that cover each of these scenarios – it shows interviewers that you’re not just hacking a solution but thinking about production robustness.

---

## 7. When Do You *Not* Need the Visited Set?

If you’re absolutely sure the two arrays have **unique** values and you know that `k` ≤ `nums1.length` + `nums2.length`, you can **drop** the visited set and push both `(i+1, j)` and `(i, j+1)` unconditionally.  
But for the general LeetCode constraints, the visited set is mandatory – otherwise you’ll push the same pair multiple times, blow up the heap, and produce wrong answers.

---

## 8. Quick Interview‑Ready Summary

- **Algorithm**: Min‑heap of `(sum, i, j)` + hash set of visited indices.  
- **Complexity**: `O(k log k)` time, `O(k)` space.  
- **Why it’s optimal**: Sorted input guarantees the next smallest sums are neighbors.  
- **Languages**: Ready‑to‑paste Java, Python, and C++ code above.  

If you can explain this in **5 minutes**, you’re halfway through a stellar interview.  

---

## 9. Next Steps – Practice & Interview Prep

1. **Run the code** against LeetCode’s hidden tests.  
2. **Time yourself** – a `Java` solution should finish under 1 s on a 10⁵‑size input with `k = 10⁴`.  
3. **Explain the trade‑offs**: Why a heap is necessary, what happens if you skip the visited set, etc.  
4. **Mock interview**: Pair up with a friend and practice the “good‑bad‑ugly” discussion – it’s a perfect conversation starter.  

---

## 10. Call to Action

**Ready to ace your next coding interview?**  
- **Download** the multi‑language solutions (copy‑paste)  
- **Run** your own tests on GitHub or a local IDE  
- **Share** this article with peers – it’s SEO‑friendly and ready for your LinkedIn post.

> 🚀 **Book a mock interview with me** or **join our weekly LeetCode challenge** – let’s turn practice into performance!  

Happy coding!