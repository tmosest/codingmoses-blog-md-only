---
title: LeetCode 3209. Number of Subarrays With AND Value of K - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🧠 LeetCode #3209 – *Number of Subarrays With AND Value of K*  
**Difficulty:** Hard | **Type:** Array | **Bit Manipulation**  

> **Goal** – For a given integer array `nums` and an integer `k`, return the number of sub‑arrays whose bitwise AND equals `k`.  
> **Example**  
> `nums = [1,1,2] , k = 1` → answer `3` (sub‑arrays: `[1,1,2]`, `[1,1,2]`, `[1,1,2]`).

> **Why it matters** – Bit‑wise operators are a staple in interviews; they test your ability to reason about binary representation and use of hash maps for counting.  
> Mastering this problem demonstrates that you can **optimize time & space** while working with cumulative bit‑operations.

Below you’ll find complete, production‑ready implementations in **Java, Python, and C++** (all `O(n log MAX)` time, `O(log MAX)` space), followed by a **SEO‑friendly blog post** that discusses the “Good, the Bad, and the Ugly” of this challenge.

---

## 🚀 Solutions

### 1️⃣ Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * Counts sub‑arrays of nums with AND value == k.
     *
     * @param nums the input array
     * @param k    the target AND value
     * @return number of qualifying sub‑arrays (long, because answer can exceed 32‑bit)
     */
    public long countSubarrays(int[] nums, int k) {
        long ans = 0L;
        Map<Integer, Integer> prev = new HashMap<>();

        for (int val : nums) {
            // 1. Sub‑array that consists solely of the current element
            if (val == k) ans++;

            Map<Integer, Integer> curr = new HashMap<>();

            // 2. Extend every existing AND value with the new element
            for (Map.Entry<Integer, Integer> e : prev.entrySet()) {
                int newAnd = e.getKey() & val;
                int cnt = e.getValue();

                if (newAnd == k) ans += cnt;        // found a valid sub‑array

                // store the new AND for future extensions
                curr.merge(newAnd, cnt, Integer::sum);
            }

            // 3. Start new sub‑array at current position
            curr.merge(val, 1, Integer::sum);
            prev = curr; // prepare for next iteration
        }
        return ans;
    }
}
```

**Complexity**

| Metric | Calculation |
|--------|-------------|
| **Time** | Each element merges at most *O(number of distinct AND values)*, bounded by 32 (bits). → `O(n * 32)` → `O(n)` |
| **Space** | Stores distinct ANDs of the current window → `O(32)` → `O(1)` |

> **Tip:** `merge()` is a Java 8+ helper that keeps the code clean and avoids manual checks.

---

### 2️⃣ Python

```python
from collections import defaultdict
from typing import List

class Solution:
    def countSubarrays(self, nums: List[int], k: int) -> int:
        ans = 0
        prev = defaultdict(int)          # AND value -> occurrences

        for val in nums:
            if val == k:
                ans += 1

            curr = defaultdict(int)

            # Extend previous ANDs
            for prev_and, cnt in prev.items():
                new_and = prev_and & val
                if new_and == k:
                    ans += cnt
                curr[new_and] += cnt

            # New sub‑array that starts here
            curr[val] += 1
            prev = curr

        return ans
```

**Complexity**

- **Time:** `O(n * 32)` → `O(n)`
- **Space:** `O(32)` → `O(1)`

> **Pythonic note** – `defaultdict(int)` eliminates the need for `get(..., 0)` boilerplate.

---

### 3️⃣ C++

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    long long countSubarrays(vector<int>& nums, int k) {
        long long ans = 0;
        unordered_map<int, int> prev;   // AND value -> occurrences

        for (int val : nums) {
            if (val == k) ans++;

            unordered_map<int, int> curr;

            for (auto &p : prev) {
                int new_and = p.first & val;
                if (new_and == k) ans += p.second;
                curr[new_and] += p.second;
            }

            curr[val]++;   // sub‑array that starts at this position
            prev.swap(curr);
        }
        return ans;
    }
};
```

**Complexity**

- **Time:** `O(n * 32)` → `O(n)`
- **Space:** `O(32)` → `O(1)`

> **C++ tip** – `prev.swap(curr)` avoids a costly copy; it's a constant‑time pointer exchange.

---

## 📖 Blog Article – “The Good, the Bad, and the Ugly of LeetCode 3209”

> **Title:** *Mastering LeetCode 3209 – Number of Subarrays With AND Value of K: The Good, the Bad, and the Ugly*  
> **Keywords:** `Number of Subarrays With AND Value of K`, `LeetCode 3209`, `bitwise AND subarray`, `Java solution`, `Python solution`, `C++ solution`, `hashmap`, `time complexity`, `job interview`, `algorithms`

---

### Introduction

If you’re eyeing a software‑engineering role, you’ll soon encounter **bitwise manipulation** questions on LeetCode and technical interviews. One such challenge is **LeetCode #3209 – Number of Subarrays With AND Value of K**. At first glance, it feels like a straightforward sliding‑window problem, but the twist is that *any* sub‑array, not just contiguous blocks of a fixed size, can produce the target value.

In this post we dissect the problem, provide ready‑to‑copy solutions in **Java, Python, and C++**, and discuss the strengths, weaknesses, and pitfalls (“the ugly”) you might encounter while tackling this hard‑level problem.

---

### 1. Problem Recap & Constraints

- **Input**: `nums` – array of up to `10^5` integers (0 ≤ nums[i] ≤ 10^9), and `k` – target AND value.
- **Output**: Number of sub‑arrays where the bitwise AND of all elements equals `k`.
- **Note**: Answer can exceed 32‑bit integer range; use 64‑bit (`long`/`long long`) to store the result.

