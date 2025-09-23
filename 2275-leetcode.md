---
title: LeetCode 2275. Largest Combination With Bitwise AND Greater Than Zero - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## LeetCode 2275 – Largest Combination With Bitwise AND Greater Than Zero  
**Languages:** Java | Python | C++  
**Difficulty:** Medium  
**Interview Relevance:** Bit‑wise operations, counting, and array manipulation – a staple for software‑engineering coding interviews.  

---

### TL;DR  
To get the largest subset of numbers whose bitwise AND is > 0, count how many numbers have each bit set.  
The answer is the maximum count among all 32 bits.  

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n × 32)** | **O(1)** |
| Python | **O(n × 32)** | **O(1)** |
| C++ | **O(n × 32)** | **O(1)** |

---

## Why This Problem Rocks the Interview

* **Shows understanding of bit‑wise operators** – the heart of many low‑level and systems‑programming questions.  
* **Requires clever observation** – you don’t need to examine all subsets; a single pass per bit is enough.  
* **Runs in linear time** – perfect for large inputs (up to 10⁵).  

---

## Solution Overview (The “Easiest” One)

1. **Iterate over all 32 bits** (0 … 31).  
2. For each bit, **count how many numbers contain that bit** (`candidate & (1 << bit)`).  
3. Keep the **maximum count** across all bits – that’s the size of the largest valid combination.  

The intuition: If every number in a combination has the same bit set, the AND of all numbers will also contain that bit and therefore be > 0. Conversely, if a bit isn’t shared by enough numbers, the AND will drop to 0.  

---

## Code Implementation

### Java

```java
import java.util.*;

public class Solution {
    public int largestCombination(int[] candidates) {
        int max = 0;
        // there are only 32 bits in a Java int
        for (int bit = 0; bit < 32; bit++) {
            int count = 0;
            int mask = 1 << bit;
            for (int num : candidates) {
                if ((num & mask) != 0) count++;
            }
            if (count > max) max = count;
        }
        return max;
    }

    public static void main(String[] args) {
        Solution s = new Solution();
        int[] a = {16, 17, 71, 62, 12, 24, 14};
        System.out.println(s.largestCombination(a)); // 4
    }
}
```

### Python

```python
class Solution:
    def largestCombination(self, candidates: list[int]) -> int:
        max_cnt = 0
        for bit in range(32):
            mask = 1 << bit
            cnt = sum(1 for num in candidates if num & mask)
            if cnt > max_cnt:
                max_cnt = cnt
        return max_cnt


# Demo
if __name__ == "__main__":
    sol = Solution()
    arr = [16, 17, 71, 62, 12, 24, 14]
    print(sol.largestCombination(arr))   # 4
```

### C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int largestCombination(vector<int>& candidates) {
        int best = 0;
        for (int bit = 0; bit < 32; ++bit) {
            int mask = 1 << bit;
            int cnt = 0;
            for (int num : candidates)
                if (num & mask) ++cnt;
            best = max(best, cnt);
        }
        return best;
    }
};

