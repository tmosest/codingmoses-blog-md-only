---
title: LeetCode 1775. Equal Sum Arrays With Minimum Number of Operations - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Equal‑Sum Arrays with Minimum Number of Operations  
**A full‑stack interview solution (Java / Python / C++) + a “Good‑Bad‑Ugly” blog post**

> **SEO Tags** – Leetcode, Equal Sum Arrays, Java, Python, C++, greedy algorithm, interview question, algorithm interview, data structures, job interview, coding interview, data‑structures‑and‑algorithms, interview preparation

---

## 1. Problem Recap

> **Leetcode #1775 – Equal Sum Arrays With Minimum Number of Operations**  
> **Difficulty:** Medium

You are given two integer arrays `nums1` and `nums2`.  
Each element is in the range `[1, 6]`. In one operation you can change *any* element in *any* array to any value from `1` to `6`.  
Return the **minimum** number of operations needed to make the sum of `nums1` equal to the sum of `nums2`.  
If it is impossible, return `-1`.

| Example | Input | Output | Explanation |
|---------|-------|--------|-------------|
| 1 | `nums1 = [1,2,3,4,5,6]`, `nums2 = [1,1,2,2,2,2]` | `3` | Change `nums2[0] → 6`, `nums1[5] → 1`, `nums1[2] → 2`. |
| 2 | `nums1 = [1,1,1,1,1,1,1]`, `nums2 = [6]` | `-1` | Impossible – we cannot decrease the sum of `nums1` enough or increase `nums2` enough. |
| 3 | `nums1 = [6,6]`, `nums2 = [1]` | `3` | Reduce two `6`s to `2` and increase `1` to `4`. |

**Constraints**

- `1 ≤ nums1.length, nums2.length ≤ 10⁵`
- `1 ≤ nums1[i], nums2[i] ≤ 6`

The problem looks deceptively simple – you only need to think about *how many* changes, not *which* elements. This observation leads to a very fast greedy solution.

---

## 2. The Core Idea

1. **Compute the current sums.**  
   `sum1 = Σ(nums1)`, `sum2 = Σ(nums2)`.
2. **If the sums are already equal → 0 operations.**  
3. **If one array’s maximum possible sum is still smaller than the other’s minimum possible sum → impossible (`-1`).**  
   (e.g. `len(nums1)*6 < len(nums2)*1` or vice‑versa.)
4. **Otherwise, let the array with the larger sum be `big`, the other be `small`.**  
   We will *decrease* values in `big` **or** *increase* values in `small`.  
   The difference `diff = sumBig - sumSmall` is the total amount we must close.

5. **Count the “power” of every element** – how much it can contribute to closing the difference in one operation:
   - For an element `x` in `big`, we can **decrease** it to `1`, so the maximum decrease is `x-1`.  
   - For an element `y` in `small`, we can **increase** it to `6`, so the maximum increase is `6-y`.  
   Store these in a frequency array `freq[1..5]` where `freq[d]` = how many operations can close **exactly** `d` units of difference.

6. **Greedy:** Use the largest possible changes first (5, 4, 3, 2, 1).  
   For each `d` from 5 down to 1:  
   ```
   use = min( freq[d], ceil(diff / d) )
   operations += use
   diff -= use * d
   if diff <= 0: break
   ```

Because each operation removes as much difference as possible, the greedy approach is optimal.

The algorithm runs in **O(n)** time (we just traverse both arrays once) and uses **O(1)** extra space (`freq[6]`).

---

## 3. Full Implementations

Below you’ll find clean, well‑commented code for **Java, Python, and C++**.  
All three follow the same algorithmic outline.

### 3.1 Java (O(n) solution)

```java
// 1775. Equal Sum Arrays With Minimum Number of Operations
// Java 17, O(n) solution – frequency array + greedy
class Solution {
    public int minOperations(int[] nums1, int[] nums2) {
        int sum1 = 0, sum2 = 0;
        for (int x : nums1) sum1 += x;
        for (int x : nums2) sum2 += x;

        // 1. sums already equal
        if (sum1 == sum2) return 0;

        // 2. impossible case
        int len1 = nums1.length, len2 = nums2.length;
        if (len1 * 6 < len2 || len2 * 6 < len1) return -1;

        // 3. make sure sum1 > sum2, otherwise swap roles
        if (sum1 < sum2) {
            return minOperations(nums2, nums1);
        }

        int diff = sum1 - sum2;   // positive difference we need to close
        int[] freq = new int[6];   // freq[1..5] – possible change size

        // 4. collect power of each element
        for (int x : nums1) {        // big array
            freq[x - 1]++;            // decrease x -> 1
        }
        for (int y : nums2) {        // small array
            freq[6 - y]++;            // increase y -> 6
        }

        // 5. greedy from biggest possible change
        int ops = 0;
        for (int d = 5; d >= 1; d--) {
            if (diff <= 0) break;
            int usable = diff / d;                    // how many full d‑ops fit
            if (diff % d != 0) usable++;              // +1 if remainder
            int take = Math.min(freq[d], usable);
            ops += take;
            diff -= take * d;
        }
        return ops;
    }
}
```

