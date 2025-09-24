---
title: LeetCode 2656. Maximum Sum With Exactly K Elements - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 2656. Maximum Sum With Exactly K Elements  
**Leetcode Easy – 100 % solved with a greedy formula**

> **Goal** – Pick an element *k* times to maximize the total score.  
> Every time you pick *m* you get `+m` to your score, the element is removed, a new element `m+1` is inserted, and the array is updated.  

---

### 📌 Problem Summary

|   |   |
|---|---|
| **Array** `nums` | 0‑indexed, `1 ≤ nums[i] ≤ 100` |
| **Length** | `1 ≤ nums.length ≤ 100` |
| **k** | `1 ≤ k ≤ 100` |
| **Return** | maximum possible score after exactly *k* picks |

---

## 1️⃣ Why a Greedy Pick Is Optimal

1. **Increasing the value** – After you pick *m*, the same element becomes *m+1*.  
2. **Future value** – Picking a smaller element can never produce a value larger than picking the current maximum, because the chosen element is *always* increased by 1 and remains the largest after the pick.

**Therefore**:  
> **Always pick the current maximum element**.  
> The sequence of picks will be:  
> `max, max+1, max+2, … , max+(k-1)`  

The total score is simply the sum of this arithmetic progression.

---

## 2️⃣ Mathematical Formula

```
sum = k * max + (k * (k - 1)) / 2
```

* `k * max` – we pick the original maximum *k* times.  
* `(k * (k - 1)) / 2` – the incremental values added by the +1 operation each time.

The formula is O(1) after finding `max`.

---

## 3️⃣ Algorithm (Pseudocode)

```
maxVal = maximum element in nums
answer = k * maxVal + k * (k - 1) / 2
return answer
```

*Finding `maxVal`* – a simple linear scan (O(n)).  
The rest is constant time.

---

## 4️⃣ Time & Space Complexity

|   |   |
|---|---|
| **Time** | `O(n)` – only the scan for the maximum |
| **Space** | `O(1)` – no extra data structures |

---

## 5️⃣ Code Implementations

> All solutions assume the array is non‑empty and *k* is valid according to the constraints.

### ⚙️ Java

```java
class Solution {
    public int maximizeSum(int[] nums, int k) {
        int max = Integer.MIN_VALUE;
        for (int n : nums) {
            if (n > max) max = n;
        }
        return k * max + k * (k - 1) / 2;
    }
}
```

### ⚙️ Python

```python
class Solution:
    def maximizeSum(self, nums: List[int], k: int) -> int:
        max_val = max(nums)
        return k * max_val + k * (k - 1) // 2
```

### ⚙️ C++

```cpp
class Solution {
public:
    int maximizeSum(vector<int>& nums, int k) {
        int mx = *max_element(nums.begin(), nums.end());
        return k * mx + (k * (k - 1)) / 2;
    }
};
```

All three implementations are less than 20 lines, fast, and clear.

---

## 6️⃣ The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Simplicity** | One‑liner formula after finding max | No need to simulate k operations | None |
| **Performance** | O(n) time, O(1) space | Works within constraints | If you naively simulate, O(nk) could be *excessive* for larger constraints |
| **Edge Cases** | Handles `k = 1`, `max` at any index | None | Wrong intuition: picking a non‑max element can *seem* to help if you forget the +1 rule |
| **Proof of Correctness** | Clear arithmetic progression | Sometimes overlooked in interviews | A greedy proof must be written: show that replacing any pick with the current max never decreases future choices |
| **Interview Talk** | Quick “pick the max” argument | Need to explain why picking the largest stays optimal | Be careful not to say “just pick the largest” without the +1 rule context – interviewers might probe you |

---

## 7️⃣ Common Interview Pitfalls

1. **Simulating the array** – Students often write a loop that removes an element and pushes back `m+1`. This is unnecessary and can lead to TLE if the constraints were larger.
2. **Missing the incremental term** – Forgetting `(k * (k-1))/2` results in under‑estimating the answer.
3. **Integer overflow** – In languages with 32‑bit limits, use `long`/`long long` if `k` or `max` were larger (not needed here, but good practice).
4. **Assuming sorting is required** – Sorting the array is O(n log n) and unnecessary.

---

## 8️⃣ How to Use This Blog for Your Job Search

1. **SEO Keywords** – The title and headings contain high‑volume terms: *Maximum Sum With Exactly K Elements, Leetcode 2656, greedy algorithm, coding interview*, etc.
2. **Readable, Structured Content** – Interviewers appreciate clean explanations. Copy the “Good, Bad, Ugly” section to showcase your analytical thinking.
3. **Link to Code** – Provide snippets in all three major languages. Recruiters often look for language versatility.
4. **Proof & Complexity** – Mention time/space complexity; recruiters want concise, correct solutions.
5. **Show the Thought Process** – In your résumé or portfolio, reference this blog post as a problem you solved; use the “Proof of Correctness” section as evidence of deep understanding.

---

## 📑 TL;DR

- **Greedy pick** the current maximum `k` times.  
- Total score = `k * max + k*(k-1)/2`.  
- O(n) time, O(1) space.  
- Implementation in Java, Python, and C++ provided.  

Use this concise, proven solution to ace Leetcode 2656 and impress interviewers with a clear, optimal algorithm. 🚀