int main() {
    Solution s;
    vector<int> a = {16, 17, 71, 62, 12, 24, 14};
    cout << s.largestCombination(a) << endl;   // 4
}
```

---

## Step‑by‑Step Explanation (For Interviewers)

1. **Why 32 bits?**  
   All given numbers fit into a 32‑bit signed integer (`1 ≤ candidates[i] ≤ 10⁷`). We can safely iterate only 32 times.

2. **Counting with a mask**  
   `mask = 1 << bit` creates a binary number with only the *bit‑th* position set.  
   `num & mask` is non‑zero iff that bit is set in *num*.

3. **Maximize the count**  
   The largest possible AND is achieved when we pick the bit that appears most often. The size of that subset is exactly that count.

4. **Time & Space**  
   *Time:* 32 × n operations → **O(n)**.  
   *Space:* Only a few integer variables → **O(1)**.

---

## Good, Bad & Ugly of This Problem

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Conceptual clarity** | The bit‑wise insight is elegant and easy to explain. | Requires awareness that *any* common bit suffices – not obvious at first glance. | Naïve solutions (checking all subsets) are exponential; a lot of wasted effort. |
| **Efficiency** | O(n) solution runs fast even for 10⁵ inputs. | The 32‑bit constant might be confusing for those thinking in terms of 64‑bit numbers. | Implementations that pre‑compute bit frequencies incorrectly (e.g., using a `HashMap` per bit) add unnecessary overhead. |
| **Edge cases** | Works for arrays of size 1 or all identical numbers. | Needs to handle the case where all numbers are zero – but constraints forbid zeros. | Failing to use `long` in languages with 32‑bit `int` overflow could break the solution for values > 2³¹‑1 (not needed here). |
| **Interview impact** | Demonstrates concise thinking and bit manipulation. | A candidate may over‑engineer with recursion or DP. | Misunderstanding that the AND of a single number is that number can cause off‑by‑one errors. |

---

## SEO‑Optimized Blog Article

> **Title:** *Cracking LeetCode 2275 – Largest Combination With Bitwise AND > 0 (Java, Python, C++)*  
> **Meta Description:** Learn the fastest solution to LeetCode 2275, a medium‑difficulty bitwise interview question. Get Java, Python, and C++ code, step‑by‑step reasoning, and interview tips to ace your job interview.

---

### 1. Introduction

When prepping for software‑engineering interviews, you’ll frequently encounter **bitwise AND** problems. One popular LeetCode challenge is **2275 – Largest Combination With Bitwise AND Greater Than Zero**. In this post, we’ll:

- Break down the problem statement
- Present a clear, O(n) solution
- Provide full Java, Python, and C++ implementations
- Discuss why this question is a gold‑mine for interviewers
- Offer “good, bad, ugly” insights and interview tips

---

### 2. Problem Restatement

> **Given:** An array of positive integers `candidates`.  
> **Task:** Find the size of the largest subset whose bitwise AND is **greater than 0**.  
> **Return:** The maximum size.

---

### 3. Intuition & Key Insight

For the AND of a set to be > 0, there must be at least one bit that is **set in every element** of that set.  
Therefore, the problem reduces to:

> *Find the bit that appears in the most numbers and return that count.*

---

### 4. Algorithm in Pseudocode

```
maxSize = 0
for bit from 0 to 31:
    count = 0
    mask = 1 << bit
    for num in candidates:
        if num & mask:
            count += 1
    maxSize = max(maxSize, count)
return maxSize
```

---

### 5. Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time | O(n × 32) → O(n) | O(n × 32) → O(n) | O(n × 32) → O(n) |
| Space | O(1) | O(1) | O(1) |

---

### 6. Full Code Snippets

*See the earlier “Code Implementation” section for clean, commented code in each language.*

---

### 7. Why Interviewers Love This Question

- **Bitwise mastery** – demonstrates comfort with low‑level operators.  
- **O(n) solution** – tests ability to transform a combinatorial problem into a counting one.  
- **Clear reasoning** – candidates can articulate a short, elegant proof of correctness.  

---

### 8. Good, Bad, Ugly

- **Good:** Straightforward, fast, and easy to explain.  
- **Bad:** The 32‑bit assumption can trip up people if they’re not mindful of integer size.  
- **Ugly:** Brute‑force subset generation leads to exponential time—an easy pitfall for unprepared candidates.

---

### 9. Interview Tips

1. **State the key insight first** – “We need a common bit across all numbers in a subset.”  
2. **Walk through the algorithm** – mention the bit mask and the two loops.  
3. **Discuss edge cases** – e.g., single‑element arrays, identical numbers.  
4. **Time & space trade‑offs** – confirm that we’re doing O(n) and O(1).  
5. **Show the code** – hand‑write the core loops; readability matters.

---

### 10. Final Thoughts

Mastering LeetCode 2275 not only boosts your portfolio but also shows recruiters you can spot patterns and write efficient code. Keep this solution in your interview playbook, and you’ll handle any bitwise AND question with confidence.

---

### 11. Call to Action

Want more interview‑ready solutions?  
- **Subscribe** to our newsletter for weekly coding challenges.  
- **Download** our interview‑prep guide for a deeper dive into bitwise problems.  
- **Share** your own approach in the comments below!

---

**Happy coding, and good luck on your next interview!**

--- 

*Word count:* ~1,200  
*Keywords:* LeetCode 2275, bitwise AND, interview question, Java solution, Python solution, C++ solution, job interview, software engineering, algorithmic thinking

--- 

This completes the full, interview‑ready guide and an SEO‑friendly blog post to help you showcase this LeetCode challenge in your job hunt. Good luck! 🚀
