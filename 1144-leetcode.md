---
title: LeetCode 1144. Decrease Elements To Make Array Zigzag - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1144. Decrease Elements To Make Array Zigzag  
**LeetCode #1144 | Medium | Greedy**

> **Goal:**  
> Given an integer array `nums`, in one move you can decrement any element by 1.  
> Return the minimum number of moves required to transform `nums` into a *zig‑zag* array, i.e.  
> - either every even‑indexed element is greater than its adjacent elements (`A[0] > A[1] < A[2] > …`)  
> - or every odd‑indexed element is greater than its adjacent elements (`A[0] < A[1] > A[2] < …`).

---

## Why is this a classic interview problem?

* **Simple input & output** → easy to verbalize during an interview.  
* **Two alternative views** (even‑peaks vs. odd‑peaks) → shows the candidate’s ability to handle branching logic.  
* **Greedy / local decisions** → perfect for teaching *“why greedy works”*.  
* **Small constraints (≤ 1000)** → the interviewer can expect an O(n) solution without stressing about performance.  

---

## The Greedy Idea – “Fix the bad peaks”

For each index `i` you only need to know whether it is **currently a peak** with respect to its two neighbours.  
If it is not a peak, we can lower it until it becomes a valid peak.  
The cheapest way is to lower it just enough to be *strictly* smaller than the smaller of its two neighbours.

```
cost(i) = max(0, nums[i] - min(nums[i‑1], nums[i+1]) + 1)
```

*The `+1` guarantees strict inequality.*  
If the index is at the boundary (`i==0` or `i==n-1`) the missing neighbour is treated as “infinite”, i.e. the element never needs to be lowered for that side.

---

## Two independent solutions

We compute the total cost for two independent scenarios:

1. **Even indices must be peaks** (`A[0] > A[1] < A[2] > …`).  
2. **Odd indices must be peaks** (`A[0] < A[1] > A[2] < …`).

The answer is the minimum of the two totals.

Because each element is inspected once, the algorithm runs in **O(n)** time and uses **O(1)** extra space.

---

## 1. Java Implementation

```java
import java.util.*;

class Solution {
    public int movesToMakeZigzag(int[] nums) {
        int evenCost = 0;   // peaks at even positions
        int oddCost  = 0;   // peaks at odd positions
        int n = nums.length;

        for (int i = 0; i < n; i++) {
            // Determine the required lower bound from the neighbours
            int lowerBound = Integer.MAX_VALUE;
            if (i > 0)   lowerBound = Math.min(lowerBound, nums[i - 1]);
            if (i + 1 < n) lowerBound = Math.min(lowerBound, nums[i + 1]);

            // If the current element is already lower than both neighbours, no cost
            if (nums[i] > lowerBound) {
                int cost = nums[i] - lowerBound + 1;
                if ((i & 1) == 0)   // even index
                    evenCost += cost;
                else
                    oddCost  += cost;
            }
        }
        return Math.min(evenCost, oddCost);
    }
}
```

> **Key points**  
> * `Integer.MAX_VALUE` guarantees that the side with no neighbour is ignored.  
> * The bit‑op `i & 1` is a tiny speed‑up over `i % 2`.  
> * No additional arrays – space is constant.

---

## 2. Python Implementation

```python
class Solution:
    def movesToMakeZigzag(self, nums: List[int]) -> int:
        even_cost = odd_cost = 0
        n = len(nums)

        for i in range(n):
            lower = float('inf')
            if i > 0:     lower = min(lower, nums[i - 1])
            if i + 1 < n: lower = min(lower, nums[i + 1])

            if nums[i] > lower:
                cost = nums[i] - lower + 1
                if i % 2 == 0:
                    even_cost += cost
                else:
                    odd_cost += cost

        return min(even_cost, odd_cost)
```

> Python is very close to the Java logic – only syntax changes.  
> `float('inf')` plays the role of “no neighbour”.

---

## 3. C++ Implementation

```cpp
class Solution {
public:
    int movesToMakeZigzag(vector<int>& nums) {
        long long evenCost = 0, oddCost = 0;   // use long long for safety
        int n = nums.size();

        for (int i = 0; i < n; ++i) {
            int lower = INT_MAX;
            if (i > 0)       lower = min(lower, nums[i-1]);
            if (i + 1 < n)   lower = min(lower, nums[i+1]);

            if (nums[i] > lower) {
                int cost = nums[i] - lower + 1;
                if (i % 2 == 0) evenCost += cost;
                else            oddCost  += cost;
            }
        }
        return static_cast<int>(min(evenCost, oddCost));
    }
};
```

> **Why `long long`?**  
> In the worst case each element can be 1000 and we could add up to 500 × 1000 ≈ 500 000.  
> Still fits in `int`, but using `long long` guarantees no overflow if constraints grow.

---

## Good, Bad & Ugly: What Interviewers Look For

| **Aspect** | **What Works** | **What Fails** | **Takeaway** |
|------------|----------------|----------------|--------------|
| **Greedy correctness** | Reasonable explanation that lowering a bad peak cannot hurt future peaks | Ignoring the `+1` for strictness | Always double‑check inequalities |
| **Boundary handling** | Treat missing neighbours as “infinite” or skip the check | Off‑by‑one errors (`i-1 >= 0`, `i+1 < n`) | Use helper function or pre‑computed min |
| **Time complexity** | O(n) single pass | Extra O(n) arrays or nested loops | Keep to linear passes |
| **Space complexity** | O(1) auxiliary | Creating copies of the array | Remember the constraint `n ≤ 1000` is forgiving, but interviewers expect optimal |
| **Readability** | Clear variable names, comments | Magic numbers (`1`) without explanation | Use meaningful names (`evenCost`, `oddCost`, `lower`) |
| **Edge cases** | Handles single element, all equal, strictly decreasing arrays | Assumes at least two elements | Always test with `n=1` |

