---
title: LeetCode 1437. Check If All 1's Are at Least Length K Places Away - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 LeetCode 1437 – “Check If All 1’s Are at Least Length K Places Away”

| Language | ✅ Time | ✅ Space |
|----------|---------|----------|
| **Java** | O(n) | O(1) |
| **Python** | O(n) | O(1) |
| **C++** | O(n) | O(1) |

> **Why this problem matters** – It’s a classic interview question that tests *array traversal*, *edge‑case handling*, and *time‑space trade‑offs*. Mastering it will give you a solid talking point for coding interviews at Google, Amazon, Microsoft, and beyond.

---

## 1. Problem Statement

> **Given** a binary array `nums` and an integer `k`, return **true** if every pair of 1s in `nums` is at least `k` positions apart.  
> **Return** false otherwise.

### Example

| Input | Output | Explanation |
|-------|--------|-------------|
| `[1,0,0,0,1,0,0,1]`, `k=2` | `true` | Each 1 is ≥ 2 indices apart. |
| `[1,0,0,1,0,1]`, `k=2` | `false` | The last two 1s are only 1 index apart. |

---

## 2. Intuition

- The condition can only fail **when two consecutive 1s are too close**.  
- So we just need to remember **the last index where we saw a 1** and, when we see a new 1, check the distance.

---

## 3. Algorithm (Linear Scan)

1. **Initialize** `last = -∞` (or `-1` to indicate “no 1 seen yet”).
2. **Iterate** over the array with index `i`:
   - If `nums[i] == 1`:
     - If `i - last - 1 < k`, return `false`.
     - Set `last = i`.
3. After the loop, return `true`.

### Why it works

- The first 1 sets the baseline `last`.  
- Every subsequent 1 must be at least `k` zeros away.  
- The formula `i - last - 1` counts the zeros between the two 1s.  
- If any gap is smaller than `k`, the rule is violated.

---

## 4. Code Implementations

### Java

```java
class Solution {
    public boolean kLengthApart(int[] nums, int k) {
        int last = -1;                     // No 1 seen yet
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 1) {
                if (i - last - 1 < k) {   // Too close?
                    return false;
                }
                last = i;                 // Update last seen 1
            }
        }
        return true;                       // All gaps satisfied
    }
}
```

### Python

```python
class Solution:
    def kLengthApart(self, nums: List[int], k: int) -> bool:
        last = -1                       # No 1 seen yet
        for i, val in enumerate(nums):
            if val == 1:
                if i - last - 1 < k:    # Gap too small
                    return False
                last = i                 # Remember this 1
        return True
```

### C++

```cpp
class Solution {
public:
    bool kLengthApart(vector<int>& nums, int k) {
        int last = -1;                  // No 1 seen yet
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == 1) {
                if (i - last - 1 < k)  // Too close
                    return false;
                last = i;               // Update last seen 1
            }
        }
        return true;                    // All gaps good
    }
};
```

All three solutions run in **O(n)** time and use **O(1)** extra space.

---

## 5. Edge Cases & Testing

| Test | Explanation |
|------|-------------|
| `[0,0,0,0]`, `k=5` | No 1s → always `true`. |
| `[1]`, `k=0` | Single 1 → `true`. |
| `[1,1]`, `k=0` | Adjacent 1s allowed (`k=0`) → `true`. |
| `[1,0,1]`, `k=2` | Gap = 1 < 2 → `false`. |

---

## 6. Variations & Extensions

- **Variable k** – The same code handles any `k` (including 0).
- **Multiple data types** – The algorithm works with any array of 0/1 values.
- **Streaming input** – Only the last index of a 1 is needed; you could stream the array if it’s too large for memory.

---

## 7. Interview Take‑aways

| ✅ Good | ⚠️ Bad | 😈 Ugly |
|---------|-------|--------|
| **Linear time** – O(n) is the best you can do for a single pass. | **Off‑by‑one errors** – Remember to subtract 1 when counting zeros. | **Over‑engineering** – Some candidates add a queue or list of 1s, which is unnecessary and increases space. |
| **Single variable** – `last` keeps the state; minimal memory. | **Missing edge cases** – Don’t forget empty arrays or `k=0`. | **Wrong variable type** – Using `int` for index is fine; avoid `long` unless array is >2^31. |
| **Clear variable names** – `lastSeenOne` or `last` helps readability. | **Complexity confusion** – Be explicit that the algorithm is O(n), not O(n²). | **Hard‑to‑read code** – Overly nested loops or unnecessary helper functions can confuse interviewers. |

