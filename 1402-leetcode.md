---
title: LeetCode 1402. Reducing Dishes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Summary**

The problem is a classic “schedule‑the‑positive‑prefix‑sum” problem.

* If you cook dishes in a *decreasing* order of their satisfaction,  
  you can keep adding a dish as long as the running prefix sum stays
  non‑negative.  
* Every time you add a new dish the *benefit* of the dish is multiplied
  by its position (1‑based).  
  The total benefit of a prefix of dishes is exactly the prefix sum
  itself.  
* Therefore, while we traverse the sorted list we keep a running
  prefix sum.  
  As soon as that prefix sum becomes negative, adding more dishes can
  only hurt the total; we stop.

The final answer is the sum of all positive prefix sums.

---

### Algorithm
1. **Sort** the array `satisfaction` in **decreasing** order.
2. Initialise `prefSum = 0`, `answer = 0`.
3. For each `s` in the sorted array  
   * `prefSum += s`  
   * If `prefSum < 0` → **break**  
   * `answer += prefSum`
4. Return `answer`.

---

### Correctness Proof  

We prove that the algorithm returns the maximum possible satisfaction.

**Lemma 1**  
Let `S` be a set of dishes chosen in some order.  
If we order the dishes of `S` in decreasing order of satisfaction, the
total satisfaction of `S` does not decrease.

*Proof.*  
The total satisfaction of `S` is  
\[
\sum_{i=1}^{k} s_{\pi(i)} \times i ,
\]
where `π` is the cooking order and `k = |S|`.  
Reordering the dishes so that `s_{π(i)} \ge s_{π(j)}` for `i < j` can only
increase each term because the multiplier `i` increases with the
position. ∎



**Lemma 2**  
Let `T` be the sorted list in decreasing order.  
Let `prefSum(i) = Σ_{j=1..i} T[j]`.  
If `prefSum(i) < 0`, then for every `j ≥ i` the total satisfaction of
any schedule that uses the first `j` dishes of `T` is **not larger** than
the schedule that uses only the first `i-1` dishes.

*Proof.*  
Adding dish `i` increases the total by `prefSum(i)`.  
If `prefSum(i) < 0`, that contribution is negative, thus any schedule
containing it can only be worse than the schedule that skips it. ∎



**Theorem**  
The algorithm outputs the maximum possible total satisfaction.

*Proof.*  
By Lemma&nbsp;1 we may assume the dishes are processed in decreasing
order.  
The algorithm keeps adding a dish as long as the running prefix sum
remains non‑negative; once it becomes negative, by Lemma&nbsp;2 any
further addition would only decrease the total.  
Hence the algorithm considers exactly the optimal set of dishes and
accumulates their correct contributions, yielding the maximum total
satisfaction. ∎



---

### Complexity Analysis

*Sorting* dominates the runtime: **O(n log n)** time.  
The algorithm uses **O(1)** extra space (apart from the input array).

---

### Reference Implementation (Python)

```python
from typing import List

class Solution:
    def maxSatisfaction(self, satisfaction: List[int]) -> int:
        # Step 1: sort in decreasing order
        satisfaction.sort(reverse=True)

        pref_sum = 0      # current prefix sum of chosen dishes
        answer = 0

        # Step 2: iterate while prefix sum stays non‑negative
        for s in satisfaction:
            pref_sum += s
            if pref_sum < 0:
                break          # adding more will only hurt
            answer += pref_sum

        return answer
```

---

### Reference Implementation (C++)

```cpp
class Solution {
public:
    int maxSatisfaction(vector<int>& satisfaction) {
        sort(satisfaction.begin(), satisfaction.end(), greater<int>());
        long long prefSum = 0, res = 0;
        for (int s : satisfaction) {
            prefSum += s;
            if (prefSum < 0) break;   // stop when prefix sum becomes negative
            res += prefSum;           // accumulate the benefit
        }
        return static_cast<int>(res);
    }
};
```

---

### Reference Implementation (Java)

```java
class Solution {
    public int maxSatisfaction(int[] satisfaction) {
        Arrays.sort(satisfaction);
        // sort in decreasing order
        for (int i = 0; i < satisfaction.length / 2; i++) {
            int tmp = satisfaction[i];
            satisfaction[i] = satisfaction[satisfaction.length - 1 - i];
            satisfaction[satisfaction.length - 1 - i] = tmp;
        }

        long prefSum = 0, res = 0;
        for (int s : satisfaction) {
            prefSum += s;
            if (prefSum < 0) break;
            res += prefSum;
        }
        return (int) res;
    }
}
```

---

This greedy algorithm is optimal and far faster than the DP solutions,
while still being very simple to understand and implement.