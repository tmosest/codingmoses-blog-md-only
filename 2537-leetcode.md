---
title: LeetCode 2537. Count the Number of Good Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2537 – Count the Number of Good Subarrays  
*LeetCode* | *Java* | *Python* | *C++* | *Sliding‑Window* | *Job Interview*  

Below you’ll find a production‑ready implementation for **Java, Python and C++** that solves the LeetCode problem **2537 – Count the Number of Good Subarrays** in *O(n)* time using the classic two‑pointer sliding‑window technique.  

---

### 1.  Problem Recap  

> **Definition** – A subarray is *good* if it contains **at least `k` pairs of equal elements**.  
>  
> Input: `int[] nums` (|nums| ≤ 10⁵, 0 ≤ nums[i] ≤ 10⁹) and `int k` (0 ≤ k ≤ 10⁹).  
> Output: the number of *good* subarrays (return type `long`/`long long`).  

The brute‑force solution would examine every subarray (O(n²)), which is far too slow for the constraints. The sliding‑window trick turns it into a linear‑time algorithm.

---

## 2.  Sliding‑Window Insight  

| What you *track* | Why it works |
|------------------|--------------|
| Frequency map (`freq`) | Lets us know how many times a value appears in the current window. |
| `pairs` – number of equal pairs in the current window | Adding an element of value `x` creates `freq[x]` new pairs (since each of the existing `freq[x]` occurrences can pair with the new one). |
| `left` – left pointer of the window | When the window is good (`pairs` ≥ `k`), every subarray that starts at an index ≤ `left` and ends at the current right index is good. The number of such subarrays equals `left`. |

**Key loop invariant** – After we shrink the window while it remains good, the window `[left … right]` is the *smallest* window ending at `right` that still satisfies the requirement. Thus all subarrays ending at `right` that start **before** `left` are guaranteed to be good, and we add `left` to the answer.

---

## 3.  Reference Implementations  

### Java

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    /**
     * Returns the number of good subarrays in O(n) time.
     * @param nums the array of integers
     * @param k    the required number of pairs
     * @return     the count of good subarrays (long to avoid overflow)
     */
    public long countGood(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        int left = 0;        // left boundary of the current window
        long ans = 0;
        int pairs = 0;       // number of equal pairs inside the window

        for (int right = 0; right < nums.length; right++) {
            int cur = nums[right];
            // Adding cur adds freq[cur] new pairs
            pairs += freq.getOrDefault(cur, 0);
            freq.put(cur, freq.getOrDefault(cur, 0) + 1);

            // Shrink the window while we have enough pairs
            while (pairs >= k) {
                ans += nums.length - right;   // all subarrays that start at 'left' are good
                int leftVal = nums[left];
                int leftFreq = freq.get(leftVal);

                // Removing leftVal reduces the number of pairs by (leftFreq - 1)
                pairs -= (leftFreq - 1);
                if (leftFreq == 1) {
                    freq.remove(leftVal);
                } else {
                    freq.put(leftVal, leftFreq - 1);
                }
                left++;
            }
        }
        return ans;
    }
}
```

---

### Python

```python
from collections import defaultdict
from typing import List

def countGood(nums: List[int], k: int) -> int:
    """
    Count good subarrays in O(n) time.
    :param nums: List[int] – input array
    :param k:    int       – required number of equal pairs
    :return:     int       – number of good subarrays
    """
    freq = defaultdict(int)   # element → frequency in current window
    left = 0                  # left boundary of window
    pairs = 0                 # number of equal pairs in the window
    ans = 0

    for right, val in enumerate(nums):
        # Adding new element creates `freq[val]` new pairs
        pairs += freq[val]
        freq[val] += 1

        # While window is still good, shrink it from the left
        while pairs >= k:
            # All subarrays starting from current 'left' to 'right' are good
            ans += right - left + 1
            left_val = nums[left]
            left_freq = freq[left_val]

            # Removing left_val reduces pairs by (left_freq - 1)
            pairs -= (left_freq - 1)
            freq[left_val] = left_freq - 1
            left += 1

    return ans
```

> **Why `ans += right - left + 1`?**  
> Once we find the minimal left that still keeps the window good, every subarray that starts at `left` **or later** and ends at `right` will also contain at least `k` pairs. The count of such subarrays is exactly `right - left + 1`.

---

### C++

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class Solution {
public:
    /**
     * @param nums the array of integers
     * @param k    the required number of pairs
     * @return     the number of good subarrays (long long to avoid overflow)
     */
    long long countGood(vector<int>& nums, int k) {
        unordered_map<int, int> freq;
        long long ans = 0;
        int left = 0;              // left boundary of the window
        long long pairs = 0;       // number of equal pairs inside the window

        for (int right = 0; right < (int)nums.size(); ++right) {
            int cur = nums[right];
            // Adding cur creates freq[cur] new pairs
            pairs += freq[cur];
            ++freq[cur];

            // Shrink the window while we have enough pairs
            while (pairs >= k) {
                ans += nums.size() - right;   // all subarrays starting at 'left' are good
                int leftVal = nums[left];
                int leftFreq = freq[leftVal];

                // Removing leftVal reduces pairs by (leftFreq - 1)
                pairs -= (leftFreq - 1);
                if (leftFreq == 1) freq.erase(leftVal);
                else freq[leftVal] = leftFreq - 1;
                ++left;
            }
        }
        return ans;
    }
};
```