> **Key points**  
> • The frequency array has only 5 entries (index 0 unused).  
> • No extra data structures such as heaps – this keeps memory usage negligible.

---

### 3.2 Python (Python 3.10, O(n) solution)

```python
# 1775. Equal Sum Arrays With Minimum Number of Operations
# Python 3.10 – O(n) greedy solution

def minOperations(nums1: list[int], nums2: list[int]) -> int:
    sum1, sum2 = sum(nums1), sum(nums2)

    if sum1 == sum2:
        return 0

    # impossible check
    if len(nums1) * 6 < len(nums2) or len(nums2) * 6 < len(nums1):
        return -1

    # ensure nums1 has the larger sum
    if sum1 < sum2:
        return minOperations(nums2, nums1)

    diff = sum1 - sum2
    freq = [0] * 6                # freq[1..5] – amount a single op can close

    for x in nums1:               # big array: max decrease x-1
        freq[x - 1] += 1
    for y in nums2:               # small array: max increase 6-y
        freq[6 - y] += 1

    ops = 0
    for d in range(5, 0, -1):     # greedily use biggest changes
        if diff <= 0:
            break
        use = min(freq[d], (diff + d - 1) // d)   # ceil(diff/d)
        ops += use
        diff -= use * d

    return ops
```

---

### 3.3 C++ (O(n) solution)

```cpp
// 1775. Equal Sum Arrays With Minimum Number of Operations
// C++20, O(n) greedy solution – frequency array
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minOperations(vector<int>& nums1, vector<int>& nums2) {
        long long sum1 = 0, sum2 = 0;
        for (int x : nums1) sum1 += x;
        for (int x : nums2) sum2 += x;

        if (sum1 == sum2) return 0;

        int n1 = nums1.size(), n2 = nums2.size();
        if (n1 * 6LL < n2 || n2 * 6LL < n1) return -1;

        if (sum1 < sum2) {                      // swap to make sum1 > sum2
            return minOperations(nums2, nums1);
        }

        long long diff = sum1 - sum2;           // positive diff
        int freq[6] = {0};

        for (int x : nums1) freq[x - 1]++;       // big array – can drop to 1
        for (int y : nums2) freq[6 - y]++;       // small array – can rise to 6

        int ops = 0;
        for (int d = 5; d >= 1 && diff > 0; --d) {
            int use = min(freq[d], (int)((diff + d - 1) / d)); // ceil
            ops += use;
            diff -= 1LL * use * d;
        }
        return ops;
    }
};
```

> All three codes are identical in logic, just different syntax.  
> Compile with `javac Solution.java`, `python3 solution.py`, `g++ -std=c++20 solution.cpp -O2 -o solution`.

---

## 4. “Good – Bad – Ugly” Analysis

### 4.1 Good

| Why it’s good | What you’ll brag about in your interview |
|---------------|----------------------------------------|
| **O(n) time** – linear in the total number of elements.  
| **O(1) space** – only a 6‑element frequency array.  
| **No heap / no sorting** – the algorithm is pure counting.  
| **Deterministic** – no randomness or tie‑breakers.  
| **Extremely readable** – the code is just a few loops and an array. |

These properties translate into a *fast, reliable solution* that works on all edge‑cases, and most importantly, it is **easy to explain**. That’s a huge plus in interviews.

---

### 4.2 Bad (the potential pitfalls)

| Pitfall | How to avoid it |
|---------|-----------------|
| **Incorrect impossible check** – forgetting the *length × 6* vs. *length × 1* logic means you may return a wrong answer for arrays that cannot reach the required sum. | Always perform the impossibility test **before** swapping the arrays. |
| **Mixing signs** – if you always assume `sum1 > sum2` but forget to swap, the diff becomes negative and the greedy loop won’t work. | Swap the arrays if `sum1 < sum2`, or equivalently, always treat `big` as the one with the larger sum. |
| **Integer overflow** – although the sums are bounded by `6 * 10⁵`, when you sum many numbers you should use `long`/`long long`. | In Java use `long`, in C++ `long long`, in Python the built‑in int is unbounded. |
| **Zero‑indexing freq** – make sure you never access `freq[0]` (we only need 1..5). | Initialise `int[] freq = new int[6]`; ignore index 0. |
| **Ceiling division** – forgetting the `+ (diff % d == 0 ? 0 : 1)` part may lead to too many operations. | Use `(diff + d - 1) / d` to compute `ceil(diff/d)` safely. |

---

### 4.3 Ugly – Why the problem feels harder than it is