---

## Testing Your Solution

```python
def test():
    sol = Solution()
    assert sol.movesToMakeZigzag([1,2,3]) == 2
    assert sol.movesToMakeZigzag([9,6,1,6,2]) == 4
    assert sol.movesToMakeZigzag([3]) == 0           # single element
    assert sol.movesToMakeZigzag([5,4,3,2,1]) == 4  # already zigzag? compute
    print("All tests passed")
test()
```

Run the above for all three implementations; they should produce the same results.

---

## SEO‑Optimized Blog Article

> **Title (H1):**  
> *“Master LeetCode 1144: Decrease Elements to Make Array Zigzag – Java, Python, C++ Solutions & Interview Insights”*

> **Meta Description (≈155 chars):**  
> *Learn the greedy algorithm for LeetCode 1144 (Decrease Elements to Make Array Zigzag). Get Java, Python, C++ code, complexity analysis, edge‑case handling, and interview tips.*

---

### 1. Introduction

> **What is a Zigzag Array?**  
> A sequence that alternates between peaks and valleys: `A[0] > A[1] < A[2] > A[3] < …` or the opposite.  
> **Why is it interesting?**  
> It forces you to think locally (each element vs. neighbours) while optimizing globally (total moves).  

### 2. Problem Restatement

> Given `nums[0 … n‑1]` (1 ≤ n ≤ 1000, 1 ≤ nums[i] ≤ 1000), return the minimum number of decrement moves needed to turn the array into a zigzag.  
> One move: `nums[i] = nums[i] - 1`.  

### 3. Intuition & Greedy Insight

* A peak must be **strictly greater** than its neighbours.  
* If it is not, the cheapest fix is to lower it just enough.  
* The optimality of this local fix follows because lowering a peak cannot improve any other element’s requirement – it only potentially relaxes constraints on neighbours.

### 4. Step‑by‑Step Algorithm

1. **Initialize two counters**: `evenCost` and `oddCost`.  
2. **Loop through all indices** `i = 0 … n‑1`.  
   * Find `lowerBound = min(nums[i-1], nums[i+1])` (ignore missing neighbours).  
   * If `nums[i] > lowerBound`, compute `cost = nums[i] - lowerBound + 1`.  
   * Add `cost` to `evenCost` if `i` is even, else to `oddCost`.  
3. **Return `min(evenCost, oddCost)`**.

### 5. Complexity Analysis

| Metric | Formula | Result |
|--------|---------|--------|
| Time   | Single pass over array | **O(n)** |
| Space  | Constant auxiliary variables | **O(1)** |

### 6. Edge Cases

| Input | Why It Matters | Expected Output |
|-------|----------------|-----------------|
| `[1]` | Single element | `0` (already zigzag) |
| `[5,5,5]` | All equal | `2` (lower middle element) |
| `[10,1,10,1,10]` | Already zigzag (odd peaks) | `0` |
| `[1,1000,1,1000,1]` | Large peaks | `999` (lower second element) |

### 7. Code in Three Languages

*Java, Python, C++ snippets (see above).*  
*All share the same logic, differing only in syntax.*

### 8. Common Pitfalls in Interviews

1. **Forgetting the strict “>” condition** – leads to under‑counting moves.  
2. **Incorrect boundary handling** – `i-1 < 0` or `i+1 >= n` checks.  
3. **Using `>=` instead of `>` when comparing with neighbours** – causes infinite loops in a naive implementation.  
4. **Over‑engineering** – building new arrays or using recursion unnecessarily.

### 9. Alternative Approaches

* **Dynamic Programming** – overkill for this problem; greedy is optimal.  
* **Two‑pass solution** – first pass compute required decreases, second pass sum. Same as greedy but split into steps.  
* **Bit‑masking** – not applicable; the pattern is fixed (peaks at even/odd indices).

### 10. Conclusion

LeetCode 1144 is a perfect showcase for a clean greedy strategy.  
By focusing on local violations and correcting them with minimal effort, you can guarantee the global optimum.  
The Java, Python, and C++ implementations above run in linear time, use constant space, and handle all edge cases gracefully.

> **Ready to ace the interview?**  
> Practice the greedy pattern, understand why it works, and remember to explain the strict inequality and boundary logic.  
> Good luck!

---

### 11. Call to Action

> If you found this article helpful, **subscribe** to our newsletter for weekly interview problem walkthroughs.  
> Drop a comment with your own edge‑case tests or ask about the next LeetCode challenge.

---

## Final Checklist Before the Interview

1. **Explain the greedy idea** – not just “it works”.  
2. **Show boundary handling** – illustrate with a short diagram.  
3. **Run through a quick test case live** – e.g., `[1,2,3]`.  
4. **Mention complexities** – always keep them in mind.  
5. **Keep the code readable** – meaningful names, comments.  

With the solution, the testing routine, and the interview insights, you’re all set to turn the *Zigzag* problem from a puzzle into a confidence‑boosting interview win.