> **Tip:** In many accepted solutions you’ll see the variable `k` being decremented/incremented to track the remaining pair quota. The logic is equivalent to what we described above – just a slightly different bookkeeping style.

---

## 4.  The “Good, Bad & Ugly” of This Solution  

| Aspect | What Makes It Good | What’s Tricky | What Could Go Wrong |
|--------|-------------------|---------------|--------------------|
| **Good** | Linear time, easy to understand once the pair‑count trick is grasped. |  |  |
| **Bad** | You must keep `k` *mutable* or maintain a separate `pairs` counter; mixing the two can lead to bugs. |  | Off‑by‑one errors when adding/removing from the window. |
| **Ugly** | The logic that “adding an element increases pairs by its current frequency” is not obvious at first glance. |  | Forgetting to update the map after decrementing frequencies can produce `-1` counts. |

---

## 5.  SEO‑Optimized Blog Post

### 🎯 Master LeetCode 2537: Count the Number of Good Subarrays

If you’re preparing for a **software engineering interview** or a **coding bootcamp** and want to ace **LeetCode 2537 – Count the Number of Good Subarrays**, you’re in the right place.  
In this article we’ll:

1. Break down the **sliding‑window** algorithm that brings the time complexity from **O(n²)** to **O(n)**.  
2. Provide clean, production‑ready code in **Java**, **Python**, and **C++**.  
3. Explain the tricky bits, the pitfalls, and how to talk about this solution in an interview.

#### Keywords:  
- LeetCode 2537  
- Count the Number of Good Subarrays  
- Sliding Window  
- Java algorithm interview  
- Python coding challenge  
- C++ algorithm explanation  
- Job interview algorithm  

---

### 📌 What Is a “Good” Subarray?

> A subarray is **good** if it contains at least `k` pairs of equal elements.  
> A pair is a pair of indices `(i, j)` with `i < j` and `nums[i] == nums[j]`.  

The naive solution would check every subarray and count its pairs, giving **O(n²)** time. That’s not feasible for `n ≤ 100,000`.  

---

### 🧠 Sliding‑Window to the Rescue

The heart of the solution is a **two‑pointer** approach:

- **Right pointer** expands the window one element at a time.  
- **Left pointer** shrinks the window while the window still satisfies the pair requirement.  

Key trick:  
*When you add a new element of value `x`, the number of new pairs equals the current frequency of `x` in the window.*  
Thus we keep a **frequency map** and a running `pairs` counter.

The invariant after shrinking the window is:  
*`[left … right]` is the smallest window that still contains at least `k` pairs.*  
All subarrays that end at `right` and start **before** `left` are automatically good.  

---

### 🛠️ Reference Code – Java, Python, C++

We’ve already provided the full implementations above. In interviews, you can pick the language you’re most comfortable with, but the logic is identical:

```java
// Java
public long countGood(int[] nums, int k) { … }
```

```python
# Python
def countGood(nums: List[int], k: int) -> int: … 
```

```cpp
// C++
long long countGood(vector<int>& nums, int k) { … }
```

> **Why do we use `long long` (or `long` in Java)?**  
> The answer can be as large as `n * (n+1) / 2`, which exceeds the 32‑bit integer range for large inputs.

---

### 📌 Talking About the Solution in an Interview

> **Question:** “Explain how you would approach LeetCode 2537.”  
> **Answer (TL;DR):**  
> “I’d use a sliding‑window. I maintain a frequency map and a `pairs` counter. As I slide the right pointer I add the new element’s current frequency to `pairs`. Then I keep moving the left pointer until `pairs` falls below `k`. Every time the window is good I add the number of subarrays that start before the left pointer to my answer.”  

If the interviewer asks for **why this is linear**:  
- The right pointer moves **n** times.  
- Each left pointer movement also moves at most **n** times.  
- All operations on the hash map are *amortised* O(1).  
- Total time is **O(n)**, memory **O(min(n, distinct values))**.

---

### 🏁 Take‑away

- Sliding‑window + frequency map = the gold‑standard solution for LeetCode 2537.  
- The pair‑count trick is the “Eureka” moment: *add `freq[x]` when you insert `x`*.  
- Having clean code in **Java, Python, C++** shows you understand language‑specific idioms while keeping the core idea unchanged.

Good luck! 💪 Remember, the key to landing that software engineering role is not just having a correct solution but being able to explain *why* it works. Happy coding!