Because `k` can be as large as `10^9`, we cannot afford a naive `O(n^2)` solution that checks every sub‑array.

---

### 2. The Good – Why This Problem is Worth Solving

1. **Shows mastery of bitwise ops**  
   Understanding how AND interacts with binary representation is essential. Each bit acts independently; the AND of a bit is 1 iff all sub‑array elements have that bit set.

2. **Demonstrates use of hash maps**  
   The canonical solution keeps a map of *current AND values* to their frequency, showing how to maintain state efficiently.

3. **Offers linear time complexity**  
   Even with `10^5` elements, an `O(n)` solution is fast enough. Interviewers love when you can reduce a seemingly quadratic problem to linear.

4. **Reveals subtle edge‑case handling**  
   For example, `k = 0` requires careful reasoning about how AND tends to zero out bits. Handling 0 correctly shows depth.

---

### 3. The Bad – Common Pitfalls & Why the Easy Approach Fails

| Pitfall | Why It Happens | What to Do Instead |
|---------|----------------|--------------------|
| **Brute force O(n²)** | Checking all sub‑arrays is obvious but too slow. | Use a rolling map to remember AND results. |
| **Misusing `int` for the answer** | The result can be `> 2^31-1`. | Use `long`/`long long`. |
| **Ignoring that AND values shrink** | You might think AND values grow. Actually, each new element can only turn bits off. | This property limits distinct AND values to ≤32 per step. |
| **Re‑building the map from scratch** | Creating a fresh map each iteration without merging is still linear, but you miss the cumulative count optimization. | Merge previous map entries with the new element. |
| **Forgetting sub‑array that starts at the current index** | The current element alone might satisfy the condition. | Always include `curr[val] += 1`. |

---

### 4. The Ugly – Edge Cases & Deep Insights

| Ugly Situation | Explanation | Fix |
|----------------|-------------|-----|
| **k = 0 and all elements are powers of two** | AND of any sub‑array that includes at least two different bits is 0. Counting correctly requires handling many zero ANDs. | The map automatically tracks `0` counts. Ensure you add `ans += prev.get(0)` when `newAnd == 0`. |
| **Large `k` that never appears** | The algorithm will still process each element, potentially wasting time on maps with `k` never reached. | Still fine; the constant factor is tiny (≤32 entries). |
| **Repeated same numbers** | When all numbers are the same, each step doubles the map size until capped by bits. | The algorithm’s `merge` handles this automatically. |
| **Negative numbers (not in constraints but worth noting)** | Bitwise AND on signed ints can be confusing. | Problem guarantees non‑negative, so ignore. |

---

### 5. Why This Solution Aces Interviews

1. **Time‑space trade‑off clarity** – You can discuss why `O(n)` time and `O(1)` space is possible, citing the 32‑bit limit.
2. **Showcases map merging** – Demonstrates that you can *combine* previous results with the current element efficiently.
3. **Versatile language implementation** – Being able to write the same logic in **Java, Python, and C++** proves language‑agnostic algorithmic thinking.
4. **Handling large outputs** – Using 64‑bit counters shows attention to data type details—a common interview question.

---

### 6. Final Thoughts & Career‑Boosting Tips

- **Practice the map‑merge pattern** in other bitwise problems (e.g., *count sub‑arrays with OR value K*).  
- **Explain your reasoning aloud** during interviews; they want to see your thought process.  
- **Prepare edge‑case tests**: all zeros, all same number, alternating bits.  
- **Mention the “max 32 distinct AND values” trick**; it often impresses interviewers as a non‑obvious observation.  

With the solutions below, you’re ready to hit the job market armed with a solid, proven algorithm.

---

## 📌 Ready‑to‑Copy Code Snippets

> Use the following snippets directly in your preferred language:

### Java

```java
public long countSubarrays(int[] nums, int k) {
    long ans = 0L;
    Map<Integer, Integer> prev = new HashMap<>();

    for (int val : nums) {
        if (val == k) ans++;

        Map<Integer, Integer> curr = new HashMap<>();
        for (Map.Entry<Integer, Integer> e : prev.entrySet()) {
            int newAnd = e.getKey() & val;
            if (newAnd == k) ans += e.getValue();
            curr.merge(newAnd, e.getValue(), Integer::sum);
        }

        curr.merge(val, 1, Integer::sum);
        prev = curr;
    }
    return ans;
}
```

### Python

```python
from collections import defaultdict

def countSubarrays(nums, k):
    ans = 0
    prev = defaultdict(int)
    for val in nums:
        if val == k: ans += 1
        curr = defaultdict(int)
        for prev_and, cnt in prev.items():
            new_and = prev_and & val
            if new_and == k: ans += cnt
            curr[new_and] += cnt
        curr[val] += 1
        prev = curr
    return ans
```

### C++

```cpp
long long countSubarrays(vector<int>& nums, int k) {
    long long ans = 0;
    unordered_map<int,int> prev;
    for (int val : nums) {
        if (val == k) ans++;
        unordered_map<int,int> curr;
        for (auto &p : prev) {
            int new_and = p.first & val;
            if (new_and == k) ans += p.second;
            curr[new_and] += p.second;
        }
        curr[val]++;        // start new sub‑array
        prev.swap(curr);
    }
    return ans;
}
```

Happy coding—and best of luck landing that dream role! 🚀

--- 

*Feel free to share this article on LinkedIn, GitHub, or Medium to showcase your algorithmic prowess.*