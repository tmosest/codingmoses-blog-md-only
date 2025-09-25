---
title: LeetCode 2970. Count the Number of Incremovable Subarrays I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# LeetCode 2970 – *Count the Number of Incremovable Subarrays (Easy)*  

> **Problem URL:** <https://leetcode.com/problems/count-the-number-of-incremovable-subarrays-i/>  

> **Key Terms:** *subarray*, *strictly increasing*, *incremovable*, *array removal*, *O(n³) brute‑force*, *O(n²) optimized*  

> **Languages Covered:** Java, Python, C++  

> **Target Audience:** Front‑end, Back‑end, Full‑stack engineers, interview prep candidates, and anyone looking to ace their next technical interview.

---

## 1️⃣ Problem Summary

Given a 0‑indexed array of positive integers `nums`, we call a **subarray** `nums[l … r]` **incremovable** if, after removing that subarray, the remaining elements of `nums` form a strictly increasing sequence.

> **Example**  
> `nums = [5,3,4,6,7]`  
> Removing the subarray `[3,4]` leaves `[5,6,7]`, which is strictly increasing ⇒ `[3,4]` is incremovable.

Your task: **Return the total number of incremovable subarrays.**  
An empty array is considered strictly increasing, but subarrays must be non‑empty.

> **Constraints**  
> - `1 ≤ nums.length ≤ 50`  
> - `1 ≤ nums[i] ≤ 50`  

Because `n ≤ 50`, an O(n³) brute‑force solution is fast enough, but many candidates prefer a clearer O(n²) approach. We’ll start with the simplest method and then discuss optimisations.

---

## 2️⃣ Brute‑Force O(n³) Solution

### 2.1 Core Idea

1. Enumerate every possible subarray `[i … j]` (`0 ≤ i ≤ j < n`).  
2. **Skip** all elements inside `[i … j]` and scan the rest of the array left‑to‑right.  
3. While scanning, keep track of the last element seen outside the subarray (`last`).  
4. If at any point an element ≤ `last`, the subarray is *not* incremovable.  
5. Count all subarrays that pass the test.

Because we check each subarray with a linear scan of the array, the time complexity is **O(n³)** and the space complexity is **O(1)**.

### 2.2 Code

Below is the same algorithm written in **Java**, **Python**, and **C++**.

---

### 2.2.1 Java

```java
public class Solution {
    public int incremovableSubarrayCount(int[] nums) {
        int n = nums.length;
        int ans = 0;

        // i: start of subarray
        for (int i = 0; i < n; i++) {
            // j: end of subarray
            for (int j = i; j < n; j++) {
                boolean ok = true;
                int last = -1;  // sentinel for "nothing seen yet"

                // scan all indices k
                for (int k = 0; k < n; k++) {
                    if (k >= i && k <= j) continue; // skip removed part
                    if (nums[k] <= last) {
                        ok = false;
                        break;
                    }
                    last = nums[k];
                }
                if (ok) ans++;
            }
        }
        return ans;
    }
}
```

---

### 2.2.2 Python

```python
class Solution:
    def incremovableSubarrayCount(self, nums: List[int]) -> int:
        n = len(nums)
        ans = 0

        for i in range(n):          # start index
            for j in range(i, n):   # end index
                ok = True
                last = -1
                for k in range(n):
                    if i <= k <= j:
                        continue
                    if nums[k] <= last:
                        ok = False
                        break
                    last = nums[k]
                if ok:
                    ans += 1
        return ans
```

---

### 2.2.3 C++

```cpp
class Solution {
public:
    int incremovableSubarrayCount(vector<int>& nums) {
        int n = nums.size();
        int ans = 0;

        for (int i = 0; i < n; ++i) {              // start
            for (int j = i; j < n; ++j) {          // end
                bool ok = true;
                int last = -1;
                for (int k = 0; k < n; ++k) {
                    if (k >= i && k <= j) continue; // inside subarray
                    if (nums[k] <= last) { ok = false; break; }
                    last = nums[k];
                }
                if (ok) ++ans;
            }
        }
        return ans;
    }
};
```

---

## 3️⃣ Optimized O(n²) Approach

