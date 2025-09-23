---
title: LeetCode 1567. Maximum Length of Subarray With Positive Product - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1567. Maximum Length of Subarray With Positive Product  
### Java, Python & C++ Solutions + SEO‑Optimized Blog Post

---

## 📄 Problem Overview  
Given an integer array `nums`, find the **maximum length** of a **contiguous subarray** whose product is **positive**.  
A subarray is a consecutive slice of the array; it can contain any number of elements (including zero).

### Constraints
| | |
|---|---|
| `1 ≤ nums.length ≤ 10⁵` | `-10⁹ ≤ nums[i] ≤ 10⁹` |

### Why This Problem Rocks  
- **Real‑world relevance**: Similar to finding the longest stretch of “good” data in time‑series or finance.  
- **Interview gold‑mine**: Many interviewers ask variations of this to test understanding of **DP**, **greedy** and **edge‑case handling**.  
- **LeetCode 1567**: High traffic, medium difficulty – perfect for building a portfolio.  

---

## 🔍 Solution Insight – One‑Pass O(n), O(1) Space

### Key Observations

1. **Zero is a reset** – the product becomes 0, so we start counting from the next element.  
2. **Negative numbers flip parity** – every time we see a negative, a positive product turns negative and vice‑versa.  
3. **Maintain two lengths**  
   - `posLen` – length of the longest subarray ending at current index with a **positive** product.  
   - `negLen` – length of the longest subarray ending at current index with a **negative** product.  

   Updating them depends on the sign of the current element.

### Transition Rules

| Current value | Effect on `posLen` | Effect on `negLen` |
|---|---|---|
| `0` | reset to `0` | reset to `0` |
| Positive | `posLen += 1` | `negLen = negLen > 0 ? negLen + 1 : 0` |
| Negative | swap `posLen` & `negLen` (after adding `1` to each) | – |

The answer is the maximum `posLen` seen during the scan.

### Complexity

- **Time**: `O(n)` – single pass over the array.  
- **Space**: `O(1)` – only a handful of integer variables.

---

## 📦 Code Implementations

### 1️⃣ Java (LeetCode‑style)

```java
class Solution {
    public int getMaxLen(int[] nums) {
        int posLen = 0;   // length of positive product subarray
        int negLen = 0;   // length of negative product subarray
        int best   = 0;   // global best

        for (int x : nums) {
            if (x == 0) {
                posLen = 0;
                negLen = 0;
            } else if (x > 0) {
                posLen++;
                negLen = negLen > 0 ? negLen + 1 : 0;
            } else {                // x < 0
                int oldPos = posLen;
                posLen = negLen > 0 ? negLen + 1 : 0;
                negLen = oldPos + 1;
            }
            best = Math.max(best, posLen);
        }
        return best;
    }
}
```

### 2️⃣ Python (LeetCode‑style)

```python
class Solution:
    def getMaxLen(self, nums: List[int]) -> int:
        pos_len = 0
        neg_len = 0
        best = 0

        for x in nums:
            if x == 0:
                pos_len, neg_len = 0, 0
            elif x > 0:
                pos_len += 1
                neg_len = neg_len + 1 if neg_len > 0 else 0
            else:                 # negative
                pos_len, neg_len = (neg_len + 1 if neg_len > 0 else 0), pos_len + 1
            best = max(best, pos_len)

        return best
```

### 3️⃣ C++ (LeetCode‑style)

```cpp
class Solution {
public:
    int getMaxLen(vector<int>& nums) {
        int posLen = 0, negLen = 0, best = 0;

        for (int x : nums) {
            if (x == 0) {
                posLen = negLen = 0;
            } else if (x > 0) {
                posLen += 1;
                negLen = negLen > 0 ? negLen + 1 : 0;
            } else { // negative
                int oldPos = posLen;
                posLen = negLen > 0 ? negLen + 1 : 0;
                negLen = oldPos + 1;
            }
            best = max(best, posLen);
        }
        return best;
    }
};
```

All three snippets run in **O(n)** time and **O(1)** space, matching the optimal solution on LeetCode.

---

