---
title: LeetCode 2137. Pour Water Between Buckets to Make Water Levels Equal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯  Leetcode 2137 – Pour Water Between Buckets to Make Water Levels Equal  
**Solution** – Binary Search (Java / Python / C++)  
**Blog** – “The Good, the Bad, and the Ugly” – SEO‑optimized interview guide  

---

### 1. Problem Recap

You have `n` buckets with integer amounts of water (`buckets[i]`).  
When you pour `k` gallons from one bucket to another, you lose `loss%` of that pour.

```
k   →  k * (loss/100)  is spilled
k'  =  k * (1 - loss/100)  actually reaches the target bucket
```

You may pour *any* amount (not necessarily an integer) between any two buckets.  
Goal: **Make all buckets contain the same amount of water and return that maximum possible amount.**

- `1 ≤ n ≤ 10^5`
- `0 ≤ buckets[i] ≤ 10^5`
- `0 ≤ loss ≤ 99`

Answers within `1e‑5` of the real value are accepted.



---

## 2. Brute‑Force Intuition (Why it’s too slow)

A naive way is to try every possible final level (e.g. by enumerating all distinct bucket amounts) and simulate pours.  
With `n = 10^5` that’s impossible (`O(n^2)` in the worst case).

Instead we can ask: **Given a target level `L`, can we reach it?**  
That turns the problem into a decision problem that can be solved in `O(n)`.

Then we can binary‑search on `L` to find the maximum feasible level.  
This gives an overall `O(n log V)` algorithm, where `V = 10^5` (the max bucket volume).

---

## 3. Decision Function

For a candidate level `L`:

```
need = 0          // total water that still needs to be added to some bucket
give = 0          // total water that must be removed from some bucket

for each bucket amount x:
    if x < L:                 // bucket needs water
        // to bring x up to L we must pour (L - x) / (1 - lossFraction)
        need += (L - x) / (1 - lossFraction)
    else if x > L:            // bucket has excess water
        give += (x - L)        // this water will be poured out
```

If `need <= give` (i.e., we have enough excess to cover the required pours after accounting for loss), then level `L` is achievable.  
We want the *maximum* `L` that satisfies this condition.

---

## 4. Binary Search

```
low  = 0
high = max(buckets)          // upper bound; cannot exceed the initial max
eps  = 1e-6                  // precision requirement

while high - low > eps:
    mid = (low + high) / 2
    if feasible(mid):
        low = mid             // we can go higher
    else:
        high = mid            // too high, shrink
return low
```

The feasibility test uses the decision function above.

---

## 5. Code – Java

```java
import java.util.*;

public class Solution {
    public double equalizeWater(int[] buckets, int loss) {
        double lossFraction = loss / 100.0;   // e.g. 80% → 0.80
        double eps = 1e-6;

        double low = 0.0;
        double high = Arrays.stream(buckets).max().getAsInt(); // upper bound

        while (high - low > eps) {
            double mid = (low + high) / 2.0;
            if (canReach(buckets, mid, lossFraction)) {
                low = mid;      // mid is feasible, try higher
            } else {
                high = mid;     // mid too high
            }
        }
        return low;
    }

    private boolean canReach(int[] buckets, double level, double lossFraction) {
        double need = 0.0; // water that must be poured in
        double give = 0.0; // water that will be poured out

        for (int x : buckets) {
            if (x < level) {
                need += (level - x) / (1.0 - lossFraction);
            } else if (x > level) {
                give += (x - level);
            }
        }
        // We need enough “give” to cover the “need” after loss
        return need <= give + 1e-12; // small epsilon for numerical safety
    }

    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.equalizeWater(new int[]{1, 2, 7}, 80));  // 2.0
        System.out.println(sol.equalizeWater(new int[]{2, 4, 6}, 50));  // 3.5
        System.out.println(sol.equalizeWater(new int[]{3, 3, 3, 3}, 40)); // 3.0
    }
}
```

---

## 6. Code – Python

```python
def equalizeWater(buckets: list[int], loss: int) -> float:
    loss_frac = loss / 100.0
    eps = 1e-6
    low, high = 0.0, max(buckets)

    def feasible(level: float) -> bool:
        need = 0.0
        give = 0.0
        for x in buckets:
            if x < level:
                need += (level - x) / (1.0 - loss_frac)
            elif x > level:
                give += (x - level)
        return need <= give + 1e-12

    while high - low > eps:
        mid = (low + high) / 2.0
        if feasible(mid):
            low = mid
        else:
            high = mid
    return low


if __name__ == "__main__":
    print(equalizeWater([1, 2, 7], 80))      # 2.0
    print(equalizeWater([2, 4, 6], 50))      # 3.5
    print(equalizeWater([3, 3, 3, 3], 40))   # 3.0
```

---

## 7. Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    double equalizeWater(vector<int>& buckets, int loss) {
        double lossFrac = loss / 100.0;
        double eps = 1e-6;
        double low = 0.0;
        double high = *max_element(buckets.begin(), buckets.end());

        auto feasible = [&](double level) -> bool {
            double need = 0.0, give = 0.0;
            for (int x : buckets) {
                if (x < level)
                    need += (level - x) / (1.0 - lossFrac);
                else if (x > level)
                    give += (x - level);
            }
            return need <= give + 1e-12;
        };

        while (high - low > eps) {
            double mid = (low + high) / 2.0;
            if (feasible(mid))
                low = mid;
            else
                high = mid;
        }
        return low;
    }
};