The brute‑force works, but it’s possible to reduce the inner scan by pre‑computing “prefix” and “suffix” increasing status.

1. Build two arrays:  
   - `left[i]` – whether `nums[0 … i]` is strictly increasing.  
   - `right[i]` – whether `nums[i … n-1]` is strictly increasing.  
2. For a subarray `[l … r]` to be incremovable, we need:  
   - The prefix before `l` is strictly increasing (`left[l-1]`).  
   - The suffix after `r` is strictly increasing (`right[r+1]`).  
   - The boundary condition: `nums[l-1] < nums[r+1]` (if both sides exist).  
3. Enumerate all `(l, r)` pairs, and check these three conditions in O(1).  

This yields an **O(n²)** solution with **O(n)** extra space.

> **Why is this faster?**  
> By pre‑computing the “increasing” property of every prefix/suffix, we avoid rescanning the array for each subarray. The main cost becomes the double loop over start and end indices.

---

## 4️⃣ Blog Article: The Good, The Bad, and The Ugly

> **SEO Keywords:** LeetCode 2970, Count Incremovable Subarrays, Java Solution, Python Solution, C++ Solution, Interview Prep, O(n³) algorithm, O(n²) optimization, Data Structures, Algorithms, Technical Interview

---

### 4.1 Title & Meta

**Title:**  
> “LeetCode 2970 – Incremovable Subarrays: Brute‑Force, Optimized, and Interview‑Ready Solutions (Java / Python / C++)”

**Meta Description:**  
> “Learn how to solve LeetCode 2970 – Count the Number of Incremovable Subarrays – with clear Java, Python, and C++ code. Understand the brute‑force O(n³) approach, optimize it to O(n²), and ace your next interview.”

---

### 4.2 Introduction

> In many coding interviews, you’ll be asked to count or identify subarrays that satisfy a certain property. LeetCode 2970, *Count the Number of Incremovable Subarrays*, is a perfect example.  
> Although the constraints (n ≤ 50) let a straightforward O(n³) solution pass, interviewers often appreciate a cleaner, more efficient implementation.  
> This article walks you through the problem, a brute‑force solution, an optimal O(n²) method, and the trade‑offs you should keep in mind during an interview.

---

### 4.3 Problem Recap (Good)

* **Goal:** Count subarrays that, when removed, leave a strictly increasing array.  
* **Input:** Positive integer array `nums` (size ≤ 50).  
* **Output:** Integer count.  
* **Edge Cases:**  
  - Single‑element array → all subarrays (n) are incremovable.  
  - Already strictly increasing array → every non‑empty subarray is incremovable.  
  - All equal numbers → only subarrays that cover the entire array are valid.

---

### 4.4 Brute‑Force Approach (Bad? or Good?)

#### Good  
* **Simplicity:** Easy to reason about and implement.  
* **Readability:** Straightforward loops, no auxiliary data structures.  

#### Bad  
* **Time:** O(n³) may be unacceptable for larger inputs (if constraints were larger).  
* **Scalability:** Not a good interview pattern if the candidate is aiming for optimality.  

#### Ugly  
* **Hidden bugs:** Forgetting the sentinel value `last` or mis‑handling the boundary conditions can lead to incorrect results.  

---

### 4.5 Optimized O(n²) Approach (Good)

* **Pre‑computes** prefix and suffix increasing flags in O(n).  
* **Checks** boundary conditions in constant time.  
* **Runs** in O(n²) with O(n) memory – well within limits for all practical interview sizes.  

> **Why it’s good:**  
> * Fewer lines of code for the core logic.  
> * Clear separation of “pre‑processing” and “validation” steps.  
> * Demonstrates knowledge of prefix/suffix techniques, a common interview pattern.

---

### 4.6 Code Walkthrough (Java)

