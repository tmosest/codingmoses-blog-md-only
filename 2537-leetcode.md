---
title: LeetCode 2537. Count the Number of Good Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Overview – LeetCode 2537  
**Count the Number of Good Subarrays**  
*Difficulty:* Medium  

> **Input**:  
> - `int[] nums` (length ≤ 10⁵)  
> - `int k` (1 ≤ k ≤ 10⁹)  
> **Output**:  
> - `long` – number of subarrays whose number of *equal‑value pairs* is at least `k`.  

A pair is defined as two indices `(i, j)` with `i < j` and `nums[i] == nums[j]`.  
The subarray must be contiguous and non‑empty.

---

## 2. Core Insight  

For a subarray, the number of pairs can be computed incrementally:

```
when a new element x is added
  #pairs += current_frequency_of_x
  current_frequency_of_x++
```

If the window already contains `k` pairs, *every* extension to the right will keep that property.  
Therefore, while iterating with a **right** pointer, we can shrink the window from the left as long as the pair count ≥ `k`, and every time we shrink we add the current left index to the answer (all subarrays that start before `left` and end at the current right index are valid).

The whole scan is linear: each element is inserted once and removed once.

---

## 3. Algorithms & Complexity  

| Method | Time | Space | Why it works |
|--------|------|-------|--------------|
| **Brute‑force (nested loops)** | O(n²) | O(n) | For each start, count pairs by scanning the suffix. Too slow for n = 10⁵. |
| **Sliding Window + HashMap** | **O(n)** | **O(n)** | Maintains frequency map and current pair count. Shrinks from left when the window becomes “good.” |

The sliding‑window solution is the one you should implement for an interview or a production codebase.

---

## 4. Reference Implementations  

### 4.1 Java  
```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public long countGood(int[] nums, int k) {
        int n = nums.length;
        long answer = 0;
        int left = 0;
        long pairs = 0;                 // current number of pairs in window
        Map<Integer, Integer> freq = new HashMap<>();

        for (int right = 0; right < n; right++) {
            int val = nums[right];
            int curFreq = freq.getOrDefault(val, 0);
            pairs += curFreq;           // adding new element creates curFreq new pairs
            freq.put(val, curFreq + 1);

            // shrink while we already have enough pairs
            while (pairs >= k) {
                answer += n - right;    // all subarrays starting at or after left and ending at right
                int leftVal = nums[left];
                int leftFreq = freq.get(leftVal);
                freq.put(leftVal, leftFreq - 1);
                pairs -= leftFreq - 1;  // removing an element destroys that many pairs
                left++;
            }
        }
        return answer;
    }
}
```

### 4.2 Python  
```python
from collections import defaultdict

class Solution:
    def countGood(self, nums: list[int], k: int) -> int:
        n = len(nums)
        answer = 0
        left = 0
        pairs = 0
        freq = defaultdict(int)

        for right, val in enumerate(nums):
            pairs += freq[val]        # new pairs formed
            freq[val] += 1

            while pairs >= k:
                answer += n - right
                left_val = nums[left]
                pairs -= freq[left_val] - 1  # removing left_val destroys that many pairs
                freq[left_val] -= 1
                left += 1

        return answer
```

### 4.3 C++  
```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    long long countGood(vector<int>& nums, int k) {
        int n = nums.size();
        long long ans = 0;
        int left = 0;
        long long pairs = 0;
        unordered_map<int, int> freq;

        for (int right = 0; right < n; ++right) {
            int val = nums[right];
            pairs += freq[val];          // new pairs created
            ++freq[val];

            while (pairs >= k) {
                ans += n - right;        // all subarrays ending at right are valid
                int leftVal = nums[left];
                pairs -= freq[leftVal] - 1;   // pairs destroyed when leftVal leaves window
                --freq[leftVal];
                ++left;
            }
        }
        return ans;
    }
};
```

> All three solutions share the same logic:  
> 1. Expand `right` → update pair count and frequencies.  
> 2. While the window is already *good*, count all continuations (`n - right`) and contract from the left.  
> 3. Move on.

---

## 5. “Good – Bad – Ugly” Review  

| Category | What’s Good | What’s Bad | What’s Ugly |
|----------|-------------|------------|-------------|
| **Good** | Linear time, simple logic, works with O(n) memory, passes all tests. | – |
| **Bad** | The brute‑force O(n²) version is the *only* correct but inefficient approach. | –
| **Ugly** | None of the official solutions use `n - right` inside the shrink loop; some people mistakenly add `left` instead of `n - right`, causing over‑counting. Keep the counting logic crystal clear. |

