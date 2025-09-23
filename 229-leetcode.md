---
title: LeetCode 229. Majority Element II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. 3‑Way Majority Element – 229. Majority Element II  
### Quick Reference  
| Language | Time | Space | Complexity | Link to LeetCode |  
|----------|------|-------|-----------|------------------|  
| **Java** | O(n) | O(1) | **O(n)** | https://leetcode.com/problems/majority-element-ii/ |  
| **Python** | O(n) | O(1) | **O(n)** | https://leetcode.com/problems/majority-element-ii/ |  
| **C++** | O(n) | O(1) | **O(n)** | https://leetcode.com/problems/majority-element-ii/ |  

> **Goal** – Return every number that appears **more than** ⌊ *n* / 3 ⌋ times.

> **Why it matters** – This is the classic interview problem that tests your ability to think about *Boyer–Moore* voting, *constant‑space* algorithms and *linear‑time* tricks. It’s a perfect showcase for a candidate who can write clean, efficient code in multiple languages.

---

## 2. Code – Java / Python / C++

> *All three implementations use the extended Boyer–Moore algorithm (also called the “2‑candidate” version).*

### 2.1 Java

```java
import java.util.*;

public class Solution {
    public List<Integer> majorityElement(int[] nums) {
        int candidate1 = 0, candidate2 = 1; // arbitrary distinct values
        int count1 = 0, count2 = 0;

        // 1st pass – find possible candidates
        for (int num : nums) {
            if (num == candidate1) {
                count1++;
            } else if (num == candidate2) {
                count2++;
            } else if (count1 == 0) {
                candidate1 = num;
                count1 = 1;
            } else if (count2 == 0) {
                candidate2 = num;
                count2 = 1;
            } else {
                count1--;
                count2--;
            }
        }

        // 2nd pass – verify candidates
        count1 = 0; count2 = 0;
        for (int num : nums) {
            if (num == candidate1) count1++;
            else if (num == candidate2) count2++;
        }

        List<Integer> result = new ArrayList<>();
        int n = nums.length;
        if (count1 > n / 3) result.add(candidate1);
        if (count2 > n / 3) result.add(candidate2);
        return result;
    }
}
```

### 2.2 Python

```python
from typing import List

class Solution:
    def majorityElement(self, nums: List[int]) -> List[int]:
        # 1st pass – candidate search
        cand1, cand2 = 0, 1   # arbitrary distinct starting values
        count1, count2 = 0, 0

        for num in nums:
            if num == cand1:
                count1 += 1
            elif num == cand2:
                count2 += 1
            elif count1 == 0:
                cand1, count1 = num, 1
            elif count2 == 0:
                cand2, count2 = num, 1
            else:
                count1 -= 1
                count2 -= 1

        # 2nd pass – verification
        count1 = count2 = 0
        for num in nums:
            if num == cand1:
                count1 += 1
            elif num == cand2:
                count2 += 1

        res = []
        n = len(nums)
        if count1 > n // 3:
            res.append(cand1)
        if count2 > n // 3:
            res.append(cand2)
        return res
```

### 2.3 C++

```cpp
#include <vector>
#include <algorithm>

class Solution {
public:
    std::vector<int> majorityElement(std::vector<int>& nums) {
        int cand1 = 0, cand2 = 1;   // distinct placeholders
        int count1 = 0, count2 = 0;

        // First pass: find potential candidates
        for (int num : nums) {
            if (num == cand1) count1++;
            else if (num == cand2) count2++;
            else if (count1 == 0) { cand1 = num; count1 = 1; }
            else if (count2 == 0) { cand2 = num; count2 = 1; }
            else { count1--; count2--; }
        }

        // Second pass: verify counts
        count1 = count2 = 0;
        for (int num : nums) {
            if (num == cand1) count1++;
            else if (num == cand2) count2++;
        }

        std::vector<int> result;
        int n = nums.size();
        if (count1 > n / 3) result.push_back(cand1);
        if (count2 > n / 3) result.push_back(cand2);
        return result;
    }
};
```

---

## 3. Blog Article – “Majority Element II: The Good, The Bad, and The Ugly”

> **Meta‑Description**: Master LeetCode 229 – Majority Element II. Learn the optimal O(n) / O(1) algorithm, see Java, Python, and C++ implementations, and understand the pitfalls that can trip up interviewers. Perfect for software‑engineering job seekers.

### 3.1 Introduction  

If you’ve hit LeetCode 229 in your interview prep, you’ve probably stared at the problem statement, imagined a *hash map* solution, and wondered why the interviewer keeps asking for “linear time and constant space.”  
Majority Element II isn’t just another counting‑problem – it’s a test of your algorithmic intuition. In this article we’ll dissect the “good” (optimal algorithm), the “bad” (naïve approaches), and the “ugly” (edge‑case traps). Along the way, you’ll see ready‑to‑copy Java, Python, and C++ code that you can paste into your interview notebook.

> **Why does this matter for your career?**  
> – Interviewers love concise, space‑efficient solutions.  
> – Demonstrates mastery of classic algorithms (Boyer–Moore).  
> – Shows you can translate ideas across languages—an essential skill for full‑stack engineers.

### 3.2 Problem Recap  

> **Given** an integer array `nums` of size `n`.  
> **Return** all elements that appear more than ⌊ *n* / 3 ⌋ times.  
> **Constraints**: 1 ≤ *n* ≤ 5 × 10⁴, |*nums[i]*| ≤ 10⁹.  

> **Observations**:  
> * At most **two** numbers can satisfy “> n/3” because 3 × (n/3) = n.  
> * This is a natural extension of the classic “Majority Element” (n/2) problem.