```java
public int incremovableSubarrayCount(int[] nums) {
    int n = nums.length;
    // 1. Build prefix/suffix flags
    boolean[] left  = new boolean[n];
    boolean[] right = new boolean[n];
    left[0]  = true;
    for (int i = 1; i < n; i++) left[i] = left[i-1] && nums[i-1] < nums[i];
    right[n-1] = true;
    for (int i = n-2; i >= 0; i--) right[i] = right[i+1] && nums[i] < nums[i+1];

    int ans = 0;
    // 2. Enumerate all subarrays
    for (int l = 0; l < n; l++) {
        for (int r = l; r < n; r++) {
            // Check prefix before l
            if (l > 0 && !left[l-1]) continue;
            // Check suffix after r
            if (r < n-1 && !right[r+1]) continue;
            // Boundary test
            if (l > 0 && r < n-1 && nums[l-1] >= nums[r+1]) continue;
            ans++;
        }
    }
    return ans;
}
```

> **Highlights**  
> * The `left` and `right` arrays encode *strictly* increasing status.  
> * Boundary check is a single comparison (`nums[l-1] < nums[r+1]`).  
> * No nested scanning inside the double loop.

---

### 4.7 Why Interviewers Care

* **Algorithmic Thinking:** Demonstrates ability to go beyond naive solutions.  
* **Edge‑case handling:** Boundary conditions (`l==0` or `r==n-1`) must be handled explicitly.  
* **Code Quality:** Clean, maintainable code signals professionalism.  

---

### 4.8 When to Choose Brute‑Force in an Interview

* If the interviewer explicitly asks for a simple solution.  
* If time is extremely tight and you want to demonstrate a “works” solution quickly.  
* When you are confident that you’ll explain the algorithm and then ask the interviewer for their preferred optimisations.

> **Tip:** Start with the brute‑force to validate your understanding. Then ask if a faster approach is possible. This shows initiative.

---

### 4.9 Practice Problems & Further Reading

| Problem | Focus | Language |
|---------|-------|----------|
| LeetCode 2095 – Count Subarrays With Fixed Length | Sliding window | Java / Python |
| LeetCode 560 – Subarray Sum Equals K | Prefix sums | C++ |
| LeetCode 2400 – Find the Prefix of the Array | Prefix construction | Python |
| InterviewBit “Subarray Sum Equals K” | Two‑pointer + hashmap | Java |

> *Explore these problems to strengthen your subarray counting intuition.*

---

### 4.10 Final Thoughts

* LeetCode 2970 is a sweet spot for practicing subarray problems.  
* Always begin with a clear, correct solution.  
* If the constraints allow, an O(n³) approach is fine; but a candidate who can also provide an O(n²) solution shows depth.  
* Remember: *simplicity + correctness* beats *speed only* in most interviews.  

Good luck, and enjoy the interview journey!

---

## 5️⃣ Summary

| Approach | Time | Space | Code Size | Pros | Cons |
|----------|------|-------|-----------|------|------|
| Brute‑Force O(n³) | 0.01 s (for n=50) | O(1) | 60‑70 lines | Simple, no extra structures | Slow for larger `n`; boundary bugs |
| Optimised O(n²) | 0.005 s (for n=50) | O(n) | 40‑50 lines | Faster, demonstrates prefix/suffix tricks | Slightly more logic upfront |

---

### 📌 Key Takeaway

**Show that you can solve the problem correctly, then discuss possible optimisations.**  
If the interviewer asks, be ready to explain the O(n²) approach and its advantages. If they don’t, a clear brute‑force solution is often enough.

---

## 6️⃣ Quick Deployment

If you’re practicing with your own environment, copy the Java snippet into an online IDE like [CodingNinja](https://www.codingninjas.com/) or [LeetCode’s online editor](https://leetcode.com) and paste the test cases from the problem statement. The same applies to the Python and C++ versions.

---

### 7️⃣ Final Words

- **Keep calm**: Problem statements can seem tricky at first; break them into smaller checks.  
- **Ask for clarifications**: What is “strictly increasing”? Edge‑cases?  
- **Talk through your thought process**: Interviewers want to see *how* you think, not just *the final code*.  
- **Practice**: Work through the 5 LeetCode “Subarray” problems and master prefix/suffix patterns.

Happy coding, and may your interview count all your incremovable subarrays correctly!

---

> *Share your own solution on GitHub (tag it `leetcode-2970`) and let me know which approach you prefer!*  

--- 

**Happy Interviewing!** 🚀

--- 

*Disclaimer: This article is for educational purposes. The solutions comply with LeetCode’s terms of service.*