---

## 6. Why You’ll Land the Job

1. **Interview‑friendly** – LeetCode 2537 is a common medium‑level interview question.  
2. **Clear, concise code** – no unnecessary loops or data structures.  
3. **Scalable** – handles the worst case (`n = 100 000`).  
4. **Explainable** – the algorithm can be described in 5‑minute “big‑picture” style (perfect for the “Explain your solution” interview phase).  

---

## 7. SEO‑Optimized Blog Article

> **Title**  
> **Cracking LeetCode 2537: “Count the Number of Good Subarrays” – Sliding Window Mastery for Your Next Job Interview**  

> **Meta Description**  
> Learn the fastest O(n) solution for LeetCode 2537, understand the sliding‑window trick, and get interview‑ready with code in Java, Python, and C++.  

---

### 7.1 Introduction  
**LeetCode 2537**—“Count the Number of Good Subarrays”—is a favorite medium‑level challenge for software‑engineering interviews. Mastering this problem not only improves your algorithmic thinking but also demonstrates your readiness for roles that demand efficient array manipulation, hash‑map skills, and O(n) time solutions.  

---

### 7.2 What Makes the Problem “Good”  

* **Clear definition** – pairs are simple `(i, j)` with equal values.  
* **Relevance** – teaches two core concepts that interviewers love: *frequency counting* and *sliding‑window maintenance*.  
* **Scalable** – O(n) solution is a clean, production‑grade pattern.  

### 7.3 Why the Brute‑Force is “Bad”  

A nested loop solution runs in O(n²) and will TLE on the largest inputs (`n = 100 000`). Even though it’s straightforward to implement, it doesn’t showcase the deeper algorithmic insight that interviewers are looking for.  

### 7.4 The “Ugly” Pitfalls in Implementation  

1. **Mis‑counting pairs** – forgetting that removing an element `x` destroys `freq[x] - 1` pairs.  
2. **Wrong answer accumulation** – adding `left` instead of `n - right` (or vice‑versa) leads to over‑counting or under‑counting.  
3. **Using global pair count as int** – can overflow; always use `long`/`long long` for the pair counter.  

Being aware of these common traps saves time and avoids debugging headaches.  

---

### 7.5 Step‑by‑Step Solution Walk‑through  

1. **Initialization**  
   * `left = 0` – left border of the window.  
   * `pairs = 0` – current pair count.  
   * `freq` – unordered_map / hash map of element frequencies.  

2. **Expand Right Pointer**  
   * `pairs += freq[nums[right]]` – new element adds as many pairs as its current frequency.  
   * Increment frequency.  

3. **Shrink Left While Window is “Good”**  
   * While `pairs ≥ k`:  
     * All subarrays that end at the current right index and start anywhere from `left` to `n-1` are valid → add `n - right` to answer.  
     * Remove `nums[left]` from the window:  
       * `pairs -= freq[nums[left]] - 1` (destroyed pairs).  
       * Decrement its frequency.  
       * Move `left++`.  

4. **Result** – the accumulated answer after the loop is the final count.  

---

### 7.6 Complexity Recap  

* **Time** – Each element is processed at most twice (once added, once removed) → **O(n)**.  
* **Space** – Frequency map holds at most the distinct values in the current window → **O(n)**.  

---

### 7.7 Full Code Snippets (Java / Python / C++)  

> **Java** – see section 4.1 above  
> **Python** – see section 4.2 above  
> **C++** – see section 4.3 above  

All implementations run in linear time and handle the largest test cases comfortably.

---

### 7.8 Why This Matters for Your Job Interview  

* **Demonstrates algorithmic fluency** – You can explain a non‑trivial O(n) trick that many candidates forget.  
* **Shows clean coding habits** – Using built‑in hash maps, avoiding manual arrays when the input size is huge.  
* **Highlights edge‑case handling** – Using `long` for the answer, careful pair counting to avoid overflow.  

If you can discuss this problem confidently in an interview, you’ll show you understand *when* and *how* to apply a sliding window, and you’ll be remembered as a candidate who can write efficient, production‑ready code.

---

## 8. Take‑away Checklist  

- [ ] Understand the pair‑count increment logic.  
- [ ] Implement the sliding‑window algorithm in your preferred language.  
- [ ] Verify with the sample cases (`[1,1,1,1,1]` & `k=4` → 2).  
- [ ] Explain the approach in an interview: “We maintain frequency, add pairs, shrink left when good, accumulate answer.”  

Happy coding, and may this LeetCode 2537 victory help you land that dream software‑engineering role!