---

## 8. SEO‑Optimized Blog Article

> **Title:** *Master LeetCode 1437: “Check If All 1’s Are at Least Length K Places Away” – Java, Python, C++ Solutions + Interview Insights*  
> **Meta Description:** Learn how to solve LeetCode 1437 in Java, Python, and C++ with a fast O(n) algorithm. Understand the intuition, edge cases, and interview tips to land your next tech job.  

---

### 🔍 Problem Overview

The challenge is to determine if every pair of 1s in a binary array is separated by at least `k` zeros. It’s a classic array‑traversal problem that appears in many coding interviews.

> **Why it’s a “must‑know”**  
> • Straightforward yet deep: tests your understanding of indices, gaps, and edge‑case handling.  
> • Ideal for “whiteboard” coding because it can be solved in a single loop.  
> • Demonstrates clean coding style – a plus point in interviews.

---

### ✅ The Linear‑Scan Solution

1. **Track the last 1**  
   - Initialize `last = -1`.  
   - When you encounter a 1 at index `i`, compare `i - last - 1` to `k`.

2. **Early exit**  
   - If the gap is smaller than `k`, return `false` immediately.  
   - Otherwise, set `last = i` and continue.

3. **Return true** after the loop if no violations were found.

**Time Complexity:** `O(n)` – one pass over the array.  
**Space Complexity:** `O(1)` – only a single integer is stored.

---

### 🚀 Code Snippets

#### Java

```java
class Solution {
    public boolean kLengthApart(int[] nums, int k) {
        int last = -1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == 1) {
                if (i - last - 1 < k) return false;
                last = i;
            }
        }
        return true;
    }
}
```

#### Python

```python
class Solution:
    def kLengthApart(self, nums: List[int], k: int) -> bool:
        last = -1
        for i, val in enumerate(nums):
            if val == 1:
                if i - last - 1 < k:
                    return False
                last = i
        return True
```

#### C++

```cpp
class Solution {
public:
    bool kLengthApart(vector<int>& nums, int k) {
        int last = -1;
        for (int i = 0; i < nums.size(); ++i) {
            if (nums[i] == 1) {
                if (i - last - 1 < k) return false;
                last = i;
            }
        }
        return true;
    }
};
```

---

### 📚 Interview Strategy

- **Explain your intuition first**: “We only care about the distance between consecutive 1s.”  
- **Show the code step by step** on a whiteboard, emphasizing the `-1` offset.  
- **Discuss edge cases**: empty array, all zeros, `k = 0`, single element.  
- **Time & space analysis**: Highlight the linear time and constant space.  
- **Optional twist**: Ask how to handle streaming input or if the array is too large.

---

### 🌟 Bonus Tips

| Tip | Why It Helps |
|-----|--------------|
| **Use meaningful names** (`lastOneIdx` vs `c`) | Improves readability for the interviewer. |
| **Early exit** | Shows you can stop at the first failure, saving time. |
| **Unit tests** | Demonstrate robustness (e.g., `[1,0,1], k=2` → false). |
| **Mention the “bad” pitfalls** | Shows you’ve thought about potential errors. |

---

### 🎯 SEO Keywords

- LeetCode 1437  
- Check If All 1's Are at Least Length K Places Away  
- Java solution LeetCode 1437  
- Python solution LeetCode 1437  
- C++ solution LeetCode 1437  
- coding interview array problem  
- interview preparation coding  
- array spacing algorithm  
- binary array interview question  

---

## 9. Final Thought

LeetCode 1437 may seem simple, but it’s a *micro‑masterclass* in array traversal, off‑by‑one handling, and clean coding. Master it, write a few unit tests, and you’ll be ready to ace the question in any coding interview. Happy coding! 🚀