1. **The “power of an element” is non‑obvious.**  
   At first glance you might think you need to pick which element to change.  
   The key insight is that you only care *how many* units you can close per operation, not *which* element.  
   Remember that every element can either shrink to `1` or grow to `6`.

2. **Balancing the two arrays.**  
   The decision to always operate on the *larger* array is simple, but you have to remember to swap the roles if necessary.

3. **Ceiling division inside the greedy loop.**  
   Many people use `diff / d` and forget the +1 when `diff % d != 0`.  
   The trick is `ceil(diff/d) = diff / d + (diff % d ? 1 : 0)`.

4. **Edge‑case “impossible” check** – some interviewers might not mention it.  
   It is easy to overlook that `len(nums1)*6` could still be less than `len(nums2)*1`.

5. **Testing on huge arrays** – the solution is linear, but you must not accidentally use an `O(n log n)` data structure such as a heap.  
   That will still pass on Leetcode, but will cost you a lot of runtime.

---

## 5. Alternative Approach (Priority Queue)

If you prefer to *actually* keep the most promising elements in a priority queue, the algorithm still works, but it is **O((n1 + n2) log(n1 + n2))** and consumes more memory.

**High‑level steps**

1. Put every element of the smaller sum array into a max‑heap (`+5` potential).  
2. Put every element of the larger sum array into a min‑heap (`-5` potential).  
3. While `diff > 0`, pop the best candidate from the appropriate heap, change it to the extreme (`1` or `6`), push the new value back, update `diff`, and count an operation.  
4. If the heap tops are already at their extremes (`1` in min‑heap or `6` in max‑heap) and `diff` still > 0 → impossible.

This approach is conceptually easy for interviewers to see but is slower and uses more memory.

---

## 6. How to Prepare for This Question

| Topic | Why it matters | Resources |
|-------|----------------|-----------|
| **Greedy Algorithms** | The core solution is greedy: “use the largest change first”. Practice with interval covering, coin change, activity selection. | *Cracking the Coding Interview*, “Greedy” section on LeetCode |
| **Counting / Frequency Arrays** | You’ll need to think in terms of “possible change size” rather than element identity. | *Algorithms on LeetCode* – “Counting Sort”, “Prefix Sum” |
| **Edge‑Case Thinking** | The impossible check and sign handling are subtle. Practice corner cases on arrays of varying lengths. | `Array Bounds` section on *Exercism* or *HackerRank* |
| **Mathematical Intuition** | The `ceil` division trick is common in many interview questions. | `Ceiling Division` article on GeeksforGeeks |
| **Time Complexity Mastery** | Understand when `O(n log n)` vs `O(n)` matters. | *Big‑O Cheat Sheet* (LeetCode) |
| **Testing** | LeetCode allows you to create huge test cases to confirm linearity. | `Time Complexity Analysis` in *LeetCode Discuss* |

### Quick study plan (7 days)

| Day | Focus | Practice |
|-----|-------|----------|
| 1 | Read the problem statement slowly, paraphrase in your own words. | Leetcode 1775 |
| 2 | Write the frequency array solution from scratch. | Solution above |
| 3 | Explain each step out loud; teach it to a rubber duck. | “Rubber Duck Debugging” technique |
| 4 | Try the priority‑queue variant; compare runtimes. | Leetcode “Time Complexity” tab |
| 5 | Write test cases: random, all same numbers, impossible cases. | `assert` statements in Python |
| 6 | Mock interview with a friend: explain the solution in 5‑minute. | InterviewBit mock interview |
| 7 | Review Good‑Bad‑Ugly analysis, jot key points on a cheat sheet. | Personal notes |

---

## 7. Conclusion

- The **O(n)** greedy solution using a frequency array is the *gold standard* for 1775 on Leetcode.  
- It showcases linearity, constant memory, and a clean counting approach—ideal for any software engineering interview.  
- Remember the few pitfalls and the impossible‑check logic; that will set you apart from candidates who overlook edge‑cases.

Good luck! And if you’re stuck, just remember: **count the amount you can close per operation, pick the largest, and repeat**. That’s the winning formula. 🚀

--- 

**Bonus**: Here’s a quick snippet to run all three implementations on the same test data, to make sure they all agree:

```cpp
// solution.cpp – test harness for all three implementations
#include <bits/stdc++.h>
using namespace std;

// (Insert C++ Solution class here)

// Python solution as function pointer – omitted for brevity

int main() {
    vector<int> a{3,2,3,2};
    vector<int> b{3,5,3,4};
    Solution s;
    cout << s.minOperations(a, b) << endl; // expected 2
}
```

Compile and run – the result should match the Leetcode expected output.

Happy coding, and may your interview be *fast, clean, and edge‑case‑proof*! 🌟

--- 

*Feel free to drop a comment if you need more explanation or have a variant of this problem you’d like to discuss.*