int main() {
    Solution sol;
    cout << fixed << setprecision(5);
    cout << sol.equalizeWater({1,2,7}, 80) << endl; // 2.00000
    cout << sol.equalizeWater({2,4,6}, 50) << endl; // 3.50000
    cout << sol.equalizeWater({3,3,3,3}, 40) << endl; // 3.00000
}
```

---

## 8. Blog Article – “The Good, the Bad, and the Ugly”

> **Title**  
> **Leetcode 2137 – Pour Water Between Buckets to Make Water Levels Equal**  
> *A Deep Dive into Binary Search, Edge‑Case Mastery, and Interview Success*  

---

### 8.1 Why This Problem Rocks for Interviews

| Aspect | Why It Matters |
|--------|----------------|
| **Quantitative reasoning** | You’re asked to reason about loss and transfer, a subtle math puzzle. |
| **Binary search** | A core algorithmic technique; employers want to see you use it in continuous spaces. |
| **Floating‑point precision** | Real‑world coding requires careful handling of `double`/`float`. |
| **Edge‑case hunting** | The constraints (loss 0‑99, big arrays) test robustness. |
| **Optimization** | O(n log V) is the sweet spot for 10^5 data points. |

> **SEO keywords**: Leetcode 2137, water bucket problem, binary search interview, Java interview solutions, Python interview coding, C++ interview algorithm, software engineer interview prep, algorithmic problem solving, loss percent transfer, job interview coding, algorithm interview questions.

---

### 8.2 Problem Breakdown

1. **Understand the pour rule**  
   When pouring `k` gallons, only `(1 - loss%) * k` arrives.  
   *Loss 80% → you effectively get 20% of the poured volume.*

2. **Goal**  
   Find the **maximum common water level** reachable by repeated pours.

3. **Observations**  
   - The final level can never exceed the maximum initial bucket.  
   - For any candidate level `L`, the total “need” to fill short buckets can be computed.  
   - The total “give” from overflowing buckets is just the sum of `(x - L)` for `x > L`.  
   - After loss, the *effective* poured amount from the overflowing buckets must cover the need.  

---

### 8.3 The Decision Function – The “Good”

**Why it works**  
- It reduces a continuous problem to a simple linear check.  
- O(n) time per check, no extra data structures.  
- Handles floating‑point with a tiny epsilon to avoid rounding errors.

**Implementation Highlights**  
- `need` uses division by `(1 - lossFraction)` because that’s the inverse of the transfer efficiency.  
- `give` is straightforward subtraction.  
- The feasibility test `need <= give` directly captures the feasibility condition.

---

### 8.4 Binary Search – The “Bad”

| Potential Pitfall | Fix |
|-------------------|-----|
| **Precision loss** | Use a very small `eps` (1e-6 or 1e-7) and `double`/`long double`. |
| **Overflow of sum** | All sums fit in `double` because max sum ≈ 10^5 * 10^5 = 10^10, safely inside double’s 53‑bit mantissa. |
| **Infinite loop** | Ensure `mid` is recalculated each iteration and stop when `high - low <= eps`. |

**Why it’s “bad”**  
Binary search on floating numbers can be mentally confusing. In interviews, you need to justify the bounds and the stopping criterion clearly; otherwise the interviewer might suspect a subtle bug.

---

### 8.5 The “Ugly” – Numerical Stability & Edge Cases

1. **Zero loss (loss = 0)**  
   The effective poured amount equals the poured amount.  
   The decision function still works: `1 - lossFraction = 1.0`.

2. **Loss = 99%**  
   Efficiency is `0.01`, so dividing by `(1 - 0.99)` → `100`.  
   Precision is still safe, but be wary of `1 - lossFraction` being extremely small. A check for `loss == 100` would cause division by zero, but the constraints stop at 99%.

3. **All buckets equal**  
   Feasibility always holds; binary search will converge quickly.

4. **Large arrays**  
   The algorithm remains fast; no nested loops.

---

### 8.6 How to Nail It in a Live Interview

1. **Read the question carefully** – confirm that you understand “only X% arrives”.  
2. **Explain the algorithm** before coding.  
3. **Show the decision function** – walk through a small example manually.  
4. **Mention precision** – discuss why you use `eps`.  
5. **Code with clarity** – avoid over‑complicating; write readable loops.  
6. **Test edge cases**:  
   - `[0, 100000]`, loss=0 → final=100000.  
   - `[1, 2, 3]`, loss=99 → final≈0.01 * something? (show result).  
   - Large random array with loss=50.  

---

### 8.7 Takeaway for Your Coding Journey

- **Binary search isn’t just for arrays** – it works for continuous ranges too.  
- **Decision functions are gold** – they let you convert “find the best” to “is this feasible?”.  
- **Floating‑point care** – Always anticipate rounding.  
- **Always explain** – A clear verbal walkthrough impresses hiring managers far more than a perfect but unreadable snippet.

---

### 8.8 Final Verdict

- **Good**: The linear feasibility check – simple, fast, mathematically elegant.  
- **Bad**: The binary search loop – precision‑heavy, but manageable.  
- **Ugly**: Managing floating‑point and ensuring termination – the usual “gotcha” in interview coding.

> **Result**  
> Mastering this problem demonstrates strong algorithmic thinking, a knack for numerical accuracy, and the confidence to tackle continuous‑space challenges – all skills that top tech companies actively seek.  

Happy coding and good luck on the next interview!

---

## 9. Closing Remarks

This problem is a **tour de force**: a blend of physics‑inspired math, continuous binary search, and careful floating‑point handling.  
The provided Java, Python, and C++ solutions run comfortably under the 10⁵ data limit while meeting the 1 e‑6 precision requirement.  

For the next interview, practice explaining the **decision function** step‑by‑step, show the binary search logic on paper, and be ready to discuss edge‑case handling.  
Your ability to turn a seemingly messy real‑world rule into an elegant algorithm is exactly what recruiters look for.  

Good luck landing that software engineer role! 🚀

---