## 📖 Blog Post – “The Good, The Bad, and the Ugly of 1567”

### Title (SEO‑Optimized)  
**“Mastering LeetCode 1567 – The Good, Bad & Ugly of the Maximum Length Subarray with Positive Product”**

> *Keywords*: LeetCode 1567, maximum length subarray, positive product, interview prep, Java Python C++ solution, DP, greedy algorithm, job interview.

---

### Introduction  

If you’re hunting for that “next great interview problem” to crack, look no further than **LeetCode 1567**. It’s a classic *array + parity* puzzle that tests both *time‑complexity awareness* and *edge‑case thinking*. In this post we’ll break down the problem, explore the pros and cons of common approaches, and finally deliver **three clean, production‑ready implementations**.

---

### The Good – Why This Problem Is a Goldmine

| ✅ Benefit | Explanation |
|---|---|
| **Simple Input** | Just a single array – no nested structures. |
| **Clear Goal** | Longest subarray with a positive product – an easy-to‑state objective. |
| **Multiple Solution Paths** | You can solve it with DP, greedy, or even divide‑and‑conquer – great for showing breadth. |
| **High Relevance** | The logic of “reset on zero” and “flip on negative” appears in many real‑world scenarios (signal processing, stock analysis, etc.). |
| **LeetCode Traffic** | High search volume → perfect for showcasing on your portfolio or GitHub. |

---

### The Bad – Common Pitfalls

1. **Misunderstanding the Product Sign**  
   *Many beginners mistakenly think a negative number flips *both* lengths.*  
2. **Ignoring Zeroes**  
   *Skipping the reset leads to incorrect subarray lengths.*  
3. **Extra Space Complexity**  
   *Some DP solutions use two arrays (`dpPos`, `dpNeg`) – unnecessary when a single pass suffices.*  
4. **Edge Cases**  
   *Single negative number, all zeros, or array length 1 – always test these before submitting.*

---

### The Ugly – Inefficient or Over‑Engineered Approaches

| Approach | Why It’s Ugly |
|---|---|
| **Brute‑Force O(n²)** | Enumerating all subarrays, recomputing product each time – far too slow for `10⁵` length. |
| **Full DP Tables** | `dpPos[i]`, `dpNeg[i]` arrays → O(n) space that can be avoided. |
| **Recursive Backtracking** | Deep recursion can hit stack limits on large inputs. |
| **Mis‑Typed Sign Check** | Using `abs(x) % 2` instead of `x < 0` can lead to overflow when `x = -10⁹`. |

---

### Step‑by‑Step Walkthrough (Dry Run)

Consider `nums = [1, -2, -3, 4]`:

| idx | value | posLen | negLen | best |
|---|---|---|---|---|
| 0 | 1 | 1 | 0 | 1 |
| 1 | -2 | 0 | 2 | 1 |
| 2 | -3 | 3 | 0 | 3 |
| 3 | 4 | 4 | 0 | 4 |

The maximum positive product subarray length is **4** – the entire array.

---

### Code‑Ready Implementations

*(Provided earlier in this document)*  
> Each solution uses a single pass, keeps only three integers, and runs in linear time.

---

### SEO Take‑away

- **Use Headers with Keywords**: “LeetCode 1567”, “maximum length subarray”, “positive product”.
- **Insert Code Snippets**: Search engines love code‑rich content.
- **Add Meta Description**: “Solve LeetCode 1567 in Java, Python, and C++ with an O(n) greedy algorithm. Read the full article to master DP and greedy tricks for job interviews.”
- **Link Back**: Embed a link to the LeetCode problem and to your GitHub repository.

---

### Closing Thoughts

Mastering **LeetCode 1567** demonstrates:

- Ability to *detect and exploit patterns* (sign flips, resets).  
- *Space‑time trade‑off* intuition (O(1) vs O(n)).  
- Versatility across languages (Java, Python, C++).  

Share these implementations on your portfolio, discuss the trade‑offs in your interview, and you’ll stand out as a **well‑rounded algorithmic thinker** ready for the next tech job.

Happy coding, and good luck on your interview journey! 🚀

---