### 3.3 The Naïve Approach – Hash Map

| Step | Operation | Complexity |
|------|-----------|------------|
| Count frequencies | `Map<Integer, Integer>` | O(n) time, O(n) space |
| Filter | iterate map | O(n) time |

```java
Map<Integer, Integer> freq = new HashMap<>();
for (int num : nums) freq.put(num, freq.getOrDefault(num, 0) + 1);
List<Integer> res = new ArrayList<>();
for (Map.Entry<Integer, Integer> e : freq.entrySet())
    if (e.getValue() > n / 3) res.add(e.getKey());
```

**Why it’s bad for interviews**  
* Uses extra memory – *O(n)*.  
* LeetCode’s follow‑up explicitly asks for O(1) space.  
* Doesn’t showcase deeper algorithmic thinking.

### 3.4 The Good – Boyer–Moore Voting (Extended)

#### 3.4.1 Intuition  

1. **Candidate selection** – We can keep track of at most two candidates (`cand1`, `cand2`) and their “votes” (`cnt1`, `cnt2`).  
2. **Vote elimination** – Whenever a new number doesn’t match either candidate and both counters are positive, we decrement both.  
3. **Proof** – If a number occurs > n/3 times, it cannot be completely eliminated because each elimination removes two numbers, and the majority must survive.  

#### 3.4.2 Two Passes  

* **Pass 1** – Find the two potential majority candidates.  
* **Pass 2** – Count their real occurrences to confirm if they indeed cross the threshold.

#### 3.4.3 Why O(1) Space?  

We never store the whole frequency map. Only two candidate variables and two counters – four integers. Memory stays constant regardless of input size.

#### 3.4.4 Edge Cases & Gotchas  

| Edge Case | What to watch out for | Fix |
|-----------|-----------------------|-----|
| Array length < 3 | Starting candidates may be the same as the only element | Initialise candidates to distinct values (`0`, `1`) before the loop. |
| All elements same | Counters will not decrement, still correct | After pass 1, the single element will survive. |
| Negative numbers | No difference – just comparisons | Use `==` for equality. |

#### 3.4.5 Implementation Snippets  

> **Java** – see code above.  
> **Python** – see code above.  
> **C++** – see code above.

All three follow the same logic, so you can comfortably switch languages on the spot.

### 3.5 The Ugly – Common Mistakes

1. **Assuming the algorithm works for n/2** – For `n/3`, you only need **two** candidates, not one.  
2. **Skipping the second validation pass** – The first pass only guarantees *possible* candidates; you must verify them.  
3. **Using `>` instead of `>=`** – The problem explicitly says “more than n/3” (strict inequality).  
4. **Initialising both candidates to the same value** – If you start with `cand1 = cand2 = 0`, the algorithm may mis‑count when the array contains only zeros.  
5. **Omitting the `else if` chain** – Mixing the conditions can lead to incorrect counter updates.

### 3.6 Testing & Edge Cases

| Test | Input | Expected | Why |
|------|-------|----------|-----|
| Single element | `[1]` | `[1]` | 1 > 1/3 |
| Two distinct | `[1,2]` | `[1,2]` | Both 1 > 2/3? No → both 1/2 > 2/3? Actually 1 > 0 | Both appear > n/3 (0) |
| No majority | `[1,2,3,4]` | `[]` | No element > 4/3 ≈ 1.33 |
| Large input | 5 × 10⁴ random numbers | O(n) runtime, constant memory | Stress test |

### 3.7 Variations & Extensions

* **k‑majority** – Generalize to “> n/k” using `k-1` candidates.  
* **Streaming data** – The same algorithm works on a data stream: keep counters, discard as you go.  
* **Parallelism** – Split array, run Boyer–Moore locally, merge candidates.  

### 3.8 Conclusion – What Interviewers Really Look For

| Trait | Evidence in Your Solution |
|-------|---------------------------|
| **Algorithmic depth** | Uses Boyer–Moore, not just hash maps |
| **Space efficiency** | Only O(1) extra memory |
| **Robustness** | Handles negatives, zeros, small arrays |
| **Code quality** | Clear, commented, multi‑language ready |
| **Communication** | Can explain reasoning in 30 seconds |

**Pro tip:** After coding, rehearse a 30‑second explanation: “We keep two candidates, decrement both when a new element comes in, and then verify counts.”  

### 3.9 Call to Action

> **Ready to ace your next coding interview?**  
> Copy the Java/Python/C++ snippets above, practice on LeetCode, and feel confident in explaining Boyer–Moore’s magic.  
> **Follow for more interview‑ready articles** – we’ll tackle sorting, DP, graph theory, and more.

---

## 4. SEO Checklist (for the blog article)

1. **Title** – “Majority Element II – LeetCode 229 | Java, Python, C++ Solutions”
2. **Meta description** – Short, keyword‑rich summary (see above).
3. **Headings** – H1, H2, H3 with target keywords (“LeetCode 229”, “Majority Element II”, “Boyer–Moore”, “O(n) time”).
4. **Internal links** – Link to related articles or your code‑pen demo page.
5. **External links** – Reference LeetCode problem page, official explanation.
5. **Alt text** – For any code screenshots or diagrams, use descriptive alt tags.
6. **Keyword density** – ~1–2 % for main keywords; natural usage.
7. **Social share** – Include share buttons; mention “share on LinkedIn, Twitter”.

With this article, not only do you solve the problem, you also position yourself as a candidate who can deliver optimized, cross‑platform code—a must‑have for today’s software‑engineering roles. Happy interviewing!